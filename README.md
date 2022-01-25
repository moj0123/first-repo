# Kubernetes In Action - Chapter 2
## 1. k8s 설치
vmware 환경에서 ubunto linux vm을 설치하고 해당 vm에 kubernetes 설치를 진행할 때에는 overlay 파라미터 값등 파라미터값을 넣어주고, 컨테이너 런타임 설치 및 설정, memory swap옵션을 disable하는등 사전에 필요한 작업과정들이 많이 있었으나 Google Cloud를 이용하여 GKE를 배포할때는 몇번의 클릭으로 Kubernetes 환경을 만들 수 있었다.
다음은 나의 GKE cluster의 간단한 정보들이다
![k8s 클러스터 이미지](https://user-images.githubusercontent.com/98390642/151022339-a28c9508-cd96-48d7-8422-3e6bd28e0b8e.png)
![k8s cluster node info](https://user-images.githubusercontent.com/98390642/151022362-753b2f02-2c1a-46d1-8813-c5d16cecaedb.png)
![k8s cluster networking info](https://user-images.githubusercontent.com/98390642/151022371-caf36ed0-66fe-4a61-b57a-cb0687526278.png)

결국에 Google Cloud를 통해서 Virtual Machine을 생성한 것이고 수동으로 설정해주던 작업을 Cloud가 자동적으로 설정해주고 배포해주는 과정을 도와준듯하다!

기본적으로 cluster안에는 마스터 노드가 포함되어있고 워커노드 갯수를 설정 할 수 있는것으로 보인다.
 Google cloud의 Cloud Shell이라는 로컬 개발머신을 통해서 cluster의 마스터노드 RESTAPI 서버를 통하여 컨트롤하는 구조인듯 하다.
 
![image](https://user-images.githubusercontent.com/98390642/151024097-2258683d-4ed7-4117-b1a7-7a9e7567c253.png)

해당 이미지가 잘 설명해준다.

각 노드들의 정보는 
kubectl describe node <node-name>  
명령어를 이용하여 상세 정보 확인이 가능하다(CPU,MEM,system info,running container etc...)

![kubectl des node-1](https://user-images.githubusercontent.com/98390642/151025088-9af900de-341e-4d99-89b1-43d5660f23cc.png)
![kubectl des node-2](https://user-images.githubusercontent.com/98390642/151025091-c3eb7f7b-3d25-4d61-81c6-de7095a7f09b.png)
![kubectl des node-3](https://user-images.githubusercontent.com/98390642/151025093-363f2927-7408-42f6-b9e8-784c4b6d2c23.png)

## 2. k8s 실행 및 접근
cluster의 구축은 매우 간단하고 빠르게 완료되었다 이제 kubernetes에 애플리케이션을 실행해보도록 하자
참고로하고있는 kubernetes in action에서는 kubctl run 명령어를 통해 replicationcontorller를 생성하고있지만
  현재 버전으로는 replicationcontroller의 생성이 kubectl run 명령어로 만들어지지 않는다는것을 알 수 있다.
  https://stackoverflow.com/questions/36205164/kubectl-run-does-not-create-replicacontroller
  
  
  
## 3. k8s logical component
