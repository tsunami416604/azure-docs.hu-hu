---
title: Ügyfél-adatforgalom elemzése
titleSuffix: ML Studio (classic) - Azure
description: Esettanulmány olyan integrált modell kidolgozásához, amely az ügyfelek adatforgalmának elemzéséhez és pontozásához Azure Machine Learning Studio (klasszikus) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 903e3f3dcbcc72289fc82ec59dec0305b6adbc17
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150918"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Ügyfél-adatforgalom elemzése Azure Machine Learning Studio (klasszikus) használatával
## <a name="overview"></a>Áttekintés
Ez a cikk a Azure Machine Learning Studio (klasszikus) használatával létrehozott ügyfél-adatforgalom-elemzési projekt hivatkozási megvalósítását ismerteti. Ebben a cikkben bemutatjuk, azok alkalmazásfüggőségeit az iparági vásárlók lemorzsolódási problémájának megoldására szolgáló kapcsolódó általános modellekről. Azt is mérheti a Machine Learning segítségével létrehozott modellek pontossága, és felmérheti a további fejlesztésére vonatkozó utasításokat.  

### <a name="acknowledgements"></a>Nyugták
Ezt a kísérletet a Serge Berger, a Microsoft legfontosabb adattudósa és Roger Barga, korábban a Microsoft Azure Machine Learning Studio (klasszikus) termék-kezelője fejlesztette ki és tesztelte. Az Azure dokumentációs csapata köszönetet mondani a segítségükért összpontosíthassák nyugtázza, és Köszönjük Ez a tanulmány megosztása.

