---
title: Használja a mintaként használható adathalmazt a Machine Learning Studióban |} Microsoft Docs
description: A Machine Learning Studio szereplő mintákat a adatkészletekből leírását. A kísérletek ezek mintaként használható adathalmazt is használhatja.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 03a0b844-e8a7-4896-996f-d3c7a0db7a50
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.openlocfilehash: 63f079f6c8e584ff3a89503104060daf056763ed
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836300"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Az Azure Machine Learning Studió mintaadatkészleteinek használata
[top]: #machine-learning-sample-datasets

Amikor az Azure Machine Learning hoz létre egy új munkaterületet, alapértelmezés szerint számos mintaként használható adathalmazt és kísérletek jelennek meg. Számos mintaként használható adathalmazt ezeket a mintákat a használják a [Azure Eszközintelligencia-katalógus](http://gallery.cortanaintelligence.com/). Mások, különböző típusú adatok gépi tanulás általában szerepel példaként szerepelnek.

Ezek az adatkészletek között az Azure Blob storage érhető el. Ezen adatkészletek esetében a következő táblázat egy közvetlen hivatkozást. Használatával a kísérletek ezek az adatkészletek is használhatja a [és adatokat importálhat] [ import-data] modul.

A többi ezek mintaként használható adathalmazt érhetők el a munkaterület **mentett adatkészletek**. Ez a modulpalettán bal oldalán a kísérletvászonra a Machine Learning Studióban találja meg.
Használhatja bármelyik ezek az adatkészletek saját kísérletben húzza a kísérletvászonra.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

<table>

<tr>
  <th>Adatkészlet neve</th>
  <th>Adatkészlet leírása</th>
</tr>

<tr>
  <td>Felnőtt nyilvántartásba bevétel bináris osztályozási adatkészlet</td>
  <td>
Egy részét a 1994 nyilvántartásba adatbázist, működő felnőttek > 100 módosított bevétel indexű 16-os életkorát.
<p></p>
<b>Használat:</b> demográfiai használatával megjósolható, hogy egy személy szerez évente több mint 50 K személyek besorolását.
<p></p>
<b>Kapcsolódó kutatási:</b> Kohavi, R., Becker, b, (1996). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai </td>
</tr>

<tr>
  <td>Repülőtéri kódok adatkészlet</td>
  <td>
USA repülőtéri kódokat.
<p></p>
Ez az adatkészlet egyes USA repülőtéri biztosítása a repülőtéri azonosítóját és nevét, és a hely várost és egy sort tartalmaz.
  </td>
</tr>

<tr>
  <td>Autó price data (Raw)</td>
  <td>
Ellenőrizze és, beleértve az ár modellhez tartozó szolgáltatások, mint a hengerszám és MPG, valamint egy biztosítási kockázati pontszám száma által autók kapcsolatos információk.
<p></p>
A kockázati pontszám kezdetben tartozik automatikus ár. Majd ellátó matematikusok symboling, hogy ez a folyamat a tényleges veszélyességét módosul. + 3 érték azt jelzi, hogy az automatikus kockázatos, és a -3, hogy az informatikai érték valószínűleg biztonságos.
<p></p>
<b>Használat:</b> előre jelezni a kockázati pontszám funkcióihoz regressziós vagy multivariate besorolásával. 
<p></p>
<b>Kapcsolódó kutatási:</b> Schlimmer, J.C. (1987). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai </td>
</tr>

<tr>
  <td>Annak bérleti UCI adatkészlet</td>
  <td>
A dataset UCI kerékpárt bérleti beruházási Bikeshare vállalati Washingtoni kerékpárt bérleti hálózat által származó valós adatok alapján.
<p></p>
Az adatkészlet egyes órában 17,379 sorok összesen 2011 és 2012, naponta egy sorból áll. Óránkénti kerékpárt bérlését tartományán: 1 – 977.

  </td>
</tr>

<tr>
  <td>Bill Gates RGB kép</td>
  <td>
Nyilvánosan elérhető képfájl CSV adatok konvertálva.
<p></p>
A kép kódja megtalálható a <strong>szín a K-közép fürtszolgáltatása mennyiségmeghatározási</strong> modell információs lapját.
  </td>
</tr>

<tr>
  <td>Vér adományozás adatok</td>
  <td>
A VÉRTRANSZFÚZIÓS Service Center a Hsin-csú város, Tajvan vér donor adatbázisából az adatok egy részét.
<p></p>
Donor adatok tartalmazzák a hónap utolsó adományozás óta), és gyakoriság, vagy véradás, utolsó adományozás óta eltelt idő teljes száma, és elajándékozni vér mennyiségét.
<p></p>
<b>Használat:</b> becsülhető besorolás keresztül, hogy a donor elajándékozni vér március 2007, ahol 1, és a célként megadott időszak, 0 ország nem ország célja. 
<p></p>
<b>Kapcsolódó kutatási:</b> Yeh, úgy, (2008). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai
<p></p>
Yeh, I-Cheng, Yang, képeznie-Jang, és tudomásul véve, Tao Ming, "Tudásbázis felderítés RFM-modellben Bernoulli feladatütemezési,"szakértői rendszereket 2008, az alkalmazásokkal <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Mell kapcsolatos adatok</td>
  <td>
