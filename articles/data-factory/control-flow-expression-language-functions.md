---
title: Kifejezés, és a functions az Azure Data Factoryban |} A Microsoft Docs
description: Ez a cikk a kifejezések és függvények, használhatja a data factory-entitások létrehozásával kapcsolatos információkat nyújt.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 5cdaba2a280221fa5fa9274ebfa6cafa18e7690c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055015"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Kifejezések és függvények az Azure Data Factoryban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-functions-variables.md)
> * [Aktuális verzió](control-flow-expression-language-functions.md)

Ez a cikk részletesen kifejezések és függvények az Azure Data Factory által támogatott. 

## <a name="introduction"></a>Bevezetés
JSON-értékeit a definícióban szövegkonstans vagy a futásidőben kiértékelt kifejezés is lehet. Példa:  
  
```json
"name": "value"
```

 (vagy)  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Kifejezések  
Kifejezések is bárhol megjelenhet egy JSON-karakterlánc értéken, és mindig egy másik JSON-értéket eredményez. Ha a JSON értéke egy kifejezés, a szervezet a kifejezés a következő bejelentkezés eltávolításával kivonjuk (\@). Ha egy konstans sztring van szükség, amely elindítja a \@, kell lennie karakterrel való átléptetése \@ \@. Az alábbi példák bemutatják, hogyan értékeli ki a kifejezéseket.  
  
|JSON-értéket|Eredmény|  
|----------------|------------|  
|"paramétereket"|A következő karaktereket: "parameters" adja vissza.|  
|"[1] Paraméterek"|A következő karaktereket: "[1] parameters" adja vissza.|  
|"\@\@"|Egy 1 karaktert tartalmazó karakterlánc "\@" adja vissza.|  
|" \@"|Egy 2 karaktert tartalmazó karakterlánc " \@" adja vissza.|  
  
 Kifejezések is megjelenhet belüli karakterláncokat, nevű szolgáltatás segítségével *karakterlánc-interpolációs* kifejezések burkolja ahol `@{ ... }`. Például:`"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Karakterlánc-interpolációs használja, az eredmény, mindig egy karakterláncot. Tegyük fel, hogy meg van adva `myNumber` , `42` és `myString` , `foo`:  
  
|JSON-értéket|Eredmény|  
|----------------|------------|  
|"\@folyamat.parameters.myString ()"| Értéket ad vissza `foo` karakterláncként.|  
|"\@{folyamat ().parameters.myString}"| Értéket ad vissza `foo` karakterláncként.|  
|"\@folyamat.parameters.myNumber ()"| Értéket ad vissza `42` , egy *szám*.|  
|"\@{folyamat ().parameters.myNumber}"| Értéket ad vissza `42` , egy *karakterlánc*.|  
|"Válasz: @{folyamat ().parameters.myNumber}"| A karakterláncot ad vissza, `Answer is: 42`.|  
|"\@concat (" válasz: ", string(pipeline().parameters.myNumber))"| A karakterláncot ad vissza `Answer is: 42`|  
|"Válasz: \@ \@{folyamat ().parameters.myNumber}"| A karakterláncot ad vissza, `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Példák

#### <a name="a-dataset-with-a-parameter"></a>Egy adatkészlet-paraméter
A következő példában a BlobDataset nevű paraméter szükséges **elérési út**. Az érték beállítható egy érték szolgál a **folderPath** tulajdonság a következő kifejezés használatával: `@{dataset().path}`. 

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

#### <a name="a-pipeline-with-a-parameter"></a>Egy folyamat egy paraméterrel
A következő példában a folyamathoz szükséges **inputPath** és **outputPath** paramétereket. A **elérési út** a paraméteres BLOB adatkészlet van beállítva, ezek a paraméterek használatával. Az itt használt szintaxis: `pipeline().parameters.parametername`. 

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
 Kifejezések belül függvények hívása. Az alábbi szakaszok ismertetik a funkciók használható egy kifejezésben.  

