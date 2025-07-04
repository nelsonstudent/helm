# Helm Example: web + postfix

Este é um exemplo simples de projeto Helm que demonstra:

- Como configurar um **chart principal** (`web`) com um **subchart** (`postfix`)
- Como declarar **dependências entre charts**
- Como **reutilizar valores** do chart pai no subchart usando `values.yaml`
- Como usar a seção especial `global:` para valores compartilhados entre charts

---

## 📁 Estrutura do Projeto

helm-example/
├── web/
│ ├── Chart.yaml # Metadados do chart principal
│ ├── values.yaml # Valores globais e do subchart
│ ├── templates/
│ │ └── configmap.yaml # Exemplo de template no chart pai
│ ├── charts/
│ │ └── postfix/ # Subchart completo
│ │ ├── Chart.yaml
│ │ ├── values.yaml
│ │ └── templates/
│ │ └── deployment.yaml

---

## 📦 1. Sobre os Charts

### `web`

O chart principal que instala uma aplicação genérica (`web`) e depende de um subchart chamado `postfix`.

### `postfix`

Um subchart que simula a instalação de um serviço como o Postfix, com suporte a configuração de `replicaCount` e `nodeSelector`.

---

## ⚙️ 2. Instalação

### Passo 1: Atualizar dependências

Entre na pasta `web` e execute o comando abaixo. Isso garante que o Helm baixe os charts dependentes corretamente para a pasta charts/.

```
cd web
helm dependency update
```

---

### Passo 2: Instalar com Helm

Após o comando mencioando no passo 1 da instalação, digitar:

helm install my-release . -f values.yaml

Isso irá instalar:

Um ConfigMap do chart web com o valor de global.environment
Um Deployment do subchart postfix com replicaCount e nodeSelector.environment.core herdados do pai


---


## 📌 3. Resultado Esperado

O Deployment do subchart será renderizado com:

```yaml
spec:
  replicas: 2
  template:
    spec:
      nodeSelector:
        environment:
          core: production
```

E o ConfigMap do chart pai será renderizado como:

```yaml
data:
  env: "production"
```


---  

  
## 🧠 4. Conceitos Envolvidos

| Conceito     | Descrição                                                                 |
|--------------|---------------------------------------------------------------------------|
| Chart        | Pacote Helm de uma aplicação                                              |
| Subchart     | Chart incluído como dependência                                           |
| Dependências | Declaradas no `Chart.yaml` com `dependencies:`                           |
| Values.yaml  | Valores de configuração dos charts                                        |
| global       | Bloco especial que permite compartilhar variáveis entre pai e filhos     |



---

## ✅ 5. Teste e Remoção

Para testar novamente:

```bash
helm uninstall my-release
helm install my-release . -f values.yaml
```

---

---

## 🛠️ Dicas para Contribuidores

### 💡 Sobre os Templates

Os arquivos localizados em `templates/` e `charts/postfix/templates/` usam o mecanismo de template do Helm (Go templates). Algumas dicas:

- Use `{{ .Values.<param> }}` para acessar valores definidos no `values.yaml`
- Para acessar valores do chart pai em um subchart, use prefixos como `{{ .Values.<subchart>.algumaCoisa }}`

Exemplo no subchart:

```yaml
replicas: {{ .Values.replicaCount }}  # Local ao subchart
# ou
replicas: {{ .Values.postfix.replicaCount }}  # Se definido no pai


---

**
📝 Observações
A estrutura pode ser usada como base para projetos maiores com múltiplos serviços.

O uso de global é opcional, mas útil para padrões corporativos e valores comuns.**


---


## 📌 Requisitos

- Helm 3.x
- Kubernetes (Minikube, Kind, ou cluster real)


