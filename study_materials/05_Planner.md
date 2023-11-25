```
<include file="$(find kitty_software)/launch/drivers/move_base.launch">
            <arg name="folder_name" value="move_base" />
            <arg name="cmd_topic" value="nav_vel" />
            <arg name="teb_config" value="teb_local_planner" />
</include>
```

```
#!/usr/bin/env python3
from logging import shutdown
import rospy
from kitty_msgs.msg import KittyState
from geometry_msgs.msg import Twist
from math import pi

def callback(msg):
    # m/s to rev/min
    my_msg.rotation_speed = msg.linear.x * rev_convertation
    my_msg.angle_steering = msg.angular.z * deg_convertation
    my_msg.front_right_steering_angle = 0
    limit_speed = 350
    if my_msg.rotation_speed != 0:
        if my_msg.rotation_speed > 0 and my_msg.rotation_speed < limit_speed:
            my_msg.rotation_speed = limit_speed
        elif my_msg.rotation_speed < 0 and my_msg.rotation_speed > -limit_speed:
            my_msg.rotation_speed = -limit_speed
    rospy.loginfo("Linear is %.2f \n angular is %.2f", my_msg.rotation_speed, my_msg.angle_steering)

# rospy.spin()


rospy.init_node('cmd_publisher')
pub = rospy.Publisher('set_speed', KittyState, queue_size=10)
rospy.Subscriber('/kitty/cmd_vel', Twist, callback, queue_size=10)

rate = rospy.Rate(5)
my_msg = KittyState()
wheel_d = 0.065 
rev_convertation = 60 / (pi*wheel_d)
deg_convertation = -180/pi

while not rospy.is_shutdown():  
    pub.publish(my_msg)
    rate.sleep()



# rospy.init_node('talker')
# pub = rospy.Publisher('my_chat_topic', String, queue_size=10)
# rate = rospy.Rate(1)

# def start_talker():
#     msg = String()
#     while not rospy.is_shutdown():
#         hello_str = "hi =) %s" % rospy.get_time()
#         rospy.loginfo(hello_str)

#         msg.data = hello_str
#         pub.publish(msg)

#         rate.sleep()

# try:
#     start_talker()
# except (rospy.ROSInterruptException, KeyboardInterrupt):
#     rospy.logerr('Exception catched')

```
