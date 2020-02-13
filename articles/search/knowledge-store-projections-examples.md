---
title: Kivetítések meghatározása a Knowledge Store-ban
titleSuffix: Azure Cognitive Search
description: Példák arra, hogy miként lehet a dúsított dokumentumokat a Power BI vagy az Azure ML-vel való használatra használni a Tudásbázisban.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165513"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Knowledge Store-kivetítések: gazdagítása és exportálása a Knowledge Store-ba

> [!IMPORTANT] 
> A Knowledge Store jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) előzetes funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

A kivetítések az adott Tudásbázisban a dúsított dokumentumok fizikai kifejezései. A dúsított dokumentumok hatékony használata struktúrát igényel. Ebben a cikkben a struktúra és a kapcsolatok megismerését, a kivetítési tulajdonságok kiépítését, valamint a létrehozott kivetítési típusok közötti adatmegjelenítés módját ismerteti. 

A kivetítés létrehozásához a shapeer-szakértelem használatával vagy egy egyéni objektum létrehozásához, vagy a beágyazott alakítás szintaxisát kell használnia. Egy adatalakzat tartalmazza az összes projekthez szükséges összes adathalmazt. Ez a dokumentum az egyes lehetőségekre mutat példát, és a létrehozott vetítések közül bármelyiket használhatja.


Háromféle kivetítési típus létezik:
+ Táblák
+ Objektumok
+ Fájlok

A táblázatos előrejelzések tárolása az Azure Table Storage szolgáltatásban történik. Az objektumok és a fájlok kivetítése blob Storage-ba történik, az objektumok kivetítése JSON-fájlként lesz mentve, és tartalmakat tartalmazhat a dokumentumból, valamint a hozzájuk tartozó összes képességet vagy dúsítást. A dúsítási folyamat olyan bináris fájlokat is képes kinyerni, mint például a képek, ezek a bináris fájlok fájl-kivetítésként vannak kiválasztva. Ha egy bináris objektum kivetítése objektumként történik, csak a hozzá társított metaadatokat JSON-blobként menti a program. 

Az Adatátalakítási és-kivetítések közötti metszéspont megismeréséhez a következő készségkészlet fogjuk használni a különböző konfigurációk feltárásához. Ez a készségkészlet a nyers képet és a szöveges tartalmat dolgozza fel. A kivetítések a dokumentum tartalmából és a szaktudás kimenetéről lesznek meghatározva a támogatni kívánt forgatókönyvek esetében.

Azt is megteheti, hogy letölti és felhasznál egy [REST API mintát](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) az ebben az útmutatóban szereplő összes hívással.

> [!IMPORTANT] 
> A kivetítések kipróbálásakor hasznos az [Indexelő gyorsítótár tulajdonságának beállítása](search-howto-incremental-index.md) a Cost Control biztosításához. A kivetítések szerkesztése azt eredményezi, hogy a teljes dokumentumot újra gazdagítja a rendszer, ha nincs beállítva az indexelő gyorsítótára. Ha a gyorsítótár be van állítva, és csak az előrejelzések frissülnek, a korábban dúsított dokumentumok készségkészlet végrehajtása nem eredményez Cognitive Servicesi díjat.


