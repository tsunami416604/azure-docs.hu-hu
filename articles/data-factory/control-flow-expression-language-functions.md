---
title: Kifejezés és függvények a Azure Data Factoryban
description: Ez a cikk a adat-előállító entitások létrehozásához használható kifejezésekkel és függvényekkel kapcsolatos információkat tartalmazza.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 75441a398e654893601cb7375ad3674d2b8aff29
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679916"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Kifejezések és függvények az Azure Data Factoryben
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-functions-variables.md)
> * [Aktuális verzió](control-flow-expression-language-functions.md)

Ez a cikk a Azure Data Factory által támogatott kifejezések és függvények részleteit ismerteti. 

## <a name="introduction"></a>Bevezetés
A definícióban található JSON-értékek lehetnek olyan literálok vagy kifejezések, amelyek kiértékelése futásidőben történik. Például:  
  
```json
"name": "value"
```

 vagy  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Kifejezések  
A kifejezések egy JSON-karakterlánc értékében bárhol megjelenhetnek, és mindig egy másik JSON-értéket eredményeznek. Ha egy JSON-érték egy kifejezés, a rendszer kinyeri a kifejezés törzsét az at-Sign (\@) eltávolításával. Ha olyan literális karakterláncra van szükség, amely a \@vel kezdődik, akkor \@\@használatával kell elmenekülnie. Az alábbi példák bemutatják a kifejezések kiértékelésének módját.  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|paraméterek|A rendszer a "parameters" karaktert adja vissza.|  
|"paraméterek [1]"|A rendszer a "Parameters [1]" karaktert adja vissza.|  
|"\@\@"|A rendszer egy "\@" karaktert tartalmazó 1 karakterből álló karakterláncot ad vissza.|  
|"\@"|A rendszer egy 2 karakterből álló karakterláncot ad vissza, amely a következőt tartalmazza: "\@".|  
  
 A kifejezések a karakterláncok között is megjelenhetnek, ha egy *karakterlánc-interpolációs* funkciót használ, ahol a kifejezések `@{ ... }`ba vannak csomagolva. Például:`"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 A karakterlánc-interpoláció használatakor az eredmény mindig karakterlánc. Tegyük fel, hogy a `myNumber` `42` és `myString`ként van megadva `foo`:  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|"\@folyamat (). Parameters. sajatString"| `foo` karakterláncként adja vissza.|  
|"\@{folyamat (). Parameters. sajatString}"| `foo` karakterláncként adja vissza.|  
|"\@folyamat (). Parameters. myNumber"| `42` értéket ad vissza *számként*.|  
|"\@{folyamat (). Parameters. myNumber}"| `42` *karakterláncként*adja vissza.|  
|"Válasz: @ {pipeline (). Parameters. myNumber}"| A `Answer is: 42`karakterláncot adja vissza.|  
|"\@concat (' válasz: ', string (folyamat (). Parameters. myNumber))"| A karakterláncot adja vissza `Answer is: 42`|  
|"Válasz: \@\@{pipeline (). Parameters. myNumber}"| A `Answer is: @{pipeline().parameters.myNumber}`karakterláncot adja vissza.|  
  
### <a name="examples"></a>Példák

#### <a name="a-dataset-with-a-parameter"></a>Egy paraméterrel rendelkező adatkészlet
A következő példában a BlobDataset egy **path**nevű paramétert vesz fel. Az érték a **folderPath** tulajdonság értékének megadására szolgál a következő kifejezés használatával: `dataset().path`. 

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

#### <a name="a-pipeline-with-a-parameter"></a>Egy paraméterrel rendelkező folyamat
A következő példában a folyamat **inputPath** és **outputPath** paramétereket használ. A paraméteres blob-adatkészlet **elérési útja** a paraméterek értékeinek használatával van beállítva. Az itt használt szintaxis a következő: `pipeline().parameters.parametername`. 

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
#### <a name="tutorial"></a>Oktatóanyag
Ez az [oktatóanyag](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) bemutatja, hogyan adhat át paramétereket egy folyamat és tevékenység között, valamint a tevékenységek között.

  
## <a name="functions"></a>Functions  
 A függvényeket a kifejezéseken belül hívhatja. A következő szakaszokban információt talál a kifejezésekben használható függvényekről.  

## <a name="string-functions"></a>Sztringfüggvények  
 A következő függvények csak a karakterláncokra vonatkoznak. A karakterláncokon számos gyűjtemény függvényt is használhat.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|concat|Tetszőleges számú karakterláncot egyesít. Ha például a Parameter1 `foo,` a következő kifejezés visszaadja `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: *n* karakterlánc<br /><br /> **Leírás**: kötelező. Az egyetlen karakterláncba egyesíthető karakterláncok.|  
