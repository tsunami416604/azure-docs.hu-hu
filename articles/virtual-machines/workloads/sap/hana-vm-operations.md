---
title: SAP HANA-infrastruktúra konfigurációi és a műveletek az Azure-ban |} A Microsoft Docs
description: Üzemeltetési útmutató a SAP HANA-rendszerek üzembe helyezett Azure-beli virtuális gépeken.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699330"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban
Ez a cikk útmutatást nyújt az Azure-infrastruktúra beállítása és működtetése az Azure-beli natív virtuális gépek (VM) üzembe helyezett SAP HANA rendszereit. A cikk emellett tartalmaz az SAP HANA kibővített M128s VM-termékváltozat-konfigurációs adatait. Ez a cikk nem helyettesítik a standard szintű SAP dokumentációját, amely magában foglalja az alábbi tartalommal:

- [Az SAP-felügyeleti útmutató](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP telepítési útmutatóinak](https://service.sap.com/instguides)
- [SAP-megjegyzések](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató használatához az alábbi Azure-összetevők alapszintű ismeretét szüksége:

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Az Azure-hálózatok és virtuális hálózatok](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

SAP NetWeaver és más Azure-beli SAP összetevők kapcsolatos további információkért tekintse meg a [Azure-beli SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) szakaszában a [Azure dokumentációja](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Az alapszintű kapcsolatos szempontok
A következő szakaszok ismertetik az alapszintű üzembe helyezéséhez Azure virtuális gépeken SAP HANA rendszereit szempontjai.

### <a name="connect-to-azure-virtual-machines"></a>Csatlakozás Azure-beli virtuális gépek
Leírtak szerint a [tervezési útmutatója az Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), két alapvető módszerek használhatók az Azure virtuális gépekhez:

- Kapcsolódás az internethez és a nyilvános végpontokat a JumpBox virtuális gép vagy a virtuális gépen futó SAP HANA keresztül.
- Csatlakozhat egy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) vagy az Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Hely – hely kapcsolatok VPN- vagy ExpressRoute szükség a termelési forgatókönyvekhez. Ez a kapcsolattípus is szükség van, amely termelési forgatókönyvekhez, amelyben a rendszer használja-e a SAP-szoftvereket az éles forgatókönyvekhez. Az alábbi képen egy példa a webhelyek közötti kapcsolat látható:

![Webhelyek közötti kapcsolat](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Válassza ki az Azure-beli Virtuálisgép-típusok
Az Azure virtuális gép típusok használata a termelési forgatókönyvekhez szerepelnek a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Éles forgatókönyvekhez natív Azure-beli Virtuálisgép-típusok választéka áll rendelkezésre.

>[!NOTE]
> Éles forgatókönyvekhez, használja a virtuális gépek típusai, amelyek szerepelnek a [SAP Megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533). Az Azure virtuális gépek használata a termelési forgatókönyvekhez, ellenőrizze az SAP HANA-tanúsítvánnyal rendelkező virtuális gépeket a közzétett SAP [IaaS platformok listájához certified](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Az Azure-beli virtuális gépek üzembe helyezéséhez használja:

- Az Azure Portalon.
- Az Azure PowerShell-parancsmagokat.
- Az Azure parancssori felület.

Az Azure virtuális gép Services révén teljes telepített SAP HANA-platform is telepítheti a [SAP-felhőplatformmal](https://cal.sap.com/). A telepítési folyamatról további információért lásd: [üzembe helyezése az SAP S/4HANA vagy BW/4hana-t az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Az automation kiadott kapcsolatos tudnivalókat lásd: [GitHub-cikkben](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-an-azure-storage-type"></a>Egy Azure Storage típusának kiválasztása
Az Azure storage két típusú SAP HANA futtatása Azure virtuális gépek kiválóan alkalmasak kínál:  

- Standard merevlemezes (HDD) meghajtók
- Prémium szintű tartós állapotú meghajtókhoz (SSD-kkel)

Ezek a lemeztípusok kapcsolatos további információkért lásd: [válassza ki a lemez típusát](../../windows/disks-types.md).

Az Azure virtuális merevlemezek két üzembe helyezési módszert kínál az Azure standard storage és a premium storage. Ha engedélyezi a teljes forgatókönyv, előnyeit [Azure felügyelt lemez](https://azure.microsoft.com/services/managed-disks/) központi telepítések.

A tárolási típusok és azok SLA-k, az IOPS és a tárolási teljesítmény listáját lásd: [felügyelt lemezeket az Azure dokumentációjában](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configure-the-storage-for-azure-virtual-machines"></a>A storage-beli virtuális gépek konfigurálása

Valószínűleg soha nem létrejöhetnek i/o-alrendszerre és azok képességeinek, mert az adott berendezés gyártójához ellenőrizze, hogy a minimális tárhellyel kapcsolatos követelmények teljesülnek-e az SAP Hana-hoz. Amikor Ön hozhat létre az Azure-infrastruktúra, érdemes figyelembe vennie néhány követelményekről kell. Emellett tisztában kell lennie a következő szakaszokban ajánlott konfigurációs követelményekről. Olyan esetekben, ahol konfigurálhatja a virtuális gépek szeretné futtatni, az SAP HANA szüksége lehet:

- Engedélyezze a legalább 250 MB/s-1 MB-os i/o-méretek esetében a /hana/log olvasási/írási köteten.
- Engedélyezze az olvasási tevékenység legalább 400 MB/s /hana/data 16 MB-os és 64-MB i/o-méretek esetében.
- Engedélyezze a legalább 250 MB/s, 16 MB-os és 64-MB i/o-mérettel /hana/data írási tevékenységét.

Alacsony – storage késései kritikus következményekkel járnak DBMS-rendszerek, annak ellenére, hogy az adatbázis-kezelő rendszer, például SAP HANA, megőrzi az adatokat a memóriában. A tranzakciós napló írási a DBMS-rendszerek körül általában a storage-ban a kritikus útvonalat. Azonban műveletek, a visszaállítási pontok írása vagy a memóriában betöltése után összeomlás utáni helyreállítást is kritikus fontosságú lehet. 

Az Azure prémium szintű lemezek /hana/data és /hana/log kötetek használata kötelező. A minimális átviteli /hana/log és az SAP által megkívánt /hana/data eléréséhez hozhat létre egy RAID 0 mdadm vagy egy logikai kötet-kezelő (LVM) használatával több Azure prémium szintű tárolólemezeket protokollon keresztül. A RAID-kötetek /hana/data, és /hana/log köteteket is használható. Javasoljuk, hogy a RAID 0 használja az alábbi stripe-méretek:

- 64 KB-os vagy/hana/adatok 128 KB
- 32 KB-/ hana/log

> [!NOTE]
> Nem kell semmilyen tárhelyredundancia-szint konfigurálása a RAID-kötetek használatával, mert az Azure premium és standard tárterület tartsa három rendszerkép egy virtuális merevlemez. A RAID kötetre használata kizárólag köteteket, amelyek elegendő i/o-átviteli konfigurálása.

Azure virtuális merevlemezek RAID alatt számos gyűlik halmozódnak az IOPS és a storage átviteli oldaláról. Ha RAID 0 3 x P30 Azure prémium szintű tárolólemezeket fölé helyezi, biztosít háromszor az IOPS és háromszor a tárterületek átviteli sebességének egyetlen Azure premium storage P30 lemez.

A következő gyorsítótárazási ajánlások az SAP Hana tegyük fel, az i/o-jellemzőkkel:

- Nincs alig olvasási számítási feladatait a HANA-adatfájlok ellen. Kivétel után indítsa újra a HANA-példány, vagy ha a HANA betöltött adatok nagy méretű i/o. Egy másik eset, nagyobb olvasási i adatok fájlokra vonatkozóan lehet HANA-adatbázis biztonsági mentése. Ennek eredményeképpen olvasási gyorsítótárazás nem értelme, mert a legtöbb esetben minden adatkötetnél fájlt kell teljesen olvasható.
- A HANA-visszaállítási pontok és a HANA összeomlás utáni helyreállítást alapú adatlöketekkel bekövetkezik szemben az adatfájlok írása. Visszaállítási pontok írása aszinkron, és nem rendelkező felhasználói tranzakciókat fel. Írása során összeomlás utáni helyreállítást adata teljesítmény kritikus fontosságú a rendszer ismét a gyors válaszadás érdekében. Összeomlás utáni helyreállítást inkább kivételes esetekben kell lennie.
- A HANA ismétlés fájlok alig minden olvasási származnak. Kivételek nagyméretű i/o esetén hajtsa végre a tranzakciónapló biztonsági mentéseivel, összeomlás utáni helyreállítást, vagy a HANA-példány újraindítása fázisa.  
- A fő terhelés, szemben a SAP HANA ismétlés naplófájl írások. A számítási feladat jellegétől függően rendelkezhet i kisebb mint 4 KB-os vagy más esetekben i/o mérete legalább 1 MB. Az írási késést, szemben a SAP HANA-visszaállítási napló a teljesítmény kritikus fontosságú.
- Az összes írási művelet a lemezen egy megbízható módon kell őrizhető meg.

Eredményeként ezek a megfigyelt IO-minták az SAP Hana állítsa be, a gyorsítótárazás a különböző kötetek, amelyek az Azure premium storage használata:

- **/ hana/adatok**: Nincs gyorsítótárazás.
- **/ hana/log**: Nincs gyorsítótárazás, egy kivétellel az M-sorozatú virtuális gépek (lásd a cikk több későbbi részében).
- **/ hana/megosztott**: Olvassa el a gyorsítótárazás.


Ezenkívül tartsa szem előtt a teljes virtuális gép i/o-átviteli méret vagy döntse el, a virtuális gép. A teljes virtuális gép tárterületek átviteli sebességének leírt [memóriahasználatra optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Linux-i/o-ütemező mód
Linux rendszerű több i/o ütemezési mód van. Linux-szállítók és az SAP közös javasoljuk, hogy a kötetek az i/o scheduler módjának beállítása a **cfq** mód a **noop** mód. További információkért lásd: [SAP Megjegyzés #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Tárolási megoldás az Write Accelerator Azure M-sorozatú virtual machineshez
 Az írási gyorsító bevezetéséről Azure M sorozatú virtuális gépek kizárólag egy az Azure szolgáltatás. A funkció célja ellen az Azure premium storage írások i/o késését javítása érdekében. Az SAP Hana Write Accelerator használható a /hana/log kötetet csak lehet. Ebből kifolyólag a konfiguráció látható, amennyiben módosítani kell. A fő módosítás Írásgyorsító csak a /hana/log köteten használatához a darabolása /hana/data és /hana/log között. 

> [!IMPORTANT]
> Az Azure az SAP HANA-minősítési M-sorozatú virtuális gépek, a/hana/naplózási kötet Write Accelerator kizárólagos. Emiatt, éles-forgatókönyvet az SAP HANA üzembe helyezéseket az Azure M sorozatú virtuális gépek várható konfigurálhatók, Write Accelerator/hana/log kötet.

> [!NOTE]
> A termelési forgatókönyvekhez e egy adott virtuális gép típusa az SAP az SAP Hana támogatott ellenőrizze a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Az alábbi táblázat ajánlott konfigurációkat.

| A VM-TERMÉKVÁLTOZATOK | RAM | Virtuális gépek maximális i/o<br /> Átviteli sebesség | /hana/data | / hana/log | / hana/megosztott | / root kötet | /usr/sap | Hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/mp | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/mp | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/mp | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 giB | 1000 MB/mp | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1,750 giB | 1000 MB/mp | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 giB | 2000 MB/mp |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 giB | 2000 MB/mp | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének megfelel-e futtatni kívánt számítási feladatokra. Ha a számítási feladatok nagyobb kötetek /hana/data és /hana/log igényel, az Azure premium storage VHD-k számának növelése. Méretezési egy további virtuális merevlemezeket, mint a felsorolt növeli az IOPS-kötettel és i/o-teljesítmény az Azure virtuális gép típusát keretein belül.

Írási gyorsító csak együtt működik [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Legalább az Azure premium storage-lemezeket a /hana/log kötetet alkotó kell telepíteni, mint a felügyelt lemezek.

Az Azure premium Storage virtuális merevlemezek Írásgyorsító támogató virtuális gépenként korlátozva van. A jelenlegi korlátok a következők:

- 16 virtuális merevlemezek egy M128xx virtuális Gépet.
- 8 virtuális merevlemezek egy M64xx virtuális Gépet.
- 4 virtuális merevlemezek egy M32xx virtuális Gépet.

Írásgyorsító engedélyezésével kapcsolatos további információkért lásd: [Írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

További információk és az Írásgyorsító korlátozásai az azonos dokumentációjában talál.


#### <a name="cost-conscious-azure-storage-configuration"></a>Költségérzékeny Azure Storage-konfiguráció
Az alábbi táblázat olyan konfigurációja, az ügyfelek által leggyakrabban használt Virtuálisgép-típusok az Azure virtuális gépeken futó SAP HANA-gazdagépre. Előfordulhat, hogy egyes virtuális gépek típusai, amelyek nem felelnek meg az összes minimális követelmények az SAP Hana-hoz. Emellett előfordulhat, egyes virtuális gépek típusai, amelyek az SAP HANA SAP hivatalosan nem támogatja. Az eddigi ezeken a virtuális gépeken végrehajtása után részletes éles forgatókönyvekhez. 

> [!NOTE]
> A termelési forgatókönyvekhez e egy adott virtuális gép típusa az SAP az SAP Hana támogatott ellenőrizze a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| A VM-TERMÉKVÁLTOZATOK | RAM | Virtuális gépek maximális i/o<br /> Átviteli sebesség | / hana/adat- és naplófájlok/hana /<br /> az LVM vagy mdadm csíkozott | / hana/megosztott | / root kötet | /usr/sap | Hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/mp | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/mp | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/mp | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1200 MB/mp | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/mp | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/mp | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/mp | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/mp | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 giB | 1000 MB/mp | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,750 giB | 1000 MB/mp | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 giB | 2000 MB/mp |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 giB | 2000 MB/mp | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


A lemezek, ajánlott a 3 x P20 oversize a típusokat a kisebb méretű virtuális Gépet a megadott hely ajánlások tekintetében a kötetek a [SAP TDI tárolási tanulmány](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). A táblázatban jelennek meg a kiválasztott történt az SAP Hana elegendő adatátviteli sebességet biztosítanak. Ha módosítani szeretné a **/hana/biztonsági mentés** kötet, amely lett méretezve, hogy kétszer a memória kötet képviselő biztonsági mentések megtartása, nyugodtan módosításokat. 

Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének megfelel-e futtatni kívánt számítási feladatokra. Ha a számítási feladatok nagyobb kötetek /hana/data és /hana/log igényel, az Azure premium storage VHD-k számának növelése. Méretezési egy további virtuális merevlemezeket, mint a felsorolt növeli az IOPS-kötettel és i/o-teljesítmény az Azure virtuális gép típusát keretein belül. 

> [!NOTE]
> A korábbi konfigurációkat nem előnyös [Azure virtuális gép egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) mert az Azure premium storage és az Azure standard szintű tárolást használ. A kijelölt választotta, a költségek optimalizálása érdekében. Válassza ki az összes lemezén prémium szintű tárolást a táblázatban, amely az Azure standard szintű storage, hogy az egyetlen virtuális gép az Azure SLA-val kompatibilis a Virtuálisgép-konfigurációt (Sxx) megjelöléssel láthatók.


> [!NOTE]
> A lemez konfigurációjára vonatkozó javaslatok a céloznia SAP biztosít az infrastruktúra-szolgáltatók minimális követelményeknek. A tényleges rendszereit és a munkaterhelés-forgatókönyvek esetében ezek a javaslatok nem adott meg bizonyos esetekben a megfelelő képességeket. Például egy ügyfél szükséges az adatok egy gyorsabb Újrabetöltés HANA újraindítás után, vagy biztonsági mentési konfigurációban a Storage nagyobb sávszélesség szükséges. Egyéb esetben például /hana/log, ahol 5000 iops-érték nem teljesíti az adott számítási feladatra. Ezekkel az ajánlásokkal használhatja kiindulási pontként, és azokat a számítási feladatok követelményei alapján.
>  

### <a name="set-up-azure-virtual-networks"></a>Az Azure virtuális hálózatok beállítása
Ha helyek közötti kapcsolat az Azure VPN-en keresztül vagy az Azure ExpressRoute, rendelkeznie kell legalább egy Azure virtuális hálózat, amely a VPN- vagy ExpressRoute-kapcsolatcsoporthoz virtuális átjárón keresztül csatlakozik. Egyszerű üzembe a virtuális átjáró is telepíthető, amelyen túl a az SAP HANA-példányok az Azure virtuális hálózat alhálózatán. 

SAP HANA telepítése, az Azure virtuális hálózatban két további alhálózatok létrehozására. Egy alhálózatot a virtuális gépek futtatásához az SAP HANA-példányok üzemelteti. A többi alhálózat JumpBox vagy a felügyeleti virtuális gépek gazdagépre SAP HANA Studio, más felügyeleti szoftverek vagy az alkalmazás fut.

> [!IMPORTANT]
> Konfigurálás [Azure hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) az SAP-alkalmazás és az adatbázis-kezelő réteg az olyan SAP NetWeaver - közötti kommunikációs útvonal, a Hybris-, vagy az SAP S/4HANA-alapú rendszer nem támogatott. Ez a korlátozás a teljesítmény és okból is. A SAP alkalmazás réteget és az adatbázis-kezelő réteg közötti kommunikációs útvonal egy közvetlen kell lennie. A korlátozás nem tartalmazza a [alkalmazásbiztonsági csoport (Alkalmazásbiztonsági) és a hálózati biztonsági csoport (NSG) szabályai](https://docs.microsoft.com/azure/virtual-network/security-overview) Ha ezen Alkalmazásbiztonsági és NSG-t szabályok lehetővé teszik a közvetlen kommunikációt elérési útja. 
>
> Más esetekben, ahol nem támogatottak a hálózati virtuális berendezések szerepelnek: 
>
> - Azure virtuális gépek, amelyek Linux támasztja közötti kommunikációs útvonalak fürt csomópontjai és SBD eszközök leírtak szerint [magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server, SAP-alkalmazások az Azure Virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> - Azure virtuális gépek és a Windows Server Scale-Out File Server közötti kommunikációs útvonalak beállítása akár leírtak szerint [egy SAP ASCS/SCS példányhoz Windows feladatátvevő fürtre a fürt az Azure-beli fájlmegosztás használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Hálózati virtuális készülékekre történő irányításához kommunikációs útvonalak is könnyen double két kommunikációs partnerek között a hálózati késést. Akkor is korlátozhatja a kritikus útvonalakat az SAP alkalmazásrétegre és az adatbázis-kezelő réteg közötti átviteli sebesség. Néhány forgatókönyvet hálózati virtuális berendezések okozhat sikertelen támasztja Linux-fürtöket. Ezek olyan esetekben, ahol a Linux támasztja fürtcsomópontok közti kommunikációra kommunikációhoz SBD eszközére egy hálózati virtuális készüléken keresztül.  
> 

> [!IMPORTANT]
> Egy másik Tervező, amely a *nem* az SAP alkalmazásrétegre és az adatbázis-kezelő réteg elkülönítése különböző Azure virtuális hálózatokra, amely nem támogatott a [társviszonyba](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) egymással. Azt javasoljuk, hogy Ön elkülönítse az SAP alkalmazásréteg és adatbázis-kezelő réteg helyett az Azure virtuális hálózatban lévő alhálózat használatával más Azure virtuális hálózatok használatával. 
>
>Ha nem kívánja a javasolt, és ehelyett elkülöníthető a két réteg eltérő virtuális hálózatokra, a két virtuális hálózat lehet [társviszonyba](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Vegye figyelembe, hogy a hálózati forgalmat két közötti [társviszonyba](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) adatátvitel költségeit az Azure virtuális hálózatok. Az SAP alkalmazásréteg és adatbázis-kezelő réteg között cseréje hatalmas adatmennyiséget, amely több terabájt áll. Ha az SAP alkalmazásréteg és adatbázis-kezelő réteg két Azure virtuális társhálózatok között elkülönülnek, felhalmozhat jelentős költségekkel jár. 

SAP HANA futtatásához a virtuális gépek telepítésekor a virtuális gépek van szükség:

- Két virtuális hálózati adapter telepítve van. Egy hálózati adapter csatlakozik a felügyeleti alhálózaton. Egy másik hálózati Adaptert a helyszíni hálózat vagy a más hálózatokból az SAP HANA-példányhoz csatlakozik az Azure-beli virtuális gépen.
- Statikus magánhálózati IP-címek mindkét virtuális hálózati adapterek vannak telepítve.

> [!NOTE]
> Azure-on keresztül statikus IP-címet rendelni azt jelenti, hogy hozzárendelhetők a virtuális hálózati adapterekhez. Statikus IP-címek belül a vendég operációs rendszer nem rendel hozzá egy virtuális hálózati adapterhez. Egyes Azure-szolgáltatásokhoz hasonlóan az Azure Backup támaszkodhat, hogy legalább az elsődleges virtuális hálózati adapter van beállítva, a DHCP és a statikus IP-címeket. További információkért lásd: [elhárítása Azure virtuális gépek biztonsági mentésének](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Több statikus IP-cím hozzárendelése virtuális Géphez, több virtuális hálózati adapterrel kell rendelni egy virtuális Gépet.
>
>

A-környezetek, amelyek támpontul van hozzon létre egy virtuális adatközpont hálózati architektúra az Azure-ban. Ez az architektúra az Azure virtuális hálózati átjáró, amely kapcsolódik a helyszíni egy külön Azure-beli virtuális hálózatban szétválasztása javasolja. A különálló virtuális hálózat üzemelteti a forgalmat, hogy a helyszínen vagy az interneten. Ezzel a megközelítéssel naplózása szoftver- és a virtuális adatközpont belép az Azure-ban a külön hub virtuális hálózatban lévő log forgalom helyezhet üzembe. Ezzel a módszerrel rendelkezik egy virtuális hálózatot üzemeltető összes szoftverek és konfigurációk, hogy az Azure-alapú bejövő és kimenő adatforgalom vonatkozik.


A virtual datacenter megközelítésével és a kapcsolódó Azure virtuális hálózat tervezési további információkért lásd: 

- [Az Azure virtual datacenter: A hálózati nézőpont](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter).
- [Az Azure virtual datacenter és a vállalati vezérlősík](https://docs.microsoft.com/azure/architecture/vdc/).


>[!NOTE]
>A központi virtuális hálózaton és a egy küllő virtuális hálózat között használatával haladó adatforgalom [Azure virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) további hatálya alá tartozik [költségek](https://azure.microsoft.com/pricing/details/virtual-network/). Azok a költségek alapján, szüksége lehet, hogy egy szigorú és-küllős hálózati tervezési fut, és több közötti feltörések [Azure ExpressRoute-átjáró](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) , hogy csatlakozni küllő virtuális hálózatok közötti társviszony megkerülése érdekében. 
>
> Az Azure ExpressRoute-átjáró bevezetése további [költségek](https://azure.microsoft.com/pricing/details/vpn-gateway/) túl. Emellett naplózni, naplózási és hálózati forgalom figyelésére használható külső szoftver további költségek léphetnek fel. Vegye figyelembe a költségeket az adatcseréhez társviszony-létesítés és a költségek további ExpressRoute-átjáró és a szoftverlicencek által létrehozott virtuális hálózaton keresztül. Dönthet egy virtuális hálózaton belüli mikroszegmentációt az elkülönítési egység helyett virtuális hálózatok, alhálózatok használatával.


IP-címek kiosztására használható különböző módszerek áttekintését lásd: [IP-címtípusokat és foglalási módszereket az Azure-ban](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Virtuális gépek, amelyek az SAP HANA futtatása esetén együttműködve hozzárendelt statikus IP-címeket. A hiba oka, hogy néhány konfigurációs attribútum Hana hivatkozhatnak az IP-címeket.

[Az Azure NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) forgalmat, annak biztosítására, hogy az SAP HANA-példány vagy a JumpBox irányíthatók. Az NSG-t, és végül [az alkalmazásbiztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) társított az SAP HANA-alhálózat és a felügyeleti alhálózaton.

Az alábbi képen egy durva telepítési séma áttekintése az SAP Hana-Központ-küllő virtuális hálózati architektúrát a következő:

![Nyers telepítési séma az SAP Hana-hoz](media/hana-vm-operations/hana-simple-networking.PNG)

Hely – hely kapcsolat nélkül az Azure-beli SAP HANA üzembe védelme biztosítható az SAP HANA-példány a nyilvános internetről, és elrejtéséhez továbbítói proxy mögött. Ez a alapvető forgatókönyvben az üzembe helyezés az Azure beépített DNS-szolgáltatások a gazdagépnevekhez támaszkodik. Egy összetettebb telepítésben lévő nyilvános IP-címeket használja, az Azure beépített DNS-szolgáltatások különösen fontosak. Azure NSG-k és [Azure hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) az internetről útválasztás az Azure virtuális hálózati architektúra az Azure-ban történő figyeléséhez. 

Az alábbi képen látható, az SAP HANA üzembe helyezése a Központ-küllő virtuális hálózat architektúra helyek közötti kapcsolat nélkül hozzávetőleges sémát:
  
![Nyers telepítési séma az SAP Hana-hoz egy helyek közötti kapcsolat nélkül](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Szabályozhatja és figyelheti a hozzáférés az Azure hálózati virtuális berendezések használata nélkül a Központ-küllő virtuális hálózati architektúra az internetről egy másik ismertetését lásd: [magas rendelkezésre állású virtuális berendezések üzembe helyezéséről](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>SAP HANA horizontális felskálázás az Azure-infrastruktúra konfigurálása
A Microsoft egy M-sorozat Virtuálisgép-Termékváltozat, amely egy SAP HANA kibővített konfigurációs minősítéssel rendelkezik. A virtuális gép M128s típusa egy legfeljebb 16 csomóponttal kibővített minősítéssel. Azure virtuális gépeken SAP HANA-kibővített minősítések változásairól, tekintse meg a [IaaS platformok listájához certified](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

A minimális operációsrendszer-kiadások kibővített konfigurációkhoz az Azure-beli virtuális gépek telepítéséhez használandó a következők:

- SUSE Linux 12 SP3 verziót kell használnia.
- Red Hat Linux 7.4.

A 16 csomópontos kibővített minősítésre:

- Egy csomópontnak számít a fő csomóponttal.
- Legfeljebb 15 csomópontok munkavégző csomópontokhoz.

>[!NOTE]
>Az Azure-beli Virtuálisgép-bővítő telepítés nincs lehetőség a készenléti csomópont használatára.
>

Miért nem lehet konfigurálni a készenléti csomópont két oka van:

- Az Azure ezen a ponton rendelkezik egyetlen natív NFS-szolgáltatás. NFS-megosztásokat, külső funkciók segítségével kell konfigurálni.
- A külső NFS konfigurációinak egyike teljesíteni tudja-e a tárolási késés feltételek az SAP Hana-hoz az üzembe helyezett Azure-beli megoldásuk.

Ennek eredményeképpen /hana/data és /hana/log kötetek nem oszthatók meg. Nem oszt meg ezeket a köteteket egyetlen csomópont megakadályozza, hogy az SAP HANA készenléti csomópont kibővített konfigurációban használatát.

Az alábbi képen egy csomópont esetén az alapvető tervezési kibővített konfigurációban:

![Horizontális felskálázás alapjait egyetlen csomópont](media/hana-vm-operations/scale-out-basics.PNG)

Az alapkonfiguráció egy virtuális gép csomópont SAP HANA kibővített hasonlóan néz ki:

- /Hana/shared, a fejleszt ki egy magas rendelkezésre állású NFS-fürtöt, SUSE Linux 12 SP3 alapján. Ez a fürt üzemelteti a kibővített konfigurációs és az SAP NetWeaver vagy BW/4HANA Central Services /hana/shared NFS-megosztásokat. Információ az ilyen konfiguráció létrehozása: [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
- Minden más lemezkötet nem megosztják egymással a különböző csomópontokon, és nem NFS alapján. Telepítési beállítások és lépések a horizontális felskálázás HANA telepítések makrókban /hana/data és /hana/log biztosítják a cikk későbbi részében.

>[!NOTE]
>Az NFS magas rendelkezésre állású fürt, amennyiben jelenik meg a grafikus elemek az SUSE Linux csak támogatott. Red hat-alapú NFS magas rendelkezésre állású megoldások javasolni fogja, később.

A csomópontok a kötetek méretezése a ugyanaz, mint a vertikális felskálázás, kivéve a /hana/shared. Az alábbi táblázat bemutatja a javasolt méretek és -típusok M128s VM-termékváltozat.

| A VM-TERMÉKVÁLTOZATOK | RAM | Virtuális gépek maximális i/o<br /> Átviteli sebesség | /hana/data | / hana/log | / root kötet | /usr/sap | Hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének megfelel-e futtatni kívánt számítási feladatokra. Ha a számítási feladatok nagyobb kötetek /hana/data és /hana/log igényel, az Azure premium storage VHD-k számának növelése. Méretezési egy további virtuális merevlemezeket, mint a felsorolt növeli az IOPS-kötettel és i/o-teljesítmény az Azure virtuális gép típusát keretein belül. A /hana/log kötetet alkotó lemezek Írásgyorsító is vonatkoznak.
 

Egy képletet, amely egy négy munkavégző csomópontok egyetlen feldolgozó csomópontonkénti memória méretét horizontális felskálázás/hana vagy megosztott kötet mérete határozza meg, lásd: [SAP HANA TDI tárhellyel kapcsolatos követelmények](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Feltételezve, hogy az SAP HANA kibővített minősített M128s Azure virtuális gép nagyjából 2 TB memóriával rendelkező választja, az SAP-ajánlásokat foglalja össze:

- Egy fő csomópontot és legfeljebb négy feldolgozó csomópontokat a /hana/shared kötet mérete 2 TB.
- Egy fő csomópontot és 5 – 8 feldolgozó csomópontokat a /hana/shared kötet mérete 4 TB-ig.
- Egy fő csomópontot és 9 és 12 feldolgozó csomópontokat a /hana/shared kötet mérete 6 TB.
- Egy fő csomópontot és 12 – 15 feldolgozó csomópontokat a /hana/shared kötet mérete 8 TB.

A többi fontos kialakítási, a képeket a horizontális felskálázás SAP HANA virtuális gép egyetlen csomópont-konfiguráció megjelenített a virtuális hálózat alhálózati konfigurációval. SAP erősen ajánlja az ügyfél és az alkalmazás-irányuló forgalom elkülönítése, a HANA-csomópontok közötti kommunikáció. 

E cél eléréséhez, csatolása két különböző virtuális hálózati adapter a virtuális gép, ahogyan az a grafikus. Mindkét virtuális hálózati adapterek külön alhálózatokon vannak, és két különböző IP-címet. Az NSG-k vagy a felhasználó által megadott útvonalak segítségével szabályozhatja a forgalom-útválasztási szabályok majd.

Az Azure-ban, különösen nincsenek azt jelenti, hogy és módszerek kényszerítéséhez a szolgáltatás és az adott virtuális hálózati adapter kvótái minőségét. Ennek eredményeképpen az ügyfél és az alkalmazás elérhető és a hitelesítést a csomóponton belüli kommunikáció szétválasztása nem nyílik meg minden olyan lehetőségeket a forgalmat egy stream rangsorolhatja a másikhoz képest. Ehelyett a elválasztása a hitelesítést a csomóponton belüli kommunikációhoz a horizontális felskálázás konfigurációk védelmi biztonsági intézkedés marad.  

>[!IMPORTANT]
>SAP erősen ajánlja, hogy a hálózati forgalmat az ügyfél és az alkalmazás és a hitelesítést a csomóponton belüli forgalom ebben a cikkben leírtak szerint, külön. Javasoljuk, hogy helyezzen egy architektúra helyen, ahogyan az előző képek.
>

Az alábbi képen egy hálózati szempontból minimálisan szükséges hálózati architektúráját mutatja be:

![Horizontális felskálázás alapjait egyetlen csomópont](media/hana-vm-operations/scale-out-networking-overview.PNG)

Eddig támogatott határértékeket 15 munkavégző csomópontok egy fő csomópont mellett.

Az alábbi képen látható a tároló szempontjából a tároló-architektúra:


![Horizontális felskálázás alapjait egyetlen csomópont](media/hana-vm-operations/scale-out-storage-overview.PNG)

Az NFS-megosztás magas rendelkezésre állású konfigurációban a /hana/shared köteten található. Az egyes virtuális gépeket a helyileg csatlakoztatott összes többi meghajtón. 

### <a name="highly-available-nfs-share"></a>Magas rendelkezésre állású az NFS-megosztások
Az eddigi az NFS magas rendelkezésre állású fürt dolgoznak SUSE Linux csak. A telepítő információkért lásd: [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Ha más HANA konfigurációs kívül az Azure virtuális hálózat, amely futtatja az SAP HANA-példányok nem osztjuk meg az NFS-fürtöt, telepítse az azonos virtuális hálózatba. Telepítse a saját alhálózatán található, és győződjön meg arról, hogy nem minden tetszőleges forgalom hozzáférhet az alhálózat. Ehelyett korlátozza a forgalmat az adott alhálózat IP-címek a virtuális gép hajtsa végre a forgalom /hana/shared kötetre.

A javaslatok láthatók, HANA horizontális felskálázás a virtuális gépek, amelyek a /hana/shared forgalmat irányítja a virtuális hálózati adapter kapcsolódik:

- Mivel /hana/shared forgalom mérsékelt, irányíthatja át a virtuális hálózati Adaptert, amely az ügyfél-hálózatot is a minimális konfigurációs van rendelve.
- Végül, / hana/megosztott irányuló forgalom továbbítására, üzembe helyezése a virtuális hálózat, ahol az SAP HANA kibővített konfiguráció üzembe helyezése a harmadik alhálózat. Rendeljen hozzá egy harmadik virtuális hálózati Adaptert az alhálózatban lévő üzemeltetett. A harmadik virtuális hálózati adapter és a társított IP-címet használja az NFS-megosztások a forgalmat. Ezután külön hozzáférés és alkalmazhat útválasztási szabályokat.

>[!IMPORTANT]
>Előfordulhat, hogy az SAP HANA üzembe helyezett kibővíthető módon rendelkező virtuális gépeken és a magas rendelkezésre állású NFS semmilyen körülmények közötti hálózati forgalom irányítása a [hálózati virtuális berendezésen](https://azure.microsoft.com/solutions/network-appliances/) vagy hasonló virtuális készülékek. Azure NSG-kkel nincs ilyen típusú eszközök. Ellenőrizze, hogy az SAP HANA rendszerű virtuális gépek megosztani az útválasztási szabályokat, győződjön meg arról, hogy hálózati virtuális berendezések vagy hasonló virtuális berendezéseket detoured vannak a magas rendelkezésre állású NFS elérésekor.
> 

Ha meg szeretné osztani a SAP HANA-konfigurációk közötti a magas rendelkezésre állású NFS-fürtöt, helyezze át minden HANA konfigurációkat az azonos virtuális hálózatban. 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>Telepítse az SAP HANA horizontális felskálázás az Azure-ban
Horizontális felskálázás SAP-konfiguráció telepítéséhez kövesse az alábbi általános lépéseket:

- Új üzembe, vagy érdekében új Azure virtuális hálózat infrastruktúráját.
- A új virtuális gépek üzembe helyezése az Azure által felügyelt prémium szintű tároló kötetek.
- Üzembe helyezése egy új vagy meglévő magas rendelkezésre állású NFS fürt alakítása a.
- Alkalmazkodjon a hálózati útválasztást, győződjön meg arról, hogy, például hitelesítést a csomóponton belüli virtuális gépek közötti kommunikáció nem keresztül irányítja át, egy [hálózati virtuális berendezésen](https://azure.microsoft.com/solutions/network-appliances/). Ugyanez igaz a virtuális gépek és az NFS magas rendelkezésre állású fürt közötti forgalom.
- Telepítse a SAP HANA fő csomóponttal.
- Alkalmazkodjon a SAP HANA-főcsomópont konfigurációs paramétereket.
- Az SAP HANA-feldolgozó csomópontokat, a telepítés folytatásához.

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>Horizontális felskálázás konfigurációban az SAP HANA telepítése
Az Azure-beli Virtuálisgép-infrastruktúra telepíti, és minden egyéb előkészített befejezte volna. Most az SAP HANA kibővített konfigurációkhoz telepítéséhez kövesse az alábbi lépéseket:

- Telepítse az SAP HANA főcsomópont SAP dokumentáció alapján.
- *A telepítés után módosítja a global.ini fájlt, és adja hozzá a paraméter "basepath_shared = nem", a global.ini*. Ez a paraméter lehetővé teszi, hogy az SAP HANA futtatásához a horizontális felskálázás megosztott /hana/data és /hana/log kötetek nélkül a csomópontok között. További információkért lásd: [SAP Megjegyzés #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Után módosítja a global.ini paraméter, indítsa újra az SAP HANA-példány.
- Adja hozzá a további feldolgozó csomópontokat. További információkért lásd: [SAP HANA-felügyeleti útmutató](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html). Adja meg az SAP HANA-csomópontok közötti kommunikáció a telepítés során vagy később használatával, például a helyi hdblcm a belső hálózathoz. További információkért lásd: [SAP Megjegyzés #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

A telepítő rutin a következő a kibővített konfigurációs telepített /hana/data és /hana/log futtatásához használja a megosztott lemezeket. A/hana vagy megosztott kötet helyezkedik el, a magas rendelkezésre állású az NFS-megosztások.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>A dinamikus rétegezési 2.0 SAP HANA Azure-beli virtuális gépek

Mellett az M-sorozatú Azure virtuális gépeken SAP HANA-tanúsítvánnyal a SAP HANA dinamikus rétegezési 2.0 (DT 2.0) használata is támogatott a Microsoft Azure-ban. SAP HANA dinamikus rétegezési dokumentációs hivatkozásokat című témakör "Hivatkozik DT 2.0 dokumentáció" a cikk későbbi részében. Nincs különbség a termék telepítése vagy működő, például egy Azure virtuális gépen, az SAP HANA Vezérlőpulton keresztül azonban néhány fontos elem kötelező a hivatalos támogatást az Azure-ban. Az alábbi szakaszokban található alábbi alapvető szempontokat ismerteti. 

Az SAP HANA DT 2.0 SAP BW vagy S4HANA által nem támogatott. A fő használati esetek most olyan natív HANA-alkalmazásokat.


### <a name="overview"></a>Áttekintés

Az alábbi képen egy DT 2.0 támogatása áttekintést ad a Microsoft Azure-on. Hajtsa végre a hivatalos hitelesítésszolgáltató ahhoz, hogy ezek kötelező követelményeknek:

- DT 2.0 egy dedikált Azure virtuális Gépen kell telepíteni. Előfordulhat, hogy nem az azonos virtuális gépen, ahol fut, az SAP HANA futtatásához.
- SAP HANA és DT 2.0-s virtuális gépek az adott Azure virtuális hálózaton belül kell telepíteni.
- Az SAP HANA és DT 2.0-s virtuális gépeket kell üzembe helyezni az Azure gyorsított hálózatkezelés engedélyezett.
- A tárolótípus DT 2.0-alapú virtuális gépek az Azure premium storage kell lennie.
- Több Azure-lemezek DT 2.0-s virtuális géphez kell csatlakoztatni.
- Létrehozása a szoftveres RAID vagy csíkozott köteten, vagy LVM vagy mdadm, szükség van az Azure-lemezek között csíkozást használatával.

A következő szakaszok további magyarázatot.

![Az SAP HANA DT 2.0 architektúrájának áttekintése](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Az SAP HANA DT 2.0 dedikált Azure virtuális gép

Az Azure IaaS-DT 2.0 csak dedikált virtuális gépeken támogatott. DT 2.0 az azonos Azure virtuális gépen, ahol a HANA-példány fut. futtatása nem engedélyezett. Kétféle virtuális gép kezdetben, az SAP HANA DT 2.0-val használható:

- M64-32ms 
- E32sv3 

Virtuális gép típusa leírásáért lásd: [memóriahasználatra optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Az alapszintű elgondolásra épül, DT 2.0-s, amely költségek csökkentése érdekében meleg adatok költségszempontok, adott logikus megfelelő Virtuálisgép-méretek használata. Nincs a lehetséges kombinációkra szigorú szabály. Ez függ az adott ügyfél számítási feladata.

Az alábbi táblázat ajánlott konfigurációkat.

| SAP HANA virtuális gép típusa | DT 2.0-s virtuális gép típusa |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


M sorozatú virtuális gépek az SAP HANA-tanúsítvánnyal rendelkező támogatott DT 2.0-alapú virtuális gépekhez, például M64-32ms és E32sv3, minden kombinációja is előfordulhatnak.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Az Azure-hálózatok és az SAP HANA DT 2.0

A DT 2.0-s virtuális gép és az SAP HANA virtuális gép legalább 10 GB-os közötti hálózati átviteli sebesség DT 2.0 telepítését egy dedikált virtuális gépen van szükség. Ennek eredményeképpen legyen kötelező a azonos Azure virtuális hálózaton belüli összes virtuális gép elhelyezése, illetve az Azure gyorsított hálózatkezelés engedélyezéséhez.

Az Azure gyorsított hálózatkezeléssel kapcsolatos további információkért lásd: [hozzon létre egy Linux rendszerű virtuális gép gyorsított hálózatkezelésű](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

### <a name="vm-storage-for-sap-hana-dt-20"></a>Az SAP HANA DT 2.0 Virtuálisgép-tároló

Megfelelően DT 2.0 ajánlott eljárások útmutatást a lemez i/o-teljesítmény minimális 50 MB/s fizikai magonként. A specifikációja DT 2.0 támogatja, két Azure-beli Virtuálisgép-típusok Hibaoldal a maximális lemez IO-átviteli sebességhatár a virtuális gép van:

- **E32sv3**:   768 MB/s, nem gyorsítótárazott, ami azt jelenti, hogy 48 MB/mp / fizikai mag arány
- **M64-32ms**:  1000 MB/s, nem gyorsítótárazott, ami azt jelenti, hogy a fizikai magonként 62,5 MB/mp-es

Szükség a több Azure-lemezek csatolása a virtuális gép DT 2.0 és a egy szoftveres RAID létrehozása csíkozást használatával az operációs rendszer szintjén, a maximális adatátviteli sebességet érhet el. Egyetlen Azure lemez nem tud biztosítani az átviteli sebességet a virtuális gép maximálisan eléréséhez. Az Azure premium storage szolgáltatás kötelező DT 2.0-val. 

- Elérhető Azure-lemeztípusokkal kapcsolatos további információkért lásd: [válassza ki a lemez típusát az Azure IaaS Windows virtuális gépek](../../windows/disks-types.md).
- Szoftveres RAID-n keresztül mdadm létrehozásával kapcsolatos további információkért lásd: [egy szoftveres RAID linuxon konfigurálása](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
- Maximális átviteli sebességet a csíkozott kötetek létrehozása LVM konfigurálásával kapcsolatos további információkért lásd: [LVM konfigurálása az Azure-beli Linuxos virtuális gépre](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).

Méret követelményeitől függően különböző lehetőség van a virtuális gépek maximális átviteli sebesség eléréséhez. Az alábbiakban a lehetséges kötet lemezkonfigurációja VM átviteli sebesség felső korlátjának elérése érdekében minden DT 2.0-s VM-típus. A E32sv3 VM számít egy bejegyzés szint kisebb számítási feladatokhoz. Ha nem elég gyors, méretezze át a virtuális Gépet M64-32ms szükség lehet.

A M64-32ms virtuális gépen a rendszer nagy mennyiségű memóriát, mivel az i/o-terhelést nem lehet, hogy eléri a korlátot, különösen a olvasásigényű munkaterhelésekhez. Lehet, hogy a stripe-csoportban kevesebb lemezt elegendő specifikus függően. Ahhoz, a biztonság kedvéért, a következő konfigurációk maximális átviteli sebesség biztosításához lettek kiválasztva.


| A VM-TERMÉKVÁLTOZATOK | Lemezkonfiguráció 1 | Lemezkonfiguráció 2 | Lemezkonfiguráció 3 | Lemez konfigurálása 4 | Lemezkonfiguráció 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1.5 TB | 


Különösen, ha a munkaterhelés olvasásigényű, adatbázis szoftver adatmennyiség ajánlott az Azure-beli gazdagéppel gyorsítótár "csak Olvasás" beállítás bekapcsolásával előfordulhat, hogy miként növelhető a i/o-teljesítményt. A tranzakciós napló az Azure-beli gazdagéppel lemezgyorsítótár lehet "none." 

A kiindulási pont, amely a naplózási kötet méretének javasoljuk a heurisztika az adatok mérete 15 százaléka. A naplózási kötet létrehozásához használja a különböző Azure-lemeztípusokkal költségek és az átviteli sebesség követelményeitől függően. A naplózási kötetnek magas i/o-teljesítmény szükség. 

Ha a virtuális gép M64-32ms írja, javasoljuk, hogy engedélyezze a [Írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Írásgyorsító egy Azure-funkció, amely a tranzakciónapló biztosít optimális lemez írási késése. Érhető el csak az M sorozat. Nincsenek megfontolandó, például a Virtuálisgép-típusonként lemezek maximális számát. Az Írásgyorsító további információkért lásd: [Írásgyorsító engedélyezése](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


Az alábbi táblázat segítséget nyújt a méretezés a naplózási kötetnek néhány példát.

| adatok kötet méretét és a lemez típusa | lemez és a naplózási kötet írja be a konfiguráció 1 | lemez és a naplózási kötet írja be a konfigurációs 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


SAP HANA kibővített hasonlóan a /hana/shared könyvtárat kell osztani az SAP HANA virtuális gép és a DT 2.0-s virtuális gép között. Azt javasoljuk, hogy használja-e az azonos architektúra, mint az SAP HANA kibővített dedikált virtuális gépeit, amelyek az NFS magas rendelkezésre állású kiszolgálója használatával. Egy közös biztonsági mentési mennyiségi használja az azonos tervezési. Azonban Ön döntheti el, ha a magas rendelkezésre állás szükséges, vagy ha elegendő, ha elegendő tárolókapacitással használata egy dedikált virtuális gép biztonsági mentési kiszolgálóként való.



### <a name="links-to-dt-20-documentation"></a>DT 2.0 parancssori felületdokumentációját mutató hivatkozások 

- [SAP HANA dinamikus rétegezési telepítési és frissítési útmutató](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA dinamikus rétegezési oktatóanyagok és források](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dinamikus rétegezési a koncepció igazolása](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Az SAP HANA 2.0 Szervizcsomagok 02 dinamikus rétegezési fejlesztései](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operations for SAP HANA az Azure virtuális gépek üzembe helyezése
A következő szakaszok ismertetik az Azure virtuális gépeken futó SAP HANA rendszereit telepítésével kapcsolatos műveleteket.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Biztonsági mentése és helyreállítása Azure virtuális gépeken való műveletek
A következő dokumentumok bemutatják, hogyan biztonsági mentése és visszaállítása az SAP HANA üzembe:

- [SAP HANA biztonsági mentés áttekintése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA fájlszintű biztonsági mentés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA tárolási pillanatkép teljesítményteszt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Indítsa el, és indítsa újra a virtuális gépek, amelyek tartalmazzák az SAP HANA
Az Azure nyilvános felhő kiemelkedő funkciója, hogy díjkötelesek csak a számítási perc. Például az SAP HANA rendszerű virtuális gép leállításakor díjköteles csak a tárolási költségeket ebben az időszakban. Egy másik szolgáltatás a statikus IP-címek a virtuális gépek számára az első központi telepítésben megadása esetén érhető el. Ha újraindítja a virtuális gép, amely rendelkezik az SAP HANA, a virtuális gép újraindul, a korábbi IP-címmel. 


### <a name="use-saprouter-for-sap-remote-support"></a>SAP távoli támogatáshoz SAProuter használja
Ha helyszíni helyhez és az Azure között helyek közötti kapcsolatot, és futtatja az SAP-összetevők, valószínűleg futtat SAProuter. Ebben az esetben kövesse ezeket a lépéseket távoli támogatása:

- A magán- és statikus IP-cím a virtuális gép a SAProuter konfigurációban futtató SAP HANA kezelése.
- Az NSG-t, amelyen a HANA virtuális Gépet a TCP/IP-port 3299 engedélyezett a forgalom az alhálózat megadása

Ha az Azure-t az internethez csatlakozik, és a egy SAP-útválasztó nem rendelkezik a virtuális gép az SAP HANA, telepítse az összetevőt. Telepítse a SAProuter egy külön virtuális gépre, a felügyeleti alhálózaton található. 

Az alábbi képen látható, az SAP HANA telepítése nélkül a helyek közötti kapcsolat és SAProuter hozzávetőleges sémát:

![Üzembe helyezés séma nyers az SAP Hana-hoz egy helyek közötti kapcsolat és SAProuter nélkül](media/hana-vm-operations/hana-simple-networking3.PNG)

Mindenképp SAProuter telepítsen egy külön virtuális gépre, és nem a JumpBox virtuális Géphez. A különálló virtuális gép statikus IP-címmel kell rendelkeznie. A SAProuter csatlakozni az SAP által futtatott SAProuter, lépjen kapcsolatba az SAP egy IP-címet. Az SAP által futtatott SAProuter a többszörözi a virtuális Gépen telepít SAProuter példány. Az SAP-IP-cím segítségével konfigurálhatja a SAProuter példány. A konfigurációs beállításokat a csak a szükséges port TCP-port 3299.

Állítsa be, és távoli támogatási kapcsolatok keresztül SAProuter felügyeletével kapcsolatban lásd: [SAP dokumentációjában](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Az Azure natív virtuális gépein az SAP HANA magas rendelkezésre állás
Ha futtatta a SUSE Linux Enterprise Server SAP alkalmazások 12 SP1 vagy újabb verzió, támasztja fürt STONITH eszközökkel is létrehozhat. Az eszközök használatával egy SAP HANA-konfigurációt, amely a szinkron replikációt használ a HANA-Rendszerreplikálást és automatikus feladatátvételi beállítása. A telepítési eljárás kapcsolatos további információkért lásd: [SAP HANA magas rendelkezésre állású útmutató az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
