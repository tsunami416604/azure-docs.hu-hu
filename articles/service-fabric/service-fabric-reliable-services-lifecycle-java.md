---
title: Azure Service Fabric Reliable Services életciklusa |} A Microsoft Docs
description: További információ a Service Fabric Reliable Services életciklusa események.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 4d97803117a875514fb36f770d551204ece0e55c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448951"
---
# <a name="reliable-services-lifecycle"></a>A Reliable Services életciklusa
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

A Reliable Services egyike az elérhető az Azure Service Fabric programozási modellek. A Reliable Services életciklusa megismerésére, esetén leginkább fontos tudni, hogy az alapszintű életciklussal kapcsolatos események. Az események pontos rendezése attól függ, hogy konfigurációs adatait. 

Általánosságban véve a Reliable Services életciklusa tartalmazza a következő események:

* Indításakor:
  * Szolgáltatások vannak felépítve.
  * Szolgáltatások kell létrehozni és nulla vagy több figyelői adja vissza.
  * Minden visszaadott figyelői nyílnak meg, a szolgáltatással való kommunikáció.
  * A szolgáltatás `runAsync` metódust meghívják, és így a szolgáltatás munkahelyi háttér vagy hosszú ideig futó végezheti.
* Leállítás: során
  * A megszakítás jogkivonat számára átadott `runAsync` meg lett szakítva, és a figyelők be van zárva.
  * A service objektum maga destructed van.

Attól függően, hogy a reliable Services állapot nélküli vagy állapotalapú Reliable Services eseményei sorrendje némileg módosulhat. 

Az állapotalapú szolgáltatások esetében is, az elsődleges felcserélés forgatókönyv cím kell. Ez a feladatütemezés során elsődleges szerepe át egy másik replikára (vagy fog érkezni) a szolgáltatás leállítása nélkül. 

Végül hogy kapcsolatos hiba vagy sikertelen feltételek.

## <a name="stateless-service-startup"></a>Állapotmentes szolgáltatás indítása
Az életciklus állapotmentes szolgáltatások nagyon egyszerű. A következő események sorrendje:

1. A szolgáltatás jön létre.
2. Ezek az események párhuzamos fordulhat elő:
    - `StatelessService.createServiceInstanceListeners()` hív, és minden visszaadott figyelői nyílnak meg. `CommunicationListener.openAsync()` az egyes figyelő neve.
    - A szolgáltatás `runAsync` metódus (`StatelessService.runAsync()`) nevezzük.
3. Ilyen esetekben a szolgáltatás saját `onOpenAsync` módszert hívja meg. Pontosabban a `StatelessService.onOpenAsync()` nevezzük. Ez egy ritka felülbírálást, de érhető el.

Fontos megjegyezni, hogy nincs közötti a hívás hozhat létre, és nyissa meg a figyelők és a Rendezés `runAsync`. A figyelők előtt nyissa meg, előfordulhat, hogy `runAsync` elindult. Ehhez hasonlóan `runAsync` előfordulhat, hogy hívható meg nyitva, a kommunikációs figyelőket előtt, illetve mielőtt, még akkor is lett felépítve. Ha bármely szinkronizálásra szükség, azt kell elvégeznie a végrehajtója. Az alábbiakban néhány gyakori megoldások:

* Néha a figyelői nem működőképes, amíg az egyéb információkat jön létre, vagy egyéb munkát. A állapotmentes szolgáltatások esetében, amely a munkahelyi általában végezhető, a szolgáltatás a konstruktor. Közben is elvégezhető a `createServiceInstanceListeners()` hívása, vagy maga a figyelő a fejlesztés részeként.
* Néha az a kód `runAsync` nem indul el, amíg meg nyitva, a figyelők. Ebben az esetben további koordináció szükség. Általános megoldás, hogy adja hozzá azt a jelzőt a figyelők. A jelző azt jelzi, ha a figyelők végzett. A `runAsync` metódus ellenőrzi ezt a tényleges munka folytatása előtt.

## <a name="stateless-service-shutdown"></a>Az állapotmentes szolgáltatás leállítása
Állapotmentes szolgáltatás leáll, amikor a ugyanazt a mintát követi, de fordított:

1. Ezek az események párhuzamos fordulhat elő:
    - Minden nyitott figyelői bezárul. `CommunicationListener.closeAsync()` az egyes figyelő neve.
    - A megszakítás jogkivonat számára átadott `runAsync()` meg lett szakítva. A megszakítás token ellenőrzése `isCancelled` tulajdonság értéke `true`, és ha a neve, a jogkivonat `throwIfCancellationRequested` metódus jelez egy `CancellationException`.
