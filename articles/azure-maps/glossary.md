---
title: Azure Maps-szószedet |} A Microsoft Docs
description: A gyakran használt feltételek mellett az Azure Maps, a Location Based Services és a GIS szakkifejezéseket.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: resource
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d220fea64f860ebe5b660f7ebe5ad7db7aec4534
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368665"
---
# <a name="glossary"></a>Szószedet

Az Azure Maps használt általános szavak listáját a következő:

## <a name="a"></a>A

<a name="address-validation"></a> **Cím érvényesítési**: ellenőrzése egy cím létezik-e.

<a name="advanced-routing"></a> **Fejlett útválasztást**: közúti útválasztási adatok, például kiszámítása elérhető tartományok (izokrón), amelyben tudatjuk a felhasználókkal műveleteket végrehajtó szolgáltatások gyűjteménye mátrixok távolság, valamint a kötegelt átirányíthatja a kéréseket.

<a name="aerial-imagery"></a> **Légifelvételes képanyag**: lásd: [képanyag műholdas](#satellite-imagery). 

<a name="along-a-route-search"></a> **Egy útvonal keresési mentén**: A térbeli lekérdezés, amely keres, amely egy megadott detour vagy távolságra útvonal elérési úton lévő adatokat.

<a name="altitude"></a> **Magasság**: A magasság vagy függőleges kiterjesztését egy referencia-felületen felett. Magasság mérések egy adott referencia datum, például a mean tengerszint alapulnak. Lásd még: jogosultságszint-emelési.

<a name="ambiguous"></a> **Nem egyértelmű**: az adatok besorolását, hogy ha egy objektum megfelelően rendelt két vagy több értéket egy adott attribútum létezik bizonytalanság állapotának bekéréséhez. Például ha geokódolás "CA" két nem egyértelmű eredménye; "Kanada" és "California", "CA" minden ország és állami kódot jelölik. 

<a name="annotation"></a> **Jegyzet**: szöveg vagy képek a térképen megjelenített információt nyújt a felhasználó számára. Jegyzet előfordulhat, hogy azonosíthatja egy adott térkép entitás leírása, egy adott területre kapcsolatos általános adatok megadása a térképen, vagy maga a térkép információkat adhat meg.

<a name="antimeridian"></a> **Antimeridian**: más néven a 180<sup>th</sup> délkörének az a pont, ahol-180 fok és hosszúság 180 fokos felel meg. Ez az ellentétes a szélességi jelöl.

<a name="application-programming-interface-api"></a> **Alkalmazásprogramozási felületet (API)**: egy szabvány, amely lehetővé teszi a fejlesztők számára az alkalmazások.

<a name="api-key"></a> **API-kulcs**: az Azure Maps kulcs megtekintéséhez.

<a name="area-of-interest-aoi"></a> **Az érdeklődés (AOI) területen**: egy adatbázis- vagy egy térkép termelési fókuszterület definiálására használatos a mértékben.

<a name="asset-tracking"></a> **Nyomon követése**: folyamatának nyomon követése egy eszköz, például olyan személy, jármű vagy más objektum helyét.

<a name="asynchronous-request"></a> **Aszinkron kérés**: egy HTTP-kérelmet, amely megnyit egy kapcsolatot, és a kérést küld a kiszolgáló, amely visszaadja az aszinkron kérés azonosítója, majd lezárja a kapcsolatot. A kiszolgáló továbbra is feldolgozni a kérést, és a felhasználó ellenőrizheti az azonosító segítségével. Ha a kérelem feldolgozása befejeződött, a felhasználói és a letöltés a válasz. Az ilyen típusú kérelem szolgál hosszú ideig futó folyamatok.

<a name="autocomplete"></a> **Az automatikus kiegészítés**: egy alkalmazásban egy szolgáltatás előrejelzi a többi, a felhasználó beírja van szó. 

<a name="autosuggest"></a> **Automatikus kiegészítés**: egy alkalmazásban egy szolgáltatás a előrejelzi a Mi a felhasználó beírja logikai lehetőségeit.

<a name="azure-location-based-services-lbs"></a> **Az Azure hely Based Services (LBS)**: amikor előzetes verzióban elérhető volt az Azure Maps korábbi neve.

<a name="azure-maps-key"></a> **Az Azure Maps kulcs**: az Azure Maps kulcs egy egyedi karakterlánccá, amellyel a felhasználó az Azure Maps alkalmazás vagy a szolgáltatási kérelem hitelesítéséhez. 

## <a name="b"></a>B

<a name="base-map"></a> **Alaptérképet**: háttér referenciainformációk, például utak arcrész és határai megjelenítő térkép alkalmazás részét.

<a name="batch-request"></a> **Kötegelt kérelem**: több kérés ötvözéséhez egyetlen.

<a name="bearing"></a> **Szem előtt**: egy pont egy másik pont viszonyítva vízszintes irányát. Ez fejezi ki szöget északi, a 0 – 360-fokos elforgatása jobbra irányba fokkal viszonyítva. 

<a name="boundary"></a> **Határ**: egy vonal- vagy sokszög megadhat, az egymás melletti politikai entitások, például az országok, körzetben és tulajdonságait. A határok, amely nem mindig a fizikai szolgáltatások, például folyókat, hegyekbe vagy falak, vagy egy sor.

<a name="bounds"></a> **Rozsah**: lásd: [Bounding box](#bounding-box).

<a name="bounding-box"></a> **Határolókeret**:-készlet koordináták képviseli egy négyszögletes területet a térképen. 

## <a name="c"></a>C

<a name="cadastre"></a> **Ingatlan**: regisztrált föld és tulajdonságai egy rekordot. Lásd még: [csomagonkénti](#parcel).

<a name="camera"></a> **Kamera**: egy interaktív térkép vezérlőelem kontextusában a fényképezőgép határozza meg a maps mező meg. A nézőpont a kamera több leképezési paraméterek; alapján határozza meg központ, nagyítási szintjét, lényegét, szem előtt. 

<a name="centroid"></a> **Középpont –**: funkció geometriai közepén. A középpont – egy vonal a középponti lenne, amíg a sokszög középpontját lenne a középső terület.

<a name="choropleth-map"></a> **Térkép Choropleth**:, amelyben a következő területekre árnyékolt arányosan folyamatban van a térképen megjelenített statisztikai változó mérés tematikus térképet. Ha például igazodóvá a határ az Egyesült Államok egyes az összes többi részére relatív sokaság alapján.

<a name="concave-hull"></a> **Konkáv hajótest**: egy lehetséges konkáv geometry, amely a megadott adatkészletben alakzatokhoz elfedi jelölő alakzat. A létrehozott alakzat alkalmazásburkoló az adatokat a Zsugorfólia, és ezután fűtés, hasonló, ami miatt a nagy kiterjedő között cave egyéb adatpont felé mutat.

<a name="consumption-model"></a> **Használati modell**: a sebesség, amellyel a jármű használ fel, ösztönzésének vagy az elektromos áram meghatározó adatok. További tájékoztatás a [fogyasztási modell dokumentáció](consumption-model.md).

<a name="control"></a> **Vezérlő**: egy önálló vagy újrafelhasználható összetevő egy grafikus felhasználói felület, amely meghatározza a felület viselkedéseinek készlete áll. Például a térkép vezérlőelem alapvetően a felhasználói felület, amely egy olyan interaktív térkép betölti a része.

<a name="convex-hull"></a> **Domború hajótest**: domború hajótest a minimális domború geometry, amely a megadott adatkészletben alakzatokhoz elfedi jelölő alakzat. A létrehozott alakzat körül az adatkészlet egy rugalmas sávon kívüli alkalmazásburkoló hasonlít.

<a name="coordinate"></a> **Koordinálása**: a szélességi és hosszúsági értékeket képviselő egy helyét egy térképen áll.

<a name="coordinate-system"></a> **Koordináta-rendszerére**: definiálására használatos pontoknak a térköz a két vagy három dimenzió hivatkozási keretrendszert.

<a name="country-code"></a> **Országkód:**: egy ország egyedi azonosítója az ISO szabvány alapján. ISO2 egy ország (például USA), mely ISO3 jelöli három karakteres kód (például Egyesült Államok) két karakterből álló kódot.

<a name="country-subdivision"></a> **Ország felosztása**: gyakran nevezik állam vagy megye országot, első szintű része.

<a name="country-secondary-subdivision"></a> **Ország másodlagos felosztása**: egy második szintű felosztása egy országot, megyét gyakran nevezik.

<a name="country-tertiary-subdivision"></a> **Ország harmadlagos felosztása**: egy harmadik szintű felosztása egy országot, általában egy ward például elnevezett területre.

<a name="cross-street"></a> **Határokon street**: egy pont, ahol a két vagy több utcák Metszés.

<a name="cylindrical-projection"></a> **Hengervetületet**: leképezés, amely púpja vagy alakzatot tangens vagy szekáns meghatározása henger sphere pontok alakítja át. Ezután az első aljára szeletelt és sík simítva a 3D henger.

## <a name="d"></a>D

<a name="datum"></a> **Datum**: A mérési rendszer hivatkozási előírások, a koordináta rendszerek Bűvös négyzet (függőleges datum) felülettel alatt vagy egy felületre (vízszintes datum) vagy a fenti magasságát.

<a name="dbf-file"></a> **A DBF-fájl**: egy adatbázis fájlformátum, amely a Alakzatfájlok (SHP) együttes alkalmazásával.

<a name="degree-minutes-seconds-dms"></a> **Párhuzamossági perc, másodperc (DMS)**: a szélességi és hosszúsági leíró mértékegysége. Olyan mértékű 1/360<sup>th</sup> egy kör. Olyan mértékű tovább oszthatók 60 perc, és a egy perc van felosztva 60 másodperc.

<a name="delaunay-triangulation"></a> **Delaunay keresztárfolyam**: összefüggő, nem átfedő háromszögek háló létrehozásához egy adatkészletből pontok technika. Minden egyes háromszög circumscribing kör nincs pontokat az adatkészletből a saját belső tartalmaz.

<a name="demographics"></a> **Demográfiai**: A statisztikai jellemzői (például az életkor, a születési arányra vonatkozó és a bevétel) egy népességi.

<a name="destination"></a> **Cél**: A záró pontot vagy a helyét, amelyben valaki utazik.

<a name="digital-elevation-model-dem"></a> **Digitális jogosultságszint-emelési modell (DEM-)**: jogosultságszint-emelési értékek adatkészlet kapcsolódó felületre, rögzített egy közös datum használatával rendszeres időközönként terület fölé. DEMs jellemzően terep mentesség ábrázolására használják.

<a name="dijkstra's-algorithm"></a> **A Dijkstra algoritmus**: egy olyan algoritmust, amely megvizsgálja a két pont közötti legrövidebb útvonal megkeresése, a hálózati kapcsolatát.

<a name="distance-matrix"></a> **Távolsági mátrix**: olyan mátrixa, amely az utazási idő- és tartalmaz távolság források és a célhelyek között. 

## <a name="e"></a>E

<a name="elevation"></a> **Jogosultságszint-emelési**: A függőleges távolság alatt egy referencia-felület vagy datum (általában mean tengerszint) vagy egy pontot vagy a fenti objektumok közül. Jogosultságszint-emelési általában a föld magasságát hivatkozik.

<a name="envelope"></a> **Burkológörbe**: lásd: [Bounding box](#bounding-box).

<a name="extended-postal-code"></a> **Bővített irányítószámot**: egy postai irányítószám, amely további információkat tartalmaznak. Például az USA-ban irányítószámok öt számjegyeket tartalmazhat, de egy bővített irányítószámot zip + 4, más néven további négy számjegy tartalmazza. Ezek a további számjegyeket egy földrajzi szegmens az öt számjegyű kézbesítési területén, például a város blokkolás, bérbe csoportja vagy egy post office jelölőnégyzetet, amely a hatékony mail rendezési és kézbesítési azonosítására szolgál.

<a name="extent"></a> **Mértékben**: lásd: [Bounding box](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Az összevont hitelesítés**:, amely lehetővé teszi egy egyszeri bejelentkezési és hitelesítési mechanizmust, amely több webes és mobilalkalmazásokban használható hitelesítési módszert. 

<a name="feature"></a> **A szolgáltatás**: olyan objektum, amely egy geometriai egyesítenek egy további metaadat-információkat. 

<a name="feature-collection"></a> **Gyűjtemény funkció**: a szolgáltatás objektumok gyűjteményét.

<a name="find-along-route"></a> **Útvonalon található**: A térbeli lekérdezés, amely keres, amely egy megadott detour vagy távolságra útvonal elérési úton lévő adatokat.

<a name="find-nearby"></a> **Keresés a közelben**: A térbeli lekérdezés keresési pontról lineáris rögzített távolság (mivel a mélyen van).

<a name="fleet-management"></a> **Adatbázisflotta felügyeleti**: például autók, látható, részeként szerezhető be és síkok haszongépjárművekre kezelését. Flottakezelés széles körű funkciókat, például a vehicle finanszírozási, karbantartási, telematika (nyomkövetési és diagnosztikai), valamint illesztőprogram, gyorsabban, üzemanyag, és az egészségügyi és biztonsági felügyeleti is tartalmazhat. Flottakezelés egy olyan vállalatok, akik szállítási támaszkodnak az üzleti minimalizálása érdekében a kockázatokat, és csökkentheti szállítási és a személyzet költségeit, közben biztosítva állami jogszabályok betartása által használt folyamat.

<a name="free-flow-speed"></a> **Ingyenes flow sebesség**: ideális körülmények között várt ingyenes flow sebessége. Általában a sebességkorlátozás.

<a name="free-form-address"></a> **Szabad formátumú címet**: A teljes címet, amely egy egysoros szöveg jelöli.

<a name="fuzzy-search"></a> **Intelligens keresést**: keresés, amely egy szabad formátumú szöveges karakterláncot, esetleg egy cím vagy a hasznos helyekre vezető útvonalak. 

## <a name="g"></a>G

<a name="geocode"></a> **Geocode**:-címet vagy a helyre, amely egy adott helyen a térképen megjeleníthető használható koordináta lett alakítva. 

<a name="geocoding"></a> **Geokódolás**: előre geokódolás néven is ismert, a rendszer helyadatok cím átalakítása koordinátáit. 

<a name="geodesic-path"></a> **Érintő geodetikus elérési**: A legrövidebb útvonal görbe felületen két pont között. Az Azure Maps meg, ha az elérési út miatt a Mercator vetületet görbe vonalként jelenik meg.

<a name="geofence"></a> **Geokerítésen**: egy meghatározott földrajzi régióban, amely kerül, vagy a régiót létezik eseményeinek indításához használható.

<a name="geojson"></a> **A GeoJSON**: általános JSON-alapú fájlformátum földrajzi vektoros adatait, például a pontokat, sorokat és poligonok tárolására szolgál. **Megjegyzés:**: egy GeoJSON, kiterjesztett verzióját használja, az Azure Maps [itt dokumentált](extend-geojson.md).

<a name="geometry"></a> **Geometriai**: például egy pont, a sor vagy a sokszög egy térbeli objektumot határozza meg.

<a name="geometrycollection"></a> **GeometryCollection**: geometry objektumok gyűjteményét.

<a name="geopol"></a> **GeoPol**: geopolitikai bizalmas adatokat, például vitatott szegélyek és helynevek hivatkozik.

<a name="georeference"></a> **Viszonyításához**: A földrajzi adatok vagy az ismert koordináta-rendszerére képanyag igazítása folyamatán. Ez a folyamat időeltolású, elforgatása, méretezése vagy az adatok eltorzítják állhat.

<a name="georss"></a> **GeoRSS**: az XML-bővítmény térbeli adatok felvétele az RSS-hírcsatornák.

<a name="gis"></a> **GIS**: "GIS" mozaikszó. A leképezés iparágban használt általános kifejezés.

<a name="gml"></a> **GML**: Geography Markup Language néven is ismert. Térbeli adatok tárolására szolgáló XML fájl kiterjesztéssel.

<a name="gps"></a> **GPS**: globális helymeghatározási rendszer néven is ismert, szatellit egy eszköz irányú pozícióját a föld meghatározására szolgáló rendszer. A föld körül keringő szatellit azt jelzi, hogy lehetővé teszik a GPS-vevők bárhol föld alapján számítja ki a saját helyét trilateration keresztül továbbítja.

<a name="gpx"></a> **GPX**: GPS csereformátumként néven is ismert, egy XML-fájl formátuma általában létre GPS-eszközökről.  

<a name="great-circle-distance"></a> **Nagy-kör távolság**: A legrövidebb távolság a Surface, a sphere két pont között.

<a name="greenwich-mean-time-gmt"></a> **(GMT) greenwichi középidő szerint**: a szélességi, amely futtatja a Királyi megfigyelő az Angliai Greenwichen keresztül ideje.

<a name="guid"></a> **GUID**: egy globálisan egyedi azonosítóját. Egy felületet, osztályban, típustár, összetevő kategória vagy rekord egyedi azonosításához használt karakterlánc.

## <a name="h"></a>H

<a name="haversine-formula"></a> **A képlet Haversine**: egy közös egyenlet egy sphere két pontjai között nagy-kör távolság kiszámítására használt.

<a name="hd-maps"></a> **HD maps**: is nagy definíció térképek, vagyis áll éles környezetet hűen közúti hálózati információkat lane jelölések, például az aláírási és iránya lámpa autonóm a szükséges.

<a name="heading"></a> **Fejléc**: valami mutat, vagy irányuló irányát. Lásd még: [ellátott](#heading).

<a name="heatmap"></a> **Intenzitástérkép**: adatok vizuális megjelenítésének, amelyben a színskála képviseli egy bizonyos területen pontok effektus sűrűségét. Lásd még: [tematikus térkép](#thermatic-map).

<a name="hybrid-imagery"></a> **Hibrid képeken**: műholdas vagy légifelvételes képanyag, amely rendelkezik közúti adatok és a címke felett átfedésben.

## <a name="i"></a>I

<a name="iana"></a> **Az IANA**: az Internet Assigned Numbers Authority rövidítése. Egy nonprofit szervezet csoportot, amely felügyeli a globális IP-címek hozzárendelését.

<a name="isochrone"></a> **Isochrone**: egy isochrone határozza meg a nézet, amelyben valaki is utazási mód kiválasztására bármely irányban a megadott időn belül egy adott helyről. Lásd még: [elérhető tartomány](#reachable-range).

<a name="isodistance"></a> **Isodistance**: Adja meg egy olyan helyre, egy isochrone határozza meg, amelyben valaki is utazási mód kiválasztására bármely irányban a megadott távolságon belül a területen. Lásd még: [elérhető tartomány](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a> **KML**: Keyhole Markup Language néven is ismert, van egy közös XML-fájlformátummal például pontok, a sorok és poligonok földrajzi vektoros adatok tárolására. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat**: multispektrális, earth-föld körül keringő szatellit fejlesztette ki, hogy gyűjtse össze a föld például mezőgazdaság, erdészet és ennek során számos iparágban használt képanyag NASA.

<a name="latitude"></a> **Szélesség**: Az Egyenlítőtől déli iránya vagy északi fokban mért angular távolság.

<a name="level-of-detail"></a> **Részletességi szint**: lásd: [nagyítási szint](#zoom-level).

<a name="lidar"></a> **LIDAR**: világos észlelése és terjedő betűszó. Tükröző felületeken a távolságmérés Lézerrel használó távoli érzékelő módszer.

<a name="linear-interpolation"></a> **Lineáris interpolációs**: Ismeretlen érték használata az ismert értékek közötti távolságot becslése.

<a name="linestring"></a> **LineString**: egy geometriai, egy sor képviseli. Vonallánc néven is ismert. 

<a name="localization"></a> **A honosítás**: különböző nyelv és kulturális környezetek támogatása.

<a name="logistics"></a> **A logisztikai**: áttérés a személyek, járművek, készletek és eszközök szabályozott módon folyamata.

<a name="longitude"></a> **Hosszúsági**: angular távolság fokban mért kezdőszögét, az USA keleti vagy nyugati irányban szélességi.

## <a name="m"></a>M

<a name="map-tile"></a> **Képezze le a csempe**: egy téglalap alakú eloszlása egy térképen vászonalapú jelölő kép. További információkért lásd: a [nagyítási szintek és a csempe grid dokumentációja](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Jelölő**: más néven a PIN-kód vagy a rajzszög egy pont helyét egy térképen jelölő ikon.

<a name="mercator-projection"></a> **Mercator vetületet**: egy hengeres térképvetület vált, a standard szintű térképvetület hajózási célból történő természetesen a konstans egyenes vonalak, más néven vonalak jelölik miatt egyenesen a szegmensek, amely az a szög megőrzése a délkörök. Minden egybesimított térkép leképezések torzítja az alakzatok vagy méretét a térkép a föld felszínén igaz elrendezését képest. A Mercator vetületet exaggerates messze az Egyenlítőtől déli területek, úgy, hogy kisebb területek jelennek meg a térképen nagyobb, mint az oszlopok módszerrel érdemes. 

<a name="multilinestring"></a> **MultiLineString**: egy geometriai, amely egy LineString objektumok gyűjteményét képviseli. 

<a name="multipoint"></a> **A multiPoint**: egy geometriai, amely egy pont objektumok gyűjteményét képviseli.

<a name="multipolygon"></a> **MultiPolygon**: egy geometriai, amely egy sokszög objektumok gyűjteményét képviseli. Például Hawaii határait megjeleníthető minden sziget lenne vázolt sokszög- és Hawaii határait így lenne, egy MultiPolygon.

<a name="municipality"></a> **Település**: megad egy városnevet vagy város. 

<a name="municipality-subdivision"></a> **Település felosztása**: egy település, például a helyek vagy a helyi terület neve például "New York" része.

## <a name="n"></a>N

<a name="navigation-bar"></a> **Navigációs sáv**: módosításával a nagyítási szintjét, lényegét, elforgatása, illetve az alap réteg váltás térkép vezérlőit készletét.

<a name="nearby-search"></a> **Keresés közeli**: A térbeli lekérdezés keresési pontról lineáris rögzített távolság (mivel a mélyen van).

<a name="neutral-ground-truth"></a> **Semleges földön hiteles**: látható, hogy ez a beállítás azt jelöli, hogy a régió hivatalosnyelv és a helyi parancsfájlokat, ha elérhető címkék.

## <a name="o"></a>O

<a name="origin"></a> **Forrás**: egy kezdőpontja vagy helyét, amelyben egy felhasználó tagja.

## <a name="p"></a>P

<a name="panning"></a> **Pásztázás**: áttérés a térkép bármelyik irányba állandó nagyítási szint egyidejű fenntartásával folyamata.

<a name="parcel"></a> **Csomagonkénti**: föld nebo vlastnost határ egy diagram.

<a name="pitch"></a> **Lényegét**: döntési mennyisége a térkép tartalmaz a függőleges 0 leolvassa ahol ugrik fel a térkép viszonyítva.

<a name="point"></a> **Pont**: egy geometriai, amely a térképen egy egyetlen pozíciót jelöli. 

<a name="points-of-interest-poi"></a> **Hasznos helyek (KOORDINÁTÁIIG)**: egy üzleti, tereptárgyak vagy a lényeges közös helyen.

<a name="polygon"></a> **Sokszög**: egy SSD-geometriai, amely egy térkép egy területét jelöli. 

<a name="polyline"></a> **Vonallánc**: egy geometriai, egy sor képviseli. LineString néven is ismert. 

<a name="position"></a> **Pozíció**: A hosszúság, szélesség és magasság (x, y, z koordináták) pont.

<a name="post-code"></a> **Irányítószám**: lásd: [Postal code](#postal-code).

<a name="postal-code"></a> **Irányítószám**: betűk vagy számok vagy mindkettő egy meghatározott formátumban, a földrajzi területek osztani zónában történő annak érdekében, hogy egyszerűbb szolgáltatásnyújtás mail ország a postai szolgáltatás által használt sorozata.

<a name="prime-meridian"></a> **Szélességi**: 0 – fok hosszúsági képviselő hosszúsági üzletági. Általában a hosszúsági értékeknek csökkentése, amíg 180-fokos nyugati irányban útközben, és növelhető, ha a keleti irányban-180 utazik-fok. 

<a name="prj"></a> **PRJ**: az adatkészlet van egy szövegfájlt, amely gyakran a a tervezett koordináta-rendszerére kapcsolatos információkat tartalmazó Alakzatfájl fájl társul.

<a name="projection"></a> **Leképezés**: például keresztező Mercator, Albers egyenlő terület és Robinson a a térképvetület alapján tervezett koordináta-rendszerére. Ezek lehetővé teszi, hogy a föld gömbfelületű egy kétdimenziós Descartes koordináta adatsík az alakzatot a projekt maps. Tervezett koordináta rendszerek vannak más néven térkép leképezések.

## <a name="q"></a>VÁLASZOK

<a name="quadkey"></a> **Quadkey**: egy base-4 címe indexet egy csempe egy quadtree mozaik rendszeren belül. További információkért lásd: [nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md) dokumentációjában talál további információt.

<a name="quadtree"></a> **Quadtree**: olyan adatszerkezet, ahol egyes csomópontok rendelkezik pontosan négy gyermekekkel. Az Azure Maps-használt mozaik elrendezés rendszer quadtree szerkezetet használ, úgy, hogy egy felhasználó közeledik egy szinttel, mivel egyes térképcsempét bontja négy alárendelt csempék.  További információkért lásd: [nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md) dokumentációjában talál további információt.

<a name="queries-per-second-qps"></a> **Lekérdezések száma második lekérdezési (QPS)**: lekérdezések vagy az, hogy egy szolgáltatás vagy a platform egy második belüli kérelmek száma. 

## <a name="r"></a>R

<a name="radial-search"></a> **Kör alakú keresési**: A térbeli lekérdezés keresési pontról lineáris rögzített távolság (mivel a mélyen van). 

<a name="raster-data"></a> **Raszter adatok**: sorok és oszlopok (vagy a rács) rendszerezve mátrixa cellák (vagy képpontban megadva), minden cella információkat, például a hőmérséklet jelölő értéket tartalmaz. Raszter a digitális légifelvételes fényképek, a műholdak üzemeltetéséhez, a digitális képek és a beolvasott maps képanyag tartalmazza.

<a name="raster-layer"></a> **Raszter réteg**: egy mozaikréteg, amely Raszter lemezképek áll.

<a name="reachable-range"></a> **Elérhető tartomány**: elérhető számos meghatározza a, amelyben valaki utazhat egy megadott idő vagy a távolság, szállítási mód kiválasztására továbbítani a bármelyik irányba helyről-területét. Lásd még: [Isochrone](#isochrone) és [Isodistance](#isodistance).

<a name="remote-sensing"></a> **Távoli érzékelő**: A folyamat gyűjtése és a egy távolság érzékelői adatainak értelmezése.

<a name="rest-service"></a> **REST-szolgáltatás**: REST mozaikszó a Representational State Transfer rövidítése. REST-szolgáltatás URL-cím-alapú webes szolgáltatás alapszintű webalkalmazás technológiáját használja a kommunikációhoz, folyamatban van a HTTP GET és POST kéréseket a leggyakrabban használt módszerek támaszkodik. Ilyen típusú szolgáltatások általában me sokkal gyorsabb, kisebb, mint a hagyományos SOAP-alapú szolgáltatások.

<a name="reverse-geocode"></a> **Fordított geokódolással**: koordináta véve, és amely meghatározza, hogy a címét, amelyben a térképen jelöli.

<a name="reproject"></a> **Reproject**: lásd: [átalakítási](#transformation).

<a name="rest-service"></a> **REST-szolgáltatás**: Representational State Transfer rövidítése. Az architektúra decentralizált, elosztott környezetben társak közötti információcsere. REST lehetővé teszi a programok kommunikáljanak az operációs rendszer vagy a platform függetlenül Hypertext Transfer Protocol (HTTP) kérelmet küld egy egységes erőforrás-azonosító (URL), majd az adatokat a különböző számítógépekre.

<a name="route"></a> **Útvonal**: két vagy több hely, előfordulhat, hogy a további információk, például az útvonalon waypoints vonatkozó utasításokat is többek között egy elérési utat.

<a name="requests-per-second-rps"></a> **Kérelmek / másodperc (RPS)**: lásd: [lekérdezések másodpercenkénti (lekérdezési QPS)](#queries-per-second-qps). 

<a name="rss"></a> **Az RSS**: kifejezés Mozaikszava az igazán egyszerű tartalomtípus-gyűjtési, erőforrás leírása keretrendszer (RDF) hely összefoglalás vagy gazdag hely összegzése, attól függően, a forrás. Egy egyszerű, strukturált XML formátumú tartalommegosztás különböző webhelyek között. RSS-dokumentumok tartalmazzák a fő metaadatok elemek, például a szerző, dátum, cím, egy rövid leírást és egy hivatkozásra kattintva. Ezen információ segítségével a felhasználó (vagy egy RSS-közzétevő szolgáltatás) döntse el, mi anyagok érdemes további vizsgálatra.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Műholdas képanyag**: síkok és rögtön a lefelé mutató szatellit által rögzített képeken.

<a name="software-development-kit-sdk"></a> **Szoftverfejlesztői készlet (SDK)**: dokumentációt, mintakódokat és API-k használatával készíthet alkalmazásokat fejlesztő segítségével mintaalkalmazások gyűjteménye.

<a name="shapefile-shp"></a> **Alakzatfájl (SHP)**: ESRI-Alakzatfájlra néven is ismert, van egy vektoros adatok tárolási formátum a helyet, alakzat és földrajzi funkciókat attribútumai tárolásához. Egy alakzatfájl a kapcsolódó fájlokat tárolja.

<a name="spherical-mercator-projection"></a> **Gömbös Mercator vetületet**: lásd: [webes Mercator](#web-mercator). 

<a name="spatial-query"></a> **Térbeli lekérdezés**: egy olyan szolgáltatás, amely egy térbeli műveletet hajt végre felé irányuló kérések. Például egy kör alakú keresést, vagy egy útvonal keresési mentén.

<a name="spatial-reference"></a> **Térbeli referencia**: egy koordináta-alapú helyi, regionális és globális rendszer pontosan az entitások földrajzi helyének azonosításához használt. Azt határozza meg a térkép-koordinátákat kapcsolódnak helyek a való világból használt koordináta-rendszerére. Térbeli hivatkozások győződjön meg arról, hogy a különböző rétegek forrásból származó térbeli adatokat a pontos megtekintése vagy elemzése integrálható legyen-e. Az Azure Maps használja a [EPSG:3857](https://epsg.io/3857) . referenciarendszer és WGS 84 koordinálása bemeneti geometriai adatok. 

<a name="sql-spatial"></a> **Térbeli SQL**: az SQL Azure és az SQL Server 2008 és újabb beépített térbeli funkciókat jelenti. A térbeli funkciója is használható, függetlenül az SQL Server egy .NET-kódtár érhető el. További információkért lásd: a [térbeli adatok (SQL Server) dokumentációja](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) további információt.

<a name="synchronous-request"></a> **Szinkron kérések**: egy HTTP-kérés megnyit egy kapcsolatot, és a válaszra vár. Böngészők oldal tehet egyidejű HTTP-kérések számának korlátozásához. Hosszú ideig futó kérelmek szinkron több egy időben történik, ha ezt a korlátot érhető el, és kérelmek késleltetett, amíg be nem fejeződik egyik más kérelmet.

## <a name="t"></a>T

<a name="telematics"></a> **Telematika**: küldésére, fogadása és távközlési eszközökön keresztül adatok tárolására együtt, ami miatt a távoli objektumok vezérlőelem nem. 

<a name="temporal-data"></a> **A historikus adatok**: adatait, amely kifejezetten hivatkozik az idő vagy dátum. A historikus adatok hivatkozhatnak, különálló eseményekről, például a villámgyors is; objektumok, például vonatok; áthelyezése vagy ismétlődő megfigyelések, például az adatforgalom érzékelőadatok számát.

<a name="terrain"></a> **Terep**: egy adott jellemző, például homokos terep vagy hegyvidéki terep kellene földek terület.

<a name="thematic-maps"></a> **Tematikus térképek**: tematikus térkép egy egyszerű térképet kérés érkezett egy földrajzi területen kapcsolatos téma tükrözik. Az ilyen típusú térkép egy általános forgatókönyv a felügyeleti régiók, például az országok néhány mérőszám az adatok alapján kiszínezheti.

<a name="tile-layer"></a> **Mozaikréteg**: egy réteget egy folyamatos rétegbe (téglalap alakú szakaszok) csempék összegyűjtésével jelenik meg. A csempék vagy raszter csempék kép vagy vektoros csempék. Raszter csempe rétegek általában jelennek meg időben és képek egy kiszolgálón tárolja. Ez is igénybe vehet, nagy mennyiségű tárhely. Vektor csempe rétegek vannak leképezve az ügyfélalkalmazásban menet közben, így a kiszolgáló oldalán tárhellyel kapcsolatos követelmények kisebbek.

<a name="time-zone"></a> **Időzóna**: egy régióban, amely egy egységes téli idő célokra jogi, kereskedelmi vagy közösségi betartja a világon. Kövesse a határok és a felosztás országok általában időzónákban.

<a name="transaction"></a> **Tranzakció**: az Azure Maps tranzakciós licencelési modellt alkalmaz, ahol;

- Egy-egy tranzakció jön létre minden kért 15 térkép vagy felé irányuló forgalom csempék esetében.
- Egy-egy tranzakció jön létre minden egyes API-hívás az Azure Maps-Közösséghez, a szolgáltatások egyik például keresés, vagy a.

<a name="transformation"></a> **Átalakítás**: eljárás, amelynek során az adatokat a különböző földrajzi koordináta rendszerek között. Előfordulhat például, néhány adat, amely az Egyesült Királyságban rögzített, és a földrajzi koordináta-rendszerére OSGB 1936 alapján. Az Azure Maps használja a [EPSG:3857](https://epsg.io/3857) WGS84 koordináta referencia rendszer változatát. Ilyen megfelelően megjeleníteni az adatokat, akkor kell rendelkeznie a rendszer a másikra alakította át koordinátái.

<a name="traveling-salesmen-problem-tsp"></a> **Traveling eladók probléma (TSP)**: Hamiltonian kapcsolatcsoport probléma, amelyben egy értékesítő meg kell találnia a leghatékonyabb látogasson el egy sorozatát leáll, majd térjen vissza a kiindulási helyét.  

<a name="trilateration"></a> **Trilateration**: A folyamat megvizsgálja, hogy minden három pont közötti pozícióját a föld felszínén megállapodást két más pontok pont meghatározása.

<a name="turn-by-turn-navigation"></a> **Kapcsolja be kapcsolja be navigációs**: útvonal vonatkozó útmutatást tartalmaz az egyes lépések egy útvonal, a felhasználók alkalmazás megközelíti a következő műveletet.

## <a name="v"></a>V

<a name="vector-data"></a> **Vektoros adatok**: koordináta alapján pontokat, sorok vagy poligonok ábrázolt adatok.

<a name="vector-tile"></a> **Vektor csempe**: az open data specifikáció az azonos csempe rendszert használ, a térkép vezérlőelem földrajzi vektoros adatok tárolására. Lásd még: [mozaikréteg](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Járműtelemetria útválasztási problémát (VRP)**: egy osztály problémák, amelyben egy járműflotta járművek rendezett útvonalak készletét kiszámítása során beállított megkötések, figyelembe véve. Ezek a korlátozások közé tartozik többek között például a szállítási időt a windows, több útvonal kapacitás, és utazás időtartama megkötések.

<a name="voronoi-diagram"></a> **Voronoi diagram**: egy partíciót, területek vagy cellák, geometriai objektumok (általában pont funkciók) egy készlete előforduló terület. E cellák és poligonok, meg kell felelniük Delaunay háromszögek feltételeit. Minden hely egy területen belül közelebb az az objektum bekeretezze, mint bármely más objektumot a készletben. Voronoi diagramok gyakran használják a földrajzi szolgáltatások köré befolyásoló területeit ábrázolni. 

## <a name="w"></a>W

<a name="waypoint"></a> **Waypoint**: egy waypoint a megadott hosszúsági és szélességi navigációs célokra használt által meghatározott földrajzi elhelyezkedését. Gyakran használják, amelyek egy ponton keresztül valakinek navigál.

<a name="waypoint-optimization"></a> **Waypoint optimalizálási**: minimalizálása érdekében az utazási idő vagy az összes megadott waypoints áthaladását szükséges távolság waypoints készletét átrendezése folyamatán. Más néven a [Traveling eladók probléma](#traveling-salesmen-problem-tsp) vagy [jármű útválasztási problémát](#vehicle-routing-problem-vrp) optimalizálása összetettségétől függően.

<a name="web-map-service-wms"></a> **Webes térkép szolgáltatás (WMS)**: WMS az Open földrajzi Consortium (OGC) szabványa, amely meghatározza a térkép lemezkép-alapú szolgáltatások. WMS-szolgáltatások képek egy térképet, igény szerinti belül meghatározott területek adja meg. Lemezképek előre megjelenített Jelképrendszer tartalmazza, és a egy elnevezett stílusokat is előfordulhat, hogy megjelenítésre, ha a szolgáltatás által definiált.

<a name="web-mercator"></a> **Webalkalmazás-Mercator**: más néven, gömbös Mercator Vetületet a Mercator vetületet enyhe variant, egy használt elsősorban a webes leképezés programokban. Az azonos képleteket, a standard szintű Mercator vetületet használt semmibe maps használ. Azonban a webes Mercator használja a gömbös képletek minden méretezhető, mivel általában nagy méretű Mercator képezi le a képernyőn ellipszoid a leképezés. Eltérést észrevétlen a globális méretekben, de a helyi területet ellipszoid Mercator leképezi az azonos méretű igaz némileg eltérnek a maps okoz.

<a name="wgs84"></a> **WGS84**: állandók vonatkoznak a Surface helyét a térképen térbeli koordináták segítségével egy készletét. Standard a WGS84 datum lesz legnagyobb online leképezés szolgáltatók és a GPS-eszközök által használt. Az Azure Maps használja a [EPSG:3857](https://epsg.io/3857) WGS84 koordináta referencia rendszer változatát.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Z koordináta**: lásd: [magasság](#altitude). 

<a name="zip-code"></a> **Irányítószám**: lásd: [Postal code](#postal-code).

<a name="Zoom level"></a> **Nagyítási szint**: részletei, és mekkora a térkép látható szintjét határozza meg. A 0. szint ki, mert a teljes, amikor a teljes világtérkép gyakran lesz a nézetben, de ország neve és a egy vonalban például korlátozott információk jelennek meg, valamint óceán nevét. Közelebb nagyítása 17 szintre, ha a térkép jelenik meg egy adott területre néhány város blokkok közúti részletes információkkal. További információkért lásd: a [nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md) dokumentációját.

