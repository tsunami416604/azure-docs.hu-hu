---
title: Az Azure Event Grid biztonsági és hitelesítés
description: Az Azure Event Gridet és a vele kapcsolatos fogalmakat ismerteti.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: babanisa
ms.openlocfilehash: db6db54d362e7ef6373271e238fdb1cf543a142e
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413485"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid biztonsági és hitelesítés 

Az Azure Event Grid hitelesítési három típusa van:

* WebHook eseménykézbesítés
* Esemény-előfizetések
* Közzététel egyéni témakörben

## <a name="webhook-event-delivery"></a>WebHook eseménykézbesítés

Webhookok az események fogadása az Azure Event Grid számos módon tartoznak. Amikor készen áll egy új esemény, Event Grid szolgáltatás a kérelem törzsében szereplő bejegyzések HTTP-kérést a konfigurált végpontnak az eseményhez.

Event Grid számos más szolgáltatásokhoz hasonlóan webhookokat támogató van szükség, hogy a Webhook-végpontot tulajdonjogának igazolásához események azáltal, hogy a végpont megkezdése előtt. Ez a követelmény megakadályozza, hogy egy rosszindulatú felhasználó események a végpont-elárasztás. Ha használja az alábbi három Azure-szolgáltatások bármelyikét, az Azure-infrastruktúra automatikusan kezeli az ellenőrzés:

* Az Azure Logic Apps esetében
* Az Azure Automation
* Az Azure Functions az Event Grid-Trigger.

Ha bármilyen más típusú végpont, például egy HTTP-eseményindító-alapú Azure-függvényt használ, a végpont kódot kell részt egy érvényesítési kézfogás az Event GRID használatával. Event Grid kétféleképpen érvényesíteni az előfizetés támogatja.

1. **(Programozott) ValidationCode kézfogás**: Ha a végpont szabályozhatja a forráskódot, ezt a módszert javasoljuk. Esemény-előfizetés létrehozása idején az Event Grid egy előfizetés érvényesítési esemény küld a végponthoz. Ez az esemény sémája hasonlít bármilyen más Event Grid-esemény. Ez az esemény adatok részének tartalmaz egy `validationCode` tulajdonság. Az alkalmazás ellenőrzi, hogy az érvényesítési kérelmet egy várt esemény-előfizetés, és az érvényesítési kódot az Event Gridbe ad. A kézfogás mechanizmus az összes Event Grid-verziót támogatja.

