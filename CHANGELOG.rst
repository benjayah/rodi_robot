^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Changelog for package rodi_robot
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Forthcoming
-----------
* rodi_robot: Reduce the rospy rate to 1 Hz
  The rospy rate for the main loop is 2 Hz, but RoDI sometimes can't cope
  with that since the sonar values are queried on each iteration, causing
  too much stress to RoDI and making a lot of HTTP connections to timeout.
  Change to 1 Hz instead which should give a sonar lecture every 1 second.
  That should be enough for most needs and reduces the number of timeouts.
* rodi_robot: Only publish sonar data if see() succeeds
  The see() function returns None if something goes wrong,
  so check the return value before attempting to use it.
* transport: Change log level for HTTP request errors
  We already know that RoDI sometimes can't cope with the request rate
  making some of these to timeout. So instead of printing all these to
  the standard error, print them as a debug information instead.
  Since now is only printed for debug purposes, also add the used URL.
* transport: Reduce the httplib timeout to 0.5 seconds
  Under a heavy load, RoDI isn't able to process the HTTP requests and the
  connections timeouts. But waiting for a second is too much and makes the
  node to be unresponsive for a second, until the HTTP requests timeouts.
  If RoDI will not be able to process the request, it's better to make the
  request to timeout early to allow the next command to be processed.
* transport: Don't return an empty string if RoDI didn't send data
  If RoDI faild to send a HTTP response payload, return None to the
  caller instead of an empty string.
* transport: Return None instead of 0 on HTTP request exception
  If an exception is raised when doing a HTTP request, return None instead
  of 0, since the former is a more standard python convention.
* rodi_robot: Limit sonar range to 1 meter
  Even while the HC-SR04 ultrasonic sensors used in RoDI is able to measure
  distances up to 400 centimeters, the firmware limits to 100 centimeters.
* Merge pull request `#1 <https://github.com/benjayah/rodi_robot/issues/1>`_ from rodibot/make-package-more-ros-pythonic
  Make package more ros pythonic
* rodi_robot: Add timeout for cmd_vel
* transport: Add timeout to the transport library
* rodi_node: Separate RoDI logic into it's own class
* rodi_node: Separate node from logic
  In order to make it more 'ROS-compliant' I've separated it into a node
  and the 'logic' part.
* rodi_robot: Use catkin v2 for the ROS package
  This is more for style than functionality
* Add .gitignore
* Change package name to rodi_robot
  The package name has been changed to rodi_robot to be more aligned with
  the ROS package naming advice (http://www.ros.org/reps/rep-0144.html)
  and to avoid using the "ws" suffix, since in ROS this usually refers to
  a workspace so is confusing to use it in a package name.
  Also change the rodi_ws_node to rodi_robot_node for consistency.
  "There are only two hard things in Computer Science: cache invalidation
  and naming things." -- Phil Karlton
* rodi_ws_node: fix pep8 issues
  This patch fixes the following issues reported by pep8:
  src/rodi_ws_node.py:8:1: E302 expected 2 blank lines, found 1
  src/rodi_ws_node.py:37:25: E261 at least two spaces before inline comment
  src/rodi_ws_node.py:40:29: E261 at least two spaces before inline comment
  src/rodi_ws_node.py:42:31: E703 statement ends with a semicolon
  src/rodi_ws_node.py:43:26: E703 statement ends with a semicolon
  src/rodi_ws_node.py:44:26: E703 statement ends with a semicolon
* transport: fix pep8 issues
  This patch fixes the following issues reported by pep8:
  src/transport.py:7:1: E302 expected 2 blank lines, found 1
  src/transport.py:14:41: E231 missing whitespace after ','
* rodi_ws_node: rename cmd_vel topic callback
  The cmd_vel topic callback is called "callback", which is a really generic
  name. So, rename it to "cmd_vel_cb" that is easier to read and understand.
* rodi_ws_node: move init_node to the correct place
  The rospy.init_node() happens even before the function definitions but is
  more sensible to do it where all the other top-level code is executed.
* rodi_ws_node: pass the transport as a callback argument
  Instead of defining the transport as a global variable, pass it to the
  callback when subscribing to the cmd_vel topic.
* rodi_ws_node: execute top-level code only if running in main scope
  A module can discover if is running in the main scope by checking whether
  its __name_\_ scope equals to __main_\_. The rodi_ws_node.py script is not
  supposed to be imported but still is a good practice to add this check.
* transport: configure RoDI hostname and port using rosparam server
  The RoDI default firmware uses 192.168.4.1 and 1234 as IP address and port
  respectively. But it could be useful to use different values in case of a
  custom firmware or when using the rodi-simulator.
  Allow configure these values as ROS parameters and also add a sample YAML.
* rodi_ws_node: publish sonar data in a /ultrasound topic
  The rodi-web API allows to get the RoDI sonar, so publish that
  information as a Range message in an /ultrasound topic.
* transport: add see command support
  The rodi-web API has a "see" command that returns the value read
  by the RoDI ultrasonic sensor. Add support for this command.
* transport: return the HTTP response body to caller
  Some rodi-web commands (i.e: see) return a value so the HTTP
  response body should be returned to the caller.
* transport: allow variable parameters for commands
  The number of arguments that are passed to the HTTP API is fixed to two,
  because move was the only supported command. Change it to support other
  commands that expect a different number of parameters.
* Add README file
* Initial commit
  This patch adds a ROS package for RoDI (Robot Didáctico Inalámbrico) to
  remote control the robot by using its default firmware web services API.
  For now only the move command is implemented, but the plan is to support
  as follow up also the sonar by publishing a Range message.
* Contributors: Gary Servin, Javier Martinez Canillas
