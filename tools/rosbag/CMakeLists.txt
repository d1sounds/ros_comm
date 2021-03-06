if(ROSBUILD)
  include(rosbuild.cmake OPTIONAL)
  return()
endif()
cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

set(ROS_BUILD_TYPE RelWithDebInfo)
#set(ROS_BUILD_TYPE Debug)

rosbuild_init()

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

SET(CMAKE_SKIP_BUILD_RPATH FALSE)
SET(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE) 
SET(CMAKE_INSTALL_RPATH "${PROJECT_SOURCE_DIR}/lib")
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

# Support large bags (>2GB) on 32-bit systems
add_definitions(-D_FILE_OFFSET_BITS=64)

rosbuild_add_library(rosbag 
	src/bag.cpp src/buffer.cpp src/bz2_stream.cpp src/chunked_file.cpp src/message_instance.cpp src/player.cpp 
	src/query.cpp src/recorder.cpp src/stream.cpp src/time_translator.cpp src/uncompressed_stream.cpp src/view.cpp)
target_link_libraries(rosbag bz2)
rosbuild_link_boost(rosbag regex program_options)

rosbuild_add_executable(record src/record.cpp)
target_link_libraries(record rosbag)

rosbuild_add_executable(play src/play.cpp)
target_link_libraries(play rosbag)

#rosbuild_add_executable(example_write examples/write.cpp)
#target_link_libraries(example_write rosbag)

INSTALL(TARGETS record play
        RUNTIME DESTINATION $ENV{ROS_ROOT}/bin)

rosbuild_download_test_data(http://pr.willowgarage.com/data/rosbag/test_indexed_1.2.bag test/test_indexed_1.2.bag 523cc0deb66269c84be4a7c605ff5eb3)
rosbuild_download_test_data(http://pr.willowgarage.com/data/rosbag/chatter_50hz.bag test/chatter_50hz.bag 00844db3729b1b5f34e767dc691bd531)
rosbuild_download_test_data(http://pr.willowgarage.com/data/rosbag/test_future_version_2.1.bag test/test_future_version_2.1.bag ddcb5e8711b16514eb640330ed93c332)

rosbuild_add_pyunit(test/test_bag.py)

rosbuild_add_gtest(test_bag test/test_bag.cpp)
target_link_libraries(test_bag rosbag)

rosbuild_add_rostest(test/rosbag_play.test)
rosbuild_add_rostest(test/play_play.test)
rosbuild_add_rostest(test/latched_pub.test)
rosbuild_add_rostest(test/latched_sub.test)
rosbuild_add_rostest(test/record_two_publishers.test)
rosbuild_add_rostest(test/record_one_publisher_two_topics.test)
add_dependencies(rostest_test_latched_sub.test rostest_test_latched_pub.test)

rosbuild_add_executable(double_pub test/double_pub.cpp)
rosbuild_declare_test(double_pub)
