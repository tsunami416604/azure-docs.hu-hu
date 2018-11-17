---
title: Az ügyfél változásainak Machine Learning segítségével elemzése |} A Microsoft Docs
description: Egy integrált modellek elemzéséhez és a vásárlók lemorzsolódásának pontozási való fejlesztésének bemutató esettanulmány
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 1333ffe2-59b8-4f40-9be7-3bf1173fc38d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: 66c8fcb54ef348ca9414d14eb80d00fa75e89ad9
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823214"
---
# <a name="analyzing-customer-churn-using-azure-machine-learning"></a>Az ügyfél az Azure Machine Learning segítségével változásainak elemzése
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja egy referenciaimplementációt, egy ügyfél lemorzsolódásának elemzésére szolgáló projekt, amely az Azure Machine Learning segítségével. Ebben a cikkben bemutatjuk, azok alkalmazásfüggőségeit az iparági vásárlók lemorzsolódási problémájának megoldására szolgáló kapcsolódó általános modellekről. Azt is mérheti a Machine Learning segítségével létrehozott modellek pontossága, és felmérheti a további fejlesztésére vonatkozó utasításokat.  

### <a name="acknowledgements"></a>Nyugták
Ez a kísérlet fejlesztette ki és Roger Barga, korábbi nevén Microsoft Azure Machine Learning Termékmenedzser, Serge Berger és egyszerű Adattudós, a Microsoft által tesztelt. Az Azure dokumentációs csapata köszönetet mondani a segítségükért összpontosíthassák nyugtázza, és Köszönjük Ez a tanulmány megosztása.

