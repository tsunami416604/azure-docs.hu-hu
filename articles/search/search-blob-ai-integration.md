---
title: A Blob storage-adatok megértéséhez használja a AI-t
titleSuffix: Azure Cognitive Search
description: Szemantikai, természetes nyelvi feldolgozás és képelemzés hozzáadása az Azure-blobokhoz egy AI-bővítési folyamat használatával az Azure Cognitive Search-ben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496434"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>A Blob storage-adatok megértéséhez használja a AI-t

Az Azure Blob storage-ban lévő adatok gyakran különféle strukturálatlan tartalmak, például képek, hosszú szövegek, PDF-dokumentumok és Office-dokumentumok. Az AI-képességek használatával az Azure Cognitive Search, megismerheti és kinyerheti az értékes információkat a blobok a legkülönfélébb módokon. A mi-k blobtartalomra történő alkalmazására példák a következők:

+ Szöveg kinyerése képekből optikai karakterfelismeréssel (OCR)
+ Jelenetleírás vagy címkék készítéséhez fényképről
+ Nyelv felismerése és szöveg fordítása különböző nyelvekre
+ Elnevezett entitásfelismeréssel (NER) rendelkező szöveg feldolgozása személyekre, dátumokra, helyekre vagy szervezetekre mutató hivatkozások kereséséhez 

Bár előfordulhat, hogy csak egy AI-képességre van szüksége, gyakori, hogy többet ugyanabba a folyamatba egyesít (például szöveget von ki egy beolvasott képből, majd megtalálja az összes hivatkozott dátumot és helyet). 

A i dúsítás új, szövegként rögzített, mezőkben tárolt információkat hoz létre. A bővítést követően teljes szöveges kereséssel elérheti ezeket az információkat a keresési indexből, vagy bővített dokumentumokat küldhet vissza az Azure storage-ba, hogy olyan új alkalmazásélményeket biztosítson be, amelyek magukban foglalják az adatok felderítési vagy elemzési forgatókönyvek feltárását. 

Ebben a cikkben a ai-dúsítást széles objektívvel tekinthetjük meg, így gyorsan megértheti a teljes folyamatot, a blobokban lévő nyers adatok átalakításától a lekérdezhető információkig egy keresési indexben vagy egy tudástárolóban.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Mit jelent a blobadatok "gazdagítása" a ai-val

*A i dúsítás* része az Azure Cognitive Search indexelési architektúrájának, amely integrálja a Microsoft beépített AI-ját vagy az Ön által biztosított egyéni AI-t. Segít a végpontok között olyan forgatókönyvek megvalósításában, amelyekben a blobokat fel kell dolgoznia (mind a meglévőket, mind az újakat, amint bejönnek vagy frissülnek), feltörni az összes fájlformátummegnyitását a képek és a szöveg kinyeréséhez, a kívánt információk kinyeréséhez különböző AI-képességek használatával, és indexelje őket a gyors kereséshez, visszakereséshez és feltáráshoz. 

Bemenetek a blobok, egyetlen tárolóban, az Azure Blob storage-ban. A blobok szinte bármilyen típusú szöveges vagy képadat lehet. 

A kimenet mindig egy keresési index, amelyet gyors szöveges kereséshez, visszakereséshez és az ügyfélalkalmazások feltárásához használnak. Emellett a kimenet is lehet egy *tudástároló,* amely a bővített dokumentumokat Azure blobokba vagy Azure-táblákba vetíti az olyan eszközökben, mint a Power BI vagy az adatelemzési számítási feladatok ban végzett mélyalsó bb-elemzéshez.

A kettő között maga a csővezeték-architektúra. A folyamat az *indexelő* szolgáltatáson alapul, amelyhez hozzárendelhet egy *skillsetet,* amely egy vagy több, a mi-t biztosító *szakértelemből* áll. A folyamat célja, hogy *dúsított dokumentumokat* készítsen, amelyek nyers tartalomként lépnek be, de további struktúrát, környezetet és információt vesznek fel a folyamaton való átszállítás közben. A bővített dokumentumok indexelés közben fordított indexek és a teljes szöveges keresésben, feltárásban és elemzésben használt egyéb struktúrák létrehozásához használatosak.

## <a name="start-with-services"></a>Kezdje a szolgáltatásokkal

Az Azure Cognitive Search és az Azure Blob storage szükséges. A Blob storage-on belül olyan tárolóra van szüksége, amely forrástartalmat biztosít.

Közvetlenül a tárfiók portálján indítható el. A bal oldali navigációs lapon a **Blob szolgáltatás** csoportban kattintson az Azure Cognitive **Search hozzáadása** elemre egy új szolgáltatás létrehozásához, vagy válasszon ki egy meglévőt. 