2. Amikor `closeAsync()` minden egyes figyelő befejezése és `runAsync()` is befejezését követően a szolgáltatás `StatelessService.onCloseAsync()` metódust meghívják, és ha létezik. Újra ez nem egy közös felülbírálás, de használható, nyugodtan zárja be az erőforrások, állítsa le a háttérben történő feldolgozás, külső állapotmentést Befejezés vagy zárja le a meglévő kapcsolatok.
3. Miután `StatelessService.onCloseAsync()` futtatása befejeződik, a service objektum destructed van.

## <a name="stateful-service-startup"></a>Állapotalapú szolgáltatás indítása
Állapotalapú szolgáltatások egy mintát, amely hasonlít az állapotmentes szolgáltatások, néhány módosítással rendelkezik.  A következő események sorrendjének egy állapotalapú service kezdve:

1. A szolgáltatás jön létre.
2. `StatefulServiceBase.onOpenAsync()` a neve. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.
3. Ezek az események párhuzamos fordulhat elő:
    - `StatefulServiceBase.createServiceReplicaListeners()` meghívása. 
      - Ha az elsődleges szolgáltatás, az összes visszaadott figyelői nyílnak meg. `CommunicationListener.openAsync()` az egyes figyelő neve.
      - Ha a szolgáltatás egy másodlagos szolgáltatást, csak a figyelők megjelölve `listenOnSecondary = true` nyílnak meg. Figyelők, amelyek meg van nyitva a másodlagos példány hozható létre, akkor az kevésbé gyakori.
    - Ha a szolgáltatás jelenleg egy elsődleges, a szolgáltatás a `StatefulServiceBase.runAsync()` módszert hívja meg.
