---
title: TELJESÍTMÉNYMÉRÉSEK NetApp Azure-fájlok |} A Microsoft Docs
description: A témakör ismerteti a teljesítményteszt teljesítménytesztek eredményeinek Azure NetApp fájlok kötetszinten.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449503"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Az Azure NetApp Files teljesítményével kapcsolatos mérőszámok

Ez a cikk ismerteti a teljesítményteszt teljesítménytesztek eredményeinek Azure NetApp fájlok kötetszinten. 

## <a name="sample-application-used-for-the-tests"></a>A tesztekben használt mintaalkalmazás

Teljesítménytesztek is futtathat egy mintaalkalmazást az Azure Files-NetApp használatával. Az alkalmazás a következő jellemzőkkel rendelkezik: 

* Egy Linux-alapú alkalmazás felhőbeli felhasználásra készült
* Is lineárisan skálázódnak a hozzáadott virtuális gépek (VM) növelheti a számítási kapacitást igény szerint
* Gyors elérhetőségét a data lake igényel
* Rendelkezik az időnként véletlenszerű, és néha szekvenciális i/o-minták 
    * Egy véletlenszerű minta nagy mennyiségű i/o közel valós idejű igényel. 
    * A szekvenciális minta nagy mennyiségű sávszélesség szükséges. 

## <a name="about-the-workload-generator"></a>Tudnivalók a munkaterhelés-generátor

Az eredmények Vdbench összefoglaló fájlok származnak. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) parancssori segédprogram, amely létrehozza a tárolási teljesítmény ellenőrzésével kapcsolatos lemez i/o-munkaterhelések. A használt ügyfél-kiszolgáló konfigurációs méretezhető.  Egyetlen vegyes master/ügyfél és 14 dedikált ügyfél virtuális gépeket tartalmazza.

## <a name="about-the-tests"></a>Tudnivalók a tesztek

A tesztek tervezték, hogy azonosítsa a korlátokat, amelyeket a mintaalkalmazást, és a válaszidő a görbék kereteken belül.  

A következő ellenőrzés futtatná: 

* 100 %-os 8-KiB véletlenszerű olvasási
* 100 %-os 8-KiB véletlenszerű írási
* 100 %-os 64-KiB Szekvenciális olvasási
* 100 %-os 64-KiB szekvenciális írási
* 50 %-os 64-KiB Szekvenciális olvasási, 50 %-os 64-KiB szekvenciális írási
* 50 %-os 8-KiB véletlenszerű olvasási, 50 %-os 8-KiB véletlenszerű írási

## <a name="bandwidth"></a>A sávszélesség

NetApp Azure Files kínál több [szolgáltatásszintek](azure-netapp-files-service-levels.md). Minden szolgáltatási szint egy eltérő mennyiségű Tib-ra, a kiosztott kapacitás (köteten kvóta) sávszélességet kínál. A sávszélesség korlátja egy kötet a szolgáltatási szint és a köteten kvóta kombinációja alapján van kiépítve. A sávszélességkorlát csak egy tényező szükséges tényleges átviteli sebességet, amelyet a rendszer bekövetkeznének meghatározására.  

4,500 MiB jelenleg a legnagyobb átviteli sebességet, amelyet értek el egy számítási feladat egy kötetet, a tesztelés ellen.  A prémium szintű szolgáltatási 70.31 TiB-kötet kvótát kiépíti elegendő sávszélességgel valósíthat meg ezt az átviteli sebességet az alábbi számítás szerint: 

![A sávszélesség-képlet](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Kvóta- és szolgáltatási szint](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Átviteli sebesség-igényes számítási feladatokhoz

Az átviteli sebesség tesztelése Vdbench és 12xD32s V3 tárolós virtuális gépek kombinációját használják. A mintául szolgáló kötetet, a teszt érhető el a következő átviteli számokat:

![Átviteli sebesség tesztelése](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O-igényes számítási feladatokhoz

Az i/o-teszt Vdbench és 12xD32s V3 tárolós virtuális gépek kombinációját használják. A mintául szolgáló kötetet, a teszt érhető el a következő i/o-számokat:

![I/o-teszt](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Késés

A teszt virtuális gépek és az Azure Files-NetApp kötet közötti távolságot hatással van az i/o-teljesítményt.  Az alábbi diagram az IOPS, késés válasz görbék a két különböző virtuális gépek és hasonlítja össze.  Egy olyan virtuális gépek, Azure NetApp fájlok mellett pedig a többi csoport további azonnal.  A további virtuális gépek számára a késések a párhuzamosság szintjét egy adott szinten elérhetők IOPS mennyisége hatással van.  Függetlenül attól olvasása esetén egy kötetet lehet hosszabb 300 000 IOPS alábbi képen szemléltetett módon: 

![Késés tanulmány](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Összefoglalás

Késleltetésre érzékeny számítási feladatok (adatbázisok) egy-ezredmásodperces válaszidő rendelkezhet. A tranzakciós teljesítmény lehet több mint 300k IOPS egyetlen kötetén.

Átviteli sebesség érzékeny alkalmazások (a streamelési és a lemezképpel végrehajtott telepítéshez) rendelkezhet 4.5GiB / s átviteli sebesség.

## <a name="example-scripts"></a>Példa parancsfájlok

Az alábbi parancsfájlpéldákat csak bemutató célú vonatkoznak.  Vannak nem termelési célra használható.  

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