Miután hozzáadja az Azure Cognitive Search-et a tárfiókhoz, a szokásos eljárást követve gazdagíthatja az adatokat bármely Azure-adatforrásban. Azt javasoljuk, hogy az **Adatok importálása** varázsló az Azure Cognitive Search egy egyszerű kezdeti bevezetés a ai gazdagítása. Ez a rövid útmutató végigvezeti a következő lépéseken: [AI-dúsítási folyamat létrehozása a portálon.](cognitive-search-quickstart-blob.md) 

A következő szakaszokban további összetevőket és fogalmakat fedezünk fel.

## <a name="use-a-blob-indexer"></a>Blob indexelő használata

AI-bővítés egy indexelési folyamat bővítménye, és az Azure Cognitive Search-ben ezek a folyamatok egy *indexelőre*épülnek. Az indexelő egy adatforrás-érzékeny alszolgáltatás, amely belső logikával rendelkezik az adatok mintavételezéséhez, a metaadatok olvasásához, az adatok beolvasásához és a natív formátumokból származó adatok JSON-dokumentumokba történő szerializálásához későbbi importáláshoz. Indexelők gyakran használják önmagukban az import, külön a ai, de ha azt szeretné, hogy hozzon létre egy AI dúsítási folyamat, szüksége lesz egy indexelő és egy skillset menni vele. Ez a szakasz kiemeli az indexelőt; a következő szakasz a készségekre összpontosít.

Az Azure Storage-ban lévő blobok indexelése az [Azure Cognitive Search Blob storage indexelővel lesz indexelve.](search-howto-indexing-azure-blob-storage.md) Ezt az indexelőt az **Adatok importálása** varázslóval, egy REST API-val vagy a .NET SDK-val hívhatja meg. A kódban ezt az indexelőt a típus beállításával és az Azure Storage-fiókot és egy blobtárolóval együtt tartalmazó kapcsolati adatok megadásával használja. A blobok részhalmaza egy virtuális könyvtár létrehozásával, amelyet paraméterként továbbíthat, vagy szűrhet egy fájltípus-kiterjesztésre.

