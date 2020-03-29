---
title: Vetületek definiálása tudástárolóban
titleSuffix: Azure Cognitive Search
description: Példák a bővített dokumentumok power BI vagy Azure ML használatával történő használatra a tudástárolóba történő kivetítésének gyakori mintáira.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943682"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Tudástár előrejelzések: Hogyan kell alakítani és export dúsítások

> [!IMPORTANT] 
> A tudástároló jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-preview verziója](search-api-preview.md) előnézeti funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

Az előrejelzések a tudástárolóban gazdagított dokumentumok fizikai kifejeződései. A bővített dokumentumok hatékony használatához struktúrára van szükség. Ebben a cikkben a szerkezetet és a kapcsolatokat is megismerheti, hogyan hozhat létre vetítési tulajdonságokat, valamint hogyan lehet az adatokat a létrehozott vetítési típusok között összekapcsolni. 

Vetítés létrehozásához az adatokat [shaper szakértelem](cognitive-search-skill-shaper.md) használatával kell alakítania egyéni objektum létrehozásához, vagy a szövegközi formázás szintaxisát kell használnia egy vetítési definíción belül. 

Az adatalakzat tartalmazza a kivetíteni kívánt összes adatot, csomópontok hierarchiájaként létrehozva. Ez a cikk az adatok formázásának számos technikát mutat be, hogy azokat olyan fizikai struktúrákba vetíthessék, amelyek elősegítik a jelentéskészítést, elemzést vagy a későbbi feldolgozást. 

Az ebben a cikkben bemutatott példák ebben a [REST API-mintában](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)találhatók, amelyet http-ügyfélben tölthet le és futtathat.

## <a name="introduction-to-the-examples"></a>Bevezetés a példák

Ha ismeri az [előrejelzéseket,](knowledge-store-projection-overview.md)akkor emlékezni fog arra, hogy három típusa van:

+ Táblák
+ Objektumok
+ Fájlok

A táblavetések az Azure Table storage-ban tárolódnak. Az objektum- és fájlvetések blobtárolóba kerülnek, ahol az objektum-vetületek JSON-fájlokként kerülnek mentésre, és tartalmazhatnak a forrásdokumentumból származó tartalmat, valamint a szakértelem kimeneteit vagy bővítését. A dúsítási folyamat bináris fájlokat is kinyerhet, például képeket, ezek a bináris fájlok fájlvetésként vannak kivetítve. Ha egy bináris objektum objektumvetületként van kivetítve, csak a hozzá társított metaadatok kerülnek JSON-blobként. 

Az adatformázás és az előrejelzések közötti metszéspont megértéséhez a következő skillset-et használjuk a különböző konfigurációk feltárásához. Ez a skillset feldolgozza a nyers kép- és szöveges tartalmat. Az előrejelzések a dokumentum tartalmából és a szakértelem kimeneteiből kerülnek meghatározásra a támogatni kívánt forgatókönyvekhez.

> [!IMPORTANT] 
> A kivetítésekkel való kísérletezés során hasznos [az indexelő gyorsítótár-tulajdonságának beállítása a](search-howto-incremental-index.md) költségszabályozás biztosítása érdekében. A vetítések szerkesztése a teljes dokumentum újra gazdagítását eredményezi, ha az indexelő gyorsítótára nincs beállítva. Ha a gyorsítótár be van állítva, és csak az előrejelzések frissítése, a korábban bővített dokumentumok skillset-végrehajtásai nem eredményeznek új Cognitive Services-díjakat.

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

Ezzel a skillset, `knowledgeStore` annak null alapul, az első `knowledgeStore` példa kitölti az objektumot, konfigurálva vetések, amelyek táblázatos adatstruktúrákat hozhatunk létre, amelyeket más forgatókönyvekben is használhatunk. 

## <a name="projecting-to-tables"></a>Kivetítés táblákra

Az Azure Storage-ban a táblákra való kivetítés hasznos lehet a Power BI-hoz hasonló eszközök használatával történő jelentéskészítéshez és elemzéshez. A Power BI a táblákból tud olvasni, és kapcsolatokat fedezhet fel a vetítés során létrehozott kulcsok alapján. Ha irányítópultot próbál építeni, a kapcsolódó adatok leegyszerűsítik ezt a feladatot. 

Tegyük fel, hogy megpróbálunk egy irányítópultot készíteni, ahol a dokumentumokból kinyert kulcskifejezéseket szófelhőként jeleníthetjük meg. A megfelelő adatstruktúra létrehozásához hozzáadhatunk egy Shaper-készséget a skillsethez, hogy olyan egyéni alakzatot hozzunk létre, amely tartalmazza a dokumentumspecifikus részleteket és a kulcskifejezéseket. Az egyéni alakzat `pbiShape` a `document` gyökércsomóponton lesz meghívva.

