---
title: Az Azure Event rács biztonsági és hitelesítési
description: Azure Event rács és a fogalmakat ismerteti.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 03/15/2018
ms.author: babanisa
ms.openlocfilehash: f97de4e93c9330206ed22c071d8ade0821bf6691
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="event-grid-security-and-authentication"></a>Esemény rács biztonsági és hitelesítési 

Az Azure Event rács három típusú hitelesítés van:

* Esemény-előfizetések
* Esemény közzététele
* WebHook esemény kézbesítés

## <a name="webhook-event-delivery"></a>WebHook esemény kézbesítés

Webhook olyan események fogadása Azure esemény rács számos módja közül. Amikor készen áll az új esemény, az esemény rács Webhook HTTP-kérelmet küld a beállított HTTP-végpont a törzsben szereplő eseménnyel.

Ha saját WebHook végpont regisztrálása esemény rács, küld, akkor egy POST kérést egy egyszerű érvényességi kóddal igazolnia a végpont tulajdonosa. Az alkalmazás kell válaszolnia által echo vissza az érvényesítési kódot. Esemény rács WebHook végpontok, amelyek még nem kapott az érvényesítés nem kézbesíteni.

### <a name="validation-details"></a>Ellenőrzési részletek

* Az esemény előfizetés létrehozása/frissítése időpontjában esemény rács visszaküldés egy "SubscriptionValidationEvent" esemény a cél-végponthoz.
* Az esemény "AEG ügyet Eseménytípus: SubscriptionValidation" fejléc értéke tartalmazza.
* Az esemény törzsében van ugyanazon séma más esemény rács eseményként is rögzíti.
* Az eseményadat tartalmazza egy véletlenszerűen generált karakterlánc "validationCode" tulajdonságot. Például "validationCode: acb13...".
* A tömb csak az érvényesítési esemény tartalmazza. A további események küldése egy külön kérelmet a után echo vissza az érvényesítési kódot.

