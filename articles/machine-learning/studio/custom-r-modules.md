---
title: Az Azure Machine Learningben egyéni R-modulok készítése |} A Microsoft Docs
description: Gyors üzembe helyezési szerzői egyéni R-modulok az Azure Machine Learningben.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 6cbc628a-7e60-42ce-9f90-20aaea7ba630
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/29/2017
ms.openlocfilehash: 812914b3b7746f75aa88cbefda3f7d4b1a591125
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263264"
---
# <a name="author-custom-r-modules-in-azure-machine-learning"></a>Egyéni R-modul létrehozása az Azure Machine Learningben
Ez a témakör azt ismerteti, hogyan hozhat létre, és a egy egyéni R modult az Azure Machine Learning üzembe helyezése. Ismerteti, milyen egyéni R-modulok és fájlokat adhat meg hozzájuk. Azt mutatja be, hogyan hozható létre a fájlokat, a modulok meghatározásához és a Machine Learning-munkaterület központi telepítés a modul regisztrálása. Az elemek és attribútumok az egyéni modult definíciójában használt majd ismerteti részletesen. Kiegészítő funkciók és a fájlok és több kimenetek használata is tárgyalja. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="what-is-a-custom-r-module"></a>Mi az, hogy egy egyéni R modult?
A **egyéni modult** egy felhasználó által definiált modul, amely feltölti a munkaterülethez, és egy Azure Machine Learning-kísérlet részeként végrehajtott. A **egyéni R modult** van egy egyéni modult, amely végrehajtja a felhasználó által definiált R függvény. **R** a egy programnyelv a statisztikai számítástechnika és a grafikai statisztikusok és az adatelemző szakemberek által algoritmusok megvalósításának széles körben használt. R jelenleg az egyetlen támogatott egyéni modulok, de támogatja a jövőbeli kiadásokban további nyelveket van ütemezve nyelv.

Egyéni modulok rendelkezik **első osztályú állapot** az Azure Machine Learning abban az értelemben, hogy azokat bármely egyéb modult hasonlóan használhatók. Azokat más modulok, a közzétett kísérletek vagy a vizualizációkban szereplő hajthatók végre. Az algoritmus által a modul, a bemeneti és a használandó a kimeneti portok, a modellezési paramétereket és egyéb futásidejű működés megvalósított szabályozhatják. Egy kísérlet, amely tartalmazza az egyéni modulok is be az Azure AI-katalógusban az egyszerű tehetők közzé.

## <a name="files-in-a-custom-r-module"></a>Egy egyéni R modult lévő fájlok
Egy egyéni R modult, legalább két fájlokat tartalmazó .zip fájl által van definiálva:

* A **forrásfájl** , amely megvalósítja az R függvény a modul által elérhetővé tett
* Egy **XML-definíciós fájljának** , amely leírja, hogy a modul egyéni felületen

