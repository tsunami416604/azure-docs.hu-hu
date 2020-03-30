---
title: Az SAP HANA tárolási architektúrája az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Az SAP HANA azure-beli (nagy példányok) üzembe helyezésének tárolási architektúrájának tárolási architektúrája.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a12c454906d6c6ff702b7f635a91361bbe3994c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616888"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA (nagy példányok) tárolási architektúrája

Az SAP HANA az Azure-ban (nagy példányok) tárolási elrendezését az SAP HANA konfigurálja a klasszikus üzembe helyezési modell sap ajánlott irányelvek szerint. Az irányelvek az [SAP HANA tárolási követelmények](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) tanulmányban vannak dokumentálva.

Az I. típusú osztály HANA nagy példánya a memóriakötet négyszeresét származtatja tárolókötetként. A HANA nagy példány egységek II típusú osztálya esetében a tároló nem négyszer több. Az egységek egy olyan kötettel érkeznek, amely a HANA tranzakciós napló biztonsági másolatainak tárolására szolgál. További információ: [SAP HANA (Large Instances) telepítése és konfigurálása az Azure-ban.](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Lásd az alábbi táblázatot a tárhelyelosztás szempontjából. A táblázat a különböző HANA nagy példányegységekhez biztosított különböző kötetek durva kapacitását sorolja fel.

| HANA nagypéldány termékváltozat | hana/adatok | hana/log | hana/megosztott | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m között | 3 328 GB | 768 GB |1 280 GB | 768 GB |
| S96 között | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4 608 GB | 1024 GB | 1 536 GB | 1024 GB |
| S192m között | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S192xm |  11 520 GB |  1 536 GB |  1 792 GB |  1 536 GB |
| S224 |  4 224 GB |  512 GB |  1024 GB |  512 GB |
| S224m között |  8 448 GB |  512 GB |  1024 GB |  512 GB |
| S384 között | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384m között | 12 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16 000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xxm között |  20 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S576m között | 20 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S576xm | 31 744 GB | 4096 GB | 2048 GB | 4096 GB |
| S768m között | 28 000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4096 GB | 6 144 GB |
| S960m között | 36 000 GB | 4100 GB | 2050 GB | 4100 GB |


A ténylegesen üzembe helyezett kötetek a központi telepítéstől és a kötetméretek megjelenítéséhez használt eszköztől függően változhatnak.

Ha egy HANA nagypéldány termékváltozatot oszt fel, néhány példa a lehetséges felosztási darabokra a következőkre tűnhet:

| Memóriapartíció GB-ban | hana/adatok | hana/log | hana/megosztott | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1 792 GB | 640 GB | 1024 GB | 640 GB |
| 1,536 | 3 328 GB | 768 GB | 1 280 GB | 768 GB |


Ezek a méretek durva kötetszámok, amelyek a központi telepítéstől és a kötetek vizsgálatához használt eszközöktől függően kis mértékben eltérhetnek. Vannak más partícióméretek is, mint például a 2.5 TB. Ezeket a tárolási méreteket a rendszer az előző partíciókhoz hasonló képlettel számítja ki. A "partíciók" kifejezés nem jelenti azt, hogy az operációs rendszer, a memória vagy a CPU-erőforrások bármilyen módon particionáltak. Azt jelzi, hogy a különböző HANA-példányok tárolási partíciókat érdemes üzembe helyezni egyetlen HANA nagy példány egység. 

Lehet, hogy több tárhelyre lesz szüksége. A tárhelyet további tárhely 1 TB-os egységekben történő megvásárlásával adhat hozzá. Ez a további tároló további kötetként is hozzáadható. A meglévő kötetek közül egy vagy több kibővítésére is használható. Az eredetileg üzembe helyezett és többnyire az előző táblák által dokumentált kötetek mérete nem csökkenthető. A kötetek nevét és a csatlakoztatási neveket sem lehet módosítani. A korábban leírt tárolókötetek NFS4-kötetként vannak csatolva a HANA nagy példányegységeihez.

A tárolási pillanatképek biztonsági mentési és visszaállítási és vész-helyreállítási célokra használható. További információ: [SAP HANA (Large Instances) magas rendelkezésre állású és vész-helyreállítási az Azure-ban.](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Tekintse át [a HLI által támogatott forgatókönyveket](hana-supported-scenario.md) a forgatókönyv tárolási elrendezésének részleteihez.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Több SAP HANA-példány futtatása egy HANA nagy példányegységen

Lehetséges, hogy egynél több aktív SAP HANA-példány hana nagy példány egységek üzemeltetése. A tárolási pillanatképek és a vész-helyreállítási képességek biztosításához az ilyen konfiguráció példányonként kötetkészletet igényel. Jelenleg a HANA nagy példányegységek a következőképpen oszthatók fel:

- **S72, S72m, S96, S144, S192**: 256 GB-os lépésekben, 256 GB-os legkisebb indítóegységgel. A különböző lépésekben, például 256 GB és 512 GB kombinálható a készülék memóriájának maximális értékével.
- **S144m és S192m**: 256 GB-os lépésekben, 512 GB-mal a legkisebb egység. A különböző lépésekben, például 512 GB és 768 GB kombinálható a készülék memóriájának maximális értékével.
- **típus:** 512 GB lépésekben, a legkisebb 2 TB indítóegységgel. A különböző lépésekben, például 512 GB, 1 TB és 1,5 TB kombinálható a készülék memóriájának maximális értékével.

Néhány példa több SAP HANA-példány oka a következőkre néz ki.

| SKU | Memória mérete | Tárhely mérete | Több adatbázissal rendelkező méretek |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768 GB-os HANA-példány<br /> vagy 1x512 GB-os példány + 1x256-GB példány<br /> vagy 3x256 GB-os példányok | 
| S72m között | 1,5 TB | 6 TB | 3x512 GB-os HANA-példányok<br />vagy 1x512 GB-os példány + 1x1-TB példány<br />vagy 6x256 GB-os példányok<br />vagy 1x1,5 TB-os példány | 
| S192m között | 4 TB | 16 TB | 8x512 GB-os példányok<br />vagy 4x1-TB-os példányok<br />vagy 4x512 GB-os példányok + 2x1 TB-os példányok<br />vagy 4x768 GB-os példányok + 2x512-GB példányok<br />vagy 1x4-TB példány |
| S384xm | 8 TB | 22 TB | 4x2 TB-os példányok<br />vagy 2x4-TB példányok<br />vagy 2x3-TB-os példányok + 1x2-TB példányok<br />vagy 2x2,5 TB-os példányok + 1x3-TB példányok<br />vagy 1x8-TB példány |


Vannak más változatok is. 

## <a name="encryption-of-data-at-rest"></a>Az inaktív adatok titkosítása
A HANA Large Instance használt tároló átlátszó titkosítást használ az adatokhoz, mivel azok a lemezeken vannak tárolva 2018 vége óta. A korábbi központi telepítések, dönthet úgy, hogy a kötetek titkosítását. Ha ezt a lehetőséget választotta, kérheti a kötetek online titkosítását. A nem titkosított kötetekről a titkosított kötetekre való áttérés átlátható, és nem igényel állásidőt. 

A ski-k I. típusú osztályával a rendszerindító titkosítatlan szöveget tároló kötet titkosítva van. A 3-as verzióhana nagy példány bélyegzők, a HANA nagy példány SUK Type II osztályhasználatával, a rendszerindító lun operációs rendszer metódusokkal kell titkosítani. A 4-es verzióhana nagy példány bélyegzőinek használata iI.type a kötet a rendszerindító lun tárolja, és alapértelmezés szerint titkosítva van. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>A hana nagy példányok nagyobb HANA-példányainak szükséges beállításai
A HANA nagy példányokban használt tároló fájlméret-korlátozással rendelkezik. A méretkorlátozás fájlonként [16 TB.](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) Az EXT3 fájlrendszerek fájlméret-korlátozásaival ellentétben a HANA nem ismeri hallgatólagosan a HANA nagy példányok tároló által kikényszerített tárolási korlátozást. Ennek eredményeképpen a HANA nem hoz létre automatikusan új adatfájlt, amikor eléri a 16 TB-os fájlméretkorlátot. Ahana megpróbálja növelni a fájlt túl 16 TB, HANA hibákat jelent, és az index szerver összeomlik a végén.

> [!IMPORTANT]
> Annak megakadályozása érdekében, hogy a HANA megpróbálja növelni az adatfájlokat a HANA nagypéldány-tároló 16 TB-os fájlméret-korláton túl, a hana global.ini konfigurációs fájljában a következő paramétereket kell beállítania
> 
> - datavolume_striping=igaz
> - datavolume_striping_size_gb = 15000
> - Lásd még: [SAP-#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Vegye figyelembe az [SAP-#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**További lépések**
- [A HANA nagy példányainak támogatott forgatókönyvei](hana-supported-scenario.md)