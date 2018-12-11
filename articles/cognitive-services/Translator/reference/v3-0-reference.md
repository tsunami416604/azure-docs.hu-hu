---
title: Translator Text API 3.0-referencia
titlesuffix: Azure Cognitive Services
description: A Translator Text API 3.0 dokumentációja.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 5c952370908919deb6531e0b175063dc2657ae98
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870402"
---
# <a name="translator-text-api-v30"></a>Translator Text API 3.0-s verzió

## <a name="whats-new"></a>Újdonságok

3-as verziója a Translator Text API modern JSON-alapú webes API-t biztosít. Ez növeli a használhatóság és teljesítményét a meglévő funkciók konszolidálása kevesebb műveletek és, olyan új funkciókat biztosít.

 * Egy parancsprogram az egyik nyelven szöveg átalakítása egy másik parancsprogramra átbetűzésű.
 * Fordítási egyetlen kérelem több nyelvhez.
 * Nyelv észlelése, a fordítási és átbetűzésű egy kérelem.
 * A kifejezés található biztonsági-fordítások és a példákat a környezetben használt kifejezések keresési felkínálásával szótár.
 * Konkrétabb nyelvi észlelésének eredménye.

## <a name="base-urls"></a>Alap URL-címek

A Microsoft Translator van szolgálja ki több adatközpontban. Jelenleg találhatók 6 [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions):

* **Dél-Amerika:** 2. nyugati RÉGIÓJA és USA nyugati középső RÉGIÓJA 
* **Ázsia Csendes-óceáni:** Délkelet-Ázsia és Dél-Korea
* **Európa:** Észak-Európában és Nyugat-Európa

A Microsoft Translator Text API kérelmek vannak a legtöbb esetben az adatközpont, ahol adja meg a kérelem legközelebb eső kezeli. Adatközpont-meghibásodás a kérelmek átirányíthatók a régión kívül.

Kényszeríti a kérelem egy adott adatközpont kell kezelnie, módosítsa a kívánt területi végpont a globális végpont az API-kérelem:

|Leírás|Régió|Alap URL-cím|
|:--|:--|:--|
|Azure|Globális|  API.cognitive.microsofttranslator.com|
|Azure|Észak-Amerika|   API-nam.cognitive.microsofttranslator.com|
|Azure|Európa|  API-eur.cognitive.microsofttranslator.com|
|Azure|Ázsia és a Csendes-óceáni térség|    API-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Hitelesítés

