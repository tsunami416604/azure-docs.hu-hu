---
title: Translator Speech API languages metódus
titleSuffix: Azure Cognitive Services
description: Használja a Translator Speech API languages metódust.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 46ac3928238382f56db5a799226bd3d9243b7ca2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966416"
---
# <a name="translator-speech-api-languages"></a>Translator Speech API: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech folyamatosan bővíti a szolgáltatásai által támogatott nyelvek listáját. Ezzel az API-val megismerheti a Translator Speech szolgáltatással jelenleg használható nyelvek készletét.

Az API-t az elérhető nyelvek beszerzését bemutató kód minták a [Microsoft Translator GitHub webhelyéről](https://github.com/MicrosoftTranslator)érhetők el.

## <a name="implementation-notes"></a>Implementációs megjegyzések

### <a name="get-languages"></a>/Languages beolvasása

Számos nyelv áll rendelkezésre a beszéd átírásához, az átmásolt szöveg lefordításához és a fordítás szintetizált beszédének létrehozásához.

Az ügyfél a `scope` lekérdezési paramétert használja annak meghatározására, hogy mely nyelveket érdekli.

* **Beszéd – szöveg:** A lekérdezési `scope=speech` paraméter használatával lekérheti a beszéd szövegbe való átírásához elérhető nyelvek készletét.
* **Szöveg fordítása:** A lekérdezési `scope=text` paraméter használatával lekérheti az átmásolt szöveg lefordításához elérhető nyelvek készletét.
* **Szöveg – beszéd:**  A lekérdezési `scope=tts` paraméterrel lekérdezheti a szövegeket és hangokat, amelyekkel a fordítás szövege visszaállítható a beszédbe.

Egy ügyfél egyszerre több készletet is lekérhet a választható lehetőségek vesszővel tagolt listájának megadásával. Például: `scope=speech,text,tts`.

A sikeres válasz egy JSON-objektum, amely egy tulajdonsággal rendelkezik az egyes kért készletekhez.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Mivel az ügyfél a `scope` lekérdezési paraméterrel kiválaszthatja, hogy a támogatott nyelvek mely készletei legyenek visszaadva, a tényleges válasz csak a fent látható tulajdonságok egy részhalmazát veheti fel.

Az egyes tulajdonságokhoz megadott érték a következő.

### <a name="speech-to-text-speech"></a>Beszéd és szöveg (beszéd)

A beszéd – szöveg tulajdonsághoz `speech`társított érték (kulcs, érték) párok szótára. Az egyes kulcsok a beszéd és a szöveg között támogatott nyelvet azonosítanak. A kulcs az az azonosító, amelyet az ügyfél továbbít az API-nak. A kulcshoz társított érték egy olyan objektum, amely a következő tulajdonságokkal rendelkezik:

* `name`: A nyelv megjelenítendő neve.
* `language`: A társított írott nyelv nyelvi címkéje. Tekintse meg az alábbi "szöveges tranzakció" című szakaszt.
Példa:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Szöveg fordítása (szöveg)

A `text` tulajdonsághoz társított érték egy olyan szótár is, amelyben az egyes kulcsok a szöveges fordításhoz támogatott nyelvet azonosítanak. A kulcshoz társított érték a nyelvet írja le:

* `name`: A nyelv megjelenítendő neve.
* `dir`: Írásirányát, amely `rtl` jobbról balra író nyelvekhez vagy `ltr` balról jobbra írt nyelvekhez használható.

Példa:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Szöveg – beszéd (TTS)

A szöveg-beszéd tulajdonsághoz (TTS) társított érték egy olyan szótár is, amelyben az egyes kulcsok támogatott hangfelismerést azonosítanak. A Hangobjektum attribútumai a következők:

* `displayName`: A hang megjelenítendő neve.
* `gender`: A hang neme (férfi vagy nő).
* `locale`: A hang nyelvi címkéje az elsődleges nyelvi alkód és a régió alkód.
* `language`: A társított írott nyelv nyelvi címkéje.
* `languageName`: A nyelv megjelenítendő neve.
* `regionName`: A régió megjelenítendő neve ehhez a nyelvhez.

Példa:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Honosítás
A szolgáltatás az összes nevet visszaadja az "Accept-Language" fejléc nyelvén, a szöveges fordításban támogatott összes nyelv esetében.

### <a name="response-class-status-200"></a>Válasz osztály (200-es állapot)
A támogatott nyelvek készletét leíró objektum.

ModelExample értéke:

Langagues {Speech (objektum, nem kötelező), szöveg (objektum, nem kötelező), TTS (objektum, nem kötelező)}

### <a name="headers"></a>Fejlécek

|Fejléc|Leírás|Type|
:--|:--|:--|
X-RequestId|A kiszolgáló által a kérelem azonosítására és a hibaelhárítási célokra használt érték.|Karakterlánc|

### <a name="parameters"></a>Paraméterek

|Paraméter|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|
|api-version    |Az ügyfél által kért API-verzió. Az engedélyezett értékek a `1.0`következők:.|query|Karakterlánc|
|scope  |A támogatott nyelvek és Hangok készletei az ügyfélnek való visszatéréshez. Ez a paraméter a kulcsszavak vesszővel tagolt listájaként van megadva. A következő kulcsszavak érhetők el:<ul><li>`speech`: A beszédfelismerés által támogatott nyelvek készletét biztosítja.</li><li>`tts`: A szöveg-beszéd átalakításhoz támogatott hangokat biztosít.</li><li>`text`: A szöveg fordításához támogatott nyelvek készletét adja meg.</li></ul>Ha nincs megadva érték, a rendszer az `scope` alapértelmezett értékeket adja meg. `text`|query|Karakterlánc|
|X-ClientTraceId    |Egy ügyfél által generált GUID, amely egy kérés nyomkövetésére szolgál. A problémák hibaelhárítása érdekében az ügyfeleknek minden kéréssel új értéket kell megadniuk, és be kell jelentkezniük.|header|Karakterlánc|
|Elfogadás – nyelv    |A válasz mezői a nyelvek vagy régiók nevei. Ezzel a paraméterrel határozhatja meg, hogy a rendszer milyen nyelven adja vissza a neveket. A nyelvet úgy kell megadni, hogy megfelelően formázott BCP 47 nyelvi címkét adjon meg. Válasszon ki egy címkét a `text` hatókörrel visszaadott nyelvi azonosítók listájából. A nem támogatott nyelvek esetében a nevek angol nyelven vannak megadva.<br/>Például a `fr` nevek francia nyelven való meghívásához használja az értéket, vagy használja `zh-Hant` a nevet a hagyományos kínai nyelven.|header|Karakterlánc|

### <a name="response-messages"></a>Válaszüzenetek

|HTTP-állapotkód|Reason|
|:--|:--|
|400|Hibás kérelem. Ellenőrizze a bemeneti paramétereket, hogy azok érvényesek legyenek. A válasz objektum részletesebb leírást tartalmaz a hibáról.|
|429|Túl sok kérés van.|
|500|Hiba történt. Ha a hiba továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosítóját (X-ClientTraceId) vagy a kérelem azonosítóját (X-kérelemazonosító).|
|503|A kiszolgáló átmenetileg nem érhető el. Próbálkozzon újra a kéréssel. Ha a hiba továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosítóját (X-ClientTraceId) vagy a kérelem azonosítóját (X-kérelemazonosító).|
