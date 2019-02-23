---
title: Állítsa be a vész-helyreállítási többrétegű SAP NetWeaver alkalmazás üzembe helyezése az Azure Site recoveryvel |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan állítható be SAP NetWeaver-alkalmazások központi telepítése az Azure Site Recovery használata vész-helyreállítási.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 68efc039c5de5d7f61b7ce34e74c6c2cf4bad027
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670876"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Set up disaster recovery for a multi-tier SAP NetWeaver app deployment

A legtöbb nagy méretű és közepes méretű SAP-környezetekhez valamilyen vész-helyreállítási megoldást használja. A robusztus és testable vészhelyreállítási megoldások fontosságát, több alapvető üzleti folyamatokat, mint az SAP alkalmazások áthelyezett nőtt. Az Azure Site Recovery tesztelte, és az SAP-alkalmazások integrált. A Site Recovery meghaladja a legtöbb helyszíni vészhelyreállítási megoldások, és egy alacsonyabb teljes birtoklási költség (TCO) versengő megoldások, mint a képességeket.

A Site Recovery a következőket teheti:
* **Helyszíni SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét engedélyezése** összetevők az Azure-ba való replikálásával.
* **Engedélyezze az Azure-ban az SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét** összetevők egy másik Azure-adatközpontba való replikálásával.
* **Egyszerűsítse a felhőbe való migrálást** az SAP üzemelő példányok áttelepítése az Azure Site Recovery használatával.
* **Egyszerűsítse az SAP projektek frissítését, tesztelését és prototípuskészítését** hozzon létre egy éles klón igény szerinti SAP alkalmazások teszteléséhez.

Ez a cikk azt ismerteti, hogyan védheti meg az SAP NetWeaver alkalmazás-KözpontiTelepítés [Azure Site Recovery](site-recovery-overview.md). Az a cikk ismerteti az ajánlott eljárások védelme érdekében a háromrétegű SAP NetWeaver üzembe helyezés az Azure Site Recovery használatával egy másik Azure-adatközpontba való replikálásával. Azt ismerteti, hogy támogatott forgatókönyveket és a konfigurációk és hogyan hajthat végre feladatátvételi tesztek (vészhelyreállítási próba) és a tényleges feladatátvételt.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy tudja, hogyan a következő feladatokat végezheti el:

