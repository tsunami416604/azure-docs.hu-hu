---
title: Kifejezés és függvények a Azure Data Factoryban | Microsoft Docs
description: Ez a cikk a adat-előállító entitások létrehozásához használható kifejezésekkel és függvényekkel kapcsolatos információkat tartalmazza.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 533d0c7461530a00e6f640ee53c0c87d336e799d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876316"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Kifejezések és függvények az Azure Data Factoryben
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-functions-variables.md)
> * [Aktuális verzió](control-flow-expression-language-functions.md)

Ez a cikk a Azure Data Factory által támogatott kifejezések és függvények részleteit ismerteti. 

## <a name="introduction"></a>Bevezetés
A definícióban található JSON-értékek lehetnek olyan literálok vagy kifejezések, amelyek kiértékelése futásidőben történik. Példa:  
  
```json
"name": "value"
```

 vagy  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Kifejezések  
A kifejezések egy JSON-karakterlánc értékében bárhol megjelenhetnek, és mindig egy másik JSON-értéket eredményeznek. Ha a JSON értéke egy kifejezés, a szervezet a kifejezés a következő bejelentkezés eltávolításával kivonjuk (\@). Ha egy konstans sztring van szükség, amely elindítja a \@, kell lennie karakterrel való átléptetése \@ \@. Az alábbi példák bemutatják a kifejezések kiértékelésének módját.  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|paraméterek|A rendszer a "parameters" karaktert adja vissza.|  
|"paraméterek [1]"|A rendszer a "Parameters [1]" karaktert adja vissza.|  
|"\@\@"|Egy 1 karaktert tartalmazó karakterlánc "\@" adja vissza.|  
|" \@"|Egy 2 karaktert tartalmazó karakterlánc " \@" adja vissza.|  
  
 A kifejezések a karakterláncok belsejében is megjelenhetnek, amely egy *karakterlánc* -interpolációs funkciót használ, `@{ ... }`ahol a kifejezések beburkoltak. Például:`"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 A karakterlánc-interpoláció használatakor az eredmény mindig karakterlánc. `myNumber` Tegyük fel `42` ,hogyakövetkezőképpen`foo`definiáltam: `myString`  
  
|JSON-érték|Eredmény|  
|----------------|------------|  
|"\@folyamat.parameters.myString ()"| Karakterláncként tér vissza `foo` .|  
|"\@{folyamat ().parameters.myString}"| Karakterláncként tér vissza `foo` .|  
|"\@folyamat (). Parameters. myNumber"| `42` Számként adjavissza.|  
|"\@{folyamat ().parameters.myNumber}"| `42` Karakterláncként térvissza.|  
|"Válasz: @{folyamat ().parameters.myNumber}"| A karakterláncot `Answer is: 42`adja vissza.|  
|"\@concat (' válasz: ', string (folyamat (). Parameters. myNumber))"| A karakterláncot adja vissza.`Answer is: 42`|  
|"Válasz: \@ \@{pipeline (). Parameters. myNumber}"| A karakterláncot `Answer is: @{pipeline().parameters.myNumber}`adja vissza.|  
  
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
A következő példában a folyamat **inputPath** és **outputPath** paramétereket használ. A paraméteres blob-adatkészlet **elérési útja** a paraméterek értékeinek használatával van beállítva. Az itt használt szintaxis a következő `pipeline().parameters.parametername`:. 

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

  
## <a name="functions"></a>Funkciók  
 A függvényeket a kifejezéseken belül hívhatja. A következő szakaszokban információt talál a kifejezésekben használható függvényekről.  

## <a name="string-functions"></a>Sztringfüggvények  
 A következő függvények csak a karakterláncokra vonatkoznak. A karakterláncokon számos gyűjtemény függvényt is használhat.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|concat|Tetszőleges számú karakterláncot egyesít. Ha például `foo,` a Parameter1 a következő kifejezéssel tér vissza `somevalue-foo-somevalue`:`concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: *N* sztring<br /><br /> **Leírás**: Kötelező. Az egyetlen karakterláncba egyesíthető karakterláncok.|  