További kiegészítő fájlok is felvehetők a .zip-fájlban, amely az egyéni modulból elérhető funkciókat biztosít. Ez a beállítás a következő cikkben a **argumentumok** része a referenciaszakasz **XML-definíciós fájljának elemeit** a rövid útmutató példát követve.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Példa a rövid útmutató: határozza meg, a csomag és a egy egyéni R modult regisztrálni
Ebben a példában azt ábrázolja, hogyan hozhat létre egy egyéni R modult szükséges fájlok, becsomagolja a zip-fájlban, majd pedig regisztrálhatja a a modul a Machine Learning munkaterületén. A példában zip-csomagot és a minta fájlokat letölthető [letöltése CustomAddRows.zip fájl](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>A forrásfájl
Vegyünk például egy **egyéni sorok hozzáadása** modul, amely módosítja a standard szintű megvalósítása a **sorok hozzáadása** modul segítségével összefűzhet két adatkészletet (adatkeretek) (megfigyelések) sorait. A standard **sorok hozzáadása** modul első bemeneti adatkészlet használatával végén a második bemeneti adatkészlet sorait fűzi hozzá a `rbind` algoritmus. A testre szabott `CustomAddRows` függvény hasonlóan a két adatkészletet fogad, de további bemenetként egy logikai felcserélés paraméter is fogad. Ha a lapozófájl-kapacitás paraméter értéke **hamis**, adja vissza, a standard szintű megvalósítás azonos adatkészlet. De ha a lapozófájl-kapacitás paraméter **igaz**, a függvény fűz a bemeneti adatkészletek első sorát a második adatkészlet végén helyette. Az R végrehajtása tartalmazó CustomAddRows.R fájl `CustomAddRows` függvény által elérhetővé tett a **egyéni sorok hozzáadása** modul rendelkezik a következő R-kód.

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
Ez elérhetővé `CustomAddRows` funkciót az Azure Machine Learning modul, egy XML-definíciós fájlt kell létrehozni, adja meg a **egyéni sorok hozzáadása** modul kell megjelenését és működését. 

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


Fontos megjegyezni, hogy értékét a **azonosító** attribútumai a **bemeneti** és **Arg** az XML-fájl elemeinek meg kell egyeznie a paraméter nevét, az R-kód a CustomAddRows.R pontosan fájlt: (*dataset1*, *dataset2*, és *lapozófájl-kapacitás* a példában). Ehhez hasonlóan az értékét a **belépési pont** attribútuma a **nyelvi** elemben pontosan meg kell egyeznie a függvény az R-szkript neve: (*CustomAddRows* a példában) . 

Ezzel szemben a **azonosító** az attribútum a **kimeneti** elem nem felel meg az R-szkript minden változó. Ha egynél több kimeneti szükség, egyszerűen listáját adja vissza a R függvény elhelyezett eredményekkel *ugyanabban a sorrendben* , **kimenetek** elemek deklarált az XML-fájlban.

### <a name="package-and-register-the-module"></a>Csomag és a modul regisztrálása
Mentés másként két fájlt *CustomAddRows.R* és *CustomAddRows.xml* majd zip a két fájlt együtt és egy *CustomAddRows.zip* fájlt.

Regisztrálja őket a Machine Learning-munkaterületet, nyissa meg a Machine Learning Studio munkaterületét, kattintson a **+ új** alsó gombra, és válasszon **a modul ZIP-csomagot a ->** feltölteni az új **Egyéni sorok hozzáadása** modul.

![Zip feltöltése](./media/custom-r-modules/upload-from-zip-package.png)

A **egyéni sorok hozzáadása** modul elérhetők, a Machine Learning-kísérletek készen áll.

## <a name="elements-in-the-xml-definition-file"></a>Az XML-definíciós fájljának elemek
### <a name="module-elements"></a>A modul elemek
A **modul** elem az XML-fájl egy egyéni modult meghatározására szolgál. Több modul lehet definiálni egy XML-fájlt több **modul** elemeket. A munkaterület minden modulja egyedi névvel kell rendelkeznie. Egy egyéni modult regisztrálni a neve megegyezik egy már létező egyéni modult, és felülírja a meglévő modul az újra. Egyedi modulokat, azonban lehet a neve megegyezik egy meglévő Azure Machine Learning modul regisztrálva. Ha így azok megjelennek a **egyéni** a modulpaletta kategóriáját.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


Belül a **modul** elem, megadhatja, hogy két további nem kötelező elemek:

* egy **tulajdonos** a modulba beágyazott elem  
* egy **leírás** elem, amely tartalmazza a szöveg, amely a modul gyors súgó jelenik meg, és ha az egérmutatót a modul a Machine Learning felhasználói felületén.

A modul elemek karakter korlátozások szabályok:

* Értékét a **neve** az attribútum a **modul** elem nem haladhatja meg a hosszabb 64 karakternél. 
* Tartalma a **leírás** elem nem haladhatja meg a 128 karakternél hosszabb.
* Tartalma a **tulajdonosa** elem nem haladhatja meg a 32 karakternél hosszabb.

Egy modul eredmények determinisztikus vagy nondeterministic.* * alapértelmezés szerint, az összes modulok tekintendők determinisztikus. Ez azt jelenti, hogy megadva bemeneti paraméterek, az adatok állandó készletét, a modul adja vissza az azonos eredmények eacRAND vagy egy functionh futtatáskor. Adja meg ezt a viselkedést, az Azure Machine Learning Studio csak Újrafuttatja a modulok determinisztikus, ha egy paraméter megjelölve, vagy a bemeneti adatok megváltoztak. A gyorsítótárazott eredményt adnak vissza a kísérleteket sokkal gyorsabb végrehajtását is biztosít.

Nincsenek funkciók, amelyek determinált, például RAND vagy függvény, amely az aktuális dátum vagy idő értéket ad vissza. Ha a modul egy determinált függvényt használja, megadhatja, a modul nem determinisztikus a beállításával a választható **isDeterministic** attribútumot **hamis**. Ez az adatblokkok, hogy a modul akkor fut újra, amikor a kísérlet fut, akkor is, ha a modul bemeneti és a paraméterek nem változtak. 

### <a name="language-definition"></a>Nyelvi definíciója
A **nyelvi** elem az XML-definíciós fájlban adja meg a modul egyéni nyelvi szolgál. Jelenleg az R az egyetlen támogatott nyelv. Értékét a **sourceFile** attribútum tartalmazza a függvény hívása a modul futtatásakor az R-fájl nevének kell lennie. Ezt a fájlt a zip-csomagját részének kell lennie. Értékét a **entryPoint** attribútum neve, a hívott függvényt, és meg kell egyeznie a forrás fájlban meghatározott érvénytelen függvény.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portok
A bemeneti és kimeneti portok egy egyéni modul meg van határozva a gyermek elemei a **portok** XML-definíciós fájljának szakaszában. Ezek az elemek sorrendjét meghatározza, hogy az elrendezés tapasztalt (UX) a felhasználók. Az első alárendelt **bemeneti** vagy **kimeneti** szerepel a **portok** XML-fájl elemet a bal szélső bemeneti portját a Machine Learning UX a válik
Minden bemeneti, és előfordulhat, hogy a kimeneti portját egy nem kötelező **leírás** gyermekelemet, amely meghatározza a szöveg látható, ha az egérmutatót az egérmutatót a port, a Machine Learning felhasználói felületén.

**Szabályok portok**:

* Maximális száma **bemeneti és kimeneti portok** minden 8 van.

### <a name="input-elements"></a>A bemeneti elemek
A bemeneti portok adatokat adnak át az R függvény és a munkaterület teszi lehetővé. A **adattípusok** , amely a bemeneti portok a következők támogatottak: 

**A DataTable:** ilyen átadott az R egy data.frame funkciót. Tulajdonképpen bármely telepítéstípushoz (például CSV-fájlok vagy ARFF fájlok), amely a Machine Learning, és amely által támogatott kompatibilisek az **DataTable** egy data.frame automatikusan alakulnak. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

A **azonosító** kapcsolódó attribútum **DataTable** bemeneti portját egyedi értékkel kell rendelkeznie, és ezt az értéket meg kell egyeznie a megfelelő nevű paraméter az R függvényben.
Nem kötelező **DataTable** értékkel rendelkezik, amely nem ad át egy kísérletet a bemeneti portok **NULL** az R függvény és a választható zip figyelmen kívül hagyja a portokat, ha a bemenet nem kapcsolódik. A **isOptional** attribútum megadása nem kötelező, mind a **DataTable** és **Zip** típusait, és *hamis* alapértelmezés szerint.

**Zip:** egyéni modulok elfogadhat bemenetként egy zip-fájlt. A bemenet a R munkakönyvtárba a függvény van csomagolva.

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Az egyéni R-modulok egy Zip-port azonosítója nem rendelkezik a paramétereket, az R-függvény megfelelően. Ennek az oka a zip-fájl automatikusan ki kell olvasni az R munkakönyvtárba.

**A bemeneti szabályok:**

* Értékét a **azonosító** attribútuma a **bemeneti** elemnek kell lennie egy érvényes R-változó neve.
* Értékét a **azonosító** attribútuma a **bemeneti** elem nem lehet hosszabb 64 karakternél.
* Értékét a **neve** attribútuma a **bemeneti** elem nem lehet hosszabb 64 karakternél.
* Tartalma a **leírás** elem nem lehet hosszabb 128 karakternél
* Értékét a **típus** attribútuma a **bemeneti** elemnek kell lennie *Zip* vagy *DataTable*.
* Értékét a **isOptional** attribútuma a **bemeneti** elem nem kötelező (és *false (hamis)* alapesetben, ha nincs megadva); de ha a célgyűjtemény meg van adva, kelllennie*igaz* vagy *hamis*.

### <a name="output-elements"></a>Kimeneti elemek
**Standard kimeneti portok:** kimeneti portok az R-függvény, amely ezután felhasználhatók a későbbi modulok, a visszaadott értékeket vannak leképezve. *A DataTable* támogatott jelenleg csak a standard kimeneti port típusa. (Támogatása *tanulók* és *alakítja át az* kapja.) A *DataTable* kimeneti számít, ha:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Az egyéni R-modulok, az értékét a kimenetek a **azonosító** attribútum nem kell semmit, az R-szkript megfelelni, de ennek egyedinek kell lennie. Számára egy egy modul kimeneti az R függvény visszatérési értéke nem lehet egy *data.frame*. Annak érdekében, hogy a kimenet egy támogatott adattípusú egynél több objektumot, a megfelelő kimeneti portok meg kell adni az XML-definíciós fájlban, és az objektumokat kell egy listát küldi vissza. A kimeneti objektumok vannak hozzárendelve a kimeneti portok balról jobbra, amely tükrözi a sorrendet, amelyben az objektumok kerülnek, a visszaadott listában.

Például, ha módosítani szeretné a **egyéni sorok hozzáadása** modul kimeneti az eredeti két adatkészletet *dataset1* és *dataset2*, az új csatlakozó adatkészlet mellett *adatkészlet*, (sorrendben, balról jobbra, mint: *adatkészlet*, *dataset1*, *dataset2*), majd adja meg a kimeneti portok a CustomAddRows.xml fájlt a következőképpen:

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

**Vizualizáció kimeneti:** azt is megadhatja a kimeneti portra típusú *Vizualizáció*, R grafikus eszköz és a konzol kimenete a kimenet megjeleníti. Ezt a portot nem része az R függvény kimenete, és nem zavarja a más típusú kimeneti port a sorrendben. Egy Vizualizáció portot ad hozzá az egyéni modulok, adjon hozzá egy **kimeneti** elem értéke az *Vizualizáció* a annak **típus** attribútum:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Kimenő szabályok:**

* Értékét a **azonosító** attribútuma a **kimeneti** elemnek kell lennie egy érvényes R-változó neve.
* Értékét a **azonosító** attribútuma a **kimeneti** elem nem lehet hosszabb 32 karakternél.
* Értékét a **neve** attribútuma a **kimeneti** elem nem lehet hosszabb 64 karakternél.
* Értékét a **típus** attribútuma a **kimeneti** elemnek kell lennie *Vizualizáció*.

### <a name="arguments"></a>Argumentumok
További adatok adható át az R függvény a meghatározott modul paraméterekkel a **argumentumok** elemet. A modul kiválasztásakor ezeket a paramétereket a Machine Learning a felhasználói felület a jobb szélső tulajdonságait tartalmazó ablaktáblán jelennek meg. Az argumentumok lehetnek, a támogatott típusok, vagy létrehozhat egy egyéni felsorolás, amikor szükséges. Hasonló a **portok** elemek **argumentumok** elemek rendelkezhet egy választható **leírás** elem, amely meghatározza a szöveg jelenik meg, amikor, vigye az egérmutatót a a paraméter neve.
Egy modul, például a DefaultValue érték, a minValue és maxValue választható tulajdonságokkal, attribútumokat is hozzáadhatók bármelyik argumentum egy **tulajdonságok** elemet. Az érvényes tulajdonságok a **tulajdonságok** elem argumentum típusa határozza meg, és a támogatott argumentumtípusaival a következő szakaszban ismertetjük. Az argumentumok a **isOptional** tulajdonság **"true"** nem igénylik a felhasználónak meg kell adnia egy értéket. Ha az argumentum nincs megadva érték, majd a van nem függvénynek átadott argumentum a belépési pont. A belépési pont függvény megadása nem kötelező argumentumok kell explicit módon kell kezelnie a függvényt, például az a belépési ponthoz függvénydefiníció a NULL alapértelmezett értéket kapja. Nem kötelező argumentum csak érvényesíti a más argumentum megkötések, azaz a min vagy max, ha a felhasználó által megadott értéket.
A bemenetek és kimenetek, rendkívül fontos, hogy a paraméterek értékűek egyedi azonosító tartozik. Ebben a gyors üzembe helyezési példában a társított azonosító paraméter lett *lapozófájl-kapacitás*.

### <a name="arg-element"></a>Arg elem
Egy modul paraméter használatával van definiálva a **Arg** gyermekeleme a **argumentumok** XML-definíciós fájljának szakaszában. A gyermek elemei a **portok** részben, a paramétereket a rendezése a **argumentumok** szakasz definiálja az elrendezést észlelt a UX a A paraméterek megjelennek az első le a felhasználói felület ugyanabban a sorrendben, amelyben az XML-fájlban vannak definiálva. A paraméterek a Machine Learning által támogatott típusok itt jelennek meg. 

**int** – egész szám (32 bites) típusú paramétert.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Nem kötelező tulajdonságok*: **min**, **maximális**, **alapértelmezett** és **isOptional**

**dupla** – a dupla típusú paramétert.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Nem kötelező tulajdonságok*: **min**, **maximális**, **alapértelmezett** és **isOptional**

**logikai** – egy logikai paramétert, amely egy jelölőnégyzet a UX képviseli

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Nem kötelező tulajdonságok*: **alapértelmezett** -false (hamis), ha nincs beállítva

**karakterlánc**: szabványos karakterláncok

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Nem kötelező tulajdonságok*: **alapértelmezett** és **isOptional**

**ColumnPicker**: egy oszlop kiválasztása paramétert. Ez a típus, egy Oszlopválasztó megjelenítve felhasználói. A **tulajdonság** elem itt arra szolgál, adja meg a portot, amelyen oszlop van kijelölve, ahol a cél port típusúnak kell lennie azonosítója *DataTable*. A kijelölés eredményét az R függvény átadott, a kiválasztott oszlop nevét tartalmazó karakterláncok listáját. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Kötelező tulajdonságai*: **portId** -típusú bemeneti elem azonosítója megegyezik *DataTable*.
* *Nem kötelező tulajdonságok*:
  
  * **allowedTypes** -szűrők az oszlop, amely választhat típusokat. Érvényes értékek a következők: 
    
    * Numerikus
    * Logikai
    * Kategorikus
    * Karakterlánc
    * Címke
    * Szolgáltatás
    * Pontszám
    * Összes
  * **alapértelmezett** -érvényes alapértelmezett beállításokat az Oszlopválasztó a következők: 
    
    * None
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

**Legördülő lista**: egy felhasználó által megadott sorszámozott () lista. A legördülő lista elemek kell megadni a **tulajdonságok** elem használatával egy **elem** elemet. A **azonosító** minden **elem** egyedinek kell lennie és a egy érvényes R-változót. Értékét a **neve** , egy **elem** funkcionál az R függvénynek átadott érték pedig a szöveg látható.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Nem kötelező tulajdonságok*:
  * **alapértelmezett** – az alapértelmezett tulajdonság értékét meg kell felelnie az egyik egy azonosítót tartalmazó a **elem** elemeket.

### <a name="auxiliary-files"></a>Külső fájlok
Összes fájl, amely az egyéni modul ZIP-fájlja kerül lesz használható végrehajtási idő alatt. Jelen könyvtárstruktúrák megmaradnak. Ez azt jelenti, sourcing works fájl azonos helyileg és az Azure Machine Learning végrehajtását. 

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
A végrehajtási környezet az R-szkript az R, ugyanazon verzióját használja, a **R-szkript végrehajtása** modul, valamint az ugyanazon alapértelmezett csomagokat. Az egyéni modul fel azokat az egyéni modult zip-csomagját is hozzáadhat további R csomagokat. Csak betöltheti őket az R-szkript, mint az a saját R-környezetben. 

**Korlátozások a végrehajtási környezet** tartalmazza:

* Nem állandó fájlrendszer: az egyéni modult futtatásakor írt fájlok nem rögzíti a ugyanazon modul több futtatás során.
* Nincs hálózati hozzáférés

