---
title: Egyéni R-modulok definiálása
titleSuffix: ML Studio (classic) - Azure
description: Ez a témakör bemutatja, hogyan hozhat létre és helyezhet üzembe egyéni R studiót (klasszikus). Ismerteti, milyen egyéni R-modulok és fájlokat adhat meg hozzájuk.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 76b2f2ae9774fe5951779912e679fa84350878c5
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153570"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Egyéni R-modulok definiálása a Azure Machine Learning Studiohoz (klasszikus)

Ez a témakör bemutatja, hogyan hozhat létre és helyezhet üzembe egyéni R studiót (klasszikus). Ismerteti, milyen egyéni R-modulok és fájlokat adhat meg hozzájuk. Azt mutatja be, hogyan hozható létre a fájlokat, a modulok meghatározásához és a Machine Learning-munkaterület központi telepítés a modul regisztrálása. Az elemek és attribútumok az egyéni modult definíciójában használt majd ismerteti részletesen. Kiegészítő funkciók és a fájlok és több kimenetek használata is tárgyalja. 



## <a name="what-is-a-custom-r-module"></a>Mi az, hogy egy egyéni R modult?
Az **Egyéni modul** egy felhasználó által definiált modul, amely feltölthető a munkaterületre, és Azure Machine learning Studio (klasszikus) kísérlet részeként is végrehajtható. Az **Egyéni r-modul** egy egyéni modul, amely egy felhasználó által definiált r-függvényt hajt végre. Az **R** olyan statisztikai számítástechnikai és grafikai programozási nyelv, amelyet a statisztikusok és az adatszakértők széles körben használnak az algoritmusok megvalósításához. R jelenleg az egyetlen támogatott egyéni modulok, de támogatja a jövőbeli kiadásokban további nyelveket van ütemezve nyelv.

Az egyéni modulok az **első osztályú állapottal** rendelkeznek Azure Machine learning Studio (klasszikus) abban az értelemben, hogy ugyanúgy használhatók, mint bármely más modul. Azokat más modulok, a közzétett kísérletek vagy a vizualizációkban szereplő hajthatók végre. Az algoritmus által a modul, a bemeneti és a használandó a kimeneti portok, a modellezési paramétereket és egyéb futásidejű működés megvalósított szabályozhatják. Egy kísérlet, amely tartalmazza az egyéni modulok is be az Azure AI-katalógusban az egyszerű tehetők közzé.

## <a name="files-in-a-custom-r-module"></a>Egy egyéni R modult lévő fájlok
Egy egyéni R modult, legalább két fájlokat tartalmazó .zip fájl által van definiálva:

* A modul által elérhető R-függvényt megvalósító **forrásfájl**
* Az egyéni modul felületét ismertető **XML-definíciós fájl**

