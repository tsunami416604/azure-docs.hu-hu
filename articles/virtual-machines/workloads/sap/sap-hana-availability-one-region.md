---
title: SAP HANA rendelkezésre állási belül egy Azure-régiót |} Microsoft Docs
description: Azure virtuális gépeken natív az Azure-régió, egy SAP HANA műveleteket ismerteti.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b397d03cbb8fc539eb5f79e183233515bf173a99
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA rendelkezésre állási egy Azure-régión belül
A cikk több rendelkezésre állási forgatókönyvek belül egy Azure-régiót. Azure rendelkezik sok terület világszerte terjednek. Azure-régiók listáját lásd: [Azure-régiók](https://azure.microsoft.com/regions/). Üzembe helyezéséhez SAP HANA virtuális gépeken belül egy Azure-régió, a Microsoft egy egyetlen virtuális gépet egy HANA példányt biztosít. Két HANA osztályt belül két virtuális gépek telepítése a megnövelt rendelkezésre állás érdekében egy [Azure rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) HANA replikációs, amely használja a rendelkezésre állás érdekében. 

Jelenleg az Azure nyilvános előzetes verziója van ajánlat [(előzetes verzió) Azure rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). Ez a cikk részletesen rendelkezésre állási zónák nem esik. De a rendelkezésre állási zónák és rendelkezésre állási csoportokkal kapcsolatos általános vitafórum magában foglalja.

Mi a különbség a rendelkezésre állási csoport és egy rendelkezésre állási zóna között az Azure-ban? Ahol a rendelkezésre állási zónák felkínált Azure-régiók több adatközpontot rendelkezik. Az adatközpontokban a ellátás áramforrásról hűtési és hálózati függetlenek egymástól. Az ajánlat egyetlen Azure régión belül különböző zónákhoz oka, hogy alkalmazások között két vagy három rendelkezésre állási zónákhoz érhető el. Feltételezve, hogy a forrás- vagy hálózati hibák csak egy rendelkezésre állási zóna infrastruktúra hatással lenne, az alkalmazások központi telepítésének egy Azure-régiót belül még mindig teljesen működőképes, ha a rendelkezésre állási zónák használatára. Néhány korlátozott kapacitás fordulhatnak elő. Például lehet, hogy a virtuális gépek által egy zóna elveszett, de a két zónában virtuális gépek továbbra is működik, és lehet. 
 
Egy Azure rendelkezésre állási csoportok pedig a logikai csoportosításhoz képessége, amelyik segít biztosítani, hogy a VM erőforrás, amelyet a rendelkezésre állási csoport belül hiba-egymástól különítve az Azure adatközponton belül telepítésekor. Azure biztosítja, hogy a virtuális gépeket helyezi-e rendelkezésre állási belül több fizikai kiszolgálót, a számítási rackszekrények, a tárolási egység és a hálózati kapcsolók között futtatási beállítva. Az egyes Azure dokumentációja, ez a konfiguráció nevezzük különböző adatközpontokon [frissítés és a tartalék tartományok](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Ezek a gyakorlatok általában egy Azure-adatközpontban belül. Feltételezve, hogy a forrás- és hálózati hibák hatással lenne a datacenter központilag telepíteni, egy Azure-régió, a kapacitás befolyásolhat.

Adatközpontokban, amelyek megfelelnek az Azure rendelkezésre állási zónák elhelyezkedését a legtöbb alkalmazás és az Adatközpont között megadott távolságra elfogadható különböző zónákhoz telepített szolgáltatások közötti hálózati késés kézbesítéséhez közötti kompromisszumos megoldásnak. Természeti katasztrófa ideális így hatással vannak a power, hálózati adjon meg, és ebben a régióban zónákon rendelkezésre állásának infrastruktúra. Azonban, emlékművi természeti katasztrófa kimutatták, rendelkezésre állási zónák előfordulhat, hogy nem mindig a rendelkezésre állást nyújtanak, amelyet egy régión belül. Gondolja át, amely a sziget Puerto Rico találati 2017. szeptember 20. a Hurrikán Maria. A Hurrikán alapvetően okozott egy majdnem 100 százalék blackout a 90 kiterjedő mérföldes sziget.

## <a name="single-vm-scenario"></a>Single-VM forgatókönyv

Single-Virtuálisgép-konfigurációban hozzon létre egy Azure virtuális gép SAP HANA-példány. Prémium szintű Azure Storage-az operációsrendszer-lemez és az adatlemezek segítségével. Az Azure hasznos üzemidő szolgáltatásiszint-szerződésben garantált 99,9 % és a szolgáltatásiszint-szerződések, az Azure-összetevőből is elegendő ahhoz, hogy az ügyfelek számára a rendelkezésre állási SLA-k teljesítéséhez. Ebben a forgatókönyvben, hogy nem szükséges, hogy kihasználja az Azure rendelkezésre állási készlet a DBMS réteget futtató virtuális gépeken. Ebben a forgatókönyvben meg két különböző szolgáltatásait használják:

- Az Azure virtuális gép automatikus újraindítás letiltása (más néven Azure szolgáltatásjavításnak)
- SAP HANA automatikus újraindítás letiltása

Az Azure virtuális gép automatikus újraindítása, vagy szolgáltatásjavításnak, az egyik funkciója, amely két szinten működik az Azure-ban:

- Az Azure-kiszolgáló gazdagép egy virtuális Gépet, amely a kiszolgáló állomás állapotát ellenőrzi.
- Az Azure fabric controller figyeli az ügyfélállapot és a kiszolgáló gazdagép rendelkezésre állását.

A health ellenőrzése funkció minden virtuális gép, amely egy Azure-kiszolgálói gazdagépet állapotát figyeli. Ha egy virtuális gép nonhealthy állapotba esik, a virtuális gép újraindítását kezdeményezheti az Azure-gazdagép-ügynök, amely a virtuális gép állapotát ellenőrzi. A fabric controller sok más paraméterekkel, amelyek jelezhetik a gazdagép kapcsolatos hibákat ellenőrzésével a gazdagép állapotát ellenőrzi. Azt is ellenőrzi a kisegítő az állomás a hálózaton keresztül. Utalhat, hogy a gazdagép problémákat okozhat a következő események:

- Ha a gazdagép hibás állapotot, a gazdagép újraindítását és a újraindítása a gazdagépen futó virtuális gépek jelzi.
- Ha a gazdagép nem megfelelő állapotban a rendszer újraindítása, a gazdagép újraindítását és a virtuális gépek, amelyek eredetileg működtek megfelelő gazdagép a gazdagép újraindítása után. Ebben az esetben az állomás van megjelölve sem működik megfelelően. Ez nem használható további központi telepítéseket törölték, vagy lecserélni.
- Ha a nem kifogástalan állapotú gazdagép problémák vannak, a rendszer újraindítása közben, azonnali indítsa újra a virtuális gépek a megfelelő gazdagép. 

A gazdagép és virtuális gép az Azure által biztosított figyelés, az Azure virtuális gépeken, hogy a gazdagéppel kapcsolatos problémákat automatikusan újraindulnak a megfelelő Azure-gazdagépre. 

A második szolgáltatása, amely ebben a forgatókönyvben szereplő támaszkodnak, az, hogy a HANA szolgáltatás újraindított virtuális gépen fut automatikusan elinduló után a virtuális gép újraindul. Beállíthat [HANA szolgáltatás automatikus újraindítás letiltása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) a figyelő szolgáltatások különböző HANA szolgáltatások segítségével.

A single-VM forgatókönyv továbbfejlesztésében előfordulhat, hogy cold feladatátvételi csomópontot ad hozzá egy SAP HANA-konfigurációját. Az SAP HANA-dokumentációban, a telepítő nevezik [automatikus feladatátvételt gazdagép](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Ebben a konfigurációban előfordulhat, hogy célszerű egy helyszíni telepítési helyzetben, ahol a Kiszolgálóhardver korlátozott, és a gazdagép egy egyetlen kiszolgálós csomópont mozgósíthatnak, éles állomásoknak csomópont automatikus feladatátvételt. De Azure, ahol az alkalmazás mögötti infrastruktúra az Azure biztosít a megfelelő célkiszolgáló sikeres VM újraindítás, azt nem célszerű a központi telepítése az SAP HANA állomás automatikus feladatátvételt. Ebből kifolyólag nem referencia-architektúrában, amely úgy rendelkezik egy készenléti csomópont HANA állomás automatikus feladatátvételt kell. Ez a SAP HANA kibővített konfigurációk is vonatkozik.

## <a name="availability-scenarios-for-two-different-vms"></a>Két különböző virtuális gépek rendelkezésre állási forgatókönyvek

Ha két Azure virtuális gépeken belül egy Azure rendelkezésre állási csoportot használja, később növelheti a hasznos üzemidő között két virtuális gépeken, ha az Azure rendelkezésre állási készlet belül egy Azure-régióban helyezi. A kiinduló beállítása az Azure-ban néz ki:

![Két virtuális gépek rétegeket ábrája](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

A másik rendelkezésre állási forgatókönyvek mutatja be, az ábrán a rétegek egyes hiányoznak. Az ábrán csak olyan rétegek, amelyek jelzik a virtuális gépek, a gazdagépek, a rendelkezésre állási csoportok és az Azure-régiók látható. Azure virtuális hálózat példányok, erőforráscsoport-sablonok és előfizetések nem szerepet játszanak az ebben a szakaszban leírt forgatókönyvek.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>A második virtuális gép párhuzamos biztonsági mentések

A legtöbb kezdetleges beállításokat egyik biztonsági mentés használandó. Különösen lehetséges, hogy tranzakciónaplók biztonsági mentése egy másik Azure virtuális gép egy virtuális géptől kiadva. Kiválaszthatja, hogy az Azure Storage típusát. Ez a beállítás Ön felelősséggel tartozik scripting végzik az első virtuális gépen, a második virtuális gép ütemezett biztonsági mentések példányát. Ha meg szeretné használni, a második Virtuálisgép-példányok, vissza kell állítania a teljes/növekményes különbségi és tranzakciónapló biztonsági mentései a pont, amelyekre szüksége van. 

Az architektúra így néz ki:

![Két virtuális gépek tárolóreplikálást ábrája](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Ez a beállítás nincs kiválóan alkalmas a kiváló helyreállítási-célkitűzés (RPO) és a helyreállítási idő célkitűzés (RTO) időpont eléréséhez. RTO alkalommal különösen tartoznának csökkenni fog teljesen a teljes adatbázis visszaállítása a másolt biztonsági mentések használatával kell miatt. Azonban ez a beállítás akkor hasznos, esetén a helyreállítás a fő példányokon nem kívánt adatok törlése. Ezzel a beállítással tetszőleges időpontban visszaállítása egy bizonyos mértékig időpontra, kinyeri az adatokat, és a törölt adatok importálása a fő példány. Ezért célszerű a módszerhez a biztonsági másolat együtt más magas rendelkezésre állású funkciójú. 

Biztonsági mentések bemásolásához, amíg lehet, hogy az SAP HANA-példány fut a fő VM-nál kisebb virtuális Gépet használ. Ne feledje, hogy csatolhat egy kisebb számot a VHD-k kisebb virtuális gépeket. További információ az egyes Virtuálisgép-típusokon korlátairól: [Azure Linux virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>SAP HANA replikációs automatikus feladatátvétel nélkül

Az ebben a szakaszban leírt forgatókönyvek SAP HANA replikációs használja. Az SAP dokumentációjáért lásd: [replikációs](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Egy Azure-régió két Azure virtuális gépeken futó rendelkezik különböző konfigurációt, ezért néhány RTO különbségek vannak. Automatikus feladatátvétel nélkül forgatókönyvek általában nem feltétlenül vonatkozik kifejezetten az Azure-régió, egy virtuális gépe. Ennek az az oka az Azure szolgáltatásjavításnak többsége az Azure-infrastruktúra hibáinak, az elsődleges virtuális gép egy másik gazdagépen újraindul. Nincsenek bizonyos peremhálózati esetekben, ahol ez a konfiguráció segíthet hibát jelentő forgatókönyvek tekintetében. Vagy, néhány esetben az ügyfél több hatékonyságát megvalósításához.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA replikációs automatikus feladatátvétel és előzetes betöltési adatok nélkül

Ebben a forgatókönyvben a SAP HANA replikációs áthelyezni az adatokat az RPO 0 eléréséhez szinkron módon használhatja. Másrészt a elég hosszú RTO, feladatátvétel vagy -adatok a HANA példány gyorsítótárba kerüli nincs szükségük van. Ebben az esetben is lehet elérése érdekében további a konfiguráció a következő műveletek végrehajtásával:

- A második virtuális gép egy másik SAP HANA-példány fut. Az SAP HANA-példány a második virtuális gép a virtuális gép memóriájának nagy vesz igénybe. Általában ez jelenti, abban az esetben, ha a második virtuális gép a feladatátvételt hajt végre. A második virtuális gép leállíthat, hogy a replikált adatok tölthetők be a második virtuális gép célzott HANA-példány a gyorsítótárba.
- Kisebb Virtuálisgép-méretet használata a második virtuális gépeken. A feladatátvétel esetén egy további lépéseket, mielőtt a kézi feladatátvételre van. Ebben a lépésben méretezze át a virtuális Gépet, a forrás virtuális gép méretét. A forgatókönyv így néz ki:

![Két virtuális gépek tárolóreplikálást ábrája](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Akkor is, ha a HANA rendszer replikációs cél nem használunk adatok preload, legalább 64 GB memóriát kell. Elegendő memória a sortárindex adatok megtartja az a cél-példány memóriáját 64 GB-os mellett is kell.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA replikációs nélkül Automatikus feladatátvétel és előzetes betöltési adatok

Ebben a forgatókönyvben előre feltölti a HANA-példányra, a második virtuális gép replikált adatokat. Ezzel a megoldással az adatok nem kerüli két előnyeit. Ebben az esetben a második virtuális gép egy másik SAP HANA-rendszeren nem futtatható. Még nem használhatja egy kisebb Virtuálisgép-méretet. Ezért az ügyfelek ritkán a forgatókönyv megvalósításához.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>SAP HANA replikációs automatikus feladatátvétellel

A standard és a leggyakrabban használt belül egy Azure-régió, két Azure virtuális gépeken futó SLES Linux rendelkezésre állási konfigurációban kell definiált feladatátvevő fürt. A SLES Linux fürt alapul a [támasztja](http://www.linux-ha.org/wiki/Pacemaker) keretrendszer együtt egy [STONITH](http://linux-ha.org/wiki/STONITH) eszköz. 

Egy SAP HANA szempontjából a használt replikációs mód van-e szinkronizálva, és az automatikus feladatátvételre van konfigurálva. A második virtuális Gépet a SAP HANA-példány meleg készenléti csomópontként működik. A készenléti csomópont kap az elsődleges SAP HANA-példány módosítása rekordok szinkron adatfolyam. Tranzakciók véglegesítve lett az alkalmazás a HANA elsődleges csomóponton, az elsődleges HANA csomópont vár, az alkalmazásnak a véglegesítés megerősítéséhez, amíg az másodlagos SAP HANA-csomópont megerősíti, hogy kapott-e a végrehajtási rekord. SAP HANA két szinkron replikáció módot kínál. Részletekért és e két szinkron replikáció mód közötti különbségek ismertetése, olvassa el a SAP [SAP HANA replikációs replikációs módjainak](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

A teljes konfiguráció így néz ki:

![Két virtuális gépek tárolási replikációs és feladatátvételi ábrája](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Ez a megoldás megoldás választása javasolt, mivel lehetővé teszi az RPO eléréséhez = 0 és egy rendkívül alacsony RTO. Konfigurálja a SAP HANA ügyfélkapcsolat, hogy az SAP HANA-ügyfelek csatlakozhatnak a virtuális IP-címet a HANA rendszer replikációs konfiguráció. Így nem kell konfigurálnia az alkalmazást, ha a másodlagos csomópont átveszi egy másik. Ebben a forgatókönyvben az elsődleges és másodlagos virtuális gépek Azure VM Termékváltozatait kell tartozniuk.

## <a name="next-steps"></a>További lépések

Ezeket a konfigurációkat, az Azure-ban való beállításának lépésenkénti útmutatásért lásd:

- [Az Azure virtuális gépeken SAP HANA-rendszer replikáció beállítása](sap-hana-high-availability.md)
- [Magas rendelkezésre állású SAP Hana replikációs használatával](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Azure-régiók SAP HANA elérhetőségét kapcsolatos további információkért lásd:

- [Azure-régiók közötti SAP HANA-elérhetősége](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

