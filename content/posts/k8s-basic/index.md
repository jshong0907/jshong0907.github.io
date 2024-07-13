---
title: "[DevOps] 쿠버네티스란?"
date: 2024-07-14T01:35:53+09:00
draft: false
authors: ["JoonShik"]
description: "쿠버네티스는 컨테이너화된 어플리케이션을 배포하기 위한 오픈소스 오케스트레이터입니다."
categories: ["DevOps"]
tags: ["kubernetes", "k8s"]
featuredImagePreview: "thumbnail.png"
---
<!--more-->

## 쿠버네티스란?
쿠버네티스는 `컨테이너`화된 어플리케이션을 배포하기위한 `오픈소스 오케스트레이터`입니다.  

컨테이너에 대한 자세한 설명은 다음을 참고해주세요.  

{{< showcase title="[DevOps] 컨테이너란?" summary="컨테이너는 호스트 운영 체제에서 응용 프로그램과 그 종속성을 포함하여 일관된 환경에서 실행될 수 있도록 격리된 경량 가상화 기술입니다." image="/posts/k8s-container/thumbnail.png" link="/posts/k8s-container" >}}

쿠버네티스는 분산 시스템에서 다음과 같은 장점을 제공합니다.  

- 신뢰성: 시스템의 일부분이 고장나더라도 제 기능을 수행할 수 있도록 합니다.  

- 가용성: 주어진 시점에 요청된 서비스를 제공합니다.  

- 확장성: 새로운 노드를 추가하는 수평적 확장 방식으로 시스템의 규모를 유연하게 조절할 수 있도록 합니다.

{{< admonition type=info title="Info" open=true >}}
가용성과 확장성은 서로를 보장하지 않습니다.  

라우터를 예로 들면 라우터는 패킷 유실에 신경을 쓰지 않기에 신뢰성이 낮지만 항상 서비스를 제공하므로 가용성이 높습니다.
{{< /admonition >}} 

쿠버네티스는 일반적으로 `개발 속도`, `확장성`, `인프라 추상화`, `효율성`, `클라우드 네이티브 에코시스템`을 제공합니다.  

### 개발 속도
서비스는 높은 신뢰성과 가용성을 가지면서도 빠르게 새로운 기능을 개발할 수 있어야 합니다.  

쿠버네티스는 `불변성`, `선언형 컨피큐레이션`, `온라인 자가 치유 시스템`, `재사용 가능한 공유 라이브러리와 도구`를 제공하며 개발자의 개발 속도를 향상시킵니다.  

#### 불변성
쿠버네티스는 `불변형 인프라`를 제공합니다.  
{{< admonition type=note title="Note" open=true >}}
- 변경 가능한 인프라: 전통적인 시스템으로 apt 명령처럼 기존 시스템에 대한 증분 업데이트를 통해 구축하는 인프라입니다.  

- 불변형 인프라: 컨테이너처럼 완전히 새로운 이미지를 빌드하고 업데이트 실행 시 전체 이미지를 새로운 버전의 이미지로 교체하는 인프라입니다.  
{{< /admonition >}} 

변경 가능한 인프라는 시스템에 직접적으로 변경을 적용하여 구성에 대한 생성 방법이 기록되지 않습니다.  

반면 불변형 인프라는 모든 설정을 기록합니다. 이러한 기록을 통해 새로운 버전에 에러가 발생한 경우 변경된 부분을 통해 해결 방법을 쉽게 파악할 수 있습니다.  
또한, 기존 이미지를 수정하지 않기 때문에 기존 이미지를 통해 손쉬운 롤백이 가능하다는 장점이 있습니다.

#### 선언형 컨피규레이션
쿠버네티스의 모든 요소는 `선언형 컨피규레이션`으로 구성될 수 있습니다. (쿠버네티스는 선언형과 명령을 컨피규레이션을 모두 지원합니다.)    
{{< admonition type=note title="Note" open=true >}}
- 명령형 컨피규레이션: 상태를 일련의 명령문 실행으로 정의합니다.  

- 선언형 컨피규레이션: 상태를 원하는 최종 상태로 정의합니다.
{{< /admonition >}} 

