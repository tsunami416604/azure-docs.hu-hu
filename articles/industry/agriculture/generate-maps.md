---
title: Leképezések előállítása
description: Útmutató térképek létrehozásához a FarmBeats-ben
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 635431fb87e5f164f92ab4b7a1027ee96e9d801a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891004"
---
# <a name="generate-maps"></a>Leképezések előállítása

Az Azure FarmBeats használatával a következő térképeket hozhatja ki a műholdképek és az érzékelő adatbemenetek használatával. A Maps segítségével megtekintheti a farm földrajzi helyét, és azonosíthatja az eszközök megfelelő helyét.

  -  **Érzékelő-elhelyezési Térkép** – ajánlásokat biztosít a használatban lévő érzékelők számának és a farmban való elhelyezésének módjáról.
  - **Satellite** indexek térképe – a farm vegetációs indexét és a víz indexét jeleníti meg.
  - A **talaj nedvességtartalmának leképezése** – a talaj nedvességtartalmának eloszlását mutatja be a műholdon tárolt adatforgalom és az érzékelők adatvédelme

## <a name="sensor-placement-maps"></a>Érzékelő elhelyezési leképezései

A FarmBeats-érzékelő elhelyezési leképezése segíti a talaj nedvességtartalmának érzékelők elhelyezését. A Térkép kimenete az érzékelők központi telepítésének koordinátáit tartalmazza. Ezekből az érzékelőkből származó bemeneteket a rendszer a műholdas képekkel együtt használja a talaj nedvességtartalmának hő létrehozásához.  

Ez a Térkép az év során több dátummal ellátott, a lombkorona szegmentálásával származtatott, még akkor is, ha a mennyezet a lombkorona részét képezi. Eltávolíthatja azokat az érzékelőket, amelyek nem szükségesek a helyen. Ez a Térkép útmutatást nyújt, és a pozíciót és a számokat a saját tudása alapján némileg megváltoztathatja (az érzékelők hozzáadása nem fogja visszafejlődés a talaj nedvességtartalmának leképezési eredményeit, de fennáll a valószínűsége annak, hogy az érzékelő száma csökken).  

## <a name="before-you-begin"></a>Előkészületek  

Az érzékelő elhelyezési térképének létrehozása előtt győződjön meg a következőkről:

- A farm méretének egynél több hektárnak kell lennie.
- A felhő nélküli Sentinel-jelenetek számának a kiválasztott dátumtartomány esetében hatnál nagyobbnak kell lennie.  
- Legalább hat felhőalapú Sentinel-jelenetnek rendelkeznie kell NDVI > = 0.3.

    > [!NOTE]
    > A Sentinel csak két egyidejű szálat engedélyez felhasználónként. Ennek eredményeképpen a feladatok várólistára kerülnek, és hosszabb időt is igénybe vehetnek.

### <a name="dependencies-on-sentinel"></a>A Sentinel függőségei  

A Sentinel függőségei a következők:

- A műhold-lemezképek letöltésére a Sentinel-teljesítmény függ. A Sentinel teljesítmény állapotának és karbantartási [tevékenységeinek](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)ellenőrzését.
- A Sentinel csak két egyidejű [letöltési szálat](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) engedélyez felhasználónként.
- A precíziós Térkép generációját a [Sentinel-lefedettség és az újralátogatási gyakoriság]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)fogja érinteni.

## <a name="create-sensor-placement-map"></a>Érzékelő elhelyezési Térkép létrehozása
A szakasz az érzékelő elhelyezési leképezések létrehozásának eljárásait részletezi.

> [!NOTE]
> Az érzékelő elhelyezését kezdeményezheti a **Maps** lapról vagy a **pontos térképek létrehozása** legördülő menüből a **Farm részletek** lapján.

