---
title: Python-bővíthetőségi használata az Azure Machine Learning adat előkészített |} A Microsoft Docs
description: Ez a dokumentum nyújt áttekintést, és néhány részletes példa a Python-kód használatát az adat-előkészítési funkcióinak bővítése érdekében
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ROBOTS: NOINDEX
ms.openlocfilehash: eceeeb30331031c51e5208e301441d17096b4a00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972980"
---
# <a name="data-preparations-python-extensions"></a>Adat-Előkészítések Python-bővítmények

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Ehhez adja meg a beépített funkciók között funkciókorlátozásokat módja az Azure Machine Learning adat előkészített magában foglalja a bővíthetőségi több szinten. Ez a dokumentum azt szerkezeti Python-szkriptet a bővíthetőséget. 

## <a name="custom-code-steps"></a>Egyéni kód lépések 
Adat-Előkészítések rendelkezik, ahol a felhasználók is írhat kódot a következő egyéni lépéseket:

* Oszlop hozzáadása
* Speciális szűrő
* Adatfolyam átalakítása
* Partíció átalakítása

## <a name="code-block-types"></a>Blokk típusú kód 
Az egyes lépéseket letiltása a kétféle kódot támogatja. Először is nyújtunk támogatást hajtja végre, mert egy operációs rendszer nélküli helyreállításra Python kifejezés. Másodszor is nyújtunk támogatást egy Python-modult, ahol egy adott függvény ismert aláírással nevezzük a kódban, Önnek kell letöltenie.

Ha például egy olyan új oszlop, amely kiszámítja a napló egy másik oszlop a következő két módon adhat hozzá:

Kifejezés 

```python    
    math.log(row["Score"])
```

Modul 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


Az oszlop hozzáadása átalakítás modul módban vár a hívott függvény található `newvalue` , amely fogad egy sor változót, és az oszlop értékét adja vissza. Ez a modul lehetnek más függvények, importálja, stb-Python-kód mennyisége.

Minden bővítmény pont részleteit az alábbi szakaszok ismertetik. 

## <a name="imports"></a>Import 
Ha a blokk típusú kifejezés használ, továbbra is felvehetőek **importálása** -utasítások használatával a kód. Minden csoportban kell lenniük a kód a felső sorok.

Javítsa ki 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Hiba  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Ha blokk modultípus használja, minden a szokásos Python való használatának szabályait, kövesse a **importálása** utasítást. 

## <a name="default-imports"></a>Alapértelmezett import
Az alábbi importálásokat a csomagban foglalt, és használható a kód mindig állnak. Nem kell újra importálja. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Új csomag telepítése
Szeretne használni egy csomagot, amely alapértelmezés szerint nincs telepítve, akkor először telepítheti az adat-Előkészítések használó környezetekben. A telepítéshez kell mind a helyi gépen, és futtatni kívánt számítási céloknak végezni.

Egy számítási célnak a csomagok telepítése, akkor módosítsa a conda_dependencies.yml fájlt a aml_config mappában található a projekt gyökerében.

### <a name="windows"></a>Windows 
Keresse meg a helyen található Windows, keresse meg a Python és a parancsfájlok könyvtár alkalmazásspecifikus telepítését. Az alapértelmezett hely a következő:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Ezután futtassa a következő parancsok egyikét: 

`conda install <libraryname>` 

vagy 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
A Mac számítógépen hely megkereséséhez, keresse meg a Python és a parancsfájlok könyvtár alkalmazásspecifikus telepítését. Az alapértelmezett hely a következő: 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Ezután futtassa a következő parancsok egyikét: 

`./conda install <libraryname>`

vagy 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Egyéni modulok használata
Adatfolyam átalakítása (szkript), a írja be a következő Python-kód

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

Az oszlop hozzáadása (szkript), állítsa be a kódot kódblokk típusa = modul, és a következő Python-kód írása

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
A különböző végrehajtási környezetekben (helyi, Docker, a Spark) jó helyen abszolút elérési út mutasson. Előfordulhat, hogy használni kívánt "os.getcwd() + relativePath" megkeresését.


## <a name="column-data"></a>Oszlopok adatainak 
Oszlop adatok elérhetők egy sor felépítését vagy kulcs-érték jelölés használatával. Oszlop neve szóközt vagy különleges karaktert tartalmaz nem érhető el a felépítését. A `row` változó mindig meg kell határozni, mindkét mód Python-bővítmények (modul és kifejezés). 

