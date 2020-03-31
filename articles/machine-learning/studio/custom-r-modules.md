---
title: Egyéni R-modulok definiálása
titleSuffix: ML Studio (classic) - Azure
description: Ez a témakör azt ismerteti, hogyan lehet egy egyéni R Studio (klasszikus) létrehozására és üzembe helyezésére. Elmagyarázza, hogy mik azok az egyéni R modulok, és milyen fájlokat használnak azok definiálására.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 5b8dab14a9416795eccef1f71988a048c8bedb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218170"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Egyéni R-modulok definiálása az Azure Machine Learning Studio-hoz (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ez a témakör azt ismerteti, hogyan lehet egy egyéni R Studio (klasszikus) létrehozására és üzembe helyezésére. Elmagyarázza, hogy mik azok az egyéni R modulok, és milyen fájlokat használnak azok definiálására. Bemutatja, hogyan hozhat létre a modulokat definiáló fájlokat, és hogyan regisztrálhatja a modult a Machine Learning-munkaterületen történő üzembe helyezéshez. Az egyéni modul definíciójában használt elemek és attribútumok ezután részletesebben ismertetik. Hogyan kell használni a kiegészítő funkciók és fájlok és több kimenet is tárgyalt. 



## <a name="what-is-a-custom-r-module"></a>Mi az egyéni R modul?
Az **egyéni modul** egy felhasználó által definiált modul, amely feltölthető a munkaterületre, és az Azure Machine Learning Studio (klasszikus) kísérlet részeként hajtható végre. Az **egyéni R modul** egy egyéni modul, amely egy felhasználó által definiált R függvényt hajt végre. **Az R** a statisztikai számítástechnika és a grafika programozási nyelve, amelyet a statisztikusok és az adatszakértők széles körben használnak algoritmusok végrehajtására. Jelenleg az R az egyetlen nyelv, amelyet az egyéni modulok támogatnak, de további nyelvek támogatása a későbbi kiadásokra van ütemezve.

Az egyéni modulok **első osztályú állapottal** rendelkeznek az Azure Machine Learning Studio (klasszikus) abban az értelemben, hogy ugyanúgy használhatók, mint bármely más modul. Ezek végrehajthatók más modulokkal, amelyek szerepelnek a közzétett kísérletekben vagy a vizualizációkban. A modul által megvalósított algoritmus, a használandó bemeneti és kimeneti portok, a modellezési paraméterek és más különböző futásidejű viselkedések felett rendelkezik. Az egyéni modulokat tartalmazó kísérlet az Azure AI-galériában is közzétehető a könnyebb megosztás érdekében.

## <a name="files-in-a-custom-r-module"></a>Fájlok egyéni R modulban
Az egyéni R-modult egy .zip fájl határozza meg, amely legalább két fájlt tartalmaz:

* A modul által kitett R függvényt megvalósító **forrásfájl**
* **Xml-definíciós fájl,** amely az egyéni modul felületét írja le

