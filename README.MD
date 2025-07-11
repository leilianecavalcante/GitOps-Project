# 🛍️ Online Boutique com GitOps (DOCKER + KIND + ArgoCD)

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Kind](https://img.shields.io/badge/KIND-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)

Este projeto é um guia prático para implantar a aplicação **Online Boutique** em um cluster Kubernetes local (criado com **KIND**) utilizando uma abordagem **GitOps** com **ArgoCD**.

---

### 🎯 Objetivos

- **Cluster Local:** Criar um cluster Kubernetes funcional na sua máquina usando KIND.
- **Automação com GitOps:** Usar o ArgoCD para sincronizar e gerenciar os manifestos da aplicação a partir de um repositório Git.
- **Acesso à Aplicação:** Acessar o frontend da loja online localmente através de `port-forward`.

---

### 🛠️ Tecnologias Utilizadas

- Docker
- KIND (Kubernetes in Docker)
- kubectl
- ArgoCD
- Git & GitHub

---

## 📋 Pré-requisitos

Antes de começar, garanta que você tenha as seguintes ferramentas instaladas e funcionando:

- [ ] Docker
- [ ] KIND
- [ ] kubectl
- [ ] Git
- [ ] Uma conta no GitHub

---

## 🚀 Guia de Deploy Passo a Passo

Siga os passos abaixo para ter a aplicação rodando.

### 1. Preparar os Repositórios Git

Primeiro, precisamos de um fork da aplicação e de um repositório para os manifestos do GitOps.

<details>
<summary><strong>Clique para expandir as instruções</strong></summary>

1.  **Faça o Fork do Online Boutique:**
    - Acesse o repositório GoogleCloudPlatform/microservices-demo e clique em "Fork".

2.  **Clone o seu fork localmente:**
    ```bash
    git clone https://github.com/<SEU_USUARIO_GITHUB>/microservices-demo.git
    ```

3.  **Crie um novo repositório no GitHub para o GitOps:**
    - Crie um repositório público chamado `gitops-microservices`.

4.  **Copie o manifesto do Kubernetes para o novo repositório:**
    ```bash
    # Crie a estrutura de pastas
    mkdir -p gitops-microservices/k8s

    # Copie o manifesto da aplicação para o novo diretório
    cp microservices-demo/release/kubernetes-manifests.yaml gitops-microservices/k8s/online-boutique.yaml
    ```

5.  **Envie o manifesto para o seu repositório GitOps:**
    ```bash
    cd gitops-microservices
    git init
    git remote add origin https://github.com/<SEU_USUARIO_GITHUB>/gitops-microservices.git
    git add .
    git commit -m "feat: Adiciona manifesto do Online Boutique"
    git push -u origin main
    ```
</details>

### 2. Criar o Cluster Kubernetes com KIND

Se você ainda não tem um cluster, crie um com o seguinte comando:

```bash
kind create cluster --name online-boutique
```

Verifique se os nós estão prontos:
```bash
kubectl get nodes
```

### 3. Instalar e Acessar o ArgoCD

Agora, vamos instalar o ArgoCD no cluster e acessar seu painel.

<details>
<summary><strong>Clique para expandir as instruções</strong></summary>

1.  **Instale o ArgoCD:**
    ```bash
    # Crie o namespace para o ArgoCD
    kubectl create namespace argocd

    # Aplique os manifestos de instalação
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```
    Aguarde até que todos os pods no namespace `argocd` estejam `Running`.
    ```bash
    kubectl get pods -n argocd --watch
    ```

2.  **Acesse o painel do ArgoCD via `port-forward`:**
    ```bash
    kubectl port-forward svc/argocd-server -n argocd 8080:443
    ```
    - Abra no seu navegador: https://localhost:8080

![](/assets/argo%201.png)

3.  **Faça o login:**
    - **Usuário:** `admin`
    - **Senha:** Recupere a senha inicial com o comando abaixo:
      ```bash
      kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
      ```
</details>

### 4. Criar e Sincronizar a Aplicação no ArgoCD

Com o ArgoCD acessível, vamos configurar a aplicação.

1.  No painel web do ArgoCD, clique em **`+ NEW APP`**.
2.  Preencha os campos da seguinte forma:
    - **Application Name:** `online-boutique`
    - **Project Name:** `default`
    - **Sync Policy:** `Automatic` (Opcional, mas recomendado)
    - **Repository URL:** `https://github.com/<SEU_USUARIO_GITHUB>/gitops-microservices.git`
    - **Revision:** `HEAD`
    - **Path:** `k8s`
    - **Cluster URL:** `https://kubernetes.default.svc`
    - **Namespace:** `default`
3.  Clique em **`CREATE`**.

![](/Assets/argo%203.png)

4.  O ArgoCD começará a sincronizar os recursos. Se você não marcou a política como automática, clique no botão **`SYNC`** no painel da aplicação.

![](/Assets/argo%204.png)

Verifique se os pods da aplicação estão sendo criados no namespace `default`:
```bash
kubectl get pods --watch
```
![](/Assets/comandos%201.png)

### 5. Acessar o Frontend da Loja

Quando todos os pods estiverem rodando, exponha o serviço do frontend.

```bash
kubectl port-forward svc/frontend-external 8081:80
```

🎉 **Pronto!** Agora você pode acessar a loja no seu navegador em http://localhost:8081.

![](/Assets/onlineb.png)

---

### 🧹 Limpeza

Para remover todos os recursos criados, execute o seguinte comando:

```bash
kind delete cluster --name online-boutique
```

## 📦 Entregas
Repositório Git público com manifest Kubernetes

ArgoCD rodando no cluster KIND

Aplicação Online Boutique sincronizada via GitOps

Frontend acessível localmente via port-forward

# 📧 Contato
Leiliane Cavalcante

LinkedIn: https://www.linkedin.com/in/leiliane-cavalcante-5313881b3/

Email: liliannoguei001@gmail.com

Link do Github : https://github.com/seu-usuario/seu-projeto
