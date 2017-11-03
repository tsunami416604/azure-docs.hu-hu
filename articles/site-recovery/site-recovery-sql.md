---
title: "Az SQL Server és az Azure Site Recovery alkalmazások replikálni |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan replikáljon az SQL Server az SQL Server vészhelyreállítási képességek az Azure Site Recovery segítségével."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: e53f60979e01a0eabe118d3ae6457a61bd4b0ded
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>SQL Server vészhelyreállítási és az Azure Site Recovery használata az SQL Server védelme

Ez a cikk ismerteti, hogyan védi az SQL Server háttéralkalmazását kombinációjából SQL Server üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási technológiákat, az alkalmazások és [Azure Site Recovery](site-recovery-overview.md).

Mielőtt elkezdené, győződjön meg arról megismerte az SQL Server katasztrófa utáni helyreállítás lehetőségekről, beleértve a Feladatátvételi fürtszolgáltatás, Always On rendelkezésre állási csoportok, az adatbázis-tükrözés és naplóküldésben.


## <a name="sql-server-deployments"></a>SQL Server-telepítések

Számos különféle munkaterheléshez alapjaként SQL Servert használja, és alkalmazások, például a SharePoint, a Dynamics és a SAP, az adatszolgáltatások végrehajtásához integrálható.  SQL Server számos módon telepíthető:

* **Önálló SQL Server**: SQL Server és az összes adatbázis üzemeltetett (fizikai vagy virtuális) egy gépen. Amikor virtualizált, helyi magas rendelkezésre állású gazdagépen a fürtszolgáltatás szolgál. Vendég szint magas rendelkezésre állású nincs implementálva.
* **SQL Server feladatátvételi fürtszolgáltatási példányok (mindig az FCI)**: instanced megosztott lemezzel rendelkező SQL Server rendszert futtató két vagy több csomópont egy Windows feladatátvevő fürtben vannak konfigurálva. Ha egy csomópont nem működik, a fürt átveheti az SQL Server egy másik példánya. A telepítő egy elsődleges helyen magas rendelkezésre állású végrehajtásához általában használatos. A központi telepítés a nem hibája vagy a megosztott tároló rétegben kimaradás elleni védelem érdekében. Egy megosztott lemez iSCSI, a fiber channel vagy a megosztott vhdx használatával valósítható meg.
* **SQL Always On rendelkezésre állási csoportok**: egy megosztott semmi-fürtöt, SQL Server-adatbázisok rendelkezésre állási csoportban, a szinkron replikáció és az automatikus feladatátvételre konfigurált a két vagy több csomópont állíthatók be.

 Ez a cikk a következő natív SQL vész helyreállítási technológiák egy távoli helyre adatbázisok helyreállításához használja:

* SQL Always On rendelkezésre állási csoportok, vész-helyreállítási biztosít az SQL Server 2012 vagy 2014 Enterprise kiadások.
* SQL adatbázis-tükrözés a magas biztonsági üzemmódot, az SQL Server Standard edition (bármilyen verzió), vagy az SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Webhely-helyreállítási támogatás

### <a name="supported-scenarios"></a>Támogatott esetek
A Site Recovery megvédheti az SQL Server, a táblázatban foglaltak szerint.

**A forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-bA**
--- | --- | ---
**Hyper-V** | Igen | Igen
**VMware** | Igen | Igen
**Fizikai kiszolgáló** | Igen | Igen

### <a name="supported-sql-server-versions"></a>Támogatott SQL Server-verziók
Ezeket az SQL Server-verziók támogatottak, a támogatott forgatókönyvekről:

* SQL Server 2016 Enterprise és Standard
* SQL Server 2014 Enterprise és Standard
* SQL Server 2012 Enterprise és Standard
* SQL Server 2008 R2 Enterprise és Standard

### <a name="supported-sql-server-integration"></a>Támogatott SQL Server-integráció

A Site Recovery integrálható natív SQL Server BCDR-technológiákkal, vész helyreállítási megoldást nyújt a táblázat foglalja össze.