쿠버네티스에서 명령형 컨피규레이션의 예시는 다음과 같습니다.  
```bash
$ kubectl run nginx --image nginx
$ kubectl expose deployment nginx --port 80 
$ kubectl scale deployment nginx --replicas 3
$ kubectl delete deployment nginx
```
위 명령들은 쿠버네티스가 해야 하는 동작을 정확히 명시합니다.  

쿠버네티스에서 선언형 컨피규레이션의 예시는 다음과 같습니다.  
```nginx.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx

---

apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  ports:
  - port: 80
  selector:
    app: nginx
```

해당 파일은 nginx 컨테이너를 80번 포트를 통해 생성하는 yaml 파일 예제입니다.  

해당 파일은 다음과 같은 명령어로 적용할 수 있습니다.  

```bash
$ kubectl apply -f nginx.yaml
```

쿠버네티스에서 모든 상태는 `yaml` 파일에 정의되고 변경이 필요하다면 파일을 수정한 후에 재적용하게됩니다.  

선언형 컨피규레이션은 결과물을 한눈에 이해하기 쉽고 개발자에 의한 에러 발생 가능성이 적으며 작업 내역을 추적하기 쉽고 롤백하기도 쉽다는 장점이 있습니다.  

선언형 컨피규레이션의 학습 난이도가 높다는 단점이 존재하지만, 일반적으로 프로덕션 환경에서 여러 개발자가 협업하는 경우 쿠버네티스는 `선언형 컨피규레이션 사용을 권장`합니다.  

#### 온라인 자가 치유 시스템
쿠버네티스는 원하는 상태의 컨피규레이션이 설정되면 끊임없이 원하는 상태와 현재 상태를 일치시키기 위한 조치를 취합니다.  
만약 수동으로 인적 개입을 통해 장애를 복구하게 되면 장애 복구에 지속적인 인적 비용이 소모되고 장애를 인지하고 대응해야하므로 일반적으로 자동으로 처리하는 것에 비하여 느리게 진행될 수밖에 없습니다.  

쿠버네티스는 자가 치유 시스템을 통해 비용 부담을 줄이고 신뢰성을 높입니다.  

### 확장성
#### 어플리케이션의 확장
쿠버네티스는 선언형 컨피규레이션을 통한 `불변적`이고 `선언적`인 특성으로 어플리케이션의 손쉬운 확장을 제공합니다.  

어플리케이션을 확장하기 위해 단순히 컨피규레이션 파일에 복제본의 수를 증가시키면 쿠번네티스가 자동으로 파드의 수를 증가시키는 방식으로 서비스를 확장하게 됩니다.  

물론 쿠버네티스 클러스터 내부에 물리적인 리소스가 부족하여 복제본을 증가시키지 못할 수도 있습니다.  
쿠버네티스는 어플리케이션과 클러스터를 완전히 분리하며 클러스터의 리소르를 추가하는 작업을 매우 손쉽게 처리할 수 있도록 합니다.  

더욱이 쿠버네티스는 각 팀에서 개별적인 서비스를 구축하도록 하기 때문에 각 팀의 리소스 사용량을 파악하여 이후 필요한 컴퓨팅 리소스를 더 정확히 예측하고 하드웨어 리소스를 미리 확보할 수 있도록 합니다.  
#### 팀의 확장
일반적으로 서비스를 개발하기 위해 적절한 팀의 규모는 6~8명 정도입니다.  
규모가 너무 커지면 공통된 목적을 갖고 팀 상황을 공유하는데 어려움을 겪게 됩니다.  

하지만 서비스 규모가 커질수록 더 많은 인원이 필요하게 됩니다.  

이를 해결하기 위해 일반적으로는 어플리케이션을 서비스 기준으로 분리하고 각 팀은 개별 마이크로서비스를 개발하는 방식을 사용하게 됩니다.  
쿠버네티스는 이러한 마이크로서비스 아키텍처를 더 쉽게 구축할 수 있도록 다양한 추상화를 제공하여 팀 간의 손쉬운 분리와 확장을 제공합니다.  
### 인프라 추상화
쿠버네티스는 인프라 리소스(서버, 네트워크, 스토리지 등)을 추상화하여 어플리케이션 배포와 관리를 단순화합니다.  

