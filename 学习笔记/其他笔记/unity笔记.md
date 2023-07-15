# unity笔记

## 可能会用到的包导入

```java
using System.Collections;
using System.Collections.Generic;
using UnityEngin
//easyTouch的
using HedgehogTeam.EasyTouch;
//UI组件的
using UnityEngine.UI;
```

## 导入外部元素

### 将需要导入的外部参数设置为public

```java
    //cube对象
    public GameObject cube;

    //音频组件对象
    public AudioSource bgm;

    //音频组件数组
    public AudioClip[] songs;

    //颜色组件数组
    public Material[] colors;

    //外显变量 和 注解
    [Tooltip("这是角速度")]
    public float roateSpeed = 30;

    //调用脚本组件对象
    public MoveScript moveScript;

    //调用文本组件
    public Text scoreText;
```

### 控制对象出现和消失功能

```java
//控制隐藏和出现
    void BolleanActive()
    {
        //如果是显示的则设置成不显示，反之
        if (cube.activeSelf)
        {
            cube.SetActive(false);
            //或者直接销毁
            //Destroy(this.gameObject);
        }
        else
        {
            cube.SetActive(true);
        }
    }
```

### 移动与旋转

* ##### 基础移动
  
  ```java
  //设置速度和时间乘积作为位移距离，沿着x轴进行移动
  float dy = speed * Time.deltaTime;
  this.transform.Translate(dy, 0, 0, Space.Self);
  ```

* ##### 指定目标移动（向量计算）
  
  ```java
  //移动到指定位置操作
      void MoveFixedPosition()
      {  
          //将这个游戏对象 看向（z轴指向cube对象的位置）
          //两个物体之间向量距离的计算
          //获取A物体
          Vector3 A = this.transform.position;
          //获取B物体
          Vector3 B = cube.transform.position;
          //两个物体间的距离 通过调用API获取
          float distance = (B - A).magnitude;
          Debug.Log("距离是" + distance);
          if (distance > 0.3f)
          {
              float speed = 3;
              // Time.deltaTime 获取上次调用方法的差距时间
              float move = speed * Time.deltaTime;
  
              //Space.Self 沿着自身轴向方向进行运动
              this.transform.Translate(0, 0, move, Space.Self);
              Debug.Log("测试成功");
          }
          else
          {
              Debug.Log("到达目的地");
          }
      }
  ```

* ##### 基础旋转
  
  ```java
          //获取当前的欧拉角度数,进行旋转
          this.transform.Rotate(0, roateSpeed * Time.deltaTime, 0, Space.Self);
          Debug.Log("啊哈哈");
  ```

* ##### 调取音乐组件
  
  ```java
       AudioSource audio = this.GetComponent<AudioSource>();
           //判断当前音乐是否在播放，进行对应的播放和停止
             if (audio.isPlaying)
                  {
                      audio.Stop();
                  }
             else
                  {
                      audio.Play();
                  }
  ```

* ##### 延时功能
  
  * ###### 延时time时间执行DestoryBullet方法
  
  ```java
  Invoke("DestoryBullet", time);
  ```
  
  * ###### 循环延迟操作
    
    > 第一个参数是执行的方法
    > 
    > 第二个参数是第一次延时的时间
    > 
    > 第三个参数是每次执行延时的时间
    
    ```java
    InvokeRepeating("CreatCube", 1f, 1f);
    ```

* ##### 通过预制体进行实例化，指定出生点（创建角色）
  
  ```java
      //定义实例化方法
      void CreatCube()
      {
          //初始化子弹的实例
          //第一个参数是预制体资源 第二个参数是父节点
          GameObject node = Object.Instantiate(cubePrefab, this.transform);
          //初始化位置
          node.transform.position = this.transform.position;
  
          //随机出生点的选择
          float dx = Random.Range(-30,30);
          node.transform.Translate(dx, 0, 0, Space.Self);
      }
  ```

* ##### 粒子特效产生与消失
  
  ```java
   ParticleSystem ps = GetComponent<ParticleSystem>();
          if (Input.GetKeyDown(KeyCode.K))
          {
              ps.Play();
          }
          if (Input.GetKeyUp(KeyCode.K))
          {
              ps.Stop();
          }
  ```

