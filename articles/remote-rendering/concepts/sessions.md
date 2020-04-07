---
title: Távoli renderelési munkamenetek
description: A távoli renderelési munkamenet leírása
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681687"
---
# <a name="remote-rendering-sessions"></a>Távoli renderelési munkamenetek

Az Azure távoli renderelés (ARR) egy *munkamenet* kulcsfontosságú fogalom. Ez a cikk ismerteti, hogy pontosan mi is az a munkamenet.

## <a name="overview"></a>Áttekintés

Az Azure remote rendering működik a komplex renderelési feladatok a felhőbe. Ezeket a renderelési feladatokat nem tudja teljesíteni akármilyen kiszolgáló, mivel a legtöbb felhőkiszolgáló nem rendelkezik GPU-kkal. Az érintett adatok mennyisége és az interaktív képkockasebességen elért eredmények szigorú követelménye miatt a felhasználói kérést kezelő kiszolgáló felelőssége nem adható át egy másik gépnek menet közben, mivel a gyakoribb webes forgalom lehetséges.

Ez azt jelenti, hogy az Azure Remote Rendering használatakor a szükséges hardverképességekkel rendelkező felhőkiszolgálót kizárólag a leképezési kérelmek kezeléséhez kell fenntartani. A *munkamenet* a kiszolgálóval való interakcióval kapcsolatban érintett minden reked. A kezdeti kéréssel kezdődik, hogy lefoglalja *(lízing)* egy gépet az Ön számára, folytatja a modellek betöltésére és kezelésére vonatkozó összes parancsot, és a felhőkiszolgálón a bérlet felszabadításával végződik.

## <a name="managing-sessions"></a>Munkamenetek kezelése

A munkamenetek kezelésére és kezelésére többféleképpen is kommunikálhat. A munkamenetek létrehozásának, frissítésének és leállításának nyelvfüggetlen módja [a munkamenet-kezelés REST API-n](../how-tos/session-rest-api.md)keresztül történik. A C# és C++ csoportban ezek a `AzureFrontend` műveletek `AzureSession`az osztályokon és a . Unity alkalmazások, vannak további segédprogram funkciók `ARRServiceUnity` által biztosított alkatrész.

Miután *csatlakozott* egy aktív munkamenethez, az olyan műveletek, mint a modellek `AzureSession` [betöltése](models.md) és a jelenettel való interakció az osztályon keresztül.

### <a name="managing-multiple-sessions-simultaneously"></a>Több munkamenet egyidejű kezelése

Nem lehet teljes mértékben *csatlakozni* több munkamenethez egy eszközről. Azonban létrehozhat, megfigyelhet és leállíthatj a kívánt munkamenetek közül egyetlen alkalmazásból. Mindaddig, amíg az alkalmazás nak nem célja, hogy valaha is csatlakozzon egy munkamenethez, nem kell futtatnia egy olyan eszközön, mint a HoloLens 2. Egy ilyen implementáció használati esete lehet, ha az üléseket egy központi mechanizmuson keresztül szeretné vezérelni. Létrehozhatunk például egy webalkalmazást, ahol több táblagép és HoloLens is bejelentkezhet. Ezután az alkalmazás megjelenítheti a táblagépeken a beállításokat, például azt, hogy melyik CAD-modellt jelenítse meg. Ha egy felhasználó kiválaszt egy beállítást, ezt az információt az összes HoloLenses közli, hogy közös élményt hozzon létre.

## <a name="session-phases"></a>Munkamenet-fázisok

Minden munkamenet több fázison megy keresztül.

### <a name="session-startup"></a>Munkamenet indítása

