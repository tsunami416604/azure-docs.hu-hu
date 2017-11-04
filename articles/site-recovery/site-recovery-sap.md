---
title: "A többrétegű SAP NetWeaver alkalmazástelepítést Azure Site Recovery segítségével védeni |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan védi az Azure Site Recovery segítségével SAP NetWeaver alkalmazástelepítések"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: manayar
ms.openlocfilehash: 951980eeba61e53c983d5b23c301c81eee9528bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>A többrétegű SAP NetWeaver alkalmazástelepítést Azure Site Recovery segítségével védelme

A legtöbb nagy- és közepes méretű SAP esetében van valamilyen vész-helyreállítási megoldás.  Hatékony és testable vész-helyreállítási megoldások fontosságát növekedett, további alapfolyamatok üzleti alkalmazások, mint az SAP kerülnek.  Az Azure Site Recovery már tesztelt és részét képező SAP-alkalmazásokból, és meghaladja a legtöbb helyszíni vész-helyreállítási megoldást, egy alacsonyabb tulajdonosi költségek (TCO) mint versengő megoldások képességeit.
Az Azure Site Recovery segítségével:
* Lehetővé teszi a helyszínen futó SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők az Azure-ba való replikálásával.
* Lehetővé teszi az Azure-t futtató SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők egy másik Azure-adatközpontba való replikálásával.
* Megkönnyíti a felhőre való áttelepítést, mivel az SAP üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.
* Leegyszerűsíti az SAP projektek frissítését, tesztelését és prototípuskészítését, mivel a segítségével egy igény szerinti éles klón hozható létre az SAP alkalmazások teszteléséhez.

Ez a cikk ismerteti, hogyan védheti SAP NetWeaver alkalmazás üzembe helyezése [Azure Site Recovery](site-recovery-overview.md). Ez a cikk ismerteti az ajánlott eljárásokat védelmére a háromrétegű SAP NetWeaver központi telepítés Azure által replikálhatók az Azure Site Recovery szolgáltatásban a támogatott forgatókönyveket és konfigurációk esetén használja egy másik Azure-adatközpontban, és hogyan hajthat végre feladatátvételt, is vizsgálati feladatátvételek (vészhelyreállítások részletezésének) vagy tényleges feladatátvételt.


## <a name="prerequisites"></a>Előfeltételek
Megkezdése előtt győződjön meg arról, hogy a következő:

