---
title: Iteratív alkalmazástervezés - LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS a modellváltozások iteratív ciklusában, az utterance (kifejezés) példák közzétételében és a végpontlekérdezések adatok gyűjtésében tanul a legjobban.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74422599"
---
# <a name="iterative-app-design-for-luis"></a>Iteratív alkalmazástervezés a LUIS számára

A Language Understanding (LUIS) alkalmazás megtanulja, és a leghatékonyabban működik iterációval. Íme egy tipikus iterációs ciklus:

* Új verzió létrehozása
* A LUIS alkalmazásséma szerkesztése. Az érintett műveletek közé tartoznak az alábbiak:
    * Leképezések példa utterances
    * Entitások
    * Szolgáltatások
* Betanítás, tesztelés és közzététel
    * Teszt az előrejelzési végponton az aktív tanuláshoz
* Adatok gyűjtése végpontlekérdezésekből

![Tartalomkészítési ciklus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>LUIS-séma létrehozása

Az alkalmazás sémája határozza meg, hogy mit kér a felhasználó (a _szándék_ vagy a _szándék_ ), és a szándék mely részei adnak meg részleteket (úgynevezett _entitásokat),_ amelyek segítségével meghatározható a válasz. 

Az alkalmazássémának az alkalmazástartományokra jellemzőnek kell lennie a releváns szavak és kifejezések meghatározásához, valamint a tipikus szósorrend meghatározásához. 

Példa utterances képviseli a felhasználói bemenetek, például a felismert beszéd vagy szöveg, hogy az alkalmazás elvárja, hogy futásidőben. 

A séma leképezéseket igényel, és entitásokkal _kell rendelkeznie._ 

### <a name="example-schema-of-intents"></a>Példa leképezéssée

A leggyakoribb séma egy szándékokkal rendezett séma. Ez a sématípus a LUIS segítségével határozza meg a felhasználó szándékát. 

A szándékséma-típus entitásokkal rendelkezhet, ha segít a LUIS-nak meghatározni a felhasználó szándékát. Például egy szállítási entitás (egy szándék leírójaként) segít a LUIS-nak meghatározni a szállítási szándékot. 

### <a name="example-schema-of-entities"></a>Entitások sémája

Az entitásséma az entitásokra összpontosít, amely a felhasználói utterances kinyert adatok. Ha például egy felhasználó azt mondaná: "Három pizzát szeretnék rendelni." Két entitás, hogy lenne kivont: _három_ és _pizzák_. Ezeket arra használják, hogy segítsenek teljesíteni a szándékot, amely a megrendelés volt. 

Egy entitás sémája esetében az utterance (kifejezés) szándéka kevésbé fontos az ügyfélalkalmazás számára. 

Az entitásséma rendszerezésének gyakori módja az összes példa utterances hozzáadása a **Nincs** szándékhoz. 

### <a name="example-of-a-mixed-schema"></a>Példa vegyes sémára

A leghatékonyabb és legkiforrottabb séma egy szándékos séma, amely az entitások és szolgáltatások teljes skáláját tartalmazza. Ez a séma megkezdheti a szándék vagy az entitás sémája, és növekszik, hogy tartalmazza a fogalmak mindkettő, mivel az ügyfélalkalmazás szüksége van ezekre az információkra. 

## <a name="add-example-utterances-to-intents"></a>Példakimondott szöveg hozzáadása a leképezésekhez

A LUIS-nak szüksége van néhány példa utterances minden **szándékot.** A példa utterances kell elég változata a szó választás és a szó sorrendben, hogy képes legyen meghatározni, hogy melyik szándék az utterance (kifejezés) célja. 

> [!CAUTION]
> Ne adjon hozzá példa utterances ömlesztve. Kezdje 15-30 konkrét és különböző példákkal. 

Minden példa utterance (kifejezés) rendelkeznie kell a **szükséges adatokat az** **entitások**által tervezett és címkézett kivonat. 

|Kulcselem|Cél|
|--|--|
|Szándék|A felhasználói utterances **besorolása** egyetlen szándékba vagy műveletbe. Ilyen `BookFlight` például a és `GetWeather`a.|
|Entitás|**A** szándék befejezéséhez szükséges utterance (kifejezés) adatok kinyerése. Ilyen például az utazás dátuma és időpontja, valamint a hely.|

A LUIS-alkalmazás úgy tervezhető, hogy figyelmen kívül hagyja az olyan kimondott szövegeket, amelyek nem relevánsak az alkalmazás tartományában azáltal, hogy az utterance **(kifejezés)** a Nincs szándékhoz rendeli.

## <a name="test-and-train-your-app"></a>Az alkalmazás tesztelése és betanítása

Miután 15–30 különböző példa utterances minden szándék, a szükséges entitások címkével, tesztelnie kell, és [betanítása](luis-how-to-train.md) a LUIS-alkalmazást. 

## <a name="publish-to-a-prediction-endpoint"></a>Közzététel előrejelzési végponton

A LUIS alkalmazást közzé kell tenni, hogy az elérhető legyen a lista [előrejelzési végponti régióiban.](luis-reference-regions.md)

## <a name="test-your-published-app"></a>A közzétett alkalmazás tesztelése

A közzétett LUIS-alkalmazást a HTTPS-előrejelzési végpontról tesztelheti. Az előrejelzési végpontról történő tesztelés lehetővé teszi a LUIS számára, hogy alacsony megbízhatóságú kimondott szövegeket [válasszon.](luis-how-to-review-endpoint-utterances.md)  

## <a name="create-a-new-version-for-each-cycle"></a>Új verzió létrehozása minden ciklushoz

Minden verzió egy pillanatkép a LUIS-alkalmazás idejében. Mielőtt módosítana az alkalmazást, hozzon létre egy új verziót. Könnyebb visszatérni egy régebbi verzióra, mint egy korábbi állapotba való leképezések és kimondott szövegeltávolítása.

A verzióazonosító karakterekből, számjegyekből vagy "." karakterekből áll, és nem lehet hosszabb 10 karakternél.

A kezdeti verzió (0.1) az alapértelmezett aktív verzió. 

### <a name="begin-by-cloning-an-existing-version"></a>Kezdje egy meglévő verzió klónozásával

Klónozzon egy meglévő verziót, amelyet minden új verzió kiindulópontjaként használhat. A verzió klónozása után az új verzió lesz az **aktív** verzió. 

### <a name="publishing-slots"></a>Közzétételi tárolóhelyek

A színpadon és/vagy a termelési tárolóhelyeken is közzéteheti. Minden bővítőhely lehet egy másik verzió, vagy ugyanazt a verziót. Ez akkor hasznos, ha a módosítások ellenőrzése az éles környezetben való közzététel előtt, amely elérhető a botok vagy más LUIS hívó alkalmazások. 

A betanított verziók nem érhetők el automatikusan a LUIS-alkalmazás [végpontján.](luis-glossary.md#endpoint) Közzé kell [tennie,](luis-how-to-publish-app.md) vagy újra közzé kell tennie egy verziót annak érdekében, hogy elérhető legyen a LUIS-alkalmazás végpontján. Az **Átmeneti** és **éles környezetben**is közzéteheti, így az alkalmazás két verziója érhető el a végponton. Ha az alkalmazás több verziójának elérhetőnek kell lennie egy végponton, exportálja a verziót, és importálja újra egy új alkalmazásba. Az új alkalmazás egy másik alkalmazásazonosítóval rendelkezik.

### <a name="import-and-export-a-version"></a>Verzió importálása és exportálása

A verzió az alkalmazás szintjén importálható. Ez a verzió lesz az aktív verzió, `versionId` és az alkalmazásfájl tulajdonságában lévő verzióazonosítót használja. Egy meglévő alkalmazásba is importálhat, verziószinten. Az új verzió lesz az aktív verzió. 

A verzió az alkalmazás vagy verzió szintjén is exportálható. Az egyetlen különbség az, hogy az alkalmazásszintű exportált verzió az aktuálisan aktív verzió, míg a verzió szintjén bármelyik verziót exportálhatja a **[Beállítások](luis-how-to-manage-versions.md)** lapon. 

Az exportált fájl nem tartalmazza a **következőket:**

* Gépmegtanult információk, mert az alkalmazás importálása után újra bevan program
* Közreműködői információ

A LUIS-alkalmazásséma biztonsági és biztonsági biztonsági tetézéséhez exportáljon egy verziót a [LUIS-portálról.](https://www.luis.ai/applications)

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Közreműködői módosítások kezelése verziókkal és közreműködőkkel

A LUIS az azure-beli erőforrásszintű engedélyek biztosításával használja az alkalmazás közreműködőinek fogalmát. Kombinálja ezt a fogalmat a verziószámozással, hogy célzott együttműködést biztosítson. 

Az alábbi módszerekkel kezelheti az alkalmazás közreműködői módosításait.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Több verzió kezelése ugyanazon az alkalmazáson belül

Kezdje az egyes szerzők alapverziójának [klónozásával.](luis-how-to-manage-versions.md#clone-a-version) 

Minden szerző módosítja az alkalmazás saját verzióját. Ha a szerző elégedett a modellel, exportálja az új verziókat a JSON fájlokba.  

Az exportált alkalmazások, .json vagy .lu fájlok a módosításokhoz hasonlíthatók. A fájlok egyesítésével egyetlen fájlt hozhat létre az új verzióból. Módosítsa `versionId` a tulajdonságot az új egyesített verzió jelzésére. Importálja a verziót az eredeti alkalmazásba. 

Ez a módszer lehetővé teszi, hogy egy aktív verzió, egy szakasz verzió, és egy közzétett verzió. Az aktív verzió eredményeit összehasonlíthatja egy közzétett verzióval (színpadvagy éles környezet) az [interaktív tesztelési ablaktáblában.](luis-interactive-test.md)

### <a name="manage-multiple-versions-as-apps"></a>Több verzió kezelése alkalmazásként

Az alapverzió [exportálása.](luis-how-to-manage-versions.md#export-version) Minden szerző importálja a verziót. Az alkalmazást importáló személy a verzió tulajdonosa. Ha végeztek az alkalmazás módosításával, exportálja a verziót. 

Az exportált alkalmazások JSON-formátumú fájlok, amelyek összehasonlíthatók a változások alapexportálásával. A fájlok egyesítésével egyetlen JSON-fájlt hozhat létre az új verzióról. Módosítsa a **versionId** tulajdonságot a JSON-ban az új egyesített verzió jelzéséhez. Importálja a verziót az eredeti alkalmazásba.

További információ a [közreműködők](luis-how-to-collaborate.md)től származó hozzájárulások ról.

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Tekintse át a végpontkimondott szöveget az új iteratív ciklus megkezdéséhez

Ha végzett egy iterációs ciklus, akkor ismételje meg a folyamatot. Kezdje [az előrejelzési végpont kimondott szövegek luis](luis-how-to-review-endpoint-utterances.md) alacsony megbízhatósággal megjelölt áttekintésével. Ellenőrizze ezeket a kimondott szövegeket a helyes előre jelzett leképezés és a helyes és teljes entitás kibontva. A módosítások áttekintése és elfogadása után a véleményezési lista üresnek kell lennie.  

## <a name="next-steps"></a>További lépések

Ismerje meg az [együttműködéssel](luis-concept-keys.md)kapcsolatos fogalmakat.
