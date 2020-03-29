---
title: Kivetítések egy tudástárolóban (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Mentse és alakítsa a bővített adatokat az AI-dúsítási indexelési folyamatból egy tudástárolóba, amelyet a teljes szöveges kereséstől eltérő esetekben is használhat. A tudástároló jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942974"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Előrejelzések egy tudástárolóban az Azure Cognitive Search-ben

> [!IMPORTANT] 
> A tudástároló jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-preview verziója](search-api-preview.md) előnézeti funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

Az Azure Cognitive Search lehetővé teszi a tartalom gazdagítását a beépített kognitív készségek és egyéni képességek az indexelés részeként. A dúsítások olyan új információkat hoznak létre, amelyek korábban nem léteztek: információk kinyerése képekből, hangulatok, kulcskifejezések és entitások felismerése a szövegből, hogy csak néhányat említsünk. A dúsítások a nem differenciált szöveget is felépítik. Mindezek a folyamatok olyan dokumentumokat eredményeznek, amelyek hatékonyabbá teszik a teljes szöveges keresést. Sok esetben a bővített dokumentumok nem a kereséshez, például a tudásbányászathoz használhatók.

Az előrejelzések, amelyek a [tudástároló](knowledge-store-concept-intro.md)részét képezik, a bővített dokumentumok nézetei, amelyek et tudásbányászati célokra fizikai tárolásra lehet menteni. A vetítés lehetővé teszi, hogy az adatokat olyan alakzattá "vetítse ki", amely megfelel az igényeinek, megőrizve a kapcsolatokat, hogy az olyan eszközök, mint a Power BI, további erőfeszítés nélkül olvashassák az adatokat.

A vetítések lehetnek táblázatos, az Azure Table storage soraiban és oszlopaiban tárolt adatokkal, vagy az Azure Blob storage-ban tárolt JSON-objektumokkal. A dúsítás adatként több kivetítést is definiálhat az adatokból. Több vetület akkor hasznos, ha ugyanazokat az adatokat eltérő en szeretné formálna az egyes használati esetekhez.

A tudástároló háromféle vetületet támogat:

+ **Táblázatok**: A sorok és oszlopok leginkább ábrázolt adatok esetén a táblavetések lehetővé teszik, hogy sémassza el az alakzatot vagy a vetületet a Table storage alkalmazásban. Csak érvényes JSON-objektumok vetíthetők ki táblaként, a bővített dokumentum tartalmazhat olyan csomópontokat, amelyek nem JSON-objektumok, és az objektumok kivetítésekor hozzon létre egy érvényes JSON-objektumot shaper szakértelemmel vagy szövegközi formázással.

+ **Objektumok:** Ha az adatok json-ábrázolására és a dúsításokra van szüksége, az objektumvetületek blobként kerülnek mentésre. Csak érvényes JSON-objektumok vetíthetők ki objektumként, a bővített dokumentum tartalmazhat olyan csomópontokat, amelyek nem JSON-objektumok, és az objektumok kivetítésekor hozzon létre egy érvényes JSON-objektumot shaper szakértelemmel vagy szövegközi formázással.

+ **Fájlok**: Ha mentenie kell a dokumentumokból kinyert képeket, a fájlvetések lehetővé teszik a normalizált képek mentését a blob storage-ba.

A környezetben definiált előrejelzések megtekintéséhez lépjen végig [a Tudástár létrehozása a REST-ben című témakörben.](knowledge-store-create-rest.md)

## <a name="projection-groups"></a>Vetítési csoportok

Bizonyos esetekben a bővített adatokat különböző formákban kell kivetítenie a különböző célok eléréséhez. A tudástároló lehetővé teszi az előrejelzések több csoportjának definiálását. A vetítési csoportok a kölcsönös kizárólagosság és rokonság következő fő jellemzőivel rendelkeznek.

### <a name="mutual-exclusivity"></a>Kölcsönös kizárólagosság