## <a name="string-functions"></a>Sztringfüggvények  
 A következő funkciók csak a karakterláncok vonatkoznak. Számos, a gyűjtemény a karakterláncok is használható.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|Concat|Tetszőleges számú karakterlánc együtt egyesíti. Például, ha a paraméter1 értéke `foo,` meghaladná a következő kifejezés `somevalue-foo-somevalue`:  `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: karakterlánc *n*<br /><br /> **Leírás**: megadása kötelező. Az egyetlen karakterláncban egyesítendő karakterláncok.|  
|karakterláncrészlet|Egy karakterek részhalmazát adja vissza egy karakterláncból. Ha például a következő kifejezést:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> adja vissza:<br /><br /> `foo`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A karakterlánc, amelyből a karakterláncrész származik.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Start index<br /><br /> **Leírás**: megadása kötelező. Az index a karakterláncrész az 1. paraméterben.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: hossza<br /><br /> **Leírás**: megadása kötelező. A karakterláncrész hossza.|  
|cserélje le|Karakterlánc lecserélése egy adott karakterlánccal. Ha például a kifejezést:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> adja vissza:<br /><br /> `the new string`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező.  Ha 2. paraméter található a karakterlánc, amely a 2. paraméter keres, és 3. paraméterrel frissít 1, paraméterben.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: régi karakterlánc<br /><br /> **Leírás**: megadása kötelező. A karakterláncot cserélje le a 3. paraméterrel, ha egyezést talál az 1. paraméterben<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: új karakterlánc<br /><br /> **Leírás**: megadása kötelező. A karakterlánc, amellyel cserélje le a karakterláncot a 2. paramétert, ha egyezést talál az 1. paraméterben.|  
|GUID azonosítója| Globálisan egyedi karakterlánc (más néven. GUID azonosítója). Ha például sikerült létrehozni a következő kimenet `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: formátuma<br /><br /> **Leírás**: nem kötelező. Egyetlen formátummegadó, amely azt jelzi, hogy [megtudhatja, hogyan formázhatja az érték a GUID azonosító](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). A formátum paraméter "N", "D", "B", "P" vagy "X" lehet. Ha nincs megadva formátum, a "D" szolgál.|  
|toLower|Egy karakterlánc kisbetűvé alakítja. Ha például a következő értéket ad vissza `two by two is four`:  `toLower('Two by Two is Four')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A kis-és alacsonyabb átalakítandó karakterláncot. Ha egy karaktert a karakterlánc nincs kisbetűs megfelelője, része a visszaadott karakterláncban változatlan.|  
|toUpper|Egy karakterlánc nagybetűssé alakít át. Ha például a következő kifejezést ad vissza `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A felső kis-és átalakítandó karakterláncot. Egy karaktert a karakterlánc nem rendelkezik egy nagybetűket egyenértékűként, ha szerepel a visszaadott karakterláncban változatlan marad.|  
|indexof|Egy karakterlánc eset belüli értéket az indexét keressük a insensitively. Ha például a következő kifejezést ad vissza `7`: `indexof('hello, world.', 'world')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A karakterlánc, amely az értéket tartalmazhatja.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A Keresés az indexben, az érték.|  
|lastindexof|Egy karakterlánc eset belül érték utolsó indexét insensitively találja. Ha például a következő kifejezést ad vissza `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A karakterlánc, amely az értéket tartalmazhatja.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A Keresés az indexben, az érték.|  
|startswith|Ellenőrzi, hogy a karakterlánc kezdődik érték eset insensitively. Ha például a következő kifejezést ad vissza `true`: `startswith('hello, world', 'hello')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A karakterlánc, amely az értéket tartalmazhatja.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. Az érték a karakterlánc kezdődhet.|  
|endswith|Ellenőrzi, hogy ha a karakterlánc végződik érték eset insensitively. Ha például a következő kifejezést ad vissza `true`: `endswith('hello, world', 'world')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A karakterlánc, amely az értéket tartalmazhatja.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. Az érték a karakterlánc végződhet.|  
|felosztás|Az elválasztó karakterlánc kettéosztása. Ha például a következő kifejezést ad vissza `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A szétvágandó karakterlánc.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. Az elválasztó.|  
  
  
## <a name="collection-functions"></a>Gyűjtemény-funkciók  
 Ezek a függvények hatnak a gyűjteményeket, például a tárolótömbök, karakterláncok és néha szótárak.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|tartalmazza a következőt:|Igaz értéket ad eredményül, ha a dictionary tartalmazza a legfontosabb, értéket tartalmaz, vagy karakterlánc részkarakterláncot tartalmaz. Ha például a következő kifejezést ad vissza `true:``contains('abacaba','aca')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjteményen belül<br /><br /> **Leírás**: megadása kötelező. A gyűjtemény, amelyben keresni kíván.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: objektum keresése<br /><br /> **Leírás**: megadása kötelező. A keresendő objektum a **gyűjteményen belül**.|  