```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Most hozzáadhatja a `knowledgeStore` objektumot, és szükség szerint konfigurálhatja az egyes forgatókönyvek kivetítéseit. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Kivetítés táblázatokra olyan forgatókönyvek esetén, mint a Power BI

> [!NOTE] 
> Mivel a Knowledge Store egy Azure Storage-fiók, a táblázatos előrejelzések az Azure Storage-táblák, amelyek a táblák tárolási korlátaira vonatkoznak, további információ: [Table Storage-korlátok](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Érdemes tudni, hogy az entitás mérete nem haladhatja meg az 1 MB-ot, és egyetlen tulajdonság sem lehet nagyobb, mint 64 KB. Ezek a megkötések jó megoldást biztosítanak a táblák nagy számú kis entitásának tárolására.

Power BI tud olvasni a táblákból, és kapcsolatokat derít fel a Knowledge Store-vetítések által létrehozott kulcsok alapján, így a táblázatok jó választást tesznek lehetővé az adatok kivetítéséhez, amikor egy irányítópultot szeretne létrehozni a dúsított adatokon. Feltételezve, hogy olyan irányítópultot próbálunk létrehozni, amelyben a dokumentumokból Word-felhőként kinyert fő kifejezéseket láthatjuk, hozzáadhatunk egy formáló képességet a készségkészlet egy olyan egyéni alakzat létrehozásához, amely a dokumentumra vonatkozó részleteket és a legfontosabb kifejezéseket tartalmaz. Adja hozzá a formáló képességet a készségkészlet, és hozzon létre egy új, ```pbiShape``` nevű dúsítást a ```document```.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Egyéni alakzat létrehozása a Shapeer-szakértelem használatával

Hozzon létre egy egyéni alakzatot, amelyet táblázatos tárolóba tud készíteni. Egyéni alakzat nélkül a leképezés csak egyetlen csomópontra hivatkozhat (a kimenet egy vetülete). Az egyéni alakzat létrehozása lehetővé teszi a különböző elemek egy új logikai egészbe való összesítését, amelyet egyetlen táblázatként lehet kiszolgálni, illetve a táblázatok gyűjteménye között darabolva és elosztva. Ebben a példában az egyéni alakzat a metaadatokat és az azonosított entitásokat és a kulcsfontosságú kifejezéseket ötvözi. Az objektum neve pbiShape, és a `/document`alatt van szülője. 

> [!IMPORTANT] 
> A dúsítás forrásának elérési útjai szükségesek ahhoz, hogy megfelelően formázott JSON-objektumokat tudjanak kialakítani. A dúsítási fa olyan dúsítást jelenthet, amely nem megfelelően formázott JSON, például akkor, ha a dúsítás egy primitve, például egy sztringhez van szülője. Vegye figyelembe, hogy a `KeyPhrases` és `Entities` egy érvényes JSON-objektumba vannak becsomagolva a `sourceContext`, ez a `keyphrases`, a `entities` pedig a primitívek gazdagítása, és a kivetítésük előtt érvényes JSON-re kell alakítani.

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForTables",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_content_type",
                    "source": "/document/metadata_storage_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_path",
                    "source": "/document/metadata_storage_path",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_content_type",
                    "source": "/document/metadata_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "Entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "pbiShape"
                }
            ]
        }
```
Adja hozzá a készségkészlet a szaktudás listához imént definiált formáló képességet. 

Most, hogy minden szükséges adattal rendelkezünk a táblákhoz, frissítse a knowledgeStore objektumot a táblázat-definíciókkal. 

