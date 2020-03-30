---
title: SAP HANA-s skin az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: SAP HANA skin az Azure-ban (nagy példányok).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617265"
---
# <a name="available-skus-for-hli"></a>HLI-hez elérhető termékváltozatok

AZ SAP HANA az Azure -on (Nagy példányok) szolgáltatás 3-as verziós bélyegzők alapján érhető el több konfigurációban az Azure régióiban:

- USA nyugati régiója
- USA keleti régiója
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Nyugat-Európa
- Észak-Európa
- Kelet-Japán
- Nyugat-Japán

AZ SAP HANA az Azure-ban (nagy példányok) 4-es verzióbélyegzők alapján érhető el több konfigurációban az Azure régióiban:

- USA nyugati régiója, 2.
- USA keleti régiója
- USA déli középső régiója
- Nyugat-Európa
- Észak-Európa



[SAP HANA tanúsítvánnyal rendelkező, a HANA nagy példányok listájának suk-jai,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) például:

| SAP-megoldás | CPU | Memory (Memória) | Storage | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| OLAP-ra optimalizálva: SAP BW, BW/4HANA<br /> vagy SAP HANA általános OLAP munkaterheléshez | SAP HANA az Azure S72-ben<br /> – 2 x Intel® Xeon® E7-8890 v3 processzor<br /> 36 CPU-mag és 72 CPU-szál |  768 GB |  3 TB | Már nem ajánlják fel. |
| --- | SAP HANA az Azure S144-en<br /> – 4 x Intel® Xeon® E7-8890 v3 processzor<br /> 72 CPU-mag és 144 CPU-szál |  1,5 TB |  6 TB | Már nem ajánlják fel. |
| --- | SAP HANA az Azure S192-ben<br /> – 4 x Intel® Xeon® E7-8890 v4 processzor<br /> 96 CPU-mag és 192 CPU-szál |  2,0 TB |  8 TB | Már nem ajánlják fel. |
| --- | SAP HANA az Azure S224-en<br /> – 4 x Intel® Xeon® Platinum 8276 processzor (más néven Cascade-tó)<br /> 112 CPU-mag és 224 CPU-szál |  3,0 TB |  6,3 TB | Elérhető revision3 és revision4 bélyegek  |
| --- | SAP HANA az Azure S384-en<br /> – 8 x Intel® Xeon® E7-8890 v4 processzor<br /> 192 CPU-mag és 384 CPU-szál |  4,0 TB |  16 TB | Elérhető Revision4 bélyegek |
| OLTP-ra optimalizálva: SAP Business Suite<br /> az SAP HANA vagy S/4HANA (OLTP)<br /> általános OLTP | SAP HANA az Azure S72m-en<br /> – 2 x Intel® Xeon® E7-8890 v3 processzor<br /> 36 CPU-mag és 72 CPU-szál |  1,5 TB |  6 TB | Már nem ajánlják fel. |
|---| SAP HANA az Azure S144m<br /> – 4 x Intel® Xeon® E7-8890 v3 processzor<br /> 72 CPU-mag és 144 CPU-szál |  3,0 TB |  12 TB | Már nem ajánlják fel. |
|---| SAP HANA az Azure S192m<br /> – 4 x Intel® Xeon® E7-8890 v4 processzor<br /> 96 CPU-mag és 192 CPU-szál  |  4,0 TB |  16 TB | Már nem ajánlják fel. |
| --- | SAP HANA az Azure S224m-en<br /> – 4 x Intel® Xeon® Platinum 8276 processzor (más néven Cascade-tó)<br /> 112 CPU-mag és 224 CPU-szál |  6,0 TB |  10,5 TB | Elérhető revision3 és revision4 bélyegek  |
|---| SAP HANA az Azure S384m<br /> – 8 x Intel® Xeon® E7-8890 v4 processzor<br /> 192 CPU-mag és 384 CPU-szál |  6,0 TB |  18 TB | Elérhető Revision4 bélyegek|
|---| SAP HANA az Azure S384xm-en<br /> – 8 x Intel® Xeon® E7-8890 v4 processzor<br /> 192 CPU-mag és 384 CPU-szál |  8,0 TB |  22 TB |  Elérhető Revision4 bélyegek |
|---| SAP HANA az Azure S576m-on<br /> – 12 x Intel® Xeon® E7-8890 v4 processzor<br /> 288 CPU-mag és 576 CPU-szál |  12,0 TB |  28 TB | Elérhető Revision4 bélyegek|
|---| SAP HANA az Azure S768m<br /> – 16 x Intel® Xeon® E7-8890 v4 processzor<br /> 384 CPU-mag és 768 CPU-szál |  16,0 TB |  36 TB | Elérhető Revision4 bélyegek|
|---| SAP HANA az Azure S960m-en<br /> – 20 x Intel® Xeon® Processzor E7-8890 v4<br /> 480 CPU-mag és 960 CPU-szál |  20,0 TB |  46 TB | Elérhető Revision4 bélyegek|


