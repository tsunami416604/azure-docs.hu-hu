---
title: A Microsoft Translator Diktálásfelismerési API nyelvek metódus |} Microsoft Docs
titleSuffix: Cognitive Services
description: A Microsoft Translator Diktálásfelismerési API nyelvek módszert.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/18
ms.author: v-jansko
ms.openlocfilehash: 5396e3be17345c3c36197a9b6cbace86e1f574c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349751"
---
# <a name="speech-api-languages"></a>Diktálásfelismerési API: nyelvek

A Microsoft Translator folyamatosan bővíti a szolgáltatások által támogatott nyelvek listáját. Ez az API segítségével felderítése a meghatározott nyelv érhető el a beszédfelismerés fordítási szolgáltatással való használatra.

Kódminták, amely tartalmazza az API felé elérhető nyelvek használatát érhetők el a [Microsoft Translator Github hely](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

/Languages beolvasása 

Nyelvek széles készlete beszéd-lefordítani a transcribed szöveg, és a fordítás szintetizált átírni érhető el.

Egy ügyfél használ a `scope` paraméterrel adjuk meg, mely nyelvek készleteinek azt iránt érdeklődik lekérdezése.

* **Beszéd-szöveg:** használja a következő lekérdezésparaméter `scope=speech` beolvasása a meghatározott beszéd átírni szöveggé elérhető nyelv.
* **Szöveg fordítási:** használja a következő lekérdezésparaméter `scope=text` beolvasni a kért szöveg lefordítani a nyelveket készlete.
* **Szöveg-beszéd átalakítás:** használja a következő lekérdezésparaméter `scope=tts` nyelveket és lefordított szöveg szintetizálásához újra üzembe a beszédfelismerés rendelkezésére hangok beolvasása.

Egy ügyfél egyszerre lekérheti több ad meg a választási lehetőségek vesszővel tagolt listája. Például: `scope=speech,text,tts`.

A sikeres válasz egy JSON-objektum tulajdonsággal minden kért set.

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

Mivel az ügyfél használhatja a `scope` vissza kell adni a lekérdezési paraméter mely támogatott nyelvek készleteinek kiválasztásához, előfordulhat, hogy egy tényleges válasz csak tartalmazza a fent látható az összes tulajdonság egy részét.

Az egyes tulajdonság megadott értéke a következő.

### <a name="speech-to-text-speech"></a>Beszéd-szöveg (beszéd)

A beszéd-szöveg tulajdonság társított értéket `speech`, egy szótár (kulcs, érték) párokat. Minden kulcs támogatja a beszéd-szöveg azonosítja. A kulcs az azonosító, amely az API-t, hogy az ügyfél továbbítja. A kulcshoz hozzárendelt érték egy objektum a következő tulajdonságokkal:

* `name`: Nyelv megjelenítendő nevét.
* `language`: A kapcsolódó nyelvi írása a címke nyelv. Olvassa el a "Szöveg traznakció".
Példa::

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Szöveg fordítási (szöveg)

A rendelt érték, amely a `text` tulajdonság akkor is a szótár, ahol minden kulcs azonosítja a szöveg fordítás támogatja. A kulcshoz hozzárendelt értéket a nyelvi ismerteti:

* `name`: Nyelv megjelenítendő nevét.
* `dir`: Ami irányú `rtl` jobbról balra író nyelvek esetén vagy `ltr` jobbról balra író nyelvek esetén.

Példa::

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Szöveg-beszéd átalakítás (tts)

A szöveg-beszéd átalakítás tulajdonság, a szöveg-beszéd átalakítás, társított érték is egy szótár, ahol minden kulcs azonosítja egy támogatott hang. Egy hang objektum attribútumai a következők:

* `displayName`: A hang megjelenített neve.
* `gender`: A hang (hímivarú vagy nőivarú) nemének.
* `locale`: A hang és az elsődleges nyelv alkód régió alkód nyelvi címkéjének.
* `language`: A kapcsolódó nyelvi írása a címke nyelv.
* `languageName`: Nyelv megjelenítendő nevét.
* `regionName`: Ezt a nyelvet a régió nevét jeleníti meg.

Példa::

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
A szolgáltatás összes nevét az "Elfogadás-Language" fejlécet, az összes szöveg fordítási által támogatott nyelvek nyelvét adja eredményül.

### <a name="response-class-status-200"></a>Válasz osztály (állapot 200)
A támogatott nyelvek készlete leíró objektum.

ModelExample érték: 

{Beszéd (objektum, nem kötelező), szöveg (objektum, nem kötelező), (objektum, nem kötelező) tts} Langagues

### <a name="headers"></a>Fejlécek

|Fejléc|Leírás|Típus|
:--|:--|:--|
X-kérelemazonosító|Érték a kiszolgáló azonosítására, a kérelem által generált és használják hibaelhárítási célból.|sztring|

### <a name="parameters"></a>Paraméterek

|Paraméter|Leírás|A paraméter típusa|Adattípus|
|:--|:--|:--|:--|
|API-verzió    |Az API-t, az ügyfél által kért verzióját. Két érték engedélyezett: `1.0`.|lekérdezés|sztring|
|scope  |Beállítja a támogatott nyelvek vagy hangok vissza az ügyfélnek. Ez a paraméter van megadva kulcsszavak vesszővel tagolt listáját. A következő kulcsszavak érhetők el:<ul><li>`speech`: Tartalmaz a beszédfelismerés átírni támogatott nyelveket.</li><li>`tts`: A több biztosít hangok szöveg-beszéd átalakítás támogatott.</li><li>`text`: A meghatározott nyelv támogatott fordítása szöveget biztosít.</li></ul>Ha nincs megadva érték, értékének `scope` alapértelmezés szerint az `text`.|lekérdezés|sztring|
|X-ClientTraceId    |Ügyfél által létrehozott GUID használni egy kérelmet nyomon követésére. Problémák hibaelhárításának megkönnyítéséhez ügyfelek kell minden egyes kérelemmel adjon meg új értéket, és jelentkezzen azt.|header|sztring|
|Fogadja el nyelv    |A válaszban szereplő mezők régiók és nyelvek neve. Ez a paraméter használatával határozza meg a nyelvet, amelyben a nevét adja vissza. A nyelv, adja meg a megfelelően formázott BCP 47 nyelvi címke van megadva. Válassza ki a címke a nyelvi azonosítót ad vissza, amelyben a a `text` hatókör. Nem támogatott nyelvek esetén a nevek szerepelnek az angol nyelv.<br/>Használja például a érték `fr` kérése nevek francia nyelven vagy értéket `zh-Hant` hagyományos kínai kérelem nevek.|header|sztring|
    
### <a name="response-messages"></a>A válaszüzenetek

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérés. Ellenőrizze, érvényes azok bemeneti paramétereket. A válasz objektum tartalmazza a hiba részletes leírását.|
|429|Túl sok kérelmet.|
|500|Hiba történt. Ha a probléma továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosító (X-ClientTraceId), vagy a kérelem azonosítója (X-kérelemazonosító).|
|503|A kiszolgáló ideiglenesen nem érhető el. Próbálja megismételni a kérelmet. Ha a probléma továbbra is fennáll, jelentse az ügyfél nyomkövetési azonosító (X-ClientTraceId), vagy a kérelem azonosítója (X-kérelemazonosító).|
