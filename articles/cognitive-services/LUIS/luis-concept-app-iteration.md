---
title: Iterációs alkalmazás tervezése – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS megtanulja a legjobbat a modell változásainak, a kitalált példáknak, a közzétételnek és az adatoknak a végponti lekérdezésekből való összegyűjtésének folyamatában.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487770"
---
# <a name="authoring-cycles-and-versions"></a>Létrehozási ciklusok és verziók

A LUIS-alkalmazás a lehető legpontosabban tanulja meg a következőket:

* új verzió létrehozása
* alkalmazás sémájának szerkesztése
    * leképezések példa hosszúságú kimondott szöveg
    * szervezetek
    * funkciókkal
* vonat
* test
* publish
    * tesztelés előrejelzési végponton az aktív tanuláshoz
* adatok összegyűjtése végponti lekérdezésekből

![Tartalomkészítési ciklus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>LUIS-séma létrehozása

Az alkalmazás sémájának célja, hogy meghatározza, hogy a felhasználó mit kér (a szándékot vagy szándékot), és hogy a kérdés mely részeit adja meg (entitások), amelyek segítenek meghatározni a választ. 

Az alkalmazás sémájának egyedinek kell lennie az alkalmazás-tartományokhoz, hogy meg lehessen határozni a releváns szavakat és kifejezéseket, valamint a szokásos Word-sorrendet. 

Például a felhasználói bevitelt jelölő hosszúságú kimondott szöveg, amelyet az alkalmazásnak a futtatáskor várhatóan kap. 

A sémához leképezések szükségesek, és _rendelkeznie kell_ entitásokkal. 

### <a name="example-schema-of-intents"></a>Példa a szándékok sémájára

A leggyakoribb séma a szándékokkal rendezett leképezési séma. Ez a típusú séma a LUIStól függ a felhasználó szándékának meghatározásához. 

A séma típusa rendelkezhet olyan entitásokkal, amelyek segítségével a LUIS meghatározza a szándékot. Például egy szállítási entitás (a szándéknak megfelelően) segít a kiszállítási szándék meghatározásában. 

### <a name="example-schema-of-entities"></a>Példa entitások sémája

Az entitások sémája az entitásokra koncentrál, amelyek a hosszúságú kimondott szöveg kinyerni kívánt adatok. 

A Kimondás szándéka kevésbé vagy nem fontos az ügyfélalkalmazás számára. 

Az entitások sémájának megszervezésének közös módszere az összes példa hosszúságú kimondott szöveg hozzáadása a nincs szándékhoz. 

### <a name="example-of-a-mixed-schema"></a>Példa vegyes sémára

A leghatékonyabb és legérettebb séma olyan leképezési séma, amely az entitások és szolgáltatások teljes skáláját tartalmazza. Ez a séma megkezdhető úgy, mint a szándék vagy az entitás sémája, és az is növekszik, hogy mindkét fogalomban szerepelnek, mivel az ügyfélalkalmazás ezekre az adatokra van szüksége. 

## <a name="add-example-utterances-to-intents"></a>Példa hosszúságú kimondott szöveg hozzáadása a leképezésekhez

A LUIS-nek néhány példát kell hosszúságú kimondott szöveg az egyes **szándékokhoz**. A példának a Word Choice és a Word hosszúságú kimondott szöveg elég variációra van szüksége ahhoz, hogy meg tudja határozni, melyik szándékot jelenti a kiírás. 

> [!CAUTION]
> Ne vegyen fel több példát a hosszúságú kimondott szöveg. Kezdje a 15 – 30 konkrét és változó példával. 

Minden esetben a kiírásnak minden szükséges adattal rendelkeznie kell az **entitásokkal**megtervezett és címkézett **adatok kinyeréséhez** . 

|Kulcs eleme|Cél|
|--|--|
|Szándék|A felhasználó hosszúságú kimondott szöveg egyetlen célra vagy műveletbe **osztályozhatja** . Ilyenek például a `BookFlight` és a `GetWeather`.|
|Entitás|Az adatok **kinyerése** a cél befejezéséhez szükséges. Ilyenek például az utazás dátuma és időpontja, valamint a hely.|

A LUIS-alkalmazást úgy tervezze meg, hogy figyelmen kívül hagyja a hosszúságú kimondott szöveg, amelyek nem relevánsak az alkalmazás tartományában azáltal, hogy az **egyiket sem** kívánja kijelölni. 

## <a name="test-and-train-your-app"></a>Az alkalmazás tesztelése és betanítása

Ha 15 – 30 különböző példát hosszúságú kimondott szöveg az egyes szándékok között, a szükséges entitásokat pedig tesztelni kell, és be kell [tanítani](luis-how-to-train.md). 

## <a name="publish-to-a-prediction-endpoint"></a>Közzététel előrejelzési végponton

Győződjön meg arról, hogy az alkalmazás közzé van téve, hogy elérhető legyen a szükséges [előrejelzési végpontok régióiban](luis-reference-regions.md) . 

## <a name="test-your-published-app"></a>A közzétett alkalmazás tesztelése

A közzétett LUIS-alkalmazást a HTTPS-előrejelzési végpontról tesztelheti. Az előrejelzési végpont tesztelése lehetővé teszi, hogy a LUIS kiválassza az alacsony megbízhatóságú hosszúságú kimondott szöveg az [ellenőrzéshez](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Új verzió létrehozása minden ciklushoz

A LUIS-verziók a hagyományos programozási verziókhoz hasonlóak. Minden verzió egy pillanatkép az alkalmazás időpontjában. Mielőtt módosításokat hajt végre az alkalmazásban, hozzon létre egy új verziót. A régebbi verzióra való visszalépéshez, majd a leképezések eltávolításához és a hosszúságú kimondott szöveg korábbi állapotba való visszaállításához.

A verzióazonosító karakterből, számjegyből vagy "." áll, és nem lehet hosszabb 10 karakternél.

A kezdeti verzió (0,1) az alapértelmezett aktív verzió. 

### <a name="begin-by-cloning-an-existing-version"></a>Kezdés egy meglévő verzió klónozásával

Meglévő verzió klónozása az új verzió kiindulási pontjaként való használatra. Miután klónozott egy verziót, az új verzió lesz az **aktív** verzió. 

### <a name="publishing-slots"></a>Közzétételi résidők
A fázisra és a termelési tárolóhelyekre tesz közzé. Az egyes tárolóhelyek eltérő verziójúak vagy azonos verziójúak lehetnek. Ez akkor lehet hasznos, ha az éles környezetbe való közzététel előtt ellenőrzi a módosításokat, ami elérhető a botok vagy más LUIS hívó alkalmazások számára. 

A betanított verziók nem érhetők el automatikusan az alkalmazás [végpontján](luis-glossary.md#endpoint). Egy verziót kell [közzétennie](luis-how-to-publish-app.md) vagy újból közzétennie ahhoz, hogy elérhető legyen az alkalmazás végpontján. Közzéteheti az **előkészítést** és a **gyártást**, így az alkalmazás két verziója érhető el a végponton. Ha egy végponton elérhetővé kell tennie az alkalmazás több verzióját, exportálnia kell a verziót, és újra importálnia kell egy új alkalmazásba. Az új alkalmazáshoz egy másik alkalmazás-azonosító tartozik.

### <a name="import-and-export-a-version"></a>Verzió importálása és exportálása
Az alkalmazás szintjén is importálhat egy verziót. Ez a verzió lesz az aktív verzió, és a verziószámot használja az alkalmazás `versionId` tulajdonságában. A verzió szintjén is importálhat egy meglévő alkalmazást. Az új verzió lesz az aktív verzió. 

Egy verziót az alkalmazás vagy a verzió szintjén is exportálhat. Az egyetlen különbség, hogy az alkalmazás-szintű exportált verzió a jelenleg aktív verzió a verzió szintjén, a **[Beállítások](luis-how-to-manage-versions.md)** lapon bármilyen verziót kiválaszthat az exportáláshoz. 

Az exportált fájl nem tartalmazza a következőket:

* a gép által megismert információk, mert az alkalmazás az importálás után újra lett betanítva
* közreműködői információ

A LUIS-alkalmazás sémájának biztonsági mentéséhez exportáljon egy verziót a LUIS portálról.

## <a name="manage-contributor-changes-with-versions-and-apps"></a>Közreműködők változásainak kezelése verziókkal és alkalmazásokkal

A LUIS az Azure-erőforrás-szintű engedélyek biztosításával biztosítja az alkalmazások közreműködőinek a fogalmát. Ezt a koncepciót a verziószámozással kombinálva megcélozható együttműködés biztosítható. 

A következő módszerekkel kezelheti az alkalmazás közreműködői módosításait.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Több verzió kezelése ugyanazon az alkalmazáson belül
Először [klónozással](luis-how-to-manage-versions.md#clone-a-version), az alapverziótól kezdve az egyes szerzőknél. 

Minden szerző módosítja az alkalmazás saját verzióját. Ha minden szerző elégedett a modellel, exportálja az új verziókat a JSON-fájlokba.  

Az exportált alkalmazások, a. JSON vagy a. lu fájlok összehasonlítható a változásokkal. Egyesítse a fájlokat úgy, hogy egyetlen fájlt hozzon létre az új verzióval. Módosítsa a **versionId** tulajdonságot úgy, hogy az az új egyesített verziót jelenti. Importálja az adott verziót az eredeti alkalmazásba. 

Ez a módszer lehetővé teszi, hogy egyetlen aktív verziót, egy szakasz verzióját és egy közzétett verziót válasszon. Az aktív verzió eredményeit összehasonlíthatja egy közzétett verzióval (fázis vagy éles környezet) az [interaktív tesztelési panelen](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Több verzió kezelése alkalmazásként
[Exportálja](luis-how-to-manage-versions.md#export-version) az alapverziót. Minden szerző importálja a verziót. Az alkalmazást importáló személy a verzió tulajdonosa. Ha elkészült az alkalmazás módosításával, exportálja a verziót. 

Az exportált alkalmazások JSON-formátumú fájlok, amelyek összehasonlítható a változtatások alapszintű exportálásával. Egyesítse a fájlokat úgy, hogy egyetlen JSON-fájlt hozzon létre az új verzióval. Módosítsa a JSON **versionId** tulajdonságát úgy, hogy az az új egyesített verziót jelenti. Importálja az adott verziót az eredeti alkalmazásba.

További információ a [közreműködők](luis-how-to-collaborate.md)hozzájárulásainak létrehozásáról.

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>A végpont hosszúságú kimondott szöveg áttekintése az új szerzői ciklus megkezdéséhez

Ha elkészült a szerzői ciklussal, megkezdheti a műveletet. Első lépésként [tekintse meg az előrejelzési végpont hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) , amely alacsony megbízhatósággal van megjelölve. Ezeket a hosszúságú kimondott szöveg a helyes előre jelzett szándékot, valamint a helyes és a kinyert entitást is megvizsgálhatja. Ha áttekinti és elfogadja a módosításokat, a felülvizsgálati listának üresnek kell lennie.  

## <a name="next-steps"></a>További lépések

Ismerje meg az [együttműködéssel](luis-concept-keys.md)kapcsolatos fogalmakat.