Amikor új munkamenet [létrehozására](../how-tos/session-rest-api.md#create-a-session)kéri az ARR-t , az első dolog, amit tesz, hogy visszaadja a munkamenet [UUID azonosítót.](https://en.wikipedia.org/wiki/Universally_unique_identifier) Ez az UUID lehetővé teszi a munkamenetre vonatkozó információk lekérdezését. Az UUID és a munkamenettel kapcsolatos néhány alapvető információ 30 napig megmarad, így a munkamenet leállítása után is lekérdezheti ezeket az információkat. Ezen a ponton a **munkamenet-állapot** indításkor lesz **jelentve.**

Ezután az Azure Remote Rendering megpróbálja megtalálni a munkamenetet üzemeltető kiszolgálót. A keresésnek két paramétere van. Először is, ez csak a tartalék szerverek a [régióban](../reference/regions.md). Ennek az az oka, hogy a régiók közötti hálózati késés túl magas lehet ahhoz, hogy tisztességes élményt biztosítson. A második tényező a megadott kívánt *méret.* Minden régióban korlátozott számú kiszolgáló, amely képes teljesíteni a *standard* vagy *prémium* méretű kérelmet. Következésképpen, ha a kért méretű kiszolgálók jelenleg használatban vannak a régióban, a munkamenet létrehozása sikertelen lesz. A hiba [oka lekérdezhető](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Ha egy *szabványos* virtuális gép méretét kéri, és a kérelem nagy igény miatt sikertelen, ez nem jelenti azt, hogy a *prémium szintű* kiszolgáló kérése sikertelen lesz is. Tehát, ha ez egy lehetőség az Ön számára, akkor próbálja meg visszaesik egy *prémium* virtuális gép.

Amikor a szolgáltatás megfelelő kiszolgálót talál, a megfelelő virtuális gépet (VM) át kell másolnia rá, hogy az Azure Remote-leképezési állomássá alakítsa. Ez a folyamat több percig is eltarthat. Ezt követően a virtuális gép elindul, és a **munkamenet állapota** átkerül **a Ready**.

Ezen a ponton a kiszolgáló kizárólag a bemenetre vár. Ez is az a pont, ahonnan kapsz számlázott a szolgáltatást.

### <a name="connecting-to-a-session"></a>Csatlakozás munkamenethez

Miután a munkamenet *készen*áll, *csatlakozhat* hozzá. A kapcsolat alatt az eszköz parancsokat küldhet a modellek betöltéséhez és módosításához. Minden ARR-állomás egyszerre csak egy ügyféleszközt szolgál ki, így amikor egy ügyfél csatlakozik egy munkamenethez, kizárólagos ellenőrzést gyakorol a megjelenített tartalom felett. Ez azt is jelenti, hogy a renderelési teljesítmény soha nem változik az ön hatáskörén kívül álló okok miatt.

> [!IMPORTANT]
> Bár csak egy ügyfél *tud csatlakozni* egy munkamenethez, a munkamenetekkel kapcsolatos alapvető információk, például az aktuális állapotuk, lekérdezhetők csatlakozás nélkül.

Amíg egy eszköz munkamenethez csatlakozik, más eszközök csatlakozási kísérletei sikertelenek lesznek. Ha azonban a csatlakoztatott eszköz bontja a kapcsolatot, akár önként, akár valamilyen hiba miatt, a munkamenet elfogad egy másik csatlakozási kérelmet. Az összes korábbi állapot (betöltött modellek és az ilyen) úgy kerül kidobásra, hogy a következő csatlakozó eszköz tiszta lappal induljon. Így a munkamenetek többször is felhasználhatók különböző eszközökkel, és a legtöbb esetben elrejtheti a munkamenet indítási terhelését a végfelhasználó elől.

> [!IMPORTANT]
> A távoli kiszolgáló soha nem változtatja meg az ügyféloldali adatok állapotát. Az ügyfélalkalmazásnak az adatok összes mutációját (például az átalakítási frissítéseket és a betöltési kérelmeket) kell végrehajtania. Minden művelet azonnal frissíti az ügyfél állapotát.

### <a name="session-end"></a>Munkamenet vége

Amikor új munkamenetet kér, meg kell adnia egy *maximális bérleti időt,* általában egy-nyolc óra tartományban. Ez az az időtartam, amely alatt a gazdagép elfogadja a bemenetet.

Két rendszeres oka van annak, hogy egy munkamenet véget ér. Manuálisan kéri a munkamenet leállítását, vagy a maximális bérleti idő lejár. Mindkét esetben az állomással való aktív kapcsolat azonnal megszakad, és a szolgáltatás le áll azon a kiszolgálón. A kiszolgáló ezután visszakerül az Azure-készletbe, és más célokra is lekérheti. A munkamenet leállítása nem vonható vissza vagy nem vonható vissza. A **munkamenet-állapot** lekérdezése egy leállított munkameneten vagy **leállítva** vagy **lejárt**értéket ad vissza attól függően, hogy manuálisan állították-e le, vagy mert elérte a maximális címbérleti időt.

A munkamenet meghibásodás miatt is leállítható.

A munkamenet leállítása után nem kell további díjat fizetnie.

> [!WARNING]
> Az, hogy csatlakozik-e egy munkamenethez, és mennyi ideig, nincs hatással a számlázásra. A szolgáltatásért fizetett fizetés a *munkamenet időtartamától*függ, ami azt jelenti, hogy a kiszolgáló csak az Ön számára van fenntartva, és a kért hardverképességektől (a virtuális gép méretétől). Ha elindítja a munkamenetet, öt percig csatlakozik, majd nem állítja le a munkamenetet, hogy a bérlet lejártáig továbbra is működjön, a teljes munkamenet-bérleti idő számlázása. Ezzel szemben a *maximális bérleti idő* többnyire biztonsági háló. Nem számít, hogy nyolc órás bérleti idővel rendelkező munkamenetet kér-e, majd csak öt percig használja, ha ezt követően manuálisan leállítja a munkamenetet.

#### <a name="extend-a-sessions-lease-time"></a>Munkamenet bérleti idejének meghosszabbítása

Meghosszabbíthatja egy aktív munkamenet [bérleti idejét,](../how-tos/session-rest-api.md#update-a-session) ha kiderül, hogy hosszabb időre van szüksége.

## <a name="example-code"></a>Mintakód

Az alábbi kód egy egyszerű megvalósítása a munkamenet indítása, várja a *kész* állapot, csatlakozás, majd a leválasztás és leállítás újra.

``` cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Több `AzureFrontend` `AzureSession` és példányok lehet karbantartani, manipulálni, és lekérdezni a kódot. De egyszerre csak egy eszköz `AzureSession` csatlakozhat egy eszközhöz.

A virtuális gép élettartama nem kötődik `AzureFrontend` a `AzureSession` példányhoz vagy a példányhoz. `AzureSession.StopAsync`kell hívni a munkamenet leállítására.

Az állandó munkamenet-azonosító helyileg `AzureSession.SessionUUID()` lekérdezhető és gyorsítótárazható. Ezzel az azonosítóval az `AzureFrontend.OpenSession` alkalmazás meghívhat, hogy kötődjenek az adott munkamenethez.

Ha `AzureSession.IsConnected` igaz, `AzureSession.Actions` a függvény `RemoteManager`egy példányát adja vissza, amely a [modellek betöltéséhez,](models.md) [az entitások](entities.md)kezeléséhez és a megjelenített jelenet [adatainak lekérdezéséhez](../overview/features/spatial-queries.md) szükséges függvényeket tartalmazza.

## <a name="next-steps"></a>További lépések

* [Entitások](entities.md)
* [Modellek](models.md)