* ##### 碰撞检测
  
  > 注意事项
  > 
  > 首先要让两个物体都有碰撞器 
  > 
  > 其次发生碰撞的物体要选定触发器
  
  ```java
   //碰撞检测
      private void OnTriggerEnter(Collider other)
      {
          //判断是否是需要销毁的物体
          if (!other.name.StartsWith("Cube"))
          {
              return;
          }
  
           //销毁子弹
           Destroy(this.gameObject);
  
          //销毁碰撞物
          Destroy(other.gameObject);
          Debug.Log("碰撞了");
      }
  ```

* ##### 确定鼠标位置，调用游戏主控，发送消息函数，调用方法
  
  ```java
          if (Input.GetMouseButtonDown(0))
          {
              Vector3 pos = Camera.main.ScreenToWorldPoint(Input.mousePosition);
              Debug.Log("位置" + pos);
              pos.z = 0;
              float distance = (pos - this.transform.position).magnitude;
              Debug.Log("距离" + distance);
              if (distance < 6)
              {
  
                  GameObject gameObject = GameObject.Find("游戏主控");
                  gameObject.SendMessage("AddScore");
                  Debug.Log("成功");
              }
          }
  ```

* ##### 其他操作

```java
     //获取到指定 的游戏对象
     cube = GameObject.Find("Cube");
     //将这个游戏对象 看向（z轴指向cube对象的位置）
     this.transform.LookAt(cube.transform);

    //获取父级对象
    void GetParent()
    {
        Transform parent = this.transform.parent;
       GameObject parentNode = parent.gameObject;
        Debug.Log("名字是" + parentNode.name);
    }

    //遍历获取子节点对象
    void GetChild()
    {
        foreach(Transform child in transform)
        {
            Debug.Log("子节点" + child.name);
        }
    }

    //随机播放音乐
    void PlayMusicRandom()
    {
        //生成随机数
        int m_index = Random.Range(0, songs.Length);
        //当前对象组件获取
        AudioSource ac = this.GetComponent<AudioSource>();
        //获得随机音乐赋值给当前组件对象
        ac.clip = this.songs[m_index];
        ac.Play();
    }

    //变换材质颜色
    void ChangeColors()
    {
        //生成随机数
        int m_index = Random.Range(0, colors.Length);
        //获取当前出现的随机颜色
        Material curColor = colors[m_index];
        //获取mesh renderer组件
        MeshRenderer chColor = this.GetComponent<MeshRenderer>();
        //赋值随机颜色给chColor组件
        chColor.material = curColor;
    }

    //引用其他组件的音乐源
    void PlayMusic2()
    {

        bgm.Play();

    }

    //引用其他组件的脚本
      void DoWork()
        {
            moveScript.roateSpeed = 180;
        }
 //触发器（无物理效果）碰撞瞬间检测
 2     void OnTriggerEnter(Collider other)
 3     {
 4         Debug.Log(other.name);
 5     }
 6     //触发器（无物理效果）持续碰撞检测
 7     void OnTriggerStay(Collider other)
 8     {
 9         Debug.Log(other.name);
10     }
11     //触发器（无物理效果）离开碰撞检测
12     void OnTriggerExit(Collider other)
13     {
14         Debug.Log(other.name);
15     } //带有物理效果的碰撞瞬间检测
 2     void OnCollisionEnter(Collision other)
 3     {
 4         print(other.transform .name);
 5     }
 6     //带有物理效果的持续碰撞检测
 7     void OnCollisionStay(Collision other)
 8     {
 9         print(other.transform.name);
10     }
11     //带有物理效果的离开碰撞检测
12     void OnCollisionExit(Collision other)
13     {
14         print(other.transform.name);
15     }
}
```

### 键盘鼠标监测功能

> Input.GetKey(KeyCode.W)：表示获取输入键
> 
> Input.GetKeyDown(KeyCode.W)：表示按下输入键
> 
> Input.GetKeyUp(KeyCode.W)：表示抬起输入键