2. **ValidationURL kézfogás (manuális)**: Bizonyos esetekben nem férhet hozzá a forráskódot a végpont a ValidationCode kézfogás megvalósításához. Például, ha egy külső szolgáltatást használ (például [Zapier](https://zapier.com) vagy [IFTTT](https://ifttt.com/)), hogy programozott módon nem tud reagálni az érvényesítési kóddal.

   Verzió 2018-05-01-preview verziótól kezdődően Event Grid egy manuális érvényesítésre kézfogás támogatja. Ha egy SDK-t, vagy az eszközt, amely API verzió 2018-05-01-preview egy esemény-előfizetést hoz létre, vagy később, az Event Grid küld egy `validationUrl` az előfizetés érvényesítése esemény részén található adatok a tulajdonság. A kézfogás elvégzéséhez, keresse meg az eseményadatokat, és manuálisan az URL egy GET kérelmet küldeni. Is használhatja, vagy a REST-ügyféllel, vagy a böngészőjében.

   A megadott URL-cím a 10 percig érvényes. Az időszakban az üzembe helyezési az esemény-előfizetés állapota `AwaitingManualAction`. Ha nem végezte el a kézi ellenőrzés 10 percen belül, a kiépítési állapot értéke `Failed`. Az esemény-előfizetés a kézi ellenőrzés előtt újra létre kell.

### <a name="validation-details"></a>Az érvényesítés részletei

* Esemény előfizetés létrehozása és frissítése időpontjában Event Grid egy előfizetés érvényesítési esemény a cél-végpontot tesz közzé. 
* Az esemény tartalmaz egy fejléc értéke "AEG ügyet Eseménytípus: SubscriptionValidation".
* Az esemény törzsét, más Event Grid-események ugyanazzal a sémával rendelkezik.
* Esemény típusa az esemény tulajdonság `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Az esemény adat tulajdonság tartalmazza a `validationCode` tulajdonság egy véletlenszerűen létrehozott karakterlánccal. Például "validationCode: acb13 …".
* API verzió 2018-05-01-preview használja, ha az esemény-adatokat is tartalmaz egy `validationUrl` tulajdonság manuális érvényesítésének az előfizetéshez tartozó URL-címet.
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
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
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

Egy HTTP 200 OK válasz állapotkódot kell visszaadnia. HTTP 202 elfogadott, hogy a rendszer nem ismeri fel érvényes Event Grid-előfizetés érvényesítési válaszhoz.

Vagy manuálisan ellenőrizheti az előfizetés egy GET kérést küld az érvényesítési URL-címet. Az esemény-előfizetés mindaddig, amíg érvényesítve függő állapotban marad.

Az előfizetés érvényesítése kézfogás kezelésére egy példa: egy [ C# minta](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Ellenőrzőlista

Esemény előfizetés létrehozása során, ha például egy hibaüzenetet lát "a megadott végpont ellenőrzése a kísérlet https://your-endpoint-here nem sikerült. További részletekért látogasson el https://aka.ms/esvalidation", az azt jelzi, hogy nincs-e hiba a érvényesítési kézfogás. Ez a hiba elhárításához ellenőrizze a következő szempontokat:

* Rendelkezik a cél végpontját ellenőrzése az alkalmazás kódja? Például ha szeretne írni egy HTTP-eseményindító-alapú Azure-függvény, rendelkezik az alkalmazás kódja, módosíthatja a hozzáférést?
* Ha rendelkezik hozzáféréssel az alkalmazáskód, a alapú ValidationCode kézfogás mechanizmus megvalósításához, a fenti példában látható módon.

* Ha Ön nem rendelkezik hozzáféréssel az alkalmazáskód (például, ha egy külső szolgáltatás, amely támogatja a webhookok használata), a manuális kézfogás mechanizmust is használhatja. Ellenőrizze, hogy a 2018-05-01-preview API-verzió vagy újabb (telepítés Event Grid Azure CLI-bővítmény)-t használ a validationUrl kapják meg az érvényesítési esemény. A manuális érvényesítésre kézfogás elvégzéséhez gépkulcsengedélyek értékének a `validationUrl` tulajdonságot, és látogasson el az URL-CÍMRE a böngészőben. Sikeres ellenőrzés esetén megjelenik egy üzenet a böngészőben, hogy az ellenőrzés nem jelez. Látni fogja, hogy esemény-előfizetés provisioningState van "sikeres". 

### <a name="event-delivery-security"></a>Kézbesítési biztonsági esemény

Egy esemény-előfizetés létrehozásakor lekérdezési paraméterek hozzáadásával a webhook URL-cím gondoskodhat a webhook-végpontot. Állítsa be a lehet például egy titkos kulcsot a lekérdezési paraméterek egyike egy [hozzáférési jogkivonat](https://en.wikipedia.org/wiki/Access_token). A webhook a titkos kulcs használatával ismeri fel az esemény érkezik Event Grid érvényes engedélyekkel. Event Grid a webhook minden eseménykézbesítés tartalmazza a lekérdezési paraméterek.

Az esemény-előfizetés szerkesztésekor a lekérdezési paraméterek nem jelenik meg vagy nem adott vissza, ha a [--– teljes-végpont-URL-címek](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) paraméter használata az Azure-ban [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Végezetül fontos megjegyezni, hogy csak az Azure Event Grid támogatja a webhook koncové body protokolu HTTPS.

## <a name="event-subscription"></a>Esemény-előfizetés

Feliratkozás egy eseményt, igazolnia kell, hogy a forrás és a kezelő a hozzáférést. Igazolja, hogy a WebHook saját az előző szakaszban ismertetett volt. Egy eseménykezelő, amely egy WebHook (például egy event hub vagy az queue storage) nem használja, ha ennek az erőforrásnak írási hozzáférés szükséges. Az engedélyek ellenőrzését egy jogosulatlan felhasználó megakadályozza az események küldése az erőforrás.

Rendelkeznie kell a **Microsoft.EventGrid/EventSubscriptions/Write** engedéllyel az erőforrás, amely az esemény forrását. Szüksége erre az engedélyre, mert a ír be egy új előfizetést az erőforrás a hatókörben. A szükséges erőforrás eltér attól még egy rendszer témakört vagy egy egyéni témakör előfizetés alapján. Ebben a szakaszban mindkét fajta ismerteti.

### <a name="system-topics-azure-service-publishers"></a>Rendszer témakörök (az Azure service közzétevők)

A rendszer témaköröket szüksége van egy új esemény-előfizetés a hatókörben az erőforrás az esemény közzétételének írási engedéllyel. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Például egy esemény, a storage-fiók előfizetés neve **fióknév**, az a Microsoft.EventGrid/EventSubscriptions/Write engedélyre van szüksége: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Egyéni témakörök

Egyéni témakörök szüksége lesz egy új esemény-előfizetés a hatókörben, az event grid-témakör írási engedéllyel. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Például a feliratkozás egyéni témakörre nevű **mytopic**, az a Microsoft.EventGrid/EventSubscriptions/Write engedélyre van szüksége: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Közzététel egyéni témakörben

Egyéni témakörök közös hozzáférésű Jogosultságkód (SAS) vagy a kulcs alapú hitelesítés használata. Azt javasoljuk, hogy a SAS, de kulcsos hitelesítés egyszerű programozási biztosít, és számos meglévő webhook-közzétevők kompatibilis. 

A hitelesítési érték szerepel a HTTP-fejléc. SAS használata **AEG ügyet sas-jogkivonat** a fejléc értékeként. Kulcsos hitelesítés használata **AEG ügyet-sas-kulcs** a fejléc értékeként.

### <a name="key-authentication"></a>Kulcsos hitelesítés

Kulcs alapú hitelesítés a hitelesítés legegyszerűbb formája. A következő formátumot használja: `aeg-sas-key: <your key>`

Például adja át a kulcsot:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokenek

Az Event Gridhez SAS-tokeneket tartalmazza az erőforrás lejárati időt és aláírás. A SAS-jogkivonat formátuma: `r={resource}&e={expiration}&s={signature}`.

Az erőforrás elérési útja, amelyhez eseményt küld event grid-témakör. Ha például egy érvényes erőforrás elérési útja: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Az aláírás-kulcsból hozza létre.

Ha például egy érvényes **AEG ügyet sas-jogkivonat** érték:

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

## <a name="management-access-control"></a>Felügyeleti hozzáférés-vezérlése

Az Azure Event Grid lehetővé teszi a különböző felügyeleti műveleteket, például az esemény-előfizetések listája, újakat hoz létre, és hozzon létre kulcsokat, a különböző felhasználókhoz megadott hozzáférési szintet szabályozza. Event Grid használja az Azure szerepköralapú hozzáférés-vezérlés (RBAC).

### <a name="operation-types"></a>Művelet típusa

Event Grid támogatja a következő műveleteket:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Az utolsó három műveletek potenciálisan titkos információt, amely lekérdezi a normál olvasási műveletek szűrt adja vissza. Javasoljuk, hogy korlátozza a hozzáférést ezeket a műveleteket. 

### <a name="built-in-roles"></a>Beépített szerepkörök

Event Grid két beépített szerepkörrel biztosít esemény-előfizetések kezeléséhez. Implementálásakor fontosak [esemény tartományok](event-domains.md) , mivel a felhasználóknak szükségük van a esemény tartományban témákra iratkozhat fel az engedélyeket biztosítanak. Ezek a szerepkörök eseményelőfizetések összpontosítanak, és ne adjon hozzáférést műveleteket, mint például a témakörök létrehozására.

Is [ezeket a szerepköröket hozzárendelni egy felhasználóhoz vagy csoporthoz](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Közreműködője (minta)**: Event Grid-előfizetés műveletek kezelése

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

**EventGrid EventSubscription olvasója (minta)**: olvassa el az Event Grid-előfizetések

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

Ha adja meg az engedélyeket, amelyek eltérnek a beépített szerepkörök van szüksége, létrehozhat egyéni szerepköröket is.

Az alábbiakban a minta Event Grid szerepkör-definíciók, amelyek lehetővé teszik a felhasználók számára a különböző műveleteket. Ezek egyéni szerepkörök nem azonosak a beépített szerepkörök, mivel azok csak az esemény-előfizetések, mint szélesebb körű hozzáférés biztosítása.

**EventGridReadOnlyRole.json**: Csak engedélyezze a csak olvasható műveletekhez.

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

**EventGridNoDeleteListKeysRole.json**: Korlátozott utáni műveletek engedélyezése, de a törlési műveletek letiltása.

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

**EventGridContributorRole.json**: Lehetővé teszi az event grid összes műveletet.

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

Létrehozhat egyéni szerepköröket a [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI-vel](../role-based-access-control/custom-roles-cli.md), és [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>További lépések

* Event Grid bemutatása, lásd: [Event Grid](overview.md)
