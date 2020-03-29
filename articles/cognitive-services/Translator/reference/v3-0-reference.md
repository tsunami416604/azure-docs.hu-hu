---
title: Fordító szöveg API V3.0 referencia
titleSuffix: Azure Cognitive Services
description: A Translator Text API V3.0 referenciadokumentációja. A Translator Text API 3-as verziója modern JSON-alapú webes API-t biztosít.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 3/13/2020
ms.author: swmachan
ms.openlocfilehash: 4180dc6127fb2d31465400b1b25fb7e2d68f4754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369165"
---
# <a name="translator-text-api-v30"></a>Fordító szöveg API-v3.0

## <a name="whats-new"></a>Újdonságok

A Translator Text API 3-as verziója modern JSON-alapú webes API-t biztosít. Javítja a használhatóságot és a teljesítményt azáltal, hogy a meglévő funkciókat kevesebb műveletbe tömöríti, és új funkciókat biztosít.

 * Átírás: szöveg konvertálása egyik nyelven az egyik parancsfájlból a másikba.
 * Fordítás több nyelvre egy kérelemben.
 * Nyelvfelismerés, fordítás és átírás egyetlen kérelemben.
 * Szótár egy kifejezés alternatív fordításainak kereséséhez, a háttérfordítások és a kontextusban használt kifejezéseket bemutató példák megkereséséhez.
 * Több informatív nyelvfelismerési eredmények.

## <a name="base-urls"></a>Alap URL-címek

