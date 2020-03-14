---
title: Az Azure Event Grid biztonsági és hitelesítés
description: Ez a cikk a Event Grid-erőforrásokhoz való hozzáférés hitelesítésének különböző módszereit ismerteti (webhook, előfizetések, egyéni témakörök)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281014"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Event Grid erőforrásokhoz való hozzáférés hitelesítése

Az Azure Event Grid hitelesítési három típusa van:

* WebHook eseménykézbesítés
* Esemény-előfizetések
* Közzététel egyéni témakörben

## <a name="webhook-event-delivery"></a>WebHook eseménykézbesítés

Webhookok az események fogadása az Azure Event Grid számos módon tartoznak. Amikor készen áll egy új esemény, Event Grid szolgáltatás a kérelem törzsében szereplő bejegyzések HTTP-kérést a konfigurált végpontnak az eseményhez.

Event Grid számos más szolgáltatásokhoz hasonlóan webhookokat támogató van szükség, hogy a Webhook-végpontot tulajdonjogának igazolásához események azáltal, hogy a végpont megkezdése előtt. Ez a követelmény megakadályozza, hogy egy rosszindulatú felhasználó események a végpont-elárasztás. Ha használja az alábbi három Azure-szolgáltatások bármelyikét, az Azure-infrastruktúra automatikusan kezeli az ellenőrzés:

* Azure Logic Apps [Event Grid-összekötővel](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation [webhookon](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) keresztül
* Azure Functions [Event Grid triggerrel](../azure-functions/functions-bindings-event-grid.md)

Ha bármilyen más típusú végpont, például egy HTTP-eseményindító-alapú Azure-függvényt használ, a végpont kódot kell részt egy érvényesítési kézfogás az Event GRID használatával. Event Grid kétféleképpen érvényesíteni az előfizetés támogatja.

1. **ValidationCode-kézfogás (programozott)** : Ha a végpont forráskódját szabályozza, ez a módszer ajánlott. Esemény-előfizetés létrehozása idején az Event Grid egy előfizetés érvényesítési esemény küld a végponthoz. Ez az esemény sémája hasonlít bármilyen más Event Grid-esemény. Az esemény adatrésze `validationCode` tulajdonságot tartalmaz. Az alkalmazás ellenőrzi, hogy az érvényesítési kérelmet egy várt esemény-előfizetés, és az érvényesítési kódot az Event Gridbe ad. A kézfogás mechanizmus az összes Event Grid-verziót támogatja.

2. **ValidationURL kézfogás (manuális)** : bizonyos esetekben a végpont forráskódját nem érheti el a ValidationCode-kézfogás megvalósításához. Ha például harmadik féltől származó szolgáltatást (például [Zapier](https://zapier.com) vagy [IFTTT](https://ifttt.com/)) használ, az érvényesítési kóddal nem lehet programozott módon válaszolni.

   Verzió 2018-05-01-preview verziótól kezdődően Event Grid egy manuális érvényesítésre kézfogás támogatja. Ha olyan SDK-val vagy eszközzel hoz létre egy esemény-előfizetést, amely az 2018-05-01-es vagy újabb API-verziót használja, akkor Event Grid egy `validationUrl` tulajdonságot küld az előfizetés-ellenőrzési esemény adatrészén. A kézfogás elvégzéséhez, keresse meg az eseményadatokat, és manuálisan az URL egy GET kérelmet küldeni. Is használhatja, vagy a REST-ügyféllel, vagy a böngészőjében.

   A megadott URL-cím 5 percig érvényes. Ebben az időszakban az esemény-előfizetés kiépítési állapota `AwaitingManualAction`. Ha nem hajtja végre a manuális ellenőrzést 5 percen belül, a kiépítési állapot értéke `Failed`. Az esemény-előfizetés a kézi ellenőrzés előtt újra létre kell.

    Ehhez a hitelesítési mechanizmushoz a webhook-végpontnak egy 200-es HTTP-állapotkódot kell visszaadnia, hogy az tudja, hogy az érvényesítési eseményre vonatkozó bejegyzés el lett fogadva, mielőtt a manuális érvényesítés módba kerül. Más szóval, ha a végpont 200-as értéket ad vissza, de nem ad vissza programozott ellenőrzési választ, a mód a manuális ellenőrzési módba kerül. Ha 5 percen belül lekéri az érvényesítési URL-címet, az ellenőrzési kézfogás sikeresnek tekintendő.

> [!NOTE]
> Önaláírt tanúsítványok használata nem támogatott az érvényesítéshez. A hitelesítésszolgáltató (CA) által aláírt tanúsítványt használjon helyette.

### <a name="validation-details"></a>Az érvényesítés részletei

* Esemény előfizetés létrehozása és frissítése időpontjában Event Grid egy előfizetés érvényesítési esemény a cél-végpontot tesz közzé. 
* Az esemény tartalmaz egy fejléc értéke "AEG ügyet Eseménytípus: SubscriptionValidation".
* Az esemény törzsét, más Event Grid-események ugyanazzal a sémával rendelkezik.
* Az esemény eventType tulajdonsága `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Az esemény adattulajdonsága egy véletlenszerűen generált karakterlánccal rendelkező `validationCode` tulajdonságot tartalmaz. Például "validationCode: acb13 …".
* Az események között szerepel egy `validationUrl` tulajdonság is, amely egy URL-címet tartalmaz az előfizetés manuális érvényesítéséhez.
* A tömb csak az érvényesítési eseményt tartalmaz. Az eseményeket küld egy külön kérelmet echo vissza az érvényesítési kód után.
* A EventGrid-Adatsík SDK-k az előfizetés érvényesítése eseményadatok és előfizetés érvényesítési válaszhoz tartozó osztályok rendelkezik.

Példa SubscriptionValidationEvent az alábbi példában látható:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Végpont tulajdonjogának igazolásához, echo vissza a validationResponse tulajdonság érvényesítési kódja a következő példában látható módon:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Egy HTTP 200 OK-válasz állapotkódot kell visszaadnia. A HTTP 202 elfogadva nem ismerhető fel érvényes Event Grid előfizetés-ellenőrzési válaszként. A http-kérelemnek 30 másodpercen belül el kell végeznie. Ha a művelet 30 másodpercen belül nem fejeződik be, akkor a rendszer megszakítja a műveletet, és 5 másodperc elteltével újra próbálkozik. Ha az összes próbálkozás sikertelen, akkor az érvényesítési kézfogási hibaként lesz kezelve.

Vagy manuálisan ellenőrizheti az előfizetés egy GET kérést küld az érvényesítési URL-címet. Az esemény-előfizetés mindaddig, amíg érvényesítve függő állapotban marad. Az érvényesítési URL-cím a 553-es portot használja. Ha a tűzfalszabályok letiltják a 553-es portot, akkor előfordulhat, hogy a rendszernek frissítenie kell a sikeres manuális kézfogásra vonatkozó szabályokat.

Az előfizetés-ellenőrzési kézfogás kezelésére példaként tekintse meg a [ C# mintát](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Ellenőrzőlista

Ha az esemény-előfizetés létrehozása során hibaüzenet jelenik meg, például: "a megadott végpont érvényesítésére tett kísérlet (https):\//Your-Endpoint-here sikertelen volt. További részletekért látogasson el a https:\//aka.ms/esvalidation "kifejezésre, amely azt jelzi, hogy hiba történt az érvényesítési kézfogásban. Ez a hiba elhárításához ellenőrizze a következő szempontokat:

* Ellenőrzi a célként megadott végponton futó alkalmazás kódját? Például ha szeretne írni egy HTTP-eseményindító-alapú Azure-függvény, rendelkezik az alkalmazás kódja, módosíthatja a hozzáférést?
* Ha rendelkezik hozzáféréssel az alkalmazáskód, a alapú ValidationCode kézfogás mechanizmus megvalósításához, a fenti példában látható módon.

* Ha Ön nem rendelkezik hozzáféréssel az alkalmazáskód (például, ha egy külső szolgáltatás, amely támogatja a webhookok használata), a manuális kézfogás mechanizmust is használhatja. Ellenőrizze, hogy a 2018-05-01-preview API-verzió vagy újabb (telepítés Event Grid Azure CLI-bővítmény)-t használ a validationUrl kapják meg az érvényesítési esemény. A manuális ellenőrzési kézfogás befejezéséhez szerezze be a `validationUrl` tulajdonság értékét, és keresse meg az URL-címet a böngészőben. Sikeres ellenőrzés esetén megjelenik egy üzenet a böngészőben, hogy az ellenőrzés nem jelez. Látni fogja, hogy esemény-előfizetés provisioningState van "sikeres". 

### <a name="event-delivery-security"></a>Kézbesítési biztonsági esemény

#### <a name="azure-ad"></a>Azure AD

Azure Active Directory használatával biztonságossá teheti a webhook-végpontot, hogy hitelesítse és engedélyezze Event Grid az események a végpontokra való közzétételéhez. Létre kell hoznia egy Azure Active Directory alkalmazást, létre kell hoznia egy szerepkört és egy szolgáltatási elvet az alkalmazásban, amely engedélyezi a Event Grid, és az esemény-előfizetést az Azure AD-alkalmazás használatára konfigurálja. [Ismerje meg, hogyan konfigurálhatja a HRE a Event Grid használatával](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Lekérdezési paraméterek
Egy esemény-előfizetés létrehozásakor lekérdezési paraméterek hozzáadásával a webhook URL-cím gondoskodhat a webhook-végpontot. A lekérdezési paraméterek egyikének megadásával titkos, például [hozzáférési jogkivonat](https://en.wikipedia.org/wiki/Access_token)lehet. A webhook a titkos kulcs használatával ismeri fel az esemény érkezik Event Grid érvényes engedélyekkel. Event Grid a webhook minden eseménykézbesítés tartalmazza a lekérdezési paraméterek.

Az esemény-előfizetés szerkesztésekor a lekérdezési paraméterek nem jelennek meg, és csak akkor jelennek meg, ha a [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) paramétert használja az Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)-ben.

Végezetül fontos megjegyezni, hogy csak az Azure Event Grid támogatja a webhook koncové body protokolu HTTPS.

## <a name="event-subscription"></a>Esemény-előfizetés

Feliratkozás egy eseményt, igazolnia kell, hogy a forrás és a kezelő a hozzáférést. Igazolja, hogy a WebHook saját az előző szakaszban ismertetett volt. Egy eseménykezelő, amely egy WebHook (például egy event hub vagy az queue storage) nem használja, ha ennek az erőforrásnak írási hozzáférés szükséges. Az engedélyek ellenőrzését egy jogosulatlan felhasználó megakadályozza az események küldése az erőforrás.

Az eseményforrás erőforrásának a **Microsoft. EventGrid/EventSubscriptions/Write** engedéllyel kell rendelkeznie. Szüksége erre az engedélyre, mert a ír be egy új előfizetést az erőforrás a hatókörben. A szükséges erőforrás eltér attól még egy rendszer témakört vagy egy egyéni témakör előfizetés alapján. Ebben a szakaszban mindkét fajta ismerteti.

### <a name="system-topics-azure-service-publishers"></a>Rendszer témakörök (az Azure service közzétevők)

A rendszer témaköröket szüksége van egy új esemény-előfizetés a hatókörben az erőforrás az esemény közzétételének írási engedéllyel. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Ha például egy **myacct**nevű Storage-fiókra szeretne előfizetni egy eseményre, szüksége lesz a Microsoft. EventGrid/EventSubscriptions/Write engedélyre a következő címen: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Egyéni témakörök

Egyéni témakörök szüksége lesz egy új esemény-előfizetés a hatókörben, az event grid-témakör írási engedéllyel. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Ha például egy **mytopic**nevű egyéni témakörre szeretne előfizetni, a következő címen kell megadnia a Microsoft. EventGrid/EventSubscriptions/Write engedélyt: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Közzététel egyéni témakörben

Egyéni témakörök közös hozzáférésű Jogosultságkód (SAS) vagy a kulcs alapú hitelesítés használata. Azt javasoljuk, hogy a SAS, de kulcsos hitelesítés egyszerű programozási biztosít, és számos meglévő webhook-közzétevők kompatibilis. 

A hitelesítési érték szerepel a HTTP-fejléc. SAS esetében használja az **AEG-sas-tokent** a fejléc értékéhez. A kulcsos hitelesítéshez használja az **AEG-sas-Key** értéket a fejléc értékeként.

### <a name="key-authentication"></a>Kulcsos hitelesítés

Kulcs alapú hitelesítés a hitelesítés legegyszerűbb formája. A formátumot használja: `aeg-sas-key: <your key>`

Például adja át a kulcsot:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokenek

Az Event Gridhez SAS-tokeneket tartalmazza az erőforrás lejárati időt és aláírás. Az SAS-token formátuma: `r={resource}&e={expiration}&s={signature}`.

Az erőforrás elérési útja, amelyhez eseményt küld event grid-témakör. Egy érvényes erőforrás elérési útja például a következő: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Az aláírás-kulcsból hozza létre.

Egy érvényes **AEG-sas-token** érték például a következő:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Az alábbi példa létrehoz egy SAS-token használható az Event GRID használatával:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

A Event Grid szolgáltatás által lemezre írt összes eseményt vagy adatfájlt egy Microsoft által felügyelt kulcs titkosítja, amely biztosítja, hogy az inaktív állapotban legyen titkosítva. Emellett az események vagy az adatmegőrzési időszak maximális időtartama 24 óra az [Event Grid újrapróbálkozási házirend](delivery-and-retry.md)betartásával. A Event Grid 24 óra elteltével automatikusan törli az összes eseményt vagy az adatmennyiséget, vagy az esemény élettartama, attól függően, hogy melyik a kisebb.

## <a name="next-steps"></a>Következő lépések

* A Event Grid bevezetését lásd: [About Event Grid](overview.md)
