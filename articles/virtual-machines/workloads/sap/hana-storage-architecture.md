---
title: SAP HANA az Azure-ban (nagyméretű példányok) tároló-architektúra |} A Microsoft Docs
description: Tároló-architektúra az Azure-ban (nagyméretű példányok) SAP HANA üzembe helyezése.
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
ms.date: 03/05/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5c5f1d8d7a9c84d807db53933f0cbb176f9fb7f2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551960"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA (nagyméretű példányok) tároló-architektúra

A tárolási elrendezés az SAP Hana az Azure-ban (nagyméretű példányok) a klasszikus üzemi modell / irányelvek ajánlott SAP az SAP Hana van konfigurálva. Az irányelvek vannak dokumentálva az [SAP HANA tárolási követelményei](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) tanulmányt.

A nagyméretű HANA-példány I osztály típusú storage-kötetként négyszer a memória kötet tartalmaz. A nagyméretű HANA-példány egységek II. típusú az osztályhoz a tároló nem négyszer további. Az egységek kapható olyan kötetre, amely a HANA tranzakciós napló biztonsági mentések tárolására szolgál. További információkért lásd: [telepítse és konfigurálja az SAP HANA (nagyméretű példányok) az Azure-ban](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tekintse meg az alábbi táblázat a Foglalás tekintetében. A táblázat a különböző kötetek, a másik egységgel nagyméretű HANA-példány a megadott nyers kapacitása.

| Nagyméretű HANA-példány Termékváltozat | Hana/adatok | Hana/log | Hana/shared | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | MAXIMUM 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |MAXIMUM 1280 GB | 768 GB |
| S96 | MAXIMUM 1280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608 GB | 1 024 GB | 1,536 GB | 1 024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12 000 GB | 2,050 GB | 2,050 GB | 2040 GB |
| S384xm | 16000 GB | 2,050 GB | 2,050 GB | 2040 GB |
| S384xxm |  20 000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576m | 20 000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4,096 GB | 2048 GB | 4,096 GB |
| S768m | 28 000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4,096 GB | 6 144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


Tényleges üzembe helyezett kötetek üzembe helyezés és az eszköz, amellyel megjelenítése a kötet mérete alapján változhat.

Ha Ön feloszthatja a HANA nagyméretű példányok Termékváltozatban, néhány példa a lehetséges osztás darab következőhöz hasonló lehet:

| Memória partíció GB-ban | Hana/adatok | Hana/log | Hana/shared | Hana/log/biztonsági mentés |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | MAXIMUM 1280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1 024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | MAXIMUM 1280 GB | 768 GB |


Ezeket a méreteket elsősorban a nyers mennyiségi számát, amelyek a központi telepítés és a kötetek megjelenítéséhez használt eszközök függően némileg eltérőek lehetnek. Még nincsenek más partícióméretei, például 2,5 TB. Ezek a storage-méretek kiszámítása egy képlettel hasonló az előző partíciók. A "partíció" kifejezés nem jelenti azt, hogy az operációs rendszer, a memória vagy a Processzor-erőforrásokat bármilyen módon particionáltak. Azt jelzi, hogy a különböző HANA-példányokhoz, érdemes egy egyetlen nagyméretű HANA-példány egységen üzembe helyezése adattárolási partíciókat. 

Szükség lehet további tárhelyet. Vásároljon további tárterületet 1 TB-os egységekben storage is hozzáadhat. A további tárhely is hozzáadhatók további kötetként. Azt is segítségével kiterjesztheti a meglévő kötetek közül legalább egyet. Nem lehet a kötetek méretének csökkentése eredetileg üzembe helyezve, és többnyire felterjesztése révén az előző táblázatokban. Emellett nem lehet módosítani a kötetek nevei, vagy csatlakoztassa a neveket. A korábban leírt köteteinek a nagyméretű HANA-példány egységek NFS4 kötetek vannak csatolva.

Használhatja a pillanatképek tárolási, biztonsági mentési és visszaállítási és vész-helyreállítási céllal. További információkért lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tekintse meg [HLI támogatott forgatókönyvek](hana-supported-scenario.md) a forgatókönyvnek a tárolási elrendezés részletekért.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Több SAP HANA-példányok futtatunk egy nagyméretű HANA-példány egység

Egynél több aktív SAP HANA-példány a nagyméretű HANA-példány egységek üzemeltetésére lehetőség. Ahhoz, hogy a pillanatképek tárolási és a vész-helyreállítási képességeit, az ilyen konfiguráció szükséges példányonként kötet. Jelenleg nagyméretű HANA-példány egységek részekre módon:

- **S72, S72m, S96, S144, S192**: 256 GB, 256 GB a legkisebb kezdési egység egységnyi növekményekben. Az egység a memória maximális kombinálhatók a különböző nagyobb, mint 256 GB és 512 GB.
- **S144m és S192m**: 256 GB és 512 GB legkisebb egységnyi növekményekben. Az egység a memória maximális kombinálhatók a különböző, például az 512 GB és 768 GB-os lépésekben.
- **Írja be a II osztály**: 512 GB-ot, a legkisebb kiindulási egységgel 2 TB-os lépésekben. Az egység a memória maximális kombinálhatók a különböző, például az 512 GB, 1 TB-os és 1,5 TB-os lépésekben.

Néhány példa a több SAP HANA-példány fut a következőhöz hasonlóan nézhet ki.

| SKU | Memória mérete | Tároló mérete | Több adatbázis mérete |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB-os HANA-példány<br /> vagy 1 x 512 GB-os példány + 1 x 256 GB-példány<br /> vagy 3 x 256 GB-példányok | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA-példányok<br />vagy 1 x 512 GB-os példány + 1 x 1 TB méretű példány<br />vagy 6 x 256 GB-példányok<br />vagy 1x1.5 TB-os példány | 
| S192m | 4 TB | 16 TB | 8 x 512 GB-példányok<br />vagy 4 x 1 TB méretű példányok<br />vagy 4 x 512 GB-os példányok + 2 darab 1 TB méretű példányok<br />vagy 4 x 768 GB-os példányok + 2 x 512 GB-példányok<br />vagy 1 darab 4 TB-os példány |
| S384xm | 8 TB | 22 TB | 4 x 2 TB-os példányok<br />vagy 2 darab 4 TB-os példányok<br />vagy 2 x 3 TB-os példányok + 1 x 2 TB-os példányok<br />vagy 2x2.5-TB-os + 1 x 3 TB-os példányok<br />vagy 1 x 8 TB méretű példány |


Más változatok is vannak. 

## <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása
Nagyméretű HANA-példány felhasznált tárterület lehetővé teszi, hogy egy adatok átlátható titkosítását, a lemezeken tárolja. Egy nagyméretű HANA-példány egység telepítésekor a engedélyezheti a titkosítást az ilyen típusú. Emellett titkosított kötetek után módosíthatja a központi telepítés történik. Az áthelyezés nem titkosított a titkosított kötetekre transzparens módon történik, és nem igényli az állásidő. 

Típusú I osztályhoz termékváltozatok, a LUN-t tárolja, a rendszerindító kötet titkosítva van. Az SKU-k a nagyméretű HANA-példányt, II. típusú osztályhoz a rendszerindító LUN-t az operációs rendszer módszerekkel titkosítania kell. További információért forduljon a Microsoft-kezelési csapatunk.

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>A nagyméretű HANA-példányokhoz nagyobb HANA-példányokhoz kötelező beállítások
Nagyméretű HANA-példányok a használt tárterület használata fájl mérete korlátozott. A [méretének korlátozása, 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) fájlonként. Ellentétben a fájlméretre vonatkozó korlátozások EXT3 fájlrendszereket, a HANA még nem ismeri implicit módon, a storage korlátozás kényszeríti ki a nagyméretű HANA-példányokhoz tárolót. Ennek eredményeképpen HANA fog nem automatikusan létrehozni egy új adatfájlt a fájl maximális mérete 16 TB-os elérésekor. Mint 16 TB a fájl megcélzott HANA próbál, HANA jelentést hibák és az index kiszolgáló összeomlik, a végén.

> [!IMPORTANT]
> Az adatfájlokat a 16 TB fájlok méretkorlátjának nagyméretű HANA-példány tárolási túli növekszik próbál HANA elkerülése érdekében állítsa be az alábbi paramétereket a Hana global.ini konfigurációs fájlban kell
> 
- datavolume_striping=true
- datavolume_striping_size_gb = 15000
- Lásd még az SAP Megjegyzés [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
- Vegye figyelembe az SAP-jegyzetnek [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Következő lépések**
- Tekintse meg [forgatókönyveket támogatja nagyméretű HANA-példányok](hana-supported-scenario.md)