> [!NOTE] 
> A táblavetések az Azure Storage-táblák, amelyeket az Azure Storage által meghatározott tárolási korlátok szabályoznak. További információt a táblázattárolási korlátok című [témakörben talál.](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model) Érdemes tudni, hogy az entitás mérete nem haladhatja meg az 1 MB-ot, és egyetlen tulajdonság nem lehet nagyobb 64 KB-nál. Ezek a korlátok a táblákat jó megoldássá teszik nagyszámú kis entitás tárolására.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Egyéni alakzat létrehozása Shaper-szakértelem használatával

Hozzon létre egy egyéni alakzatot, amelyet a táblatárolóba vetíthet. Egyéni alakzat nélkül a vetület csak egyetlen csomópontra hivatkozhat (kimenetenként egy vetületre). Egyéni alakzat létrehozásával a különböző elemeket összesítheti egy új logikai egészbe, amely egyetlen táblázatként vetíthető ki, illetve szeletelhető és elosztható táblák gyűjteménye között. 

Ebben a példában az egyéni alakzat egyesíti a metaadatokat, az azonosított entitásokat és a kulcskifejezéseket. Az objektum `pbiShape` neve a program `/document`ban történik, és a program a alatt van. 

> [!IMPORTANT] 
> A formázás egyik célja annak biztosítása, hogy minden gazdagító csomópont jól formázott JSON-ban legyen kifejezve, ami szükséges a tudástárolóba való kivetítéshez. Ez különösen akkor igaz, ha egy dúsító fa nem jól formázott JSON-csomópontokat tartalmaz (például ha egy dúsítás primitív, például karakterláncfölé van véve).
>
> Figyelje meg az utolsó `KeyPhrases` `Entities`két csomópontot, és . Ezek egy érvényes JSON objektumba `sourceContext`vannak csomagolva a. Erre a `keyphrases` primitívek engazdagításaként és `entities` annak meggazdagítására van szükség, és a kivetítés előtt érvényes JSON-ra kell konvertálni.
>


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

Adja hozzá a fenti Shaper készség a skillset. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Most, hogy már rendelkezünk a táblákra való kivetítéshez szükséges összes adattal, frissítse a knowledgeStore objektumot a tábladefiníciókkal. Ebben a példában három táblával rendelkezünk, `source` `generatedKeyName` amelyeket a és a `tableName`tulajdonságok beállításával határozunk meg.

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

A munkát az alábbi lépésekkel dolgozhatja fel:

1. Állítsa ```storageConnectionString``` be a tulajdonságot egy érvényes V2 általános célú tárfiók kapcsolati karakterláncra.  

1. Frissítse a skillset a PUT kérés kiadásával.

1. A skillset frissítése után futtassa az indexelőt. 

Most már van egy működő vetítése három asztallal. Ha ezeket a táblákat power BI-ba importálja, a Power BI automatikusan felfedezi a kapcsolatokat.

Mielőtt továbblépne a következő példára, tegyük lehetővé, hogy újra szempontjait a táblázat vetülete megérteni a mechanika szeletelés és a kapcsolódó adatok.

### <a name="slicing"></a>Szeletelés 

A szeletelés olyan technika, amely egy egész összevont alakzatot alkotó részekre oszt fel. Az eredmény különálló, de kapcsolódó táblákból áll, amelyekkel egyénileg dolgozhat.

A példában `pbiShape` az összevont alakzat (vagy dúsítási csomópont). A vetítési `pbiShape` definícióban a rendszer további táblázatokra van szeletelve, ```keyPhrases``` ami ```Entities```lehetővé teszi az alakzat egyes részeinek kihúzását, és a . A Power BI-ban ez akkor hasznos, ha az egyes dokumentumokhoz több entitás és kulcsmondat van társítva, és további elemzéseket kap, ha az entitásokat és a keyPhraseeket kategorizált adatként látja.

A szeletelés implicit módon kapcsolatot hoz létre a ```generatedKeyName``` szülő- és a gyermektáblák között, a szülőtáblában lévővel azonos nevű oszlopot hoz létre a gyermektáblában. 

### <a name="naming-relationships"></a>Kapcsolatok elnevezése

