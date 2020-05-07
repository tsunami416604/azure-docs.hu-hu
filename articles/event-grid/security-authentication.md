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
ms.openlocfilehash: 528c3613549ee49009f99d45e5bd9c2cf1745d78
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779994"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Azure Event Grid erőforrásokhoz való hozzáférés hitelesítése
Ez a cikk a következő forgatókönyvekkel kapcsolatos információkat tartalmazza:  

- Olyan ügyfelek hitelesítése, amelyek az eseményeket a közös hozzáférési aláírás (SAS) vagy a kulcs használatával Azure Event Grid témakörökre teszik közzé. 
- A webhook-végpont biztonságossá tétele Azure Active Directory (Azure AD) használatával a Event Grid hitelesítéséhez, hogy az események a végponthoz legyenek **kézbesítve** .

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>A közzétételi ügyfelek hitelesítése SAS vagy kulcs használatával
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

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Az események kézbesítésének hitelesítése webhook-végpontokra
Az alábbi szakaszok azt ismertetik, hogyan hitelesíthető az események kézbesítése a webhook-végpontokra. A használt módszertől függetlenül egy érvényesítési kézfogási mechanizmust kell használnia. Részletekért lásd: [webhook-esemény kézbesítése](webhook-event-delivery.md) . 

### <a name="using-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) használata
Az Azure Active Directory (Azure AD) használatával biztonságossá teheti a webhook-végpontot, hogy hitelesítse és engedélyezze Event Grid, hogy eseményeket továbbítson a végpontoknak. Létre kell hoznia egy Azure AD-alkalmazást, létre kell hoznia egy szerepkört és egy szolgáltatási elvet az alkalmazásban az Event Grid engedélyezéséhez, és konfigurálnia kell az esemény-előfizetést az Azure AD-alkalmazás használatára. [Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt Event Grid használatával](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Az ügyfél titkos kulcsa lekérdezési paraméterként
A webhook-végpont biztonságossá tételéhez lekérdezési paramétereket adhat hozzá a webhook URL-címéhez az esemény-előfizetés létrehozásakor. A lekérdezési paraméterek egyikének beállításával az ügyfél titkos kulcsa, például [hozzáférési jogkivonat](https://en.wikipedia.org/wiki/Access_token) vagy közös titok lehet. A webhook használhatja a titkos kulcsot, hogy felismerje az eseményt, Event Grid érvényes engedélyekkel érkezik. A Event Grid a következő lekérdezési paramétereket fogja tartalmazni minden eseménynek a webhookhoz való továbbításakor. Ha az ügyfél titkos kulcsát frissíti, a rendszer az esemény-előfizetést is frissítenie kell. Ha el szeretné kerülni a kézbesítési hibákat a titkos rotáció során, akkor a webhook a régi és az új titkokat is elfogadja a korlátozott időtartamra. 

Mivel a lekérdezési paraméterek tartalmazhatják az ügyfél titkos kulcsait, a rendszer extra gondossággal kezeli őket. A rendszer titkosítottként tárolja őket, és nem érhető el a szolgáltatási operátorok számára. Nincsenek naplózva a szolgáltatási naplók/Nyomkövetések részeként. Az esemény-előfizetés szerkesztésekor a lekérdezési paraméterek nem jelennek meg, és csak akkor jelennek meg, ha a [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) paramétert használja az Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)-ben.

További információ az események webhookok általi kézbesítéséről: [webhook-esemény kézbesítése](webhook-event-delivery.md)

> [!IMPORTANT]
A Azure Event Grid csak a **https** webhook-végpontokat támogatja. 

## <a name="next-steps"></a>További lépések

- A Event Grid bevezetését lásd: [About Event Grid](overview.md)
