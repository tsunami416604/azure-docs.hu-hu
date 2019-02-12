---
title: Az Azure Analytics nyilvános adatkészletek |} A Microsoft Docs
description: Nyilvános adatkészletek ismerje meg, hogy prototípus használ, és Azure elemzési szolgáltatások és -megoldások teszteléséhez.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/01/2018
ms.openlocfilehash: 8aad3cf0bfeb2f8586bca1072f3e395e196f3e0c
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098493"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Nyilvános adatkészletek a teszteléshez és prototípus-készítés

Tallózással keresse meg az adatok esetén, amelyek segítségével készíthet prototípusokat és tesztelési tárolási és analitikai szolgáltatásokat és megoldásokat nyilvános adatkészletek listájával.

## <a name="us-government-and-agency-data"></a>USA Kormányzati és ügynökség adatok

| Adatforrás | Tudnivalók az adatokról | A fájlokkal kapcsolatos |
|---|---|---|
| [Egyesült Államok kormányzati adatok](https://www.census.gov/data.html) | Mezőgazdaság, oktatásra, fogyasztói, rendszereit, education, energiát, amelyek több mint 190,000 adatkészletek pénzügyi, egészségügyi, helyi kormányzati, gyártási, hajózási, óceán, közbiztonsági megfontolásokból és adatelemzési oktatási és kutatási az Egyesült Államokban | Különböző méretű HTML, XML, CSV, JSON, az Excel és sokan mások is beleértve különböző formátumú fájlok. Rendelkezésre álló adatkészletek formátum szerint szűrheti. |
| [Egyesült Államok népszámlálási adatok](https://www.census.gov/data.html) | A US kapcsolatos statisztikai adatokat | Adatkészletek többféle formátumban vannak. |
| [Earth science data from NASA](https://earthdata.nasa.gov/) | Mezőgazdaság, légköri, bioszféra, oktatásra, cryosphere, emberi dimenziók, hidroszféra, föld surface, tenger, sun-earth interakciók, és további több mint 32 000 adatok gyűjteményeket. | Adatkészletek többféle formátumban vannak. |
| [A járatok késésének légitársaság és egyéb szállítási adatok](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Az Egyesült Államok Minisztériumának a szállítás statisztika (BTS) számok belföldi repülőjáratok időben teljesítményének üzemeltetett részéről nagy Hivatala (pont). Összefoglaló információkat az idő, késleltetett, törölt és forgalomelterelés repülőjáratok száma jelenik meg... a webhelyen közzétett összegzési táblázatok." | CSV-formátumú fájlok. |
| [Forgalom kontrollcsoportban - USA Fatality elemzési Reporting System (FARS)](http://www.nhtsa.gov/FARS) | "FARS NHTSA, kongresszusa, így egy országos népszámlálási és az American nyilvános éves adatokat végzetes sérülések kapcsolatban érte a gépjármű forgalom szoftverleállások." | "Létrehozása a FARS lekérdezés rendszer használatával futtassa online fatality adatainak Ön a tulajdonosa. Vagy az összes FARS adatokat letölteni az FTP-hely a nyújtjuk 1975." |
| [Toxikus kémiai adat - EPA toxicitás ForeCaster (ToxCast™)](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "Chemicals ezer EPA legtöbb frissíthetők, a nyilvánosan elérhető nagy átviteli sebességű toxicitás adatok. Ezek az adatok jön létre a EPA ToxCast kutatási erőfeszítéseket keresztül." | Adatkészletek különböző formátumúak, beleértve a táblázatok, R-csomagok és MySQL-adatbázis fájlok érhetők el. |
| [Toxikus kémiai adatok - NIH Tox21 adatok Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "A 2014 Tox21 adatok kihívás célja a chemicals és keresztül a Toxicology biológiai átvételére mérgezést eredményezhet módon akadályozza a 21-kezdeményezéshez a tesztelt vegyületek tisztában az adatszakértők." | Adatkészletek érhetők el a SMILES és SDF formátumokat. Az adatok biztosít "-tevékenységi adatait és a ~ 10 000 összetevők Tox21 gyűjtemény kémiai struktúrák assay (Tox21 10K)." |
| [A NCBI biotechnológia és a genome adatait](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Több adatkészletek gének, a genomok díját és a fehérjék. | Adatkészletek szöveg, XML, NAGYOLVASZTÓ és más formátumban vannak. Egy NAGYOLVASZTÓ alkalmazás érhető el. |

## <a name="other-statistical-and-scientific-data"></a>Egyéb statisztikai és tudományos adatok

| Adatforrás | Tudnivalók az adatokról | A fájlokkal kapcsolatos |
|---|---|---|
| [New York City-i taxik adatait](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Taxi trip rekordok tartalmazzák a mezők rögzítése felvétel és dropoff dátumok vagy időpontok, felvétel és dropoff helyek trip távokat, tételes vitel, forgalmi típusok, fizetési típusait, és utasforgalmat illesztőprogram jelentett száma." | CSV-fájlok találhatók adatkészletek hónap szerint. |
| [A Microsoft Research adatkészletek – "Data Science a kutatási"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Több adatkészletek ember és számítógép-interakció, hang/kép, adatbányászati/információk adatlekérés, a földrajzi helye, természetes nyelvi feldolgozás és robotika/számítógépes látástechnológiai kiterjedő. | Adatkészletek letölthető zip többféle formátumban vannak. |
| [Nyilvános genom adatok](http://www.completegenomics.com/public-data/) | "Adatok kezébe a különböző emberi teljes genomok díját is szabadon nyilvánosan elérhető bármely részfeladatának tanulmány javítása..." A szolgáltató, a teljes Genomics egy privát profitorientált corporation. | Adatkészletek, miután kinyerési, a program UNIX szöveges formátumban. Elemzési eszközök is érhetők el. |
| [Nyissa meg tudományos adatok Felhőbeli adatok](https://www.opensciencedatacloud.org/) | "Nyílt tudományos adatok Felhőbeli biztosítja a tudományos Közösség, az erőforrások tárolásához, megosztása és terabájt, petabájtszintű és tudományos adatkészletek elemzése."| Adatkészletek többféle formátumban vannak. |
| [Globális hőmérséklet-adatok – WorldClim](http://worldclim.org/) | "WorldClim olyan készlete, globális éghajlatváltozás rétegek (berácsozott éghajlati adatokat), körülbelül 1 km2 térbeli pontossággal. Ezen adatok használhatók a leképezési és a térbeli modellezési." | Ezek a fájlok tartalmazzák a földrajzi adatok. További információ: [adatformátum](http://worldclim.org/formats1). |
| [Emberi társadalom – a GDELT projekt adatait](http://www.gdeltproject.org/data.html) | "A GDELT projekt a legnagyobb, legteljesebb és legnagyobb felbontást emberi társadalom minden eddiginél létrehozott adatbázis megnyitása." | A nyers adatok fájlok CSV formátumban. |
| [Hirdetés kattintson a machine Learning Criteo az előrejelzési adatok](http://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "A legnagyobb minden eddiginél nyilvánosan elérhető gépi Tanulási adatkészlet." További információ: [Criteo az 1 TB-os kattintson előrejelzési adatkészlet](https://blogs.technet.microsoft.com/machinelearning/2015/04/01/now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [ClueWeb09 szöveg adatbányászati adatkészlet Lemur a projektből](http://www.lemurproject.org/clueweb09.php/) | "A ClueWeb09 adatkészlet létrehozása kutatás támogatására információk lekéréséhez és a kapcsolódó emberi nyelvi technológiákról. Folyamatautomatizálást körülbelül 1 milliárd weblapok 10 nyelven a január és február 2009 összegyűjtött." | Lásd: [adatkészlet adatai](http://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Online szolgáltatás adatai

| Adatforrás | Tudnivalók az adatokról | A fájlokkal kapcsolatos |
|---|---|---|
| [GitHub-archívum](https://www.githubarchive.org/) | "GitHub archív érhető el nyilvános GitHub idővonalát [események] rögzítésére, archiválhatja, és győződjön meg arról, hogy könnyen elérhető további elemzés céljából." | Töltse le a webes ügyfél JSON-kódolású esemény archívumok .gz (Gzip) formátumban. |
| [A GHTorrent projekt GitHub-tevékenység adatainak](http://ghtorrent.org/) | "A GHTorrent projekt [a] annak érdekében, hogy hozzon létre egy skálázható, lekérdezhető, offline tükrözött az adatok a GitHub REST API-n keresztül érhető el. GHTorrent figyeli a GitHub nyilvános esemény idősorán. Az egyes eseményekhez lekéri a tartalmát, és azok függőségeit, teljes körűen." | MySQL-adatbázis memóriaképek CSV formátumban vannak. |
| [Stack Overflow adatok memóriakép](https://archive.org/details/stackexchange) | "Ez a felhasználó által biztosított összes tartalom, a Stack Exchange hálózaton [többek között a Stack overflow-n] egy anonimizált memóriakép." | "[Például a Stack overflow-n] minden hely formázott XML-fájlok, amelyek egy külön archív zip-n keresztül 7-zip bzip2 tömörítéssel. Minden hely archív tartalmaz bejegyzések, a felhasználók, a szavazatok, a megjegyzéseket, a PostHistory és a PostLinks." |