A ```generatedKeyName``` ```referenceKeyName``` és a tulajdonságok segítségével az adatok at táblák között, vagy akár a vetítési típusok között. A gyermektábla/vetítés minden sorában van egy tulajdonság, amely a szülőre mutat vissza. A gyermek oszlopának vagy tulajdonságának ```referenceKeyName``` neve a szülőtől származik. Ha ```referenceKeyName``` a nincs megadva, a szolgáltatás ```generatedKeyName``` alapértelmezés szerint a szülőtől. 

A Power BI ezekre a létrehozott kulcsokra támaszkodik a táblákon belüli kapcsolatok felderítéséhez. Ha a gyermektábla oszlopát eltérően kell ```referenceKeyName``` elnevezni, állítsa be a tulajdonságot a szülőtáblára. Az egyik példa az ```generatedKeyName``` lenne, hogy állítsa be az ```referenceKeyName``` azonosítót a pbiDocument táblában, és a DocumentID. Ez azt eredményezné, hogy a pbiEntities és pbiKeyPhrases táblák, amelyek a dokumentumazonosítót tartalmazó oszlopban documentID-nek nevezik el.

## <a name="projecting-to-objects"></a>Kivetítés objektumokra

Az objektum-kivetítések nem ugyanazok a korlátozások, mint a táblázat-kivetítések, és jobban megfelelnek a nagy dokumentumok kivetítéséhez. Ebben a példában a teljes dokumentumot egy objektumvetületre vetítjük ki. Az objektumkivetítések egyetlen kivetítésre korlátozódnak egy tárolóban, és nem szeletelhetők.

Objektumvetület definiálásához a ```objects``` tömböt használjuk a vetítésekben. Új alakzatot hozhat létre a Shaper szakértelem használatával, vagy használhatja az objektumvetület szövegközi alakítását. Míg a táblázatok példa mutatta be a megközelítés egy alakzat létrehozása és szeletelés, ez a példa azt mutatja, a szövegközi formázás. 

A szövegközi formázás egy új alakzat létrehozásának lehetősége a vetítés bemeneteinek meghatározásában. A szövegközi formázás egy névtelen objektumot hoz létre, amely megegyezik `pbiShape`azzal, amit egy Shaper képesség eredményezne (a mi esetünkben). A szövegközi formázás akkor hasznos, ha olyan alakzatot határoz meg, amelyet nem kíván újrafelhasználni.

Az előrejelzések tulajdonság egy tömb. Ebben a példában egy új vetítési példányt adunk hozzá a tömbhöz, ahol a knowledgeStore definíció inline vetületeket tartalmaz. Inline vetületek használataesetén kihagyhatja a Shaper-képességet.

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

## <a name="projecting-to-file"></a>Kivetítés fájlba

A fájlvetések olyan képek, amelyeket vagy a forrásdokumentumból, vagy a dúsítási kimenetekből nyernek ki, amelyek kivetíthetők a dúsítási folyamatból. Az objektumvetéshez hasonló fájlvetések az Azure Storage-ban blobként vannak megvalósítva, és tartalmazzák a lemezképet. 

Fájlvetés létrehozásához a `files` vetítési objektum tömbjét használjuk. Ez a példa a dokumentumból kinyert `samplefile`összes lemezképet egy tárolóba vetíti ki.

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

Egy összetettebb forgatókönyv szükség lehet a projekt tartalmat vetítési típusok között. Ha például bizonyos adatokat, például a kulcskifejezéseket és az entitásokat táblákba kell kivetítenie, mentse a szöveg és az elrendezés szövegének OCR-eredményeit objektumként, majd vetítse ki a képeket fájlként. 

Ebben a példában a skillset frissítései a következő módosításokat tartalmazzák:

1. Hozzon létre egy táblázatot, amely minden dokumentumhoz egy sort tartalmazó sort hoz létre.
1. Hozzon létre egy, a dokumentumtáblához kapcsolódó táblát, amelyben minden kulcsmondatot sorként azonosítanak.
1. Hozzon létre egy, a bizonylattáblához kapcsolódó táblát, amelyben minden entitást sorként azonosítanak ebben a táblában.
1. Objektumvetület létrehozása az egyes képek elrendezésszövegével.
1. Hozzon létre egy fájlvetést, kivetítve minden egyes kibontott képet.
1. Hozzon létre egy kereszthivatkozás-táblázatot, amely a dokumentumtáblára, az objektumvetületre és a fájlvetületre hivatkozik.

Ezek a változások a knowledgeStore definíciója lejjebb jelenik meg. 

### <a name="shape-data-for-cross-projection"></a>Alakadatok a keresztvetítéshez

