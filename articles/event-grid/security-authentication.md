---
title: Az Azure Event Grid biztonsága és hitelesítése
description: Ez a cikk az Event Grid-erőforrásokhoz (WebHook, előfizetések, egyéni témakörök) való hozzáférés hitelesítésének különböző módjait ismerteti.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532393"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Hozzáférés hitelesítése az Eseményrács erőforrásaihoz

Az Azure Event Grid háromféle hitelesítési típust rendelkezik:

- WebHook esemény kézbesítése
- Esemény-előfizetések
- Egyéni témakör közzététele

## <a name="webhook-event-delivery"></a>WebHook-esemény kézbesítése

A webhookok az Azure Event Gridből érkező események fogadásának egyik módja. Amikor egy új esemény készen áll, az Event Grid szolgáltatás POSTs egy HTTP-kérelmet a konfigurált végpont az esemény a kérelem törzsében.

Sok más, webhookokat támogató szolgáltatáshoz hasonlóan az Event Grid megköveteli, hogy bizonyítsa a Webhook-végpont tulajdonjogát, mielőtt az eseményeket az adott végpontra hozná. Ez a követelmény megakadályozza, hogy egy rosszindulatú felhasználó elárasztsa a végpontot eseményekkel. Az alábbiakban felsorolt három Azure-szolgáltatás bármelyikének használatakor az Azure-infrastruktúra automatikusan kezeli ezt az ellenőrzést:

