---
title: Az Azure Machine Learning modulok egyéni R szerzői |} Microsoft Docs
description: Gyors üzembe helyezési egyéni R modul az Azure Machine Learning-szerzésre vonatkozó információ.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6cbc628a-7e60-42ce-9f90-20aaea7ba630
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/29/2017
ms.openlocfilehash: 945c238411dac8f2e64666935308ef3c1cb2f0ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="author-custom-r-modules-in-azure-machine-learning"></a>Egyéni R-modul létrehozása az Azure Machine Learningben
Ez a témakör ismerteti a létrehozásáról és központi telepítése az Azure Machine Learning egy egyéni R modult. Egyéni R modul és a megadásukhoz használt fájlokat ismerteti. Azt mutatja be, hogyan készítse a fájlokat, a modulok meghatározásához és a Machine Learning-munkaterület központi telepítés modul regisztrálása. Az elemek és attribútumok az egyéni modul definíciójában használja majd ismerteti részletesen. Kiegészítő funkciók és a fájlok és a több kimenet használata is ismertet. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="what-is-a-custom-r-module"></a>Mi az, hogy egy egyéni R modult?
A **egyéni modul** egy felhasználó által definiált modul, amely fel van töltve a munkaterület, és az Azure Machine Learning kísérlet részeként végre. A **egyéni R modul** egy egyéni modul, amely végrehajtja a felhasználó által definiált R függvény. **R** statisztikai számítások és grafikus statisztikusok és az adatok kutatók által algoritmusok megvalósításának széles körben használt programozási nyelv. R jelenleg az egyetlen támogatott egyéni modulokkal, de támogatja a későbbi kiadásokban további nyelvek van ütemezve nyelv.

Az egyéni modulok rendelkezik **első osztályú állapot** az Azure Machine Learning abban az értelemben, hogy azok csakúgy, mint bármely más modul használható. Az egyéb modulok, közzétett kísérletek vagy a képi megjelenítések végrehajthatók. A modul, a bemeneti és kimeneti portok használandó, a modellezési paraméterek és más különböző futásidejű viselkedések által megvalósított algoritmus befolyásolni. A kísérlet, amely tartalmazza az egyéni modulok is azokat az Azure Eszközintelligencia-katalógus az egyszerű tehetők közzé.

## <a name="files-in-a-custom-r-module"></a>Egy egyéni R modul fájlok
Egy egyéni R modul egy .zip fájlt, amely legalább két fájlt tartalmaz határozzák meg:

* A **forrásfájl** , amely megvalósítja az R függvény jelennek meg, ha a modul
* Egy **XML-definíciós fájljának** , amely leírja, hogy a modul egyéni felület