Ehhez a következő lépések szükségesek:

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.
2. Válassza a **Maps létrehozása** elemet, és válassza az **érzékelő elhelyezése** a legördülő menüből lehetőséget.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Válassza az **érzékelő elhelyezése**lehetőséget.
  Megjelenik az érzékelő elhelyezése ablak.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Válasszon ki egy farmot a **Farm** legördülő menüből.  
   A farm kereséséhez vagy kiválasztásához görgessen a legördülő listából, vagy írja be a farm nevét a szövegmezőbe.
5. Ha az előző évhez szeretne térképet készíteni, válassza az **ajánlott** lehetőséget.
6. Ha egy egyéni dátumtartomány leképezését szeretné előállítani, válassza a **Dátumtartomány kiválasztása**lehetőséget, és adja meg azt a kezdő és záró dátumot, amelynél el szeretné készíteni az érzékelő elhelyezési térképét.
7. Válassza a **leképezések előállítása**lehetőséget.
 Megjelenik egy megerősítő üzenet a feladatok részleteivel.

  A feladat állapotáról további információt a **feladatok megtekintése**című szakaszban talál. Ha a feladatok állapota *nem sikerült*, a *hibás* állapot elemleírásában részletes hibaüzenet jelenik meg. Ebben az esetben ismételje meg a fent felsorolt lépéseket, és próbálkozzon újra.

  Ha a probléma továbbra is fennáll, tekintse meg a [hibakeresés](troubleshoot-project-farmbeats.md) szakaszt, vagy lépjen kapcsolatba az [Azure FarmBeats Fórumával a kapcsolódó naplók támogatásához](https://aka.ms/FarmBeatsMSDN) .

### <a name="view-and-download-sensor-placement-map"></a>Érzékelő elhelyezési térképének megtekintése és letöltése

Ehhez a következő lépések szükségesek:

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Válassza a **szűrő** lehetőséget az ablak bal oldali navigációs sávján.
  A **szűrő** ablak a keresési feltételeket jeleníti meg.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Válassza a **típus**, a **dátum** és a **név** lehetőséget a legördülő menüből, majd az **alkalmaz** gombra kattintva keresse meg a megtekinteni kívánt térképet.
  A feladatok létrehozásának dátuma type_farmname_YYYY-hh-nn formátumban jelenik meg.
4. Görgesse végig az elérhető térképek listáját a lap végén található navigációs sávok használatával.
5. Válassza ki a megtekinteni kívánt térképet. Megjelenik egy előugró ablak, amely a kiválasztott Térkép előnézetét jeleníti meg.
6. Válassza a **Letöltés**lehetőséget, majd töltse le az érzékelő koordinátáinak GeoJSON-fájlját.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Szatellit indexek térképe

A következő szakaszokban ismertetjük a Satellite indexek térképének létrehozásával és megtekintésével kapcsolatos eljárásokat.

> [!NOTE]
> A **Térkép oldaláról** vagy a **pontos térképek létrehozása** legördülő menüből a **Farm részletek** lapján kezdeményezheti a Satellite indexek leképezését.

A FarmBeats lehetővé teszi a normalizált különbségi vegetációs index (NDVI), a továbbfejlesztett vegetációs index (EVI) és a normalizált különbözeti (NDWI) leképezések létrehozását a farmokhoz. Ezek az indexek segítenek meghatározni, hogyan növekszik a termés, vagy a múltban, valamint a reprezentatív vízszinteket a helyszínen.


> [!NOTE]
> A Térkép létrehozási napjaihoz Sentinel-rendszerkép szükséges.


## <a name="create-satellite-indices-map"></a>Satellite indexek létrehozása – Térkép

Ehhez a következő lépések szükségesek:

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.
2. Válassza a **Maps létrehozása** elemet, és válassza a **Satellite indexek** lehetőséget a legördülő menüből.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Válassza a **szatellit indexek**lehetőséget.
  Megjelenik a Satellite indexek ablak.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Válasszon ki egy farmot a legördülő menüből.  
   A farm kereséséhez és kiválasztásához görgessen a legördülő listából, vagy írja be a farm nevét.   
5. Ha a múlt héten szeretne térképet készíteni, válassza **a hét** lehetőséget.
6. Ha leképezést szeretne készíteni az egyéni dátumtartomány számára, válassza a **Dátumtartomány kiválasztása**lehetőséget, és adja meg a kezdő és a záró dátumot, amelyhez a Satellite indexek leképezését szeretné készíteni.
7. Válassza a **leképezések előállítása**lehetőséget.
    Megjelenik egy megerősítő üzenet a feladatok részleteivel.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    A feladat állapotáról további információt a **feladatok megtekintése**című szakaszban talál. Ha a feladatok állapota *nem sikerült*, a *hibás* állapot elemleírásában részletes hibaüzenet jelenik meg. Ebben az esetben ismételje meg a fent felsorolt lépéseket, és próbálkozzon újra.

    Ha a probléma továbbra is fennáll, tekintse meg a [hibakeresés](troubleshoot-project-farmbeats.md) szakaszt, vagy lépjen kapcsolatba az [Azure FarmBeats Fórumával a kapcsolódó naplók támogatásához](https://aka.ms/FarmBeatsMSDN) .

### <a name="view-and-download-map"></a>Térkép megtekintése és letöltése

Ehhez a következő lépések szükségesek:

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Válassza a **szűrő** lehetőséget az ablak bal oldali navigációs sávján, a **szűrő** ablak a keresési feltételeket jeleníti meg.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Válassza a **típus**, a **dátum** és a **név** lehetőséget a legördülő menüből, majd az **alkalmaz** gombra kattintva keresse meg a megtekinteni kívánt térképet.
  A feladatok létrehozásának dátuma type_farmname_YYYY-hh-nn formátumban jelenik meg.

4. Görgesse végig az elérhető térképek listáját a lap végén található navigációs sávok használatával.
5. A **farm nevének** és **dátumának**minden kombinációja esetében a következő három Térkép érhető el:
    - NDVI
    - EVI
    - NDWI
6. Válassza ki a megtekinteni kívánt térképet. Megjelenik egy előugró ablak, amely a kiválasztott Térkép előnézetét jeleníti meg.
7. Válassza a **Letöltés** legördülő menüt a letöltési formátum kiválasztásához és a Térkép letöltéséhez és a számítógép helyi mappájába való tárolásához.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>A talaj nedvesség-hő beolvasása

A talaj nedvességtartalma az a víz, amelyet a rendszer a talaj részecskék közötti térközben tárol. A talaj nedvesség-hő segít megérteni a talaj nedvességtartalmának adatait bármilyen mélységben, a farmokon belüli nagy felbontásban. Pontos és felhasználható nedvesség-hő létrehozásához az érzékelők egységes üzembe helyezésére van szükség, amelyben az érzékelők ugyanabból a szolgáltatóból származnak. A különböző szolgáltatók eltérőek lehetnek a talaj nedvességtartalmának mérésével, valamint a kalibrálási különbségekkel. A hő az adott mélységben üzembe helyezett érzékelők használatával generáljuk.

### <a name="before-you-begin"></a>Előkészületek  

A talaj nedvességtartalmának létrehozása előtt győződjön meg arról, hogy a következők hő:

- Legalább három nedvesség-érzékelőt kell üzembe helyezni. A Microsoft azt javasolja, hogy ne hozzon létre egy talajvédelmi nedvesség-leképezést az érzékelők üzembe helyezése és a farmhoz társítása előtt.  
- A talaj nedvességtartalmának hő a Sentinel Path lefedettsége, a Cloud Cover és a Cloud Shadow is befolyásolja. Legalább egy Cloud Free Sentinel-színtérnek elérhetőnek kell lennie az elmúlt 120 napban, azon a napon, ameddig a talaj nedvességtartalmának leképezését kérték.
- A farmon üzembe helyezett érzékelők legalább felének online állapotban kell lennie, és adatfolyamot kell biztosítania az adatközpontnak.
- A hő az azonos szolgáltatótól származó Sensor-mértékek használatával kell létrehozni.


## <a name="create-soil-moisture-heatmap"></a>Talaj nedvesség-hő létrehozása

Ehhez a következő lépések szükségesek:

1. A kezdőlapon kattintson a Maps ( **térképek** ) elemre a bal oldali navigációs menüben a Maps oldal megtekintéséhez.
2. Válassza a **térképek létrehozása** elemet, és válassza a **talaj nedvesség** elemet a legördülő menüből.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Válassza ki a **talaj nedvességét**.
    Megjelenik a talaj nedvessége ablak.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Válasszon ki egy farmot a **Farm** legördülő menüből.  
   A farm kereséséhez és kiválasztásához görgessen a legördülő listából, vagy írja be a farm nevét a farm kiválasztása legördülő menüből.
5. A **felszín nedvesség-érzékelői mértékének kiválasztása** legördülő menüben válassza ki a talaj nedvességtartalmának érzékelő mértékét (mélységét), amelyhez a térképet elő szeretné állítani.
Az érzékelő mértékének megkereséséhez nyissa meg az **érzékelők**elemet, és válassza ki a talaj nedvességtartalmának érzékelőjét. Ezután az **érzékelő tulajdonságai** szakaszban használja az értéket a **mérték neve** mezőben.
6. Ha **még ma** vagy **ezen a héten**szeretne előállítani, válassza ki az egyik lehetőséget.
7. Ha egyéni dátumtartományt szeretne előállítani, válassza a **Dátumtartomány kiválasztása**lehetőséget, és adja meg azt a kezdő és záró dátumot, amelynek a talaj nedvességtartalmának leképezését elő szeretné állítani.
8. Válassza a **leképezések előállítása**lehetőséget.
 Megjelenik egy megerősítő üzenet a feladatok részleteivel.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    A feladat állapotáról további információt a **feladatok megtekintése**című szakaszban talál. Ha a feladatok állapota *nem sikerült*, a *hibás* állapot elemleírásában részletes hibaüzenet jelenik meg. Ebben az esetben ismételje meg a fent felsorolt lépéseket, és próbálkozzon újra.

    Ha a probléma továbbra is fennáll, tekintse meg a [hibakeresés](troubleshoot-project-farmbeats.md) szakaszt, vagy lépjen kapcsolatba az [Azure FarmBeats Fórumával a kapcsolódó naplók támogatásához](https://aka.ms/FarmBeatsMSDN) .

### <a name="view-and-download-map"></a>Térkép megtekintése és letöltése

Ehhez a következő lépések szükségesek:

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Válassza ki a **szűrő** lehetőséget az ablak bal oldali navigációs sávján, a **szűrő** ablakban megjelenik, ahol kereshet térképeket.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Válassza a **típus**, a **dátum** és a **név** lehetőséget a legördülő menüből, majd az **alkalmaz** gombra kattintva keresse meg a megtekinteni kívánt térképet. A feladatok létrehozásának dátuma type_farmname_YYYY-hh-nn formátumban jelenik meg.
4. Válassza a tábla fejlécek melletti **Rendezés** ikont a farm, a dátum, a létrehozás, a feladat azonosítója és a feladattípus alapján történő rendezéshez.
5. Görgesse végig az elérhető térképek listáját az oldal végén található navigációs gombok használatával.
6. Válassza ki a megtekinteni kívánt térképet. Megjelenik egy előugró ablak, amely a kiválasztott Térkép előnézetét jeleníti meg.
7. Válassza a **Letöltés** legördülő menüt, hogy kiválassza a letöltési formátumot, és a Térkép le legyen töltve, és a megadott mappában legyen tárolva.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
