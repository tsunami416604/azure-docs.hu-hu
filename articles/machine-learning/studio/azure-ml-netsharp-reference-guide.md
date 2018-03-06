---
title: "NET # Neurális hálózatokat Specification nyelvi útmutató – az Azure gépi tanulás |} Microsoft Docs"
description: "A Net # Neurális szintaxisának hálózatok nyelv, és példák segítségével Net # egyéni Neurális hálózat modell létrehozása"
services: machine-learning
documentationcenter: 
author: jeannt
manager: cgronlun
editor: 
ms.assetid: cfd1454b-47df-4745-b064-ce5f9b3be303
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/01/2018
ms.author: jeannt
ms.openlocfilehash: a166b45e7e482092006ddad276986b6f8b0f378c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Útmutató az Azure Machine Learning Net # Neurális hálózat nyelv

NET #, amely a Neurális hálózat architektúrák azonosítására szolgál a Microsoft által kifejlesztett nyelvet. Net # Neurális hálózat struktúra révén összetett struktúrák például mély Neurális hálózatokat vagy a tetszőleges dimenziók, vagyis a javításához kapcsolatos adatok, például a lemezkép, hang-, vagy videó convolutions definiálhat.

Ezek a környezetek is használhatja a Net # architektúra specifikációban:

+ Minden Neurális hálózat modul a Microsoft Azure Machine Learning Studióban: [Multiclass Neurális hálózat](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [két osztályú Neurális hálózat](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network), és [Neurális hálózat regressziós](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neurális hálózat funkciók MicrosoftML: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) és [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)az R nyelvhez és [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) a Python.


Ez a cikk ismerteti az alapvető fogalmakat és szintaxist használó Net # egyéni Neurális hálózat fejlesztéséhez szükséges: 

+ Neurális hálózat követelményei és az elsődleges összetevők definiálása
+ A szintaxis és a Net # nyelv kulcsszavát
+ Net # használatával létrehozott egyéni Neurális hálózatokat példák 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="neural-network-basics"></a>Neurális hálózat alapjai

Neurális hálózat struktúra áll a csomópontokra, amelyeket a rétegek, és a súlyozott kapcsolatok (illetve szélek) vannak rendszerezve a csomópontok között. A kapcsolatok irányt, és minden egyes kapcsolathoz a forráscsomópont és a célcsomópontra.  

Minden egyes trainable réteg (egy rejtett vagy egy kimeneti réteg) rendelkezik egy vagy több **kapcsolat kötegek**. Egy kapcsolat köteg egy forrás réteg és a forrás réteg közötti kapcsolatok meghatározása áll. Egy adott csomagban lévő összes kapcsolat ossza meg az azonos forrás réteg és a azonos cél réteg. Net # kapcsolat csomag egyik gyermekszoftver tekinthető, hogy a csomag cél réteg tartoznak.

NET # támogatja a különféle típusú csomagok segítségével szabhatja testre a módon bemenetek vannak leképezve a Rejtett réteg és a kimenetek leképezve.

Az alapértelmezett vagy a standard csomagot egy **teljes csomag**, az a forrás-réteg minden csomópontja csatlakozó minden csomópont, a cél rétegben.

Emellett Net # használatát támogatja a következő négy speciális kapcsolat csomagokat:

+ **Szűrt kötegek**. A felhasználó a réteg forráscsomópont és a réteg célcsomópont használatával adhatja meg a predikátum. Csomópontok kapcsolódnak, amikor a predikátum értéke igaz.

+ **Convolutional csomagok**. A felhasználó csomópontok kis környékeken definiálhat a forrás rétegben. A cél rétegben minden csomópont csatlakozik-e a csomópontok a forrás rétegben egy hálózatok.

+ **Csomagok készletezését** és **válasz normalizálási kötegek**. Ezek hasonlóak convolutional kötegek abban, hogy a felhasználó határozza meg a kis környékeken csomópontok, a forrás rétegben. A különbség, hogy a súlyok ezeket a csomagokat a széleinek nincsenek trainable. Ehelyett egy előre definiált függvény alkalmazzák a forrás csomópont értékek megadásával határozza meg a célként megadott értékét.


## <a name="supported-customizations"></a>Támogatott testreszabások

Az Azure Machine Learning létrehozott Neurális hálózat modellek architektúrájának nagymértékben testreszabható Net # használatával. A következőket teheti:

+ Rejtett rétegek készítését és minden egyes rétegben található csomópontok számának.
+ Adja meg, hogyan rétegek csatlakozniuk kell egymáshoz.
+ Adja meg a Speciális kapcsolat struktúrák, például convolutions és a súlyozást csomagok megosztása.
+ Adjon meg másik aktiválási funkciók.

További részletek a meghatározás nyelvi szintaxisa: [struktúra Specification](#Structure-specifications).  

Néhány általános gépi tanulási a feladatok, simplex bonyolult, a Neurális hálózatokat meghatározásának tekintse meg a [példák](#Examples-of-Net#-usage).

## <a name="general-requirements"></a>Általános követelmények

+ Pontosan egy kimeneti réteg legalább egy bemeneti réteg és nulla vagy több rejtett rétegben kell. 
+ Minden egyes réteg csomópontok, tetszőleges dimenziók téglalap alakú tömbje fogalmilag rendezett rögzített számú rendelkezik. 
+ Bemeneti rétegek társított képzett paraméter nélküli, és ahol példányadatokat belép a hálózatot képviselő. 
+ Trainable rétegek (rejtett, és a kimeneti rétegek) társított súlyok és elfogultság ismert képzett paraméterek. 
+ A forrás és cél csomópontok külön rétegekben kell lennie. 
+ Kapcsolatok aciklikus; kell lennie. Ez azt jelenti nem lehet a kezdeti forráshelyen csomópont vezető kapcsolatok láncolata.
+ A kimeneti réteg nem lehet kapcsolat köteg forrás réteget.  

## <a name="structure-specifications"></a>Struktúra specifikációk

Három szakaszból tevődik össze a Neurális hálózat struktúra specifikációval: a **konstans deklarációjában**, a **deklaráció réteg**, a **kapcsolat deklaráció**. Szerepel továbbá egy nem kötelező **fájlmegosztás a nyilatkozatot** szakasz. A szakaszok bármilyen sorrendben adható meg.

## <a name="constant-declaration"></a>Konstans deklarációjában

Egy konstans deklarációjában nem kötelező megadni. Ez lehetővé teszi a Neurális hálózat definíciójának máshol használt értékek megadhatók. A nyilatkozat utasítás áll egy egyenlőségjellel és egy érték kifejezést azonosítója.

Például a következő utasítás definiál egy állandó `x`:  

`Const X = 28;`

Egyidejűleg két vagy több állandók megadásához tegye a típusú azonosító neveket és értékeket kell használni, és pontosvesszővel válassza el egymástól elválasztani őket. Példa:

`Const { X = 28; Y = 4; }`

Minden egyes hozzárendelési kifejezés jobb oldalán lehet egy egész számot, egy valós szám, egy logikai érték (IGAZ vagy hamis) vagy egy kifejezésnek. Példa:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Réteg nyilatkozat

A réteg szükség esetén. Azt határozza meg, méretének és a réteg, beleértve a kapcsolat kötegek és attribútumok forrását. A nyilatkozat utasítás kezdődik-e a neve, a réteg (bemeneti, rejtett vagy kimeneti), a dimenziók a réteg (egy pozitív egész számok rekord) követ. Példa:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;  
```

+ A termék dimenzió a rétegben található csomópontok számának. Ebben a példában a rendszer két dimenzió [5,20], ami azt jelenti, hogy a réteg 100 csomópontok szerepelnek.
+ A rétegek deklarálható bármilyen sorrendben, egy kivétellel: Ha egynél több bemeneti réteg van definiálva, deklarálva van a sorrend meg kell egyeznie a szolgáltatások a bemeneti adatok sorrendjét.

Adja meg, hogy egy rétegben található csomópontok számának automatikusan meghatározni a `auto` kulcsszó. A `auto` kulcsszó különböző hatások, attól függően, hogy a réteg van:

+ A bemeneti réteg nyilatkozat, a csomópontok száma a szolgáltatások a bemeneti adatok száma.
+ A Rejtett réteg nyilatkozatot, a csomópontok száma az a szám, a paraméter értéke meg **rejtett csomópontok száma**.
+ A kimeneti réteg nyilatkozat, a csomópontok számát: a két osztályú osztályozási, a regresszióra és a multiclass besorolási kimeneti csomópontok száma egyenlő 1 2.

A következő hálózatdefiníció például lehetővé teszi, hogy az automatikusan meghatározott összes réteg mérete:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;  
```

Egy olyan trainable réteghez (rejtett vagy kimeneti rétegek) réteg nyilatkozatot is választhatóan a kimeneti függvény (más néven az aktiválási függvény), amely alapértelmezés szerint az **sigmoid** a besorolási modell, és  **lineáris** regressziós modell. Akkor is, ha használja az alapértelmezett, akkor is explicit módon állapot aktiválás függvény jobb érthetőség kedvéért bizonyos igény.

A következő kimeneti-funkciók támogatottak:

+ sigmoid
+ lineáris
+ softmax
+ rlinear
+ Négyzetes
+ sqrt
+ srlinear
+ ABS
+ TANH
+ brlinear

Például a következő nyilatkozatot használ a **softmax** függvény:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Kapcsolat nyilatkozat

Határozza meg a trainable réteg, után azonnal meghatározta a rétegek közötti kapcsolatok kell deklarálnia. A kapcsolat köteg deklaráció kezdődik-e a kulcsszó `from`, utána pedig a nevét a csomag forrás réteg és milyen típusú kapcsolat csomag létrehozásához.

Jelenleg kapcsolat csomagok öt típusú támogatottak:

+ **Teljes** csomagokat, a kulcsszó által jelzett `all`
+ **Szűrt** csomagokat, a kulcsszó által jelzett `where`, utána pedig a predikátum kifejezés
+ **Convolutional** csomagokat, a kulcsszó által jelzett `convolve`, utána pedig a konvolúció attribútumok
+ **Készletezését** csomagokat, a kulcsszavak által jelzett **készlet maximális** vagy **készlet témakörök**
+ **Válasz normalizálási** csomagokat, a kulcsszó által jelzett **válasz alapértelmezetté**

## <a name="full-bundles"></a>Teljes kötegek

Egy teljes kapcsolat csomag minden csomópontjáról kapcsolatot tartalmaz, mindegyik csomópontra a cél rétegben a forrás rétegben. Ez az az alapértelmezett hálózati kapcsolat típusa.

## <a name="filtered-bundles"></a>Szűrt kötegek

Köteg végrehajtása szűrt kapcsolaton keresztül specifikáció tartalmazza a predikátum, szintaktikailag, kifejezett jelentős, például a C# lambda kifejezésben. Az alábbi példa meghatározza, hogy két szűrt csomagokat:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  
```

+ A predikátum a `ByRow`, `s` képviselő index a bemeneti réteg csomópontok a téglalap alakú tömbbe paraméter `Pixels`, és `d` indexet a Rejtett réteg csomópontok a tömbbe jelölő paraméter `ByRow`. Mindkét típusú `s` és `d` hosszúságú két számokból álló rekordot van. Fogalmilag `s` keresztül az egész számok minden pár címtartományok `0 <= s[0] < 10` és `0 <= s[1] < 20`, és `d` az egész számok, minden pár keresztül címtartományok `0 <= d[0] < 10` és `0 <= d[1] < 12`. 

+ A jobb oldalon a predikátum kifejezés nincs egy feltételt. Ebben a példában minden egyes értékéhez `s` és `d` úgy, hogy a feltétel igaz, nincs-e a réteg forráscsomópont él a célcsomópontra réteg. Ebből kifolyólag a szűrőkifejezés azt jelzi, hogy, hogy a csomag tartalmaz-e a kapcsolat által meghatározott csomópontról `s` a csomópont által meghatározott `d` az összes olyan esetekben, amikor s [0] [0] d egyenlőnek.

Másik lehetőségként a szűrt köteg súlyok készlete is megadhat. Az érték a **súlyok** attribútumnak kell lennie egy rekordot a pontértékek határozzák meg a csomag kapcsolatok számának megfelelő hosszúságú lebegőpontos. Alapértelmezés szerint a súlyok véletlenszerűen generált.

Súlyozási értékeket a cél csomópont indexe szerint vannak csoportosítva. Ez azt jelenti, ha az első célcsomópont csatlakozik K forrás csomópontok, az első `K` elemei a **súlyok** rekordot a súlyok a első célcsomópont index forrássorendben. Ugyanez vonatkozik, a fennmaradó cél csomópontok.

Akkor adja meg a súlyok közvetlenül konstans érték lehet. Például ha korábban már megismerte a súlyok, megadhatja azokat állandók ezen szintaxis használatával:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Convolutional kötegek

Ha a betanítási adatok struktúrája a homogén, convolutional kapcsolatok gyakran használják az adatok magas szintű funkciók további. Például kép, hang- vagy videó, térbeli vagy időbeli granularitása lehet az adatokat viszonylag egységes.  

Convolutional kötegek alkalmaz téglalap alakú **kernelek** , amely a dimenziók keresztül van ütközik. Tulajdonképpen minden kernel néven a helyi környékeken, alkalmazza a súlyok készletét **kernel-alkalmazásokra**. A forrás rétegben, amely hivatkozunk a csomópont minden kernel alkalmazás megfelel-e a **központi csomópont**. A kernel súlyozását sok kapcsolatok között vannak megosztva. Convolutional a köteg minden egyes kernel téglalap alakú pedig minden kernel-alkalmazásokra méretének.  

Convolutional csomagokat támogatja a következő attribútumokat:

**InputShape** határozza meg a forrás réteg dimenzióinak a convolutional köteg céljából. Az értéknek pozitív egész számok rekordot kell lennie. Az egész számok szorzatát a forrás rétegben található csomópontok számának egyenlőnek kell lennie, de egyéb, nem kell a forrás réteg deklarált dimenzióinak kereséséhez. Ez a rekord hossza válik a **aritása** értékét a convolutional csomagot. Aritása általában a számú argumentum vagy egy függvény által végrehajtható operandusok hivatkozik.

Az alakzat és helyét. a kernelek megadásához használja az attribútumok **KernelShape**, **Stride**, **kitöltési**, **LowerPad**, és  **UpperPad**:   

+ **KernelShape**: (kötelező) meghatározza a convolutional köteg minden kernel dimenzióinak. Az értéknek kell lennie, amely a kötegben szereplő argumentumszámmal egyenlő hosszal pozitív egész számok rekordot. Lehet, hogy minden összetevő a rekord nem lehet nagyobb a megfelelő összetevője **InputShape**. 

+ **STRIDE**: (opcionális) határozza meg, amely a központi csomópontok közötti távolság szerint (minden dimenzió egy lépésköz mérete), konvolúció mozgó lépés mérete. Az értéknek kell lennie, amely a csomag az aritása hosszal pozitív egész számok rekordot. Lehet, hogy minden összetevő a rekord nem lehet nagyobb a megfelelő összetevője **KernelShape**. Az alapértelmezett értéke egynek összes összetevőkkel rekordot. 

+ **Megosztás**: (nem kötelező) határozza meg a minden egyes dimenziójának a konvolúció megosztásának súlyt. Az érték lehet egy logikai érték vagy egy logikai érték, amely a csomag az aritása hosszúságú rekord. Egyetlen logikai értéket az időtartam csak a megadott értéknél összes összetevőkkel megfelelő hosszúságú rekord lehet. Az alapértelmezett érték: egy rekord, amely az összes IGAZ érték áll. 

+ **MapCount**: a convolutional köteg maps szolgáltatás számát (nem kötelező) meghatározza. Az érték egy pozitív egész szám vagy egy pozitív egész számok az aritása a kötegben hosszúságú rekord lehet. Csak egyetlen egész értéket az időtartam csak az első a megadott értéknél összetevőkkel megfelelő hosszúságú rekord lehet, és a fennmaradó összetevők egy egyenlő. Az alapértelmezett érték: egyet. Teljes száma a szolgáltatás a maps a termék a rekord összetevőt. A összetevői között és az összes faktoring meghatározza, hogy a szolgáltatás térkép értékek csoportosításának a cél csomópontok. 

+ **Súlyozás**: (opcionális) határozza meg a csomag kezdeti súlyok. Az érték lebegőpontos pontértékek, amely kernelek hányszor hosszal súlyok száma / kernel, a cikk későbbi részében meghatározott rekordot kell lennie. Az alapértelmezett súlyozás véletlenszerűen jönnek létre.  

Kitöltési,-folyamatban, egymást kölcsönösen kizáró tulajdonságok szabályozó tulajdonságok két csoportjára van:

+ **Kitöltési**: (opcionális) megállapítja e bemeneti használatával kell lenniük egy **alapértelmezett kitöltő séma**. Az érték lehet egy logikai érték, vagy egy logikai érték, amely a csomag az aritása hosszúságú rekord lehet. 

    Egyetlen logikai értéket az időtartam csak a megadott értéknél összes összetevőkkel megfelelő hosszúságú rekord lehet. 
    
    Egy dimenzió értéke igaz, ha a forrás lesz logikailag kiegészítve az adott dimenzióban kiegészítő rendszermag alkalmazásokhoz, nulla értékű cellákkal úgy, hogy az adott dimenzióban első és utolsó kernelek központi csomópontjai legyenek a első és utolsó csomópontokat, amelyek a forrás rétegben dimenzió. Így minden dimenzió "típusú" csomópontok száma határozza meg automatikusan, pontosan megfelelően `(InputShape[d] - 1) / Stride[d] + 1` kernelek a tömöttnek forrás rétegbe. 
    
    Egy dimenzió értéke HAMIS, ha a kernelek határozzák meg, hogy mindkét oldalon kimenő fennmaradó csomópontok száma (legfeljebb 1 különbségek) megegyezik. Ez az attribútum alapértelmezett értéke egy rekord összes összetevőkkel értéke FALSE.

+ **UpperPad** és **LowerPad**: (nem kötelező) adja meg nagyobb mértékben vezérelheti a használandó szövegtávolság mértéke. **Fontos:** lehet, hogy ezek az attribútumok meghatározott if és csak akkor, ha a **kitöltési** fenti tulajdonság ***nem*** definiálva. Az értékeket, amelyek a kötegben szereplő argumentumszámmal hosszúságú egész rekordokat kell lennie. Ha ezek az attribútumok meg van adva, "típusú" csomópontokat ad hozzá a bemeneti réteg minden dimenzió az alsó és felső végén. Minden dimenzió az alsó és felső végződik hozzáadandó csomópontok száma határozza meg **LowerPad**[i] és **UpperPad**[i] osztályban. 

    Biztosítsa, hogy kernelek csak "tényleges" és "típusú" csomópont nem az, hogy a következő feltételeknek kell teljesülniük:
      - Az egyes összetevők **LowerPad** kell feltétlenül kisebb, mint `KernelShape[d]/2`. 
      - Az egyes összetevők **UpperPad** nem nagyobbnak kell lennie `KernelShape[d]/2`. 
      - Ezek az attribútumok alapértelmezett értéke egy rekord összes összetevőkkel, mint 0. 

    A beállítás **kitöltési** = true lehetővé teszi, hogy a legtöbb kitöltési tartani "" a kernel belső "valós" bemeneti szükséges. A kimeneti méretének kiszámításához kicsit matematikai értékre változik. Általában a kimeneti méretének *D* számítja ki, hogy `D = (I - K) / S + 1`, ahol `I` bemeneti mérete `K` kernel mérete `S` van a stride és `/` szám (szám nullához van ). Ha UpperPad = [1, 1], a bemeneti mérete `I` tulajdonképpen 29, és így `D = (29 - 5) / 2 + 1 = 13`. Azonban ha **kitöltési** lényegében = true, `I` lekérdezi bumped által `K - 1`; ezért `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Az értékek megadásával **UpperPad** és **LowerPad** elérhetővé, mint ha csupán be a térközt több ellenőrzést **kitöltési** = true.

Convolutional hálózatok és az alkalmazásokkal kapcsolatos további információkért lásd: ezek a cikkek: 

+ [http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
+ [http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
+ [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Csomagok készletezése

A **köteg készletezését** vonatkozik geometriai hasonló convolutional kapcsolatot, de forrás csomópont értékek előre meghatározott funkciókat használ kapcsolattípusokból a cél a csomópont-érték. Emiatt készletezésével kötegek rendelkezik (súlyok vagy elfogultság) trainable állapot nélküli. Egyesítési csomagokat támogatja kivételével minden convolutional attribútumot **megosztás**, **MapCount**, és **súlyok**.

Általában a szomszédos készletezésével egységek összesített kernelek nem lehetnek átfedésben. Ha Stride [d] [d] KernelShape minden dimenzió, a kapott réteg a hagyományos helyi készletezésével szintje, amely általában alkalmazottja convolutional Neurális hálózatokat. Minden célcsomópont kiszámítja a maximális vagy a forrás rétegben a kernel tevékenységeit középértéke.  

Az alábbi példában látható készletezésével csomagot: 

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }  
```

+ A csomag az aritása a következő: 3: Ez azt jelenti, hogy a rekordok hosszát `InputShape`, `KernelShape`, és `Stride`. 
+ A forrás rétegben csomópontok száma `5 * 24 * 24 = 2880`. 
+ Ennek oka egy hagyományos helyi készletezésével réteg **KernelShape** és **Stride** egyenlő. 
+ A cél rétegben csomópontok száma `5 * 12 * 12 = 1440`.

Egyesítési rétegek kapcsolatos további információkért lásd: ezek a cikkek:  

+ [http://www.cs.Toronto.edu/~hinton/absps/imagenet.PDF](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (szakaszban 3.4)
+ [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
+ [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Válasz normalizálási kötegek

**Válasz normalizálási** egy helyi normalizálási rendszer Geoffrey Hinton, először bevezetett és mások, a dokumentum [ImageNet Classiﬁcation Convolutional Neurális hálózatokat a](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). 

Válasz normalizálási Neurális hálók általánosítása elősegítésére szolgál. Amikor egy idegsejt aktiválási nagyon magas szintű folyamatban van, egy helyi válasz normalizálási réteg mellőzi a környező idegsejtek csoportjának viselkedését aktiválási szintjét. Ehhez három paraméterekkel (`α`, `β`, és `k`) és egy convolutional struktúra (vagy hálózatok alakzat). A cél rétegben minden idegsejt **y** idegsejt megfelel **x** a forrás rétegben. Az aktiválási szintjét **y** képlettel a következő, ahol `f` idegsejt, az aktiválási szintje és `Nx` a kernel (vagy a készlet, amely tartalmazza a idegsejtek csoportjának viselkedését a helyek, a **x**), a következő convolutional struktúra által meghatározott módon:  

![a képlethez convolutional struktúra](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Válasz normalizálási kötegek támogatja a convolutional attribútumok kivéve **megosztás**, **MapCount**, és **súlyok**.  

+ Ha a kernel tartalmazza-e, azonos leképezés idegsejtek csoportjának viselkedését ***x***, a rendszer séma nevezzük **normalizálási képezi le azonos**. Azonos térkép normalizálási, az első koordináta meghatározásához **InputShape** 1 értéket kell megadni.

+ Ha a kernel tartalmazza-e az azonos térbeli helyzetben idegsejtek csoportjának viselkedését ***x***, de a idegsejtek csoportjának viselkedését a maps-más, a rendszer séma neve **keresztben leképezi a normalizálási**. Az ilyen típusú válasz normalizálási egy formája, amelyet a típus van a valós idegsejtek csoportjának viselkedését, többek között a különböző térképeken számított idegsejt kimenetek nagy aktiválási szintjeinek verseny létrehozása inspirálta oldalirányú gátló valósítja meg. Meghatározásához maps normalizálási között, az első koordináta nagyobb, mint egy és a maps száma nem lehet nagyobb egész számnak kell lennie, és a koordináták a többi 1 értéket kell megadni.

Válasz normalizálási csomagok forrás csomópont értékek megadásával határozza meg a cél csomópont érték előre definiált függvényt alkalmazni, mert nincs trainable állapot (súlyok vagy elfogultság) rendelkeznek.

> [!NOTE]
> A célfájl layer csomópontja idegsejtek csoportjának viselkedését, amelyek a kernelek központi csomópont felel meg. Például ha `KernelShape[d]` páratlan, majd `KernelShape[d]/2` felel meg a központi kernel csomópont. Ha `KernelShape[d]` az még akkor is, a központi csomópont `KernelShape[d]/2 - 1`. Ezért ha `Padding[d]` hamis, az első és az utolsó `KernelShape[d]/2` csomópontok nem rendelkeznek megfelelő csomópontokat a cél rétegben. Ez a helyzet elkerülése érdekében adja meg a **kitöltési** mint [értéke true, true,..., igaz].

A korábban ismertetett négy attribútumok mellett válasz normalizálási csomagok is támogatja a következő attribútumok:

+ **Alpha**: (kötelező) adja meg egy lebegőpontos érték, amely megfelelne a `α` a korábbi képletben. 
+ **Beta**: (kötelező) adja meg egy lebegőpontos érték, amely megfelelne a `β` a korábbi képletben. 
+ **Eltolás**: (nem kötelező) adja meg egy lebegőpontos érték, amely megfelelne a `k` a korábbi képletben. Alapértelmezés szerint az 1.

A következő példa egy válasz normalizálási köteg ezek az attribútumok használatával határozza meg:  

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }  
```

+ A forrás réteg öt 12 x 12 1440 csomópontok összesítés aof léptékű maps tartalmazza. 
+ Értékének **KernelShape** jelzi, hogy ez egy azonos normalizálási térképréteg, ahol a pontos 3 x 3 téglalap az. 
+ Az alapértelmezett érték **kitöltési** értéke HAMIS, a célfájl layer csak 10 csomópontok be van minden dimenzió. Egy csomópont a cél rétegben, amely megfelel a forrás-réteg minden csomópontja egészítse kitöltési = [true, true, true]; és RN1 méretének módosítása [5, 12, 12].  

## <a name="share-declaration"></a>Fájlmegosztás a nyilatkozatot

NET # opcionálisan is támogatja a megosztott súlyozással több csomagokat. A súlyok bármely két kötegek megoszthatók, ha azok struktúrák azonos. A következő szintaxist a megosztott súlyozással rendelkező csomagok határozza meg:  

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

Például a következő megosztás-adja meg a réteg nevek, amely azt jelzi, hogy a súlyok és a elfogultság megosztott:  

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases  
```

+ A bemeneti szolgáltatások particionáltak két egyenlő méretű bemeneti rétegben. 
+ A Rejtett réteg majd számítási a két bemeneti rétegek magasabb szintű funkciók. 
+ A megosztás-deklaráció azt jelenti, hogy *H1* és *H2* a a megfelelő bemenetei ugyanúgy kell számolni.  

Azt is megteheti ez sikerült lehet megadni két külön megosztás-deklarációk az alábbiak szerint:  

```Net#
share { Data1 => H1, Data2 => H2 } // share weights  
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases  
```

A rövid alak: / is használhatja, csak ha a rétegek csomagban szerepel. Megosztás általában lehetséges csak akkor, ha a megfelelő struktúra megegyezik, ami azt jelenti, hogy rendelkeznek a azonos méretűnek, azonos convolutional geometriai és így tovább.  

## <a name="examples-of-net-usage"></a>Net # használati példák

Ez a témakör néhány példa arra, hogyan használhatja a Net # rejtett réteg hozzáadásához meghatározhatja, hogy a Rejtett réteg más rétegeiből kommunikál, és állítsa be convolutional hálózatok.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Adja meg egy egyszerű egyéni Neurális hálózat: "Hello, World" – példa

Ez egyszerű példa bemutatja, hogyan, amely rendelkezik egy rejtett rétegben Neurális hálózat modell létrehozásához.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;  
```

A példa bemutatja, néhány alapvető parancsok az alábbiak szerint:  

+ Az első sor meghatározza a bemeneti réteg (nevű `Data`). Ha a `auto` kulcsszóval, a Neurális hálózat automatikusan felveszi a szolgáltatás az összes oszlop a bemeneti példák. 
+ A második sor hoz létre a rejtett rétegben. A név `H` a rejtett rétegben, amely 200 csomópont van rendelve. Ez a réteg teljesen csatlakozni a bemeneti réteg.
+ A harmadik sorban határozza meg a kimeneti réteg (nevű `O`), 10 kimeneti csomópontok, amely tartalmazza. Neurális hálózat besorolást használják, ha van egy kimeneti csomópont / osztály. A kulcsszó **sigmoid** azt jelzi, hogy a kimeneti függvénynek a kimeneti réteg van hozzárendelve.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Adja meg a több rejtett réteg: számítógép átfogóan bemutató példa

A következő példa bemutatja, hogyan adhat meg egy kicsit bonyolultabb Neurális hálózat, a több egyéni rejtett réteg.  

```Net#
// Define the input layers 
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100] 
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]  
{
from Gather all;
from MetaData all;
}  
```

Ebben a példában a Neurális hálózatokat nyelv több funkcióit mutatja be:

+ A struktúrának van két bemeneti réteg `Pixels` és `MetaData`.
+ A `Pixels` réteg rétegeket célfájl, két kapcsolat csomagokat, a forrás-rétege `ByRow` és `ByCol`.
+ A rétegek `Gather` és `Result` cél rétegek több kapcsolat csomagokat a rendszer.
+ A kimeneti réteg `Result`, a cél réteg két kapcsolat kötegek; egy, a második szintű rejtett réteg `Gather` cél réteg, míg a másik, a bemeneti réteg `MetaData` cél rétegként.
+ A Rejtett réteg `ByRow` és `ByCol`, adja meg a szűrt kapcsolat predikátum kifejezések használatával. Pontosabban, a csomópont `ByRow` : [x, y] csatlakozik-e a csomópontok `Pixels` , hogy rendelkezik az első index koordinálja a csomópont egyenlőnek csomagazonosítóját az első összehangolják x. Hasonlóképpen, a csomópont `ByCol` : [x, y] csatlakozik-e a csomópontok `Pixels` második koordinálja, y, hogy rendelkezik a második index koordinálja a csomópont egyik végpontjára.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Adja meg a multiclass besorolási convolutional hálózati: számjegy felismerés – példa

A következő hálózati meghatározását arra tervezték, hogy ismeri fel azt a számot, és azt szemlélteti, hogy néhány speciális technikák Neurális hálózat testreszabásához.  

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve 
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve 
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;  
```

+ A struktúrának van egy bemeneti rétegben `Image`.
+ A kulcsszó `convolve` azt jelzi, hogy a rétegek nevű `Conv1` és `Conv2` convolutional rétegek vannak. Minden, a réteg nyilatkozatok követi a konvolúció attribútumok listáját.
+ A háló harmadik rejtett réteg, `Hid3`, amely teljes mértékben csatlakozik-e a második rejtett réteg `Conv2`.
+ A kimeneti réteg `Digit`, csak a harmadik rejtett réteg csatlakozik `Hid3`. A kulcsszó `all` azt jelzi, hogy a kimeneti réteg teljesen csatlakozik `Hid3`.
+ Az aritása a konvolúció három: a rekordok hosszát `InputShape`, `KernelShape`, `Stride, and `megosztás ". 
+ A súlyok / kernel száma `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. Vagy `26 * 50 = 1300`.
+ Kiszámolhatja, minden egyes rejtett rétegben található csomópontok az alábbiak szerint:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5` `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ A csomópontok száma kerülhet sor, a réteg deklarált dimenzióinak használatával [50, 5, 5], az alábbiak szerint: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Mivel `Sharing[d]` FALSE csak `d == 0`, kernelek száma `MapCount * NodeCount\[0] = 10 * 5 = 50`. 

## <a name="acknowledgements"></a>Nyugták

A Net # nyelv Neurális hálózatokat architektúrájának testreszabásához fejlesztette ki Microsoft Shon Katzenberger (felelős mérnök, gépi tanulás) és ALEKSZEJ Kamenev (szoftver visszafejtés, a Microsoft Research). Ez belső használatra készült a gépi tanulási projektek és alkalmazások szövegelemzések kép észlelési kezdve. További információkért lásd: [Neurális háló Azure ml - Net # bemutatása](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
