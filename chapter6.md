
# Kubernetes In Action - Chapter 6
## 1. Volume
쿠버네티스 볼륨은 파드의 구성요소로 컨테이너와 동일하게 파드 스펙에서 정의된다.

파드 내부의 각 컨테이너는 고유하게 분리된 파일시스템을 가짐.  
파일시스템은 컨테이너 이미지에서 제공되어 각 컨테이너는 서로 별도의 파일시스템을 가진다. (컨테이너간 파일시스템 공유 불가능)
![image](https://user-images.githubusercontent.com/98390642/156348472-18a22c78-cb15-435c-b022-28f146622e23.png)


컨테이너들끼리 데이터를 공유하기 위해, 그리고 실제 데이터를 가진 디렉터리를 보존하기 위해 볼륨을 사용함.

볼륨의 유형
1. emptyDir
2. gitRepo
3. hostPath
4. PersistentDisk
5. Persistent Volume, PersistentVolumeClaim

## 2. emptyDir
컨테이너들끼리 데이터를 공유하기 위해서 볼륨을 사용하는 것 (가장 간단한 방법)
최초 볼륨이 생성될 때 볼륨 내용이 비어있기 때문에 emptyDir로 명명됨
동일 파드에서 실행 중인 컨테이너 간 파일을 공유 할 때 유용하다.

![image](https://user-images.githubusercontent.com/98390642/156348937-22f67148-4f39-4072-a674-ac61ebbddd26.png)

![image](https://user-images.githubusercontent.com/98390642/156349340-05a0c119-8a20-4d47-8f4f-541d1f5256f1.png)

emptyDir은 디스크가 아닌 메모리를 사용하는 tmpfs 파일시스템으로 생성가능하다.

## 3. gitRepo
기본적으로 emptyDir 볼륨이며 파드가 시작되면 컨테이너가 생성되기전에 git repository를 복제하고 특정 리비전을 체크아웃해 데이터로 채움.

![image](https://user-images.githubusercontent.com/98390642/156350008-6761b2d0-4e11-4b9f-9420-68773ec5dc3d.png)

![image](https://user-images.githubusercontent.com/98390642/156350192-139557eb-79c7-4da4-be26-2c5bfc51b447.png)

git repository에 변경을 푸시할 때마다 웹사이트의 새 버전을 서비스하기 위해 파드를 삭제해줘야함
이러한 단점을 보완하기 위해서 사이드카 컨테이너를 통해서 로컬 디렉토리를 git repository와 동기화되도록 함.

지금까지 gitRepo와 emptyDir 볼륨은 파드가 삭제되면 볼륨과 콘텐츠는 삭제된다.
이후부터 볼 다른 유형의 볼륨은 새 디렉터리를 생성하지 않고 대신 기존에 존재하는 외부 디렉터리를 파드의 컨테이너 파일시스템에 마운트한다.

## 4. hostPath
hostPath 볼륨은 노드 파일시스템의 특정 파일이나 디렉터리를 가리킨다.
동일 노드에 실행 중인 파드가 hostPath 볼륨의 동일 경로를 사용 중이면 동일한 파일이 표시됨.
hostPath 볼륨의 콘텐츠는 파드가 종료되어도 삭제되지 않음.
![image](https://user-images.githubusercontent.com/98390642/156351291-fb5d8d43-d6dd-4603-bf4f-177059a48815.png)
![image](https://user-images.githubusercontent.com/98390642/156351622-718a5167-69b4-4a06-8025-b29d608fa4d3.png)

이 볼륨은 파드가 어떤 노드에 스케줄링 되느냐에 따라 민감하기 때문에 일반적인 파드에 사용하는것은 좋지 않음 .

## 5. Persistent Storage
파드의 컨테이너는 어떤 클러스터 노드에서도 접근이 필요하기 때문에  NAS(Network Attached Storage) 유형에 저장돼야함.
이러한 목적의 Persistent Storage는 Cloud 환경에서 제공하는 GCE Persistent Disk, AWS Elastic Block Store Volume 과 NFS 볼륨, 그리고 ISCSI, GlusterFS, rdb, fc 등등 있다.
![image](https://user-images.githubusercontent.com/98390642/156360633-e1c6fc27-d8c6-4826-98a0-72512961533a.png)
![image](https://user-images.githubusercontent.com/98390642/156361800-5ee758f2-6e86-499c-a3e2-bf2cf7114535.png)

persistent 볼륨은 각 볼륨 유형별로 필요한 속성 세부정보가 필요하다.
하지만 이러한 인프라스트럭처를 참조하는 것은 쿠버네티스가 추구하는 것과는 거리가 멀다.

이 문제점은 Persistent Volume Claim으로 개선 할 수 있으며, 동일한 파드 정의를 다른 클러스터에서 사용가능하도록 만들어줌

## 6. Persistent Volume, Persistent Volume Claim 
인프라스트럭처의 세부사항을 처리하지 않고 애플리케이션이 쿠버네티스 클러스터에 스토리지를 요청할 수 있도록 하기 위해 Persistent Volume 과 Persistent Volume Claim 이 도입됨.

![image](https://user-images.githubusercontent.com/98390642/156362724-d1bf51a6-6937-465e-8ada-af7dbcd65ec4.png)
개발자는 Persistent Volume Claim을 통하여 Persistent Volume에 바인딩되어 볼륨을 사용할 수 있다.
PersistentVolumeClaim은 인프라스트럭처의 세부사항이 참조되지 않는다.
인프라스트럭처의 세부사항을 참조하여 Persistent Volume을 생성하는것은 관리자의 역할이다.

![image](https://user-images.githubusercontent.com/98390642/156363258-57989be1-2be6-4908-b294-fa3b8c8a5ba7.png)

![image](https://user-images.githubusercontent.com/98390642/156363331-8b64fa83-2ce5-4578-88cc-ec236063faf6.png)


지금까지는 Persistent Volume을 관리자가 미리 프로비저닝한 상황에서 Persistent Volume Claim을 사용할 수 있다.
그러나 동적인 프로비저닝을 통하여 Persistent Volume을 자동으로 프로비저닝이 가능하다.

![image](https://user-images.githubusercontent.com/98390642/156363811-6a6d4831-e27d-478a-8f1c-0bf753215cb0.png)

동적인 프로비저닝을 위해서 StorageClass 라는 오브젝트를 사용한다.
![image](https://user-images.githubusercontent.com/98390642/156364330-1de81896-af15-463d-8a11-09361209ebca.png)
![image](https://user-images.githubusercontent.com/98390642/156364392-051b5728-ba2c-4d74-ae1a-a04c3f415830.png)







