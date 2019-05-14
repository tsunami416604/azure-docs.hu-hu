---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az űrlap felismerő elemezni az űrlap és a táblák adatait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 8fb382227c71fce7ebe062057adf5edfb90a1a92
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601623"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

Az Azure Form Recognizer egy Cognitive Services-szolgáltatás, amely gépi tanulási technológiával azonosítja és nyeri ki a kulcs-érték párokat és táblaadatokat az űrlapdokumentumokból. Ezután strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. Az egyéni űrlap felismerő modell egy egyszerű REST API használatával csökkenthető, és egyszerűen integrálhatja a munkafolyamat vagy alkalmazás hívása. A kezdéshez csak kell öt űrlap dokumentumok vagy egy ugyanolyan típusú, mint a bemeneti anyagot találhat az üres formájában. Gyorsan hozzáférhet pontos eredményeket, amelyek az adott tartalom (nagy erőforrásigényű) kézi beavatkozás vagy kiterjedt data science szakértelem nélkül.

## <a name="request-access"></a>Hozzáférés kérése
Űrlap felismerő egy korlátozott hozzáférésű előzetes verzióban érhető el. Töltse ki az előzetes verzió eléréséhez, és küldje el a [űrlap felismerő hozzáférési kérelem](https://aka.ms/FormRecognizerRequestAccess) űrlap. Az űrlap kér információt, a vállalat és a felhasználói forgatókönyvet, amely űrlap felismerő fog használni. Ha a kérést az Azure Cognitive Services-csapat jóváhagyja, kap egy e-mailt a szolgáltatás eléréséhez szükséges utasításokat.

## <a name="what-it-does"></a>Művelet

Amikor a bemeneti adatokat küld, az algoritmus, betanítja, fürtök típusok, az űrlapok felderíti, mely kulcsok és a táblák jelen, és megtanulja kulcsok értékek és táblák bejegyzések. A nem felügyelt tanítás lehetővé teszi, hogy a modell manuális adatcímkézé, valamint intenzív kódolás és karbantartás nélkül megértse az elrendezést, illetve a mezők és bejegyzések közötti kapcsolatokat. Ezzel szemben az előre betanított machine learning-modellek szabványos igényelnek, és kevésbé pontos bemeneti anyagot, amely eltér a hagyományos formátumok, mint például az iparág-specifikus űrlapok együtt használva.

Után a modell betanítását, tesztelése és újratanítása azt, és végül, amellyel igény szerint további űrlapok adatok megbízhatóan kinyerése.

## <a name="what-it-includes"></a>Mit tartalmaz?

Űrlap felismerő REST API-ként érhető el. Létrehozását, betanítását és pontozását egy modellt az API meghívásával. Ha azt szeretné, a helyi Docker-tárolóban futtathatja a modellt.

## <a name="input-requirements"></a>A bemeneti követelmények

Űrlap felismerő működik a fenti követelményeknek megfelelő bemeneti dokumentum:

* Formátumban kell lennie, JPG, PNG vagy PDF-fájl (szöveges vagy beolvasott). Beágyazott szöveg PDF-fájlok nincsenek ajánlott, mert nem fordulhat elő hiba karakterfelismerés és helyen.
* Fájl mérete kisebb, mint 4 megabájt (MB) kell lennie.
* Képek a dimenziók 50 x 50 képpont méretű és 4200 x 4200 képpont között kell lennie.
* Nyomtatott dokumentumok gépről beolvasott, űrlapok magas színvonalú vizsgálatok kell lennie.
* Szöveg kell használnia a Latin ábécében (angol nyelvű karakter).
* Adatok (nem kézzel írott) kell nyomtatni.
* Kulcsokat és értékeket kell tartalmaznia.
* Kulcsokkal fölött, vagy az értékek, de nem alábbi balra vagy jobbra is megjelenhetnek.

Űrlap felismerő jelenleg nem támogatja az ilyen típusú bemeneti adatok:

* Összetett táblázatok (beágyazott táblákat, egyesített fejlécek vagy cellák és így tovább).
* Jelölőnégyzetek vagy választógomb gombot.
* PDF-dokumentumok hosszabb, mint az 50.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

**1. lépés:** Űrlap felismerő erőforrás létrehozása az Azure Portalon.

**2. lépés:** Próbálja ki a rövid útmutató a gyakorlatban használható tapasztalatot:
* [Rövid útmutató: Űrlap felismerő modellek betanítása és űrlap adatokat nyerhet ki a REST API-val a curl használatával](quickstarts/curl-train-extract.md)
* [Rövid útmutató: Űrlap felismerő modellek betanítása és űrlap adatokat nyerhet ki a REST API a pythonnal](quickstarts/python-train-extract.md)

Azt javasoljuk, hogy az ingyenes szolgáltatást használja, ha Ön éppen technológia megismerésére, de ne feledje, hogy ingyenes oldalak számát havi 500 oldalak korlátozódik.

**3. lépés:** A REST API-k áttekintése

A következő API-k használatával betanítása és strukturált adatok kinyerése az űrlapok.

| REST API | Leírás |
|-----|-------------|
| Betanítás | Az űrlapok elemzése ugyanabból a típusú öt űrlapok vagy egy üres képernyő használatával új modell betanításához.  |
| Elemzés  |Egyetlen dokumentum, kulcs-érték párok és táblák kibontani az űrlapot a egyéni modell adatfolyamként átadott elemzése.  |

Fedezze fel a [REST API-referencia dokumentumban](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Ez a szolgáltatás érhető el egy [előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) alatt Azure-szolgáltatás a [Online szolgáltatási feltételek](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Csakúgy, mint a cognitive services összes szolgáltatása, az űrlap felismerő szolgáltatással a fejlesztők célszerű tisztában lennie a Microsoft-házirendek a vásárlói adatokat. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Végezze el a [rövid](quickstarts/curl-train-extract.md) való használatának első lépései a [felismerő API](https://aka.ms/form-recognizer/api).