**Funkció** | **Részletek** | **SQL Server** |
--- | --- | ---
**Always On rendelkezésre állási csoport** | Több különálló példány az SQL Server futtatása több csomóponttal rendelkező feladatátvevő fürtben.<br/><br/>Adatbázisok másolható (tükrözött) az SQL Server-példányokat, hogy nincs megosztott tárhely szükséges feladatátvevő csoportokba csoportosíthatók.<br/><br/>Itt katasztrófa utáni helyreállítás egy elsődleges hely és egy vagy több másodlagos hely között. Két csomópontot is kell beállítani egy megosztott semmi szinkron replikáció és automatikus feladatátvételt egy rendelkezésre állási csoportban konfigurált SQL Server-adatbázisok fürt. | Az SQL Server 2014 & 2012 Enterprise edition
**Feladatátvételi fürtszolgáltatás (mindig az FCI)** | SQL Server kihasználja a Windows feladatátvételi fürtszolgáltatás magas rendelkezésre állás, a helyszíni SQL Server-munkaterhelések.<br/><br/>SQL Server-példány fut a megosztott lemezek csomópontja feladatátvevő fürtben van konfigurálva. Ha a példány nem működik a fürt feladatátadás másikat.<br/><br/>A fürt nem elleni hiba vagy üzemszünet korlátozza a megosztott tárolóeszköz. A megosztott lemez végrehajtható, szálcsatorna, iSCSI vagy a megosztott vhdx-fájlokat. | SQL Server Enterprise kiadása<br/><br/>SQL Server Standard edition (legfeljebb csak két csomópont)
**Az adatbázis-tükrözés (magas biztonsági mód)** | Egy másodlagos példányt egyetlen adatbázis védi. Elérhető mindkét magas biztonsági (szinkron) és nagy teljesítményű (aszinkron) replikációs mód. A feladatátvevő fürtök nem igényel. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise minden kiadás
**Önálló SQL-kiszolgáló** | Az SQL Server és az adatbázis egy kiszolgálón (fizikai vagy virtuális) üzemelteti. Ha a kiszolgáló virtuális állomás fürtszolgáltatás magas rendelkezésre állású használható. Nincs vendégszintű magas rendelkezésre állású. | Enterprise vagy Standard edition

## <a name="deployment-recommendations"></a>Telepítési javaslatok

A táblázat összefoglalja, az SQL Server BCDR-technológiákkal integrálása a Site Recovery javaslatok.

| **Verzió** | **Kiadás** | **Üzembe helyezés** | **A helyszíni helyszíni** | **Az Azure-bA helyszíni** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 vagy 2012 |Enterprise |Feladatátvevőfürt-példány |Always On rendelkezésre állási csoportok |Always On rendelkezésre állási csoportok |
|| Enterprise |Always On rendelkezésre állási csoportokat magas rendelkezésre álláshoz |Always On rendelkezésre állási csoportok |Always On rendelkezésre állási csoportok | |
|| Standard |Feladatátvevőfürt-példány (FCI) |A helyi tükör helyreállítási helyreplikálásának |A helyi tükör helyreállítási helyreplikálásának | |
|| Enterprise vagy Standard |Különálló |Helyreállítási helyreplikálásának |Helyreállítási helyreplikálásának | |
| SQL Server 2008 R2 vagy 2008 |Enterprise vagy Standard |Feladatátvevőfürt-példány (FCI) |A helyi tükör helyreállítási helyreplikálásának |A helyi tükör helyreállítási helyreplikálásának |
|| Enterprise vagy Standard |Különálló |Helyreállítási helyreplikálásának |Helyreállítási helyreplikálásának | |
| SQL Server (bármilyen verzió) |Enterprise vagy Standard |Feladatátvevőfürt-példány - DTC-alkalmazás |Helyreállítási helyreplikálásának |Nem támogatott |

## <a name="deployment-prerequisites"></a>Üzembehelyezési előfeltételek

* Egy helyi SQL Server-telepítéséhez, egy SQL Server támogatott verzióját futtatja. Általában szükség az Active Directory az SQL-kiszolgáló.
* A forgatókönyv követelményei számára telepíteni kívánja. További információ a támogatási követelmények [replikálást az Azure-](site-recovery-support-matrix-to-azure.md) és [helyszíni](site-recovery-support-matrix.md), és [telepítésének előfeltételei](site-recovery-prereq.md).
* Az Azure-ban helyreállítási beállításához futtassa a [Azure virtuális gép Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) eszköz az SQL Server virtuális gépeken, győződjön meg arról, és az Azure Site Recovery kompatibilis fontosságúak.

## <a name="set-up-active-directory"></a>Az Active Directory beállítása

Active Directory beállítása a másodlagos helyreállítási hely, az SQL Server megfelelő futtatásához.

