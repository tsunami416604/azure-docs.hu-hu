---
title: Biztonság és hitelesítés Azure Event Grid
description: Ez a cikk a Event Grid-erőforrásokhoz való hozzáférés hitelesítésének különböző módszereit ismerteti (webhook, előfizetések, egyéni témakörök)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532393"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Event Grid erőforrásokhoz való hozzáférés hitelesítése

A Azure Event Grid háromféle hitelesítési típust tartalmaz:

- Webhook-esemény kézbesítése
- Esemény-előfizetések
- Egyéni témakör közzététele

## <a name="webhook-event-delivery"></a>Webhook-esemény kézbesítése

A webhookok egyike a Azure Event Grid események fogadásának számos módja. Ha egy új esemény elkészült, Event Grid a szolgáltatás HTTP-kérelmet küld a konfigurált végpontnak a kérelem törzsében lévő eseménnyel.

A webhookokat támogató számos más szolgáltatáshoz hasonlóan a Event Grid megköveteli, hogy igazolja a webhook-végpont tulajdonosát, mielőtt megkezdi az események továbbítását a végpontnak. Ez a követelmény megakadályozza, hogy egy rosszindulatú felhasználó elárasztsa a végpontot az eseményekkel. Ha az alább felsorolt három Azure-szolgáltatás bármelyikét használja, az Azure-infrastruktúra automatikusan kezeli ezt az ellenőrzést:

- Azure Logic Apps [Event Grid-összekötővel](https://docs.microsoft.com/connectors/azureeventgrid/)
- Azure Automation [webhookon](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) keresztül
- Azure Functions [Event Grid triggerrel](../azure-functions/functions-bindings-event-grid.md)

Ha bármilyen más típusú végpontot használ, például egy HTTP-triggeren alapuló Azure-függvényt, a végponti kódnak a Event Grid használatával kell részt vennie egy érvényesítési kézfogásban. Event Grid az előfizetés érvényesítésének két módját támogatja.

1. **Szinkron kézfogás**: az esemény-előfizetés létrehozásakor Event Grid elküld egy előfizetés-ellenőrzési eseményt a végpontnak. Az esemény sémája hasonló a többi Event Grid eseményhez. Az esemény adatrésze tartalmaz egy `validationCode` tulajdonságot. Az alkalmazás ellenőrzi, hogy az érvényesítési kérelem a várt esemény-előfizetésre van-e kiválasztva, és a válaszban szereplő érvényesítési kódot adja vissza szinkron módon. Ez a kézfogási mechanizmus minden Event Grid verzióban támogatott.

2. **Aszinkron kézfogás**: bizonyos esetekben nem lehet szinkron módon visszaadni a ValidationCode. Ha például harmadik féltől származó szolgáltatást (például [`Zapier`](https://zapier.com) vagy [IFTTT](https://ifttt.com/)) használ, az érvényesítési kóddal nem lehet programozott módon válaszolni.

   Az 2018-05-01-es verziótól kezdődően a Event Grid támogatja a manuális ellenőrzési kézfogást. Ha olyan SDK-val vagy eszközzel hoz létre egy esemény-előfizetést, amely az 2018-05-01-es vagy újabb API- `validationUrl` verziót használja, akkor Event Grid egy tulajdonságot küld az előfizetés-ellenőrzési esemény adatrészében. A kézfogás elvégzéséhez keresse meg az adott URL-címet az eseményekhez, és tegye meg a GET-kérést. Használhatja a REST-ügyfelet vagy a webböngészőt is.

   A megadott URL-cím **5 percig**érvényes. Ebben az időszakban az esemény-előfizetés kiépítési állapota a következő: `AwaitingManualAction`. Ha nem hajtja végre a manuális ellenőrzést 5 percen belül, a kiépítési állapot értéke: `Failed`. A manuális ellenőrzés megkezdése előtt újra létre kell hoznia az esemény-előfizetést.

   Ehhez a hitelesítési mechanizmushoz a webhook-végpontnak egy 200-es HTTP-állapotkódot kell visszaadnia, hogy az tudja, hogy az érvényesítési eseményre vonatkozó bejegyzés el lett fogadva, mielőtt a manuális érvényesítés módba kerül. Más szóval, ha a végpont 200-as értéket ad vissza, de nem ad vissza egy érvényesítési válasz szinkron módon történő visszaadását, a mód a manuális ellenőrzési módba kerül. Ha 5 percen belül lekéri az érvényesítési URL-címet, az ellenőrzési kézfogás sikeresnek tekintendő.

> [!NOTE]
> Önaláírt tanúsítványok használata nem támogatott az érvényesítéshez. A hitelesítésszolgáltató (CA) által aláírt tanúsítványt használjon helyette.

### <a name="validation-details"></a>Érvényesítés részletei

- Az esemény-előfizetés létrehozása/frissítése során Event Grid egy előfizetés-ellenőrzési eseményt könyvel a célként megadott végpontra.
- Az esemény egy "AEG-Event-Type: SubscriptionValidation" fejléc-értéket tartalmaz.
- Az esemény törzsének ugyanaz a sémája, mint a többi Event Grid eseménynek.
- Az esemény eventType tulajdonsága `Microsoft.EventGrid.SubscriptionValidationEvent`.
- Az esemény adattulajdonsága egy véletlenszerűen generált `validationCode` karakterlánccal rendelkező tulajdonságot tartalmaz. Például: "validationCode: acb13...".
- Az eseményhez tartozik egy `validationUrl` tulajdonság is, amely egy URL-címet tartalmaz az előfizetés manuális érvényesítéséhez.
- A tömb csak az érvényesítési eseményt tartalmazza. A többi eseményt külön kérelemben küldi el a rendszer az érvényesítési kód visszhangjának visszalépése után.
- A EventGrid Adatsík SDK-k az előfizetés-ellenőrzési esemény és az előfizetés-ellenőrzési válasznak megfelelő osztályokkal rendelkeznek.

A következő példában egy SubscriptionValidationEvent látható:

```json
[
  {
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
  }
]
```

A végpont tulajdonjogának bizonyításához ECHO a validationResponse tulajdonságban található érvényesítési kódot az alábbi példában látható módon adja vissza:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Egy HTTP 200 OK-válasz állapotkódot kell visszaadnia. A HTTP 202 elfogadva nem ismerhető fel érvényes Event Grid előfizetés-ellenőrzési válaszként. A http-kérelemnek 30 másodpercen belül el kell végeznie. Ha a művelet 30 másodpercen belül nem fejeződik be, akkor a rendszer megszakítja a műveletet, és 5 másodperc elteltével újra próbálkozik. Ha az összes próbálkozás sikertelen, akkor az érvényesítési kézfogási hibaként lesz kezelve.

Vagy manuálisan is érvényesítheti az előfizetést egy GET kérelem küldésével az érvényesítési URL-címre. Az esemény-előfizetés függő állapotban marad mindaddig, amíg az érvényesítés be nem fejeződik. Az érvényesítési URL-cím a 553-es portot használja. Ha a tűzfalszabályok letiltják a 553-es portot, akkor előfordulhat, hogy a rendszernek frissítenie kell a sikeres manuális kézfogásra vonatkozó szabályokat.

Az előfizetés-ellenőrzési kézfogás kezelésére példát a [C#-minta](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)című témakörben talál.

## <a name="troubleshooting-eventsubsciption-validation"></a>EventSubsciption-ellenőrzés hibaelhárítása

Ha az esemény-előfizetés létrehozása során hibaüzenet jelenik meg, például: "a megadott végpont hitelesítésének megkísérlése:\//Your-Endpoint-here sikertelen. További részletekért látogasson el a\/https:/aka.MS/esvalidation nevű oldalra, amely azt jelzi, hogy hiba történt az érvényesítési kézfogásban. A hiba megoldásához ellenőrizze a következő szempontokat:

- Végezzen HTTP-BEJEGYZÉST a webhook URL-címére a Poster vagy a curl vagy hasonló eszköz használatával egy [minta SubscriptionValidationEvent](#validation-details) -kérelem Törzsével.
- Ha a webhook szinkron ellenőrzési kézfogási mechanizmust valósít meg, ellenőrizze, hogy a ValidationCode a válasz részeként adja-e vissza.
- Ha a webhook aszinkron ellenőrzési kézfogási mechanizmust valósít meg, ellenőrizze, hogy a HTTP-bejegyzés 200 OK-e.
- Ha a webhook 403 (tiltott) értékkel tér vissza a válaszban, ellenőrizze, hogy a webhook egy Azure Application Gateway vagy webalkalmazási tűzfal mögött van-e. Ha igen, akkor le kell tiltania ezeket a tűzfalszabályok, és végre kell hajtania a HTTP-KÖZZÉTÉTELt:

  920300 (a kérelemből hiányzik egy Accept fejléc, ezt kijavíthatjuk)

  942430 (korlátozott SQL-karakteres anomáliák észlelése (argumentumok): a speciális karakterek száma túllépve (12)

  920230 (több URL-kódolás észlelhető)

  942130 (SQL injection támadás: az SQL-tautológia észlelve.)

  931130 (lehetséges távoli fájlok felvételének (RFI) támadása = off-domain Reference/link)

### <a name="event-delivery-security"></a>Esemény-kézbesítési biztonság

#### <a name="azure-ad"></a>Azure AD

Azure Active Directory használatával biztonságossá teheti a webhook-végpontot, hogy hitelesítse és engedélyezze Event Grid az események a végpontokra való közzétételéhez. Létre kell hoznia egy Azure Active Directory alkalmazást, létre kell hoznia egy szerepkört és egy szolgáltatási elvet az alkalmazásban, amely engedélyezi a Event Grid, és az esemény-előfizetést az Azure AD-alkalmazás használatára konfigurálja. [Ismerje meg, hogyan konfigurálhatja a HRE a Event Grid használatával](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Lekérdezési paraméterek

A webhook-végpont biztonságossá tételéhez lekérdezési paramétereket adhat hozzá a webhook URL-címéhez az esemény-előfizetés létrehozásakor. A lekérdezési paraméterek egyikének megadásával titkos, például [hozzáférési jogkivonat](https://en.wikipedia.org/wiki/Access_token)lehet. A webhook használhatja a titkos kulcsot, hogy felismerje az eseményt, Event Grid érvényes engedélyekkel érkezik. A Event Grid a következő lekérdezési paramétereket fogja tartalmazni minden eseménynek a webhookhoz való továbbításakor.

Az esemény-előfizetés szerkesztésekor a lekérdezési paraméterek nem jelennek meg, és csak akkor jelennek meg, ha a [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) paramétert használja az Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)-ben.

Végezetül fontos megjegyezni, hogy Azure Event Grid csak a HTTPS webhook-végpontokat támogatja.

## <a name="event-subscription"></a>Esemény-előfizetés

Egy eseményre való előfizetéshez bizonyítania kell, hogy van hozzáférése az eseményforrás és a kezelőhöz. Az előző szakaszban leírtak szerint a webhook tulajdonosa is szerepelt. Ha olyan eseménykezelőt használ, amely nem webhook (például egy Event hub vagy várólista-tároló), írási hozzáféréssel kell rendelkeznie az adott erőforráshoz. Ez az engedély-ellenőrzési funkció megakadályozza, hogy jogosulatlan felhasználó küldjön eseményeket az erőforrásba.

Az eseményforrás erőforrásának a **Microsoft. EventGrid/EventSubscriptions/Write** engedéllyel kell rendelkeznie. Erre az engedélyre azért van szükség, mert az erőforrás hatókörében új előfizetést ír. A szükséges erőforrás különbözik attól függően, hogy egy rendszertémakörre vagy egy egyéni témakörre való feliratkozásra van-e szükség. Ebben a szakaszban mindkét típust ismertetjük.

### <a name="system-topics-azure-service-publishers"></a>Rendszertémakörök (Azure-szolgáltatás közzétevői)

A rendszertémakörökhöz engedély szükséges ahhoz, hogy új esemény-előfizetést írjon az eseményt közzétevő erőforrás hatókörén. Az erőforrás formátuma:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Ha például egy **myacct**nevű Storage-fiókra szeretne előfizetni egy eseményre, szüksége lesz a Microsoft. EventGrid/EventSubscriptions/Write engedélyre a következőn:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Egyéni témakörök

Egyéni témakörök esetén engedély szükséges ahhoz, hogy új esemény-előfizetést írjon az Event Grid-témakör hatókörébe. Az erőforrás formátuma:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Ha például egy **mytopic**nevű egyéni témakörre szeretne előfizetni, szüksége lesz a Microsoft. EventGrid/EventSubscriptions/Write engedélyre a következőn:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Egyéni témakör közzététele

Az egyéni témakörök megosztott elérési aláírást (SAS) vagy kulcsos hitelesítést használnak. A SAS használatát javasoljuk, de a kulcsos hitelesítés egyszerű programozást tesz lehetővé, és számos meglévő webhook-közzétevővel kompatibilis.

Adja meg a hitelesítési értéket a HTTP-fejlécben. SAS esetében használja az **AEG-sas-tokent** a fejléc értékéhez. A kulcsos hitelesítéshez használja az **AEG-sas-Key** értéket a fejléc értékeként.

### <a name="key-authentication"></a>Kulcsos hitelesítés

A kulcsos hitelesítés a hitelesítés legegyszerűbb formája. Használja a formátumot:`aeg-sas-key: <your key>`

Egy kulcsot például a következővel adhat át:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokenek

A Event Grid SAS-jogkivonatai közé tartozik az erőforrás, a lejárati idő és az aláírás. Az SAS-token formátuma: `r={resource}&e={expiration}&s={signature}`.

Az erőforrás az Event Grid-témakör elérési útja, amelyhez eseményeket küld. Egy érvényes erőforrás elérési útja például a következő `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`:. Az összes támogatott API-verzió megtekintéséhez lásd: [Microsoft. EventGrid erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Az aláírást egy kulcsból kell előállítani.

Egy érvényes **AEG-sas-token** érték például a következő:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Az alábbi példa egy SAS-tokent hoz létre a Event Gridhoz való használatra:

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

## <a name="endpoint-validation-with-cloudevents-v10"></a>Végpont ellenőrzése a CloudEvents 1.0-s verziójában
Ha már ismeri a Event Gridt, érdemes lehet Event Grid végpont-ellenőrzési kézfogását a visszaélések megelőzésére. A CloudEvents 1.0-s verziójában a HTTP-beállítások módszer használatával valósítja meg a saját [visszaélés elleni védelem szemantikai](security-authentication.md#webhook-event-delivery) beállításait. További információk [itt](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ha a kimenethez a CloudEvents sémát használja, a Event Grid a Event Grid érvényesítési esemény mechanizmusa helyett az CloudEvents 1.0-s verziójának védelme szolgáltatást használja.

## <a name="next-steps"></a>További lépések

- A Event Grid bevezetését lásd: [About Event Grid](overview.md)
