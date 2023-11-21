<!-- omit from toc --> 
# Картографирование и локализация роботов

- [Едем дальше?](#едем-дальше)
- [Поехали!](#поехали)


## Едем дальше?

Помните еще на первом занятие мы с вами запускали `launch` в котором подгружалась модель нашего робота в каком-то мире? Пришло время разобраться с тем, что, на самом деле мы запускали.

<p align="center">
    <img src=../assets/lesson_03/turtle_world.png width=400/>
</p>

Итак, поехали! Чтобы посмотреть их начинку воспользуемся утилитой `roscat`! Например, залезем в `turtlebot3_world.launch`:

```bash
roscat turtlebot3_gazebo turtlebot3_world.launch
```

В результате получим:

```xml
<launch>
  <arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>
  <arg name="x_pos" default="-2.0"/>
  <arg name="y_pos" default="-0.5"/>
  <arg name="z_pos" default="0.0"/>

  <include file="$(find gazebo_ros)/launch/empty_world.launch">
    <arg name="world_name" value="$(find turtlebot3_gazebo)/worlds/turtlebot3_world.world"/>
    <arg name="paused" value="false"/>
    <arg name="use_sim_time" value="true"/>
    <arg name="gui" value="true"/>
    <arg name="headless" value="false"/>
    <arg name="debug" value="false"/>
  </include>

  <param name="robot_description" command="$(find xacro)/xacro --inorder $(find turtlebot3_description)/urdf/turtlebot3_$(arg model).urdf.xacro" />

  <node pkg="gazebo_ros" type="spawn_model" name="spawn_urdf"  args="-urdf -model turtlebot3_$(arg model) -x $(arg x_pos) -y $(arg y_pos) -z $(arg z_pos) -param robot_description" />
</launch>
```

Хмм, а тут немало всего, но мы на прошлом уроке уже познакомились с тегами  `arg`, `node` и `param`!

А ещё, мы нашли, почему он всё время требует переменную окружения `TURTLEBOT3_MODEL`! Вот и первый момент, который хочется исправить в этих скриптах, чтобы его не задавать!

Выполните в терминале из папки `home` команду:

```bash
gedit ./.bashrc
```
У вас откроется файл, в котором мы добавляли наше рабочее пространство, в самой последней строчке запишите строчку `export TURTLEBOT3_MODEL="burger"`. Именно это избавит нас от постоянного написания модели робота при запуске `launch` файлов в `turtlebot3`

Кстати, мы вам не рассказали, но роботы у нас тоже бывают разные, в них отличаются названия. Есть: `waffle, burger и waffle pi` - выберите ту, которая вам больше нравится.

<p align="center">
    <img src=../assets/lesson_03/turtle_family.png width=600/>
</p>

Давайте теперь посмотрим, что у нас вообще есть в пакете `turtlebot3_gazebo`, для этого сделаем команду:

```bash
roscd turtlebot3_gazebo
code .
```
Так, мы откроем пакет в `VS Code`, а в папке `launch` можем найти кучу файлов для запуска робота на различных площадках:

<p align="center">
    <img src=../assets/lesson_03/gazebo.png width=200/>
</p>

Наиболее интересным, на наш взгляд является файл `turtlebot3_house.launch`. Давайте скопируем его в наш пакет, для этого создадим папку `world` и поместим его туда, сохраня название. Давайте запустим !

```bash
roslaunch kitty_package turtlebot3_house.launch
```

<p align="center">
    <img src=../assets/lesson_03/house.png width=600/>
</p>

Дальше давайте исследуем пакет `turtlebot3_slam` и посмотрим `turtlebot3_slam.launch`

```bash
<launch>
    <!-- Arguments -->
    <arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>
    <arg name="configuration_basename" default="turtlebot3_lds_2d.lua"/>
    <arg name="open_rviz" default="true"/>
    <arg name="set_base_frame" default="base_footprint"/>
    <arg name="set_odom_frame" default="odom"/>
    <arg name="set_map_frame"  default="map"/>
    
    <!-- TurtleBot3 -->
    <include file="$(find turtlebot3_bringup)/launch/turtlebot3_remote.launch">
      <arg name="model" value="$(arg model)" />
    </include>
  
    <node pkg="gmapping" type="slam_gmapping" name="turtlebot3_slam_gmapping" output="screen">
      <param name="base_frame" value="$(arg set_base_frame)"/>
      <param name="odom_frame" value="$(arg set_odom_frame)"/>
      <param name="map_frame"  value="$(arg set_map_frame)"/>
      <rosparam command="load" file="$(find turtlebot3_slam)/config/gmapping_params.yaml" />
    </node>
  
    <!-- rviz -->
    <group if="$(arg open_rviz)"> 
      <node pkg="rviz" type="rviz" name="rviz" required="true"
            args="-d $(find turtlebot3_slam)/rviz/turtlebot3_gmapping.rviz"/>
    </group>
  </launch>
```

<p align="center">
    <img src=../assets/lesson_03/rviz_setting.png width=200/>
</p>

Давайте скопируем его в наш пакет, для этого в папке `kitty_software` сохраним его. А далее запустим (прошлый наш launch вырубать не надо):

```bash
roslaunch kitty_package turtlebot3_slam.launch
```
<p align="center">
    <img src=../assets/lesson_03/rviz_house.png width=600/>
</p>

>🦾	Мы добрались до первого задания. Вы не заметили, что при запуске `Gazebo` у нас начинает подлагивать компьютер? Давайте отключим его отображение, а для этого разберемся с помощью какого аргумента мы можем это отключить в `turtlebot3_house.launch`.

>🦾	Мы говорили, что менять аргументы в исходном файле это не слишком хорошая практика, поэтому давайте в начале `turtlebot3_house.launch` добавим тег `arg` с названием `gazebo`, который будет принимать значения либо `True` или `False`, а само значение перекидывать в найденный вами в прошлом задание аргумент `<include file="$(find gazebo_ros)/launch/empty_world.launch">`

##  Поехали!

На прошлом уроке мы с вами создавали файл, который позволяет управлять нашим роботом с компьютера. Назвали мы его, как `my_joy_launch.launch` или как-то похоже

>🦾 Новое задание. Давайте объедим три файла в один (тот, который запускает симулятор мира робота, построение карты и управление с джойстика) - мы ведь решили уже, что не очень удобно запускать по 20 файлов одновременно. Создадим в `kitty_software` файл `full_turtle_joy_mapping.launch`

Справились? Запускаем!

```bash
roslaunch kitty_package full_turtle_joy_mapping.launch
```

Теперь мы можем кататься по помещению и строить карту - пробуем!

<p align="center">
    <img src=../assets/lesson_03/house_mapping.png width=600/>
</p>