4. A replika figyelő minden után `openAsync()` meghívja a Befejezés gombra és `runAsync()` nevezzük, `StatefulServiceBase.onChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.

Hasonló állapotmentes szolgáltatások, az állapotalapú szolgáltatásból, a hiba nem nincs összehangolását a sorrendet, amelyben a figyelők létrejön és megnyílik, és ha `runAsync` nevezzük. Koordinációs van szüksége, ha a megoldások hasonlóan. Azonban az állapotalapú szolgáltatás egy további eset. Tegyük fel, hogy a hívások a kommunikációs figyelőket kiszolgálófarmban szükséges információ néhány belül tartani [a Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Mivel a kommunikációs figyelőket megnyithat, mielőtt a Reliable Collections olvasható és írható, és mielőtt `runAsync` elindul, néhány további koordináció szükség. A legegyszerűbb és leggyakrabban használt megoldás az, a hibakódot ad vissza, a kommunikációs figyelőket. Az ügyfél használja a hibakódot funkcióját, és ismételje meg a kérelmet.

## <a name="stateful-service-shutdown"></a>Állapotalapú szolgáltatás leállítása
Állapotmentes szolgáltatások, mint a leállítás során életciklusesemények ugyanaz, mint indításakor, de fordított irányú. Ha egy állapotalapú szolgáltatás leáll, az alábbi események történnek:

1. Ezek az események párhuzamos fordulhat elő:
    - Minden nyitott figyelői bezárul. `CommunicationListener.closeAsync()` az egyes figyelő neve.
    - A megszakítás jogkivonat számára átadott `runAsync()` meg lett szakítva. A megszakítás token hívása `isCancelled()` metódus visszatért `true`, és ha a neve, a jogkivonat `throwIfCancellationRequested()` metódus jelez egy `OperationCanceledException`.
2. Miután `closeAsync()` minden egyes figyelő befejezése és `runAsync()` is befejezését követően a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.

   > [!NOTE]  
   > Várakozás a `runAsync` befejezéséhez van szükség, csak akkor, ha ez a másodpéldány az elsődleges replika.

3. Miután a `StatefulServiceBase.onChangeRoleAsync()` metódus befejeződik, a `StatefulServiceBase.onCloseAsync()` módszert hívja meg. Ez a hívás nem szokványos felülbírálást, de érhető el.
3. Miután `StatefulServiceBase.onCloseAsync()` futtatása befejeződik, a service objektum destructed van.

## <a name="stateful-service-primary-swaps"></a>Állapotalapú szolgáltatás elsődleges cseréje
Egy állapotalapú szolgáltatás futása megnyitott kommunikációs figyelőket és a `runAsync` módszer neve csak az elsődleges replikára, állapotalapú szolgáltatások esetében. Másodlagos replikák tevődnek, de nincs további hívások megtekintése. Egy állapotalapú szolgáltatás fut, amíg a replika, amely jelenleg az elsődleges módosíthatja. Az életciklussal kapcsolatos események egy állapotalapú replika számára látható attól függ, hogy visszalép a replikát, vagy több előléptetése során a lapozófájl-kapacitás.

### <a name="for-the-demoted-primary"></a>Az lefokozott elsődleges
A Service Fabric kell az elsődleges másodpéldány, amely az üzenetek feldolgozásának leállítása, és állítsa le a háttérben történő munka lefokozása. Ez a lépés hasonlít, ha a szolgáltatás leállt. Egy különbség az, hogy a szolgáltatás nem destructed nebo closed, mert egy másodlagos marad. Az alábbi események történnek:

1. Ezek az események párhuzamos fordulhat elő:
    - Minden nyitott figyelői bezárul. `CommunicationListener.closeAsync()` az egyes figyelő neve.
    - A megszakítás jogkivonat számára átadott `runAsync()` meg lett szakítva. A megszakítás token ellenőrzése `isCancelled()` metódus visszatért `true`. Ha a neve, a jogkivonat `throwIfCancellationRequested()` metódus jelez egy `OperationCanceledException`.
2. Miután `closeAsync()` minden egyes figyelő befejezése és `runAsync()` is befejezését követően a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.

### <a name="for-the-promoted-secondary"></a>Az előléptetett másodlagos
Ehhez hasonlóan a Service Fabric kell rendelkeznie a másodlagos másodpéldány, amely figyeli a hálózati üzenetek indításához, és kezdje bármilyen háttérfeladatok végrehajtásához szükséges hitelesítenie kelljen magát. Ez a folyamat hasonlít a szolgáltatás létrehozásakor. A különbség az, hogy maga a replika már létezik. Az alábbi események történnek:

1. Ezek az események párhuzamos fordulhat elő:
    - `StatefulServiceBase.createServiceReplicaListeners()` meghívott és bármely figyelői megnyitott adja vissza. `CommunicationListener.openAsync()` az egyes figyelő neve.
    - A szolgáltatás `StatefulServiceBase.runAsync()` módszert hívja meg.
2. A replika figyelő minden után `openAsync()` meghívja a Befejezés gombra és `runAsync()` nevezzük, `StatefulServiceBase.onChangeRoleAsync()` nevezzük. Ez a hívás nem gyakran bírálja felül a szolgáltatásban.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Állapotalapú szolgáltatás leállítása és elsődleges lefokozása során felmerülő gyakori hibák
A Service Fabric állapotalapú szolgáltatás több okból az elsődleges változik. A leggyakoribb okai a következők [fürt újraegyensúlyozása](service-fabric-cluster-resource-manager-balancing.md) és [alkalmazásfrissítés](service-fabric-application-upgrade.md). Ezek a műveletek során fontos, hogy a szolgáltatás tiszteletben tartja a `cancellationToken`. Ez akkor is igaz során normál szolgáltatás leállítása, például ha a szolgáltatás törölve lett.

Szolgáltatások, amelyek tisztán kezelik a megszakítás több problémákat tapasztalhatnak. Ezek a műveletek lassúak, mert a szolgáltatások szabályosan leállítani a Service Fabric várakozik. Sikertelen frissítések végső soron ez is vezethet, időtúllépés és a visszaállítás. Tartsa tiszteletben a megszakítás token hibát is okozhat imbalanced fürtök. Fürtök kiegyensúlyozatlanná válnak, mivel a csomópontok gyakran használt adatok beolvasása. Azonban a szolgáltatások nem lehet rebalanced mert máshol helyezze el őket túl sokáig tart. 

Mivel a szolgáltatások állapot-nyilvántartó, annak valószínű oka is, hogy a szolgáltatások használata [a Reliable Collections](service-fabric-reliable-services-reliable-collections.md). A Service Fabric, az elsődleges visszalép, az első dolog, amely akkor fordul elő, esetén, hogy az alapul szolgáló állapot írási hozzáférést visszavonták. Ez egy második együttesét problémák, amelyek hatással lehetnek a szolgáltatás-életciklusának vezet. A gyűjtemények visszaadott kivételek alapján ütemezését, és hogy éppen áthelyezik a replika vagy leáll. Fontos az ilyen kivételek megfelelően kezeli. 

A Service Fabric által okozott kivételek feldolgozását kiadásokhoz állandó [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) vagy átmeneti [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Állandó kivételeket kell naplózza és lépett fel. Átmeneti kivételek végrehajtásával lehet újrapróbálkozni újrapróbálkozási logika alapján.

Teszteléséről és ellenőrzéséről a Reliable Services fontos része a kivételeket, amelyeket a használatával kezeli a `ReliableCollections` service-életciklusesemények együtt. Azt javasoljuk, hogy mindig terhelés alatt a szolgáltatás. Is végre kell hajtania verziófrissítések és [káosz tesztelés](service-fabric-controlled-chaos.md) éles környezetben való üzembe helyezés előtt. Az alábbi alapszintű lépések segítségével, győződjön meg arról, hogy a szolgáltatás implementálásának ellenőrzéséhez, és megfelelően kezeli az életciklussal kapcsolatos események.

## <a name="notes-on-service-lifecycle"></a>A szolgáltatás-életciklusának megjegyzések
* Mindkét a `runAsync()` módszer és a `createServiceInstanceListeners/createServiceReplicaListeners` hívást kötelezők. Egy szolgáltatás előfordulhat, hogy rendelkezik ilyennel, mindkettő, vagy sem. Például, ha a szolgáltatás felhasználói hívások válaszként összes feladatát, nincs szükség ahhoz, hogy megvalósítása `runAsync()`. Csak a kommunikációs figyelőket és az ahhoz tartozó kódot szükség.  Hasonlóképpen létrehozása és a kommunikációs figyelőket adatszolgáltató nem kötelező. A szolgáltatás csak háttérműveletek szeretné elvégezni, előfordulhat, hogy rendelkezik, így csak kell megvalósítani `runAsync()`.
* Egy szolgáltatás befejezéséhez érvényes `runAsync()` sikeresen és azt adják vissza. Ez nem tekinthető a hibától. Azt jelöli, hogy a háttérben futó munkáját a szolgáltatást, Befejezés. A stateful Reliable Services `runAsync()` lenne újra meghív, ha a szolgáltatás elsődleges visszalép, és majd előléptetve elsődleges.
* Ha egy szolgáltatás kilép a `runAsync()` szerint néhány váratlan kivétel történt kivétel, ez a hiba. A service objektum leáll, és a egy állapot jelenik meg hibaüzenet.
* Bár ezek a metódusok visszatérésre nincs időkorlát van, azonnal elveszíti írni. Ezért nem tudja végrehajtani a tényleges munka. Azt javasoljuk, hogy visszatér a megszakítási kérés fogadásakor a lehető leggyorsabban. Ha a szolgáltatás egy ésszerű időn belül nem válaszol ezen API-hívások, a Service Fabric kényszerített előfordulhat, hogy a szolgáltatás leáll. Ez általában csak alkalmazásfrissítések, vagy egy szolgáltatás törlésekor során történik. Ez az időtúllépési érték alapértelmezés szerint 15 perc.
* A hibák a `onCloseAsync()` elérési út eredményez `onAbort()` hívott. Ez a hívás a szolgáltatás karbantartása, és azok által igényelt erőforrásokat kiadása egy utolsó-esélye, a legjobb lehetőség. Ezt általában nevezzük, amikor egy állandó hibát észlel a csomóponton, vagy amikor a Service Fabric nem megbízható életciklusnak a kezelésében a szolgáltatáspéldány belső hiba miatt.
* `OnChangeRoleAsync()` Ha az állapotalapú szolgáltatás replika például szerepkör, megváltoztatja az elsődleges vagy másodlagos nevezzük. Elsődleges replikára kapnak írási állapota (megengedett való létrehozásához és írásához a Reliable Collections). Másodlagos replikák olvasási állapota (csak olvasható meglévő megbízható gyűjteményekből) vannak megadva. A legtöbb munkát egy állapotalapú szolgáltatásban történik, az elsődleges replika. Másodlagos replikák csak olvasható érvényesítési, jelentés létrehozásához, data szintű adatbányászatra vagy más csak olvasási feladatokat hajthat végre.

## <a name="next-steps"></a>További lépések
* [A Reliable Services bemutatása](service-fabric-reliable-services-introduction.md)
* [A Reliable Services a rövid útmutató](service-fabric-reliable-services-quick-start-java.md)

