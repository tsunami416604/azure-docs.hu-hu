---
title: "Ügyfél Kavarog egyidejűleg Machine Learning segítségével elemzése |} Microsoft Docs"
description: "Egy integrált modell elemzésére és a felhasználói forgalom pontozási fejlődő bemutató esettanulmány"
services: machine-learning
documentationcenter: 
author: jeannt
manager: jhubbard
editor: cgronlun
ms.assetid: 1333ffe2-59b8-4f40-9be7-3bf1173fc38d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: jeannt
ms.openlocfilehash: 57044afe946e21d4b3cfa991772e780e59a1710e
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Az ügyfél változásainak elemzése az Azure Machine Learning segítségével
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja a felhasználói forgalom elemzése projekt, Azure Machine Learning segítségével részét képező egy hivatkozás végrehajtása. Ez a cikk arról lesz szó kapcsolódó általános modellek holistically a ipari ügyfél forgalmának kezeléséhez, a probléma megoldására. Is mérjük a modellt a Machine Learning segítségével beépített pontosságát, és további fejlesztési irányban értékeléséhez.  

### <a name="acknowledgements"></a>A nyugtázás
Ehhez a kísérlethez fejlesztette ki és tesztelt Serge Berger, egyszerű adatok tudósok Microsoft és Roger Barga, a Microsoft Azure Machine Learning korábban termék Manager. Az Azure dokumentációs csapattól gratefully elfogadja a saját ismereteit, és Köszönjük őket, hogy ez a dokumentum megosztása.

> [!NOTE]
> Ehhez a kísérlethez használt adatok nincs nyilvánosan elérhető. Példa bemutatja, hogyan hozhat létre a gépi tanulási modell a forgalom elemzése, lásd: [kereskedelmi kavarog folyamatmodell-sablont](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1) a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/)
> 
> 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-problem-of-customer-churn"></a>A probléma az ügyfél os forgalom
A fogyasztó piacon és az összes vállalati ágazatban vállalatok rendelkeznek, forgalom kezelésére. Egyes esetekben adatforgalom túlzott, és hogyan befolyásolja a szabályozási döntések. A hagyományos megoldás, hogy magas-innovációkká alakuljon churners előrejelzése és azok marketingkampányok segítõ szolgáltatáson keresztül vagy különleges felmentésekről alkalmazásával igények kielégítéséhez. Ezek a módszerek iparági iparág és még egy adott felhasználó fürtről egy másik belül (például távközlési) egy iparági eltérőek lehetnek.

A közös tényező, hogy a vállalkozások kell ilyen speciális ügyfél megőrzési erőfeszítések minimalizálása érdekében. Ebből kifolyólag természetes módszer lehet a pontszám forgalom valószínűségét minden ügyfélnek, és oldja meg a felső N néhányat a meglévők közül. Előfordulhat, hogy a felső ügyfelek legnyereségesebb megfelelően. Például az összetettebb forgatókönyveket nyereség függvény alkalmaznak a különleges felmentéssel jelöltek kiválasztása során. Ezeket a szempontokat azonban csak a teljes forgalom kapcsolatos stratégia részét képezik. Vállalatok számára is figyelembe fiók kockázat (és társított kockázattűrése), a szinten, és a beavatkozás és egyértelmű felhasználói szegmentálást költsége.  

## <a name="industry-outlook-and-approaches"></a>Iparági outlook és módszerek
Kifinomult kezelésének forgalmának kezeléséhez egy összetett iparági bejelentkezési. A klasszikus lehet például a távközlési iparági ahol előfizetők ismert, hogy gyakran váltson egy szolgáltató által a másikra. Ez önkéntes forgalom elsődleges szempont. Ezenkívül szolgáltatóktól rendelkezik összegzi a rendszer jelentős Tudásbázis kapcsolatos *kavarog egyidejűleg illesztőprogramok*, amelyeket a tényezőket, amelyek az ügyfelek számára a Váltás meghajtó.