Példák 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>Oszlop hozzáadása 
### <a name="purpose"></a>Cél
Az oszlop hozzáadása bővítmény pont lehetővé teszi egy olyan új oszlop kiszámításához Python írása. Írt hozzáfér a teljes sort. Egy új oszlop értékét minden egyes sorára szükséges. 

### <a name="how-to-use"></a>A használat módja
Ez a bővítmény pont az oszlop hozzáadása (szkript)-blokk használatával adhat hozzá. A legfelső szintű elérhető **átalakítások** is, a menüben a **oszlop** helyi menü. 

### <a name="syntax"></a>Szintaxis
Kifejezés

```python
    math.log(row["Score"])
```

Modul 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Speciális szűrő
### <a name="purpose"></a>Cél 
A speciális szűrési bővítmény pont lehetővé teszik egyéni szűrő írását. Hozzáfér az egész sort, és a kódot kell visszaadnia a True (például a sor) vagy False (kizárás a sor). 

### <a name="how-to-use"></a>A használat módja
Ez a bővítmény pont a speciális szűrőt (szkript)-blokk használatával adhat hozzá. A legfelső szintű elérhető **átalakítások** menü. 

### <a name="syntax"></a>Szintaxis

Kifejezés

```python
    row["Score"] > 95
```

Modul  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Adatfolyam átalakítása
### <a name="purpose"></a>Cél 
Az adatfolyam átalakítása bővítmény pont lehetővé teszi az adatfolyam teljesen alakíthatja. Rendelkezik, amely tartalmazza az oszlopokat és sorokat, amelyek még feldolgozásra Pandas dataframe való hozzáférést. A kód az új adatokat Pandas dataframe kell visszaadnia. 

>[!NOTE]
>A Python a memóriába az adatok a Pandas dataframe a Ez a bővítmény használata esetén. 
>
>A Spark az alakzatot egyetlen munkavégző csomópont gyűjtött összes adat. Nagyon nagy méretű adatok esetén egy feldolgozó előfordulhat, hogy elfogy a memória. Körültekintően használja.

### <a name="how-to-use"></a>A használat módja 
Ez a bővítmény pont az adatfolyam átalakítása (szkript)-blokk használatával adhat hozzá. A legfelső szintű elérhető **átalakítások** menü. 
### <a name="syntax"></a>Szintaxis 

Kifejezés

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modul 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Partíció átalakítása  
### <a name="purpose"></a>Cél 
A partíció átalakítása bővítmény pont eloszlása az adatfolyam átalakítása teszi lehetővé. Rendelkezik, amely tartalmazza az összes oszlopot és sort, hogy a partíció Pandas dataframe való hozzáférést. A kód az új adatokat Pandas dataframe kell visszaadnia. 

>[!NOTE]
>A Python előfordulhat, hogy végül egy olyan partíciót, vagy több partíciót az adatok méretétől függően. A Spark dolgozik, amely tárolja az adatokat egy partíció egy adott munkavégző csomóponton dataframe. Mindkét esetben nem biztos, hogy a teljes adatkészlet elérését. 


### <a name="how-to-use"></a>A használat módja
Ez a bővítmény pont a partíció átalakítása (szkript) blokk használatával adhat hozzá. A legfelső szintű elérhető **átalakítások** menü. 

### <a name="syntax"></a>Szintaxis 

Kifejezés 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modul 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Hibás értékek  
Az adat-Előkészítések hibaértékek fogalma létezik. 

Egyéni Python-kódban hibaértékek hibát lehetőség. Nevű Python osztály példányai `DataPrepError`. Ez az osztály egy Python-kivétel burkolja, és néhány tulajdonságát. A tulajdonságok a következő hiba történt az eredeti érték feldolgozásakor, valamint az eredeti értékre vonatkozó adatokat tartalmaznak. 


### <a name="datapreperror-class-definition"></a>Az osztálykiterjesztések definíciója DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Az adat-Előkészítések Python-keretrendszer egy DataPrepError létrehozása általában néz ki: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>A használat módja 
Ez akkor lehetséges, ha a Python-bővítmény pontján DataPrepErrors létrehozásához visszatérési értékként az előző létrehozási módjának használatával futtatja. Sokkal több valószínű, hogy DataPrepErrors ütközött egy bővítmény pontján adatot dolgoz fel. Ezen a ponton a Python-kódok kezelnie kell a DataPrepError érvényes adattípusú értékként.

#### <a name="syntax"></a>Szintaxis 
Kifejezés 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Modul 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