1. [A virtuális gépek replikálása Azure-bA](azure-to-azure-walkthrough-enable-replication.md)
2. Hogyan [egy helyreállítási hálózathoz. terv](site-recovery-azure-to-azure-networking-guidance.md)
3. [A teszt feladatátvétel az Azure-bA](azure-to-azure-walkthrough-test-failover.md)
4. [A feladatátvétel az Azure-bA](site-recovery-failover.md)
5. Hogyan [tartományvezérlő replikálása](site-recovery-active-directory.md)
6. Hogyan [SQL-kiszolgáló replikálása](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Támogatott esetek
Az Azure Site Recovery Megvalósíthat egy vész-helyreállítási megoldást a következő esetekben:
* SAP rendszerekre egy Azure-adatközpontban esetében, amelyek egy másik Azure datacenter (Azure-Azure DR), a tervezett módon [Itt](https://aka.ms/asr-a2a-architecture).
* SAP rendszerekre VMWare (vagy fizikai) kiszolgálók helyszíni egy vész-Helyreállítási replikálása az Azure datacenter (VMware-Azure DR), amely kell néhány további összetevők, mivel a tervezett hely [Itt](https://aka.ms/asr-v2a-architecture).
* SAP rendszert futtató Hyper-V a helyszíni egy vész-Helyreállítási replikálása az Azure datacenter (DR Hyper-V-az-Azure-bA), amely kell néhány további összetevők, mivel a tervezett hely [Itt](https://aka.ms/asr-h2a-architecture).

Ez a dokumentum az első esetben - Azure-Azure DR - bemutatása az Azure Site Recovery SAP vész helyreállítási funkciók használja. Mivel Azure Site Recovery replikációs alkalmazás független, a következő témakörben ismertetett folyamat ahhoz, hogy az egyéb forgatókönyvek, valamint várt.

### <a name="required-foundation-services"></a>Szükséges foundation szolgáltatások
Összes dokumentáció példánkban lett telepítve a következő foundation szolgáltatásokkal telepített:
* Expressroute-on vagy a pont-pont virtuális magánhálózat (VPN)
* Azure-beli legalább egy Active Directory tartományvezérlő és DNS-kiszolgáló

Javasoljuk, hogy létrejött-e a fenti infrastruktúra Azure Site Recovery üzembe helyezése előtt.


## <a name="typical-sap-application-deployment"></a>Tipikus SAP-alkalmazás telepítése
Nagy számú SAP-ügyfelek általában telepíteni 6 – 20 egyedi SAP alkalmazások között.  Ezek az alkalmazások többsége a SAP NetWeaver ABAP vagy Java motorok alapulnak.  A core NetWeaver alkalmazásokat támogató a rendszer sok kisebb adott, nem NetWeaver SAP önálló motorok és általában valamilyen nem SAP-alkalmazásokból.  

Nagyon fontos a készlet összes az SAP-alkalmazásokból fut, a fekvő annak meghatározásához, a telepítési módban (rétegének 2 vagy 3 szintű), a verziók, a javítások, méretét, kavarog sebességét, és a lemez adatmegőrzési követelmények.

![Telepítési minta](./media/site-recovery-sap/sap-typical-deployment.png)

Az SAP-adatbázisokhoz adatmegőrző réteget a natív adatbázis-kezelő eszközök, például az SQL Server AlwaysOn, Oracle DataGuard vagy HANA replikációs keresztül kell védeni. Az ügyfél réteg van is nem Azure Site Recovery által védett, de fontos figyelembe venni a témakörök, amelyek hatással lehet az ebben a rétegben, mint például a DNS-propagálás késleltetés, a biztonság és a távelérést a vész-Helyreállítási datacenter.

Az Azure Site Recovery-e az alkalmazási rétegre, többek között (A) SCS az ajánlott megoldás. Más alkalmazások, például NetWeaver SAP nem SAP alkalmazásokat és az általános SAP-környezet részét képezik, és meg is kell védeni az Azure Site Recovery szolgáltatással.

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása
Hajtsa végre a [Ez az útmutató](azure-to-azure-walkthrough-enable-replication.md) a vész-Helyreállítási Azure adatközpontba SAP alkalmazás virtuális gépeire replikálást indítani.

Ha statikus IP-címet használ, az IP-cím azt szeretné, hogy a virtuális gép számítási és hálózati beállításainak a hálózati illesztő kártyák szakaszában érvénybe is megadhat.

![Cél IP](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása
A helyreállítási terv lehetővé teszi, hogy a különböző rétegek egy többrétegű alkalmazást, ezért az alkalmazás konzisztencia fenntartása a feladatátvételi sorrendje. Ismertetett lépéseket követve [Itt](site-recovery-create-recovery-plans.md) többrétegű webalkalmazás esetén a helyreállítási terv létrehozása során.

### <a name="adding-scripts-to-the-recovery-plan"></a>A helyreállítási terv parancsprogramokat hozzáadása
Szükség lehet az Azure virtuális gépek feladás egy vagy több feladatátvételi és tesztelési célú feladatátvétel megfelelő működéséhez az alkalmazások bizonyos műveleteket. Automatizálható a feladás egy vagy több feladatátvételi művelet, például a DNS-bejegyzés frissítéséhez, és adja hozzá a megfelelő parancsfájlokat a helyreállítási tervben a kötések és kapcsolatok, módosítása [Ez a cikk](site-recovery-create-recovery-plans.md#add-scripts).

### <a name="dns-update"></a>DNS-frissítési
Ha a DNS-ben van konfigurálva a DNS dinamikus frissítést, majd a virtuális gépek általában után elindítja az új IP-cím frissítse a DNS-ben. Ha hozzá szeretne adni egy explicit DNS frissíteni a virtuális gépet az új IP-címet, majd adja hozzá ezt a lépést [parancsprogramot a DNS-ben IP frissítéséhez](https://aka.ms/asr-dns-update) a post műveletek a helyreállítási terv csoportok.  

## <a name="example-azure-to-azure-deployment"></a>Azure-Azure központi telepítésre példát
Az ábrán alatt az Azure Site Recovery Azure-Azure vész-Helyreállítási forgatókönyv írja le:
* Az elsődleges adatközpont szingapúri (Azure Délkelet-Ázsia) és a vész-Helyreállítási datacenter Hongkong (Azure Kelet-Ázsia).  Ebben a forgatókönyvben helyi magas rendelkezésre állású azzal, hogy két virtuális gépeken futó SQL Server AlwaysOn szingapúri szinkron módban valósul meg.
* A fájl megosztási ASC segítségével magas rendelkezésre ÁLLÁSÚ biztosítanak az SAP egyetlen ponton felmerülő hibákat. Fájl megosztási ASC nincs szükség a fürt megosztott lemez és alkalmazásokhoz, mint a SIOS nem kötelező megadni.
* A DBMS réteg vész-Helyreállítási védelem aszinkron replikáció használatával érhető el.
* Ebből a forgatókönyvből megtudhatja "szimmetrikus DR" – egy olyan vész-Helyreállítási megoldás, amely az üzemi pontos replikájának használt kifejezés, ezért az SQL Server vész-Helyreállítási megoldás rendelkezik-e helyi magas rendelkezésre állású. Szimmetrikus vész-Helyreállítási használata nem kötelező, az adatbázis réteghez, és számos ügyfél használja ki, rugalmasan hozhat létre egy helyi magas rendelkezésre állási csomópontot gyorsan vész-Helyreállítási esemény után a felhőben történő alkalmazáshoz.
* Az ábra szemlélteti a SAP NetWeaver ASC és alkalmazás réteg replikálása az Azure Site Recovery által.

![Replikációs forgatókönyv](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>A teszt feladatátvétel
Hajtsa végre a [Ez az útmutató](azure-to-azure-walkthrough-test-failover.md) feladatátvételi teszt végrehajtásához.

1.  Nyissa meg az Azure-portálhoz, és válassza a Recovery Services-tároló.
2.  Kattintson a helyreállítási tervben készült SAP-alkalmazásokból (s).
3.  Kattintson a "Test Failover".
4.  Válassza ki a helyreállítási pont és a teszt feladatátvételi megkezdéséhez Azure virtuális hálózat.
5.  A másodlagos környezetben működik, ha az érvényesítést végezheti el.
6.  Ha az ellenőrzés befejeződött, kattintson a "Karbantartása a feladatátvételi teszt" és a feladatátvételi környezetet törlése.

## <a name="doing-a-failover"></a>A feladatátvétel
Hajtsa végre a [Ez az útmutató](site-recovery-failover.md) Ha feladatátvételt végez.

1.  Nyissa meg az Azure-portálhoz, és válassza a Recovery Services-tároló.
2.  Kattintson a helyreállítási tervben készült SAP alkalmazás(ok).
3.  Kattintson a "Failover".
4.  Válassza ki a helyreállítási pontot a feladatátvételi folyamat elindításához.

## <a name="next-steps"></a>Következő lépések
A vész-helyreállítási megoldást SAP NetWeaver üzembe helyezése az Azure Site Recovery létrehozásával kapcsolatos további [e tanulmány](http://aka.ms/asr-sap). A tanulmány is ismerteti, amelyek különböző SAP-architektúra javaslatok, támogatott alkalmazások és a Virtuálisgép-típusokon az SAP, az Azure-on, és ismerteti lehetséges tesztelési terveket, a vész-helyreállítási megoldás.

További információ [replikál a többi munkaterhelését](site-recovery-workload.md) Site Recovery segítségével.
