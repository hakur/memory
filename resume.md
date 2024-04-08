# <center>YuXing</center>

## Summary
* Gender：Male
* Age: 31
* Education：Chongqing Institute of Engineering 2011.09 - 2014.06
* Major：website design and build
* Phone：+86 173-4592-9030
* Email：yuxing951@hotmail.com
* Working Experience：10+ years
* github: github.com/hakur

## Skills:
1. Golang(mastered), Python, Devops, PHP, Javscript, Vue, Kubernetes, Redis, Mysql, Prometheus, Nats,
kubernetes operator development, AWS Cloud (short time)
2. Linux server management, Shell programming
3. Cloud native security software development, Ebpf linux kernel monitoring development

## English Skill
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Passed China public test Pretco-A ，read document is fine, communicate in general talk speed

## Working Experience
* #### Xiamen Fuyun Information Technology Co., Ltd.
    * Job Title: Senior golang engineer 2022.03 - now
    * Job Content
        * maintain golang/python/c project
        * maintain kuberentes clusters
        * write cloud native security scanner software, write ebpf linux kernel monitoringsoftware,
        * write open policy agent risk detector for kuberetes yaml and dockerfile
* #### Chengdu Lingfang Technology
    * Job Title: Senior golang engineer 2021.07 - 2021.09
    * Job Content
        * design automatic operated mysql and redis clusters based on kubernetes operator
        * plan golang developer’s job content as golang development team leader
        * make kubernetes lectures for java developer team. maintain kubernetes clusters.
* #### Beijing Litsoft Technology Co., Ltd.
    * Job Title: Senior golang engineer 2021.01 - 2021.06
    * Job Content
        * migrate Sensetime tech's Sense Experience X system to AWS cloud design multi region web service metrics collect to Cortex+Prometheus storage
        * research Nvidia CUDA VGPU share for multi tenants.
* #### Shenzhen Wise2c Technology
    * Job Title: Junior golang engineer 2018.12 - 2020.12
    * Job Content：
        * wisecloud platform is main product of Wise2c tech. participate wisecloud's design and develope wisecloud
        * maintain with kuberenetes installer breeze. keep lean and research new kuberenetes features.
* #### Chengdu Hermes-Sys Technology Co., Ltd.
    * Job Title: PHP team leader 2016.03 – 2018.12
    * Job Content
        * containerize all products, build CI/CD system for developers
        * write kuberentes device plugin for Nvidia GPU cloud and 3D render cloud on kuberentes
        * build web developer team and plan member's job content, host container skills share meetings
* #### Sichuan YiShuLoongTen Technology
    * Job Title: PHP team leader 2015.05 - 2016.02
    * Job Content: Lead the web team to complete web system development and containerize web system
* #### Chengdu RuiShinYuan Technology
    * Job Title: Primary PHP developer 2014.03 - 2014.12
    * Job Content: Develope web shop system
* #### Chengdu Sobey Digital Technology Co., Ltd.
    * Job Title: Primary PHP developer 2013.09 - 2014.02
    * Job Content: Develope Sobey's OTTCMS TV BOX web backen management system

## Projects
1. #### Cloud Armor Agent (Xiamen Fuyun Information Technology Co., Ltd.)
    * ##### Duration：2022.03 - now
    * ##### Description
        host machine and container security two in one product,contains web cloud console ui and agent side client, agent installed on every host machine or run as container, very low resource cost but provide many useful security bilities
    * ##### Responsibility
        1. finishied restruction and maintenance of image vulnerability scan (os package and program dev library) product independenty, it's called docker-safedog
        2. finishied design and build up of risk detect software independenty, used scan bad value in kubernetes yaml and dockerfile,it's called misconf, based on open policy agent execution engine
        3. fnishied golang ebpf kernel event monitoring and grab software, it's called sdbpfdog,it can grab kernel events from kernel space to user space then broast to all other subscribers connected on unix socket, subscribe bility base on c++98 code, it privode theese bilities:
            * watch every dir and file path write and motify event, support custom path and recusive listen depth
            * every dns request and reponse information record
            * protect a path's read/motify from any process and any user (include root user)
            * record every process's execution and thier exec args user and from which container
            * record every shell history without history shell command
            * record which process triggered syscall
        4. maintain container/image python scanner plugin named Doc
