---
title: Red Hat Enterprise Linux rendszerképek az Azure-ban | Microsoft Docs
description: Ismerkedjen meg Red Hat Enterprise Linux rendszerképekkel Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: e6109a87750e588b12bfc9836c5db3db55420ec2
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133800"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Az Azure-ban elérhető Red Hat Enterprise Linux-(RHEL-) rendszerképek
Az Azure különféle RHEL-rendszerképeket kínál különböző használati esetekhez.

> [!NOTE]
> Minden RHEL-lemezkép elérhető az Azure nyilvános és Azure Government felhőkben. Nem érhetők el az Azure China-felhőkben.

## <a name="list-of-rhel-images"></a>RHEL-lemezképek listája
Ez az Azure-ban elérhető RHEL-lemezképek listája. Ha másként nincs megadva, az összes rendszerkép LVM-particionált, és a normál RHEL-adattárakhoz (nem EUS, nem E4S) van csatolva. Az alábbi rendszerképek jelenleg általános használatra elérhetők:

Ajánlat| SKU | Particionálás | Kiépítés | Megjegyzések
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux-ügynök |
|             | 6.8      | RAW    | Linux-ügynök |
|             | 6.9      | RAW    | Linux-ügynök |
|             | 6.10     | RAW    | Linux-ügynök |
|             | 7 – NYERS    | RAW    | Linux-ügynök | RHEL 7. x rendszerképekből álló család. <br> Alapértelmezés szerint a normál adattárakhoz van csatolva (nem EUS).
|             | 7-LVM    | LVM    | Linux-ügynök | RHEL 7. x rendszerképekből álló család. <br> Alapértelmezés szerint a normál adattárakhoz van csatolva (nem EUS). Ha az üzembe helyezéshez szabványos RHEL lemezképet keres, használja ezt a lemezképet és/vagy annak 2. generációjának megfelelőjét.
|             | 7lvm – Gen2| LVM    | Linux-ügynök | 2\. generációs, RHEL 7. x rendszerképek. <br> Alapértelmezés szerint a normál adattárakhoz van csatolva (nem EUS). Ha az üzembe helyezéshez szabványos RHEL lemezképet keres, használja ezt a lemezképet és/vagy annak 1. generációs megfelelőjét.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7. x rendszerképekből álló család. <br> Alapértelmezés szerint a normál adattárakhoz van csatolva (nem EUS).
|             | 7.2      | RAW    | Linux-ügynök |
|             | 7.3      | RAW    | Linux-ügynök |
|             | 7.4      | RAW    | Linux-ügynök | A EUS-adattárakhoz van csatolva, alapértelmezés szerint április 2019.
|             | 74 – Gen2  | RAW    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 7.5      | RAW    | Linux-ügynök | A EUS-adattárakhoz a 2019-as számú alapértelmezett módon van csatolva.
|             | 75 – Gen2  | RAW    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 7,6      | RAW    | Linux-ügynök | A EUS-adattárakhoz a 2019-as számú alapértelmezett módon csatolva.
|             | 76 – Gen2  | RAW    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 7,7      | LVM    | Linux-ügynök | Alapértelmezés szerint csatolva van a EUS-adattárakhoz.
|             | 8        | LVM    | Linux-ügynök | RHEL 8. x képcsalád
|             | 8 – Gen2   | LVM    | Linux-ügynök | Hyper-V Generation 2 – RHEL 8. x rendszerképek.
RHEL-SAP      | 7.4      | LVM    | Linux-ügynök | RHEL 7,4 SAP HANA és üzleti alkalmazásokhoz. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 74sap – Gen2| LVM    | Linux-ügynök | RHEL 7,4 SAP HANA és üzleti alkalmazásokhoz. 2\. generációs rendszerkép. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 7.5       | LVM    | Linux-ügynök | RHEL 7,5 SAP HANA és üzleti alkalmazásokhoz. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 75sap – Gen2| LVM    | Linux-ügynök | RHEL 7,5 SAP HANA és üzleti alkalmazásokhoz. 2\. generációs rendszerkép. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 7,6       | LVM    | Linux-ügynök | RHEL 7,6 SAP HANA és üzleti alkalmazásokhoz. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 76sap – Gen2| LVM    | Linux-ügynök | RHEL 7,6 SAP HANA és üzleti alkalmazásokhoz. 2\. generációs rendszerkép. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
|             | 7,7       | LVM    | Linux-ügynök | RHEL 7,7 SAP HANA és üzleti alkalmazásokhoz. A E4S-adattárakhoz csatolva díjat számítunk fel az SAP és a RHEL, valamint az alap számítási díj után.
RHEL-SAP-HANA | 6.7       | RAW    | Linux-ügynök | SAP HANA RHEL 6,7. Elavult a RHEL-SAP-lemezképek mellett.
|             | 7.2       | LVM    | Linux-ügynök | SAP HANA RHEL 7,2. Elavult a RHEL-SAP-lemezképek mellett.
|             | 7.3       | LVM    | Linux-ügynök | SAP HANA RHEL 7,3. Elavult a RHEL-SAP-lemezképek mellett.
RHEL-SAP-APPS | 6.8       | RAW    | Linux-ügynök | RHEL 6,8 az SAP Business Applicationshoz. Elavult a RHEL-SAP-lemezképek mellett.
|             | 7.3       | LVM    | Linux-ügynök | RHEL 7,3 az SAP Business Applicationshoz. Elavult a RHEL-SAP-lemezképek mellett.
RHEL – HA       | 7.4       | LVM    | Linux-ügynök | RHEL 7,4, HA a bővítmény be van kapcsolva. Díjat számítunk fel, és a RHEL az alap számítási díj alapján kell fizetni.
|             | 7.5       | LVM    | Linux-ügynök | RHEL 7,5, HA a bővítmény be van kapcsolva. Díjat számítunk fel, és a RHEL az alap számítási díj alapján kell fizetni.
|             | 7,6       | LVM    | Linux-ügynök | RHEL 7,6, HA a bővítmény be van kapcsolva. Díjat számítunk fel, és a RHEL az alap számítási díj alapján kell fizetni.
RHEL – SAP-HA   | 7.4          | LVM    | Linux-ügynök | RHEL 7,4 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 74sapha – Gen2 | LVM    | Linux-ügynök | RHEL 7,4 for SAP az HA és a Update Services szolgáltatással. 2\. generációs rendszerkép. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 7.5          | LVM    | Linux-ügynök | RHEL 7,5 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 7,6          | LVM    | Linux-ügynök | RHEL 7,6 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 76sapha – Gen2 | LVM    | Linux-ügynök | RHEL 7,6 for SAP az HA és a Update Services szolgáltatással. 2\. generációs rendszerkép. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 7,7          | LVM    | Linux-ügynök | RHEL 7,7 for SAP az HA és a Update Services szolgáltatással. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
|             | 77sapha – Gen2 | LVM    | Linux-ügynök | RHEL 7,7 for SAP az HA és a Update Services szolgáltatással. 2\. generációs rendszerkép. E4S-adattárakhoz csatolva. Az alapszintű számítási díjak mellett az SAP és a HA-adattárak, valamint a RHEL díját is felszámítjuk.
RHEL – BYOS     |RHEL – lvm74| LVM    | Linux-ügynök | A RHEL 7,4 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm75| LVM    | Linux-ügynök | A RHEL 7,5 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm76| LVM    | Linux-ügynök | A RHEL 7,6 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm77| LVM    | Linux-ügynök | A RHEL 7,7 BYOS-lemezképek, amelyek nem kapcsolódnak a frissítések valamelyikéhez, nem számítanak fel RHEL prémium díjat.
|             |RHEL – lvm8 | LVM    | Linux-ügynök | A RHEL 8 BYOS-lemezkép (a RHEL alverziója a lemezkép verziójának értéke) nem csatolható a frissítések, és nem számítja fel a RHEL-díjat.

## <a name="next-steps"></a>Következő lépések
* További információ az [Azure-beli Red Hat-lemezképekről](./redhat-images.md).
* További információ a [Red Hat frissítési infrastruktúráról](./redhat-rhui.md).
* További információ a [RHEL BYOS ajánlatáról](./byos.md).
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.
