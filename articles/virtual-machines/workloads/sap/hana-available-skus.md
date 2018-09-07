---
title: Az SAP Hana az Azure-ban (nagyméretű példányok) termékváltozatok |} A Microsoft Docs
description: SKU-k az SAP Hana az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ed7291333e7baa764b09e66aa5cfaedc77072fb
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028253"
---
# <a name="available-skus-for-hli"></a>Rendelkezésre álló termékváltozatok HLI

SAP HANA az Azure-ban (nagyméretű példányok) szolgáltatás több konfiguráció az Azure-régióban az USA nyugati régiója és USA keleti régiója, Kelet-Ausztrália, Délkelet-Ausztrália, Nyugat-Európa, Észak-Európa, kelet-japán és Nyugat-Japánban érhető el.

[Az SAP HANA-tanúsítvánnyal rendelkező SKU-k, HANA nagyméretű példányok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) például a listából:

| SAP-megoldás | CPU | Memory (Memória) | Storage | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| Az OLAP-optimalizálva: SAP BW, BW/4hana-t<br /> vagy az SAP HANA általános OLAP számítási feladatokhoz | SAP HANA az Azure S72<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 processzormagot és 72 CPU szálak |  768 GB |  3 TB | Elérhető |
| --- | SAP HANA az Azure S144<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 processzormagot és 144 CPU szálak |  1,5 TB |  6 TB | Többé nem érhető el |
| --- | SAP HANA az Azure S192<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 processzormagot és 192 CPU szálak |  2.0-S TB |  8 TB | Elérhető |
| --- | SAP HANA az Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 processzormagot és 384 CPU szálak |  4.0 TB |  16 TB | Elérhető |
| OLTP-hez optimalizálva: SAP Business Suite<br /> az SAP HANA vagy az S/4hana-t (OLTP),<br /> általános OLTP | SAP HANA az Azure S72m<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 processzormagot és 72 CPU szálak |  1,5 TB |  6 TB | Elérhető |
|---| SAP HANA az Azure S144m<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 processzormagot és 144 CPU szálak |  3.0 TB |  12 TB | Többé nem érhető el |
|---| SAP HANA az Azure S192m<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 processzormagot és 192 CPU szálak  |  4.0 TB |  16 TB | Elérhető |
|---| SAP HANA az Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 processzormagot és 384 CPU szálak |  6.0 TB |  18 TB | Elérhető |
|---| SAP HANA az Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 processzormagot és 384 CPU szálak |  8.0 TB |  22 TB |  Elérhető |
|---| SAP HANA az Azure S576m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 processzormagot és 576 CPU szálak |  12.0-S TB |  28 TB | Elérhető |
|---| SAP HANA az Azure S768m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 processzormagot és 768 CPU szálak |  16,0 TB |  36 TB | Elérhető |
|---| SAP HANA az Azure S960m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 processzormagot és 960 CPU szálak |  20.0 TB |  46 TB | Elérhető |


A SAP HANA TDIv5 SAP specifikus méretezése és ügyfél-specifikus projektek, amely kiszolgálókonfigurációk, amelyek nem szerepelnek az igazolt vezethet teszi lehetővé:

- [Az SAP HANA Certified készülékek](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Az SAP HANA-tanúsítvánnyal rendelkező IaaS-platformon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Sok esetben ügyfél-specifikus kiszolgáló konfigurációkról biztosítunk több memóriát az SAP-minősítéssel rendelkező kiszolgáló egységeket. SAP dolgozik, az ügyfelek rendelkeznek SAP-támogatás és a saját specifikus méretű kiszolgálókonfigurációk tanúsítása lehetőségét. Az Azure-ban a következő HANA nagyméretű példányok standard termékváltozatok érhetők el, és a Microsoft ár a ilyen TDIv5 specifikus méretezési projektek listája.


| Eredeti Termékváltozat, amely lehet <br /> a kiterjesztett memória | CPU | Memory (Memória) | Storage | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| S192m kiterjeszthető | SAP HANA az Azure S192xm<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 processzormagot és 192 CPU szálak |  6.0 TB |  16 TB | Elérhető |
| S384xm kiterjeszthető | SAP HANA az Azure S384xxm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 processzormagot és 384 CPU szálak |  12.0-S TB |  28 TB | Elérhető |
| S576m kiterjeszthető | SAP HANA az Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 processzormagot és 576 CPU szálak |  18.0 TB |  41 TB | Elérhető |
| S768m kiterjeszthető | SAP HANA az Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 processzormagot és 768 CPU szálak |  24,0 TB |  56 TB | Elérhető |

- CPU-magok = Processzormagok nem – a hyper-threaded vonatkozóan a processzorok, a kiszolgáló egység összege.
- Szálak CPU = Processzormagok többszálú vonatkozóan a kiszolgáló egység a processzor által biztosított számítási szálak összege. A legtöbb egységek használata a Hyper-Threading technológiát alapértelmezés szerint vannak konfigurálva.
- S768m szállítói javaslatai szerinti lépéseket, S768xm és S960m nem konfigurált Hyper-Threading használata az SAP HANA futtatásához.


A kiválasztott konfigurációkkal a számítási feladatok, a Processzor-erőforrások és a kívánt memória függenek. Az OLTP számítási feladatokhoz való felhasználáshoz optimalizált SKU-ja az OLAP-munkaterhelések esetében használjon lehetőség. 

A hardver, az ajánlatok, kivéve a projektek, a specifikus méretezési egység alap olyan SAP HANA TDI-tanúsítvánnyal. Hardver két különböző osztályú oszthatja be a termékváltozatok:

- S72, S72m, S144, S144m, S192, S192m és S192xm, amely a "Type I osztály" nevezik, SKU-k.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm és S960m nevezzük, amely a "Type II osztály" termékváltozatok.

Egy teljes HANA nagyméretű szolgáltatáspéldányban kizárólag egyetlen ügyfél számára nem lefoglalt&#39;s használja. Emiatt a számítási és tárolási erőforrások, valamint az Azure-ban telepített hálózati háló keresztül csatlakozik a rackszekrények vonatkozik. Nagyméretű HANA-példány infrastruktúrát, mint például az Azure, helyez üzembe különböző ügyfél &quot;bérlők&quot; , amelyek elkülönülnek egymástól a következő három szinten:

- **Hálózati**: a HANA nagyméretű szolgáltatáspéldányban belüli virtuális hálózatok elkülönítését.
- **Tárolási**: storage virtuális gépek, amelyek hozzárendelt tárolóköteteket és bérlők között tárolóköteteket elkülönítése elkülönítését.
- **COMPUTE**: egyetlen új bérlő dedikált kiszolgáló egységek hozzárendelését. Nem nehéz vagy helyreállítható particionálás server egységek. Megosztás nélkül egyetlen kiszolgáló vagy a gazdagép egység bérlők között. 

A központi telepítéseket, egységek nagyméretű HANA-példány a különböző bérlők között nem láthatók-e egymáshoz. Üzembe helyezett különböző bérlők HANA nagyméretű példányok egység nem tud közvetlenül kommunikálni egymással, nagyméretű HANA-példány stamp szintjén. Csak nagyméretű HANA-példány egység egy bérlőn belül a nagyméretű HANA-példány stamp szintjén is kommunikálhatnak egymással.

A nagyméretű szolgáltatáspéldányban egy üzembe helyezett tenant számlázási célból van rendelve egy Azure-előfizetéssel. Egy hálózatot akkor érhető el a virtuális hálózatok más Azure-előfizetések belül az azonos Azure-regisztrációjához. Ha telepít egy másik Azure-előfizetésében ugyanazon Azure-régióban, is lehet váltani, kérje meg egy elkülönített nagyméretű HANA-példány bérlő számára.

Futó SAP HANA nagyméretű HANA-példány és az Azure-ban üzembe helyezett virtuális gépeken futó SAP HANA között jelentős különbség van:

- Nincs nem virtualizálási réteg az SAP Hana az Azure-ban (nagyméretű példányok). Az alapul szolgáló operációs rendszer nélküli hardver teljesítményét kap.
- Egy adott ügyfél eltérően az Azure-ban, az SAP HANA az Azure-ban (nagyméretű példányok) kiszolgáló van kijelölve. Nincs, hogy egy kiszolgáló-egység vagy a gazdagép keményen vagy helyreállítható particionálva van lehetőség. Ennek eredményeképpen egy nagyméretű HANA-példány egység használatos teljes bérlőre, és az adott Önhöz rendelve. Egy újraindítás vagy leállítás, a kiszolgáló nem vezethet automatikusan az operációs rendszer és az SAP HANA üzembe helyezéséhez egy másik kiszolgálón. (Írja be a I osztályból SKU-k, az egyetlen kivétel, ha egy kiszolgáló problémákat észlel, és újbóli üzembe helyezés kell hajtható végre egy másik kiszolgálón.)
- Azure-ban, ahol processzor állomástípusok ki van jelölve a legjobb ár-teljesítmény arányt, ellentétben a processzor választott SAP Hana az Azure-ban (nagyméretű példányok) a következők az Intel E7v3 és E7v4 processzor sor legmagasabb végrehajtása.

**Következő lépések**
- Tekintse meg [HLI méretezése](hana-sizing.md)
