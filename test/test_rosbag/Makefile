# The all target does the heavy lifting, creating the build directory and
# invoking CMake
all:
	@mkdir -p build
	-mkdir -p bin
	cd build && cmake -DCMAKE_TOOLCHAIN_FILE=`rospack find rosbuild`/rostoolchain.cmake ..
ifneq ($(MAKE),)
	cd build && $(MAKE) $(ROS_PARALLEL_JOBS)
else
	cd build && $(MAKE) $(ROS_PARALLEL_JOBS)
endif

PACKAGE_NAME=$(shell basename $(PWD))

# The clean target blows everything away
# It also removes auto-generated message/service code directories, 
# to handle the case where the original .msg/.srv file has been removed,
# and thus CMake no longer knows about it.
clean-msg-only:
	rm -rf msg/cpp msg/lisp msg/oct msg/java srv/cpp srv/lisp srv/oct srv/java src/$(PACKAGE_NAME)/msg src/$(PACKAGE_NAME)/srv

clean:
	-cd build && $(MAKE) clean
	rm -rf msg/cpp msg/lisp msg/oct msg/java srv/cpp srv/lisp srv/oct srv/java src/$(PACKAGE_NAME)/msg src/$(PACKAGE_NAME)/srv
	rm -rf build
	rm -f .build-version
	rm -f test/*.bag
	rm -rf msg
	rm -rf msg_gen

# All other targets are just passed through
test: all
	./generate_data; if cd build && $(MAKE) -k $@; then $(MAKE) test-results; else $(MAKE) test-results && exit 1; fi
tests: all
	cd build && $(MAKE) $@
test-future: all
	cd build && $(MAKE) -k $@
gcoverage: all
	cd build && $(MAKE) $@

include $(shell rospack find mk)/buildtest.mk