```java
        //鼠标左键监测功能
        if (Input.GetMouseButtonDown(0))
        {
           Deubg.Log("鼠标监测功能")
        }
        //wasd监测
        if (Input.GetKey(KeyCode.W))
        {
            float dy = speed * Time.deltaTime;
            this.transform.Translate(dy, 0, 0, Space.Self);
        }
        if (Input.GetKey(KeyCode.S))
        {
            float dy = speed * Time.deltaTime;
            this.transform.Translate(-dy, 0, 0, Space.Self);
        }
        if (Input.GetKey(KeyCode.A))
        {
            float dy = speed * Time.deltaTime;
            this.transform.Translate(0, 0, dy, Space.Self);
        }
        if (Input.GetKey(KeyCode.D))
        {
            float dy = speed * Time.deltaTime;
            this.transform.Translate(0, 0, -dy, SpacetonDown(0))
        }

        //上下左右键监测
        if (Input.GetKey(KeyCode.UpArrow))
        {
            float dy = speed * Time.deltaTime;
            this.transform.Translate(dy, 0, 0, Space.Self);
        }
        if (Input.GetKey(KeyCode.DownArrow))
        {
            float dy = speed * Time.deltaTime;
            this.transform.Translate(-dy, 0, 0, Space.Self);
        }
        if (Input.GetKey(KeyCode.RightArrow))
        {
            float dy = speed * Time.deltaTime;
            this.transform.Translate(0, 0, dy, Space.Self);
        }
        if (Input.GetKey(KeyCode.LeftArrow))
        {
            float dy = speed * Time.deltaTime;
            this.transform.Translate(0, 0, -dy, Space.Self);
        }
    }
```

## 键盘对应目录