```json

"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Állítsa a ```storageConnectionString``` tulajdonságot érvényes v2 általános célú Storage-fiók kapcsolódási karakterláncára. Ebben az esetben a kivetítési objektumban három táblát definiálunk a ```tableName```, ```source``` és ```generatedKeyName``` tulajdonságok beállításával. Most már frissítheti a készségkészlet a PUT-kérelem kiállításával.

### <a name="slicing"></a>Szeletelés 

Ha olyan összevont alakzatot indít, amelyben az összes tervezett tartalom egyetlen alakban (vagy dúsítási csomópontban) van, akkor a szeletelő lehetővé teszi, hogy egyetlen csomópontot több táblába vagy objektumba lehessen darabolni. Itt a ```pbiShape``` objektum több táblázatba van darabolva. A szeletelési funkció lehetővé teszi, hogy kihúzza az alakzat egyes részeit, ```keyPhrases``` és ```Entities``` különálló táblákba. Ez akkor lehet hasznos, ha az egyes dokumentumokhoz több entitás van társítva. A szeletelők implicit módon létrehoznak egy kapcsolatot a szülő és a gyermek táblázat között, a fölérendelt táblában lévő ```generatedKeyName``` használatával, hogy egy azonos nevű oszlopot hozzon létre a gyermek táblában. 

### <a name="naming-relationships"></a>Elnevezési kapcsolatok
A ```generatedKeyName``` és ```referenceKeyName``` tulajdonságok a táblák közötti vagy akár a kivetítési típusok közötti összekötésére szolgálnak. A gyermektábla és a leképezés minden sorának egy tulajdonsága a szülőre mutat vissza. A gyermek oszlopának vagy tulajdonságának neve a szülő ```referenceKeyName```. Ha a ```referenceKeyName``` nincs megadva, a szolgáltatás alapértelmezés szerint a szülőtől a ```generatedKeyName```. A PowerBI ezen generált kulcsokra támaszkodik a táblákon belüli kapcsolatok felderítése érdekében. Ha az alárendelt táblában lévő oszlopnak másképpen kell megneveznie, állítsa be a ```referenceKeyName``` tulajdonságot a fölérendelt táblán. Az egyik példa az ```generatedKeyName``` AZONOSÍTÓként való beállítása a pbiDocument táblában, a ```referenceKeyName``` pedig DocumentID. Ez azt eredményezi, hogy a pbiEntities és a pbiKeyPhrases tábla oszlopa a DocumentID nevű dokumentum azonosítóját tartalmazza.

Mentse a frissített készségkészlet, és futtassa az indexelő, most már van egy működő kivetítése három táblával. A táblázatok Power BIba való importálása a kapcsolatok automatikus felfedését eredményezi Power BI.

## <a name="projecting-to-objects"></a>Kivetítés az objektumokra

Az objektumok kivetítése nem ugyanazokkal a korlátozásokkal rendelkezik, mint a tábla-kivetítések, jobban alkalmazkodnak a nagyméretű dokumentumok kivetítéséhez. Ebben a példában a teljes dokumentumot egy objektum-kivetítésre tervezjük. Az objektumok kivetítése a tároló egyetlen leképezésére korlátozódik.
Az objektumok kivetítésének definiálásához a ```objects``` tömböt fogjuk használni az előrejelzésekben. Létrehozhat egy új alakzatot a formázó eszközzel, vagy az objektum leképezésének beágyazott alakításával. Míg a táblák példája egy alakzat és egy szeletelés létrehozásának megközelítését szemlélteti, ez a példa a beágyazott alakítás használatát mutatja be. A beágyazott kialakítás lehetővé teszi, hogy egy új alakzatot hozzon létre a bemenetek definíciójában a kivetítéshez. A beágyazott kialakítás egy névtelen objektumot hoz létre, amely megegyezik a hasonló formáló által előállított objektummal. A beágyazott formázás akkor hasznos, ha olyan alakzatot határoz meg, amelyet nem kíván használni.
A vetítések tulajdonság egy tömb, amely ebben a példában egy új leképezési példányt ad hozzá a tömbhöz. Frissítse a knowledgeStore-definíciót a definiált kivetítésekkel, és a beágyazott kivetítések használata esetén nincs szükség formázó képességre.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```
## <a name="file-projections"></a>Fájlok kivetítése

A fájl-kivetítések a forrás dokumentumból kinyert vagy a dúsítási folyamatból kiosztható alkoholtartalom-kimenetek. Az objektumok kivetítéséhez hasonló fájl-kivetítések blobként vannak implementálva, és tartalmazzák a rendszerképet. A fájlok leképezésének létrehozásához a leképezési objektumban a ```files``` tömböt használjuk. Ez a példa a dokumentumból kinyert összes lemezképet egy `samplefile`nevű tárolóba tervezi.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Kivetítés több típusra

Az összetettebb forgatókönyvek esetében előfordulhat, hogy a tartalom a leképezési típusok között szerepel. Ha például bizonyos adatmennyiségeket, például kulcsfontosságú kifejezéseket és entitásokat szeretne táblázatként felvenni, mentse a szöveg és az elrendezés szövegének OCR-eredményeit objektumként, és a képeket fájlként jeleníti meg. A készségkészlet frissítése a következő lesz:

1. Hozzon létre egy táblázatot az egyes dokumentumok sorával.
2. Hozzon létre egy táblázatot a táblázathoz kapcsolódó minden egyes, az ebben a táblázatban szereplő sorként azonosított kulcsszóval.
3. Hozzon létre egy táblázatot a táblázathoz kapcsolódóan minden olyan entitással, amely az ebben a táblázatban szereplő sorként van meghatározva.
4. Hozzon létre egy objektumot az elrendezés szövegével minden képhez.
5. Hozzon létre egy leképezést a kinyert képek kivetítéséhez.
6. Hozzon létre egy kereszthivatkozási táblázatot, amely a dokumentum táblára mutató hivatkozásokat tartalmaz, valamint az elrendezés szövegét és a fájl leképezését tartalmazó objektumot.

Első lépésként vegyen fel egy új formálói szakértelmet ahhoz a skill tömbhöz, amely létrehoz egy formázott objektumot. 

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForCross",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                },
                {
                    "name": "images",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*",
                    "inputs": [
                        {
                            "name": "image",
                            "source": "/document/normalized_images/*"
                        },
                        {
                            "name": "layoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                        ]
                }
                
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "crossProjection"
                }
            ]
        }
```

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

Az objektumok kivetítéséhez az egyes kivetítésekhez, az objektumok kivetítéséhez vagy a fájl-kivetítésekhez szükséges tároló neve nem osztható meg tárolóval. 

### <a name="relationships"></a>Kapcsolatok

Ez a példa a kivetítések egy másik funkcióját is kiemeli, ha több különböző típusú kivetítést határoz meg ugyanabban a leképezési objektumban, fennáll a kapcsolat a különböző típusok (táblák, objektumok, fájlok) között, amely lehetővé teszi a a dokumentumhoz tartozó táblázatos sorral kezdheti meg, és megkeresheti az adott dokumentumban található lemezképek összes OCR-szövegét az objektum leképezésében. Ha nem szeretné, hogy a kapcsolódó adat, a különböző vetületi objektumokban határozza meg a kivetítéseket, például az alábbi kódrészlet a kapcsolódó táblákat eredményezi, de a táblák és az OCR-szöveg kivetítései között nem áll fenn kapcsolat. A kivetítési csoportok akkor hasznosak, ha ugyanazokat az információkat különböző alakzatokban szeretné felvenni a különböző igényekhez. Például egy kivetítési csoport a Power BI irányítópulthoz és egy másik kivetítési csoporthoz, amely az adatvédelemre szolgáló AI-modell betanításához használható.
A különböző típusú vetítések kiépítésekor először a fájl-és objektum-kivetítések jönnek létre, és az elérési utak hozzáadódnak a táblákhoz.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

Ezek a példák azt mutatják be, hogyan használhatók a kivetítések, most már jól kell ismernie az egyes forgatókönyvek kivetítésének kialakításával kapcsolatos fogalmakat.

## <a name="common-issues"></a>Gyakori problémák

A kivetítés meghatározásakor van néhány gyakori probléma, amely nem várt eredményeket okozhat.

1. A karakterlánc-gazdagítás nem alakítható ki. Ha a karakterláncok gazdagítva vannak, például ```merged_content``` a fő kifejezésekkel gazdagítva, a dúsított tulajdonság a dúsítási fában merged_content gyermeke lesz. A leképezés időpontjában azonban át kell alakítani egy érvényes JSON-objektumot egy névvel és egy értékkel.
2. A forrás elérési útjának végén lévő ```/*``` kihagyása. Ha például egy kivetítés forrása ```/document/pbiShape/keyPhrases``` a Key mondatok tömbje egyetlen objektumként/sorként van kiemelve. Ha a forrás elérési útját úgy állítja be, hogy ```/document/pbiShape/keyPhrases/*```, az egyes kulcsokra vonatkozó kifejezések egyetlen sorát vagy objektumát adja meg.
3. Az elérésiút-választók megkülönböztetik a kis-és nagybetűket, és hiányzó bemeneti figyelmeztetéseket okozhatnak, ha nem a pontos esetet használja a választóhoz.

