---
title: Kifejezés, és az Azure Data Factory funkciók |} Microsoft Docs
description: Ez a cikk tájékoztatást ad azokról a kifejezések és függvények, amelyek a data factory entitások létrehozásakor is használhatja.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 1625b37a41082f8536d103701b1356a13a5dd837
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Kifejezések és az Azure Data Factory funkciók
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-functions-variables.md)
> * [2. verzió – Előzetes verzió](control-flow-expression-language-functions.md)

Ez a cikk ismerteti a kifejezések és az Azure Data Factory (2-es verziójú) által támogatott funkciók részleteit. 

## <a name="introduction"></a>Bevezetés
JSON-értékek meghatározásában szövegkonstans vagy futásidőben kiértékelt kifejezés lehet. Példa:  
  
```json
"name": "value"
```

 (vagy)  
  
```json
"name": "@pipeline().parameters.password"
```


> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [funkciók és a Data Factory V1 változók](v1/data-factory-functions-variables.md).


## <a name="expressions"></a>Kifejezések  
Kifejezések bárhol megjelenhet egy JSON karakterláncértéket, és egy másik JSON-érték mindig eredményez. A JSON értéke egy kifejezés, ha a szervezet a kifejezés ki kell olvasni, a-kukac eltávolításával (@). Egy szöveges karakterlánc van szüksége a kezdetű @, @ használatával kell megjelölni@. Az alábbi példák bemutatják, hogyan kifejezések kiértékelése.  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|"paraméterek"|A "parameters" karaktert adja vissza.|  
|"[1] Paraméterek"|A "parameters [1]" karaktert adja vissza.|  
|"@@"|Egy 1 tartalmazó karakterlánc ' @' adja vissza.|  
|" @"|Egy 2 tartalmazó karakterlánc ' @' adja vissza.|  
  
 Kifejezések akkor is megjelenhet, karakterláncok, belül nevű szolgáltatás segítségével *köztes karakterlánc* adott kifejezések csomagolni vannak `@{ ... }`. Például:`"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Karakterlánc köztes használ, az eredmény: mindig karakterlánc. Tegyük fel például, meg van adva `myNumber` , `42` és `myString` , `foo`:  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|"@pipeline(). parameters.myString"| Beolvasása `foo` karakterláncként.|  
|"@{pipeline().parameters.myString}"| Beolvasása `foo` karakterláncként.|  
|"@pipeline(). parameters.myNumber"| Beolvasása `42` , egy *szám*.|  
|"@{a következő feldolgozási sorban ().parameters.myNumber}"| Beolvasása `42` , egy *karakterlánc*.|  
|"Válasz: @{a következő feldolgozási sorban ().parameters.myNumber}"| A karakterláncot ad vissza, `Answer is: 42`.|  
|"@concat(" Válasz: ", string(pipeline().parameters.myNumber))"| A karakterláncot ad vissza `Answer is: 42`|  
|"Válasz: @@ {a következő feldolgozási sorban ().parameters.myNumber}"| A karakterláncot ad vissza, `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Példák

#### <a name="a-dataset-with-a-parameter"></a>A paraméter egy olyan adatkészlet
A következő példában a BlobDataset egy paramétert fogad, nevű **elérési**. Az érték szolgál egy értéket, az a **folderPath** tulajdonság a következő kifejezés használatával: `@{dataset().path}`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>Egy folyamat paraméterrel
A következő példában a feldolgozási folyamat veszi **inputPath** és **outputPath** paraméterek. A **elérési** a paraméteres BLOB adatkészlet van beállítva, ezek a paraméterek használatával. Az itt használt szintaxisa: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
  
## <a name="functions"></a>Functions  
 Kifejezések függvények hívása. Az alábbi szakaszok ismertetik a funkciók kifejezésben használható.  

