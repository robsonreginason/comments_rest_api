##

**teste**

_teste_
~Teste~
(teste)

```python
print("Hello, world!")
```

### Teste

*Teste*

**Teste**

***Teste***

1. testes
2. testes
3. testes
4. testes


---
--
-
---



>Teste
>teste
---
>>teste
>>teste

# Pipeline DevOps para Aplicação Web

Este documento descreve a pipeline DevOps para a implantação e manutenção de uma aplicação web. A pipeline cobre desde o desenvolvimento até a produção, incluindo integração contínua, entrega contínua e monitoramento.

---

## 1. Requisitos

Antes de iniciar a configuração da pipeline, certifique-se de ter os seguintes requisitos:

* **Ferramentas:**
  - Git
  - Docker
  - Kubernetes
  - Jenkins
  - Prometheus
  - Grafana

* **Ambiente:**
  - Servidor de desenvolvimento
  - Cluster Kubernetes para produção

---

## 2. Estrutura da Pipeline

A pipeline é dividida nas seguintes etapas:

1. **Desenvolvimento**
2. **Integração Contínua**
3. **Entrega Contínua**
4. **Monitoramento e Logs**

### Desenvolvimento

Desenvolvimento é feito utilizando as seguintes tecnologias:

- **Linguagens:** Python, JavaScript
- **Frameworks:** Django, React

```python
# Exemplo de código Python
def hello_world():
    print("Olá, mundo!")
```

# Estruturação e Implantação do Projeto

## Escolha do Sistema Operacional

Após estruturar a implantação do projeto, foi definido trabalhar com SO Windows, pois, inicialmente, não havia os recursos necessários para criar um lab/ambiente de testes.

## Problema com Jenkins

Ao instalar o Jenkins, a pasta `/secrets` não estava disponível no local esperado. A pasta `secrets` contém a primeira senha de utilização do Jenkins. Foram realizados testes reinstalando e testando versões antigas, porém sem sucesso.

## Mudança para Linux

Para não onerar o tempo, o sistema operacional foi alterado para Linux, na distribuição Ubuntu.

## Provisionamento de Infraestrutura

No provisionamento da infraestrutura, foi utilizada uma VM já existente, com Docker, Terraform e Kubernetes instalados.

