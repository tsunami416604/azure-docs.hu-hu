---
Cím: A mintául szolgáló adatkészletek titleSuffix használja: Azure Machine Learning Studio description: A Machine Learning Studióban szereplő minta modellek használt adatkészletekhez leírása. A kísérletek ezek mintaadatkészletek is használhat.
szolgáltatások: gépi tanulási ms.service: gépi tanulási ms.subservice: studio ms.topic: cikk

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro ms.date: 01/19/2018
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Az Azure Machine Learning Studió mintaadatkészleteinek használata
[top]: #machine-learning-sample-datasets

Amikor létrehoz egy új munkaterületet az Azure Machine Learning, a számos mintaadatkészletek és kísérletek alapértelmezés szerint szerepelnek. A mintául szolgáló modell által használt ezek mintaadatkészletek számos a [Azure AI-katalógusban](http://gallery.cortanaintelligence.com/). Mások, általában használt gépi tanulási adatokat különböző típusú példaként szerepelnek.

Ezek az adatkészletek néhány elérhető az Azure Blob storage-ban. Ezen adatkészletek esetében a következő táblázat a közvetlen hivatkozást tartalmaz. Használatával a kísérletek az ezeket az adatkészleteket is használhatja a [adatok importálása] [ import-data] modul.

A munkaterület alatt érhető el a többi ezek mintaadatkészletek **mentett adatkészletek**. Ez a kísérlet vászon, a Machine Learning Studióban a bal oldalon a modulpaletta találhatja.
Is használhatja ezeket az adatkészleteket bármelyikét a saját kísérletben húzza a kísérletvászonra.




<table>

<tr>
  <th>Adatkészlet neve</th>
  <th>Adatkészlet leírása</th>
</tr>

<tr>
  <td>Felnőtt népszámlálási jövedelem bináris osztályozási adatkészlet</td>
  <td>
> 100 módosított jövedelem indexű 16 idősebb működő felnőttek használatával 1994 népszámlálási adatbázis egy részét.
<p></p>
<b>Használat:</b> Személyek demográfiai használatával előre, hogy egy személy bevétele évente több mint 50 ezer besorolása.
<p></p>
<b>Kapcsolódó Research:</b> Kohavi, r, Becker, b, (1996). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi </td>
</tr>

<tr>
  <td>Repülőtér kódok adatkészlet</td>
  <td>
Egyesült Államok repülőtér kódokat.
<p></p>
Ez az adatkészlet minden Egyesült államokbeli repülőtér, a repülőtéren Azonosítóját és nevét, és a hely város és állam megadása egy sort tartalmaz.
  </td>
</tr>

<tr>
  <td>Autó price data (Raw)</td>
  <td>
Győződjön meg arról, és az árat, beleértve a modell a szolgáltatások, például hengerszám és MPG, valamint egy biztosítási kockázati pontszám száma szerint tulajdonságkészletét kapcsolatos információk.
<p></p>
A kockázati pontszám először kapcsolódik automatikus ár. Ezután módosul, a tényleges kockázat a biztosítási matematikusok, symboling a folyamatot. + 3 érték azt jelzi, hogy az automatikus kockázatos, és a -3 értéket, hogy biztonságos-e valószínűleg.
<p></p>
<b>Használat:</b> Előrejelezheti a kockázati pontszám funkcióihoz, regressziós vagy többváltozós besorolást. 
<p></p>
<b>Kapcsolódó Research:</b> Schlimmer, J.C. (1987). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi </td>
</tr>

<tr>
  <td>Kerékpár UCI Kerékpárkölcsönzési adatkészlet</td>
  <td>
UCI uci Kerékpárkölcsönzési adatkészletet, amely azon alapul, amely fenntartja a Washington, D.C. uci kerékpárkölcsönzési hálózat tőke Bikeshare vállalat valós adatait.
<p></p>
Az adatkészlet 17,379 sorok összesen 2011 és 2012, naponta, óránként egy sorral rendelkezik. Kerékpárkölcsönzés óránként tartományán az 1-től 977.

  </td>
</tr>

<tr>
  <td>Bill Gates RGB-lemezkép</td>
  <td>
Nyilvánosan elérhető képfájl alakítani a CSV-adatból.
<p></p>
Az a kód alakítása. a kép a <strong>szín a K-közép-fürtözés használatával mennyiségmeghatározási</strong> modell részletei lapot.
  </td>
</tr>

<tr>
  <td>Vér összeg egyeztetéséről adatok</td>
  <td>
A vérátömlesztésben Service Center, Hsin-Chu város, Tajvan vér donor adatbázisából az adatok egy részét.
<p></p>
Donor szerepel a hónap utolsó összeg egyeztetéséről óta), és a gyakoriság, vagy termékadományainak, utolsó összeg egyeztetéséről óta eltelt idő teljes száma, és szóló vér mennyiségét.
<p></p>
<b>Használat:</b> A cél, hogy-e a donor szóló vér március 2007, ahol 1, 0 és a célként megadott időszak során ország nem ország előrejelzése besorolási keresztül. 
<p></p>
<b>Kapcsolódó Research:</b> Yeh, I.C., (2008). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi
<p></p>
Yeh, e-Cheng, Yang, King-Jang, és sítése, címke-PN-Roaming, "Knowledge discovery az RFM-modellben Bernoulli feladatütemezési,"szakértői rendszereket 2008, az alkalmazásokkal <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Mell adatok</td>
  <td>