|substring|Egy karakterláncból származó karakterek részhalmazát adja vissza. Például a következő kifejezés:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> adja vissza<br /><br /> `foo`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az a karakterlánc, amelyből a rendszer az alkarakterláncot veszi.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Kezdő index<br /><br /> **Leírás**: Kötelező. Az index, ahol az alsztring az 1. paraméterben kezdődik. A kezdő index nulla-alapú. <br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: Hossz<br /><br /> **Leírás**: Kötelező. Az alsztring hossza.|  
|csere|Egy sztringet cserél egy adott sztringre. Például a következő kifejezés:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> adja vissza<br /><br /> `the new string`<br /><br /> **Paraméter száma**: 1<br /><br /> **Name**: karakterlánc<br /><br /> **Leírás**: Kötelező.  Ha a 2. paraméter szerepel az 1. paraméterben, a 2. paraméterben keresett karakterláncot, a 3. paraméterrel frissítve.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Régi sztring<br /><br /> **Leírás**: Kötelező. A 3-as paraméterrel lecserélt karakterlánc, ha egyezés található az 1. paraméterben.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: Új sztring<br /><br /> **Leírás**: Kötelező. A 2. paraméterben szereplő karakterlánc cseréjéhez használt karakterlánc, ha egyezés található az 1. paraméterben.|  
|GUID| Globálisan egyedi karakterláncot hoz létre (más néven. GUID). Például a következő kimenet hozható létre `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Formátum<br /><br /> **Leírás**: Nem kötelező. Egyetlen formátum megadása, amely [a GUID értékének formázását](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)jelzi. A Format paraméter lehet "N", "D", "B", "P" vagy "X". Ha nincs megadva a formátum, a rendszer a "D" karakterláncot használja.|  
|toLower|Egy karakterláncot kisbetűsre alakít. A következő függvény például a következőket `two by two is four`adja vissza:`toLower('Two by Two is Four')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az alsó burkolatra konvertálandó karakterlánc. Ha a karakterlánc egyik karaktere nem rendelkezik kisbetűvel, akkor a visszaadott karakterláncban változatlan marad.|  
|toUpper|A karakterláncot nagybetűssé alakítja. A következő kifejezés például a függvényt `TWO BY TWO IS FOUR`adja vissza:`toUpper('Two by Two is Four')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. A felső burkolatra konvertálandó karakterlánc. Ha a karakterlánc egyik karaktere nem rendelkezik nagybetűvel, akkor a visszaadott karakterláncban változatlan marad.|  
|indexOf|A sztring kis-és nagybetűk közötti érték indexének megkeresése. Az index nulla-alapú. A következő kifejezés például a függvényt `7`adja vissza:`indexof('hello, world.', 'world')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az értéket tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az indexben keresendő érték.|  
|lastindexof|A sztringben lévő érték utolsó indexének megkeresése nem bizalmasan történik. Az index nulla-alapú. A következő kifejezés például a függvényt `3`adja vissza:`lastindexof('foofoo', 'foo')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az értéket tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az indexben keresendő érték.|  
|startswith|Ellenőrzi, hogy a sztring kis-és nagybetűkkel kezdődik-e. A következő kifejezés például a függvényt `true`adja vissza:`startswith('hello, world', 'hello')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az értéket tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az az érték, amelynek a karakterlánca kezdődhet.|  
|endswith|Ellenőrzi, hogy a karakterlánc a kis-és nagybetűket nem megkülönböztető értékre végződik-e. A következő kifejezés például a függvényt `true`adja vissza:`endswith('hello, world', 'world')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az értéket tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az az érték, amelynek a karakterlánca végződhet.|  
|split|Feldarabolja a karakterláncot egy elválasztó használatával. A következő kifejezés például a függvényt `["a", "b", "c"]`adja vissza:`split('a;b;c',';')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. A felosztott karakterlánc.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az elválasztó.|  
  
  
## <a name="collection-functions"></a>Gyűjtési függvények  
 Ezek a függvények olyan gyűjteményeknél működnek, mint a tömbök, karakterláncok és esetenként a szótárak.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|contains|Igaz értéket ad vissza, ha a szótár tartalmaz egy kulcsot, a lista tartalmaz értéket, vagy karakterláncot tartalmaz. A függvény például a következő kifejezést adja vissza:`true:``contains('abacaba','aca')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Gyűjteményen belül<br /><br /> **Leírás**: Kötelező. A gyűjtemény, amelyben keresni szeretne.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Objektum keresése<br /><br /> **Leírás**: Kötelező. Az objektum, amely a **gyűjteményen belül**található.|  
|length|Egy tömb vagy karakterlánc elemeinek számát adja vissza. A következő kifejezés például a függvényt `3`adja vissza:`length('abc')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Collection<br /><br /> **Leírás**: Kötelező. A gyűjtemény, amely a hosszát kapja.|  
|empty|Igaz értéket ad vissza, ha az objektum, tömb vagy karakterlánc üres. A következő kifejezés például a függvényt `true`adja vissza:<br /><br /> `empty('')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Collection<br /><br /> **Leírás**: Kötelező. A gyűjtemény, amelyből ellenőrizhető, hogy üres-e.|  
|intersection|Egyetlen tömböt vagy objektumot ad vissza, amely az átadott tömbök vagy objektumok közös elemeit tartalmazza. Ez a függvény például a következőt adja vissza `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> A függvény paraméterei lehetnek objektumok vagy tömbök készletei (nem ezek keverékei). Ha két objektum létezik ugyanazzal a névvel, akkor a végső objektumban az utolsó objektum jelenik meg.<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: *N* gyűjtemény<br /><br /> **Leírás**: Kötelező. Az értékelendő gyűjtemények. Az objektumnak az összes olyan gyűjteményben szerepelnie kell, amely az eredményben megjelenik.|  
|union|Egyetlen tömböt vagy objektumot ad vissza, amely az összes tömbben vagy objektumban található elemnek megfelel. Ez a függvény például a következőt adja vissza:`[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> A függvény paraméterei lehetnek objektumok vagy tömbök készletei (nem ezek keverékei). Ha két objektum ugyanazzal a névvel van ellátva a végső kimenetben, a végső objektumban az utolsó objektum jelenik meg.<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: *N* gyűjtemény<br /><br /> **Leírás**: Kötelező. Az értékelendő gyűjtemények. A gyűjtemények bármelyikében megjelenő objektum megjelenik az eredményben.|  
|első|Az átadott tömb vagy karakterlánc első elemét adja vissza. Ez a függvény például a következőt adja vissza `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Collection<br /><br /> **Leírás**: Kötelező. Az a gyűjtemény, amelyből az első objektumot el kell végezni.|  
|utolsó|Az átadott tömb vagy karakterlánc utolsó elemét adja vissza. Ez a függvény például a következőt adja vissza `3`:<br /><br /> `last('0123')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Collection<br /><br /> **Leírás**: Kötelező. Az a gyűjtemény, amelyből az utolsó objektumot el kell végezni.|  
|take|Az átadott tömb vagy karakterlánc első számlálási elemeit adja vissza, például a függvény eredménye `[1, 2]`:`take([1, 2, 3, 4], 2)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Collection<br /><br /> **Leírás**: Kötelező. A gyűjtemény, amelyből az első számú objektumot el kell végezni.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Count<br /><br /> **Leírás**: Kötelező. A gyűjteményből átvenni kívánt objektumok száma. Pozitív egész számnak kell lennie.|  
|kihagyás|A tömbben lévő elemeket adja vissza indexek **száma**alapján, például ez a függvény `[3, 4]`a következőt adja vissza:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Collection<br /><br /> **Leírás**: Kötelező. A gyűjtemény, amelyből ki kell hagyni az első számú objektumot.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Count<br /><br /> **Leírás**: Kötelező. A **gyűjtemény**elejéről eltávolítandó objektumok száma. Pozitív egész számnak kell lennie.|  
  
