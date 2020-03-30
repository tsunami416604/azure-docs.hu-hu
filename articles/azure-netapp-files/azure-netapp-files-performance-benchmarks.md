---
title: Az Azure NetApp Files teljesítményteszteredményei | Microsoft dokumentumok
description: Az Azure NetApp-fájlok teljesítménytesztjeinek eredményeit ismerteti a kötet szintjén.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881757"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Az Azure NetApp Files teljesítményteszt-eredményei

Ez a cikk az Azure NetApp-fájlok teljesítménytesztjeinek eredményeit ismerteti a kötet szintjén. 

## <a name="sample-application-used-for-the-tests"></a>A vizsgálatokhoz használt mintaalkalmazás

A teljesítményteszteket egy mintaalkalmazással futtatták az Azure NetApp-fájlok használatával. Az alkalmazás a következő jellemzőkkel rendelkezik: 

* A felhőhöz készült Linux-alapú alkalmazás
* Lineárisan skálázható a hozzáadott virtuális gépekkel (VM-ekkel) a számítási teljesítmény szükség szerint történő növelése érdekében
* Gyors hozzáférést igényel az adattóhoz
* Van I/ O minták, amelyek néha véletlenszerű és néha szekvenciális 
    * A véletlenszerű minta nagy mennyiségű I/O-hoz alacsony késleltetést igényel. 
    * A szekvenciális minta nagy sávszélességet igényel. 

## <a name="about-the-workload-generator"></a>A munkaterhelés-generátorról

Az eredmények a Vdbench összefoglaló fájljaiból származnak. [A Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) egy parancssori segédprogram, amely lemezEs I/O-számítási feladatokat hoz létre a tárolási teljesítmény érvényesítéséhez. A használt ügyfél-kiszolgáló konfiguráció méretezhető.  Egyetlen vegyes főkiszolgálót/ügyfelet és 14 dedikált ügyfélvirtuális gépet tartalmaz.

## <a name="about-the-tests"></a>A tesztekről

A vizsgálatok célja az volt, hogy azonosítsák a mintaalkalmazás által esetleg megállapítandó határértékeket, valamint azt a válaszidőt, amely a határértékekig ível.  

A következő teszteket futtatták: 

* 100% 8-KiB véletlenszerű olvasás
* 100% 8-KiB véletlenszerű írás
* 100% 64-KiB szekvenciális olvasás
* 100% 64-KiB szekvenciális írás
* 50% 64-KiB szekvenciális olvasás, 50% 64-KiB szekvenciális írás
* 50% 8-KiB véletlenszerű olvasás, 50% 8-KiB véletlenszerű írás

## <a name="bandwidth"></a>Sávszélesség

Az Azure NetApp Files több [szolgáltatási szintet](azure-netapp-files-service-levels.md)kínál. Minden szolgáltatási szint különböző mennyiségű sávszélességet kínál a kiosztott kapacitás TiB-je (mennyiségi kvóta) szerint. A kötet sávszélesség-korlátja a szolgáltatási szint és a mennyiségi kvóta kombinációja alapján van kiépítve. A sávszélesség-korlát csak egy tényező a megvalósítandó átviteli mennyiség meghatározásában.  

Jelenleg 4500 MiB a legnagyobb átviteli, amelyet a tesztelés során egyetlen kötettel szembeni munkaterhelés elért.  A prémium szintű szolgáltatási szint, a kötet kvóta 70,31 TiB biztosít elegendő sávszélességet, hogy megvalósítsa ezt az átviteli teljesítmény egy számítás az alábbi: 

![Sávszélesség képlete](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kvóta és szolgáltatási szint](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Nagy átviteli sebességű munkaterhelések

Az átviteli teszt vdbench-et és 12xD32s V3-tároló virtuális gépek kombinációját használta. A vizsgálat során a minta térfogata a következő átviteli értékeket érte el:

![Átviteli áttétel-vizsgálat](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O-igényes munkaterhelések

Az I/O-teszt Vdbench-et és 12xD32s V3 tárolóvirtuális gépek kombinációját használta. A vizsgálat során a minta térfogata a következő I/O-számokat érte el:

![I/O-vizsgálat](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Késés

A teszt virtuális gépek és az Azure NetApp Files kötet közötti távolság hatással van az I/O-teljesítményre.  Az alábbi diagram összehasonlítja az IOPS versus késés válaszgörbék két különböző virtuális gépkészlet.  A virtuális gépek egyik készlete az Azure NetApp-fájlok közelében található, a másik pedig távolabb.  A virtuális gépek további készletének megnövekedett késése hatással van az IOPS egy adott párhuzamossági szinten elért mennyiségére.  Ettől függetlenül a kötetek olvasása meghaladhatja a 300 000 IOPS-t az alábbi ábrán látható módon: 

![Látencia vizsgálat](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Összefoglalás

Késés-érzékeny számítási feladatok (adatbázisok) egy ezredmásodperces válaszidő vel rendelkezhet. A tranzakciós teljesítmény több mint 300 k IOPS egyetlen köteten.

Az átviteli sebességű alkalmazások (streameléshez és képalkotáshoz) 4.5GiB/s átviteli sebességű ek lehetnek.

## <a name="example-scripts"></a>Példa parancsfájlok

A következő példaparancsfájlok csak demonstrációs célokat szolgálnak.  Nem használhatók termelési célokra.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
