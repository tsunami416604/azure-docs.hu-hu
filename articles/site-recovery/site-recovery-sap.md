---
title: SAP NetWeaver vész-helyreállítás beállítása Azure Site Recovery
description: Ez a cikk azt ismerteti, hogyan állíthatja be a vész-helyreállítást az SAP NetWeaver alkalmazás-telepítésekhez Azure Site Recovery használatával.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 29b3e4af33702c75e92b5e36c5521d9af12b1013
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533848"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Vész-helyreállítás beállítása többrétegű SAP NetWeaver-alkalmazások telepítéséhez

A legtöbb nagy méretű és közepes méretű SAP-telepítés a vész-helyreállítási megoldás valamilyen formáját használja. A robusztus és tesztelhető vész-helyreállítási megoldások fontossága nagyobb, mint az alapvető üzleti folyamatok, például az SAP. A Azure Site Recovery SAP-alkalmazásokkal lett tesztelve és integrálva. Site Recovery meghaladja a legtöbb helyszíni vész-helyreállítási megoldás képességeit, valamint a versengő megoldásoknál alacsonyabb teljes tulajdonlási költséget (TCO).

A Site Recovery a következőket teheti:
* Az összetevők Azure-ba történő replikálásával **engedélyezheti az SAP NetWeaver és a nem NetWeaver éles környezetben futó alkalmazások védelmét** .
* **Engedélyezze az Azure-on futó SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét** az összetevők másik Azure-adatközpontba való replikálásával.
* **Egyszerűsítse a felhőbe való áttelepítést** a site Recovery használatával az SAP üzembe helyezéséhez az Azure-ba.
* **Egyszerűsítse az SAP-projektek frissítéseit, tesztelését és a prototípus** -készítést azáltal, hogy az SAP-alkalmazások teszteléséhez igény szerinti éles klónozást hoz létre.

Ez a cikk azt ismerteti, hogyan védhető az SAP NetWeaver-alkalmazások központi telepítése [Azure site Recovery](site-recovery-overview.md)használatával. A cikk bemutatja az Azure-beli háromrészes SAP NetWeaver-környezet védelmére vonatkozó ajánlott eljárásokat, ha Site Recovery használatával replikál egy másik Azure-adatközpontba. Ismerteti a támogatott forgatókönyveket és konfigurációkat, valamint a feladatátvételi teszt (vész-helyreállítási részletezés) és a tényleges feladatátvételek végrehajtását.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy tudja, hogyan végezheti el a következő feladatokat:

* [Virtuális gép replikálása az Azure-ba](azure-to-azure-walkthrough-enable-replication.md)
* [Helyreállítási hálózat kialakítása](site-recovery-azure-to-azure-networking-guidance.md)
* [Végezzen feladatátvételi tesztet az Azure-ba](azure-to-azure-walkthrough-test-failover.md)
* [Feladatátvétel az Azure-ba](site-recovery-failover.md)
* [Tartományvezérlő replikálása](site-recovery-active-directory.md)
* [SQL Server replikálása](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Támogatott esetek
A következő esetekben használhatja a Site Recoveryt a vész-helyreállítási megoldások megvalósításához:
* Egy Azure-adatközpontban futó SAP-rendszerek, amelyek egy másik Azure-adatközpontba replikálódnak (Azure – Azure vész-helyreállítás). További információ: [Azure – Azure replikációs architektúra](https://aka.ms/asr-a2a-architecture).
* A helyszíni VMware-(vagy fizikai) kiszolgálókon futó SAP-rendszerek, amelyek egy Azure-adatközpontban található vész-helyreállítási helyre replikálódnak (VMware – Azure vész-helyreállítás). Ehhez a forgatókönyvhöz további összetevőkre van szükség. További információ: [VMware – Azure replikációs architektúra](https://aka.ms/asr-v2a-architecture).
* A helyszíni Hyper-V-n futó SAP-rendszerek, amelyek egy Azure-adatközpontban található vész-helyreállítási helyre replikálódnak (Hyper-V – Azure vész-helyreállítás). Ehhez a forgatókönyvhöz további összetevőkre van szükség. További információ: [Hyper-V – Azure replikációs architektúra](https://aka.ms/asr-h2a-architecture).

Ebben a cikkben egy **Azure – Azure vész-** helyreállítási forgatókönyvet használunk a site Recovery SAP vész-helyreállítási képességeinek bemutatására. Mivel Site Recovery replikáció nem alkalmazásspecifikus, a leírt folyamat várhatóan más forgatókönyvekre is vonatkozik.

### <a name="required-foundation-services"></a>Szükséges alapszolgáltatások
A cikkben ismertetett forgatókönyvben a következő alapszolgáltatások vannak telepítve:
* Azure ExpressRoute vagy Azure VPN Gateway
* Legalább egy Active Directory tartományvezérlő és DNS-kiszolgáló, amely az Azure-ban fut

Javasoljuk, hogy a Site Recovery üzembe helyezése előtt hozza létre ezt az infrastruktúrát.

## <a name="reference-sap-application-deployment"></a>Az SAP-alkalmazások telepítésének referenciája

Ez a hivatkozás azt mutatja be, hogy az SAP NetWeaver az Azure-beli Windows-környezetben, magas rendelkezésre állással fut.  Ez az architektúra olyan virtuálisgép-méretekkel van telepítve, amelyek módosíthatók a szervezet igényeinek megfelelően.

![Tipikus SAP-telepítési minta diagramja](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Vész-helyreállítási megfontolások

A vész-helyreállítási (DR) esetében át kell tudnia adni a feladatátvételt egy másodlagos régióba. Minden egyes réteg más stratégiával biztosít vészhelyreállítási (DR) védelmet.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>SAP web diszpécser készletet futtató virtuális gépek 
A web diszpécser összetevő a SAP-alkalmazások kiszolgálói közötti SAP-forgalomhoz használt terheléselosztó. Ha magas rendelkezésre állást szeretne elérni a web diszpécser összetevőjénél, Azure Load Balancer a párhuzamos webes diszpécser telepítő megvalósítására szolgál a HTTP (S) forgalom eloszlásának ciklikus, a kiegyenlítő készletben elérhető webes kiosztói között. Ezt a rendszer a Azure Site Recovery (ASR) és az Automation-parancsfájlok használatával replikálja a terheléselosztó a vész-helyreállítási régióban való konfigurálásához. 

#### <a name="vms-running-application-servers-pool"></a>Application Server-készletet futtató virtuális gépek
A ABAP alkalmazás-kiszolgálók bejelentkezési csoportjainak kezeléséhez a rendszer a SMLG tranzakciót használja. A terheléselosztási függvényt használja a központi szolgáltatások SAPGUIs belül a számítási feladatok elosztásához a SAP Application Servers-készletek között a és az RFC-forgalom számára. Ez Azure Site Recovery használatával lesz replikálva 

#### <a name="vms-running-sap-central-services-cluster"></a>SAP Central Services-fürtöt futtató virtuális gépek
Ez a viszonyítási architektúra központi szolgáltatásokat futtat az alkalmazási szinten lévő virtuális gépeken. A központi szolgáltatások egy lehetséges meghibásodási pont (SPOF), ha egyetlen virtuális gépre van üzembe helyezve – tipikus telepítés esetén, ha a magas rendelkezésre állás nem követelmény.<br>

Magas rendelkezésre állású megoldás megvalósításához egy megosztott fürtlemez vagy egy fájlmegosztási fürt is használható. A virtuális gépek megosztott lemezes fürthöz való konfigurálásához használja a Windows Server feladatátvevő fürtöt. A Felhőbeli tanúsító kvórum tanúsító. 
 > [!NOTE]
 > Azure Site Recovery nem replikálja a Felhőbeli tanút, ezért javasolt a Felhőbeli tanú üzembe helyezése a vész-helyreállítási régióban.

A feladatátvevő fürt környezetének támogatásához a [SIOS DataKeeper-fürt kiadása](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) a fürt megosztott kötetének működését a fürtcsomópontok által birtokolt független lemezek replikálásával hajtja végre. Az Azure nem támogatja natív módon a megosztott lemezeket, ezért a SIOS által biztosított megoldásokat igényel. 

A fürtözés kezelésének másik módja egy fájlmegosztási fürt implementálása. Az [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) nemrég módosította a központi szolgáltatások telepítési mintáját, hogy a/sapmnt globális könyvtárakat egy UNC elérési úton keresztül elérje. Azonban továbbra is ajánlott biztosítani, hogy a/sapmnt UNC-megosztás erősen elérhető legyen. Ezt a központi szolgáltatási példányon végezheti el a Windows Server feladatátvevő fürt és a kibővíthető fájlkiszolgáló (SOFS) és a Windows Server 2016 Közvetlen tárolóhelyek (S2D) funkciójának használatával. 
 > [!NOTE]
 > Jelenleg Azure Site Recovery támogatja a virtuális gépek csak a közvetlen tárolóhelyek és a SIOS Datakeeper passzív csomópontjának összeomlását.


## <a name="disaster-recovery-considerations"></a>Vészhelyreállítási szempontok

A Azure Site Recovery segítségével összehangolhatja a teljes körű SAP-telepítés feladatátvételét az Azure-régiók között.
A vész-helyreállítás beállításának lépései a következők: 

1. Virtuális gépek replikálása 
2. Helyreállítási hálózat kialakítása
3.  Tartományvezérlő replikálása
4.  Adatbázis-réteg replikálása 
5.  Futtasson egy feladatátvételi tesztet 
6.  Feladatátvétel futtatása 

Alább látható az ebben a példában használt egyes szintek vész-helyreállítására vonatkozó javaslat. 

 **SAP-rétegek** | **Ajánlás**
 --- | ---
**SAP web diszpécser készlet** |  Replikálás a site Recovery használatával 
**SAP Application Server-készlet** |  Replikálás a site Recovery használatával 
**SAP központi szolgáltatások fürtje** |  Replikálás a site Recovery használatával 
**Active Directory-beli virtuális gépek** |  Active Directory-replikáció 
**SQL Database-kiszolgálók** |  SQL always on Replication

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

Az összes SAP-alkalmazás virtuális gép Azure vész-helyreállítási adatközpontba történő replikálásának megkezdéséhez kövesse a [virtuális gépek Azure](azure-to-azure-walkthrough-enable-replication.md)-ba történő replikálásának útmutatóját.


* A Active Directory és a DNS védelmével kapcsolatos útmutatásért tekintse meg a [Active Directory és a DNS-dokumentum védelme](site-recovery-active-directory.md) című témakört.

* Az SQL Serveren futó adatbázis-rétegek védelméről a SQL Server dokumentumok [védelme](site-recovery-sql.md) című témakörben talál útmutatást.

## <a name="networking-configuration"></a>Hálózati konfiguráció

Ha statikus IP-címet használ, megadhatja azt az IP-címet, amelyet el szeretne végezni a virtuális gépen. Az IP-cím beállításához lépjen a **számítási és hálózati beállítások** > **hálózati adapter**elemre.

![A magánhálózati IP-cím megadását bemutató képernyőkép a Site Recovery hálózati adapter panelen](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv a feladatátvétel során a többrétegű alkalmazások különböző szintjeinek sorrendjét támogatja. Az előkészítés segíti az alkalmazások konzisztenciájának fenntartását. Ha többrétegű webalkalmazáshoz hoz létre helyreállítási tervet, hajtsa végre a [helyreállítási terv létrehozása a site Recovery használatával](site-recovery-create-recovery-plans.md)című témakörben ismertetett lépéseket.

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása a feladatátvételi csoportokhoz

1.  Hozzon létre egy helyreállítási tervet az alkalmazáskiszolgáló, a web diszpécser és az SAP Central Services-alapú virtuális gépek hozzáadásával.
2.  A virtuális gépek csoportosításához kattintson a "Testreszabás" elemre. Alapértelmezés szerint az összes virtuális gép az "1. csoport" részét képezi.



### <a name="add-scripts-to-the-recovery-plan"></a>Parancsfájlok hozzáadása a helyreállítási tervhez
Ahhoz, hogy alkalmazásai megfelelően működjenek, előfordulhat, hogy az Azure-beli virtuális gépeken műveleteket kell végrehajtania a feladatátvételt követően vagy feladatátvételi teszt során. Automatizálhat néhány feladatátvétel utáni műveletet. Frissítheti például a DNS-bejegyzést, és módosíthatja a kötéseket és a kapcsolatokat úgy, hogy hozzáadja a megfelelő parancsfájlokat a helyreállítási tervhez.


A leggyakrabban használt Azure Site Recovery szkripteket az Automation-fiókjába telepítheti az alábbi "üzembe helyezés az Azure-ban" gombra kattintva. Ha bármilyen közzétett parancsfájlt használ, kövesse a parancsfájl útmutatását.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adjon hozzá egy előműveleti parancsfájlt az "1. csoporthoz" a feladatátvételi SQL rendelkezésre állási csoporthoz. Használja a minta parancsfájlokban közzétett "ASR-SQL-FailoverAG" szkriptet. Ügyeljen arra, hogy kövesse a parancsfájl útmutatását, és végezze el a szükséges módosításokat a parancsfájlban.
2. Adjon hozzá egy post Action-szkriptet a terheléselosztó a webes szinten (1. csoport) a feladatátvételen áthelyezett virtuális gépeken való csatolásához. Használja a minta parancsfájlokban közzétett "ASR-AddSingleLoadBalancer" szkriptet. Ügyeljen arra, hogy kövesse a parancsfájl útmutatását, és végezze el a szükséges módosításokat a parancsfájlban.

![SAP helyreállítási terv](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1.  A Azure Portal válassza ki a Recovery Services-tárolót.
2.  Válassza ki az SAP-alkalmazásokhoz létrehozott helyreállítási tervet.
3.  Kattintson a **Feladatátvétel tesztelése** elemre.
4.  A feladatátvételi teszt folyamatának elindításához válassza ki a helyreállítási pontot és az Azure-beli virtuális hálózatot.
5.  Ha a másodlagos környezet működik, végezze el az érvényesítést.
6.  Az érvényesítések befejezése után a feladatátvételi környezet tisztításához válassza a **feladatátvételi teszt**lehetőséget.

További információ: a [feladatátvétel tesztelése az Azure-ban site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1.  A Azure Portal válassza ki a Recovery Services-tárolót.
2.  Válassza ki az SAP-alkalmazásokhoz létrehozott helyreállítási tervet.
3.  Válassza a **Feladatátvétel** lehetőséget.
4.  A feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot.

További információ: [feladatátvétel site Recoveryban](site-recovery-failover.md).

## <a name="next-steps"></a>Következő lépések
* Ha többet szeretne megtudni az SAP NetWeaver-környezetek vész-helyreállítási megoldásának létrehozásáról Site Recovery használatával, tekintse meg a letölthető tanulmányi [SAP NetWeaver: vész-helyreállítási megoldás létrehozása a Azure site Recoveryával](https://aka.ms/asr_sap)című témakört. A tanulmány a különböző SAP-architektúrákkal kapcsolatos ajánlásokat ismerteti, felsorolja az Azure-beli SAP-beli támogatott alkalmazásokat és virtuálisgép-típusokat, valamint ismerteti a vész-helyreállítási megoldás tesztelési tervének beállításait.
* További információ [más számítási feladatok replikálásáról](site-recovery-workload.md) site Recovery használatával.
