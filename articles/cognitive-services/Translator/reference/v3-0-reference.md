---
title: Translator Text API V 3.0 – dokumentáció
titleSuffix: Azure Cognitive Services
description: A Translator Text API V 3.0 dokumentációja.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: ad619ad965cf4b7d94b781818c658152f71250a7
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595002"
---
# <a name="translator-text-api-v30"></a>Translator Text API v 3.0

## <a name="whats-new"></a>Újdonságok

A Translator Text API 3. verziója modern JSON-alapú webes API-t biztosít. Javítja a használhatóságot és a teljesítményt azáltal, hogy a meglévő funkciókat kevesebb műveletre összevonja, és új funkciókat biztosít.

 * A nyelv átalakítása egy parancsfájlból egy másik parancsfájlba.
 * Fordítás több nyelvre egy kérelemben.
 * Nyelvfelismerés, fordítás és írás egyetlen kérelemben.
 * Szótár a kifejezés alternatív fordításának kereséséhez, hogy megkeresse a háttér-fordításokat és példákat a kontextusban használt kifejezések megjelenítéséhez.
 * További informatív nyelvfelismerés eredményei.

## <a name="base-urls"></a>Alap URL-címek

A Microsoft Translator több adatközpont-helyből is kiszolgálható. Jelenleg 10 [Azure](https://azure.microsoft.com/global-infrastructure/regions)-régióban találhatók:

* **Amerika** USA keleti régiója, USA déli középső régiója, az USA nyugati középső régiója és az USA 2. nyugati régiója 
* **Ázsia és a Csendes-óceáni térség:** Dél-Korea, Kelet-Japán, Délkelet-Ázsia és Kelet-Ausztrália
* **Európa** Észak-Európa és Nyugat-Európa

A Microsoft Translator Text APIra irányuló kérelmeket a legtöbb esetben az adatközpont kezeli, amely a kérelem helyétől legközelebb található. Adatközpont meghibásodása esetén a kérést az Azure földrajzán kívül is át lehet irányítani.

Ha szeretné kényszeríteni a kérést, hogy az adott Azure földrajza kezelhető legyen, módosítsa az API-kérés globális végpontját a kívánt regionális végpontra:

|Leírás|Az Azure földrajza|Alap URL-cím|
|:--|:--|:--|
|Azure|Globális (nem regionális)|   api.cognitive.microsofttranslator.com|
|Azure|Egyesült Államok|   api-nam.cognitive.microsofttranslator.com|
|Azure|Európa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Ázsia és a Csendes-óceáni térség|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Authentication

Fizessen elő Translator Text API vagy [Cognitive Services több szolgáltatásra](https://azure.microsoft.com/pricing/details/cognitive-services/) a Microsoft Cognitive Services-ben, és használja az előfizetési kulcsot (amely a Azure Portalban érhető el) a hitelesítéshez. 

Az előfizetés hitelesítéséhez három fejléc használható. Ez a táblázat leírja, hogyan használják a rendszer a következőket:

|Fejlécek|Leírás|
|:----|:----|
|OCP-Apim-Subscription-Key|*Ha a titkos kulcsot átadja, használja Cognitive Services*-előfizetéssel.<br/>Az érték a Translator Text API előfizetéséhez tartozó Azure titkos kulcs.|
|Authorization|*Ha hitelesítési tokent továbbít, használja Cognitive Services-előfizetést.*<br/>Az érték a tulajdonosi jogkivonat: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Ha több szolgáltatásból álló titkos kulcsot továbbít, használja a Cognitive Services Multi-Service előfizetést.*<br/>Az érték a Multi-Service előfizetés régiója. Ez az érték nem kötelező, ha nem használ több szolgáltatást használó előfizetést.|

###  <a name="secret-key"></a>Titkos kulcs
Az első lehetőség a `Ocp-Apim-Subscription-Key` fejléc használatával történő hitelesítés. Egyszerűen adja hozzá `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` a fejlécet a kérelemhez.

### <a name="authorization-token"></a>Engedélyezési jogkivonat
Azt is megteheti, hogy kicseréli a titkos kulcsot egy hozzáférési jogkivonatra. Ezt a `Authorization` tokent az egyes kérések fejlécként tartalmazzák. Az engedélyezési jogkivonat beszerzéséhez tegyen fel `POST` egy kérelmet a következő URL-címre:

| Környezet     | Hitelesítési szolgáltatás URL-címe                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Az alábbi példa egy titkos kulcsot tartalmazó jogkivonat beszerzésére vonatkozó kérelmeket mutat be:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Egy sikeres kérelem visszaadja a kódolt hozzáférési tokent egyszerű szövegként a válasz törzsében. Az érvényes jogkivonatot tulajdonosi jogkivonatként adja át a fordító szolgáltatásnak az engedélyezésben.

```
Authorization: Bearer <Base64-access_token>
```

A hitelesítési jogkivonat 10 percig érvényes. A tokent újra fel kell használni, amikor több hívást végez a fordítói API-kon. Ha azonban a program hosszabb időn keresztül kezdeményezi a kérelmeket a Translator API-nak, akkor a programnak rendszeres időközönként új hozzáférési jogkivonatot kell igényelnie (például 8 percenként).

### <a name="multi-service-subscription"></a>Több szolgáltatásra kiterjedő előfizetés

Az utolsó hitelesítési lehetőség a kognitív szolgáltatás többszolgáltatásos előfizetésének használata. Ez lehetővé teszi, hogy egyetlen titkos kulcsot használjon a kérelmek több szolgáltatáshoz való hitelesítéséhez. 

Több szolgáltatásból álló titkos kulcs használata esetén két hitelesítési fejlécet kell tartalmaznia a kérelemmel. Első lépésként a titkos kulcsot adja meg, a második az előfizetéshez társított régiót határozza meg. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

A Multi-Service Text API-előfizetéshez régió szükséges. A kiválasztott régió az egyetlen olyan régió, amelyet a többszolgáltatásos előfizetési kulcs használatakor használhat a szöveges fordításhoz, és a Azure Portalon keresztül a többszolgáltatásos előfizetésre való feliratkozáskor választott régiónak kell lennie.

Az elérhető régiók `australiaeast`a `brazilsouth`következők `canadacentral`, `centralindia` `centraluseuap` `eastasia` ,,`eastus2`,,,,,,,,, `eastus` `japaneast` `northeurope` `southcentralus` `southeastasia` ,`uksouth`,,és .`westus2` `westcentralus` `westeurope` `westus`

Ha a lekérdezési sztringben megadja a titkos kulcsot a paraméterrel `Subscription-Key`, akkor a régiót a lekérdezési paraméterrel `Subscription-Region`kell megadnia.

Ha tulajdonosi jogkivonatot használ, be kell szereznie a tokent a régió végpontján: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Hibák

A standard hibaérték a name/Value pár nevű `error`JSON-objektum. Az érték egy JSON-objektum is, amely tulajdonságokkal rendelkezik:

  * `code`: Kiszolgáló által definiált hibakód.

  * `message`: Egy karakterlánc, amely a hiba ember által olvasható ábrázolását adja meg.

Az ingyenes próbaverziós előfizetéssel rendelkező ügyfelek például a következő hibaüzenetet kapják meg az ingyenes kvóta kimerülése után:

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
A hibakód egy 6 számjegyből álló szám, amely a 3 számjegyből álló HTTP-állapotkódot kombinálja, majd egy 3 számjegyű számot, amely további kategorizálja a hibát. Gyakori hibakódok:

| Kód | Leírás |
|:----|:-----|
| 400000| Az egyik kérelem bemenete érvénytelen.|
| 400001| A "scope" paraméter érvénytelen.|
| 400002| A "Category" paraméter érvénytelen.|
| 400003| A nyelv megadása hiányzik vagy érvénytelen.|
| 400004| A célként megadott parancsfájl-megadó ("to script") hiányzik vagy érvénytelen.|
| 400005| Egy bemeneti szöveg hiányzik vagy érvénytelen.|
| 400006| A nyelv és a szkript kombinációja érvénytelen.|
| 400018| A forrás parancsfájl-megadása ("from script") hiányzik vagy érvénytelen.|
| 400019| A megadott nyelv egyike nem támogatott.|
| 400020| A bemeneti szöveg tömb egyik eleme nem érvényes.|
| 400021| Az API-verzió paramétere hiányzik vagy érvénytelen.|
| 400023| A megadott nyelvi párok egyike érvénytelen.|
| 400035| A forrás nyelve ("from" mező) érvénytelen.|
| 400036| A célként megadott nyelv ("to" mező) hiányzik vagy érvénytelen.|
| 400042| A megadott beállítások egyike ("beállítások" mező) érvénytelen.|
| 400043| Az ügyfél nyomkövetési azonosítója (ClientTraceId mező vagy X-ClientTranceId fejléc) hiányzik vagy érvénytelen.|
| 400050| A bemeneti szöveg túl hosszú. [Kérelmek korlátozásának](../request-limits.md)megtekintése.|
| 400064| A "Translation" paraméter hiányzik vagy érvénytelen.|
| 400070| A cél-parancsfájlok (ToScript paraméter) száma nem egyezik meg a célként megadott nyelvek számával (a paraméterrel).|
| 400071| Az érték nem érvényes a TextType.|
| 400072| A bemeneti szöveg tömbje túl sok elemet tartalmaz.|
| 400073| A parancsfájl paramétere érvénytelen.|
| 400074| A kérelem törzse nem érvényes JSON.|
| 400075| A nyelvi pár és a kategória kombináció érvénytelen.|
| 400077| Túllépte a kérések maximális méretét. [Kérelmek korlátozásának](../request-limits.md)megtekintése.|
| 400079| A és a nyelv közötti fordításra kért egyéni rendszer nem létezik.|
| 400080| A nyelv vagy a parancsfájl nem támogatja az írást.|
| 401000| A kérés nincs engedélyezve, mert a hitelesítő adatok hiányoznak vagy érvénytelenek.|
| 401015| "A megadott hitelesítő adatok a Speech API-hoz tartoznak. Ehhez a kérelemhez a Text API hitelesítő adatai szükségesek. Használja az előfizetést Translator Text API. "|
| 403000| A művelet nem engedélyezett.|
| 403001| A művelet nem engedélyezett, mert az előfizetés túllépte az ingyenes kvótát.|
| 405000| A kért erőforrás nem támogatja a kérelem metódusát.|
| 408001| A kért fordítási rendszer előkészítése folyamatban van. Próbálkozzon újra néhány perc múlva.|
| 408002| A kérelem időtúllépést várt a bejövő adatfolyamra való várakozás közben. Az ügyfél nem hozott létre kérelmet a kiszolgáló várakozási ideje alatt. Az ügyfél bármikor megismételheti a kérést anélkül, hogy később módosításokat kellene megismételnie.|
| 415000| A Content-Type fejléc hiányzik vagy érvénytelen.|
| 429000, 429001, 429002| A kiszolgáló elutasította a kérelmet, mert az ügyfél túllépte a kérelmek korlátait.|
| 500000| Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse a hibát dátum/idő szerint, a válasz fejléce X-kérelemazonosító, valamint az ügyfél-azonosítót az X-ClientTraceId kérelem fejlécében.|
| 503000| Szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra. Ha a hiba továbbra is fennáll, jelentse a hibát dátum/idő szerint, a válasz fejléce X-kérelemazonosító, valamint az ügyfél-azonosítót az X-ClientTraceId kérelem fejlécében.|