|Hossza|Egy tömb vagy karakterlánc elemeinek számát adja vissza. Ha például a következő kifejezést ad vissza `3`:  `length('abc')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: megadása kötelező. A gyűjtemény hosszának beolvasása.|  
|üres|Igaz értéket ad eredményül, ha az objektum, tömb vagy karakterlánc üres. Ha például a következő kifejezést ad vissza `true`:<br /><br /> `empty('')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: megadása kötelező. Ellenőrizze, hogy üres gyűjtemény.|  
|Metszet|Egyetlen olyan tömböt vagy objektumot a közös elemeit adja vissza a tömbök vagy objektumok át. Például ez a függvény visszaad `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> A függvény paramétereit egy objektumhalmazban vagy egy tömbökből álló halmazban (nem egy objektumhalmazban) lehet. Ha két objektum ugyanazzal a névvel, a végső objektumban az utolsó adott nevű objektum jelenik meg.<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: gyűjtemény *n*<br /><br /> **Leírás**: megadása kötelező. A kiértékelendő gyűjtemények. Az objektum összes gyűjtemény megjelenjen az eredményben az átadott kell lennie.|  
|Union|Egyetlen olyan tömböt vagy objektumot az elemek tömbök vagy objektumok bármelyikében szereplő átadott azt adja vissza. Például ez a függvény visszaad `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> A függvény paramétereit egy objektumhalmazban vagy egy tömbökből álló halmazban (nem egy objektumhalmazban) lehet. Ha két objektum a végső kimenet ugyanazzal a névvel, a végső objektumban az utolsó adott nevű objektum jelenik meg.<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: gyűjtemény *n*<br /><br /> **Leírás**: megadása kötelező. A kiértékelendő gyűjtemények. Olyan objektum, amely a gyűjtemények bármelyikében megjelenik az eredmény jelenik meg.|  
|első|Az átadott tömb vagy karakterlánc az első elemét adja vissza. Például ez a függvény visszaad `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: megadása kötelező. A gyűjtemény, az első objektumot.|  
|utolsó|Az átadott tömb vagy karakterlánc az utolsó elemét adja vissza. Például ez a függvény visszaad `3`:<br /><br /> `last('0123')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: megadása kötelező. A gyűjtemény, az utolsó objektumot.|  
|hajtsa végre a megfelelő|Visszaadja az első **száma** az átadott tömb vagy karakterlánc elemeit, például ez a függvény visszaad `[1, 2]`:  `take([1, 2, 3, 4], 2)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: megadása kötelező. A gyűjtemény, az első **száma** objektumait ki.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: száma<br /><br /> **Leírás**: megadása kötelező. A beolvasandó objektumok száma a **gyűjtemény**. Pozitív egész számnak kell lennie.|  
|Kihagyás|Elemeit adja vissza, a tömb indexpozíciónál kezdődő **száma**, például ez a függvény visszaad `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény<br /><br /> **Leírás**: megadása kötelező. A gyűjtemény, amelynek az első **száma** objektumait ki.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: száma<br /><br /> **Leírás**: megadása kötelező. Elejéről eltávolítandó objektumok száma **gyűjtemény**. Pozitív egész számnak kell lennie.|  
  