## <a name="logical-functions"></a>Logikai függvények  
 Ezek a függvények a feltételeken belül hasznosak lehetnek, és bármilyen típusú logikát kiértékelnek.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|equals|Igaz értéket ad vissza, ha két érték egyenlő. Ha például a Parameter1 a foo, a következő kifejezés visszatérhet `true`:`equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: Kötelező. Az objektum, amelyet össze kell hasonlítani a **2**. objektummal.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: Kötelező. Az **1**. objektumhoz összehasonlítandó objektum.|  
|kevesebb|Igaz értéket ad vissza, ha az első argumentum kisebb a másodiknál. Megjegyzés: az értékek csak Integer, float vagy string típusúak lehetnek. A következő kifejezés például a függvényt `true`adja vissza:`less(10,100)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: Kötelező. Az objektum, amellyel ellenőrizhető, hogy kisebb-e a **2**. objektumnál.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: Kötelező. Az objektum, amely azt vizsgálja, hogy az nagyobb-e, mint az **1. objektum**.|  
|lessOrEquals|Igaz értéket ad vissza, ha az első argumentum kisebb a másodiknál, vagy azzal egyenlő. Megjegyzés: az értékek csak Integer, float vagy string típusúak lehetnek. A következő kifejezés például a függvényt `true`adja vissza:`lessOrEquals(10,10)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: Kötelező. Az az objektum, amellyel ellenőrizhető, hogy a 2. **objektum**kisebb vagy egyenlő-e.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: Kötelező. Az az objektum, amely ellenőrizze, hogy az **1**. objektumnál nagyobb vagy egyenlő-e.|  
|nagyobb|Igaz értéket ad vissza, ha az első argumentum nagyobb a másodiknál. Megjegyzés: az értékek csak Integer, float vagy string típusúak lehetnek. A következő kifejezés például a függvényt `false`adja vissza:`greater(10,10)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: Kötelező. Az objektum, amellyel ellenőrizhető, hogy nagyobb-e a **2**. objektumnál.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: Kötelező. Az objektum, amelyből ellenőrizhető, hogy az **1**. objektumnál kisebb-e.|  
|greaterOrEquals|Igaz értéket ad vissza, ha az első argumentum nagyobb a másodiknál, vagy azzal egyenlő. Megjegyzés: az értékek csak Integer, float vagy string típusúak lehetnek. A következő kifejezés például a függvényt `false`adja vissza:`greaterOrEquals(10,100)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. objektum<br /><br /> **Leírás**: Kötelező. Az az objektum, amelyik azt vizsgálja, hogy a **2. objektum**értéke nagyobb vagy egyenlő-e.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. objektum<br /><br /> **Leírás**: Kötelező. Az az objektum, amellyel ellenőrizhető, hogy az **1**. objektumnál kisebb vagy egyenlő-e.|  
|és|Igaz értéket ad vissza, ha mindkét paraméter igaz. Mindkét argumentumnak logikai értéknek kell lennie. A következő eredmény `false`:`and(greater(1,10),equals(0,0))`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. logikai érték<br /><br /> **Leírás**: Kötelező. Az első argumentumnak kell lennie `true`.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Logikai érték 2<br /><br /> **Leírás**: Kötelező. A második argumentumnak a `true`értéknek kell lennie.|  
|or|Igaz értéket ad vissza, ha a paraméterek bármelyike igaz. Mindkét argumentumnak logikai értéknek kell lennie. A következő eredmény `true`:`or(greater(1,10),equals(0,0))`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. logikai érték<br /><br /> **Leírás**: Kötelező. Az első argumentum, amely `true`lehet.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Logikai érték 2<br /><br /> **Leírás**: Kötelező. A második argumentum lehet `true`.|  
|nem|Igaz értéket ad vissza, ha `false`a paraméter értéke. Mindkét argumentumnak logikai értéknek kell lennie. A következő eredmény `true`:`not(contains('200 Success','Fail'))`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Logikai<br /><br /> **Leírás**: Igaz értéket ad vissza, ha `false`a paraméter értéke. Mindkét argumentumnak logikai értéknek kell lennie. A következő eredmény `true`:`not(contains('200 Success','Fail'))`|  
|Ha|Egy megadott értéket ad vissza, amely alapján, `true` ha a megadott kifejezés a vagy `false`az eredményt adja.  A következő függvény például a következőket `"yes"`adja vissza:`if(equals(1, 1), 'yes', 'no')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Kifejezés<br /><br /> **Leírás**: Kötelező. Logikai érték, amely meghatározza, hogy a kifejezés melyik értéket adja vissza.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: True<br /><br /> **Leírás**: Kötelező. Az az érték, amelyet a kifejezés `true`esetén vissza kell adni.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: False (Hamis)<br /><br /> **Leírás**: Kötelező. Az az érték, amelyet a kifejezés `false`esetén vissza kell adni.|  
  
