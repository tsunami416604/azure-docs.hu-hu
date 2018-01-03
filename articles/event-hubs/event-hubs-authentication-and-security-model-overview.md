---
title: "Azure Event Hubs-hitelesítés és a biztonsági modell áttekintése |} Microsoft Docs"
description: "Event Hubs hitelesítés és a biztonsági modell – áttekintés."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: sethm;clemensv
ms.openlocfilehash: bfe7b95236c1a5336c1bb3a93d0eb5ca880adabf
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Event Hubs hitelesítés és a biztonsági modell – áttekintés

Az Azure Event Hubs biztonsági modell megfelel-e a következő követelményeknek:

* Csak ügyfeleket, hogy érvényes hitelesítő adatokat küldhet egy eseményközpontba.
* Egy ügyfél egy másik ügyfél nem tudja megszemélyesíteni.
* Egy támadó ügyfelet is letiltja a adatok küldése az eseményközpontba.

## <a name="client-authentication"></a>Ügyfél-hitelesítés

Az Event Hubs biztonsági modell kombinációja alapján [közös hozzáférésű Jogosultságkód (SAS)](../service-bus-messaging/service-bus-sas.md) jogkivonatok és *esemény-közzétevők*. Egy esemény-közzétevő határozza meg egy virtuális végpont az eseményközpontban. A közzétevő csak használható küldhet üzeneteket az eseményközpontba. Nincs lehetőség a közzétevők érkező üzenetek fogadására.

Az eseményközpontok általában egy publisher ügyfelenként funkcióit használja. Az eseményközpontok a közzétevők bármelyikét küldött összes üzenet a várólistában levő adott eseményközpont belül. Közzétevők részletes hozzáférés-vezérlés és a szabályozás engedélyezéséhez.

Minden Event Hubs-ügyfél hozzá van rendelve egy egyedi jogkivonatot, amely az ügyfél feltöltődött. A jogkivonatok úgy, hogy mindegyik egyedi token engedélyezi a hozzáférést egy másik egyedi publisher előállítása. Ügyfél, amely rendelkezik egy token csak egy publisher, de nincs más publisher küldhet. Több ügyfelek osztják meg ugyanezt a tokent, ha azok a közzétevők osztja ki.

Bár nem javasolt, akkor lehet jogkivonatokkal eseményközpontokba való közvetlen hozzáférést biztosító eszközök ellátására. Minden olyan eszköz, amely tárolja Ez a token közvetlenül az adott eseményközpont küldhet üzeneteket. Ilyen eszköz sávszélesség-szabályozás nem függvénye. Továbbá az eszköz nem lehet feketelistára teszi, az adott eseményközpont küld.

Összes jogkivonatot egy SAS-kulcs van aláírva. Általában minden jogkivonatok aláírt ugyanazzal a kulccsal. Az ügyfelek nem ismerik a kulcsot. Ez megakadályozza, hogy más ügyfelekkel gyártási jogkivonatokat.

### <a name="create-the-sas-key"></a>A SAS-kulcs létrehozása

Az Event Hubs névtér létrehozásakor a szolgáltatás automatikusan létrehozza az SAS-kulcsot 256 bites nevű **RootManageSharedAccessKey**. Ez a szabály rendelkezik társított párjának küldés engedélyezése az elsődleges és másodlagos kulcsok, figyelésére és a névtérhez jogosultságaik kezelését. További kulcsok is létrehozhat. Javasoljuk, hogy a egy kulcs, küldjön engedélyeket biztosít az adott event hubs eredményez. Ez a témakör hátralévő, feltételezzük, hogy ez a kulcs elnevezett **EventHubSendKey**.

Az alábbi példa létrehoz egy csak küldési kulcsot, az eseményközpont létrehozásakor:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Rendszer jogkivonatokat hoz létre