A machine learning szakirodalomban gyakran megjelenő Oncology Intézet által biztosított három kapcsolatos kapcsolatos adatkészletek egyikét. Diagnosztikai információ laboratóriumi elemzési szolgáltatás, körülbelül 300 darab egyesíti.
<p></p>
<b>Használat:</b> kapcsolatos típusú besorolását, 9-es attribútumok alapján, amelyeket lineáris és kategorikus. 
<p></p>
<b>Kapcsolódó kutatási:</b> Wohlberg, W.H., utca, W.N. & Mangasarian, O.L. (1995). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai </td>
</tr>

<tr>
  <td>Mell kapcsolatos szolgáltatások <td>
A dataset 102K gyanús területek (jelöltek) röntgen-lemezképek, információkat tartalmaz, minden egyes leírt 117 funkcióihoz. A szolgáltatások saját fejlesztésű, és azok jelentését nem látható a dataset creators (Siemens egészségügy) által. 
  </td>
</tr>

<tr>
  <td>Mell kapcsolatos információ</td>
  <td>
A dataset röntgen lemezkép minden gyanús területre további információkat tartalmaz. Minden egyes példa információkat nyújt (például címke, beteg Azonosítóját, a teljes képhez képest javítás koordinátáit) mell kapcsolatos funkciók adatkészlet megfelelő sorok számát. Minden egyes türelmet számos példát. Egy kapcsolatos rendelkező betegeknél néhány példa pozitív és negatív között. Betegek, akik nem rendelkeznek egy kapcsolatos, az összes többek között negatív. A dataset adatkészletben 102K példák. A dataset optimalizálva, 0,6 % pontok pozitív, a többi negatív. A dataset lett rendelkezésére Siemens egészségügy.
  </td>
</tr>

<tr>
  <td>Megosztott CRM Appetency címkék</td>
  <td>
