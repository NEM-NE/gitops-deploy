# GitOps boilerplate

[gitops-app로 이동하기](https://github.com/NEM-NE/gitops-app)

## 소개

K8s 기반 환경에서 GitOps를 쉽게 적용하기 위한 보일러플레이트입니다.

GitOps 방식에 맞게 어플리케이션 소스코드와 Dockerfile이 있는 Application Repo, 배포를 위한 매니페스트가 있는 Config Repo로 구성되어 있습니다.

gitops-app 파일구조

```yaml
├── README.md
└── app
    ├── Dockerfile
    ├── bin
    ├── build.gradle
    ├── gradle
    ├── gradlew
    ├── gradlew.bat
    ├── settings.gradle
    └── src
```

gitops-deploy 파일구조

```yaml
├── README.md
├── argocd
│   └── application.yaml
├── kubernetes-manifests
│   ├── app.cluster-role.yaml
│   ├── app.deployment.yaml
│   ├── app.service-account.yaml
│   ├── app.service.yaml
│   └── ingress.yaml
└── skaffold.yaml
```

### Workflows

1. 개발자가 gitops-app에 PR를 보낸다.
2. PR이 main 브랜치에 머지되면 build.yaml이 실행된다.
    1. 도커허브에 로그인한다.
    2. 변경된 소스코드가 반영된 이미지를 생성하고 Push한다.
    3. gitops-deploy에 변경되었다고 Trigger한다.
3. gitops-deploy에서는 gitops-app에서 온 정보를 바탕으로 update-image.yaml을 실행한다.
    1. 해당 이미지를 사용하는 메니페스트를 적절한 태그로 변경해준다.
    2. 커밋을 추가하여 ArgoCD가 변경되었음을 인지하도록 한다.
4. ArgoCD는 변경을 확인하고 ReSync를 해준다.

### 사전에 설정해야할 것들

```yaml
# gitops-app
secrets.DOCKER_USER
secrets.DOCKER_PASSWORD
secrets.EXAMPLE_GITOPS_DEPLOY_TRIGGER # PAT로 Repo 관련 권한만 허용해주면 됩니다.

# gitops-deploy
secrets.DOCKER_USER
```

### 사용 기술 스택

- docker
- k8s
- skaffold
- argocd
- github actions