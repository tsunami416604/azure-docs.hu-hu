---
title: Alkalmazásait az SQL Server és az Azure Site Recovery replikálja |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan replikálhat az Azure Site Recovery használatával az SQL Server vészhelyreállítási funkciók az SQL Server.
services: site-recovery
documentationcenter: ''
author: prateek9us
manager: gauravd
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: pratshar
ms.openlocfilehash: c877f4bbc0ed14e859ff39f1d719a9cd0b787118
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920831"
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Az SQL Server-vészhelyreállítás és az Azure Site Recovery használatával az SQL Server védelme

Ez a cikk azt ismerteti, hogyan védheti meg az SQL Server háttéralkalmazását az SQL Server üzletmenet-folytonossági és vészhelyreállítási (BCDR) helyreállítási technológiák kombinációját használó alkalmazások és [Azure Site Recovery](site-recovery-overview.md).

Mielőtt elkezdené, győződjön meg arról, az SQL Server vész-helyreállítási lehetőségei, beleértve a Feladatátvételi fürtszolgáltatás, Always On rendelkezésre állási csoportokkal, az adatbázis-tükrözési és naplóküldés ismernie.


## <a name="sql-server-deployments"></a>SQL Server-telepítéseket

Számos számítási feladatokhoz használja az SQL Server alaprendszert, és integrálható alkalmazásokat, például a SharePoint, a Dynamics és a SAP, az adatszolgáltatások megvalósításához.  Az SQL Server számos módon telepíthető:

* **Önálló SQL Server**: (fizikai vagy virtuális) egyetlen gépen üzemeltetett SQL Server és az összes adatbázishoz. Virtualizált, amikor a fürtszolgáltatás gazdagép helyi magas rendelkezésre állású szolgál. Vendégszintű magas rendelkezésre állású nincs megvalósítva.
* **SQL Server feladatátvételi fürtszolgáltatási példányok (mindig az FCI)**: legalább két csomóponttal instanced megosztott lemezzel rendelkező SQL Server szoftvert futtató Windows feladatátvevő fürtben vannak konfigurálva. Ha egy csomópont nem működik, a fürt átveheti az SQL Server egy másik példányhoz. A telepítő egy elsődleges helyen magas rendelkezésre állás megvalósításához általában szolgál. A központi telepítési hiba, illetve a megosztott tárolási réteg leállás nem ellen. Az iSCSI, a fiber channel vezérlőt használó vagy a megosztott vhdx-fájlt egy megosztott lemezt kell végrehajtani.
* **SQL Always On rendelkezésre állási csoportok**: a megosztott semmi fürt, egy rendelkezésre állási csoportban, a szinkron replikáció és automatikus feladatátvételi konfigurált SQL Server-adatbázisok legalább két csomóponttal állíthatók be.

 Ez a cikk használja az alábbi natív SQL katasztrófa utáni helyreállítás technológiákat adatbázisok egy távoli helyre történő helyreállítását:

* SQL Always On rendelkezésre állási csoportok, a vész-helyreállítási adja meg az SQL Server 2012 vagy 2014 Enterprise kiadások.
* Az SQL adatbázis-tükrözés a magas biztonsági üzemmódú, SQL Server 2008 R2 vagy SQL Server Standard kiadásához (bármilyen verzió).

## <a name="site-recovery-support"></a>Site Recovery támogatási

### <a name="supported-scenarios"></a>Támogatott esetek
Site Recovery szolgáltatás védi az SQL Server, a táblázat foglalja össze.

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Igen | Igen
**VMware** | Igen | Igen
**Fizikai kiszolgáló** | Igen | Igen
**Azure**|NA| Igen

### <a name="supported-sql-server-versions"></a>Támogatott SQL Server-verziók
Ezek az SQL Server-verziók támogatottak, a támogatott forgatókönyveket:

* SQL Server 2016 Enterprise és Standard
* SQL Server 2014 Enterprise és Standard
* SQL Server 2012 Enterprise és Standard
* Az SQL Server 2008 R2 Enterprise és Standard

### <a name="supported-sql-server-integration"></a>Támogatott SQL Server-integráció

A Site Recovery egy vész-helyreállítási megoldást biztosít a táblázatban összefoglalt natív SQL Server BCDR-technológiákkal integrálható.

