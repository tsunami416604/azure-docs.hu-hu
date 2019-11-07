---
title: Biztonság és hitelesítés Azure Event Grid
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: b9e471928940094b29bdffeb73ea42fe852492cb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665576"
---
# <a name="event-grid-security-and-authentication"></a>Biztonság és hitelesítés Event Grid 

A Azure Event Grid háromféle hitelesítési típust tartalmaz:

* Webhook-esemény kézbesítése
* Esemény-előfizetések
* Egyéni témakör közzététele

## <a name="webhook-event-delivery"></a>Webhook-esemény kézbesítése

A webhookok egyike a Azure Event Grid események fogadásának számos módja. Ha egy új esemény elkészült, Event Grid a szolgáltatás HTTP-kérelmet küld a konfigurált végpontnak a kérelem törzsében lévő eseménnyel.

A webhookokat támogató számos más szolgáltatáshoz hasonlóan a Event Grid megköveteli, hogy igazolja a webhook-végpont tulajdonosát, mielőtt megkezdi az események továbbítását a végpontnak. Ez a követelmény megakadályozza, hogy egy rosszindulatú felhasználó elárasztsa a végpontot az eseményekkel. Ha az alább felsorolt három Azure-szolgáltatás bármelyikét használja, az Azure-infrastruktúra automatikusan kezeli ezt az ellenőrzést:

* Azure Logic Apps [Event Grid-összekötővel](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation [webhookon](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) keresztül
* Azure Functions [Event Grid triggerrel](../azure-functions/functions-bindings-event-grid.md)

Ha bármilyen más típusú végpontot használ, például egy HTTP-triggeren alapuló Azure-függvényt, a végponti kódnak a Event Grid használatával kell részt vennie egy érvényesítési kézfogásban. Event Grid az előfizetés érvényesítésének két módját támogatja.

1. **ValidationCode-kézfogás (programozott)** : Ha a végpont forráskódját szabályozza, ez a módszer ajánlott. Az esemény-előfizetés létrehozásakor Event Grid elküld egy előfizetés-ellenőrzési eseményt a végpontnak. Az esemény sémája hasonló a többi Event Grid eseményhez. Az esemény adatrésze `validationCode` tulajdonságot tartalmaz. Az alkalmazás ellenőrzi, hogy az érvényesítési kérelem a várt esemény-előfizetésre vonatkozik-e, és megismétli az érvényesítési kódot a Event Grid. Ez a kézfogási mechanizmus minden Event Grid verzióban támogatott.

2. **ValidationURL kézfogás (manuális)** : bizonyos esetekben a végpont forráskódját nem érheti el a ValidationCode-kézfogás megvalósításához. Ha például harmadik féltől származó szolgáltatást (például [Zapier](https://zapier.com) vagy [IFTTT](https://ifttt.com/)) használ, az érvényesítési kóddal nem lehet programozott módon válaszolni.

   Az 2018-05-01-es verziótól kezdődően a Event Grid támogatja a manuális ellenőrzési kézfogást. Ha olyan SDK-val vagy eszközzel hoz létre egy esemény-előfizetést, amely az 2018-05-01-es vagy újabb API-verziót használja, akkor Event Grid egy `validationUrl` tulajdonságot küld az előfizetés-ellenőrzési esemény adatrészén. A kézfogás elvégzéséhez keresse meg az adott URL-címet az eseményben, és manuálisan küldje el a GET-kérést. Használhatja a REST-ügyfelet vagy a webböngészőt is.

   A megadott URL-cím 5 percig érvényes. Ebben az időszakban az esemény-előfizetés kiépítési állapota `AwaitingManualAction`. Ha nem hajtja végre a manuális ellenőrzést 5 percen belül, a kiépítési állapot értéke `Failed`. A manuális ellenőrzés megkezdése előtt újra létre kell hoznia az esemény-előfizetést.

    Ehhez a hitelesítési mechanizmushoz a webhook-végpontnak egy 200-es HTTP-állapotkódot kell visszaadnia, hogy az tudja, hogy az érvényesítési eseményre vonatkozó bejegyzés el lett fogadva, mielőtt a manuális érvényesítés módba kerül. Más szóval, ha a végpont 200-as értéket ad vissza, de nem ad vissza programozott ellenőrzési választ, a mód a manuális ellenőrzési módba kerül. Ha az érvényesítési URL-cím 5 percen belül beolvasható, az ellenőrzési kézfogás sikeresnek tekintendő.

> [!NOTE]
> Önaláírt tanúsítványok használata nem támogatott az érvényesítéshez. A hitelesítésszolgáltató (CA) által aláírt tanúsítványt használjon helyette.

### <a name="validation-details"></a>Érvényesítés részletei

* Az esemény-előfizetés létrehozása/frissítése során Event Grid egy előfizetés-ellenőrzési eseményt könyvel a célként megadott végpontra. 
* Az esemény egy "AEG-Event-Type: SubscriptionValidation" fejléc-értéket tartalmaz.
* Az esemény törzsének ugyanaz a sémája, mint a többi Event Grid eseménynek.
* Az esemény eventType tulajdonsága `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Az esemény adattulajdonsága egy véletlenszerűen generált karakterlánccal rendelkező `validationCode` tulajdonságot tartalmaz. Például: "validationCode: acb13...".
* Az események között szerepel egy `validationUrl` tulajdonság is, amely egy URL-címet tartalmaz az előfizetés manuális érvényesítéséhez.
* A tömb csak az érvényesítési eseményt tartalmazza. A többi eseményt külön kérelemben küldi el a rendszer az érvényesítési kód visszhangjának visszalépése után.
* A EventGrid Adatsík SDK-k az előfizetés-ellenőrzési esemény és az előfizetés-ellenőrzési válasznak megfelelő osztályokkal rendelkeznek.

A következő példában egy SubscriptionValidationEvent látható:

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

A végpont tulajdonjogának bizonyításához ECHO a validationResponse tulajdonságban található érvényesítési kódot az alábbi példában látható módon adja vissza:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Egy HTTP 200 OK-válasz állapotkódot kell visszaadnia. A HTTP 202 elfogadva nem ismerhető fel érvényes Event Grid előfizetés-ellenőrzési válaszként. A http-kérelemnek 30 másodpercen belül el kell végeznie. Ha a művelet 30 másodpercen belül nem fejeződik be, a rendszer megszakítja a műveletet, és 5 másodperc elteltével újra próbálkozik. Ha az összes próbálkozás sikertelen, akkor az érvényesítési kézfogási hibaként lesz kezelve.

Vagy manuálisan is érvényesítheti az előfizetést egy GET kérelem küldésével az érvényesítési URL-címre. Az esemény-előfizetés függő állapotban marad mindaddig, amíg az érvényesítés be nem fejeződik. Az érvényesítési URL-cím a 553-es portot használja. Ha a tűzfalszabályok letiltják a 553-es portot, akkor előfordulhat, hogy a rendszernek frissítenie kell a sikeres manuális kézfogásra vonatkozó szabályokat.

Az előfizetés-ellenőrzési kézfogás kezelésére példaként tekintse meg a [ C# mintát](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Ellenőrzőlista

Ha az esemény-előfizetés létrehozása során hibaüzenet jelenik meg, például: "a megadott végpont érvényesítésére tett kísérlet (https):\//Your-Endpoint-here sikertelen volt. További részletekért látogasson el a https:\//aka.ms/esvalidation "kifejezésre, amely azt jelzi, hogy hiba történt az érvényesítési kézfogásban. A hiba megoldásához ellenőrizze a következő szempontokat:

* Szabályozhatja az alkalmazás kódját a cél végponton? Ha például egy HTTP trigger-alapú Azure-függvényt írunk, hozzáférhet az alkalmazás kódjához, és módosíthatja azt?
* Ha rendelkezik hozzáféréssel az alkalmazás kódjához, hajtsa végre a ValidationCode-alapú kézfogási mechanizmust a fenti mintában látható módon.

* Ha nem fér hozzá az alkalmazás kódjához (például ha olyan külső szolgáltatást használ, amely támogatja a webhookokat), használhatja a manuális kézfogási mechanizmust. Győződjön meg arról, hogy a 2018-05-01-Preview API-verziót vagy újabb verzióját használja (Event Grid Azure CLI-bővítmény telepítése), hogy megkapja a validationUrl az érvényesítési eseményben. A manuális ellenőrzési kézfogás befejezéséhez szerezze be a `validationUrl` tulajdonság értékét, és keresse meg az URL-címet a böngészőben. Ha az érvényesítés sikeres, akkor egy üzenet jelenik meg a böngészőben, hogy az érvényesítés sikeres volt. Láthatja, hogy az esemény-előfizetés provisioningState "sikeres". 

### <a name="event-delivery-security"></a>Esemény-kézbesítési biztonság

A webhook-végpont biztonságossá tételéhez lekérdezési paramétereket adhat hozzá a webhook URL-címéhez az esemény-előfizetés létrehozásakor. A lekérdezési paraméterek egyikének megadásával titkos, például [hozzáférési jogkivonat](https://en.wikipedia.org/wiki/Access_token)lehet. A webhook használhatja a titkos kulcsot, hogy felismerje az eseményt, Event Grid érvényes engedélyekkel érkezik. A Event Grid a következő lekérdezési paramétereket fogja tartalmazni minden eseménynek a webhookhoz való továbbításakor.

Az esemény-előfizetés szerkesztésekor a lekérdezési paraméterek nem jelennek meg, és csak akkor jelennek meg, ha a [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) paramétert használja az Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)-ben.

Végezetül fontos megjegyezni, hogy Azure Event Grid csak a HTTPS webhook-végpontokat támogatja.

## <a name="event-subscription"></a>Esemény-előfizetés

Egy eseményre való előfizetéshez bizonyítania kell, hogy van hozzáférése az eseményforrás és a kezelőhöz. Az előző szakaszban leírtak szerint a webhook tulajdonosa is szerepelt. Ha olyan eseménykezelőt használ, amely nem webhook (például egy Event hub vagy várólista-tároló), írási hozzáféréssel kell rendelkeznie az adott erőforráshoz. Ez az engedély-ellenőrzési funkció megakadályozza, hogy jogosulatlan felhasználó küldjön eseményeket az erőforrásba.

Az eseményforrás erőforrásának a **Microsoft. EventGrid/EventSubscriptions/Write** engedéllyel kell rendelkeznie. Erre az engedélyre azért van szükség, mert az erőforrás hatókörében új előfizetést ír. A szükséges erőforrás különbözik attól függően, hogy egy rendszertémakörre vagy egy egyéni témakörre való feliratkozásra van-e szükség. Ebben a szakaszban mindkét típust ismertetjük.

### <a name="system-topics-azure-service-publishers"></a>Rendszertémakörök (Azure-szolgáltatás közzétevői)

A rendszertémakörökhöz engedély szükséges ahhoz, hogy új esemény-előfizetést írjon az eseményt közzétevő erőforrás hatókörén. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Ha például egy **myacct**nevű Storage-fiókra szeretne előfizetni egy eseményre, szüksége lesz a Microsoft. EventGrid/EventSubscriptions/Write engedélyre a következő címen: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Egyéni témakörök

Egyéni témakörök esetén engedély szükséges ahhoz, hogy új esemény-előfizetést írjon az Event Grid-témakör hatókörébe. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Ha például egy **mytopic**nevű egyéni témakörre szeretne előfizetni, a következő címen kell megadnia a Microsoft. EventGrid/EventSubscriptions/Write engedélyt: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Egyéni témakör közzététele

Az egyéni témakörök megosztott elérési aláírást (SAS) vagy kulcsos hitelesítést használnak. A SAS használatát javasoljuk, de a kulcsos hitelesítés egyszerű programozást tesz lehetővé, és számos meglévő webhook-közzétevővel kompatibilis. 

Adja meg a hitelesítési értéket a HTTP-fejlécben. SAS esetében használja az **AEG-sas-tokent** a fejléc értékéhez. A kulcsos hitelesítéshez használja az **AEG-sas-Key** értéket a fejléc értékeként.

### <a name="key-authentication"></a>Kulcsos hitelesítés

A kulcsos hitelesítés a hitelesítés legegyszerűbb formája. A formátumot használja: `aeg-sas-key: <your key>`

Egy kulcsot például a következővel adhat át:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokenek

A Event Grid SAS-jogkivonatai közé tartozik az erőforrás, a lejárati idő és az aláírás. Az SAS-token formátuma: `r={resource}&e={expiration}&s={signature}`.

Az erőforrás az Event Grid-témakör elérési útja, amelyhez eseményeket küld. Egy érvényes erőforrás elérési útja például a következő: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

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

## <a name="management-access-control"></a>Felügyeleti Access Control

Azure Event Grid lehetővé teszi a különböző felhasználók számára megadott hozzáférési szint szabályozását különböző felügyeleti műveletek, például esemény-előfizetések listázása, új adatbázisok létrehozása és kulcsok létrehozása céljából. Event Grid az Azure szerepköralapú hozzáférés-vezérlését (RBAC) használja.

### <a name="operation-types"></a>Működési típusok

Event Grid a következő műveleteket támogatja:

* Microsoft. EventGrid/*/READ
* Microsoft. EventGrid/*/Write
* Microsoft. EventGrid/*/delete
* Microsoft. EventGrid/eventSubscriptions/getFullUrl/művelet
* Microsoft. EventGrid/témakörök/Listkeys műveletének beolvasása/művelet
* Microsoft. EventGrid/témakörök/regenerateKey/művelet

Az utolsó három művelet visszaküldi a potenciálisan titkos adatokat, amelyek kiszűrik a normál olvasási műveleteket. Javasoljuk, hogy korlátozza a hozzáférést ezekhez a műveletekhez. 

### <a name="built-in-roles"></a>Beépített szerepkörök

A Event Grid két beépített szerepkört biztosít az esemény-előfizetések kezeléséhez. Fontos, hogy az [események tartományának](event-domains.md) megvalósítása során a felhasználók az esemény tartományában lévő témakörökre való előfizetéshez szükséges engedélyeket adják meg a felhasználóknak. Ezek a szerepkörök az esemény-előfizetésekre összpontosítanak, és nem biztosítanak hozzáférést olyan műveletekhez, mint például a témakörök létrehozása.

[Ezeket a szerepköröket hozzárendelheti egy felhasználóhoz vagy csoportjához](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription közreműködő (előzetes verzió)** : Event Grid előfizetési műveletek kezelése

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription-olvasó (előzetes verzió)** : Event Grid-előfizetések olvasása

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>Egyéni szerepkörök

Ha a beépített szerepköröktől eltérő engedélyeket kell megadnia, létrehozhat egyéni szerepköröket.

A következő példa olyan Event Grid szerepkör-definíciókat mutat be, amelyek lehetővé teszik a felhasználók számára a különböző műveletek elvégzését. Ezek az egyéni szerepkörök különböznek a beépített szerepköröktől, mert szélesebb körű hozzáférést biztosítanak, mint a csak az esemény-előfizetések.

**EventGridReadOnlyRole. JSON**: csak olvasási műveletek engedélyezése.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole. JSON**: a korlátozás utáni műveletek engedélyezése, de a törlési műveletek letiltása.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole. JSON**: engedélyezi az összes Event Grid-műveletet.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Egyéni szerepköröket a [PowerShell](../role-based-access-control/custom-roles-powershell.md), az [Azure CLI](../role-based-access-control/custom-roles-cli.md)és a [Rest](../role-based-access-control/custom-roles-rest.md)használatával hozhat létre.

## <a name="next-steps"></a>További lépések

* A Event Grid bevezetését lásd: [About Event Grid](overview.md)