Például kézibeszélő vagy az eszköz választott nem jól ismert vezetőjeként a mobiltelefon üzleti forgalmáról. Ennek eredményeképpen a népszerű házirend kézibeszélő ára subsidize új előfizetők és meglévő ügyfelek frissítéshez teljes díjjal. Hagyományosan ezzel a házirend-szolgáltató egy másik hopping ügyfelek új esetén kedvezményes díjat vezetett. Ez, viszont rendelkezik kéri pontosítsa a stratégiák szolgáltatók.

A kézibeszélő ajánlatok magas illékonyság módszere, amely gyorsan az aktuális kézibeszélő modellek alapuló forgalom modelljei érvénytelenné szempontja. Emellett mobiltelefonok nem csak telekommunikációs eszközöket, olyan is módon utasítások (vegye figyelembe az iPhone). A közösségi előre rendszeres távközlési adatkészletek hatókörén kívül vannak.

Nettó modellezési az eredménye, hogy egy hang házirend meg nem megtervezi a egyszerűen forgalom ismert okait kiküszöbölése révén. Folyamatos modellezési stratégiát, beleértve a klasszikus modellt a számlálása kategorikus változók (például a döntési fák), valójában **kötelező**.

Big data készletek használ az ügyfelek, a szervezetek big data-elemzések (ebben az esetben, a forgalom észlelési big Data típusú adatok alapján), a probléma hatékony megközelítés végzik. További információk a forgalom ETL szakasz ajánlások a big Data típusú adatok megközelítésének találja.  

## <a name="methodology-to-model-customer-churn"></a>A modell felhasználói forgalom módszerét.
Gyakori problémák megoldásához folyamat felhasználói forgalom megoldására írja le a számok 1-3:  

1. A kockázat modell lehetővé teszi, hogy figyelembe kell venni, hogyan érinti az műveletek a valószínűség és a kockázatot jelent.
2. Egy beavatkozás modell lehetővé teszi, hogy figyelembe kell venni, hogyan beavatkozás szintjét hatással lehet a forgalom mennyiségétől és a felhasználói valószínűségét élettartamértékénél (CLV).
3. Az elemzés adatmodelljeinek a minőségi elemzés, amely képes biztosítani az optimális ajánlat ügyfélszegmensek megcélzó proaktív marketingkampányt eszkalálása.  

![][1]

Ez a megközelítés előre kinézetű forgalom kezelheti a legjobb módszer, de összetettségét és az ismét: több modellre archetype és nyomkövetési függőségek modellek között van. A modellek közötti interakció is lehet beágyazott, az alábbi ábrán látható módon:  

![][2]

*4. ábra: Több modellre archetype egyesített*  

A modellek közötti interakció kulcs esetén a körű megközelítés kézbesíthet ügyfél megőrzési dolgozunk. Egyes modellek feltétlenül csökkenti időbeli; az architektúra ezért egy implicit hurok (az adatok adatbányászati SZÍNELOSZLÁS-DM szabvány által beállított archetype hasonló [***3***]).  

Az általános kockázati-döntési-marketing Szegmentálás/felbontás ellen ciklus még mindig egy általánosított struktúra, és számos üzleti problémák alkalmazható. Forgalom elemzése egyszerűen erős jellemző a csoportját, problémák oka azonban minden tulajdonságokat bonyolult üzleti probléma, amely nem engedélyezi a egyszerűsített prediktív megoldás. A közösségi szempontok kavarog modern megközelítésének nem különösen emel ki a módszert, de a közösségi szempontok a modellezési archetype vannak ágyazva, azok bármely modellben lenne.  

Itt érdekes kiegészítése big data-elemzések. Napjaink telekommunikációs és a kereskedelmi cégek vásárlóiknak részletes adatainak gyűjtését, és azt is könnyen várható, hogy több modellre kapcsolat szükség lesz egy közös trend, adott feltörekvő például az eszközök internetes hálózatát trendeket és a széles körű az eszközök, amelyek lehetővé teszik az üzleti rétege intelligens megoldást bevezetését.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>A Machine Learning Studióban a modellezési archetype megvalósítása
Megadott csak ismertetett problémát, mi az a legjobb módszer egy integrált modellezési és megközelítés pontozási megvalósításához? Ebben a szakaszban a bemutatjuk, hogyan azt valósítható meg ezzel Azure Machine Learning Studio használatával.  