## <a name="logical-functions"></a>Logikai függvények  
 Ezek a függvények feltételek belül lehetnek hasznosak, bármilyen típusú logikai kiértékeléséhez használható.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|egyenlő|Igaz értéket ad eredményül, ha két érték egyenlő. Például ha parameter1 foo, a következő kifejezés termékazonosítóhoz `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: megadása kötelező. Az objektumhoz hasonlítandó **objektum 2**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: megadása kötelező. Az objektumhoz hasonlítandó **objektum 1**.|  
|kevesebb|Igaz értéket ad eredményül, ha az első argumentum kisebb a másodiknál. Vegye figyelembe, hogy értékeket csak egész szám típusú, lebegőpontos vagy karakterlánc lehet. Ha például a következő kifejezést ad vissza `true`:  `less(10,100)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: megadása kötelező. Az objektum, ellenőrizze, hogy kevesebb mint **objektum 2**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: megadása kötelező. Ellenőrizze, hogy nagyobb, mint az objektum **objektum 1**.|  
|lessOrEquals|Igaz értéket ad eredményül, ha az első argumentum értéke kisebb vagy egyenlő a másodiknál. Vegye figyelembe, hogy értékeket csak egész szám típusú, lebegőpontos vagy karakterlánc lehet. Ha például a következő kifejezést ad vissza `true`:  `lessOrEquals(10,10)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: megadása kötelező. Az objektum, ellenőrizze, hogy kisebb vagy egyenlő **objektum 2**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: megadása kötelező. Az objektum, ellenőrizze, hogy nagyobb vagy egyenlő **objektum 1**.|  
|nagyobb|Igaz értéket ad eredményül, ha az első argumentum értéke nagyobb a másodiknál. Vegye figyelembe, hogy értékeket csak egész szám típusú, lebegőpontos vagy karakterlánc lehet. Ha például a következő kifejezést ad vissza `false`:  `greater(10,10)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: megadása kötelező. Ellenőrizze, hogy nagyobb, mint az objektum **objektum 2**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: megadása kötelező. Az objektum, ellenőrizze, hogy kevesebb mint **objektum 1**.|  
|greaterOrEquals|Igaz értéket ad eredményül, ha az első argumentum kisebb a másodiknál. Vegye figyelembe, hogy értékeket csak egész szám típusú, lebegőpontos vagy karakterlánc lehet. Ha például a következő kifejezést ad vissza `false`:  `greaterOrEquals(10,100)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1 objektum<br /><br /> **Leírás**: megadása kötelező. Az objektum, ellenőrizze, hogy nagyobb vagy egyenlő **objektum 2**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2 objektum<br /><br /> **Leírás**: megadása kötelező. Az objektum, ellenőrizze, hogy kisebb vagy egyenlő **objektum 1**.|  
|és|Igaz értéket ad eredményül, ha mindkét paraméter igaz. Mindkét argumentumot kell lennie a logikai értékek. A következő értéket ad vissza `false`:  `and(greater(1,10),equals(0,0))`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: logikai 1<br /><br /> **Leírás**: megadása kötelező. Az első argumentum, amelyet be kell `true`.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: logikai 2<br /><br /> **Leírás**: megadása kötelező. A második argumentum kell `true`.|  
|vagy|Igaz értéket ad eredményül, ha a paraméterek bármelyike igaz. Mindkét argumentumot kell lennie a logikai értékek. A következő értéket ad vissza `true`:  `or(greater(1,10),equals(0,0))`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: logikai 1<br /><br /> **Leírás**: megadása kötelező. Az első argumentum, amely lehet `true`.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: logikai 2<br /><br /> **Leírás**: megadása kötelező. A második argumentum értéke lehet `true`.|  
|nem|Igaz értéket ad eredményül, ha a paraméter `false`. Mindkét argumentumot kell lennie a logikai értékek. A következő értéket ad vissza `true`:  `not(contains('200 Success','Fail'))`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: logikai<br /><br /> **Leírás**: IGAZ értéket ad vissza, ha a paraméter `false`. Mindkét argumentumot kell lennie a logikai értékek. A következő értéket ad vissza `true`:  `not(contains('200 Success','Fail'))`|  
|Ha|Ha a megadott kifejezést kapott alapján egy megadott értéket adja vissza `true` vagy `false`.  Ha például a következő értéket ad vissza `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: kifejezés<br /><br /> **Leírás**: megadása kötelező. A kifejezés egy logikai érték, amely meghatározza, hogy melyik értéket adja vissza.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: igaz<br /><br /> **Leírás**: megadása kötelező. A visszaadandó érték, ha a kifejezés `true`.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: False (hamis)<br /><br /> **Leírás**: megadása kötelező. A visszaadandó érték, ha a kifejezés `false`.|  
  