Példa SubscriptionValidationEvent van az alábbi példában látható módon:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Végpont igazolva, hogy echo vissza a Ellenőrzőkód validationResponse tulajdonság a következő példában látható módon:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```
### <a name="event-delivery-security"></a>Esemény szállítási biztonság

Egy esemény előfizetés létrehozásakor lekérdezési paraméterek hozzáadásával a webhook URL-cím biztonságossá teheti a webhook-végpontot. Állítsa be a következő lekérdezési paraméterek kell lennie, mint a titkos kulcs egy [hozzáférési jogkivonat](https://en.wikipedia.org/wiki/Access_token) használó a webhook is ismeri fel az esemény érkezik esemény rács érvényes engedélyekkel. Esemény rács minden esemény kézbesítése a következő webhook vegye fel a lekérdezési paramétereket.

Ha az esemény-előfizetést szerkesztésével, a lekérdezési paraméterek nem fog jelenik meg vagy visszaadott, kivéve, ha a [---teljes-végpont-URL-címek](https://docs.microsoft.com/en-us/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_show) paraméter használata az Azure-ban [CLI](https://docs.microsoft.com/en-us/cli/azure?view=azure-cli-latest).

Végezetül fontos ügyeljen arra, hogy csak Azure esemény rács támogatja a HTTPS-végpontnak webhook.

## <a name="event-subscription"></a>Az esemény-előfizetés

Egy esemény előfizetni, rendelkeznie kell a **Microsoft.EventGrid/EventSubscriptions/Write** engedéllyel rendelkezik a szükséges erőforrás. Meg kell ezt az engedélyt, mivel szeretne írni egy új előfizetést a hatókörben, az erőforrás. A szükséges erőforrás eltér e még, egyéni vagy egy rendszer témakört való előfizetés. Ez a szakasz ismerteti a kétféle típusú.

### <a name="system-topics-azure-service-publishers"></a>Rendszer témakörök (az Azure szolgáltatáshoz kiadók)

A rendszer témakörök kell egy új Eseményelőfizetés a hatókörből az erőforrás-közzététel az esemény írási engedéllyel. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Például egy eseménnyel a storage-fiók előfizetés neve **fióknév**, a Microsoft.EventGrid/EventSubscriptions/Write engedély szükséges: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Egyéni kapcsolatos témakörök

Egyéni témaköröket egy új Eseményelőfizetés a hatókörben, a következő esemény rács témakörben írási engedéllyel kell rendelkezni. Az erőforrás formátuma: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Például egy egyéni témakör előfizetni nevű **mytopic**, a Microsoft.EventGrid/EventSubscriptions/Write engedély szükséges: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>A témakör a közzététel

Témakörök használja, vagy a közös hozzáférésű Jogosultságkód (SAS), vagy a hitelesítés. Azt javasoljuk, hogy SAS, de hitelesítés egyszerű programozási biztosít, és sok meglévő webhook közzétevők kompatibilis. 

A hitelesítés érték szerepel a HTTP-fejléc. A SAS-használjon **AEG ügyet-sas-jogkivonat** fejlécértéket. A hitelesítés, használjon **AEG ügyet-sas-kulcs** fejlécértéket.

### <a name="key-authentication"></a>Hitelesítés

Hitelesítés a hitelesítési legegyszerűbb formája, amely. A következő formátumot használja: `aeg-sas-key: <your key>`

Például adja meg egy kulcs:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-tokenek

SAS-tokenje esemény rács közé tartozik, az erőforrás lejárati időt és aláírás. A SAS-jogkivonat formátuma: `r={resource}&e={expiration}&s={signature}`.

Az erőforrás elérési útja, amelyhez események küldjük esemény rács témakör. Például egy érvényes erőforrás elérési útja: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Az aláírás generálása egy kulcsot.

Például egy érvényes **AEG ügyet-sas-jogkivonat** érték:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

A következő példa egy SAS-jogkivonat használata esemény rácshoz hozza létre:

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

## <a name="management-access-control"></a>Felügyeleti hozzáférés-vezérlés

Azure esemény rács lehetővé teszik a különböző felügyeleti műveleteket, például az esemény-előfizetések listája, hozzon létre újakat, és kulcsok létrehozása különböző felhasználók számára megadott hozzáférési szintet. Esemény rács Azure szerepköralapú hozzáférés ellenőrzése (RBAC) használja.

### <a name="operation-types"></a>Művelet típusa

Az Azure event rács támogatja a következő műveleteket:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Az utolsó három műveletek térjen vissza a potenciálisan bizalmas adatokat, amelyeket normál olvasási műveletek lekérdezi szűrve. Ajánlott eljárás, hogy ezek a műveletek való hozzáférés korlátozása. Egyéni szerepkörök segítségével hozhatók létre [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md), [Azure parancssori felület (CLI)](../role-based-access-control/role-assignments-cli.md), és a [REST API](../role-based-access-control/role-assignments-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Érvényesítési szerepkör alapú hozzáférés-ellenőrzést (RBAC)

Az alábbi lépések segítségével a különböző felhasználók számára az RBAC kényszerítése:

#### <a name="create-a-custom-role-definition-file-json"></a>Hozzon létre egy egyéni szerepkör-definíció fájlt (.JSON kiterjesztésű)

Az alábbiakban minta esemény rács szerepkör-definíciók, amelyek lehetővé teszik a felhasználók más-más részhalmazához műveletek végrehajtásához.

**EventGridReadOnlyRole.json**: csak az olvasási műveletek engedélyezése.

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

**EventGridNoDeleteListKeysRole.json**: engedélyezze a korlátozott utáni műveletek azonban engedélyezi a törlési műveletek naplóját.

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

**EventGridContributorRole.json**: lehetővé teszi, hogy az összes esemény rács műveletek.

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

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>Hozzon létre, és az Azure parancssori felület segítségével egyéni szerepkör hozzárendelése

Segítségével hozhat létre egy egyéni biztonsági szerepkört:

```azurecli
az role definition create --role-definition @<file path>
```

A szerepkör hozzárendelése felhasználóhoz, használja:

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>További lépések

* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md)