**Funkció** | **Részletek** | **SQL Server** |
--- | --- | ---
**Always On rendelkezésre állási csoport** | Több különálló példány az SQL Server futtatása több csomóponttal rendelkező feladatátvevő fürtben.<br/><br/>Adatbázisok lehet csoportosítani feladatátvételi csoportokba másolható (tükrözött) az SQL Server-példányokat, hogy a nem megosztott tárolóra van szükség.<br/><br/>Itt a vész-helyreállítási egy elsődleges hely és a egy vagy több másodlagos hely között. Két csomópont állítható a megosztott semmi nem SQL Server-adatbázisok a fürt egy rendelkezésre állási csoportban, a szinkron replikáció és automatikus feladatátvételi konfigurálva. | Az SQL Server 2016, az SQL Server 2014 és SQL Server 2012 Enterprise edition
**A Feladatátvételi fürtszolgáltatás (mindig az FCI)** | Az SQL Server kihasználja a Windows feladatátvételi fürtszolgáltatás magas rendelkezésre állás, a helyszíni SQL Server számítási feladatok számára.<br/><br/>Az SQL Server-példányok futó megosztott lemezzel rendelkező csomópontok feladatátvevő fürtben vannak konfigurálva. Ha egy példány nem működik a fürt átadja a feladatokat másikat.<br/><br/>A fürt nem hiba vagy a megosztott tárolóban leállások ellen. A megosztott lemez implementálható az iSCSI, a fiber channel vezérlőt használó, vagy a megosztott vhdx-fájlokat. | Az SQL Server Enterprise kiadás<br/><br/>Az SQL Server Standard kiadása esetén (legfeljebb csak két csomópont)
**Az adatbázis-tükrözés (magas biztonsági üzemmódú)** | Egy másodlagos példányt egyetlen adatbázist védi. Mindkét magas biztonsági (szinkron) elérhető és nagy teljesítményű (aszinkron) replikációs mód. Nincs szükség a feladatátvevő fürt. | SQL Server 2008 R2<br/><br/>Az SQL Server Enterprise minden kiadás
**Különálló SQL Server** | Az SQL Server és adatbázis egyetlen kiszolgálón (fizikai vagy virtuális) üzemelnek. Ha a kiszolgáló virtuális gazdagépen a fürtszolgáltatás magas rendelkezésre állású használható. Vendégszintű magas rendelkezésre állás. | Enterprise vagy Standard edition

## <a name="deployment-recommendations"></a>Telepítési javaslatok

Ez a táblázat összefoglalja a javaslatok az SQL Server BCDR-technológiákkal való integrálásához a Site recoveryvel.

| **Verzió** | **Kiadás** | **Üzembe helyezés** | **A helyi helyszíni** | **Az Azure-bA helyszíni** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 vagy 2012 |Enterprise |Feladatátvevőfürt-példány |Always On rendelkezésre állási csoportok |Always On rendelkezésre állási csoportok |
|| Enterprise |Always On rendelkezésre állási csoportokat magas rendelkezésre állás érdekében |Always On rendelkezésre állási csoportok |Always On rendelkezésre állási csoportok | |
|| Standard |Feladatátvevőfürt-példány (FCI) |Site Recovery-replikációja helyi tükrözött |Site Recovery-replikációja helyi tükrözött | |
|| Enterprise vagy Standard |Különálló |Site Recovery-replikációja |Site Recovery-replikációja | |
| Az SQL Server 2008 R2 vagy 2008 |Enterprise vagy Standard |Feladatátvevőfürt-példány (FCI) |Site Recovery-replikációja helyi tükrözött |Site Recovery-replikációja helyi tükrözött |
|| Enterprise vagy Standard |Különálló |Site Recovery-replikációja |Site Recovery-replikációja | |
| SQL Server (bármilyen verzió) |Enterprise vagy Standard |Feladatátvevőfürt-példány - DTC-alkalmazás |Site Recovery-replikációja |Nem támogatott |

## <a name="deployment-prerequisites"></a>Üzembehelyezési előfeltételek

* Egy helyszíni SQL Server-telepítéséhez, támogatott SQL Server verziót futtat. Általában is szüksége lesz az Active Directory az SQL Serverhez.
* A követelmények, a forgatókönyv számára telepíteni kívánja. További információ a támogatási követelmények [az Azure-bA](site-recovery-support-matrix-to-azure.md) és [helyszíni](site-recovery-support-matrix.md), és [üzembe helyezési Előfeltételek](site-recovery-prereq.md).
* Állítsa be az Azure-ban, futtassa a [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) eszköz az SQL Server virtuális gépeken, ellenőrizze, hogy azok az Azure és a Site Recovery kompatibilis.

