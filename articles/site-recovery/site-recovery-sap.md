---
title: Az SAP NetWeaver vészhelyreállítási szolgáltatásának beállítása az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan állíthatja be az SAP NetWeaver vész-helyreállítási az Azure Site Recovery segítségével.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190796"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Vészhelyreállítás beállítása többrétegű SAP NetWeaver alkalmazás központi telepítéséhez

A legtöbb nagy méretű és közepes méretű SAP-telepítések valamilyen formában a vész-helyreállítási megoldás. A robusztus és tesztelhető vész-helyreállítási megoldások fontossága nőtt, ahogy egyre több alapvető üzleti folyamat kerül át az olyan alkalmazásokba, mint az SAP. Az Azure Site Recovery tesztelt és integrálva van az SAP-alkalmazásokkal. A Site Recovery meghaladja a legtöbb helyszíni vész-helyreállítási megoldás képességeit, és alacsonyabb teljes tulajdonlási költséggel rendelkezik, mint a konkurens megoldások.

A Site Recovery segítségével a következőkre van szüksége:
* Engedélyezze az SAP NetWeaver és a nem NetWeaver éles alkalmazások védelmét, amelyek a helyszínen futnak az összetevők Azure-ba replikálásával.
* Engedélyezze az Azure-on futó SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők egy másik Azure-adatközpontba történő replikálásával.
* A Site Recovery segítségével áttelepítheti az Azure-ba az SAP üzemelő példányt, így leegyszerűsítheti a felhőre való átállást.
* Egyszerűsítse az SAP-projektfrissítéseket, tesztelést és prototípusokat egy éles klón igény szerinti létrehozásával az SAP-alkalmazások teszteléséhez.

Az SAP NetWeaver alkalmazás-telepítések az Azure Site Recovery használatával [védhetik.](site-recovery-overview.md) Ez a cikk ismerteti a háromrétegű SAP NetWeaver-telepítés azure-beli védelmének ajánlott gyakorlati tanácsait, amikor egy másik Azure-adatközpontba replikál a Site Recovery használatával. A cikk ismerteti a támogatott forgatókönyvek és konfigurációk, és hogyan kell teszt feladatátvételek (vész-helyreállítási fúrók) és a tényleges feladatátvételek.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy tudja, hogyan kell elvégezni a következő feladatokat:

* [Virtuális gép replikálása az Azure-ba](azure-to-azure-walkthrough-enable-replication.md)
* [Helyreállítási hálózat tervezése](site-recovery-azure-to-azure-networking-guidance.md)
* [Tesztfeladat-átvétel az Azure-ba](azure-to-azure-walkthrough-test-failover.md)
* [Feladatátvétel az Azure-ba](site-recovery-failover.md)
* [Tartományvezérlő replikálása](site-recovery-active-directory.md)
* [SQL Server-példány replikálása](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Támogatott esetek

A Site Recovery segítségével a következő esetekben valósíthat meg vész-helyreállítási megoldást:
* Az SAP-rendszerek egy Azure-adatközpontban futnak, és replikálja őket egy másik Azure-adatközpontba (Azure-ból Azure-ba vész-helyreállítási). 
   További információ: [Azure-to-Azure replikációs architektúra](https://aka.ms/asr-a2a-architecture).
* Sap-rendszerekkel rendelkezik a VMware (vagy fizikai) kiszolgálókon a helyszínen. Az SAP-rendszereket egy Azure-adatközpontban (VMware-Azure-ba vész-helyreállítási) egy vész-helyreállítási helyre is replikálja. 
   Ebben a forgatókönyvben további összetevőkre van szükség. További információ: [VMware-to-Azure replikációs architektúra](https://aka.ms/asr-v2a-architecture).
* Sap-rendszerek a helyszíni Hyper-V-n futnak. Az SAP-rendszereket egy Azure-adatközpontban (Hyper-V-Azure-hoz-Azure vész-helyreállítási) egy vész-helyreállítási helyre is replikálja.
   Ebben a forgatókönyvben további összetevőkre van szükség. További információ: [Hyper-V-to-Azure replikációs architektúra](https://aka.ms/asr-h2a-architecture).

Ebben a cikkben egy **Azure-to-Azure** vész-helyreállítási forgatókönyvet használunk. A forgatókönyv bemutatja az SAP vész-helyreállítási képességeit a site recovery. Mivel a Site Recovery replikációja nem alkalmazásspecifikus, a leírt folyamat várhatóan más esetekre is vonatkozik.

### <a name="required-foundation-services"></a>Szükséges alapítványi szolgáltatások
Az ebben a cikkben tárgyalt forgatókönyvben a következő alapszolgáltatások vannak telepítve:
* Azure ExpressRoute vagy Azure VPN-átjáró
* Legalább egy Azure Active Directory-tartományvezérlő és DNS-kiszolgáló, amely az Azure-ban fut

Azt javasoljuk, hogy a Site Recovery telepítése előtt hozza létre ezt az infrastruktúrát.

## <a name="reference-sap-application-deployment"></a>SAP-alkalmazás telepítésének hivatkozása

Ez a referenciaarchitektúra sap NetWeaver fut egy Windows-környezetben az Azure-ban a magas rendelkezésre állású. Ez az architektúra telepítve van a konkrét virtuális gép (VM) méretek, amelyek módosíthatják, hogy megfeleljen a szervezet igényeinek.

![Egy tipikus SAP-telepítési minta diagramja](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Vészhelyreállítási szempontok

Vész-helyreállítási kell lennie egy másodlagos régiófeladat-át. Minden egyes szint más-más stratégiát használ a vész-helyreállítási védelem biztosításához.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>SAP webdiszpécserkészleteket futtató virtuális gépek

A webdiszpécser összetevő az SAP-alkalmazáskiszolgálók közötti SAP-forgalom terheléselosztójaként működik. A Web Dispatcher összetevő magas rendelkezésre állásának elérése érdekében az Azure Load Balancer megvalósítja a párhuzamos webdiszpécser beállítást. A Web Dispatcher ciklikus multiplexeléses konfigurációt használ a HTTP(S) forgalom elosztásához a kiegyensúlyozó készletben elérhető webdiszpécsere-kezelők között.

#### <a name="vms-running-application-servers-pools"></a>Alkalmazáskiszolgáló-készleteket futtató virtuális gépek
Az SMLG-tranzakció kezeli az ABAP alkalmazáskiszolgálók bejelentkezési csoportjait. A központi szolgáltatások üzenetkiszolgálón belüli terheléselosztási funkciót használja a számítási feladatok elosztásához az SAP-alkalmazások kiszolgálókészletei között SAPGUI-k és RFC-forgalom számára. Ezt a kezelést a Site Recovery segítségével replikálhatja.

#### <a name="vms-running-sap-central-services-clusters"></a>SAP központi szolgáltatási fürtöket futtató virtuális gépek
Ez a referenciaarchitektúra központi szolgáltatásokat futtat az alkalmazásszinten lévő virtuális gépeken. A központi szolgáltatások egy lehetséges egyetlen hibapont, ha egyetlen virtuális gép. A tipikus üzembe helyezés és a magas rendelkezésre állás nem követelmény.

Magas rendelkezésre állású megoldás megvalósításához használhat megosztott lemezfürtöt vagy fájlmegosztási fürtöt. Ha virtuális gépeket szeretne konfigurálni egy megosztott lemezfürthöz, használja a Windows Server feladatátvevő fürtöt. Azt javasoljuk, hogy a felhőbeli tanút használja kvórumtanúként.

 > [!NOTE]
 > Mivel a Site Recovery nem replikálja a felhőbeli tanúsító, azt javasoljuk, hogy telepítse a felhőbeli tanúsító a vész-helyreállítási régióban.

A feladatátvevő fürtkörnyezet támogatásához a [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) elvégzi a fürt megosztott kötetfunkcióját. A függvényben a SIOS DataKeeper Cluster replikálja a fürtcsomópontok tulajdonában lévő független lemezeket. Mivel az Azure natív módon nem támogatja a megosztott lemezeket, a SIOS által biztosított megoldásokat igényel.

A fürtözést fájlmegosztási fürt megvalósításával is kezelheti. [Az SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) nemrég módosította a központi szolgáltatások telepítési mintáját, hogy unc-útvonalon keresztül férjen hozzá a /sapmnt globális könyvtárakhoz. Továbbra is javasoljuk, hogy a /sapmnt UNC megosztás magas rendelkezésre állású legyen. Ellenőrizheti a központi szolgáltatások példányát. Használja a Windows Server feladatátvevő fürtöt a Scale Out File Server (SOFS) kiszolgálóval és a Windows Server 2016 Direct (S2D) szolgáltatással.

 > [!NOTE]
 > A Site Recovery jelenleg csak a közvetlen tárolóhelyeket használó virtuális gépek összeomlás-konzisztens pontreplikációját és a SIOS Datakeeper passzív csomópontját támogatja.


## <a name="more-disaster-recovery-considerations"></a>További vész-helyreállítási szempontok

A Site Recovery használatával vezényelheti a teljes SAP-telepítés feladatátvételt az Azure-régiók között.
A vészhelyreállítás beállításának lépéseit az alábbiakban ismerteti:

1. Virtuális gépek replikálása
1. Helyreállítási hálózat tervezése
1. Tartományvezérlő replikálása
1. Adatbázisszint replikálása
1. Futtasson egy feladatátvételi tesztet
1. Feladatátvétel futtatása

Az alábbiakban a példában használt minden egyes réteg vész-helyreállítási javaslata.

 **SAP-szintek** | **Ajánlás**
 --- | ---
**SAP webdiszpécser készlet** |  Replikálás a Hely helyreállítása használatával 
**SAP alkalmazáskiszolgáló-készlet** |  Replikálás a Hely helyreállítása használatával 
**SAP központi szolgáltatások fürtje** |  Replikálás a Hely helyreállítása használatával 
**Active directory virtuális gépek** |  Az Active Directory replikációjának használata 
**SQL-adatbáziskiszolgálók** |  Az SQL Server mindig replikációs használata

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

Az SAP-alkalmazás virtuális gépei replikálásának megkezdéséhez kövesse a [virtuális gép Replikálása az Azure-ba](azure-to-azure-walkthrough-enable-replication.md)című útmutatót.

* Az Active Directory és a DNS védelmével kapcsolatos útmutatásért olvassa el [az Active Directory és a DNS védelmét.](site-recovery-active-directory.md)

* Az SQL Server en futó adatbázisréteg védelmével kapcsolatos útmutatásért olvassa el az SQL Server védelme című [cikket.](site-recovery-sql.md)

## <a name="networking-configuration"></a>Hálózati konfiguráció

Statikus IP-cím használata esetén megadhatja azt az IP-címet, amelyet a virtuális gépnek el kell vennie. Az IP-cím beállításához nyissa meg a **Számítási és hálózati beállítások** > **Hálózati csatoló kártyát.**

![Képernyőkép, amely bemutatja, hogyan állíthat be privát IP-címet a Webhely-helyreállítási hálózati csatoló kártya ablaktáblájában](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása

A helyreállítási terv támogatja a különböző szintek szekvenálása egy többrétegű alkalmazás feladatátvétel során. A szekvenálás segít fenntartani az alkalmazások konzisztenciáját. Amikor helyreállítási tervet hoz létre egy többrétegű webalkalmazáshoz, hajtsa végre a Helyreállítási terv létrehozása a Site Recovery használatával című részben [ismertetett](site-recovery-create-recovery-plans.md)lépéseket.

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuális gépek hozzáadása feladatátvételi csoportokhoz

1. Hozzon létre egy helyreállítási tervet az alkalmazáskiszolgáló, a webdiszpécser és az SAP Központi szolgáltatások virtuális gépei hozzáadásával.
1. Válassza **a Testreszabás lehetőséget** a virtuális gépek csoportosításához. Alapértelmezés szerint minden virtuális gép az 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Parancsfájlok hozzáadása a helyreállítási tervhez
Ahhoz, hogy az alkalmazások megfelelően működjenek, előfordulhat, hogy bizonyos műveleteket kell végeznie az Azure virtuális gépeken. Végezze el ezeket a műveleteket a feladatátvétel után vagy egy teszt feladatátvétel során. A feladatátvételt követő egyes műveletek et is automatizálhat. Frissítse például a DNS-bejegyzést, és módosítsa a kötéseket és a kapcsolatokat a megfelelő parancsfájlok helyreállítási tervhez való hozzáadásával.

A leggyakrabban használt Site Recovery parancsfájlokat az Azure Automation-fiókba telepítheti az **Azure-ba való üzembe helyezés**lehetőséget választva. Ha bármely közzétett parancsfájlt használ, kövesse a parancsfájlban található útmutatást.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adjon hozzá egy művelet előtti parancsfájlt az 1- es csoporthoz, hogy az SQL Server rendelkezésre állási csoportját átvegye. Használja a mintaparancsfájlokban közzétett ASR-SQL-FailoverAG parancsfájlt. Kövesse a parancsfájlban található útmutatást, és megfelelően hajtsa végre a szükséges módosításokat a parancsfájlban.
1. Adjon hozzá egy művelet utáni parancsfájlt, amelyhez egy terheléselosztót csatolhat a webes réteg (1. csoport) feladatátvevő virtuális gépeihez. Használja a mintaparancsfájlokban közzétett ASR-AddSingleLoadBalancer parancsfájlt. Kövesse a parancsfájlban található útmutatást, és szükség szerint hajtsa végre a szükséges módosításokat a parancsfájlban.

![SAP helyreállítási terv](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. Az Azure Portalon válassza ki a Recovery Services-tároló.
1. Válassza ki az SAP-alkalmazásokhoz létrehozott helyreállítási tervet.
1. Kattintson a **Feladatátvétel tesztelése** elemre.
1. A teszt feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot és az Azure virtuális hálózatot.
1. Ha a másodlagos környezet ben, végezze el az ellenőrzéseket.
1. Ha az ellenőrzések befejeződtek, tisztítsa meg a feladatátvételi környezetet a **Karbantartás teszt feladatátvétel**lehetőségének kiválasztásával.

További információ: [Feladatátvétel tesztelése az Azure-ban a Site Recovery.for](site-recovery-test-failover-to-azure.md)more information, see Test failover to Azure in Site Recovery .

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1. Az Azure Portalon válassza ki a Recovery Services-tároló.
1. Válassza ki az SAP-alkalmazásokhoz létrehozott helyreállítási tervet.
1. Válassza a **Feladatátvétel** lehetőséget.
1. A feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot.

További információ: [Feladatátvétel a Site Recovery alkalmazásban.](site-recovery-failover.md)

## <a name="next-steps"></a>További lépések
* További információ az SAP NetWeaver-telepítések vész-helyreállítási megoldásának létrehozásáról a Site Recovery használatával. Lásd a letölthető tanulmány [SAP NetWeaver: Building a Disaster Recovery megoldás site recovery](https://aka.ms/asr_sap). A tanulmány ismerteti a különböző SAP-architektúrák ajánlásait. Láthatja a támogatott alkalmazások és virtuálisgép-típusok az SAP az Azure-ban. A vész-helyreállítási megoldás tesztelésére is vannak tervlehetőségek.
* További információ a [többi számítási feladat replikálásáról](site-recovery-workload.md) a Site Recovery használatával.
