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
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870882"
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

* 100 %-os véletlenszerű 8 KiB olvasása
* 100 %-os 8 KiB véletlenszerű írási
* 100 %-os 64 KiB Szekvenciális olvasási
* 100 %-os 64 KiB szekvenciális írási
* 50 %-os 64 KiB Szekvenciális olvasási, 50 %-os 64 KiB szekvenciális írási
* 50 %-os véletlenszerű 8 KiB olvasni, 50 %-os 8 KiB véletlenszerű írási

## <a name="bandwidth"></a>A sávszélesség

NetApp Azure Files kínál több [szolgáltatásszintek](azure-netapp-files-service-levels.md). Minden szolgáltatási szint egy eltérő mennyiségű Tib-ra, a kiosztott kapacitás (köteten kvóta) sávszélességet kínál. A sávszélesség korlátja egy kötet a szolgáltatási szint és a köteten kvóta kombinációja alapján van kiépítve. Vegye figyelembe, hogy a sávszélességre vonatkozó korlátját csak egy tényező szükséges tényleges átviteli sebességet, amelyet a rendszer bekövetkeznének meghatározásához.  

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

A teszt virtuális gépek és az Azure Files-NetApp kötet közötti távolságot hatással van az i/o-teljesítményt.  Az alábbi diagram az IOPS, késés válasz görbék a két különböző virtuális gépek és hasonlítja össze.  Egy olyan virtuális gépek, Azure NetApp fájlok mellett pedig a többi csoport további azonnal.  Vegye figyelembe, hogy a további virtuális gépek számára a nagyobb késés hatással van a párhuzamosság szintjét egy adott szinten elérhetők IOPS mennyiségét.  Függetlenül attól olvasása esetén egy kötetet lehet hosszabb 300 000 IOPS alábbi képen szemléltetett módon: 

![Késés tanulmány](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Összefoglalás

Késleltetésre érzékeny számítási feladatok (adatbázisok) rendelkezhet egy ezredmásodpercnél több válaszideje. A tranzakciós teljesítmény lehet több mint 300k IOPS egyetlen kötetén.

Átviteli sebesség érzékeny alkalmazások (a streamelési és a lemezképpel végrehajtott telepítéshez) rendelkezhet 4.5GiB / s átviteli sebesség.
