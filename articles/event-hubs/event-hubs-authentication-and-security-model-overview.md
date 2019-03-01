---
title: A hitelesítési és biztonsági modell – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Event hubs hitelesítési és biztonsági modell.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 19b347423c28b4c615f90f325ead462b9d3e8e9e
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990034"
---
# <a name="azure-event-hubs---authentication-and-security-model"></a>Az Azure Event Hubs - hitelesítési és biztonsági modell

Az Azure Event Hubs biztonsági modell megfelel az alábbi követelményeknek:

* Csak olyan ügyfelek érvényes hitelesítő adatokat küldhet adatokat egy eseményközpontba.
* Egy ügyfél egy másik ügyfél nem lehet megszemélyesíteni.
* Adatok küldése eseményközpontba blokkolható a támadó ügyfelet.

## <a name="client-authentication"></a>Ügyfél-hitelesítés

Az Event Hubs biztonsági modellt kombinációját alapján [közös hozzáférésű Jogosultságkód (SAS)](../service-bus-messaging/service-bus-sas.md) jogkivonatok és *esemény-közzétevők*. Egy esemény-közzétevő egy eseményközpontba egy virtuális végpontot határozza meg. A kiadó csak használható üzeneteket küldeni egy eseményközpontba. Nem alkalmas közzétevőtől származó üzenetek fogadásához.

Általában egy eseményközpontba alkalmaz egy közzétevő ügyfelenként. Sem a kiadók az eseményközpontok felé küldött összes üzenet a várólistán lévő adott eseményközpontban. Közzétevők részletes hozzáférés-vezérléshez és a szabályozás engedélyezéséhez.

Minden Event Hubs-ügyfél hozzá van rendelve egy egyedi jogkivonatot, amely az ügyfél fel a rendszer. A jogkivonatokat hoz létre, hogy minden egyes egyedi tokent egy másik egyedi közzétevő engedélyezi a hozzáférést. Ügyfél, amely rendelkezik egy token csak egy közzétevő, de nincs más kiadó küldhet. Ha több ügyfél ugyanezt a tokent, majd azok fájlmegosztások közzétevő.

Noha nem ajánlott, lehetőség, amely közvetlen hozzáférést biztosít egy eseményközpontba jogkivonatokkal eszközök számára. Bármely eszköz, amely tartalmazza a token küldhet üzeneteket közvetlenül adott event hubs-eseményközpontba. Ilyen eszközök nem vonatkoznak a szabályozás. Továbbá az eszköz nem tiltólistán ennek az eseményközpontnak küldjön.

Az összes jogkivonatok az SAS-kulcs van bejelentkezve. Általában minden jogkivonatok ugyanazzal a kulccsal van bejelentkezve. Az ügyfelek nem ismeri a kulcsot. Ez megakadályozza, hogy más ügyfelek gyártási jogkivonatokat.

### <a name="create-the-sas-key"></a>A SAS-kulcsának létrehozása

Event Hubs-névtér létrehozásakor a szolgáltatás automatikusan előállítja az SAS-kulcsot 256 bites nevű **RootManageSharedAccessKey**. Ez a szabály rendelkezik egy elsődleges és másodlagos kulcsok, amelyek küldési biztosítanak álló kulcspárral, figyelésére és kezelésére a névtérhez. További kulcsokat is létrehozhat. Javasoljuk, hogy a kulcs, küldjön engedélyeket biztosít az adott event hubs előállításához. Ez a témakör további részében, feltételezzük, hogy ezt a kulcsot elnevezett **EventHubSendKey**.

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

### <a name="generate-tokens"></a>Jogkivonatokat hoz létre