```java
 Backspace     退格键
 Delete      Delete键
 Tab        TabTab键
 Clear  Clear键
 Return  回车键
 Pause  暂停键
 Escape  ESC键
 Space  空格键
 Keypad0  小键盘0
 Keypad1  小键盘1
 Keypad2  小键盘2
 Keypad3  小键盘3
 Keypad4  小键盘4
 Keypad5  小键盘5
 Keypad6  小键盘6
 Keypad7  小键盘7
 Keypad8  小键盘8
 Keypad9  小键盘9
 KeypadPeriod 小键盘“.”
 KeypadDivide 小键盘“/”
 KeypadMultiply小键盘“*”
 KeypadMinus 小键盘“-”
 KeypadPlus 小键盘“+”
 KeypadEnter 小键盘“Enter”
 KeypadEquals 小键盘“=”
 UpArrow  方向键上
 DownArrow 方向键下
 RightArrow 方向键右
 LeftArrow 方向键左
 Insert  Insert键
 Home  Home键
 EndEnd键
 PageUp  PageUp键
 PageDown PageDown键
 F1功能键F1
 F2功能键F2
 F3功能键F3
 F4功能键F4
 F5功能键F5
 F6功能键F6
 F7功能键F7
 F8  功能键F8
 F9  功能键F9
 F10  功能键F10
 F11  功能键F11
 F12  功能键F12
 F13  功能键F13
 F14  功能键F14
 F15  功能键F15
 Alpha0 按键0
 Alpha1 按键1
 Alpha2 按键2
 Alpha3 按键3
 Alpha4 按键4
 Alpha5 按键5
 Alpha6 按键6
 Alpha7 按键7
 Alpha8 按键7
 Alpha9 按键9
 Exclaim ‘!’键
 DoubleQuote双引号键
 Hash Hash键
 Dollar ‘$’键
 AmpersandAmpersand键
 Quote 单引号键
 LeftParen 左括号键
 RightParen右括号键
 Asterisk ‘ * ’键
 Plus  ‘ +’键
 Comma ‘ , ’键
 Minus ‘ - ’键
 Period ‘ . ’键
 Slash ‘ / ’键
 Colon ‘ : ’键
 Semicolon‘ ; ’键
 Less  ‘< ‘键
 Equals ‘ = ‘键
 Greater ‘ >‘键
 Question ‘ ? ’键
 At  ‘@’键
 LeftBracket‘ [ ‘键
 Backslash ‘ \ ’键
 RightBracket‘ ] ’键
 Caret ‘ ^ ’键
 Underscore‘ _ ’键
 BackQuote‘ ` ’键
 A  ‘a’键
 B  ‘b’键
 C  ‘c’键
 D  ‘d’键
 E  ‘e’键
 F  ‘f’键
 G  ‘g’键
 H  ‘h’键
 I  ‘i’键
 J  ‘j’键
 K  ‘k’键
 L  ‘l’键
 M  ‘m’键
 N  ‘n’键
 O  ‘o’键
 P  ‘p’键
 Q  ‘q’键
 R  ‘r’键
 S  ‘s’键
 T  ‘t’键
 U  ‘u’键
 V  ‘v’键
 W  ‘w’键
 X  ‘x’键
 Y  ‘y’键
 Z  ‘z’键
 Numlock Numlock键
 Capslock 大小写锁定键
 ScrollLockScroll Lock键
 RightShift 右上档键
 LeftShift 左上档键
 RightControl右Ctrl键
 LeftControl左Ctrl键
 RightAlt 右Alt键
 LeftAlt 左Alt键
 LeftApple 左Apple键
 LeftWindows左Windows键
 RightApple右Apple键
 RightWindows右Windows键
 AltGr Alt Gr键
 Help  Help键
 Print  Print键
 SysReq Sys Req键
 Break Break键
 Mouse0 鼠标左键
 Mouse1  鼠标右键
 Mouse2 鼠标中键
 Mouse3 鼠标第3个按键
 Mouse4 鼠标第4个按键
 Mouse5  鼠标第5个按键
 Mouse6  鼠标第6个按键
 JoystickButton0手柄按键0
 JoystickButton1手柄按键1
 JoystickButton2手柄按键2
 JoystickButton3手柄按键3
 JoystickButton4手柄按键4
 JoystickButton5手柄按键5
 JoystickButton6手柄按键6
 JoystickButton7手柄按键7
 JoystickButton8手柄按键8
 JoystickButton9手柄按键9
 JoystickButton10手柄按键10
 JoystickButton11手柄按键11
 JoystickButton12手柄按键12
 JoystickButton13手柄按键13
 JoystickButton14手柄按键14
 JoystickButton15手柄按键15
 JoystickButton16手柄按键16
 JoystickButton17手柄按键17
 JoystickButton18手柄按键18
 JoystickButton19手柄按键19
 Joystick1Button0第一个手柄按键0
 Joystick1Button1第一个手柄按键1
 Joystick1Button2第一个手柄按键2
 Joystick1Button3第一个手柄按键3
 Joystick1Button4第一个手柄按键4
 Joystick1Button5第一个手柄按键5
 Joystick1Button6第一个手柄按键6
 Joystick1Button7第一个手柄按键7
 Joystick1Button8第一个手柄按键8
 Joystick1Button9第一个手柄按键9  
 Joystick1Button10第一个手柄按键10
 Joystick1Button11第一个手柄按键11
 Joystick1Button12第一个手柄按键12
 Joystick1Button13第一个手柄按键13
 Joystick1Button14第一个手柄按键14
 Joystick1Button15第一个手柄按键15
 Joystick1Button16第一个手柄按键16
 Joystick1Button17第一个手柄按键17
 Joystick1Button18第一个手柄按键18
 Joystick1Button19第一个手柄按键19
 Joystick2Button0第二个手柄按键0
 Joystick2Button1第二个手柄按键1
 Joystick2Button2第二个手柄按键2
 Joystick2Button3第二个手柄按键3
 Joystick2Button4第二个手柄按键4
 Joystick2Button5第二个手柄按键5
 Joystick2Button6第二个手柄按键6
 Joystick2Button7第二个手柄按键7
 Joystick2Button8第二个手柄按键8
 Joystick2Button9第二个手柄按键9
 Joystick2Button10第二个手柄按键10
 Joystick2Button11第二个手柄按键11
 Joystick2Button12第二个手柄按键12
 Joystick2Button13第二个手柄按键13
 Joystick2Button14第二个手柄按键14
 Joystick2Button15第二个手柄按键15
 Joystick2Button16第二个手柄按键16
 Joystick2Button17第二个手柄按键17
 Joystick2Button18第二个手柄按键18
 Joystick2Button19第二个手柄按键19
 Joystick3Button0第三个手柄按键0
 Joystick3Button1第三个手柄按键1
 Joystick3Button2第三个手柄按键2
 Joystick3Button3第三个手柄按键3
 Joystick3Button4第三个手柄按键4
 Joystick3Button5第三个手柄按键5
 Joystick3Button6第三个手柄按键6
 Joystick3Button7第三个手柄按键7
 Joystick3Button8第三个手柄按键8
 Joystick3Button9第三个手柄按键9
 Joystick3Button10第三个手柄按键10
 Joystick3Button11第三个手柄按键11
 Joystick3Button12第三个手柄按键12
 Joystick3Button13第三个手柄按键13
 Joystick3Button14第三个手柄按键14
 Joystick3Button15第三个手柄按键15
 Joystick3Button16第三个手柄按键16
 Joystick3Button17第三个手柄按键17
 Joystick3Button18第三个手柄按键18
 Joystick3Button19第三个手柄按键19
