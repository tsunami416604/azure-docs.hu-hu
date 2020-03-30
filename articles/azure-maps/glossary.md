---
title: Azure Maps szószedet | Microsoft dokumentumok
description: Az Azure Maps, a Helyalapú szolgáltatások és a GIS általánosan használt kifejezések szószedete.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: c9d5c2efc14dec5beee0eaf81a4f628421ef3d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657044"
---
# <a name="glossary"></a>Szószedet

Az alábbi lista az Azure Maps-szolgáltatásokkal használt gyakori szavakat ismerteti.

## <a name="a"></a>A

<a name="address-validation"></a>**Címérvényesítés**: A cím létezésének ellenőrzésére szolgáló folyamat.

<a name="advanced-routing"></a>**Speciális útválasztás**: Olyan szolgáltatások gyűjteménye, amelyek előrehaladnak útválasztási adatok használatával; például az elérhető tartományok (iochronok), a távolságmátrixok és a kötegelt útvonalkérelmek kiszámítása.

<a name="aerial-imagery"></a>**Légi felvételek:** lásd [a műholdas képeket.](#satellite-imagery) 

<a name="along-a-route-search"></a>**Útvonalkeresés mentén:** Olyan térbeli lekérdezés, amely egy megadott kerülőúton vagy az útvonalútvonaltól való távolságon belül keresi az adatokat.

<a name="altitude"></a>**Magasság**: Egy pont magassága vagy függőleges magassága a referenciafelület felett. A magasságmérések egy adott referenciaadaton alapulnak, például az átlagos tengerszinten. Lásd még: magasság.

<a name="ambiguous"></a>**Nem egyértelmű**: Az adatbesorolás bizonytalansági állapota, amely akkor áll fenn, ha egy objektumhoz egy adott attribútumhoz két vagy több érték is hozzárendelhető. Például a "CA" geokódoláskor két kétértelmű eredményt ad vissza: "Kanada" és "Kalifornia". A "CA" egy ország és egy állami kód a "Kanada" és a "Kalifornia" esetében. 

<a name="annotation"></a>**Jegyzet**: A térképen megjelenő szöveg vagy grafika, amely a felhasználó tájékoztatására szolgál. A jegyzet elazonosíthat vagy leírhat egy adott térképentitást, általános információkat adhat meg a térképen lévő területről, vagy információkat adhat meg magáról a térképről.

<a name="antimeridian"></a>**Antimeridian**: Más néven a 180<sup>th</sup> Meridian. Ez az a pont, ahol -180 fok és 180 fok hosszúságtalálkozik. Ami a világ első számú meridiánjának ellentéte.

<a name="application-programming-interface-api"></a>**Alkalmazásprogramozási felület (API)**: Olyan specifikáció, amely lehetővé teszi a fejlesztők számára, hogy alkalmazásokat hozzanak létre.

<a name="api-key"></a>**API-kulcs**: Lásd: [Megosztott kulcshitelesítés.](#shared-key-authentication)

<a name="area-of-interest-aoi"></a>**Érdeklődési terület (AOI)**: A térkép vagy adatbázis-előállítás fókuszterületének meghatározásához használt mérték.

<a name="asset-tracking"></a>**Eszközkövetés**: Egy eszköz, például egy személy, jármű vagy más objektum helyének nyomon követésének folyamata.

<a name="asynchronous-request"></a>**Aszinkron kérés**: Olyan HTTP-kérelem, amely kapcsolatot nyit meg, és olyan kérést küld a kiszolgálónak, amely az aszinkron kérés azonosítóját adja vissza, majd bezárja a kapcsolatot. A kiszolgáló folytatja a kérelem feldolgozását, és a felhasználó ellenőrizheti az állapotot az azonosító használatával. Amikor a kérelem feldolgozása befejeződött, a felhasználó letöltheti a választ. Ez a kérelemtípus hosszú ideig futó folyamatokhoz használatos.

<a name="autocomplete"></a>**Automatikus kiegészítés**: Az alkalmazás olyan szolgáltatása, amely előre jelzi a felhasználó által beírt szó többi részét. 

<a name="autosuggest"></a>**Automatikus javaslat**: Az alkalmazás olyan szolgáltatása, amely előre jelzi a felhasználó által beírt logikai lehetőségeket.

<a name="azure-location-based-services-lbs"></a>**Azure helyalapú szolgáltatások (LBS)**: Az Azure Maps korábbi neve, amikor előzetes verzióban volt.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure AD)**: Az Azure AD a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatása. Az Azure Maps Azure AD-integráció jelenleg előzetes verzióban érhető el az összes Azure Maps API-hoz. Az Azure AD támogatja a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Maps-erőforrások részletes elérésének engedélyezéséhez. Ha többet szeretne megtudni az Azure Maps Azure AD-integrációról, olvassa el az [Azure Maps és az Azure AD](azure-maps-authentication.md) és a Hitelesítés kezelése című [témakört az Azure Maps ben.](how-to-manage-authentication.md)

<a name="azure-maps-key"></a>**Azure Maps kulcs:** Lásd: [Megosztott kulcshitelesítés.](#shared-key-authentication)

## <a name="b"></a>B

<a name="base-map"></a>**Alaptérkép**: A térképalkalmazás azon része, amely háttér-referenciainformációkat jelenít meg, például utakat, tereptárgyakat és politikai határokat.

<a name="batch-request"></a>**Kötegelt kérelem:** Több kérelem egyetlen kérelembe történő egyesítésének folyamata.

<a name="bearing"></a>**Irány**: Egy pont vízszintes iránya egy másik ponthoz képest. Ezt az északi szöghez viszonyított szögként fejezik ki, 0 foktól 360 fokig az óramutató járásával megegyező irányban. 

<a name="boundary"></a>**Határ**: A szomszédos politikai entitásokat, például országokat/régiókat, körzeteket és tulajdonságokat elválasztó vonal vagy sokszög. A határ olyan vonal, amely lehet, hogy nem követi a fizikai jellemzőket, például folyókat, hegyeket vagy falakat.

<a name="bounds"></a>**Határok:** Lásd: [Határoló keret](#bounding-box).

<a name="bounding-box"></a>**Határolókeret**: A térképen egy téglalap alakú területet ábrázoló koordináták. 

## <a name="c"></a>C#

<a name="cadastre"></a>**Kataszter**: Bejegyzett földterület és ingatlanok nyilvántartása. Lásd [még: Parcel](#parcel).

<a name="camera"></a>**Kamera**: Interaktív térképvezérlő környezetében a kamera határozza meg a térképlátómezőt. A kamera nézetablakát több térképparaméter alapján határozzák meg: középpont, nagyítási szint, hangmagasság, csapágy. 

<a name="centroid"></a>**Centroid**: A geometriai központja a funkció. A vonal centroidja lenne a középpont, míg a sokszög centroidja lenne a terület középpontja.

<a name="choropleth-map"></a>**Choropleth térkép**: Tematikus térkép, amelyen a területek a statisztikai változó mérésével arányosan árnyékoltak. Ez a statisztikai változó megjelenik a térképen. Például az egyes amerikai államok határvonalának színezése az összes többi állam relatív népessége alapján.

<a name="concave-hull"></a>**Homorú hajótest**: Olyan alakzat, amely egy lehetséges homorú geometriát jelöl, amely körülveszi a megadott adatkészlet összes alakzatát. A generált alakzat hasonló ahhoz, mintha az adatokat műanyag fóliával csomagolnák, majd felmelegítnék, így a pontok közötti nagy átívelés más adatpontok felé vezetne.

<a name="consumption-model"></a>**Fogyasztási modell**: Olyan információ, amely meghatározza, hogy a jármű milyen sebességgel fogyaszt üzemanyagot vagy villamos energiát. Lásd még a [fogyasztási modell dokumentációját](consumption-model.md).

<a name="control"></a>**Control**: Egy grafikus felhasználói felületből álló önálló vagy újrafelhasználható összetevő, amely a felület viselkedési készletét határozza meg. Például egy térképvezérlő általában a felhasználói felület azon része, amely interaktív térképet tölt be.

<a name="convex-hull"></a>**Konvex hajótest**: A konvex hajótest olyan alakzat, amely a megadott adatkészlet összes alakzatát körülölelő minimális konvex geometriát jelöli. A létrehozott alakzat hasonló az adatkészlet körül egy rugalmas sáv burkoló.

<a name="coordinate"></a>**Koordináta**: A térképen egy hely ábrázolására használt hosszúsági és szélességi értékeket jelöli.

<a name="coordinate-system"></a>**Koordináta-rendszer**: Referenciakeretrendszer, amely két vagy három dimenzióban határozza meg a pontok helyzetét a térben.

<a name="country-code"></a>**Országkód**: Egy ország/régió egyedi azonosítója az ISO-szabvány alapján. Az ISO2 egy kétkarakteres kód egy országhoz (például US), amely az ISO3 egy háromkarakterű kódot (például USA-t) jelöl.

<a name="country-subdivision"></a>**Országfelosztás**: Egy ország/régió első szintű albontása, közismert nevén állam vagy tartomány.

<a name="country-secondary-subdivision"></a>**Ország másodlagos alkörzete**: Egy ország/régió másodfokú albontása, közismert nevén megye.

<a name="country-tertiary-subdivision"></a>**Ország felsőfokú alosztálya**: Egy ország/régió harmadik szintű albontása, jellemzően egy megnevezett terület, például egy egyházközség.

<a name="cross-street"></a>**Kereszt utca**: Az a pont, ahol két vagy több utca metszi.

<a name="cylindrical-projection"></a>**Hengeres vetület**: Olyan vetület, amely a szferoidból vagy gömbből egy érintő- vagy szeánhengerre alakítja át a pontokat. A hengert ezután felülről lefelé szeleteljük, és síkba lapítják.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: A mérési rendszer referencia-specifikációi, egy felületen lévő koordináta-pozíciórendszer (vízszintes adat) vagy a felület feletti vagy alatti magasságok (függőleges adat).

<a name="dbf-file"></a>**DBF fájl**: Az adatbázis fájlformátum, amely együtt használják Shapefiles (SHP).

<a name="degree-minutes-seconds-dms"></a>**Fok perc másodperc (DMS):** A szélesség és a hosszúság leírására szolgáló mértékegység. A fokozat egy kör<sup>th</sup> 1/360-a. A fokozat tovább oszlik 60 perc, és egy perc van osztva 60 másodperc.

<a name="delaunay-triangulation"></a>**Delaunay háromszögelés**: Összefüggő, egymást nem átfedő háromszögek ből álló háló létrehozásának technikája egy ponthalmazból. Minden háromszög körülírási köre nem tartalmaz pontokat az adatkészletbelsejéből.

<a name="demographics"></a>**Demográfiai**adatok : Az emberi populáció statisztikai jellemzői (például életkor, születési arány és jövedelem).

<a name="destination"></a>**Úti cél:** Az a végpont vagy hely, ahová valaki utazik.

<a name="digital-elevation-model-dem"></a>**Digitális magasságmodell (DEM):** Egy felülethez kapcsolódó magasságértékek adatkészlete, amelyet egy adott területen rendszeres időközönként, közös adat használatával rögzítenek. A DEM-eket általában a terepdomborulék ábrázolására használják.

<a name="dijkstra's-algorithm"></a>**Dijkstra algoritmusa**: Egy algoritmus, amely megvizsgálja a hálózat kapcsolatát, hogy megtalálja a legrövidebb utat két pont között.

<a name="distance-matrix"></a>**Távolságmátrix**: Olyan mátrix, amely az eredeti és az úti célok között tartalmazza az utazási idő és a távolság adatait. 

## <a name="e"></a>E

<a name="elevation"></a>**Magasság**: Egy pont vagy egy objektum függőleges távolsága a referenciafelület vagy adat felett vagy alatt. A referenciafelület általában az átlagos tengerszint. A magasság általában a föld függőleges magasságára utal.

<a name="envelope"></a>**Boríték**: Lásd: [Határolókeret](#bounding-box).

<a name="extended-postal-code"></a>**Kiterjesztett irányítószám**: Olyan irányítószám, amely további információkat tartalmazhat. Az USA-ban például az irányítószámok öt számjegyből állnak. De a kiterjesztett irányítószám, az úgynevezett zip+4, négy további számjegyet tartalmaz. Ezek a további számjegyek az ötjegyű megjelenítési területen belüli földrajzi szegmensek azonosítására szolgálnak, például egy városblokk, egy apartmancsoport vagy egy postafiók. A földrajzi szegmens ismerete segíti a hatékony levélrendezést és kézbesítést.

<a name="extent"></a>**Terjedelem**: Lásd: [Határoló keret](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Összevont hitelesítés:** Olyan hitelesítési módszer, amely lehetővé teszi egyetlen bejelentkezési/hitelesítési mechanizmus alkalmazását több webes és mobilalkalmazásban. 

<a name="feature"></a>**Funkció**: Olyan objektum, amely a geometriát további metaadat-adatokkal kombinálja. 

<a name="feature-collection"></a>**Funkciógyűjtemény**: Jellemzőobjektumok gyűjteménye.

<a name="find-along-route"></a>**Útvonal menti keresés**: Olyan térbeli lekérdezés, amely egy megadott kerülőút-időn vagy az útvonalútvonaltól számított távolságon belüli adatokat keres.

<a name="find-nearby"></a>**Közeli keresése**: Olyan térbeli lekérdezés, amely egy ponttól rögzített egyenes vonalbeli távolságot keres (légvonalban).

<a name="fleet-management"></a>**Flottamenedzsment**: Haszongépjárművek, például autók, teherautók, hajók és repülőgépek kezelése. A flottakezelés számos funkciót tartalmazhat, például a járműfinanszírozást, a karbantartást, a telematikai (nyomon követés és diagnosztika), valamint a vezető, a sebesség, az üzemanyag, valamint az egészségügyi és biztonsági menedzsmentet. A Fleet Management egy olyan folyamat, amelyet olyan vállalatok használnak, amelyek a szállításra támaszkodnak az üzletükben. A vállalatok minimalizálni kívánják a kockázatokat, és csökkenteni kívánják a teljes szállítási és személyzeti költségeiket, miközben biztosítják a kormányzati jogszabályoknak való megfelelést.

<a name="free-flow-speed"></a>**Szabad áramlási sebesség**: Ideális körülmények között várható szabad áramlási sebesség. Általában a sebességkorlátozás.

<a name="free-form-address"></a>**Szabadűrlap címe**: Egy szövegsorként ábrázolt teljes cím.

<a name="fuzzy-search"></a>**Fuzzy keresés**: Olyan keresés, amely szabad formátumú szövegsorozatot vesz fel, amely lehet cím vagy érdekes pont. 

## <a name="g"></a>G

<a name="geocode"></a>**Geokód**: Olyan cím vagy hely, amelyet koordinátává alakítottak át, és amely az adott hely térképen való megjelenítésére használható. 

<a name="geocoding"></a>**Geokódolás**: Más néven forward geocoding, a helyadatok címének koordinátákká történő átalakításának folyamata. 

<a name="geodesic-path"></a>**Geodéziai útvonal:** Az ívelt felület két pontja közötti legrövidebb útvonal. Az Azure Maps ben való renderelés kor ez az elérési út a Mercator-vetület miatt ívelt vonalként jelenik meg.

<a name="geofence"></a>**Geokerítés**: Olyan meghatározott földrajzi régió, amely események indítására használható, amikor egy eszköz belép a régióba, vagy létezik.

<a name="geojson"></a>**GeoJSON**: Egy közös JSON-alapú fájlformátum, amely földrajzi vektoradatok, például pontok, vonalak és sokszögek tárolására szolgál. **Megjegyzés:** Az Azure Maps a GeoJSON bővített verzióját használja [az itt dokumentáltan.](extend-geojson.md)

<a name="geometry"></a>**Geometria**: Térbeli objektumot jelöl, például pontot, vonalat vagy sokszöget.

<a name="geometrycollection"></a>**GeometryCollection**: Geometriaobjektumok gyűjteménye.

<a name="geopol"></a>**GeoPol**: Geopolitikailag érzékeny adatokra utal, például vitatott határokra és helynevekre.

<a name="georeference"></a>**Georeferencia**: A földrajzi adatok vagy képek ismert koordináta-rendszerhez való igazításának folyamata. Ez a folyamat az adatok eltolásával, elforgatásával, méretezésével vagy ferdítésével állhat.

<a name="georss"></a>**GeoRSS**: AZ RSS-hírcsatornákhoz térbeli adatok hozzáadására szolgáló XML-bővítmény.

<a name="gis"></a>**GIS**: A "Földrajzi információs rendszer" rövidítése. A leképezési iparág leírására használt általános kifejezés.

<a name="gml"></a>**GML**: Földrajz jelölőnyelv néven is ismert. XML-fájlkiterjesztése térbeli adatok tárolására.

<a name="gps"></a>**GPS**: Más néven global positioning system, egy olyan rendszer, műholdak meghatározására használt eszközök helyzetét a földön. A keringő műholdak olyan jeleket továbbítanak, amelyek lehetővé teszik a GPS-vevő számára, hogy a föld bármely pontján kiszámítsa a saját helyét a trilateráción keresztül.

<a name="gpx"></a>**GPX**: Más néven GPS eXchange formátum, egy XML fájlformátum, amelyet általában GPS-eszközökből hoznak létre.  

<a name="great-circle-distance"></a>**Nagy körtávolság:** A gömb felszínén lévő két pont közötti legrövidebb távolság.

<a name="greenwich-mean-time-gmt"></a>**Greenwichi középidő (GMT):** Az idő az elsődleges meridián, amely végigfut a Royal Observatory Greenwich, Anglia.

<a name="guid"></a>**GUID**: Globálisan egyedi azonosító. Kapcsolat, osztály, típustár, összetevőkategória vagy rekord egyedi azonosítására szolgáló karakterlánc.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversine formula**: Egy gömb két pontja közötti nagy körtávolság kiszámításának gyakori egyenlete.

<a name="hd-maps"></a>**HD térképek**: Más néven Nagy felbontású térképek, áll a nagy hűség úthálózat információk, mint például a sávjelzések, jelzések, és irányjelző fények szükséges autonóm vezetés.

<a name="heading"></a>**Irány**: Az az irány, amerre valami mutat vagy felé néz. Lásd [még: Csapágy](#heading).

<a name="heatmap"></a>**Hőtérkép**: Olyan adatvizualizáció, amelyben egy színtartomány egy adott terület pontjainak sűrűségét jelöli. Lásd még: Tematikus térkép.

<a name="hybrid-imagery"></a>**Hibrid képek:** Olyan műholdas vagy légi felvételek, amelyek felett útadatok és címkék vannak eltitkezve.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: Az Internet Assigned Numbers Authority rövidítése. A globális IP-címfoglalást felügyelő nonprofit csoport.

<a name="isochrone"></a>**Isochrone**: Az izochrone határozza meg azt a területet, ahol valaki egy adott időpontban utazhat egy adott helyről bármilyen irányban. Lásd [még: Elérhető tartomány](#reachable-range).

<a name="isodistance"></a>**Izotávolság**: Adott hely esetén az izochrone határozza meg azt a területet, ahol valaki egy meghatározott távolságon belül bármely irányban utazhat. Lásd [még: Elérhető tartomány](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: Kulcslyuk-jelölőnyelv néven is ismert fájlfájl a földrajzi vektoradatok, például pontok, vonalak és sokszögek tárolására. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: Multispektrális, föld körüli műholdak által kifejlesztett NASA, hogy összegyűjti képek a föld. Ezt a képet számos iparágban használják, például a mezőgazdaságban, az erdészetben és a térképészetben.

<a name="latitude"></a>**Szélesség:** Az egyenlítőtől fokban mért szögtávolság északon vagy délen.

<a name="level-of-detail"></a>**Részletességi szint**: Lásd a nagyítási szintet.

<a name="lidar"></a>**Lidar**: Betűszó a fény érzékelésére és távolságmérésére. Távérzékelési technika, amely lézerrel méri a fényvisszaverő felületektől való távolságokat.

<a name="linear-interpolation"></a>**Lineáris interpoláció**: Ismeretlen érték becslése az ismert értékek közötti lineáris távolság alapján.

<a name="linestring"></a>**LineString**: Egy vonal at jelölő geometria. Más néven vonallánc. 

<a name="localization"></a>**Lokalizáció**: A különböző nyelvek és kultúrák támogatása.

<a name="logistics"></a>**Logisztika**: Az emberek, járművek, készletek vagy eszközök összehangolt mozgatásának folyamata.

<a name="longitude"></a>**Hosszúság:** Az elsődleges meridiántól fokban mért szögtávolság keleti vagy nyugati irányban.

## <a name="m"></a>M

<a name="map-tile"></a>**Térképcsempe**: Egy térképvászon partícióját jelölő téglalap alakú kép. További információt a [Nagyítási szintek és a mozaikrács dokumentációjában talál.](zoom-levels-and-tile-grid.md)

<a name="marker"></a>**Jelölő**: Más néven tű vagy nyomótű egy olyan ikon, amely egy ponthelyet jelöl a térképen.

<a name="mercator-projection"></a>**Mercator-vetület**: Hengeres térképvetület, amely a szabványos térképvetületké vált hajózási célokra, mivel képes állandó pálya vonalait, az úgynevezett rhumb vonalakat egyenes szegmensként ábrázolni, amelyek a meridiánokkal való szögeket megóznak. Minden sík térkép vetület torzítja a térkép formai vagy méreteit a Föld felszínének valódi elrendezéséhez képest. A Mercator vetülete eltúlozza az Egyenlítőtől távol lévő területeket, így a kisebb területek nagyobbak a térképen, ahogy közeledik a pólusokhoz. 

<a name="multilinestring"></a>**MultiLineString**: LineString objektumok gyűjteményét képviselő geometria. 

<a name="multipoint"></a>**MultiPoint**: Pontobjektumok gyűjteményét reprezentatot képviselő geometria.

<a name="multipolygon"></a>**MultiPoligon**: Sokszögobjektumok gyűjteményét képviselő geometria. Például Hawaii határának megjelenítéséhez minden szigetet sokszög körvonalaz. Így a határ a Hawaii lenne így egy MultiPolygon.

<a name="municipality"></a>**Község**: Város vagy város. 

<a name="municipality-subdivision"></a>**Községalbontás**: Egy település alkörzete, például egy szomszédság vagy a helyi terület neve, például "belváros".

## <a name="n"></a>N

<a name="navigation-bar"></a>**Navigációs sáv**: A térképen a nagyítási szint, a hangmagasság, az elforgatás és az alaptérképréteg beállítására szolgáló vezérlőkészlet.

<a name="nearby-search"></a>**Közeli keresés**: Olyan térbeli lekérdezés, amely egy ponttól rögzített egyenes vonalbeli távolságot keres (légvonalban).

<a name="neutral-ground-truth"></a>**Semleges földi igazság:** Olyan térkép, amely a címkéket az általa képviselt régió hivatalos nyelvén és a helyi parancsfájlokban jeleníti meg, ha rendelkezésre állnak.

## <a name="o"></a>O

<a name="origin"></a>**Origin**: Az a kezdőpont vagy hely, ahol a felhasználó van.

## <a name="p"></a>P

<a name="panning"></a>**Pásztázás**: A térkép bármely irányba történő mozgatásának folyamata, miközben állandó nagyítási szintet tart fenn.

<a name="parcel"></a>**Parcella**: Földterület vagy ingatlan határa.

<a name="pitch"></a>**Pitch**: A térkép dőlésének mértéke ahhoz a függőlegeshez viszonyítva, ahol a 0 egyenesen lefelé néz a térképen.

<a name="point"></a>**Pont**: Olyan geometria, amely egyetlen pozíciót jelöl a térképen. 

<a name="points-of-interest-poi"></a>**Érdekes helyek (POI):** Üzleti vállalkozás, tájékozódási pont vagy közös nevező.

<a name="polygon"></a>**Sokszög**: A térkép egy területének jelölő szilárdtest-geometria. 

<a name="polyline"></a>**Vonallánc: Vonallánc:** Vonalat ábrázoló geometria. LineString néven is ismert. 

<a name="position"></a>**Pozíció:** Egy pont hosszúsági, szélességi és magassági (x,y,z koordinátái) száma.

<a name="post-code"></a>**Irányítószám**: [Lásd: Irányítószám](#postal-code).

<a name="postal-code"></a>**Irányítószám:** Betűk vagy számok sorozata, vagy mindkettő, meghatározott formátumban. Az irányítószámot egy ország/régió postai szolgáltatása használja a földrajzi területek zónákra osztására a postai küldemények kézbesítésének egyszerűsítése érdekében.

<a name="primary-key"></a>**Elsődleges kulcs:** Az Azure Maps megosztott kulcshitelesítéshez biztosított két előfizetési kulcs közül az első. Lásd: [Megosztott kulcshitelesítés](#shared-key-authentication).

<a name="prime-meridian"></a>**Prime meridián**: A hosszúsági vonal, amely 0 fokos hosszúsági fokot jelent. Általában a hosszúsági értékek csökkennek, ha nyugatfelé haladva 180 fokig, és növeli, ha keleti irányban utazik - 180 fokra. 

<a name="prj"></a>**PRJ**: Olyan szövegfájl, amely gyakran egy Shapefile fájlt kísér, amely az adatkészlet tervezett koordináta-rendszerére vonatkozó információkat tartalmazza.

<a name="projection"></a>**Vetítés**: Térképvetületen alapuló tervezett koordináta-rendszer, például keresztirányú Mercator, Albers egyenlő terület és Robinson. Ezek lehetővé teszik a Föld gömbfelszínének térképeit egy kétdimenziós descartes-i koordinátasíkra. A tervezett koordináta-rendszereket néha térkép-vetületnek is nevezik.

## <a name="q"></a>Q

<a name="quadkey"></a>**Négykulcsos**: Négyfa-csempén belüli csempe 4-es címindexe. További információt a [Szintek nagyítása és a mozaikrács](zoom-levels-and-tile-grid.md) dokumentációja című témakörben talál.

<a name="quadtree"></a>**Négyfa:** Olyan adatstruktúra, amelyben minden csomópontnak pontosan négy gyermeke van. Az Azure Mapsben használt mozaikrendszer egy négyfa-struktúrát használ, amely a felhasználó egy szintre nagyításával minden egyes térképcsempe négy alajtóra oszlik.  További információt a [Szintek nagyítása és a mozaikrács](zoom-levels-and-tile-grid.md) dokumentációja című témakörben talál.

<a name="queries-per-second-qps"></a>**Másodpercenkénti lekérdezések (QPS)**: A szolgáltatásra vagy platformra egy másodpercen belül benyújtható lekérdezések vagy kérelmek száma. 

## <a name="r"></a>R

<a name="radial-search"></a>**Sugaras keresés**: Olyan térbeli lekérdezés, amely egy ponttól rögzített egyenes vonalbeli távolságot keres (légvonalban). 

<a name="raster-data"></a>**Raszteradatok**: Olyan cellák (vagy képpontok) mátrixa, amelyek sorokba és oszlopokba (vagy rácsba) vannak rendezve, ahol minden cella olyan értéket tartalmaz, amely az adatokat, például a hőmérsékletet jelöli. Raster's közé digitális légi fényképek, képek műholdak, digitális képek, és a beolvasott térképeket.

<a name="raster-layer"></a>**Raszterréteg**: Raszterképekből álló mozaikréteg.

<a name="reachable-range"></a>**Elérhető tartomány**: Az elérhető tartomány határozza meg azt a területet, ahol valaki egy adott időn vagy távolságon belül utazhat, a szállítási mód hoz, bármely irányban egy helyről. Lásd még: [Isochrone](#isochrone) és [Isodistance](#isodistance).

<a name="remote-sensing"></a>**Távérzékelés**: Az érzékelőadatok távolról történő gyűjtésének és értelmezésének folyamata.

<a name="rest-service"></a>**REST szolgáltatás**: A REST betűszó a reprezentációs állapotátvitelrövidítés. A REST-szolgáltatás egy URL-alapú webszolgáltatás, amely az alapvető webes technológiára támaszkodik a kommunikációhoz, a leggyakoribb módszer a HTTP GET és a POST kérelmek. Az ilyen típusú szolgáltatások általában nekem sokkal gyorsabb és kisebb, mint a hagyományos SOAP-alapú szolgáltatások.

<a name="reverse-geocode"></a>**Fordított geokód**: A koordináta átvételének és annak a címnek a meghatározásának folyamata, amely a térképen van.

<a name="reproject"></a>**Reproject**: [Lásd: Átalakítás](#transformation).

<a name="rest-service"></a>**REST szolgáltatás**: A képviseleti állapot átvitelének rövidítése. Egy decentralizált, elosztott környezetben lévő társak közötti információcsere architektúrája. A REST lehetővé teszi, hogy a különböző számítógépeken futó programok az operációs rendszertől vagy platformtól függetlenül kommunikáljanak. A szolgáltatás http-kérelmet küldhet egy egységes erőforrás-lokátornak (URL), és visszaszerezheti az adatokat.

<a name="route"></a>**Útvonal**: Két vagy több hely közötti útvonal, amely további információkat is tartalmazhat, például az útvonal mentén lévő útpontokra vonatkozó utasításokat.

<a name="requests-per-second-rps"></a>**Kérelmek másodpercenként (RPS)**: [Lásd: Lekérdezések másodpercenként (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: A forrástól függően az igazán egyszerű szindikálás rövidítése, az Erőforrás-leírás keretrendszerének (RDF) webhelyösszegzése vagy a bővített webhely összegzése. Egyszerű, strukturált XML-formátum a tartalom különböző webhelyek közötti megosztásához. Az RSS-dokumentumok olyan kulcsfontosságú metaadat-elemeket tartalmaznak, mint a szerző, a dátum, a cím, a rövid leírás és a hiperszöveges hivatkozás. Ez az információ segít a felhasználónak (vagy egy RSS-megjelenítői szolgáltatásnak) eldönteni, hogy mely anyagokat érdemes további vizsgálatra.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Műholdas képek:** Olyan képek, amelyeket a repülőgépek és a műholdak rögzítettek, amelyek egyenesen lefelé mutatnak.

<a name="secondary-key"></a>**Másodlagos kulcs:** Az Azure Maps megosztott kulcshitelesítéshez biztosított két előfizetési kulcs közül a második. Lásd: [Megosztott kulcshitelesítés](#shared-key-authentication).

<a name="shapefile-shp"></a>**Shapefile (SHP)**: Esri shapefile néven is ismert vektoradat-tárolási formátum a földrajzi jellemzők helyének, alakjának és attribútumainak tárolására. Az alakzatfájl kapcsolódó fájlok készletében van tárolva.

<a name="shared-key-authentication"></a>**Megosztott kulcsú hitelesítés:** A megosztott kulcsú hitelesítés az Azure Maps-fiók által generált kulcsok átadásán alapul, minden egyes kérelemnél az Azure Maps számára. Ezeket a kulcsokat gyakran előfizetési kulcsoknak nevezik. A kulcsok biztonsága érdekében ajánlott rendszeresen újragenerálni a kulcsokat. Két kulcs áll rendelkezésre, így az egyik kulcs használatával karbantarthatja a kapcsolatokat a másik újragenerálása közben. A kulcsok újragenerálásakor frissítenie kell a fiókot elérő alkalmazásokat az új kulcsok használatához. Ha többet szeretne megtudni az Azure Maps-hitelesítésről, olvassa el az Azure Maps és az [Azure AD](azure-maps-authentication.md) és [a Hitelesítés kezelése című témakört az Azure Mapsben.](how-to-manage-authentication.md)

<a name="software-development-kit-sdk"></a>**Szoftverfejlesztői készlet (SDK):** Dokumentációk, mintakódok és mintaalkalmazások gyűjteménye, amelyek segítségével a fejlesztők API-t használhatnak alkalmazások készítéséhez.

<a name="spherical-mercator-projection"></a>**Gömb mercator vetítés**: [Lásd: Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Térbeli lekérdezés**: Térbeli műveletet végző szolgáltatáskérés. Például radiális keresés, vagy útvonalkeresés.

<a name="spatial-reference"></a>**Térbeli hivatkozás**: A földrajzi entitások pontos megkeresésére használt koordináta-alapú helyi, regionális vagy globális rendszer. Ez határozza meg a koordináta-rendszert, amely a térképkoordinátákat a valós világ helyeihez kapcsolja. A térbeli hivatkozások biztosítják, hogy a különböző rétegekből vagy forrásokból származó térbeli adatok integrálhatók legyenek a pontos megtekintéshez vagy elemzéshez. Az Azure Maps az [EPSG:3857](https://epsg.io/3857) koordináta-referenciarendszert és a WGS 84-et használja a bemeneti geometriai adatokhoz.

<a name="sql-spatial"></a>**SQL spatial**: Az SQL Azure-ba és az SQL Server 2008-ba és újabb abból épített térbeli funkciókra utal. Ez a térbeli funkció .NET-kódtárként is elérhető, amely az SQL Server kiszolgálótól függetlenül használható. További információt a [Spatial Data (SQL Server) dokumentációjában](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) talál további információkért.

<a name="subscription-key"></a>**Előfizetési kulcs**: Lásd: [Megosztott kulcshitelesítés](#shared-key-authentication).

<a name="synchronous-request"></a>**Szinkron kérés:** Egy HTTP-kérelem megnyit egy kapcsolatot, és várja a választ. A böngészők korlátozzák az egy lapról létrehozható egyidejű HTTP-kérelmek számát. Ha egyszerre több hosszú ideig futó szinkron kérés történik, akkor ez a korlát elérhető. A kérelmek et a többi kérés teljesítéséig elhalasztjuk.

## <a name="t"></a>T

<a name="telematics"></a>**Telematika:** Információk küldése, fogadása és tárolása távközlési eszközökön keresztül, valamint a távoli tárgyak vezérlésének megvalósítása. 

<a name="temporal-data"></a>**Időbeli adatok**: Olyan adatok, amelyek kifejezetten az időpontokra vagy dátumokra vonatkoznak. Az időbeli adatok különálló eseményekre, például villámcsapásokra utalhatnak; mozgó tárgyak, például vonatok; vagy ismételt megfigyelések, mint például a forgalmi érzékelők számai.

<a name="terrain"></a>**Terep**: Olyan földterület, amelynek különleges jellemzője, mint például a homokos vagy a hegyvidéki terep.

<a name="thematic-maps"></a>**Tematikus térképek**: A tematikus térkép egy egyszerű térkép készült, hogy tükrözze a témát egy földrajzi területen. Az ilyen típusú leképezések gyakori forgatókönyve a felügyeleti régiók, például az országok/régiók színezése bizonyos adatmutatók alapján.

<a name="tile-layer"></a>**Mozaikréteg**: Olyan réteg, amely úgy jelenik meg, hogy térképcsempéket (téglalap alakú szakaszokat) folyamatos rétegbe szerel össze. A csempék raszteres képmozaikok vagy vektormozaikok. A rasztermozaik-rétegek általában előre jelennek meg, és a rendszer képekként tárolja őket a kiszolgálón. A raszteres mozaikrétegek nagy tárhelyet használhatnak. A vektormozaik-rétegek közel valós időben jelennek meg az ügyfélalkalmazáson belül. Így a kiszolgálóoldali tárolási követelmények kisebbek a vektormozaik-rétegek esetében.

<a name="time-zone"></a>**Időzóna**: A világ azon régiója, amely jogi, kereskedelmi és társadalmi célokra egységes standard időt tart be. Az időzónák általában az országok/régiók és azok alkörzeteinek határait követik.

<a name="transaction"></a>**Tranzakció**: Az Azure Maps tranzakciós licencelési modellt használ, ahol;

- Minden 15 kért térkép- vagy forgalmi csempéhez egy tranzakció jön létre.
- Minden API-híváshoz egy tranzakció jön létre az Azure Maps egyik szolgáltatásához. A keresés és az útválasztás példák az Azure Maps szolgáltatásra.

<a name="transformation"></a>**Átalakítás**: Az adatok különböző földrajzi koordináta-rendszerek közötti átalakításának folyamata. Előfordulhat például, hogy az Egyesült Királyságban rögzített adatok az OSGB 1936 földrajzi koordináta-rendszerén alapulnak. Az Azure Maps a WGS84 [EPSG:3857](https://epsg.io/3857) koordináta-referenciarendszer-változatát használja. Az adatok helyes megjelenítéséhez a koordinátákat egyik rendszerről a másikra kell átalakítani.

<a name="traveling-salesmen-problem-tsp"></a>**Utazó salesmen probléma (TSP)**: A Hamilton-áramkör probléma, amelyben egy eladó meg kell találni a leghatékonyabb módja annak, hogy látogassa meg egy sor megáll, majd térjen vissza a kiindulási helyre.  

<a name="trilateration"></a>**Trilateráció**: A földfelszínen lévő pont helyzetének két másik ponthoz viszonyítva történő meghatározásának folyamata, mindhárom pont közötti távolság mérésével.

<a name="turn-by-turn-navigation"></a>**Turn-by-turn navigáció**: Olyan alkalmazás, amely útvonalutasításokat biztosít az útvonal minden egyes lépéséhez, ahogy a felhasználók közelednek a következő manőverhez.

## <a name="v"></a>V

<a name="vector-data"></a>**Vektoradatok:** Pontok, vonalak vagy sokszögekként ábrázolt koordináta alapú adatok.

<a name="vector-tile"></a>**Vektorcsempe**: Nyílt adatspecifikáció a térinformatikai vektoradatok tárolására, ugyanazt a mozaikrendszert használva, mint a térképvezérlő. Lásd még: [Mozaikréteg](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Járműút-probléma (VRP):** Olyan problémaosztály, amelyben egy járműpark rendezett útvonalait úgy számítják ki, hogy a korlátozások at figyelembe véve figyelembe veszik. Ezek a korlátok magukban foglalhatják a kézbesítési időablakokat, a több útvonalkapacitást és az utazás időtartamának korlátozását.

<a name="voronoi-diagram"></a>**Voronoi diagram**: A tér felosztása területekre, vagy sejtekre, amelyek geometriai objektumokat, általában pontjellemzőket foglalnak körül. Ezeknek a sejteknek vagy sokszögeknek meg kell felelniük a Delaunay háromszögek kritériumainak. Egy területen belül minden hely közelebb van a körülvett objektumhoz, mint a készlet bármely más objektumához. Voronoi diagramok gyakran használják körülhatotják területek befolyása körül földrajzi jellemzők. 

## <a name="w"></a>W

<a name="waypoint"></a>**Útpont**: Az útpont egy meghatározott földrajzi hely, amelyet a hosszúság és a szélesség határoz meg, és amelyet navigációs célokra használnak. Gyakran egy olyan pontot jelölnek, ahol valaki végignavigál egy útvonalon.

<a name="waypoint-optimization"></a>**Útpont optimalizálása**: Az útpontok halmazának átrendezése az összes megadott útponton való áthaladáshoz szükséges utazási idő vagy távolság minimalizálása érdekében. Az optimalizálás összetettségétől függően ezt az optimalizálást gyakran [utazási üzletemberek problémájának](#traveling-salesmen-problem-tsp) vagy [járműútválasztási problémának nevezik.](#vehicle-routing-problem-vrp)

<a name="web-map-service-wms"></a>**Webmap-szolgáltatás (WMS):** A WMS egy nyílt földrajzi konzorciumi (OGC) szabvány, amely képalapú térképszolgáltatásokat határoz meg. A WMS-szolgáltatások igény szerint térképképeket biztosítanak a térkép bizonyos területeihez. A képek előre renderelt szimbólumokat tartalmaznak, és a szolgáltatás által meghatározott számos elnevezett stílus egyikében jelenhetnek meg.

<a name="web-mercator"></a>**Web Mercator**: Más néven gömb mercator vetítés. Ez egy kis változata a Mercator vetítés, az egyik elsősorban a web-alapú térképészeti programok. Ugyanazokat a képleteket használja, mint a szabványos Mercator vetület, mint a kis léptékű térképekhez. A Web Mercator azonban a gömb alakú képleteket használja minden léptékben, de a nagyméretű Mercator térképek általában a vetület ellipszoid formáját használják. Az eltérés globális szinten észrevehetetlen, de a helyi területek térképei kissé eltérnek a valódi ellipszis Mercator térképektől, azonos léptékben.

<a name="wgs84"></a>**WGS84**: A térbeli koordinátáknak a térkép felszínén lévő helyekkel való kapcsolatára használt állandók halmaza. A WGS84 adat a legtöbb online térképszolgáltató és GPS-eszköz által használt szabvány. Az Azure Maps a WGS84 [EPSG:3857](https://epsg.io/3857) koordináta-referenciarendszer-változatát használja.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z koordináta:** Lásd [magasság](#altitude). 

<a name="zip-code"></a>**Irányítószám**: [Lásd: Irányítószám](#postal-code).

<a name="Zoom level"></a>**Nagyítási szint**: Megadja a részletesség szintjét és azt, hogy a térkép mekkora része látható. Ha a nagyított egészen a szint 0, a teljes világtérkép gyakran látható. De a térkép korlátozott részleteket jelenít meg, például az ország-/régióneveket, a határokat és az óceánneveket. Ha a 17-es szinthez közelít, a térkép néhány városblokk ot jelenít meg részletes útinformációkkal. Az azure térképekben a legmagasabb nagyítási szint 22. További információt a [Nagyítási szintek és](zoom-levels-and-tile-grid.md) a mozaikrács dokumentációjában talál.

