---
title: A többrétegű SAP NetWeaver alkalmazástelepítést Azure Site Recovery segítségével védeni |} Microsoft Docs
description: A cikkből megtudhatja, hogyan védheti SAP NetWeaver alkalmazástelepítések Azure Site Recovery segítségével.
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: asgang
ms.openlocfilehash: 27dfdec4e833a2f30963157ba2f4d95232e21270
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267332"
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Egy többrétegű SAP NetWeaver alkalmazástelepítést védeni a Site Recovery segítségével

A legtöbb nagy méretű és közepes méretű SAP üzemelő valamilyen vész-helyreállítási megoldást használja. Hatékony és testable vész-helyreállítási megoldások fontosságát növekedett, további alapfolyamatok üzleti alkalmazások, például SAP kerülnek. Az Azure Site Recovery tesztelte, és integrálva az SAP-alkalmazásokból. A Site Recovery meghaladja a kapacitását a legtöbb helyszíni vész-helyreállítási megoldások, és egy alacsonyabb tulajdonosi költségek (TCO) mint versengő megoldások.

A Site Recovery a következőket teheti:
* **Engedélyezze a védelmet a helyszíni SAP NetWeaver és nem NetWeaver éles alkalmazások** által összetevők replikálása Azure-bA.
* **SAP NetWeaver és nem NetWeaver az Azure-on futó termelési alkalmazások védelem** összetevők egy másik Azure adatközpontba replikálja.
* **Áttelepülés a felhőbe egyszerűsítése** az SAP üzemelő példányt át az Azure Site Recovery segítségével.
* **SAP projekt frissítéseket, a tesztelés és prototípusának egyszerűsítése** hozzon létre egy éles klónozása az SAP-alkalmazásokból tesztelési igény.

Ez a cikk ismerteti, hogyan védheti SAP NetWeaver alkalmazástelepítések [Azure Site Recovery](site-recovery-overview.md). A cikk ismerteti a gyakorlati tanácsok a védelme a háromrétegű SAP NetWeaver üzembe helyezés során, az Azure Site Recovery segítségével egy másik Azure adatközpontba replikálja. Ismerteti, hogyan támogatott forgatókönyvek és a konfigurációkat és a feladatátvételi tesztet (vészhelyreállítások részletezésének) végrehajtása és a tényleges feladatátvételt.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy tudja, hogyan hajtsa végre a következő feladatokat:

* [A virtuális gépek replikálása Azure-bA](azure-to-azure-walkthrough-enable-replication.md)
* [Egy helyreállítási hálózathoz. terv](site-recovery-azure-to-azure-networking-guidance.md)
* [Végezzen feladatátvételi tesztet az Azure-bA](azure-to-azure-walkthrough-test-failover.md)
* [Hajtsa végre a feladatátvételt az Azure-bA](site-recovery-failover.md)
* [A tartományvezérlő replikálja.](site-recovery-active-directory.md)
* [SQL Server replikálása](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Támogatott esetek
A Site Recovery segítségével valósítja meg a vész-helyreállítási megoldást a következő esetekben:
* SAP rendszerekre egy Azure-adatközpontban, amelyek egy másik Azure datacenter (Azure-Azure vész-helyreállítási) replikálódnak. További információkért lásd: [Azure-Azure replikációs architektúra](https://aka.ms/asr-a2a-architecture).
* A VMware rendszerben (vagy fizikai) SAP rendszerekre kiszolgálók helyszíni, hogy a replikálás egy katasztrófa utáni helyreállítás helyéhez egy Azure-adatközpontban (VMware-Azure katasztrófa utáni helyreállítás). Ez a forgatókönyv néhány további összetevők igényel. További információkért lásd: [VMware-Azure replikációs architektúra](https://aka.ms/asr-v2a-architecture).
* SAP rendszereken futó Hyper-V a helyszínen, a vész helyreállítási hely egy Azure-adatközpontban (Hyper-V-az-Azure-bA vész-helyreállítási) replikációját. Ez a forgatókönyv néhány további összetevők igényel. További információkért lásd: [Hyper-V--Azure-beli replikációs architektúra](https://aka.ms/asr-h2a-architecture).

Ebben a cikkben használjuk egy **Azure-Azure** katasztrófa utáni helyreállítása a Site Recovery SAP vész helyreállítási funkcióinak bemutatása. Mivel a Site Recovery replikációs nem az alkalmazás-specifikus, ismertetett eljárás várt is alkalmazhat a többi olyan forgatókönyvet.

### <a name="required-foundation-services"></a>Szükséges foundation szolgáltatások
A forgatókönyvben a cikkben arról lesz szó a következő foundation szolgáltatások vannak telepítve:
* Az Azure expressroute-on vagy az Azure VPN Gateway
* Legalább egy Active Directory-tartományvezérlő és DNS-kiszolgáló, az Azure-ban fut

Azt javasoljuk, hogy kapcsolatot hoz létre az infrastruktúra a Site Recovery telepítése előtt.

## <a name="reference-sap-application-deployment"></a>Hivatkozás SAP alkalmazás központi telepítése

A referencia-architektúrában látható SAP NetWeaver környezetben futó Windows Azure magas rendelkezésre állású.  Ez az architektúra van szükség az adott virtuális gép (VM), amelyek módosíthatók a szervezet igényeinek megfelelően van telepítve.

![Egy tipikus SAP telepítési minta ábrája](./media/site-recovery-sap/reference_sap.png)

## <a name="disaster-recovery-considerations"></a>Vész-helyreállítási szempontok

A vészhelyreállítás (DR) egy másodlagos régióba történő feladatátvételt képesnek kell lennie. Minden egyes réteg más stratégiával biztosít vészhelyreállítási (DR) védelmet.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>SAP webes kézbesítő készlet futó virtuális gépeket 
A webes kézbesítő összetevő SAP forgalom között az SAP alkalmazáskiszolgálók terheléselosztó használható. A webes kézbesítő összetevő magas rendelkezésre állásának eléréséhez, az Azure Load Balancer a párhuzamos webes kézbesítő telepítő megvalósítását a HTTP (S)-forgalom elosztását a rendelkezésre álló webes elosztás a terheléselosztó-készlet ciklikus multiplexelési konfigurációjának szolgál. Ez a rendszer replikálja Azure hely Recovery(ASR) használatával, és automatizálási parancsfájlokat terheléselosztó konfigurálásához a vész-helyreállítási régió fog történni. 

####<a name="vms-running-application-servers-pool"></a>Alkalmazáskészlet-kiszolgálókon futó virtuális gépeket
Bejelentkezési csoportok ABAP alkalmazáskiszolgálók kezeléséhez, a SMLG tranzakció szolgál. A terheléselosztási függvény központi szolgáltatások üzenet-kiszolgálón belül használja SAPGUIs és RFC oszthatja meg a munkaterhelést SAP alkalmazáskészlet-kiszolgálók közötti forgalmat. Ez a rendszer replikálja Azure Site Recovery segítségével 

####<a name="vms-running-sap-central-services-cluster"></a>SAP központi szolgáltatások fürt futó virtuális gépeket
A referencia-architektúrában központi szolgáltatások virtuális gépeken futtatja az alkalmazáshoz tartozó. A központi szolgáltatások egy potenciális hibaérzékeny pontot (SPOF) egy virtuális központi telepítési – tipikus telepítése esetén magas rendelkezésre állás nem követelmény.<br>

Egy magas rendelkezésre állású megoldás bevezetésére, vagy egy megosztott lemezfürt, vagy egy fájlkiszolgáló-megosztás fürt használható. Virtuális gépek megosztott lemezfürt konfigurálásához használja a Windows Server feladatátvételi fürtben. A kvórum tanúsítóját felhő tanúsító ajánlott. 
 > [!NOTE]
 > Az Azure Site Recovery nem replikálja a felhő tanúsító ezért javasoljuk, hogy a felhő tanúsító a vész-helyreállítási régióban telepítése.

A feladatátvevő fürt környezet támogatásához [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) által a fürtcsomópontok által birtokolt független lemezek replikálásához a fürt megosztott kötet funkciót hajt végre. Azure natív módon nem támogatja a megosztott lemezeket, és ezért a SIOS által biztosított megoldások szükségesek. 

Fürtszolgáltatás kezelésére úgy, hogy egy fájlkiszolgáló-megosztás fürt alkalmazza. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) nemrég módosította a szolgáltatásokhoz központi telepítési minta a /sapmnt globális könyvtárak keresztül UNC elérési út elérésére. Ez a változás eltávolítja SIOS kapcsolatos követelmények vagy más megosztott lemez megoldás, a központi szolgáltatások virtuális gépeken. Győződjön meg arról, hogy a /sapmnt UNC-megosztásnevet magas rendelkezésre állású továbbra is ajánlott. Ezt megteheti a központi Services-példányon méretezési kimenő fájlkiszolgálóval (sofs-sel) és a tárolóhelyek közvetlen (S2D) szolgáltatás a Windows Server 2016 Windows Server feladatátvevő fürt segítségével. 
 > [!NOTE]
 > Jelenleg Azure Site Recovery-támogatás csak összeomlás tárolóhelyek – közvetlen használó virtuális gépek alkalmazáskonzisztens replikálása 


## <a name="disaster-recovery-considerations"></a>Vészhelyreállítási szempontok

Azure Site Recovery segítségével levezényelni a nem keresztül, teljes SAP üzemelő Azure-régiók között.
A vész-helyreállítási beállításának lépései a következők 

1. Virtuális gépek replikálása 
2. Egy helyreállítási hálózathoz. terv
3.  A tartományvezérlő replikálja.
4.  Alap adatrétegbeli replikálása 
5.  Végezzen feladatátvételi tesztet 
6.  Feladatátvétellel 

Alább a javaslat a példában szereplő minden egyes réteg vész-helyreállítási van. 

 **SAP rétegek** | **A javaslat**
 --- | ---
**SAP webes kézbesítő készlet** |  Replikálásához a Site recovery segítségével 
**SAP alkalmazáskészlet-kiszolgáló** |  Replikálásához a Site recovery segítségével 
**SAP szolgáltatásokhoz központi fürtre** |  Replikálásához a Site recovery segítségével 
**Az Active directory virtuális gépek** |  Active directory-replikáció 
**SQL adatbázis-kiszolgálók** |  Mindig a replikációs SQL

##<a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

SAP alkalmazás virtuális gépeire replikálása az Azure vész-helyreállítási datacenter elindításához kövessék [a virtuális gépek replikálása Azure-bA](azure-to-azure-walkthrough-enable-replication.md).


* Active Directory és a DNS védelméről útmutatásért tekintse meg [Active Directory védelmére és a DNS-](site-recovery-active-directory.md) dokumentum.

* Működő SQL server adatbázis-rétegből ellátásáról útmutatásért tekintse meg [SQL Server védelme](site-recovery-active-directory.md) dokumentum.

## <a name="networking-configuration"></a>Hálózati konfigurációja

Ha egy statikus IP-címet használja, az IP-cím, amelyet a virtuális gépen ahhoz, hogy is megadhat. Az IP-cím megadásához keresse fel **számítási és hálózati beállításai** > **hálózati kártya**.

![Egy privát IP-címének beállítása a Site Recovery hálózati illesztő kártya ablaktáblán képernyőkép](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv támogatja a különböző rétegek alkalmazás-előkészítés során egy többrétegű alkalmazást egy feladatátvétel során. Alkalmazás-előkészítés segítségével biztosítja az egységességet alkalmazás. A helyreállítási terv többrétegű webalkalmazás létrehozásakor teljes lépéseit ismerteti [helyreállítási terv létrehozása a Site Recovery segítségével](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok hozzáadása

1.  A helyreállítási terv létrehozásához adja meg a kiszolgáló, webes kézbesítő és SAP központi szolgáltatások virtuális gépeken.
2.  Kattintson a "Testreszabás" a virtuális gépek csoportosításához. Alapértelmezés szerint az összes virtuális gép "Csoport 1" részét képezik.



### <a name="add-scripts-to-the-recovery-plan"></a>A helyreállítási terv parancsprogramokat hozzáadása
Az alkalmazások megfelelő működéséhez szükség lehet néhány a műveleteket az Azure virtuális gépeken a feladatátvétel után, vagy egy feladatátvételi tesztje közben. Néhány feladatátvételt követően művelet automatizálható. Például a DNS-bejegyzés frissítéséhez, és módosítsa a kötések és kapcsolatok megfelelő parancsfájlokat ad hozzá a helyreállítási terv.


A leggyakrabban használt Azure Site Recovery-parancsfájlok telepítheti be az alábbi "Az Azure telepítéséhez" gombra kattintva Automation-fiók. Ha bármely közzétett parancsfájl használ, győződjön meg arról, akkor kövesse az útmutató a parancsfájlt.

[![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Előtti parancsfájlművelet hozzáadása csoporthoz "% 1" SQL rendelkezésre állási csoport feladatai. A parancsfájllal "ASR-SQL-FailoverAG" a minta parancsfájlokat tesznek közzé. Győződjön meg arról, kövesse az útmutató a parancsfájlt, és végrehajtják a szükséges módosításokat a parancsfájl megfelelően.
2. Post művelet parancsfájl csatolni a feladatait egy terheléselosztó hozzáadása a virtuális gépeket webes réteg (1. csoport). A parancsfájllal "ASR-AddSingleLoadBalancer" a minta parancsfájlokat tesznek közzé. Győződjön meg arról, kövesse az útmutató a parancsfájlt, és végrehajtják a szükséges módosításokat a parancsfájl megfelelően.

![SAP helyreállítási terv](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1.  Az Azure-portálon válassza ki a Recovery Services-tároló.
2.  Válassza ki a helyreállítási tervet, amelyet létrehozott SAP-alkalmazásokból.
3.  Válassza ki **feladatátvételi teszt**.
4.  A teszt feladatátvételi folyamat elindításához válassza ki a helyreállítási pont és az Azure virtuális hálózat.
5.  A másodlagos környezet esetén hajtsa végre az érvényesítést.
6.  Ellenőrzés be nem fejeződik, a feladatátvételi környezetet tisztítása kijelölése **karbantartása a feladatátvételi teszt**.

További információkért lásd: [az Azure Site Recovery a feladatátvételi teszt](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1.  Az Azure-portálon válassza ki a Recovery Services-tároló.
2.  Válassza ki a helyreállítási tervet, amelyet létrehozott SAP-alkalmazásokból.
3.  Válassza ki **feladatátvételi**.
4.  A feladatátvételi folyamat elindításához válassza ki a helyreállítási pontot.

További információkért lásd: [a Site Recovery feladatátvételi](site-recovery-failover.md).

## <a name="next-steps"></a>További lépések
* A Site Recovery segítségével a vészhelyreállítási megoldás az SAP NetWeaver központi telepítések létrehozásával kapcsolatos további tudnivalókért tekintse meg a letölthető [SAP NetWeaver: egy vész-helyreállítási megoldást az Azure Site Recovery szolgáltatással építése](http://aka.ms/asr-sap). A tanulmány különböző SAP architektúrák javaslatokat ismerteti, támogatott alkalmazások és a Virtuálisgép-típusokon Azure SAP, és ismerteti a vész-helyreállítási megoldás tesztelési terv beállításait.
* További információ [replikál a többi munkaterhelését](site-recovery-workload.md) Site Recovery segítségével.