## <a name="conversion-functions"></a>Konverziós függvények  
 Ezek a függvények a nyelvben lévő natív típusok közötti átalakításra szolgálnak:  
  
-   Karakterlánc  
  
-   integer  
  
-   float  
  
-   boolean  
  
-   tömbök  
  
-   szótárak  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|int|A paraméter átalakítása egész számra. Például a következő kifejezés a 100 értéket adja vissza számként, nem pedig karakterláncot:`int('100')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Value<br /><br /> **Leírás**: Kötelező. Az egész számra konvertált érték.|  
|Karakterlánc|Alakítsa át a paramétert karakterlánccá. A következő kifejezés például a függvényt `'10'`adja vissza:  `string(10)`Egy objektumot karakterlánccá is konvertálhat, például ha az **ize** paraméter egy tulajdonsággal `bar : baz`rendelkező objektum, akkor a következő fog visszaadni `{"bar" : "baz"}``string(pipeline().parameters.foo)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Value<br /><br /> **Leírás**: Kötelező. A sztringre konvertált érték.|  
|JSON|Alakítsa át a paramétert JSON típusú értékké. Ez a karakterlánc () ellentéte. A következő kifejezés például tömbként tér `[1,2,3]` vissza karakterlánc helyett:<br /><br /> `json('[1,2,3]')`<br /><br /> Hasonlóképpen egy sztringet is konvertálhat objektumra. Például a következőt `json('{"bar" : "baz"}')` adja vissza:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. A natív típusú értékké konvertált karakterlánc.<br /><br /> A JSON-függvény az XML-bemenetet is támogatja. Például a következő paraméter értéke:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> a következő JSON-ra konvertálódik:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|A paraméter argumentumának átalakítása lebegőpontos számra. A következő kifejezés például a függvényt `10.333`adja vissza:`float('10.333')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Value<br /><br /> **Leírás**: Kötelező. A lebegőpontos számra konvertált érték|  
|bool|A paraméter átalakítása logikai értékre. A következő kifejezés például a függvényt `false`adja vissza:`bool(0)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Value<br /><br /> **Leírás**: Kötelező. A logikai értékké konvertált érték.|  
|összefonódik|Az első nem null értékű objektumot adja vissza az átadott argumentumokban. Megjegyzés: üres karakterlánc nem null. Ha például az 1. és a 2. paraméter nincs definiálva, `fallback`a a következőt adja vissza:`coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Paraméter száma**: 1... *n*<br /><br /> **Név**: Object*n*<br /><br /> **Leírás**: Kötelező. Az ellenőrzési `null`objektumok.|  
|Base64|A bemeneti karakterlánc Base64-ábrázolását adja vissza. A következő kifejezés például a függvényt `c29tZSBzdHJpbmc=`adja vissza:`base64('some string')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. karakterlánc<br /><br /> **Leírás**: Kötelező. A Base64-ábrázolásba kódolni kívánt karakterlánc.|  
|base64ToBinary|Base64 kódolású karakterlánc bináris ábrázolását adja vissza. A következő kifejezés például egy karakterlánc bináris ábrázolását adja vissza: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. A Base64 kódolású karakterlánc.|  
|base64ToString|Egy bementi-kódolású karakterlánc karakterlánc-ábrázolását adja vissza. A következő kifejezés például egy bizonyos karakterláncot ad vissza `base64ToString('c29tZSBzdHJpbmc=')`:.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. A Base64 kódolású karakterlánc.|  
|Binary|Egy érték bináris ábrázolását adja vissza.  A következő kifejezés például egy karakterlánc bináris ábrázolását adja vissza:`binary(‘some string’).`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Value<br /><br /> **Leírás**: Kötelező. A binárisra konvertált érték.|  
|dataUriToBinary|Egy adat URI azonosítójának bináris ábrázolását adja vissza. A következő kifejezés például egy karakterlánc bináris ábrázolását adja vissza:`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. A bináris ábrázolásra konvertálandó adat URI-ja.|  
|dataUriToString|Egy adat-URI karakterlánc-ábrázolását adja vissza. A következő kifejezés például egy bizonyos karakterláncot ad vissza:`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br />**Leírás**: Kötelező. A karakterlánc-ábrázolásra konvertálandó adat URI-ja.|  
|dataUri|Egy érték adaturi-JÁT adja vissza. A következő kifejezés például az adatok visszaadása:`text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: Value<br /><br />**Leírás**: Kötelező. Az adaturi formátumra konvertálandó érték.|  
|decodeBase64|Egy bemeneti bementi sztring sztringjét adja vissza. A következő kifejezés például a függvényt `some string`adja vissza:`decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Egy bemeneti bementi sztring sztringjét adja vissza.|  
|encodeUriComponent|URL – elkerüli az átadott karakterláncot. A következő kifejezés például a függvényt `You+Are%3ACool%2FAwesome`adja vissza:`encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. A menekülési URL-cím – nem biztonságos karakterek a következőből:.|  
|decodeUriComponent|Nem URL-cím – elkerüli az átadott karakterláncot. A következő kifejezés például a függvényt `You Are:Cool/Awesome`adja vissza:`encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. Az URL-cím nem biztonságos karaktereinek dekódolására szolgáló sztring.|  
|decodeDataUri|Egy bemeneti adat URI-karakterláncának bináris ábrázolását adja vissza. A következő kifejezés például a bináris ábrázolását `some string`adja vissza:`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br /> **Leírás**: Kötelező. A dataURI a dekódolni kívánt bináris ábrázolást.|  
|uriComponent|Egy érték URI-kódolású ábrázolását adja vissza. A függvény például a következő kifejezést adja vissza:`You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Paraméter részletei: Száma 1. név: Karakterlánc, leírás: Kötelező. Az URI-ként kódolt karakterlánc.|  
|uriComponentToBinary|Egy URI-kódolású karakterlánc bináris ábrázolását adja vissza. Az alábbi kifejezés például a következő bináris ábrázolását `You Are:Cool/Awesome`adja vissza:`uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Sztring<br /><br />**Leírás**: Kötelező. Az URI-kódolású karakterlánc.|  
|uriComponentToString|Egy URI-kódolású karakterlánc karakterlánc-ábrázolását adja vissza. A következő kifejezés például a függvényt `You Are:Cool/Awesome`adja vissza:`uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: Sztring<br /><br />**Leírás**: Kötelező. Az URI-kódolású karakterlánc.|  
|xml|Az érték XML-ábrázolásának visszaadása. A következő kifejezés például egy XML-tartalmat ad vissza, amelyet `'\<name>Alan\</name>'`a `xml('\<name>Alan\</name>')`:. Az XML-függvény a JSON-objektumok bemenetét is támogatja. A paramétert `{ "abc": "xyz" }` például XML-tartalomra konvertálja a rendszer`\<abc>xyz\</abc>`<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: Value<br /><br />**Leírás**: Kötelező. Az XML-re konvertálandó érték.|  
|XPath|Olyan XML-csomópontok tömbjét adja vissza, amelyek megfelelnek egy olyan érték XPath-kifejezésének, amelyet az XPath-kifejezés kiértékel.<br /><br />  **1. példa**<br /><br /> Tegyük fel, hogy a "P1" paraméter értéke a következő XML karakterlánc-ábrázolása:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Ez a kód:`xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> visszatérés<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> mivel<br /><br /> 2. Ez a kód:`xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> visszatérés<br /><br /> `13`<br /><br /> <br /><br /> **2. példa**<br /><br /> A következő XML-tartalomnak kell megadnia:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Ez a kód:`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> or<br /><br /> 2. Ez a kód:`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> adja vissza<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> és<br /><br /> 3. Ez a kód:`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> adja vissza<br /><br /> ``bar``<br /><br /> **Paraméter száma**: 1<br /><br />**Név**: Xml<br /><br />**Leírás**: Kötelező. Az az XML, amelyen az XPath-kifejezést ki kell értékelni.<br /><br /> **Paraméter száma**: 2<br /><br />**Név**: XPath<br /><br />**Leírás**: Kötelező. Az értékelendő XPath-kifejezés.|  
|array|Alakítsa át a paramétert egy tömbre.  A következő kifejezés például a függvényt `["abc"]`adja vissza:`array('abc')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Value<br /><br /> **Leírás**: Kötelező. Egy tömbre konvertált érték.|
|createArray|Létrehoz egy tömböt a paraméterekből.  A következő kifejezés például a függvényt `["a", "c"]`adja vissza:`createArray('a', 'c')`<br /><br /> **Paraméter száma**: 1... n<br /><br /> **Név**: Bármely n<br /><br /> **Leírás**: Kötelező. Egy tömbbe egyesíthető értékek.|