## <a name="string-functions"></a>Karakterlánc  
 A következő funkciók csak karakterláncok vonatkoznak. Számos, a gyűjtemény a karakterláncokra is használható.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|Concat|Tetszőleges számú karakterláncok együtt egyesíti. Például, ha parameter1 `foo,` meghaladná a következő kifejezés `somevalue-foo-somevalue`:  `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Számú paraméter**: 1... *n*<br /><br /> **Név**: karakterlánc *n*<br /><br /> **Leírás**: kötelező. A karakterláncok egyesítése egyetlen karakterlánccá egyesít.|  
|Substring|Egy karakterlánc karakterből álló alkészletet ad vissza. Például a következő kifejezést:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> adja vissza:<br /><br /> `foo`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A karakterláncot, ahol a substring használatban van.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: kezdőindex<br /><br /> **Leírás**: kötelező. Ahol a substring megkezdődik az 1. paraméter indexe.<br /><br /> **Számú paraméter**: 3<br /><br /> **Név**: hossza<br /><br /> **Leírás**: kötelező. A substring hosszát.|  
|cserélje le|Lecseréli a karakterlánc egy adott karakterláncot. Ha például a kifejezésnek:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> adja vissza:<br /><br /> `the new string`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező.  Ha a paramétert 2 paraméter 1, a keresés paraméter 2 és 3. paraméter frissült karakterláncot tartalmaz.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: régi karakterlánc<br /><br /> **Leírás**: kötelező. Ha van egyezés paraméter 1 paraméterrel 3 cserélendő karakterláncot<br /><br /> **Számú paraméter**: 3<br /><br /> **Név**: új karakterlánc<br /><br /> **Leírás**: kötelező. Cserélje le a karakterlánc a 2. paraméter, ha van egyezés paraméter 1 használt karakterlánc.|  
|GUID| Globálisan egyedi karakterlánc (más néven. (GUID). Például sikerült létrehozni a következő kimeneti `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Egyetlen formátummegadó jelző [a Guid értékének formázása](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). A formátum-paraméter "N", "D", "B", "P" vagy "X" lehet. Ha formátum nem áll rendelkezésre, "D" szolgál.|  
|toLower|Tartalmazó karakterláncot kisbetűssé alakítja. Például a következő értéket ad vissza `two by two is four`:  `toLower('Two by Two is Four')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A kis-és alacsonyabb alakítandó karakterláncot. Ha egy karaktert a karakterlánc nem rendelkezik a kisbetűs megfelelőjét, szerepel a visszaadott karakterlánc változatlan.|  
|toUpper|Tartalmazó karakterláncot nagybetűssé alakítja. Például az alábbi kifejezés eredménye `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A felső kis-és alakítandó karakterláncot. Ha egy karaktert a karakterlánc nem rendelkezik nagybetűs egyenértékű, szerepel a visszaadott karakterlánc változatlan.|  
|indexof|A karakterlánc eset belüli értéket indexe insensitively található. Például az alábbi kifejezés eredménye `7`: `indexof('hello, world.', 'world')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A értéket tartalmazó karakterlánc.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. Az érték indexe.|  
|lastindexof|A karakterlánc eset belüli értéket utolsó indexe insensitively található. Például az alábbi kifejezés eredménye `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A értéket tartalmazó karakterlánc.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. Az érték indexe.|  
|startswith elemnek|Ellenőrzi, hogy a karakterlánc kezdődik-e érték eset insensitively. Például az alábbi kifejezés eredménye `true`: `lastindexof('hello, world', 'hello')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A értéket tartalmazó karakterlánc.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A karakterlánc értéke kezdődik.|  
|megadott módon végződő|Ellenőrzi, hogy ha a karakterlánc végződik érték eset insensitively. Például az alábbi kifejezés eredménye `true`: `lastindexof('hello, world', 'world')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A értéket tartalmazó karakterlánc.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. Az érték a karakterlánc végén is.|  
|felosztás|Felosztja a karakterláncot, amely egy elválasztó. Például az alábbi kifejezés eredménye `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A karakterlánc, amely van szétosztva.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. Az elválasztó.|  
  
  
## <a name="collection-functions"></a>Gyűjtemény-funkciók  
 Ezek a funkciók például a tömböket, karakterláncok és néha szótárak gyűjtemények hatnak.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|tartalmazza a következőt:|Igaz értéket ad eredményül, ha a szótár tartalmaz-e, kulcslista értéket tartalmaz, vagy karakterlánc karakterláncrészletet tartalmazza. Például az alábbi kifejezés eredménye `true:``contains('abacaba','aca')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjteményen belül<br /><br /> **Leírás**: kötelező. A gyűjteményt, amelyben keresni.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: objektum keresése<br /><br /> **Leírás**: kötelező. Az objektum belül található a **gyűjteményben**.|  