Az indexelő nem a "dokumentum repedés", megnyitva egy blobot, hogy vizsgálja meg a tartalmat. Az adatforráshoz való csatlakozás után ez az első lépés a folyamatban. Blob-adatok esetén itt található a PDF-dokumentum, az irodai dokumentumok, a kép és más tartalomtípusok észlelése. Dokumentum csinos szöveg kinyerése nem számít. A képkinyeréssel történő dokumentumfeltörést az [árképzési oldalon](https://azure.microsoft.com/pricing/details/search/)található díjak kalkulálják.

Bár minden dokumentum megrepedt, a gazdagodás csak akkor fordul elő, ha kifejezetten megadja az ehhez szükséges készségeket. Ha például a folyamat kizárólag képelemzésből áll, a tárolóban vagy a dokumentumokban lévő szöveget a rendszer figyelmen kívül hagyja.

A Blob indexelő jön a konfigurációs paramétereket, és támogatja a változás követés, ha az alapul szolgáló adatok elegendő információt szolgáltatnak. Az Azure Cognitive Search Blob [storage indexelőjének](search-howto-indexing-azure-blob-storage.md)alapvető funkcióiról többet is megtudhat.

## <a name="add-ai-components"></a>AI-összetevők hozzáadása

A I dúsítás olyan modulokra utal, amelyek mintákat vagy jellemzőket keresnek, majd ennek megfelelően hajtanak végre egy műveletet. A fényképek arcfelismerése, a fényképek szöveges leírása, a dokumentum ban található kulcskifejezések észlelése és az OCR (vagy a nyomtatott vagy kézzel írt szöveg bináris fájlokban való felismerése) szemléltető példa.

Az Azure Cognitive Search, *készségek* az egyes összetevői a ai-feldolgozás, amely használhatja önálló, vagy más készségekkel kombinálva. 

+ A beépített képességeket a Cognitive Services támogatja, a számítógépes látáson alapuló képelemzéssel, a szövegelemzésen alapuló természetes nyelvi feldolgozással. A teljes listát [lásd: Beépített szakértelem a tartalom gazdagításához.](cognitive-search-predefined-skills.md)

+ Egyéni képességek egyéni kód, csomagolva egy [felület definíciója,](cognitive-search-custom-skill-interface.md) amely lehetővé teszi a folyamatba való integrációt. Az ügyfélmegoldásokban bevett gyakorlat, hogy mindkettőt használja, olyan egyéni képességekkel, amelyek nyílt forráskódú, harmadik vagy belső AI-modulokat biztosítanak.

A *skillset* a folyamatban használt szakértelem gyűjteménye, és a dokumentum repedési fázis a tartalom elérhetővé teszi a dokumentum repedésfázisa után. Az indexelő pontosan egy skillsetet használhat fel, de ez a skillset egy indexelőtől függetlenül létezik, így más esetekben újra felhasználhatja.

Az egyéni készségek bonyolultnak tűnhetnek, de a megvalósítás szempontjából egyszerűek és egyértelműek lehetnek. Ha meglévő csomagok, amelyek minta egyezési vagy besorolási modellek, a blobokból kinyert tartalom átadható ezeknek a modelleknek feldolgozásra. Mivel a ai-dúsítás Azure-alapú, a modellnek az Azure-on is kell lennie. Néhány gyakori üzemeltetési módszer közé tartozik az [Azure Functions](cognitive-search-create-custom-skill-example.md) vagy [a Containers](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)használata.

A Cognitive Services által támogatott beépített képességekhez egy [csatolt Cognitive Services](cognitive-search-attach-cognitive-services.md) all-in-one előfizetési kulcs szükséges, amely hozzáférést biztosít az erőforráshoz. Az all-in-one billentyű vel képelemzést, nyelvfelismerést, szövegfordítást és szövegelemzést biztosít. Az egyéb beépített képességek az Azure Cognitive Search szolgáltatásai, és nem igényelnek további szolgáltatást vagy kulcsot. A szövegformáló, az osztó és az összeolvadás olyan segítő képesség, amely néha szükséges a folyamat tervezésekor.

Ha csak egyéni képességeket és beépített közüzemi képességeket használ, nincs függőség vagy a Cognitive Services-hez kapcsolódó költségek.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Ai-dúsított kimenet felhasználása a termelési-értékesítési láncban lejjebb lévő megoldásokban

A I-dúsítás kimenete vagy egy keresési index az Azure Cognitive Search, vagy egy [tudástároló](knowledge-store-concept-intro.md) az Azure Storage-ban.

Az Azure Cognitive Search, a keresési index segítségével interaktív feltárása szabad szöveges és szűrt lekérdezések egy ügyfélalkalmazásban. A mi-n keresztül létrehozott bővített dokumentumok JSON-ban vannak formázva, és ugyanúgy indexelnek, mint az Azure Cognitive Search indexelése, kihasználva az indexelő által nyújtott összes előnyt. Az indexelés során például a blob indexelő konfigurációs paraméterekre és beállításokra hivatkozik bármely mezőleképezés vagy változásészlelési logika használatához. Ezek a beállítások teljes mértékben elérhetők a rendszeres indexelés és a ai dúsított számítási feladatok. Az indexelés utáni, ha a tartalom az Azure Cognitive Search tárolja, hozhat létre gazdag lekérdezések és szűrő kifejezések a tartalom megértéséhez.

Az Azure Storage-ban a tudástároló két megnyilvánulások: egy blob tároló, vagy táblák table storage. 

+ A blob tároló rögzíti a bővített dokumentumokat teljes egészében, ami akkor hasznos, ha azt szeretné, hogy más folyamatokba. 

+ Ezzel szemben a Table storage képes a bővített dokumentumok fizikai vetületeinek befogadására. Létrehozhat olyan szeleteket vagy rétegeket a bővített dokumentumokból, amelyek meghatározott részeket tartalmaznak vagy zárnak ki. A Power BI-ban végzett elemzéshez az Azure Table storage táblái a további vizualizáció és feltárás adatforrásává válnak.

A folyamat végén található bővített dokumentum különbözik az eredeti bemeneti változattól azáltal, hogy további mezők et tartalmaz, amelyek a dúsítás során kinyert vagy létrehozott új információkat tartalmaznak. Így az eredeti és a létrehozott tartalom kombinációjával dolgozhat, függetlenül attól, hogy melyik kimeneti struktúrát használja.

## <a name="next-steps"></a>További lépések

Sokkal többet tehet az AI-bővítéssel, hogy a legtöbbet hozhassa ki az azure Storage-ban tárolt adataiból, beleértve a Cognitive Services különböző módokon történő kombinálását, valamint az egyéni képességek szerkesztését olyan esetekben, amikor nincs meglévő Cognitive Service a forgatókönyvhöz. Ha többet szeretne megtudni az alábbi linkeken.

+ [Blobok feltöltése, letöltése és listázása az Azure Portalon (Azure Blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Blob indexelő beállítása (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [AI-bővítés – áttekintés (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Skillset létrehozása (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Csomópontok leképezése egy jegyzetfában (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