> [!NOTE]
> Ehhez a kísérlethez használt adatokat nem nyilvánosan érhető. Egy példa bemutatja, hogyan hozhat létre egy gépi tanulási modellt a forgalom elemzése:: [kiskereskedelmi vásárlói lemorzsolódás folyamatmodell-sablont](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1) a [Azure AI-katalógusban](http://gallery.cortanaintelligence.com/)
> 
> 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-problem-of-customer-churn"></a>A probléma a vásárlók lemorzsolódásának
A fogyasztó piacon elérhető, és az összes vállalati ágazatban vállalkozások kell forgalom kezelésére. Néha túlzott és befolyásolja a házirendet érintő döntések. A hagyományos megoldás, ha nagy-upsell churners előre jelezni, és oldja meg saját igényeinek megfelelő marketingkampányokba, recepciószolgálata szolgáltatáson keresztül vagy a speciális felmentésekről alkalmazásával. Ezek a módszerek eltérőek lehetnek industry iparági. Még vésve egy adott fogyasztói fürtről a másikra egy iparági (például távközlési) belül.

A gyakori tényezővel az, hogy vállalkozások minimalizálása érdekében ezeket külön ügyfél felhasználómegtartása kell-e. Így természetes módszer lehet a pontszám minden ügyfél a lemorzsolódás valószínűségét, és oldja meg a felső N azokat. Előfordulhat, hogy a legjelentősebb ügyfeleket legjövedelmezőbb azokat. Például kifinomultabb forgatókönyvekben nyereség függvény alkalmazzák a speciális felmentéssel a deduplikációra kijelölt során. Ezeket a szempontokat azonban csak a teljes forgalom kapcsolatos stratégia részét képezik. Vállalkozások is figyelembe kell fiók kockázati (és a kapcsolódó kockázattűrése), a szint és a beavatkozás és egyértelmű ügyfélcsoportok költségét.  

## <a name="industry-outlook-and-approaches"></a>Iparági az outlook és módszerek
Kifinomult kezelését a lemorzsolódás érett iparág bejelentkezési. A klasszikus például, a távközlési iparágban, ahol előfizetők ismert, hogy gyakran váltson egy szolgáltatói egy másikba. Önkéntes kihagyásával az elsődleges szempont. Ezenkívül szolgáltatók rendelkezik, amikor jelentős Tudásbázis kapcsolatos *illesztőprogramok churn*, melyek a tényezőket, amelyek váltson ügyfelei meghajtó.

Például kézibeszélő vagy az eszköz el egy jól ismert illesztőprogram a mobiltelefon üzleti adatforgalom. Ennek eredményeképpen egy népszerű szabályzatot, hogy kézibeszélő árát subsidize új előfizetők számára, és a frissítéshez a meglévő ügyfelek számára a teljes díj díja szerint számítjuk fel. Ez a szabályzat hagyományosan ügyfelek egy szolgáltató a másikra segítségével tehetjük meg új kedvezményes díjat vezetett. Ennek érdekében, a rendszer felkéri szolgáltatók számára, hogy stratégiákkal pontosításához.

Magas változékonyságán kézibeszélő ajánlatok a fontos, hogy gyorsan érvényteleníti modellek adatforgalom aktuális kézibeszélő modellek alapuló tényező. Ezenkívül mobiltelefonokról nem csak a távközlési eszközöket, azok is el utasítások (vegye figyelembe az iPhone-t). A közösségi előrejelzőket rendszeres távközlési adatkészletek hatókörén kívül vannak.

Az eredmény a modellezési, hogy egy hang házirend nem lényegesen egyszerűen elkerülve a lemorzsolódás ismert okait. A folyamatos modellezési stratégiát, beleértve a klasszikus modellt számszerűsítik a kategorikus változók (például a döntési fák algoritmus), valójában **kötelező**.

Big data jellegű adatkészletek használatával az ügyfelek, a szervezetek big data-elemzés (különösen, a big data-alapú forgalom észlelési), a probléma hatékony megközelítés végzik. További információk a big Data típusú adatok megközelítés ETL szakasz ajánlások lemorzsolódási problémájának találja.  

## <a name="methodology-to-model-customer-churn"></a>A modell vásárlók lemorzsolódásának módszertan
Gyakori problémák megoldásához folyamat megoldani a vásárlók lemorzsolódásának megfelelőként jelenik a számok 1-3:  

1. Kockázati modell lehetővé teszi, hogy hogyan befolyásolja az műveletek valószínűségét, és a kockázati.
2. Egy beavatkozás modellje lehetővé teszi, fontolja meg, hogyan beavatkozás szintjét a valószínűsége annak, és a vásárlói mennyisége hatással lehetnek a csoportélettartam értékének (CLV).
3. Ez az elemzés adatmodelljeinek egy minőségi elemző, amely a célozza meg, hogy az optimális ajánlat ügyfélszegmensek proaktív marketingkampány eszkalálása.  

![][1]

A továbbítás akik megközelítés kezelni az adatforgalom a legjobb módszer, de együttműködik a összetettséget: fejlesszen többmodelles archetype és nyomkövetési függőségek modellek között kell, hogy. A modellek közötti interakció is technológiába ágyazott, az alábbi ábrán látható módon:  

![][2]

*4. ábra: Egységes többmodelles archetype*  

A modellek közötti interakció az kulcs, ha egy átfogó megközelítés továbbítására az ügyfélmegtartást. Minden modell feltétlenül replikákban idővel; az architektúra ezért implicit ciklusba (az adatok adatbányászati SZÍNELOSZLÁS-DM szabvány által beállított archetype hasonló [***3***]).  

A teljes ciklus a kockázat-döntési-marketing Szegmentálás/idősorfelbontási még egy általánosított szerkezete, és számos üzleti problémára vonatkozik. Lemorzsolódásának elemzése egyszerűen a csoport problémák erős képviselője azért azonban egy összetett üzleti probléma, amely nem engedélyezi a prediktív megoldás egyszerűsített képességekre vonatkozó. A vásárlói lemorzsolódás modern megközelítését közösségi aspektusait nem különösen kiemelt a megközelítéssel, de a közösségi szempontok a a modellezési archetype vannak ágyazva, azok bármely modellben lenne.  

Egy érdekes emellett a big data-elemzés. A mai távközlési és kereskedelmi vállalkozások az ügyfeleknek az alábbi adatok gyűjtése, és azt is könnyen bizonyulhat, hogy többmodelles kapcsolat szükség lesz egy gyakori trend, adott jelentkező trendeket, például az eszközök internetes hálózata és a széles körben használt az eszközök, amelyek lehetővé teszik az üzleti több rétegén intelligens megoldások bevezetését.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>A modellezés archetype megvalósítása a Machine Learning Studióban
Adja meg az imént ismertetett problémát, mi az a legjobb módszer egy integrált modellezési és pontozás megközelítés implementálása? Ebben a szakaszban bemutatjuk, hogyan tudjuk valósítható meg ez az Azure Machine Learning Studio használatával.  

A több modell megközelítés kell, a forgalom egy globális archetype tervezésekor. Még a relevanciaprofil (prediktív) részét megközelítés többmodelles kell lennie.  

Az alábbi ábrán látható a prototípus hoztunk létre, a Machine Learning Studióban lemorzsolódási előrejelzésére négy pontozási algoritmusok alkalmazó. Többmodelles módszerével oka nem csak egy nagyobb pontosság, de is túlzott méretezés ellen védelmet biztosító és javítása előírásoknak megfelelő szolgáltatás kiválasztása ensemble osztályozó létrehozása céljából.  

![][3]

*5. ábra: Prototípust egy megközelítést modellezési adatforgalom*  

A következő szakaszok további információt a prototípus-pontozási modelljei, hogy mi a Machine Learning Studio használatával.  

### <a name="data-selection-and-preparation"></a>Kijelölt adatok és -előkészítés
A modellek létrehozásához használt adatokat, és pontszám ügyfelek CRM-függőleges megoldásról, lett lekérve az adatokkal a rejtjelezett felhasználói adatok védelme. Az adatok az Egyesült Államokban 8000 előfizetésekkel kapcsolatos információkat tartalmaz, és amely ötvözi az három forrásának: provisioning (előfizetés metaadatai) adatok, tevékenység adatokat (használat a rendszer) és ügyfél-támogatási adatok. Az adatok nem tartalmaz minden olyan cégnek kapcsolatos adatokat az ügyfelek; Ez például nem tartalmaz hűségprogramok használatán keresztül metaadatok vagy -kreditemet pontszámokat.  

Az egyszerűség kedvéért ETL és folyamatok adattisztító hatókörén kívül mivel feltételezzük, hogy adat-előkészítési már rendelkezik-e már végzett máshol.   

Szolgáltatás kiválasztása a modellezési előzetes többszörösére pontozási előrejelzőket, szerepel a folyamat a véletlenszerű erdő modult használó készlete alapul. A megvalósítás a Machine Learning Studióban hogy kiszámítása a középérték középérték és tartományok reprezentatív funkciók. Ha például hozzáadtunk összesítések a mennyiségi adatok, például a felhasználói tevékenység minimális és maximális értékeket.    

A legutóbbi hat hónapig historikus információkat is rögzített. Adatok egy évig elemeztük, és azt létre, hogy akkor is, ha voltak statisztikailag trendeket, lemorzsolódási hatása jelentősen csökken hat hónapig érvényesek.  

A legfontosabb, hogy, hogy a teljes folyamatot, beleértve az ETL-szolgáltatás kiválasztása lapon, és modellezés a Machine Learning Studióban, a Microsoft Azure-ban adatforrások felhasználásával lett megvalósítva.   

Az alábbi ábrák bemutatják a használt adatok.  

![][4]

*6. ábra: Cikkből szerint (rejtjelezett) adatforrás*  

![][5]

*7. ábra: Funkciók adatforrásból kinyert*
 

> Vegye figyelembe, hogy ezek az adatok személyes, és ezért a modell és az adatok nem oszthatók meg.
> Azonban egy hasonló modell, nyilvánosan elérhető adatok használatával, lásd: Ez a minta Kísérletezgessen egy kicsit a [Azure AI-katalógusban](http://gallery.cortanaintelligence.com/): [telekommunikációs az ügyfél változásainak](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Többet szeretne megtudni, hogyan implementálható a Cortana Intelligence Suite segítségével lemorzsolódási elemzési modell, azt is javasoljuk [ebben a videóban](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) vezető Programmenedzsere hétköznapon Hyong Tok szerint. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>A prototípust használt algoritmusok
A következő négy gépi tanulási algoritmusok segítségével hozhat létre, a prototípus (nincs testreszabása):  

1. Logisztikai regressziós (LR)
2. Gyorsított döntési fa (BT)
3. Átlagolt perceptron (AP)
4. Támogató vektorgép (SVM)  

A következő ábra szemlélteti a kísérlet tervezőfelületére, amely azt jelzi, hogy a feladatütemezés, amelyben a modellek létrehozott egy része:  

![][6]  

*8. ábra: Modellek létrehozása a Machine Learning Studióban*  

### <a name="scoring-methods"></a>Pontozó módszerek
A négy modell címkézett betanítási adatkészletet használatával pontozását azt.  

Azt is be a pontozási adatkészletet, egy SAS vállalati Miner 12 asztali kiadásának használatával létrehozott összehasonlítható modell. A SAS-modell és az összes négy Machine Learning Studio-modell pontosságának mérni azt.  

## <a name="results"></a>Results (Eredmények)
Ebben a szakaszban azt jelenleg a kapcsolatos a pontozási adatkészleten alapuló modell pontossága megállapításokat.  

### <a name="accuracy-and-precision-of-scoring"></a>És pontozás pontossága
Az Azure Machine Learning végrehajtása általában SAS mögött van, körülbelül 10 – 15 % (terület alatt görbe vagy AUC) pontossággal.  

Azonban a legfontosabb mérőszám adatforgalom-e a téves besorolás arány:, mint az osztályozó által előrejelzett felső N churners, melyikük ténylegesen fejeződött **nem** a forgalom, és a kapott különleges kezelést? A következő diagram az összes modellt a téves besorolás aránya hasonlítja össze:  

![][7]

*9. ábra: Passau prototípus terület alatt görbévé*

### <a name="using-auc-to-compare-results"></a>Az eredmények összehasonlítása AUC használatával
Terület alatt görbe (AUC) egy globális érték, képviselő metrika *separability* a pontszámok pozitív és negatív számítógépcsoportokon a felosztások között. A hagyományos fogadó operátor jellemző (ROC) gráfhoz hasonló, de egy fontos különbség az, hogy a AUC metrika nem kell választani egy küszöbértéket. Ehelyett azt az eredményről keresztül **összes** választási lehetőségek. Ezzel szemben a hagyományos ROC-diagramon látható a riasztási aránnyal a függőleges tengely és a vízszintes tengelyről téves riasztási aránnyal, és a besorolási küszöbérték változó.   

AUC általában használják, érdemes érték, a különböző algoritmusok (vagy a különböző rendszerek) mert lehetővé teszi a modellek révén AUC értékekre kell összehasonlítani. Ez az iparágban például meteorológia és biosciences egy népszerű megközelítés. Ebből kifolyólag AUC jelöli egy népszerű eszköz osztályozó teljesítményének értékeléséhez.  

### <a name="comparing-misclassification-rates"></a>Téves besorolás díjak összehasonlítása
Körülbelül 8000 előfizetések CRM-adatok használatával az adott adatkészlet téves besorolás fel azt képest.  

* A SAS téves besorolás sebesség 10 – 15 % volt.
* A Machine Learning Studio téves besorolás az első 200 – 300 churners 15 – 20 %-os volt.  

A távközlési iparágban fontos csak a vásárlói lemorzsolódás recepciószolgálata szolgáltatásként vagy egyéb speciális kezelés felajánlásával őket a legmagasabb kockázattal rendelkező ügyfelek cím. Ebben a tekintetben a Machine Learning Studio megvalósítás hatékonysága eléri a SAS modell eredményei éri el.  

Hasonlóképpen pontossága fontosabb, mint a pontossága, mivel ez többnyire megfelelő Írisz lehetséges churners iránt.  

Az alábbi ábrán a Wikipedia a kapcsolatot a színes, könnyen érthető kép ábrázolja:  

![][8]

*10. ábra: Magával a pontosság és a pontosság*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Gyorsított döntési fa modell pontosságát és a pontosság eredményei
Az alábbi ábra a pontozás a Machine Learning prototípus használata a gyorsított döntési fa modell, amely a négy modellek között a legpontosabb történetesen nyers eredményét jeleníti meg:  

![][9]

*11. ábra: Gyorsított döntési fa modell jellemzői*

## <a name="performance-comparison"></a>Teljesítmény összehasonlítása
Hogy képest, a sebesség, amellyel adatokat pontozását volt az a Machine Learning Studio-modellek és a egy SAS vállalati Miner 12.1 asztali kiadásának használatával létrehozott összehasonlítható modell használatával.  

A következő táblázat összefoglalja az algoritmusok teljesítményének:  

*1. táblázat. Általános teljesítménye (pontosság) algoritmusok*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Átlagos modell |A legjobb modellt |Az alulteljesítő |Átlagos modell |

A modellek a végrehajtási, de a pontosság sebességének 15-25 %-kal outperformed SAS volt nagymértékben par a Machine Learning Studióban futtatott.  

## <a name="discussion-and-recommendations"></a>Hozzászólás és javaslatok
A távközlési iparágban több eljárások kiderült rendelkezik, lemorzsolódásának elemzése a többek között:  

* Származtasson négy alapvető kategória metrikái:
  * **Entitás (például egy előfizetés)**. Alapvető információkat szeretne az előfizetés és/vagy a vásárlói lemorzsolódás tárgyát képező kiépítése.
  * **Tevékenység** – Szerezze be a kapcsolódó entitás, például a bejelentkezések száma az összes lehetséges használati információkat.
  * **Ügyfél-támogatási**. Gyűjtsön információkat az ügyfél-támogatási naplók jelzi, hogy az előfizetés volt-e a problémákat és az ügyfél-támogatási interakciók.
  * **Versenyképes és üzleti adatokat**. Az ügyfél lehetséges minden olyan információt (például lehet érhető el vagy visszakövetését, hogy).
* Meghajtó szolgáltatás kiválasztása fontosak használja. Ez azt jelenti, hogy a gyorsított döntési fa modell mindig a ígéret megközelítést.  

Ezekben a kategóriákban használatát hoz létre a finomabb, amely egy egyszerű *determinisztikus* megközelítéseket, formázott ésszerű tényező befolyásolja, kategóriánként indexek ügyfelek lemorzsolódásának kitett azonosításához elegendőnek kell lennie. Sajnos bár ebben a formátumban egyértelmű úgy tűnik, egy hamis ismertetése. Az oka, hogy adatváltozás historikus érvénybe, és a vásárlói lemorzsolódás hozzájáruló tényezők rendszerint átmeneti állapotok. Mit kell figyelembe venni, így még ma ügyfél érdeklődők holnap lehet különböző, és természetesen lesz különböző hat hónap múlva. Ezért egy *valószínűségi* modellje áttelepítésére.  

Ez fontos megfigyelési gyakran üzleti, amely általában egy üzleti intelligencia megközelítéssel szeretnék analytics van kihagyott, leginkább, mert egy egyszerűbb értékesítésére, és könnyen érthető megjegyzésblokkok írására automation elismeri.  

Azonban beváltja az önkiszolgáló elemzés, Machine Learning Studio használatával is, ha az adatokat, részleg vagy osztály által osztályozott négy karakterkategóriából gépi tanulási lemorzsolódási kapcsolatos értékes forrást válik.  

Egy másik izgalmas funkció hamarosan elérhető az Azure Machine Learning, lehetővé teszi, hogy egy egyéni modult, előre meghatározott, amelyek már elérhetők a tárházba. Ez a funkció alapvetően hoz lehetőséget válassza ki a kódtárakat, és vertikális piacok sablonok létrehozásához. Egy fontos különbséget jelent az Azure Machine Learning piacon.  

Reméljük, hogy továbbra is a jövőben ez a témakör különösen kapcsolatos big data-elemzés.
  

## <a name="conclusion"></a>Összegzés
Ez a tanulmány azt ismerteti, hogy észszerű megközelítése problémájának közös vásárlók lemorzsolódásának általános keretrendszer használatával. Azt a modellek pontozása prototípusát minősülnek, és az Azure Machine Learning használatával implementálja. Végül azt adatokon, a pontosság és a teljesítmény, a prototípus-megoldás összehasonlítható algoritmusok a biztonsági Társítások kapcsolatban.  

 

## <a name="references"></a>Referencia
[1] prediktív elemzési: túl az előrejelzéseket, Nyugat McKnight információkezelés p.18 – 20 2011. júliusi/augusztus.  

[2] Wikipedia-cikk: [pontosság és a pontosság](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [SZÍNELOSZLÁS-DM 1.0-s: részletes adatok adatbányászati útmutatója](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [big Data típusú adatok Marketing: az ügyfelek hatékonyabb megszólítása és a meghajtó érték](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [telekommunikációs churn folyamatmodell-sablont](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) a [Azure AI-katalógusban](http://gallery.cortanaintelligence.com/) 
 

## <a name="appendix"></a>Függelék
![][10]

*12. ábra: Pillanatképét bemutató adatváltozás-prototípuson*

[1]: ./media/azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/azure-ml-customer-churn-scenario/churn-10.png
