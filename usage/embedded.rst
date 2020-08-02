===========================
Working on the Raspberry Pi
===========================

.. _EmbeddedDetailed:

This chapter contains all details about working with the Nimbus directly on the Raspberry Pi,
such as:

- acquiring and accessing data
- change settings like exposure time and resolution
- adding a custom processing statge to the processing pipeline

You can either use the nimbus interface in a synchronous or in an asynchronous manner.

Synchronous vs. Asynchronous Interface
======================================
Synchronous Interface
---------------------
In computer science and network technology, synchronous communication is a mode of communication in which the communication partners (processes) always synchronize when sending or receiving data.
They wait (block) until the communication is completed. 
Waiting both when sending and receiving data corresponds to a rendezvous of the two processes involved.

Such a synchronous interface can be implemented with the C-Interface described below.
It is not recommended to perform time-consuming calculations directly in the C-interface, since this reduces the frame rate due to the blocked main process.

In such a case the use of the ROS (Robot Operating System) driver or a similar implemenation is recommended.
The ROS driver implements the C-interface and forms an abstraction of it.
If the frame rate limit is set high enough, all new data will be provided directly in ROS.
However, the preprocessing stage is no longer blocked when the data is available in ROS.



Asynchronous Interface
----------------------
In computer science and network technology, asynchronous communication refers to a mode of communication in which the sending and receiving of data takes place with a time delay and without blocking the process.
The Pyhton interface (nimbus-python) represents such an asynchronous interface.
The same applies to the web interface (nimbus-web). Both receive their data via websockets and settings are changed via JSON RPC.


Configuring nimbus
==================
In the case of the nimbusServer, setting changes are made via the JSON RPC interface.
In `nimbus-python <https://github.com/pieye/nimbus-python>`_ settings like exposure are already available as function calls.
In addition, it is possible to change the exposure in the web browser and to observe the result directly.
An implemenation for the C-Interface is shown below.


Adding a custom processing stage
================================

C-Interface
-----------
After having built and installed nimbus-userland (make install), all libraries are installed under /usr/local/lib, all headers are installed under /usr/local/include
The header nimbusPreprocessInterface.h contains a low level c interface to libnimbus-preprocessing.so. This is useful when you want to integrate nimbus with external applications.

Example
-------
The following snippet demonstrates the interface

.. code-block:: c

    #include <stdio.h>
    #include <unistd.h>
    #include "nimbusPreprocessInterface.h"

    int g_cnt;

    void imageCallback(void* unused0, void* img, void* unused1) {
        uint16_t* ampl = nimbus_seq_get_amplitude(img);
        uint8_t* conf = nimbus_seq_get_confidence(img);
        ImgHeader_t* header = nimbus_seq_get_header(img);
        printf("got an image\n");
        g_cnt++;
        nimbus_seq_del(img); //<- free the image pointer this call is necessary to return img resource to nimbus
    }

    int main(int argc, char** argv) {
    int i;

    printf("nimbus-userland version: %s\n", nimbus_get_version());

    if (nimbus_preproc_init()) {
        nimbus_preproc_seq_cb(imageCallback);
        g_cnt = 0;
        while (g_cnt < 100) {
            usleep(10000);
        }
    }
    else {
        printf("init nimbus failed!\n");
    }
        return 0;
    }


In case you want to adjust the auto exposure we provide the following interface:

.. code-block:: c

    AutoExposureParams_t m_params;
    m_params.amplitude     = amplitude;     //0.0 - ~1500.0
    m_params.max_exposure  = max_exposure;  //0-32767	
    m_params.hdr_factor    = hdr_factor;    //0.0 - 1.0
    m_params.exposure_mode = exposure_mode; //MANUAL = 0, MANUAL_HDR  = 1, AUTO = 2, AUTO_HDR = 3
    bool set_exposure = nimbus_autoexposure_set_params(&m_params);


We assume that the previous snipped is stored in a file called main.cpp.
You can than build this file with gcc main.cpp -o myApp -lnimbus-preprocessing and run it ./myApp.
If nimbus-proprocessing lib cannot be found, either add -L/usr/local/lib to gcc options or run sudo ldconfig.
