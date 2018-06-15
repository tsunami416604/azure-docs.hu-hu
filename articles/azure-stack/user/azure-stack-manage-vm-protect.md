---
title: Azure-veremben telepített virtuális gépek védelme |} Microsoft Docs
description: Irányelvek Azure verem telepített virtuális gépek védelme.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/14/2018
ms.author: jeffgilb
ms.reviewer: hector.linares
ms.openlocfilehash: 734ee0e6ffb0dab660a2b63b431780208e0e0484
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165264"
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Azure verem telepített virtuális gépek védelme

Ez a cikk használja a virtuális gépek (VM), hogy a felhasználók Azure veremben védelméhez terv kidolgozása útmutatójaként.

Az adatvesztéssel és a nem tervezett leállás elleni kell megvalósítani a felhasználói alkalmazások és az adatok biztonsági mentés-helyreállítás vagy vész-helyreállítási terv. A terv előfordulhat, hogy minden alkalmazáshoz egyedi, de a szervezete átfogó az üzletmenet folytonossága és vészhelyreállítás BC vagy-helyreállítási stratégia által meghatározott keretek követi. A jó kiindulási pont [rugalmas alkalmazások az Azure-tervezése](https://docs.microsoft.com/azure/architecture/resiliency), általános minták és gyakorlatok biztosító alkalmazások rendelkezésre állásáról és rugalmasság.

>[!IMPORTANT]
> A biztonsági mentés-helyreállítás, valamint a vész-helyreállítási tervek folyamatos tesztelése. Ez annak érdekében, hogy kell:
> * A csomagok működik
> * A csomagok továbbra is igényeihez számára tervezték őket.

## <a name="azure-stack-infrastructure-recovery"></a>Az Azure verem infrastruktúra recovery

Felhasználók felelőssége külön védi a virtuális gépek Azure verem infrastruktúra-szolgáltatásokat.

A helyreállítási terv verem Azure infrastruktúra-szolgáltatásokat a **nem** közé tartozik a felhasználói virtuális gépeket, storage-fiókok vagy adatbázisok helyreállítását. Az alkalmazás tulajdonosa Ön felelősséggel tartozik az alkalmazások és adatok helyreállítási terv megvalósításához.

Ha hosszabb ideig offline állapotban az Azure-verem felhő vagy véglegesen helyreállíthatatlan kell rendelkeznie a helyreállítási terv elhelyezni, amelyek:

* Biztosítja a minimális állásidő érdekében
* Tartja a kritikus fontosságú virtuális gépek, például adatbázis-kiszolgálók fut
* Lehetővé teszi az alkalmazások a felhasználói kérelmek tartsa karbantartásába

Az Azure-verem felhő üzemeltetője a helyreállítási terv az alapul szolgáló Azure verem infrastruktúra és a szolgáltatások létrehozásáért felelős. További tudnivalókért olvassa el a cikk [végzetes adatvesztés helyreállíthatók](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data).

## <a name="sourcetarget-combinations"></a>Forrás/cél kombinációk

Minden Azure verem felhő telepítve van egy adatközpont. Egy külön környezetre szükség, így helyreállíthatja az alkalmazások. A helyreállítási környezet lehet egy másik Azure verem felhő ugyanabban az adatközpontban vagy az Azure nyilvános felhőjében. Az adatok közös joghatóság alá és az adatvédelmi követelmények határozza meg a helyreállítási környezet az alkalmazáshoz. Engedélyezi az egyes alkalmazásokhoz tartozó védelmet, mert így rugalmasan kiválaszthatja egy adott helyreállítási lehetőséget a minden egyes. Alkalmazások adatainak biztonsági mentését egy másik adatközponthoz egy előfizetés lehet. Egy másik előfizetésben replikálja adatokat az Azure nyilvános felhőjében.

A biztonsági mentés-helyreállítás és vész-helyreállítási stratégia minden alkalmazás határozza meg a cél az egyes alkalmazásokhoz tervezéséhez. A helyreállítási terv segít a szervezet megfelelő méretezés a tárolási kapacitás szükséges a helyszíni és a nyilvános felhőben felhasználási projektre.

|  | Globális Azure | Az Azure vermet helyezett a CSP adatközpontjában, és a CSP által működtetett | Ügyfél adatközpontjába telepített, és az ügyfél által üzemeltetett Azure verem |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Az Azure vermet helyezett a CSP adatközpontjában, és a CSP által működtetett** | A működtetett CSP Azure verem felhasználói virtuális gépeket telepít. Felhasználói virtuális gépeket biztonsági másolatból történt visszaállítása vagy közvetlenül az Azure-bA a feladatátvételt. | Kriptográfiai Szolgáltató működik az elsődleges és másodlagos példányai Azure verem saját adatközpontokban. Felhasználói virtuális gépeket állították helyre, vagy átadja a feladatokat a két Azure verem példányai között. | CSP-hez tartozó Azure, az elsődleges helyen működik. Az ügyfél datacenter célja restore vagy a feladatátvétel. |
| **Ügyfél adatközpontjába telepített, és az ügyfél által üzemeltetett Azure verem** | Felhasználói virtuális gépeket a rendszer telepíti az ügyfél üzemeltetett Azure verem. Felhasználói virtuális gépeket biztonsági másolatból történt visszaállítása vagy közvetlenül az Azure-bA a feladatátvételt. | Ügyfél működik az elsődleges és másodlagos példányai Azure verem saját adatközpontokban. Felhasználói virtuális gépeket állították helyre, vagy átadja a feladatokat a két Azure verem példányai között. | Ügyfél tartozó Azure, az elsődleges helyen működik. A CSP adatközpontjában célja restore vagy a feladatátvétel. |

![Forrás-cél kombinációja](media\azure-stack-manage-vm-backup\vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Alkalmazás helyreállítási célok

Szüksége lesz a szervezet minden alkalmazáshoz tűri állásidő és adatvesztést meghatározni. Mennyiségi meghatározására állásidő és adatvesztést által, hogy minimalizálja a vállalata egy olyan vészhelyzet esetén a helyreállítási tervet is létrehozhat. Az egyes alkalmazásokhoz vegye figyelembe:

 - **A helyreállítási idő célkitűzése (RTO)**  
RTO az esemény után az alkalmazás nem érhető el lehet maximális elfogadható idő. Egy RTO 90 percig például azt jelenti, hogy kell lennie az alkalmazás fut. 90 percen belül a kezdetektől egy olyan vészhelyzet esetén a visszaállítási lehetőségével. Ha alacsony RTO, előfordulhat, hogy megakadályozhatja a második központi telepítés folyamatosan futó készenléti regionális kimaradás elleni védelem érdekében.
 - **Helyreállítási időkorlát (RPO)**  
A helyreállítási Időkorlát adatvesztés elfogadható során egy olyan vészhelyzet esetén a maximális időtartama. Ha például egyetlen adatbázisban tárol adatokat, és nem készít replikát egy másik adatbázisba, és óránként végez biztonsági mentéseket, akkor legfeljebb egy órányi adatot veszíthet.

Az RTO-ra és az RPO-ra üzleti követelményként tekinthet. A kockázatbecslés adható meg az alkalmazás RTO és a helyreállítási Időkorlát elvégzésével.

A metrika egy másik **visszaállítás középidő** (MTTR), amelyek a meghibásodás után az alkalmazás visszaállítása szükséges átlagos idő. MTTR tapasztalati érték esetén a rendszer. Ha az MTTR nagyobb, mint az RTO, akkor a rendszer hibája az üzletmenet elfogadhatatlan mértékű szüneteltetését okozza, mert nem lehet visszaállítani a rendszert a megadott RTO-n belül.

### <a name="backup-restore"></a>Backup-restore

A Virtuálisgép-alapú alkalmazások leggyakoribb védelmi rendszere biztonsági mentési szoftver használatára. Az operációs rendszer, az operációs rendszer konfigurálása, bináris alkalmazásfájlokat és alkalmazásadatok biztonsági mentése a virtuális gépek általában tartalmazza. A biztonsági mentések hozza létre a köteteket, lemezek vagy a teljes virtuális gép pillanatkép létrehozása van folyamatban. Az Azure veremnek megfelelő a szeretne biztonsági másolatot készíteni a vendég operációs rendszer vagy az Azure-verem tárolásból összefüggésében rugalmasan, és számítási API-k. Az Azure verem nem támogatja a véve a biztonsági mentések a hipervizor szinten.
 
![Backup-restor](media\azure-stack-manage-vm-backup\vm_backupdataflow_03.png)

Az alkalmazás helyreállítása szükséges visszaállítása egy vagy több, azonos felhőben, vagy új felhő. A datacenter vagy a nyilvános felhő felhő célba. A választott felhő teljesen a vezérlőben, és az adatvédelmi és közös joghatóság alá követelmények alapján.
 
 - RTO: Az órában mért állásidő
 - A helyreállítási Időkorlát: Változó adatvesztés (attól függően, hogy biztonsági mentési gyakoriság)
 - Üzembe helyezési topológia: aktív/passzív

#### <a name="planning-your-backup-strategy"></a>A biztonsági mentési stratégia tervezése

A biztonsági mentési stratégia megtervezése és méretezési követelmények meghatározásánál tart kezdődik-e a szükséges védelemmel ellátni Virtuálisgép-példányok száma mennyiségi meghatározására. Minden virtuális gépek biztonsági mentéséről környezetben kiszolgálók közötti közös stratégiát is. Az Azure-vermet, van azonban néhány virtuális gépek biztonsági mentése szükséges. Például egy méretezési csoportban lévő virtuális gépek minősülnek elmúló erőforrásokat, amelyek származnak, majd lépjen, néha minden külön értesítés nélkül. Például egy adatbázis vagy az objektum-tároló egy külön tárház kell védeni tartós adatokat tárolja.

Virtuális gépek biztonsági mentéséről Azure veremben szempontokat:

 - **Kategorizálási**
    - Fontolja meg a modell, ahol felhasználók engedélyezve a virtuális gép biztonsági mentése.
    - Adja meg a helyreállítási szolgáltatásiszint-szerződés (SLA) az alkalmazások és az üzlet fontossága alapján.
 - **Méretezés**
    - Vegye figyelembe a lépcsőzetes biztonsági mentések, amikor előkészítésének számos új virtuális gépek (Ha a biztonsági mentés szükség).
    - Értékelje ki a biztonsági mentési termék, amely hatékonyan rögzítése és a megoldás a tartalom minimalizálása érdekében biztonsági mentési adatokat.
    - Értékelje ki a biztonságimásolat-készítő, amely hatékonyan tárolni a biztonsági mentési adatok növekményes vagy különbözeti biztonsági mentések használatával minimalizálása érdekében a teljes biztonsági mentés van szükség a környezet összes virtuális gép között.
 - **Visszaállítás**
    - Biztonságimásolat-készítő visszaállíthatja a virtuális lemezeket, egy meglévő virtuális Gépre, vagy a teljes VM erőforrás adatainak és a társított virtuális lemezekkel. A visszaállítási séma van szüksége, attól függ, hogyan szeretné visszaállítani az alkalmazást, és azt befolyásolja az alkalmazás helyreállításra. Például lehet könnyebben telepítse újra az SQL server olyan sablon alapján, és majd állítsa vissza az adatbázisok visszaállítása a teljes virtuális gép vagy a virtuális gépek halmazát helyett.

### <a name="replicationmanual-failover"></a>Replikációs/kézi feladatátvételt

Egy másik módjáról magas rendelkezésre állást támogató, hogy az alkalmazás virtuális gépek replikálása egy másik felhőhöz, és kézi feladatátvételre támaszkodnak. Az operációs rendszer, a bináris alkalmazásfájlokat és az alkalmazás adatainak a replikálását a virtuális gép vagy vendég operációs rendszer szintjén hajtható végre. A feladatátvételi kezeli, amely nem része az alkalmazás további szoftverrel.

Ezt a módszert használja az alkalmazás központi telepítése egy felhőben, és a virtuális gép replikálódik a többi felhőt. Ha akkor váltódik ki, a feladatátvétel, a másodlagos virtuális gépek kell a második felhőben kapcsolható be. Bizonyos esetekben a feladatátvételt a virtuális gépek és nevű lemezek számukra hoz létre. Ez a folyamat befejeződik, különösen a egy többrétegű alkalmazást, amely egy adott kezdeti feladatütemezési szükséges egy hosszú időt vehet igénybe. Is futnia kell az alkalmazás még nem megkezdheti a szolgáltatáskéréseket lépéseket lehet.

![Replikációs-kézi feladatátvételt](media\azure-stack-manage-vm-backup\vm_backupdataflow_02.png)

 - RTO: Percben mért állásidő
 - A helyreállítási Időkorlát: Változó adatvesztés (replikáció gyakoriságától függően)
 - Üzembe helyezési topológia: aktív/passzív készenléti által
 
### <a name="high-availabilityautomatic-failover"></a>Magas rendelkezésre állás vagy automatikus feladatátvétel

Alkalmazások esetében, ahol az üzleti tűri néhány másodperceken vagy perceken állásidő és minimális adatvesztéssel akkor fontolja meg egy magas rendelkezésre állású konfigurációban. Magas rendelkezésre állású alkalmazások úgy tervezték, hogy gyorsan és automatikusan helyreállni hibák. Helyi hardver hibák, az Azure verem infrastruktúra magas rendelkezésre állás a fizikai hálózat két legfontosabb állvány kapcsolók használatával valósítja meg. Számítási szintű hibák, az Azure verem használja több csomópont egy méretezési egység. A virtuális gép szinten használata méretezési csoportok együtt tartalék tartományok biztosításához csomóponthibák nem miatt le az alkalmazást.

Az alkalmazásnak méretezési csoportok együtt, szüksége lesz a natív módon támogatja a magas rendelkezésre állású vagy támogatja a fürtképző szoftverek használatát. Például a Microsoft SQL Server támogatja a magas rendelkezésre állású natív módon szinkron véglegesítésű módot használó adatbázisokról. Azonban ha csak is támogatja az aszinkron replikációját, majd lesz adatvesztést. Alkalmazások is telepíthetők a feladatátvevő fürtök, ahol a fürtszoftver kezeli-e az alkalmazás automatikus feladatátvétel be.

Ezzel a megközelítéssel az alkalmazás csak aktív egy felhőben, de a szoftver központi telepítése több felhők. A többi felhőből készenléti üzemmódban megkezdheti az alkalmazás, a feladatátvétel kezdeményezése esetén van.

 - RTO: Állásidő mérése másodpercben történik
 - A helyreállítási Időkorlát: Minimális adatvesztéssel
 - Üzembe helyezési topológia: aktív/aktív készenléti által

### <a name="fault-tolerance"></a>Hibatűrés

Fizikai redundanciát és infrastruktúra szolgáltatás rendelkezésre állása csak hardver elleni védelem érdekében az Azure verem hiba/hibák szinten, ilyen a lemezen, tápegység, hálózati portot vagy csomópont. Azonban ha az alkalmazás mindig elérhetőnek kell lennie, és soha nem el adatokat, akkor az alkalmazás natív módon alkalmazza a hibatűrést, vagy egy másik szoftver használatával a hibatűrés.

Először annak érdekében, hogy a virtuális gépek méretezési központilag telepített alkalmazás beállítja a csomópont-szintű hibák elleni védelem érdekében. Offline állapotra vált, a felhő elleni védelem érdekében, ugyanazt az alkalmazást kell már regisztrálva van olyan másik felhőt, így folytathatja a szolgáltatáskéréseket megszakítás nélkül. Ez a modell gyakran emlegetik úgy az aktív-aktív központi telepítése.

Ne feledje, hogy minden Azure verem felhő egymástól, független, a felhők mindig minősülnek aktív, az infrastruktúra szempontjából. Ebben az esetben az alkalmazás több aktív példányok telepít egy vagy több aktív felhőt.

 - RTO: Nincs leállás
 - A helyreállítási Időkorlát: Adatvesztés nélküli
 - Üzembe helyezési topológia: aktív/aktív

### <a name="no-recovery"></a>Egyetlen helyreállítási

Egyes alkalmazások a környezetben nem esetleg nem tervezett leállások vagy adatvesztés elleni védelem. Például fejlesztési használt virtuális gépek és tesztelési rendszerint nem kell helyre kell állítani. Eldöntheti, hogy egy alkalmazás vagy egy adott virtuális gép védelme nélkül az. Azure verem nem ajánlja fel a biztonsági mentési vagy a virtuális gépek replikálása az alapul szolgáló infrastruktúra. Hasonló az Azure-ba, szüksége lesz a részt az egyes virtuális gépek minden egyes előfizetésnél a védelem.

 - RTO: helyreállíthatatlan
 - A helyreállítási Időkorlát: Teljes adatvesztés

## <a name="recommended-topologies"></a>Ajánlott topológiák

Az Azure-verem üzembe helyezéshez szempontokat:

|     | Ajánlás | Hozzászólások |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Virtuális gépek biztonsági mentési/visszaállítási már telepítették az adatközpontban található egy külső biztonsági mentési cél | Ajánlott | A meglévő biztonsági mentési infrastruktúra és a működési képességek előnyeit. Ügyeljen arra, hogy a biztonsági mentési infrastruktúra méretezze, hogy készen álljon a további Virtuálisgép-példány védelmét. Ellenőrizze, hogy biztonsági mentési infrastruktúra nincs közel az adatforrásból. A forrás Azure verem, egy másodlagos Azure verem-példányt, vagy Azure virtuális gépek is helyreállíthatja. |
| Virtuális gépek biztonsági mentési/visszaállítási egy dedikált Azure verem külső biztonsági mentési cél | Ajánlott | Azure verem vásárolhat az új biztonsági mentési infrastruktúra vagy dedikált biztonsági mentési infrastruktúra kiépítéséhez. Ellenőrizze, hogy biztonsági mentési infrastruktúra nincs közel az adatforrásból. A forrás Azure verem, egy másodlagos Azure verem-példányt, vagy Azure virtuális gépek is helyreállíthatja. |
| Erre a globális Azure vagy egy megbízható szolgáltató virtuális gépek biztonsági mentése/visszaállítása | Ajánlott | Mindaddig, amíg az adatok védelmét és szabályozási követelmények is megfelel, tárolhatja a biztonsági másolatok globális Azure vagy egy megbízható szolgáltató. Ideális esetben a szolgáltató is fut Azure verem így konzisztencia működési élményt nyújt történő visszaállításához. |
| Replikálás/feladatátvételi virtuális gépek külön Azure veremben példányra | Ajánlott | A feladatátvételi esetben szüksége lesz egy teljesen működőképes második Azure verem felhő rendelkezik kiterjesztett alkalmazás állásidő elkerülése érdekében. |
| Replikálás/feladatátvételi virtuális gépek közvetlenül az Azure vagy egy megbízható szolgáltató | Ajánlott | Mindaddig, amíg az adatok védelmét és szabályozási követelmények is megfelel, replikálja az adatokat globális Azure vagy egy megbízható szolgáltató. Ideális esetben a szolgáltató is fut Azure verem így konzisztencia működési élményt nyújt a feladatátvételt követően. |
| Az alkalmazás adatait a azonos Azure verem felhő biztonsági mentési cél telepítése | Nem ajánlott | Ne tárolja a biztonsági mentések azonos Azure verem felhőben. A felhő nem tervezett leállás is csoportszolgáltatáshoz az elsődleges adatok és a biztonsági mentési adatokat. Ha egy biztonsági mentési cél telepítését a virtuális készülék (alkalmazásában optimalizálást biztonsági mentése és visszaállítása), győződjön meg minden adat folyamatosan külső biztonsági mentési helyre másolja. |
| Az ugyanazon az Azure-verem megoldást futtató állványra a fizikai biztonsági mentési készülék telepítése | Nem támogatott | Ezen a ponton az időben azokat az eszközöket nem lehet csatlakozni az állvány kapcsolók, amelyek nem részei az eredeti megoldásban tetején. |

## <a name="next-steps"></a>További lépések

Ez a cikk általános irányelveket előírt telepített Azure veremben felhasználói virtuális gépek védelmét. Azure-szolgáltatások felhasználói virtuális gépeket védelméhez használatával kapcsolatos információkért lásd:

 - [Azure Backup használatával biztonsági másolatot a fájlokhoz és alkalmazásokhoz Azure verem](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure verem az Azure Backup Server támogatása](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure verem támogatása az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/)  

A virtuális gépek védelmét Azure veremben kínáló partner termékekkel kapcsolatos további tudnivalókért tekintse meg a "[védett alkalmazások és adatok Azure veremben](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