A machine learning elsajátításához gyakran megjelenő Oncology Institute által biztosított három rák kapcsolódó adatkészletek egyikét. Egyesíti a körülbelül 300 darab laboratóriumi elemzési funkciók diagnosztikai adatokat.
<p></p>
<b>Használat:</b> A rák típusát besorolása, 9 attribútumok alapján, amelyek némelyike lineáris, és kategorikus. 
<p></p>
<b>Kapcsolódó Research:</b> Wohlberg, W.H., utca, W.N. & Mangasarian, O.L. (1995). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi </td>
</tr>

<tr>
  <td>Mell rák funkciók <td>
Az adatkészlet 102K gyanús régiók (jelöltek) röntgen-lemezképek információkat tartalmaz, az egyes 117 funkcióihoz ismertetjük. A szolgáltatások saját fejlesztésű, és azok jelentését ne derüljön adatkészlet létrehozói (a Siemens Healthcare) által. 
  </td>
</tr>

<tr>
  <td>Mell rák adatai</td>
  <td>
Az adatkészlet röntgenfelvétel kép minden egyes gyanús régió további információkat tartalmaz. Valamennyi példa információkat (például címke, betegek koordináták képest a teljes képet javítás-azonosító) kapcsolatos mell rák funkciók adatkészlet a megfelelő sor számára. Egyes betegek számos példát. A betegek, akik rendelkeznek a rák néhány példa pozitív és negatív néhány. A betegek, akik nem rendelkeznek a rák minden példa lehet negatív. Az adatkészlet 102K példákat tartalmaz. Az adatkészlet torzítatlan, a pontokat a 0.6-os aránya pozitívak, a többi negatív. Az adatkészlet a Siemens Healthcare által elérhető történt.
  </td>
</tr>

<tr>
  <td>A megosztott CRM szolgáltatóváltást címkék</td>
  <td>
