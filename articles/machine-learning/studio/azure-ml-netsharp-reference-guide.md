---
title: Net# egyéni neurális hálózatok
titleSuffix: ML Studio (classic) - Azure
description: Szintaktikai útmutató a Net# neurális hálózatok specifikációnyelvéhez. Ismerje meg, hogyan hozhat létre egyéni neurális hálózati modelleket az Azure Machine Learning Studio (klasszikus) alkalmazásban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: c1912e670a9cf1c178b58cefbd33171f15be2483
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218250"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio-classic"></a>Útmutató az Azure Machine Learning Studio net# neurális hálózati specifikációs nyelvéhez (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A Net# egy olyan nyelv, amelyet a Microsoft fejlesztett ki, és amely összetett neurális hálózati architektúrák, például mély neurális hálózatok vagy tetszőleges méretű konvolúciók meghatározására szolgál. Az összetett struktúrák segítségével javíthatja az adatok, például a kép, a videó vagy a hang tanulását.

A net# architektúra specifikációja a következő környezetekben használható:

+ A Microsoft Azure Machine Learning Studio összes neurális hálózati modulja (klasszikus): [Többosztályos neurális hálózat](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [kétosztályos neurális hálózat](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network)és [neurális hálózati regresszió](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neurális hálózati függvények a Microsoft ML Serverben: [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) és [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet)az R nyelvhez, és [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network) pythonhoz.


Ez a cikk ismerteti az alapvető fogalmak és szintaxis fejlesztéséhez szükséges egyéni neurális hálózat segítségével Net#:

+ Neurális hálózati követelmények és az elsődleges összetevők meghatározása
+ A Net# specifikáció nyelvének szintaxisa és kulcsszavai
+ Példák a Net használatával létrehozott egyéni neurális hálózatokra #



## <a name="neural-network-basics"></a>A neurális hálózat alapjai

A neurális hálózati struktúra rétegekbe rendezett csomópontokból és a csomópontok közötti súlyozott kapcsolatokból (vagy élekből) áll. A kapcsolatok irányítottak, és minden kapcsolatnak van egy forráscsomópontja és egy célcsomópontja.

Minden betanítható réteg (rejtett vagy kimeneti réteg) egy vagy több **kapcsolatköteggel rendelkezik.** A kapcsolatköteg egy forrásrétegből és az adott forrásrétegből származó kapcsolatok specifikációjából áll. Egy adott köteg összes kapcsolata megosztja a forrás- és célrétegeket. A Net#-ban a kapcsolatköteg a köteg célrétegéhez tartozónak minősül.

A Net# különböző típusú kapcsolatkötegeket támogat, amelyek lehetővé teszik a bemenetek rejtett rétegekhez való leképezésének és a kimenethez való leképezésének testreszabását.

Az alapértelmezett vagy szabványos csomag egy **teljes köteg,** amelyben a forrásréteg minden csomópontja a célréteg minden csomópontjához csatlakozik.

Emellett a Net# a következő négy féle speciális kapcsolatcsomagot támogatja:

+ **Szűrt kötegek**. Predikátumot a forrásréteg-csomópont és a célréteg-csomópont helyével határozhat meg. A csomópontok akkor vannak csatlakoztatva, amikor az állítmány igaz.

+ **Konvolúciós kötegek**. A forrásrétegben definiálhatja a csomópontok kis környékét. A célréteg minden csomópontja a forrásréteg egyik csomópontjához csatlakozik.

+ **Kötegek** és **válasznormalizálási kötegek összevonása.** Ezek hasonlóak a konvolúciós kötegekhez, mivel a felhasználó a forrásrétegben lévő csomópontok kis környékeit határozza meg. A különbség az, hogy a súlyok a szélek ezekben a kötegek nem trainable. Ehelyett egy előre definiált függvény tanuskodik a forráscsomópont értékeire a célcsomópont értékének meghatározásához.


## <a name="supported-customizations"></a>Támogatott testreszabások

Az Azure Machine Learning Studio (klasszikus) neurális hálózati modellek architektúrája széles körben testreszabható a Net#használatával. A következőket teheti:

+ Hozzon létre rejtett rétegeket, és szabályozza a csomópontok számát az egyes rétegekben.
+ Adja meg, hogy a rétegek hogyan kapcsolódjanak egymáshoz.
+ Speciális kapcsolódási struktúrák, például konvolúciók és súlymegosztási csomagok definiálása.
+ Adjon meg különböző aktiválási funkciókat.

A specifikáció nyelvének szintaxisáról a [Struktúramező című](#structure-specifications)témakörben talál részleteket.

Példák a neurális hálózatok definiálására néhány gyakori gépi tanulási feladatokhoz, az egyszerűtől az összetettig, [lásd: Példák.](#examples-of-net-usage)

## <a name="general-requirements"></a>Általános követelmények

+ Pontosan egy kimeneti rétegnek kell lennie, legalább egy bemeneti rétegnek és nulla vagy több rejtett rétegnek.
+ Minden réteg nek van egy rögzített számú csomópontja, amelyek fogalmilag tetszőleges méretű téglalap alakú tömbbe vannak rendezve.
+ A bemeneti rétegek nem rendelkeznek betanított paramétereket, és azt a pontot jelölik, ahol a példányadatok belépnek a hálózatba.
+ A betanítható rétegek (a rejtett és a kimeneti rétegek) betanított paramétereket társítottak, úgynevezett súlyokat és torzításokat.
+ A forrás- és célcsomópontoknak külön rétegekben kell lenniük.
+ A csatlakozásnak aciklikusnak kell lennie; más szóval nem létezhet kapcsolatlánc, amely a kezdeti forráscsomóponthoz vezetvissza.
+ A kimeneti réteg nem lehet a kapcsolatköteg forrásrétege.

## <a name="structure-specifications"></a>Szerkezeti előírások

A neurális hálózati szerkezet specifikációja három részből áll: az **állandó deklarációból**, a **rétegdeklarációból**, a **kapcsolati deklarációból**. Van egy opcionális **megosztási deklarációs** szakasz is. A szakaszok tetszőleges sorrendben megadhatók.

## <a name="constant-declaration"></a>Állandó nyilatkozat

Az állandó deklaráció megadása nem kötelező. Lehetővé teszi a neurális hálózat definíciójában máshol használt értékek meghatározásához. A deklarációs nyilatkozat egy azonosítóból, majd egy egyenlőségjelből és egy értékkifejezésből áll.

A következő utasítás például egy `x`állandót határoz meg:

`Const X = 28;`

Két vagy több állandó egyidejű definiálásához csatolja az azonosítóneveket és értékeket kapcsos zárójelek be, és pontosvesszővel válassza el őket. Példa:

`Const { X = 28; Y = 4; }`

Az egyes hozzárendelési kifejezések jobb oldala lehet egész szám, valós szám, logikai érték (Igaz vagy Hamis) vagy matematikai kifejezés. Példa:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Rétegdeklaráció

A rétegdeklaráció szükséges. Meghatározza a réteg méretét és forrását, beleértve a kapcsolatkötegeket és attribútumokat. A deklarációs utasítás a réteg nevével kezdődik (bemenet, rejtett vagy kimenet), amelyet a réteg méretei követnek (pozitív egész számok törzse). Példa:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ A méretek szorzata a rétegben lévő csomópontok száma. Ebben a példában két dimenzió van [5,20], ami azt jelenti, hogy 100 csomópont van a rétegben.
+ A rétegek bármilyen sorrendben deklarálhatók, egy kivétellel: Ha egynél több bemeneti réteg van definiálva, a deklarált sorrendnek meg kell egyeznie a bemeneti adatokban lévő szolgáltatások sorrendjével.

Ha meg szeretné adni, hogy egy rétegcsomópontjainak `auto` számát automatikusan meg lehessen határozni, használja a kulcsszót. A `auto` kulcsszó különböző hatások, attól függően, hogy a réteg:

+ A bemeneti réteg deklarációjában a csomópontok száma a bemeneti adatokban lévő szolgáltatások száma.
+ Rejtett rétegdeklarációesetén a csomópontok száma a **rejtett csomópontok száma**paraméterértékében megadott szám.
+ A kimeneti rétegdeklarációban a csomópontok száma 2 a kétosztályos besorolás, 1 a regresszió, és egyenlő a kimeneti csomópontok száma a többosztályos besoroláshoz.

A következő hálózati definíció például lehetővé teszi az összes réteg méretének automatikus meghatározását:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

A betanítható réteg (a rejtett vagy kimeneti rétegek) rétegdeklarációja opcionálisan tartalmazhatja a kimeneti függvényt (más néven aktiválási függvényt), amely alapértelmezés szerint a besorolási modellek **szigmoidja,** a regressziós modellek esetében **pedig lineáris.** Még akkor is, ha az alapértelmezett, akkor kifejezetten meg az aktiválási funkciót, ha szükséges az egyértelműség érdekében.

A következő kimeneti függvények támogatottak:

+ szigmaoid
+ Lineáris
+ softmax között
+ rlineáris
+ Square
+ sqrt
+ srlinear
+ Abs
+ tanh
+ brlineáris

A következő deklaráció például a **softmax** függvényt használja:

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Kapcsolatdeklaráció

Közvetlenül a betanítható réteg meghatározása után deklarálnia kell a megadott rétegek közötti kapcsolatokat. A kapcsolatköteg deklarációja a kulcsszóval `from`kezdődik, amelyet a köteg forrásrétegének neve és a létrehozandó kapcsolatköteg nevével követ.

Jelenleg ötféle kapcsolatköteg támogatott:

+ **Teljes** csomagok, a kulcsszó által jelzett`all`
+ **Szűrt** kötegek, amelyeket `where`a kulcsszó jelöl, majd egy predikátumkifejezés
+ **Konvolúciós** kötegek, amelyeket a kulcsszó `convolve`jelöl, majd a konvolúcióattribútumok
+ **Kötegek összevonása,** amelyet a **maximális készlet** vagy az átlagos készlet kulcsszavak **jeleznek**
+ **Válasznormalizálási** kötegek, amelyeket a **kulcsszóválasz normája** jelez

## <a name="full-bundles"></a>Teljes csomagok

A teljes kapcsolatköteg a forrásréteg minden csomópontjáról a célréteg egyes csomópontjaihoz való csatlakozást tartalmaz. Ez az alapértelmezett hálózati kapcsolattípus.

## <a name="filtered-bundles"></a>Szűrt kötegek

A szűrt kapcsolatköteg specifikációja tartalmaz egy predikátum, szintaktikailag kifejezett, ugyanúgy, mint egy C # lambda kifejezés. A következő példa két szűrt köteget határoz meg:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ A `ByRow`predikátumban `s` egy olyan paraméter, amely a bemeneti réteg csomópontjainak téglalap `Pixels`alakú `d` tömbjében lévő indexet jelöli, és egy paraméter, amely a rejtett réteg csomópontjainak tömbjében indexet jelöl. `ByRow` A kettő `s` típusa, és `d` a kettes hosszúságú egész számok ból áll. Fogalmilag `s` az összes egész párra terjed `0 <= s[0] < 10` `0 <= s[1] < 20`ki, `d` és a , és a tartományok `0 <= d[0] < 10` `0 <= d[1] < 12`at az összes egész párra, a és a.

+ Az állítmánykifejezés jobb oldalán van egy feltétel. Ebben a példában minden `s` `d` érték, és úgy, hogy a feltétel Igaz, van egy él a forrásréteg csomóponta a célréteg csomópont. Így ez a szűrőkifejezés azt jelzi, hogy a csomag `s` tartalmaz egy kapcsolatot a `d` csomópont által meghatározott csomópont által meghatározott minden olyan esetben, amikor s[0] egyenlő d[0].

A szűrt köteghez megadhatja a súlyok készletét. **A Súlyok** attribútum értékének lebegőpontos értékek nek kell lennie, amelynek hossza megegyezik a köteg által meghatározott kapcsolatok számával. Alapértelmezés szerint a súlyok véletlenszerűen jönnek létre.

A súlyértékek a célcsomópont indexe szerint vannak csoportosítva. Ez azt, hogy ha az első célcsomópont K forráscsomópontokhoz csatlakozik, a `K` **Súlyok** törzselső elemei az első célcsomópont súlyozásai forrásindex sorrendben. Ugyanez vonatkozik a többi célcsomópontra is.

Lehetőség van a súlyok közvetlenül állandó értékekként való megadására. Ha például korábban megtanulta a súlyokat, a következő szintaxissal adhatmeg állandóként:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Konvolúciós kötegek

Ha a betanítási adatok homogén szerkezetűek, konvolúciós kapcsolatok gyakran használják az adatok magas szintű jellemzőinek megismerésére. Kép-, hang- vagy videoadatokesetén például a térbeli vagy időbeli dimenziósság meglehetősen egységes lehet.

A konvolúciós kötegek téglalap alakú **magokat** alkalmaznak, amelyek átcsúsznak a méreteken. Lényegében minden kernel meghatározza a helyi környékeken alkalmazott súlyok egy csoportját, a **továbbiakban: kernelalkalmazások**. Minden kernelalkalmazás a forrásréteg egy csomódjának felel meg, amelyet **központi csomópontnak**nevezünk. A rendszermag súlyozása sok kapcsolat között megoszlik. Egy konvolúciós kötegben minden kernel téglalap alakú, és minden kernelalkalmazás azonos méretű.

A konvolúciós kötegek a következő attribútumokat támogatják:

**Az InputShape** a forrásréteg méretdimenzionális dimenzióját határozza meg ennek a konvolúciós kötegnek a alkalmazásában. Az értéknek pozitív egész számok ból álló tőbónak kell lennie. Az egész számok szorzatának meg kell egyeznie a forrásrétegcsomópontjainak számával, de egyébként nem kell megegyeznie a forrásrétegre deklarált dimenzióval. Ennek a nyomósnak a hossza lesz a konvolúciós köteg **aritásértéke.** Az aritás általában arra utal, hogy egy függvény hány argumentumot vagy operandust vehet fel.

A kernelek alakjának és helyének meghatározásához használja a **KernelShape**, **Stride**, **Padding**, **LowerPad**és **UpperPad**attribútumokat:

+ **KernelShape**: (kötelező) A konvolúciós köteg minden egyes kernelének méretitását határozza meg. Az értéknek pozitív egész számokból álló tőbőnek kell lennie, amelynek hossza megegyezik a köteg aritását. A tuple minden egyes összetevője nem lehet nagyobb, mint az **InputShape**megfelelő összetevője.

+ **Lépés:**(nem kötelező) A konvolúció csúszólépés-méreteit határozza meg (minden dimenzióhoz egy lépésméret), azaz a központi csomópontok közötti távolság. Az értéknek pozitív egész számokból álló tőbőnek kell lennie, amelynek hossza a köteg aritása. A hangmegta minden egyes összetevője nem lehet nagyobb, mint a **KernelShape**megfelelő összetevője. Az alapértelmezett érték egy olyan alapértelmezett érték, amelynek minden összetevője egyenlő egy.

+ **Megosztás**: (nem kötelező) Meghatározza a konvolúció egyes dimenzióinak súlymegosztását. Az érték lehet egyetlen logikai érték vagy logikai értékek egy törzse, amelynek hossza a köteg aritása. Egyetlen logikai érték a megfelelő hosszúságú, a megadott értékkel egyenlő összes összetevővel rendelkező bejegyzéssel bővül. Az alapértelmezett érték egy olyan alapértelmezett érték, amely az összes Igaz értéket alkotja.

+ **MapCount**: (nem kötelező) A konvolúciós köteg funkcióleképezéseinek számát adja meg. Az érték lehet egyetlen pozitív egész szám vagy pozitív egész számok ból álló nagy egész számok, amelyek hossza a köteg aritása. Egyetlen egész érték a megfelelő hosszúságú, a megadott értékkel egyenlő első összetevőkkel, a többi összetevő pedig egy értékkel egyenlő. Az alapértelmezett érték egy. A jellemzőtérképek teljes száma a tőrész-számláló összetevőinek szorzata. Ennek a teljes számnak a faktoringja az összetevők között határozza meg, hogy a szolgáltatásleképezési értékek hogyan vannak csoportosítva a célcsomópontokban.

+ **Súlyok:**(nem kötelező) A köteg kezdeti súlyozását határozza meg. Az értéknek lebegőpontos értékek nek kell lennie, amelynek hossza a cikk későbbi részében meghatározott kernelenkénti súlyok számának szorzata. Az alapértelmezett súlyok véletlenszerűen jönnek létre.

A kitöltést két tulajdonságcsoport szabályozza, amelyek egymást kölcsönösen kizárják egymást:

+ **Kitöltés**: (nem kötelező) Az **alapértelmezett kitöltési séma**segítségével határozza meg, hogy a bemenetet ki kell-e párnázni . Az érték lehet egyetlen logikai érték, vagy lehet logikai értékek egy tőzete, amelynek hossza a köteg aritása.

    Egyetlen logikai érték a megfelelő hosszúságú, a megadott értékkel egyenlő összes összetevővel rendelkező bejegyzéssel bővül.

    Ha egy dimenzió értéke Igaz, a forrás logikailag ki van párnázva ebben a dimenzióban nulla értékű cellákkal a további kernelalkalmazások támogatásához, így az adott dimenzió első és utolsó kernelének központi csomópontjai az első és az utolsó csomópontok az adott dimenzióban dimenziót a forrásrétegben. Így az egyes dimenziókban lévő "dummy" csomópontok számát automatikusan meghatározzák, hogy pontosan `(InputShape[d] - 1) / Stride[d] + 1` illeszkedjenek a kerneleka párnázott forrásrétegbe.

    Ha egy dimenzió értéke Hamis, a kernelek úgy vannak definiálva, hogy a kimaradt mindkét oldalon lévő csomópontok száma megegyezik (legfeljebb 1-es különbség). Ennek az attribútumnak az alapértelmezett értéke egy olyan alapértelmezett érték, amelynek összes összetevője hamis.

+ **UpperPad** és **LowerPad**: (nem kötelező) Nagyobb ellenőrzést biztosít a használandó kitöltés mennyiségének felett. **Fontos:** Ezek az attribútumok akkor és csak akkor definiálhatók, ha a fenti **Kitöltés** tulajdonság ***nincs*** definiálva. Az értékeknek egész értékű nek kell lenniük, és a köteg aritását tartalmazó hosszúságúaknak kell lenniük. Ha ezek az attribútumok meg vannak adva, a "dummy" csomópontok hozzáadódnak a bemeneti réteg egyes dimenzióinak alsó és felső végeihez. Az egyes dimenziók alsó és felső végeihez hozzáadott csomópontok számát a **LowerPad**[i] és **az UpperPad**[i] határozza meg.

    Annak érdekében, hogy a kernelek csak a "valódi" csomópontoknak feleljenek meg, a "dummy" csomópontoknak, a következő feltételeknek kell teljesülniük:
  - Minden egyes összetevője **LowerPad** kell `KernelShape[d]/2`szigorúan kevesebb, mint .
  - Az **UpperPad** minden egyes összetevője `KernelShape[d]/2`nem lehet nagyobb, mint .
  - Ezeknek az attribútumoknak az alapértelmezett értéke egy olyan alapértelmezett érték, amelynek összes összetevője 0.

    A **Padding** = true beállítás annyi kitöltést tesz lehetővé, amennyire csak szükséges ahhoz, hogy a kernel "középpontja" a "valódi" bemeneten belül maradjon. Ez megváltoztatja a matematikai egy kicsit a kimeneti méret kiszámításához. Általában a *D* kimeneti méret `D = (I - K) / S + 1`számítása: , ahol `I` a bemeneti méret, `K` a kernel mérete, `S` a lépés, és `/` egész osztás (kerek nulla felé). Ha az UpperPad = [1, 1] `I` beállítást állítja `D = (29 - 5) / 2 + 1 = 13`be, a bemeneti méret gyakorlatilag 29, és így . Azonban, ha **Padding** = `I` igaz, lényegében lesz ütközött fel `K - 1`; ezért `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`. Az **UpperPad** és **a LowerPad** értékeinek megadásával sokkal jobban szabályozhatja a kitöltést, mint ha csak a **Padding** = true-t állítaná be.

A konvolúciós hálózatokról és azok alkalmazásairól az alábbi cikkekben talál további információt:

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Kötegek összevonása

A **készletezési köteg** a konvolúciós kapcsolathoz hasonló geometriát alkalmaz, de előre definiált függvényeket használ a csomópontértékek forrásához a célcsomópont értékének leszámítolásához. Ezért a gyűjtőkötegek nem rendelkeznek vonatozható állapottal (súlyok vagy torzítások). A készletezési csomagok támogatják az összes konvolúciós attribútumot, kivéve **a Megosztást**, **a MapCount-ot**és **a Súlyokat**.

A szomszédos készletezési egységek által összegzett kernelek általában nem fedik át egymást. Ha a Stride[d] minden dimenzióban megegyezik a KernelShape[d] szinttel, akkor a kapott réteg a hagyományos helyi készletezési réteg, amelyet gyakran alkalmaznak a konvolúciós neurális hálózatokban. Minden célcsomópont kiszámítja a rendszermag tevékenységeinek maximális vagy középértéket a forrásrétegben.

A következő példa egy készletezési csomagot mutat be:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ A köteg aritása 3: azaz a tuples `InputShape` `KernelShape`hossza `Stride`és a .
+ A forrásrétegben lévő csomópontok száma `5 * 24 * 24 = 2880`.
+ Ez egy hagyományos helyi készletezési réteg, mert a **KernelShape** és **a Stride** egyenlő.
+ A célrétegben lévő csomópontok száma `5 * 12 * 12 = 1440`.

A rétegek összevonásáról az alábbi cikkekben talál további információt:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)(3.4. szakasz)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Válasznormalizálási kötegek

**Válasz normalizálás** a helyi normalizálási rendszer, amely először vezette be Geoffrey Hinton, és mtsai, a papír [ImageNet osztályozás deep convolutional neural networks](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

A válasz normalizálását a neurális hálók általánosításának elősegítésére használják. Amikor egy neuron tüzelési egy nagyon magas aktiválási szint, a helyi válasz normalizálási réteg elnyomja az aktiválási szintet a környező neuronok. Ez három paraméter ( ,`α` `β`, `k`és ) és egy konvolúciós struktúra (vagy szomszédsági alakzat) használatával történik. Minden neuron a cél réteg **y** megfelel egy neuron **x** a forrás réteg. Az **y** aktiválási szintjét a következő `f` képlet adja meg, ahol `Nx` a neuron aktiválási szintje van, és a kernel (vagy az **x**szomszédságában lévő neuronokat tartalmazó készlet), a következő konvolúciós szerkezet meghatározása szerint:

![képlet konvolúciós szerkezethez](./media/azure-ml-netsharp-reference-guide/formula_large.png)

A válasznormalizálási csomagok támogatják az összes konvolúciós attribútumot, kivéve **a Megosztást**, **a MapCount-ot**és **a Súlyokat**.

+ Ha a kernel neuronokat tartalmaz ugyanabban a térképen, mint ***x***, a normalizálási sémát ugyanaznak a **térképnormalizációnak nevezzük.** Azonos leképezésnormalizálás meghatározásához az **InputShape** első koordinátának 1-es értékkel kell rendelkeznie.

+ Ha a kernel olyan neuronokat tartalmaz, amelyek ugyanabban a térbeli pozícióban vannak, mint ***x***, de a neuronok más térképeken vannak, a normalizálási sémát a **térképek en keresztül normalizálásnak nevezik.** Ez a fajta válasz normalizálása megvalósítja egyfajta oldalsó gátlás ihlette a típus található valós neuronok, verseny létrehozása a nagy aktiválási szintek között neuron kimenetek számított különböző térképeken. A térképek normalizálásának definiálásához az első koordinátának egynél nagyobbnak, és nem nagyobbnak kell lennie, mint a térképek száma, és a többi koordinátának 1-es értékkel kell rendelkeznie.

Mivel a válasznormalizálási csomagok előre definiált függvényt alkalmaznak a forráscsomópont-értékekre a célcsomópont értékének meghatározásához, nincs betanítható állapotuk (súlyok vagy torzítások).

> [!NOTE]
> A célréteg csomópontjai olyan neuronoknak felelnek meg, amelyek a kernelek központi csomópontjai. Ha például `KernelShape[d]` páratlan, `KernelShape[d]/2` akkor megfelel a központi kernelcsomópontnak. Ha `KernelShape[d]` egyenletes, a központi csomópont `KernelShape[d]/2 - 1`a . Ezért ha `Padding[d]` hamis, az első `KernelShape[d]/2` és az utolsó csomópontok nem rendelkeznek megfelelő csomópontokkal a célrétegben. Ennek elkerülése érdekében definiálja **a paddingt** [igaz, igaz, ..., igazként].

A korábban ismertetett négy attribútumon kívül a válasznormalizálási csomagok a következő attribútumokat is támogatják:

+ **Alfa**: (kötelező) Az előző `α` képletnek megfelelő lebegőpontos értéket ad meg.
+ **Béta**: (kötelező) Az előző `β` képletnek megfelelő lebegőpontos értéket ad meg.
+ **Eltolás**: (nem kötelező) Az előző `k` képletnek megfelelő lebegőpontos értéket ad meg. Alapértelmezés szerint 1.

A következő példa egy válasznormalizálási csomagot határoz meg az alábbi attribútumok használatával:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ A forrásréteg öt térképet tartalmaz, amelyek mindegyike 12x12-es méretű, összesen 1440 csomópontban.
+ A **KernelShape** értéke azt jelzi, hogy ez egy ugyanaz a térképnormalizálási réteg, ahol a környék egy 3x3-as téglalap.
+ A **kitöltés** alapértelmezett értéke Hamis, így a célréteg dimenziókban csak 10 csomópont van. Ha egy olyan csomópontot szeretne felvenni a célrétegbe, amely megfelel a forrásréteg minden csomópontjának, adja hozzá a Padding = [igaz, igaz, igaz] ; és az RN1 méretét [5, 12, 12]-re változtatja.

## <a name="share-declaration"></a>Megosztási nyilatkozat

Net# opcionálisan támogatja a definiálása több kötegek megosztott súlyokkal. Bármely két köteg súlya megosztható, ha szerkezetük azonos. A következő szintaxis megosztott súlyokkal rendelkező kötegeket határoz meg:

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

A következő megosztási deklaráció például a rétegneveket adja meg, jelezve, hogy mind a súlyokat, mind az elfogultságokat meg kell osztani:

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

+ A bemeneti funkciók két egyenlő méretű bemeneti rétegre vannak felosztva.
+ A rejtett rétegek ezután magasabb szintű funkciókat számíthatnak ki a két bemeneti rétegen.
+ A részvénydeklaráció előírja, hogy a *H1* és *a H2* adatokból azonos módon kell kiszámítani.

Ezt két külön részvénynyilatkozattal is meg lehet határozni az alábbiak szerint:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

A rövid űrlapot csak akkor használhatja, ha a rétegek egyetlen köteget tartalmaznak. A megosztás általában csak akkor lehetséges, ha a megfelelő szerkezet azonos, ami azt jelenti, hogy azonos méretűek, azonos konvolúciós geometriával stb.

## <a name="examples-of-net-usage"></a>Példák a Net# használatára

Ez a szakasz néhány példát mutat be arra, hogyan használhatja a Net# használatával rejtett rétegek hozzáadását, a rejtett rétegek más rétegekkel való interakciójának módját, és hogyan hozhat létre konvolúciós hálózatokat.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Egyszerű egyéni neurális hálózat definiálása: "Hello World" példa

Ez az egyszerű példa bemutatja, hogyan hozhat létre egy neurális hálózati modell, amely egyetlen rejtett réteget.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

A példa néhány alapvető parancsot mutat be az alábbiak szerint:

+ Az első sor határozza meg `Data`a bemeneti réteget (named). A kulcsszó `auto` használatakor a neurális hálózat automatikusan tartalmazza a bemeneti példák összes jellemzőoszlopát.
+ A második sor létrehozza a rejtett réteget. A `H` név a rejtett réteghez van rendelve, amely 200 csomópontból áll. Ez a réteg teljes mértékben a bemeneti réteghez van csatlakoztatva.
+ A harmadik sor határozza meg `Out`a kimeneti réteget (elnevezett ), amely 10 kimeneti csomópontot tartalmaz. Ha a neurális hálózat besorolásra szolgál, osztályonként egy kimeneti csomópont van. A **sigmoid** kulcsszó azt jelzi, hogy a kimeneti függvény a kimeneti rétegre lesz alkalmazva.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Több rejtett réteg definiálása: példa a számítógépes látásra

A következő példa bemutatja, hogyan definiálhatja egy kissé összetettebb neurális hálózat, több egyéni rejtett rétegek.

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

Ez a példa a neurális hálózatok specifikációs nyelvének számos jellemzőjét mutatja be:

+ A szerkezet két bemeneti rétegből áll, `Pixels` és `MetaData`.
+ A `Pixels` réteg két kapcsolatköteg forrásrétege, `ByRow` célrétegekkel `ByCol`és .
+ A `Gather` rétegek `Result` és a célrétegek több kapcsolatkötegben.
+ A kimeneti `Result`réteg , két kapcsolatköteg célrétege; az egyik a második `Gather` szintű rejtett réteg, mint a `MetaData` cél réteg, a másik a bemeneti réteg, mint a cél réteg.
+ A rejtett `ByRow` rétegek `ByCol`és a , a szűrt kapcsolatot predikátumkifejezések használatával adja meg. Pontosabban, a csomópont `ByRow` a [x, y] csatlakozik a `Pixels` csomópontok, amelyek az első index koordináta egyenlő a csomópont első koordináta, x. Hasonlóképpen, a csomópont `ByCol` a [x, y] csatlakozik a `Pixels` csomópontok, amelyek a második index koordináta belül a csomópont második koordináta, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Konvolúciós hálózat definiálása többosztályos osztályozáshoz: számjegyfelismerési példa

A következő hálózat definíciója a számok felismerésére szolgál, és néhány speciális technikát mutat be a neurális hálózat testreszabásához.

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

+ A szerkezet egyetlen bemeneti `Image`réteggel rendelkezik, .
+ A `convolve` kulcsszó azt jelzi, `Conv1` `Conv2` hogy a megnevezett rétegek konvolúciós rétegek. A rétegdeklarációk mindegyikét a konvolúciós attribútumok listája követi.
+ A háló egy harmadik `Hid3`rejtett réteggel rendelkezik, amely `Conv2`teljes mértékben csatlakozik a második rejtett réteghez, .
+ A kimeneti `Digit`réteg , , csak a `Hid3`harmadik rejtett réteghez csatlakozik, . A `all` kulcsszó azt jelzi, hogy a `Hid3`kimeneti réteg teljes mértékben kapcsolódik ahoz.
+ A konvolúció aritása három: a `InputShape`tuples `KernelShape` `Stride`, `Sharing`, és .
+ A súlyok száma kernelenként `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`. Vagy `26 * 50 = 1300`.
+ Az egyes rejtett rétegek csomópontjait a következőképpen számíthatja ki:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ A csomópontok teljes száma a réteg deklarált méretével számítható ki ([50, 5, 5], az alábbiak szerint:`MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Mivel `Sharing[d]` a rendszer `d == 0`csak a Hamis `MapCount * NodeCount\[0] = 10 * 5 = 50`, a kernelek száma a.

## <a name="acknowledgements"></a>Nyugták

A neurális hálózatok architektúrájának testreszabására szolgáló Net# nyelvet a Microsoft fejlesztette ki Shon Katzenberger (Architect, Machine Learning) és Alexey Kamenev (szoftvermérnök, Microsoft Research). Belsőleg gépi tanulási projektekhez és alkalmazásokhoz használják, a képészleléstől a szövegelemzésig. További információ: [Neural Nets in Azure Machine Learning studio - Introduction to Net#](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)
