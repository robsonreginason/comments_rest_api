#### Escolha de Tecnologias

1. **Flask**: Escolhido pela sua simplicidade e flexibilidade, permitindo o desenvolvimento rápido de APIs RESTful. A documentação rica e a vasta comunidade de suporte foram fatores decisivos.
2. **Gunicorn**: Utilizado como servidor WSGI para produção. Gunicorn é capaz de lidar com múltiplas conexões simultâneas e é compatível com vários servidores web, tornando-o ideal para escalabilidade.
3. **Terraform**: Ferramenta de infraestrutura como código (IaC) utilizada para provisionar e gerenciar a infraestrutura na AWS. Escolhida pela sua capacidade de definir a infraestrutura de forma declarativa e garantir consistência entre ambientes.
4. **Ansible**: Ferramenta de automação de configuração e gerenciamento de aplicações. Escolhida por sua simplicidade e suporte a uma ampla gama de módulos para diferentes sistemas operacionais e ambientes de nuvem.
5. **Jenkins**: Ferramenta de integração contínua e entrega contínua (CI/CD). Utilizada para automatizar o processo de build, teste e deploy, garantindo entregas rápidas e consistentes.
6. **Prometheus e Grafana**: Conjunto de ferramentas para monitoramento e visualização de métricas. Prometheus é utilizado para coletar métricas da aplicação, enquanto Grafana é usado para criar dashboards interativos.

#### Configuração da Infraestrutura (IaaS)

Usamos o Terraform para criar uma instância EC2 na AWS, configurada com o Amazon Linux 2. A infraestrutura inclui:

- **EC2 Instance**: Instância t2.micro configurada para hospedar a aplicação Flask. Utilizamos uma AMI padrão da Amazon Linux 2.
- **Security Groups**: Configurados para permitir tráfego HTTP e HTTPS, garantindo a segurança do ambiente.

Exemplo de configuração do Terraform:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "api_instance" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  tags = {
    Name = "API_Comment"
  }
}

output "instance_ip" {
  value = aws_instance.api_instance.public_ip
}
```

#### Configuração de Hosts (IaC)

Ansible foi utilizado para configurar a instância EC2 com as dependências necessárias e para implantar a aplicação Flask. As tarefas incluem:

- **Instalação de dependências**: Python, pip, Flask, Gunicorn.
- **Deploy da aplicação**: Transferência do código-fonte para a instância e configuração do Gunicorn para servir a aplicação.

Exemplo de playbook Ansible:

```yaml
- name: Configurar Servidor API
  hosts: api_servers
  become: yes
  tasks:
    - name: Instalar Python e pip
      apt:
        name:
          - python3
          - python3-pip
        state: present

    - name: Instalar dependências Python
      pip:
        name:
          - Flask
          - gunicorn

    - name: Copiar código da aplicação
      copy:
        src: /local/path/to/app/
        dest: /remote/path/to/app/

    - name: Iniciar aplicação com Gunicorn
      shell: |
        cd /remote/path/to/app/
        gunicorn -b 0.0.0.0:5000 app:app
```

### Pipeline de Deploy Automatizado

O pipeline de CI/CD foi implementado no Jenkins e inclui as seguintes etapas:

- **Clonagem do repositório de código**: Baixa o código mais recente do GitHub.
- **Instalação de dependências**: Instala as dependências listadas no requirements.txt.
- **Execução de testes**: Executa testes automatizados para garantir a integridade do código.
- **Deploy**: Utiliza Ansible para implantar a aplicação na infraestrutura provisionada.

Exemplo de configuração de pipeline no Jenkins:

```groovy
pipeline {
    agent any
    stages {
        stage('Clonar Repositório') {
            steps {
                git 'https://github.com/your-repo/comments-api.git'
            }
        }
        stage('Instalar Dependências') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Executar Testes') {
            steps {
                sh 'pytest tests/'
            }
        }
        stage('Deploy') {
            steps {
                sh 'ansible-playbook -i inventory.ini deploy.yml'
            }
        }
    }
    post {
        success {
            echo 'Deploy concluído com sucesso!'
        }
        failure {
            echo 'Ocorreu um erro durante o deploy.'
        }
    }
}
```

### Monitoramento e Métricas

Para monitorar o desempenho da aplicação, utilizamos Prometheus para coletar métricas e Grafana para visualização. Configuramos o Prometheus para monitorar o endpoint `/metrics` da aplicação Flask, expondo métricas como latência de requisições e taxa de erro.

Exemplo de configuração do Prometheus:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'comments_api'
    static_configs:
      - targets: ['localhost:5000']
```

### Mudanças de Plano

- **Uso de Docker Abandonado**: Inicialmente planejamos usar Docker, mas a complexidade adicional e as restrições de tempo nos levaram a optar por uma solução mais direta com Ansible e Terraform.
- **Shift para Ansible**: Optamos por Ansible ao invés de scripts bash para maior flexibilidade e facilidade de manutenção.

### Ideias para Melhorias Futuras

- **CI/CD com GitLab**: Considerar a migração do pipeline para GitLab CI/CD para uma integração mais estreita com o repositório de código e funcionalidades avançadas de CI/CD.
- **Suporte Multi-região**: Adicionar suporte para múltiplas regiões na AWS para maior disponibilidade e resiliência.
- **Segurança**: Implementar autenticação e autorização, possivelmente com OAuth ou JWT, e adicionar SSL/TLS para todas as comunicações.
- **Monitoramento Avançado**: Integrar ELK stack (Elasticsearch, Logstash, Kibana) para análise de logs e melhorar o sistema de alertas.

### Testes e Qualidade

- **Cobertura de Testes**: Ampliar a suíte de testes para incluir mais cenários de uso e testes de carga.
- **Feedback do Usuário**: Utilizar ferramentas de análise para obter feedback contínuo sobre o desempenho e a usabilidade da aplicação.

### Notas Adicionais

- **Manutenção e Atualizações**: Estabelecer um cronograma regular para atualizações de segurança e manutenção, garantindo a estabilidade e a segurança da aplicação.