- Azure Logic-alkalmazások [eseményrács-összekötővel](https://docs.microsoft.com/connectors/azureeventgrid/)
- Azure Automation [webhookon](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) keresztül
- Azure-függvények [eseményrács-eseményindítóval](../azure-functions/functions-bindings-event-grid.md)

Ha bármilyen más típusú végpontot használ, például egy HTTP-eseményindító alapú Azure-függvényt, a végpontkódnak részt kell vennie az Event Griddel való érvényesítési kézfogásban. Az Event Grid két módon támogatja az előfizetés érvényesítését.

1. **Szinkron kézfogás:** Az esemény-előfizetés létrehozásakor az Event Grid egy előfizetés-érvényesítési eseményt küld a végpontnak. Az esemény sémája hasonló bármely más Event Grid-eseményhez. Az esemény adatrésze tartalmaz `validationCode` egy tulajdonságot. Az alkalmazás ellenőrzi, hogy az érvényesítési kérelem egy várt esemény-előfizetéshez, és szinkron módon adja vissza az érvényesítési kódot a válaszban. Ez a kézfogási mechanizmus az Event Grid összes verziója támogatja.

2. **Aszinkron kézfogás:** Bizonyos esetekben nem tudja visszaadni a ValidationCode válaszszinkron módon. Ha például egy külső szolgáltatást (például [`Zapier`](https://zapier.com) vagy [IFTTT-t)](https://ifttt.com/)használ, nem tud programozott módon válaszolni az érvényesítési kóddal.

   A 2018-05-01-preview verziótól kezdve az Event Grid támogatja a manuális érvényesítési kézfogást. Ha egy esemény-előfizetést hoz létre egy SDK-val vagy olyan eszközzel, amely a 2018-05-01-preview API-verziót vagy újabb verziót használja, az Event Grid egy tulajdonságot `validationUrl` küld az előfizetés-érvényesítési esemény adatrészében. A kézfogás befejezéséhez keresse meg az URL-címet az eseményadatokközött, és kérjen get-kérést. Használhatja a REST-ügyfelet vagy a webböngészőt.

   A megadott URL **5 percig**érvényes. Ez alatt az idő alatt az esemény-előfizetés létesítési állapota. `AwaitingManualAction` Ha 5 percen belül nem fejezi be a manuális érvényesítést, a létesítési állapot `Failed`a. A manuális ellenőrzés megkezdése előtt újra létre kell hoznia az esemény-előfizetést.

   Ez a hitelesítési mechanizmus azt is megköveteli, hogy a webhook-végpont 200-as HTTP-állapotkódot ad vissza, hogy tudja, hogy az érvényesítési esemény POST-kódja elfogadásra került, mielőtt manuális érvényesítési módba helyezhető volna. Más szóval, ha a végpont 200-at ad vissza, de nem ad vissza érvénytelenítő módon érvényesítési válaszértéket, a mód manuális érvényesítési módba vált. Ha 5 percen belül get van az érvényesítési URL-en, az érvényesítési kézfogás sikeresnek minősül.

> [!NOTE]
> Az önaláírt tanúsítványok használata az ellenőrzéshez nem támogatott. Ehelyett használjon egy hitelesítésszolgáltatótól származó aláírt tanúsítványt.

### <a name="validation-details"></a>Érvényesítésrészletei

- Az esemény-előfizetés létrehozásakor/frissítésénél az Event Grid egy előfizetés-érvényesítési eseményt tesz közzé a célvégpontra.
- Az esemény egy "aeg-event-type: SubscriptionValidation" fejlécértéket tartalmaz.
- Az eseménytörzs sémája megegyezik a többi Event Grid-eseményeéval.
- Az `Microsoft.EventGrid.SubscriptionValidationEvent`esemény eventType tulajdonsága .
- Az esemény adattulajdonsága `validationCode` egy véletlenszerűen generált karakterlánccal rendelkező tulajdonságot tartalmaz. Például: "validationCode: acb13...".
- Az eseményadatok egy `validationUrl` olyan tulajdonságot is tartalmaznak, amelynek URL-címe van az előfizetés manuális érvényesítéséhez.
- A tömb csak az érvényesítési eseményt tartalmazza. A többi eseményt külön kérelemben küldi el a rendszer, miután visszaküldte az érvényesítési kódot.
- Az EventGrid DataPlane SDK-k az előfizetés-érvényesítési eseményadatoknak és az előfizetés-érvényesítési válasznak megfelelő osztályokkal rendelkeznek.

Egy példa SubscriptionValidationEvent látható a következő példában:

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

A végpont tulajdonjogának bizonyításához a következő példában látható módon ismét visszakell adnia az érvényesítési kódot a validationResponse tulajdonságban:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Http 200 OK válaszállapotkódot kell visszaadnia. Az Elfogadott HTTP 202 nem ismeri fel érvényes Event Grid-előfizetés-érvényesítési válaszként. A http-kérelemnek 30 másodpercen belül be kell fejeződnie. Ha a művelet nem fejeződik be 30 másodpercen belül, akkor a művelet megszakad, és 5 másodperc elteltével újra megkísérthető. Ha az összes kísérlet sikertelen, akkor a rendszer érvényesítési kézfogási hibaként kezeli.

Vagy manuálisan is érvényesítheti az előfizetést, ha get-kérelmet küld az érvényesítési URL-címre. Az esemény-előfizetés függőben marad, amíg érvényesítik. Az érvényesítési URL az 553-as portot használja. Ha a tűzfalszabályok blokkolják az 553-as portot, akkor a sikeres manuális kézfogáshoz szükség lehet a szabályok frissítésére.

Az előfizetés-érvényesítési kézfogás kezelésének például tekintse meg a [C# minta című témakört.](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)

## <a name="troubleshooting-eventsubsciption-validation"></a>EventSubsciption érvényesítésének hibaelhárítása

Az esemény-előfizetés létrehozása során, ha egy hibaüzenet jelenik meg, például\/:"A megadott végpont érvényesítésének kísérlete https: /your-endpoint-here failed. További részletekért látogasson\/el a https: /aka.ms/esvalidation", azt jelzi, hogy az érvényesítési kézfogás hiba történt. A hiba elhárításához ellenőrizze az alábbi szempontokat:

- A Http-posta a webhook URL-t egy [minta SubscriptionValidationEvent](#validation-details) kérelem törzs segítségével Postman vagy curl vagy hasonló eszköz.
- Ha a webhook szinkron érvényesítési kézfogási mechanizmust valósít meg, ellenőrizze, hogy a ValidationCode a válasz részeként ad vissza.
- Ha a webhook aszinkron érvényesítési kézfogási mechanizmust valósít meg, ellenőrizze, hogy ön-e a HTTP POST 200 OK-t ad vissza.
- Ha a webhook 403-as (Tiltott) a válaszban, ellenőrizze, hogy a webhook mögött egy Azure Application Gateway vagy webalkalmazás tűzfala. Ha igen, akkor le kell tiltania ezeket a tűzfalszabályokat, és újra meg kell tennie a HTTP POST-ot:

  920300 (Kérés hiányzik egy Accept Header, tudjuk kijavítani ezt)

  942430 (Korlátozott SQL-karakteranomáliadetektálás (args): # a speciális karakterek túllépve (12))

  920230 (több URL-kódolás észlelve)

  942130 (SQL injection attack: SQL Tautology észlelt.)

  931130 (lehetséges távoli fájlfelvétel (RFI) támadás = tartományon kívüli hivatkozás/hivatkozás)

### <a name="event-delivery-security"></a>Eseménykézbesítés biztonsága

#### <a name="azure-ad"></a>Azure AD

A webhook-végpont biztonságossá teheti az Azure Active Directory használatával hitelesítheti és engedélyezheti az Event Grid események közzétételét a végpontokon. Létre kell hoznia egy Azure Active Directory-alkalmazást, létre kell hoznia egy szerepkör- és szolgáltatáselvet az alkalmazásban, amely engedélyezi az Event Gridet, és konfigurálnia kell az esemény-előfizetést az Azure AD-alkalmazás használatára. [További információ az AAD beállításáról az Event Grid](secure-webhook-delivery.md)segítségével.

#### <a name="query-parameters"></a>Lekérdezési paraméterek

A webhook-végpont biztonságossá teheti a lekérdezési paraméterek hozzáadása a webhook URL-cím létrehozásakor egy esemény-előfizetés. Állítsa be az egyik lekérdezési paramétert titkos kulcsként, például [hozzáférési jogkivonatként.](https://en.wikipedia.org/wiki/Access_token) A webhook használhatja a titkos felismerni az esemény érkezik Event Grid érvényes engedélyekkel. Event Grid tartalmazza ezeket a lekérdezési paramétereket minden esemény kézbesítése a webhook.

Az Esemény-előfizetés szerkesztésekor a lekérdezési paraméterek csak akkor jelennek meg, illetve nem adnak vissza, ha az Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) [-include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) paramétert használja.

Végül fontos megjegyezni, hogy az Azure Event Grid csak https webhook-végpontokat támogat.

## <a name="event-subscription"></a>Esemény-előfizetés

Egy eseményre való feliratkozáshoz bizonyítania kell, hogy hozzáférése van az eseményforráshoz és -kezelőhöz. Annak bizonyítása, hogy a WebHook a webhook az előző szakaszban. Ha olyan eseménykezelőt használ, amely nem WebHook (például eseményközpont vagy várólista-tároló), írási hozzáféréssel kell rendelkeznie az erőforráshoz. Ez az engedélyellenőrzés megakadályozza, hogy egy jogosulatlan felhasználó eseményeket küldjön az erőforrásnak.

A **Microsoft.EventGrid/EventSubscriptions/Write** engedéllyel kell rendelkeznie az eseményforrásként lévő erőforráshoz. Erre az engedélyre azért van szüksége, mert új előfizetést ír az erőforrás hatókörén. A szükséges erőforrás attól függően változik, hogy rendszertémakörre vagy egyéni témakörre iratkozik-e fel. Mindkét típust ebben a szakaszban ismerteti.

### <a name="system-topics-azure-service-publishers"></a>Rendszertémakörök (Azure-szolgáltatás közzétevői)

Rendszertémakörök esetén engedélyt kell kérnie egy új esemény-előfizetés írásához az eseményt közzéíró erőforrás hatókörén keresztül. Az erőforrás formátuma:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Ha például elő szeretne fizetni egy **myacct**nevű tárfiók egy eseményére, akkor a Microsoft.EventGrid/EventSubscriptions/Write engedélyre van szüksége:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Egyéni témakörök

Egyéni témakörök esetén engedélyt kell kérnie egy új esemény-előfizetés írásához az eseményrács-témakör hatókörében. Az erőforrás formátuma:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Ha például elő szeretne fizetni egy **mytopic**nevű egyéni témakörre, a Microsoft.EventGrid/EventSubscriptions/Write engedélyre van szüksége:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Egyéni témakör közzététele

Az egyéni témakörök a megosztott hozzáférésű aláírást (SAS) vagy a kulcshitelesítést használják. SAS-t ajánlunk, de a kulcshitelesítés egyszerű programozást biztosít, és számos meglévő webhook-gyártóval kompatibilis.

A hitelesítési értéket a HTTP fejlécben is felkell tüntetni. A SAS-hoz használja **az aeg-sas-token** a fejléc érték. A kulcshitelesítéshez használja **az aeg-sas-key-t** a fejlécértékhez.

### <a name="key-authentication"></a>Kulcshitelesítés

A kulcshitelesítés a hitelesítés legegyszerűbb formája. A következő formátumhasználata:`aeg-sas-key: <your key>`

Például a következő vel ad át egy kulcsot:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokenek

Az Event Grid SAS-jogkivonatai tartalmazzák az erőforrást, a lejárati időt és az aláírást. A SAS-token formátuma: `r={resource}&e={expiration}&s={signature}`.

Az erőforrás annak az eseményrács-témakörnek az elérési útja, amelyre eseményeket küld. Egy érvényes erőforráselérési út `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`például a következő: . Az összes támogatott API-verziót a [Microsoft.EventGrid erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)című témakörben láthatja. 

Az aláírást egy kulcsból hozza létre.

Például egy érvényes **aeg-sas-token** érték:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

A következő példa létrehoz egy SAS-jogkivonatot az Event Griddel való használatra:

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

Az Event Grid szolgáltatás által lemezre írt összes eseményt vagy adatot egy Microsoft által felügyelt kulcs titkosítja, amely biztosítja, hogy az inkban van titkosítva. Ezenkívül az események vagy adatok által megőrzött maximális időtartam 24 óra az [Event Grid újrapróbálkozási házirendjének](delivery-and-retry.md)betartásával. Az Event Grid 24 óra elteltével automatikusan törli az összes eseményt vagy adatot, vagy az esemény az élő adást, attól függően, hogy melyik a kisebb.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Végpont-érvényesítés a CloudEvents 1.0-s verzióját alkalmazva
Ha már ismeri az Event Gridet, előfordulhat, hogy ismeri az Event Grid végpontérvényesítési kézfogását a visszaélések megelőzése érdekében. A CloudEvents 1.0-s verziójásaját [visszaélésvédelmi szemantikát](security-authentication.md#webhook-event-delivery) valósít meg a HTTP OPTIONS metódus használatával. További információk [itt](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). A CloudEvents séma kimeneti használatakor az Event Grid a CloudEvents 1.0-s verzióját használja az Event Grid érvényesítési eseménymechanizmusa helyett.

## <a name="next-steps"></a>További lépések

- Az Eseményrács bemutatása az [Eseményrács – bemutatkozása:](overview.md)
