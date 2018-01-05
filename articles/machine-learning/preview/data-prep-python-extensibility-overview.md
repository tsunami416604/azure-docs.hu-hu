---
title: "Python bővítési használata Azure Machine Learning adatok előkészített |} Microsoft Docs"
description: "Ez a dokumentum nyújt áttekintését és részletes néhány olyan Python kód segítségével történő bővítése adatok előkészítése"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 3c3864480d2fcba4f6d388d4e0d00b917cb62d2b
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="data-preparations-python-extensions"></a>Előkészített Python adatkiterjesztések
Beépített szolgáltatásai között a funkció hézagok kitöltése módja Azure Machine Learning adatok előkészített bővítési több szinten tartalmazza. Ebben a dokumentumban a Python-parancsfájl bővíthetőséget szerkezeti azt. 

## <a name="custom-code-steps"></a>Egyéni kód lépései 
Adatok előkészített rendelkezik a következő egyéni lépéseket, ahol a felhasználók is írhat kódot:

* Fájl olvasó *
* Író *
* Oszlop hozzáadása
* Speciális szűrő
* Adatfolyam átalakítása
* Átalakítás partíció

* Ezeket a lépéseket jelenleg nem támogatottak a Spark végrehajtása.

## <a name="code-block-types"></a>Kód blokk típusok 
Az egyes lépések két kód blokk típus támogatott. Először is támogatott egy operációs rendszer Python kifejezést, mert a végrehajtása. Második, ahol egy ismert aláírás az adott funkciót ezt nevezik a kódot megadnia a Python modul támogatott.

Például egy olyan új oszlop, amely kiszámítja a napló egy másik oszlop a következő két módon adhat hozzá:

Kifejezés 

```python    
    math.log(row["Score"])
```

Modul 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


Az oszlop hozzáadása átalakító modul módban vár a hívott függvény található `newvalue` , amely fogad egy sor változó, és az oszlop értékét adja vissza. Ez a modul tartalmazhatnak mennyisége Python kódját a más funkciók, importálja, stb.

A következő szakaszok minden bővítmény pont részleteit ismerteti. 

## <a name="imports"></a>Importálása 
Ha a kifejezés blokk típus használata, továbbra is felvehetőek **importálása** utasítást, hogy a kódot. Minden csoportban kell lenniük a kód a felső sorokban.

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
 
 
A modul blokktípus használatakor minden normál Python való használatának szabályait követheti a **importálása** utasítást. 

## <a name="default-imports"></a>Alapértelmezett importálása
Az alábbi importálásokat a rendszer mindig szerepel, és használható a kódban. Nem kell újra importálja. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Új csomagok telepítése
Alapértelmezés szerint nem telepített csomag használatára, először telepítse az adatok előkészített használó környezetekben. A telepítés azért van szükség, mind a helyi számítógépen, és minden számítási célokon futtatni kívánt.

Számítási célja a csomagok telepítéséhez kell módosítani a conda_dependencies.yml fájlt a aml_config mappában a projekt gyökerében található.

### <a name="windows"></a>Windows 
A Windows helyen található, a Python és a parancsfájlok könyvtárát alkalmazásspecifikus telepítése található. Az alapértelmezett hely a következő:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

Ezután futtassa az alábbi parancsok egyikét: 

`conda install <libraryname>` 

vagy 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
A Mac helyen található, a Python és a parancsfájlok könyvtárát alkalmazásspecifikus telepítése található. Az alapértelmezett hely a következő: 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

Ezután futtassa az alábbi parancsok egyikét: 

`./conda install <libraryname>`

vagy 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Az egyéni modulok használata
Átalakítás Adatfolyamblokk (parancsfájl) írja be python kódot ehhez hasonló:

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

Oszlop hozzáadása (parancsfájl), állítson be kód blokktípus modul = és python írás a következő kódot:

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
A különböző végrehajtási környezeteket (helyi, docker spark) mutasson a abszolút elérési utat a megfelelő helyen. Előfordulhat, hogy használni kívánt "os.getcwd() + relativePath" megkeresését.


## <a name="column-data"></a>Oszlop adattípusa 
Oszlop adattípusa sor felépítését, vagy a kulcs-érték jelölés használatával elérhető. Oszlop neve szóközt vagy speciális karaktereket tartalmaz pontjelöléssel nem lehet elérni. A `row` változó mindig meg kell határozni, Python-bővítmények (modul és kifejezés) mindkét üzemmódban. 

Példák 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>Fájl-olvasó 
### <a name="purpose"></a>Cél 
A fájl olvasó bővítmény pont lehetővé teszi teljes vezérlés folyamata a fájl olvasásakor az adatfolyam. A rendszer meghívja a kódot, és adja át a listában, amely fel kell dolgozni fájlok. A kódot kell létrehozni, és térjen vissza a Pandas dataframe. 

>[!NOTE]
>A bővítmény pont nem érhető el a Spark. 


### <a name="how-to-use"></a>A használat módja 
A bővítmény pontot hozzáférhet a **nyissa meg az adatforrás** varázsló. Válasszon **fájl** első lapján, majd válassza a fájl helye. Az a **Fájlparaméterekre válasszon** lap a **Fájltípus** legördülő menüben válassza ki **egyéni fájl (parancsfájl)**. 

A kód egy "df", amely leírja a fájlokat szeretné olvasni nevű Pandas dataframe kap. Ha úgy döntött, hogy több fájlt tartalmazó könyvtár megnyitásához, akkor a dataframe egynél több sorban tartalmazza.  

