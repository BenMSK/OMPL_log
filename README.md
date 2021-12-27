# OMPL_log
tip for using ompl 

# ros catkin_ws에서 OMPL을 빌드하여 사용하는 방법.

***  

### ~~[1] OMPL git clone (NOT USED): ~~

~~OMPL 라이브러리 자체가, ROS의 package라고 생각을 하자.~~
~~현재 시스템의 catkin_ws/src 경로에 ompl github을 clone한다.~~
```
$ ~/catkin_ws/src && git clone https://github.com/ompl/ompl.git
```
***

### ~~[2] build (NOT USED)~~:

~~clone된 ompl src folder가 있는 상태에서, catkin_ws에 catkin_make_isolated를 진행한다.~~

```
$ ~/catkin_ws && catkin_make_isoated
```
~~isolated로 진행할 때, 각 catkin_ws/src에 package를 각각 빌드 하는 것으로 보인다.~~
~~일반적인 catkin_make 보다 더 느리다...~~
***  

***  
본 절에서 ompl의 최신 버전을 다운받고, 이를 커스터마이징한 후, 빌드하여 ros에서 linking하는 방법을 설명한다.
### [1] OMPL installation: 
우선 아래의 링크에서 'From source' 칸에서 **./install-ompl-ubuntu.sh**을 다운받는다.
https://ompl.kavrakilab.org/installation.html


그 후, 해당 폴더에 들어가서
```
$ ~/OMPL_INSTALLATION_FOLDER && ./install-ompl-ubuntu.sh
```

이를 실행하게 되면서, 크게 설정할 수 있는 부분은 두가지이다.
install-ompl-ubuntu.sh 에서  
1) ompl library 경로를 추가하는 부분:  
install_ompl() 함수에서 github ompl 최신 버전을 clone한 후, CMAKE를 하는 과정  
cmake ../.. -DPYTHON_EXEC=/usr/bin/python${PYTHONV}  

이때 이 문구에 CMAKE_INSTALL 경로를 추가해준다.
cmake ../.. -DPYTHON_EXEC=/usr/bin/python${PYTHONV}  -DCMAKE_INSTALL_PREFIX=/opt/ros/kinetic ../..  
이렇게 정하게 되면, /opt/ros/kinetic 경로에 MAKE 경로를 추가하게 된다. 
이를 설정하지 않고 진행하게 된다면, 기본적으로 /usr/lib 경로에 ompl이 빌드된다.  
  
2) sudo make install
install_ompl() 함수에서 마지막 줄에 있는 구문으로, 이 의미는 github ompl source를 빌드하여, 위에서 정한 경로에 libompl.so, libompl-<version>.so, ... 를 생성하게 된다.  
***

***
### [2] ompl customizing:

우리는 위에서 받은 최신 github ompl 버전을 커스터마이징하여 사용가능하다.
현재 폴더 'OMPL_INSTALLATION_FOLDER'에는 한가지 폴더와 파일이 있을 것이다.  
**./install-ompl-ubuntu.sh** **ompl-1.5.0** (1.5.0은 버전에 따라 차이 존재.)  
  
ompl-1.5.0 폴더에 들어간다.  
이 폴더는 ompl source 폴더이며 (결국, github에서 받은 폴더),  
우리는 이 폴더에 ompl source를 수정하여 커스터마이징이 가능하다.
주로 src 폴더의 코드들을 수정한다. (보통은...)
***


***
### [3] build again!:

우리는 위에서 받은 최신 github ompl 버전을 /OMPL_INSTALLATION_FOLDER/ompl-1.5.0/에서 커스터마이징하였다.  
이제 terminal에서 다음과 같은 명령어를 쳐서 빌드한다.  

```
$ cd ~/OMPL_INSTALLATION_FOLDER/ompl-1.5.0/build/Release
```
  
