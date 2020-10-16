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
  
2) sudo make install
install_ompl() 함수에서 마지막 줄에 있는 구문으로, 이 의미는 github ompl source를 빌드하여, 위에서 정한 경로에 libompl.so, libompl-<version>.so, ... 를 생성하게 된다.

***

### [2] build (NOT USED):

clone된 ompl src folder가 있는 상태에서, catkin_ws에 catkin_make_isolated를 진행한다.

```
$ ~/catkin_ws && catkin_make_isoated
```
isolated로 진행할 때, 각 catkin_ws/src에 package를 각각 빌드 하는 것으로 보인다.
일반적인 catkin_make 보다 더 느리다...
***  



### [3] tip:
이 부분은 내 컴퓨터에만 해당아는 내용으로 넘어가도 됨.
기존: doRRTstar 패키지에서 ompl을 찾을 때, /opt/ros/kinetic/lib/x~/lib/libompl.so를 찾아서 진행.
이렇게 진행 시, ompl을 installation page에서 다운 받고 build하고 진행하는 과정이 필요함.
ompl installation page [https://ompl.kavrakilab.org/installation.html]
이렇게 진행 시, ompl 라이브러리는 /usr/lib/ 에 위치하게 된다.
하지만, catkin_make 시, doRRT star가 여전히 /opt/ros/kinetic/lib/x~/lib/libompl.so 경로를 참조하려고 하는 문제가 발생...
해결 못해서, 위와 같은 접근 방식으로 진행...

> This repository has **non-profit purposes**.\
> It is going to be updated, futher.\
> If you have any question, \
> please contact to msk930512@gmail.com.
