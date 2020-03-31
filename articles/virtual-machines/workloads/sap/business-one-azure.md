---
title: SAP Business One az Azure virtuális gépeken | Microsoft dokumentumok
description: SAP Business One az Azure-ban.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18409f93ab50f7d031ec78a55b9eaf8ad1b85a49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101414"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One az Azure Virtual Machinesban
Ez a dokumentum útmutatást nyújt az SAP Business One azure-beli virtuális gépeken való üzembe helyezéséhez. A dokumentáció nem helyettesíti az SAP-hoz tartozó Business one telepítési dokumentációját. A dokumentációnak ki kell terjednie az Azure-infrastruktúra alapvető tervezési és üzembe helyezési irányelveire a Business One-alkalmazások futtatásához.

A Business One két különböző adatbázist támogat:
- SQL Server - [lásd: SAP Note #928839 – A Microsoft SQL Server kiadásának tervezése](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - az SAP HANA pontos SAP Business One támogatási mátrixához, az [SAP termék rendelkezésre állási mátrixa](https://support.sap.com/pam)

Az SQL Server esetében az Azure Virtual [Machines DBMS-telepítés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) az SAP NetWeaver hez című központi telepítési szempontjai érvényesek. az SAP HANA esetében a szempontokat ez a dokumentum is megemlíti.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató használatához alapvető ismeretekre van szüksége a következő Azure-összetevőkről:

- [Azure virtuális gépek Windows rendszeren](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Azure virtuális gépek Linuxon](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-hálózatok és virtuális hálózatok kezelése a PowerShell segítségével](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Azure-hálózatés virtuális hálózatok CLI-vel](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure-lemezek kezelése az Azure CLI használatával](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Még akkor is, ha érdekli az üzleti csak egy, a dokumentum [Az Azure virtuális gépek tervezése és megvalósítása az SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) lehet egy jó információforrás.

A feltételezés az, hogy az SAP Business One-t üzembe helyező példányként ön a következő:

- Az SAP HANA telepítése egy adott infrastruktúrára, például egy virtuális gépre
- Ismerős az SAP Business One alkalmazás telepítése olyan infrastruktúrára, mint az Azure virtuális gépek
- Az SAP Business One és a választott DBMS rendszer működésének ismerete
- Az azure-beli infrastruktúra üzembe helyezésének ismerete

Mindezek a területek nem tartoznak ebbe a dokumentumba.

Az Azure dokumentációja mellett ismernie kell a fő SAP-megjegyzéseket, amelyek az Egyes üzletágra vagy az SAP for Business One központi jegyzeteire hivatkoznak:

- [528296 - Általános áttekintés imbolika az SAP Business One kiadásokhoz és a kapcsolódó termékekhez](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - Az SAP Business One 9.2-es verziójának kiadási frissítései, az SAP HANA verziója](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - Központi megjegyzés az SAP Business One számára 9,3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - Az SAP Business One 9.3 kiadási frissítéseinek megjegyzése](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - Kollektív megjegyzés az SAP Business One számára 9,3 Általános kérdések](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - Kollektív tanácsadási megjegyzés az SAP HANA-val kapcsolatos SAP Business One témaköreihez, az SAP HANA verziójához](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Business One architektúra
A Business One egy olyan alkalmazás, amely két szinttel rendelkezik:

- Ügyfélszint "kövér" ügyféllel
- Egy bérlő adatbázissémáját tartalmazó adatbázisréteg

[Az SAP Business One rendszergazdai útmutatója](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) jobb áttekintést nyújt abból, hogy mely összetevők futnak az ügyfélrészben, és mely alkatrészek futnak a kiszolgálórészen. 

Mivel az ügyfélréteg és a DBMS-szint között súlyos késés kritikus kölcsönhatás van, mindkét rétegnek az Azure-ban kell lennie az Azure-ban való üzembe helyezéskor. általában az, hogy a felhasználók a Távoli asztali szolgáltatások szolgáltatásba egy vagy több virtuális gépet tartalmaznak, amelyek a Business One ügyfélösszetevőihez távoli asztali szolgáltatások szolgáltatását futtatják.

### <a name="sizing-vms-for-sap-business-one"></a>Virtuális gépek méretezése az SAP Business One számára

Az ügyfél virtuális gépeinek méretezését illetően az SAP dokumentálja az erőforrás-követelményeket az [SAP Business One hardverkövetelmények útmutatójában.](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf) Az Azure esetében a dokumentum 2.4.

A Business One ügyfél-összetevők és a DBMS-gazdagépek üzemeltetésére szolgáló Azure virtuális gépekként csak az SAP NetWeaver által támogatott virtuális gépek engedélyezettek. Az SAP NetWeaver által támogatott Azure-virtuális gépek listájának megkereséséhez olvassa el [az SAP Note #1928533.](https://launchpad.support.sap.com/#/notes/1928533)

Az SAP HANA futtatása a DBMS-háttérkapcsolat a Business One, csak a virtuális gépek, amelyek szerepelnek a [HANA certifeid IaaS platformlista hantifeid iaas platformlista](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) hana támogatott hana. A Business One ügyfél-összetevők nem érinti ez az erősebb korlátozás az SAP HANA dbms-rendszerként.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Az SAP Business One operációs rendszerének kiadásai

Elvileg mindig a legjobb, ha a legújabb operációs rendszer kiadások. Különösen a Linux-térben vezették be az új Azure-funkciókat a Suse és a Red Hat különböző újabb kisebb kiadásaival. A Windows oldalán erősen ajánlott a Windows Server 2016 használata.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Infrastruktúra üzembe helyezése az Azure for SAP Business One-ban
A következő néhány fejezetben az SAP üzembe helyezéséhez fontos infrastruktúra-darabok.

### <a name="azure-network-infrastructure"></a>Azure hálózati infrastruktúra
Az Azure-ban üzembe helyezve szükséges hálózati infrastruktúra attól függ, hogy egyetlen Business One-rendszert telepít-e saját maga számára. Vagy akár egy hoster, aki ad otthont több tucat Business One rendszerek az ügyfelek számára. Előfordulhat, hogy a kialakítás is kisebb változásokat, hogy hogyan csatlakozik az Azure-hoz. A különböző lehetőségeken keresztül, egy olyan kialakítás, ahol VPN-kapcsolattal rendelkezik az Azure-ba, és ahol [vpn-en](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) vagy [ExpressRoute-on](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) keresztül bővíti az Active Directoryt az Azure-ba.

![Egyszerű hálózati konfiguráció a Business One-nal](./media/business-one-azure/simple-network-with-VPN.PNG)

A bemutatott egyszerűsített konfiguráció számos olyan biztonsági példányt vezet be, amelyek lehetővé teszik az útválasztás szabályozását és korlátozását. Úgy kezdődik, 

- Az útválasztó/tűzfal a helyszíni ügyféloldalán.
- A következő példány az [Azure Network Security Group,](https://docs.microsoft.com/azure/virtual-network/security-overview) amely segítségével bevezetheti az útválasztási és biztonsági szabályok at az Azure Virtuális hálózat, amely futtatja az SAP Business one konfiguráció.
- Annak elkerülése érdekében, hogy a Business One ügyfél felhasználói is láthatják a kiszolgálót, amely az adatbázist futtatja a Business One kiszolgálót, el kell különítenia az egy ügyfélnek üzemeltető virtuális géptől és az üzleti egy kiszolgálótól a virtuális hálózaton belül két különböző alhálózatban.
- A business one kiszolgálóhoz való hozzáférés korlátozása érdekében a két különböző alhálózathoz rendelt Azure NSG-t használja.

Az Azure hálózati konfigurációkifinomultabb verziója az Azure által dokumentált, a hub és a [küllőarchitektúra ajánlott gyakorlatain](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)alapul. A hub és a spoke architektúrájának mintája az első egyszerűsített konfigurációt a következőhöz hasonlóra változtatná:


![Hub és küllős konfiguráció a Business One-nal](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Azokban az esetekben, amikor a felhasználók az interneten keresztül, privát kapcsolat nélkül csatlakoznak az Azure-hoz, a hálózat azure-beli kialakítását össze kell hangolni az [Azure és az internet közötti DMZ](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)Azure-referenciaarchitektúrájában dokumentált alapelvekhez.

### <a name="business-one-database-server"></a>Business One adatbázis-kiszolgáló
Az adatbázis típusához az SQL Server és az SAP HANA érhetők el. Függetlenül a DBMS, olvassa el a dokumentumot szempontok az [Azure virtuális gépek DBMS üzembe helyezés sap számítási feladatok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) általános megértéséhez a DBMS-telepítések az azure virtuális gépek és a kapcsolódó hálózati és tárolási témakörök.

Bár az egyedi és általános adatbázis-dokumentumokmár hangsúlyozva vannak, ismerkedjen meg a következőkkel:

- [A Windows virtuális gépek azure-beli elérhetőségének kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) és [a Linux-alapú virtuális gépek azure-beli elérhetőségének kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [A virtuális gépekre vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Ezek a dokumentumok segíthetnek a tárolási típusok kiválasztásának és a magas rendelkezésre állású konfigurációkiválasztásának eldöntésében.

Elvileg a következőket kell tennie:

- Prémium szintű SSD-ket használhat a standard HDD-k felett. Ha többet szeretne megtudni a rendelkezésre álló lemeztípusokról, olvassa el a [Lemeztípus kiválasztása című](../../windows/disks-types.md) cikkünket.
- Azure felügyelt lemezek használata nem felügyelt lemezeken
- Győződjön meg arról, hogy rendelkezik a lemezkonfigurációval beállított megfelelő IOPS és I/O átviteli
- Kombinálja a /hana/data és a /hana/log kötetet a költséghatékony tárolási konfiguráció érdekében


#### <a name="sql-server-as-dbms"></a>SQL Server adatbázis-kezelő ként
Az SQL Server DBMS for Business One szolgáltatásként való üzembe helyezéséhez folytassa végig az [SQL Server Azure Virtual Machines DBMS telepítését az SAP NetWeaver számára.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver) 

Az SQL Server DBMS-oldalán a következő durva méretezési becslések:

| Felhasználók száma | vCPU-k | Memory (Memória) | Példa virtuálisgép-típusokra |
| --- | --- | --- | --- |
| akár 20 | 4 | 16 GB | D4s_v3 E4s_v3 E4s_v3 E4s_v3 |
| akár 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| akár 80 | 16 | 64 GB | E16s_v3. D16s_v3. |
| akár 150 | 32 | 128 GB | E32s_v3 E32s_v3 D32s_v3 |

A fent felsorolt méretezésnek ötletet kell adnia, hogy hol kezdjem. Előfordulhat, hogy kevesebb vagy több erőforrásra van szüksége, ebben az esetben az azure-adaptáció egyszerű. A virtuálisgép-típusok közötti módosítás csak a virtuális gép újraindításával lehetséges.

#### <a name="sap-hana-as-dbms"></a>SAP HANA dbms-ként
Az SAP HANA használatával DBMS-ként a következő szakaszokban kövesse az SAP HANA az [Azure-műveletek útmutatójának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)a dokumentum szempontjait.

Az SAP HANA azure-beli azure-beli adatbázisként való magas rendelkezésre állású és vész-helyreállítási konfigurációk esetében olvassa el az [SAP HANA magas rendelkezésre állását](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) az Azure virtuális gépek számára, és az adott dokumentumból származó dokumentációt.

Az SAP HANA biztonsági mentési és visszaállítási stratégiák, olvassa el a dokumentum [biztonsági mentési útmutató az SAP HANA az Azure virtuális gépeken,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) és a dokumentáció tanod az adott dokumentumból.

 
### <a name="business-one-client-server"></a>Business One ügyfélkiszolgáló
Ezeknél az összetevőknél a tárolási szempontok nem az elsődleges szempont. mindazonáltal, ha azt szeretnénk, hogy egy megbízható platform. Ezért az Azure Premium Storage-t kell használnia ehhez a virtuális géphez, még az alap virtuális merevlemezhez is. A virtuális gép méretezése az [SAP Business One hardverkövetelmények útmutatójában](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)megadott adatokkal. Az Azure esetében a dokumentum 2.4. A követelmények kiszámításakor össze kell hasonlítania őket a következő dokumentumokkal, hogy megtalálja az ideális virtuális gépet az Ön számára:

- [A Windows rendszerű virtuális gépek méretei az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [SAP megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Hasonlítsa össze a szükséges processzorok és memória számát a Microsoft által dokumentált memóriával. A virtuális gépek kiválasztásakor tartsa szem előtt a hálózati átviteli szintet is.








