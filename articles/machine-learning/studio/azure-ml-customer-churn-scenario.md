---
title: Ügyfél-adatforgalom elemzése
titleSuffix: ML Studio (classic) Azure
description: Esettanulmány olyan integrált modell kidolgozásához, amely az ügyfelek adatforgalmának elemzéséhez és pontozásához Azure Machine Learning Studio (klasszikus) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 063b745bbf1c5e8453e0f6abe3cefdc76a60b5f9
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619737"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Ügyfél-adatforgalom elemzése Azure Machine Learning Studio (klasszikus) használatával
## <a name="overview"></a>Áttekintés
Ez a cikk a Azure Machine Learning Studio (klasszikus) használatával létrehozott ügyfél-adatforgalom-elemzési projekt hivatkozási megvalósítását ismerteti. Ebben a cikkben a kapcsolódó általános modelleket tárgyaljuk az ipari ügyfelek adatforgalmával kapcsolatos problémák holisztikus megoldásához. A Machine Learning használatával létrehozott modellek pontosságát is mérjük, és értékeljük a további fejlesztés irányait.  

### <a name="acknowledgements"></a>Nyugtázása
Ezt a kísérletet a Serge Berger, a Microsoft legfontosabb adattudósa és Roger Barga, korábban a Microsoft Azure Machine Learning Studio (klasszikus) termék-kezelője fejlesztette ki és tesztelte. Az Azure dokumentációs csapata hálásan tudomásul veszi szakértelmét, és köszönjük nekik a tanulmány megosztását.

