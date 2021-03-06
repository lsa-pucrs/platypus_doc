
=========================
GAMS/MADARA Compilation
=========================
In this page, we will provide you a quick start to compile GAMS/MADARA for Linux Desktop, more specifically all scripts was tested with Linux Ubuntu 16.04.


It is expected that all dependencies be resolved automatically by scripts. You have to be aware and look for errors of download failures, which the script can't handle.

To download GAMS/MADARA, just run:

.. code-block:: bash

	$ export GAMS_ROOT=$HOME/gams
	$ export CORES=4
	$ git clone -b master --single-branch https://github.com/jredmondson/gams $GAMS_ROOT

After that, you should compile the following one of the following commands.

Build C++ with Tests

.. code-block:: bash

	$ $GAMS_ROOT/scripts/linux/base_build.sh prereqs ace madara gams vrep tests

Build C++ with Tests and Java support

.. code-block:: bash

	$ $GAMS_ROOT/scripts/linux/base_build.sh prereqs ace madara gams vrep tests java

Build C++ with Tests and Android support

.. code-block:: bash

	$ $GAMS_ROOT/scripts/linux/base_build.sh prereqs ace madara gams vrep tests android

Build C++ with ROS support

.. code-block:: bash

	$ $GAMS_ROOT/scripts/linux/base_build.sh prereqs ace madara gams ros


When the compilation finishes, you should see in folder you called the above script the following folders:
	
.. code-block:: bash

   . 
   ├── ace  
   ├── gams  
   ├── madara  
   └── vrep  

The script below show several environment variables that must be configured to properly run and compile your gams/madara projects.

.. code-block:: bash

	$ export PROJECT_HOME=$HOME/gamsProjects
	$ export ACE_ROOT=$HOME/ace/ACE_wrappers
	$ export MADARA_ROOT=$HOME/madara
	$ export GAMS_ROOT=$HOME/gams
	$ export VREP_ROOT=$HOME/vrep
	$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ACE_ROOT/lib:$MADARA_ROOT/lib:$GAMS_ROOT/lib:$VREP_ROOT
	$ export PATH=$PATH:$ACE_ROOT/bin:$MADARA_ROOT/bin:$GAMS_ROOT/bin:
	$ export CORES=4
	$ export NDK=$HOME/android_arm_tools
	$ export LOCAL_CROSS_PREFIX=$NDK/bin/arm-linux-androideabi-
	$ export SYSROOT=$NDK/sysroot
	$ export PATH=$PATH:$VREP_ROOT

NOTE: Do not combine several arguments into ``base_build.sh``, otherwise you will suffer with anomalous compilation effects.


JAVA SUPPORT
------------

To allow the java compilation, you should install JAVA JDK (suggested JAVA 8) and configure the environment variable named ``JAVA_HOME``. To do that, just run the following commands:

.. code-block:: bash

	$ sudo add-apt-repository ppa:webupd8team/java
	$ sudo apt-get update
	$ sudo apt-get install oracle-java8-set-default
	$ export JAVA_HOME=/usr/lib/jvm/java-8-oracle
	
	
If you get an error in ``JavaPlatform``, you should edit ``javaPlatForm.cpp`` and ``JavaPlatform.h`` so the signature of JavaPlatform::move look likes this in ``JavaPlatform.h``:

.. code-block:: c++

	int move (const pose::Position & position, double epsilon = 0.1) override;

and looks this in ``JavaPlatform.cpp``:

.. code-block:: c++

	int gams::platforms::JavaPlatform::move (const pose::Position & position, double epsilon) 


ANDROID SUPPORT
---------------

If you are going to use Android in your aplications, you should download Android NDK. Extract NDK files into a folder and run the following shell script command inside of that folder.

.. code-block:: bash

	$ ./build/tools/make-standalone-toolchain.sh --toolchain=arm-linux-androideabi-4.9 --arch=arm --platform=android-14 --install-dir=../android_arm_tools

After the process is over, ``../android_arm_tools`` should be automatically created. After that, verify if the following environment variables are pointing to correct paths: ``NDK``, ``LOCAL_CROSS_PREFIX``, ``SYSROOT``.

.. code-block:: bash

	$ export NDK=\$HOME/bin/android_arm_tools
	$ export LOCAL_CROSS_PREFIX=\$NDK/bin/arm-linux-androideabi-
	$ export SYSROOT=\$NDK/sysroot




Minor fixes: UniformRandomEdgeCoverage
-----------

You should configure the variable ``initialized_`` to be true in the method ``generate_new_position``:


.. code-block:: bash

	void
	gams::algorithms::area_coverage::UniformRandomEdgeCoverage::generate_new_position (void)
	{
	  if (platform_ && *platform_->get_platform_status ()->movement_available)
	  {
		....... 
	    initialized_ = true;
	  }
	}

minor fixes: AlgorithmFactoryRepository.cpp
-------------------------------------------

You should change the following code from:

.. code-block:: bash

    add (aliases, new area_coverage::PriorityWeightedRandomAreaCoverageFactory ());

    add (aliases, new area_coverage::SnakeAreaCoverageFactory ());

to:

.. code-block:: bash

  add (aliases, new area_coverage::PriorityWeightedRandomAreaCoverageFactory ());

    aliases.resize (2);
    aliases[0] = "snake";
    aliases[1] = "sac";

    add (aliases, new area_coverage::SnakeAreaCoverageFactory ());