개발자는 클라우드 환경이나 물리적 인프라의 세부 사항에 대하여 고려할 필요 없이 고수준의 API를 통해 서비스를 개발하고, 쿠버네티스가 자원을 효율적으로 관리하고 할당하게됩니다.  

### 효율성
쿠버네티스에서 서비스는 격리되어있기에 여러 서비스는 서로 영향을 주지 않고 동일한 서버에 배포할 수 있습니다.  
이는 더 적은 서버로 여러 서비스를 제공할 수 있도록 합니다.  

### 클라우드 네이티브 에코시스템
쿠버네티스는 클라우드 네이티브 애플리케이션 개발을 촉진하는 다양한 도구와 서비스들을 포함한 생태계를 제공합니다.  
예를 들어, 모니터링, 로깅, 보안, CI/CD 도구 등 다양한 추가 기능을 쿠버네티스와 통합하여 사용할 수 있습니다.  

이는 개발 및 운영 프로세스를 자동화하고, 안정성과 관리 용이성을 향상시키는 데 도움이 됩니다.

## 쿠버네티스 로컬 환경 구축
쿠버네티스 실습 환경 구축을 위해 로컬 환경에 서비스를 구축하도록 하겠습니다.

로컬에 쿠버네티스 환경 구축을 위해서는 `미니큐브(minikube)`, `가상머신`, `kind(Kubernetes in docker)`가 존재하는데 도커를 사용하여 손쉽게 설치 및 관리할 수 있는 `kind`를 활용하여 쿠버네티스 클러스터를 구축하도록 하겠습니다.  

### Kind 설치
로컬 환경에 docker는 설치되어있어야 합니다.  

만약 `go`가 설치되어 있다면 다음과 같이 간단하게 설치 가능합니다.  

```bash
$ go install sigs.k8s.io/kind@v0.20.0
```

`Mac OS`이고 `brew`가 설치되어있다면 `brew`를 통한 설치도 지원하고 있습니다.  

```bash
$ berw install kind
```

이외는 [kind 설치 문서](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)를 참조해주세요.  

### Kind를 통한 쿠버네티스 환경 설정
다음과 같이 `kind create` 명령을 통해 클러스터 생성이 가능합니다.  

```bash
$ kind create cluster
$ kind get clusters
kind
$ kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:63736
CoreDNS is running at https://127.0.0.1:63736/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

위의 구성으로는 단일 노드의 쿠버네티스 클러스터가 구축되는데, kind를 통해 여러 노드의 클러스터 환경을 구축해볼 수도 있습니다.

```kind-config.yaml
kind: Cluster  
apiVersion: kind.x-k8s.io/v1alpha4  
nodes:  
- role: control-plane  
- role: worker  
- role: worker
```

위는 1개의 컨트롤 플레인과 2개의 워커 노드를 생성하는 설정 파일입니다.  

다음과 같이 클러스터를 설정 퐈일과 함께 생성하도록 하겠습니다.  

```bash
$ kind create cluster --config kind-config.yaml
```

이제 노드가 여러개 확인되는 것을 볼 수 있습니다.  

```bash
$ docker container ls
c93f406952f0   kindest/node:v1.27.3   "/usr/local/bin/entr…"   44 seconds ago   Up 40 seconds   127.0.0.1:63736->6443/tcp                    kind-control-plane
daff6046cfb4   kindest/node:v1.27.3   "/usr/local/bin/entr…"   44 seconds ago   Up 40 seconds                                                kind-worker
d80628741d43   kindest/node:v1.27.3   "/usr/local/bin/entr…"   44 seconds ago   Up 40 seconds                                                kind-worker2

$ kubectl get nodes
NAME                 STATUS   ROLES           AGE   VERSION
kind-control-plane   Ready    control-plane   51s   v1.27.3
kind-worker          Ready    <none>          28s   v1.27.3
kind-worker2         Ready    <none>          32s   v1.27.3
```




