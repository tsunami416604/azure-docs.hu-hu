---
title: SAP HANA rendelkezésre állása az Azure-régiók |} A Microsoft Docs
description: Amikor az SAP HANA futtatásához az Azure-beli virtuális gépek több Azure-régióban rendelkezésre állási szempontok áttekintése.
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
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95ada2cb146bdbc972afee883a1d174c95aa67d7
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297582"
---
# <a name="sap-hana-availability-across-azure-regions"></a>SAP HANA rendelkezésre állása az Azure-régiók

Ez a cikk ismerteti a különböző Azure-régióban a SAP HANA rendelkezésre állása kapcsolódó forgatókönyveket. Az Azure-régiók közötti távolság, mert az SAP HANA rendelkezésre állási beállítása több Azure-régióban különleges szempontok foglalja magában.

## <a name="why-deploy-across-multiple-azure-regions"></a>Miért érdemes üzembe helyezni a több Azure-régiók között

Az Azure-régiók gyakran elválasztott nagy távolságra. Az egyes geopolitikai régiókban függően Azure-régiók közötti távolság lehet több száz mérföld, vagy akár több ezer mérföldre, például az Egyesült Államokban. Távolság, mert a két különböző Azure-régióban üzembe helyezett eszközök közötti hálózati forgalom jelentős hálózati körbejárási késés tapasztalható. A késés elég jelentős ahhoz a tipikus SAP számítási feladatok két SAP HANA-példányok között szinkron adatcsere kizárása. 