A KDD Cup 2009 felhasználói kapcsolat előrejelzés kihívás címkéi (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Megosztott CRM-forgalom címkék</td>
  <td>
A KDD Cup 2009 felhasználói kapcsolat előrejelzés kihívás címkéi (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>Megosztott CRM-adatkészlet</td>
  <td>
Ezeket az adatokat a KDD Cup 2009 felhasználói kapcsolat előrejelzés kihívás származik (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
A dataset 50K ügyfelek narancssárga francia Telecom vállalattól tartalmazza. Minden egyes ügyfélnek van 230 anonimizált funkciók, amelyek 190 numerikus és 40 kategorikus. A szolgáltatások akkor nagyon ritka.
  </td>
</tr>

<tr>
  <td>Megosztott CRM Upselling címkék</td>
  <td>
A KDD Cup 2009 felhasználói kapcsolat előrejelzés kihívás címkéi (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Energiahatékonyság regressziós adatok</td>
  <td>
Szimulált energia profilok, 12 különböző épület alakzatok alapján gyűjteménye. Az épületek megkülönböztetett forgalomosztályból nyolc funkcióihoz. Ez magában foglalja, üveg terület, az üveg terület terjesztési és tájolását.
<p></p>
<b>Használat:</b> használnak regressziós vagy besorolási minősítés alapján rendelkezésre álló két valós értékelni válaszok energiahatékonyság előre jelezni. Több osztály besorolási van a válasz változó kerekítése a legközelebbi egész kerek. 
<p></p>
<b>Kapcsolódó kutatási:</b> Xifara, A. & Tsanas, azonosítójához. (2012). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai </td>
</tr>

<tr>
  <td>Felé továbbított adatok késlelteti</td>
  <td>
Utas repülési időben teljesítményadatokat venni az Egyesült Államok TranStats adatok gyűjteménye Részleg a szállítására (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">időben</a>).
<p></p>
A dataset az adott időszakban 2013. április-október ismerteti. Azure Machine Learning Studio feltöltését, mielőtt a dataset feldolgozott az alábbiak szerint:
<ul>
  <li>Az adatkészletet, amelyek csak a 70 legforgalmasabb repülőtéren a szárazföldi USA a lett szűrve</li>
  <li>Több mint 15 perces késleltetett volt címkézett visszavont járatok</li>
  <li>Elterelt járatok szűrhető volt.</li>
  <li>A következő oszlopok kiválasztott: év, hónap, DayofMonth, DayOfWeek, vivőjel, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, megszakítva</li>
</ul>
</td>
</tr>

<tr>
  <td>Repülési időben teljesítmény (Raw)</td>
  <td>
Repülőgép repülési érkezők és a 2011. októberi az Amerikai Egyesült Államokból eltérést rögzíti.
<p></p>
<b>Használat:</b> repülési késések előre jelezni. 
<p></p>
<b>Kapcsolódó kutatási:</b> a szállítására részleg USA <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time"> http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time </a>.
  </td>
</tr>

<tr>
  <td>Erdőtüzek adatai</td>
  <td>
Időjárás adatokat tartalmaz, például a hőmérséklet és a páratartalom indexek és szél sebessége. Északkelet-Portugáliában előfordult erdőtüzek rekordjának együtt területe az adatok forrása.
<p></p>
<b>Használat:</b> Ez az regressziós nehéz feladat, ahol a célja, hogy előre jelezni erdőtüzek kiírt területén. 
<p></p>
<b>Kapcsolódó kutatási:</b> Cortez, p, & Morais, azonosítójához. (2008). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai
<p></p>
[Cortez és Morais, 2007] P Cortez és A. Morais. A Data Mining megközelítése időjárási adatokkal előrejelzése erdőtüzek. A J. Neves, M. f Santos és J. Machado Eds., mesterséges Eszközintelligencia, a 13 EPIA 2007 - mesterséges intelligens, December, Guimarães, Portugália, oldal 512-523, 2007 portugál konferencia eljárás új trendeket. APPIA, ISBN-13 978-989-95618-0-9. Megtekinthető a következő helyen: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf"> http://www.dsi.uminho.pt/~pcortez/fires.pdf </a>.
  </td>
</tr>

<tr>
  <td>Német hitelkártya UCI adatkészlet</td>
  <td>
A UCI Statlog (német hitelkártya) dataset (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + német + jóváírás + adatok</a>), a german.data fájllal.
<p></p>
A dataset személyek, attribútumok, mint magas vagy alacsony hitelkockázatok szerint osztályozza. Minden egyes példa egy személy jelöli. Nem találhatók 20 funkciókat, mind a numerikus, és a kategorikus, és egy bináris címke (a jóváírási kockázati érték). Hitelkockázati kockázat bejegyzések rendelkezik címke = 2, alacsony követel kockázat tételek rendelkezik címke = 1. Egy alacsony kockázat példa annyira misclassifying költségének értéke 1, mivel a magas kockázatú példa legalacsonyabb misclassifying költségét 5.
  </td>
</tr>

<tr>
  <td>IMDB Movie címek</td>
  <td>
A dataset filmek, a Twitter Twitter-üzeneteket volt minősített információt tartalmaz: IMDB movie azonosító, a movie nevét, a genre és a gyártási év. A dataset 17K filmek szerepelnek. A dataset jelent a dokumentum "használatát. Dooms, T. De Pessemier és L. Martens-féle. MovieTweetings: Dataset minősítés film begyűjti a Twitteren. Workshop közösségi és emberi számítási ajánló rendszerek esetén: RecSys 2013 CrowdRec."
  </td>
</tr>

<tr>
  <td>A két Iris osztály adatokat</td>
  <td>
Lehet, hogy ez az a legjobb ismert adatbázis a minta felismerés szakirodalomban találhatók. A dataset nem viszonylag nagy, 50 példák minden szirom mérések három iris fajtáinak tartalmazó.
<p></p>
<b>Használat:</b> előre jelezni a mérések iris típusát.  
<p></p>
<b>Kapcsolódó kutatási:</b> Fisher, R.A. (1988). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai </td>
</tr>

<tr>
  <td>Film Twitter-üzenetek</td>
  <td>
A dataset jelent Movie Tweetings DataSet. A dataset adatkészletben filmek, jól strukturált Twitter-üzeneteket a Twitteren kinyert 170K minősítését. Minden példány jelöli egy tweetet, és egy rekord: felhasználói azonosító IMDB movie azonosító, minősítés, timestamp, számának Kedvencek tweetet, és a retweets a tweetet a száma. A dataset lett által elérhetővé tett A. említett, S. Dooms a, b Loni és D. Tikk ajánló rendszerek Challenge 2014.
  </td>
</tr>

<tr>
  <td>Különböző autók fogyasztási adatai</td>
  <td>
Ez az adatkészlet a StatLib Library Carnegie Mellon egyetemi az adatkészlet kis mértékben módosított verzióját. A dataset a kézikönyvben 1983 American statisztikai társítás lett megadva.
<p></p>
Az adatok a miles / gallonra különböző autók üzemanyag-fogyasztás sorolja fel. Azt is tartoznak hengerszám, motor elmozdulását, lóerő, teljes súlya és gyorsítás száma.
<p></p>
<b>Használat:</b> üzemanyag-fogyasztási három többértékű diszkrét attribútumok és öt folytonos attribútumok alapján előre jelezni. 
<p></p>
<b>Kapcsolódó kutatási:</b> StatLib, Carnegie Mellon egyetemi, (1993). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai </td>
</tr>

<tr>
  <td>Pima indiai termelőktől cukorbetegség bináris osztályozási adatkészlet</td>
  <td>
A cukorbetegség National Institute és emésztőtraktus és vese betegségek adatbázis adatok egy részét. A dataset gyakorolt nőivarú Pima indiai örökségének fókusz lett szűrve. Az adatok például glükóz és inulin szintek, valamint lifestyle tényezők orvosi adatokat tartalmazza.
<p></p>
<b>Használat:</b> előre jelezni, hogy rendelkezik-e a tulajdonos cukorbetegség (bináris osztályozás). 
<p></p>
<b>Kapcsolódó kutatási:</b> Sigillito, V. (1990). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml"</a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai </td>
</tr>

<tr>
  <td>Éttermi ügyféladatok</td>
  <td>
Ügyfelek, beleértve a demográfiai és beállítások metaadatainak készlete.
<p></p>
<b>Használat:</b> ehhez az adatkészlethez, képzése, és tesztelje a ajánló rendszert a többi két éttermi adathalmazt együtt használja. 
<p></p>
<b>Kapcsolódó kutatási:</b> Bache, K. és Lichman, M. (2013). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, iskolai az adatokat és számítástechnikai.
  </td>
</tr>

<tr>
  <td>Éttermi szolgáltatás adatok</td>
  <td>
A metaadatok éttermekben és a hozzájuk tartozó funkciók áttekintése, például étele típusa, a étkezési stílus és a hely készlete.
<p></p>
<b>Használat:</b> ehhez az adatkészlethez, képzése, és tesztelje a ajánló rendszert a többi két éttermi adathalmazt együtt használja. 
<p></p>
<b>Kapcsolódó kutatási:</b> Bache, K. és Lichman, M. (2013). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, iskolai az adatokat és számítástechnikai.
  </td>
</tr>

<tr>
  <td>Éttermi minősítése</td>
  <td>
Adott felhasználók éttermekben méretű 0 2 minősítések tartalmazza.
<p></p>
<b>Használat:</b> ehhez az adatkészlethez, képzése, és tesztelje a ajánló rendszert a többi két éttermi adathalmazt együtt használja. 
<p></p>
<b>Kapcsolódó kutatási:</b> Bache, K. és Lichman, M. (2013). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, iskolai az adatokat és számítástechnikai.
  </td>
</tr>

<tr>
  <td>Készült Annealing több osztály adatkészlet</td>
  <td>
Ez az adatkészlet kísérletek primerek acélból származó rekordokat tartalmazza. (Width, vastagsága, típusa (gyújtótekercs, lap stb.) a létrejövő készült típusok a fizikai attribútumokat tartalmaz.
<p></p>
<b>Használat:</b> bármely két numerikus osztályattribútumokhoz; keménység vagy erőssége előre jelezni. Előfordulhat, hogy is elemez korrelációk attribútumok között.
<p></p>
Készült besorolási hajtsa végre egy szabvány, SAE és más szervezetekkel. Egy adott "osztály" (Ez az osztály változó) keres, és szeretné tudni, hogy a szükséges értékeket. 
<p></p>
<b>Kapcsolódó kutatási:</b> Sterling, d & Buntine, w (NA). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, a iskolai az adatokat és a számítástechnikai
<p></p>
Acélminőségek hasznos útmutatóját itt található: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Teleszkóppal adatok</td>
  <td>
Magas energia gamma alkotóelem rekord felszakadásáig háttérzaj, mindkettő szimulált Monte Carlo eljárással együtt.
<p></p>
A szimuláció szándékával lett ground alapú légköri Cherenkov gamma teleszkópok pontosságának javítása érdekében. Ez történik, statisztikai módszerek segítségével különböztetheti meg a kívánt jel (Cherenkov sugárzási zuhanyozók) és a háttérzaj (hadronic zuhanyozók cosmic sugarak a felső környezetben által kezdeményezett).
<p></p>
A korábban már Előfeldolgozott egy nyújtott alakú fürt létrehozása a hosszan a tengely nincs objektumorientált a kamera center felé. A három pont (gyakran nevezik Hillas paraméterek) jellemzői megkülönböztetés használható kép paraméterek közé tartoznak.
<p></p>
<b>Használat:</b> előre jelezni, hogy mulatni képe jelöl jel vagy a háttérben zaj.
<p></p>
<b>Megjegyzések:</b> egyszerű besorolás pontossága értéke nem értelmezhető, ezeket az adatokat, egy háttér esemény zárolásának, jel egy jel esemény háttereként zárolásának óta. Különböző osztályozó összehasonlítása a ROC diagramhoz kell használni. A valószínűsége annak, egy háttér esemény fogadására jel egyet a következő küszöbértékek alá kell lennie: 0,01, 0,02, 0,05, 0,1 vagy 0,2.
<p></p>
Vegye figyelembe azt is, hogy kellőképpen-e a háttérben események (hadronic zuhanyozók h) száma. Valós mérések a h vagy zaj osztály jelenti. a legtöbb esemény. 
<p></p>
<b>Kapcsolódó kutatási:</b> Bock, R.K. (1995). UCI gépi tanulási a tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Egyetemi a kaliforniai, iskolai vonatkozó információk </td>
</tr>

<tr>
  <td>Időjárás-adatkészlet</td>
  <td>
Óránkénti szárazföldi időjárási észrevételt NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">201304 201310 adatainak egyesített</a>).
<p></p>
A időjárási adatok repülőtéri időjárási esetében, az adott időszakban 2013. április-október kiterjedő észrevételeket ismerteti. Azure Machine Learning Studio feltöltését, mielőtt a dataset feldolgozott az alábbiak szerint:
<ul>
  <li>Időjárás állomás azonosítók megfelelő repülőtéri azonosítók hozzá lettek rendelve</li>
  <li>Nincs társítva a 70 legforgalmasabb repülőtéren időjárási állomások szűrhető volt.</li>
  <li>A dátum oszlop külön év, hónap és nap oszlopok lett felosztása</li>
  <li>A következő oszlopok kiválasztott: AirportID, év, hónap, nap, idő, időzóna, SkyCondition, látható, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius RelativeHumidity, Szélsebesség, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, magasságmérő</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 adatkészlet</td>
  <td>
Adatok Wikipedia származik (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) cikkeket S & P 500 vállalatok, tárolt XML-adatok alapján.
<p></p>
Azure Machine Learning Studio feltöltését, mielőtt a dataset feldolgozott az alábbiak szerint:
<ul>
  <li>Bontsa ki a szöveges tartalom minden adott vállalat esetében</li>
  <li>Wiki formázás eltávolítása</li>
  <li>Távolítsa el a nem alfanumerikus karaktereket</li>
  <li>A kisbetűssé alakítandó az összes szöveg</li>
  <li>Ismert vállalati kategóriák lettek hozzáadva.</li>
</ul>
<p></p>
Vegye figyelembe, hogy bizonyos vállalatok egy cikk nem található, így a rekordok száma 500-nál kisebb.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
A dataset ügyféladatok és egy közvetlen levelezési kapcsolatos a válasz kapcsolatos tartalmazza. Minden egyes sorára ügyfél jelöli. A dataset kapcsolatos felhasználói demográfiai és viselkedése túli kilenc szolgáltatásokat tartalmaz, és három címkézését, oszlopok (fel, az átalakítás és töltött).  Látogasson el a bináris oszlop, amely jelzi, hogy az ügyfél ellátogat a marketingkampányt után. Átalakítás azt jelzi, hogy az ügyfél vásárolt. Töltött telt összege.  A dataset lett rendelkezésére Kevin Hillstrom a MineThatData E-Mail elemzés és a Data Mining kérésekor.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Tesztelési példák RCV1-V2 Reuters hírek adatkészlet tulajdonságai. A dataset adatkészletben 781K hírcikkeket azonosítók együtt (a DataSet adatkészlet első oszlop). Minden cikk tokenekre bontott stopworded, és kocsány. A dataset David által történt érhető el. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Példák RCV1-V2 Reuters hírek adatkészlet tulajdonságai. A dataset adatkészletben 23K hírcikkeket azonosítók együtt (a DataSet adatkészlet első oszlop). Minden cikk tokenekre bontott stopworded, és kocsány. A dataset David által történt érhető el. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
A KDD Cup 1999 Tudásbázis felderítési és adatbányászat adatkészlet eszközök verseny (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
A dataset letöltötte és az Azure Blob storage szolgáltatásban tárolt (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) és a képzési és a teszteléshez adatkészletek is tartalmaz. A képzési dataset adatkészletben körülbelül, 126K sorok és 43 oszlopok, beleértve a feliratok. Három oszlopot a címke része, és 40 oszlopok álló numerikus és a karakterlánc kategorikus funkciók érhetők el a modell betanítása. A vizsgálati adatok körülbelül 22.5K tesztelése példák mint a betanítási adatok azonos 43 oszlopokkal rendelkezik.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
A témakör a hozzárendeléseinek RCV1-V2 Reuters hírek adatkészlet írt hírcikkeket. Egy hírek cikk több témakört is hozzárendelhető. Minden egyes sorára formátuma "&lt;témakör&gt; &lt;dokumentumazonosítója&gt; 1". A dataset 2.6-os M témakör hozzárendeléseket tartalmazza. A dataset David által történt érhető el. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Ezeket az adatokat a KDD Cup 2010 Student teljesítmény értékelési kihívás származik (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">student teljesítmény értékelési</a>). A használt adatok a Algebra_2008_2009 gyakorlókészlethez (Stamper, J., Niculescu-Mizil, A., Ritter, S. Gordon, G.J. & Koedinger, k. r. (2010). Algebra I 2008-2009. KDD Cup 2010 oktatási Data Mining ellenőrző kérdés adatkészlet. A Keresés <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> vagy <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.
<p></p>
A dataset letöltötte és az Azure Blob storage szolgáltatásban tárolt (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) és a rendszer-oktatás student naplófájlokat tartalmazza. A megadott szolgáltatások közé tartozik a probléma azonosítója és a rövid leírását, student azonosítója, Timestamp típusú és hány kísérletet a student előtt a probléma megoldásához a megfelelő módon. Az eredeti dataset adatkészletben 8.9M rekordok; Ez az adatkészlet lett az első 100K sorokra lefelé-mintát. A dataset adatkészletben különböző típusú 23 tabulátorral tagolt oszlopok: numerikus, kategorikus, és a Timestamp típusú.
  </td>
</tr>

</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
