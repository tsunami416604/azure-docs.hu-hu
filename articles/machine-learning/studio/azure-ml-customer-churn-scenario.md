---
title: Vevői lemorzsolódás elemzése
titleSuffix: ML Studio (classic) - Azure
description: Esettanulmány egy integrált modell kifejlesztéséhez az Azure Machine Learning Studio (klasszikus) használatával az ügyfelek lemorzsolódásának elemzéséhez és pontozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 4cf918abae51ca330054ef86e57095d29a21a37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204528"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Ügyféllemorzsolódás elemzése az Azure Machine Learning Studio használatával (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja egy ügyfél lemorzsolódás elemzési projekt referencia-megvalósítása, amely az Azure Machine Learning Studio (klasszikus) használatával készült. Ebben a cikkben megvitatjuk a kapcsolódó általános modellek holisztikusan megoldani a problémát az ipari ügyfél lemorzsolódás. A Machine Learning használatával készített modellek pontosságát is mérjük, és felmérjük a további fejlesztéshez vezető utasításokat.  

### <a name="acknowledgements"></a>Nyugták
Ezt a kísérletet Serge Berger, a Microsoft vezető adattudósa és Roger Barga, a Microsoft Azure Machine Learning Studio (klasszikus) termékmenedzsere fejlesztette ki és tesztelte. Az Azure dokumentációs csapata hálásan elismeri szakértelmüket, és megköszöni nekik, hogy megosztották ezt a tanulmányt.

