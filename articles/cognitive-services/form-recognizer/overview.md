---
title: Mi az űrlap felismerő?
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az űrlap felismerő elemezni az űrlap és a táblák adatait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: df3db534550e709e40cc94d5f951056d93a1003e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027074"
---
# <a name="what-is-form-recognizer"></a>Mi az űrlap felismerő?

Azure űrlap felismerő az kognitív szolgáltatás, amely gépi tanulási technológia használatával azonosíthatja és kulcs-érték párok, és a táblák adatainak kinyerése űrlap dokumentumok. Majd exportálja a strukturált adatok, amely tartalmazza a kapcsolatokat az eredeti fájlt. Az egyéni űrlap felismerő modell annak érdekében, hogy csökkenti az összetettséget és könnyedén beépítheti azt a munkafolyamat vagy az alkalmazások egy egyszerű REST API használatával hívható meg. Csak akkor kell öt űrlap dokumentumok vagy egy ugyanolyan típusú, mint a bemeneti anyagot találhat az első lépések üres formájában. Is processzoraival gyors, pontosan, és fenntarthatja az adott tartalomra (nagy erőforrásigényű) kézi beavatkozás vagy kiterjedt data science szakértelem nélkül.

## <a name="request-access"></a>Hozzáférés kérése
Űrlap felismerő egy korlátozott hozzáférésű az előzetes verzióként érhető el. Az előzetes verzió eléréséhez, kérjük töltse ki és küldje el a [Cognitive Services űrlap felismerő hozzáférési kérelem](https://aka.ms/FormRecognizerRequestAccess) űrlap. Az űrlap kér információt, a vállalat és a felhasználói forgatókönyvet, amely űrlap felismerő fog használni. Ha a kérést az Azure Cognitive Services-csapat jóváhagyja, kap egy e-mailt az útmutatást nyújt a szolgáltatás eléréséhez.

## <a name="what-it-does"></a>Művelet

Amikor a bemeneti adatokat küld, az algoritmus, betanítja, fürtök típusok, az űrlapok felderíti, mely kulcsok és a táblák jelen, és megtanulja kulcsok értékek és táblák bejegyzések. Felügyeletlen learning lehetővé teszi, hogy a modell megértéséhez nélkül az adatok manuális címkézésre, vagy nagy számításigényű kódolása és karbantartási elrendezést és a mezők és bejegyzések közötti kapcsolatokat. Ezzel szemben az előre betanított gépi tanulási modellek szabványos igényelnek, és kevésbé pontosak, a bemeneti anyagot, amely eltér a hagyományos formátumok, mint iparág-specifikus űrlapok.

Miután a modell tanítása, tesztelése, újratanítása és idővel, amellyel igény szerint további űrlapok adatok megbízhatóan kinyerése.

## <a name="what-it-includes"></a>Mit tartalmaz?

Űrlap felismerő REST API-ként érhető el. Hozhat létre, betanítását és pontozását egy modellt az API meghívásával, és igény szerint futtathatja a modell egy helyi Docker-tárolóban.

## <a name="input-requirements"></a>A bemeneti követelmények

Űrlap felismerő működik a bemeneti dokumentumokon, amelyek megfelelnek a következő követelményeknek:

* JPG, PNG vagy PDF formátumban (szöveges vagy beolvasott). Szöveg beágyazott PDF-fájlok előnyösek, mert nem fordulhat elő hiba karakterfelismerés és helyen.
* Fájl mérete kisebb, mint 4 megabájt (MB) kell lennie.
* Képek dimenziók és kell esnie 50 x 50 4200 x 4200 képpont
* Nyomtatott dokumentumok gépről beolvasott, ha űrlapok a magas színvonalú vizsgálatok kell lennie
* A Latin ábécében (angol nyelvű karakter) kell használnia.
* A nyomtatott adatokat (nem kézzel írott)
* Tartalmaznia kell a kulcsok és értékek
* Kulcsokkal fölött, vagy az értékek, de nem alábbi balra vagy jobbra is megjelenhetnek.

Ezenkívül űrlap felismerő még nem támogatja a következő típusú bemeneti adatok:

* Összetett táblázatok (beágyazott táblákat, egyesített fejlécek vagy cellák és így tovább) 
* Jelölőnégyzetek vagy választógomb gomb
* Több mint 50 oldalak PDF-dokumentumok

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

**1. lépés:** Űrlap felismerő erőforrás létrehozása az Azure Portalon.

**2. lépés:** Próbálja ki a rövid útmutató a gyakorlatban használható tapasztalatot:
* [Rövid útmutató: Egy űrlap felismerő modell betanítását, és bontsa ki az űrlapadatok REST API használatával a curl használatával](quickstarts/curl-train-extract.md)
* [Rövid útmutató: Egy űrlap felismerő modell betanítását, és bontsa ki az űrlapadatok REST API-val, a Python](quickstarts/python-train-extract.md)

Azt javasoljuk, az ingyenes szolgáltatás tanulási célokra, de vegye figyelembe, hogy ingyenes oldalak számát havi 500 oldalakra korlátozott.

**3. lépés:** Tekintse át a REST API-t használja a következő API-k betanítására és strukturált adatok kinyerése az űrlapok.

| REST API | Leírás |
|-----|-------------|
| Betanítás | Az űrlapok használatával ugyanabból a típusú 5 űrlapok vagy egy üres képernyő elemzése új modell betanításához.  |
| Elemzés  |Egyetlen dokumentum, kulcs-érték párok és táblák kibontani az űrlapot a egyéni modell adatfolyamként átadott elemzése.  |

Fedezze fel a [REST API-referencia dokumentumban](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

A szolgáltatás érhető el egy [előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) alatt egy Azure Service a [Online szolgáltatási feltételek](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Megőrzi az adatok tulajdonjogát, és csak azt biztosítása érdekében használjuk az Online szolgáltatások az Ön szerződésében ismertetett módon:

### <a name="processing-of-customer-data-ownership"></a>Vásárlói adatok feldolgozása tulajdonjog

Vásárlói adatok használni vagy más módon feldolgozott, csak adja meg az ügyfél az Online szolgáltatások, többek között ezek a szolgáltatások biztosításával összeegyeztethető célokra. A Microsoft nem használja vagy ellenkező esetben az ügyféladatok feldolgozását vagy lesz nyer információt azokból hirdetési vagy hasonló kereskedelmi célú. A felek közötti érvénnyel ügyfél megőrzi minden jogot, címet és érdekeltséget is, és a vásárlói adatokhoz. A Microsoft nem rights az ügyféladatokat, a jogok ügyfél engedélyezi a Microsoftnak, hogy az Online szolgáltatások biztosítása az ügyfél nem szerez be.

Az összes Cognitive Services, az űrlap felismerő szolgáltatással a fejlesztők célszerű tisztában lennie a vásárlói adatokat a Microsoft házirendeket. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Hajtsa végre egy [rövid](quickstarts/curl-train-extract.md) használatának megkezdéséhez a [felismerő API](https://aka.ms/form-recognizer/api).