|Hossza|A tömb vagy karakterlánc elemek számát adja vissza. Például az alábbi kifejezés eredménye `3`:  `length('abc')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: kötelező. A gyűjtemény hossza eléréséhez.|  
|üres|Igaz értéket ad eredményül, ha az objektum, a tömb vagy karakterlánc üres. Például az alábbi kifejezés eredménye `true`:<br /><br /> `empty('')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: kötelező. Annak ellenőrzése, hogy az üres gyűjtemény.|  
|metszetének|Egy egyetlen tömb vagy objektum a szokványos elemeket a tömb, vagy átadott objektumok közötti adja vissza. Például, a függvény `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> A paraméterek, a függvény egy objektum vagy egy lemezkészlet tömbök (nem ezek keveréke) lehet. Ha két objektum ugyanazzal a névvel, a végső objektum megjelenik az utolsó objektum ezzel a névvel.<br /><br /> **Számú paraméter**: 1... *n*<br /><br /> **Név**: gyűjtemény *n*<br /><br /> **Leírás**: kötelező. A gyűjtemények kiértékelése. Az objektum összes gyűjtemény megjelenjen az eredményben továbbított kell lennie.|  
|a UNION|Egyetlen tömb vagy objektum, amely a tömb vagy objektum átadott az elemek ad vissza. Például adja vissza ezt a függvényt `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> A paraméterek, a függvény egy objektum vagy egy lemezkészlet tömbök (nem ezek keveréke) lehet. Ha ezzel a névvel, a végső kimenetet a két objektum, a végső objektum az utolsó objektum ezen a néven jelenik meg.<br /><br /> **Számú paraméter**: 1... *n*<br /><br /> **Név**: gyűjtemény *n*<br /><br /> **Leírás**: kötelező. A gyűjtemények kiértékelése. Olyan objektum, amely akkor jelenik meg, a gyűjtemények bármelyikében az eredmény jelenik meg.|  
|első|Az első elem a tömb vagy az átadott karakterlánc visszaadása. Például, a függvény `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: kötelező. A gyűjteményt, amelyben az első objektumot igénybe vehet.|  
|utolsó|Az utolsó elem a tömb vagy az átadott karakterlánc visszaadása. Például, a függvény `3`:<br /><br /> `last('0123')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: kötelező. A gyűjtemény utolsó dimenzióobjektumot érvénybe.|  
|hajtsa végre a megfelelő|Visszaadja az első **száma** az átadott tömb vagy karakterlánc elemeit, például a függvény `[1, 2]`:  `take([1, 2, 3, 4], 2)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: kötelező. A gyűjtemény első érvénybe **száma** a következő helyről objektumokat.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: száma<br /><br /> **Leírás**: kötelező. A történő objektumok száma a **gyűjtemény**. Pozitív egész számnak kell lennie.|  
|Kihagyása|A elemeket adja vissza, a tömb indextől kezdődő **száma**, például a függvény `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: kötelező. Kihagyja az első gyűjtemény **száma** a következő helyről objektumokat.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: száma<br /><br /> **Leírás**: kötelező. Előre hozása távolítsa el az objektumok száma **gyűjtemény**. Pozitív egész számnak kell lennie.|  
  