A vetítésekhez szükséges alakzatok eléréséhez először adjon hozzá egy új Shaper `crossProjection`készséget, amely létrehoz egy alakú objektumot, amelynek neve . 

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

### <a name="define-table-object-and-file-projections"></a>Táblázat-, objektum- és fájlvetületek meghatározása

Az összevont crossProjection objektumból az objektumot több táblára szeletelhetjük, az OCR-kimenetet blobként rögzíthetjük, majd mentheti a lemezképet fájlként (a Blob storage-ban is).

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

Az objektum-kivetítések hez minden vetéshez, objektumvetülethez vagy fájlvetéshez tárolónév szükséges. 

### <a name="relationships-among-table-object-and-file-projections"></a>Táblák, objektumok és fájlvetületek közötti kapcsolatok

Ez a példa a vetítések egy másik jellemzőjére is rávilágít. Ha többféle vetületet határoz meg ugyanazon a vetítési objektumon belül, akkor a különböző típusokon (táblázatokon, objektumokon, fájlokon) belül és azok között is van egy kapcsolat, amely lehetővé teszi, hogy egy dokumentum táblázatsorával kezdje, és megkeresse a képek hez tartozó összes OCR-szöveget. az objektum vetületében. 

Ha nem szeretné, hogy az adatok kapcsolódnak, határozza meg a vetületeket a különböző vetítési objektumokban. A következő kódrészlet például azt eredményezi, hogy a táblák egymáshoz kapcsolódnak, de a táblák és az objektum (OCR-szöveg) vetületei közötti kapcsolatok nélkül. 

A vetítési csoportok akkor hasznosak, ha ugyanazokat az adatokat különböző alakzatokban szeretné kivetíteni a különböző igényekhez. Például egy vetítési csoport a Power BI irányítópult, és egy másik vetítési csoport a gépi tanulási modell betanításához használt adatok egy egyéni szakértelem.

A különböző típusú vetületek létrehozásakor először fájl- és objektumvetületek jönnek létre, és a görbék hozzáadódnak a táblákhoz.

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

## <a name="common-issues"></a>Gyakori problémák

Az előrejelzés meghatározásakor van néhány gyakori probléma, amely nem várt eredményeket okozhat. Ellenőrizze ezeket a problémákat, ha a tudástároló kimenete nem az, amit elvár.

+ Nem alakítja a karakterlánc-dúsításokat érvényes JSON-sá. Ha a karakterláncok gazdagodnak, például `merged_content` kulcskifejezésekkel gazdagodnak, a dúsított tulajdonság a dúsító fán `merged_content` belüli gyermekként jelenik meg. Az alapértelmezett ábrázolás nem jól formázott JSON. Így a vetítési időpontban győződjön meg arról, hogy a dúsítást érvényes JSON-objektummá alakítja, amelynek neve és értéke van.

+ ```/*``` A forráselérési út végének elhagyása. Ha a vetület `/document/pbiShape/keyPhrases`forrása, akkor a program a tömb billentyűkifejezéseit egyetlen objektumként/sorként vetíti ki. Ehelyett állítsa be úgy `/document/pbiShape/keyPhrases/*` a forrásútvonalat, hogy az minden egyes kulcskifejezéshez egyetlen sort vagy objektumot hozzon létre.

+ Elérési út szintaktikai hibái. Az elérési út választók nem tudják kijelölni a kis- és nagybetűket, és hiányzó beviteli figyelmeztetéseket eredményezhetnek, ha nem a pontos kis- és nagybetűket használja a választóhoz.

## <a name="next-steps"></a>További lépések

Ebben a cikkben szereplő példák bemutatják az előrejelzések létrehozásának gyakori mintáit. Most, hogy van egy jó megértése a fogalmak, akkor jobban felszerelt építeni előrejelzések az adott forgatókönyv.

Az új funkciók feltárása korként vegye figyelembe a növekményes bővítést a következő lépésként. A növekményes bővítés a gyorsítótárazáson alapul, amely lehetővé teszi a képességek módosítása által egyébként nem érintett dúsítások újrafelhasználását. Ez különösen hasznos az OCR-t és a képelemzést tartalmazó folyamatok esetében.

> [!div class="nextstepaction"]
> [Bevezetés a növekményes dúsításba és gyorsítótárazásba](cognitive-search-incremental-indexing-conceptual.md)

Az előrejelzések áttekintéséhez tudjon meg többet a képességekről, például a csoportokról és a szeletelésről, és arról, hogyan [definiálhatja őket egy skillsetben.](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Előrejelzések egy tudástárban](knowledge-store-projection-overview.md)

