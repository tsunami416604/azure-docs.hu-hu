---
title: Az Azure Service Fabric és az API Management áttekintése |} A Microsoft Docs
description: Ez a cikk az Azure API Management használatát, a Service Fabric-alkalmazások átjáróként.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 60466f0c3c0e674dcbfa287a0368462fd5a1d18f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120312"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric és Azure API Management áttekintése

A felhőalapú alkalmazásokhoz általában előtér-átjáró szükséges, amely egyetlen belépési pontként szolgálhat a felhasználók, eszközök és egyéb alkalmazások számára. A Service Fabricben átjáró lehet bármely állapotmentes szolgáltatás például egy [ASP.NET Core-alkalmazás](service-fabric-reliable-services-communication-aspnetcore.md), vagy egy másik szolgáltatás, például a bejövő forgalomra tervezett [az Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [az IoT Hub](https://docs.microsoft.com/azure/iot-hub/), vagy [az Azure API Management](https://docs.microsoft.com/azure/api-management/).

Ez a cikk az Azure API Management használatát, a Service Fabric-alkalmazások átjáróként. Az API Management közvetlenül integrálható a Service Fabric lehetővé teszi, hogy tegye közzé az API-k az útválasztási szabályokat a háttér-Service Fabric-szolgáltatások széles skáláját. 

## <a name="availability"></a>Rendelkezésre állás

> [!IMPORTANT]
> Ez a funkció érhető el a **prémium** és **fejlesztői** szinten az API Management miatt a szükséges virtuális hálózati támogatás.

## <a name="architecture"></a>Architektúra

Egy közös Service Fabric-architektúra egy egyoldalas webalkalmazást, amely lehetővé teszi a HTTP-hívások háttér-HTTP API-jainak elérhetővé tevő használ. A [Service Fabric-bevezető mintaalkalmazás](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) Ez az architektúra egy példát mutat be.

Ebben a forgatókönyvben egy állapot nélküli webes szolgáltatás szolgál az átjáró a Service Fabric-alkalmazásba. E módszerhez szükség van egy webszolgáltatás, amelyet a proxy HTTP-kéréseket a háttér-szolgáltatásaikhoz, is írhat az alábbi ábrán látható módon:

![Service Fabric és Azure API Management a topológia áttekintése][sf-web-app-stateless-gateway]

Alkalmazások egyre összetettebbé válnak, mivel így tegye az átjárók, amelyek egy API-t számtalan háttérszolgáltatások elé kell. Az Azure API Management-útválasztási szabályok, hozzáférés-vezérlést, sebességkorlátozást, figyelési, eseménynaplózás és válaszok gyorsítótárazását, minimális befektetett munkával az összetett API-k kezelésére szolgál. Az Azure API Management szolgáltatás felderítése a Service Fabric, partíció megoldás és a replika kiválasztása intelligensen irányíthatja a kérelmeket közvetlenül a Service Fabric háttérszolgáltatások így nem kell írni a saját állapot nélküli API-átjáró támogatja. 

Ebben a forgatókönyvben a webes felhasználói felület továbbra is biztosítja egy webszolgáltatással, miközben HTTP API-hívások felügyelt és Azure API Management keresztül irányítja át az alábbi ábrán látható módon:

![Service Fabric és Azure API Management a topológia áttekintése][sf-apim-web-app]

## <a name="application-scenarios"></a>Alkalmazáshasználati helyzetek

A Service Fabric Services lehet állapot nélküli vagy állapotalapú, és azok particionálható három sémák egyikével: egyedülálló, int-64 tartományt, és a nevesített. Végpont-hozzárendelésre van szükség, egy adott partícióra konkrét példányok azonosítása. A szolgáltatás, mind a service-példány nevét a végpont feloldásakor (például `fabric:/myapp/myservice`), valamint a szolgáltatás az adott partíció meg kell adni, kivéve a singleton partíció.

Az Azure API Management állapotmentes szolgáltatások, állapotalapú szolgáltatások és bármilyen particionálási sémát bármely kombinációja használható.

## <a name="send-traffic-to-a-stateless-service"></a>Küldeni a forgalmat a állapotmentes szolgáltatás

A legegyszerűbb esetben a forgalom állapotmentes szolgáltatáspéldány lesznek továbbítva. Ennek érdekében egy API Management műveletet tartalmaz egy bejövő feldolgozás szabályzat egy Service Fabric-háttérrendszert, amely leképezi a Service Fabric háttérbeli egy adott állapotmentes szolgáltatás-példányában. A szolgáltatásnak küldött kérések érkeznek az állapotmentes szolgáltatás példányának véletlenszerű replika.

#### <a name="example"></a>Példa
A következő esetben egy Service Fabric-alkalmazás tartalmaz egy állapotmentes szolgáltatás nevű `fabric:/app/fooservice`, amely közzéteszi a belső HTTP-API. A szolgáltatás-példány neve jól ismert, és lehet változtatható közvetlenül a bejövő feldolgozás az API Management házirendben. 

![Service Fabric és Azure API Management a topológia áttekintése][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Az állapotalapú szolgáltatások forgalmat küldeni

Az állapotmentes szolgáltatás forgatókönyv hasonló, forgalom továbbítható egy állapotalapú service-példányt. Ebben az esetben az API Management-műveletet tartalmazza, egy bejövő feldolgozás szabályzat egy Service Fabric-háttérrendszert, amely egy kérelem egy adott partíció egy adott *állapotalapú* szolgáltatáspéldány. A partíció leképezni egy lambda módszer használatával a bejövő HTTP-kérelem, például egy értéket az URL-címet a bemeneti adatokat keresztül számított minden kérelemhez. A szabályzat csak az elsődleges másodpéldány, vagy az olvasási műveletek esetében egy véletlenszerű replika kérelmek küldésére konfigurálhatók.

#### <a name="example"></a>Példa

A következő esetben a Service Fabric-alkalmazás tartalmazza a nevű particionált az állapotalapú szolgáltatások `fabric:/app/userservice` , amely elérhetővé teszi a belső HTTP-API. A szolgáltatás-példány neve jól ismert, és lehet változtatható közvetlenül a bejövő feldolgozás az API Management házirendben.  

A szolgáltatás particionálása az Int64 partícióséma használatával két partíció és kiterjedő kulcstartományhoz `Int64.MinValue` való `Int64.MaxValue`. A háttér-szabályzat kiszámítja egy partíciókulcsot, hogy a tartományában átalakításával a `id` bár bármely algoritmus itt is használható a partíciókulcs számítási egy 64 bites egész számot, hogy a kérelem URL-cím a megadott érték. 

![Service Fabric és Azure API Management a topológia áttekintése][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Küldeni a forgalmat a több állapotmentes szolgáltatások

A speciális esetben megadhatja egy API Management művelet, amely kérelmek egynél több szolgáltatáspéldány van leképezve. Ebben az esetben minden műveletet tartalmaz egy szabályzatot, amely a kérések képez le egy adott szolgáltatáspéldány, a bejövő HTTP-kérelemben szereplő, például az URL-cím elérési út vagy a lekérdezési karakterláncokat, és az állapotalapú szolgáltatások esetén egy partíción belül a szolgáltatáspéldány értékei alapján. 

Ennek érdekében egy API Management műveletet tartalmaz egy bejövő feldolgozás szabályzat egy Service Fabric-háttérrendszert, amely leképezi a Service Fabric háttér-a bejövő HTTP-kérelem lekért értékek alapján egy állapotmentes szolgáltatás-példányában. A szolgáltatás példánya egy véletlenszerű replikája szolgáltatáspéldány kérések érkeznek.

#### <a name="example"></a>Példa

Ebben a példában egy új állapot nélküli szolgáltatáspéldány létrehozása az alkalmazás minden felhasználóhoz egy dinamikusan előállított nevű használatával a következő képletre:
 
- `fabric:/app/users/<username>`

  Minden szolgáltatásnak van egy egyedi nevet, de a nevek nem ismertek induló, mert a szolgáltatások létrehozott felhasználói válaszul, vagy rendszergazdai adjon meg, és így nem módosítható az APIM-házirendek vagy útválasztási szabályokat. Ehelyett a nevét, amelyhez a kérés küldése a szolgáltatás a háttér-szabályzat-definíció létrehozása a `name` a kérelem URL-cím a megadott érték. Példa:

  - Kérelem `/api/users/foo` szolgáltatáspéldány irányítja a rendszer `fabric:/app/users/foo`
  - Kérelem `/api/users/bar` szolgáltatáspéldány irányítja a rendszer `fabric:/app/users/bar`

![Service Fabric és Azure API Management a topológia áttekintése][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Küldeni a forgalmat a több állapotalapú szolgáltatások

Az állapotmentes szolgáltatás példához hasonló, az API Management művelet leképezhet kérelmek egynél több **állapotalapú** szolgáltatás példány, ebben az esetben akkor is előfordulhat, hogy kell végrehajtani a partíció feloldási állapot-nyilvántartó minden szolgáltatáspéldány.

Ennek érdekében egy API Management műveletet tartalmaz egy bejövő feldolgozás szabályzat egy Service Fabric-háttérrendszert, amely leképezi a Service Fabric háttér-a bejövő HTTP-kérelem lekért értékek alapján egy állapotalapú szolgáltatás-példányában. Mellett egy kérelem leképezése az adott szolgáltatáspéldány, a kérelem is is le lehet képezni egy adott partíción belül a szolgáltatáspéldány, és szükség esetén az elsődleges másodpéldány, vagy egy véletlenszerű másodlagos replikát a partíción belül.

#### <a name="example"></a>Példa

Ebben a példában egy új állapot-nyilvántartó szolgáltatáspéldány létrehozása az alkalmazás minden felhasználó egy dinamikusan előállított nevű használatával a következő képletre:
 
- `fabric:/app/users/<username>`

  Minden szolgáltatásnak van egy egyedi nevet, de a nevek nem ismertek induló, mert a szolgáltatások létrehozott felhasználói válaszul, vagy rendszergazdai adjon meg, és így nem módosítható az APIM-házirendek vagy útválasztási szabályokat. Ehelyett a nevét, amelyhez a kérés küldése a szolgáltatás a háttér-szabályzat-definíció létrehozása a `name` értéket megadva a kérelem URL-címet. Példa:

  - Kérelem `/api/users/foo` szolgáltatáspéldány irányítja a rendszer `fabric:/app/users/foo`
  - Kérelem `/api/users/bar` szolgáltatáspéldány irányítja a rendszer `fabric:/app/users/bar`

Minden szolgáltatáspéldány is particionálva van, az Int64 partícióséma használatával két partíció és kiterjedő kulcstartományhoz `Int64.MinValue` való `Int64.MaxValue`. A háttér-szabályzat kiszámítja egy partíciókulcsot, hogy a tartományában átalakításával a `id` bár bármely algoritmus itt is használható a partíciókulcs számítási egy 64 bites egész számot, hogy a kérelem URL-cím a megadott érték. 

![Service Fabric és Azure API Management a topológia áttekintése][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>További lépések

Kövesse a [oktatóanyag](service-fabric-tutorial-deploy-api-management.md) állíthatja be az API Management és a flow szolgáltatásokra irányuló kérések API Management szolgáltatáson keresztül az első Service Fabric-fürtben.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png