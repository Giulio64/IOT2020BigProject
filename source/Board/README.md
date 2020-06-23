#How to run
- Go into the folder "Beacon" and compile the code with the command "make".
- Go into the folder "RIOT/examples/gnrc_border_router/" and compile the code with the command "make BOARD=nrf52dk".
- Open iot-lab and start an experiment with two nrf52dk boards and one A8 board at the Saclay site.
- Flash one of the two nrf52dk boards with the beacon executable and the other with the gnrc_border_router.
- From the terminal ssh into your iot-lab account at the Saclay site.
- Issue the command "ip -6 route", it should show which tap interfaces and which ipv6 addresses are occupied;
- Issue the command "sudo ethos_uhcpd.py nrf52dk-'ID' tap'N' 'ADDR'/64" where 'ID' is the identifier of one of the two nrf52dk board 'N' is the number of a tap interface that is not already in use and 'ADDR' is a free ipv6 address.
- NOTE: sometimes this does not work properly at the first start, it can be fixed by restarting the board with the command "restart".
- Issue the command "ble adv" to make the board start advertise itself over Bluetooth.
- Ssh again into iot-lab from another terminal and issue the command "ssh root@node-a8-'ID'" where 'ID' is the identifier of the A8 board.
- Create a "config.conf" file with the following content:

        # add some debug output
        trace_output protocol

        # listen for MQTT-SN traffic on UDP port 1885
        listener 1885 INADDR_ANY mqtts
        ipv6 true

        # listen to MQTT connections on tcp port 1886
        listener 1886 INADDR_ANY
        ipv6 true
- Issue the command "ip -6 -o addr show eth0" to get the address of the board.
- Issue the command "broker_mqtts config.conf" to start the broker.
- Ssh again into iot-lab from another terminal and issue the command "nc nrf52dk-'ID' 20000" where 'ID' is the identifier of the second nrf52dk board.
- Issue the command "ble scan" and then "ble connect 0" to connect to the border router.
- A global address should be assigned to the board you can check this with the command "ifconfig".
- NOTE: it may take a while, continue to issue the command "ifconfig" until it has a global address before moving to the next step.
- Issue the command "start 'ADDR'" where 'ADDR' is the address of the A8 board. It should connect to the broker and start publishing and it should also start to broadcast the beacon signal.