|substring|Egy karakterláncból származó karakterek részhalmazát adja vissza. Például a következő kifejezés:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> adja vissza<br /><br /> `foo`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az a karakterlánc, amelyből a rendszer az alkarakterláncot veszi.<br /><br /> **Paraméter száma**: 2<br /><br /> **Name**: index indítása<br /><br /> **Leírás**: kötelező. Az index, ahol az alsztring az 1. paraméterben kezdődik. A kezdő index nulla-alapú. <br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: hossz<br /><br /> **Leírás**: kötelező. Az alsztring hossza.|  
|csere|Egy sztringet cserél egy adott sztringre. Például a következő kifejezés:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> adja vissza<br /><br /> `the new string`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező.  Ha a 2. paraméter szerepel az 1. paraméterben, a 2. paraméterben keresett karakterláncot, a 3. paraméterrel frissítve.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: régi sztring<br /><br /> **Leírás**: kötelező. A 3-as paraméterrel lecserélt karakterlánc, ha egyezés található az 1. paraméterben.<br /><br /> **Paraméter száma**: 3<br /><br /> **Name**: új sztring<br /><br /> **Leírás**: kötelező. A 2. paraméterben szereplő karakterlánc cseréjéhez használt karakterlánc, ha egyezés található az 1. paraméterben.|  
|GUID| Globálisan egyedi karakterláncot hoz létre (más néven. GUID). Például a következő kimenet hozható létre `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Egyetlen formátum megadása, amely [a GUID értékének formázását](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)jelzi. A Format paraméter lehet "N", "D", "B", "P" vagy "X". Ha nincs megadva a formátum, a rendszer a "D" karakterláncot használja.|  
|toLower|Egy karakterláncot kisbetűsre alakít. A `two by two is four`például a következőt adja vissza: `toLower('Two by Two is Four')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az alsó burkolatra konvertálandó karakterlánc. Ha a karakterlánc egyik karaktere nem rendelkezik kisbetűvel, akkor a visszaadott karakterláncban változatlan marad.|  
|toUpper|A karakterláncot nagybetűssé alakítja. Például a következő kifejezés visszaadja a `TWO BY TWO IS FOUR`: `toUpper('Two by Two is Four')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. A felső burkolatra konvertálandó karakterlánc. Ha a karakterlánc egyik karaktere nem rendelkezik nagybetűvel, akkor a visszaadott karakterláncban változatlan marad.|  
|indexOf|A sztring kis-és nagybetűk közötti érték indexének megkeresése. Az index nulla-alapú. Például a következő kifejezés visszaadja a `7`: `indexof('hello, world.', 'world')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az értéket tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az indexben keresendő érték.|  
|lastindexof|A sztringben lévő érték utolsó indexének megkeresése nem bizalmasan történik. Az index nulla-alapú. Például a következő kifejezés visszaadja a `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az értéket tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az indexben keresendő érték.|  
|startswith|Ellenőrzi, hogy a sztring kis-és nagybetűkkel kezdődik-e. Például a következő kifejezés visszaadja a `true`: `startswith('hello, world', 'hello')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az értéket tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az az érték, amelynek a karakterlánca kezdődhet.|  
|endswith|Ellenőrzi, hogy a karakterlánc a kis-és nagybetűket nem megkülönböztető értékre végződik-e. Például a következő kifejezés visszaadja a `true`: `endswith('hello, world', 'world')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az értéket tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az az érték, amelynek a karakterlánca végződhet.|  
|felosztás|Feldarabolja a karakterláncot egy elválasztó használatával. Például a következő kifejezés visszaadja a `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. A felosztott karakterlánc.<br /><br /> **Paraméter száma**: 2<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az elválasztó.|  
  
  
## <a name="collection-functions"></a>Gyűjtési függvények  
 Ezek a függvények olyan gyűjteményeknél működnek, mint a tömbök, karakterláncok és esetenként a szótárak.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|tartalmaz|Igaz értéket ad vissza, ha a szótár tartalmaz egy kulcsot, a lista tartalmaz értéket, vagy karakterláncot tartalmaz. Például a következő kifejezés visszaadja a `true:``contains('abacaba','aca')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjteményen belül<br /><br /> **Leírás**: kötelező. A gyűjtemény, amelyben keresni szeretne.<br /><br /> **Paraméter száma**: 2<br /><br /> **Name**: objektum keresése<br /><br /> **Leírás**: kötelező. Az objektum, amely a **gyűjteményen belül**található.|  
|hossza|Egy tömb vagy karakterlánc elemeinek számát adja vissza. Például a következő kifejezés visszaadja a `3`: `length('abc')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: gyűjtemény<br /><br /> **Leírás**: kötelező. A gyűjtemény, amely a hosszát kapja.|  
|üres|Igaz értéket ad vissza, ha az objektum, tömb vagy karakterlánc üres. A következő kifejezés például a `true`t adja vissza:<br /><br /> `empty('')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: gyűjtemény<br /><br /> **Leírás**: kötelező. A gyűjtemény, amelyből ellenőrizhető, hogy üres-e.|  
|kereszteződés|Egyetlen tömböt vagy objektumot ad vissza, amely az átadott tömbök vagy objektumok közös elemeit tartalmazza. Ez a függvény például a `[1, 2]`t adja vissza:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> A függvény paraméterei lehetnek objektumok vagy tömbök készletei (nem ezek keverékei). Ha két objektum létezik ugyanazzal a névvel, akkor a végső objektumban az utolsó objektum jelenik meg.<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: gyűjtemény *n*<br /><br /> **Leírás**: kötelező. Az értékelendő gyűjtemények. Az objektumnak az összes olyan gyűjteményben szerepelnie kell, amely az eredményben megjelenik.|  
|Union|Egyetlen tömböt vagy objektumot ad vissza, amely az összes tömbben vagy objektumban található elemnek megfelel. Ez a függvény például a következőt adja vissza: `[1, 2, 3, 10, 101]:`<br /><br /> : `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> A függvény paraméterei lehetnek objektumok vagy tömbök készletei (nem ezek keverékei). Ha két objektum ugyanazzal a névvel van ellátva a végső kimenetben, a végső objektumban az utolsó objektum jelenik meg.<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: gyűjtemény *n*<br /><br /> **Leírás**: kötelező. Az értékelendő gyűjtemények. A gyűjtemények bármelyikében megjelenő objektum megjelenik az eredményben.|  
|első|Az átadott tömb vagy karakterlánc első elemét adja vissza. Ez a függvény például a `0`t adja vissza:<br /><br /> `first([0,2,3])`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: gyűjtemény<br /><br /> **Leírás**: kötelező. Az a gyűjtemény, amelyből az első objektumot el kell végezni.|  
|utolsó|Az átadott tömb vagy karakterlánc utolsó elemét adja vissza. Ez a függvény például a `3`t adja vissza:<br /><br /> `last('0123')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: gyűjtemény<br /><br /> **Leírás**: kötelező. Az a gyűjtemény, amelyből az utolsó objektumot el kell végezni.|  
|Eltarthat|Az átadott tömb vagy karakterlánc első **számlálási** elemeit adja vissza, például a függvény visszaadja `[1, 2]`: `take([1, 2, 3, 4], 2)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: gyűjtemény<br /><br /> **Leírás**: kötelező. A gyűjtemény, amelyből az első **számú** objektumot el kell végezni.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: darabszám<br /><br /> **Leírás**: kötelező. A **gyűjteményből**átvenni kívánt objektumok száma. Pozitív egész számnak kell lennie.|  
|kihagyása|A tömbben lévő elemeket adja vissza indexek **száma**alapján, például a függvény `[3, 4]`adja vissza:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: gyűjtemény<br /><br /> **Leírás**: kötelező. A gyűjtemény, amelyből ki kell hagyni az első **számú** objektumot.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: darabszám<br /><br /> **Leírás**: kötelező. A **gyűjtemény**elejéről eltávolítandó objektumok száma. Pozitív egész számnak kell lennie.|  
  
