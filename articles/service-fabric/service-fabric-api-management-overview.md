---
title: "Az Azure Service Fabric API Management áttekintése |} Microsoft Docs"
description: "Ebben a cikkben megismerkedhet az Azure API Management használatával a Service Fabric-alkalmazások átjáróként."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: ea3b1f50bada3c1301f8661f8f0b4866cb1c732c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="service-fabric-with-azure-api-management-overview"></a>A Service Fabric Azure API Management áttekintése

A felhőalapú alkalmazásokhoz általában adjon meg egy olyan hibaérzékeny pontot érkező felhasználók, eszközök és más alkalmazások előtér-átjáró szükséges. A Service Fabric átjáró lehet bármely állapotmentes szolgáltatások, mint egy [ASP.NET Core alkalmazás](service-fabric-reliable-services-communication-aspnetcore.md), vagy egy másik szolgáltatás, amely a forgalom érkező, például a [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/), vagy [az Azure API Management](https://docs.microsoft.com/azure/api-management/).

Ebben a cikkben megismerkedhet az Azure API Management használatával a Service Fabric-alkalmazások átjáróként. Az API Management közvetlenül integrálható a Service Fabric, lehetővé téve az API-k közzététele az útválasztási szabályokat a Service Fabric háttérszolgáltatások széles skáláját. 

## <a name="architecture"></a>Architektúra
Egy közös Service Fabric-architektúrát használ egy egyoldalas webalkalmazást, amely HTTP-hívást háttér szolgáltatásaihoz HTTP API-k visszaállítását végzi. A [Service Fabric-bevezető mintaalkalmazás](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) ebbe az architektúrába példáját mutatja be.

Ebben a forgatókönyvben egy állapot nélküli webszolgáltatás lesz az átjáró a Service Fabric-alkalmazás. Ez a megközelítés szükséges, hogy egy webszolgáltatás, amelyet a háttér szolgáltatásaihoz, proxy HTTP-kérelmekre is írhat, mint az alábbi ábrán is látható:

![A Service Fabric Azure API Management topológia – áttekintés][sf-web-app-stateless-gateway]

Alkalmazások növekedésével a összetettségét, így tegye az átjárók, hogy az API-k tárfiókokhoz háttérszolgáltatások elé kell. Az Azure API Management összetett API-k útválasztási szabályokat, hozzáférés-vezérlés, sebességkorlátozást, figyelés, eseménynaplózás és választ meg a minimális munka gyorsítótárazást kezelésére terveztek. Az Azure API Management Service Fabric szolgáltatás felderítése, a partíció megoldás és a replika kiválasztása az intelligens módon továbbításához kérelmek közvetlenül a Service Fabric a háttér-szolgáltatásaihoz, így nem kell írni a saját állapotmentes API-átjáró támogatja. 

Ebben a forgatókönyvben a webes felhasználói felület továbbra is kiszolgált keresztül egy webszolgáltatás-bővítmény, amíg HTTP API-hívások által kezelt és Azure API Management keresztül jutnak el az alábbi ábrán látható módon:

![A Service Fabric Azure API Management topológia – áttekintés][sf-apim-web-app]

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

Lehet, hogy szolgáltatásait a Service Fabric állapot nélküli és állapotalapú, és azok particionálható három rendszerek egyikének használatával: egypéldányos, int-64 tartományon, és megnevezett. Szolgáltatási végpont feloldási van szükség, egy adott szolgáltatáspéldány az adott partíciók azonosításához. Egy szolgáltatás, a szolgáltatás példány nevét a végpont feloldásakor (például `fabric:/myapp/myservice`), valamint a szolgáltatás adott partíció meg kell adni, kivéve a singleton partíció.

Az Azure API Management állapotmentes szolgáltatások, az állapotalapú szolgáltatások és bármely particionálási sémát bármely kombinációja használható.

## <a name="send-traffic-to-a-stateless-service"></a>Adatforgalom küldését az állapotmentes szolgáltatások

A legegyszerűbb esetben a forgalmat továbbítja állapotmentes szolgáltatások példányához. Ennek érdekében az API Management művelet tartalmaz egy bejövő feldolgozási házirendje a Service Fabric-háttér, amely a megadott állapot nélküli példánya a Service Fabric-háttér van leképezve. A szolgáltatásnak küldött kérelmeket a állapotmentes szolgáltatások példány véletlenszerű másolatának kell küldeni.

#### <a name="example"></a>Példa
Az alábbi példa a Service Fabric-alkalmazás tartalmazza egy állapotmentes szolgáltatások nevű `fabric:/app/fooservice`, amely közzétesz egy belső HTTP API-t. A service-példány neve ismert, és kódolva közvetlenül a az API Management bejövő házirend. 

![A Service Fabric Azure API Management topológia – áttekintés][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Adatforgalom küldését az állapotalapú szolgáltatás

Az állapotmentes szolgáltatások forgatókönyv hasonló, forgalom továbbítható egy állapotalapú service-példány. Ebben az esetben az API Management műveletet tartalmaz egy bejövő feldolgozási házirendje a Service Fabric-háttér, amely leképezhető egy kérelem egy adott partícióra meghatározott *állapotalapú alkalmazások és szolgáltatások* szolgáltatáspéldány. A partíció minden kérés keresztül használja a bejövő HTTP-kérelem URL-címet az érték például az egyes bemeneti lambda metódus számított hozzárendelését. A házirend beállítható a kérelmek küldésére, csak az elsődleges másodpéldány, vagy egy véletlenszerű replikára az olvasási műveletek.

#### <a name="example"></a>Példa

Abban az esetben, a Service Fabric-alkalmazás tartalmazza a particionált állapotalapú service nevű `fabric:/app/userservice` , amely közzétesz egy belső HTTP API-t. A service-példány neve ismert, és kódolva közvetlenül a az API Management bejövő házirend.  

A szolgáltatás particionálva van, és két partíció és kiterjedő kulcs széles Int64 partícióséma használatával `Int64.MinValue` való `Int64.MaxValue`. A háttér-házirend kiszámítja a partíciós kulcs, azon belül átalakításával a `id` megadott érték kérelem URL-címet egy 64 bites egész számként, bár bármely algoritmushoz itt is használható a partíciós kulcs kiszámításához. 

![A Service Fabric Azure API Management topológia – áttekintés][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Több állapotmentes szolgáltatások forgalmat küldeni

A speciális esetben egy API-kezelés művelet, amely leképezhető kérelmek egynél több szolgáltatáspéldány adhat meg. Ebben az esetben minden műveletet tartalmaz egy házirendet, amely leképezhető kérelmek egy adott szolgáltatáspéldány az a bejövő HTTP-kérelem, például az URL-cím elérési út vagy a lekérdezési karakterlánc, és az állapotalapú szolgáltatások esetén a partíción belül a szolgáltatáspéldány értékei alapján. 

Ennek érdekében az API Management művelet tartalmazza egy bejövő feldolgozási házirendje a Service Fabric-háttér, amely a Service Fabric-háttér a bejövő HTTP-kérelem lekért értékek alapján az állapot nélküli szolgáltatáspéldány van leképezve. A szolgáltatáspéldány véletlenszerű replikáját egy szolgáltatáspéldány kérelmek kerülnek.

#### <a name="example"></a>Példa

Ebben a példában egy új állapotmentes szolgáltatáspéldány hozzon létre egy alkalmazás felhasználóinak egy dinamikusan előállított neve a következő képlet szerint:
 
 - `fabric:/app/users/<username>`

 Minden szolgáltatás egyedi névvel rendelkezik, de a nevek nem ismert kezdeti, mert a szolgáltatások az választ a felhasználó hozott létre, vagy a rendszergazda adja meg, és így nem lehet kódolt APIM házirendek vagy útválasztási szabályokat. Ehelyett a nevét, amelyhez a kérés küldése a szolgáltatás a háttér-házirend-definíció létrehozása történik a `name` a kérelem URL-cím a megadott érték. Példa:

  - A kérelmek `/api/users/foo` szolgáltatáspéldány irányítása`fabric:/app/users/foo`
  - A kérelmek `/api/users/bar` szolgáltatáspéldány irányítása`fabric:/app/users/bar`

![A Service Fabric Azure API Management topológia – áttekintés][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Több állapotalapú szolgáltatások forgalmat küldeni

Az állapotmentes szolgáltatások példához hasonló, az API Management művelet leképezheti kérelmek egynél több **állapotalapú alkalmazások és szolgáltatások** példány szolgáltatási, ebben az esetben akkor is előfordulhat, hogy kell végrehajtani a partíció feloldási minden állapotalapú service-példány.

Ennek érdekében az API Management művelet tartalmazza egy bejövő feldolgozási házirendje a Service Fabric-háttér, amely a Service Fabric-háttér a bejövő HTTP-kérelem lekért értékek alapján az állapot-nyilvántartó szolgáltatáspéldány van leképezve. Mellett egy kérelem hozzárendelése az adott szolgáltatáspéldány, a kérelem csak is képezhető le egy adott partíción belül a szolgáltatáspéldány számára, és szükség esetén az elsődleges másodpéldány, vagy egy véletlenszerű másodlagos replikát a partíción belül.

#### <a name="example"></a>Példa

Ebben a példában egy új állapot-nyilvántartó szolgáltatáspéldány létrejön az alkalmazás minden felhasználó a következő képlet szerint dinamikusan generált néven:
 
 - `fabric:/app/users/<username>`

 Minden szolgáltatás egyedi névvel rendelkezik, de a nevek nem ismert kezdeti, mert a szolgáltatások az választ a felhasználó hozott létre, vagy a rendszergazda adja meg, és így nem lehet kódolt APIM házirendek vagy útválasztási szabályokat. Ehelyett a nevét, amelyhez a kérés küldése a szolgáltatás a háttér-házirend-definíció létrehozása történik a `name` értéket megadva a kérelem URL-címe. Példa:

  - A kérelmek `/api/users/foo` szolgáltatáspéldány irányítása`fabric:/app/users/foo`
  - A kérelmek `/api/users/bar` szolgáltatáspéldány irányítása`fabric:/app/users/bar`

Minden szolgáltatáspéldány is particionálva van, és két partíció és kiterjedő kulcs széles Int64 partícióséma használatával `Int64.MinValue` való `Int64.MaxValue`. A háttér-házirend kiszámítja a partíciós kulcs, azon belül átalakításával a `id` megadott érték kérelem URL-címet egy 64 bites egész számként, bár bármely algoritmushoz itt is használható a partíciós kulcs kiszámításához. 

![A Service Fabric Azure API Management topológia – áttekintés][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Következő lépések

Kövesse a [oktatóanyag](service-fabric-tutorial-deploy-api-management.md) állíthatja be az első Service Fabric-fürt az API Management és a folyamat által az API Management keresztül a szolgáltatásokhoz.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png