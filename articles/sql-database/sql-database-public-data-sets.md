---
title: "Az Azure használatával nyilvános adatkészletek |} Microsoft Docs"
description: "Nyilvános adatkészletek ismerje meg, hogy prototípus segítségével, és Azure analytics szolgáltatások és -megoldások teszteléséhez."
services: sql-database
documentationcenter: 
author: douglaslMS
manager: craigg
editor: 
tags: 
ms.custom: reference
ms.assetid: 
ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 03/20/2017
ms.author: douglasl
ms.openlocfilehash: ffb8c7cb4a5337e075c69f5e74552f7ead7c7565
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>A teszteléshez és prototípusának nyilvános adatkészletek

Keresse meg az adatok, amelyekkel prototípus és tesztelési tárolási és elemzési szolgáltatások és megoldások nyilvános adatkészletek listáját.

## <a name="us-government-and-agency-data"></a>USA Kormánya és ügynökség adatok

| Adatforrás | Tudnivalók az adatokról | A fájlok |
|---|---|---|
| [Egyesült Államokbeli kormányzati adatok](https://www.census.gov/data.html) | Több mint 190,000 adatkészletek mezőgazdaság, ideértve az éghajlatból, fogyasztói, ökoszisztéma, oktatási, energia, pénzügyi, állapotát, helyi kormányzati, gyártási, tengeri, óceáni, nyilvános biztonsági és tudományos és kutatási az Egyesült Államokban | HTML, XML, CSV, JSON, Excel és sok más különböző formátumokban különböző méretű fájlok. Rendelkezésre álló adatkészletek formátum szerint szűrheti. |
| [USA nyilvántartásba adatok](https://www.census.gov/data.html) | Az Egyesült Államok sokaságát statisztikai adatait | Adatkészletek különböző formátumokban is. |
| [A föld tudományos adatok NASA](https://earthdata.nasa.gov/) | Több mint 32 000 adatok gyűjtemények mezőgazdaság, légkör, bioszféra, ideértve az éghajlatból, cryosphere, emberi dimenziók, hidroszféra, föld felületet, óceánok, sun-earth kapcsolati, és több. | Adatkészletek különböző formátumokban is. |
| [Légitársaság repülési késést és egyéb szállítására adatok](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Az Egyesült Államok Részleg a szállítására (pont) iroda szállítására statisztika (BTS) felvételek belföldi járatok időben teljesítményének üzemeltetett részéről nagy csomagazonosítóját. Összefoglaló információkat időben, a késleltetett, törölt és elterelt járatok száma megjelenik... ezen a webhelyen közzétett összegzési táblázatok." | A fájlok CSV formátumban. |
| [Forgalom kontrollcsoportban - US Fatality elemzés jelentéskészítő rendszer (FARS)](http://www.nhtsa.gov/FARS) | "FARS NHTSA, Kongresszus, így országos nyilvántartásba, és az Amerikai nyilvános végzetes sérülések éves adatok jelentkezett a lap forgalom összeomlik." | "Létrehozása a saját fatality adatait a FARS lekérdezés rendszerrel futtatható. Vagy letöltheti az összes FARS adatok 1975, hogy az FTP-helyről." |
| [Mérgező kémiai adat - EPA toxicitás ForeCaster (ToxCast™)](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "A több ezer chemicals EPA legnaprakészebb, nyilvánosan elérhető nagy átviteli toxicitás adatok. Ezek az adatok jön létre a EPA ToxCast kutatási erőfeszítéseket keresztül." | Például táblázatokat, az R csomagokat és a MySQL-adatbázis fájljainak különböző formátumokban adatkészletek érhetők el. |
| [Mérgező kémiai adatok - NIH Tox21 adatok Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "A 2014 Tox21 adatok kihívás célja segíteni kutatók chemicals és megzavarhatják mérgezést eredményezhet, melyek biológiai útjainak 21 kezdeményezi a Toxicology keresztül tesztelt vegyületek lehetőségeinek ismertetése." | Adatkészletek és is elérhetők SMILES SDF formátumban. Adatok "tevékenységek adatai és a Tox21 gyűjtemény ~ 10 000 összetevők kémiai struktúrák assay (Tox21 10-K)." |
| [A NCBI biotechnológia és genom adatait](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Több adatkészletek gének, genomok és fehérjék. | Adatkészletek szöveg, XML, NAGYOLVASZTÓ és egyéb formátumokban szerepelnek. Egy NAGYOLVASZTÓ alkalmazás érhető el. |

## <a name="other-statistical-and-scientific-data"></a>Más statisztikai és tudományos adatok

| Adatforrás | Tudnivalók az adatokról | A fájlok |
|---|---|---|
| [New York Város taxi adatok](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Taxi feljegyzések metódushoz is felvegyenek mezőket a rögzítés felvétel és Gyűjtőtár dátum/idő, felvétel és Gyűjtőtár helyek út távolság, részletezett vitel, arány típusok, fizetési típusok és illesztőprogram-jelentett utas adatokra." | Adatkészletek hónap CSV-fájlok közé. |
| [A Microsoft Research adatkészletek - "Tudományos a Research. adatokat"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Több adatkészletek számítógépes emberi beavatkozást, a hang-és videófolyamot, az adatok adatbányászati/információk beolvasása, a földrajzi /-helyet, a természetes nyelvű feldolgozási és a robotics és számítógépes látástechnológiai. | Adatkészletek különböző formátumokban letölthető zip vannak. |
| [Nyilvános genom adatok](http://www.completegenomics.com/public-data/) | "Emberi teljes genomok érdekében adatok számos használhatók szabadon nyilvános javítása érdekében minden genomikus vizsgálat..." A szolgáltató, teljes genomika, akkor a titkos nyereségorientált vállalatnak. | Adathalmazokat, kapcsolattípus kibontása után vannak UNIX text formátumban. Elemzésére szolgáló eszközöket is elérhetők. |
| [Nyissa meg tudományos adatok Felhőbeli adatát](https://www.opensciencedatacloud.org/) | "A nyitott tudományos adatok felhő biztosít a tudományos közösségi erőforrásokkal tárolás, megosztás, és adjon és petabájtnyi méretű tudományos adathalmazok elemzése."| Adatkészletek különböző formátumokban is. |
| [Globális ideértve az éghajlatból adatok - WorldcLIM](http://worldclim.org/) | "WorldClim olyan globális ideértve az éghajlatból rétegek (ideértve az éghajlatból berácsozott adatokat), körülbelül 1 km2 térbeli pontossággal. Ezen adatok használhatók a leképezési és térbeli modellezési." | Ezek a fájlok tartalmazzák a földrajzi adatok. További információk: [adatformátum](http://worldclim.org/formats1). |
| [Emberi társadalom - GDELT a projekt adatait](http://www.gdeltproject.org/data.html) | "A GDELT projekt a legnagyobb, minden részletre és legnagyobb felbontást korábban már hozott létre emberi társadalom adatbázis megnyitása." | A nyers adatok fájlok CSV formátumban. |
| [Reklám kattintson előrejelzési adatokat Criteo a gépi tanulás](http://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "A legnagyobb legalább egyszer nyilvánosan elérhető ML adatkészletet." További információk: [Criteo tartozó 1 TB-os kattintson előrejelzés Dataset](https://blogs.technet.microsoft.com/machinelearning/2015/04/01/now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [ClueWeb09 szöveg adatbányászati adatkészlet Lemur a projektben](http://www.lemurproject.org/clueweb09.php/) | "Az adatok beolvasása és a kapcsolódó emberi nyelvi technológiák kutatási támogatásához a ClueWeb09 adatkészlet lett létrehozva. Olyan karakterekből áll, körülbelül 1 milliárd weblapok 10 nyelven január és február 2009 összegyűjtött. " | Lásd: [adatkészlet adatai](http://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Online szolgáltatás adatai

| Adatforrás | Tudnivalók az adatokról | A fájlok |
|---|---|---|
| [GitHub-archívum](https://www.githubarchive.org/) | "GitHub archívum egy projekt nyilvános GitHub idővonalát [események], archiválhatja, és hogy könnyen elérhető legyen, további elemzés céljára." | Töltse le a webes ügyfél JSON-encloded esemény archívumokat .gz (Gzip) formátumban. |
| [GitHub-tevékenységek adatait a GHTorrent projektből](http://ghtorrent.org/) | "A GHTorrent projekt [] annak érdekében, hogy az adatok a Githubon REST API-n keresztül felajánlott méretezhető, queriable, kapcsolat nélküli tükör létrehozása. GHTorrent figyeli a Githubon nyilvános esemény idősorán. Az egyes eseményekhez lekérdezi a tartalmát és függőségi viszonyaikat, teljes körűen." | MySQL-adatbázis memóriaképek CSV formátumban vannak. |
| [Túlcsordulás adatok Veremkiíratás](https://archive.org/details/stackexchange) | "Ez az összes felhasználó hozzájárult tartalom a verem Exchange hálózaton [többek között a Stack Overflow] egy anonimizált memóriakép." | "[Például a Stack Overflow] helyekhez formázott egy külön archív XML-fájlokat tartalmazó zip keresztül 7 zip az bzip2 tömörítésével lehetett. Minden hely archív tartalmaz bejegyzéseket, a felhasználók, a szavazatok, a megjegyzéseket, a PostHistory és a PostLinks." |