Az SAS-kulcsot használó tokenek hozhat létre. Csak egy token ügyfelenként kell eredményez. Jogkivonatok az alábbi módszerrel majd előállított. Minden jogkivonatok segítségével hozhatók létre a **EventHubSendKey** kulcs. A tokenek hozzá van rendelve egy egyedi URI-t.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Ez a metódus hívásakor az URI Azonosítót kell megadni: `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Minden jogkivonatokat URI megegyezik, kivéve a `PUBLISHER_NAME`, amely nem lehet ugyanaz a minden token. Ideális esetben `PUBLISHER_NAME` , amely megkapja a jogkivonatot az ügyfél Azonosítójának jelöli.

Ez a módszer létrehoz egy jogkivonatot az alábbi szerkezettel:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

A jogkivonat lejárati ideje másodpercben 1970. január 1. a van megadva. A következő egy példa egy jogkivonatot:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Általában a jogkivonatok élettartama hasonlít, vagy meghaladja az ügyfél élettartamát. Ha az ügyfél új jogkivonat beszerzése képességgel rendelkezik, egy rövidebb élettartamot rendelkező jogkivonatokat használható.

### <a name="sending-data"></a>Adatok küldése

A jogkivonatok létrehozása után, minden ügyfél a saját egyedi jogkivonattal lett kiépítve.

Az ügyfél eseményközpontnak adatokat küld, azt a küldési kérelem a token címkét. Megakadályozza a lehallgatást, és a token ellophassák támadók, hogy az az ügyfél és az event hubs közötti kommunikációra egy titkosított csatornán keresztül.

### <a name="blacklisting-clients"></a>Az ügyfelek letiltott

A jogkivonat támadók ellopják, a támadó megszemélyesíthet-e az ügyfél, amelynek token ellopott. Letiltott ügyfél jeleníti meg, hogy az ügyfél használhatatlanná amíg nem kap egy új jogkivonatot, amely egy másik közzétevőt használ.

## <a name="authentication-of-back-end-applications"></a>Háttér-alkalmazások hitelesítése

Hitelesítést végezni, amely az adatokat az Event Hubs-ügyfelek által generált háttér-alkalmazások, az Event Hubs egy Service Bus-témakörök használt modell hasonló biztonsági modellt alkalmaz. Az Event Hubs fogyasztói csoportot megegyezik egy Service Bus-témakörbe való előfizetés. Egy ügyfél egy fogyasztói csoportot hozhat létre, ha a felhasználói csoport létrehozására vonatkozó kérelem kíséri jogkivonatot, hogy biztosít kezelése jogosultságokkal az event hubs és a névteret, amelyhez az event hubs tartozik. Egy ügyfél egy felhasználói csoport származó adatok felhasználásához, ha a fogadási kérelem egy jogkivonatot, amely megadja az adott felhasználói csoportban, az event hubs vagy a névteret, amelyhez az event hubs tartozik fogadási jogosultságot kap.

A Service Bus jelenlegi verziója nem támogatja a SAS-szabályokat az egyes előfizetések. Ugyanez érvényes Event Hubs fogyasztói csoportok. SAS-támogatás a jövőben is a szolgáltatások lesz hozzáadva.

Egyéni fogyasztói csoportok SAS hitelesítési hiányában a SAS-kulcs segítségével biztonságos minden felhasználói csoport egy közös kulccsal. Ez a megközelítés lehetővé teszi az alkalmazás bármelyik a fogyasztói csoportok eseményközpontban az adatokat.

## <a name="next-steps"></a>További lépések

Az Event Hubs kapcsolatos további információkért látogasson el a következő témaköröket:

* [Event Hubs – áttekintés]
* [Közös hozzáférésű Jogosultságkód áttekintése]
* [Az Event Hubsot használó mintaalkalmazások]

[Event Hubs – áttekintés]: event-hubs-what-is-event-hubs.md
[Az Event Hubsot használó mintaalkalmazások]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Közös hozzáférésű Jogosultságkód áttekintése]: ../service-bus-messaging/service-bus-sas.md