* [A virtuális gépek replikálása az Azure-bA](azure-to-azure-walkthrough-enable-replication.md)
* [A helyreállítási hálózat tervezése](site-recovery-azure-to-azure-networking-guidance.md)
* [Végezzen feladatátvételi tesztet az Azure-bA](azure-to-azure-walkthrough-test-failover.md)
* [Ehhez a feladatátvétel az Azure-bA](site-recovery-failover.md)
* [A tartományvezérlő replikálása](site-recovery-active-directory.md)
* [SQL Server replikálása](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Támogatott esetek
A Site Recovery segítségével megvalósítani egy vész-helyreállítási megoldást a következő esetekben:
* SAP-rendszerek egy Azure-adatközpontban fut, amely egy másik Azure-adatközpontba (Azure – Azure vészhelyreállítási) replikálni. További információkért lásd: [Azure – Azure replikációs architektúra](https://aka.ms/asr-a2a-architecture).
* VMware-en (vagy fizikai) futó SAP-rendszereinket helyszíni egy vész-helyreállítási helyként az Azure-adatközpontban (VMware – Azure vészhelyreállítási) a replikáció. Ez a forgatókönyv néhány további összetevőket igényel. További információkért lásd: [VMware – Azure replikációs architektúra](https://aka.ms/asr-v2a-architecture).
* Hyper-V helyszíni replikált egy vész-helyreállítási helyként az Azure-adatközpontban (Hyper-V – Azure vészhelyreállítási) futó SAP-rendszereinket. Ez a forgatókönyv néhány további összetevőket igényel. További információkért lásd: [Hyper-V – Azure replikációs architektúra](https://aka.ms/asr-h2a-architecture).

Ebben a cikkben használjuk egy **Azure – Azure** vész-helyreállítási helyzetekre használatával a Site Recovery az SAP vész-helyreállítási képességeit mutatja be. A Site Recovery replikációs nem alkalmazás-specifikus, mert a leírt folyamat várhatóan más esetben is érvényesek.

### <a name="required-foundation-services"></a>Szükséges foundation services
Az ebben a cikkben bemutatjuk a forgatókönyv a következő foundation szolgáltatások vannak telepítve:
* Az Azure ExpressRoute vagy az Azure VPN Gateway
* Legalább egy Active Directory tartományvezérlővel és DNS-kiszolgáló Azure-ban

Azt javasoljuk, hogy az infrastruktúra létrehozásához, a Site Recovery üzembe helyezése előtt.

## <a name="reference-sap-application-deployment"></a>Referencia az SAP-alkalmazások központi telepítése

Ez a referenciaarchitektúra bemutatja, az SAP NetWeaver környezetben futó Windows Azure-ban magas rendelkezésre állású.  Ez az architektúra, amely a szervezet igényeinek megfelelően módosíthatja adott virtuális gép (VM) méretek van telepítve.

![Az SAP üzembe helyezési jellegzetes ábrája](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Vészhelyreállítási szempontok

A vészhelyreállítás (DR) meg kell tudni átadja a feladatokat egy másodlagos régióba. Minden egyes réteg más stratégiával biztosít vészhelyreállítási (DR) védelmet.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Virtuális gépeken futó SAP Web Dispatcher-készlet 
A Web Dispatcher összetevő terheléselosztóként szolgál az SAP-forgalmat az SAP-alkalmazáskiszolgálók között. Magas rendelkezésre állás a Web Dispatcher-összetevő, a párhuzamos Web Dispatcher-telepítő megvalósítása HTTP (S)-forgalom elosztását az elérhető webes elosztás a terheléselosztó készlet Ciklikus időszeleteléses konfigurációját az Azure Load Balancer szolgál ki. Ez replikálja az Azure Site Recovery (ASR) használatával, és automatizálási szkriptek load balancer konfigurálása a vészhelyreállítási régióban lévő lesz használható. 

#### <a name="vms-running-application-servers-pool"></a>Alkalmazáskészlet-kiszolgálókon futó virtuális gépek
Bejelentkezési csoportokat ABAP alkalmazáskiszolgálók kezeléséhez, az SMLG tranzakció szolgál. A terheléselosztási függvényt az üzenet-kiszolgálón, a központi szolgáltatások használatával oszthatja meg a munkaterhelést SAP alkalmazáskészlet-kiszolgálók között SAPGUIs és RFC forgalmat. A rendszer replikálja az Azure Site Recoveryvel 

#### <a name="vms-running-sap-central-services-cluster"></a>SAP Central Services fürt futó virtuális gépek
Ez a referenciaarchitektúra a virtuális gépek központi szolgáltatást futtat az alkalmazás szinten. A központi szolgáltatások egy potenciálisan hibaérzékeny pont (SPOF), egyetlen virtuális gép telepítésekor – szokásos telepítése, ha magas rendelkezésre állás nem követelmény.<br>

Egy magas rendelkezésre állású megoldás bevezetésére, vagy egy megosztott lemezfürtök, vagy egy fájlkiszolgáló-megosztás fürt használható. Megosztott lemezfürt virtuális gépek konfigurálásához használja a Windows Server feladatátvételi fürtben. Felhőbeli tanúsító kvórum tanúsító ajánlott. 
 > [!NOTE]
 > Az Azure Site Recovery nem replikálja a felhőbeli tanúsító ezért javasoljuk, hogy a vészhelyreállítási régióban lévő a felhőbeli tanúsító telepítése.

A feladatátvételi fürtkörnyezetnek támogatásához [az SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) hajtja végre a fürt megosztott kötet függvény szerepét a fürtcsomópontok független lemezeinek replikálásával. Az Azure nem natív módon támogatja a megosztott lemezeket, és ezért az SIOS által biztosított megoldások igényel. 

Kezelje a fürtszolgáltatás egy másik úgy, hogy egy fájlkiszolgáló-megosztás fürt megvalósítása. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) nemrég módosította a szolgáltatások központi telepítési minta a /sapmnt globális könyvtárak keresztül UNC elérési út elérésére. Azonban továbbra is ajánlott annak biztosításához, hogy a /sapmnt UNC megosztást magas rendelkezésre állású. Ezt megteheti a Central Services-példányon a méretezési csoport ki fájlkiszolgáló (SOFS) és a közvetlen tárolóhelyek (S2D) szolgáltatást a Windows Server 2016 Windows Server feladatátvételi fürt használatával. 
 > [!NOTE]
 > Jelenleg az Azure Site Recovery támogatási csak összeomlás storage spaces direct és a passzív az SIOS Datakeeper node használatával virtuális gépek alkalmazáskonzisztens replikálása


## <a name="disaster-recovery-considerations"></a>Vészhelyreállítási szempontok

Az Azure Site Recovery segítségével előkészíthető a feladatátvétel alatt teljes SAP üzemelő példány Azure-régióban.
Az alábbiakban a vész-helyreállítási beállításának lépései 

1. Virtuális gépek replikálása 
2. A helyreállítási hálózat tervezése
3.  A tartományvezérlő replikálása
4.  Adatok csomagnál replikálása 
5.  Futtasson egy feladatátvételi tesztet 
6.  Feladatátvétel futtatása 

Alább a javaslatot, az egyes csomagok ebben a példában használt vész-helyreállítási van. 

 **Az SAP-szint** | **Az ajánlás**
 --- | ---
**SAP Web Dispatcher pool** |  Replikálni a Site recovery használatával 
**SAP alkalmazáskészletet kiszolgáló** |  Replikálni a Site recovery használatával 
**Az SAP Central Services fürt** |  Replikálni a Site recovery használatával 
**Az Active directory virtuális gépek** |  Az Active directory-replikáció 
**SQL database-kiszolgálók** |  SQL always a replikáció

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

SAP alkalmazás virtuális gépek replikálása az Azure vész-helyreállítási adatközpont, kövesse az útmutató [a virtuális gépek replikálása az Azure-bA](azure-to-azure-walkthrough-enable-replication.md).


* Az Active Directory és DNS védelme útmutatásért tekintse meg [védelme az Active Directory és DNS-](site-recovery-active-directory.md) dokumentumot.

* SQL Serveren futó adatbázisszint védelméről útmutatásért tekintse meg [védelme SQL Server](site-recovery-active-directory.md) dokumentumot.

## <a name="networking-configuration"></a>Hálózati konfiguráció

Statikus IP-címet használ, ha azt szeretné, hogy a virtuális gép IP-címet is megadhat. Az IP-cím megadásához keresse fel **számítás és hálózat beállításlapon** > **hálózati kártya**.

![Képernyőkép a magánhálózati IP-cím beállítása a Site Recovery hálózati adapter kártya panelen](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv támogatja az alkalmazás-előkészítés különböző rétegek egy többrétegű alkalmazást, a feladatátvétel alatt. Alkalmazás-előkészítés segít fenntartani a konzisztenciát alkalmazás. Amikor létrehoz egy helyreállítási terv többszintű webalkalmazások, teljes körű lépéseket ismertetett [helyreállítási terv létrehozása a Site Recovery használatával](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok felvétele

1.  Hozzon létre egy helyreállítási tervet az alkalmazáskiszolgáló, a web dispatcher és az SAP Central services virtuális gépek hozzáadásával.
2.  Kattintson a "Testreszabás" gombra a virtuális gépek csoportosítása. Alapértelmezés szerint minden virtuális gép a csoport "% 1" részét képezik.



### <a name="add-scripts-to-the-recovery-plan"></a>A helyreállítási tervbe szkriptek hozzáadása
Az alkalmazások megfelelő működéséhez szükség lehet néhány művelet az Azure-beli virtuális gépeken ehhez a feladatátvétel után, vagy tesztcélú feladatátvétel alatt. Egyes feladatátvétel utáni műveletek automatizálható. Például, a DNS-bejegyzés frissítéséhez, és módosíthatja a kötések és kapcsolatok hozzáadásával a megfelelő parancsfájlokat a helyreállítási tervbe.


A leggyakrabban használt Azure Site Recovery-szkriptek üzembe helyezhető az Automation-fiókját, ha a "Üzembe helyezés az Azure" gombra kattint. Bármely közzétett parancsfájl használatakor győződjön meg arról, hogy kövessék a parancsfájl.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Előzetes művelet parancsfájl hozzáadása (1. csoport) a feladatátvétel SQL rendelkezésre állási csoporthoz. Az "ASR-SQL-FailoverAG" parancsfájl használata a minta parancsprogramok közzé. Győződjön meg arról, kövesse a parancsfájl található útmutatást, és megfelelően végezze el a szükséges módosításokat a parancsfájl.
2. Csatlakoztassa a feladatait egy terheléselosztó post művelet parancsfájl hozzáadása a feladatátviteli virtuális gépeket a webes réteg (1. csoport). Az "ASR-AddSingleLoadBalancer" parancsfájl használata a minta parancsprogramok közzé. Győződjön meg arról, kövesse a parancsfájl található útmutatást, és megfelelően végezze el a szükséges módosításokat a parancsfájl.

![Az SAP-helyreállítási terv](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1.  Az Azure Portalon válassza ki a helyreállítási tárban.
2.  Válassza ki a helyreállítási tervet, amelyet létrehozott SAP-alkalmazások.
3.  Kattintson a **Feladatátvétel tesztelése** elemre.
4.  A teszt feladatátvételi folyamat indításához válassza ki a helyreállítási pont és az Azure virtuális hálózat.
5.  A másodlagos környezet esetén hajtsa végre az ellenőrzések.
6.  Ha ellenőrzés befejeződött, tisztítsa meg a feladatátvételi környezet válassza **feladatátvételi teszt utáni karbantartás**.

További információkért lásd: [az Azure-bA a Site Recovery feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1.  Az Azure Portalon válassza ki a helyreállítási tárban.
2.  Válassza ki a helyreállítási tervet, amelyet létrehozott SAP-alkalmazások.
3.  Válassza a **Feladatátvétel** lehetőséget.
4.  A feladatátvételi folyamat indításához válassza ki a helyreállítási pontot.

További információkért lásd: [feladatátvétel a Site Recoveryben](site-recovery-failover.md).

## <a name="next-steps"></a>További lépések
* A Site Recovery használatával egy vészhelyreállítási megoldás az SAP NetWeaver-telepítések létrehozásával kapcsolatos további tudnivalókért lásd: a letölthető tanulmány [SAP NetWeaver: Létrehozását olyan vész-helyreállítási megoldást az Azure Site Recovery](https://aka.ms/asr-sap). A tanulmány különböző SAP-architektúrák vonatkozó javaslatokat tárgyalja, sorolja fel az támogatott alkalmazások és a virtuális gépek típusai az Azure-beli SAP és a vész-helyreállítási megoldás tesztelési terv beállításait ismerteti.
* Tudjon meg többet [más munkaterhelések replikálásához](site-recovery-workload.md) Site Recovery használatával.