A Microsoft Translator több adatközpont-helyről is kiszolgálásra kerül. Jelenleg 10 [Azure-földrajzi területen](https://azure.microsoft.com/global-infrastructure/regions)találhatók:

* **Amerika:** USA keleti régiója, USA déli középső régiója, USA nyugati régiója 2 
* **Ázsia és a csendes-óceáni térség:** Korea South, Japán Kelet-, Délkelet-Ázsia és Ausztrália Kelet-
* **Európa:** Észak-Európa és Nyugat-Európa

A Microsoft Translator Text API-hoz érkező kérelmeket a legtöbb esetben az az adatközpont kezeli, amely a legközelebb van a kérelem származási helyéhez. Adatközpont-hiba esetén a kérelem az Azure földrajzi adottságain kívül rekedhet.

Ha egy adott Azure-földrajzi terület által kell kezelni a kérést, módosítsa az API-kérelem globális végpontját a kívánt regionális végpontra:

|Leírás|Az Azure földrajza|Kiindulási URL-cím|
|:--|:--|:--|
|Azure|Globális (nem regionális)|   api.cognitive.microsofttranslator.com|
|Azure|Egyesült Államok|   api-nam.cognitive.microsofttranslator.com|
|Azure|Európa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Ázsia és a Csendes-óceáni térség|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Hitelesítés

Iratkozzon fel a Translator Text [API-ra vagy a Cognitive Services többszolgáltatásos szolgáltatására](https://azure.microsoft.com/pricing/details/cognitive-services/) az Azure Cognitive Servicesben, és az (az Azure Portalon elérhető) előfizetési kulcsával hitelesítheti magát. 

Három fejlécet használhat az előfizetés hitelesítéséhez. Ez a táblázat az egyes használatot ismerteti:

|Fejlécek|Leírás|
|:----|:----|
|Ocp-Apim-Subscription-Key|*A Cognitive Services-előfizetéssel használható, ha a titkos kulcsot adja át.*<br/>Az érték az Azure titkos kulcsa a Translator Text API-ra való előfizetéshez.|
|Engedélyezés|*A Cognitive Services-előfizetéssel használható, ha hitelesítési jogkivonatot ad át.*<br/>Az érték a bemutatóra `Bearer <token>`szóló token: .|
|Ocp-Apim-Előfizetés-régió|*A Cognitive Services többszolgáltatásos és regionális fordítói erőforrással használható.*<br/>Az érték a multi-service vagy regionális fordító erőforrás régiója. Ez az érték nem kötelező, ha egy globális fordító erőforrás használata.|

###  <a name="secret-key"></a>Titkos kulcs
Az első lehetőség a `Ocp-Apim-Subscription-Key` fejléc használatával történő hitelesítés. Adja `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` hozzá a fejlécet a kéréshez.

#### <a name="authenticating-with-a-global-resource"></a>Hitelesítés globális erőforrással

Globális [fordítóerőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)használata esetén meg kell adnia egy fejlécet a fordító API-jának hívásához.

|Fejlécek|Leírás|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Az érték az Azure titkos kulcsa a Translator Text API-ra való előfizetéshez.|

Íme egy példa a Fordító API-nak a globális fordítói erőforrás használatával történő hívására

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsoft.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Hitelesítés regionális erőforrással

Ha egy [regionális fordító erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)használ.
Vannak 2 fejlécek, amelyek meg kell hívnia a fordító API-t.

|Fejlécek|Leírás|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Az érték az Azure titkos kulcsa a Translator Text API-ra való előfizetéshez.|
|Ocp-Apim-Előfizetés-régió| Az érték a fordítóerőforrás régiója. |

Íme egy példa a translator API hívására a regionális fordítói erőforrás használatával

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsoft.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Hitelesítés többszolgáltatásos erőforrással

Kognitív szolgáltatás többszolgáltatásos erőforrás használata esetén. Ez lehetővé teszi, hogy egyetlen titkos kulcsot használjon több szolgáltatás ra vonatkozó kérelmek hitelesítéséhez. 

Ha többszolgáltatásos titkos kulcsot használ, a kérelemhez két hitelesítési fejlécet kell mellékelve. Vannak 2 fejlécek, amelyek meg kell hívnia a fordító API-t.

|Fejlécek|Leírás|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Az érték a többszolgáltatásos erőforrás Azure titkos kulcsa.|
|Ocp-Apim-Előfizetés-régió| Az érték a többszolgáltatásos erőforrás régiója. |

Régió szükséges a többszolgáltatásos szöveges API-előfizetéshez. A kiválasztott régió az egyetlen régió, amely a többszolgáltatásos előfizetési kulcs használatakor használhatja a szövegfordítást, és ugyanabban a régióban kell lennie, amelyet akkor választott ki, amikor az Azure Portalon keresztül regisztrált a többszolgáltatásos előfizetésre.

A rendelkezésre `canadacentral`álló `centralindia` `centralus`régiók `eastasia`a `eastus` `eastus2` `francecentral`következők, `japaneast` `japanwest` `koreacentral` `australiaeast` `brazilsouth`, `northcentralus`, `northeurope` `southcentralus` `southeastasia` `centraluseuap`, `uksouth`, `westcentralus` `westeurope`, `westus` `westus2`, `southafricanorth`, , , , , , , , , , , .

Ha a lekérdezési karakterláncban átadja a `Subscription-Key`titkos kulcsot a paraméterrel, `Subscription-Region`akkor meg kell adnia a régiót a lekérdezési paraméterrel.

### <a name="authenticating-with-an-access-token"></a>Hitelesítés hozzáférési jogkivonattal
Másik lehetőségként a titkos kulcsot egy hozzáférési jogkivonatra cserélheti. Ez a jogkivonat minden `Authorization` kérésfejlécként szerepel. Engedélyezési jogkivonat beszerzéséhez `POST` kérjen kérelmet a következő URL-címre:

| Erőforrás típusa     | Hitelesítési szolgáltatás URL-címe                                |
|-----------------|-----------------------------------------------------------|
| Globális          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regionális vagy multi-service | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Íme egy titkos kulcsot kapó jogkivonat beszerzésére irányuló példakérések:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

A sikeres kérelem a kódolt hozzáférési jogkivonatot egyszerű szövegként adja vissza a választörzsben. Az érvényes jogkivonat átkerül a Fordító szolgáltatás, mint egy tulajdonosi jogkivonatot az engedélyezés.

```http
Authorization: Bearer <Base64-access_token>
```

A hitelesítési jogkivonat 10 percig érvényes. A jogkivonatot újra fel kell használni, ha a fordító API-k több hívást. Ha azonban a program hosszabb időn keresztül kéri a Fordító API-t, akkor a programnak rendszeres időközönként (például 8 percenként) új hozzáférési jogkivonatot kell kérnie.

## <a name="virtual-network-support"></a>Virtuális hálózatok támogatása

A fordítószolgáltatás már elérhető a virtuális hálózat`WestUS2` `EastUS`funkcióival korlátozott régiókban `SouthCentralUS`( , , `WestUS`, , `Central US EUAP`, , . `global` A virtuális hálózat engedélyezéséhez olvassa el [az Azure Cognitive Services virtuális hálózatok konfigurálása című témakört.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal) 

Ha bekapcsolja ezt a funkciót, az egyéni végponthasználatával meg kell hívnia a Translator API-t. A globális fordítóvégpont ("api.cognitive.microsofttranslator.com") nem használható, és nem hitelesíthető hozzáférési jogkivonattal.

Az egyéni végpontot a fordítói erőforrás létrehozása után [megtalálhatja.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)

|Fejlécek|Leírás|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Az érték az Azure titkos kulcsa a Translator Text API-ra való előfizetéshez.|
|Ocp-Apim-Előfizetés-régió| Az érték a fordítóerőforrás régiója. Ez az érték nem kötelező, ha az erőforrás`global`|

Íme egy példa kérelem a Translator API hívására az egyéni végpont használatával

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Hibák

A szabványos hibaválasz egy JSON-objektum, `error`amelynek neve név/érték pár. Az érték egyjezésű JSON-objektum is:

  * `code`: A kiszolgáló által definiált hibakód.
  * `message`: A hiba ember által olvasható ábrázolását adó karakterlánc.

Például egy ingyenes próba-előfizetéssel rendelkező ügyfél a következő hibaüzenetet kapja, ha az ingyenes kvóta kimerül:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
A hibakód egy 6 jegyű szám, amely a 3 jegyű HTTP-állapotkódot, majd egy 3 jegyű számot követ a hiba további kategorizálása érdekében. Gyakori hibakódok a következők:

| Kód | Leírás |
|:----|:-----|
| 400000| A kérelem bemeneteinek egyike érvénytelen.|
| 400001| A "scope" paraméter érvénytelen.|
| 400002| A "category" paraméter érvénytelen.|
| 400003| Egy nyelvmegnevező hiányzik vagy érvénytelen.|
| 400004| A célparancsfájl-megnevező ("To script") hiányzik vagy érvénytelen.|
| 400005| Egy bemeneti szöveg hiányzik vagy érvénytelen.|
| 400006| A nyelv és a parancsfájl kombinációja érvénytelen.|
| 400018| Egy forrásparancsfájl-megnevező ("Forrásparancsfájl") hiányzik vagy érvénytelen.|
| 400019| A megadott nyelvek egyike nem támogatott.|
| 400020| A bemeneti szöveg tömbjének egyik eleme érvénytelen.|
| 400021| Az API-verzió paraméter hiányzik vagy érvénytelen.|
| 400023| A megadott nyelvpár egyike érvénytelen.|
| 400035| A forrásnyelv ("Forrás" mező) érvénytelen.|
| 400036| A célnyelv ("To" mező) hiányzik vagy érvénytelen.|
| 400042| A megadott beállítások egyike ("Beállítások" mező) érvénytelen.|
| 400043| Az ügyfélnyomkövetési azonosító (ClientTraceId mező vagy X-ClientTranceId fejléc) hiányzik vagy érvénytelen.|
| 400050| A bemeneti szöveg túl hosszú. A [kéréskorlátok](../request-limits.md)megtekintése .|
| 400064| A "fordítás" paraméter hiányzik vagy érvénytelen.|
| 400070| A célparancsfájlok száma (ToScript paraméter) nem egyezik meg a célnyelvek számával (To parameter).|
| 400071| Az érték nem érvényes a TextType típusra.|
| 400072| A bemeneti szöveg tömbje túl sok elemet tartalmaz.|
| 400073| A parancsfájl paraméter érvénytelen.|
| 400074| A kérelem törzse érvénytelen JSON.|
| 400075| A nyelvpár és a kategóriakombináció érvénytelen.|
| 400077| Túllépte a kérelem maximális méretét. A [kéréskorlátok](../request-limits.md)megtekintése .|
| 400079| A nyelv és a nyelv közötti fordításhoz kért egyéni rendszer nem létezik.|
| 400080| A nyelv vagy a parancsfájl nem támogatja az átírást.|
| 401000| A kérelem nincs engedélyezve, mert a hitelesítő adatok hiányoznak vagy érvénytelenek.|
| 401015| "A megadott hitelesítő adatok a beszédfelismerési API-hoz tartoznak. Ez a kérelem a szöveges API hitelesítő adatait igényli. Használjon előfizetést a Translator Text API-ra."|
| 403000| A művelet nem engedélyezett.|
| 403001| A művelet nem engedélyezett, mert az előfizetés túllépte az ingyenes kvótát.|
| 405000| A kérelem metódusa nem támogatott a kért erőforráshoz.|
| 408001| A kért fordítási rendszer előkészítése folyamatban van. Próbálkozzon újra néhány perc múlva.|
| 408002| A kérelem idővel elévül a bejövő adatfolyamra várva. Az ügyfél nem adott ki kérést a kiszolgáló várakozási időn belül. Az ügyfél bármikor megismételheti a kérést módosítások nélkül.|
| 415000| A Content-Type fejléc hiányzik vagy érvénytelen.|
| 429000, 429001, 429002| A kiszolgáló elutasította a kérést, mert az ügyfél túllépte a kérelemkorlátot.|
| 500000| Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse azt a hiba dátumával és időpontjával, az X-RequestId válaszfejléc azonosítójával és az Ügyfélazonosítóval az X-ClientTraceId kérelemfejlécből.|
| 503000| A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra. Ha a hiba továbbra is fennáll, jelentse azt a hiba dátumával és időpontjával, az X-RequestId válaszfejléc azonosítójával és az Ügyfélazonosítóval az X-ClientTraceId kérelemfejlécből.|

## <a name="metrics"></a>Mérőszámok 
Metrikák lehetővé teszik, hogy a fordító használati és rendelkezésre állási információk az Azure Portalon, a metrikák szakaszban látható az alábbi képernyőképen látható. További információ: [Data and platform metrics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

![Fordítói metrikák](../media/translatormetrics.png)

Ez a táblázat felsorolja a rendelkezésre álló metrikák leírását, hogyan használják a fordítási API-hívások figyelésére.

| Mérőszámok | Leírás |
|:----|:-----|
| Összes hívás| Az API-hívások teljes száma.|
| TotalTokenCalls| A hitelesítési jogkivonat használatával a tokenszolgáltatáson keresztül érkező API-hívások teljes száma.|
| Sikeres hívások| A sikeres hívások száma.|
| Összes hiba| A hibaválaszt eredményező hívások száma.|
| Blokkolt hívások| A túllépett hívások száma vagy a kvótakorlát.|
| Kiszolgálóhibák| A kiszolgáló belső hibával rendelkező hívások száma(5XX).|
| Ügyfélhibák| Ügyféloldali hibával rendelkező hívások száma (4XX).|
| Késés| A kérelem befejezésének időtartama ezredmásodpercben.|
| Lefordított karakterek| A bejövő szöveges kérelemben szereplő karakterek teljes száma.|