> [!NOTE]
> Ehhez a kísérlethez használt adatokat nem nyilvánosan érhető. A következő témakörből megtudhatja, hogyan hozhat létre egy gépi tanulási modellt az adatváltozások elemzéséhez: a kiskereskedelmi adatváltozási [modell sablonja](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1) [Azure AI Gallery](https://gallery.azure.ai/)
> 
> 



## <a name="the-problem-of-customer-churn"></a>A probléma a vásárlók lemorzsolódásának
A fogyasztó piacon elérhető, és az összes vállalati ágazatban vállalkozások kell forgalom kezelésére. Néha túlzott és befolyásolja a házirendet érintő döntések. A hagyományos megoldás, ha nagy-upsell churners előre jelezni, és oldja meg saját igényeinek megfelelő marketingkampányokba, recepciószolgálata szolgáltatáson keresztül vagy a speciális felmentésekről alkalmazásával. Ezek a módszerek eltérőek lehetnek industry iparági. Még vésve egy adott fogyasztói fürtről a másikra egy iparági (például távközlési) belül.

A gyakori tényezővel az, hogy vállalkozások minimalizálása érdekében ezeket külön ügyfél felhasználómegtartása kell-e. Így természetes módszer lehet a pontszám minden ügyfél a lemorzsolódás valószínűségét, és oldja meg a felső N azokat. Előfordulhat, hogy a legjelentősebb ügyfeleket legjövedelmezőbb azokat. Például kifinomultabb forgatókönyvekben nyereség függvény alkalmazzák a speciális felmentéssel a deduplikációra kijelölt során. Ezeket a szempontokat azonban csak a teljes forgalom kapcsolatos stratégia részét képezik. Vállalkozások is figyelembe kell fiók kockázati (és a kapcsolódó kockázattűrése), a szint és a beavatkozás és egyértelmű ügyfélcsoportok költségét.  

## <a name="industry-outlook-and-approaches"></a>Iparági az outlook és módszerek
Kifinomult kezelését a lemorzsolódás érett iparág bejelentkezési. A klasszikus például, a távközlési iparágban, ahol előfizetők ismert, hogy gyakran váltson egy szolgáltatói egy másikba. Önkéntes kihagyásával az elsődleges szempont. Emellett a szolgáltatók jelentős ismereteket gyűjtöttek az adatforgalom- *illesztőprogramokról*, amelyek az ügyfelek által a váltáshoz szükséges tényezők.

Például kézibeszélő vagy az eszköz el egy jól ismert illesztőprogram a mobiltelefon üzleti adatforgalom. Ennek eredményeképpen egy népszerű szabályzatot, hogy kézibeszélő árát subsidize új előfizetők számára, és a frissítéshez a meglévő ügyfelek számára a teljes díj díja szerint számítjuk fel. Ez a szabályzat hagyományosan ügyfelek egy szolgáltató a másikra segítségével tehetjük meg új kedvezményes díjat vezetett. Ennek érdekében, a rendszer felkéri szolgáltatók számára, hogy stratégiákkal pontosításához.

Magas változékonyságán kézibeszélő ajánlatok a fontos, hogy gyorsan érvényteleníti modellek adatforgalom aktuális kézibeszélő modellek alapuló tényező. Ezenkívül mobiltelefonokról nem csak a távközlési eszközöket, azok is el utasítások (vegye figyelembe az iPhone-t). A közösségi előrejelzőket rendszeres távközlési adatkészletek hatókörén kívül vannak.

Az eredmény a modellezési, hogy egy hang házirend nem lényegesen egyszerűen elkerülve a lemorzsolódás ismert okait. Valójában a folyamatos modellezési stratégia, beleértve a kategorikus változókat (például döntési fákat) számszerűsítő klasszikus modelleket, **kötelező megadni**.

Big data jellegű adatkészletek használatával az ügyfelek, a szervezetek big data-elemzés (különösen, a big data-alapú forgalom észlelési), a probléma hatékony megközelítés végzik. További információk a big Data típusú adatok megközelítés ETL szakasz ajánlások lemorzsolódási problémájának találja.  

## <a name="methodology-to-model-customer-churn"></a>A modell vásárlók lemorzsolódásának módszertan
Gyakori problémák megoldásához folyamat megoldani a vásárlók lemorzsolódásának megfelelőként jelenik a számok 1-3:  

1. Kockázati modell lehetővé teszi, hogy hogyan befolyásolja az műveletek valószínűségét, és a kockázati.
2. Egy beavatkozás modellje lehetővé teszi, fontolja meg, hogyan beavatkozás szintjét a valószínűsége annak, és a vásárlói mennyisége hatással lehetnek a csoportélettartam értékének (CLV).
3. Ez az elemzés adatmodelljeinek egy minőségi elemző, amely a célozza meg, hogy az optimális ajánlat ügyfélszegmensek proaktív marketingkampány eszkalálása.  

![Ábra, amely bemutatja, hogy a kockázati tolerancia és a döntési modellek hogyan eredményeznek gyakorlatban hasznosítható elemzéseket](./media/azure-ml-customer-churn-scenario/churn-1.png)

A továbbítás akik megközelítés kezelni az adatforgalom a legjobb módszer, de együttműködik a összetettséget: fejlesszen többmodelles archetype és nyomkövetési függőségek modellek között kell, hogy. A modellek közötti interakció is technológiába ágyazott, az alábbi ábrán látható módon:  

![Adatváltozási modell interakciós diagramja](./media/azure-ml-customer-churn-scenario/churn-2.png)

*4. ábra: egységes multi-Model archetípus*  

A modellek közötti interakció az kulcs, ha egy átfogó megközelítés továbbítására az ügyfélmegtartást. Minden modell szükségszerűen csökkenti az idő múlását; Ezért az architektúra egy implicit hurok (hasonlóan a ROPOGÓs-DM adatbányászati standard által beállított archetípushoz, [***3***]).  

A teljes ciklus a kockázat-döntési-marketing Szegmentálás/idősorfelbontási még egy általánosított szerkezete, és számos üzleti problémára vonatkozik. Lemorzsolódásának elemzése egyszerűen a csoport problémák erős képviselője azért azonban egy összetett üzleti probléma, amely nem engedélyezi a prediktív megoldás egyszerűsített képességekre vonatkozó. A vásárlói lemorzsolódás modern megközelítését közösségi aspektusait nem különösen kiemelt a megközelítéssel, de a közösségi szempontok a a modellezési archetype vannak ágyazva, azok bármely modellben lenne.  

Egy érdekes emellett a big data-elemzés. A mai távközlési és kereskedelmi vállalkozások az ügyfeleknek az alábbi adatok gyűjtése, és azt is könnyen bizonyulhat, hogy többmodelles kapcsolat szükség lesz egy gyakori trend, adott jelentkező trendeket, például az eszközök internetes hálózata és a széles körben használt az eszközök, amelyek lehetővé teszik az üzleti több rétegén intelligens megoldások bevezetését.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>A modellezési archetípus implementálása Machine Learning Studio (klasszikus)
Az ismertetett probléma miatt mi a legjobb módszer egy integrált modellezési és pontozási módszer megvalósítására? Ebben a szakaszban bemutatjuk, hogyan valósult meg ez a Azure Machine Learning Studio (klasszikus) használatával.  

A több modell megközelítés kell, a forgalom egy globális archetype tervezésekor. Még a relevanciaprofil (prediktív) részét megközelítés többmodelles kell lennie.  

Az alábbi ábrán az általunk létrehozott prototípus látható, amely négy pontozási algoritmust alkalmaz Machine Learning Studio (klasszikus) a forgalom előrejelzésére. Többmodelles módszerével oka nem csak egy nagyobb pontosság, de is túlzott méretezés ellen védelmet biztosító és javítása előírásoknak megfelelő szolgáltatás kiválasztása ensemble osztályozó létrehozása céljából.  

![Egy összetett Studio (klasszikus) munkaterületet ábrázoló képernyőkép, amely számos összekapcsolt modullal rendelkezik](./media/azure-ml-customer-churn-scenario/churn-3.png)

*5. ábra: az adatváltozások modellezési megközelítésének prototípusa*  

A következő szakaszokban további részleteket talál a Machine Learning Studio (klasszikus) használatával megvalósított prototípus-pontozási modellről.  

### <a name="data-selection-and-preparation"></a>Kijelölt adatok és -előkészítés
A modellek létrehozásához használt adatokat, és pontszám ügyfelek CRM-függőleges megoldásról, lett lekérve az adatokkal a rejtjelezett felhasználói adatok védelme. Az adatok az Egyesült Államokban 8000 előfizetésekkel kapcsolatos információkat tartalmaz, és amely ötvözi az három forrásának: provisioning (előfizetés metaadatai) adatok, tevékenység adatokat (használat a rendszer) és ügyfél-támogatási adatok. Az adatok nem tartalmazzák az ügyfelekkel kapcsolatos üzleti adatokat; például nem tartalmazza a lojalitási metaadatokat vagy a jóváírási pontszámokat.  

Az egyszerűség kedvéért ETL és folyamatok adattisztító hatókörén kívül mivel feltételezzük, hogy adat-előkészítési már rendelkezik-e már végzett máshol.

Szolgáltatás kiválasztása a modellezési előzetes többszörösére pontozási előrejelzőket, szerepel a folyamat a véletlenszerű erdő modult használó készlete alapul. Machine Learning Studio (klasszikus) megvalósításához a reprezentatív funkciók középértékét, középértékét és tartományait számítjuk ki. Ha például hozzáadtunk összesítések a mennyiségi adatok, például a felhasználói tevékenység minimális és maximális értékeket.

A legutóbbi hat hónapig historikus információkat is rögzített. Adatok egy évig elemeztük, és azt létre, hogy akkor is, ha voltak statisztikailag trendeket, lemorzsolódási hatása jelentősen csökken hat hónapig érvényesek.  

A legfontosabb szempont, hogy a teljes folyamat, beleértve az ETL-t, a szolgáltatások kijelölését és a modellezést Machine Learning Studio (klasszikus), Microsoft Azure adatforrások használatával implementálták.   

Az alábbi ábrák bemutatják a használt adatok.  

![A nyers értékekkel használt adatok mintáját ábrázoló képernyőfelvétel](./media/azure-ml-customer-churn-scenario/churn-4.png)

*6. ábra: adatforrás kivonata (eltorzítva)*  

![Az adatforrásból kinyert statisztikai funkciókat ábrázoló képernyőkép](./media/azure-ml-customer-churn-scenario/churn-5.png)

*7. ábra: az adatforrásból kinyert funkciók*
 

> Vegye figyelembe, hogy ezek az adatok személyes, és ezért a modell és az adatok nem oszthatók meg.
> Ahhoz azonban, hogy egy hasonló modell nyilvánosan elérhető adatforgalomot használjon, tekintse meg ezt a minta kísérletet a [Azure AI Gallery](https://gallery.azure.ai/): [távközlési ügyfelek](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383)adatforgalmáról.
> 
> Ha többet szeretne megtudni arról, hogyan valósítható meg a forgalom-elemzési modell a Cortana Intelligence Suite használatával, ajánljuk [ezt a videót](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) a Senior program Manager pici Hyong tok használatával is. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>A prototípust használt algoritmusok
A következő négy gépi tanulási algoritmusok segítségével hozhat létre, a prototípus (nincs testreszabása):  

1. Logisztikai regressziós (LR)
2. Gyorsított döntési fa (BT)
3. Átlagolt perceptron (AP)
4. Támogató vektorgép (SVM)  

A következő ábra szemlélteti a kísérlet tervezőfelületére, amely azt jelzi, hogy a feladatütemezés, amelyben a modellek létrehozott egy része:  

![A Studio-kísérlet vászonjának kis szakaszának képernyőképe](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*8. ábra: modellek létrehozása Machine Learning Studio (klasszikus)*  

### <a name="scoring-methods"></a>Pontozó módszerek
A négy modell címkézett betanítási adatkészletet használatával pontozását azt.  

Azt is be a pontozási adatkészletet, egy SAS vállalati Miner 12 asztali kiadásának használatával létrehozott összehasonlítható modell. A SAS-modell pontosságát és mind a négy Machine Learning Studio (klasszikus) modellt mértük.  

## <a name="results"></a>Results (Eredmények)
Ebben a szakaszban azt jelenleg a kapcsolatos a pontozási adatkészleten alapuló modell pontossága megállapításokat.  

### <a name="accuracy-and-precision-of-scoring"></a>És pontozás pontossága
Általánosságban elmondható, hogy a Azure Machine Learning Studio (klasszikus) implementációja a 10-15% (a görbe vagy a AUC alatti terület) pontossága alatt áll az SAS mögött.  

Azonban a forgalom legjelentősebb mérőszáma a téves besorolási arány: vagyis az osztályozó által előre jelzett legfontosabb N-adatforgalom, amelyek valójában **nem** voltak forgalomban, és különleges kezelést is kapott? A következő diagram az összes modellt a téves besorolás aránya hasonlítja össze:  

![4 algoritmus teljesítményét összehasonlító görbe gráf alatti terület](./media/azure-ml-customer-churn-scenario/churn-7.png)

*9. ábra: Passau prototípus területe a görbe alatt*

### <a name="using-auc-to-compare-results"></a>Az eredmények összehasonlítása AUC használatával
A görbe (AUC) alatti terület egy metrika, amely a pozitív és negatív populációk eredményeinek eloszlása közötti *separability* globális mértékét jelöli. A hagyományos fogadó operátor jellemző (ROC) gráfhoz hasonló, de egy fontos különbség az, hogy a AUC metrika nem kell választani egy küszöbértéket. Ehelyett összefoglalja az eredményeket az **összes** lehetséges lehetőség közül. Ezzel szemben a hagyományos ROC-diagramon látható a riasztási aránnyal a függőleges tengely és a vízszintes tengelyről téves riasztási aránnyal, és a besorolási küszöbérték változó.   

A AUC a különböző algoritmusok (vagy különböző rendszerek) esetében érdemes használni, mert lehetővé teszi a modellek összehasonlítását a AUC értékeivel. Ez az iparágban például meteorológia és biosciences egy népszerű megközelítés. Ebből kifolyólag AUC jelöli egy népszerű eszköz osztályozó teljesítményének értékeléséhez.  

### <a name="comparing-misclassification-rates"></a>Téves besorolás díjak összehasonlítása
Körülbelül 8000 előfizetések CRM-adatok használatával az adott adatkészlet téves besorolás fel azt képest.  

* A SAS téves besorolás sebesség 10 – 15 % volt.
* A Machine Learning Studio (klasszikus) téves besorolási arány 15-20% volt a Top 200-300-adatforgalomhoz.  

A távközlési iparágban fontos csak a vásárlói lemorzsolódás recepciószolgálata szolgáltatásként vagy egyéb speciális kezelés felajánlásával őket a legmagasabb kockázattal rendelkező ügyfelek cím. Ebben a tekintetben a Machine Learning Studio (klasszikus) implementáció az SAS-modellel egyenrangú eredményeket hoz létre.  

Hasonlóképpen pontossága fontosabb, mint a pontossága, mivel ez többnyire megfelelő Írisz lehetséges churners iránt.  

Az alábbi ábrán a Wikipedia a kapcsolatot a színes, könnyen érthető kép ábrázolja:  

![Két cél. Az egyik cél, hogy a találatok lazán vannak csoportosítva, de közel vannak a "kis pontosság: jó igaz" értékhez, a gyenge pontossághoz. Egy másik cél szorosan csoportosítva van, de távol van a "kis pontosságú: gyenge megbízhatóság", jó pontossággal jelölt Bulls-szemtől.](./media/azure-ml-customer-churn-scenario/churn-8.png)

*10. ábra: a pontosság és a pontosság közötti kompromisszum*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Gyorsított döntési fa modell pontosságát és a pontosság eredményei
Az alábbi ábra a pontozás a Machine Learning prototípus használata a gyorsított döntési fa modell, amely a négy modellek között a legpontosabb történetesen nyers eredményét jeleníti meg:  

![A pontosságot, a pontosságot, a visszahívás, az F-score, a AUC, az átlagos naplók elvesztését és a betanítási napló elvesztését ábrázoló táblázat négy algoritmus esetén](./media/azure-ml-customer-churn-scenario/churn-9.png)

*11. ábra: a megerősítő döntési fa modelljének jellemzői*

## <a name="performance-comparison"></a>Teljesítmény összehasonlítása
Összehasonlítjuk az adatgyűjtési sebességet a Machine Learning Studio (klasszikus) modellek és egy hasonló modell használatával, amely az SAS Enterprise Miner 12,1 asztali kiadásával lett létrehozva.  

A következő táblázat összefoglalja az algoritmusok teljesítményének:  

*1. táblázat. Az algoritmusok általános teljesítménye (pontossága)*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Átlagos modell |A legjobb modellt |Az alulteljesítő |Átlagos modell |

A Machine Learning Studio (klasszikus) által szolgáltatott modellek a végrehajtás gyorsasága érdekében 15-25%-kal, de a pontosság nagyjából par volt.  

## <a name="discussion-and-recommendations"></a>Hozzászólás és javaslatok
A távközlési iparágban több eljárások kiderült rendelkezik, lemorzsolódásának elemzése a többek között:  

* Származtasson négy alapvető kategória metrikái:
  * **Entitás (például egy előfizetés)** . Alapvető információkat szeretne az előfizetés és/vagy a vásárlói lemorzsolódás tárgyát képező kiépítése.
  * **Tevékenység** – Szerezze be a kapcsolódó entitás, például a bejelentkezések száma az összes lehetséges használati információkat.
  * **Ügyfélszolgálati támogatás**. Gyűjtsön információkat az ügyfél-támogatási naplók jelzi, hogy az előfizetés volt-e a problémákat és az ügyfél-támogatási interakciók.
  * **Versenyképes és üzleti adatszolgáltatások**. Az ügyfél lehetséges minden olyan információt (például lehet érhető el vagy visszakövetését, hogy).
* Meghajtó szolgáltatás kiválasztása fontosak használja. Ez azt jelenti, hogy a gyorsított döntési fa modell mindig a ígéret megközelítést.  

Ennek a négy kategóriának a használata azt az illúziót hozza létre, hogy egy egyszerű *determinisztikus* megközelítés a kategóriánkénti ésszerű tényezők alapján Sajnos bár ebben a formátumban egyértelmű úgy tűnik, egy hamis ismertetése. Az oka, hogy adatváltozás historikus érvénybe, és a vásárlói lemorzsolódás hozzájáruló tényezők rendszerint átmeneti állapotok. Mit kell figyelembe venni, így még ma ügyfél érdeklődők holnap lehet különböző, és természetesen lesz különböző hat hónap múlva. Ezért szükség van egy *valószínűségi* modellre.  

Ez fontos megfigyelési gyakran üzleti, amely általában egy üzleti intelligencia megközelítéssel szeretnék analytics van kihagyott, leginkább, mert egy egyszerűbb értékesítésére, és könnyen érthető megjegyzésblokkok írására automation elismeri.  

Az önkiszolgáló elemzések Machine Learning Studio (klasszikus) használatával történő használatának ígérete azonban az, hogy a négy, osztással vagy részleggel osztályozott információ értékes forrást jelent a gépi tanulással kapcsolatban.  

Azure Machine Learning Studio (klasszikus) egy másik izgalmas funkciója, amely lehetővé teszi egyéni modul hozzáadását a már elérhető előre definiált modulok tárházához. Ez a funkció alapvetően hoz lehetőséget válassza ki a kódtárakat, és vertikális piacok sablonok létrehozásához. A Azure Machine Learning Studio (klasszikus) fontos megkülönböztetője a piacon.  

Reméljük, hogy továbbra is a jövőben ez a témakör különösen kapcsolatos big data-elemzés.
  

## <a name="conclusion"></a>Összegzés
Ez a tanulmány azt ismerteti, hogy észszerű megközelítése problémájának közös vásárlók lemorzsolódásának általános keretrendszer használatával. A pontozási modellek prototípusát tekintették, és a Azure Machine Learning Studio (klasszikus) használatával Implementáljuk. Végül azt adatokon, a pontosság és a teljesítmény, a prototípus-megoldás összehasonlítható algoritmusok a biztonsági Társítások kapcsolatban.  

 

## <a name="references"></a>Referencia
[1] prediktív elemzés: az előrejelzéseken felül a W. McKnight, az Information Management, a július/August 2011, a p. 18 – 20.  

[2] wikipedia-cikk: [pontosság és pontosság](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [ropogós-DM 1,0: részletes adatbányászati útmutató](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Value marketing: az ügyfelek hatékonyabb és a meghajtó értéke](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] a távközlési adatforgalom [modell-sablonja](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5) [Azure AI Gallery](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>Függelék
![Bemutató pillanatképe a változási prototípusról](./media/azure-ml-customer-churn-scenario/churn-10.png)

*12. ábra: bemutató pillanatképe a változási prototípusról*