```

## easytouch手势

```java
单手指事件：

On_Cancel ：当系统取消跟踪触摸的时候发生

On_TouchStart：当手指刚触摸到屏幕时发生

On_TouchDown：当触摸正式被激活的时候发生

On_TouchUp ：当手指刚离开屏幕那一刻发生

On_SimpleTap：也是当手指刚离开屏幕时发生

On_DoubleTap：当手指连续按下两次的时候发生

On_LongTapStart ：当手指按下那一刻发生，但不能有移动直到所设置的长按的所需时间到了

On_LongTap ：当手指按在屏幕上达到长按所需的时间后则被认为长按有效，也就是此时发生，所以触发时机在On_LongTapStart之后

On_LongTapEnd ：当手指完成有效长按时，抬起的那一刻发生

On_DragStart ：当开始拖动可选对象那一刻发生

On_Drag ：当拖动正式被激活时发生

On_DragEnd：当不再拖动可选物体，手指在离开屏幕那一刻发生

On_SwipeStart：手指开始滑动屏幕的那一刻发生

On_Swipe：手指滑动屏幕正式被激活的时候发生

On_SwipeEnd：手指滑动完屏幕，离开屏幕的那一刻发生



双手指事件：

On_TouchStart2Fingers：就像On_TouchStart，但要用两个手指去触发

On_TouchDown2Fingers ：就像On_TouchDown，但要用两个手指去触发

On_TouchUp2Fingers：就像On_TouchUp，但要用两个手指去触发

On_SimpleTap2Fingers：就像On_SimpleTap，但要用两个手指去触发



On_DoubleTap2Fingers：就像On_DoubleTap，但要用两个手指去触发

On_LongTapStart2Fingers：就像On_LongTapStart，但要用两个手指去触发

On_LongTap2Fingers：就像On_LongTap，但要用两个手指去触发

On_LongTapEnd2Fingers：就像On_LongTapEnd，但要用两个手指去触发

On_Twist：当扭动手势发生时触发

On_TwistEnd：当扭动手势结束时触发

On_Pinch：当缩放手势被正式激活时触发

On_PinchIn：当缩放手势是缩小手势时触发

On_PinchOut：当缩放手势是扩大手势时触发

On_PinchEnd：当缩放手势结束并且离开屏幕的那一刻触发

On_DragStart2Fingers ：就像On_DragStart，但要用两个手指去触发

On_Drag2Fingers：就像On_Drag，但要用两个手指去触发

On_DragEnd2Fingers：就像On_DragEnd，但要用两个手指去触发

On_SwipeStart2Fingers：就像On_SwipeStart，但要用两个手指去触发



On_Swipe2Fingers：就像On_Swipe，但要用两个手指去触发

On_SwipeEnd2Fingers：就像On_SwipeEnd，但要用两个手指去触发



Unity UI：

On_OverUIElement ：当手指触碰到UGUI游戏对象的时候触发，前提是EasyTocuh的Unity UI compatibillity被勾选

On_UIElementUp：当手指离开UGUI游戏对象的那一刻触发，前提是EasyTocuh的Unity UI compatibillity被勾选



静态函数：

SetEnabled：      启用或禁用EasyTouch

GetEnabled: 启用EasyTouch就返回true

SetUICompatibily : 是否启用了UGUI兼容性（EasyTouch能否检测到UGUI）

GetUIComptability:启用UGUI兼容性就返回true