További kiegészítő fájlok funkciókat biztosít, amelyek az egyéni modul elérhető .zip-fájlban is szerepelhet. Ez a beállítás ismertet a **argumentumok** részben része **az XML-definíciós fájl elemeinek** a gyors üzembe helyezési példában a következő.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Gyors üzembe helyezési példa: határozza meg, a csomagot, majd egy egyéni R modult regisztrálni
Ez a példa bemutatja, hogyan hozható létre egy egyéni R modult szükséges fájlokat, csomagot készíthet egy zip-fájlt, és regisztrálja a modul a Machine Learning-munkaterület. A példa zip-csomag és a minta fájlok letölthetők [letöltése CustomAddRows.zip fájl](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>A forrásfájl
Vegyünk például egy **egyéni hozzáadása sorok** modul, amely a szabványos végrehajtásának módosítja a **sorok hozzáadása** modul összefűzésére két adatkészletet (adatkeretek) (megfigyelések) sorát használja. A standard **sorok hozzáadása** modul a sorok, a második bemeneti adatkészlet hozzáfűzi a bemeneti adatkészlet használatával végén a `rbind` algoritmus. A testre szabott `CustomAddRows` függvény hasonlóképpen fogad el két adatkészletet, de egy logikai swap paraméter további bemenetként is fogad. Ha a lapozófájl-kapacitás paraméter értéke **hamis**, a szabványos megvalósítástól azonos adatkészlet adja vissza. De ha a lapozófájl-kapacitás paraméter **igaz**, a függvény hozzáfűzi a bemeneti adatkészlet első sorát a második dataset végén helyette. Az R végrehajtása tartalmazó CustomAddRows.R fájl `CustomAddRows` függvény által elérhetővé tett a **egyéni hozzáadása sorok** modul rendelkezik a következő R-kód.

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

### <a name="the-xml-definition-file"></a>Az XML-definíciós fájlt
Teszi közzé a `CustomAddRows` függvényt egy Azure Machine Learning-modul, egy XML-definíciós fájljának, létre kell hozni adja meg, hogyan a **egyéni hozzáadása sorok** modul kell kinézete és viselkedése. 

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


Fontos megjegyezni, hogy értékének a **azonosító** attribútumait a **bemeneti** és **Arg** az XML-fájl elemeinek pontosan meg kell egyeznie a paraméter nevét az R-kód a CustomAddRows.R fájlban: (*dataset1*, *dataset2*, és *swap* a példában). Ehhez hasonlóan az értékét a **belépési pont** attribútuma a **nyelvi** elem pontosan meg kell egyeznie a nevét, a függvény az R-parancsfájl: (*CustomAddRows* a példában). 

Ezzel szemben a **azonosító** az attribútum a **kimeneti** elem nem felel meg az R-parancsfájl a változó. Ha több kimenet szükség, egyszerűen listáját adja vissza az R funkcióról elhelyezett eredményekkel *ugyanabban a sorrendben* , **kimenetek** az XML-fájlban deklarált elemeket.

### <a name="package-and-register-the-module"></a>Csomag és a modul regisztrálása
Mentés másként két fájlt *CustomAddRows.R* és *CustomAddRows.xml* és majd zip a két fájl együtt történő egy *CustomAddRows.zip* fájlt.

Regisztrálja őket a Machine Learning munkaterülettel, nyissa meg a Machine Learning Studio a munkaterületet, kattintson a **+ új** alsó gombra, majd válassza a **modul ZIP-csomag a ->** töltse fel az új **egyéni hozzáadása sorok** modul.

![Zip feltöltése](./media/custom-r-modules/upload-from-zip-package.png)

A **egyéni hozzáadása sorok** modul készen áll a érhető el, a Machine Learning kísérleteket.

## <a name="elements-in-the-xml-definition-file"></a>Az XML-definíciós fájl elemei
### <a name="module-elements"></a>A modul elemei
A **modul** elem az XML-fájl egy egyéni modul azonosítására szolgál. Több modul adható meg egy XML-fájl több **modul** elemek. A munkaterület minden modulja egy egyedi névvel kell rendelkeznie. Egy egyéni modult regisztrálni a neve megegyezik egy már meglévő egyéni modult, és felülírja a meglévő modul az újjal. Azonban az egyéni modulok lehet a neve megegyezik egy meglévő Azure Machine Learning-modul regisztrálva. Ha igen, azok megjelennek a **egyéni** a modulpalettán kategóriáját.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


Belül a **modul** elem, két további választható elem adhat meg:

* egy **tulajdonos** a modulba beágyazott elem  
* egy **leírás** elem, amely tartalmazza a szöveg, amely a modul gyors súgó jelenik meg, és ha mutat a modul a Machine Learning felhasználói felületén.

A modul elemekben karakterek korlátok szabályokat:

* Értékét a **neve** attribútumnak a **modul** elem legfeljebb 64 karakternél hosszabb. 
* A tartalmát a **leírás** elem legfeljebb 128 karakter hosszúságú.
* A tartalmát a **tulajdonos** elem legfeljebb 32 karakter hosszúságú.

Lehet, hogy egy modul eredmények determinisztikus vagy nondeterministic.* * alapértelmezés szerint, minden modul lévőknek tekintendők determinisztikus. Ez azt jelenti, hogy a megadott bemeneti paraméterek és egy nem változó készlete, a modul visszaküldje-e az azonos eredmények eacRAND vagy egy functionh futtatáskor. Ez a viselkedés, Azure Machine Learning Studio csak Újrafuttatja determinisztikus, ha a paraméter jelölésű modulok vagy a bemeneti adatok változásairól. A gyorsítótárazott eredményt visszaadó sokkal gyorsabb végrehajtási kísérletek emellett.

Nincsenek determinált, például VÉL vagy az aktuális dátum vagy idő függvény funkciókat. Ha a modul determinált függvényt használ, azt is adja meg, hogy a modul nem determinisztikus úgy, hogy a nem kötelező **isDeterministic** attribútumot **hamis**. Ez biztosítja, hogy a modul akkor fut újra, amikor a kísérlet fut, akkor is, ha a modul bemeneti és a paraméterek nem változtak. 

### <a name="language-definition"></a>Nyelv meghatározása
A **nyelvi** az XML-definíciós fájljának elem használatával adja meg az egyéni modul nyelv. R jelenleg az egyetlen támogatott nyelv. Értékét a **sourceFile** attribútum tartalmazza a függvény hívása a modul futtatásakor R-fájl nevének kell lennie. Ez a fájl a zip-csomagját részének kell lennie. Értékét a **entryPoint** attribútum neve, a hívott függvény, és meg kell egyeznie a forrás fájlban meghatározott érvénytelen függvény.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portok
Egy egyéni modul bemeneti és kimeneti portok gyermekelemei vannak megadva a **portok** az XML-definíciós fájljának szakasza. Ezek az elemek sorrendjét a elrendezés tapasztalt (UX) felhasználók határozza meg. Az első gyermek **bemeneti** vagy **kimeneti** szerepel a **portok** elem az XML-fájl lesz a bal szélső bemeneti portját a Machine Learning UX a
Adja meg mindegyik, és előfordulhat, hogy a kimeneti portra egy nem kötelező **leírás** , amely meghatározza a szöveg látható, ha az a port a Machine Learning felhasználói felületén keresztül vigye az egérmutatót gyermekelemet.

**Szabályok portok**:

* Maximális száma **bemeneti és kimeneti portok** minden 8 van.

### <a name="input-elements"></a>Bemeneti elemei
Bemeneti portok lehetővé teszik adatok átadása az R függvény és a munkaterületen. A **adattípusok** , amely a bemeneti portok a következők támogatottak: 

**A DataTable:** ehhez a típushoz átadódik az R egy data.frame funkciót. Tulajdonképpen bármely típusa (például a CSV-fájlok vagy a ARFF fájlok), és hogy Machine Learning által támogatott kompatibilisek-e **DataTable** egy data.frame automatikusan alakulnak. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

A **azonosító** társított minden egyes attribútum **DataTable** bemeneti porthoz egyedi értéknek kell tartoznia, és ezt az értéket meg kell egyeznie a megfelelő nevű az R-függvény paramétere.
Nem kötelező **DataTable** nem átadott kísérlet a bemeneti portok adható meg érték **NULL** átadott az R és az opcionális zip portok figyelmen kívül hagyja, ha a bemeneti nincs csatlakoztatva. A **isOptional** attribútum megadása nem kötelező, mind a **DataTable** és **Zip-** , és meg kell adnia *hamis* alapértelmezés szerint.

**Zip:** egyéni modulok fogad el bemenetként egy zip-fájlt. A bemeneti van csomagolva, a függvény a R működő könyvtárba

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Az egyéni R modul egy Zip-porthoz azonosítója nem kell megegyeznie a paramétereket a R függvény. Ennek oka az, a zip-fájl automatikusan az R munkakönyvtár ki kell olvasni.

**Bemeneti szabályok:**

* Értékét a **azonosító** attribútuma a **bemeneti** elemnek kell lennie egy érvényes R változónevet.
* Értékét a **azonosító** attribútuma a **bemeneti** elem nem lehet hosszabb 64 karakternél.
* Értékét a **neve** attribútuma a **bemeneti** elem nem lehet hosszabb 64 karakternél.
* A tartalmát a **leírás** elem nem lehet hosszabb 128 karakternél
* Értékét a **típus** attribútuma a **bemeneti** elemnek kell lennie *Zip-* vagy *DataTable*.
* Értékét a **isOptional** attribútuma a **bemeneti** elem nincs szükség (és *hamis* alapértelmezés szerint ha nincs megadva); de ha a célgyűjtemény meg van adva, akkor kell *igaz* vagy *hamis*.

### <a name="output-elements"></a>Kimeneti elemei
**Standard kimeneti portot:** kimeneti portok vannak leképezve a visszatérési érték a R függvényből, amelyek ezután felhasználhatók a további modulokat. *A DataTable* nem támogatott jelenleg csak a standard kimeneti port típusú. (Támogatása *tanulókkal* és *átalakítja* kapja.) A *DataTable* output típusúként van definiálva:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Az egyéni R modul, az értékét a kimenetek a **azonosító** attribútumnak nincs megfelel a semmit az R-parancsfájl, de ennek egyedinek kell lennie. A kimenet egy egy modul, az R függvény visszatérési értéke kell lennie egy *data.frame*. Ahhoz, hogy a támogatott adatobjektum-típus egynél több objektum kimeneti, a megfelelő kimeneti portok meg kell adni az XML-definíciós fájl, és az objektumok listáját küldi vissza kell. A kimeneti objektum kimeneti portot balról jobbra, amely tükrözi a sorrendet, amelyben az objektumok kerülnek, a visszaadott lista van rendelve.

Például, ha módosítani szeretné a **egyéni hozzáadása sorok** modul az eredeti két adatkészletek kimeneti *dataset1* és *dataset2*, mellett az új illesztett adatkészlet *dataset*, (sorrendje, balról jobbra,: *dataset*, *dataset1*, *dataset2*), majd határozza meg a kimeneti portok CustomAddRows.xml fájlban a következőképpen:

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


És a "CustomAddRows.R" a megfelelő sorrendben listájaként adja vissza az objektumok listáját:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**A képi megjelenítés kimeneti:** azt is megadhatja a kimeneti portra típusú *képi megjelenítés*, az R grafikus eszköz és a konzol kimeneti kimenete jelenít meg. Ezt a portot nem az R függvény kimeneti része, és nem zavarja a más típusú kimeneti port sorrendje. A képi megjelenítés port hozzáadása az egyéni modulok, vegye fel egy **kimeneti** elem értéke az *képi megjelenítés* a a **típus** attribútum:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Kimeneti szabályok:**

* Értékét a **azonosító** attribútuma a **kimeneti** elemnek kell lennie egy érvényes R változónevet.
* Értékét a **azonosító** attribútuma a **kimeneti** elem nem lehet hosszabb 32 karakternél.
* Értékét a **neve** attribútuma a **kimeneti** elem nem lehet hosszabb 64 karakternél.
* Értékét a **típus** attribútuma a **kimeneti** elemnek kell lennie *képi megjelenítés*.

### <a name="arguments"></a>Argumentumok
További adatok átadhatók R függvény a meghatározott modul paraméterei keresztül a **argumentumok** elemet. A modulok kiválasztásakor ezeket a paramétereket a Machine Learning felhasználói felület jobb szélső tulajdonságok ablaktábláján jelennek meg. Az argumentumok a támogatott típusok lehetnek, vagy létrehozhat egy egyéni számbavételi szükség esetén. Hasonló a **portok** elemek, **argumentumok** elemek lehet egy nem kötelező **leírás** elem, amely meghatározza a szöveg, ha az egérrel a paraméternév mutat.
Egy modul, például a defaultValue, minValue és maxValue választható tulajdonságok szerint attribútumok adhatók hozzá bármelyik argumentum egy **tulajdonságok** elemet. Érvényes tulajdonságait a **tulajdonságok** elem argumentum típusa határozza meg, és a támogatott argumentum típusú a következő szakaszban ismertetjük. Argumentumokat a **isOptional** tulajdonsága **"true"** nincs szükség a felhasználó számára adjon meg egy értéket. Ha az argumentum nincs megadva érték, majd a rendszer nem argumentumot a belépési pont függvény. A belépési pont függvény választható argumentumokat kell explicit módon kell kezelnie a függvény, pl. hozzárendelve a belépési pont függvény definícióját a NULL alapértelmezett értékű. Egy nem kötelező argumentumában csak érvényesíti más argumentum korlátait, azaz a min vagy max, ha a felhasználó által megadott értéket.
Csakúgy, mint a be- és kimenetekkel, rendkívül fontos, hogy rendelkezik-e a paraméterek hozzájuk rendelt egyedi azonosító érték. Gyors üzembe helyezési példában a társított id paraméter lett *swap*.

### <a name="arg-element"></a>Arg elem
A modul paraméter használatával van definiálva a **Arg** gyermekeleme a **argumentumok** az XML-definíciós fájljának szakasza. Például alárendelt elemei a **portok** részben, a paraméterek a rendezés a **argumentumok** szakaszban határozza meg a metódusban. a UX elrendezés A paraméterek jelennek meg a felső le a felhasználói felületen ugyanabban a sorrendben, amelyben az XML-fájlban vannak definiálva. Az itt felsorolt paramétereket támogatja a Machine Learning típusok. 

**int** – egész szám (32 bites) típusú paramétert.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Választható tulajdonságok*: **min**, **maximális**, **alapértelmezett** és **isOptional**

**kettős** – dupla típusú paraméter.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Választható tulajdonságok*: **min**, **maximális**, **alapértelmezett** és **isOptional**

**logikai** – a logikai paraméter által a UX jelölőnégyzettel jelölt

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Választható tulajdonságok*: **alapértelmezett** -hamis, ha nincs beállítva.

**karakterlánc**: szabványos karakterláncok

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Választható tulajdonságok*: **alapértelmezett** és **isOptional**

**ColumnPicker**: egy oszlop kiválasztási paraméter. Ez a típus a UX, egy Oszlopválasztó képezi le. A **tulajdonság** elem használatával itt adja meg a port, amelyen oszlop van kijelölve, ahol a cél port típusúnak kell lennie a azonosítóját *DataTable*. A kijelölés eredményét a kijelölt oszlop nevét tartalmazó karakterláncok listáját az R függvény van át. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Kötelező tulajdonságokban*: **portId** -bemenet elem azonosítója megegyezik-e típusú *DataTable*.
* *Választható tulajdonságok*:
  
  * **allowedTypes** -szűrők, amelyen kiválaszthatja a típusokat a oszlop. Érvényes értékek a következők: 
    
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

**Legördülő lista**: egy felhasználó által megadott felsorolt (legördülő) listán. A legördülő lista elemek belül vannak megadva a **tulajdonságok** elem használatával egy **elem** elemet. A **azonosító** minden **elem** egyedinek kell lennie, és egy érvényes R változó. Értékét a **neve** , egy **elem** látható szöveg és az értéket, amelyet az R függvény is szolgál.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Választható tulajdonságok*:
  * **alapértelmezett** -az alapértelmezett tulajdonság értékét meg kell felelnie az azonosító értéke az egyik a **elem** elemek.

### <a name="auxiliary-files"></a>Külső fájlok
Minden olyan fájlt, amely az egyéni modul ZIP-fájlja kerül lesz használható végrehajtási ideje alatt. A jelen könyvtárstruktúrák megmaradnak. Ez azt jelenti, hogy a fájl works forrás azonos helyileg Azure Machine Learning végrehajtása. 

> [!NOTE]
> Figyelje meg, hogy minden fájlok kibontása "src" könyvtárhoz, az összes elérési utat kell "src /" előtag.
> 
> 

Például hogy távolítsa el a NAs sorokat az adatkészletet, és is távolítsa el az ismétlődő sorokat előtt ellenőrizze a CustomAddRows kívánja, és a már leírt egy R függvény, amelyet, amely egy fájlban RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
A kiegészítő fájlt RemoveDupNARows.R CustomAddRows függvényében is forrás:

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

Ezt követően töltse fel az "CustomAddRows.R", "CustomAddRows.xml" és "RemoveDupNARows.R" egy egyéni R modult tartalmazó zip-fájl.

## <a name="execution-environment"></a>Végrehajtási környezet
A végrehajtási környezet az R-parancsfájl az R, ugyanazon verzióját használja, a **R-parancsfájl végrehajtása** modul és a ugyanazon alapértelmezett csomagok használhatók. Az egyéni modul belefoglalja ezeket az egyéni modul zip-csomagját is hozzáadhat további R csomagokat. Csak töltődnek be azokat az R-parancsfájl, mint az R környezetben. 

**A végrehajtási környezet korlátai** tartalmazza:

* Nem állandó fájlrendszer: ugyanabban a modulban több kísérletei közötti nem maradnak meg az egyéni modul futtatásakor írt fájlokat.
* Nincs hálózati hozzáférés