## <a name="conversion-functions"></a>Konverziós függvények  
 Ezek a függvények használhatók minden nyelven natív típusok között:  
  
-   sztring  
  
-   egész szám  
  
-   lebegőpontos  
  
-   logikai  
  
-   tömbök  
  
-   a szótárak  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|int|A paraméter átalakítása egész szám. A következő kifejezést például 100, egy szám, nem pedig egy karakterláncot ad vissza:  `int('100')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: megadása kötelező. Az egész alakítandó érték.|  
|sztring|A paraméter átalakítása egy karakterláncot. Például a következő kifejezést ad vissza `'10'`: `string(10)` is átválthat egy objektum egy karakterláncot, például ha a **foo** paraméter egy tulajdonságot tartalmazó objektumot `bar : baz`, akkor a következő lenne a visszaadandó `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: megadása kötelező. Az érték, amely egy karakterlánccá.|  
|JSON|A paraméter átalakítása JSON típusú értékké. Fontos string() ellentéte. Ha például a következő kifejezést ad vissza `[1,2,3]` karakterlánc helyett egy tömb:<br /><br /> `json('[1,2,3]')`<br /><br /> Hasonlóképpen átválthat egy karakterláncot egy objektumot. Ha például `json('{"bar" : "baz"}')` adja vissza:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A natív típusú értékké alakítandó karakterlánc.<br /><br /> A json-függvény, valamint a bemeneti xml támogatja. Ha például a paraméter értékét:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> a következő json alakítja át:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|lebegőpontos|A paraméterargumentum átalakítása lebegőpontos számmá. Ha például a következő kifejezést ad vissza `10.333`:  `float('10.333')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: megadása kötelező. A lebegőpontos számmá alakítandó érték.|  
|Logikai|A paraméter átalakítása logikai érték beolvasása. Ha például a következő kifejezést ad vissza `false`:  `bool(0)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: megadása kötelező. Egy logikai alakítandó érték.|  
|Coalesce|Az átadott argumentumok az első nem nullértékű objektumát adja vissza. Megjegyzés: az üres karakterláncot nem null. Például, ha 1. és 2 paraméter nincs meghatározva, ez visszaadja `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: objektum*n*<br /><br /> **Leírás**: megadása kötelező. Az objektumok keresése `null`.|  
|Base64|A bemeneti karakterláncot a base64 kódolású karakterláncként adja vissza. Ha például a következő kifejezést ad vissza `c29tZSBzdHJpbmc=`:  `base64('some string')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc-1<br /><br /> **Leírás**: megadása kötelező. A base64-ábrázolással kódolandó karakterlánc.|  
|base64ToBinary|A base64-kódolású karakterláncot bináris kódolású karakterláncként adja vissza. Ha például a következő kifejezést adja vissza néhány karakterlánc bináris megfelelőjét: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A base64-kódolású karakterlánc.|  
|base64ToString|Based64 kódolt karakterláncot karakterláncként ad vissza. Például az alábbi kifejezés egy karakterláncot ad vissza: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A base64-kódolású karakterlánc.|  
|Bináris|Egy értéket bináris kódolású karakterláncként adja vissza.  Például az alábbi kifejezés egy bizonyos karakterlánc bináris megfelelőjét adja vissza: `binary(‘some string’).`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: megadása kötelező. A bináris alakítandó érték.|  
|dataUriToBinary|Adat-URI bináris kódolású karakterláncként adja vissza. A következő kifejezést például bizonyos karakterlánc bináris megfelelőjét adja vissza: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. Az adat-URI bináris ábrázolásra átalakítandó.|  
|dataUriToString|Adat-URI karakterláncként való visszaadása. A következő kifejezést például néhány karakterláncot ad vissza: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br />**Leírás**: megadása kötelező. Az adat-URI karakterlánc-ábrázolásra átalakítandó.|  
|dataUri|Adat-URI értéket adja vissza. Például a következő kifejezést ad vissza adatokat: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: érték<br /><br />**Leírás**: megadása kötelező. Az érték alakítandó adat URI-t.|  
|decodeBase64|Egy bemeneti based64 karakterláncot karakterláncként ad vissza. Ha például a következő kifejezést ad vissza `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: egy bemeneti based64 karakterláncot karakterláncként ad vissza.|  
|encodeUriComponent|URL-cím-Kilépés az átadott karakterlánc. Ha például a következő kifejezést ad vissza `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A karakterlánc URL-címekben nem biztonságos karaktereit karaktert.|  
|decodeUriComponent|Megszünteti-URL-cím-Kilépés az átadott karakterlánc. Ha például a következő kifejezést ad vissza `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. Az URL-címekben nem biztonságos karaktereit dekódolandó karakterláncot.|  
|decodeDataUri|A bemeneti adatok URI karakterlánc bináris megfelelőjét adja vissza. Ha például a következő kifejezést a bináris kódolású karakterláncként adja vissza `some string`:  `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br /> **Leírás**: megadása kötelező. A bináris értékként való dekódolandó dataURI.|  
|uriComponent|Visszaadása egy URI-t egy értékének kódolva. Ha például a következő kifejezést ad vissza `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Paraméterekkel kapcsolatos részletek: Szám: 1, Name: karakterlánc, Leírás: megadása kötelező. A kell URI-ként kódolandó karakterlánc.|  
|uriComponentToBinary|Adja vissza bináris kódolású karakterláncként egy URI-ként kódolt karakterlánc. Például a következő kifejezést adja vissza bináris értékként `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: karakterlánc<br /><br />**Leírás**: megadása kötelező. Az URI-ként kódolt karakterlánc.|  
|uriComponentToString|Adja vissza karakterláncként egy URI-ként kódolt karakterlánc. Ha például a következő kifejezést ad vissza `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: karakterlánc<br /><br />**Leírás**: megadása kötelező. Az URI-ként kódolt karakterlánc.|  
|xml|Az érték egy XML-ábrázolását adja vissza. Ha például a következő kifejezést adja vissza által jelölt tartalom xml `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. Az xml-függvény JSON objektum bemeneti is támogatja. Ha például a paraméter `{ "abc": "xyz" }` alakítja át egy xml-tartalom `\<abc>xyz\</abc>`<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: érték<br /><br />**Leírás**: megadása kötelező. XML átalakítása értéke.|  
|XPath|Az xpath-kifejezés, amely az xpath-kifejezés kiértékelése érték megfelelő xml-csomópontnak tömbjét adja vissza.<br /><br />  **1. példa**<br /><br /> Tegyük fel, a p1"paraméter értéke a következő XML formátumú karakterláncként:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Ez a kód: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> adna vissza<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> mivel a<br /><br /> 2. Ez a kód: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> adna vissza<br /><br /> `13`<br /><br /> <br /><br /> **2. példa**<br /><br /> Adja meg a következő XML-tartalom:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Ez a kód: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> vagy<br /><br /> 2. Ez a kód: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> adja vissza<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> és<br /><br /> 3. Ez a kód: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> adja vissza<br /><br /> ``bar``<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: Xml<br /><br />**Leírás**: megadása kötelező. Az XML, amelyen az XPath-kifejezés kiértékelése.<br /><br /> **Paraméter száma**: 2<br /><br />**Név**: XPath<br /><br />**Leírás**: megadása kötelező. A kiértékelendő XPath kifejezés.|  
|tömb|A paraméter átalakítása egy tömb.  Ha például a következő kifejezést ad vissza `["abc"]`: `array('abc')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: megadása kötelező. A tömb alakítandó érték.|
|createArray|Létrehoz egy tömböt a paraméterek közül.  Ha például a következő kifejezést ad vissza `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Paraméter száma**: 1.. n<br /><br /> **Név**: bármely n<br /><br /> **Leírás**: megadása kötelező. A tömbben egyesítendő értékek.|