Az egy csoportba vetített összes tartalom független a más vetítési csoportokba vetített adatoktól.
Ez a függetlenség azt jelenti, hogy ugyanazokat az adatokat másképp alakíthatja, mégis megismételheti az egyes vetítési csoportokban.

### <a name="relatedness"></a>Rokonság

A vetítési csoportok mostantól lehetővé teszik a dokumentumok vetítési típusokra való kivetítését, miközben megőrzi a vetítési típusok közötti kapcsolatokat. Az egyetlen vetítési csoporton belül kivetített összes tartalom megőrzi az adatokon belüli kapcsolatokat a vetítési típusok között. A táblákon belül a kapcsolatok egy létrehozott kulcson alapulnak, és minden gyermekcsomópont megtartja a szülőcsomópontra mutató hivatkozást. A különböző típusok (táblák, objektumok és fájlok) között a kapcsolatok megmaradnak, ha egyetlen csomópontot vetítanek ki különböző típusok között. Vegyünk például egy olyan forgatókönyvet, amelyben képeket és szöveget tartalmazó dokumentummal rendelkezik. A szöveget táblákra vagy objektumokra vetítheti, a képeket pedig olyan fájlokra vetítheti, ahol a táblák vagy objektumok oszlopa/tulajdonsága tartalmazza a fájl URL-címét.

## <a name="input-shaping"></a>Beviteli formázás

Az adatok megfelelő alakzatba vagy szerkezetbe való beszerzése kulcsfontosságú a hatékony használathoz, legyen szó táblákról vagy objektumokról. Az adatok kialakításának vagy strukturálásának lehetősége a hozzáférés és használat során a skillset-en belül **shaper** szakértelemként elérhető kulcsfontosságú képesség.  

Az előrejelzések könnyebben meghatározhatóak, ha a dúsítási fában olyan objektum található, amely megfelel a vetítés sémájának. A frissített [Shaper szakértelem](cognitive-search-skill-shaper.md) lehetővé teszi, hogy a dúsítási fa különböző csomópontjaiból származó objektumokat összeállítson, és egy új csomópont alatt szülőként szolgáljon. A **Shaper** szakértelem lehetővé teszi, hogy összetett típusokat definiáljon beágyazott objektumokkal.

Ha egy új alakzat van definiálva, amely tartalmazza a kivetítéshez szükséges összes elemet, akkor ezt az alakzatot használhatja a kivetítések forrásaként vagy egy másik szakértelem beviteleként.

## <a name="projection-slicing"></a>Vetítési szeletelés

Vetítési csoport definiálásakor a dúsítási fa egyetlen csomópontja több kapcsolódó táblára vagy objektumra vágható. Ha egy meglévő vetítés gyermekének forráselérési útját tartalmazó vetítést ad hozzá, a gyermekcsomópontot kivágja a szülőcsomópontból, és az új, mégis kapcsolódó táblába vagy objektumba vetíti. Ez a módszer lehetővé teszi, hogy egyetlen csomópontot definiáljon egy formázó-szakértelemben, amely az összes kivetítés forrása lehet.

## <a name="table-projections"></a>Táblázat előrejelzések

Mivel megkönnyíti az importálást, javasoljuk, hogy a Power BI segítségével táblavetéseket készítsen az adatok feltárásához. Ezenkívül a tábla-előrejelzések lehetővé teszik a táblakapcsolatok számosságának módosítását. 

Az indexben lévő egyetlen dokumentumot több táblába is kivetítheti, megőrizve a kapcsolatokat. Ha több táblára vetíti a projektet, a teljes alakzat minden táblába kilesz vetítve, kivéve, ha a gyermekcsomópont egy másik tábla forrása ugyanazon a csoporton belül.

### <a name="defining-a-table-projection"></a>Táblázatvetítés meghatározása