SetAutoUpdateUI:当你的手指在屏幕中滑动的时候，一碰到UI元素，是否把碰到的UI元素的信息进行自动更新

GetAutoUpdateUI:如果AutoUpdateUI启用了就返回true

SetEnableAutoSelect:是否启用自动选择

GetEnableAutoSelect:EnableAutoSelect启用的时候，返回true

SetAutoUpdatePickedObject:是否启用自动更新选择的可选游戏对象

GetAutoUpdatePickedObject:如果AutoUpdatePickedObject启用就返回true

Set3DPickableLayer:设置哪个层的3D游戏对象可以被选取

Get3DPickableLayer:返回可选取的3D游戏对象所在的层

AddCamera:添加一个深度摄像机（就是Camera游戏对象的ClearFlags为Depth Only，而场景自带的MainCamera的ClearFlags为Skybox，通常一个场景只有一个Skybox模式的主摄像机，负责将其拍摄范围内的物体显示到屏幕上，但是又可以有多个深度摄像机，使得就算不在主摄像机拍摄范围的物体但在深度摄像机拍摄范围的物体也能显示到屏幕上），使得被这深度摄像机所拍摄的物体也能受到EasyTouch的屏幕检测

RemoveCamera：删除一个摄像机

GetCamera：返回一个摄像机

SetEnable2DCollider：是否启用2D碰撞检测

GetEnable2DCollider：如果Enable2DCollider被启用就返回true

Set2DPickableLayer：设置哪个层的2D游戏对象能够进行选取

Get2DPickableLayer：返回可选取的2D游戏对象所在的层

SetGesturePriority：设置手势优先级

GetGesturePriority：得到手势优先级

SetStationaryTolerance：设置固定的容错率（因为手指很可能并不能完全覆盖在选取的游戏对象上，所以有容错率）

GetStationaryTolerance：得到固定的容错率

SetLongTapTime：设置长按所需的时间

GetlongTapTime：得到长按所需的时间

SetDoubleTapTime：设置双击间隔时间

GetDoubleTapTime：得到双击间隔时间

SetSwipeTolerance：设置滑动容错率（因为手指很可能并不能完全覆盖在选取的游戏对象上，所以有容错率）

GetSwipeTolerance：得到滑到容错率

SetEnable2FingersGesture：是否启用双手指

GetEnable2FingersGesture：如果Enable2FingersGesture启用就返回true

SetTwoFingerPickMethod：设置双手指选取物体的方式

GetTwoFingerPickMethod：返回双手指选取物体的方式

SetEnablePinch：是否启用缩放手势

GetEnablePinch：EnablePinch启用了，就返回true



SetMinPinchLength：设置最小缩放检测长度

GetMinPinchLength：返回最小缩放检测长度

SetEnableTwist：是否启用旋动手势

GetEnableTwist：EnableTwist启用了就返回true

SetMinTwistAngle：设置最小旋动检测角度

GetMinTwistAngle：得到最小旋动检测角度

GetSecondeFingerSimulation：是否启用第二只手指的仿真体

SetSecondFingerSimulation：如果第二手指的仿真体启用了，就返回true



Gesture类：

变量：

fingerIndex：触发手势事件的手指所对应的下标，如果是双手指触发则返回-1

touchCount ：被激活的触摸数量。用于管理多点触摸。

startPosition：触发触摸事件的时候，单手指的起始位置，或者多手指触发时的起始平均位置

position ：触发触摸事件的时候，单手指触发的当前位置，或者多手指触发时的当前平均位置

deltaPosition：因为输入相应一般都在Update函数里，所以自然是有位置和时间的变化量的，而deltaPosition就是最近一次触摸与本次触摸的位置变化量

actionTime：反应时间，就是说easytouch的同一个事件触发之间是有时间间隔的

deltaTime：因为输入相应一般都在Update函数里，所以自然是有位置和时间的变化量的，而deltaTime就是最近一次触摸与本次触摸的时间变化量

swipe：滑动或拖动的方向，这个值是通过计算当前手势的deltaPosition得到的，也就是手势的startPosition和手势结束时的当前position所得到的计算结果

swipeLength：手指在屏幕上滑动了多少像素，这个值是通过计算当手势结束时，该手势的startPosition和position得到的