2. #### Meiyijia open cloud platform (Chengdu Lingfang Technology)
    * ##### Duration: 2021.07 - 2021.09
    * ##### Description
        A saas cloud platform under Dongguan Sugar & Wine Group Co Ltd Meiyijia chain supermaket. Carrying and running various backend programs for users, prodive third party identify access, mysql & redis database trusteeship service, and virtual machine create management service
    * ##### Responsibility
        1. deisgn and migrate kuberentes clusters to high available
        2. deisgn automatic operated mysql saas service on kuberntes, support MGR and Semi-Sync Mode
        3. design automatic operated redis saas serivce on kubernetes, support redis cluster mode, fronted accesss is predixy
3. #### SenseTime's Sense Experience X System (Beijing Litsoft Technology Co., Ltd.)
    * ##### Duration: 2021.01 - 2021.06
    * ##### Description
        used for human face recognize and security guard system, migrate this system to AWS Cloud
    * ##### Responsibility
        1. design multi regions prometheus metrics collect and long term storage, based on cortex clusters
        2. migrate Sense Experience X system to AWS Cloud, collect and organize document of Senese Experience X system, convert ansible install files to helm chart files, research Nvidia VGPU sharing (customed cuda so library)
4. #### Wisecloud cloud platform (Shenzhen Wise2c Technology)
    * ##### Duration: 2018.12 - 2020.12
    * ##### Description
        Enterprised Kubernetes finacial cloud platform, prodide tenant,devops,image repository,application orchestration,log collect tracing,monitoring and alert,grafana graph,istio policy,elastic autoscaling,ingress service etc...
    * ##### Responsibility
        1. design multi kuberentes clusters prometheus monitoring system based on cortex clsuter, write grafana graph to show metrics data, write prometheus alert rule for alert manager notifications
        2. daintain application orchestration service
        3. design and use filebeat collection container logs to elatisc search, show and search on kibana dashboard
        4. write web pages based on vue and javascript, coordinate web front developers and web backend developers
        5. design and write user message dispach system named msghub
        6. add windows node support for kuberentes cluster on wisecloud
        7. add nvidia GPU support for kubernetes cluster on wisecloud
        8. write pod instance count elastic scale service named auto-scaling
        9. maintain kubernetes cluster installer named breeeze
5. #### AI training cloud platform (Chengdu Hermes-Sys Technology Co., Ltd.)
    * ##### Duration: 2018.03 - 2018.10
    * ##### Description
        openstack gpu-passthrough based Nvidia GPU virutal machines cloud for tensorflow training
    * ##### Responsibility
        1. deisgn platform architecture, disscuss requirements, make develop plan, containerize all services
        2. implement docker gpu share, docker run 3d game with X11 desktop inside container
        3. install Nvidia GTX-970 and Tesla-M60 GPU on kubernetes for deferent training requirements
        4. use kubernetes client-go for custom schedule logic
        5. use drone.io as CI/CD system on kubernetes
        6. maintain kuberenetes clusters and maintain physical machines with operator

6. #### Paas cloud platform (Chengdu Hermes-Sys Technology Co., Ltd.)
    * ##### Duration: 2017.06 - 2018.03
    * ##### Description
        openstack gpu-passthrough based Nvidia GPU virtual machines cloud for 3D realtime backend render farm
    * ##### Responsibility
        1. deisgn platform architecture, disscuss requirements, make develop plan, containerize all
        services
        2. use consul as service auto register and discovery
        3. write web console backed based on PHP
        4. write openstack interact logic code base on golang, write cloud init tool named PassInit
        running when virtual machine start
        5. write render module auto update logic in PassInit tool
        6. deisgn database table fields and write document

7. #### SIVB emulation cloud platform (Chengdu Hermes-Sys Technology Co., Ltd.)
    * ##### Duration: 2017.02 - 2017.05
    * ##### Description
        A web paas platform provide netdisk storage flying emulation system data, user management and data share, access control management, hardware resource pool monitoring and virtual machine management.
        
        base on openstack and QNX system, all services for Chengdu ZR Aerospace Technology Co.,Ltd. A sub company under Hermes-Sys
    * ##### Responsibility
        1. deisgn platform architecture, disscuss requirements, make develop plan, containerize all
        services
        2. write server management,monitoring,netdisk,user and access control modules
        3. write javascript ajax data request interact
        4. write cephfs cluster access PHP module for files storage
        5. use golang interact with openstack api for server management
        6. deisgn database table fields and write user manual document
