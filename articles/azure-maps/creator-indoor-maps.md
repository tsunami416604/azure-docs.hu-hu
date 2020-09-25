---
title: Beltéri térképek használata Azure Maps Creatorben
description: Ez a cikk a Azure Maps Creator-szolgáltatásokra vonatkozó fogalmakat ismerteti
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ad1b7ae08e74f455190c44a813dde44b0b683014
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91311359"
---
# <a name="creator-for-indoor-maps"></a>A beltéri térképek létrehozója

Ez a cikk bemutatja a Azure Maps létrehozója által alkalmazott fogalmakat és eszközöket. Javasoljuk, hogy olvassa el ezt a cikket, mielőtt elkezdi használni a Azure Maps Creator API-t és az SDK-t.

A Creator használatával a térképi funkciókkal rendelkező alkalmazásokat hozhat létre a beltéri térképi adatközpontok alapján. Ez a cikk a térképi adatai feltöltésének, átalakításának, létrehozásának és használatának folyamatát ismerteti. A teljes munkafolyamat az alábbi ábrán látható.

![Létrehozó Térkép munkafolyamata](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>Azure Maps létrehozó létrehozása

A Creator Services használatához Azure Maps létrehozót egy Azure Maps-fiókban kell létrehozni. Azure Maps létrehozójának Azure Maps-ben való létrehozásával kapcsolatos információkért lásd: a [Azure Maps létrehozójának kezelése](how-to-manage-creator.md).

## <a name="upload-a-drawing-package"></a>Rajzfájl feltöltése

A létrehozó a feltöltött rajzfájl konvertálásával gyűjti a beltéri térképi adatokat. A rajzolási csomag egy felépített vagy átalakított létesítményt jelöl. A csomagra vonatkozó követelményekkel kapcsolatos információkért lásd: [rajzi csomag követelményei](drawing-requirements.md).

Egy rajzfájl feltöltéséhez használja a [Azure Maps Adatfeltöltő API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) -t.  A sikeres feltöltés után az adatfeltöltő API egy felhasználói adatazonosítót () ad vissza `udid` . A `udid` következő lépésben a feltöltött csomag belső leképezési adatként való átalakítására lesz szükség.

## <a name="convert-a-drawing-package"></a>Rajzfájl konvertálása

A [Azure Maps átalakítási szolgáltatás](https://docs.microsoft.com/rest/api/maps/conversion) egy feltöltött rajzobjektumot helyez át a beltéri térképi adategységbe. A konverziós szolgáltatás a csomagot is ellenőrzi. Az érvényesítési problémák két típusba sorolhatók: hibák és figyelmeztetések. Ha bármilyen hiba észlelhető, az átalakítási folyamat sikertelen lesz. Ha a rendszer figyelmeztetések észlelését észleli, az átalakítás sikeres lesz. Javasoljuk azonban, hogy tekintse át és oldja fel az összes figyelmeztetést. A figyelmeztetés azt jelenti, hogy az átalakítás egy részét figyelmen kívül hagyták vagy automatikusan rögzítették. A figyelmeztetések feloldása hibát okozhat az utóbbi folyamatokban. További információ: a [csomagra vonatkozó figyelmeztetések és hibák kirajzolása](drawing-conversion-error-codes.md).

Ha hiba történik, az átalakítási szolgáltatás hivatkozást biztosít a [Azure Maps rajzolási hibákra](drawing-error-visualizer.md) , amely az önálló webalkalmazást jeleníti meg. A rajzolási hiba láthatóvá tételével megvizsgálhatja az átalakítási folyamat során felmerülő [rajzi csomagok figyelmeztetéseit és hibáit](drawing-conversion-error-codes.md) . A hibák kijavítása után megpróbálkozhat a csomag feltöltésével és átalakításával.

## <a name="create-indoor-map-data"></a>Beltéri térképi adatkészletek létrehozása

Azure Maps Creator három szolgáltatást biztosít:

* [Adatkészlet szolgáltatás](https://docs.microsoft.com/rest/api/maps/dataset/createpreview).
Az adatkészlet szolgáltatással hozzon létre egy adatkészletet egy konvertált rajz-csomag adatainak használatával.
* [Tileset szolgáltatás](https://docs.microsoft.com/rest/api/maps/tileset/createpreview).
Az tileset szolgáltatás használatával hozzon létre egy adatkészletet vektoros ábrázolással. Az alkalmazások használhatnak egy tileset az adatkészlet vizualizációs csempe alapú nézetének megjelenítéséhez.
* [Szolgáltatás állapota szolgáltatás](https://docs.microsoft.com/rest/api/maps/featurestate). A szolgáltatás állapota szolgáltatással támogatja a dinamikus leképezések stílusát. A dinamikus Térkép stílusa lehetővé teszi, hogy az alkalmazások valós idejű eseményeket tükrözzék a IoT rendszer által biztosított tárhelyeken.

### <a name="datasets"></a>Adathalmazok

Az adatkészlet egy beltéri térképi funkciók gyűjteménye. A beltéri leképezés funkciói a konvertált rajzi csomagban definiált létesítményeket jelölik. Miután létrehozta az adatkészletet az [adatkészlet szolgáltatással](https://docs.microsoft.com/rest/api/maps/dataset/createpreview), tetszőleges számú [tilesets](#tilesets) vagy [szolgáltatásbeli statesets](#feature-statesets)hozhat létre.

Az [adatkészlet szolgáltatás](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) bármikor lehetővé teszi a fejlesztők számára, hogy létesítményeket adjanak hozzá vagy távolítanak el egy meglévő adatkészlethez. Ha további információt szeretne arról, hogyan frissíthet egy meglévő adatkészletet az API-val, tekintse meg az [adatkészlet-szolgáltatás](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)hozzáfűzési beállításait. Az adatkészletek frissítését bemutató példát az [adatkezeléssel](#data-maintenance)foglalkozó témakörben talál.

### <a name="tilesets"></a>Tilesets

A tileset olyan vektoros adatgyűjtemény, amely egységes rácsos csempéket jelöl. A fejlesztők a [tileset szolgáltatás](https://docs.microsoft.com/rest/api/maps/tileset/createpreview) segítségével tilesets hozhatnak létre egy adatkészletből.

A különböző tartalmi szakaszok megjelenítéséhez több tilesets is létrehozhat ugyanabból az adatkészletből. Tegyük fel például, hogy egy tileset bútorokkal és berendezéssel, valamint egy másik tileset bútorok és berendezések nélkül.  Dönthet úgy is, hogy létrehoz egy tileset a legfrissebb adatfrissítésekkel, és egyet a legfrissebb adatfrissítések nélkül.

A vektoros adatok mellett a tileset metaadatokat biztosít a Térkép renderelési optimalizálásához. A tileset-metaadatok például a tileset minimális és maximális nagyítási szintjét tartalmazzák. A metaadatok egy határoló mezőt is biztosítanak, amely meghatározza a tileset földrajzi mértékét. A határolókeret lehetővé teszi, hogy az alkalmazások programozott módon állítsa be a megfelelő középpontot. További információ a tileset-metaadatokról: [tileset List API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview).

Miután létrejött a tileset, azt a [Render V2 szolgáltatás](#render-v2-service)kérheti le.

Ha egy tileset elavulttá válik, és már nem hasznos, törölheti a tileset. A tilesets törlésével kapcsolatos további információkért lásd az  [adatkarbantartást](#data-maintenance)ismertető témakört.

>[!NOTE]
>A tileset független a létrehozott adatkészlettől. Ha tilesets hoz létre egy adatkészletből, majd később frissíti az adatkészletet, akkor a tilesets nem frissül. Az adatkészlet változásainak tükrözéséhez új tilesets kell létrehoznia. Hasonlóképpen, ha töröl egy tileset, az adatkészlet nem lesz hatással.

### <a name="feature-statesets"></a>Szolgáltatás statesets

A statesets olyan dinamikus tulajdonságok (*állapotok*) gyűjteményei, amelyek adatkészlet-funkciókhoz, például helyiségekhez vagy berendezésekhez vannak rendelve. Az *állapot* például lehet hőmérséklet vagy kihasználtság. Minden *állapot* egy kulcs/érték pár, amely tartalmazza a tulajdonság nevét, az értéket és az utolsó frissítés időbélyegét.

A szolgáltatás [állapotának szolgáltatása](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview) lehetővé teszi az adatkészlet szolgáltatás stateset létrehozását és kezelését. A stateset egy vagy több *állapota*határozza meg. Az egyes szolgáltatások, például a szobák rendelkezhetnek egy *állapottal* .

A stateset lévő egyes *állapotok* értéke frissíthető vagy lekérhető IoT-eszközök vagy más alkalmazások számára.  A [szolgáltatás állapotának frissítési API](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview)-ját használva például a terület kihasználtságát mérő eszközök szisztematikusan közzétehetik a helyiségek állapotának változását.

Az alkalmazások a funkciók stateset használatával dinamikusan tehetik elérhetővé a létesítmények funkcióit a jelenlegi állapotuk és a megfelelő Térkép stílusa alapján. További információ a funkciók statesets a renderelési Térképekben való használatáról: [beltéri web SDK modul](#indoor-maps-module).

>[!NOTE]
>A tilesets-hez hasonlóan az adatkészlet módosítása nincs hatással a meglévő funkció stateset, és a stateset törlése nem lesz hatással arra az adatkészletre, amelyhez csatolva van.

## <a name="using-indoor-maps"></a>Beltéri térképek használata

### <a name="render-v2-service"></a>Render V2 szolgáltatás

A Azure Maps [Render V2 szolgáltatás-Get Map csempe API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) kiterjeszthető a Creator tilesets támogatására.

[Render V2 szolgáltatás – Térkép állapota csempe API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) lehetővé teszi az alkalmazások számára a tilesets kérését. A tilesets ezután integrálható egy Térkép vezérlőelembe vagy SDK-ba. A render v2 szolgáltatást használó Térkép vezérlőelemre például a [beltéri térképek modulban](#indoor-maps-module)talál példát.

### <a name="web-feature-service-api"></a>Web Feature Service API

Az adatkészleteket a [web Feature Service (WFS) API](https://docs.microsoft.com/rest/api/maps/wfs)használatával kérdezheti le. A WFS követi a [nyílt TÉRINFORMATIKAI konzorcium API-funkciókat](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). A WFS API lehetővé teszi, hogy az adatkészleten belül lekérdezési szolgáltatásokat is lekérdezzen. A WFS használatával például megkeresheti az adott létesítmény és a padló szintjének összes közepes méretű tárgyalóját.

### <a name="indoor-maps-module"></a>Beltéri térképek modul

A [Azure Maps web SDK](https://docs.microsoft.com/azure/azure-maps/) a beltéri térképek modult is tartalmazza. Ez a modul kiterjesztett funkciókat biztosít a Azure Maps *térképkezelés* Library-hez. A beltéri térképek modul a Creatorben létrehozott beltéri térképeket jeleníti meg. Integrálja a widgeteket, például a *padló választóját*, ami segít a felhasználóknak megjeleníteni a különböző szinteket.

A beltéri térképek modul lehetővé teszi, hogy olyan webalkalmazásokat hozzon létre, amelyek integrálják a beltéri leképezési [szolgáltatásokat más Azure Maps szolgáltatásokkal](https://docs.microsoft.com/azure/azure-maps/). Az alkalmazás leggyakoribb beállításai közé tartozhatnak az ismeretek hozzáadása a beltéri térképekhez más térképekről, például az úthálózatról, a képekről, az időjárásról és az átvitelről.

A beltéri térképek modul támogatja a dinamikus leképezések stílusát is. Részletes útmutató a funkciók stateset dinamikus stílusának megvalósításához az alkalmazásokban: [a beltéri Térkép modul használata](how-to-use-indoor-module.md)

### <a name="azure-maps-integration"></a>Azure Maps integráció

A beltéri térképekhez kapcsolódó megoldások fejlesztése során megtudhatja, hogyan integrálhatja a meglévő Azure Maps képességeket. Például az eszközök nyomon követése vagy a biztonsági forgatókönyvek a [Azure Maps GEOKERÍTÉSEN API](https://docs.microsoft.com/rest/api/maps/spatial/postgeofence) és a Creator Indoor Maps használatával valósíthatók meg. A Geokerítésen API segítségével meghatározható például, hogy egy feldolgozó adott beltéri területekre lép-e be vagy kilép. A Azure Maps IoT-telemetria való összekapcsolásával kapcsolatban [itt](tutorial-iot-hub-maps.md)talál további információt.

### <a name="data-maintenance"></a>Adatkarbantartás

 Azure Maps létrehozói lista, frissítés és törlés API lehetővé teszi az adatkészletek, a tilesets és a szolgáltatás statesets listázását, frissítését és törlését.

>[!NOTE]
>Amikor áttekinti az elemek listáját, és úgy dönt, hogy törli őket, figyelembe kell vennie a törlés hatását az összes függő API-ra vagy alkalmazásra vonatkozóan. Ha például olyan tileset szeretne törölni, amelyet egy alkalmazás jelenleg használ a [Render v2 – Map csempe-kinyerő API-](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)val, a tileset törlésével az alkalmazás nem fogja megjeleníteni a tileset.

### <a name="example-updating-a-dataset"></a>Példa: adatkészlet frissítése

Az alábbi példa bemutatja, hogyan frissítheti az adatkészleteket, hogyan hozhat létre új tileset, és törölhet egy régi tileset.

1. Az új rajzfájl feltöltéséhez és átalakításához kövesse a [rajzolási csomag feltöltése](#upload-a-drawing-package) és a [rajzolási csomagok konvertálása](#convert-a-drawing-package) szakasz lépéseit.

2. Az [adatkészlet létrehozása API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) segítségével fűzze hozzá a konvertált adatokat a meglévő adatkészlethez.

3. A [tileset Create API](https://docs.microsoft.com/rest/api/maps/tileset/createpreview) használatával hozzon létre egy új tileset a frissített adatkészletből. Mentse az új tilesetId a 4. lépéshez.

4. Frissítse az alkalmazás tileset-azonosítóját a frissített Campus-adatkészlet vizualizációjának engedélyezéséhez. Ha a régi tileset már nincs használatban, törölheti.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: létrehozói beltéri Térkép létrehozása](tutorial-creator-indoor-maps.md)