A modell több megközelítés az kell, a forgalom egy globális archetype tervezése során. Még a pontozási (prediktív) része a megközelítést több modellre kell lennie.  

Az alábbi ábrán látható a prototípus létrehozott, a Machine Learning Studióban forgalmának kezeléséhez előre négy pontozási algoritmus alkalmazó. A több modellre megközelítéssel oka, hogy nem csak egy ensemble osztályozó fokozni, de is túlzott méretezés elleni és javítása előíró szolgáltatás kiválasztása létrehozásához.  

![][3]

*5. ábra: A forgalom modellezési megközelítés az Prototípuson*  

A következő szakaszokban további információt a modellt, amely azt a Machine Learning Studio használatával megvalósított pontozási típuséval.  

### <a name="data-selection-and-preparation"></a>Kijelölt adatok és előkészítése
Az adatok segítségével hozhatók létre a modellek és pontszám ügyfelek kapott egy CRM függőleges megoldás, az adatok rejtjelezett felhasználói adatok védelme. Az adatok az Egyesült Államokban 8000 előfizetések információkat tartalmaz, és három forrásának össze: a kiépítési adatokat (előfizetés metaadatai), a tevékenységek adatai (a rendszer használati) és a támogatási ügyféladatok. Az adatok nem tartalmaz semmilyen üzleti kapcsolatos adatokat az ügyfelek; például az tartalmazza hűség metaadatok vagy jóváírás pontszámait.  

Az egyszerűség kedvéért ETL és a folyamatok tisztításokat adatok hatókörén kívül mivel feltételezzük, hogy adatok előkészítése már rendelkezik-e már végzett máshol.   

Szolgáltatás kiválasztása a modellezési alapján előzetes többszörösére pontozási kiszámítására, a folyamat által használt a véletlenszerű erdő modul szereplő vonatkozóan. A Machine Learning Studióban végrehajtásához igazolnia számítja ki a a középérték, a középérték és a tartományok reprezentatív szolgáltatások. Például azt a minőségi adatok, például a felhasználói tevékenység minimális és maximális értékek összesítések hozzá.    

Azt is rögzített historikus adatokat a legutóbbi hat hónapban. Elemeztük az adatok egy évig, és azt létrehozni, hogy akkor is, ha nincsenek statisztikailag jelentős trendeket, a forgalom gyakorolt hatása nagy mértékben csökken hat hónap után.  

A legfontosabb pont, hogy a teljes folyamatot, beleértve az ETL, szolgáltatás kiválasztása, és modellezési a Machine Learning Studióban, az adatforrásoknak a Microsoft Azure használatával lett megvalósítva.   

Az alábbi ábrák bemutatják a használt adatokat.  

![][4]

*6. ábra: Cikkből-adatforrás (rejtjelezett)*  

![][5]

*7. ábra: Szolgáltatások kinyert adatforrás*
 

