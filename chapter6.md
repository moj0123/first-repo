
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
5. PersistentVolumeClaim

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

## 5. Persistent