További kiegészítő fájlok is felvehetők a .zip-fájlban, amely az egyéni modulból elérhető funkciókat biztosít. Ezt a beállítást az **XML-definíciós fájl** hivatkozási szakaszának **argumentumok** részében találja, a gyors üzembe helyezési példát követve.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Példa a rövid útmutató: határozza meg, a csomag és a egy egyéni R modult regisztrálni
Ebben a példában azt ábrázolja, hogyan hozhat létre egy egyéni R modult szükséges fájlok, becsomagolja a zip-fájlban, majd pedig regisztrálhatja a a modul a Machine Learning munkaterületén. A zip-csomag és a minta fájljai letölthetők a [CustomAddRows. zip fájl letöltésével](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>A forrásfájl
Vegyünk például egy olyan **egyéni sorok hozzáadása** modult, amely módosítja a sorok **hozzáadása** modul standard implementációját, amely két adatkészletből (adatkeretből) való összefűzéshez használatos. A standard **Add sorok** modul hozzáfűzi a második bemeneti adatkészlet sorait az első bemeneti adatkészlet végéhez a `rbind` algoritmus használatával. A testreszabott `CustomAddRows` függvény Hasonlóképpen két adatkészletet fogad el, de egy logikai swap paramétert is elfogad további bemenetként. Ha a swap paraméter **hamis**értékre van beállítva, akkor ugyanazt az adatkészletet adja vissza, mint a normál implementáció. Ha azonban a swap paraméter értéke **true (igaz**), a függvény hozzáfűzi az első bemeneti adatkészlet sorait a második adatkészlet végéhez. Az **egyéni sorok hozzáadása** modul által elérhető r `CustomAddRows` funkció megvalósítását tartalmazó CustomAddRows. R fájl a következő R-kóddal rendelkezik.

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
Ahhoz, hogy a `CustomAddRows` funkció elérhető legyen Azure Machine Learning Studio (klasszikus) modulként, létre kell hoznia egy XML-definíciós fájlt, amely meghatározza, hogy az **egyéni sorok hozzáadása** modul hogyan nézzen és viselkedjen. 

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


Fontos megjegyezni, hogy az XML-fájlban szereplő **bemeneti** és **ARG** elemek **azonosító** attribútumainak értékének meg kell egyeznie a CustomAddRows. r fájlban található r-kód Function paraméterének nevével pontosan: (*DataSet1 elemet*, *dataset2*és *swap* a példában). Hasonlóképpen, a **nyelvi** elem **BelépésiPont** attribútumának értékének meg kell egyeznie a függvény nevével az R-szkriptben pontosan: (a példában a*CustomAddRows* ). 

Ezzel szemben a **kimeneti** elem **ID** attribútuma nem felel meg az R-szkriptben szereplő változóknak. Ha egynél több kimenetre van szükség, egyszerűen visszaállíthat egy listát az R-függvényből, amelynek *a sorrendje megegyezik* a **kimeneti** elemek XML-fájlban való deklarált eredményeivel.

### <a name="package-and-register-the-module"></a>Csomag és a modul regisztrálása
Mentse ezt a két fájlt a *CustomAddRows. R* és a *CustomAddRows. XML* fájlként, majd a két fájlt egy *CustomAddRows. zip* fájlba.

Ha regisztrálni szeretné őket a Machine Learning munkaterületen, lépjen a munkaterületre Azure Machine Learning Studio (klasszikus) területen, kattintson az **+ új** gombra az alján, és válassza a **modul-> a zip-csomagból** lehetőséget az új **egyéni sorok hozzáadása** modul feltöltéséhez.

![Zip feltöltése](./media/custom-r-modules/upload-from-zip-package.png)

Az **egyéni sorok hozzáadása** modul most már készen áll a Machine learning kísérletek elérésére.

## <a name="elements-in-the-xml-definition-file"></a>Az XML-definíciós fájljának elemek
### <a name="module-elements"></a>A modul elemek
A **Module** elem egy egyéni modul definiálására használható az XML-fájlban. Több modul is definiálható egy XML-fájlban több **modul** elem használatával. A munkaterület minden modulja egyedi névvel kell rendelkeznie. Egy egyéni modult regisztrálni a neve megegyezik egy már létező egyéni modult, és felülírja a meglévő modul az újra. Az egyéni modulok azonban a meglévő Azure Machine Learning Studio (klasszikus) modul nevével megegyező névvel regisztrálhatók. Ha igen, a modul paletta **Egyéni** kategóriájában jelennek meg.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


A **modul** elemen belül két további választható elemet is megadhat:

* a modulba ágyazott **tulajdonosi** elem  
* a **modul** gyors súgójában megjelenő szöveg, amely a Machine learning felhasználói felületén található modul fölé mutat.

A modul elemek karakter korlátozások szabályok:

* A **Module** elem **Name** attribútumának értéke nem lehet hosszabb 64 karakternél. 
* A **Leírás** elem tartalma nem lehet hosszabb 128 karakternél.
* A **tulajdonos** elem tartalma nem lehet hosszabb 32 karakternél.

Egy modul eredmények determinisztikus vagy nondeterministic.* * alapértelmezés szerint, az összes modulok tekintendők determinisztikus. Ez azt eredményezi, hogy a bemeneti paraméterek és adatok változatlanul eltérő készletét kell visszaadnia, és a modulnak ugyanazt az eredményt kell visszaadnia, vagy eacRAND. Ennek a viselkedésnek a miatt a Azure Machine Learning Studio (klasszikus) csak determinisztikus jelölésű modulokat futtat, ha a paraméter vagy a bemeneti adatok módosultak. A gyorsítótárazott eredményt adnak vissza a kísérleteket sokkal gyorsabb végrehajtását is biztosít.

Nincsenek funkciók, amelyek determinált, például RAND vagy függvény, amely az aktuális dátum vagy idő értéket ad vissza. Ha a modul determinált függvényt használ, megadhatja, hogy a modul nem determinisztikus, ha a választható **isDeterministic** attribútumot **hamis**értékre állítja. Ez az adatblokkok, hogy a modul akkor fut újra, amikor a kísérlet fut, akkor is, ha a modul bemeneti és a paraméterek nem változtak. 

### <a name="language-definition"></a>Nyelvi definíciója
Az XML-definíciós fájl **nyelvi** eleme az egyéni modul nyelvének megadására szolgál. Jelenleg az R az egyetlen támogatott nyelv. A **sourceFile** attribútum értékének meg kell egyeznie azon R-fájl nevével, amely a modul futásakor hívni kívánt függvényt tartalmazza. Ezt a fájlt a zip-csomagját részének kell lennie. A **BelépésiPont** attribútum értéke a hívott függvény neve, és meg kell egyeznie a forrásfájl által definiált érvényes függvénnyel.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portok
Az egyéni modul bemeneti és kimeneti portjai az XML-definíciós fájl **portok** szakaszának alárendelt elemeiben vannak megadva. Ezek az elemek sorrendjét meghatározza, hogy az elrendezés tapasztalt (UX) a felhasználók. Az XML-fájl **portok** elemében felsorolt első gyermek **bemenet** vagy **kimenet** a Machine learning UX bal szélső bemeneti portja lesz.
Előfordulhat, hogy az egyes bemeneti és kimeneti portok opcionális **leírási** alárendelt elemmel rendelkeznek, amely megadja az egérmutatónak a Machine learning felhasználói felületen lévő port fölé helyezésekor megjelenő szöveget.

**Portok szabályai**:

* A **bemeneti és a kimeneti portok** maximális száma 8.

### <a name="input-elements"></a>A bemeneti elemek
A bemeneti portok adatokat adnak át az R függvény és a munkaterület teszi lehetővé. A bemeneti portok által támogatott **adattípusok** a következők: 

**DataTable:** Ezt a típust a rendszer adat. keretként továbbítja az R-függvénynek. Valójában minden olyan típust (például CSV-fájlt vagy ARFF-fájlt), amelyet a Machine Learning támogat, és amelyek kompatibilisek a **DataTable adattáblával** , automatikusan átváltanak egy adatkeretbe. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

Az egyes **DataTable** bemeneti portokhoz társított **ID** attribútumnak egyedi értékkel kell rendelkeznie, és ennek az értéknek meg kell egyeznie az R-függvény megfelelő megnevezett paraméterével.
Azok a nem kötelező **DataTable** portok, amelyeket nem a kísérlet bemenetként továbbítanak, az R-függvénynek **Null** értéket adtak át, és a választható zip-portok figyelmen kívül lesznek hagyva, ha a bemenet nincs csatlakoztatva. A **isOptional** attribútum nem kötelező a **DataTable** és a **zip** típushoz, és alapértelmezés szerint *hamis* .

**Zip:** Az egyéni modulok bemenetként is elfogadhatják a ZIP-fájlokat. A bemenet a R munkakönyvtárba a függvény van csomagolva.

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Az egyéni R-modulok esetében a zip-portok AZONOSÍTÓjának nem kell egyeznie az R függvény egyetlen paraméterével sem. Ennek az oka a zip-fájl automatikusan ki kell olvasni az R munkakönyvtárba.

**Bemeneti szabályok:**

* A **bemeneti** elem **ID** attribútumának értékének érvényes R-változó névnek kell lennie.
* A **bemeneti** elem **ID** attribútumának értéke nem lehet hosszabb 64 karakternél.
* A **bemeneti** elem **Name** attribútumának értéke nem lehet hosszabb 64 karakternél.
* A **Leírás** elem tartalma nem lehet hosszabb 128 karakternél.
* A **bemeneti** elem **Type** attribútumának értéke csak *zip* vagy *DataTable*lehet.
* A **bemeneti** elem **isOptional** attribútumának értéke nem kötelező (és alapértelmezés szerint *hamis* , ha nincs megadva); Ha azonban meg van adva, akkor *igaz* vagy *hamis értéknek*kell lennie.

### <a name="output-elements"></a>Kimeneti elemek
**Szabványos kimeneti portok:** A kimeneti portok az R-függvény visszatérési értékeire vannak leképezve, amelyet aztán a későbbi modulok használhatnak. A *DataTable* az egyetlen, jelenleg támogatott szabványos kimeneti port. (A *tanulók* és az *átalakítások* támogatása hamarosan megtörténik.) A *DataTable* kimenet a következőképpen van definiálva:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Az egyéni R-modulok kimenetei esetében az **ID** attribútum értékének nem kell megfelelnie az R-parancsfájlban található semmit, de egyedinek kell lennie. Egyetlen modul kimenete esetén az R függvény visszatérési értékének egy *adat. frame típusúnak*kell lennie. Annak érdekében, hogy a kimenet egy támogatott adattípusú egynél több objektumot, a megfelelő kimeneti portok meg kell adni az XML-definíciós fájlban, és az objektumokat kell egy listát küldi vissza. A kimeneti objektumok vannak hozzárendelve a kimeneti portok balról jobbra, amely tükrözi a sorrendet, amelyben az objektumok kerülnek, a visszaadott listában.

Ha például módosítani kívánja az **egyéni sorok hozzáadása** modult, hogy az eredeti két adatkészletet, a *DataSet1 elemet* és a *dataset2*az új csatlakoztatott adatkészlet, az *adatkészlet*(egy rendelésben, balról jobbra, mint: *adatkészlet*, *DataSet1 elemet*, *dataset2*) alapján adja meg, akkor a következő módon határozza meg a kimeneti portokat a CustomAddRows. xml fájlban:

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


És az objektumok listájában, a megfelelő sorrendben a "CustomAddRows.R" listáját adja vissza:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Vizualizáció kimenete:** Megadhat egy *vizualizáció*típusú kimeneti portot is, amely megjeleníti az R Graphics eszköz és a konzol kimenetének kimenetét. Ezt a portot nem része az R függvény kimenete, és nem zavarja a más típusú kimeneti port a sorrendben. Ha vizualizációs portot szeretne hozzáadni az egyéni modulokhoz, adjon hozzá egy **kimeneti** elemet a **Type** attribútumhoz a *vizualizáció* értékével:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Kimeneti szabályok:**

* A **kimeneti** elem **ID** attribútumának értékének érvényes R-változó névnek kell lennie.
* A **kimeneti** elem **ID** attribútumának értéke nem lehet hosszabb 32 karakternél.
* A **kimeneti** elem **Name** attribútumának értéke nem lehet hosszabb 64 karakternél.
* A **kimeneti** elem **Type** attribútumának értékének a *vizualizációnak*kell lennie.

### <a name="arguments"></a>Argumentumok
További adatok adhatók át az R függvénynek az **argumentumok** elemben definiált modul-paramétereken keresztül. A modul kiválasztásakor ezeket a paramétereket a Machine Learning a felhasználói felület a jobb szélső tulajdonságait tartalmazó ablaktáblán jelennek meg. Az argumentumok lehetnek, a támogatott típusok, vagy létrehozhat egy egyéni felsorolás, amikor szükséges. A **portok** elemeihez hasonlóan az **argumentumok** elemei opcionális **leírási** elemmel is rendelkezhetnek, amely meghatározza azt a szöveget, amely akkor jelenik meg, amikor az egérmutatót a paraméter neve fölé viszi.
Egy modul nem kötelező tulajdonságai, például a defaultValue, a minValue és a maxValue bármely argumentumként hozzáadhatók attribútumként egy **tulajdonság** elemhez. A **Properties** elem érvényes tulajdonságai az argumentum típusától függenek, és a következő szakaszban szereplő támogatott argumentum-típusokkal vannak leírva. Az **"igaz"** értékre beállított **isOptional** tulajdonsággal rendelkező argumentumok nem igénylik, hogy a felhasználó értéket adjon meg. Ha az argumentum nincs megadva érték, majd a van nem függvénynek átadott argumentum a belépési pont. A belépési pont függvény megadása nem kötelező argumentumok kell explicit módon kell kezelnie a függvényt, például az a belépési ponthoz függvénydefiníció a NULL alapértelmezett értéket kapja. Nem kötelező argumentum csak érvényesíti a más argumentum megkötések, azaz a min vagy max, ha a felhasználó által megadott értéket.
A bemenetekhez és kimenetekhez hasonlóan elengedhetetlen, hogy mindegyik paraméterhez egyedi azonosító értékek legyenek társítva. A gyors üzembe helyezési példában a kapcsolódó azonosító/paraméter *felcserélve*.

### <a name="arg-element"></a>Arg elem
A modul paramétereit az XML-definíciós fájl **argumentumok** szakaszának **ARG** gyermek eleme alapján definiáljuk. A **portok** szakasz alárendelt elemeihez hasonlóan az **argumentumok** szakaszban szereplő paraméterek sorrendje határozza meg az UX-ben észlelt elrendezést. A paraméterek megjelennek az első le a felhasználói felület ugyanabban a sorrendben, amelyben az XML-fájlban vannak definiálva. A paraméterek a Machine Learning által támogatott típusok itt jelennek meg. 

**int** – egész szám (32 bites) típusú paraméter.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Választható tulajdonságok*: **min**., **Max**., **alapértelmezett** és **isOptional**

**Double** – egy Double Type paraméter.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Választható tulajdonságok*: **min**., **Max**., **alapértelmezett** és **isOptional**

**bool** – egy logikai paraméter, amelyet az UX egy jelölőnégyzete jelöl.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Választható tulajdonságok*: **default** -false, ha nincs beállítva

**String**: standard sztring

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* Nem *kötelező tulajdonságok*: **alapértelmezett** és **isOptional**

**ColumnPicker**: oszlop kiválasztási paramétere. Ez a típus, egy Oszlopválasztó megjelenítve felhasználói. Itt megadhatja annak a portnak az azonosítóját, amelyből az oszlopok ki vannak választva, ahol a célként megadott portnak *DataTable*típusúnak kell lennie. A kijelölés eredményét az R függvény átadott, a kiválasztott oszlop nevét tartalmazó karakterláncok listáját. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Szükséges tulajdonságok*: **PortId** – a *DataTable*típusú bemeneti elem azonosítójának felel meg.
* Nem *kötelező tulajdonságok*:
  
  * **allowedTypes** – a kiválasztható oszlopok típusát szűri. Érvényes értékek a következők: 
    
    * Numeric
    * Logikai
    * Kategorikus
    * Sztring
    * Címke
    * Funkció
    * Pontszám
    * Összes
  * **alapértelmezett** – az oszlop választójának alapértelmezett értékei a következők: 
    
    * Nincs
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Összes

**Legördülő menü**: felhasználó által megadott enumerálás (legördülő lista). A legördülő elemek a **Tulajdonságok** elemen belül, egy **Item** elem használatával vannak megadva. Az egyes **elemek** **azonosítójának** egyedinek és érvényes R-változónak kell lennie. Az **elemek** **nevének** értéke a megjelenő szövegként és az R-függvénynek átadott értékként szolgál.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* Nem *kötelező tulajdonságok*:
  * **alapértelmezett** – az alapértelmezett tulajdonság értékének meg kell egyeznie az **elem** egyik elemének azonosító értékével.

### <a name="auxiliary-files"></a>Külső fájlok
Összes fájl, amely az egyéni modul ZIP-fájlja kerül lesz használható végrehajtási idő alatt. Jelen könyvtárstruktúrák megmaradnak. Ez azt jelenti, hogy a fájl beszerzése a helyi és a Azure Machine Learning Studio (klasszikus) végrehajtással azonos módon működik. 

> [!NOTE]
> Figyelje meg, hogy az összes fájlok kibontása "src" könyvtár, így az összes elérési utat kell "src /" előtaggal.
> 
> 

Tegyük fel például, távolítsa el a NAs sorokat az adatkészlet, és is távolítsa el az ismétlődő sorokat szerint kiírta volna az CustomAddRows előtt szeretne, és a egy R-függvényt, amely egy fájlban RemoveDupNARows.R végez, amely már leírt:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
A kiegészítő fájl RemoveDupNARows.R CustomAddRows függvényében is forrás:

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

Ezután töltse fel a 'CustomAddRows.R', "CustomAddRows.xml" és "RemoveDupNARows.R" egy egyéni R modult tartalmazó zip-fájlt.

## <a name="execution-environment"></a>Végrehajtási környezet
Az R-szkript végrehajtási környezete ugyanazt az R-verziót használja, mint az r **szkript végrehajtása** modul, és ugyanazokat az alapértelmezett csomagokat használhatja. Az egyéni modul fel azokat az egyéni modult zip-csomagját is hozzáadhat további R csomagokat. Csak betöltheti őket az R-szkript, mint az a saját R-környezetben. 

**A végrehajtási környezet korlátai** a következők:

* Nem állandó fájlrendszer: az egyéni modult futtatásakor írt fájlok nem rögzíti a ugyanazon modul több futtatás során.
* Nincs hálózati hozzáférés