## <a name="logical-functions"></a>Logikai funkciók  
 Ezek a funkciók hasznos feltételek belül, bármilyen típusú logika kiértékeléséhez használható.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|egyenlő|Igaz értéket ad eredményül, ha két érték egyenlő. Például ha parameter1 PEL, az alábbi kifejezés alakítanák vissza `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: kötelező. Az objektum összehasonlítandó **objektum 2**.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: kötelező. Az objektum összehasonlítandó **objektum 1**.|  
|kevesebb|Igaz értéket ad eredményül, ha az első argumentum nem kisebb, mint a második. Vegye figyelembe, hogy értékek csak lehetnek típusa egész szám, lebegőpontos vagy karakterlánc. Például az alábbi kifejezés eredménye `true`:  `less(10,100)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: kötelező. Az objektum ellenőrizze, hogy van legalább **objektum 2**.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: kötelező. Annak ellenőrzése, hogy az nagyobb, mint az objektum **objektum 1**.|  
|lessOrEquals|Igaz értéket ad eredményül, ha az első argumentum értéke nagyobb, mint a második. Vegye figyelembe, hogy értékek csak lehetnek típusa egész szám, lebegőpontos vagy karakterlánc. Például az alábbi kifejezés eredménye `true`:  `lessOrEquals(10,10)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: kötelező. Annak ellenőrzése, hogy az kisebb vagy egyenlő objektum **objektum 2**.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: kötelező. Nagyobb vagy egyenlő esetén ellenőrizze az objektum **objektum 1**.|  
|nagyobb|Igaz értéket ad eredményül, ha az első argumentum értéke nagyobb, mint a második. Vegye figyelembe, hogy értékek csak lehetnek típusa egész szám, lebegőpontos vagy karakterlánc. Például az alábbi kifejezés eredménye `false`:  `greater(10,10)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: kötelező. Annak ellenőrzése, hogy az nagyobb, mint az objektum **objektum 2**.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: kötelező. Az objektum ellenőrizze, hogy van legalább **objektum 1**.|  
|greaterOrEquals|Igaz értéket ad eredményül, ha az első argumentum nagyobb vagy egyenlő a második. Vegye figyelembe, hogy értékek csak lehetnek típusa egész szám, lebegőpontos vagy karakterlánc. Például az alábbi kifejezés eredménye `false`:  `greaterOrEquals(10,100)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: kötelező. Nagyobb vagy egyenlő esetén ellenőrizze az objektum **objektum 2**.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: kötelező. Annak ellenőrzése, hogy az kisebb vagy egyenlő, mint az objektum **objektum 1**.|  
|és|Igaz értéket ad eredményül, ha két paramétert teljesül. A logikai mindkét argumentumot kell. A következő értéket ad vissza `false`:  `and(greater(1,10),equals(0,0))`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: logikai 1<br /><br /> **Leírás**: kötelező. Az első argumentum kell lennie a `true`.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: logikai 2<br /><br /> **Leírás**: kötelező. A második argumentum lehet `true`.|  
|vagy|Igaz értéket ad eredményül, ha a paraméterek bármelyike igaz. A logikai mindkét argumentumot kell. A következő értéket ad vissza `true`:  `or(greater(1,10),equals(0,0))`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: logikai 1<br /><br /> **Leírás**: kötelező. Az első argumentum, amelyek esetleg `true`.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: logikai 2<br /><br /> **Leírás**: kötelező. A második argumentum lehet `true`.|  
|nem|Igaz értéket ad eredményül, ha a paraméter `false`. A logikai mindkét argumentumot kell. A következő értéket ad vissza `true`:  `not(contains('200 Success','Fail'))`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: logikai<br /><br /> **Leírás**: IGAZ értéket ad vissza, ha a paraméter `false`. A logikai mindkét argumentumot kell. A következő értéket ad vissza `true`:  `not(contains('200 Success','Fail'))`|  
|Ha|Ha a megadott kifejezést kapott alapján egy megadott értéket adja vissza `true` vagy `false`.  Például a következő értéket ad vissza `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: kifejezés<br /><br /> **Leírás**: kötelező. Logikai érték, amely meghatározza, melyik értéket adja vissza a kifejezést.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: igaz<br /><br /> **Leírás**: kötelező. A visszatérési érték, ha a kifejezés `true`.<br /><br /> **Számú paraméter**: 3<br /><br /> **Név**: hamis<br /><br /> **Leírás**: kötelező. A visszatérési érték, ha a kifejezés `false`.|  
  