Amikor egy tábla vetületet definiál a `knowledgeStore` skillset elemén belül, kezdje a dúsítási fa csomópontjának hozzárendelésével a táblaforráshoz. Ez a csomópont általában egy **Shaper** szakértelem kimenete, amelyet hozzáadott a képzettségek listájához egy adott alakzat létrehozásához, amelyet táblákba kell kivetítenie. A projektáltal kiválasztott csomópont több táblára is szeletelhető. A táblák definíciója a kivetíteni kívánt táblák listája.

Minden táblázathoz három tulajdonság ra van szükség:

+ tableName: A tábla neve az Azure Storage-ban.

+ generatedKeyName: A sort egyedileg azonosító kulcs oszlopneve.

+ forrás: A dúsító fa csomópontja, amelyből a dúsításokat szerzi be. Ez a csomópont általában egy formázó kimenete, de bármelyik szakértelem kimenete lehet.

Íme egy példa a tábla kivetítések.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Amint azt ebben a példában is mutatja, a kulcskifejezések és entitások különböző táblákba vannak modellezve, és minden sorhoz visszamutató hivatkozást tartalmaznak a szülőre (MainTable).

## <a name="object-projections"></a>Tárgyvetítések

Az objektum-kivetítések a dúsítási fa JSON-ábrázolásai, amelyek bármely csomópontról beszerezhetők. Sok esetben ugyanaz a **Shaper** szakértelem, amely táblavetületet hoz létre, objektumvetület létrehozásához használható. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Objektumvetület létrehozásához néhány objektumspecifikus attribútumra van szükség:

+ storageContainer: A blob tároló, ahol az objektumok mentésre kerülnek
+ forrás: A dúsító fa csomópontjának elérési útja, amely a vetület gyökere

## <a name="file-projection"></a>Fájlvetés

A fájlvetések hasonlóak az objektum-kivetítésekhez, és csak a `normalized_images` gyűjteményre hatnak. Az objektumvetületekhez hasonlóan a fájlvetések is a blobtárolóba kerülnek a dokumentumazonosító base64 kódolású értékének mappaelőtaggal. A fájlvetések nem osztozhatnak ugyanazon a tárolón, mint az objektum-kivetítések, és egy másik tárolóba kell kivetíteni őket.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Vetítési életciklus

Az előrejelzések életciklusa az adatforrás forrásadataihoz van kötve. Az adatok frissítésekor és újraindexelése kor az előrejelzések frissülnek a dúsítások eredményeivel, biztosítva, hogy az előrejelzések végül konzisztensek legyenek az adatforrásban lévő adatokkal. A kivetítések öröklik az indexhez beállított törlési házirendet. Az előrejelzések nem törlődnek, ha az indexelő vagy maga a keresési szolgáltatás is törlődik.

## <a name="using-projections"></a>Vetületek használata

Az indexelő futtatása után olvashatja a vetített adatokat a megadott tárolókban vagy táblákban a vetítések.

Az elemzésekhez a Power BI feltárása olyan egyszerű, mint az Azure Table storage beállítása, mint az adatforrás. A különböző kapcsolatok segítségével egyszerűen létrehozhat vizualizációkat az adatokon.

Másik lehetőségként, ha a bővített adatokat egy adatelemzési folyamatban kell használnia, [betöltheti a blobok adatait egy Pandas DataFrame-be.](../machine-learning/team-data-science-process/explore-data-blob.md)

Végül, ha az adatokat a tudástárolóból kell exportálnia, az Azure Data Factory összekötőkkel rendelkezik az adatok exportálásához és az Ön által választott adatbázisba való beírásához. 

## <a name="next-steps"></a>További lépések

Következő lépésként hozza létre az első tudástárolót mintaadatok és utasítások használatával.

> [!div class="nextstepaction"]
> [Hozzon létre egy tudástárolót a REST-ben.](knowledge-store-create-rest.md)

A speciális vetítések fogalmait lefedő oktatóanyag, mint a szeletelés, a szövegközi formázás és a kapcsolatok, kezdje [a tudástárolóban lévő kivetítések meghatározásával](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Vetületek definiálása tudástárolóban](knowledge-store-projections-examples.md)