A dataframe a következő oszlopokkal rendelkezik:

- Elérési út: A fájl olvasását.
- PathHint: Megtudhatja, ahol a fájl megtalálható-e. Értékek: Helyi AzureBlobStorage és AzureDataLakeStorage.
- AuthenticationType: A fájl elérésére használt hitelesítés típusa. Értékek: None, SasToken és OAuthToken.
- AuthenticationValue: None, vagy a token által használható tartalmazza.

### <a name="syntax"></a>Szintaxis 
Kifejezés 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Modul  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>író 
### <a name="purpose"></a>Cél 
Az író bővítmény pont lehetővé teszi teljes mértékben a folyamat az adatok írása az adatfolyam a vezérlő. A rendszer meghívja a kódot, és egy dataframe megfelel. A kód az adatok írása, de azt szeretné, használhatja a dataframe. 

>[!NOTE]
>Az író bővítmény pont nem érhető el a Spark.


### <a name="how-to-use"></a>A használat módja 
A bővítmény pont a írási (parancsfájl) adatfolyamblokk használatával adhat hozzá. Érhető el a legfelső szintű **átalakítások** menü.

### <a name="syntax"></a>Szintaxis 
Kifejezés

```python
    df.to_csv('c:\\temp\\output.csv')
```

Modul

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Egyéni írási blokk közepén lépések listáját is létezik. Ha egy modul használata a írási kell függvénynek a dataframe, amely a bemeneti a következő lépéssel. 

## <a name="add-column"></a>Oszlop hozzáadása 
### <a name="purpose"></a>Cél
Az oszlop hozzáadása bővítmény pont lehetővé teszi egy olyan új oszlop kiszámításához Python írását. Írt kódot hozzáférhet a teljes sor. Minden egyes sorára olyan új oszlop értékét kell. 

### <a name="how-to-use"></a>A használat módja
A bővítmény pont az oszlop hozzáadása (parancsfájl) blokk használatával adhat hozzá. Érhető el a legfelső szintű **átalakítások** , valamint az a menü a **oszlop** helyi menüje. 

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
A speciális szűrési bővítmény pont lehetővé teszi egy egyéni szűrő írását. Rendelkezik hozzáféréssel a teljes sort, és a kódot kell visszaadnia igaz (beleértve a sor) vagy False (zárja ki a sor). 

### <a name="how-to-use"></a>A használat módja
A bővítmény pont a speciális (parancsfájl) szűrőblokk használatával adhat hozzá. Érhető el a legfelső szintű **átalakítások** menü. 

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
Az adatfolyam átalakítása bővítmény pont lehetővé teszi teljesen átalakítási az adatfolyam. Egy Pandas dataframe, amely tartalmazza az oszlopok és sorok, amelyekre még feldolgozás hozzáférése. A kód egy új adatokkal Pandas dataframe kell visszaadnia. 

>[!NOTE]
>A Python minden van az adatok betöltését a memóriába a Pandas dataframe használata ehhez a kiterjesztéshez. 
>
>A Spark egyetlen munkavégző csomópont alakzatot gyűjtött összes adat. Ha az adatok nagy, egy munkavégző előfordulhat, hogy elfogy a memória. Körültekintően használja.

### <a name="how-to-use"></a>A használat módja 
A bővítmény pont az átalakítás (parancsfájl) adatfolyamblokk használatával adhat hozzá. Érhető el a legfelső szintű **átalakítások** menü. 
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
  

## <a name="transform-partition"></a>Átalakítás partíció  
### <a name="purpose"></a>Cél 
A partíció átalakítási bővítmény pont lehetővé teszi az adatfolyam partíciójának átalakító. Egy Pandas dataframe, amely tartalmazza az oszlopok és sorok adott partíció hozzáférése. A kód egy új adatokkal Pandas dataframe kell visszaadnia. 

>[!NOTE]
>A Python akkor előfordulhat, hogy végül egy olyan partíciót, vagy több partíciót, az adatok méretétől függően. A Spark dolgozunk a dataframe, amely tárolja az adatokat a partíciókhoz egy adott munkavégző csomóponton. Mindkét esetben nem biztos, hogy rendelkezik-e hozzáféréssel a teljes adatkészletet. 


### <a name="how-to-use"></a>A használat módja
A bővítmény pont az átalakítás partíció (parancsfájl) blokk használatával adhat hozzá. Érhető el a legfelső szintű **átalakítások** menü. 

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
### <a name="error-values"></a>Hiba értékek  
Az adatok előkészített hibaértékek fogalma létezik. 

Úgy is hibaértékek egyéni Python-kódban előforduló. Nevű Python osztály példányai `DataPrepError`. Ez az osztály egy Python kivétel becsomagolja, és tulajdonságok néhány rendelkezik. A Tulajdonságok tartalmaz információt a következő hiba történt az eredeti érték feldolgozásakor, valamint az eredeti érték. 


### <a name="datapreperror-class-definition"></a>DataPrepError osztálydefiníció
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Az előkészített Python keretében egy DataPrepError létrehozását általában néz ki: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>A használat módja 
Lehetőség létrehozni DataPrepErrors visszatérési értékként az előző létrehozási módszer használatával egy bővítmény ponton Python futtatásakor. Sokkal valószínű, hogy az adatok egy bővítmény ponton feldolgozásakor hibát DataPrepErrors. Ezen a ponton a egyéni Python-kódot kell kezelni egy DataPrepError érvényes adattípusú értékként.

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