## <a name="conversion-functions"></a>Átalakítás funkciók  
 Ezek a függvények minden nyelven natív típusai közötti átváltásra használhatók:  
  
-   karakterlánc  
  
-   egész szám  
  
-   Lebegőpontos  
  
-   logikai  
  
-   Tömbök  
  
-   szótárak  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|int|A paraméter átalakítása egy egész számot. Például a következő kifejezést ad vissza, nem pedig egy karakterlánc, 100:  `int('100')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. Az érték, amely alakítja át egy egész számot.|  
|karakterlánc|A paraméter alakítható át karakterlánccá. Például az alábbi kifejezés eredménye `'10'`: `string(10)` is átválthat egy objektum egy karakterlánc, például ha a **PEL** paraméter egy tulajdonság az objektum `bar : baz`, akkor a következő lenne térjen vissza `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. Az érték, amely karakterlánccá alakítja át.|  
|JSON-ban|A paraméternek JSON típusú értékké konvertálni. String() ellentéte. Például az alábbi kifejezés eredménye `[1,2,3]` tömb, nem pedig egy karakterlánc:<br /><br /> `parse('[1,2,3]')`<br /><br /> Hasonlóképpen átválthat a karakterlánc objektum. Például `json('{"bar" : "baz"}')` adja vissza:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A karakterlánc, amely natív típusa értékre alakítja át.<br /><br /> A json-függvény xml bemenet is támogatja. Ha például a paraméter értékét:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> a következő JSON formátumúvá alakul:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|Lebegőpontos|A paraméter argumentum konvertálható lebegőpontos szám. Például az alábbi kifejezés eredménye `10.333`:  `float('10.333')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. Az érték, amely a lebegőpontos számként alakítja át.|  
|logikai érték|A paraméter átalakítása olyan logikai érték. Például az alábbi kifejezés eredménye `false`:  `bool(0)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. Az érték, amely egy logikai érték alakítja át.|  
|Egyesítés|Az argumentumként átadott az első nem null objektumot ad vissza. Megjegyzés: üres karakterlánc értéke nem null. Például ha az 1. és 2 paraméterek nincsenek megadva, ez visszaad `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Számú paraméter**: 1... *n*<br /><br /> **Név**: objektum*n*<br /><br /> **Leírás**: kötelező. Az objektumok kereséséhez `null`.|  
|a Base64|A bemeneti karakterlánc a base64 alakot adja vissza. Például az alábbi kifejezés eredménye `c29tZSBzdHJpbmc=`:  `base64('some string')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc-1<br /><br /> **Leírás**: kötelező. A karakterlánc helyére base64 kódolása.|  
|base64ToBinary|A base64 kódolású karakterlánc bináris alakot adja vissza. Például az alábbi kifejezés egy karakterlánc bináris alakot adja vissza: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A base64 kódolású karakterlánc.|  
|base64ToString|A based64 kódolású karakterlánc karakterlánc alakot adja vissza. Például az alábbi kifejezés egy karakterláncot ad vissza: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A base64 kódolású karakterlánc.|  
|Bináris|Egy bináris értéket képviselő alakot adja vissza.  Az alábbi kifejezés például néhány karakterlánc bináris alakot adja vissza: `binary(‘some string’).`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. Az érték, amely bináris alakítja át.|  
|dataUriToBinary|Egy adat-URI azonosító egy bináris alakot adja vissza. Az alábbi kifejezés például néhány karakterlánc bináris ábrázolását adja vissza: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. Az adatok átalakítása a bináris megjelenítése URI.|  
|dataUriToString|Egy adat-URI azonosító egy karakterlánc alakot adja vissza. Az alábbi kifejezés például egy karakterláncot ad vissza: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br />**Leírás**: kötelező. Az adatok átalakítása karakteres URI.|  
|dataUri|Egy adat-URI azonosító a értékét adja vissza. Például az alábbi kifejezés adatait jeleníti meg: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Számú paraméter**: 1<br /><br />**Név**: érték<br /><br />**Leírás**: kötelező. A érték konvertálása az adat-URI azonosító.|  
|decodeBase64|Egy bemeneti based64 karakterlánc karakterlánc alakot adja vissza. Például az alábbi kifejezés eredménye `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: egy bemeneti based64 karakterlánc karakterlánc alakot adja vissza.|  
|encodeUriComponent|URL-Kilépés az átadott karakterlánc. Például az alábbi kifejezés eredménye `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. Az URL nem biztonságos karakter átléptetése a karakterláncot.|  
|decodeUriComponent|UN-URL-Kilépés az átadott karakterlánc. Például az alábbi kifejezés eredménye `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A karakterlánc az URL nem biztonságos karaktereket dekódolására.|  
|decodeDataUri|Egy bemeneti adatokat URI karakterlánc bináris alakot adja vissza. Például a következő kifejezést ad vissza a bináris megjelenítése `some string`:  `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: kötelező. A bináris megjelenítése a dekódolás dataURI.|  
|uriComponent|Egy URI-t adja vissza egy értékének kódolva. Például az alábbi kifejezés eredménye `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Paraméter részletei: Szám: 1, Name: karakterlánc, Leírás: kötelező. A karakterlánc URI-kódolt.|  
|uriComponentToBinary|Visszaadja a bináris megjelenítése egy URI-kódolású karakterlánc. Például az alábbi kifejezés egy bináris alakot adja vissza `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: karakterlánc<br /><br />**Leírás**: kötelező. Az URI-kódolású karakterlánc.|  
|uriComponentToString|Visszaadja a URI karakterlánc-ábrázolása kódolású karakterlánc. Például az alábbi kifejezés eredménye `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Számú paraméter**: 1<br /><br />**Név**: karakterlánc<br /><br />**Leírás**: kötelező. Az URI-kódolású karakterlánc.|  
|xml|Térjen vissza az értéket XML-ábrázolása. Például az alábbi kifejezés egy XML-kódot eredményezzen tartalom által képviselt `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. Az xml-függvény JSON objektum bemeneti is támogatja. Például a paraméter `{ "abc": "xyz" }` alakítja át egy XML-tartalom `\<abc>xyz\</abc>`<br /><br /> **Számú paraméter**: 1<br /><br />**Név**: érték<br /><br />**Leírás**: kötelező. Az érték átalakítása XML.|  
|XPath|Az xpath-kifejezés az érték, amely az xpath kifejezés értéke megfelelő XML-csomópontnak tömbjét adja vissza.<br /><br />  **1. példa**<br /><br /> Tegyük fel, a "p1" paraméter értéke a következő XML-karakterlánc-ábrázolása:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Ezt a kódot: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> alakítanák vissza<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> mivel<br /><br /> 2. Ezt a kódot: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> alakítanák vissza<br /><br /> `13`<br /><br /> <br /><br /> **2. példa**<br /><br /> Adja meg a következő XML-tartalom:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Ezt a kódot: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> vagy<br /><br /> 2. Ezt a kódot: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> adja vissza<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> és<br /><br /> 3. Ezt a kódot: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> adja vissza<br /><br /> ``bar``<br /><br /> **Számú paraméter**: 1<br /><br />**Név**: Xml<br /><br />**Leírás**: kötelező. Az XML, amelyiken az XPath-kifejezés kiértékelése.<br /><br /> **Számú paraméter**: 2. régiója<br /><br />**Név**: XPath<br /><br />**Leírás**: kötelező. Az XPath-kifejezés kiértékelése.|  
|tömb|A paraméter átalakítása tömb.  Például az alábbi kifejezés eredménye `["abc"]`: `array('abc')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. Az érték, amely a tömb alakítja át.|
|createArray|A paraméter egy tömb jön létre.  Például az alábbi kifejezés eredménye `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Számú paraméter**: 1.. n<br /><br /> **Név**: bármely n<br /><br /> **Leírás**: kötelező. Az értékek egy tömb egységgé kombinálják.|