Fizessen elő a Translator Text API vagy [Cognitive Services teljes körű](https://azure.microsoft.com/pricing/details/cognitive-services/) Microsoft Cognitive Services, és az előfizetés key (az Azure Portalon érhető el) hitelesítést használni. 

Három fejlécek előfizetését hitelesítés használatával. A táblázat az egyes használatát ismerteti:

|Fejlécek|Leírás|
|:----|:----|
|OCP-Apim-Subscription-Key|*Cognitive Services-előfizetés használata, a titkos kulcs átadásakor*.<br/>A Translator Text API-előfizetéséhez tartozó Azure titkos kulcs értéke.|
|Engedélyezés|*Cognitive Services-előfizetés használata egy hitelesítési tokent átadásakor.*<br/>A tulajdonosi jogkivonat értéke: `Bearer <token>`.|
|OCP-Apim-előfizetés-régió|*Használat a Cognitive Services teljes körű előfizetéssel egy teljes körű titkos kulcs átadásakor.*<br/>A régió, a teljes körű előfizetés értéke. Ezt az értéket nem kötelező, ha nem használ egy teljes körű előfizetéssel.|

###  <a name="secret-key"></a>Titkos kulcs
Az első lehetőség az, hogy hitelesítést végezni a `Ocp-Apim-Subscription-Key` fejléc. Egyszerűen adja meg a `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` fejlécet a kérelemhez.

### <a name="authorization-token"></a>Engedélyezési jogkivonat
Másik lehetőségként tudjon cserélni a titkos kulcs hozzáférési jogkivonat helyeként. Ez a token részét képezi, minden egyes kérelemmel a `Authorization` fejléc. Egy engedélyezési jogkivonatot beszerezni, győződjön meg arról, egy `POST` kérelem a következő URL-CÍMRE:

| Környezet     | Hitelesítési szolgáltatás URL-címe                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Az alábbiakban például kérelmek egy adott titkos kulcs token beszerzése:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

A kérelem sikeres a kódolt hozzáférési jogkivonatot a válasz törzsében egyszerű szövegként adja vissza. Az érvényes token az engedélyt a tulajdonosi jogkivonattal, a Translator szolgáltatás kerülnek.

```
Authorization: Bearer <Base64-access_token>
```

Egy hitelesítési tokent a 10 percig érvényes. A jogkivonat a Translator API-k több hívása esetén kell újra felhasználni. Azonban ha a program kéréseket hajt végre a Translator API egy kiterjesztett időszakon belül, majd a program kell kérnie egy új hozzáférési jogkivonat rendszeres időközönként (például: 8 percenként).

### <a name="all-in-one-subscription"></a>Teljes körű előfizetéssel

A legutóbbi hitelesítési lehetőség, hogy a Cognitive Services-szolgáltatás teljes körű előfizetéssel. Ez lehetővé teszi, hogy egyetlen titkos kulcs több szolgáltatás-kérelmek hitelesítéséhez. 

Ha egy teljes körű titkos kulcsot használja, meg kell adnia két hitelesítési fejléceket a kérelmét. Az első továbbítja a titkos kulcsot, a második adja meg a régiót, az Ön előfizetéséhez rendelve. 
* `Ocp-Api-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Ha a titkos kulcsot adja át a lekérdezési karakterlánc paraméterrel `Subscription-Key`, majd a lekérdezési paramétert meg kell adnia a régiót `Subscription-Region`.

Ha tulajdonosi jogkivonattal használ, be kell szereznie a jogkivonatot a régió végpontról: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

Választható régiók a következők `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, és `westus2`.

Régió szükség a teljes körű Text API-előfizetésre.

## <a name="errors"></a>Hibák

Standard hiba választ a név-érték pár nevű JSON-objektum `error`. Az érték akkor is egy JSON-tulajdonságokkal rendelkező objektum:

  * `code`: A kiszolgáló által meghatározott hibakód.

  * `message`: Egy karakterlánc, így a hiba emberek számára olvasható reprezentációját.

Ha például egy ingyenes próba-előfizetéssel rendelkező ügyfél lenne a következő hibaüzenet után az ingyenes kvótát kimerül:

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
A hibakód egy 6 jegyű számot csoportba foglalása 3 számjegyből HTTP-állapotkód további követ és 3-jegyű szám kategorizálása a hibát. Gyakori hibakódok a következők:

| Kód | Leírás |
|:----|:-----|
| 400000| A kérelem bemenetek egyike érvénytelen.|
| 400001| A "hatókör" paraméter érvénytelen.|
| 400002| A "category" paraméter érvénytelen.|
| 400003| A nyelvi típusmegadása hiányzik vagy érvénytelen.|
| 400004| A célként megadott parancsfájl formátumú ("a parancsfájl") nem található vagy érvénytelen.|
| 400005| Egy bemeneti szöveg nem található vagy érvénytelen.|
| 400006| A nyelvi és parancsfájl kombinációja érvénytelen, nem.|
| 400018| Egy adatforrás parancsfájl formátumú ("a parancsfájl") nem található vagy érvénytelen.|
| 400019| Egyet a megadott nyelv nem támogatott.|
| 400020| A bemeneti szöveg a tömbben található elemek egyike érvénytelen.|
| 400021| Az API verzió paramétere hiányzik vagy érvénytelen.|
| 400023| A megadott nyelvre a pár egyik není platná.|
| 400035| A Forrásnyelv ("feladó" mező) nem érvényes.|
| 400036| A célként megadott nyelv ("" mező) nem található vagy érvénytelen.|
| 400042| A beállítások megadott ("Beállítások" mező) egyike nem érvényes.|
| 400043| Az ügyfél-nyomkövetési Azonosítót (ClientTraceId mező vagy X-ClientTranceId fejléc) nem található vagy érvénytelen.|
| 400050| A bemeneti szöveg neve túl hosszú.|
| 400064| A "fordítási" paramétere hiányzik vagy érvénytelen.|
| 400070| A célként megadott parancsprogramok (ToScript paraméter) száma nem egyezik meg a cél nyelvek (, paraméterben) száma.|
| 400071| Az érték érvénytelen TextType.|
| 400072| A bemeneti szöveg a tömb túl sok elemet tartalmaz.|
| 400073| A parancsfájl paraméter nem érvényes.|
| 400074| A kérelem törzse nem érvényes JSON.|
| 400075| A nyelvi pár és kategória kombináció nem lesz érvényes.|
| 400077| Kérelem maximális mérete túl lett lépve.|
| 400079| Az egyéni rendszer közötti fordítás és nyelv közül a kért nem létezik.|
| 401000| A kérelem nem engedélyezett, mert hitelesítő adatok hiányoznak vagy érvénytelen.|
| 401015| "A megadott hitelesítő adatok vannak a beszédfelismerő API-hoz. A kérelem hitelesítő adatok szükségesek a szöveges API-hoz. Használja a Translator Text API-előfizetés."|
| 403000| A művelet nem engedélyezett.|
| 403001| A művelet nem engedélyezett, mert az előfizetés túllépte az ingyenes kvótát.|
| 405000| Kérelmi metódus nem támogatott a kért erőforrás.|
| 408001| A kért egyéni fordítási rendszer még nem érhető el. Próbálkozzon újra néhány perc múlva.|
| 415000| A Content-Type fejléc nem található vagy érvénytelen.|
| 429000, 429001, 429002| A kiszolgáló elutasította a kérelmet, mert az ügyfél túl sok kérelmet küld. Kérelmek szabályozása elkerülése érdekében gyakoriságának csökkentése.|
| 500 000 összeget| Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse be a dátum/idő hiba, kérjen azonosító X-RequestId: válaszfejléc, és a kérelem fejlécében X-ClientTraceId ügyfél-azonosítója.|
| 503000| Szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra. Ha a hiba továbbra is fennáll, jelentse be a dátum/idő hiba, kérjen azonosító X-RequestId: válaszfejléc, és a kérelem fejlécében X-ClientTraceId ügyfél-azonosítója.|

