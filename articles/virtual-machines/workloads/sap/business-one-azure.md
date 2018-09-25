---
title: SAP Business egy on Azure Virtual Machines |} A Microsoft Docs
description: SAP Business on Azure egy.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e8bd5ddab4553807f59b7afdf32fbfc1703e3d75
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949533"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business egy on Azure Virtual machines szolgáltatásban
Ez a dokumentum nyújt útmutatást, üzembe helyezéséhez SAP Business One az Azure Virtual machines szolgáltatásban. A dokumentáció nem helyettesíti a üzleti SAP-dokumentáció áll. A dokumentáció az Azure-infrastruktúra egy üzleti alkalmazások futtatásához az alapvető tervezési és telepítési irányelveket kell kiterjednie.

Üzleti egy két különböző adatbázisokat támogatja:
- Az SQL Server - lásd [SAP Megjegyzés #928839 – Microsoft SQL Server Tartalomkiadás tervezése](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - pontos SAP HANA, SAP Business One támogatási mátrixa kivétele a [SAP-termék rendelkezésre állási mátrix](https://support.sap.com/pam)

SQL Server, az alapszintű telepítésével kapcsolatos megfontolások kapcsolatos leírtak szerint a [SAP NetWeaver az Azure Virtual Machines DBMS üzembe](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) vonatkozik. az SAP Hana-hoz szempontok szerepelnek ebben a dokumentumban.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató használatához az alábbi Azure-összetevők alapszintű ismeretét szüksége:

- [A Windows Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Az Azure virtual machines a Linux rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure hálózati és virtuális hálózatok kezelése a PowerShell segítségével](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Az Azure-hálózatok és virtuális hálózatok a CLI használatával ](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Az Azure CLI használatával Azure-lemezek kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Akkor is, ha az egyik üzleti érdekli, csak a dokumentumok [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) egy kiváló forrásként is lehet.

Feltételezzük, hogy a példány üzembe helyezéséhez SAP Business One áll:

- Ismerős egy adott infrastruktúrát, például egy virtuális Gépet az SAP HANA telepítése
- Az SAP Business One alkalmazás-infrastruktúrát, például az Azure virtuális gépek telepítéséhez ismerős
- SAP Business One és a kiválasztott adatbázis-kezelő rendszer működő ismerik
- Ismeri az Azure-beli infrastruktúra telepítése

Ezek a területek nem ebben a dokumentumban tárgyalt.

Mellett az Azure dokumentációja fő SAP-megjegyzések, amelyre hivatkozik egy üzleti, vagy amelyek az SAP central megjegyzések egy vállalati tisztában kell lennie:

- [528296 – SAP Business egy kiadásainak listáját és a kapcsolódó termékekkel általános áttekintés Megjegyzés](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - kiadás frissítéseket Megjegyzés az SAP Business egy 9.2, az SAP HANA-verzió](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 – az SAP Business Kanada Megjegyzés egy 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - kiadás frissítése Megjegyzés for SAP Business egy 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - az SAP Business kollektív Megjegyzés egy 9.3 általános problémák](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - kollektív tanácsadási Megjegyzés SAP HANA-Related témakörök az SAP Business One, az SAP HANA-verzió](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Egy üzleti-architektúra
Üzleti egy olyan alkalmazás két szinten:

- Egy ügyfél réteget az "fat" ügyfél
- A bérlő az adatbázissémát tartalmazó adatbázis-rétegből

A jobb áttekintése, amely összetevő fut-e az ügyfél részben, és amely részek futnak a kiszolgálón lévő leírása itt található [SAP Business egy rendszergazdai útmutatója](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Mivel az ügyfél és az adatbázis-kezelő réteget kritikus belőle nagy késést, mindkét rétegben kell az Azure-ban üzembe helyezésekor az Azure-ban található. Fontos, hogy a felhasználók ezután be egy vagy több virtuális gépet egy távoli asztali szolgáltatások futtatása távoli asztali szolgáltatások service az üzleti egy ügyfél összetevők szokásos.

### <a name="sizing-vms-for-sap-business-one"></a>Virtuális gépek méretezési for SAP Business egy

Kapcsolatos, az ügyfél virtuális gép a méretezés, az erőforrás-követelmények vannak dokumentálva az SAP a dokumentum [SAP Business egy hardver Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Azure-ban hogy a dokumentum 2.4 fejezetben ismertetett követelményeknek kiszámítása, és szüksége.

Az Azure virtuális gépként az üzleti egy ügyféloldali összetevőit és az adatbázis-kezelő gazdagép üzemeltetésére csak virtuális gépek szerepelnek a támogatott SAP NetWeaver engedélyezettek. Című témakörben találja meg az SAP NetWeaver a támogatott Azure-beli virtuális gépek, [SAP Megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533).

SAP HANA DBMS-háttérrendszer futtató for Business One csak virtuális gépeit, amelyek fel vannak sorolva referenciaként on HANA, az üzleti a [HANA certifeid IaaS platformok listáját](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) Hana támogatottak. Az üzleti egy ügyfél összetevők nem érinti a erősebb korlátozás az SAP Hana DBMS rendszerként.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Az SAP Business One használandó operációs rendszer kiadásai

Alapvetően, mindig érdemes választani, a legújabb operációsrendszer-kiadások. A Linuxos térben, különösen az új Azure-szolgáltatásokat a Suse és a Red Hat, különböző újabb kisebb kiadásokban jelent meg. A Windows oldalon a Windows Server 2016 használatával erősen ajánlott.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Az Azure-infrastruktúra telepítése az SAP Business One
A következő néhány fejezeteiben az infrastruktúra építőkockákat, függetlenül attól, hogy az SAP üzembe helyezéséhez.

### <a name="azure-network-infrastructure"></a>Az Azure hálózati infrastruktúra
A hálózati infrastruktúra üzembe kell helyeznie az Azure-ban a e maga telepít egyetlen több üzleti rendszer függ. Vagy hogy-e ki egy üzleti rendszerek tucat üzemelteti az ügyfelek egy tárhelyszolgáltató. Is lehet kisebb módosítások a kialakításban az e csatlakoztatásának módja az Azure-bA. Áthaladna különböző lehetőségeit, egy tervezési amelyekben egy VPN-kapcsolat az Azure-bA és kiterjeszti az Active Directoryban hol [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) az Azure-bA.

![Az üzleti egy egyszerű hálózati konfiguráció](./media/business-one-azure/simple-network-with-VPN.PNG)

A bemutatott egyszerűsített konfiguráció számos biztonsági példányok, amelyek lehetővé teszik a vezérlő és a korlát útválasztás mutatja be. Kezdődik 

- Az útválasztó/tűzfal helyszíni ügyfél oldalán.
- A következő alkalom a [Azure-beli hálózati biztonsági csoport](https://docs.microsoft.com/azure/virtual-network/security-overview) , hogy, ezáltal az Útválasztás és a biztonsági szabályok az Azure virtuális hálózaton, amely futtatja az SAP Business egy konfigurációs.
- Annak érdekében, hogy egy üzleti felhasználók által is látható az üzleti egy kiszolgálóra, amely futtatja az adatbázist futtató kiszolgáló, el kell választani a virtuális Gépet üzemeltető, az üzleti egy ügyfél és az üzleti egy kiszolgálót két külön alhálózatokon, a virtuális hálózaton belül.
- Használja a külön alhálózathoz rendelt Azure NSG újra annak érdekében, hogy férjenek hozzá az üzleti egy kiszolgálót.

Egy Azure-beli hálózati konfiguráció egy kifinomultabb verzió az Azure-alapú [küllős architektúra ajánlott dokumentálni](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Küllős architektúra minta megváltozna első egyszerűsített konfiguráció egy ehhez hasonló:


![Az üzleti egy küllős konfiguráció](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Olyan esetekben, ahol a felhasználók az interneten keresztül az Azure-ra privát kapcsolat nélkül csatlakozik, az Azure-ban a hálózat kialakítása számára az Azure-referenciaarchitektúra leírt elveket kell hangolni [DMZ-t az Azure között és az Internet](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Üzleti egy adatbázis-kiszolgáló
Az adatbázis típusát, az SQL Server és az SAP HANA érhetők el. Függetlenül az adatbázis-kezelő, olvassa el a dokumentumot [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) beolvasni az azure virtuális gépek és a kapcsolódó hálózati és tárolási DBMS-környezetét egy általános ismertetése a témakörök.

Abban az esetben, ha már az adott és általános adatbázis-dokumentumokban kiemelését, meg kell győződnie, saját magának ismeri:

- [Azure-beli Windows virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) és [az Azure-ban Linux rendszerű virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [Virtuális gépekre vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Ezeket a dokumentumokat kell segítségével eldöntheti, hogy a tárolási típusok és magas rendelkezésre állású konfiguráció kiválasztása.

Alapvetően a következőket kell elvégeznie:

- Használat [az Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) keresztül [az Azure standard szintű Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- Az Azure Managed disks használata keresztül nem felügyelt lemezek
- Győződjön meg arról, hogy rendelkezik-e konfigurálva a lemezkonfigurációt elegendő IOPS és az i/o átviteli
- Annak érdekében, hogy a költség-tárolási konfigurációt/hana/adatok és /hana/log kötet egyesítése


#### <a name="sql-server-as-dbms"></a>SQL Server adatbázis-kezelő
Telepítése az SQL Server adatbázis-kezelő, egy üzleti, látogasson el a dokumentumot mentén [SAP NetWeaver az SQL Server Azure virtuális gépek DBMS üzembe](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

Az SQL Server adatbázis-kezelő oldalán hozzávetőleges méretezési becsléseket a következők:

| Felhasználók száma | Virtuális magok | Memory (Memória) | Példa virtuális gépek típusai |
| --- | --- | --- | --- |
| legfeljebb 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| akár 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| legfeljebb 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| legfeljebb 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

A fent felsorolt méretezési ötlet kell biztosítania, ahol a következővel kell kezdődnie. Elképzelhető, hogy nincs szüksége több vagy kevesebb erőforrásokat, ebben az esetben az azure-betanítás ördöngösség. A virtuális gépek típusai közötti változást minden lehetséges a csak a virtuális gép újraindítását.


#### <a name="sap-hana-as-dbms"></a>Az SAP Hana-hoz, mint az adatbázis-kezelő
SAP HANA használata, adatbázis-kezelő az alábbi szakaszok azt érdemes követnie a dokumentum a szempontok [SAP HANA az Azure-műveletek útmutatójában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

A magas rendelkezésre állású és vész-helyreállítási konfigurációkban az SAP HANA-adatbázisként körül for Business One, az Azure-ban, olvassa el a dokumentációt [SAP HANA magas rendelkezésre állás az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) és a dokumentáció Ebből a dokumentumból mutatott.

Az SAP Hana biztonsági mentését és visszaállítását stratégiák, érdemes elolvasni a dokumentumot [biztonsági mentési útmutató az SAP Hana az Azure Virtual machines szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) , és ebből a dokumentumból mutatott a dokumentációt.

 
### <a name="business-one-client-server"></a>Üzleti egy ügyfél-kiszolgáló
Ezeknek az összetevőknek a tárterülettel kapcsolatos szempontok nem tartoznak az elsődleges szempont. Mindazonáltal szeretné megbízható platformon. Ezért használjon az Azure Premium Storage virtuális géphez, az alap virtuális merevlemez esetén is. Méretezés a virtuális gép a megadott adatokat [SAP Business egy hardver Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Azure-ban hogy a dokumentum 2.4 fejezetben ismertetett követelményeknek kiszámítása, és szüksége. Számít a követelményeknek, mivel kell őket hasonlítani az Ön számára ideális megoldás a virtuális gép található a következő dokumentumokat:

- [Az Azure-beli Windows virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [SAP-Jegyzetnek #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Hasonlítsa össze a processzorokat és memóriát, mi a Microsoft dokumentált szükséges számát. Azt is hálózati átviteli sebesség vegye figyelembe a virtuális gépek kiválasztásakor.








