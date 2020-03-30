---
title: A mintaadatkészletek használata
titleSuffix: ML Studio (classic) - Azure
description: A Machine Learning Studio (klasszikus) mintamodelljeiben használt adatkészletek leírása. Ezeket a mintaadatkészleteket a kísérletekhez használhatja.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: b7b8606f7f15f8d6fdd66681a1c7ade60ff506f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217786"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>A mintaadatkészletek használata az Azure Machine Learning Studio-ban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[top]: #machine-learning-sample-datasets

Amikor új munkaterületet hoz létre az Azure Machine Learning Studio (klasszikus) környezetében, alapértelmezés szerint számos mintaadatkészlet és kísérlet szerepel. Ezek közül a mintaadatkészletek közül sokat az [Azure AI-galériában](https://gallery.azure.ai/)lévő mintamodellek használnak. Mások példaként a gépi tanulásban általában használt különböző típusú adatok.

Ezen adatkészletek egy része elérhető az Azure Blob storage-ban. Ezekhez az adatkészletekhez az alábbi táblázat közvetlen kapcsolatot tartalmaz. Ezeket az adatkészleteket a kísérletekben az Adatok importálása modul segítségével [használhatja.][import-data]

A többi mintaadatkészlet a **mentett adatkészletek**területen érhető el a munkaterületen. Ezt a machine learning studio (klasszikus) kísérleti vászon bal oldalán található modulpalettában találja.
Ezen adatkészletek bármelyikét használhatja a saját kísérletében, ha a kísérleti vászonra húzza.

## <a name="datasets"></a>Adathalmazok

<table>

<tr>
  <th>Adatkészlet neve</th>
  <th>Adatkészlet leírása</th>
</tr>

<tr>
  <td>Felnőtt census income bináris osztályozási adatkészlet</td>
  <td>
Az 1994-es népszámlálási adatbázis egy részhalmaza, amely 16 év feletti dolgozó felnőtteket használ, kiigazított jövedelmi indexe > 100.
<p></p>
<b>Használat:</b> A demográfia segítségével osztályozhatja azokat, akik megjósolják, hogy egy személy évente több mint 50 ezret keres-e.
<p></p>
<b>Kapcsolódó kutatás:</b> Kohavi, R., Becker, B., (1996). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola </td>
</tr>

<tr>
  <td>Repülőtéri kódok adatkészlete</td>
  <td>
Amerikai repülőtér-kódok.
<p></p>
Ez az adatkészlet minden egyes amerikai repülőtérhez egy sort tartalmaz, amely megadja a repülőtéri azonosító számát és nevét a helyvárossal és az állammal együtt.
  </td>
</tr>

<tr>
  <td>Gépjármű áradatai (Nyers)</td>
  <td>
Információk az autók ról gyártmány és modell szerint, beleértve az árat, olyan jellemzőket, mint a hengerek száma és az MPG, valamint a biztosítási kockázati pontszám.
<p></p>
A kockázati pontszám kezdetben az automatikus árhoz kapcsolódik. Ezt követően a biztosításmatematikusok által szimbólumként ismert folyamat tényleges kockázatához igazítják. A +3 érték azt jelzi, hogy az automatikus kockázatos, és értéke -3, hogy valószínűleg biztonságos.
<p></p>
<b>Használat:</b> A kockázati pontszám előrejelzése funkciók szerint, regressziós vagy többváltozós besorolás használatával. 
<p></p>
<b>Kapcsolódó kutatás:</b> Schlimmer, J.C. (1987). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola </td>
</tr>

<tr>
  <td>Kerékpárkölcsönzés UCI adatkészlet</td>
  <td>
UCI Bike Rental adatkészlet, amely a Capital Bikeshare cég valós adataira épül, amely kerékpárkölcsönző hálózatot tart fenn Washington DC-ben.
<p></p>
Az adatkészlet 2011-ben és 2012-ben minden egyes nap minden egyes órájára egy sort tartalmaz, összesen 17 379 sort. Az óránkénti kerékpárkölcsönzés 1 és 977 között mozog.

  </td>
</tr>

<tr>
  <td>Bill Gates RGB-kép</td>
  <td>
A nyilvánosan elérhető képfájl CSV-adatokká konvertálva.
<p></p>
A kép konvertálásának kódja a <strong>K-Means fürtözési modell részletes oldalának K-Means fürtözési</strong> modell részletes oldalán található.
  </td>
</tr>

<tr>
  <td>Véradási adatok</td>
  <td>
A tajvani Hsin-Chu City Vérátömlesztési Szolgáltató Központjának véradó adatbázisából származó adatok egy része.
<p></p>
A donoradatok tartalmazzák az utolsó adományozás óta eltelt hónapokat), valamint az adományok gyakoriságát, illetve az adományok teljes számát, az utolsó adományozás óta eltelt időt és az adományozott vér mennyiségét.
<p></p>
<b>Használat:</b> A cél az, hogy a besorolás segítségével megjósolják, hogy a donor 2007 márciusában adott-e vért, ahol az 1 a célidőszakban a donort, 0 pedig nem donort jelez. 
<p></p>
<b>Kapcsolódó kutatás:</b> Igen, I.C., (2008). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola
<p></p>
Yeh, I-Cheng, Yang, King-Jang, és Ting, Tao-Ming, "Tudás felfedezés rfm modell segítségével Bernoulli szekvencia, "Expert Systems with Applications, 2008,<a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Emlőrák ra vonatkozó adatok</td>
  <td>
