Ram usage seems to increases over TIME. A statement like "you need xx RAM" is incomplete and clearly misleading. Additional details are required to bracket the memory status, 
1. after how long? 
2. which system (Win(version?)/Mac/linux(distro?), 
3. Docker(yes/no), 
4. processor(intel/amd/arm 32/64bit 
5. parameters in config.json 
6. kernel configuration.

My understanding is that at some point in time you need to restart the node on any system. How often? I cannot speak for other systems, Again based only on experience (after ~4months), my take is that it is safe to restart the RPI3 every 3-4 days, RAM usage after 3-4 days is about 1-1.2gb (zram gives you ~2gb, without physical swap). 
What does it mean restarting the node every, say 3 days? Every month the node restart 10times, each time ~15-30 second (to be safe) to complete the process. It means your RPI3 node will be down max ~5 min every month. I let people enjoying statistics to find out what is the probability that your baking privilege is lost in those 5 min with 1-10-100.. rolls, assuming you have no preview of the incoming baking allocations (not entirely true, I think). 
* Worth the risk? 
* Is it cost effective to have a dedicate more powerful rig for a Tezos node? 
* what about spreading rolls over multiple tiny machines?

***
Here is a small script (untested) to check the current memory usage, if it exceeds a certain threshold (in the script 1400MB), the node will restart automatically. The script should be used with crontab, running automatically, say every hour).

    #! /bin/bash

    memt=$(grep -m 1 "MemTotal" /proc/meminfo |awk '{print $2 }')
    memf=$(grep -m 1 "MemFree" /proc/meminfo |awk '{print $2 }')
    memb=$(grep -m 1 "Buffers" /proc/meminfo |awk '{print $2 }')
    memc=$(grep -m 1 "Cached" /proc/meminfo |awk '{print $2 }')
    mems=$(grep -m 1 "Slab" /proc/meminfo |awk '{print $2 }')
    memu="$((($memt-$memf-$memb-$memc-$mems)/1024))"
    echo $memt
    echo $memf
    echo $memb
    echo $memc
    echo $memu

    swapt=$(grep -m 1 "SwapTotal" /proc/meminfo |awk '{print $2 }')
    swapf=$(grep -m 1 "SwapFree" /proc/meminfo |awk '{print $2 }')
    swapu="$((($swapt-$swapf)/1024))"
    echo $swapt
    echo $swapf
    echo $swapu

    memtu=$((memu+swapu))

    if [$memtu -gt 1400]
    then

    kill -9 $(pgrep -f tezos-client)
    sleep 4
    kill -9 $(pgrep -f tezos-node)

    D=$(ls  /tmp/.X11-unix|tr 'X' ':')

    sleep 15
    DISPLAY=$D xterm -geometry 120x40+0+0 -hold -e bash -c "cpulimit -l 80 /home/tzuser/tezos/tezos-node run"&
    sleep 60
    DISPLAY=$D xterm -geometry 120x40+200+400 -hold -e bash -c "/home/tzuser/tezos/tezos-client bootstrapped"&

    fi
