---
title: Az SAP HANA az Azure virtuális gépek tárolási konfigurációk |} A Microsoft Docs
description: Tárajánlatok virtuális géphez, amelyek rendelkeznek SAP HANA üzembe helyezett őket.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735511"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Az SAP HANA az Azure virtuális gépek tárolási konfigurációk

Az Azure biztosít a különböző típusú tárolóhelyek, hogy az SAP HANA futtató Azure virtuális gépek kiválóan alkalmasak. Az Azure storage típusát, amely a hasonló központi telepítések listának az SAP HANA lehessen venni: 

- Standard SSD meghajtók (SSD)
- Prémium szintű tartós állapotú meghajtókkal (SSD)
- Nyilvános előzetes verzióban elérhető ultranagy SSD és az SAP-alkalmazások éles környezetben még nem támogatott

Ezek a lemeztípusok kapcsolatos további információkért tekintse meg ezt a cikket [lemez típusának kiválasztása](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Az Azure két üzembe helyezési módszert kínál az Azure Standard és Premium Storage virtuális merevlemezek. Ha engedélyezi a teljes forgatókönyv, előnyeit [Azure felügyelt lemez](https://azure.microsoft.com/services/managed-disks/) központi telepítések. 

A tárolási típusok és azok SLA-k, az IOPS és a tárolási teljesítmény listáját, tekintse át a [felügyelt lemezeket az Azure dokumentációjában](https://azure.microsoft.com/pricing/details/managed-disks/).

**Javaslat: Azure Premium Storage együttes használata az Azure Írásgyorsító és üzembe helyezés az Azure Managed Disks használata**

A helyszíni világ ritkán kellett érdeklik az i/o-alrendszerre és annak képességeit. Oka az, hogy az adott berendezés gyártójához, győződjön meg arról, hogy a minimális tárhellyel kapcsolatos követelmények teljesülnek-e az SAP Hana-hoz szükség volt. Létrehozása az Azure-infrastruktúra saját maga is érdemes figyelembe vennie néhány ezeknek az igényeknek. A minimális átviteli sebesség előrejelzéséhez, hogy a rendszer felkéri eredményez van szükség:

- Engedélyezze az olvasási/írási a **/hana/log** egy 250 MB/s, 1 MB i/o-mérettel
- Enable olvasása tevékenység legalább 400 MB/s a **/hana/adatok** 16 MB és 64 MB-os i/o-méretek
- Engedélyezze a legalább 250 MB/s az írási tevékenység **/hana/adatok** , 16 MB és 64 MB-os i/o-mérettel

A megadott alacsony tárolási késései kritikus következményekkel járnak DBMS-rendszerek, még akkor is, mint az adatbázis-kezelő rendszer, például SAP HANA, megtartása a memóriában. A tranzakciós napló írási a DBMS-rendszerek körül általában a storage-ban a kritikus útvonalat. De is műveletek, például visszaállítási pontok írása vagy a Betöltés a memóriában, kritikus fontosságú lehet olyan összeomlás utáni helyreállítást követően. Ezért **kötelező** kihasználhatja az Azure Premium szintű lemezekkel **/hana/adatok** és **/hana/log** köteteket. A minimális átviteli sebessége növelhető **/hana/log** és **/hana/adatok** igény szerint az SAP, RAID 0 létrehozásához szükséges MDADM vagy LVM több Azure Premium Storage-lemez használatával. És használja, mint a RAID-kötetek **/hana/adatok** és **/hana/log** köteteket. 

**Javaslat: Ahogy a stripe-méretet nyújt a a RAID 0 a javaslat van használandó:**

- 64 KB-os vagy 128 KB-   **/hana/adatok**
- 32 KB-   **/hana/log**

> [!NOTE]
> Nem kell bármilyen RAID-kötetek használatával, mivel a prémium és Standard tárterület tartsa egy virtuális merevlemez három rendszerkép tárhelyredundancia-szint konfigurálása. A RAID-kötetek használatának tisztán, hogy elegendő i/o-teljesítményt biztosító kötetek konfigurálni.

Halmozódó Azure virtuális merevlemezek RAID alatt számos, a halmozódnak az IOPS és a storage átviteli oldaláról. Tehát ha RAID 0 x 3 P30 Azure Premium Storage-lemez fölé helyezi, azt kell biztosíthatja háromszor az IOPS és háromszor a tárterületek átviteli sebességének egyetlen Azure Premium Storage P30 lemez.

Az alábbi gyorsítótárazási javaslatokat feltételezzük az i/o-jellemzőkkel az SAP Hana-hoz, hogy a lista:

- Nincs alig ellen a HANA-adatfájlokat olvasható számítási feladatait. Kivétel után indítsa újra a HANA-példány, vagy ha a HANA betöltött adatok nagy méretű i/o. Egy másik eset, nagyobb olvasási i adatok fájlokra vonatkozóan lehet HANA-adatbázis biztonsági mentése. Ennek eredményeképpen olvasási gyorsítótárazás többnyire nem értelmezhető óta az esetek többségében, minden adatkötetnél fájlt kell teljesen olvasható.
- A HANA-visszaállítási pontok és a HANA összeomlás utáni helyreállítást alapú adatlöketekkel bekövetkezik szemben az adatfájlok írása. Írás visszaállítási pontok aszinkron, és be minden felhasználói tranzakció nem tárolnak. Írása során összeomlás utáni helyreállítást adata teljesítmény kritikus fontosságú a rendszer ismét a gyors válaszadás érdekében. Azonban összeomlás utáni helyreállítást kell lennie, hanem kivételes esetekben
- A HANA ismétlés fájlok alig minden olvasási származnak. Kivétel a nagy i/o végrehajtásakor a tranzakciónapló biztonsági mentéseivel, összeomlás utáni helyreállítást, vagy a HANA-példányok újraindítása fázisban.  
- Az SAP HANA ismétlés naplófájl fő terhelés írások. Számítási feladat jellegétől függ rendelkezhet i 4 KB-os vagy más esetben i/o-méret 1 MB vagy annál kisebb. Az írási késést, szemben a SAP HANA-visszaállítási napló a teljesítmény kritikus fontosságú.
- A rendszer megőrzi a lemezen egy megbízható módon kell az összes írási művelet

**Javaslat: Eredményeként ezek a megfigyelt IO-minták az SAP Hana a gyorsítótárazás az Azure Premium Storage tárolást használ a különböző kötetek kell beállítani hasonló:**

- **/ hana/adatok** – nincs gyorsítótárazás
- **/ hana/log** – nincs gyorsítótárazás – M-sorozat (lásd a dokumentum későbbi) kivételt
- **/ hana/megosztott** – olvasási gyorsítótárazás


A teljes virtuális gép i/o-átviteli azt is vegye figyelembe, amikor méretezést, vagy egy virtuális gép mellett dönt. Teljes virtuális gép tárterületek átviteli sebességének a cikkben leírt [memóriahasználatra optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Linux i/o-ütemező mód
Linux rendszerű több i/o ütemezési mód van. Linux-szállítók és az SAP közös javasoljuk, hogy konfigurálja újra a kötetek az i/o scheduler módja a **cfq** mód a **noop** mód. Részletek hivatkozott [SAP Megjegyzés #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Az Azure M sorozatú virtuális gépek Azure Write Accelerator éles tárolási megoldás
Az Azure Írásgyorsító egy funkció, amely a kezdeti bevezetési az Azure M sorozatú virtuális gépek kizárólag. Az államok neve, ahogy az a funkciók célja, hogy javítása az Azure Premium Storage írására i/o-késését. Az SAP Hana, Write Accelerator használható kellene a **/hana/log** csak kötetre. Ezért a **/hana/adatok** és **/hana/log** Azure Write Accelerator támogatásával végzett külön kötetek vannak a **/hana/log** csak kötetre. 

> [!IMPORTANT]
> SAP HANA-minősítési Azure M sorozatú virtuális gépek kizárólag a Azure Write Accelerator van a **/hana/log** kötet. Ennek eredményeképpen éles forgatókönyvet az SAP HANA üzembe helyezéseket az Azure M sorozatú virtuális gépek várható Azure Write Accelerator konfigurálni a **/hana/log** kötet.  

> [!NOTE]
> A termelési forgatókönyvekhez e egy adott virtuális gép típusa az SAP az SAP Hana támogatott ellenőrizze a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Javaslat: Üzemi forgatókönyvek esetén ajánlott konfigurációkat a következőhöz hasonló:**

| A VM-TERMÉKVÁLTOZATOK | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | /hana/data | / hana/log | / hana/megosztott | / root kötet | /usr/sap | Hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

M416xx_v2 virtuális gépek típusai, nem még tesszük elérhetővé a Microsoft által a nyilvánosság számára. Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének megfelel-e futtatni kívánt számítási feladatokra. Ha a munkaterhelés igényel nagyobb kötetek **/hana/adatok** és **/hana/log**, növelje az Azure Premium Storage virtuális merevlemezeket kell. Méretezési egy további virtuális merevlemezeket, mint a felsorolt növeli az IOPS-kötettel és i/o-teljesítmény az Azure virtuális gép típusát keretein belül.

Az Azure Írásgyorsító csak együtt működik [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Tehát legalább az Azure Premium Storage-lemez versenytársaihoz képest a **/hana/log** kötetet kell üzembe helyezni, felügyelt lemezeket.

Az Azure Premium Storage virtuális merevlemezek Azure Write Accelerator támogató virtuális gépenként korlátozva van. A jelenlegi korlátok a következők:

- 16 virtuális merevlemezek egy M128xx és M416xx VM
- 8 virtuális merevlemezek egy M64xx és M208xx VM
- 4 virtuális merevlemezek egy M32xx VM

A cikkben található részletes útmutatást nyújt az Azure Írásgyorsító engedélyezése [Írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Részletek és az Azure Írásgyorsító korlátozásai a azonos dokumentációjában található.

**Javaslat: Kell használnia az Írásgyorsító a /hana/log kötetet alkotó lemezek**


## <a name="cost-conscious-azure-storage-configuration"></a>A Cost tudatos Azure Storage-konfiguráció
Az alábbi táblázat a felhasználók által is használt virtuális gépek típusai konfigurálása az Azure virtuális gépeken futó SAP HANA-gazdagépre. Előfordulhat, hogy egyes virtuális gépek típusai, amely nem felel meg az összes minimális tárolási feltétel az SAP Hana-hoz vagy nem hivatalosan támogatott az SAP HANA SAP által. Azonban eddig ezeken a virtuális gépeken tűnt részletes végrehajtani a nem éles környezethez. A **/hana/adatok** és **/hana/log** egy kötetet a kombinálásával. Azure Write Accelerator használatának eredményeként egy korlátozás iops válhat.

> [!NOTE]
> A támogatott SAP-forgatókönyvek, ellenőrizze az e egy adott virtuális gép típusa az SAP az SAP Hana támogatott a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Vásárlásával válthatnak az áttérést jelent a költség tudatos megoldás, korábbi javaslatok [Azure standard szintű HDD lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) jobb végrehajtására és megbízhatóbb [Azure Standard SSD-lemez](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| A VM-TERMÉKVÁLTOZATOK | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | / hana/adat- és naplófájlok/hana /<br /> az LVM vagy MDADM csíkozott | / hana/megosztott | / root kötet | /usr/sap | Hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


M416xx_v2 virtuális gépek típusai, nem még tesszük elérhetővé a Microsoft által a nyilvánosság számára. A lemezek esetében a 3 x P20 oversize típusokat a kisebb méretű virtuális gép a kötetek kapcsolatos ajánlások a következők szerint terület ajánlott a [SAP TDI tárolási tanulmány](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Azonban a választási lehetőség jelenik meg a tábla történt az SAP Hana elegendő adatátviteli sebességet biztosítanak. Ha módosításai van szüksége a **/hana/biztonsági mentés** kötet, amely lett méretezve, hogy a biztonsági mentések kétszer a memória kötet képviselő, nyugodtan módosíthatja.   
Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének megfelel-e futtatni kívánt számítási feladatokra. Ha a munkaterhelés igényel nagyobb kötetek **/hana/adatok** és **/hana/log**, növelje az Azure Premium Storage virtuális merevlemezeket kell. Méretezési egy további virtuális merevlemezeket, mint a felsorolt növeli az IOPS-kötettel és i/o-teljesítmény az Azure virtuális gép típusát keretein belül. 

> [!NOTE]
> A fenti konfigurációk nem kiaknázhatják a [Azure virtuális gép egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) óta az Azure Premium Storage és az Azure standard szintű tárolást használnak. Azonban a kijelölt lett választva, a költségek optimalizálása érdekében. A Premium Storage kiválasztása a fent felsorolt Azure standard szintű tárolást (Sxx), hogy a virtuális gép konfigurációja megfelelő, az egyetlen virtuális gép az Azure SLA-val, a lemezek kell.


> [!NOTE]
> A lemez konfigurációjára vonatkozó javaslatok conditions stated above céloz meg SAP fejezi ki, az infrastruktúra-szolgáltatók felé minimális követelményeknek. A valódi ügyfelek központi telepítések és a munkaterhelés-forgatókönyvek esetében helyzetekben talált, ezekkel az ajánlásokkal még nem adott meg elegendő képességeket. Ezek a sikerült helyzetek, amikor egy ügyfél szükséges HANA újraindítás után egy gyorsabb töltse be újra az adatok, vagy ha biztonsági mentési konfiguráció szükséges nagyobb sávszélességet, a Storage. Egyéb esetben foglalt **/hana/log** hol nem volt elegendő adott számítási feladatra 5000 iops értékre. Így hajtsa végre a megfelelő ezeket a javaslatokat a kiindulási pont, és alkalmazkodik a számítási feladatok követelményei alapján.
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Az SAP Hana az Azure Ultranagy SSD tároló konfigurálása
A Microsoft folyamatban bemutatása az Azure storage új típust nevezzük [Azure Ultranagy SSD](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/). A nagy különbség érhető el, amennyiben az Azure storage és Ultranagy SSD, hogy a lemez képességeket is nincs kötve a lemez mérete többé. Az Ultranagy SSD-Tárolóeszközön ügyfélként meghatározhatja ezeket a képességeket:

- A 65 536 GIB-ra és a 4 GB lemez mérete
- Iops-érték és közé eső 100 IOPS 160 KB iops-érték (legfeljebb függ, valamint a virtuális gépek típusai)
- Storage átviteli sebesség 300 MB/mp -ben való 2000 MB/mp

Részletekért keresse ki a cikket [bejelentése Ultranagy SSD – Azure-lemezek technológia (előzetes verzió) a következő generációja](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

UltraSSD definiálásához egyetlen lemezre, amely megfelel a méret, IOPS és a lemez átviteli tartomány lehetőségét biztosítja. Logikaikötet-kezelők például LVM vagy MDADM épülő Azure Premium Storage segítségével hozhat létre, amelyek megfelelnek az IOPS és a tároló átviteli sebességet megkövetelő kötetek helyett. Konfigurációs vegyesen UltraSSD és a Premium Storage között futtatható. Ennek eredményeképpen a teljesítmény kritikus fontosságú /hana/data és /hana/log kötetek UltraSSD használatát korlátozhatja, és terjed ki az Azure Premium Storage a kötetek

| A VM-TERMÉKVÁLTOZATOK | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | / hana/adatmennyiség | / hana/adatok i/o-teljesítmény | /hana/data IOPS | / hana/naplózási kötet | / hana/log i/o-teljesítmény | / hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1000 MB/s |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1000 MB/s | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2000 MB/s | 7200 GB | 1500M Bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

M416xx_v2 virtuális gépek típusai, nem még tesszük elérhetővé a Microsoft által a nyilvánosság számára. A felsorolt értékek kiindulási pontként szolgálnak, és valódi igényeinek értékelni kell. Az Azure Ultranagy SSD előnye az, hogy az IOPS és átviteli sebesség értékeinek alakíthatók a virtuális gép leállítása nélkül, vagy a rendszerre alkalmaz a Leállítás, a számítási feladatok.   

> [!NOTE]
> Az eddigi UltraSSD storage-pillanatképeket tároló nem áll rendelkezésre. Ez a beállítás megakadályozza a virtuális gép pillanatképeket az Azure Backup szolgáltatásainak használatát

## <a name="next-steps"></a>További lépések
További információkért lásd:

- [Az SAP HANA magas rendelkezésre állású útmutató az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).