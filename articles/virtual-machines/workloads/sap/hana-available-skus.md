---
title: SKU-SAP HANA az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: SKU-SAP HANA az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad4e3ff3df8b4aeecbbbee7883ba383b9fd0d9c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617265"
---
# <a name="available-skus-for-hli"></a>HLI-hez elérhető termékváltozatok

Az Azure (nagyméretű példányok) szolgáltatás 3. változatán alapuló SAP HANA a következő Azure-régiók számos konfigurációjában érhető el:

- USA nyugati régiója
- USA keleti régiója
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Nyugat-Európa
- Észak-Európa
- Kelet-Japán
- Nyugat-Japán

Az Azure (nagyméretű példányok) szolgáltatás 4. változatán alapuló SAP HANA a következő Azure-régiók számos konfigurációjában érhető el:

- USA nyugati régiója, 2.
- USA keleti régiója
- USA déli középső régiója
- Nyugat-Európa
- Észak-Európa



[SAP HANA a HANA Large instances listához tartozó hitelesített SKU](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) -ket, például:

| SAP-megoldás | CPU | Memory (Memória) | Tárterület | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| OLAP-hoz optimalizált: SAP BW, BW/4HANA<br /> vagy SAP HANA általános OLAP számítási feladatokhoz | SAP HANA az Azure S72<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 CPU-magok és 72 CPU-szálak |  768 GB |  3 TB | Többé nem ajánlott |
| --- | SAP HANA az Azure S144<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 CPU-magok és 144 CPU-szálak |  1,5 TB |  6 TB | Többé nem ajánlott |
| --- | SAP HANA az Azure S192<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 CPU-magok és 192 CPU-szálak |  2,0 TB |  8 TB | Többé nem ajánlott |
| --- | SAP HANA az Azure S224<br /> – 4 x Intel® Xeon® Platinum 8276 processzor (más néven Cascade Lake)<br /> 112 CPU-magok és 224 CPU-szálak |  3,0 TB |  6,3 TB | Elérhető a Revision3-és Revision4-bélyegekben  |
| --- | SAP HANA az Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-magok és 384 CPU-szálak |  4,0 TB |  16 TB | Elérhető a Revision4 Stamps szolgáltatásban |
| OLTP-re optimalizált: SAP Business Suite<br /> SAP HANA vagy S/4HANA (OLTP) esetén<br /> általános OLTP | SAP HANA az Azure S72m<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 CPU-magok és 72 CPU-szálak |  1,5 TB |  6 TB | Többé nem ajánlott |
|---| SAP HANA az Azure S144m<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 CPU-magok és 144 CPU-szálak |  3,0 TB |  12 TB | Többé nem ajánlott |
|---| SAP HANA az Azure S192m<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 CPU-magok és 192 CPU-szálak  |  4,0 TB |  16 TB | Többé nem ajánlott |
| --- | SAP HANA az Azure S224m<br /> – 4 x Intel® Xeon® Platinum 8276 processzor (más néven Cascade Lake)<br /> 112 CPU-magok és 224 CPU-szálak |  6,0 TB |  10,5 TB | Elérhető a Revision3-és Revision4-bélyegekben  |
|---| SAP HANA az Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-magok és 384 CPU-szálak |  6,0 TB |  18 TB | Elérhető a Revision4 Stamps szolgáltatásban|
|---| SAP HANA az Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-magok és 384 CPU-szálak |  8,0 TB |  22 TB |  Elérhető a Revision4 Stamps szolgáltatásban |
|---| SAP HANA az Azure S576m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-magok és 576 CPU-szálak |  12,0 TB |  28 TB | Elérhető a Revision4 Stamps szolgáltatásban|
|---| SAP HANA az Azure S768m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-magok és 768 CPU-szálak |  16,0 TB |  36 TB | Elérhető a Revision4 Stamps szolgáltatásban|
|---| SAP HANA az Azure S960m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 CPU-magok és 960 CPU-szálak |  20,0 TB |  46 TB | Elérhető a Revision4 Stamps szolgáltatásban|


SAP HANA TDIv5 az SAP lehetővé teszi az ügyfél-specifikus méretezést és az ügyfél-specifikus projekteket, amelyek olyan kiszolgálói konfigurációkhoz vezethetnek, amelyek nem szerepelnek a minősítéssel a ben:

- [SAP HANA Certified készülékek](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Sok esetben ezek az ügyfél-specifikus kiszolgálói konfigurációk több memóriát hordoznak, mint az SAP-tanúsítvánnyal rendelkező kiszolgálói egységek. Az SAP használata során az ügyfeleknek lehetősége van SAP-támogatást kapni, és hitelesíteni az ügyfél-specifikus méretű kiszolgálói konfigurációkat. Az Azure-ban a következő HANA nagyméretű példányok standard SKU-ra van lehetőség, valamint a Microsoft árlista az ilyen TDIv5 ügyfél-specifikus méretezési projektekhez.

| SKU|CPU | Memory (Memória) | Tárterület | Rendelkezésre állás |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA az Azure S96<br /> – 2 x Intel® Xeon® Processor E7-8890 v4<br /> 48 CPU-magok és 96 CPU-szálak |  768 GB |  3 TB | Elérhető a Revision3-és Revision4-bélyegekben|


| Eredeti SKU, amely lehet <br /> kiterjesztve a memóriában | CPU | Memory (Memória) | Tárterület | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| A S192m bővíthető | SAP HANA az Azure S192xm<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 CPU-magok és 192 CPU-szálak |  6,0 TB |  16 TB | Többé nem ajánlott |
| A S384xm bővíthető | SAP HANA az Azure S384xxm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-magok és 384 CPU-szálak |  12,0 TB |  28 TB | Elérhető a Revision4 Stamps szolgáltatásban |
| A S576m bővíthető | SAP HANA az Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-magok és 576 CPU-szálak |  18,0 TB |  41 TB | Elérhető a Revision4 Stamps szolgáltatásban|
| A S768m bővíthető | SAP HANA az Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-magok és 768 CPU-szálak |  24,0 TB |  56 TB | Elérhető a Revision4 Stamps szolgáltatásban |

- CPU-magok = a kiszolgálói egység processzorai összegének nem Hyper-threaded CPU-magok összessége.
- CPU-szálak = a kiszolgálói egység processzorai összegének a Hyper-threaded CPU-magok által megadott számítási szálak összessége. A legtöbb egység alapértelmezés szerint a Hyper-Threading technológia használatára van konfigurálva.
- A szállítói javaslatok alapján a S768m, a S768xm és a S960m nem úgy vannak konfigurálva, hogy a Hyper-Threading szolgáltatást használják SAP HANA futtatásához.


A kiválasztott konfigurációk a munkaterhelés, a CPU-erőforrások és a kívánt memória függenek. A OLTP számítási feladatnak az OLAP számítási feladathoz optimalizált SKU-k használatára van lehetősége. 

Az ajánlatok hardveres alapja, az ügyfél-specifikus méretezési projektekhez tartozó egységek kivételével SAP HANA TDI-tanúsítvánnyal rendelkezik. A hardver két különböző osztálya osztható fel a SKU-ba:

- A S72, a S72m, a S96, a S144, a S144m, a S192, a S192m, a S192xm, a S224 és a S224m, amelyeket az SKU típusú "I osztálynak" nevezünk.
- A S384, a S384m, a S384xm, a S384xxm, a S576m, a S576xm S768m, a S768xm és a S960m, amelyek az SKU Type II osztályának nevezzük.
- Ha a 4.5 TB-tól eltérő S224 SKU-ajánlat érdekli a Optane-mel való 9TB, további információért forduljon a Microsoft-fiók csapatához. 


Egy teljes HANA nagyméretű példány bélyegzője nem kizárólag egyetlen ügyfél&#39;általi használatra van lefoglalva. Ez a tény az Azure-ban üzembe helyezett hálózati hálón keresztül csatlakoztatott számítási és tárolási erőforrások állványára vonatkozik. A HANA nagyméretű példány-infrastruktúrája, például az Azure, a következő három szinten helyezi üzembe a különböző ügyfél-&quot;bérlőket&quot; amelyek egymástól elszigeteltek:

- **Hálózat**: a HANA nagyméretű példány bélyegzőn belüli virtuális hálózatok elkülönítése.
- **Tárterület**: elkülönítés a tároló virtuális gépei között, amelyekhez tárolási kötetek vannak rendelve, és elkülönítik a tárolási köteteket a bérlők között.
- **Számítás**: kiszolgálói egységek dedikált hozzárendelése egyetlen bérlőhöz. A kiszolgálói egységek nem rendelkeznek kemény vagy lágy particionálással. Egyetlen kiszolgáló vagy gazda egység megosztása a bérlők között. 

A HANA nagyméretű példány-egységek különböző bérlők közötti központi telepítései nem láthatók egymás számára. A különböző bérlők által üzembe helyezett HANA nagyméretű példányok nem tudnak közvetlenül kommunikálni egymással a HANA nagyméretű példány-bélyegző szintjén. Az egyik bérlőn belüli HANA Large instances egységek csak a HANA nagyméretű példány-bélyegző szintjén tudnak kommunikálni egymással.

A nagyméretű példányok bélyegzőn üzembe helyezett bérlők egy Azure-előfizetéshez vannak rendelve számlázási célokra. A hálózatok esetében az adott Azure-regisztráción belül más Azure-előfizetések virtuális hálózatáról is elérhető. Ha ugyanabban az Azure-régióban egy másik Azure-előfizetéssel telepíti a üzembe helyezést, dönthet úgy is, hogy egy különálló HANA nagyméretű példány bérlőt kér.

Jelentős különbségek vannak a HANA nagyméretű példányon futó SAP HANA és az Azure-ban üzembe helyezett virtuális gépeken futó SAP HANA között:

- Az Azure-ban nincs SAP HANA virtualizációs réteg (nagyméretű példányok). Az alapul szolgáló operációs rendszer nélküli hardverek teljesítményét kapja meg.
- Az Azure-tól eltérően az Azure-beli (nagyméretű példányok) kiszolgáló SAP HANA egy adott ügyfélhez van hozzárendelve. Nincs lehetőség arra, hogy a kiszolgáló egysége vagy gazdagépe kemény vagy puha particionálású legyen. Ennek eredményeképpen a HANA nagyméretű példányok egysége a bérlőhöz és az Önhöz hozzárendelt teljes egészében van kiosztva. A kiszolgáló újraindítása vagy leállítása nem vezet automatikusan az operációs rendszerhez, és SAP HANA egy másik kiszolgálóra. (Az I osztályba tartozó SKU típus esetében az egyetlen kivétel az, ha egy kiszolgáló problémákba ütközik, és egy másik kiszolgálón kell végrehajtani az újratelepítést.)
- Az Azure-tól eltérően, ahol a gazdagép processzor-típusai a legjobb ár/teljesítmény arányhoz vannak kiválasztva, az Azure-ban (nagyméretű példányok) SAP HANA számára kiválasztott processzor-típusok az Intel E7v3 és a E7v4 processzor legmagasabb teljesítménye.

**Következő lépések**
- [HLI méretezése](hana-sizing.md)
