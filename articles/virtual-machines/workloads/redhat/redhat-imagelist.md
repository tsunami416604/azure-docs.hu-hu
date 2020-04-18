---
title: Red Hat Enterprise Linux-lemezképek az Azure-ban | Microsoft dokumentumok
description: Ismerje meg a Red Hat Enterprise Linux-lemezképeket a Microsoft Azure-ban
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 028c30fced14a60af9f5683e6c6e087b15591735
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605478"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>A Red Hat Enterprise Linux (RHEL) lemezképei elérhetők az Azure-ban
Az Azure különböző RHEL-lemezképeket kínál a különböző használati esetekhez.

> [!NOTE]
> Az összes RHEL-lemezkép elérhető az Azure nyilvános és az Azure Government-felhőiben. Nem érhetők el az Azure China felhőiben.

## <a name="list-of-rhel-images"></a>RHEL-képek listája
Ez az Azure-ban elérhető RHEL-lemezképek listája. Eltérő rendelkezés hiányában minden kép LVM-particionált és rendszeres RHEL-adattárakhoz van csatolva (nem EUS, nem E4S). A következő képek jelenleg általános használatra állnak rendelkezésre:

Ajánlat| SKU | Particionálás | Kiépítés | Megjegyzések
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | Nyers    | Linux-ügynök |
|             | 6.8      | Nyers    | Linux-ügynök |
|             | 6.9      | Nyers    | Linux-ügynök |
|             | 6.10     | Nyers    | Linux-ügynök |
|             | 7-RAW    | Nyers    | Linux-ügynök | RHEL 7.x család képek. <br> Alapértelmezés szerint a rendszeres adattárakhoz (nem az EUS-hez) csatolva.
|             | 7-LVM    | Lvm    | Linux-ügynök | RHEL 7.x család képek. <br> Alapértelmezés szerint a rendszeres adattárakhoz (nem az EUS-hez) csatolva. Ha egy szabványos RHEL-lemezképet keres a telepítéshez, használja ezt a lemezképet és/vagy a 2.
|             | 7lvm-gen2| Lvm    | Linux-ügynök | 2. generáció, RHEL 7.x család képek. <br> Alapértelmezés szerint a rendszeres adattárakhoz (nem az EUS-hez) csatolva. Ha egy szabványos RHEL-lemezképet keres a telepítéshez, használja ezt a lemezképet és/vagy az 1.
|             | 7-RAW-CI | NYERS-CI | felhő-init  | RHEL 7.x család képek. <br> Alapértelmezés szerint a rendszeres adattárakhoz (nem az EUS-hez) csatolva.
|             | 7.2      | Nyers    | Linux-ügynök |
|             | 7.3      | Nyers    | Linux-ügynök |
|             | 7.4      | Nyers    | Linux-ügynök | 2019 áprilisától alapértelmezés szerint csatolva van az EUS-adattárakhoz.
|             | 74-generációs2  | Nyers    | Linux-ügynök | Alapértelmezés szerint az EUS-adattárakhoz kapcsolódnak.
|             | 7,5      | Nyers    | Linux-ügynök | 2019 júniusátortól alapértelmezés szerint csatolva van az EUS-adattárakhoz.
|             | 75-generációs2  | Nyers    | Linux-ügynök | Alapértelmezés szerint az EUS-adattárakhoz kapcsolódnak.
|             | 7.6      | Nyers    | Linux-ügynök | 2019 májusátatól alapértelmezés szerint csatolva van az EUS-adattárakhoz.
|             | 76-generációs2  | Nyers    | Linux-ügynök | Alapértelmezés szerint az EUS-adattárakhoz kapcsolódnak.
|             | 7.7      | Lvm    | Linux-ügynök | Alapértelmezés szerint az EUS-adattárakhoz kapcsolódnak.
|             | 8-LVM    | Lvm    | Linux-ügynök | RHEL 8.x család képek. Rendszeres adattárakhoz csatolva.
|             | 8-lvm-gen2| Lvm    | Linux-ügynök | Hyper-V Generation 2 - RHEL 8.x család képek. Rendszeres adattárakhoz csatolva.
|             | 8        | Lvm    | Linux-ügynök | RHEL 8.0 képek
|             | 8 generációs2   | Lvm    | Linux-ügynök | Hyper-V Generation 2 - RHEL 8.0 képek.
|             | 8.1      | Lvm    | Linux-ügynök | RHEL 8.1 képek. Jelenleg rendszeres adattárakhoz kapcsolódik.
|             | 81gen2   | Lvm    | Linux-ügynök | Hyper-V Generation 2 - RHEL 8.1 képek. Jelenleg rendszeres adattárakhoz kapcsolódik.
RHEL-NEDV      | 7.4      | Lvm    | Linux-ügynök | RHEL 7.4 SAP HANA és üzleti alkalmazások hoz. Az E4S-tárolókhoz kapcsolódó an- e, az SAP és az RHEL díja, valamint az alap számítási díj díja.
|             | 74sap-gen2| Lvm    | Linux-ügynök | RHEL 7.4 SAP HANA és üzleti alkalmazások hoz. 2. generációs kép. Az E4S-tárolókhoz kapcsolódó an- e, az SAP és az RHEL díja, valamint az alap számítási díj díja.
|             | 7,5       | Lvm    | Linux-ügynök | RHEL 7.5 SAP HANA és üzleti alkalmazások esetén. Az E4S-tárolókhoz kapcsolódó an- e, az SAP és az RHEL díja, valamint az alap számítási díj díja.
|             | 75sap-gen2| Lvm    | Linux-ügynök | RHEL 7.5 SAP HANA és üzleti alkalmazások esetén. 2. generációs kép. Az E4S-tárolókhoz kapcsolódó an- e, az SAP és az RHEL díja, valamint az alap számítási díj díja.
|             | 7.6       | Lvm    | Linux-ügynök | RHEL 7.6 SAP HANA és üzleti alkalmazások hoz. Az E4S-tárolókhoz kapcsolódó an- e, az SAP és az RHEL díja, valamint az alap számítási díj díja.
|             | 76sap-gen2| Lvm    | Linux-ügynök | RHEL 7.6 SAP HANA és üzleti alkalmazások hoz. 2. generációs kép. Az E4S-tárolókhoz kapcsolódó an- e, az SAP és az RHEL díja, valamint az alap számítási díj díja.
|             | 7.7       | Lvm    | Linux-ügynök | RHEL 7.7 SAP HANA és üzleti alkalmazások esetén. Az E4S-tárolókhoz kapcsolódó an- e, az SAP és az RHEL díja, valamint az alap számítási díj díja.
RHEL-SAP-HANA | 6.7       | Nyers    | Linux-ügynök | RHEL 6.7 SAP HANA esetén. Elavult az RHEL-SAP-lemezképek javára.
|             | 7.2       | Lvm    | Linux-ügynök | RHEL 7.2 sap hana esetén. Elavult az RHEL-SAP-lemezképek javára.
|             | 7.3       | Lvm    | Linux-ügynök | RHEL 7.3 sap hana esetén. Elavult az RHEL-SAP-lemezképek javára.
RHEL-SAP-ALKALMAZÁSOK | 6.8       | Nyers    | Linux-ügynök | RHEL 6.8 SAP üzleti alkalmazásokhoz. Elavult az RHEL-SAP-lemezképek javára.
|             | 7.3       | Lvm    | Linux-ügynök | RHEL 7.3 SAP üzleti alkalmazásokhoz. Elavult az RHEL-SAP-lemezképek javára.
RHEL-HA       | 7.4       | Lvm    | Linux-ügynök | RHEL 7.4 HA kiegészítővel. Az alapszámítási díjon felül prémiumot számít fel a HA és az RHEL számára.
|             | 7,5       | Lvm    | Linux-ügynök | RHEL 7.5 HA add-Onnal. Az alapszámítási díjon felül prémiumot számít fel a HA és az RHEL számára.
|             | 7.6       | Lvm    | Linux-ügynök | RHEL 7.6 HA add-Onnal. Az alapszámítási díjon felül prémiumot számít fel a HA és az RHEL számára.
RHEL-SAP-HA   | 7.4          | Lvm    | Linux-ügynök | RHEL 7.4 az SAP ha és frissítési szolgáltatások. Az E4S-adattárakhoz csatolva. Az alap számítási díjakon felül felszámít ja díjat az SAP és a HA adattárakért, valamint az RHEL-ért.
|             | 74sapha-gen2 | Lvm    | Linux-ügynök | RHEL 7.4 az SAP ha és frissítési szolgáltatások. 2. generációs kép. Az E4S-adattárakhoz csatolva. Az alap számítási díjakon felül felszámít ja díjat az SAP és a HA adattárakért, valamint az RHEL-ért.
|             | 7,5          | Lvm    | Linux-ügynök | RHEL 7.5 az SAP ha és frissítési szolgáltatások. Az E4S-adattárakhoz csatolva. Az alap számítási díjakon felül felszámít ja díjat az SAP és a HA adattárakért, valamint az RHEL-ért.
|             | 7.6          | Lvm    | Linux-ügynök | RHEL 7.6 az SAP ha és frissítési szolgáltatások. Az E4S-adattárakhoz csatolva. Az alap számítási díjakon felül felszámít ja díjat az SAP és a HA adattárakért, valamint az RHEL-ért.
|             | 76sapha-gen2 | Lvm    | Linux-ügynök | RHEL 7.6 az SAP ha és frissítési szolgáltatások. 2. generációs kép. Az E4S-adattárakhoz csatolva. Az alap számítási díjakon felül felszámít ja díjat az SAP és a HA adattárakért, valamint az RHEL-ért.
|             | 7.7          | Lvm    | Linux-ügynök | RHEL 7.7 az SAP ha és frissítési szolgáltatások. Az E4S-adattárakhoz csatolva. Az alap számítási díjakon felül felszámít ja díjat az SAP és a HA adattárakért, valamint az RHEL-ért.
|             | 77sapha-gen2 | Lvm    | Linux-ügynök | RHEL 7.7 az SAP ha és frissítési szolgáltatások. 2. generációs kép. Az E4S-adattárakhoz csatolva. Az alap számítási díjakon felül felszámít ja díjat az SAP és a HA adattárakért, valamint az RHEL-ért.
rhel-byos     |rhel-lvm74| Lvm    | Linux-ügynök | Az RHEL 7.4 BYOS-képek, amelyek nem kapcsolódnak semmilyen frissítési forráshoz, nem számítanak fel RHEL-díjat.
|             |rhel-lvm75| Lvm    | Linux-ügynök | Az RHEL 7.5 BYOS-képek, amelyek nem kapcsolódnak semmilyen frissítési forráshoz, nem számítanak fel RHEL-díjat.
|             |rhel-lvm76| Lvm    | Linux-ügynök | Az RHEL 7.6 BYOS-képek, amelyek nem kapcsolódnak semmilyen frissítési forráshoz, nem számítanak fel RHEL-díjat.
|             |rhel-lvm77| Lvm    | Linux-ügynök | Az RHEL 7.7 BYOS-képek, amelyek nem kapcsolódnak semmilyen frissítési forráshoz, nem számítanak fel RHEL-díjat.
|             |rhel-lvm8 | Lvm    | Linux-ügynök | Rhel 8 BYOS-lemezképek (az RHEL alverzió a kép verzióértékében jelenik meg), amely nem kapcsolódik semmilyen frissítési forráshoz, nem számít fel RHEL-prémiumot.

> [!NOTE]
> Az RHEL-SAP-HANA termékkínálatot a Red Hat élettartama alatt tartja. A meglévő telepítések továbbra is normálisan működnek, de a Red Hat azt javasolja, hogy az ügyfelek migrálni az RHEL-SAP-HANA rendszerképek az RHEL-SAP-HA rendszerképek, amely tartalmazza az SAP HANA-tárolók, valamint a HA-bővítmény. A Red Hat SAP felhőajánlatairól [bővebben itt](https://access.redhat.com/articles/3751271)olvashat.

## <a name="next-steps"></a>További lépések
* További információ a [Red Hat-képekről az Azure-ban.](./redhat-images.md)
* További információ a [Red Hat update infrastruktúráról.](./redhat-rhui.md)
* További információ az [RHEL BYOS ajánlatról.](./byos.md)
* A Red Hat támogatási irányelveiről az RHEL összes verziójára vonatkozó információk a [Red Hat Enterprise Linux Életciklus](https://access.redhat.com/support/policy/updates/errata) oldalán találhatók.
