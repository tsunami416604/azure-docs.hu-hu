---
title: Azure Service Fabric API Management áttekintéssel
description: Ez a cikk bemutatja, hogyan használhatja az Azure API Management-t a Service Fabric alkalmazásaihoz tartozó átjáróként.
author: vturecek
ms.topic: conceptual
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 2a331715d4e4538cfdda8d958ff549a81b627b79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76028553"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric és Azure API Management – áttekintés

A felhőalapú alkalmazásokhoz általában előtér-átjáró szükséges, amely egyetlen belépési pontként szolgálhat a felhasználók, eszközök és egyéb alkalmazások számára. Service Fabric az átjáró bármilyen állapot nélküli szolgáltatás lehet, például [ASP.net Core alkalmazás](service-fabric-reliable-services-communication-aspnetcore.md)vagy más, a bejövő forgalomra tervezett szolgáltatás, például [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT hub](https://docs.microsoft.com/azure/iot-hub/)vagy [Azure API Management](https://docs.microsoft.com/azure/api-management/).

Ez a cikk bemutatja, hogyan használhatja az Azure API Management-t a Service Fabric alkalmazásaihoz tartozó átjáróként. A API Management közvetlenül integrálható Service Fabricekkel, így lehetővé teszi az API-k széles körű útválasztási szabályokkal történő közzétételét a háttérbeli Service Fabric-szolgáltatásokhoz.

## <a name="availability"></a>Rendelkezésre állás

> [!IMPORTANT]
> Ez a funkció a szükséges virtuális hálózatok támogatásának köszönhetően a API Management **prémium** és **fejlesztői** szintjein érhető el.

## <a name="architecture"></a>Architektúra

Egy közös Service Fabric architektúra egy egyoldalas webalkalmazást használ, amely HTTP-hívásokat tesz elérhetővé HTTP API-kat használó háttér-szolgáltatásokhoz. A [Service Fabric első lépéseket](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) bemutató példa erre az architektúrára mutat példát.

Ebben az esetben az állapot nélküli webszolgáltatás átjáróként szolgál a Service Fabric alkalmazásban. Ehhez a megközelítéshez olyan webszolgáltatást kell írnia, amely HTTP-kéréseket tud a háttér-szolgáltatásokhoz adni, az alábbi ábrán látható módon:

![Service Fabric az Azure API Management topológia áttekintése][sf-web-app-stateless-gateway]

Mivel az alkalmazások összetettséget mutatnak, ezért olyan átjáróknak kell lenniük, amelyeknek a számtalan háttér-szolgáltatás előtt API-t kell tartalmazniuk. Az Azure API Management úgy lett kialakítva, hogy összetett API-kat kezeljen az útválasztási szabályokkal, a hozzáférés-vezérléssel, a ráta korlátozásával, a figyeléssel, az események naplózásával és a válasz gyorsítótárazásával, és minimális Az Azure API Management támogatja Service Fabric szolgáltatások felderítését, a partíciók feloldását és a replika kiválasztását, hogy a kéréseket közvetlenül a háttérbeli szolgáltatásokhoz irányítsa a Service Fabric, így nem kell saját állapot nélküli API-átjárót írnia. 

Ebben az esetben a webes felhasználói felületet továbbra is egy webszolgáltatáson keresztül kézbesítjük, míg a HTTP API-hívásokat az Azure API Management felügyeli és irányítja át, ahogy az a következő ábrán látható:

![Service Fabric az Azure API Management topológia áttekintése][sf-apim-web-app]

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

A Service Fabric szolgáltatásai lehetnek állapot nélküliek vagy állapotok, és a három séma egyikével particionálva lehetnek: egyszeres, int-64 tartomány és névvel ellátott. A szolgáltatás végpontjának feloldásához egy adott szolgáltatási példány egy adott partícióját kell azonosítani. Egy szolgáltatás végpontjának feloldásakor a szolgáltatás példányának nevét (például: `fabric:/myapp/myservice`), valamint a szolgáltatás adott partícióját is meg kell adni, kivéve az egyedi partíciók esetében.

Az Azure API Management az állapot nélküli szolgáltatások, az állapot-nyilvántartó szolgáltatások és a particionálási sémák bármilyen kombinációjával használható.

## <a name="send-traffic-to-a-stateless-service"></a>Forgalom elküldése állapot nélküli szolgáltatásba

A legegyszerűbb esetben a rendszer továbbítja a forgalmat egy állapot nélküli szolgáltatási példányra. Ennek eléréséhez egy API Management művelet egy olyan Service Fabric háttérbeli bejövő feldolgozási szabályzatot tartalmaz, amely egy adott állapot nélküli Service-példányhoz van leképezve a Service Fabric háttérbe. A szolgáltatásnak küldött kérelmeket a szolgáltatás egy véletlenszerűen kiválasztott példánya továbbítja.

**Például**

A következő forgatókönyvben egy Service Fabric alkalmazás tartalmaz egy nevű `fabric:/app/fooservice`állapot nélküli szolgáltatást, amely egy belső http API-t tesz elérhetővé. A szolgáltatás példányának neve jól ismert, és a API Management bejövő feldolgozási házirendben közvetlenül is kódolható. 

![Service Fabric az Azure API Management topológia áttekintése][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Forgalom küldése állapot-nyilvántartó szolgáltatásnak

Az állapot nélküli szolgáltatási forgatókönyvhöz hasonlóan a forgalom is továbbítható egy állapot-nyilvántartó szolgáltatási példányba. Ebben az esetben egy API Management művelet egy olyan Service Fabric háttérbeli bejövő feldolgozási szabályzatot tartalmaz, amely egy adott *állapot-nyilvántartó* példány egy adott partíciójának egy kérését képezi le. Az egyes kérések hozzárendelésére szolgáló partíciót lambda módszerrel kell kiszámítani a bejövő HTTP-kérésből származó bizonyos adatokkal, például az URL-cím elérési útjában szereplő értékkel. A házirend úgy konfigurálható, hogy csak az elsődleges replikára, vagy az olvasási műveletek véletlenszerű replikára küldje a kérelmeket.

**Például**

A következő forgatókönyvben a Service Fabric alkalmazás egy nevű `fabric:/app/userservice` particionált állapot-nyilvántartó szolgáltatást tartalmaz, amely egy belső http API-t tesz elérhetővé. A szolgáltatás példányának neve jól ismert, és a API Management bejövő feldolgozási házirendben közvetlenül is kódolható.  

A szolgáltatás particionálva van a Int64 partíciós séma használatával két partícióval és egy, a `Int64.MinValue` - `Int64.MaxValue`ra kiterjedő kulcs tartománnyal. A háttérrendszer-házirend kiszámítja az adott tartományon belüli partíciós kulcsot `id` úgy, hogy az URL-kérelem útvonalán megadott értéket egy 64 bites egész számra konvertálja, de a partíciós kulcs kiszámításához itt bármely algoritmus használható. 

![Service Fabric az Azure API Management topológia áttekintése][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Forgalom küldése több állapot nélküli szolgáltatásba

A fejlettebb forgatókönyvekben olyan API Management műveletet határozhat meg, amely több szolgáltatási példányra is leképezi a kérelmeket. Ebben az esetben minden művelet egy olyan házirendet tartalmaz, amely a bejövő HTTP-kérelemből származó értékek alapján leképezi a kérelmeket egy adott szolgáltatási példányra, például az URL-cím vagy a lekérdezési karakterláncot, valamint az állapot-nyilvántartó szolgáltatások esetében a szolgáltatás példányán belüli partíciót.

Ennek eléréséhez egy API Management művelet egy olyan Service Fabric háttérrel rendelkező bejövő feldolgozási szabályzatot tartalmaz, amely a bejövő HTTP-kérelemből beolvasott értékek alapján leképezi a Service Fabric háttér nélküli szolgáltatási példányát. A szolgáltatásnak küldött kéréseket a szolgáltatás egy véletlenszerűen kiválasztott példánya továbbítja.

**Például**

Ebben a példában egy új állapot nélküli szolgáltatási példány jön létre a dinamikusan generált névvel rendelkező alkalmazás minden felhasználója számára a következő képlet használatával:

- `fabric:/app/users/<username>`

  Minden szolgáltatás egyedi névvel rendelkezik, de a nevek nem ismertek, mert a szolgáltatások felhasználói vagy rendszergazdai bevitelre válaszul jönnek létre, ezért nem lehet a APIM-házirendekben vagy útválasztási szabályokban rögzített módon kódolni. Ehelyett annak a szolgáltatásnak a neve jelenik meg, amelyre a kérést küldeni kívánja a háttér-házirend definíciójában az `name` URL-kérelem elérési útján megadott érték alapján. Például:

  - A szolgáltatás példányára irányuló kérelem `/api/users/foo` átirányítva`fabric:/app/users/foo`
  - A szolgáltatás példányára irányuló kérelem `/api/users/bar` átirányítva`fabric:/app/users/bar`

![Service Fabric az Azure API Management topológia áttekintése][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Forgalom küldése több állapot-nyilvántartó szolgáltatásnak

Az állapot nélküli szolgáltatás példájának megfelelően egy API Management művelet több **állapot-nyilvántartó** szolgáltatási példányra is leképezheti a kérelmeket, ebben az esetben előfordulhat, hogy a partíciók feloldását is el kell végeznie az egyes állapot-nyilvántartó szolgáltatás példányai esetében.

Ennek eléréséhez egy API Management művelet egy olyan Service Fabric háttérbeli bejövő feldolgozási szabályzatot tartalmaz, amely a bejövő HTTP-kérelemből beolvasott értékek alapján leképez egy állapot-nyilvántartó szolgáltatási példányt a Service Fabric háttérbe. Az adott szolgáltatási példányra vonatkozó kérések leképezése mellett a kérést a szolgáltatási példányon belül egy adott partícióra is lehet hozzárendelni, és opcionálisan az elsődleges replikára vagy a partíción belüli másodlagos replikára is.

**Például**

Ebben a példában egy új állapot-nyilvántartó szolgáltatási példány jön létre az alkalmazás minden felhasználója számára egy dinamikusan generált névvel a következő képlet használatával:

- `fabric:/app/users/<username>`

  Minden szolgáltatás egyedi névvel rendelkezik, de a nevek nem ismertek, mert a szolgáltatások felhasználói vagy rendszergazdai bevitelre válaszul jönnek létre, ezért nem lehet a APIM-házirendekben vagy útválasztási szabályokban rögzített módon kódolni. Ehelyett annak a szolgáltatásnak a neve jelenik meg, amelyre a kérést küldeni kívánja a háttér-házirend definíciójában az `name` URL-kérelem elérési útjának megadott érték alapján. Például:

  - A szolgáltatás példányára irányuló kérelem `/api/users/foo` átirányítva`fabric:/app/users/foo`
  - A szolgáltatás példányára irányuló kérelem `/api/users/bar` átirányítva`fabric:/app/users/bar`

A Int64 partíciós séma két partícióval és egy, a rendszerre kiterjedő kulcstároló `Int64.MinValue` használatával is particionálva van `Int64.MaxValue`. A háttérrendszer-házirend kiszámítja az adott tartományon belüli partíciós kulcsot `id` úgy, hogy az URL-kérelem útvonalán megadott értéket egy 64 bites egész számra konvertálja, de a partíciós kulcs kiszámításához itt bármely algoritmus használható. 

![Service Fabric az Azure API Management topológia áttekintése][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>További lépések

Kövesse az [oktatóanyagot](service-fabric-tutorial-deploy-api-management.md) , és állítsa be az első Service Fabric-fürtöt API Management és flow-kérelmekkel a szolgáltatásokra API Managementon keresztül.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