## <a name="math-functions"></a>Matematikai függvények  
 Ezekkel a függvényekkel használható mindkét típusú számot: **egész számok** és **lebegő**.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|hozzáadás|A két szám hozzáadásának eredményét adja vissza. Például ez a függvény visszaad `20.333`:  `add(10,10.333)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: pedig 1<br /><br /> **Leírás**: megadása kötelező. A hozzáadandó szám **pedig 2**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. Összeadandóhoz<br /><br /> **Leírás**: megadása kötelező. A hozzáadandó szám **pedig 1**.|  
|Sub|Az a két szám kivonásának eredménye eredményét adja vissza. Például ez a függvény visszaad: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Kisebbítendőt<br /><br /> **Leírás**: megadása kötelező. A szám, amely **Kivonandót** törlődik.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Kivonandót<br /><br /> **Leírás**: megadása kötelező. A szám, távolítsa el a **Kisebbítendőt**.|  
|MUL számú|A két szám a szorzás eredményét adja vissza. Ha például a következő értéket ad vissza `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. Szorzandót<br /><br /> **Leírás**: megadása kötelező. A szám szorzása **Szorzandót 2** együtt.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. Szorzandót<br /><br /> **Leírás**: megadása kötelező. A szám szorzása **Szorzandót 1** együtt.|  
|DIV|A két szám hányadosának eredményét adja vissza. Ha például a következő értéket ad vissza `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: osztandó<br /><br /> **Leírás**: megadása kötelező. A szám, mellyel osztani a **osztó**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: osztó<br /><br /> **Leírás**: megadása kötelező. Az osztandó szám a **osztandó** szerint.|  
|MOD|A két szám (modulo) osztás után a fennmaradó eredményét adja vissza. Ha például a következő kifejezést ad vissza `2`:<br /><br /> `mod(10,4)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: osztandó<br /><br /> **Leírás**: megadása kötelező. A szám, mellyel osztani a **osztó**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: osztó<br /><br /> **Leírás**: megadása kötelező. Az osztandó szám a **osztandó** szerint. Az osztás után a maradékot.|  
|perc|Nincsenek a két különböző minták, ez a függvény hívásakor: `min([0,1,2])` itt min egy diagnosztikakonfigurációs tömböt. Ez a kifejezés eredménye `0`. Másik lehetőségként, ez a funkció is igénybe vehet értékek egy vesszővel tagolt listája: `min(0,1,2)` ezt a funkciót is 0 értéket adja vissza. Vegye figyelembe, hogy minden érték számok, kell lennie, ezért ha a paraméter egy tömb, a számokat tartalmazhat.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény vagy érték<br /><br /> **Leírás**: megadása kötelező. Ez lehet egy olyan értéktömböt kell keresni a minimális érték, vagy a egy halmaz első értéke.<br /><br /> **Paraméter száma**: 2... *n*<br /><br /> **Név**: érték *n*<br /><br /> **Leírás**: nem kötelező. Ha az első paraméterként egy értéket, majd további értékeket adhat át, és a minimális átadott értékek adja vissza.|  
|max.|Van két különböző minták, ez a függvény hívásakor:  `max([0,1,2])`<br /><br /> Itt maximális száma egy diagnosztikakonfigurációs tömböt. Ez a kifejezés eredménye `2`. Másik lehetőségként, ez a funkció is igénybe vehet értékek egy vesszővel tagolt listája: `max(0,1,2)` Ez a függvény a 2 értéket adja vissza. Vegye figyelembe, hogy minden érték számok, kell lennie, ezért ha a paraméter egy tömb, a számokat tartalmazhat.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény vagy érték<br /><br /> **Leírás**: megadása kötelező. Ez lehet egy olyan értéktömböt kell keresni a maximális érték, vagy a egy halmaz első értéke.<br /><br /> **Paraméter száma**: 2... *n*<br /><br /> **Név**: érték *n*<br /><br /> **Leírás**: nem kötelező. Ha az első paraméterként egy értéket, majd további értékeket adhat át, és a maximális átadott értékek adja vissza.|  
|Címtartomány| Létrehoz egy meghatározott számmal, egész számok tömbje és a visszaadott tömb hosszának megadása. Például ez a függvény visszaad `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Start index<br /><br /> **Leírás**: megadása kötelező. Legyen a tömbben található első egész szám.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: száma<br /><br /> **Leírás**: megadása kötelező. A tömbben található egész számok száma.|  
|rand| Létrehoz egy véletlenszerű egész szám (a két végpontot is beleértve a megadott tartományban. Ez visszaadhatja például `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Minimum<br /><br /> **Leírás**: megadása kötelező. A visszaadható legkisebb egész.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: maximális<br /><br /> **Leírás**: megadása kötelező. A visszaadható legnagyobb egész.|  
  