## <a name="logical-functions"></a>Logikai függvények  
 Ezek a függvények a feltételeken belül hasznosak lehetnek, és bármilyen típusú logikát kiértékelnek.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|egyenlő|Igaz értéket ad vissza, ha két érték egyenlő. Ha például a Parameter1 az ize, a következő kifejezés visszaadja a `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: kötelező. Az objektum, amelyet össze kell hasonlítani a **2. objektummal**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: kötelező. Az **1. objektumhoz**összehasonlítandó objektum.|  
|kisebb|Igaz értéket ad vissza, ha az első argumentum kisebb a másodiknál. Megjegyzés: az értékek csak Integer, float vagy string típusúak lehetnek. Például a következő kifejezés visszaadja a `true`: `less(10,100)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: kötelező. Az objektum, amellyel ellenőrizhető, hogy kisebb-e a **2. objektumnál**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: kötelező. Az objektum, amely azt vizsgálja, hogy az nagyobb-e, mint az **1. objektum**.|  
|lessOrEquals|Igaz értéket ad vissza, ha az első argumentum kisebb a másodiknál, vagy azzal egyenlő. Megjegyzés: az értékek csak Integer, float vagy string típusúak lehetnek. Például a következő kifejezés visszaadja a `true`: `lessOrEquals(10,10)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: kötelező. Az az objektum, amellyel ellenőrizhető, hogy a 2. **objektum**kisebb vagy egyenlő-e.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: kötelező. Az az objektum, amely ellenőrizze, hogy az **1. objektumnál**nagyobb vagy egyenlő-e.|  
|nagyobb|Igaz értéket ad vissza, ha az első argumentum nagyobb a másodiknál. Megjegyzés: az értékek csak Integer, float vagy string típusúak lehetnek. Például a következő kifejezés visszaadja a `false`: `greater(10,10)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: kötelező. Az objektum, amellyel ellenőrizhető, hogy nagyobb-e a **2. objektumnál**.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: kötelező. Az objektum, amelyből ellenőrizhető, hogy az **1. objektumnál**kisebb-e.|  
|greaterOrEquals|Igaz értéket ad vissza, ha az első argumentum nagyobb a másodiknál, vagy azzal egyenlő. Megjegyzés: az értékek csak Integer, float vagy string típusúak lehetnek. Például a következő kifejezés visszaadja a `false`: `greaterOrEquals(10,100)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: kötelező. Az az objektum, amelyik azt vizsgálja, hogy a **2. objektum**értéke nagyobb vagy egyenlő-e.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: kötelező. Az az objektum, amellyel ellenőrizhető, hogy az **1. objektumnál**kisebb vagy egyenlő-e.|  
|és|Igaz értéket ad vissza, ha mindkét paraméter igaz. Mindkét argumentumnak logikai értéknek kell lennie. A `false`a következőt adja vissza: `and(greater(1,10),equals(0,0))`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Boolean 1<br /><br /> **Leírás**: kötelező. Az első argumentum, amelynek `true`kell lennie.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: logikai 2<br /><br /> **Leírás**: kötelező. A második argumentumnak `true`nak kell lennie.|  
|vagy|Igaz értéket ad vissza, ha a paraméterek bármelyike igaz. Mindkét argumentumnak logikai értéknek kell lennie. A `true`a következőt adja vissza: `or(greater(1,10),equals(0,0))`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Boolean 1<br /><br /> **Leírás**: kötelező. Az első argumentum, amely `true`lehet.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: logikai 2<br /><br /> **Leírás**: kötelező. A második argumentum lehet `true`.|  
|nem|Igaz értéket ad eredményül, ha a paraméter `false`. Mindkét argumentumnak logikai értéknek kell lennie. A `true`a következőt adja vissza: `not(contains('200 Success','Fail'))`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: logikai<br /><br /> **Leírás**: igaz értéket ad vissza, ha a paraméter `false`. Mindkét argumentumnak logikai értéknek kell lennie. A `true`a következőt adja vissza: `not(contains('200 Success','Fail'))`|  
|Ha|Egy megadott értéket ad vissza, amely alapján, ha a megadott kifejezés `true` vagy `false`t eredményez.  A `"yes"`például a következőt adja vissza: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: kifejezés<br /><br /> **Leírás**: kötelező. Logikai érték, amely meghatározza, hogy a kifejezés melyik értéket adja vissza.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: true<br /><br /> **Leírás**: kötelező. A kifejezés `true`akor visszaadni kívánt érték.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: hamis<br /><br /> **Leírás**: kötelező. A kifejezés `false`akor visszaadni kívánt érték.|  
  
## <a name="conversion-functions"></a>Átalakítási függvények  
 Ezek a függvények a nyelvben lévő natív típusok közötti átalakításra szolgálnak:  
  
-   sztring  
  
-   egész szám  
  
-   lebegőpontos  
  
-   logikai  
  
-   tömbök  
  
-   szótárak  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|int|A paraméter átalakítása egész számra. Például a következő kifejezés a 100 értéket adja vissza számként, nem pedig karakterláncot: `int('100')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. Az egész számra konvertált érték.|  
|sztring|Alakítsa át a paramétert karakterlánccá. Például a következő kifejezés visszaadja a `'10'`: `string(10)` egy objektumot karakterlánccá is konvertálhat, például ha a **foo** paraméter egy tulajdonsággal rendelkező objektum `bar : baz`, akkor a következő `{"bar" : "baz"}` fog visszaadni `string(pipeline().parameters.foo)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. A sztringre konvertált érték.|  
|JSON|Alakítsa át a paramétert JSON típusú értékké. Ez a karakterlánc () ellentéte. Például a következő kifejezés `[1,2,3]` tömböt ad vissza karakterlánc helyett:<br /><br /> `json('[1,2,3]')`<br /><br /> Hasonlóképpen egy sztringet is konvertálhat objektumra. A `json('{"bar" : "baz"}')` például a következőt adja vissza:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. A natív típusú értékké konvertált karakterlánc.<br /><br /> A JSON-függvény az XML-bemenetet is támogatja. Például a következő paraméter értéke:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> a következő JSON-ra konvertálódik:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|lebegőpontos|A paraméter argumentumának átalakítása lebegőpontos számra. Például a következő kifejezés visszaadja a `10.333`: `float('10.333')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. A lebegőpontos számra konvertált érték|  
|logikai|A paraméter átalakítása logikai értékre. Például a következő kifejezés visszaadja a `false`: `bool(0)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. A logikai értékké konvertált érték.|  
|összefonódik|Az első nem null értékű objektumot adja vissza az átadott argumentumokban. Megjegyzés: üres karakterlánc nem null. Ha például az 1. és a 2. paraméter nincs definiálva, akkor a `fallback`a következőt adja vissza: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**:*n* objektum<br /><br /> **Leírás**: kötelező. A `null`keresésére szolgáló objektumok.|  
|Base64|A bemeneti karakterlánc Base64-ábrázolását adja vissza. Például a következő kifejezés visszaadja a `c29tZSBzdHJpbmc=`: `base64('some string')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. karakterlánc<br /><br /> **Leírás**: kötelező. A Base64-ábrázolásba kódolni kívánt karakterlánc.|  
|base64ToBinary|Base64 kódolású karakterlánc bináris ábrázolását adja vissza. A következő kifejezés például egy karakterlánc bináris ábrázolását adja vissza: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. A Base64 kódolású karakterlánc.|  
|base64ToString|Egy bementi-kódolású karakterlánc karakterlánc-ábrázolását adja vissza. A következő kifejezés például egy bizonyos karakterláncot ad vissza: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. A Base64 kódolású karakterlánc.|  
|Bináris|Egy érték bináris ábrázolását adja vissza.  A következő kifejezés például egy karakterlánc bináris ábrázolását adja vissza: `binary(‘some string’).`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. A binárisra konvertált érték.|  
|dataUriToBinary|Egy adat URI azonosítójának bináris ábrázolását adja vissza. A következő kifejezés például egy karakterlánc bináris ábrázolását adja vissza: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. A bináris ábrázolásra konvertálandó adat URI-ja.|  
|dataUriToString|Egy adat-URI karakterlánc-ábrázolását adja vissza. A következő kifejezés például egy bizonyos karakterláncot ad vissza: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br />**Leírás**: kötelező. A karakterlánc-ábrázolásra konvertálandó adat URI-ja.|  
|dataUri|Egy érték adaturi-JÁT adja vissza. A következő kifejezés például az adatok visszaadása: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: érték<br /><br />**Leírás**: kötelező. Az adaturi formátumra konvertálandó érték.|  
|decodeBase64|Egy bemeneti bementi sztring sztringjét adja vissza. Például a következő kifejezés visszaadja a `some string`: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: egy bemeneti bementi karakterláncot ad vissza.|  
|encodeUriComponent|URL – elkerüli az átadott karakterláncot. Például a következő kifejezés visszaadja a `You+Are%3ACool%2FAwesome`: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. A menekülési URL-cím – nem biztonságos karakterek a következőből:.|  
|decodeUriComponent|Nem URL-cím – elkerüli az átadott karakterláncot. Például a következő kifejezés visszaadja a `You Are:Cool/Awesome`: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. Az URL-cím nem biztonságos karaktereinek dekódolására szolgáló sztring.|  
|decodeDataUri|Egy bemeneti adat URI-karakterláncának bináris ábrázolását adja vissza. A következő kifejezés például a `some string`bináris ábrázolását adja vissza: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: kötelező. A dataURI a dekódolni kívánt bináris ábrázolást.|  
|uriComponent|Egy érték URI-kódolású ábrázolását adja vissza. Például a következő kifejezés visszaadja a `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Paraméter részletei: szám: 1, név: karakterlánc, leírás: kötelező. Az URI-ként kódolt karakterlánc.|  
|uriComponentToBinary|Egy URI-kódolású karakterlánc bináris ábrázolását adja vissza. A következő kifejezés például `You Are:Cool/Awesome`bináris ábrázolását adja vissza: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br />**Leírás**: kötelező. Az URI-kódolású karakterlánc.|  
|uriComponentToString|Egy URI-kódolású karakterlánc karakterlánc-ábrázolását adja vissza. Például a következő kifejezés visszaadja a `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Paraméter száma**: 1<br /><br />**Name**: karakterlánc<br /><br />**Leírás**: kötelező. Az URI-kódolású karakterlánc.|  
|xml|Az érték XML-ábrázolásának visszaadása. A következő kifejezés például egy `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`által képviselt XML-tartalmat ad vissza. Az XML-függvény a JSON-objektumok bemenetét is támogatja. A `{ "abc": "xyz" }` paraméter például XML-tartalomra konvertálódik `\<abc>xyz\</abc>`<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: érték<br /><br />**Leírás**: kötelező. Az XML-re konvertálandó érték.|  
|XPath|Olyan XML-csomópontok tömbjét adja vissza, amelyek megfelelnek egy olyan érték XPath-kifejezésének, amelyet az XPath-kifejezés kiértékel.<br /><br />  **1. példa**<br /><br /> Tegyük fel, hogy a "P1" paraméter értéke a következő XML karakterlánc-ábrázolása:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Ez a kód: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> visszatérés<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> mivel<br /><br /> 2. Ez a kód: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> visszatérés<br /><br /> `13`<br /><br /> <br /><br /> **2. példa**<br /><br /> A következő XML-tartalomnak kell megadnia:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1. Ez a kód: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> vagy<br /><br /> 2. Ez a kód: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Adja vissza<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> és<br /><br /> 3. Ez a kód: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Adja vissza<br /><br /> ``bar``<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: XML<br /><br />**Leírás**: kötelező. Az az XML, amelyen az XPath-kifejezést ki kell értékelni.<br /><br /> **Paraméter száma**: 2<br /><br />**Név**: XPath<br /><br />**Leírás**: kötelező. Az értékelendő XPath-kifejezés.|  
|tömb|Alakítsa át a paramétert egy tömbre.  Például a következő kifejezés visszaadja a `["abc"]`: `array('abc')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: érték<br /><br /> **Leírás**: kötelező. Egy tömbre konvertált érték.|
|createArray|Létrehoz egy tömböt a paraméterekből.  Például a következő kifejezés visszaadja a `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Paraméter száma**: 1... n<br /><br /> **Név**: bármelyik n<br /><br /> **Leírás**: kötelező. Egy tömbbe egyesíthető értékek.|