## <a name="math-functions"></a>Matematikai függvények  
 Ezeket a funkciókat használhatja bármelyik típusú számot: **egész számok** és **képest az előtérben**.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|hozzáadás|A két szám hozzáadása eredményét adja vissza. Például, a függvény `20.333`:  `add(10,10.333)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: Summand 1<br /><br /> **Leírás**: kötelező. A hozzáadni kívánt számot **Summand 2**.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: Summand 2<br /><br /> **Leírás**: kötelező. A hozzáadni kívánt számot **Summand 1**.|  
|Sub|A kivonás a két szám eredményét adja vissza. Például adja vissza ezt a funkciót: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: Minuend<br /><br /> **Leírás**: kötelező. A szám, amely **Subtrahend** törlődik.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: Subtrahend<br /><br /> **Leírás**: kötelező. Távolítsa el a számot a **Minuend**.|  
|MUL számú|A két szám a szorzás eredményét adja vissza. Például a következő értéket ad vissza `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: Multiplicand 1<br /><br /> **Leírás**: kötelező. A szám szorzóját **Multiplicand 2** együtt.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: Multiplicand 2<br /><br /> **Leírás**: kötelező. A szám szorzóját **Multiplicand 1** együtt.|  
|DIV|A két szám hányadosának eredményét adja vissza. Például a következő értéket ad vissza `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: osztandó<br /><br /> **Leírás**: kötelező. A szám nullával a **osztó**.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: osztó<br /><br /> **Leírás**: kötelező. A szám felosztása a **osztandó** által.|  
|MOD|A két szám (modulo) osztás után a fennmaradó eredményét adja vissza. Például az alábbi kifejezés eredménye `2`:<br /><br /> `mod(10,4)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: osztandó<br /><br /> **Leírás**: kötelező. A szám nullával a **osztó**.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: osztó<br /><br /> **Leírás**: kötelező. A szám felosztása a **osztandó** által. A felosztás után ezeket a többi használatban van.|  
|perc|Nincsenek a két különböző mintát, ez a függvény hívásakor: `min([0,1,2])` itt min tömb lehet. A kifejezés eredménye `0`. Azt is megteheti, ez a funkció is igénybe vehet az értékek vesszővel tagolt listáját: `min(0,1,2)` ezt a funkciót is 0 értéket adja vissza. Vegye figyelembe, minden értékek csak számokat, ha a paraméter egy tömb van való csak számokat.<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjtemény vagy érték<br /><br /> **Leírás**: kötelező. Ez lehet egy tömböt találja a minimális értéknél, vagy egy első értékét.<br /><br /> **Számú paraméter**: 2... *n*<br /><br /> **Név**: érték *n*<br /><br /> **Leírás**: nem kötelező. Ha az első paraméter értéke, majd lehet további értéket átadni, és a minimális átadott értékek ad vissza.|  
|max.|Nincsenek két különböző mintát, ez a függvény hívásakor:  `max([0,1,2])`<br /><br /> Itt maximális tömb lehet. A kifejezés eredménye `2`. Azt is megteheti, ez a funkció is igénybe vehet az értékek vesszővel tagolt listáját: `max(0,1,2)` Ez a függvény a 2 értéket adja vissza. Vegye figyelembe, minden értékek csak számokat, ha a paraméter egy tömb van való csak számokat.<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: gyűjtemény vagy érték<br /><br /> **Leírás**: kötelező. A maximális érték vagy egy első érték található egy tömböt lehetnek.<br /><br /> **Számú paraméter**: 2... *n*<br /><br /> **Név**: érték *n*<br /><br /> **Leírás**: nem kötelező. Ha az első paraméter értéke, majd lehet további értéket átadni, és a maximális átadott értékek ad vissza.|  
|tartomány| Létrehoz egy adott értéket egész számok tömbje és a visszaadott tömb hosszának megadása. Például, a függvény `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: kezdőindex<br /><br /> **Leírás**: kötelező. A tömb első egész.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: száma<br /><br /> **Leírás**: kötelező. Egész számok, amelyek a tömb száma.|  
|VÉL| Létrehoz egy véletlenszerű egész (mindkét vége között lehet a megadott tartományban. Ez visszaadhatja például `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: minimális<br /><br /> **Leírás**: kötelező. A legkisebb egész szám visszatérő.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: maximális<br /><br /> **Leírás**: kötelező. A legnagyobb egész számot visszatérő.|  
  