Az SAP HANA TDIv5 alatt az SAP lehetővé teszi az ügyfélspecifikus méretezést és az ügyfélspecifikus projekteket, amelyek kiszolgálókonfigurációkhoz vezethetnek, amelyek nem szerepelnek a következő ben tanúsítottként:

- [SAP HANA tanúsítvánnyal rendelkező készülékek](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA tanúsítvánnyal rendelkező IaaS-platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Sok esetben ezek az ügyfélspecifikus kiszolgálókonfigurációk több memóriát hordoznak, mint az SAP-val hitelesített kiszolgálóegységek. Az SAP-val való munka közben az ügyfeleknek lehetőségük van sap-támogatást kérni, és igazolni az ügyfélspecifikus méretű kiszolgálókonfigurációkat. Az Azure-ban a következő HANA nagypéldány szabványos termékkészletállmák érhetők el, és az ilyen TDIv5 ügyfélspecifikus méretezési projektek Microsoft árlistájában.

| SKU|CPU | Memory (Memória) | Storage | Rendelkezésre állás |
| ---| --- | --- | --- | --- |
| S96 között | SAP HANA az Azure S96-on<br /> – 2 x Intel® Xeon® Processzor E7-8890 v4<br /> 48 CPU-mag és 96 CPU-szál |  768 GB |  3 TB | Elérhető revision3 és revision4 bélyegek|


| Eredeti termékváltozat, amely <br /> bővítve a memóriában | CPU | Memory (Memória) | Storage | Rendelkezésre állás |
| --- | --- | --- | --- | --- |
| Az S192m kiterjeszthető | SAP HANA az Azure S192xm-en<br /> – 4 x Intel® Xeon® E7-8890 v4 processzor<br /> 96 CPU-mag és 192 CPU-szál |  6,0 TB |  16 TB | Már nem ajánlják fel. |
| Az S384xm kiterjeszthető | SAP HANA az Azure S384xxm<br /> – 8 x Intel® Xeon® E7-8890 v4 processzor<br /> 192 CPU-mag és 384 CPU-szál |  12,0 TB |  28 TB | Elérhető Revision4 bélyegek |
| Az S576m kiterjeszthető | SAP HANA az Azure S576xm-on<br /> – 12 x Intel® Xeon® E7-8890 v4 processzor<br /> 288 CPU-mag és 576 CPU-szál |  18,0 TB |  41 TB | Elérhető Revision4 bélyegek|
| Az S768m kiterjeszthető | SAP HANA az Azure S768xm-en<br /> – 16 x Intel® Xeon® E7-8890 v4 processzor<br /> 384 CPU-mag és 768 CPU-szál |  24,0 TB |  56 TB | Elérhető Revision4 bélyegek |

- CPU magok = a kiszolgálóegység processzorainak összegének nem hiperszálas CPU-magok összege.
- CPU-szálak = a kiszolgálóegység processzorainak összegének hiperszálas CPU-magok által biztosított számítási szálak összege. A legtöbb egység alapértelmezés szerint a Hyper-Threading technológiát használja.
- A beszállítói ajánlások alapján az S768m, az S768xm és az S960m nincs beállítva az SAP HANA futtatásához használt hyper-threading használatára.


A kiválasztott konfigurációk a munkaterheléstől, a PROCESSZOR-erőforrásoktól és a kívánt memóriától függenek. Az OLTP-számítási feladat az OLAP-számítási feladatokra optimalizált su-kat is használhatja. 

Az ajánlatok hardveralapja, kivéve az ügyfélspecifikus méretezési projektek egységeit, SAP HANA TDI-tanúsítvánnyal rendelkezik. A hardverkét különböző osztálya osztja fel a terméktájékoztatót:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 és S224m, amelyeket a skus-ok "I. típusú osztályának" neveznek.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm és S960m, amelyeket a kkv-k "II.
- Ha érdeklik a 4,5 TB-tól 9TB-ig kínált egyéb S224 terméktájékoztatók az Optane-nál, további információkért lépjen kapcsolatba a Microsoft-fiók csapatával. 


A teljes HANA nagy példány bélyegzője nem kizárólag egyetlen ügyfél számára van kiosztva,&#39;s használata. Ez a tény az Azure-ban üzembe helyezett hálózati hálón keresztül csatlakoztatott számítási és tárolási erőforrások állványaira is vonatkozik. A HANA nagypéldány-infrastruktúra, például &quot;az&quot; Azure, különböző ügyfél-bérlőket telepít, amelyek a következő három szinten vannak elkülönítve egymástól:

- **Hálózat**: A HANA nagy példány bélyegzőn belüli virtuális hálózatokon keresztüli elkülönítés.
- **Tárolás:** Elkülönítés tárolóvirtuális gépeken keresztül, amelyek tárolási kötetek hozzárendelt és elkülöníteni tárolókötetek bérlők között.
- **Számítási**: A kiszolgálóegységek dedikált hozzárendelése egyetlen bérlőhöz. Nincs kemény vagy puha particionálás a kiszolgálóegységekben. Egyetlen kiszolgáló vagy állomásegység nem oszd meg a bérlők között. 

A hana nagy példány egységek különböző bérlők közötti telepítések nem láthatók egymás számára. A különböző bérlőkben üzembe helyezett HANA nagy példányegységek nem kommunikálhatnak közvetlenül egymással a HANA nagy példány bélyegzőszintjén. Csak hana nagy példány egységek egy bérlőn belül kommunikálhatnak egymással a HANA nagy példány bélyegző szintjén.

A nagy példány bélyegzőjében üzembe helyezett bérlő számlázási célokra egy Azure-előfizetéshez van hozzárendelve. A hálózat érhető el a virtuális hálózatok más Azure-előfizetések ugyanazon az Azure-regisztráción belül érhető el. Ha egy másik Azure-előfizetéssel telepíti ugyanabban az Azure-régióban, dönthet úgy is, hogy egy külön HANA nagy példány bérlőjét kér.

Jelentős különbségek vannak az SAP HANA hana hana-n és az Azure-ban üzembe helyezett virtuális gépeken futó SAP HANA futtatása között:

- Nincs virtualizációs réteg az SAP HANA az Azure-ban (nagy példányok). Ön kap a teljesítmény a mögöttes csupasz fém hardver.
- Az Azure-ral ellentétben az SAP HANA az Azure-beli (nagy példányok) kiszolgáló dedikált egy adott ügyfél. Nincs lehetőség arra, hogy a kiszolgálóegység vagy a gazdagép erősen vagy lágyan particionált. Ennek eredményeképpen a HANA nagy példány egység a bérlő höz rendelt egészként, és ezzel együtt. A kiszolgáló újraindítása vagy leállítása nem vezet automatikusan az operációs rendszerhez, és az SAP HANA egy másik kiszolgálón van telepítve. (Az I. típusú osztály példányai esetében az egyetlen kivétel az, ha egy kiszolgáló problémákba ütközik, és az újratelepítést egy másik kiszolgálón kell végrehajtani.)
- Az Azure-ral ellentétben, ahol a gazdaprocesszor-típusok a legjobb ár/teljesítmény arányhoz vannak kiválasztva, az Azure-beli SAP HANA -hoz választott processzortípusok (nagy példányok) az Intel E7v3 és E7v4 processzorcsalád legnagyobb teljesítményűek.

**További lépések**
- HLI [méretezésének ajánlása](hana-sizing.md)
