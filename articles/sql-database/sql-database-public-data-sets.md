---
title: Nyilvános adatkészletek az Azure-elemzéshez
description: Ismerje meg a nyilvános adatkészleteket, amelyek segítségével prototípus és teszt Az Azure analytics szolgáltatások és megoldások.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 4b1c1a963b065411f1a0ab84141bdf1835930ebb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973543"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Nyilvános adatkészletek teszteléshez és prototípus-készítéshez

A nyilvános adatkészletek ezen listájában olyan adatokat kereshet, amelyek segítségével prototípust készíthet, valamint tesztelheti a tárolási és elemzési szolgáltatásokat és megoldásokat.

## <a name="us-government-and-agency-data"></a>Az Egyesült Államok kormányának és ügynökségének adatai

| Adatforrás | Tudnivalók az adatokról | A fájlokról |
|---|---|---|
| [Az Egyesült Államok kormányának adatai](https://catalog.data.gov/dataset) | Több mint 250.000 adatkészletek, amelyek a mezőgazdaság, az éghajlat, a fogyasztók, az ökoszisztémák, az oktatás, az energia, a pénzügy, az egészségügy, a helyi önkormányzatok, a gyártás, a tengeri, óceán, a közbiztonság, valamint a tudomány és a kutatás az USA-ban. | Fájlok különböző méretű különböző formátumokban, beleértve a HTML, XML, CSV, JSON, Excel, és még sokan mások. A rendelkezésre álló adatkészleteket fájlformátum szerint szűrheti. |
| [Amerikai népszámlálási adatok](https://www.census.gov/data.html) | Statisztikai adatok az Egyesült Államok lakosságáról | Az adatkészletek különböző formátumokban vannak. |
| [Földtudományi adatok a NASA-tól](https://earthdata.nasa.gov/) | Több mint 32.000 adatgyűjtés, amely a mezőgazdaság, légkör, bioszféra, klíma, krioszféra, emberi dimenziók, hidroszféra, szárazföldi felület, óceánok, nap-föld kölcsönhatások, és így tovább. | Az adatkészletek különböző formátumokban vannak. |
| [A légi járat késése és egyéb szállítási adatok](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Az Egyesült Államok Közlekedési Minisztériumának (DOT) Közlekedési Statisztikai Hivatala (BTS) nyomon követi a nagy légi fuvarozók által üzemeltetett belföldi járatok időben történő teljesítményét. Összefoglaló információk száma a pontos, késleltetett, törölt, és eltérített járatok jelennek meg ... az ezen a honlapon közzétett összefoglaló táblázatokban." | A fájlok CSV formátumban vannak. |
| [Halálos kimenetelű közlekedési balesetek – Us Fatality Analysis Reporting System (FARS)](https://www.nhtsa.gov/FARS) | "FARS egy országos népszámlálás, amely NHTSA, kongresszus, és az amerikai nyilvános éves adatok halálos sérüléseket szenvedett a gépjármű-közlekedési balesetek." | "Hozza létre saját halálozási adatait online futva a FARS query rendszer használatával. Vagy töltse le az összes FARS adatot 1975-től az FTP-helyről." |
| [Mérgező kémiai adatok - EPA Toxicitási ForeCaster (ToxCast™) adatok](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "Az EPA legfrissebb, nyilvánosan elérhető nagy átmenő képességű toxicitási adatai több ezer vegyi anyagra. Ezeket az adatokat az EPA ToxCast kutatási erőfeszítései generálják." | Az adatkészletek különböző formátumokban érhetők el, beleértve a táblázatokat, az R csomagokat és a MySQL adatbázisfájlokat. |
| [Mérgező kémiai adatok - NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "A 2014-es Tox21 adatkihívás célja, hogy segítsen a tudósoknak megérteni a 21. | Az adatkészletek SMILES és SDF formátumban érhetők el. Az adatok "vizsgálati aktivitási adatokat és kémiai struktúrákat tartalmaznak a Tox21 ~10 000 vegyület (Tox21 10K) gyűjteményén". |
| [Biotechnológiai és genomadatok az NCBI-ből](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Több adathalmaz, amely a géneket, genomokat és fehérjéket fedi le. | Az adatkészletek szövegben, XML-ben, BLAST-ban és más formátumokban találhatók. A BLAST alkalmazás elérhető. |

## <a name="other-statistical-and-scientific-data"></a>Egyéb statisztikai és tudományos adatok

| Adatforrás | Tudnivalók az adatokról | A fájlokról |
|---|---|---|
| [New York-i taxi adatok](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "A taxiutak nyilvántartásai olyan mezőket tartalmaznak, amelyek rögzítik a felvételi és a lemorzsolódási dátumokat/időpontokat, a felvételi és leadási helyeket, az utazási távolságokat, a tételes viteldíjakat, a díjtípusokat, a fizetési típusokat és a járművezető által jelentett utasszámokat." | Az adatkészletek a CSV-fájlokban havi adatok. |
| [Microsoft Research adatkészletek - "Data Science for Research"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Több adathalmaz, amely az emberi-számítógép interakciót, az audio/video, az adatbányászat/információvisszakeresés, a térinformatikai/helymeghatározási, a természetes nyelvi feldolgozást és a robotikát/számítógépes látást tartalmazza. | Adatkészletek különböző formátumokban, cipzáras letölthető. |
| [Nyilvános genomadatok](https://www.completegenomics.com/public-data/) | "A teljes emberi genomok változatos adathalmaza szabadon hozzáférhető nyilvános használatra, hogy fokozza a genomikus vizsgálatokat..." A teljes genomika i. magánvállalat. | Az adatkészletek a kibontás után UNIX szövegformátumban vannak. Elemzési eszközök is rendelkezésre állnak. |
| [Tudományos adatok megnyitása](https://www.opensciencedatacloud.org/projects/) | "Az Open Science Data Cloud a tudományos közösség számára erőforrásokat biztosít a terabájtos és petabájtos tudományos adatkészletek tárolásához, megosztásához és elemzéséhez."| Az adatkészletek különböző formátumokban vannak. |
| [Globális éghajlati adatok - WorldClim](https://worldclim.org/) | "WorldClim egy sor globális éghajlati rétegek (rácsos éghajlati adatok) a térbeli felbontása körülbelül 1 km2. Ezek az adatok térképezésre és térbeli modellezésre használhatók." | Ezek a fájlok térinformatikai adatokat tartalmaznak. További információ: [Data format](https://worldclim.org/formats1). |
| [Adatok az emberi társadalomról - A GDELT projekt](https://www.gdeltproject.org/data.html) | "A GDELT Projekt a valaha létrehozott legnagyobb, legátfogóbb és legnagyobb felbontású nyílt adatbázis az emberi társadalomszámára." | A nyers adatfájlok CSV formátumúak. |
| [A Criteo gépi tanulásának hirdetési kattintási előrejelzései](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "A valaha megjelent legnagyobb, nyilvánosan kiadott ML-adatkészlet." További információ: [Criteo 1 TB-os kattintási adatkészlete.](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/) | |
| [ClueWeb09 szöveg bányászati adatkészlet a Lemur Project](https://www.lemurproject.org/clueweb09.php/) | "a ClueWeb09 adathalmaz volt teremtett -hoz támogat kutatás -ra információ visszaszerzés és kapcsolódó emberi nyelv technológiai. Ez áll körülbelül 1 milliárd weboldalak 10 nyelven gyűjtött januárban és februárban 2009." | Lásd: [Adatkészlet-információk](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Online szolgáltatás adatai

| Adatforrás | Tudnivalók az adatokról | A fájlokról |
|---|---|---|
| [GitHub archívum](https://www.githubarchive.org/) | "A GitHub Archive egy olyan projekt, amely rögzíti a nyilvános GitHub idővonalat [események] számára, archiválja, és könnyen hozzáférhetővé teszi további elemzés céljából." | Töltse le a JSON-kódolású eseményarchívumokat .gz (Gzip) formátumban egy webes kliensből. |
| [GitHub tevékenységadatok a GHTorrent projektből](http://ghtorrent.org/) | "A GHTorrent projekt [is] arra törekszik, hogy hozzon létre egy skálázható, lekérdezhető, offline tükör az adatok kínált a GitHub REST API-t. A GHTorrent figyeli a GitHub nyilvános eseményidő-sorát. Minden esemény esetében teljes körűen lekéri annak tartalmát és függőségeit." | A MySQL adatbázis-memóriaképek CSV formátumúak. |
| [Túlcsordulási adatkiírás verem](https://archive.org/details/stackexchange) | "Ez egy anonimizált memóriakép a Stack Exchange hálózaton lévő összes felhasználó által készített tartalomról [beleértve a Veremtúlcsordulást]." | "Minden webhely [mint például a Stack Overflow] van formázva, mint egy külön archívum, amely XML fájlok at zip keresztül zip segítségével bzip2 tömörítés. Minden webhely archívum tartalmazza hozzászólások, felhasználók, szavazatok, hozzászólások, PostHistory, és PostLinks." |