A jogkivonatok az SAS-kulcs használatával is létrehozhat. Ügyfelenként csak egy tokent kell dolgoznia. Jogkivonatok majd elő lehet állítani a következő módszerrel. Az összes jogkivonatok segítségével hozhatók létre a **EventHubSendKey** kulcsot. Minden tokenhez hozzá van rendelve egy egyedi URI-t. A "resource" paraméter felel meg a szolgáltatás (ebben az esetben eseményközpontból) URI-végpontra.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Ez a metódus hívásakor meg kell adni az URI-t `https://<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Az összes jogkivonatokat, az URI-ja azonos, kivéve a `PUBLISHER_NAME`, amely minden tokenhez eltérőnek kell lennie. Ideális esetben `PUBLISHER_NAME` jelöli, amely megkapja ezt a jogkivonatot az ügyfél azonosítója.

Ez a módszer létrehoz egy tokent az alábbi struktúra használatával:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

A jogkivonat lejárati ideje 1970. január 1. a másodpercek alatt van megadva. Az alábbiakban látható egy példa a jogkivonat:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Általában a jogkivonatok-élettartamot, amely a következőhöz hasonló, vagy meghaladja az ügyfél a gyűjteményszintű rendelkezik. Ha az ügyfél képes az új jogkivonat beszerzése, egy rövidebb gyűjteményszintű-jogkivonatokat is használható.

### <a name="sending-data"></a>Adatok küldése

A jogkivonatok létrehozása után, minden ügyfél saját egyedi tokenhez van kiépítve.

Amikor az ügyfél adatokat küld egy eseményközpontba, akkor a jogkivonattal a küldési kérelmek címkék. Hogy megakadályozza a lehallgatást, és a jogkivonat ellophatják a támadók, az ügyfél és az eseményközpont közötti kommunikáció egy titkosított csatornán keresztül kell történnie.

### <a name="blacklisting-clients"></a>Az ügyfelek áruházat

Ha a jogkivonat támadók ellopják, a támadó az ügyfél, amelynek jogkivonat ellopott tudja megszemélyesíteni. Ügyfél áruházat jelenik meg, hogy az ügyfél használhatatlan amíg nem kap egy új jogkivonatot, amely egy másik közzétevő használja.

## <a name="authentication-of-back-end-applications"></a>Az alkalmazások háttér-hitelesítés

Háttér-használó alkalmazások programozása az Event Hubs-ügyfelek által létrehozott adatokat hitelesítéséhez, az Event Hubs egy a Service Bus-üzenettémakörök használt modell hasonló biztonsági modellt alkalmaz. Az Event Hubs fogyasztói csoportot megegyezik a Service Bus témakör-előfizetés. Egy ügyfél egy fogyasztói csoportot hozhat létre, ha a fogyasztói csoportot létrehozására vonatkozó kérelem kíséri egy jogkivonatot, hogy engedélyezi kezelése jogosultságokkal az event hubs, illetve a névtér, amely az event hubs tartozik. Egy ügyfél számára engedélyezett a tárolt adatokat használják egy fogyasztói csoportot Ha fogadási kérést egy jogkivonatot, amely megadja az adott felhasználói csoportban, az event hubs vagy a névtér, amely az event hubs tartozik fogadási jogosultságot kíséri.

A Service Bus jelenlegi verziója nem támogatja a SAS-szabályok az egyes előfizetések. Ugyanez érvényes Eseményközpont fogyasztói csoportja. SAS fogja támogatni a mindkét funkció a jövőben.

Egyéni fogyasztói csoportok SAS hitelesítési hiányában a SAS-kulcsok segítségével összes fogyasztóvédelmi csoport biztonságos közös kulccsal. Ez a megközelítés lehetővé teszi az alkalmazás bármely eseményközpont fogyasztói csoportját a tárolt adatokat használják.

## <a name="next-steps"></a>További lépések

Az Event Hubs kapcsolatos további információkért látogasson el a következő témaköröket:

* [Event Hubs – áttekintés]
* [Közös hozzáférésű Jogosultságkódok áttekintése]
* [Az Event Hubsot használó mintaalkalmazások]

[Event Hubs – áttekintés]: event-hubs-what-is-event-hubs.md
[Az Event Hubsot használó mintaalkalmazások]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Közös hozzáférésű Jogosultságkódok áttekintése]: ../service-bus-messaging/service-bus-sas.md