Másrészről szervezetek gyakran távolság követelmény között van az elsődleges adatközpont helyét, és a egy másodlagos adatközpontba. Távolságskála követelmény segít a rendelkezésre állást biztosító, egy szélesebb körű földrajzi helyen természeti katasztrófa esetére. Ilyenek például a hurrikánok, nyomja le a Karib-szigetek, és a Floridai szeptember és 2017. október. A szervezeténél esetleg legalább egy távolság minimális követelménynek. A legtöbb Azure-ügyfelek, a legkisebb távolságot definici igényel tervezheti meg a rendelkezésre állása [Azure-régiók](https://azure.microsoft.com/regions/). Mivel a két Azure-régiók közötti távolság túl nagy a HANA szinkron replikáció mód használatára, RTO és RPO válthatja, üzembe helyezése több régióban rendelkezésre állású konfigurációkhoz, és a egy második további központi telepítéseket az majd kiegészítése régió.

Érdemes figyelembe venni ebben a forgatókönyvben egy másik szempont feladatátvételi és ügyfelet átirányítani. Feltételezzük, hogy két különböző Azure-régiókban always az SAP HANA-példányok közötti feladatátvétel manuális feladatátvételt. Az SAP HANA-rendszerreplikálást replikációs mód értéke aszinkron, mert nincs egy lehetséges, hogy az elsődleges HANA-példány látható adatok nem még végzett, a másodlagos HANA-példány. Ezért az Automatikus feladatátvétel nincs lehetőség a konfigurációk ahol a replikáció az aszinkron. Akár manuálisan szabályozott feladatátvételt, mint egy feladatátvételt a gyakorlatban a kell intézkedéseket annak biztosítása érdekében, hogy az véglegesített adatokat az elsődleges oldalán, a másodlagos példányra előtt saját kezűleg helyezhet át a többi Azure-régióban.
 
Azure virtuális hálózat egy másik IP-címtartományt használja. Az IP-címek vannak telepítve, a második Azure-régióban. Tehát, vagy módosítani szeretné az SAP HANA-ügyfél konfigurációját, vagy lehetőleg kell létrehoznia a névfeloldás módosításához szükséges lépések. Ezzel a módszerrel az ügyfelek megnyílik az új másodlagos hely kiszolgálójának IP-címét. További információkért tekintse meg az SAP cikket [ügyfél kapcsolat helyreállítása után átvétel](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Egyszerű rendelkezésre állási két Azure-régiók között

Előfordulhat, hogy nem kíván egy adott régión belül minden rendelkezésre állási konfiguráció bevezetni, de továbbra is fennáll az igény, hogy ha a számítási feladatokhoz, szolgálja ki, ha katasztrófa történik. Ilyen esetekben szokásos esetben azok éles rendszerek. Bár a rendszer le fele egy nap vagy akár egy napot, fenntartható, nem engedélyezi a rendszer 48 óra vagy több nem érhető el. Ahhoz, hogy a telepítő kevésbé költséges, még akkor is, kevésbé fontos a virtuális gépen egy másik rendszer fut. A másik rendszerbe egy cél működik. A kisebb legyen a másodlagos régió virtuális gép méretezéséhez, és nem szeretné előre betöltheti az adatokat is. A feladatátvétel manuális, és számos további lépés szükséges a teljes alkalmazás verem feladatátvételt jár, mivel a virtuális gép leállítására, méretezze át, és indítsa újra a virtuális gép további időt fogadható el.

Ha a forgatókönyvben a DR cél megosztási egy virtuális gép QA rendszerrel használja, kell ezeket a szempontokat figyelembe venni:

- Kettő [üzemmódokat](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) delta_datashipping és logreplay, amelyek állnak rendelkezésre ilyen forgatókönyv esetén
- Mindkét művelet üzemmódnak megvannak a különböző memóriakövetelményei konfigurálások adatok nélkül
- Delta_datashipping preload beállítás nélküli jelentősen kevesebb memóriát, mint logreplay szükségük lehet szükség. Tekintse meg az SAP-dokumentum 4.3 fejezet [hogyan, hajtsa végre rendszer replikációs az SAP Hana-hoz](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Logreplay üzemmódja nélkül preload memóriakövetelménynek nem determinisztikus, és függ, hogy a betöltött oszlopcentrikus struktúrák. A szélsőséges esetben szükség lehet a memória az elsődleges példány 50 %-át. A memória a logreplay üzemmódja nem e úgy döntött, hogy az adatok előre betöltött vagy nem rendelkezik a függ.


![Két virtuális gép két régióban keresztül ábrája](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Ebben a konfigurációban nem adja meg az RPO = 0, mert a HANA system replikációs mód aszinkron. Ha meg kell adnia egy helyreállítási Időkorlát = 0, ez a konfiguráció nem választott konfigurációját.

Lehet, hogy módosítsa, hogy a konfigurációs módosítások is konfigurálhat egy adatok betöltését. Azonban természeténél a manuális feladatátvétel és a tényt, hogy az alkalmazási rétegekben is át kell helyezni a második régiót, akkor előfordulhat, hogy nincs értelme adatok betöltésére. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Rendelkezésre állási egy régión belül és régiók közötti összevonás 

Rendelkezésre állási belül és régiók közötti kombinációját előfordulhat, hogy az vezérlik, hogy ezek a tényezők:

- Helyreállítási Időkorlát követelményt = 0 Azure régiókon belül.
- A szervezet nem hajlandó vagy képes globális műveleteket, egy fő természeti katasztrófa, amely hatással van a nagyobb régióra hatással van. Ez az eset a bizonyos hurrikánok, amelyek az elmúlt néhány évben a Karib-szigetek találati esetében.
- Olyan szabályzat, amely igény szerint, amelyek egyértelműen túli milyen az Azure rendelkezésre állási zónák tud biztosítani az elsődleges és másodlagos helyek közötti távolság.

Ezekben az esetekben, milyen SAP hívások beállíthat egy [SAP HANA többrétegű rendszer replikációs konfiguráció](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) HANA rendszerreplikáció használatával. Az architektúra hasonlóan néz ki:

![Három virtuális gép két régióban keresztül ábrája](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Az SAP bevezetett [több cél replikációs](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) a HANA 2.0 SPS3. Több célrendszer replikáció előnyei a frissítési forgatókönyvek során. A DR-hely (2 régió) például, ha a magas rendelkezésre ÁLLÁSÚ másodlagos hely nem működik a karbantartás vagy frissítés nem változik. További információk a HANA több cél replikációs annak [Itt](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Lehetséges architektúra használatával több cél replikációs hasonlóan néz ki:

![Három virtuális gép két régióban milti célzott keresztül ábrája](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Ha a szervezete követelményei magas rendelkezésre állású készültségi a second(DR) az Azure-régióban, majd az architektúra jelenne meg:

![Három virtuális gép két régióban milti célzott keresztül ábrája](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Üzemmódja logreplay használ, ez a konfiguráció biztosítja a helyreállítási Időkorlátot = 0, az alacsony RTO, az elsődleges régióban. A konfiguráció finomat RPO is biztosít, ha egy második régióban helyezze át van szó. Az RTO időpontokat, a második régiót adatokat előre feltölti e függenek. Sok ügyfél a virtuális gép használja a másodlagos régióban egy tesztgépen futtatásához. Az adott használati eset, nem kell előre feltölti az adatokat.

> [!IMPORTANT]
> A művelet módok a különböző szintek között kell lennie a homogén. Ön **nem** működési mód közötti 1. rétegbeli és a 2. rétegbeli és delta_datashipping adja meg a 3 rétegbeli logreply használja. Csak az egyik vagy a más művelet módot kell lennie minden szint esetében egységes választhat. Mivel delta_datashipping nem megfelelő, hogy az RPO = 0, a művelet csak ésszerű módot az ilyen többrétegű konfiguráció logreplay marad. Működési módok és bizonyos korlátozásokkal kapcsolatban tekintse meg az SAP cikket [működési mód az SAP HANA-rendszerreplikálást](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>További lépések

Ezek a konfigurációk, az Azure-ban való beállításának részletes útmutatásért lásd:

- [Azure virtuális gépeken futó SAP HANA rendszerreplikáció beállítása](sap-hana-high-availability.md)
- [Magas rendelkezésre állás az SAP Hana rendszerreplikáció használatával](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
