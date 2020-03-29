---
title: Emberi címkével ellátott transzkripciós irányelvek - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerés pontosságának javítása érdekében, például ha szavakat törölnek vagy helytelenül helyettesítenek, használhat unk emberi címkével ellátott átiratokat a hangadatokkal együtt. Az emberi címkével ellátott átiratok szóról-szóra, szó szerinti átiratok egy hangfájlról.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806062"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Hogyan hozzunk létre az emberi címkével ellátott átiratok

Ha javítani szeretné a felismerés pontosságát, különösen a szavak törlésekor vagy helytelen helyettesítésekor felmerülő problémákat, akkor a hangadatokkal együtt használjon emberi címkével ellátott átiratokat. Mik azok az emberi címkével ellátott átiratok? Ez egyszerű, szóról szóra, szó szerinti átiratai egy hangfájlnak.

A felismerés javításához nagy mintára van szükség az átírási adatokból, ezért javasoljuk, hogy 10 és 1000 óra közötti átírási adatokat biztosítson. Ezen az oldalon áttekintjük a kiváló minőségű átiratok létrehozását segítő irányelveket. Ez az útmutató területi beállítás szerint van felosztva, az amerikai angol, a mandarin kínai és a német nyelvű szakaszok.

## <a name="us-english-en-us"></a>Amerikai angol (en-US)

Az angol hanganyagok emberi címkével ellátott átiratait egyszerű szövegként kell megadni, csak ASCII karakterek használatával. Kerülje a Latin-1 vagy Unicode írásjelek használatát. Ezeket a karaktereket gyakran véletlenül adják hozzá, amikor szöveget másol egy szövegszerkesztő alkalmazásból, vagy adatokat kaparaj le a weboldalakról. Ha ezek a karakterek jelen vannak, frissítse őket a megfelelő ASCII helyettesítéssel.

Íme, néhány példa:

| Kerülendő karakterek | Substitution | Megjegyzések |
| ------------------- | ------------ | ----- |
| "Helló világ" | "Helló világ" | A nyitó és záró idézőjelek megfelelő ASCII karakterekkel lettek helyettesítve. |
| John napja | John napja | Az aposztróf ot a megfelelő ASCII karakterrel helyettesítették. |
| jó volt - nem, nagyszerű volt! | jó volt... nem, nagyszerű volt! | Az em kötőjelet két kötőjel váltotta fel. |

### <a name="text-normalization-for-us-english"></a>Szöveg normalizálása amerikai angol

A szöveg normalizálása a szavak egységes formátumba való átalakítása, amelyet a modell betanításakor használnak. Néhány normalizálási szabály automatikusan vonatkozik a szövegre, azonban javasoljuk, hogy az emberi címkével ellátott átírási adatok előkészítésekor használja ezeket az irányelveket:

- Írja le a rövidítéseket szavakkal.
- Írjon ki nem szabványos numerikus karakterláncokat szavakkal (például könyvelési kifejezésekkel).
- A nem betűrendes karaktereket vagy a vegyes alfanumerikus karaktereket ejtik.
- A szavakként kiejtett rövidítéseket nem szabad szerkesztetten kell (például "radar", "lézer", "RAM" vagy "NATO").
- Írja ki azokat a rövidítéseket, amelyekkülön betűként vannak kiejteni, és minden betűt szóközválaszt.

Íme néhány példa a normalizálásra, amelyet végre kell hajtania az átíráson:

| Eredeti szöveg               | Szöveg normalizálás után              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Orvos Bruce Banner                   |
| James Bond, 007 (amerikai)             | James Bond, dupla oh hét           |
| Ke$ha                       | Kesha                                 |
| Milyen hosszú a 2x4         | Milyen hosszú a kettő a négy           |
| A találkozó 1-3-ig tart. | A találkozó 1-től 15 óráig tart. |
| A vércsoportom O+         | A vércsoportom O pozitív.           |
| A víz H20                | A víz H 2 O                        |
| Játssz OU812 a Van Halen     | Játssz O U 8 1 2 által Van Halen           |
| UTF-8 AJ-vel              | U T F 8 anyagjegyzékkel                      |

A következő normalizálási szabályok automatikusan vonatkoznak az átírásokra:

- Használjon kisbetűket.
- Távolítson el minden írásjelet, kivéve az aposztrófot a szavakban.
- A számokat szóra/kimondott formára, például dollárösszegekre bontva.

Íme néhány példa a normalizálás automatikusan végzett az átírás:

| Eredeti szöveg                          | Szöveg normalizálás után          |
| -------------------------------------- | --------------------------------- |
| "Szent tehén!" mondta Batman.               | szent tehén mondta Batman              |
| "Mi van?" Mondta Batman társa, Robin. | mit mondott Batman társa Robin |
| Menj, kapd el őket!                            | megy kap em                         |
| Én kettős csuklós                     | Én kettős csuklós                |
| 104 Elm utca                         | egy oh négy Elm utca            |
| Hangoljon a 102,7-re                          | dallam egy oh két pont hét    |
| Pi körülbelül 3,14                       | pi körülbelül három pont egy négy  |
| 3,14-be kerül \$                        | három tizennégy           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarin kínai (zh-CN)

