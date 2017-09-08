# azure-iot-sdk-c-20170825 
azure iot c sdk 20170825

Because of the change of the certificates, we change one of the sample in iothub_client to test the different certs files.
Change the iothub_client samples iothub_client_sample_mqtt to make you choose which cert array you want, old or new.
You can build the whole sdk using the initial method and then enter into the iothub_client_sample_mqtt and run the iothub_client_sample_mqtt

certs.c include two arrays :
certificates_new is the CA array in certs folder in 2017-03-10 sdk version and later.
certificates_old is the CA array in certs folder in 2017-02-24 sdk version and before.

You should create iothub and device on Azure and input your own iothub ConnectionString in the iothub_client_sample_mqtt.c.

/*build the whole sdk*/
cd azure-iot-sdk-c-20170825
mkdir build
cd build
cmake ..
cmake --build .

/*run the sample*/
cd build/iothub_client/samples/iothub_client_sample_mqtt
./iothub_client_sample_mqtt

You can input your choose of the certs version.