swipeVector：在屏幕上滑动的矢量方向

deltaPinch：最近一次缩放与当前缩放的缩放变化量。此变化量对于On_Pinchin和On_PinchOut是正数。对于扩大手势是正数，对于缩小手势是负数

twistAngle：最新一次调用的旋动（扭动）手势的角度与当前角度的变化值，顺时针是负值，逆时针是正值

twoFingerDistance：双手指触发时，两只手指在屏幕上相距的像素距离

pickedObject：当触摸开始那一刻所碰到的可选取的游戏对象（如果不是可选取对象则返回none）又或者当auto update picked gameobject被设置为true时，自动更新所得到的可选取对象（也就是你贴着屏幕滑动，只要你碰到可选取游戏对象，那么这个pickedObject代表的就自动变更为那个游戏对象，当然这一切是基于auto update picked gameobject这个属性被设置为true的情况下）

pickedCamera：用手势去操纵的摄像机（例如操纵视野的放大缩小，镜头移动会用到）

isGuiCamera：用手势去选取操纵的摄像机是否GUI摄像机

isOverGUI：当前触摸发生的那一刹那，手指是否触碰到UGUI游戏对象，又或者在当Auto update picked Unity UI被勾选上设置为true的时候，实时检测手指是否在UGUI上。只有在UI Compatibility Mode设置发false禁用了，才会根据情况返回正确的值，不然则恒定返回false

pickedUIElement：当前触摸发生的那一刹那，手指是否触碰到并且选取到UGUI游戏对象，又或者在当Auto update picked Unity UI被勾选上设置为true的时候，实时检测手指是否在UGUI上。只有在UI Compatibility Mode设置为false禁用了，才会根据情况返回正确的值，不然则恒定返回null



方法：

GetTouchToWorldPoint(float z)：将当前触摸位置从当前屏幕坐标转换为距离摄像机z个单位处的世界坐标

GetTouchToWorldPoint(Vector3 position3D)：将当前触摸位置从当前屏幕坐标转换为世界坐标，这个position3D参数是目标在世界坐标系中的位置，正因为如此，可以用来计算摄像机与目标物体的距离长度

GetSwipeOrDragAngle：返回滑动或拖动的角度

NormalizedPosition：返回相对于屏幕的归一化位置

GetCurrentPickedObject：获取当前所选取的游戏对象，如果你把Auto update picked gameobject设置为true，则不需要用到这函数

IsOverUIElement：如果当前触摸发生在UGUI游戏对象上就返回true

IsOverRectTransform：如果当前触摸的是recttransform，则返回true

GetCurrentFirstPickedUIElement：得到当前被选中的UGUI游戏对象，如果Auto update picked Unity UI勾选设置为true了，则不需要用到此函数
```

```java
Tap : 手指移动的间距小于 Stationary tolerance 值, 被识别为点击操作

Slips : 手指移动的间距大于 Stationary tolerance 值, 被识别为滑动操作

Stationary tolerance

手指移动间距 (像素)  


Long tap time

长按多久才出发点击


Double tap time

双击最大间隔时间


Swipe tolerance

手势方向角度误差  


双指手势属性

EasyTouch <wbr>单指或多指手势动作识别

2 Fingers gesture

开启双指手势识别


Pick method

选中物体的方式

Finger : 双指必须同时触摸同一个物体

Average : 双指的中心位置在物体上就可以选中物体



Enable swipe & drag

能够识别滑动或拖动手势



Enable Pinch

识别向内挤压动作



Min pinch length

识别挤压动作的最小移动间距


Enable twist

能识别选择动作


Min twist angle

最小能够识别旋转角度
在场景中一个物体上添加Easytouch物体







1: 单指触摸屏幕

EasyTouch.On_TouchStart += MyTouchStart;  //  开始触摸瞬间        void MyTouchStart (Gesture gesture){ … }  //注册触摸事件的自定义方法, 参数是Gesture

EasyTouch.On_TouchDown +=MyTouchDown  //触摸状态                 void MyTouchDown(Gesture gesture)

EasyTouch.On_TouchUp+=MyTouchUp  //停止触摸                             void MyTouchUp(Gesture gesture)