A évi Versenysorozatából Cup 2009 ügyfél kapcsolat előrejelzési kihívás címkéit (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>A megosztott CRM Lemorzsolódási címkék</td>
  <td>
A évi Versenysorozatából Cup 2009 ügyfél kapcsolat előrejelzési kihívás címkéit (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>A megosztott CRM-adatkészlet</td>
  <td>
Az adatok származási évi Versenysorozatából Cup 2009 ügyfél kapcsolat előrejelzési challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Az adatkészlet-ügyfelek a francia telekommunikációs cég narancssárga 50 ezer tartalmazza. Minden ügyfél rendelkezik, amelyek 190 numerikus 230 anonimizált funkciók és 40 kategorikus. A szolgáltatások nagyon ritka.
  </td>
</tr>

<tr>
  <td>A megosztott CRM Upselling címkék</td>
  <td>
A évi Versenysorozatából Cup 2009 ügyfél kapcsolat előrejelzési kihívás címkéit (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Regresszió energiahatékonyság adatok</td>
  <td>
Szimulált energia-profilok alapján 12 épület különböző alakzatok gyűjteménye. Az épületek nyolc funkcióihoz különbözteti meg. Ide tartoznak a terület, a üveg terület terjesztési és a tájolás üveg.
<p></p>
<b>Használat:</b> A két valós értékű válaszok egyik alapú energiahatékonyság minősítés előrejelzése regressziós vagy a besorolási használja. A többcsoportos besoroláshoz a válasz változó pedig a legközelebbi egész round van. 
<p></p>
<b>Kapcsolódó Research:</b> Xifara, A. & Tsanas, területen. (2012). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi </td>
</tr>

<tr>
  <td>Repülési késlelteti az adatok</td>
  <td>
Utas repülési időben teljesítményadatok származik az USA TranStats adatok gyűjteménye Minisztériumának (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">időben</a>).
<p></p>
Az adatkészlet az adott időszakban 2013. április október ismerteti. Mielőtt feltöltené az Azure Machine Learning Studióban, az adatkészlet a következőképpen dolgozta:
<ul>
  <li>Az adatkészlet az Egyesült Államok szárazföldi területén csak 70 legforgalmasabb repülőterek terjed ki lett szűrve</li>
  <li>Megszakított repülőjáratok legális, legfeljebb 15 perccel késleltetve vannak</li>
  <li>Forgalomelterelés repülőjáratok ki lett szűrve</li>
  <li>A következő oszlopok kijelölve: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>A repülési időben teljesítmény (Raw)</td>
  <td>
Repülőgép repülési beérkező kérelmek és az Egyesült Államokon a 2011. október távozás rekordjait.
<p></p>
<b>Használat:</b> A járatok késésének előrejelzésére. 
<p></p>
<b>Kapcsolódó Research:</b> Az Egyesült Államok közlekedési részleg <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time"> http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time </a>.
  </td>
</tr>

<tr>
  <td>Erdőtüzek adatai</td>
  <td>
Időjárási adatokat tartalmaz, például hőmérséklettel és páratartalommal kapcsolatos indexeket és a szél sebessége. Az adatok egy adott területre északkelet Portugália erdőtüzek rekordjait együtt származik.
<p></p>
<b>Használat:</b> Ez a regressziós nehéz feladat, ahol a cél az, hogy előrejelzése erdőtüzek kiírt területéhez. 
<p></p>
<b>Kapcsolódó Research:</b> Cortez, o., & Morais, területen. (2008). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi
<p></p>
[Cortez és Morais, 2007] O. Cortez és A. Morais. Egy Data Mining megközelítése az időjárási adatok felhasználásával előre jelezni erdőtüzek. A J. Neves, M. f Santos és J. Machado EDS, a mesterséges intelligencia, a 13. EPIA 2007 - portugál konferencia, a mesterséges intelligencia, a decemberi, Guimarães, Portugália, oldal 512-523, 2007 eljárás új trendeket. APPIA, ISBN-13 978-989-95618-0-9. Elérhető: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf"> http://www.dsi.uminho.pt/~pcortez/fires.pdf </a>.
  </td>
</tr>

<tr>
  <td>Német hitelkártya UCI adatkészlet</td>
  <td>
A UCI Statlog (német hitelkártya) dataset (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + német + + adatokból</a>), a german.data fájllal.
<p></p>
Az adatkészlet személyek, attribútumok, kis vagy nagy hitelkockázatok által leírt osztályozza. Minden példánál a személyt jelöli. Nincsenek 20 szolgáltatásait, numerikus és a kategorikus, és a egy bináris címkét (a jóváírási kockázati érték). Kredit magas kockázati bejegyzések címkével rendelkezik = 2, alacsony kredit kockázati bejegyzések címkével rendelkezik = 1. Egy alacsony kockázat példa annyira misclassifying költségének értéke 1, mivel egy alacsony, magas kockázatú példa misclassifying költsége az 5.
  </td>
</tr>

<tr>
  <td>IMDB film címe</td>
  <td>
Az adatkészlet is minősített, a Twitter-tweetek filmek kapcsolatos információkat tartalmazza: IMDB movie azonosító, a film, a műfaj és a gyártási év. Az adatkészlet 17K filmek találhatók. A tanulmány "S. a jelent meg az adatkészlet Dooms, T. De Pessemier és L. Martens-féle. MovieTweetings: adatkészlet minősítés film gyűjteni a Twitteren. Workshop közösségi és emberi számítási ajánló rendszerek esetén: RecSys 2013 CrowdRec."
  </td>
</tr>

<tr>
  <td>Két Iris-osztályt adatok</td>
  <td>
Talán ez az a legjobb ismert adatbázis található, a minta felismerés elsajátításához. Az adatkészlet viszonylag kicsi, 50 példák egyes szirom mérések három iris fajtáinak tartalmazó.
<p></p>
<b>Használat:</b> Előrejelezheti a mérések Írisz típusát.  
<p></p>
<b>Kapcsolódó Research:</b> Fisher, R.A. (1988). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi </td>
</tr>

<tr>
  <td>Film Tweetek</td>
  <td>
Az adatkészlet jelent a film Tweetings adatkészlet. Az adatkészlet 170K minősítések filmekhez, a Twitteren jól strukturált tweetek kinyert rendelkezik. Minden példány egy tweetet jelöl, és egy rekord: felhasználói azonosító IMDB film-azonosító, minősítés, timestamp, számának kedvenceihez, így a tweet, valamint a tweet retweets számát. Az adatkészlet intéztek elérhető A. mondta, %s Dooms a, b Loni és D. Tikk ajánló rendszerek Challenge 2014.
  </td>
</tr>

<tr>
  <td>Különböző autók adatai</td>
  <td>
Ez az adatkészlet egy kis mértékben módosított verzióját az adatkészletet a StatLib Library Carnegie Mellon Egyetem. Az adatkészlet a kézikönyvben 1983 amerikai statisztikai társítás lett megadva.
<p></p>
Az adatok különböző autók fogyasztási az üzemanyag fogyasztás sorolja fel. Például a hengerszám, motor elmozdulást, lóerő, teljes súlya és gyorsítás számát információkat is tartalmaz.
<p></p>
<b>Használat:</b> Előrejelezheti az üzemanyag-fogyasztási többértékű diszkrét attribútumok három és öt folyamatos attribútumok alapján. 
<p></p>
<b>Kapcsolódó Research:</b> StatLib, Carnegie Mellon University (1993). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi </td>
</tr>

<tr>
  <td>Pima indiai termelőktől küzdő bináris osztályozási adatkészlet</td>
  <td>
A küzdő National Institute és emésztőtraktus és vese betegségek adatbázis származó adatok egy részét. Az adatkészlet Pima indiai örökségének női betegek fókusz lett szűrve. Egészségügyi adatok, például glükóz és inulin szintek, valamint lifestyle tényezők szerepel.
<p></p>
<b>Használat:</b> Előrejelezheti, hogy rendelkezik-e a tulajdonos küzdő (bináris osztályozás). 
<p></p>
<b>Kapcsolódó Research:</b> Sigillito, V. (1990). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml"</a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi </td>
</tr>

<tr>
  <td>Éttermek vásárlói adatok</td>
  <td>
Azokról az ügyfelekről, többek között demográfiai adatok és beállítások metaadatainak gyűjteménye.
<p></p>
<b>Használat:</b> A másik két éttermi adatkészletet, és ez az adatkészlet használatával taníthat vagy tesztelhet egy ajánló rendszer. 
<p></p>
<b>Kapcsolódó Research:</b> Bache, K. és Lichman, M. (2013). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, iskolai információs és számítógép-tudományi.
  </td>
</tr>

<tr>
  <td>Éttermek a szolgáltatás adatokat</td>
  <td>
Éttermek és a hozzájuk tartozó funkciók, például az élelmiszer-típus, étkeztetés stílus és a hely metaadatait készlete.
<p></p>
<b>Használat:</b> A másik két éttermi adatkészletet, és ez az adatkészlet használatával taníthat vagy tesztelhet egy ajánló rendszer. 
<p></p>
<b>Kapcsolódó Research:</b> Bache, K. és Lichman, M. (2013). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, iskolai információs és számítógép-tudományi.
  </td>
</tr>

<tr>
  <td>Éttermek minősítések</td>
  <td>
Minősítések által adott felhasználók éttermek a méretezési csoport 0 2 tartalmazza.
<p></p>
<b>Használat:</b> A másik két éttermi adatkészletet, és ez az adatkészlet használatával taníthat vagy tesztelhet egy ajánló rendszer. 
<p></p>
<b>Kapcsolódó Research:</b> Bache, K. és Lichman, M. (2013). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, iskolai információs és számítógép-tudományi.
  </td>
</tr>

<tr>
  <td>Acélszürke Annealing többcsoportos adatkészlet</td>
  <td>
Ez az adatkészlet kísérletek primerek acélból rekordjait tartalmazza. (Szélesség, vastagsága, típusa (tekercse, lap stb.) a létrejövő acélszürke típusok a fizikai attribútumokat tartalmaz.
<p></p>
<b>Használat:</b> Két numerikus osztály attribútumokat; előrejelzése keménység vagy erősségét. Előfordulhat, hogy is elemezheti az attribútumok közötti összefüggéseket.
<p></p>
Acélszürke osztályzatainak kövesse a szabvány, SAE és más szervezetek által definiált. Egy adott "osztály" (Ez az osztály változó) keres, és szeretné tudni, hogy a szükséges értékeket. 
<p></p>
<b>Kapcsolódó Research:</b> Sterling, d & Buntine, l. (NA). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, a School, adatokat és a számítógép-tudományi
<p></p>
Acélminőségek hasznos útmutató itt található: <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Teleszkóppal adatok</td>
  <td>
Magas energia gamma particle rekordját tevékenységcsúcsok is egy Monte Carlo folyamattal szimulált háttérzaj együtt.
<p></p>
A szimuláció szándéka volt az alapoktól-alapú légköri Cherenkov gamma teleszkópok pontosságának javítása. Ez történik, statisztikai módszerek használatával különbözteti meg a kívánt jel (Cherenkov sugárzási zuhanyozók) és a háttérzaj (hadronic zuhanyozók cosmic sugarak felső légköri által kezdeményezett).
<p></p>
Az adatok Előfeldolgozott lett a hosszú-nyújtott alakú fürt létrehozása a tengely felé a kamera center összpontosul. A három pontra (más néven Hillas paraméterek) jellemzőit megkülönböztetés használható kép paraméterek közé tartoznak.
<p></p>
<b>Használat:</b> Előrejelezheti, hogy mulatni képe jelöl jel vagy a háttérben zaj.
<p></p>
<b>Megjegyzések</b>: Egyszerű besorolás pontossága nem értelmezhető adatok, a Írisz egy háttér-esemény, mivel jel rosszabb, mint az Írisz egy jel esemény háttereként óta. Különböző deklarációkkal összehasonlításáért a ROC graph kell használni. A valószínűsége annak, egy háttér-eseményt fogad, jel egyet az alábbi küszöbértékek alá kell lenniük: 0,01, 0,02, 0,05, 0,1 vagy 0.2-es.
<p></p>
Továbbá vegye figyelembe, hogy alábecsülte van-e a háttérben futó események (hadronic zuhanyozók h) száma. A h vagy zaj osztály valós mérések események többsége jelenti. 
<p></p>
<b>Kapcsolódó Research:</b> Bock, R.K. (1995). UCI Machine Learning-tárház <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, iskolai vonatkozó információk </td>
</tr>

<tr>
  <td>Időjárás-adatkészlet</td>
  <td>
A NOAA óránkénti szárazföldi időjárási megfigyelések (<a href="http://az754797.vo.msecnd.net/data/WeatherDataset.csv">201310 201304 adatainak egyesített</a>).
<p></p>
Az időjárási adatok repülőtér időjárási állomásokon, az adott időszakban 2013. április október vonatkozó kérés érkezett megfigyelések ismerteti. Mielőtt feltöltené az Azure Machine Learning Studióban, az adatkészlet a következőképpen dolgozta:
<ul>
  <li>Időjárásjelző azonosítók megfelelő repülőtér azonosítók hozzá lettek rendelve</li>
  <li>Időjárás-állomások 70 legforgalmasabb repülőterek hozzá nem rendelt kiszűrte az</li>
  <li>A dátum oszlop külön év, hónap és nap oszlopok lett felosztva.</li>
  <li>A következő oszlopok kijelölve: AirportID, év, hónap, nap, ideje, időzóna, SkyCondition, láthatóság, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, szélsebesség, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, magasságmérő</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 Dataset</td>
  <td>
Adatok Wikipedia származik (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) S & P 500-as vállalatok, XML-adatok tárolva cikkek alapján.
<p></p>
Mielőtt feltöltené az Azure Machine Learning Studióban, az adatkészlet a következőképpen dolgozta:
<ul>
  <li>Minden egyes adott vállalat szöveges tartalom kibontása</li>
  <li>Odebrat formátování wiki</li>
  <li>Nem alfanumerikus karakterek eltávolítása</li>
  <li>A teljes szöveg átalakítása kisbetűvé</li>
  <li>Ismert vállalati kategóriák lettek hozzáadva</li>
</ul>
<p></p>
Vegye figyelembe, hogy bizonyos vállalatok egy cikk nem található, így a rekordok száma 500-nál kisebb.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Az adatkészlet tartalmaz, a válasz egy közvetlen levelezési kampányra vonatkozó jelzések és a vásárlói adatokat. Minden egyes sor az ügyfelet jelöli. Az adatkészlet tartalmaz kilenc szolgáltatások felhasználói demográfiai és múltbeli viselkedés, és harmadik felirat oszlopok (és csak látogasson el az átalakítás).  Látogasson el egy bináris oszlop, amely azt jelzi, hogy egy ügyfél ellátogat a marketingkampány után. Átalakítás azt jelzi, hogy egy ügyfél vásárolt. Költségek az összeg, hogy a rendszer.  Az adatkészlet volt rendelkezésére Kevin Hillstrom MineThatData E-Mail Analytics és Data Mining Challenge számára.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Tesztelési példák RCV1-V2 Reuters hírek adatkészlet tulajdonságai. Az adatkészlet 781K híreket, valamint a hozzájuk tartozó azonosítóik rendelkezik (az adatkészlet az első oszlop). Minden egyes cikk tokenekre bontott stopworded, és kocsány. Az adatkészlet David által elérhető történt. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Példák az RCV1-V2 Reuters hírek adatkészlet tulajdonságai. Az adatkészlet 23K híreket, valamint a hozzájuk tartozó azonosítóik rendelkezik (az adatkészlet az első oszlop). Minden egyes cikk tokenekre bontott stopworded, és kocsány. Az adatkészlet David által elérhető történt. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Adatkészlet a az évi Versenysorozatából Cup 1999 Knowledge Discovery and Data szintű adatbányászatra eszközök verseny (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Az adatkészlet letöltötte és az Azure Blob storage szolgáltatásban tárolt (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) és a tanítási és egy tesztelési is tartalmaz. A betanítási adatkészletet rendelkezik körülbelül 126K sorok és 43 oszlopok, beleértve a címkéket. Három oszlopot a címke adatai részét képezik, és 40 oszlopok, szám és karakterlánc kategorikus funkcióját, amely a modell betanításához érhetők el. A Tesztadatok körülbelül 22,5 K tesztelése a példákat, mint a betanítási adatok 43 ugyanazokat az oszlopokat tartalmaz.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Témakör-hozzárendelések RCV1-V2 Reuters hírek adatkészlet újságcikkek keresése. Hír több témakört is hozzárendelhető. Minden egyes sor formátuma "&lt;témakör neve&gt; &lt;dokumentumazonosító&gt; 1". Az adatkészlet 2,6 millió témakör hozzárendeléseket tartalmaz. Az adatkészlet David által elérhető történt. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Az adatok származási a évi Versenysorozatából Cup 2010 tanulói teljesítmény kiértékelése kihívás (<a href="http://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">tanulói teljesítmény kiértékelése</a>). A használt adatokat a Algebra_2008_2009 gyakorlókészlethez (Stamper, J., Niculescu-Mizil, A., Ritter, s, Gordon, G.J. & Koedinger, k. r. (2010). Algebra I 2008-2009. Kérdés adatkészlethez az évi Versenysorozatából Cup 2010 oktatási Data Mining kérdés. Keresse meg a <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Az adatkészlet letöltötte és az Azure Blob storage szolgáltatásban tárolt (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) és a egy diák oktatási a rendszer a naplófájlokat tartalmazza. A megadott szolgáltatásai a probléma azonosítója, és a rövid leírását, ő, időbélyegző és hány kísérletek a tanulói, a probléma megoldásához a megfelelő módon előtt. Az eredeti adathalmazból rendelkezik 8.9M rekordok; Ez az adatkészlet lett, le mintavételezés az első 100 ezer olyan sorokat. Az adatkészlet már 23 tabulátorokkal tagolt oszlopokban a különféle fenyegetési típusokat: numerikus, kategorikus, és az időbélyegző.
  </td>
</tr>

</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