즉, ompl-1.5.0 폴더에서 /build/Release 에 들어간다.  
이 폴더의 의미는 우리가 [1] 과정에서 빌드하면서 생기는 ompl build 폴더이며, 
우리가 수정한 source 코드의 실행 파일을 만들어서 저장이 되는 곳이다.  
아래와 같이 설치 경로 (INSTALL_PREFIX=~)를 '/opt/ros/kinetic ~'으로 안한다면, default로 bin에 설치되는 듯.  
```
/OMPL_INSTALLATION_FOLDER/ompl-1.5.0/build/Release$ cmake ../.. -DPYTHON_EXEC=/usr/bin/python${PYTHONV}  -DCMAKE_INSTALL_PREFIX=/opt/ros/kinetic ../..  
```
```
/OMPL_INSTALLATION_FOLDER/ompl-1.5.0/build/Release$ sudo make install
```

이를 진행하게 되면, 우리는 [1] 과정을 반복하게 되는 것으로, 
수정된 source가 반영되어 지정한 경로 (-DCMAKE_INSTALL_PREFIX)에 빌드된 파일을 생성하게 된다.
***


***
### [4] ROS linking

이 과정은 필요하지 않을 수 있다. 기억하기 위해 남겨둔다.  
ROS package에서 OMPL을 사용하기 위해서 보통 필요한 과정은  
ROS package에 있는 CMakeLists.txt 를 수정해야 한다.  
보통 다음과 같은 3가지 줄이 추가되어야 한다.  

find_package(OMPL)  
include_directories(  
  ${catkin_INCLUDE_DIRS}  
  ${OMPL_INCLUDE_DIRS}  
)  
  
target_link_libraries(project_name  
   ${catkin_LIBRARIES}  
   ${OMPL_LIBRARIES}  
)
보통 위 과정만으로 충분하지만, 내 경우에는 ${OMPL_LIBRARIES} 경로가 이상하게 잡힌다.  
따라서, 이 경로를 임의로 설정해줘야 위에서 내가 진행한 새롭게 빌드된 코드를 찾아서 catkin_make가 가능하다.  
다음 2줄을 추가하였다.  
set(OMPL_LIBRARIES /opt/ros/kinetic/lib/libompl.so)  
set(OMPL_INCLUDE_DIRS /opt/ros/kinetic/include/ompl-1.5)  

libompl.so의 경우는 [1] 혹은 [3] 과정에서 해당 경로에 생성되는 source file (.so)이며,  
include/ompl-1.5의 경우는 include dir를 특정해주는 곳이다.

최종적으로는  
set(OMPL_LIBRARIES /opt/ros/kinetic/lib/libompl.so)  
set(OMPL_INCLUDE_DIRS /opt/ros/kinetic/include/ompl-1.5)  
find_package(OMPL)  
include_directories(  
  ${catkin_INCLUDE_DIRS}  
  ${OMPL_INCLUDE_DIRS}  
)  
  
target_link_libraries(project_name  
   ${catkin_LIBRARIES}  
   ${OMPL_LIBRARIES}  
)
***

FIN.  

### [999] tip:
이 부분은 내 컴퓨터에만 해당아는 내용으로 넘어가도 됨.
기존: doRRTstar 패키지에서 ompl을 찾을 때, /opt/ros/kinetic/lib/x~/lib/libompl.so를 찾아서 진행.
이렇게 진행 시, ompl을 installation page에서 다운 받고 build하고 진행하는 과정이 필요함.
ompl installation page [https://ompl.kavrakilab.org/installation.html]
이렇게 진행 시, ompl 라이브러리는 /usr/lib/ 에 위치하게 된다.
하지만, catkin_make 시, doRRT star가 여전히 /opt/ros/kinetic/lib/x~/lib/libompl.so 경로를 참조하려고 하는 문제가 발생...
해결 못해서, 위와 같은 접근 방식으로 진행...

### [XXX] Py-bindings:
./install_ubuntu_sh --python으로 실행 뒤,  
ompl build/Release에서, 'make -j 20 update_bindings'  
'make py_ompl' 진행 후, 부터 python3에서 import ompl이 동작. python3 
  
  
> This repository has **non-profit purposes**.\
> It is going to be updated, futher.\
> If you have any question, \
> please contact to msk930512@gmail.com.