* **Kis vállalati**– néhány alkalmazások és a helyszíni hely egyetlen tartományvezérlővel rendelkezik, ha azt szeretné, hogy áthelyezze a feladatokat a teljes webhelyet, javasolt a tartományvezérlő replikálja a másodlagos adatközpontba, vagy az Azure Site Recovery-replikációt használ.
* **Közepes vagy nagyméretű vállalat**– Ha nagyszámú alkalmazásokat, egy Active Directory-erdőben van, és szeretné adni az alkalmazás vagy munkaterhelés át, ajánlott állít be egy további tartományvezérlőt a másodlagos adatközpontba, vagy az Azure-ban. Ha az Always On rendelkezésre állási csoportokat használ az egy távoli helyre történő helyreállítást, ajánlott állít be egy másik tartományvezérlő a másodlagos helyen vagy az Azure, a helyreállított SQL Server-példány használata.

A jelen cikkben lévő utasítások feltételezik, hogy a tartományvezérlő érhető el a másodlagos helyen. [További](site-recovery-active-directory.md) az Active Directory, a Site Recovery védelméről.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integrálható az SQL Server Always On Azure replikációs

Ez mit kell tennie:

1. Parancsfájlok importálnia kell az Azure Automation-fiók. A parancsfájlok SQL rendelkezésre állási csoport feladatátvételre tartalmazza a egy [Resource Manager virtuális gép](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) és egy [klasszikus virtuális gép](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Az ASR-SQL-FailoverAG hozzáadása az első csoport a helyreállítási terv előtti műveletként.

1. Kövesse az utasításokat a parancsfájlban elérhető hozzon létre egy automatizálási változó adja meg a rendelkezésre állási csoportok nevét.

### <a name="steps-to-do-a-test-failover"></a>Feladatátvételi teszt lépéseit

SQL Always On nem natív módon támogatja a feladatátvételi tesztet. Ezért ajánlott a következők:

1. Állítson be [Azure biztonsági mentés](../backup/backup-azure-vms.md) replikát a rendelkezésre állási csoport az Azure virtuális gépen.

1. Ahhoz, hogy kiváltsa a helyreállítási terv teszt feladatátvétele, a virtuális gép helyreállítása az előző lépésben készült biztonsági másolatból.

    ![Állítsa vissza az Azure biztonsági másolatból ](./media/site-recovery-sql/restore-from-backup.png)

1. [A kvórum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) a virtuális gép biztonsági másolatból állítottak vissza.

1. IP-címe a figyelő frissítse olyan IP-címet a feladatátvételi teszt hálózatában érhető el.

    ![Frissítse a figyelő IP](./media/site-recovery-sql/update-listener-ip.png)

1. Figyelő online állapotba.

    ![Figyelő kapcsolása](./media/site-recovery-sql/bring-listener-online.png)

1. Hozzon létre egy terhelés-kiegyenlítő, egy előtérbeli IP-készlet minden egyes rendelkezésre állási csoport figyelőjének megfelelő létrehozott IP és háttérkészlethez hozzáadott SQL virtuális géphez.

     ![Terheléselosztó - előtér-IP-címkészlet létrehozása ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Terheléselosztó - háttérkészlet létrehozása ](./media/site-recovery-sql/create-load-balancer2.png)

1. Végezzen a helyreállítási terv feladatátvételi tesztet.

### <a name="steps-to-do-a-failover"></a>A feladatátvétel lépéseit

Miután hozzáadta a parancsfájl a helyreállítási tervben és a helyreállítási terv feladatátvételi teszt elvégzésével érvényesítve, a helyreállítási terv feladatátvételi teheti meg.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>A replikáció egy másodlagos helyszíni helyre az SQL Server Always On integrálása

Ha az SQL Server rendelkezésre állási csoportokat használ a magas rendelkezésre állású (vagy egy FCI), azt javasoljuk, rendelkezésre állási csoportokat, valamint a helyreállítási helyen. Vegye figyelembe, hogy ez vonatkozik az elosztott tranzakciók nem használó alkalmazások.

1. [Adatbázisok konfigurálása](https://msdn.microsoft.com/library/hh213078.aspx) rendelkezésre állási csoportokba.
1. Virtuális hálózat létrehozása a másodlagos helyen.
1. A pont-pont VPN-kapcsolat állítható be a virtuális hálózat és az elsődleges hely között.
1. Hozzon létre egy virtuális gépet a helyreállítási helyen, és az SQL Server telepítését.
1. Terjessze ki a meglévő Always On rendelkezésre állási csoportokat az új SQL Server virtuális Gépen. Az SQL Server-példány beállítása egy aszinkron replika másolatot.
1. Hozzon létre egy rendelkezésre állási csoport figyelőjével, vagy frissítse a létező figyelőt, hogy tartalmazzák az aszinkron replika virtuális gép.
1. Győződjön meg arról, hogy az alkalmazás farm használatára van beállítva a figyelő. Ha telepítés használatával az adatbázis-kiszolgáló nevét, frissítse úgy, hogy a figyelő használja, így nem kell azt úgy átkonfigurálni, a feladatátvétel után.

Az elosztott tranzakciókat használó alkalmazásokat, azt javasoljuk, a Site Recovery telepítése [VMware vagy fizikai kiszolgáló helyek közötti replikálás](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Helyreállítási terv kapcsolatos szempontok
1. Ez a parancsfájlpélda hozzáadása a VMM-könyvtárban, az elsődleges és másodlagos helyeken.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Amikor létrehoz egy helyreállítási tervet, az alkalmazás, egy előtti művelet hozzáadása parancsprogrammal létrehozva, csoport-1. lépés, amely hívja meg a rendelkezésre állási csoportok feladatátvételt parancsfájl.

## <a name="protect-a-standalone-sql-server"></a>Különálló SQL Server védelme

Ebben a forgatókönyvben azt javasoljuk, hogy a Site Recovery-replikáció használatával védi az SQL Server-számítógépen. A pontos lépések az függvényében, hogy az SQL Server egy virtuális gép vagy fizikai kiszolgálók, és hogy az Azure-bA replikálni kívánt vagy egy másodlagos helyszíni hely. További tudnivalók [Site Recovery forgatókönyvek](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>(Standard edition és Windows Server 2008 R2) SQL Server-fürt védelme

SQL Server Standard edition vagy SQL Server 2008 R2 rendszerű fürtökön javasoljuk, használjon a Site Recovery replikációs SQL-kiszolgáló védelme érdekében.

### <a name="on-premises-to-on-premises"></a>Két helyszíni hely közötti replikálás

* Ha az alkalmazás használja az elosztott tranzakciók ajánlott telepít [SAN-replikálással a Site Recovery](site-recovery-vmm-san.md) Hyper-V-környezethez vagy [VMware VMware vagy fizikai kiszolgálót](site-recovery-vmware-to-vmware.md) VMware környezetben.
* Nem DTC-alkalmazások esetében használja a fenti megközelítést helyi magas biztonsági adatbázis tükrözött, ami egy önálló kiszolgáló, a fürt helyreállításához.

### <a name="on-premises-to-azure"></a>Az Azure-bA helyszíni

A Site Recovery nem biztosít a Vendég Fürtözési támogatás az Azure-bA replikálása esetén. SQL Server is nem biztosít egy alacsony költségű vész-helyreállítási megoldást a Standard kiadásban. Ebben a forgatókönyvben javasoljuk, hogy a helyszíni SQL Server önálló SQL Server-fürt védelmét, és végezze el a helyreállítást a Azure-ban.

1. További önálló SQL Server-példány konfigurálása a helyszíni hely.
1. Konfigurálja a tükör a védeni kívánt adatbázisok kiszolgálásához. Konfigurálja a tükrözés magas biztonsági módban.
1. A Site Recovery a helyszíni hely konfigurálása ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) vagy [VMware virtuális gépek/fizikai kiszolgálók)](site-recovery-vmware-to-azure-classic.md).
1. Site Recovery-replikáció használatával replikálja az új SQL Server-példány az Azure-bA. Mivel tükör magas biztonsági másolatot, az elsődleges fürt szinkronizálja, de az Azure Site Recovery-replikáció használatával replikálja.


![Standard fürt](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Feladat-visszavétel kapcsolatos szempontok

Az SQL Server Standard-fürtök esetén a feladat-visszavétel nem tervezett feladatátvétel után szükséges egy SQL server biztonsági mentés és visszaállítás az eredeti fürtre, a tükör reestablishment a tükör példányból.

## <a name="next-steps"></a>Következő lépések
[További](site-recovery-components.md) Site Recovery architektúrájáról kapcsolatban.