## <a name="math-functions"></a>Matematikai függvények  
 Ezek a függvények bármelyik típusú számhoz használhatók: **egész** számok és **lebegőpontok**.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|add|A két szám hozzáadásának eredményét adja vissza. Ez a függvény például a következő `20.333`adja vissza: `add(10,10.333)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Summand 1<br /><br /> **Leírás**: kötelező. A **2. Summand**hozzáadandó szám.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Summand 2<br /><br /> **Leírás**: kötelező. Az **1. Summand**hozzáadandó szám.|  
|Sub|A két szám kivonásának eredményét adja vissza. Ez a függvény például a következőt adja vissza: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: kisebbítendőt<br /><br /> **Leírás**: kötelező. Az a szám, amelyből a **kivonandó** el lett távolítva.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: kivonandó<br /><br /> **Leírás**: kötelező. A **kisebbítendőt**eltávolítandó szám.|  
|mul|A két szám szorzásának eredményét adja vissza. A `103.33`például a következőt adja vissza:<br /><br /> `mul(10,10.333)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Multiplicand 1<br /><br /> **Leírás**: kötelező. A **Multiplicand 2** és közötti szorzásának száma.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Multiplicand 2<br /><br /> **Leírás**: kötelező. A **Multiplicand 1** és közötti szorzásának száma.|  
|div|A két szám osztásának eredményét adja vissza. A `1.0333`például a következőt adja vissza:<br /><br /> `div(10.333,10)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: osztalék<br /><br /> **Leírás**: kötelező. Az **osztó**által elosztani kívánt szám.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: osztó<br /><br /> **Leírás**: kötelező. A **osztalék** felosztására szolgáló szám.|  
|mod|A maradék eredményét adja vissza a két szám (többtényezős) osztása után. A következő kifejezés például a `2`t adja vissza:<br /><br /> `mod(10,4)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: osztalék<br /><br /> **Leírás**: kötelező. Az **osztó**által elosztani kívánt szám.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: osztó<br /><br /> **Leírás**: kötelező. A **osztalék** felosztására szolgáló szám. A felosztás után a maradékot a rendszer elvégzi.|  
|perc|A függvény hívásához két különböző mintázat létezik: `min([0,1,2])` itt a percben egy tömb található. A kifejezés `0`értéket ad vissza. Másik lehetőségként az értékek vesszővel tagolt listáját is elvégezheti: `min(0,1,2)` ez a függvény 0 értéket ad vissza. Vegye figyelembe, hogy minden értéknek számnak kell lennie, tehát ha a paraméter egy tömb, csak számokat kell tartalmaznia.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény vagy érték<br /><br /> **Leírás**: kötelező. Az értékek tömbje lehet a minimális érték vagy a készlet első értékének megkeresése.<br /><br /> **Paraméter száma**: 2... *n*<br /><br /> **Név**: érték *n*<br /><br /> **Leírás**: nem kötelező. Ha az első paraméter értéke, akkor további értékeket adhat át, és a rendszer az összes átadott érték minimumát adja vissza.|  
|Max|A függvény hívásához két különböző minta használható: `max([0,1,2])`<br /><br /> Itt Max egy tömböt vesz igénybe. A kifejezés `2`értéket ad vissza. Másik lehetőségként az értékek vesszővel tagolt listáját is elvégezheti: `max(0,1,2)` ez a függvény 2 értéket ad vissza. Vegye figyelembe, hogy minden értéknek számnak kell lennie, tehát ha a paraméter egy tömb, csak számokat kell tartalmaznia.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: gyűjtemény vagy érték<br /><br /> **Leírás**: kötelező. Az értékek tömbje lehet, hogy megkeresse a maximális értéket, vagy egy készlet első értékét.<br /><br /> **Paraméter száma**: 2... *n*<br /><br /> **Név**: érték *n*<br /><br /> **Leírás**: nem kötelező. Ha az első paraméter értéke, akkor további értékeket adhat át, és a rendszer az összes átadott érték maximális számát adja vissza.|  
|tartomány| Egy adott számból kezdődő egész számok tömbjét hozza létre, és meghatározza a visszaadott tömb hosszát. Ez a függvény például a `[3,4,5,6]`t adja vissza:<br /><br /> `range(3,4)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: index indítása<br /><br /> **Leírás**: kötelező. Ez az első egész szám a tömbben.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: darabszám<br /><br /> **Leírás**: kötelező. A tömbben lévő egész számok száma.|  
|Rand| Egy véletlenszerű egész számot hoz létre a megadott tartományon belül (mindkét végponton. Például visszatérhet `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: minimum<br /><br /> **Leírás**: kötelező. A visszaadott legkisebb egész szám.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: maximum<br /><br /> **Leírás**: kötelező. A visszaadott legmagasabb egész szám.|  
  
