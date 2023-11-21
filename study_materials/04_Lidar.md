## YDLidar X2

В наших с вами задачах мы будем использовать небольшой лидар, представленный на картинке ниже:

<p align="center">
<img src="../assets/lesson_02/ydlidar_pic.jpg" width=400>
</p>


Lidar YDLidar X2 - это лидар с 2D-сканированием, основанный на принципе тени.

Основные характеристики Lidar YDLidar X2:

- Максимальное угловое разрешение: 1200 (одинарный зрачок), - 600 (двойной зрачок) градусов
- Максимальное угловое поле зрения: 270 градусов
- Максимальное расстояние обнаружения: 20 метров
- Скорость обмена данными: 8000-10000 бит/с
- Диапазон рабочих температур: -10

Для работы с датчиком Lidar YDLidar X2 необходимо установить пакеты:

- ```YDLidar-SDK library```
- ```ydlidar_ros_driver```

Вообще, вся установка - код, который вы можете запустить из любого места у вас описана уже в ```00_installation.md```, воспользовавшись командой вы и все и установите:

 ```bash
 sudo sh [CODE_NAME]
 ```

Поехали дальше. На [страничке Github нашего лидара](https://github.com/YDLIDAR/ydlidar_ros_driver) мы можем найти информацию о том, что лидар публикует, какой тестовый launch файл мы можем запустить, ну и он сам - давайте на него взглянем:

```xml
<launch>

  <arg name="tf" default="true" />
  <node name="ydlidar_lidar_publisher"  pkg="ydlidar_ros_driver"  type="ydlidar_ros_driver_node" output="screen" respawn="false" >
    <!-- string property -->
    <param name="port"         type="string" value="/dev/ydlidar"/>  
    <param name="frame_id"     type="string" value="laser_frame"/>
    <param name="ignore_array"     type="string" value=""/>

    <!-- int property -->
    <param name="baudrate"         type="int" value="115200"/>  
    <!-- 0:TYPE_TOF, 1:TYPE_TRIANGLE, 2:TYPE_TOF_NET -->
    <param name="lidar_type"       type="int" value="1"/>  
    <!-- 0:YDLIDAR_TYPE_SERIAL, 1:YDLIDAR_TYPE_TCP -->
    <param name="device_type"         type="int" value="0"/>  
    <param name="sample_rate"         type="int" value="3"/>  
    <param name="abnormal_check_count"         type="int" value="4"/>  

    <!-- bool property -->
    <param name="resolution_fixed"    type="bool"   value="true"/>
    <param name="auto_reconnect"    type="bool"   value="true"/>
    <param name="reversion"    type="bool"   value="false"/>
    <param name="inverted"    type="bool"   value="true"/>
    <param name="isSingleChannel"    type="bool"   value="true"/>
    <param name="intensity"    type="bool"   value="false"/>
    <param name="support_motor_dtr"    type="bool"   value="false"/>
    <param name="invalid_range_is_inf"    type="bool"   value="false"/>
    <param name="point_cloud_preservative"    type="bool"   value="false"/>

    <!-- float property -->
    <param name="angle_min"    type="double" value="-180" />
    <param name="angle_max"    type="double" value="180" />
    <param name="range_min"    type="double" value="0.1" />
    <param name="range_max"    type="double" value="12.0" />
    <!-- frequency is invalid, External PWM control speed -->
    <param name="frequency"    type="double" value="10.0"/>
  </node>
  
  <group if="$(arg tf)">
      <node pkg="tf" type="static_transform_publisher" name="base_link_to_laser4"
    args="0.0 0.0 0.2 0.0 0.0 0.0 /base_footprint /laser_frame 40" />
  </group>

</launch>
```

Давайте внутри нашего пакета создадим папку  ```kitty_vision```, а в ней папку ```launch```, а там ```drivers``` и скопируем туда этот launch, например, под названием ```start_ydlidar_x2.launch```.  

Помните, когда мы с вами проходили первый урок, мы уже познакомились с ```rviz```? Давайте теперь его запустим в другом терминале командой 

```
rviz
```

У нас открылось окно, но никакой информации из него мы пока получить не можем.

<p align="center">
<img src="../assets/lesson_02/rviz01.png" width=500>
</p>

Следовательно, его нужно настроить, а для этого `Add->By topic->/scan->LaserScan` и `Fixed Frame` меняем на `base_footprint`:

<p align="center">
<img src="../assets/lesson_02/rviz02.png" width=500>
</p>

Отлично! Теперь мы видим, что данные с лидара к нам приходят. Можно еще поменять отображение точек, например изменить их стиль `Style` или размер `Size (m)`. Настройте, как вам будет удобнее и поехали дальше. 

<p align="center">
<img src="../assets/lesson_02/rviz03.png" width=500>
</p>

На самом деле, расскажем вам по секрету не всегда обязательно открывать второй терминал для запуска ```rviz```, а для этого можно воспользоваться строчкой:

```
<node name="rviz" pkg="rviz" type="rviz" args="-d /path/to/your/rvizconfigfile"/>
```

Обратите внимание, что ```"path/to/your/rvizconfigfile" ``` это путь к конфигу rviz, который вы должны создать. Если у вас пока нет такого файла, то уберите ```args```, как аргумент. Но давайте все равно разберемся, как нам сохранить конфигурационный файл. 


После того, как вы все настроили, сохраняем наш конфиг в папку ```rviz``` внутри ```kitty_vision```. Для этого нужно ее заранее создать и выполнить `Ctrl+Shift+s`. Названия желательно делать понятные каждому, поэтому предлагаем оставить такое: `start_ydlidar.rviz`

Отлично! С этим мы справились.

>🦾	Теперь предлагаем создать ```launch``` файл, который будет запускать у нас и драйвер для лидара и ```rviz```. Попробуйте это сделать самостоятельно, а потом загляните в решение. Ваше решение поместите в папку ```kitty_vision->launch->full->full_start_lidar.launch```

<details>
<summary>
👾👾👾 Внимание ответ!
</summary>

```xml
<launch>
 <!-- Start ydlidar -->
 <include file="$(find kitty_package)kitty_vision/launch/drivers/start_ydlidar_x2.launch" />

 <!-- Start rviz -->
 <node name="rviz" pkg="rviz" type="rviz" args="-d /path/to/your/rvizconfigfile"/>
</launch>
```

</details>


>🦾 Для закрепления создайте опцию для вашего ```full_start_lidar.launch```, в котором ```rviz``` будет запускаться только в том случае, если `<arg>` принимает значение `True`. В таком случае ваш `launch` с лидаром будет запускаться, как:

```bash
roslaunch kitty_package full_start_lidar.launch rviz:=True
```

или

```bash
roslaunch kitty_package full_start_lidar.launch rviz:=False
```
<p align="center">
<img src="../assets/lesson_02/terminal01.png" width=500>
</p>

<p align="center">
<img src="../assets/lesson_02/terminal02.png" width=500>
</p>

Параметр  `support_motor_dtr` используется в драйвере YDLIDAR для определения того, поддерживает ли устройство управление направлением вращения двигателя через вывод DTR (Data Terminal Ready). По умолчанию, в `launch` лидара, который мы с вами запускаем, этот параметр принимает значение `False` , давайте откроем терминал и передадим ему значение `True`

```bash
rostopic pub -1 /support_motor_dtr std_msgs/Bool "data: true"
```

Давайте проверим, что комманды таким образом доходят, для этого создадим ```kitty_vision->scripts->check_rostopic.py```

```python
#!/usr/bin/env python
import rospy
from std_msgs.msg import Bool

def callback(data):
    rospy.loginfo("Received %s", data.data)

rospy.init_node('check_rostopic')
rospy.Subscriber("/support_motor_dtr", Bool, callback)
rospy.spin()
```

Далее в одном терминале выполните команду, а в другом запустите ваш скрипт. Тогда в первом терминале должно будет появится сообщение:

``` bash
INFO: Received True
```

<p align="center">
<img src="../assets/lesson_02/terminal03.png" width=500>
</p>

>🦾 Напишите программу, в котором функция `send_stop_command()` создает Publisher на топик /`support_motor_dtr`. Затем она создает сообщение типа `Bool` с полем `data`, установленным в `False`. И наконец, публикует сообщение. Объект `rospy.Rate` обеспечивает отправку сообщения со скоростью 10 Гц.

<details>
<summary>
👾👾👾 Внимание ответ!
</summary>

```python
#!/usr/bin/env python

import rospy
from std_msgs.msg import Bool

def send_stop_command():
    # Publisher setup
    pub = rospy.Publisher('/support_motor_dtr', Bool, queue_size=10)
    rospy.init_node('support_motor_dtr_stopper', anonymous=True)
    rate = rospy.Rate(10) # 10hz
    msg = Bool()
    msg.data = False
    rospy.loginfo("Sending stop command...")
    pub.publish(msg)
    rate.sleep()

if __name__ == '__main__':
    try:
        send_stop_command()
    except rospy.ROSInterruptException:
        pass
```
</details>