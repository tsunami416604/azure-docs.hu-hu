---
title: Egyéni entitáskeresési kognitív keresési készség
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search kognitív keresési folyamatának szövegéből különböző egyéni entitásokat nyerhet ki. Ez a szakértelem jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 8674438032ebd925296c95e9ffa0a2a0b95322f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369777"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Egyéni entitáskeresgő kognitív képessége (előzetes verzió)

> [!IMPORTANT] 
> Ez a szakértelem jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Jelenleg nincs portál- vagy .NET SDK-támogatás.

Az **Egyéni entitáskeresési** szakértelem a szavak és kifejezések egyéni, felhasználó által definiált listájából származó szöveget keres. A lista használatával minden dokumentumot címkéz, amely bármilyen egyező entitással van ellátva. A készség is támogatja bizonyos fokú fuzzy megfelelő, hogy lehet alkalmazni, hogy megtalálja mérkőzések, amelyek hasonlóak, de nem egészen pontos.  

Ez a szakértelem nem kötődik a Cognitive Services API-hoz, és ingyenesen használható az előzetes verzió ban. Továbbra is [csatolja a Cognitive Services-erőforrást,](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)azonban a napi dúsítási korlát felülbírálásához. A napi korlát a Cognitive Services ingyenes elérésére vonatkozik, ha az Azure Cognitive Search-en keresztül érhető el.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>Adatkorlátok
+ A támogatott bemeneti rekord maximális mérete 256 MB. Ha meg kell szakítania az adatokat, mielőtt elküldené azokat az egyéni entitás-kereshető szakértelemnek, fontolja meg a [Szövegfelosztás szakértelem](cognitive-search-skill-textsplit.md)használatát.
+ A támogatott entitások maximális definíciós táblája 10 MB, ha az *entitásokDefitionUri* paraméterhasználatával van megadva. 
+ Ha az entitások inline definiálva vannak, az *inlineEntitiesDefinition* paraméter használatával a maximális támogatott méret 10 KB.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| entitásokDefinitionUri    | JSON- vagy CSV-fájl elérési útja, amely tartalmazza az összes egyező célszöveget. Ez az entitásdefiníció egy indexelő futtatásának elején olvasható; a fájl futtatás közbeni frissítései csak a későbbi futtatások után valósulnak meg. Ennek a konfigurációnak HTTPS-en keresztül elérhetőnek kell lennie. Lásd: [Egyéni entitásdefiníciós](#custom-entity-definition-format) formátum" alább a várt CSV- vagy JSON-sémát.|
|inlineEntitiesDefinition | Inline JSON entitásdefiníciók. Ez a paraméter felülírja az entitiesDefinitionUri paramétert, ha van ilyen. Legfeljebb 10 KB konfiguráció adható meg a szövegközi konfigurációban. Lásd: [Egyéni entitás definíciója](#custom-entity-definition-format) az alábbi a várt JSON-séma. |
|defaultLanguageCode |    (Nem kötelező) A bemeneti szöveg tokenizálásához és határvonalához használt szöveg nyelvkódja. A következő nyelvek `da, de, en, es, fi, fr, it, ko, pt`támogatottak: . Az alapértelmezett érték`en`az angol ( ). Ha nyelvkód-országkód formátumot ad meg, a program csak a formátum languagecode részét használja.  |


## <a name="skill-inputs"></a>Szakértelem bemenetei

| Bemenet neve      | Leírás                   |
|---------------|-------------------------------|
| szöveg          | Az elemző szöveg.          |
| languageCode    | Választható. Az alapértelmezett szint a `"en"`.  |


## <a name="skill-outputs"></a>Szakértelem kimenetei


| Kimenet neve      | Leírás                   |
|---------------|-------------------------------|
| Entitások | Objektumok tömbje, amelyek a talált egyezésekkel és a kapcsolódó metaadatokkal kapcsolatos információkat tartalmaznak. Az azonosított entitások mindegyike a következő mezőket tartalmazhatja:  <ul> <li> *név*: Az azonosított legfelső szintű entitás. Az entitás a "normalizált" képernyőt jelöli. </li> <li> *id*: Az entitás egyedi azonosítója a felhasználó által az "Egyéni entitásdefiníciós formátum" mezőben meghatározottak szerint.</li> <li> *description*: Az entitás leírása a felhasználó által az "Egyéni entitásdefiníciós formátum" mezőben meghatározottak szerint. </li> <li> *típus:* Entitástípus a felhasználó által az "Egyéni entitásdefiníciós formátum" mezőben meghatározottak szerint.</li> <li> *altípus:* Entitás altípus a felhasználó által az "Egyéni entitásdefiníciós formátum" mezőben meghatározottak szerint.</li>  <li> *egyezik*: Gyűjtemény, amely leírja az adott entitás minden egyezését a forrásszövegben. Minden mérkőzésnek a következő tagjai lesznek: </li> <ul> <li> *szöveg*: A nyers szöveg megegyezik a forrásdokumentumban. </li> <li> *eltolás*: Az a hely, ahol az egyezés található a szövegben. </li> <li> *hossz*: Az egyező szöveg hossza. </li> <li> *matchDistance*: Az egyezéstől eltérő karakterek száma az eredeti entitásnévtől vagy aliastól.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Egyéni entitásdefiníció formátuma

Az egyéni entitások listájának három különböző módja van az egyéni entitások keresése szakértelemnek való megadására. A listát a. CSV fájl, a . JSON-fájlként vagy szövegközi definícióként a szakértelem definíciójának részeként.  

Ha a definíciós fájl egy . CSV vagy . JSON-fájl, a fájl elérési útját meg kell adni az *entitásokDefitionUri* paraméter részeként. Ebben az esetben a fájl letöltése egyszer minden indexelő futtatásakor egyszer lesz letöltve. A fájlnak mindaddig elérhetőnek kell lennie, amíg az indexelő futni kíván. Továbbá, a fájl kell kódolni UTF-8.

Ha a definíció inline meg van adva, akkor az *inlineEntitiesDefinition* szakértelem paraméter tartalmaként kell megadni. 

### <a name="csv-format"></a>CSV formátum

A vesszővel tagolt érték (CSV) fájlban megkereshető egyéni entitások definícióját úgy adhathatja meg, hogy megadja a fájl elérési útját, és beállítja azt az *entitásokDefitionUri* szakértelem paraméterben. Az elérési útnak https-helyen kell lennie. A definíciós fájl mérete legfeljebb 10 MB lehet.

A CSV formátum egyszerű. Minden sor egyedi entitást jelöl, az alábbiak szerint:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

Ebben az esetben három entitás, amely visszaadható entitások találhatók (Bill Gates, Satya Nadella, Microsoft), de a rendszer azonosítja őket, ha a kifejezés a szövegben bármelyik feltétel (alias) egyeztetve. Ha például a "William H. Gates" karakterlánc megtalálható egy dokumentumban, a "Bill Gates" entitás egyezése lesz visszaadva.

### <a name="json-format"></a>JSON formátum

A JSON-fájlban keresandó egyéni entitások definícióját is megadhatja. A JSON formátum egy kicsit nagyobb rugalmasságot biztosít, mivel lehetővé teszi a megfelelő szabályok meghatározását kifejezésenként. Megadhatja például az egyes kifejezéshez a fuzzy egyezési távolságot (Damerau-Levenshtein távolság), vagy hogy az egyeztetésnek kis- és nagybetűket kell-e figyelembe vennie. 

 Csakúgy, mint a CSV-fájlok, meg kell adnia a JSON-fájl elérési útját, és be kell állítania az *entitiesDefitionUri* szakértelem paraméterben. Az elérési útnak https-helyen kell lennie. A definíciós fájl mérete legfeljebb 10 MB lehet.

A legalapvetőbb JSON egyéni entitáslista-definíció lehet az egyező entitások listája:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

A JSON-definíció összetettebb példája opcionálisan megadhatja az egyes entitások azonosítóját, leírását, típusát és altípusát , valamint más *aliasokat.* Ha egy alias kifejezés egyeztetése történik, az entitás t is visszaadja:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

Az alábbi táblázatok részletesebben ismertetik a különböző konfigurációs paramétereket, amelyeket az egyező entitások definiálásakor állíthat be:

|  Mező neve  |        Leírás  |
|--------------|----------------------|
| név | A legfelső szintű entitásleíró. A szakértelem kimenetében szereplő egyezések ezen a néven lesznek csoportosítva, és a talált szöveg "normalizált" formáját kell képviselniük.  |
| leírás  | (Nem kötelező) Ez a mező áthaladásként használható az egyező szöveg(ek) egyéni metaadataihoz. Ennek a mezőnek az értéke az entitás minden egyezésével megjelenik a szakértelem kimenetében. |
| type | (Nem kötelező) Ez a mező áthaladásként használható az egyező szöveg(ek) egyéni metaadataihoz. Ennek a mezőnek az értéke az entitás minden egyezésével megjelenik a szakértelem kimenetében. |
| Altípus | (Nem kötelező) Ez a mező áthaladásként használható az egyező szöveg(ek) egyéni metaadataihoz. Ennek a mezőnek az értéke az entitás minden egyezésével megjelenik a szakértelem kimenetében. |
| id | (Nem kötelező) Ez a mező áthaladásként használható az egyező szöveg(ek) egyéni metaadataihoz. Ennek a mezőnek az értéke az entitás minden egyezésével megjelenik a szakértelem kimenetében. |
| kis- és nagybetűk megkülönböztetése | (Nem kötelező) Az alapértelmezett érték hamis. Logikai érték, amely azt jelzi, hogy az entitásnevével való összehasonlításnak érzékenynek kell-e lennie a karakterházra. A "Microsoft" nem megfelelő kis- és nagybetűket nem megkülönböztető egyezései lehetnek: microsoft, microSoft, MICROSOFT |
| fuzzyEditTávolság | (Nem kötelező) Alapértelmezés szerint 0. Maximális értéke 5. Az entitás nevével egyező eltérő karakterek elfogadható számát jelöli. A lehető legkisebb fuzziness egy adott mérkőzés vissza.  Ha például a szerkesztési távolság 3, a "Windows 10" továbbra is megfelel a "Windows", a "Windows10" és a "Windows 7" beállításnak. <br/> Ha a kis- és nagybetűk megkülönböztetése hamis, a kis- és nagybetűk közötti különbségek nem számítanak bele a fuzziness toleranciába, de egyébként igen. |
| defaultCaseSensitive | (Nem kötelező) Módosítja az entitás alapértelmezett kis- és nagybetűk megkülönböztetését. Az összes alias alapértelmezett értékének módosítására használható: kis- és nagybetűkMegkülönböztetés. |
| defaultFuzzyEditDistance | (Nem kötelező) Módosítja az entitás alapértelmezett intelligens szerkesztési távolságának értékét. Az összes álnév alapértelmezett értékének módosítására használható: fuzzyEditDistance értékek. |
| Aliasok | (Nem kötelező) Összetett objektumok tömbje, amely a gyökérentitás nevének alternatív helyesírási vagy szinonimáinak megadására használható. |

| Alias tulajdonságai | Leírás |
|------------------|-------------|
| szöveg  | Egyes célentitások nevének másik helyesírása vagy ábrázolása.  |
| kis- és nagybetűk megkülönböztetése | (Nem kötelező) Ugyanúgy működik, mint a fenti "caseSensitive" paraméter, de csak erre az aliasra vonatkozik. |
| fuzzyEditTávolság | (Nem kötelező) Ugyanúgy működik, mint a fenti "fuzzyEditDistance" paraméter, de csak erre az aliasra vonatkozik. |


### <a name="inline-format"></a>Szövegközi formátum

Bizonyos esetekben kényelmesebb lehet az egyéni entitások listáját közvetlenül a szakértelem-definícióba egyeztetni. Ebben az esetben a fent leírthoz hasonló JSON formátumot használhat, de a szakértelem-definíció ban van.
Csak a 10 KB-nál kisebb (szerializált méretű) konfigurációk definiálhatók szövegközi. 

##    <a name="sample-definition"></a>Minta definíciója

A szövegközi formátumot használó mintaszakértelem-definíció az alábbiakban látható:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Másik lehetőségként, ha úgy dönt, hogy mutatót ad az entitások definíciós fájljára, az entitásokDefiníciósUri formátumot használó mintaszakértelem-definíció ja:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Mintabevitel

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Példa kimenet

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Figyelmeztetés: Elérte a találatok maximális kapacitását, kihagyva az összes további ismétlődő egyezést.

Ez a figyelmeztetés akkor jelenik meg, ha az észlelt egyezések száma nagyobb, mint a megengedett maximális érték. Ebben az esetben leállítjuk a kettős egyezéseket. Ha ez elfogadhatatlan az Ön számára, kérjük, nyújtson be [támogatási jegyet,](https://ms.portal.azure.com/#create/Microsoft.Support) hogy segíthessünk önnek az egyéni használati ügyben.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Entitásfelismerési szakértelem (jól ismert entitások keresése)](cognitive-search-skill-entity-recognition.md)