A mandarin kínai hanganyag emberi címkével ellátott átiratait UTF-8 kódolva kell, hogy legyen egy bájtsorrend-jelölővel. Kerülje a félszélességű írásjelek használatát. Ezek a karakterek véletlenül is szerepelhetnek, amikor előkészíti az adatokat egy szövegszerkesztő programban, vagy adatokat kapar a weboldalakról. Ha ezek a karakterek jelen vannak, frissítse őket a megfelelő teljes szélességű helyettesítéssel.

Íme, néhány példa:

| Kerülendő karakterek | Substitution   | Megjegyzések |
| ------------------- | -------------- | ----- |
| "A"( """ | "A"( """ | A nyitó és záró idézőjelek megfelelő karakterekkel lettek helyettesítve. |
| A? | A?| A kérdőjel et megfelelő karakterrel helyettesítették. |

### <a name="text-normalization-for-mandarin-chinese"></a>Szöveg normalizálása mandarin kínai

A szöveg normalizálása a szavak egységes formátumba való átalakítása, amelyet a modell betanításakor használnak. Néhány normalizálási szabály automatikusan vonatkozik a szövegre, azonban javasoljuk, hogy az emberi címkével ellátott átírási adatok előkészítésekor használja ezeket az irányelveket:

- Írja le a rövidítéseket szavakkal.
- Írjon ki numerikus karakterláncokat kimondott formában.

Íme néhány példa a normalizálásra, amelyet végre kell hajtania az átíráson:

| Eredeti szöveg | Szöveg normalizálás után |
| ------------- | ------------------------ |
| 21 | A |
| 3, 504 | A (啦ン) |

A következő normalizálási szabályok automatikusan vonatkoznak az átírásokra:

- Az összes írásjel eltávolítása
- Számok kibontása szóbeli formába
- Teljes szélességű betűk átalakítása félszélességű betűkké
- Nagybetűk használata minden angol szóhoz

Íme néhány példa a normalizálás automatikusan végzett az átírás:

| Eredeti szöveg | Szöveg normalizálás után |
| ------------- | ------------------------ |
| 3.1415 | A (sz) |
| ¥ 3,5 | 啦ンンン |
| w f y z | W F Y Z |
| 1992 啦 8 啦 8 啦 8 啦 | A 啦ンンンンン |
| 啦 a啦啦啦啦? | A 啦-t a |
| 啦 5:00 啦ン | 啦ンンン ンン |
| 21-21 | A |

## <a name="german-de-de-and-other-languages"></a>Német (de-DE) és más nyelvek

A német hang (és más nem angol vagy mandarin kínai nyelvek) emberi címkével ellátott átiratoknak UTF-8-nak kell lenniük, bájtsorrend-jelölővel kódolva. Minden hangfájlhoz egy emberi címkével ellátott átiratot kell biztosítani.

### <a name="text-normalization-for-german"></a>Szöveg normalizálása német nyelven

A szöveg normalizálása a szavak egységes formátumba való átalakítása, amelyet a modell betanításakor használnak. Néhány normalizálási szabály automatikusan vonatkozik a szövegre, azonban javasoljuk, hogy az emberi címkével ellátott átírási adatok előkészítésekor használja ezeket az irányelveket:

- Írjon tizedespontokat ""-ként, és ne ""..
- Az időelválasztókat " és nem "" néven írják. (például: 12:00 Uhr).
- Rövidítések, mint a "ca.". nem cserélik ki. Javasoljuk, hogy a teljes szóbeli űrlapot használja.
- A program eltávolítja a négy \*fő matematikai operátort (+, -, , és /). Javasoljuk, hogy helyettesítse őket az írott formában: "plusz", "mínusz", "mal" és "geteilt".
- Az összehasonlító operátorok eltávolítása (=, < és >). Javasoljuk, hogy cserélje ki őket a "gleich", "kleiner als" és a "grösser als" helyett.
- Írja frakciók, mint például 3/4, írásban formában (például: "drei viertel" helyett 3/4).
- Cserélje ki az "€" szimbólumot az "Euro" feliratú űrlapra.

Íme néhány példa a normalizálásra, amelyet végre kell hajtania az átíráson:

| Eredeti szöveg    | Szöveg a felhasználó normalizálása után | Szöveg a rendszer normalizálása után       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12,23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plusz 3 mínusz 4              | zwei plusz drei mínusz vier             |

A következő normalizálási szabályok automatikusan vonatkoznak az átírásokra:

- Az összes szöveghez használjon kisbetűket.
- Távolítson el minden írásjelet, beleértve a különböző idézőjeleket is ("teszt", "teszt", "teszt", "teszt" és «teszt» rendben van).
- Dobja sorok bármilyen különleges karakter ebből a készletből: ¢ ¤ ¥ ¥ § © ª ¬ ® ° ± ² μ × ÿ Ø ¬ ¬ ¬ .
- A számokat szóban álló űrlapra bontva, beleértve a dollár vagy euró összegeket is.
- Fogadja umlauts csak a, o, és te. Mások helyébe a "th", vagy el kell dobni.

Íme néhány példa a normalizálás automatikusan végzett az átírás:

| Eredeti szöveg    | Szöveg normalizálás után |
| ---------------- | ------------------------ |
| Frankfurter Gyűrű | frankfurter gyűrű         |
| ¡Eine Frage!     | eine frage (eine frage)               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Következő lépések

- [Az adatok előkészítése és tesztelése](how-to-custom-speech-test-data.md)
- [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
- [Az adatok kiértékelése](how-to-custom-speech-evaluate-data.md)
- [A modell betanítása](how-to-custom-speech-train-model.md)
- [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