Az Onkológiai Intézet által a gépi tanulás szakirodalomban gyakran megjelenő három rákkal kapcsolatos adatkészlet egyike. A diagnosztikai információkat mintegy 300 szövetminta laboratóriumi elemzésének jellemzőivel kombinálja.
<p></p>
<b>Használat:</b> Osztályozza a rák típusát, 9 attribútum oka alapján, amelyek közül néhány lineáris és néhány kategorikus. 
<p></p>
<b>Kapcsolódó kutatás:</b> Wohlberg, W.H., Utca, W.N., & Mangasarian, O.L. (1995). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola </td>
</tr>

<tr>
  <td>Mellrák jellemzők <td>
Az adatkészlet 102K gyanús régiók (jelöltek) adatait tartalmazza a röntgenképek, amelyek mindegyike 117 funkciók által leírt információkat tartalmaz. A funkciók védettek, és jelentésüket nem fedik fel az adatkészlet alkotói (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Mellrák Info</td>
  <td>
Az adatkészlet további információkat tartalmaz a röntgenkép minden gyanús régiójáról. Minden példa információt (például címkét, betegazonosítót, a teljes képhez viszonyított javítás koordinátáit) tartalmaz a Mellrák jellemzői adatkészlet megfelelő sorszámáról. Minden betegnek számos példa van. A rákos betegek esetében néhány példa pozitív és néhány negatív. A betegek, akik nem rendelkeznek a rák, minden példa negatív. Az adatkészlet 102K példákat tartalmaz. Az adatkészlet elfogult, a pontok 0,6%-a pozitív, a többi negatív. Az adatkészletet a Siemens Healthcare bocsátotta rendelkezésre.
  </td>
</tr>

<tr>
  <td>CRM előlegcímkék megosztva</td>
  <td>
Címkék a KDD Cup 2009 ügyfélkapcsolat-előrejelzés kihívás (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM churn címkék megosztva</td>
  <td>
Címkék a KDD Cup 2009 ügyfélkapcsolat-előrejelzéskihívás (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM-adatkészlet megosztva</td>
  <td>
Ezek az adatok a KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívásból származnak (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Az adatkészlet 50 Ezer ügyfelet tartalmaz a francia telecomcégtől, az Orange-tól. Minden ügyfél 230 anonimizált funkcióval rendelkezik, amelyek közül 190 numerikus és 40 kategorikus. A funkciók nagyon ritkák.
  </td>
</tr>

<tr>
  <td>CRM upselling címkék megosztott</td>
  <td>
Címkék a KDD Cup 2009 ügyfélkapcsolat-előrejelzés kihívás (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Energiahatékonysági regressziós adatok</td>
  <td>
Szimulált energiaprofilok gyűjteménye, 12 különböző épületalakzat alapján. Az épületeket nyolc jellemző különbözteti meg. Ez magában foglalja az üvegezési területet, az üvegezési terület eloszlását és a tájolást.
<p></p>
<b>Használat:</b> A regresszió vagy az osztályozás segítségével előre jelezheti az energiahatékonysági besorolást, amely a két valós értékű válasz egyikeként alapul. Többosztályos besorolás esetén a válaszváltozó kerekítése a legközelebbi egész számra. 
<p></p>
<b>Kapcsolódó kutatás:</b> Xifara, A. & Tsanas, A. (2012). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola </td>
</tr>

<tr>
  <td>Járatkésések adatai</td>
  <td>
Az Egyesült Államok Közlekedési Minisztériumának TranStats adatgyűjtéséből származó utasrepülési teljesítményadatok (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time</a>).
<p></p>
Az adatkészlet a 2013 április-októberi időszakot fedi le. Az Azure Machine Learning Studio (klasszikus) gépi tanulási stúdióba való feltöltés előtt az adatkészlet feldolgozása a következőképpen történt:
<ul>
  <li>Az adatkészletet úgy szűrték, hogy csak az USA 70 legforgalmasabb repülőterére terjedjen ki.</li>
  <li>A törölt járatokat több mint 15 perccel később törölték</li>
  <li>Kiszűrték az eltérített járatokat</li>
  <li>A következő oszlopok vannak kiválasztva: Év, Hónap, DayofMonth, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>Repülési idő teljesítmény (nyers)</td>
  <td>
2011 októberétől az Egyesült Államokon belüli repülőgép-érkezések és -indulások feljegyzései.
<p></p>
<b>Használat:</b> Jósolja meg a járatkéséseket. 
<p></p>
<b>Kapcsolódó kutatás:</b> Az Egyesült Államok Közlekedési <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>Minisztériumából.
  </td>
</tr>

<tr>
  <td>Erdőtüzek adatai</td>
  <td>
Időjárási adatokat tartalmaz, például hőmérséklet- és páratartalom-indexeket és szélsebességet. Az adatok északkeleti Portugália egy olyan területéről származnak, ahol erdőtüzeket tartalmazó feljegyzések et is elkell végezni.
<p></p>
<b>Használat:</b> Ez egy nehéz regressziós feladat, ahol a cél az erdőtüzek leégett területének előrejelzése. 
<p></p>
<b>Kapcsolódó kutatás:</b> Cortez, P., & Morais, A. (2008). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola
<p></p>
[Cortez és Morais, 2007] P. Cortez és A. Morais. Adatbányászati megközelítés az erdőtüzek meteorológiai adatok felhasználásával történő előrejelzéséhez. In J. Neves, M. F. Santos és J. Machado Eds., New Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 - Portugál Konferencia a mesterséges intelligenciáról, december, Guimarães, Portugália, 512-523 pp. 2007. APPIA, ISBN-13 978-989-95618-0-9. Elérhető: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr>
  <td>Német hitelkártya UCI adatkészlet</td>
  <td>
Az UCI Statlog (német hitelkártya) adatkészlet (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>) a german.data fájl használatával.
<p></p>
Az adatkészlet az attribútumok készletével leírva alacsony vagy magas hitelkockázatként sorolja be a személyeket. Minden példa egy személyt jelöl. 20 funkció létezik, mind numerikus, mind kategorikus, és egy bináris címke (a hitelkockázati érték). A magas hitelkockázati tételek címkével rendelkeznek = 2, az alacsony hitelkockázati tételek címkével rendelkeznek = 1. Az alacsony kockázatú példák magasként való téves besorolásának költsége 1, míg a magas kockázatú példák alacsonyként való téves besorolásának költsége 5.
  </td>
</tr>

<tr>
  <td>IMDB filmcímek</td>
  <td>
Az adatkészlet információkat tartalmaz a Twitter-tweetekben értékelt filmekről: IMDB filmazonosító, filmnév, műfaj és gyártási év. Az adatkészletben 17 K-s filmek találhatók. Az adatkészlet et az "S. Dooms, T. De Pessemier és L. Martens. MovieTweetings: a Movie Rating dataset gyűjtött Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems (CrowdRec at RecSys 2013) workshop on Crowdsourcing and Human Computation for Recommender Systems (CrowdRec at RecSys 2013."
  </td>
</tr>

<tr>
  <td>Iris két osztály adatok</td>
  <td>
Ez talán a legismertebb adatbázis megtalálható a minta felismerés irodalomban. Az adatkészlet viszonylag kicsi, amely 50 példát tartalmaz három íriszfajtászalma sziromméréseiből.
<p></p>
<b>Használat:</b> Jósolja meg az írisz típusát a mérésekből.  
<p></p>
<b>Kapcsolódó kutatás:</b> Fisher, R.A. (1988). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola </td>
</tr>

<tr>
  <td>Film Tweets</td>
  <td>
Az adatkészlet a Movie Tweetings adatkészlet kibővített verziója. Az adatkészlet 170K minősítést tartalmaz a filmekhez, amelyeket jól strukturált tweetekből nyernek ki a Twitteren. Minden példány egy tweetet jelöl, és egy tuple: felhasználói azonosító, IMDB filmazonosító, értékelés, időbélyeg, a tweet kedvencek száma és a tweet retweetjeinek száma. Az adatkészletet Az A. Said, S. Dooms, B. Loni és D. Tikk bocsátotta rendelkezésre az Recommender Systems Challenge 2014 számára.
  </td>
</tr>

<tr>
  <td>MPG adatok különböző autók</td>
  <td>
Ez az adatkészlet a Carnegie Mellon Egyetem StatLib könyvtára által biztosított adatkészlet kissé módosított változata. Az adatkészletet az 1983-as Amerikai Statisztikai Szövetség kiállításán használták.
<p></p>
Az adatok szerint a különböző autók üzemanyag-fogyasztása mérföld/gallonban. Olyan információkat is tartalmaz, mint például a hengerek száma, a motor hengerűrtartalma, a lóerő, a teljes tömeg és a gyorsulás.
<p></p>
<b>Használat:</b> A tüzelőanyag-fogyasztás előrejelzése három többértékű különálló attribútum és öt folyamatos attribútum alapján. 
<p></p>
<b>Kapcsolódó kutatás:</b> StatLib, Carnegie Mellon Egyetem, (1993). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola </td>
</tr>

<tr>
  <td>Pima Indians Diabetes bináris besorolásadatkészlet</td>
  <td>
A National Institute of Diabetes and Digestive and Kidney Diseases adatbázis adatainak egy részhalmaza. Az adatkészletet szűrték, hogy a Pima indiai örökség női betegeire összpontosítsanak. Az adatok olyan orvosi adatokat tartalmaznak, mint a glükóz és az inzulinszint, valamint az életmódbeli tényezők.
<p></p>
<b>Használat:</b> Tippelje meg, hogy az alany cukorbeteg-e (bináris osztályozás). 
<p></p>
<b>Kapcsolódó kutatás:</b> Sigillito, V. (1990). UCI gépi <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml</a>tanulási adattár " . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola </td>
</tr>

<tr>
  <td>Éttermi ügyféladatok</td>
  <td>
Az ügyfelekmetaadatait, beleértve a demográfiai adatokat és a beállításokat.
<p></p>
<b>Használat:</b> Használja ezt az adatkészletet a másik két éttermi adatkészlettel kombinálva egy ajánló rendszer betanításához és teszteléséhez. 
<p></p>
<b>Kapcsolódó kutatás:</b> Bache, K. és Lichman, M. (2013). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Éttermi szolgáltatás adatai</td>
  <td>
Az éttermekről és azok jellemzőiről , például az ételtípusról, az étkezési stílusról és a helyről.
<p></p>
<b>Használat:</b> Használja ezt az adatkészletet a másik két éttermi adatkészlettel kombinálva egy ajánló rendszer betanításához és teszteléséhez. 
<p></p>
<b>Kapcsolódó kutatás:</b> Bache, K. és Lichman, M. (2013). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Éttermi értékelések</td>
  <td>
A felhasználók által a 0-tól 2-ig terjedő skálán lévő éttermeknek adott minősítéseket tartalmazza.
<p></p>
<b>Használat:</b> Használja ezt az adatkészletet a másik két éttermi adatkészlettel kombinálva egy ajánló rendszer betanításához és teszteléséhez. 
<p></p>
<b>Kapcsolódó kutatás:</b> Bache, K. és Lichman, M. (2013). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Acéllágyítástöbbosztályos adatkészlet</td>
  <td>
Ez az adatkészlet acéllágyítási kísérletek rekordsorozatát tartalmazza. Ez tartalmazza a fizikai attribútumok (szélesség, vastagság, típus (tekercs, lemez, stb) a kapott acéltípusok.
<p></p>
<b>Használat:</b> Jósolja meg a két numerikus osztályattribútum bármelyikét; keménység vagy erő. Elemezheti az attribútumok közötti korrelációkat is.
<p></p>
Az acélminőségek a SAE és más szervezetek által meghatározott meghatározott szabványt követik. Egy adott "osztályzatot" (az osztályváltozót) keres, és szeretné megérteni a szükséges értékeket. 
<p></p>
<b>Kapcsolódó kutatás:</b> Sterling, D. & Buntine, W. (NA). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola
<p></p>
Egy hasznos útmutató az acél minőségek itt található:<a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Teleszkóp adatai</td>
  <td>
Rekord a nagy energiájú gamma részecske kitörések együtt háttérzaj, mindkettő szimulált egy Monte Carlo folyamat.
<p></p>
A szimuláció célja az volt, hogy javítsa a földi légköri Cherenkov gamma teleszkópok pontosságát. Ez statisztikai módszerekkel történik a kívánt jel (Cserenkov-sugárzászuhanyok) és a háttérzaj (a felső légkörben a kozmikus sugarak által kezdeményezett hadronikus zuhanyok) megkülönböztetésére.
<p></p>
Az adatokat előre feldolgozták, hogy hosszúkás fürtöt hozzanak létre, a hosszú tengely pedig a kamera középpontja felé irányul. Ennek az ellipszisnek a jellemzői (gyakran Hillas paraméterek) a megkülönböztetésre használható képparaméterek közé tartoznak.
<p></p>
<b>Használat:</b> Tippelje meg, hogy a zuhany képe jel- vagy háttérzajt jelöl-e.
<p></p>
<b>Megjegyzések:</b> Az egyszerű besorolási pontosság nem értelmezhető ezen adatok esetében, mivel a háttéresemény jelként való besorolása rosszabb, mint a jelesemény háttérként való besorolása. A különböző osztályozók összehasonlításához a ROC-grafikont kell használni. A háttéresemény jelként való elfogadásának valószínűsége a következő küszöbértékek egyike alatt kell, hogy legyen: 0,01, 0,02, 0,05, 0,1 vagy 0,2.
<p></p>
Is, vegye figyelembe, hogy a háttér események száma (h, a hadronikus zuhanyzók) alábecsülik. Valós mérések esetén a h vagy zaj osztály képviseli az események többségét. 
<p></p>
<b>Kapcsolódó kutatás:</b> Bock, R.K. (1995). UCI gépi <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>tanulási adattár . Irvine, CA: Kaliforniai Egyetem, Információs Iskola </td>
</tr>

<tr>
  <td>Időjárási adatkészlet</td>
  <td>
A NOAA óránkénti szárazföldi időjárási megfigyelései (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">201304 és 201310</a>között egyesített adatok ).
<p></p>
Az időjárási adatok a repülőtéri meteorológiai állomásokról származó, 2013 április-októberi időszakra vonatkozó megfigyeléseket fedik le. Az Azure Machine Learning Studio (klasszikus) gépi tanulási stúdióba való feltöltés előtt az adatkészlet feldolgozása a következőképpen történt:
<ul>
  <li>A meteorológiai állomás azonosítóit a megfelelő repülőtéri azonosítókhoz</li>
  <li>A 70 legforgalmasabb repülőtérhez nem kapcsolódó meteorológiai állomásokat kiszűrték</li>
  <li>A Dátum oszlop külön Év, Hónap és Nap oszlopokra van felosztva</li>
  <li>A következő oszlopok at választották: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 adatkészlet</td>
  <td>
Az adatok a<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>Wikipédiából származnak ( ) az egyes S&P 500 vállalat cikkei alapján, XML-adatként tárolva.
<p></p>
Az Azure Machine Learning Studio (klasszikus) gépi tanulási stúdióba való feltöltés előtt az adatkészlet feldolgozása a következőképpen történt:
<ul>
  <li>Szövegtartalom kinyerése minden egyes vállalathoz</li>
  <li>Wikiformázás eltávolítása</li>
  <li>Nem alfanumerikus karakterek eltávolítása</li>
  <li>Az összes szöveg átalakítása kisbetűssé</li>
  <li>Ismert vállalati kategóriákkal bővült</li>
</ul>
<p></p>
Ne feledje, hogy egyes vállalatok esetében nem található cikk, így a rekordok száma kevesebb, mint 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Az adatkészlet ügyféladatokat és a közvetlen levelezési kampányra adott válaszukra vonatkozó jelzéseket tartalmaz. Minden sor egy vevőt jelöl. Az adatkészlet kilenc szolgáltatást tartalmaz a felhasználói demográfiai adatokról és a múltbeli viselkedésről, valamint három címkeoszlopot (látogatás, konverzió és költés).  A látogatás egy bináris oszlop, amely azt jelzi, hogy egy ügyfél a marketingkampány után látogatott meg. A konverzió azt jelzi, hogy az ügyfél vásárolt valamit. A költés az elköltött összeg.  Az adatkészletet Kevin Hillstrom bocsátotta rendelkezésre a MineThatData e-mail elemzési és adatbányászati kihívásszámára.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv.</a></td>
  <td>
Az RCV1-V2 Reuters híradatkészletteszt-példák jellemzői. Az adatkészlet 781K híreket tartalmaz az azonosítóikkal együtt (az adatkészlet első oszlopa). Minden cikk tokenized, stopworded, és stemmed. Az adatkészletet David bocsátotta rendelkezésre. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Az RCV1-V2 Reuters híradatkészlet képzési példáinak jellemzői. Az adatkészlet 23K-s híreket tartalmaz az azonosítóikkal együtt (az adatkészlet első oszlopa). Minden cikk tokenized, stopworded, és stemmed. Az adatkészletet David bocsátotta rendelkezésre. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv.</a><br></td>
  <td>
Adatkészlet a KDD Cup 1999 Tudásfelfedezés és Adatbányászati Eszközök Verseny (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Az adatkészlet et az Azure Blob storage<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">(network_intrusion_detection.csv)</a>letöltötte és tárolta, és betanítási és tesztelési adatkészleteket is tartalmaz. A betanítási adatkészlet körülbelül 126 Ezer sorból és 43 oszlopból áll, beleértve a címkéket is. Három oszlop a címkeinformáció részét képezi, és 40 oszlop, amely numerikus és karakterlánc/kategorikus jellemzőkből áll, elérhető a modell betanításához. A vizsgálati adatok körülbelül 22.5K tesztpéldákkal rendelkeznek, amelyek ugyanazt a 43 oszlopot tartalmazják, mint a betanítási adatokban.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Az RCV1-V2 Reuters híradatkészletében található hírek témakör-hozzárendelései. A hírcikk több témakörhöz is hozzárendelhető. Az egyes sorok formátuma&lt;&gt; &lt;a "&gt; témakör név dokumentum azonosítója 1". Az adatkészlet 2,6 m-es témakör-hozzárendeléseket tartalmaz. Az adatkészletet David bocsátotta rendelkezésre. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Ezek az adatok származnak A KDD Cup 2010 Student teljesítmény értékelési kihívás (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">hallgatói teljesítmény értékelése</a>). A felhasznált adatok a Algebra_2008_2009 képzési készlet (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Kihívás adatkészlet kndd kupa 2010 oktatási adatbányászati kihívás. Keresse meg a <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Az adatkészlet et az Azure Blob storage<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">(student_performance.txt)</a>letöltötte és tárolta, és egy diákoktatórendszer naplófájljait tartalmazza. A mellékelt funkciók közé tartozik a problémaazonosító és annak rövid leírása, a diákazonosító, az időbélyeg, valamint az, hogy a tanuló hány kísérletet tett a probléma megfelelő megoldása előtt. Az eredeti adatkészlet 8,9 m-es rekordokkal rendelkezik; ezt az adatkészletet az első 100 K-s sorokra mintavételezte. Az adatkészlet 23 tabulátorral tagolt oszlopa van különböző típusú: numerikus, kategorikus és időbélyeg.
  </td>
</tr>

</table>

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Indítsa el a kísérleteket példákkal](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