További kiegészítő fájlok is szerepelhetnek a .zip fájlban, amely az egyéni modulból elérhető funkciókat biztosít. Ezt a beállítást az **XML-definíciós fájl Elemei** című referenciaszakasz **Argumentumok** részében tárgyalja a rövid útmutatópéldát követően.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Rövid útmutató példa: egyéni R-modul definiálása, csomagolása és regisztrálása
Ez a példa bemutatja, hogyan hozhat létre egy egyéni R-modul által igényelt fájlokat, csomagolja őket egy zip-fájlba, majd regisztrálja a modult a Machine Learning-munkaterületen. A példa zip csomag és a minta fájlokletölthető [Download CustomAddRows.zip fájlt](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>A forrásfájl
Vegyünk példát egy **egyéni sorok hozzáadása** modul, amely módosítja a szabványos végrehajtását a Sorok **hozzáadása** modul használt összefűzése sorok (megfigyelések) két adatkészletek (adatkeretek). A szabványos **Sorok hozzáadása** modul hozzáfűzi a sorokat a második bemeneti `rbind` adatkészlet végén az első bemeneti adatkészlet segítségével az algoritmus. A testreszabott `CustomAddRows` függvény hasonlóképpen két adatkészletet fogad el, de egy logikai swap paramétert is elfogad további bemenetként. Ha a lapozó paraméter **értéke HAMIS,** akkor ugyanazt az adatkészletet adja vissza, mint a szabványos implementáció. Ha azonban a lapozó paraméter **IGAZ,** a függvény az első bemeneti adatkészlet sorait fűzi a második adatkészlet végéhez. Az **Egyéni Sorok hozzáadása** modul által elérhetőr `CustomAddRows` függvény megvalósítását tartalmazó CustomAddRows.R fájl a következő R-kóddal rendelkezik.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>Az XML-definíciós fájl
Az Azure `CustomAddRows` Machine Learning Studio (klasszikus) modulként való elérhetővé tenni ezt a funkciót egy XML-definíciós fájlt kell létrehozni, amely megadja, hogy az **egyéni sorok hozzáadása** modul hogyan nézzen ki és viselkedjen. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


Fontos megjegyezni, hogy az **XML-fájlbemeneti** és **Arg-elemeinek** **azonosító** attribútumainak értékének pontosan meg kell egyeznie a CustomAddRows.R fájlR-kódjának függvényparaméter-nevével: (*dataset1*, *dataset2*és a példában szereplő *felcserélés).* Hasonlóképpen a **Language** elem **entryPoint** attribútumának értékének pontosan meg kell egyeznie az R parancsfájl ban lévő függvény nevével: ( A példában*található CustomAddRows).* 

Ezzel szemben a **Kimenetelem** **id** attribútuma nem felel meg az R parancsfájl egyetlen változójának sem. Ha egynél több kimenetre van szükség, egyszerűen adja vissza az R függvény egy listáját, amelynek eredménye *ugyanabban a sorrendben* van, mint a **Kimenetek** elemek deklarálva az XML-fájlban.

### <a name="package-and-register-the-module"></a>A modul csomagolása és regisztrálása
Mentse ezt a két fájlt *CustomAddRows.r* és *CustomAddRows.xml* néven, majd zip-elje a két fájlt egy *CustomAddRows.zip* fájlba.

Ha regisztrálni szeretné őket a Machine Learning-munkaterületen, nyissa meg a munkaterületet az Azure Machine Learning Studio (klasszikus) alkalmazásban, kattintson az alján a **+ÚJ** gombra, és válassza a **MODULE -> FROM ZIP PACKAGE** lehetőséget az új egyéni sorok **hozzáadása** modul feltöltéséhez.

![Zip feltöltése](./media/custom-r-modules/upload-from-zip-package.png)

Az **egyéni sorok hozzáadása** modul most már készen áll a Machine Learning-kísérletek általi elérésére.

## <a name="elements-in-the-xml-definition-file"></a>Az XML-definíciós fájl elemei
### <a name="module-elements"></a>Modulelemek
A **modul** elem egyéni modul definiálására szolgál az XML-fájlban. Több modul definiálható egy XML-fájlban több **modulelem** használatával. A munkaterület minden moduljának egyedi névvel kell rendelkeznie. Regisztráljon egy olyan egyéni modult, amelynek neve megegyezik egy meglévő egyéni modul nevével, és lecseréli a meglévő modult az újra. Az egyéni modulok azonban ugyanazzal a névvel regisztrálhatók, mint egy meglévő Azure Machine Learning Studio (klasszikus) modul. Ha igen, akkor a modulpaletta **Egyéni** kategóriájában jelennek meg.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


A **Modul** elemen belül két további választható elemet adhat meg:

* a modulba ágyazott **tulajdonoselem**  
* egy **Leírás** elem, amely olyan szöveget tartalmaz, amely a modul gyors súgójában jelenik meg, és amikor a Machine Learning felhasználói felületén a modul fölé viszi az egérmutatót.

A modulelemek karakterkorlátaira vonatkozó szabályok:

* A **Modul** elemben lévő **névattribútum** értéke nem haladhatja meg a 64 karakter hosszúságot. 
* A **Leírás** elem tartalma nem haladhatja meg a 128 karakter hosszúságot.
* A **Tulajdonos** elem tartalma nem haladhatja meg a 32 karakter hosszúságot.

A modul eredményei lehetnek determinisztikusak vagy nem determinisztikusak.** Alapértelmezés szerint minden modul determinisztikusnak minősül. Ez azt, hogy a bemeneti paraméterek és adatok változatlan készletét tekintve a modulnak ugyanazokat az eredményeket kell visszaadnia eacRAND vagy a függvény futtatási ideje. Ebben a viselkedésben az Azure Machine Learning Studio (klasszikus) csak akkor fut le determinisztikusként megjelölt modulokat, ha egy paraméter vagy a bemeneti adatok megváltoztak. A gyorsítótárazott eredmények visszaadása a kísérletek sokkal gyorsabb végrehajtását is biztosítja.

Vannak olyan függvények, amelyek nem determinisztikusak, például a RAND vagy az aktuális dátumot vagy időt visszaadó függvény. Ha a modul nem determinisztikus függvényt használ, megadhatja, hogy a modul nem determinisztikus, ha a választható **isDeterministic** attribútumot **FALSE-ra**állítja be. Ez biztosítja, hogy a modul a kísérlet futtatásakor újrafusson, még akkor is, ha a modul bemenete és paraméterei nem változtak. 

### <a name="language-definition"></a>Nyelv definíciója
Az XML-definíciós fájl **Nyelvi** eleme az egyéni modul nyelvének megadására szolgál. Jelenleg az R az egyetlen támogatott nyelv. A **sourceFile** attribútum értékének annak az R-fájlnak a nevének kell lennie, amely a modul futtatásakor hívandó függvényt tartalmazza. Ennek a fájlnak a zip csomag részét kell, hogy legyen. A **entryPoint** attribútum értéke a hívott függvény neve, és meg kell egyeznie a forrásfájlban definiált érvényes függvénysel.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portok
Az egyéni modul bemeneti és kimeneti portjai az XML-definíciós fájl **Portok** szakaszának gyermekelemeiben vannak megadva. Ezeknek az elemeknek a sorrendje határozza meg a felhasználók által tapasztalt elrendezést (UX). Az XML-fájl **Port elemében** felsorolt első **gyermekbemenet** vagy **-kimenet** lesz a Gépi tanulási felhasználói felület bal szélső bemeneti portja.
Minden bemeneti és kimeneti portrendelkezhet egy választható **Description** gyermekelemmel, amely megadja azt a szöveget, amely akkor jelenik meg, amikor az egérmutatót a Machine Learning felhasználói felületének portja fölé viszi.

**Portszabályok**:

* A **bemeneti és kimeneti portok** maximális száma egyenként 8.

### <a name="input-elements"></a>Bemeneti elemek
A bemeneti portok lehetővé teszik az adatok átadást az R függvénynek és a munkaterületnek. A bemeneti portok által támogatott **adattípusok** a következők: 

**DataTable:** Ez a típus adat.frame-ként kerül át az R függvénybe. Valójában a Machine Learning által támogatott és a **DataTable-vel** kompatibilis bármely típus (például CSV-fájlok vagy ARFF-fájlok) automatikusan adat.frame-é alakul át. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

Az egyes **DataTable** beviteli portokhoz társított **id** attribútumnak egyedi értékkel kell rendelkeznie, és ennek az értéknek meg kell egyeznie az R függvényben a megfelelő elnevezett paraméterrel.
A kísérletben bemenetként át nem adott nem kötelező **DataTable** portok értéke az R függvénynek átadott NULL értékkel **rendelkezik,** és a választható zip-portokat a rendszer figyelmen kívül hagyja, ha a bemenet nincs csatlakoztatva. Az **isOptional** attribútum nem kötelező mind a **DataTable,** mind a **Zip** típusok esetében, és alapértelmezés szerint *hamis.*

**Irányítószám:** Az egyéni modulok bemenetként elfogadnak egy zip fájlt. Ez a bemenet a funkció R munkakönyvtárába van csomagolva

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Az egyéni R-modulok esetében a Zip-port azonosítójának nem kell megfelelnie az R függvény egyetlen paraméterének sem. Ennek az az oka, hogy a zip-fájl automatikusan kinyeri az R munkakönyvtárba.

**Beviteli szabályok:**

* **A bemeneti** elem **id** attribútumának értéke érvényes R-változónév.
* A **bemeneti** elem **id** attribútumának értéke nem lehet hosszabb 64 karakternél.
* A **Bemeneti** elem **névattribútumának** értéke nem lehet hosszabb 64 karakternél.
* A **Leírás** elem tartalma nem lehet hosszabb 128 karakternél.
* **A Bemeneti** elem **típusattribútumának** értéke *Zip* vagy *DataTable*legyen.
* A **bemeneti** elem **kiválasztható** attribútumának értéke nem kötelező (és alapértelmezés szerint *hamis,* ha nincs megadva); de ha meg van adva, akkor *igaznak* vagy *hamisnak*kell lennie.

### <a name="output-elements"></a>Kimeneti elemek
**Szabványos kimeneti portok:** A kimeneti portok az R függvény ből származó visszatérési értékekre vannak leképezve, amelyeket a ztánkövetkező modulok használhatnak. *DataTable* az egyetlen szabványos kimeneti port típus támogatott jelenleg. (A *tanulók* és *az átalakítások* támogatása hamarosan elérhető.) A *DataTable* kimenet a következőképpen határozható meg:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Az egyéni R-modulok kimenetei esetén az **id** attribútum értékének nem kell megfelelnie az R-parancsfájlban lévő kontrájainak, de egyedinek kell lennie. Egyetlen modulkimenet esetén az R függvény ből származó visszatérési értéknek *adatértéknek kell lennie.frame*. A támogatott adattípus több objektumának kimenetéhez meg kell adni a megfelelő kimeneti portokat az XML-definíciós fájlban, és az objektumokat listának kell adni. A kimeneti objektumok balról jobbra vannak hozzárendelve a kimeneti portokhoz, tükrözve azt a sorrendet, amelyben az objektumok a visszaadott listában vannak.

Ha például módosítani szeretné az **Egyéni sorok hozzáadása** modult az eredeti két adatkészlet, az *adatkészlet1* és az *adatkészlet2*kimenetére, az új illesztett adatkészleten, *adatkészleten*, (sorrendben, balról jobbra, mint: *adatkészlet*, *adatkészlet1*, *adatkészlet2*), majd a CustomAddRows.xml fájl kimeneti portjait a következőképpen határozza meg:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


És adja vissza a lista objektumainak listáját a megfelelő sorrendben a "CustomAddRows.R" szerint:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Vizualizációkimenet:** Megadhat egy *Visualization*típusú kimeneti portot is, amely az R grafikus eszköz és a konzol kimenetének kimenetét jeleníti meg. Ez a port nem része az R függvény kimenetének, és nem zavarja a többi kimeneti porttípus sorrendjét. Ha vizualizációs portot szeretne hozzáadni az egyéni modulokhoz, adjon hozzá egy **Kimeneti** elemet, amelynek **típusattribútumához** *képi megjelenítés* érték van:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Kimeneti szabályok:**

* **A Kimeneti** elem **id** attribútumának értéke érvényes R változónév.
* A **Kimeneti** elem **id** attribútumának értéke nem lehet hosszabb 32 karakternél.
* A **Kimeneti** elem **névattribútumának** értéke nem lehet hosszabb 64 karakternél.
* A **Kimenetelem** **típusattribútumának** értéke *képi megjelenítés*.

### <a name="arguments"></a>Argumentumok
Az Argumentumok elemben definiált modulparamétereken keresztül további adatok adhatók át az R **függvénynek.** Ezek a paraméterek a machine learning felhasználói felületének jobb szélső tulajdonsága ablaktáblájában jelennek meg, ha a modul ki van jelölve. Az argumentumok bármelyik támogatott típus lehetnek, vagy szükség esetén egyéni felsorolást is létrehozhat. A **Portok** elemekhez hasonlóan az **Argumentumok** elemek hez hasonlóan lehet egy választható **Leírás** elem, amely megadja azt a szöveget, amely akkor jelenik meg, amikor az egérmutatót a paraméter neve fölé viszi.
A modul választható tulajdonságai, például a defaultValue, a minValue és a maxValue bármely argumentumhoz hozzáadható tulajdonságok, mint tulajdonságok elem **attribútumai.** **A Tulajdonságok** elem érvényes tulajdonságai az argumentum típusától függenek, és a következő szakaszban a támogatott argumentumtípusokkal vannak leírva. Az isOptional tulajdonsággal rendelkező **argumentumok** **nem** igénylik, hogy a felhasználó értéket adjon meg. Ha az argumentum nem ad meg értéket, akkor az argumentum nem kerül átadásra a belépési pont függvénynek. A belépési pont függvény nem kötelező argumentumait a függvénynek explicit módon kell kezelnie, például null alapértelmezett értéket kell hozzárendelnie a belépési pont függvény definíciójában. A választható argumentum csak akkor kényszeríti ki a többi argumentummegkötést, azaz a min vagy a max.
A bemenetekhez és kimenetekhez ugyanúgy fontos, hogy minden paraméterhez egyedi azonosítóértékek tartoznak. A rövid útmutató példánkban a kapcsolódó id/paraméter *csere*volt.

### <a name="arg-element"></a>Arg elem
A modulparaméter az XML-definíciós fájl **Arguments szakaszának** **Arg** gyermekelemével van definiálva. A **Portok** szakasz gyermekelemeihez ugyanúgy, az **Argumentumok** szakasz paramétereinek sorrendje határozza meg a felhasználói felületen előforduló elrendezést. A paraméterek felülről lefelé jelennek meg a felhasználói felületen ugyanabban a sorrendben, ahogyan az XML-fájlban definiálják őket. A gépi tanulás által támogatott paraméterek típusai itt találhatók. 

**int** – egész (32 bites) típusparaméter.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Választható tulajdonságok:* **min**, **max**, **alapértelmezett** és **Választható**

**dupla** – dupla típusú paraméter.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Választható tulajdonságok:* **min**, **max**, **alapértelmezett** és **Választható**

**bool** – egy logikai paraméter, amelyet egy ux-i jelölőnégyzet jelöl.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Választható tulajdonságok*: **alapértelmezett** - hamis, ha nincs beállítva

**string**: egy szabványos karakterlánc

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Választható tulajdonságok:* **alapértelmezett** **és választható**

**ColumnPicker**: oszlopválasztó paraméter. Ez a típus oszlopválasztóként jelenik meg a felhasználói környezetben. A **Tulajdonság** elem itt annak a portnak az azonosítóját adja meg, amelyből az oszlopok ki vannak jelölve, ahol a célport típusának *DataTable*típusúnak kell lennie. Az oszlopkijelölés eredménye a kijelölt oszlopneveket tartalmazó karakterláncok listájaként kerül átaz R függvénybe. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Kötelező tulajdonságok*: **portId** - egyezik a DataTable típusú bemeneti elem *azonosítójával.*
* *Választható tulajdonságok:*
  
  * **allowedTypes** - Szűri azoszlopokat, amelyekből kiválaszthat. Az érvényes értékek a következők: 
    
    * Numerikus
    * Logikai
    * Kategorikus
    * Sztring
    * Címke
    * Szolgáltatás
    * Pontszám
    * Összes
  * **alapértelmezett** – Az oszlopválasztó érvényes alapértelmezett beállításai a következők: 
    
    * None
    * Numerikus funkció
    * Numerikus címke
    * Numerikus pontszám
    * NumériA Összes
    * Logikai funkció
    * Logikai címke
    * Logikai pontszám
    * Logikai összes
    * Kategorikus funkció
    * Kategorikus Címke
    * Kategorikuspontszám
    * KategoricalAll
    * Karakterláncfunkció
    * StringLabel (Karakterláncfelirat)
    * Karakterláncpontszám
    * StringAll között
    * AllLabel felirat
    * AllFeature funkció
    * AllScore (Összes pontszám)
    * Összes

**Legördülő lista:** felhasználó által megadott enumerált (legördülő) lista. A legördülő elemek a **Tulajdonságok** elemben egy **Elemelemet** használva vannak megadva. **Az egyes elemek** **azonosítójának** egyedinek és érvényes R változónak kell lennie. **Az elem** **nevének** értéke a megjelenő szövegként és az R függvénynek átadott értékként is szolgál.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Választható tulajdonságok:*
  * **default** - Az alapértelmezett tulajdonság értékének meg kell felelnie az egyik Cikk elem azonosító **értékének.**

### <a name="auxiliary-files"></a>Kiegészítő fájlok
Az egyéni modul ZIP-fájljába helyezett fájlok a végrehajtás ideje alatt is használhatók lesznek. A jelen lévő könyvtárstruktúrák megmaradnak. Ez azt jelenti, hogy a fájlbeszerzés ugyanúgy működik helyileg és az Azure Machine Learning Studio (klasszikus) végrehajtás. 

> [!NOTE]
> Figyelje meg, hogy az összes fájl kibontása az "src" könyvtárba kerül, így minden elérési útnak "src/" előtaggal kell rendelkeznie.
> 
> 

Tegyük fel például, hogy el szeretné távolítani a NA-kat tartalmazó sorokat az adatkészletből, és el szeretné távolítani az ismétlődő sorokat is, mielőtt kiírja azt a CustomAddRows-ba, és már írt egy R függvényt, amely ezt egy File RemoveDupNARows.R fájlban teszi:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
A CustomAddRows függvényben beszerezheti az RemoveDupNARows.R kiegészítő fájlt:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

Ezután töltsön fel egy "CustomAddRows.R", "CustomAddRows.xml" és "RemoveDupNARows.R" (CustomAddRows.R', "CustomAddRows.xml" és "RemoveDupNARows.R" (CustomAddRows.R) és "RemoveDupNARows.R" (CustomAddRows.R) és "RemoveDupNARows.R" (CustomAddRows.R) és "RemoveDupNARows.R" (CustomAddRows.R) és "RemoveDupN

## <a name="execution-environment"></a>Végrehajtási környezet
Az R parancsfájl végrehajtási környezete az R ugyanazon verzióját **használja,** mint az R parancsfájl végrehajtása modul, és ugyanazokat az alapértelmezett csomagokat használhatja. További R-csomagokat is hozzáadhat az egyéni modulhoz, ha azokat az egyéni modul zip csomagba is beírja. Csak töltse be őket az R-szkriptbe, ahogy a saját R környezetében tenné. 

**A végrehajtási környezet korlátai a** következők:

* Nem állandó fájlrendszer: Az egyéni modul futtatásakor írt fájlok nem maradnak meg ugyanazon modul több futtatása során.
* Nincs hálózati hozzáférés

