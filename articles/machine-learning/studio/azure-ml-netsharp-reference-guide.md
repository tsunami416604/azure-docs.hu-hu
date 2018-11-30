---
title: 'NET # Neurális hálózati – az Azure Machine Learning Studióban |} A Microsoft Docs'
description: 'Szintaxisa a Net # Neurális hálózatok specifikációs nyelv együtt példa bemutatja, hogyan hozhat létre egy egyéni Neurális hálózat modell Net # az Azure Machine Learning Studio használatával.'
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: cfd1454b-47df-4745-b064-ce5f9b3be303
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/01/2018
ms.openlocfilehash: 3aa364e92dd7ce3742d28ac2b36d9a7f16cbebbf
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52315307"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio"></a>Útmutató az Azure Machine Learning Studio Net # Neurális hálózati specifikációs nyelvhez

NET # nyelv, amely a Neurális hálózat architektúráinak definiálására használatos a Microsoft által kifejlesztett. Net # Neurális hálózat struktúráját használatával lehetővé teszi összetett többek között a Neurális hálózatokat vagy tetszőleges dimenziók, amelyekről ismert, hogy a adatok, például a kép, hang vagy videó learning javítása convolutions meghatározásához.

Ezek a környezetek a Net # architektúra specifikációjának használhatja:

+ A Microsoft Azure Machine Learning Studióban az összes Neurális hálózati modulok: [osztályú Neurális hálózat](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [Two-Class Neurális hálózat](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network), és [Neurális hálózat regresszió](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ MicrosoftML Neurális hálózat függvényei: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) és [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)az R nyelvhez és [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) Pythonhoz készült.


Ez a cikk ismerteti az alapvető fogalmait és a egy egyéni, használatával Net # Neurális hálózat kialakításához szükséges szintaxist: 

+ Neurális hálózat követelményei és az elsődleges összetevőket definiálása
+ Szintaxisát és kulcsszavait a Net # specifikációs nyelv
+ Példák be egyéni Neurális hálózatokra létrehozott Net # használatával 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="neural-network-basics"></a>Neurális hálózat alapjai

A csomópontok közötti csomópontot, amely a rétegek, és a súlyozott kapcsolatok (és élek) vannak rendszerezve Neurális hálózat struktúra áll. A kapcsolatok irányt, és minden egyes kapcsolat rendelkezik a forráscsomóponton, és a célcsomópontra.  

Minden egyes trainable réteg (a rejtett vagy egy kimenete rétegen) rendelkezik egy vagy több **kapcsolat csomagjaiból**. Egy kapcsolat a csomag forrás réteg és a egy adott forrás réteg kapcsolatot specifikace áll. A megadott csomagban lévő összes kapcsolatot ossza meg az azonos forrásból réteg és a azonos cél-réteg. A Net # kapcsolat csomag egyik gyermekszoftver a csomag cél réteghez tartozó tekintendők.

NET # támogatja a különböző típusú kapcsolat segítségével módszert bemenetei testreszabhatja kötegbe vannak rejtett réteget hozzárendelve, és a kimenetek leképezve.

Az alapértelmezett vagy a standard csomag van egy **teljes csomag**, amelyek a forrás-réteg minden egyes csomópont csatlakozik a cél-réteg minden csomópontján az.

Ezenkívül Net # támogatja a következő négy típusú speciális kapcsolati csomagok:

+ **A szűrt csomagjaiból**. A felhasználó egy predikátum adhatja meg a forráscsomóponton réteg és a réteg a célcsomóponton használatával. Csomópontok csatlakoznak, amikor a predikátum értéke igaz.

+ **Konvolúciós csomagjaiból**. A felhasználó csomópontok kis környékeken definiálhat a forrás-rétegben. A cél-réteg minden egyes csomópontjának van csatlakoztatva, a forrás-réteget a csomópontok egy helyek.

+ **Csomagok készletezési** és **válasz normalizálási csomagjaiból**. Ezek hasonlóak konvolúciós csomagjaiból, hogy a felhasználó határozza meg a kis környékeken csomópontok, a forrás-rétegben. A különbség az, hogy ezek a csomagok a élt súlyozását ne legyenek trainable. Ehelyett egy előre meghatározott függvény a cél csomópont érték meghatározásához a forrás-csomópont értékei vonatkozik.


## <a name="supported-customizations"></a>Támogatott testreszabások

Az architektúra a Neurális hálózat modellek Azure Machine Learning szolgáltatásban létrehozott Net # használatával nagymértékben testre szabható. A következőket teheti:

+ Hozzon létre rejtett réteget, és minden egyes rétegben lévő csomópontok számát.
+ Adja meg, hogyan rétegek kapcsolódnia kell egymáshoz.
+ Adja meg a speciális kapcsolati és -struktúrákat, convolutions súly csomagjaiból megosztása.
+ Adjon meg másik aktiválási funkciók.

A specifikáció nyelv szintaxisát a részletekért lásd: [struktúra specifikáció](#Structure-specifications).  

Néhány gyakori machine learning-feladatokat, az összetett, egyoldalasan Neurális hálózatok definiálása példák: [példák](#Examples-of-Net#-usage).

## <a name="general-requirements"></a>Általános követelmények

+ Pontosan egy kimeneti réteg legalább egy bemeneti réteg és nulla vagy több rejtett réteget kell. 
+ Az egyes rétegek rendelkezik egy rögzített csomópontok számát, a koncepciót tekintve egy téglalap alakú tömb tetszőleges méretű szerint rendezve. 
+ Bemeneti rétegek nem lehetnek kapcsolódó betanított paraméterei, és ahol a példányok adatait belép a hálózat képviseli. 
+ (A rejtett, és a kimeneti rétegek) trainable rétegek betanított paraméterek, más néven súlyok és milyen előítéletek befolyásolják vannak társítva. 
+ A forrás- és célcsomópontok külön rétegekben kell lennie. 
+ Kapcsolatok aciklikus; kell lennie. más szóval nem lehetnek a kezdeti forráshelyen csomópontnak vezető kapcsolatok láncolata.
+ A kimeneti réteget nem lehet egy kapcsolat a csomag forrás réteget.  

## <a name="structure-specifications"></a>Struktúra specifikációk

Neurális hálózat struktúra specifikáció három szakaszból áll: a **állandó deklarace**, a **deklarace. réteg**, a **kapcsolat deklarace**. Emellett van egy nem kötelező **deklarace megosztása** szakaszban. A szakaszok bármilyen sorrendben adható meg.

## <a name="constant-declaration"></a>Állandó nyilatkozat

Egy állandó deklaráció nem kötelező. Biztosít egy azt jelenti, hogy a Neurális hálózat definíciójában máshol használt értékek meghatározásához. A nyilatkozat utasítás egyenlőségjelet és a egy értéke kifejezést azonosítót tartalmaz.

Például a következő utasítás határozza meg egy állandó `x`:  

`Const X = 28;`

Két vagy több állandók egyidejűleg definiálásához azonosító neveket és értékeket tegye zárójelek között, és pontosvesszővel válassza el egymástól őket. Példa:

`Const { X = 28; Y = 4; }`

Minden egyes hozzárendelés kifejezés jobb oldalán lehet egy egész számot, egy valós szám, logikai érték (IGAZ vagy hamis) vagy olyan kifejezésnek. Példa:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Réteg nyilatkozat

A réteg nyilatkozat megadása kötelező. Azt határozza meg, a méretét és a réteg, beleértve a kapcsolat kötegek és attribútumok forrását. A nyilatkozat utasítás kezdődik a neve, a réteg (bemeneti, rejtett vagy kimeneti), a dimenziók a réteg (pozitív egész számot tartalmazó rekordot) követ. Példa:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;  
```

+ A termék, a dimenziók használata a rétegben található csomópontok számát. Ebben a példában a rendszer két dimenzió [5,20], ami azt jelenti, hogy 100 csomópont a rétegben.
+ A rétegek je nutné deklarovat bármilyen sorrendben, egy kivétellel: Ha egynél több bemeneti réteg van definiálva, a sorrendet, amelyben deklarált meg kell egyeznie a szolgáltatások a bemeneti adatok sorrendjét.

Adja meg, hogy egy rétegben lévő csomópontok automatikusan meghatározni a `auto` kulcsszót. A `auto` kulcsszó különböző hatásokkal, attól függően, a réteg van:

+ Egy bemeneti réteg deklaraci a csomópontok számát a szolgáltatások a bemeneti adatok száma.
+ A rejtett rétegben határozza meg, a csomópontok számát a számát, amelyet a paraméter értéke által meghatározott **rejtett csomópontok**.
+ A csomópontok számát a kimenete rétegen nyilatkozat, 2 a két osztályú osztályozás, a regressziós és többosztályos osztályozási kimeneti csomópontok száma egyenlő 1.

Például a következő hálózati definíció lehetővé teszi, hogy automatikusan meghatározott összes réteg mérete:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;  
```

Egy trainable réteg (a rejtett vagy kimeneti rétegek) réteg nyilatkozatot szükség esetén belefoglalhatja a kimeneti függvény (más néven az aktiválási függvény), amely alapértelmezés szerint a **sigmoid** osztályozási modell, és  **lineáris** regressziós modellek esetén. Akkor is, ha az alapértelmezett használatához, is explicit módon állapot az aktiválási függvény, az átláthatóság érdekében szükség esetén.

A következő kimeneti függvények támogatottak:

+ sigmoid
+ Lineáris
+ softmax
+ rlinear
+ Négyszög
+ sqrt
+ srlinear
+ ABS
+ TANH
+ brlinear

Például az alábbi nyilatkozatot használja a **softmax** függvény:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Kapcsolat nyilatkozat

Után azonnal meghatározása a trainable réteget, be kell állítania a definiált rétegek közötti kapcsolatok. A kapcsolat csomag nyilatkozat kulcsszóval kezdődik `from`, utána pedig nevét a csomag forrás-réteg és milyen típusú kapcsolat csomagot hozhat létre.

Jelenleg kapcsolat csomagok öt típusú támogatottak:

+ **Teljes** csomagok, a kulcsszó jelzi `all`
+ **A szűrt** csomagok, a kulcsszó által jelzett `where`, utána pedig egy predikátum kifejezés
+ **Konvolúciós** csomagok, a kulcsszó által jelzett `convolve`, utána pedig a konvolúció attribútumok
+ **Készletezését** csomagok, a kulcsszavakat által jelzett **készlet max.** vagy **jelenti azt, hogy a készlet**
+ **Válasz normalizálási** csomagok, a kulcsszó által jelzett **válasz normát**

## <a name="full-bundles"></a>Teljes kötegek

Egy teljes kapcsolati csomag ki minden csomópontból kapcsolatot tartalmaz a forrás-rétegben a cél-réteg minden egyes csomópontjára. Ez az az alapértelmezett hálózati kapcsolat típusa.

## <a name="filtered-bundles"></a>Szűrt kötegek

Egy szűrt kapcsolaton keresztül csomag specifikációt tartalmaz egy predikátum, szintaktikailag, kifejezett olyannyira, például egy C# lambda kifejezésnek. Az alábbi példa két szűrt csomagjaiból határozza meg:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  
```

+ A predikátum a `ByRow`, `s` jelölő index a bemeneti réteg csomópontok a téglalap alakú tömbbe paraméter `Pixels`, és `d` indexet a csomópontok a Rejtett réteg tömbbe jelölő paraméter `ByRow`. Mindkét típusú `s` és `d` hosszúságú két egész számot tartalmazó rekordot van. Elméleti szinten `s` keresztül az egész számok minden pár címtartományok `0 <= s[0] < 10` és `0 <= s[1] < 20`, és `d` az egész számok, minden pár keresztül címtartományok `0 <= d[0] < 10` és `0 <= d[1] < 12`. 

+ A predikátum kifejezés jobb oldalán van egy feltételt. Ebben a példában minden egyes értékéhez `s` és `d` úgy, hogy a feltétel igaz, nincs-e a célcsomópontra réteg az adatforrás réteg csomópontból versenyképességét. Ebből kifolyólag a szűrőkifejezés azt jelzi, hogy, hogy a csomag tartalmaz-e a kapcsolat a csomópont által meghatározott `s` a csomópont által meghatározott `d` minden olyan esetben, ha [0] s d [0] nem található.

Szükség esetén a szűrt köteg súlyok készletét is megadhat. Az érték a **súlyok** attribútumnak kell lennie, amely megfelel a kapcsolatok a csomagazonosító alapján meghatározott számát hosszúságú értékei lebegőpontos rekordot. Alapértelmezés szerint a súlyok véletlenszerűen generált.

Súlyértékeket cél csomópont index szerint vannak csoportosítva. Azt jelenti, ha az első célcsomópont K forrás csomópontok, csatlakozik az első `K` elemei a **súlyok** rekord index-sorrendben az első célcsomópont súlyok. Ugyanez vonatkozik, a fennmaradó cél csomópontok.

Adja meg a súlyok közvetlenül állandó értékek, lehetőség. Például ha korábban már megismerte a súlyok, megadhatja azokat a szintaxis használatával állandóként:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Konvolúciós kötegek

Ha a betanítási adatok struktúrája a homogén, konvolúciós kapcsolatok gyakran használják az adatok magas szintű szolgáltatásai további. Például, kép, hang- vagy videofájl adat, térbeli vagy a historikus dimenziói lehet viszonylag egységes.  

Konvolúciós csomagjaiból szoftverbiztonsági téglalap alakú **kernelekkel** , amely a dimenziók keresztül vannak ütközik. Lényegében minden kernel súlyok helyi környékeken a alkalmazni néven készletét **kernel-alkalmazásokra**. Minden egyes kernel alkalmazás felel meg a forrás rétegben, amelynek a neve a csomópont a **központi csomóponton**. A kernel súlyozását sok kapcsolat között vannak megosztva. Konvolúciós a köteg minden egyes kernel téglalap alakú, amely minden kernel-alkalmazásokra csak azonos méretű.  

Konvolúciós csomagok támogatják a következő attribútumokat:

**InputShape** határozza meg a forrás réteg dimenzióinak a konvolúciós csomag alkalmazásában. Az értéknek pozitív egész számot tartalmazó rekordot kell lennie. Az egész számok szorzatát egyenlőnek kell lennie a forrás-rétegben lévő csomópontok számát, de egyébként nem kell egyeznie a forrás réteg deklarált dimenzióinak. Ez a rekord hossza válik a **arita** értékét a konvolúciós csomagot. Általában arita minimális számára utal argumentumok vagy operandusok, amely egy függvényt is igénybe vehet.

Az alakzat és helyét. a kernelekkel meghatározásához használja az attribútumok **KernelShape**, **Stride**, **Padding**, **LowerPad**, és  **UpperPad**:   

+ **KernelShape**: (kötelező) Defines a konvolúciós csomagot az egyes kernel dimenzióinak. Az értéknek pozitív egész számok használata, amely megegyezik a arita Pro instanci a csomagot a rekordot kell lennie. Ez a rekord minden összetevője nem lehet nagyobb, mint a megfelelő összetevője **InputShape**. 

+ **STRIDE**: (nem kötelező) határozza meg, amely a központi csomópontok közötti távolság konvolúció (minden dimenzió egy lépéssel mérete), a mozgó lépés méretét. Az értéknek pozitív egész számok használata, amely a arita Pro instanci a csomagot a rekordot kell lennie. Ez a rekord minden összetevője nem lehet nagyobb, mint a megfelelő összetevője **KernelShape**. Az alapértelmezett érték: az összes összetevő egyenlő egy rekordot. 

+ **Megosztás**: (nem kötelező) határozza meg a súly minden dimenzió, a konvolúció megosztása. Az érték lehet egy logikai érték, vagy egy rekord, logikai értékek használata, amely a arita Pro instanci a csomagot. Egyetlen logikai érték terjeszteni egy rekord megadott értéke megegyezik az összes összetevővel megfelelő hosszúságú lehet. Az alapértelmezett értéke egy rekord, amely az összes tényleges értékek áll. 

+ **MapCount**: (nem kötelező) Defines a konvolúciós csomagot a maps szolgáltatás száma. Az érték lehet egy pozitív egész számnak vagy pozitív egész számok használata, amely a arita Pro instanci a csomagot a rekordot. Egyetlen egész értéket terjeszteni kell a megfelelő hosszúságú egyenlő a megadott értéket az első összetevőkkel rekordot, és a fennmaradó összetevőket egy egyenlő. Az alapértelmezett érték egyike. A maps szolgáltatás teljes száma, amely az a rekord összetevőit. A teljes szám faktoring az összetevőben határozza meg, hogyan funkció térkép értékek szerint vannak csoportosítva a cél csomópontok. 

+ **Súlyok**: (nem kötelező) határozza meg a köteg kezdeti súlyok. Az értéknek kell lennie a lebegőpontos használata, amely kernelekkel hányszor értékei súlyok száma / kernel, az a cikk későbbi részében megadott rekordot. Az alapértelmezett súlyok véletlenszerűen jönnek létre.  

Nincsenek két készletnyi kitöltési, a folyamatban, egymást kölcsönösen kizáró tulajdonságok vezérlő tulajdonságok:

+ **Padding**: (nem kötelező) Meghatározza hogy kell-e a bemeneti hosszúságra használatával egy **alapértelmezett kitöltés séma**. Az érték lehet egy logikai érték, vagy egy rekord, logikai értékek, amely a csomagot a arity hosszúságú lehet. 

    Egyetlen logikai érték terjeszteni egy rekord megadott értéke megegyezik az összes összetevővel megfelelő hosszúságú lehet. 
    
    Egy dimenzió értéke igaz, ha a forrás logikailag számokhoz az, hogy a dimenzió nulla értékű cellákat támogatásához további kernel-alkalmazásokra, úgy, hogy a első és utolsó kernelekkel, hogy a dimenzió központi csomópontján, amely első és utolsó csomópontja a forrás réteg dimenzió. Ebből kifolyólag egyes dimenzióban lévő "üres" csomópontok automatikusan határozza meg, hogy pontosan illeszkedjen `(InputShape[d] - 1) / Stride[d] + 1` kernelekkel a tömöttnek forrás rétegbe. 
    
    Egy dimenzió értéke HAMIS, ha a kernelekkel határozzák meg, hogy mindkét oldalon, amely ki van hátra a csomópontok száma (legfeljebb 1 számít) megegyezik. Ez az attribútum alapértelmezett értéke egy rekord összes összetevőkkel egyenlő a False (hamis).

+ **UpperPad** és **LowerPad**: (nem kötelező) adjon meg nagyobb mértékben vezérelheti a használandó található Kitöltés mennyisége. **Fontos:** lehet, hogy ezek az attribútumok meghatározott if, és csak akkor, ha a **Padding** fenti tulajdonság ***nem*** definiálva. Az értékek egész értékű rekordokat, amelyek a csomagot a arity hosszúságú lehet. Ha ezek az attribútumok meg van adva, a bemeneti réteg minden dimenzió az alsó és felső végén "üres" csomópontokat ad hozzá. Minden dimenzió az alsó és felső célból hozzáadott csomópontok száma határozza meg **LowerPad**[i] és **UpperPad**[i] jelölik. 

    Győződjön meg arról, hogy a kernelt csak "valódi" csomópontok és a "üres" csomópontok megfelelnek-e, hogy a következő feltételeknek kell teljesülniük:
      - Minden egyes összetevője **LowerPad** kell feltétlenül kevesebb mint `KernelShape[d]/2`. 
      - Minden egyes összetevője **UpperPad** nem lehet nagyobb, mint `KernelShape[d]/2`. 
      - Ezek az attribútumok alapértelmezett értéke egy rekord összes összetevőkkel 0 egyenlő. 

    A beállítás **Padding** = true lehetővé teszi, hogy a lehető kitöltési, hogy a "center" kerneljének belül a "valódi" bemeneti van szükség szerint. Ez módosítja egy kicsit a számítástechnika a kimeneti mérete helyett a számításokat. A kimeneti mérete általában *D* számítja ki, hogy `D = (I - K) / S + 1`, ahol `I` bemeneti mérete, `K` a kernel mérete `S` van a stride és `/` szám (Forduló nullához van ). Ha UpperPad = [1, 1], a bemeneti méret `I` lényegében 29, és így `D = (29 - 5) / 2 + 1 = 13`. Azonban, hogy amikor **Padding** = true, lényegében `I` lekérdezi bumped által `K - 1`; ezért `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Az értékek megadásával **UpperPad** és **LowerPad** kap, ha csak állít be, mint a kitöltési több ellenőrzést **Padding** = true.

Konvolúciós hálózatokat és azok alkalmazásokkal kapcsolatos további információkért tanulmányozza a következő cikkeket: 

+ [http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html)
+ [http://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf) 
+ [http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Csomagok készletezési

A **csomag készletezési** konvolúciós kapcsolat hasonló geometriai vonatkozik, de a forrás-csomópont értékei előre definiált függvények használatával származtatni a céloldali csomópont értéke. Ezért a készletezésével csomagjaiból nincs trainable állapot (súlyok vagy esetleges előítéleteire nézve) rendelkezik. Készletezésével csomagjaiból támogatja a konvolúciós attribútumok kivételével **megosztási**, **MapCount**, és **súlyok**.

Általában a szomszédos készletezésével egységek összesítése kernelekkel ne legyenek átfedésben. Ha Stride [d] [d] KernelShape minden dimenzió, a réteg kapott, amely a a hagyományos helyi készletezésével, ami gyakran alkalmazzák a konvolúciós Neurális hálózatokkal. Minden egyes célcsomópont kiszámítja a maximális vagy az átlag, a tevékenységek a kernel a forrás-rétegben.  

Az alábbi példában látható készletezésével csomagot: 

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }  
```

+ A csomagot a arity: 3: azt jelenti, a rekordok felsorolásának hossza `InputShape`, `KernelShape`, és `Stride`. 
+ A szám a csomópontok a forrás-rétegben `5 * 24 * 24 = 2880`. 
+ Ennek oka az olyan hagyományos helyi készletezésével réteg **KernelShape** és **Stride** egyenlő. 
+ A cél rétegben csomópontok száma `5 * 12 * 12 = 1440`.

Készletezésével rétegek kapcsolatos további információkért tanulmányozza a következő cikkeket:  

+ [http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (A szakaszban 3.4-es)
+ [http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
+ [http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Válasz normalizálási kötegek

**Válasz normalizálási** egy helyi normalizálási séma Geoffrey Hinton, először bevezetett és mások a tanulmány [épít Classiﬁcation a Konvolúciós Neurális hálózatokat](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). 

Válasz normalizálási, ezzel elősegítve a Neurális hálózatokkal általánossá szolgál. Egy idegsejt aktiválási nagyon magas szintű ez, ha egy helyi válasz normalizálási réteg elrejti a környező idegsejtek csoportjának viselkedését aktiválási szintjét. Ez a három paraméter használatával történik (`α`, `β`, és `k`) és a egy konvolúciós struktúra (vagy hálózatok alakzat). A cél-réteg minden idegsejt **y** idegsejt megfelel **x** a forrás-rétegben. Aktiválási szintjét **y** adja meg a következő képletet, ahol `f` idegsejt, az aktiválási szint és `Nx` a kernel (vagy a készlet, amely tartalmazza a idegsejtek csoportjának viselkedését a helyek, a **x**), a következő konvolúciós struktúra által meghatározott módon:  

![a képlet konvolúciós struktúra](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Válasz normalizálási csomagjaiból támogatja a konvolúciós attribútumok kivételével **megosztási**, **MapCount**, és **súlyok**.  

+ Tartalmazza-e a kernel idegsejtek csoportjának viselkedését, azonos térképen ***x***, a normalizálási séma nevezzük **azonos leképezése normalizálási**. Azonos térkép normalizálási, az első koordináta az meghatározásához **InputShape** 1 értékkel kell rendelkeznie.

+ A kernel tartalmazza-e a idegsejtek csoportjának viselkedését a térbeli helyén ***x***, azonban a idegsejtek csoportjának viselkedését más Maps Közösséghez, a normalizálási séma neve **keresztben leképezi a normalizálási**. Válasz normalizálási az ilyen típusú oldalirányú gátló ihletet létrehozása verseny big Data típusú aktiválási szintek különböző térképeken számított idegsejt kimenetek között található a valódi idegsejtek csoportjának viselkedését, típus szerint egy formája implementál. Annak meghatározásához, a maps normalizálási között, az első koordináta a nagyobb, mint egy és a maps száma nem lehet nagyobb egész számnak kell lennie, és a koordináták a többi 1 értékkel kell rendelkeznie.

Válasz normalizálási csomagok forrás csomópont értékek megadásával határozza meg a cél csomópont értéke egy előre meghatározott függvény alkalmazni, mert nincs trainable állapot (súlyok vagy esetleges előítéleteire nézve) rendelkeznek.

> [!NOTE]
> A csomópontok a cél rétegben idegsejtek csoportjának viselkedését, amelyek a kernelekkel központi csomópontjai felelnek meg. Például ha `KernelShape[d]` páratlan, majd `KernelShape[d]/2` felel meg a központi kernel csomópontra. Ha `KernelShape[d]` páros, a központi csomóponton jelenleg `KernelShape[d]/2 - 1`. Ezért ha `Padding[d]` hamis, az első és utolsó `KernelShape[d]/2` csomópontok nem rendelkeznek megfelelő csomópontok a cél rétegben. Ezen helyzet elkerülése érdekében adjon meg **Padding** , [true, true,..., igaz].

A korábban ismertetett négy attribútumok mellett válasz normalizálási csomagok is támogatja a következő attribútumokkal:

+ **Alpha**: (kötelező) adja meg egy lebegőpontos érték, amely megfelel `α` a képlet. 
+ **Bétaverzió**: (kötelező) adja meg egy lebegőpontos érték, amely megfelel `β` a képlet. 
+ **Eltolás**: (nem kötelező) adja meg egy lebegőpontos érték, amely megfelel `k` a képlet. Alapértelmezés szerint az 1.

Az alábbi példa meghatározza egy válasz normalizálási csomag használatával ezek az attribútumok:  

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }  
```

+ A forrás réteg öt aof dimenziójában 12 x 12, 1440 csomópontokon összesítés a maps tartalmazza. 
+ Az érték **KernelShape** azt jelzi, hogy ez egy azonos normalizálási térképréteg, 3 x 3 téglalap a hálózatok esetén. 
+ Az alapértelmezett érték **Padding** értéke HAMIS, így a cél réteg rendelkezik minden dimenzió csak 10 csomópont. Egy csomópont a cél-rétegben, amely megfelel a forrás-réteg minden csomópontján történő felvételéhez adja hozzá kitöltés = [true, true, true]; és RN1 méretének módosítása [5, 12, 12].  

## <a name="share-declaration"></a>Deklarace megosztása

NET # igény szerint is támogatja a megosztott súlyok több kötegbe. Bármely két csomagjaiból súlyozását megoszthatók, ha azok struktúrákat megegyezik. A következő szintaxist a megosztott súlyok csomagjaiból határozza meg:  

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

Például a következő fájlmegosztás-nyilatkozat megadja a réteg nevét, amely azt jelzi, hogy a súlyok és a feldolgozáshoz megosztott:  

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

+ A bemeneti funkciói két egyenlő méretű bemeneti rétegek vannak particionálva. 
+ A rejtett réteget majd a magasabb szintű szolgáltatásai a két bemeneti rétegek számítási. 
+ A megosztás deklarace azt jelenti, hogy *H1* és *H2* , a megfelelő bemenetek ugyanúgy kell számolni.  

Azt is megteheti ez sikerült adható meg két különálló megosztás-deklarációkat a következőképpen:  

```Net#
share { Data1 => H1, Data2 => H2 } // share weights  
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases  
```

A rövid űrlapot is használhatja, csak ha a rétegek szerepel a csomagban. Megosztás általában lehetőség csak akkor, ha a megfelelő szerkezete azonos, ami azt jelenti, hogy rendelkezik az azonos méretű, azonos konvolúciós geometry és így tovább.  

## <a name="examples-of-net-usage"></a>Net # használati példák

Ez a témakör néhány példát, hogyan használhatja a Net # rejtett réteget hozzáadásához adja meg a módon, hogy rejtett réteget egyéb biztonsági interaktívan, konvolúciós hálózatokat hozhat létre.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Egy egyszerű egyéni Neurális hálózat meghatározása: "Hello World" példa

Ebben az egyszerű példában bemutatja, hogyan hozhat létre, amely rendelkezik egy rejtett rétegben Neurális hálózat modell.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;  
```

A példa szemlélteti a következő néhány alapvető parancsot:  

+ Az első sora határozza meg a bemeneti réteg (nevű `Data`). Ha a `auto` kulcsszó, a Neurális hálózat automatikusan tartalmazza az összes funkció oszlop a bemeneti példák. 
+ A második sor hoz létre a rejtett rétegben. A név `H` a rejtett rétegben, amelynek 200 csomópontok hozzá van rendelve. Ez a réteg a bemeneti réteg teljes csatlakozik.
+ A harmadik sora határozza meg, a kimenete rétegen (nevű `O`), amely 10 kimeneti csomópont tartalmazza. A Neurális hálózat besorolást használják, van-e egy kimeneti csomópontonkénti osztály. A kulcsszó **sigmoid** azt jelzi, hogy a kimeneti függvény a kimenete rétegen van alkalmazva.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Több rejtett réteg meghatározása: számítógép vision példa

A következő példa bemutatja, hogyan definiálhat egy némileg összetettebb Neurális hálózat, a több egyéni rejtett réteget.  

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

Ebben a példában a Neurális hálózati specifikációs nyelv számos funkcióit mutatja be:

+ A struktúrának van két bemeneti réteget, `Pixels` és `MetaData`.
+ A `Pixels` réteg, amely egy forrás két kapcsolat csomagok, a cél rétegeket, `ByRow` és `ByCol`.
+ A rétegek `Gather` és `Result` cél rétegeket, a kapcsolat több kötegbe vannak.
+ A kimenete rétegen `Result`, egy cél, két kapcsolat csomagjaiból: egy, a második szintű rejtett réteg `Gather` cél réteg, míg a másik a bemeneti réteggel `MetaData` cél összetevővel.
+ A rejtett réteget `ByRow` és `ByCol`, adja meg a szűrt kapcsolat predikátum kifejezések alapján. Pontosabban, a csomópont a `ByRow` , [x, y] csomópontjainak csatlakozik `Pixels` , hogy rendelkezik az első index koordinálja a csomópont megegyezik a első koordináta, x. Hasonlóképpen, a csomópont `ByCol` , [x, y] csatlakozik-e a csomópontok `Pixels` , hogy rendelkezik a második index koordinálja a csomópont egyikében a második koordináta, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Többosztályos osztályozási konvolúciós hálózat meghatározása: számjegy felismerés példa

A következő hálózati definícióját úgy tervezték, hogy ismeri fel a számokat, és azt mutatja be néhány speciális módszerekig Neurális hálózat testreszabásához.  

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
+ A kulcsszó `convolve` azt jelzi, hogy a rétegek nevű `Conv1` és `Conv2` konvolúciós rétegek vannak. A réteg nyilatkozatok mindegyike a konvolúció attribútumok listája követi.
+ A háló harmadik rejtett réteget, `Hid3`, amely teljes mértékben csatlakozik a második rejtett réteg `Conv2`.
+ A kimenete rétegen `Digit`, csak a harmadik rejtett réteg csatlakozik `Hid3`. A kulcsszó `all` azt jelzi, hogy a kimenete rétegen teljes csatlakozik `Hid3`.
+ A arita Pro instanci a konvolúció az harmadik: a rekordok felsorolásának hossza `InputShape`, `KernelShape`, `Stride, and `megosztás ". 
+ A kernel kiszolgálónként súlyok száma `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. Vagy `26 * 50 = 1300`.
+ A következő kiszámíthatja a minden egyes rejtett rétegben található csomópontok:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ A csomópontok száma összesen számítható ki a réteg a deklarált dimenziói használatával [50, 5, 5], a következő: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Mivel `Sharing[d]` hamis csak `d == 0`, kernelekkel száma `MapCount * NodeCount\[0] = 10 * 5 = 50`. 

## <a name="acknowledgements"></a>Nyugták

A Net # nyelv Neurális hálózati architektúrájának testreszabásához fejlesztette Microsoft Shon Katzenberger (mérnök, Machine Learning), és ALEKSZEJ Kamenev (Szoftvermérnök, a Microsoft Research). Machine learning-projektek és az alkalmazások és a lemezkép észlelési text analytics belsőleg használatos. További információkért lásd: [Azure ml - Bevezetés a Net # Neurális hálózatokkal](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