## <a name="date-functions"></a>Dátum függvények  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|utcnow|Az aktuális időbélyeget adja vissza karakterláncként. Például `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|addSeconds|Egész számú másodpercet ad hozzá az átadott karakterlánc-időbélyeghez. A másodpercek száma lehet pozitív vagy negatív. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: időbélyeg<br /><br /> **Leírás**: kötelező. Az időpontot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: másodperc<br /><br /> **Leírás**: kötelező. A hozzáadandó másodpercek száma. Negatív lehet a kivonás másodpercben.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|addminutes|Egész számú percet ad hozzá az átadott karakterlánc-időbélyeghez. A percek száma lehet pozitív vagy negatív. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: időbélyeg<br /><br /> **Leírás**: kötelező. Az időpontot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: perc<br /><br /> **Leírás**: kötelező. A hozzáadandó percek száma. A percek kivonása negatív lehet.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|addhours|Egész számú órát ad hozzá az átadott karakterlánc-időbélyeghez. Az órák száma lehet pozitív vagy negatív. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: időbélyeg<br /><br /> **Leírás**: kötelező. Az időpontot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: óra<br /><br /> **Leírás**: kötelező. A hozzáadandó órák száma. Az órák kivonása negatív lehet.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|addDays|Egész számú napot ad hozzá az átadott karakterlánc-időbélyeghez. A napok száma lehet pozitív vagy negatív. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: időbélyeg<br /><br /> **Leírás**: kötelező. Az időpontot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: nap<br /><br /> **Leírás**: kötelező. A hozzáadandó napok száma. A kivonási napokat negatívan lehet kivonni.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|formatDateTime|Egy dátum formátumú karakterláncot ad vissza. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br />A dátum éééé/hh/nn formátumban való formázásához használja a következőt: formatDateTime (utcnow (), éééé/hh/nn).</br>Ha nevet szeretne fűzni a dátumhoz, használja a @concatt ("foo-", "/", formatDateTime (utcnow (), éééé/hh/nn)).<br><br> **Paraméter száma**: 1<br /><br /> **Név**: dátum<br /><br /> **Leírás**: kötelező. A dátumot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: formátum<br /><br /> **Leírás**: nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja. |  

## <a name="next-steps"></a>További lépések
A kifejezésekben használható rendszerváltozók listáját a [rendszerváltozók](control-flow-system-variables.md)című részben tekintheti meg.
