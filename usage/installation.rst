============
Installation
============

raspberry
=========
NImbus userland has to be compiled on a raspberry pi!

CMake and git
-------------

Install cmake and git

.. code-block:: shell

    sudo apt-get install cmake git


Build dependencies
------------------

Install userland dependencies:

- libi2c-dev
- libcurl
- libmicrohttpd
- libjsoncpp
- libargtable
- libhiredis

.. code-block:: shell

    sudo apt-get install libcurl4-openssl-dev libmicrohttpd-dev libjsoncpp-dev libargtable2-dev libhiredis-dev libi2c-dev libssl-dev libboost-all-dev

Clone and build nimbus
----------------------

.. code-block:: shell

    git clone ssh://git@gitlab.pieye.org:2000/pieye/nimbus-userland.gits
    cd NimbusUserland
    mkdir build
    cd build/
    cmake -DCMAKE_BUILD_TYPE=Release ..
    make
    make install
    cpack