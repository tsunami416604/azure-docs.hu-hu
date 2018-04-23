---
title: A többrétegű SAP NetWeaver alkalmazástelepítést Azure Site Recovery segítségével védeni |} Microsoft Docs
description: A cikkből megtudhatja, hogyan védheti SAP NetWeaver alkalmazástelepítések Azure Site Recovery segítségével.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: b6ab734186f23d51d60e51bd0946329d5209097b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
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

Ez a cikk egy Azure-Azure vész-helyreállítási forgatókönyv használjuk a Site Recovery SAP vész helyreállítási funkcióinak bemutatása. Mivel a Site Recovery replikációs nem az alkalmazás-specifikus, ismertetett eljárás várt is alkalmazhat a többi olyan forgatókönyvet.

### <a name="required-foundation-services"></a>Szükséges foundation szolgáltatások
A forgatókönyvben a cikkben arról lesz szó a következő foundation szolgáltatások vannak telepítve:
* Az Azure expressroute-on vagy az Azure VPN Gateway
* Legalább egy Active Directory-tartományvezérlő és DNS-kiszolgáló, az Azure-ban fut

Azt javasoljuk, hogy kapcsolatot hoz létre az infrastruktúra a Site Recovery telepítése előtt.

## <a name="typical-sap-application-deployment"></a>Tipikus SAP-alkalmazás telepítése
Nagy számú SAP-ügyfelek általában telepíteni 6 – 20 egyedi SAP alkalmazások között. Ezeket az alkalmazásokat a legtöbb SAP NetWeaver ABAP vagy Java motorok alapulnak. Sok kisebb, adott nem - NetWeaver SAP önálló motorok, és általában nem SAP alkalmazásmódjai, támogatja az alapvető NetWeaver alkalmazásokat.  

Nagyon fontos a készlet összes a SAP-alkalmazást, hogy a környezetében. Verziók, javítások, méretét, forgalom sebességét és adatmegőrzési lemezkövetelmények telepítési módban (kétrétegű és háromrétegű), majd meghatározásához.

![Egy tipikus SAP telepítési minta ábrája](./media/site-recovery-sap/sap-typical-deployment.png)

Natív adatbázis-kezelő eszközök, például az SQL Server AlwaysOn, Oracle Data Guard vagy SAP HANA replikációs használatával védi az SAP adatbázis adatmegőrző réteget. Az SAP adatbázisrétegben, például az ügyfél réteg a Site Recovery által nem védett. Fontos figyelembe venni azon tényezőket, ez a réteg érintő. Tényezők közé tartozik a DNS-propagálás késleltetés, a biztonsági és a távelérés a vész-helyreállítási adatközpontra.

A Site Recovery-e az alkalmazási rétegre, beleértve az SAP SCS és az ASC az ajánlott megoldás. Más alkalmazások, például NetWeaver SAP-alkalmazásokból és nem SAP-alkalmazásokból, a SAP telepítési környezetben részét képezik. Azokat a Site Recovery szolgáltatással kell védeni.

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása
SAP alkalmazás virtuális gépeire replikálása az Azure vész-helyreállítási datacenter elindításához kövessék [a virtuális gépek replikálása Azure-bA](azure-to-azure-walkthrough-enable-replication.md).

Ha egy statikus IP-címet használja, az IP-cím, amelyet a virtuális gépen ahhoz, hogy is megadhat. Az IP-cím megadásához keresse fel **számítási és hálózati beállításai** > **hálózati kártya**.

![Egy privát IP-címének beállítása a Site Recovery hálózati illesztő kártya ablaktáblán képernyőkép](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv támogatja a különböző rétegek alkalmazás-előkészítés során egy többrétegű alkalmazást egy feladatátvétel során. Alkalmazás-előkészítés segítségével biztosítja az egységességet alkalmazás. A helyreállítási terv többrétegű webalkalmazás létrehozásakor teljes lépéseit ismerteti [helyreállítási terv létrehozása a Site Recovery segítségével](site-recovery-create-recovery-plans.md).

### <a name="add-scripts-to-the-recovery-plan"></a>A helyreállítási terv parancsprogramokat hozzáadása
Az alkalmazások megfelelő működéséhez szükség lehet néhány a műveleteket az Azure virtuális gépeken a feladatátvétel után, vagy egy feladatátvételi tesztje közben. Néhány feladatátvételt követően művelet automatizálható. Például a DNS-bejegyzés frissítéséhez, és módosítsa a kötések és kapcsolatok megfelelő parancsfájlokat ad hozzá a helyreállítási terv.

### <a name="dns-update"></a>DNS-frissítési
Ha a DNS dinamikus DNS-frissítési konfigurált, virtuális gépek általában frissítse a DNS-az új IP-címmel indításakor. Ha hozzá szeretne adni egy explicit lépés DNS frissítése a virtuális gépeket az új IP-címekkel rendelkező, adja hozzá a [parancsfájl a DNS-ben az IP-címének frissítése](https://aka.ms/asr-dns-update) a feladatátvételt követően műveletek a helyreállítási terv csoportok.  

## <a name="example-azure-to-azure-deployment"></a>Azure-Azure központi telepítésre példát
Az alábbi ábrán látható, a Site Recovery Azure Azure vész-helyreállítási forgatókönyv:

![Egy Azure-Azure replikációs forgatókönyvének ábrája](./media/site-recovery-sap/sap-replication-scenario.png)

* Az elsődleges adatközpont szingapúri (Azure Délkelet-Ázsia) van. A vész-helyreállítási datacenter Hongkong (Azure Kelet-Ázsia) van. Ebben a forgatókönyvben két virtuális gépeken futó SQL Server AlwaysOn szingapúri szinkron módban helyi magas rendelkezésre állást biztosítja.
* A fájlmegosztás SAP ASC az SAP egyetlen ponton felmerülő hibákat magas rendelkezésre állást biztosít. A fájlmegosztás ASC egy fürt megosztott lemez nem igényel. Alkalmazások, például SIOS nem lesz szükség.
* A DBMS réteg vész-helyreállítási védelem aszinkron replikáció használatával érhető el.
* A forgatókönyv bemutatja, "szimmetrikus vész-helyreállítási." Ez a kifejezés egy vész-helyreállítási megoldást, amely az üzemi pontos replikájának ismerteti. A vész-helyreállítási megoldás az SQL Server helyi magas rendelkezésre állású rendelkezik. Szimmetrikus vész-helyreállítási nem kötelező, az adatbázis réteg. Sok ügyfél felhőalapú telepítések gyorsan elkészítse vész-helyreállítási esemény után a helyi magas rendelkezésre állású csomópont rugalmasan előnyeit.
* Az ábra mutatja be, a SAP NetWeaver ASC és a Site Recovery által replikált kiszolgáló alkalmazásréteg.

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