## <a name="set-up-active-directory"></a>Active Directory beállítása

Állítsa be az Active Directory, a másodlagos helyreállítási hely az SQL Server megfelelően fusson.

* **Kis vállalati**– az alkalmazások és a helyszíni helyhez tartozó egyetlen tartományvezérlőt kis számú, ha átadja a feladatokat a teljes helyre szeretné azt javasoljuk, használja a Site Recovery replikációs replikálása a másodlagos tartományvezérlő Datacenter, vagy az Azure-bA.
* **Közepes és nagyvállalati**– Ha nagy számú alkalmazások, az Active Directory-erdőben van, és szeretne végezni az irányítást az alkalmazást vagy munkaterhelést futtatják, javasoljuk, hogy beállította egy további tartományvezérlőt a másodlagos adatközponthoz, vagy az Azure-ban. Ha AlwaysOn rendelkezésre állási csoportok egy távoli helyen való helyreállításához használja, javasoljuk a helyreállított SQL Server-példány használata a másodlagos helyen vagy az Azure-ban, egy másik további tartományvezérlő beállítása.

A jelen cikkben lévő utasítások feltételezik, hogy a tartományvezérlő a másodlagos helyen érhető el. [További információ](site-recovery-active-directory.md) Active Directory a Site Recovery védelméről.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Az SQL Server Always On integrálása az Azure-bA

Itt látható, mit kell tennie:

1. Parancsfájlok importálja az Azure Automation-fiók. Ez tartalmazza a feladatátvétel SQL rendelkezésre állási csoport szkripteket a egy [Resource Manager virtuális gépének](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) és a egy [klasszikus virtuális gép](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Adja hozzá az ASR-SQL-FailoverAG előzetes műveletként az első csoport a helyreállítási terv.

1. Kövesse az utasításokat a parancsprogram elérhető a nevét, a rendelkezésre állási csoportok egy automatizálási változó létrehozása.

### <a name="steps-to-do-a-test-failover"></a>Végezzen feladatátvételi tesztet lépései

SQL Always On nem natív módon támogatja a feladatátvételi tesztet. Ezért ajánlott a következők:

1. Állítsa be a [Azure Backup](../backup/backup-azure-arm-vms.md) replikát a rendelkezésre állási csoportot az Azure-beli virtuális gépen.

1. Mielőtt elindítaná a helyreállítási terv teszt feladatátvétele, a virtuális gép helyreállítása az előző lépésben biztonsági.

    ![Az Azure biztonsági másolat visszaállítása ](./media/site-recovery-sql/restore-from-backup.png)

1. [A kvórum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) a virtuális gép biztonsági mentésből.

1. Frissítési IP-címét a figyelő egy IP-cím elérhető a feladatátvételi teszt hálózatában.

    ![Frissítse a figyelő IP](./media/site-recovery-sql/update-listener-ip.png)

1. Figyelő online állapotba.

    ![Figyelő Online állapotba helyezés](./media/site-recovery-sql/bring-listener-online.png)

1. Load balancer létrehozása egy előtérbeli IP-címkészlet minden rendelkezésre állási csoport figyelőjének megfelelő alatt létrehozott IP és a háttérkészlet hozzáadása SQL virtuális géppel.

     ![Load Balancer - előtérbeli IP-készlet létrehozása ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Load Balancer létrehozása – háttérkészlet ](./media/site-recovery-sql/create-load-balancer2.png)

1. Ezt a helyreállítási terv feladatátvételi tesztet.

### <a name="steps-to-do-a-failover"></a>Ehhez a feladatátvétel lépések

Miután hozzáadta a parancsfájl a helyreállítási tervben szereplő és érvényesíteni a helyreállítási terv feladatátvételi teszt elvégzésével, a helyreállítási terv feladatátvételi teheti meg.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integrálható SQL Server Always On a egy másodlagos helyszíni helyre történő replikálásához

Ha az SQL Server rendelkezésre állási csoportokat magas rendelkezésre állás (vagy egy FCI-t) használ, a helyreállítási helyen, valamint a rendelkezésre állási csoportok használatát javasoljuk. Vegye figyelembe, hogy ez az elosztott tranzakciók nem használó alkalmazásokra vonatkozik.

1. [-Adatbázisok konfigurálása](https://msdn.microsoft.com/library/hh213078.aspx) rendelkezésre állási csoportokba.
1. Virtuális hálózat létrehozása a másodlagos helyen.
1. Állítsa be egy helyek közötti VPN-kapcsolatot a virtuális hálózat és az elsődleges hely között.
1. Hozzon létre egy virtuális gépet a helyreállítási helyre, és az SQL Server telepítését.
1. A meglévő Always On rendelkezésre állási csoportok kiterjesztése az új SQL Server virtuális gépre. Egy aszinkron replika másolás az SQL Server-példány konfigurálása.
1. Hozzon létre egy rendelkezésre állási csoport figyelőjét, vagy frissítse a létező figyelőt, hogy tartalmazza a aszinkron replika virtuális gép.
1. Győződjön meg arról, hogy az alkalmazás farm használatára van beállítva a figyelő. Ha a telepítő használatával az adatbázis-kiszolgáló nevét, frissítse úgy, hogy a figyelő használja, így nem szükséges, konfigurálja újra a feladatátvételt követően.

Az elosztott tranzakciókat használó alkalmazásokhoz, javasoljuk a Site Recovery üzembe helyezése [VMware/fizikai kiszolgáló helyek közötti replikáció](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Helyreállítási terv kapcsolatos szempontok
1. Ez a példaszkript hozzáadása a VMM-erőforrástárban, az elsődleges és másodlagos helyen.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. A helyreállítási terv az alkalmazás létrehozásakor egy előzetes művelet hozzáadása a rendelkezésre állási csoportok feladatátvételét parancsfájlt meghívó parancsfájlalapú 1-csoport lépésben.

## <a name="protect-a-standalone-sql-server"></a>Egy önálló SQL Server védelme

Ebben a forgatókönyvben azt javasoljuk, hogy a Site Recovery replikációs védeni az SQL Servert futtató gép használja. A pontos lépések e SQL Server egy virtuális gép vagy fizikai kiszolgálóra, és hogy az Azure-bA replikálni kívánt vagy egy másodlagos helyszíni hely függ. Ismerje meg [Site Recovery forgatókönyvek](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>(Standard edition vagy Windows Server 2008 R2) SQL Server-fürt védelme

SQL Server Standard edition vagy SQL Server 2008 R2 rendszert futtató fürtre javasoljuk a Site Recovery replikációs használatával az SQL Server védelme.

### <a name="on-premises-to-on-premises"></a>Két helyszíni hely közötti replikálás

* Ha az alkalmazás használja az elosztott tranzakciók javasoljuk, hogy telepít [SAN-replikáció a Site Recovery](site-recovery-vmm-san.md) Hyper-V környezetben, vagy [VMware/fizikai kiszolgáló VMware](site-recovery-vmware-to-vmware.md) VMware környezetben.
* A DTC által alkalmazások a fenti módszer használatával a fürt helyreállítására önálló kiszolgálóként, kihasználva a helyi magas biztonsági adatbázis-tükrözés.

### <a name="on-premises-to-azure"></a>Az Azure-bA helyszíni

A Site Recovery nem biztosít a Vendég fürt támogatás, ha az Azure-ba történő. Az SQL Server is nem biztosít egy alacsony költségű vész-helyreállítási megoldást a Standard kiadása esetén. Ebben a forgatókönyvben javasoljuk, hogy a helyszíni SQL Server-fürt egy különálló SQL Server védelmét, és végezze el a helyreállítást a az Azure-ban.

1. További önálló SQL Server-példány konfigurálása a helyszíni helyre.
1. A védeni kívánt adatbázisokat a tükör egyikükön példányát konfigurálja. Állítsa be a magas biztonsági üzemmódú tükrözés.
1. A Site Recovery a helyszíni helyen konfigurálása ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) vagy [VMware virtuális gépek/fizikai kiszolgálók)](site-recovery-vmware-to-azure-classic.md).
1. A Site Recovery replikációs használatával Azure-bA replikálni az új SQL Server-példányon. Tükrözött magas biztonsági másolatot, mert szinkronizálja az elsődleges fürttel, de az Azure-bA a Site Recovery replikációs replikálja.


![Standard fürt](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Feladat-visszavétel kapcsolatos szempontok

Az SQL Server Standard fürtök esetén a feladat-visszavétel nem tervezett feladatátvétel után szükséges egy SQL server biztonsági másolat és helyreállítás az eredeti fürthöz, a tükör reestablishment a tükör példányból.

## <a name="next-steps"></a>További lépések
[További](site-recovery-components.md) kapcsolatos Site Recovery architektúrájáról.