> Vegye figyelembe, hogy ezek az adatok személyes, és ezért a modell és az adatok nem osztható meg.
> Azonban egy hasonló modell nyilvánosan elérhető adatok használatával, lásd: a minta a kísérletezhet a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/): [Telco ügyfél Kavarog](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Hogyan implementálható a forgalom elemzési modellek Cortana Intelligence Suite használatával kapcsolatos további tudnivalókért is javasoljuk [Ez a videó](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) által kiemelt Programvezető hétköznapon Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>A prototípus használt algoritmusok
A következő négy gépi tanulási algoritmusok hozhat létre a prototípus (nincs Testreszabás) használtuk:  

1. Logisztikai regresszió (LR)
2. Súlyozott döntési fa (BT)
3. Átlagolt perceptron (AP)
4. Támogatja a vektoros machine (SVM)  

Az alábbi ábrán látható egy részét a kísérlet a tervezési felülethez, amely azt a sorrendet, amelyben a modellek hozta létre:  

![][6]  

*8. ábra: Modellek létrehozása a Machine Learning Studióban*  

### <a name="scoring-methods"></a>A pontozási módszerek
A négy modellek címkézett képzési dataset használatával pontozni azt.  

A pontozási adatkészlet SAS vállalati bányászati 12 asztali verzióját használatával készültek összehasonlítható modell is elküldése megtörtént. A SAS-modell és az összes négy Machine Learning Studio modell pontosságát mérni azt.  

## <a name="results"></a>Results (Eredmények)
Ez a szakasz azt jelent-e az eredményekről a modellek pontozási adatkészlet alapján pontosságát kapcsolatos.  

### <a name="accuracy-and-precision-of-scoring"></a>És pontozási pontossága
Az Azure Machine Learning végrehajtása általában SAS mögött van, pontossággal körülbelül 10 – 15 % (terület a görbe vagy AUC).  

Azonban a legfontosabb mérőszám a forgalom az-e a téves besorolás gyakoriság: Ez azt jelenti, hogy az a legfontosabb N churners, mint az osztályozó által előre jelzett, amely egyiket ténylegesen volt **nem** kavarog egyidejűleg, és még a különleges kezelést kapott? Az alábbi ábrán ez téves besorolás sebességét, a modellek hasonlítja össze:  

![][7]

*9. ábra: Passau prototípus területen görbévé*

### <a name="using-auc-to-compare-results"></a>Eredmények összehasonlítását AUC használatával
Terület a görbe (AUC) a globális mérték jelölő metrika *separability* vonatkozó eredmények pozitív és negatív feltöltések disztribúciók között. A hagyományos fogadó operátor jellemző (ROC) diagramhoz hasonló, de egy fontos különbséggel, hogy a AUC metrika nem szükséges hozzá válassza ki a küszöbérték. Ehelyett összefoglalja eredményeit keresztül **összes** lehetőségeket. Ezzel szemben a hagyományos ROC graph a függőleges tengely és a téves pozitív alapján a vízszintes tengelyre pozitív arányát jeleníti meg, és a besorolási küszöbérték függ.   

AUC szolgál érdemes intézkedésként különböző algoritmusokat (vagy különböző rendszerek esetén), mivel így modellek segítségével AUC értékeik összehasonlítani. Ez az a népszerű megközelítés például meteorológia és biosciences iparágakban. Ebből kifolyólag AUC jelöli egy népszerű eszköz osztályozó teljesítmény értékeléséhez.  

### <a name="comparing-misclassification-rates"></a>Téves besorolás díjszabás összehasonlítása
Körülbelül 8000 előfizetések CRM adatok felhasználásával a téves besorolás szerint rangsorolja a szóban forgó adatkészlethez azt képest.  

* A biztonsági Társítások téves besorolás 10 – 15 % volt.
* A Machine Learning Studio téves besorolás az első 200-300 churners 15-20 %-os volt.  

A távközlési iparág fontos csak ezek az ügyfelek a kavarog egyidejűleg segítõ szolgáltatásként vagy más speciális kezelés felajánlásával őket a legmagasabb kockázattal rendelkező megoldására. Ebben a tekintetben a Machine Learning Studio megvalósítása a SAS-modell hatékonysága eléri eredmények éri el.  

Hasonlóképpen pontosság oka fontosabb, mint a pontossága dolgozunk leginkább megfelelő zárolásának lehetséges churners iránt érdeklődik.  

A következő ábra a Wikipedia a kapcsolatot a színes, könnyen érthető grafikus szemlélteti:  

![][8]

*10. ábra: Kompromisszumot pontosság és a pontosság között*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Súlyozott döntési fa modell pontosságát, és a pontosság eredmények
A következő diagram nyers használata a Machine Learning prototípus súlyozott döntési fa modell, amely éppen a legpontosabb a négy modellek között pontozási eredményeinek jeleníti meg:  

![][9]

*11. ábra: Súlyozott döntési fa modell jellemzői*

## <a name="performance-comparison"></a>Teljesítmény összehasonlítása
A sebesség, amellyel adatokat lett pontozza a mennyiségeket a Machine Learning Studio-modellek és a SAS vállalati bányászati 12.1 asztali kiadása használatával létrehozott összehasonlítható modell használatával összehasonlítva azt.  

A következő táblázat összefoglalja a teljesítmény algoritmusok:  

*1. táblázat. Általános teljesítménye (pontosság) algoritmusok*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Átlagos modell |A legjobb modell |Underperforming |Átlagos modell |

A modellek üzemeltetett a végrehajtási, de pontossága sebességének 15-25 %-kal outperformed SAS lett nagymértékben par a Machine Learning Studióban.  

## <a name="discussion-and-recommendations"></a>Az ismertető és javaslatok
A távközlési iparágban több eljárások rendelkezik kiderült, elemezheti a forgalom, beleértve:  

* Származtatni a használatmérés négy alapvető kategóriák:
  * **Entitás (például egy előfizetés)**. Az előfizetés és/vagy az ügyfél, amely a forgalom tárgya alapvető információkat kiépítéséhez.
  * **Tevékenység**. Szerezze be a kapcsolódó entitás, például a bejelentkezések száma az összes lehetséges használati adatait.
  * **Ügyfél-támogatási**. Nem kér be adatokat annak jelzésére, hogy az előfizetés kellett problémák vagy ügyfélszolgálata interakció naplókból a felhasználói támogatási információkat.
  * **Versenyképes és üzleti adatokat**. Minden lehetséges kapcsolatos információkhoz az ügyfél (például lehet nem érhető el vagy nehéz nyomon követése).
* Meghajtó szolgáltatásválasztást fontosak használja. Ez azt jelenti, hogy a súlyozott döntési fa modell mindig a ígéret megközelítést.  

Négy kategóriába használatát hoz létre a érhet, amely egy egyszerű *determinisztikus* módszert használja, kategóriánként, nagy valószínűséggel tényezők alakult indexek alapján vevők forgalom kockázatának azonosítására elegendőnek kell lennie. Sajnos bár ez formátumban egyértelmű úgy tűnik, továbbra is egy hamis ismertetése. Az oka, hogy forgalmának kezeléséhez egy ideiglenes hatást és a tényezők kavarog egyidejűleg általában átmeneti állapotban van. Mit kell figyelembe venni, így ma ügyfél vezet holnap lehet különböző, és biztosan lesz különböző hat hónap múlva. Ezért egy *probabilisztikus* modell áttelepítésére.  

Ez fontos megfigyelési gyakran kihagyott üzleti, amely általában inkább a business intelligence megközelítéssel elemzés, főleg, mivel már egy egyszerűbb eladásra és egyszerű automatizálási elismeri.  

A felhőalkalmazások nyújtotta önkiszolgáló Analytics a Machine Learning Studio használatával azonban, hogy a négy található információs kategóriák között, részleg vagy osztály által osztályozott számítógép biztonságával kapcsolatos forgalom értékes forrássá válik.  

Az Azure Machine Learning hamarosan egy másik izgalmas képessége azt a képességet egy egyéni modul hozzáadása a tárházban az előre definiált modulok által már biztosított. Ez a funkció tulajdonképpen lehetőséget válassza ki a szalagtárak és az függőleges piacok sablonok létrehozása a hoz létre. Az Azure gépi tanulás fontos különbséget piacon.  

Reméljük, ez a témakör a jövőben folytatja big data elemzés különösen kapcsolódik.
  

## <a name="conclusion"></a>Összegzés
A dokumentum ismerteti egy ésszerű megközelítése problémájának közös ügyfél forgalmának kezeléséhez egy általános keretrendszer használatával. Azt a prototípus modellek pontozó minősül, és valósítják meg az Azure Machine Learning segítségével. Végül azt vizsgálja, a pontosság és a teljesítmény tekintetében a SAS összehasonlítható algoritmusok prototípus megoldás.  

 

## <a name="references"></a>Referencia
[1] prediktív elemzési: előrejelzéseket, Nyugat McKnight információkezelés p.18-20 2011. júliusi augusztusi felett.  

[2] Wikipedia cikk: [pontosság és a pontosság](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [SZÍNELOSZLÁS-DM 1.0: részletes adatok adatbányászati útmutató](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [big Data típusú adatok Marketing: hatékonyabban végezhetnek az ügyfelek és a meghajtó érték](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Telco kavarog folyamatmodell-sablont](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) a [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) 
 

## <a name="appendix"></a>Függelék:
![][10]

*12. ábra: Pillanatképe forgalom prototípuson bemutató*

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