> [!NOTE]
> A kísérlethez használt adathalmaz nem nyilvánosan elérhető. A következő témakörből megtudhatja, hogyan hozhat létre egy gépi tanulási modellt az adatváltozások elemzéséhez: a kiskereskedelmi adatváltozási [modell sablonja](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>Az ügyfelek adatforgalmával kapcsolatos probléma
A fogyasztói piacon és minden nagyvállalati ágazatban tevékenykedő vállalkozásoknak a változásokkal kell foglalkoznia. Időnként a forgalom túlzott, és hatással van a szabályzatra vonatkozó döntések meghozatalára. A hagyományos megoldás az, hogy megjósolja a nagy teljesítményű és az igényeiket a concierge-szolgáltatás, a marketing-kampányok vagy a speciális kiosztások alkalmazása révén. Ezek a módszerek az iparágtól az iparágig eltérőek lehetnek. Egy adott fogyasztói fürttől akár egy másik iparágon (például a távközlésen) belül is változhatnak.

A gyakori tényező az, hogy a vállalatoknak minimálisra kell csökkenteniük ezeket a speciális ügyfelek megőrzési erőfeszítéseit. A természetes módszer tehát az, ha az összes ügyfelet kihasználja a változás valószínűségével, és megkeresi a legfontosabb N-ot. A legfontosabb ügyfelek lehetnek a legjövedelmezőbb adatbázisok. Például bonyolultabb forgatókönyvekben a profit funkciót a jelöltek kiválasztásakor a speciális felmentéshez kell alkalmazni. Ezek a szempontok azonban csak a teljes stratégia részét képezik az adatforgalom kezeléséhez. A vállalatoknak figyelembe kell venniük a kockázatokat (és a hozzájuk kapcsolódó kockázati toleranciát), a beavatkozás szintjét és költségeit, valamint a mérvadó ügyfelek szegmentálását.  

## <a name="industry-outlook-and-approaches"></a>Iparági kilátások és megközelítések
A forgalom kifinomult feldolgozása egy érett iparág jele. A klasszikus példa az a távközlési iparág, ahol az előfizetőknek gyakran kell váltaniuk az egyik szolgáltatóról a másikra. Ez az önkéntes változás elsődleges szempont. Emellett a szolgáltatók jelentős ismereteket gyűjtöttek az adatforgalom- *illesztőprogramokról*, amelyek az ügyfelek által a váltáshoz szükséges tényezők.

Például a kézibeszélő vagy az eszköz választása a mobiltelefonos üzleti forgalom jól ismert mozgatórugója. Ennek eredményeképpen egy népszerű szabályzat egy új előfizetők árának a megfizetése, és a meglévő ügyfelek teljes díja a frissítéshez. Ennek a szabályzatnak a segítségével a felhasználók az egyik szolgáltatóról a másikra ugrálva új kedvezményt nyerhetnek. Ez viszont a szolgáltatók számára kéri a stratégiák pontosítását.

A kézibeszélő-ajánlatok magas volatilitása olyan tényező, amely gyorsan érvényteleníti a jelenlegi kézibeszélő-modelleken alapuló adatforgalom modelljeit. Emellett a mobiltelefonok nem csak a telekommunikációs eszközök, hanem a Fashion-utasítások (az iPhone-t is figyelembe veszik). Ezek a közösségi előrejelzők a normál távközlési adatkészletek hatókörén kívül esnek.

A modellezéssel kapcsolatos nettó eredmény az, hogy nem lehet hangházirendet kidolgozni, mert nem kell megszüntetnie az adatforgalom ismert okait. Valójában a folyamatos modellezési stratégia, beleértve a kategorikus változókat (például döntési fákat) számszerűsítő klasszikus modelleket, **kötelező megadni**.

A big data készletek használata az ügyfeleken a szervezetek big data elemzést hajtanak végre (különösen a big data alapján történő adatváltozások észlelését) a probléma hatékony megközelítése érdekében. További információ a big datai megközelítésről az ETL-re vonatkozó ajánlásokat ismertető szakaszban.  

## <a name="methodology-to-model-customer-churn"></a>Az ügyfél-adatforgalom modellezésének módszertana
Az ügyfelek adatforgalmának megoldására szolgáló gyakori problémamegoldó folyamat az 1-3-es számú ábrán látható:  

1. A kockázati modell segítségével megvizsgálhatja, hogy a műveletek milyen hatással vannak a valószínűségre és a kockázatokra.
2. Az intervenciós modell lehetővé teszi, hogy meggondolja, hogyan érintheti a beavatkozási szint a forgalom valószínűségét és az ügyfél élettartamának értékét (CLV).
3. Ez az elemzés egy olyan minőségi elemzéshez nyújt támogatást, amely egy proaktív marketing-kampányhoz tartozik, amely az optimális ajánlat biztosítására célozza az ügyfelek szegmenseit.  

![Ábra, amely bemutatja, hogy a kockázati tolerancia és a döntési modellek hogyan eredményeznek gyakorlatban hasznosítható elemzéseket](./media/azure-ml-customer-churn-scenario/churn-1.png)

Ez a előremutató megközelítés a legjobb módszer a forgalom kezeléséhez, de összetettsége van: egy többmodelles archetípusot és a modellek közötti nyomkövetési függőségeket kell kifejleszteni. A modellek közötti interakció a következő ábrán látható módon ágyazható be:  

![Adatváltozási modell interakciós diagramja](./media/azure-ml-customer-churn-scenario/churn-2.png)

*4. ábra: egységes multi-Model archetípus*  

A modellek közötti interakció kulcsfontosságú, ha átfogó megközelítést kívánunk biztosítani az ügyfelek megtartására. Minden modell szükségszerűen csökkenti az idő múlását; Ezért az architektúra egy implicit hurok (hasonlóan a ROPOGÓs-DM adatbányászati standard által beállított archetípushoz, [***3***]).  

A kockázat-döntési marketing szegmentálása/bomlási ciklusa továbbra is általánosított struktúra, amely számos üzleti problémára alkalmazható. Az adatváltozások elemzése egyszerűen csak erős képviselő a probléma megoldására, mivel egy összetett üzleti probléma összes tulajdonságát mutatja, amely nem teszi lehetővé egyszerűsített prediktív megoldás használatát. Az adatváltozások modern megközelítésének közösségi szempontjai nem kiemelten kiemelten szerepelnek a megközelítésben, de a közösségi szempontok a modellezési archetípusban vannak, mivel bármilyen modellben lennének.  

Itt is érdekes big data elemzések. A mai távközlési és kiskereskedelmi vállalatok teljes körű adatokat gyűjtenek az ügyfelekről, és könnyen bemutatjuk, hogy a többmodelles kapcsolatok iránti igény általános trend lesz, mivel a feltörekvő trendek, például a eszközök internetes hálózata és a mindenütt elérhetők eszközök, amelyek lehetővé teszik, hogy a vállalatok több rétegen is intelligens megoldásokat alkalmazzanak.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>A modellezési archetípus implementálása Machine Learning Studio (klasszikus)
Az ismertetett probléma miatt mi a legjobb módszer egy integrált modellezési és pontozási módszer megvalósítására? Ebben a szakaszban bemutatjuk, hogyan teljesítjük ezt a Azure Machine Learning Studio klasszikus verziójának használatával.  

A többmodelles megközelítés a globális archetípusok megtervezése során szükséges. Még a megközelítés pontozási (prediktív) részét is többmodelles értékre kell állítani.  

Az alábbi ábrán az általunk létrehozott prototípus látható, amely négy pontozási algoritmust alkalmaz Machine Learning Studio (klasszikus) a forgalom előrejelzésére. A többmodelles megközelítés használatának oka nem csupán egy Ensemble-osztályozó létrehozása a pontosság növeléséhez, hanem a túlterhelések elleni védelemhez és az előírásos funkciók kiválasztásának javításához is.  

![Egy összetett Studio (klasszikus) munkaterületet ábrázoló képernyőkép, amely számos összekapcsolt modullal rendelkezik](./media/azure-ml-customer-churn-scenario/churn-3.png)

*5. ábra: az adatváltozások modellezési megközelítésének prototípusa*  

A következő szakaszokban további részleteket talál a Machine Learning Studio (klasszikus) használatával megvalósított prototípus-pontozási modellről.  

### <a name="data-selection-and-preparation"></a>Adatkiválasztás és-előkészítés
A modellek létrehozásához használt adatok és az ügyfelek pontszáma egy CRM vertikális megoldásból származik, és az adatok az ügyfelek adatainak védelme érdekében eltorzítva vannak. Az adatok az Egyesült államokbeli 8 000-előfizetésekkel kapcsolatos információkat tartalmaznak, és három forrást egyesítenek: az adatok kiépítési (előfizetési metaadatok), a tevékenységre vonatkozó adatokat (a rendszer használatát) és az ügyfél-támogatási adatokat. Az adatok nem tartalmazzák az ügyfelekkel kapcsolatos üzleti adatokat; például nem tartalmazza a lojalitási metaadatokat vagy a jóváírási pontszámokat.  

Az egyszerűség kedvéért az ETL és az adattisztítási folyamatok a hatókörön kívül vannak, mivel feltételezzük, hogy az adatelőkészítés máshol már megtörtént.

A modellezés funkcióinak kiválasztása a véletlenszerű erdő modult használó folyamaton belül az előrejelzők készletének előzetes fontossági pontozásán alapul. Machine Learning Studio (klasszikus) megvalósításához a reprezentatív funkciók középértékét, középértékét és tartományait számítjuk ki. Például hozzáadta a minőségi adatok összesítéseit, például a felhasználók tevékenységének minimális és maximális értékeit.

A legutóbbi hat hónapra vonatkozóan is rögzítettünk információkat. A rendszer egy évig elemezte az adatelemzést, és úgy állapították meg, hogy még akkor is, ha statisztikailag jelentős tendenciát mutatnak, a változás hatása jelentősen csökken hat hónap elteltével.  

A legfontosabb szempont, hogy a teljes folyamat, beleértve az ETL-t, a szolgáltatások kijelölését és a modellezést Machine Learning Studio (klasszikus), Microsoft Azure adatforrások használatával implementálták.   

Az alábbi ábrák a felhasznált adathalmazokat szemléltetik.  

![A nyers értékekkel használt adatok mintáját ábrázoló képernyőfelvétel](./media/azure-ml-customer-churn-scenario/churn-4.png)

*6. ábra: adatforrás kivonata (eltorzítva)*  

![Az adatforrásból kinyert statisztikai funkciókat ábrázoló képernyőkép](./media/azure-ml-customer-churn-scenario/churn-5.png)

*7. ábra: az adatforrásból kinyert funkciók*
 

> Vegye figyelembe, hogy ezek az adatkészletek magánjellegűek, ezért a modell és az adattípusok nem oszthatók meg.
> Ahhoz azonban, hogy egy hasonló modell nyilvánosan elérhető adatforgalomot használjon, tekintse meg ezt a minta kísérletet a [Azure AI Gallery](https://gallery.azure.ai/): [távközlési ügyfelek](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383)adatforgalmáról.
> 
> Ha többet szeretne megtudni arról, hogyan valósítható meg a forgalom-elemzési modell a Cortana Intelligence Suite használatával, ajánljuk [ezt a videót](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) a Senior program Manager pici Hyong tok használatával is. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>A prototípusban használt algoritmusok
A következő négy gépi tanulási algoritmust használtuk a prototípus létrehozásához (testreszabás nélkül):  

1. Logisztikai regresszió (LR)
2. Megnövelt döntési fa (BT)
3. Átlagos perceptron (AP)
4. A Vector Machine (SVM) támogatása  

A következő ábra a kísérlet tervezési felületének egy részét szemlélteti, amely a modellek létrehozásának sorrendjét jelzi:  

![A Studio-kísérlet vászonjának kis szakaszának képernyőképe](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*8. ábra: modellek létrehozása Machine Learning Studio (klasszikus)*  

### <a name="scoring-methods"></a>Pontozási módszerek
A négy modellt egy címkével ellátott betanítási adatkészlet használatával szerezte be.  

A pontozási adatkészletet egy összehasonlítható modellre is elküldte, amely az SAS Enterprise Miner 12 asztali kiadásával készült. A SAS-modell pontosságát és mind a négy Machine Learning Studio (klasszikus) modellt mértük.  

## <a name="results"></a>Results (Eredmények)
Ebben a szakaszban bemutatjuk a modellek pontosságával kapcsolatos megállapításokat a pontozási adatkészlet alapján.  

### <a name="accuracy-and-precision-of-scoring"></a>Pontozás pontossága és pontossága
Általánosságban elmondható, hogy a Azure Machine Learning Studio klasszikus verziójának implementálása a (z) 10-15% (a görbe vagy a AUC alatti terület) pontossága mögötti SAS.  

Azonban a forgalom legjelentősebb mérőszáma a téves besorolási arány: vagyis az osztályozó által előre jelzett legfontosabb N-adatforgalom, amelyek valójában **nem** voltak forgalomban, és különleges kezelést is kapott? A következő diagram az összes modellhez hasonlítja ezt a téves besorolási arányt:  

![4 algoritmus teljesítményét összehasonlító görbe gráf alatti terület](./media/azure-ml-customer-churn-scenario/churn-7.png)

*9. ábra: Passau prototípus területe a görbe alatt*

### <a name="using-auc-to-compare-results"></a>Az AUC használata az eredmények összehasonlításához
A görbe (AUC) alatti terület egy metrika, amely a pozitív és negatív populációk eredményeinek eloszlása közötti *separability* globális mértékét jelöli. A hagyományos fogadó operátor jellemző (ROC) gráfhoz hasonló, de az egyik fontos különbség, hogy a AUC metrika nem igényli a küszöbérték kiválasztását. Ehelyett összefoglalja az eredményeket az **összes** lehetséges lehetőség közül. Ezzel szemben a hagyományos ROC-gráf a függőleges tengely pozitív arányát és a vízszintes tengelyen a hamis pozitív arányt mutatja, és a besorolási küszöbérték változó.   

A AUC a különböző algoritmusok (vagy különböző rendszerek) esetében érdemes használni, mert lehetővé teszi a modellek összehasonlítását a AUC értékeivel. Ez egy népszerű megközelítés olyan iparágakban, mint a meteorológia és a Bioscience. Így a AUC egy népszerű eszközt képvisel az osztályozó teljesítményének értékeléséhez.  

### <a name="comparing-misclassification-rates"></a>A téves besorolás arányának összehasonlítása
Összehasonlítjuk a kérdéses adatkészletben található, körülbelül 8 000 előfizetés CRM-adataival.  

* Az SAS-besorolási arány 10-15% volt.
* A Machine Learning Studio (klasszikus) téves besorolási arány 15-20% volt a Top 200-300-adatforgalomhoz.  

A telekommunikációs iparágban fontos, hogy csak azokat az ügyfeleket kezelje, akik a legnagyobb kockázatot jelentenek, ha concierge szolgáltatást vagy más különleges kezelést biztosítanak. Ebben a tekintetben a Machine Learning Studio (klasszikus) implementáció az SAS-modellel egyenrangú eredményeket hoz létre.  

Ugyanezen jogkivonatban a pontosság sokkal fontosabb, mint a pontosság, mivel főleg a lehetséges adatforgalom megfelelő besorolását érdeklik.  

A wikipedia következő diagramja egy élénk, könnyen értelmezhető grafikában ábrázolja a kapcsolatot:  

![Két cél. Az egyik cél, hogy a találatok lazán vannak csoportosítva, de közel vannak a "kis pontosság: jó igaz" értékhez, a gyenge pontossághoz. Egy másik cél szorosan csoportosítva van, de távol van a "kis pontosságú: gyenge megbízhatóság", jó pontossággal jelölt Bulls-szemtől.](./media/azure-ml-customer-churn-scenario/churn-8.png)

*10. ábra: a pontosság és a pontosság közötti kompromisszum*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>A megnövelt döntési famodell pontossági és pontossági eredményei
A következő diagram a pontozásból származó nyers eredményeket jeleníti meg a megnövelt döntési famodellhez tartozó Machine Learning prototípus használatával, amely a négy modell közül a legpontosabb:  

![A pontosságot, a pontosságot, a visszahívás, az F-score, a AUC, az átlagos naplók elvesztését és a betanítási napló elvesztését ábrázoló táblázat négy algoritmus esetén](./media/azure-ml-customer-churn-scenario/churn-9.png)

*11. ábra: a megerősítő döntési fa modelljének jellemzői*

## <a name="performance-comparison"></a>Teljesítmény-összehasonlítás
Összehasonlítjuk az adatgyűjtési sebességet a Machine Learning Studio (klasszikus) modellek és egy hasonló modell használatával, amely az SAS Enterprise Miner 12,1 asztali kiadásával lett létrehozva.  

A következő táblázat összefoglalja az algoritmusok teljesítményét:  

*1. táblázat. Az algoritmusok általános teljesítménye (pontossága)*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Átlagos modell |A legjobb modell |Alulteljesítő |Átlagos modell |

A Machine Learning Studio (klasszikus) által szolgáltatott modellek a végrehajtás gyorsasága érdekében 15-25%-kal, de a pontosság nagyjából par volt.  

## <a name="discussion-and-recommendations"></a>Vitafórumok és javaslatok
A távközlési iparágban számos gyakorlat alakult ki a forgalom elemzéséhez, többek között:  

* Metrikák származtatása négy alapvető kategóriához:
  * **Entitás (például egy előfizetés)** . Az előfizetésre és/vagy az ügyfélre vonatkozó alapvető információk kiépítése a forgalom tárgya.
  * **Tevékenység** – Szerezze be az entitáshoz kapcsolódó összes lehetséges használati információt, például a bejelentkezések számát.
  * **Ügyfélszolgálati támogatás**. Az ügyfélszolgálati naplókból származó információk begyűjtése annak jelzésére, hogy az előfizetés az ügyfélszolgálattal kapcsolatos problémákkal vagy interakciókkal járt-e.
  * **Versenyképes és üzleti adatszolgáltatások**. Szerezzen be bármilyen információt az ügyfélről (például nem érhető el vagy nem nehéz nyomon követni).
* A szolgáltatás kiválasztásának fontossága. Ez azt jelenti, hogy a megnövelt döntési fa modell mindig ígéretes megközelítés.  

Ennek a négy kategóriának a használata azt az illúziót hozza létre, hogy egy egyszerű *determinisztikus* megközelítés a kategóriánkénti ésszerű tényezők alapján Sajnos, bár ez a fogalom kézenfekvőnek tűnik, ez hamis megértést mutat. Ennek az az oka, hogy a forgalom időbeli hatás, és a változásokhoz hozzájáruló tényezők általában átmeneti állapotokban vannak. Mi vezet az ügyfél számára, hogy a mai napig elhagyhatja a jövőt, és az is előfordulhat, hogy már hat hónapja lesz. Ezért szükség van egy *valószínűségi* modellre.  

Ez a fontos megfigyelés gyakran megtekinthető az üzleti életben, ami általában előnyben részesíti az elemzések üzleti intelligenciával kapcsolatos megközelítését, főleg azért, mert ez egy könnyebben értékesíthető és egyszerű automatizálási megoldás.  

Az önkiszolgáló elemzések Machine Learning Studio (klasszikus) használatával történő használatának ígérete azonban az, hogy a négy, osztással vagy részleggel osztályozott információ értékes forrást jelent a gépi tanulással kapcsolatban.  

A Azure Machine Learning Studio klasszikus verziójának egy másik izgalmas funkciója, amely lehetővé teszi, hogy egyéni modult adjon hozzá a már elérhető előre definiált modulok tárházához. Ez a képesség lényegében lehetőséget teremt a kódtárak kiválasztására és sablonok létrehozására a vertikális piacok számára. A piacon a Azure Machine Learning Studio klasszikus verziójának fontos megkülönböztetője.  

Reméljük, hogy a jövőben is folytatjuk ezt a témakört, különösen a big data analyticshez kapcsolódóan.
  

## <a name="conclusion"></a>Összegzés
Ez a tanulmány egy ésszerű módszert mutat be az ügyfelek adatforgalmával kapcsolatos gyakori problémák általános keretrendszer használatával történő kezeléséhez. A pontozási modellek prototípusát tekintették, és a Azure Machine Learning Studio klasszikus verzióját használva Implementáljuk. Végezetül felmértük a prototípus megoldás pontosságát és teljesítményét az SAS-ben található hasonló algoritmusok tekintetében.  

 

## <a name="references"></a>Referencia
[1] prediktív elemzés: az előrejelzéseken felül a W. McKnight, az Information Management, a július/August 2011, a p. 18 – 20.  

[2] wikipedia-cikk: [pontosság és pontosság](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [ropogós-DM 1,0: részletes adatbányászati útmutató](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Value marketing: az ügyfelek hatékonyabb és a meghajtó értéke](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] a távközlési adatforgalom [modell-sablonja](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Függelék
![Bemutató pillanatképe a változási prototípusról](./media/azure-ml-customer-churn-scenario/churn-10.png)

*12. ábra: bemutató pillanatképe a változási prototípusról*
