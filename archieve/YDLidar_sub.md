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