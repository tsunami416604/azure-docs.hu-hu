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
ms.openlocfilehash: 3b8af6f3f91c54c78cc3b277068272c27055e4aa
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816883"
---
# <a name="glossary"></a>Szószedet

Az Azure Maps használt általános szavak listáját a következő:

## <a name="a"></a>A

<a name="address-validation"></a> **Cím érvényesítési**: A folyamat egy cím létezik-e ellenőrzésére.

<a name="advanced-routing"></a> **Fejlett útválasztást**: Közúti útválasztási adatok, például kiszámítása elérhető tartományok (izokrón), a distance mátrixokban és a kérelmeket a batch használatával speciális műveleteket végrehajtó szolgáltatások gyűjteménye.

<a name="aerial-imagery"></a> **Légifelvételes képanyag**: Lásd: [képanyag műholdas](#satellite-imagery). 

<a name="along-a-route-search"></a> **Egy útvonal keresési mentén**: A térbeli lekérdezés, amely keres, amely egy megadott detour vagy távolságra útvonal elérési úton lévő adatokat.

<a name="altitude"></a> **Magasság**: A magasság vagy függőleges kiterjesztését egy referencia-felületen felett. Magasság mérések egy adott referencia datum, például a mean tengerszint alapulnak. Lásd még: jogosultságszint-emelési.

<a name="ambiguous"></a> **Nem egyértelmű**: Az adatok besorolását, hogy ha egy objektum megfelelően rendelt két vagy több értéket egy adott attribútum létezik bizonytalanság állapotát. Például ha geokódolás "CA" két nem egyértelmű eredménye; "Kanada" és "California", "CA" minden ország és állami kódot jelölik. 

<a name="annotation"></a> **Jegyzet**: Szöveg vagy a kép jelenik meg a térképen az információt nyújt a felhasználó számára. Jegyzet előfordulhat, hogy azonosíthatja egy adott térkép entitás leírása, egy adott területre kapcsolatos általános adatok megadása a térképen, vagy maga a térkép információkat adhat meg.

<a name="antimeridian"></a> **Antimeridian**: Más néven a 180<sup>th</sup> délkörének az a pont, ahol-180 fok és hosszúság 180 fokos felel meg. Ez az ellentétes a szélességi jelöl.

<a name="application-programming-interface-api"></a> **Alkalmazás programozási felületét (API)**: A szabvány, amely lehetővé teszi a fejlesztők számára az alkalmazások.

<a name="api-key"></a> **API-kulcs**: Tekintse meg az Azure Maps-kulcsot.

<a name="area-of-interest-aoi"></a> **(AOI) érdeklődési körét**: Egy adatbázis- vagy egy térkép termelési fókuszterület definiáló mértékben.

<a name="asset-tracking"></a> **Nyomon követése**: Egy eszköz, például olyan személy, jármű vagy más objektum helyét követési folyamata.

<a name="asynchronous-request"></a> **Aszinkron kérés**: HTTP-kérést, amely megnyit egy kapcsolatot, és a egy kérelmet küld a kiszolgálóra, amely visszaadja az aszinkron kérés azonosítója, majd lezárja a kapcsolatot. A kiszolgáló továbbra is feldolgozni a kérést, és a felhasználó ellenőrizheti az azonosító segítségével. Ha a kérelem feldolgozása befejeződött, a felhasználói és a letöltés a válasz. Az ilyen típusú kérelem szolgál hosszú ideig futó folyamatok.

<a name="autocomplete"></a> **Az automatikus kiegészítés**: Egy szolgáltatás, az alkalmazások előrejelzi a többi, a felhasználó beírja van szó. 

<a name="autosuggest"></a> **Automatikus kiegészítés**: Az alkalmazás egyik szolgáltatása a előrejelzi a Mi a felhasználó beírja logikai lehetőségeit.

<a name="azure-location-based-services-lbs"></a> **Az Azure Location Based Services (LBS)**: Ha az előzetes verzióban elérhető volt az Azure Maps korábbi neve.

<a name="azure-maps-key"></a> **Az Azure Maps kulcs**: Az Azure Maps kulcs egy egyedi karakterlánccá, amellyel a felhasználó az Azure Maps alkalmazás vagy a szolgáltatási kérelem hitelesítéséhez. 

## <a name="b"></a>B

<a name="base-map"></a> **Alaptérképet**: Háttérben futó referenciainformációk, például utak arcrész és határai megjelenítő térkép alkalmazás részét.

<a name="batch-request"></a> **Kötegelt kérelem**: Több kérés kombinálásával egyetlen folyamat.

<a name="bearing"></a> **Szem előtt**: A vízszintes iránya egy pontot egy másik pont viszonyítva. Ez fejezi ki szöget északi, a 0 – 360-fokos elforgatása jobbra irányba fokkal viszonyítva. 

<a name="boundary"></a> **Határ**: Egy vonal- vagy sokszög megadhat, az egymás melletti politikai entitások, például az országok, körzetben és tulajdonságait. A határok, amely nem mindig a fizikai szolgáltatások, például folyókat, hegyekbe vagy falak, vagy egy sor.

<a name="bounds"></a> **Rozsah**: Lásd: [Bounding box](#bounding-box).

<a name="bounding-box"></a> **Határolókeret**: Egy négyszögletes területet a térképen képviseli koordináták készlete. 

## <a name="c"></a>C

<a name="cadastre"></a> **Ingatlan**: Egy rekord regisztrált föld és tulajdonságait. Lásd még: [csomagonkénti](#parcel).

<a name="camera"></a> **Kamera**: Egy interaktív térkép vezérlőelem kontextusában a fényképezőgép határozza meg a maps mező meg. A nézőpont a kamera több leképezési paraméterek; alapján határozza meg központ, nagyítási szintjét, lényegét, szem előtt. 

<a name="centroid"></a> **Középpont –**: Egy szolgáltatás geometriai közepén. A középpont – egy vonal a középponti lenne, amíg a sokszög középpontját lenne a középső terület.

<a name="choropleth-map"></a> **Térkép Choropleth**: Tematikus térkép, amelyben a következő területekre árnyékolt folyamatban van a térképen megjelenített statisztikai változó mérés arányosan. Ha például igazodóvá a határ az Egyesült Államok egyes az összes többi részére relatív sokaság alapján.

<a name="concave-hull"></a> **Konkáv hajótest**: Egy lehetséges konkáv geometry, amely a megadott adatkészletben alakzatokhoz elfedi jelölő alakzat. A létrehozott alakzat alkalmazásburkoló az adatokat a Zsugorfólia, és ezután fűtés, hasonló, ami miatt a nagy kiterjedő között cave egyéb adatpont felé mutat.

<a name="consumption-model"></a> **Használati modell**: A sebesség, amellyel a jármű használ fel, ösztönzésének vagy az elektromos áram meghatározó adatok. További tájékoztatás a [fogyasztási modell dokumentáció](consumption-model.md).

<a name="control"></a> **Vezérlő**: Egy önálló vagy újrafelhasználható összetevő egy grafikus felhasználói felület, amely meghatározza a felület viselkedéseinek készlete áll. Például a térkép vezérlőelem alapvetően a felhasználói felület, amely egy olyan interaktív térkép betölti a része.

<a name="convex-hull"></a> **Domború hajótest**: Domború hajótest is a minimális domború geometry, amely a megadott adatkészletben alakzatokhoz elfedi jelölő alakzat. A létrehozott alakzat körül az adatkészlet egy rugalmas sávon kívüli alkalmazásburkoló hasonlít.

<a name="coordinate"></a> **Koordinálása**: A szélességi és hosszúsági értékeket képviselő egy helyét egy térképen áll.

<a name="coordinate-system"></a> **Koordináta-rendszerére**: Referencia-keretrendszer definiáló pontoknak két vagy három dimenziók a területen.

<a name="country-code"></a> **Országkód:**: Egy országot, ISO szabvány alapján egyedi azonosítója. ISO2 egy ország (például USA), mely ISO3 jelöli három karakteres kód (például Egyesült Államok) két karakterből álló kódot.

<a name="country-subdivision"></a> **Ország felosztása**: Az első szintű felosztása, ország, állam vagy megye gyakran nevezik.

<a name="country-secondary-subdivision"></a> **Ország másodlagos felosztása**: A második szintű felosztása egy országot, megyét gyakran nevezik.

<a name="country-tertiary-subdivision"></a> **Ország harmadlagos felosztása**: Egy harmadik szintű felosztása egy országot, például egy ward általában egy elnevezett területen.

<a name="cross-street"></a> **Határokon street**: Ha két vagy több utcák metszetoszlop pont.

<a name="cylindrical-projection"></a> **Hengervetületet**: Egy leképezési, amely púpja vagy alakzatot tangens vagy szekáns meghatározása henger sphere pontok alakítja át. Ezután az első aljára szeletelt és sík simítva a 3D henger.

## <a name="d"></a>D

<a name="datum"></a> **Datum**: A mérési rendszer hivatkozási előírások, koordináta rendszerek Bűvös négyzet (függőleges datum) felülettel alatt vagy egy felületre (vízszintes datum) vagy a fenti magasságát.

<a name="dbf-file"></a> **A DBF-fájl**: Egy adatbázis fájlformátum-Alakzatfájlok (SHP) együtt használják.

<a name="degree-minutes-seconds-dms"></a> **Párhuzamossági perc, másodperc (DMS)**: A szélességi és hosszúsági leíró mértékegysége. Olyan mértékű 1/360<sup>th</sup> egy kör. Olyan mértékű tovább oszthatók 60 perc, és a egy perc van felosztva 60 másodperc.

<a name="delaunay-triangulation"></a> **Delaunay keresztárfolyam**: Összefüggő, nem átfedő háromszögek háló létrehozásához egy adatkészletből pontok módszer. Minden egyes háromszög circumscribing kör nincs pontokat az adatkészletből a saját belső tartalmaz.

<a name="demographics"></a> **Demográfiai**: Egy népességi statisztikai jellemzői (például az életkor, a születési arányra vonatkozó és a bevétel).

<a name="destination"></a> **Cél**: A záró pontot vagy a helyét, amelyben valaki utazik.

<a name="digital-elevation-model-dem"></a> **Digitális jogosultságszint-emelési modell (DEM-)**: Jogosultságszint-emelés értékek kapcsolatos egy felület egy közös datum használatával rendszeres időközönként terület fölé rögzített adatkészlet. DEMs jellemzően terep mentesség ábrázolására használják.

<a name="dijkstra's-algorithm"></a> **A Dijkstra algoritmus**: Megvizsgálja két pont közötti legrövidebb útvonal megkeresése a hálózat csatlakoztatásához algoritmus.

<a name="distance-matrix"></a> **Távolsági mátrix**: A mátrix, amely tartalmazza az utazási időt és a távolság információk források és a célhelyek között. 

## <a name="e"></a>E

<a name="elevation"></a> **Jogosultságszint-emelési**: A függőleges távolság alatt egy referencia-felület vagy datum (általában mean tengerszint) vagy egy pontot vagy a fenti objektumok közül. Jogosultságszint-emelési általában a föld magasságát hivatkozik.

<a name="envelope"></a> **Burkológörbe**: Lásd: [Bounding box](#bounding-box).

<a name="extended-postal-code"></a> **Bővített irányítószámot**: Egy postai irányítószám, amely további információkat tartalmaznak. Például az USA-ban irányítószámok öt számjegyeket tartalmazhat, de egy bővített irányítószámot zip + 4, más néven további négy számjegy tartalmazza. Ezek a további számjegyeket egy földrajzi szegmens az öt számjegyű kézbesítési területén, például a város blokkolás, bérbe csoportja vagy egy post office jelölőnégyzetet, amely a hatékony mail rendezési és kézbesítési azonosítására szolgál.

<a name="extent"></a> **Mértékben**: Lásd: [Bounding box](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Az összevont hitelesítés**: Hitelesítési módszerként, amely lehetővé teszi egy egyszeri bejelentkezési és hitelesítési mechanizmust, amely több webes és mobilalkalmazások használható. 

<a name="feature"></a> **A szolgáltatás**: Egy objektum, amely egy geometriai egyesítenek egy további metaadat-információkat. 

<a name="feature-collection"></a> **Gyűjtemény funkció**: A szolgáltatás objektumok gyűjteményét.

<a name="find-along-route"></a> **Útvonalon található**: A térbeli lekérdezés, amely keres, amely egy megadott detour vagy távolságra útvonal elérési úton lévő adatokat.

<a name="find-nearby"></a> **Keresés a közelben**: A térbeli lekérdezés keresési pontról lineáris rögzített távolság (mivel a mélyen van).

<a name="fleet-management"></a> **Adatbázisflotta felügyeleti**: Például az autók, látható, részeként szerezhető be és síkok haszongépjárművekre kezelését. Flottakezelés széles körű funkciókat, például a vehicle finanszírozási, karbantartási, telematika (nyomkövetési és diagnosztikai), valamint illesztőprogram, gyorsabban, üzemanyag, és az egészségügyi és biztonsági felügyeleti is tartalmazhat. Flottakezelés egy olyan vállalatok, akik szállítási támaszkodnak az üzleti minimalizálása érdekében a kockázatokat, és csökkentheti szállítási és a személyzet költségeit, közben biztosítva állami jogszabályok betartása által használt folyamat.

<a name="free-flow-speed"></a> **Ingyenes flow sebesség**: Az ingyenes flow sebessége ideális körülmények között várt. Általában a sebességkorlátozás.

<a name="free-form-address"></a> **Szabad formátumú címet**: Teljes cím, amely egy egysoros szöveg jelöli.

<a name="fuzzy-search"></a> **Intelligens keresést**: A keresés, amely egy szabad formátumú szöveges karakterláncot, esetleg egy cím vagy a hasznos helyekre vezető útvonalak. 

## <a name="g"></a>G

<a name="geocode"></a> **Geocode**: Egy cím vagy helyre, amely egy adott helyen a térképen megjeleníthető használható koordináta lett alakítva. 

<a name="geocoding"></a> **Geokódolás**: Más néven előre geokódolás, az a folyamat helyadatok cím átalakítása koordináták. 

<a name="geodesic-path"></a> **Érintő geodetikus elérési**: A legrövidebb útvonal görbe felületen két pont között. Az Azure Maps meg, ha az elérési út miatt a Mercator vetületet görbe vonalként jelenik meg.

<a name="geofence"></a> **Geokerítésen**: Egy meghatározott földrajzi régióban kerül, vagy a régiót létezik eseményeinek indításához használható.

<a name="geojson"></a> **A GeoJSON**: JSON-alapú fájlformátumot például pontok, a sorok és poligonok földrajzi vektoros adatok tárolására szolgál. **Megjegyzés**: Az Azure Maps GeoJSON, kiterjesztett verzióját használja [itt dokumentált](extend-geojson.md).

<a name="geometry"></a> **Geometriai**: Például egy pont, a sor vagy a sokszög egy térbeli objektumot határozza meg.

<a name="geometrycollection"></a> **GeometryCollection**: Geometriai objektumok gyűjteményét.

<a name="geopol"></a> **GeoPol**: Geopolitikai bizalmas adatokat, például vitatott szegélyek és helynevek hivatkozik.

<a name="georeference"></a> **Viszonyításához**: A földrajzi adatok vagy az ismert koordináta-rendszerére képanyag igazítása folyamatán. Ez a folyamat időeltolású, elforgatása, méretezése vagy az adatok eltorzítják állhat.

<a name="georss"></a> **GeoRSS**: Térbeli adatok felvétele az RSS-hírcsatornák XML kiterjesztéssel.

<a name="gis"></a> **GIS**: "GIS" mozaikszó. A leképezés iparágban használt általános kifejezés.

<a name="gml"></a> **GML**: Más néven a Geography Markup Language. Térbeli adatok tárolására szolgáló XML fájl kiterjesztéssel.

<a name="gps"></a> **GPS**: Más néven globális helymeghatározási rendszer egy rendszer-eszközök irányú pozícióját a föld meghatározására szolgáló műholdak. A föld körül keringő szatellit azt jelzi, hogy lehetővé teszik a GPS-vevők bárhol föld alapján számítja ki a saját helyét trilateration keresztül továbbítja.

<a name="gpx"></a> **GPX**: GPS csereformátumként néven is ismert, egy XML-fájl formátuma általában jön létre a GPS-eszközökről.  

<a name="great-circle-distance"></a> **Nagy-kör távolság**: A legrövidebb távolság, a Surface, a sphere két pont között.

<a name="greenwich-mean-time-gmt"></a> **(GMT) greenwichi középidő**: A szélességi, amely futtatja a Királyi megfigyelő az Angliai Greenwichen keresztül ideje.

<a name="guid"></a> **GUID**: Egy globálisan egyedi azonosítóját. Egy felületet, osztályban, típustár, összetevő kategória vagy rekord egyedi azonosításához használt karakterlánc.

## <a name="h"></a>H

<a name="haversine-formula"></a> **A képlet Haversine**: A sphere két pontjai között nagy-kör távolság kiszámítására használt gyakori egyenlőségi.

<a name="hd-maps"></a> **HD maps**: Más néven nagy definíció térképek, áll éles környezetet hűen közúti hálózati információkat lane jelölések, például aláírási és iránya lámpa autonóm a szükséges.

<a name="heading"></a> **Fejléc**: Valami mutat, vagy irányuló iránya. Lásd még: [ellátott](#heading).

<a name="heatmap"></a> **Intenzitástérkép**: Adatok vizuális megjelenítésének, amelyben a színskála pontok egy bizonyos területen sűrűsége jelölik. Lásd még a tematikus térképet.

<a name="hybrid-imagery"></a> **Hibrid képeken**: Műholdas vagy légifelvételes képanyag, amely rendelkezik közúti adatok és a címke felett átfedésben.

## <a name="i"></a>I

<a name="iana"></a> **AZ IANA**: Az Internet Assigned Numbers Authority rövidítése. Egy nonprofit szervezet csoportot, amely felügyeli a globális IP-címek hozzárendelését.

<a name="isochrone"></a> **Isochrone**: Egy isochrone határozza meg, hogy a terület, amelyben valaki is utazási mód kiválasztására bármely irányban a megadott időn belül egy adott helyről. Lásd még: [elérhető tartomány](#reachable-range).

<a name="isodistance"></a> **Isodistance**: Adja meg egy helyet, egy isochrone meghatározza a területét, amelyben valaki is utazási mód kiválasztására bármely irányban a megadott távolságon belül. Lásd még: [elérhető tartomány](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a> **KML**: Más néven Keyhole Markup Language, egy közös XML-fájlformátummal például pontok, a sorok és poligonok földrajzi vektoros adatok tárolására. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat**: Multispektrális, earth-föld körül keringő szatellit NASA által fejlesztett, gyűjtse össze a föld például mezőgazdaság, erdészet és ennek során számos iparágban használt képeken.

<a name="latitude"></a> **Szélesség**: Az angular távolság fokban mért kezdőszögét az Egyenlítőtől déli iránya vagy északi.

<a name="level-of-detail"></a> **Részletességi szint**: Tekintse meg a nagyítási szint.

<a name="lidar"></a> **LIDAR**: Világos észlelése és terjedő betűszó. Tükröző felületeken a távolságmérés Lézerrel használó távoli érzékelő módszer.

<a name="linear-interpolation"></a> **Lineáris interpolációs**: Az ismeretlen érték használata az ismert értékek közötti távolságot becslése.

<a name="linestring"></a> **LineString**: Egy geometriai, egy sor képviseli. Vonallánc néven is ismert. 

<a name="localization"></a> **A honosítás**: Különböző nyelv és kulturális környezetek támogatása.

<a name="logistics"></a> **A logisztikai**: A folyamat helyezi át a személyek, járművek, készletek és eszközök szabályozott módon.

<a name="longitude"></a> **Hosszúsági**: Az angular távolság fokban mért kezdőszögét, az USA keleti vagy nyugati irányban szélességi.

## <a name="m"></a>M

<a name="map-tile"></a> **Képezze le a csempe**: Egy eloszlása egy térképen vászonalapú jelölő téglalap alakú kép. További információkért lásd: a [nagyítási szintek és a csempe grid dokumentációja](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Jelölő**: Más néven a PIN-kód vagy Rajzszög egy pont helyét egy térképen jelölő ikon.

<a name="mercator-projection"></a> **Mercator vetületet**: Hengeres térképvetület, amely a standard szintű térképvetület vált hajózási célokra képességét a sorok természetesen a konstans egyenes vonalak egyenes szegmenset, amely az a szög, és a hosszúsági takarítson meg, más néven képviselő miatt. Minden egybesimított térkép leképezések torzítja az alakzatok vagy méretét a térkép a föld felszínén igaz elrendezését képest. A Mercator vetületet exaggerates messze az Egyenlítőtől déli területek, úgy, hogy kisebb területek jelennek meg a térképen nagyobb, mint az oszlopok módszerrel érdemes. 

<a name="multilinestring"></a> **MultiLineString**: Egy geometriai, amely egy LineString objektumok gyűjteményét képviseli. 

<a name="multipoint"></a> **A multiPoint**: Egy geometriai, amely egy pont objektumok gyűjteményét képviseli.

<a name="multipolygon"></a> **MultiPolygon**: Egy geometriai, amely egy sokszög objektumok gyűjteményét képviseli. Például Hawaii határait megjeleníthető minden sziget lenne vázolt sokszög- és Hawaii határait így lenne, egy MultiPolygon.

<a name="municipality"></a> **Település**: A város vagy város. 

<a name="municipality-subdivision"></a> **Település felosztása**: Egy település, például a helyek vagy a helyi terület neve például "New York" része.

## <a name="n"></a>N

<a name="navigation-bar"></a> **Navigációs sáv**: A nagyítási szintjét, lényegét, elforgatás módosításával, illetve közötti váltás az alap réteg térkép vezérlőit halmaza.

<a name="nearby-search"></a> **Keresés közeli**: A térbeli lekérdezés keresési pontról lineáris rögzített távolság (mivel a mélyen van).

<a name="neutral-ground-truth"></a> **Semleges földön hiteles**: Egy térképen jeleníti meg az azt jelenti, hogy a régió hivatalosnyelv és a helyi parancsfájlokat, ha elérhető címkék.

## <a name="o"></a>O

<a name="origin"></a> **Forrás**: Egy kezdőpontja vagy helyét, amelyben egy felhasználó tagja.

## <a name="p"></a>P

<a name="panning"></a> **Pásztázás**: Áttérés a térkép bármelyik irányba állandó nagyítási szint egyidejű fenntartásával folyamata.

<a name="parcel"></a> **Csomagonkénti**: A diagram föld nebo vlastnost határ.

<a name="pitch"></a> **Lényegét**: Döntési mennyisége a térkép tartalmaz, a függőleges 0 leolvassa ahol ugrik fel a térkép viszonyítva.

<a name="point"></a> **Pont**: Egy geometriai, amely a térképen egy egyetlen pozíciót jelöli. 

<a name="points-of-interest-poi"></a> **Hasznos helyek (KOORDINÁTÁIIG)**: Egy üzleti, tereptárgyak vagy a lényeges közös helyen.

<a name="polygon"></a> **Sokszög**: Egy szilárd geometry, amely egy térkép egy területét jelöli. 

<a name="polyline"></a> **Vonallánc**: Egy geometriai, egy sor képviseli. LineString néven is ismert. 

<a name="position"></a> **Pozíció**: A hosszúság, szélesség és magasság (x, y, z koordináták) pont.

<a name="post-code"></a> **Irányítószám**: Lásd: [Postal code](#postal-code).

<a name="postal-code"></a> **Irányítószám**: Betűk vagy számok vagy mindkettő egy meghatározott formátumban, a földrajzi területek osztani zónában történő annak érdekében, hogy egyszerűbb szolgáltatásnyújtás mail ország a postai szolgáltatás által használt sorozata.

<a name="prime-meridian"></a> **Szélességi**: Egy 0-fok hosszúsági képviselő hosszúsági sor. Általában a hosszúsági értékeknek csökkentése, amíg 180-fokos nyugati irányban útközben, és növelhető, ha a keleti irányban-180 utazik-fok. 

<a name="prj"></a> **PRJ**: Egy szöveges fájl, amely gyakran a a tervezett koordináta-rendszerére kapcsolatos információkat tartalmazó Alakzatfájl fájl társul az adatkészlet szerepel.

<a name="projection"></a> **Leképezés**: A tervezett koordináta-rendszerére például keresztező Mercator, Albers egyenlő terület és Robinson a térképvetület alapján. Ezek lehetővé teszi, hogy a föld gömbfelületű egy kétdimenziós Descartes koordináta adatsík az alakzatot a projekt maps. Tervezett koordináta rendszerek vannak más néven térkép leképezések.

## <a name="q"></a>Q

<a name="quadkey"></a> **Quadkey**: Egy base-4 címe indexe egy csempét egy quadtree mozaik rendszeren belül. További információkért lásd: [nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md) dokumentációjában talál további információt.

<a name="quadtree"></a> **Quadtree**: Olyan adatszerkezet, ahol egyes csomópontok rendelkezik pontosan négy gyermekekkel. Az Azure Maps-használt mozaik elrendezés rendszer quadtree szerkezetet használ, úgy, hogy egy felhasználó közeledik egy szinttel, mivel egyes térképcsempét bontja négy alárendelt csempék.  További információkért lásd: [nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md) dokumentációjában talál további információt.

<a name="queries-per-second-qps"></a> **Lekérdezések másodpercenkénti (lekérdezési QPS)**: Lekérdezések vagy az, hogy egy szolgáltatás vagy a platform egy második belüli kérelmek száma. 

## <a name="r"></a>R

<a name="radial-search"></a> **Kör alakú keresési**: A térbeli lekérdezés keresési pontról lineáris rögzített távolság (mivel a mélyen van). 

<a name="raster-data"></a> **Raszter adatok**: Cellák (vagy képpontban megadva) össze vannak rendezve sorok és oszlopok (vagy a rács), minden cella információkat, például a hőmérséklet jelölő értéket tartalmaz. Raszter a digitális légifelvételes fényképek, a műholdak üzemeltetéséhez, a digitális képek és a beolvasott maps képanyag tartalmazza.

<a name="raster-layer"></a> **Raszter réteg**: Mozaikréteg, amely Raszter lemezképek áll.

<a name="reachable-range"></a> **Elérhető tartomány**: Elérhető számos meghatározza a, amelyben valaki utazhat egy megadott idő vagy a távolság, szállítási mód kiválasztására továbbítani a bármelyik irányba helyről-területét. Lásd még: [Isochrone](#isochrone) és [Isodistance](#isodistance).

<a name="remote-sensing"></a> **Távoli érzékelő**: A folyamat gyűjtése és a egy távolság érzékelői adatainak értelmezése.

<a name="rest-service"></a> **REST-szolgáltatás**: Kifejezés mozaikszava REST a Representational State Transfer rövidítése. REST-szolgáltatás URL-cím-alapú webes szolgáltatás alapszintű webalkalmazás technológiáját használja a kommunikációhoz, folyamatban van a HTTP GET és POST kéréseket a leggyakrabban használt módszerek támaszkodik. Ilyen típusú szolgáltatások általában me sokkal gyorsabb, kisebb, mint a hagyományos SOAP-alapú szolgáltatások.

<a name="reverse-geocode"></a> **Fordított geokódolással**: A folyamat koordináta véve, és amely meghatározza, hogy a cím, amelyben a térképen jelöli.

<a name="reproject"></a> **Reproject**: Lásd: [átalakítási](#transformation).

<a name="rest-service"></a> **REST-szolgáltatás**: Kifejezés mozaikszava az Representational State Transfer. Az architektúra decentralizált, elosztott környezetben társak közötti információcsere. REST lehetővé teszi a programok kommunikáljanak az operációs rendszer vagy a platform függetlenül Hypertext Transfer Protocol (HTTP) kérelmet küld egy egységes erőforrás-azonosító (URL), majd az adatokat a különböző számítógépekre.

<a name="route"></a> **Útvonal**: Két vagy több hely, előfordulhat, hogy a további információk, például az útvonalon waypoints vonatkozó utasításokat is többek között az elérési útja.

<a name="requests-per-second-rps"></a> **Kérelmek / másodperc (RPS)**: Lásd: [lekérdezések másodpercenkénti (lekérdezési QPS)](#queries-per-second-qps). 

<a name="rss"></a> **RSS**: Kifejezés mozaikszava az igazán egyszerű tartalomtípus-gyűjtési, erőforrás leírása keretrendszer (RDF) hely összefoglalás vagy gazdag hely összegzése, attól függően, a forrás. Egy egyszerű, strukturált XML formátumú tartalommegosztás különböző webhelyek között. RSS-dokumentumok tartalmazzák a fő metaadatok elemek, például a szerző, dátum, cím, egy rövid leírást és egy hivatkozásra kattintva. Ezen információ segítségével a felhasználó (vagy egy RSS-közzétevő szolgáltatás) döntse el, mi anyagok érdemes további vizsgálatra.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Műholdas képanyag**: Rögzített síkok, és rögtön a lefelé mutató szatellit képeken.

<a name="software-development-kit-sdk"></a> **Szoftverfejlesztői készlet (SDK)**: Dokumentációt, mintakódokat és mintaalkalmazások egy fejlesztői használata API-alkalmazások készítése érdekében gyűjteménye.

<a name="shapefile-shp"></a> **Alakzatfájl (SHP)**: Más néven az ESRI-Alakzatfájlra egy vektoros adatok tárolási formátum tárolásához, a hely, alakzat és földrajzi funkciókat attribútumai. Egy alakzatfájl a kapcsolódó fájlokat tárolja.

<a name="spherical-mercator-projection"></a> **Gömbös Mercator vetületet**: Lásd: [Mercator webes](#web-mercator). 

<a name="spatial-query"></a> **Térbeli lekérdezés**: Egy olyan szolgáltatás, amely egy térbeli műveletet hajt végre felé irányuló kérések. Például egy kör alakú keresést, vagy egy útvonal keresési mentén.

<a name="spatial-reference"></a> **Térbeli referencia**: A koordináta-alapú helyi, regionális és globális rendszer pontosan az entitások földrajzi helyének azonosításához használt. Azt határozza meg a térkép-koordinátákat kapcsolódnak helyek a való világból használt koordináta-rendszerére. Térbeli hivatkozások győződjön meg arról, hogy a különböző rétegek forrásból származó térbeli adatokat a pontos megtekintése vagy elemzése integrálható legyen-e. Az Azure Maps használja a [EPSG:3857](https://epsg.io/3857) . referenciarendszer és WGS 84 koordinálása bemeneti geometriai adatok. 

<a name="sql-spatial"></a> **Térbeli SQL**: Az SQL Azure és az SQL Server 2008 és újabb beépített térbeli funkciókat jelenti. A térbeli funkciója is használható, függetlenül az SQL Server egy .NET-kódtár érhető el. További információkért lásd: a [térbeli adatok (SQL Server) dokumentációja](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) további információt.

<a name="synchronous-request"></a> **Szinkron kérések**: HTTP-kérést megnyit egy kapcsolatot, és a válaszra vár. Böngészők oldal tehet egyidejű HTTP-kérések számának korlátozásához. Hosszú ideig futó kérelmek szinkron több egy időben történik, ha ezt a korlátot érhető el, és kérelmek késleltetett, amíg be nem fejeződik egyik más kérelmet.

## <a name="t"></a>T

<a name="telematics"></a> **Telematika**: Küldő, fogadására és távközlési eszközökön keresztül adatok tárolására együtt, ami miatt a távoli objektumok vezérlőelem nem. 

<a name="temporal-data"></a> **A historikus adatok**: Adatok, amely kifejezetten hivatkozik az időpontok és dátumok. A historikus adatok hivatkozhatnak, különálló eseményekről, például a villámgyors is; objektumok, például vonatok; áthelyezése vagy ismétlődő megfigyelések, például az adatforgalom érzékelőadatok számát.

<a name="terrain"></a> **Terep**: Egy adott jellemző, például homokos terep vagy hegyvidéki terep kellene földek terület.

<a name="thematic-maps"></a> **Tematikus térképek**: Tematikus térkép egy egyszerű térképet kérés érkezett egy földrajzi területen kapcsolatos téma tükrözik. Az ilyen típusú térkép egy általános forgatókönyv a felügyeleti régiók, például az országok néhány mérőszám az adatok alapján kiszínezheti.

<a name="tile-layer"></a> **Mozaikréteg**: A réteg (téglalap alakú szakaszok) csempék összeállítsanak egy folyamatos rétegre szerint jelenik meg. A csempék vagy raszter csempék kép vagy vektoros csempék. Raszter csempe rétegek általában jelennek meg időben és képek egy kiszolgálón tárolja. Ez is igénybe vehet, nagy mennyiségű tárhely. Vektor csempe rétegek vannak leképezve az ügyfélalkalmazásban menet közben, így a kiszolgáló oldalán tárhellyel kapcsolatos követelmények kisebbek.

<a name="time-zone"></a> **Időzóna**: Egy régióban, amely betartja a jogi, kereskedelmi és közösségi célból egy egységes téli idő világszerte. Kövesse a határok és a felosztás országok általában időzónákban.

<a name="transaction"></a> **Tranzakció**: Az Azure Maps tranzakciós licencelési modellt alkalmaz, ahol;

- Egy-egy tranzakció jön létre minden kért 15 térkép vagy felé irányuló forgalom csempék esetében.
- Egy-egy tranzakció jön létre minden egyes API-hívás az Azure Maps-Közösséghez, a szolgáltatások egyik például keresés, vagy a.

<a name="transformation"></a> **Átalakítás**: A különböző földrajzi koordináta rendszerek közötti átalakítás folyamatán. Előfordulhat például, néhány adat, amely az Egyesült Királyságban rögzített, és a földrajzi koordináta-rendszerére OSGB 1936 alapján. Az Azure Maps használja a [EPSG:3857](https://epsg.io/3857) WGS84 koordináta referencia rendszer változatát. Ilyen megfelelően megjeleníteni az adatokat, akkor kell rendelkeznie a rendszer a másikra alakította át koordinátái.

<a name="traveling-salesmen-problem-tsp"></a> **Traveling eladók probléma (TSP)**:  Hamiltonian kapcsolatcsoport probléma, amelyben egy értékesítő meg kell találnia a leghatékonyabb látogasson el egy sorozatát leáll, majd térjen vissza a kiindulási helyét.  

<a name="trilateration"></a> **Trilateration**: A folyamat megvizsgálja, hogy minden három pont közötti pozícióját a föld felszínén megállapodást két más pontok pont meghatározására.

<a name="turn-by-turn-navigation"></a> **Kapcsolja be kapcsolja be navigációs**: Útmutatás az útvonal minden útvonal, a felhasználók alkalmazás megközelíti a következő műveletet.

## <a name="v"></a>V

<a name="vector-data"></a> **Vektoros adatok**: Koordinálhat, pontokat, vonalakat vagy poligonok ábrázolt adatok alapján.

<a name="vector-tile"></a> **Vektor csempe**: Az open data specifikáció az azonos csempe rendszert használ, a térkép vezérlőelem földrajzi vektoros adatok tárolására. Lásd még: [mozaikréteg](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Járműtelemetria útválasztási problémát (VRP)**: Egy osztály problémák, amelyben egy járműflotta járművek rendezett útvonalak készletét kiszámítása során beállított megkötések, figyelembe véve. Ezek a korlátozások közé tartozik többek között például a szállítási időt a windows, több útvonal kapacitás, és utazás időtartama megkötések.

<a name="voronoi-diagram"></a> **Voronoi diagram**: Egy partíciót, területek vagy cellák terület, körülvevő geometriai objektumok (általában pont funkciók) egy készlete. E cellák és poligonok, meg kell felelniük Delaunay háromszögek feltételeit. Minden hely egy területen belül közelebb az az objektum bekeretezze, mint bármely más objektumot a készletben. Voronoi diagramok gyakran használják a földrajzi szolgáltatások köré befolyásoló területeit ábrázolni. 

## <a name="w"></a>W

<a name="waypoint"></a> **Waypoint**: Egy waypoint a megadott hosszúsági és szélességi navigációs célokra használt által meghatározott földrajzi elhelyezkedését. Gyakran használják, amelyek egy ponton keresztül valakinek navigál.

<a name="waypoint-optimization"></a> **Waypoint optimalizálási**: Waypoints minimalizálása érdekében az utazási idő vagy a távolság egy készletét átrendezése folyamata szükséges az összes megadott waypoints áthaladását. Más néven a [Traveling eladók probléma](#traveling-salesmen-problem-tsp) vagy [jármű útválasztási problémát](#vehicle-routing-problem-vrp) optimalizálása összetettségétől függően.

<a name="web-map-service-wms"></a> **Térkép webszolgáltatás (WMS)**: WMS az Open földrajzi Consortium (OGC) szabványa, amely meghatározza a térkép lemezkép-alapú szolgáltatások. WMS-szolgáltatások képek egy térképet, igény szerinti belül meghatározott területek adja meg. Lemezképek előre megjelenített Jelképrendszer tartalmazza, és a egy elnevezett stílusokat is előfordulhat, hogy megjelenítésre, ha a szolgáltatás által definiált.

<a name="web-mercator"></a> **Webalkalmazás-Mercator**: Más néven gömbös Mercator Vetületet a Mercator vetületet, elsősorban a webes leképezés programok használt egyik enyhe variant. Az azonos képleteket, a standard szintű Mercator vetületet használt semmibe maps használ. Azonban a webes Mercator használja a gömbös képletek minden méretezhető, mivel általában nagy méretű Mercator képezi le a képernyőn ellipszoid a leképezés. Eltérést észrevétlen a globális méretekben, de a helyi területet ellipszoid Mercator leképezi az azonos méretű igaz némileg eltérnek a maps okoz.

<a name="wgs84"></a> **WGS84**: A Surface helyét a térképen térbeli koordináták kapcsolatosak konstansok gyűjteménye. Standard a WGS84 datum lesz legnagyobb online leképezés szolgáltatók és a GPS-eszközök által használt. Az Azure Maps használja a [EPSG:3857](https://epsg.io/3857) WGS84 koordináta referencia rendszer változatát.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Z koordináta**: Lásd: [magasság](#altitude). 

<a name="zip-code"></a> **Irányítószám**: Lásd: [Postal code](#postal-code).

<a name="Zoom level"></a> **Nagyítási szint**: A részletes, és mekkora a térkép látható szintjét határozza meg. A 0. szint ki, mert a teljes, amikor a teljes világtérkép gyakran lesz a nézetben, de ország neve és a egy vonalban például korlátozott információk jelennek meg, valamint óceán nevét. Közelebb nagyítása 17 szintre, ha a térkép jelenik meg egy adott területre néhány város blokkok közúti részletes információkkal. További információkért lásd: a [nagyítási szintek és csemperács](zoom-levels-and-tile-grid.md) dokumentációját.