## <a name="math-functions"></a>Matematikai függvények  
 Ezek a függvények bármelyik típusú számhoz használhatók: **egész** számok és **lebegőpontok**.  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|hozzáadás|A két szám hozzáadásának eredményét adja vissza. Ez a függvény például a következőt adja vissza `20.333`:`add(10,10.333)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. Summand<br /><br /> **Leírás**: Kötelező. A **2. Summand**hozzáadandó szám.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. Summand<br /><br /> **Leírás**: Kötelező. Az **1. Summand**hozzáadandó szám.|  
|sub|A két szám kivonásának eredményét adja vissza. Ez a függvény például a következőt `-0.333`adja vissza:<br /><br /> `sub(10,10.333)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Kisebbítendőt<br /><br /> **Leírás**: Kötelező. Az a szám, amelyből a **kivonandó** el lett távolítva.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Kivonandó<br /><br /> **Leírás**: Kötelező. A **kisebbítendőt**eltávolítandó szám.|  
|mul|A két szám szorzásának eredményét adja vissza. A következő függvény például a következőket `103.33`adja vissza:<br /><br /> `mul(10,10.333)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: 1. Multiplicand<br /><br /> **Leírás**: Kötelező. A **Multiplicand 2** és közötti szorzásának száma.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: 2. Multiplicand<br /><br /> **Leírás**: Kötelező. A **Multiplicand 1** és közötti szorzásának száma.|  
|div|A két szám osztásának eredményét adja vissza. A következő függvény például a következőket `1.0333`adja vissza:<br /><br /> `div(10.333,10)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Osztalék<br /><br /> **Leírás**: Kötelező. Az **osztó**által elosztani kívánt szám.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Osztó<br /><br /> **Leírás**: Kötelező. A **osztalék** felosztására szolgáló szám.|  
|mod|A maradék eredményét adja vissza a két szám (többtényezős) osztása után. A következő kifejezés például a függvényt `2`adja vissza:<br /><br /> `mod(10,4)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Osztalék<br /><br /> **Leírás**: Kötelező. Az **osztó**által elosztani kívánt szám.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Osztó<br /><br /> **Leírás**: Kötelező. A **osztalék** felosztására szolgáló szám. A felosztás után a maradékot a rendszer elvégzi.|  
|perc|A függvény hívásához két különböző minta létezik: `min([0,1,2])`Itt a minimum egy tömböt vesz igénybe. Ez a kifejezés `0`a értéket adja vissza. Másik lehetőségként a függvény vesszővel tagolt értékeket tartalmazó listát is igénybe vehet:  `min(0,1,2)`Ez a függvény a 0 értéket is visszaadja. Vegye figyelembe, hogy minden értéknek számnak kell lennie, tehát ha a paraméter egy tömb, csak számokat kell tartalmaznia.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Gyűjtemény vagy érték<br /><br /> **Leírás**: Kötelező. Az értékek tömbje lehet a minimális érték vagy a készlet első értékének megkeresése.<br /><br /> **Paraméter száma**: 2... *n*<br /><br /> **Név**: *N* érték<br /><br /> **Leírás**: Nem kötelező. Ha az első paraméter értéke, akkor további értékeket adhat át, és a rendszer az összes átadott érték minimumát adja vissza.|  
|max.|A függvény hívásához két különböző minta létezik:`max([0,1,2])`<br /><br /> Itt Max egy tömböt vesz igénybe. Ez a kifejezés `2`a értéket adja vissza. Másik lehetőségként a függvény vesszővel tagolt értékeket tartalmazó listát is igénybe vehet:  `max(0,1,2)`Ez a függvény a 2 értéket adja vissza. Vegye figyelembe, hogy minden értéknek számnak kell lennie, tehát ha a paraméter egy tömb, csak számokat kell tartalmaznia.<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Gyűjtemény vagy érték<br /><br /> **Leírás**: Kötelező. Az értékek tömbje lehet, hogy megkeresse a maximális értéket, vagy egy készlet első értékét.<br /><br /> **Paraméter száma**: 2... *n*<br /><br /> **Név**: *N* érték<br /><br /> **Leírás**: Nem kötelező. Ha az első paraméter értéke, akkor további értékeket adhat át, és a rendszer az összes átadott érték maximális számát adja vissza.|  
|tartomány| Egy adott számból kezdődő egész számok tömbjét hozza létre, és meghatározza a visszaadott tömb hosszát. Ez a függvény például a következőt adja vissza `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Kezdő index<br /><br /> **Leírás**: Kötelező. Ez az első egész szám a tömbben.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Count<br /><br /> **Leírás**: Kötelező. A tömbben lévő egész számok száma.|  
|Rand| Egy véletlenszerű egész számot hoz létre a megadott tartományon belül (mindkét végponton. Például a következőt térhet vissza `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Minimális<br /><br /> **Leírás**: Kötelező. A visszaadott legkisebb egész szám.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Maximum<br /><br /> **Leírás**: Kötelező. A visszaadott legmagasabb egész szám.|  
  
## <a name="date-functions"></a>Dátum függvények  
  
|Függvény neve|Leírás|  
|-------------------|-----------------|  
|utcnow|Az aktuális időbélyeget adja vissza karakterláncként. Például `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Formátum<br /><br /> **Leírás**: Nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|addSeconds|Egész számú másodpercet ad hozzá az átadott karakterlánc-időbélyeghez. A másodpercek száma lehet pozitív vagy negatív. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Timestamp<br /><br /> **Leírás**: Kötelező. Az időpontot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Másodperc<br /><br /> **Leírás**: Kötelező. A hozzáadandó másodpercek száma. Negatív lehet a kivonás másodpercben.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: Formátum<br /><br /> **Leírás**: Nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|addminutes|Egész számú percet ad hozzá az átadott karakterlánc-időbélyeghez. A percek száma lehet pozitív vagy negatív. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Timestamp<br /><br /> **Leírás**: Kötelező. Az időpontot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: perc<br /><br /> **Leírás**: Kötelező. A hozzáadandó percek száma. A percek kivonása negatív lehet.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: Formátum<br /><br /> **Leírás**: Nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|addhours|Egész számú órát ad hozzá az átadott karakterlánc-időbélyeghez. Az órák száma lehet pozitív vagy negatív. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Timestamp<br /><br /> **Leírás**: Kötelező. Az időpontot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Óra<br /><br /> **Leírás**: Kötelező. A hozzáadandó órák száma. Az órák kivonása negatív lehet.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: Formátum<br /><br /> **Leírás**: Nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|addDays|Egész számú napot ad hozzá az átadott karakterlánc-időbélyeghez. A napok száma lehet pozitív vagy negatív. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Timestamp<br /><br /> **Leírás**: Kötelező. Az időpontot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Nap<br /><br /> **Leírás**: Kötelező. A hozzáadandó napok száma. A kivonási napokat negatívan lehet kivonni.<br /><br /> **Paraméter száma**: 3<br /><br /> **Név**: Formátum<br /><br /> **Leírás**: Nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  
|formatDateTime|Egy dátum formátumú karakterláncot ad vissza. Az eredmény az ISO 8601 formátumban megadott karakterlánc ("o") alapértelmezés szerint, kivéve, ha meg van határozva a formátum megadása. Például `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Paraméter száma**: 1<br /><br /> **Név**: Date<br /><br /> **Leírás**: Kötelező. A dátumot tartalmazó sztring.<br /><br /> **Paraméter száma**: 2<br /><br /> **Név**: Formátum<br /><br /> **Leírás**: Nem kötelező. Egy [formázó karakter](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) vagy egy [egyéni formázási minta](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , amely az időbélyeg értékének formázását jelzi. Ha nincs megadva a formátum, a rendszer az ISO 8601 formátumot ("o") használja.|  

## <a name="next-steps"></a>További lépések
A kifejezésekben használható rendszerváltozók listáját a rendszerváltozók című [](control-flow-system-variables.md)részben tekintheti meg.