## <a name="date-functions"></a>Dátum-funkciók  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|utcnow|Az aktuális időbélyeg karakterláncként adja vissza. Például `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|masodpercekHozzaadasa|Az átadott karakterlánc időbélyeg ad hozzá egy ennyi ideig. Azon másodpercek számát pozitív vagy negatív is lehet. Az eredmény: ISO 8601 formátumban ("no") alapértelmezés szerint karakterlánc, kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Az idő tartalmazó karakterlánc.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: másodperc<br /><br /> **Leírás**: kötelező. Az időtartamot másodpercben, adja hozzá. Negatív kivonandó időnél másodperc lehet.<br /><br /> **Számú paraméter**: 3<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|addminutes|Az átadott karakterlánc időbélyeg ad hozzá egy egész számot hány perc. A percet lehet pozitív vagy negatív. Az eredmény: ISO 8601 formátumban ("no") alapértelmezés szerint karakterlánc, kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Az idő tartalmazó karakterlánc.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: perc<br /><br /> **Leírás**: kötelező. A hozzáadandó percek számát. Negatív kivonandó időnél perc lehet.<br /><br /> **Számú paraméter**: 3<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|addhours|Az átadott karakterlánc időbélyeg ad hozzá az órát egész szám. Az órát lehet pozitív vagy negatív. Az eredmény: ISO 8601 formátumban ("no") alapértelmezés szerint karakterlánc, kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Az idő tartalmazó karakterlánc.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: üzemideje (óra)<br /><br /> **Leírás**: kötelező. A hozzáadandó órák száma. Negatív kivonandó időnél óra lehet.<br /><br /> **Számú paraméter**: 3<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|napokHozzaadasa|Az átadott karakterlánc időbélyeg ad hozzá egy nap egész szám. Hány napig lehet pozitív vagy negatív. Az eredmény: ISO 8601 formátumban ("no") alapértelmezés szerint karakterlánc, kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: kötelező. Az idő tartalmazó karakterlánc.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: napok<br /><br /> **Leírás**: kötelező. A hozzáadni kívánt napok száma. Negatív kivonandó időnél nap lehet.<br /><br /> **Számú paraméter**: 3<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  
|formatDateTime|Dátum formátumú karakterláncot ad vissza. Az eredmény: ISO 8601 formátumban ("no") alapértelmezés szerint karakterlánc, kivéve, ha a formátummegadóval való ábrázoláshoz valósul meg. Például `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Számú paraméter**: 1<br /><br /> **Név**: dátuma<br /><br /> **Leírás**: kötelező. A dátum tartalmazó karakterlánc.<br /><br /> **Számú paraméter**: 2. régiója<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátum megadása](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formátum mintát](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogy az időbélyegző értékének formázásának módja. Ha formátum nem áll rendelkezésre, az ISO 8601 formátumban ("no") használatos.|  

## <a name="next-steps"></a>További lépések
A rendszer változók kifejezésekben használható listájáért lásd: [rendszerváltozók](control-flow-system-variables.md).
