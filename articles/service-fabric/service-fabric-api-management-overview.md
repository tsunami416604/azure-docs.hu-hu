---
title: Azure Service Fabric az API Management szolgáltatással – áttekintés
description: Ez a cikk az Azure API Management szolgáltatás átjáróként való használatának bemutatása.
author: vturecek
ms.topic: conceptual
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 2a331715d4e4538cfdda8d958ff549a81b627b79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028553"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric és Azure API Management – áttekintés

A felhőalapú alkalmazásokhoz általában előtér-átjáró szükséges, amely egyetlen belépési pontként szolgálhat a felhasználók, eszközök és egyéb alkalmazások számára. A Service Fabric-ben az átjáró bármilyen állapotmentes szolgáltatás lehet, például egy [ASP.NET Core alkalmazás,](service-fabric-reliable-services-communication-aspnetcore.md)vagy egy másik, forgalomforgalom-forgalomhoz tervezett szolgáltatás, például [az Event Hubs,](https://docs.microsoft.com/azure/event-hubs/) [az IoT Hub](https://docs.microsoft.com/azure/iot-hub/)vagy az Azure API [Management.](https://docs.microsoft.com/azure/api-management/)

Ez a cikk az Azure API Management szolgáltatás átjáróként való használatának bemutatása. Az API Management közvetlenül integrálható a Service Fabric, amely lehetővé teszi, hogy api-k at közzé tesz a háttér-Service Fabric-szolgáltatások számos útválasztási szabályok at.

## <a name="availability"></a>Rendelkezésre állás

> [!IMPORTANT]
> Ez a funkció az API Management **prémium** és **fejlesztői** szintjein érhető el a szükséges virtuális hálózati támogatás miatt.

## <a name="architecture"></a>Architektúra

A common Service Fabric architektúra egy egyoldalas webalkalmazást használ, amely HTTP-hívásokat hoz a HTTP API-kat elérhető háttérszolgáltatásokhoz. A [Service Fabric első lépések mintaalkalmazás](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) egy példát mutat erre az architektúrára.

Ebben a forgatókönyvben egy állapotmentes webszolgáltatás szolgál a Service Fabric-alkalmazás átjáróként szolgál. Ez a megközelítés megköveteli, hogy írjon egy webszolgáltatás, amely proxy HTTP-kérelmek háttérszolgáltatások, ahogy az alábbi ábrán látható:

![Service Fabric az Azure API Management topológiával – áttekintés][sf-web-app-stateless-gateway]

Az alkalmazások összetettségének növekedésével az átjárók is, amelyeknek api-t kell bemutatniuk számtalan háttérszolgáltatás előtt. Az Azure API Management úgy lett kialakítva, hogy összetett API-kat kezeljen útválasztási szabályokkal, hozzáférés-vezérléssel, sebességkorlátozással, figyeléssel, eseménynaplózással és válaszgyorsítótárazással, minimális munkával az Ön részéről. Az Azure API Management támogatja a Service Fabric szolgáltatásfelderítés, partíciófeloldás és replika kiválasztása intelligens módon irányítja a kérelmeket közvetlenül a service fabric háttérszolgáltatások, így nem kell írni a saját állapotmentes API-átjáró. 

Ebben a forgatókönyvben a webes felhasználói felület továbbra is egy webszolgáltatáson keresztül érhető el, míg a HTTP API-hívások kezelése és az Azure API Management en keresztül történik, ahogy az az alábbi ábrán látható:

![Service Fabric az Azure API Management topológiával – áttekintés][sf-apim-web-app]

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

A Service Fabric szolgáltatásai lehetnek állapot nélküliek vagy állapotalapúak, és három séma egyikével particionálhatók: singleton, int-64 tartomány, és elnevezett. A szolgáltatásvégpont-feloldás hoz egy adott szolgáltatáspéldány egy adott partíciójának azonosítását. Egy szolgáltatás végpontjának feloldásakor meg kell adni mind `fabric:/myapp/myservice`a szolgáltatáspéldány nevét (például), mind a szolgáltatás adott partícióját, kivéve az egyedi partíció esetében.

Az Azure API Management állapotmentes szolgáltatások, állapotalapú szolgáltatások és particionálási sémák bármilyen kombinációjával használható.

## <a name="send-traffic-to-a-stateless-service"></a>Forgalom küldése állapotmentes szolgáltatásba

A legegyszerűbb esetben a forgalom továbbítása egy állapotmentes szolgáltatáspéldány. Ennek elérése érdekében egy API-felügyeleti művelet tartalmaz egy bejövő feldolgozási szabályzatot egy Service Fabric háttérrendszer, amely leképezi egy adott állapotnélküli szolgáltatáspéldány a Service Fabric háttérrendszer. Az adott szolgáltatásnak küldött kérelmeket a szolgáltatás egy véletlenszerű példányának küldi el a rendszer.

**Példa**

A következő esetben egy Service Fabric-alkalmazás tartalmaz `fabric:/app/fooservice`egy állapotmentes szolgáltatás nevű, amely egy belső HTTP API-t tesz elérhetővé. A szolgáltatáspéldány neve jól ismert, és lehet kódolni közvetlenül az API Management bejövő feldolgozási szabályzatban. 

![Service Fabric az Azure API Management topológiával – áttekintés][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Forgalom küldése állapotalapú szolgáltatásra

Az állapotmentes szolgáltatásforgatókönyvhöz hasonlóan a forgalom is továbbítható egy állapotalapú szolgáltatáspéldányba. Ebben az esetben egy API-felügyeleti művelet egy bejövő feldolgozási szabályzatot tartalmaz egy Service Fabric háttérrendszerrel, amely leképezi a kérelmet egy adott *állapotalapú* szolgáltatáspéldány egy adott partícióra. Az egyes kérelmek leképezéséhez szükséges partíciót egy lambda metódus on keresztül számítja ki a bejövő HTTP-kérelem, például az URL-elérési út egy érték használatával. A házirend beállítható úgy, hogy csak az elsődleges kópiának vagy az olvasási műveletek véletlenszerű replikájának küldjön kérelmeket.

**Példa**

A következő esetben a Service Fabric-alkalmazás egy particionált állapotalapú szolgáltatás nevű, `fabric:/app/userservice` amely egy belső HTTP API-t tesz elérhetővé. A szolgáltatáspéldány neve jól ismert, és lehet kódolni közvetlenül az API Management bejövő feldolgozási szabályzatban.  

A szolgáltatás particionált az Int64 partícióséma használatával, `Int64.MinValue` két `Int64.MaxValue`partícióval és egy kulcstartománysal, amely a-ig terjed. A háttérházirend egy partíciókulcsot számít ki a tartományon belül az URL-kérelem elérési útján megadott `id` érték 64 bites egész számmá konvertálásával, bár itt bármilyen algoritmus használható a partíciókulcs kiszámításához. 

![Service Fabric az Azure API Management topológiával – áttekintés][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Forgalom küldése több állapotmentes szolgáltatásra

Speciálisabb esetekben definiálhat egy API Management-műveletet, amely a kérelmeket egynél több szolgáltatáspéldányhoz rendeli hozzá. Ebben az esetben minden művelet tartalmaz egy szabályzatot, amely leképezi a kérelmeket egy adott szolgáltatáspéldány értékek alapján a bejövő HTTP-kérelem, például az URL-elérési út vagy lekérdezési karakterlánc, és abban az esetben, állapotalapú szolgáltatások, a szolgáltatáspéldány on belüli partíció.

Ennek elérése érdekében egy API Management-művelet tartalmaz egy bejövő feldolgozási szabályzatot egy Service Fabric háttérrendszer, amely leképezi egy állapotmentes szolgáltatáspéldánya a Service Fabric háttérrendszer a bejövő HTTP-kérelemből beolvasott értékek alapján. A szolgáltatáshoz érkező kérelmeket a szolgáltatás egy véletlenszerű példányának küldi el a rendszer.

**Példa**

Ebben a példában egy új állapotmentes szolgáltatáspéldány jön létre az alkalmazás minden felhasználója számára dinamikusan generált névvel a következő képlet használatával:

- `fabric:/app/users/<username>`

  Minden szolgáltatás egyedi névvel rendelkezik, de a nevek nem ismertek előre, mert a szolgáltatások felhasználói vagy rendszergazdai bevitelre válaszul jönnek létre, és így nem kódolhatók apim-házirendekbe vagy útválasztási szabályokba. Ehelyett annak a szolgáltatásnak a neve, amelynek a kérelmet küldeni kell, a háttérházirend-definícióban jön létre az `name` URL-kérelem elérési útján megadott értékből. Példa:

  - A kérelem `/api/users/foo` a szolgáltatáspéldányhoz van irányítva`fabric:/app/users/foo`
  - A kérelem `/api/users/bar` a szolgáltatáspéldányhoz van irányítva`fabric:/app/users/bar`

![Service Fabric az Azure API Management topológiával – áttekintés][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Forgalom küldése több állapotalapú szolgáltatásra

Az állapotmentes szolgáltatás példához hasonlóan egy API Management-művelet egynél több **állapotalapú** szolgáltatáspéldányhoz is leképezhet kérelmeket, ebben az esetben előfordulhat, hogy minden állapotalapú szolgáltatáspéldányhoz partíciófeloldást kell végrehajtania.

Ennek elérése érdekében egy API Management-művelet tartalmaz egy bejövő feldolgozási szabályzatot egy Service Fabric háttérrendszer, amely leképezi egy állapotalapú szolgáltatáspéldány a Service Fabric háttérrendszer a bejövő HTTP-kérelemből beolvasott értékek alapján. A mellett, hogy egy kérelmet egy adott szolgáltatáspéldány, a kérelem is leképezhető egy adott partícióra a szolgáltatáspéldányon belül, és opcionálisan vagy az elsődleges replika vagy egy véletlenszerű másodlagos replika a partíción belül.

**Példa**

Ebben a példában egy új állapotalapú szolgáltatáspéldány jön létre az alkalmazás minden felhasználója számára dinamikusan generált névvel a következő képlet használatával:

- `fabric:/app/users/<username>`

  Minden szolgáltatás egyedi névvel rendelkezik, de a nevek nem ismertek előre, mert a szolgáltatások felhasználói vagy rendszergazdai bevitelre válaszul jönnek létre, és így nem kódolhatók apim-házirendekbe vagy útválasztási szabályokba. Ehelyett annak a szolgáltatásnak a neve, amelynek a kérelmet küldeni kell, a háttérházirend-definícióban jön létre az `name` URL-kérelem elérési útjának megadott értékből. Példa:

  - A kérelem `/api/users/foo` a szolgáltatáspéldányhoz van irányítva`fabric:/app/users/foo`
  - A kérelem `/api/users/bar` a szolgáltatáspéldányhoz van irányítva`fabric:/app/users/bar`

Minden szolgáltatáspéldány particionálva van az Int64 partíciósémával, amely `Int64.MinValue` `Int64.MaxValue`két partícióval és egy kulcstartománysal van elosztva. A háttérházirend egy partíciókulcsot számít ki a tartományon belül az URL-kérelem elérési útján megadott `id` érték 64 bites egész számmá konvertálásával, bár itt bármilyen algoritmus használható a partíciókulcs kiszámításához. 

![Service Fabric az Azure API Management topológiával – áttekintés][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>További lépések

Kövesse az [oktatóanyagot](service-fabric-tutorial-deploy-api-management.md) az első Service Fabric-fürt API-kezeléssel és folyamatkérésekkel az API Management szolgáltatáson keresztül történő beállításához.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