> [!NOTE]
> A kísérlethez használt adatok nem nyilvánosan hozzáférhetők. Ha például egy gépi tanulási modellt hozhat létre a lemorzsolódáselemzéshez, tekintse meg: [Kiskereskedelmi lemorzsolódási modellsablon](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) az [Azure AI-galériában](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>A probléma az ügyfél lemorzsolódás
A fogyasztói piacon és minden vállalati ágazatban működő vállalkozásoknak meg kell birkózniuk a lemorzsolódással. Néha a lemorzsolódás túlzott, és befolyásolja a politikai döntéseket. A hagyományos megoldás az, hogy megjósolni a nagy hajlandóság churners és igényeik kielégítésére keresztül concierge szolgáltatás, marketing kampányok, vagy alkalmazásával különleges felmentést. Ezek a megközelítések iparágonként eltérőek lehetnek. Akár egy adott fogyasztói klaszterről egy másikra is változhatnak egy iparágon belül (például távközlés).

A közös tényező az, hogy a vállalkozásoknak minimalizálniuk kell ezeket a különleges ügyfélmegőrzési erőfeszítéseket. Így a természetes módszer az lenne, hogy pontszám minden ügyfél a valószínűsége lemorzsolódás és cím a felső N is. A legjobb ügyfelek lehetnek a legjövedelmezőbbek. Például a kifinomultabb forgatókönyvekben profitfunkciót alkalmaznak a különleges felmentésre jelentkezők kiválasztásasorán. Ezek a megfontolások azonban csak egy részét képezik a lemorzsolódás kezelésére vonatkozó teljes stratégiának. A vállalkozásoknak figyelembe kell venniük a kockázatot (és a kapcsolódó kockázati toleranciát), a beavatkozás szintjét és költségét, valamint a valószínű ügyfélszegmentációt.  

## <a name="industry-outlook-and-approaches"></a>Iparági kilátások és megközelítések
A lemorzsolódás kifinomult kezelése egy érett iparág jele. A klasszikus példa a távközlési ágazat, ahol az előfizetők ismert, hogy gyakran váltani az egyik szolgáltató a másikra. Ez az önkéntes lemorzsolódás elsődleges szempont. Ráadásul a szolgáltatók jelentős ismereteket gyűjtöttek a *lemorzsolódási vezetőkről*, amelyek olyan tényezők, amelyek az ügyfeleket a váltásra késztetik.

Például, kézibeszélő vagy berendezés válogatott van egy jól- tudni vezető -ból lemorzsolódás -ban mozgatható telefon teendő. Ennek eredményeképpen a népszerű politika az, hogy támogassa a készülék árát az új előfizetők számára, és teljes árat számít sértsen meg a meglévő ügyfelekkel a frissítésért. Történelmileg ez a házirend oda vezetett, hogy az ügyfelek egyik szolgáltatóról a másikra ugráltak, hogy új kedvezményt kapjanak. Ez pedig arra késztette a szolgáltatókat, hogy finomítsák stratégiáikat.

A kézibeszélő kínálatának magas volatilitása olyan tényező, amely gyorsan érvényteleníti a jelenlegi kézibeszélő-modelleken alapuló lemorzsolódási modelleket. Ezen túlmenően, a mobiltelefonok nem csak a távközlési eszközök, ők is divat nyilatkozatok (úgy az iPhone). Ezek a társadalmi előrejelzők kívül esnek a rendszeres távközlési adatkészletek hatókörén.

A modellezés nettó eredménye az, hogy nem lehet kidolgozni egy megbízható politikát egyszerűen kiküszöbölve az ismert okokat lemorzsolódás. Valójában kötelező a folyamatos modellezési stratégia, beleértve **a**kategorikus változókat számszerűsítő klasszikus modelleket (például a döntési fákat).

A big data-készletek et az ügyfeleiken használva a szervezetek big data-elemzéseket végeznek (különösen a big data-adatokon alapuló lemorzsolódásészlelést), mint a probléma hatékony megközelítését. Az ETL-javaslatok című szakaszban található a big data-alapú megközelítésről.  

## <a name="methodology-to-model-customer-churn"></a>Az ügyféllemorzsolódás modellezésének módszertana
Az 1-3.  

1. A kockázati modell lehetővé teszi, hogy fontolja meg, hogyan hatások műveletek valószínűség és kockázat.
2. Az intervenciós modell lehetővé teszi annak mérlegelését, hogy a beavatkozás szintje hogyan befolyásolhatja a lemorzsolódás valószínűségét és az ügyfél élettartama értékének (CLV) összegét.
3. Ez az elemzés alkalmas arra, hogy egy minőségi elemzés, amely eszkalálódik egy proaktív marketing kampány, amely az ügyfelek szegmenseit célozza, hogy az optimális ajánlatot.  

![Az ábrán látható, hogy a kockázattűrés és a döntési modellek hogyan eredményeznek hasznos elemzéseket](./media/azure-ml-customer-churn-scenario/churn-1.png)

Ez az előretekintő megközelítés a legjobb módja a lemorzsolódás kezelésének, de összetettséggel jár: ki kell dolgoznunk egy többmodelles archetípust és nyomon követési függőségeket a modellek között. A modellek közötti kölcsönhatás az alábbi ábrán látható módon foglalható be:  

![Lemorzsolódási modell interakciós diagramja](./media/azure-ml-customer-churn-scenario/churn-2.png)

*4. ábra: Egységes többmodelles archetípus*  

A modellek közötti interakció kulcsfontosságú, ha azt akarjuk, hogy holisztikus megközelítést ügyfél megtartása. Minden modell szükségszerűen romlik az idő múlásával; ezért az architektúra egy implicit hurok (hasonlóan a CRISP-DM adatbányászati szabvány által beállított archetípushoz, [***3***]).  

A kockázat-döntés-marketing szegmentáció/bomlás teljes ciklusa még mindig általános szerkezet, amely számos üzleti problémára alkalmazható. A lemorzsolódás elemzés egyszerűen erős képviselője ennek a problémacsoportnak, mert egy összetett üzleti probléma minden olyan jellemzőit mutatja, amely nem teszi lehetővé az egyszerűsített prediktív megoldást. A modern lemorzsolódási megközelítés társadalmi aspektusai nem különösebben jelennek meg a megközelítésben, de a szociális szempontok a modellezési archetípusba vannak beágyazva, mint bármely modellben.  

Egy érdekes kiegészítés itt a big data-elemzés. A mai távközlési és kiskereskedelmi vállalkozások kimerítő adatokat gyűjtenek ügyfeleikről, és könnyen előre látjuk, hogy a többmodelles internetkapcsolat szükségessége közös tendenciává válik, tekintettel az olyan új tendenciákra, mint a dolgok internete és a mindenütt jelenlévők. amelyek lehetővé teszik az üzleti vállalkozások számára, hogy intelligens megoldásokat alkalmazzanak több rétegben.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>A modellezési archetípus megvalósítása a Machine Learning Studio-ban (klasszikus)
Tekintettel a leírt problémára, mi a legjobb módja az integrált modellezési és pontozási megközelítés megvalósításának? Ebben a szakaszban bemutatjuk, hogyan valósítottuk meg ezt az Azure Machine Learning Studio (klasszikus) használatával.  

A többmodelles megközelítés elengedhetetlen a lemorzsolódás globális archetípusának tervezésekor. Még a pontozási (prediktív) része a megközelítés kell többmodell.  

Az alábbi ábra az általunk létrehozott prototípust mutatja be, amely négy pontozási algoritmust alkalmaz a Machine Learning Studio (klasszikus) alkalmazásban a lemorzsolódás előrejelzésére. A többmodelles megközelítés használatának oka nem csak az, hogy együttesosztályozót hozzon létre a pontosság növelése érdekében, hanem a túlillesztés elleni védelem és az előíró funkciók kiválasztásának javítása is.  

![Egy összetett Studio (klasszikus) munkaterületet ábrázoló képernyőkép, amely számos összekapcsolt modult tartalmaz](./media/azure-ml-customer-churn-scenario/churn-3.png)

*5. ábra: A lemorzsolódásmodellezési megközelítés prototípusa*  

A következő szakaszok további részleteket tartalmaznak a machine learning studio (klasszikus) használatával megvalósított prototípus pontozási modellről.  

### <a name="data-selection-and-preparation"></a>Adatok kiválasztása és előkészítése
A modellek és a pontszám ügyfelek létrehozásához használt adatok crm vertikális megoldásból származnak, az adatokat pedig az ügyfelek adatainak védelme érdekében titorították el. Az adatok 8000 egyesült államok-i előfizetésadatait tartalmazzák, és három forrást egyesít: az adatok kiépítése (előfizetési metaadatok), tevékenységadatok (a rendszer használata) és az ügyfélszolgálati adatok. Az adatok nem tartalmaznak üzleti információkat az ügyfelekről; például nem tartalmazza a hűség metaadatait vagy a hitelpontszámokat.  

Az egyszerűség kedvéért az ETL és az adattisztítási folyamatok hatókörön kívül esnek, mert feltételezzük, hogy az adatok előkészítése már máshol történt.

A modellezéshez használt funkciók kiválasztása a véletlenszerű erdőmodult használó folyamat részét képező előrejelzőkészlet előzetes jelentősségi pontozásán alapul. A Machine Learning Studio (klasszikus) megvalósításához kiszámítottuk a reprezentatív funkciók átlagát, mediánját és tartományait. Például aktaggregátumokat adtunk hozzá a minőségi adatokhoz, például a felhasználói tevékenység minimális és maximális értékeit.

Az elmúlt hat hónap időbeli információit is rögzítettük. Egy évig elemeztük az adatokat, és megállapítottuk, hogy még ha statisztikailag szignifikáns tendenciák is voltak, a lemorzsolódásra gyakorolt hatás hat hónap után jelentősen csökkent.  

A legfontosabb pont az, hogy a teljes folyamat, beleértve az ETL, a szolgáltatás kiválasztása és a modellezés a Machine Learning Studio (klasszikus), adatforrások használatával a Microsoft Azure-ban.   

Az alábbi ábrák a felhasznált adatokat mutatják be.  

![Képernyőkép a nyers értékekkel használt adatokból](./media/azure-ml-customer-churn-scenario/churn-4.png)

*6. ábra: Adatforrás-részlet (elhomályosítva)*  

![Az adatforrásból kinyert statisztikai funkciókat ábrázoló képernyőkép](./media/azure-ml-customer-churn-scenario/churn-5.png)

*7. ábra: Adatforrásból kinyert jellemzők*
 

> Vegye figyelembe, hogy ezek az adatok titkosak, ezért a modell és az adatok nem oszthatók meg.
> Azonban egy hasonló modell nyilvánosan elérhető adatokat használó, lásd ezt a mintakísérletet az [Azure AI Galéria:](https://gallery.azure.ai/) [Telco Customer Churn](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Ha többet szeretne megtudni arról, hogyan valósíthat meg egy lemorzsolódáselemzési modellt a Cortana Intelligence Suite használatával, [ezt a videót](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) Wee Hyong Tok vezető programmenedzsertől is ajánljuk. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>A prototípusban használt algoritmusok
A prototípus elkészítéséhez a következő négy gépi tanulási algoritmust használtuk (nincs testreszabás):  

1. Logisztikai regresszió (LR)
2. Kiemelt döntési fa (BT)
3. Átlagos perceptron (AP)
4. Támogató vektorgép (SVM)  

Az alábbi ábra a kísérlet tervezési felületének egy részét mutatja be, amely a modellek létrehozásának sorrendjét jelzi:  

![Képernyőkép a stúdiókísérlet vászonjának egy kis részéről](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*8. ábra: Modellek létrehozása a Machine Learning Studio-ban (klasszikus)*  

### <a name="scoring-methods"></a>Pontozási módszerek
A négy modellt egy címkézett betanítási adatkészlet használatával szereztük ki.  

A pontozási adatkészletet egy hasonló modellhez is benyújtottuk, amelyet a SAS Enterprise Miner 12 asztali kiadásával készítettünk. A SAS-modell és mind a négy Machine Learning Studio (klasszikus) modell pontosságát mértük.  

## <a name="results"></a>Results (Eredmények)
Ebben a szakaszban bemutatjuk a modellek pontosságával kapcsolatos megállapításainkat a pontozási adatkészlet alapján.  

### <a name="accuracy-and-precision-of-scoring"></a>A pontozás pontossága és pontossága
Általában az Azure Machine Learning Studio (klasszikus) megvalósítása körülbelül 10–15%-kal (curve vagy AUC alatt) a SAS mögött van.  

Azonban a legfontosabb mérőszám a lemorzsolódásban a téves besorolási arány: azaz a legjobb N churners, ahogy azt az osztályozó megjósolta, melyikük valójában **nem** kavarog, és mégis különleges bánásmódban részesült? Az alábbi ábra az összes modell téves besorolási arányát hasonlítja össze:  

![A görbediagram alatti terület, amely összehasonlítja 4 algoritmus teljesítményét](./media/azure-ml-customer-churn-scenario/churn-7.png)

*9. ábra: Passau prototípus területe görbe alatt*

### <a name="using-auc-to-compare-results"></a>Az AUC használata az eredmények összehasonlításához
A görbe alatti terület (AUC) egy olyan mérőszám, amely a pozitív és negatív populációk pontszámainak eloszlása közötti *elválasztódás* globális mérőszámát jelenti. Ez hasonló a hagyományos receiver operator characteristic (ROC) grafikonhoz, de az egyik fontos különbség az, hogy az AUC metrika nem követeli meg a küszöbérték kiválasztását. Ehelyett összefoglalja az eredményeket az **összes** lehetséges választási lehetőségről. Ezzel szemben a hagyományos ROC-grafikon a függőleges tengely pozitív és a vízszintes tengelyen lévő hamis pozitív árfolyamot mutatja, és az osztályozási küszöbérték változó.   

Az AUC-t a különböző algoritmusok (vagy különböző rendszerek) értékének mérésére használják, mivel lehetővé teszi a modellek AUC-értékekkel történő összehasonlítását. Ez egy népszerű megközelítés az olyan iparágakban, mint a meteorológia és a biotudományok. Így az AUC népszerű eszköz az osztályozó teljesítmény értékelésére.  

### <a name="comparing-misclassification-rates"></a>A téves besorolási arányok összehasonlítása
Összehasonlítottuk a szóban forgó adatkészlet téves besorolási arányait körülbelül 8000 előfizetés CRM-adatainak felhasználásával.  

* A SAS téves besorolási aránya 10-15% volt.
* A Machine Learning Studio (klasszikus) téves besorolási aránya 15-20% volt a top 200-300 kavarogók esetében.  

A távközlési iparban fontos, hogy csak azokat az ügyfeleket szólítsa meg, akiknél a legnagyobb a kockázat a lemorzsolódásra azáltal, hogy concierge szolgáltatást vagy más különleges bánásmódot kínál nakik. Ebben a tekintetben a Machine Learning Studio (klasszikus) megvalósítása eredményeket ér el a SAS-modellel egyenrangúan.  

Ugyanezen az alapon a pontosság fontosabb, mint a pontosság, mert leginkább a potenciális köpüllők helyes osztályozása érdekel minket.  

A Következő ábra a Wikipedia ábrázolja a kapcsolatot egy élénk, könnyen érthető grafika:  

![Két célpont. Az egyik cél azt mutatja, találati jelek lazán csoportosítva, de közel a bika-szem jelölt "Alacsony pontosság: jó igazság, rossz pontosság. Egy másik cél szorosan csoportosítva, de messze a bikák-szem jelölt "Alacsony pontosság: rossz igazság, jó pontosság"](./media/azure-ml-customer-churn-scenario/churn-8.png)

*10. ábra: A pontosság és a pontosság közötti kompromisszum*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Pontosság és pontosság a kiemelt döntési famodellhez
A következő diagram a Machine Learning prototípusának a kiemelt döntési fa modellhez való pontozási nyers eredményeit jeleníti meg, amely történetesen a legpontosabb a négy modell közül:  

![Táblázatkódrészlet, amely négy algoritmus pontosságát, pontosságát, visszahívását, F-pontszámát, AUC-ját, átlagos naplóveszteségét és betanítási naplóveszteségét mutatja](./media/azure-ml-customer-churn-scenario/churn-9.png)

*11. ábra: A döntési fa modell jellemzőinek növelése*

## <a name="performance-comparison"></a>Teljesítmény-összehasonlítás
Összehasonlítottuk az adatok pontozott sebességét a Machine Learning Studio (klasszikus) modellekkel és a SAS Enterprise Miner 12.1 asztali kiadásával létrehozott hasonló modellvel.  

Az alábbi táblázat az algoritmusok teljesítményét foglalja össze:  

*1. táblázat. Az algoritmusok általános teljesítménye (pontossága)*

| LR | BT | AP | Svm |
| --- | --- | --- | --- |
| Átlagos modell |A legjobb modell |Alulteljesítő |Átlagos modell |

A Machine Learning Studio (klasszikus) által üzemeltetett modellek 15-25%-kal felülmúlták a SAS-t a végrehajtás sebességében, de a pontosság nagyrészt egyenrangú volt.  

## <a name="discussion-and-recommendations"></a>Vita és ajánlások
A távközlési iparban számos gyakorlat alakult ki a lemorzsolódás elemzésére, többek között:  

* Négy alapvető kategória mutatóinak származtatása:
  * **Entitás (például előfizetés)**. Alapvető információk biztosítása az előfizetésről és/vagy a lemorzsolódás tárgyát képező ügyfélről.
  * **Tevékenység**. Az entitáshoz kapcsolódó összes lehetséges használati információ, például a bejelentkezések száma.
  * **Ügyfélszolgálat .** Adatok begyűjtésére az ügyfélszolgálati naplókból annak jelzésére, hogy az előfizetésnek voltak-e problémái vagy interakciói az ügyfélszolgálattal.
  * **Versenyképes és üzleti adatok**. Szerezzen be minden lehetséges információt az ügyfélről (például lehet, hogy nem érhető el, vagy nehezen nyomon követhető).
* A fontosság használatával megválaszthatja a funkciókat. Ez azt jelenti, hogy a felerősített döntési fa modell mindig ígéretes megközelítés.  

E négy kategória használata azt az illúziót kelti, hogy egy egyszerű *determinisztikus* megközelítés, amely kategóriánként ésszerű tényezők alapján létrehozott indexeken alapul, elegendőa konkretizálja a lemorzsolódás kockázatának kitett ügyfeleket. Sajnos, bár ez a fogalom tűnik elfogadható, ez egy hamis megértést. Ennek az az oka, hogy a lemorzsolódás egy időbeli hatás, és a tényezők hozzájárulnak a lemorzsolódás általában átmeneti államokban. Mi vezet az ügyfél, hogy fontolja meg elhagyja ma lehet más holnap, és ez biztosan más lesz hat hónap múlva. Ezért a *valószínűségi* modell szükséges.  

Ezt a fontos megfigyelést gyakran figyelmen kívül hagyják az üzleti életben, amely általában az elemzés üzleti intelligencia-orientált megközelítését részesíti előnyben, főként azért, mert könnyebb eladni, és elismeri az egyszerű automatizálást.  

Azonban az ígéret az önkiszolgáló elemzés segítségével Machine Learning Studio (klasszikus) az, hogy a négy információkategória, osztályszerint osztályozott vagy osztály, értékes forrássá válik a gépi tanulás a lemorzsolódás.  

Egy másik izgalmas képesség az Azure Machine Learning Studio (klasszikus) az a képesség, hogy egy egyéni modult a dótér előre definiált modulok, amelyek már elérhető. Ez a képesség lényegében lehetőséget teremt a könyvtárak kiválasztására és a vertikális piacok sablonjainak létrehozására. Ez egy fontos különbséget az Azure Machine Learning Studio (klasszikus) a piacon.  

Reméljük, hogy a jövőben is folytathatjuk ezt a témát, különösen a big data-elemzéssel kapcsolatban.
  

## <a name="conclusion"></a>Összegzés
Ez a dokumentum egy általános keretrendszer használatával az ügyféllemorzsolódás gyakori problémájának kezelésére vonatkozó ésszerű megközelítést ismerteti. A modellek pontozásának prototípusát vettük figyelembe, és az Azure Machine Learning Studio (klasszikus) használatával valósítottuk meg. Végül értékeltük a prototípus megoldás pontosságát és teljesítményét a SAS összehasonlítható algoritmusai tekintetében.  

 

## <a name="references"></a>Referencia
[1] Prediktív analitika: Túl az előrejelzéseken, W. McKnight, Information Management, 2011.  

[2] Wikipedia cikk: [Pontosság és pontosság](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Lépésről lépésre adatbányászati útmutató](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Data Marketing: Vegye nek sebeskedjenek hatékonyabban és értéknövelés](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco lemorzsolódás modellsablon](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) az [Azure AI-galériában](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Függelék
![Pillanatkép a lemorzsolódás prototípusának bemutatójáról](./media/azure-ml-customer-churn-scenario/churn-10.png)

*12. ábra: Pillanatkép a lemorzsolódás prototípusáról*