## <a name="date-functions"></a>Dátumfüggvények  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|utcnow|Az aktuális timestamp karakterláncként adja vissza. Például `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: formátuma<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátummegadó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni Formátumminta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogyan formázhatja az időbélyegző értékét. Ha nincs megadva formátum, az ISO 8601 formátumot ("o") használja.|  
|masodpercekHozzaadasa|Egy egész számmal megadott hozzáadása az átadott karakterlánc-időbélyeghez. Másodpercek száma pozitív vagy negatív is lehet. Ez egy karakterlánc, az ISO 8601 formátumot ("o") alapértelmezés szerint, ha egy formátummegadó biztosítja. Például `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: megadása kötelező. Az időt tartalmazó karakterlánc.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: másodperc<br /><br /> **Leírás**: megadása kötelező. A hozzáadandó másodpercek száma. Előfordulhat, hogy lehet negatív másodpercek kivonása céljából.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: formátuma<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátummegadó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni Formátumminta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogyan formázhatja az időbélyegző értékét. Ha nincs megadva formátum, az ISO 8601 formátumot ("o") használja.|  
|addminutes|Egy egész számmal megadott percszám hozzáadása az átadott karakterlánc-időbélyeghez. Percek száma pozitív vagy negatív is lehet. Ez egy karakterlánc, az ISO 8601 formátumot ("o") alapértelmezés szerint, ha egy formátummegadó biztosítja. Ha például `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: megadása kötelező. Az időt tartalmazó karakterlánc.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: perc alatt<br /><br /> **Leírás**: megadása kötelező. A hozzáadandó percek számát. Előfordulhat, hogy lehet negatív percek kivonása céljából.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: formátuma<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátummegadó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni Formátumminta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogyan formázhatja az időbélyegző értékét. Ha nincs megadva formátum, az ISO 8601 formátumot ("o") használja.|  
|addhours|Egy egész számmal megadott óraszám hozzáadása az átadott karakterlánc-időbélyeghez. Órák száma pozitív vagy negatív is lehet. Ez egy karakterlánc, az ISO 8601 formátumot ("o") alapértelmezés szerint, ha egy formátummegadó biztosítja. Például `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: megadása kötelező. Az időt tartalmazó karakterlánc.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: óra<br /><br /> **Leírás**: megadása kötelező. A hozzáadandó órák száma. Előfordulhat, hogy lehet negatív órák kivonása céljából.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: formátuma<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátummegadó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni Formátumminta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogyan formázhatja az időbélyegző értékét. Ha nincs megadva formátum, az ISO 8601 formátumot ("o") használja.|  
|napokHozzaadasa|Egy egész számmal megadott nap hozzáadása az átadott karakterlánc-időbélyeghez. Azon napok száma pozitív vagy negatív is lehet. Ez egy karakterlánc, az ISO 8601 formátumot ("o") alapértelmezés szerint, ha egy formátummegadó biztosítja. Például `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: időbélyeg<br /><br /> **Leírás**: megadása kötelező. Az időt tartalmazó karakterlánc.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: nap<br /><br /> **Leírás**: megadása kötelező. A hozzáadandó napok száma. Előfordulhat, hogy lehet negatív napok kivonása céljából.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: formátuma<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátummegadó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni Formátumminta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogyan formázhatja az időbélyegző értékét. Ha nincs megadva formátum, az ISO 8601 formátumot ("o") használja.|  
|formatDateTime|Dátumformátumú karakterláncot ad vissza. Ez egy karakterlánc, az ISO 8601 formátumot ("o") alapértelmezés szerint, ha egy formátummegadó biztosítja. Például `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: dátum<br /><br /> **Leírás**: megadása kötelező. A dátumot tartalmazó karakterláncot.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: formátuma<br /><br /> **Leírás**: nem kötelező. Vagy egy [egyetlen formátummegadó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni Formátumminta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) azt jelzi, hogyan formázhatja az időbélyegző értékét. Ha nincs megadva formátum, az ISO 8601 formátumot ("o") használja.|  

## <a name="next-steps"></a>További lépések
Használhatja a kifejezések rendszerváltozók listáját lásd: [rendszerváltozók](control-flow-system-variables.md).