判断当前是否触摸脚本所在的游戏物体

If(gesture.PickGameObject=this.gameObject) 



2: 单指点击屏幕


       EasyTouch.On_SimpleTap += MySimpleTap       

       void MySimpleTap(Gesture gesture) 


3: 单指长按屏幕   长按激活, 会有Start 到 End 的过程  而触摸是 Start Down Up 的过程


       EasyTouch.On_LongTapStart += MyLongTapStart  //开始点击瞬间   

       EasyTouch.On_LongTap +=MyLongTap   //长按中

       EasyTouch.On_LongTapEnd+=MyLongTapEnd  //停止点击


4:单指双击屏幕

       EasyTouch.On_DoubleTap+= MyDoubleTap      

       void MyDoubleTap(Gesture gesture)

双指操作


       EasyTouch.On_TouchStart2Fingers +=MyTouchStart2Fingers      //双指触摸瞬间    void MyTouchStart2Fingers(Gesture gesture)  {  …  }

       EasyTouch.On_TouchDown2Fingers +=MyTouchDown2Fingers     //双指触摸时      

       EasyTouch.On_TouchUp2Fingers+=MyTouchUp2Fingers                 // 双指离开屏幕时



EasyTouch.On_Cancle2Fingers+=MyCancle2Fingers      //  取消双指触摸(只要有一只手指离开便生效)  







在OnTouchStart2Finger 监听事件函数中

开启pinch模式

       EasyTouch.SetEnablePinch(true);

关闭扭转模式

       EasyTOuch.SetEnableTwist(false);



在On_PinchIn监听函数中

获得每秒中 双指的缩放值

float zoom = gesture.deltaPinch *time.deltaTime;     //注意gesture.deltaTime 恒大于0



对当前物体缩放

Vector3 scale=transform.localScale

Transform.localScale=new Vector3(scale.x-zoom,scale.y-zoom,scale.z-zoom);



在On_PinchOut 监听函数中

Transform.localScale=new Vector3(scale.x+zoom,scale.y+zoom,scale.z+zoom);   //加上缩放系统





在On_PinchEnd监听方法中

开启扭转模式

       EasyTouch.SetEnableTwist(true);



双指旋转



       EasyTouch.OnTouchStart2Fingers

       EasyTouch.OnTwist += MyTwist  //开始扭转

       EasyTouch.OnTwistEnd   //扭转结束

       EasyTouch.OnCancle2Finger  //双指离开屏幕



在OnTouchStart2Fingers 中 开启扭转模式,关闭旋转模式



在OnTwist的监听函数中

Void MyTwist(Gesture gesture)

{

       If(gesture.pickGameObject=this.gameObject)

{

       transform.rotate(new vector3(0,0,gesture.twistAngle));      //gesture.twistAngle   扭转角度

}

}




通用操作

通用是指单个手指或多个手指都能触发的事件    有拖拽动作, 手指划过姿势动作(一个方向划过)



拖拽操作



       EasyTouch.On_DragStart+= MyDragStart  //开始滑动瞬间   void MyDragStart(Gesture gesture)

       EasyTouch.On_Drag +=MyDrag   //滑动中

       EasyTouch.On_DragEnd+=MyDragEnd   //结束滑动时 




      gesture.fingerIndex    等于0单指,1双指    //当前接触屏幕的手指数,在滑动屏幕时,需要先在On_DragStart中记录手指的数目,在On_Drag过程中检验手指数目是否和开始时数量一致     单个手指,多个手指都会调用这个方法



       If(gesture.pickedGameobject=this.gameObject&& gesture.fingerIndex= =0)




      Vector3 touchPointInWorld =gesture.GetTouchToWorldPoint( gesture.pickObject.transform.position)  //获得当前触摸点在世界中的位置,世界坐标触摸点离摄像机的距离和传入参数的物体到摄像机的距离相同  



       float angle=gesture.GetSwipeOrDragAngle();    // 拖动的角度 ;  xy轴    -180°到180°   x轴正方向 0°  y轴正方向 90° y轴负方向 -90°





划过姿势

       EasyTouch.OnSwipe += MySwipe



       EasyTouch.swipeDirection  swipe=gesture.swipe;    //滑动方向 :  上下左右
```
