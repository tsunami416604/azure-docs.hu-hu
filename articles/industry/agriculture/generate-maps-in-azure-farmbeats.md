---
title: Térképek létrehozása
description: Ez a cikk ismerteti, hogyan hozhat létre leképezéseket az Azure FarmBeats.This article describes how to generate maps in Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271771"
---
# <a name="generate-maps"></a>Térképek létrehozása

Az Azure FarmBeats használatával a következő leképezéseket hozhat létre műholdas képekkel és érzékelőadatokkal. A Térképek segítségével megtekintheti a farm földrajzi helyét, és meghatározhatja az eszközök megfelelő helyét.

  - **Érzékelő elhelyezési térképe**: Javaslatokat ad arra vonatkozóan, hogy hány érzékelőt kell használni, és hol kell elhelyezni őket egy farmon.
  - **Műholdas indexek térkép**: Egy gazdaság vegetációs és vízindexét jeleníti meg.
  - **Talaj Nedvesség hőtérkép**: A talaj nedvességeloszlását mutatja a műholdas adatok és az érzékelő adatok beolvasztásával.

## <a name="sensor-placement-map"></a>Érzékelő elhelyezési térképe

A FarmBeats érzékelő elhelyezése térkép segíti a talaj nedvességérzékelők elhelyezését. A térkép kimenete az érzékelők telepítéséhez használható koordináták listájából áll. A bemenetek ezen érzékelők használják együtt műholdas képek generálni a talaj nedvesség hőtérkép.

Ez a térkép a lombkorona szegmentálásával származik, ahogy az az év során több dátumon is látható. Még a csupasz talaj és az épületek is a lombkorona részét képezik. Eltávolíthatja azokat az érzékelőket, amelyek nem szükségesek a helyszínen. Ez a térkép útmutatást nyújt, és az egyéni tudás alapján kissé megváltoztathatja a pozíciót és a számokat. Az érzékelők hozzáadása nem csökkenti a talaj nedvességhőtérképének eredményeit, de fennáll annak a lehetősége, hogy a hőtérkép pontossága romlik, ha az érzékelő száma csökken.

## <a name="before-you-begin"></a>Előkészületek

Az érzékelőelhelyezési térkép létrehozása előtt teljesítse az alábbi előfeltételeket:

- A gazdaság méretének egy hektárnál többnek kell lennie.
- A felhőmentes Sentinel-jelenetek számának a kiválasztott dátumtartományban hatnál többnek kell lennie.
- Legalább hat felhőmentes Sentinel-jelenetnek 0,3-nál nagyobb vagy azzal egyenlő normalizált különbségű vegetációs indexszel (NDVI) kell rendelkeznie.

    > [!NOTE]
    > A Sentinel felhasználónként csak két egyidejű szálat engedélyez. Ennek eredményeképpen a feladatok várólistára kerülnek, és hosszabb időt vehet igénybe.

### <a name="dependencies-on-sentinel"></a>Függőségek a Sentineltől

A következő függőségek a Sentinelre vonatkoznak:

- A Sentinel teljesítményétől függünk a műholdas képek letöltéséhez. Ellenőrizze a Sentinel teljesítményállapotát és karbantartási [tevékenységeit.](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)
- A Sentinel felhasználónként csak két egyidejű [letöltési szálat](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) engedélyez.
- A precíziós térképgenerálást befolyásolja a [Sentinel lefedettsége és a revisit gyakoriság.]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)

## <a name="create-a-sensor-placement-map"></a>Érzékelőelhelyezési térkép létrehozása
Ez a szakasz az érzékelőelhelyezési térképek létrehozásának eljárásait részletezi.

> [!NOTE]
> Az érzékelőelhelyezési térképet a **Térképek** lapon vagy a **Pontossági térképek létrehozása legördülő** menüből kezdeményezheti a **Farm részletei** lapon.

Kövesse az alábbi lépéseket.

1. A kezdőlapon nyissa meg a bal oldali navigációs menü **Térképek** lapján.
2. Válassza **a Térképek létrehozása**lehetőséget, majd a legördülő menüBen válassza az Érzékelő **elhelyezése** parancsot.

    ![Érzékelő elhelyezésének kiválasztása](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Miután **kiválasztotta az Érzékelő elhelyezése**lehetőséget, megjelenik az **Érzékelő elhelyezése** ablak.

    ![Érzékelő elhelyezése ablak](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Válasszon ki egy farmot a **Farm** legördülő menüből.
   A farm kereséséhez és kijelöléséhez görgethet a legördülő listában, vagy beírhatja a farm nevét a szövegmezőbe.
5. Az elmúlt évre vonatkozó térkép létrehozásához válassza az **Ajánlott**lehetőséget.
6. Ha egyéni dátumtartományhoz szeretne térképet létrehozni, válassza a **Dátumtartomány kiválasztása**lehetőséget. Adja meg azt a kezdő és záró dátumot, amelyhez létre szeretné hozni az Érzékelőelhelyezés imát.
7. Válassza **a Térképek létrehozása**lehetőséget.
 Megjelenik egy megerősítő üzenet a feladat részleteivel.

  A feladat állapotáról a **Feladatok megtekintése**című szakaszban talál további információt. Ha a feladat állapota *sikertelen,* akkor egy részletes hibaüzenet jelenik meg a *Sikertelen* állapot elemleírásában. Ebben az esetben ismételje meg az előző lépéseket, majd próbálkozzon újra.

  Ha a probléma továbbra is fennáll, tekintse meg a [Hibaelhárítás szakaszban,](troubleshoot-azure-farmbeats.md) vagy lépjen kapcsolatba az [Azure FarmBeats fórum a](https://aka.ms/FarmBeatsMSDN) megfelelő naplók támogatása.

### <a name="view-and-download-a-sensor-placement-map"></a>Érzékelőelhelyezési térkép megtekintése és letöltése

Kövesse az alábbi lépéseket.

1. A kezdőlapon nyissa meg a bal oldali navigációs menü **Térképek** lapján.

    ![Válassza a bal oldali navigációs menü Térképek parancsát](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Válassza a **Szűrő** lehetőséget az ablak bal oldali navigációs sávján.
  A **Szűrő** ablak ban megjelennek a keresési feltételek.

    ![Szűrő ablak](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. A legördülő menükben válassza a **Típus**, **dátum**és **Név** értékek lehetőséget. Ezután válassza az **Alkalmaz** lehetőséget a megtekinteni kívánt térkép kereséséhez.
  A feladat létrehozásának dátuma type_farmname_YYYY-MM-DD formátumban jelenik meg.
4. Görgessen végig a rendelkezésre álló térképek listáján az oldal végén található navigációs sávok segítségével.
5. Jelölje ki a megtekinteni kívánt térképet. Egy előugró ablak jeleníti meg a kijelölt térkép előnézetét.
6. Válassza **a Letöltés**lehetőséget, és töltse le az érzékelő koordinátáinak GeoJSON-fájlját.

    ![Érzékelő elhelyezése térkép előnézete](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Műholdas indexek térképe

A következő szakaszok ismertetik a műholdas indexek térképének létrehozásával és megtekintésével járó eljárásokat.

> [!NOTE]
> Műholdas indexek leképezését a **Térképek** oldalon vagy a **Pontossági térképek létrehozása legördülő** menüből kezdeményezheti a **Farm részletei** lapon.

FarmBeats lehetővé teszi, hogy létrehoz NDVI, Enhanced Vegetation Index (EVI), és normalizált különbség víz index (NDWI) térképek gazdaságok. Ezek az indexek segítenek meghatározni, hogy a növény jelenleg növekszik, vagy nőtt a múltban, és a reprezentatív vízszint a talajban.


> [!NOTE]
> Sentinel-kép szükséges azokhoz a napokhoz, amikor a térkép létrejön.


## <a name="create-a-satellite-indices-map"></a>Műholdas indextérkép létrehozása

Kövesse az alábbi lépéseket.

1. A kezdőlapon nyissa meg a bal oldali navigációs menü **Térképek** lapján.
2. Válassza **a Térképek létrehozása**lehetőséget, majd a legördülő menü Műholdas **indexek** parancsát.

    ![Válassza a Műholdas indexek lehetőséget a legördülő menüből](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. A **Műholdas indexek**kiválasztása után megjelenik a **Műholdas indexek** ablak.

    ![Műholdas indexek ablak](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Válasszon ki egy farmot a legördülő menüből.
   A farm kereséséhez és kiválasztásához görgethet a legördülő listában, vagy megadhatja a farm nevét.   
5. Az utolsó heti térkép létrehozásához válassza a This Week (Ezen a **héten)** lehetőséget.
6. Ha egyéni dátumtartományhoz szeretne térképet létrehozni, válassza a **Dátumtartomány kiválasztása**lehetőséget. Adja meg azt a kezdési és befejezési dátumot, amelyhez létre szeretné hozni a Műholdas indexek térképét.
7. Válassza **a Térképek létrehozása**lehetőséget.
    Megjelenik egy megerősítő üzenet a feladat részleteivel.

    ![Műholdas indexek térkép-visszaigazolási üzenete](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    A feladat állapotáról a **Feladatok megtekintése**című szakaszban talál további információt. Ha a feladat állapota *sikertelen,* akkor egy részletes hibaüzenet jelenik meg a *Sikertelen* állapot elemleírásában. Ebben az esetben ismételje meg az előző lépéseket, majd próbálkozzon újra.

    Ha a probléma továbbra is fennáll, tekintse meg a [Hibaelhárítás szakaszban,](troubleshoot-azure-farmbeats.md) vagy lépjen kapcsolatba az [Azure FarmBeats fórum a](https://aka.ms/FarmBeatsMSDN) megfelelő naplók támogatása.

### <a name="view-and-download-a-map"></a>Térkép megtekintése és letöltése

Kövesse az alábbi lépéseket.

1. A kezdőlapon nyissa meg a bal oldali navigációs menü **Térképek** lapján.

    ![Térképek kiválasztása](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Válassza a **Szűrő** lehetőséget az ablak bal oldali navigációs sávján. A **Szűrő** ablak ban megjelennek a keresési feltételek.

    ![A Szűrő ablak keresési feltételeket jelenít meg](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. A legördülő menükben válassza a **Típus**, **dátum**és **Név** értékek lehetőséget. Ezután válassza az **Alkalmaz** lehetőséget a megtekinteni kívánt térkép kereséséhez.
  A feladat létrehozásának dátuma type_farmname_YYYY-MM-DD formátumban jelenik meg.

4. Görgessen végig a rendelkezésre álló térképek listáján az oldal végén található navigációs sávok segítségével.
5. A **farmnév** és a **dátum**minden kombinációjához a következő három térkép áll rendelkezésre:
    - NDVI között
    - Evi
    - NDWI között
6. Jelölje ki a megtekinteni kívánt térképet. Egy előugró ablak jeleníti meg a kijelölt térkép előnézetét.
7. A **Download** letöltési formátum kiválasztásához válassza a Letöltés lehetőséget a legördülő menüből. A térkép letöltése és tárolása a számítógép helyi mappájában történik.

    ![Kiválasztott műholdas indexek térkép előnézete](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Talaj Nedvesség hőtérkép

A talaj nedvessége az a víz, amit a talajrészecskék közötti terekben tartanak.A Soil Moisture hőtérkép segítségével bármilyen mélységben, nagy felbontásban, a gazdaságon belül megértheti a talaj nedvességtartalmát. A pontos és használható talajnedvesség hőtérkép létrehozásához az érzékelők egységes telepítésére van szükség. Az összes érzékelőnek ugyanattól a szolgáltatótól kell származnia. A különböző szolgáltatók különbséget mutatnak a talaj nedvességtartalmának mérésében, valamint a kalibrálási különbségekben. A hőtérkép egy adott mélységre generálódik az adott mélységben telepített érzékelők segítségével.

### <a name="before-you-begin"></a>Előkészületek

A Talajnedvesség hőtérkép létrehozása előtt teljesítse az alábbi előfeltételeket:

- Legalább három talajnedvesség-érzékelőt kell telepíteni. Ne próbálja meg létrehozni a talaj nedvesség hőtérkép előtt érzékelők telepítése és kapcsolódó gazdaságban.
- A Soil Moisture hőtérkép generálását befolyásolja a Sentinel útvonallefedettsége, felhőtakarója és felhőárnyéka. Legalább egy felhőmentes Sentinel Jelenetnek rendelkezésre kell állnia az elmúlt 120 napban attól a naptól számítva, amikor a Soil Moisture hőtérképet kérték.
- A farmon telepített érzékelők legalább felének online állapotban kell lennie, és adatfolyam-továbbítást kell rendelkeznie az adatközpontba.
- A hőtérképet ugyanattól a szolgáltatótól származó érzékelőmérők segítségével kell létrehozni.


## <a name="create-a-soil-moisture-heatmap"></a>Hozzon létre egy talaj nedvesség hőtérkép

Kövesse az alábbi lépéseket.

1. A kezdőlapon nyissa meg a bal oldali navigációs menü **Térképek** lapját a **Térképek** lap megtekintéséhez.
2. Válassza **a Térképek létrehozása**lehetőséget, majd a legördülő menü **Talajnedvesség** parancsát.

    ![Válassza a Talajnedvesség lehetőséget a legördülő menüből](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Miután **kiválasztotta**a Talaj nedvességét , megjelenik a **Talajnedvesség** ablak.

    ![Talaj nedvesség ablak](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Válasszon ki egy farmot a **Farm** legördülő menüből.
   A farm kereséséhez és kiválasztásához görgethet a legördülő listából, vagy megadhatja a farm nevét a **Farm kiválasztása** legördülő menüben.
5. A **Talajnedvesség-érzékelő kiválasztása Legördülő** menüben válassza ki azt a talajnedvesség-érzékelőt, amelynek a térképét létre szeretné hozni.
Az érzékelő mértékének megkereséséhez nyissa meg **az Érzékelők**lehetőséget, és válassza ki a talajnedvesség-érzékelőt. Ezután az **Érzékelő tulajdonságai** csoportban használja a Mérték neve **című**szakasz értékét.
6. Ha térképet szeretne létrehozni a **Ma** vagy **a Hét**számára, válasszon a lehetőségek közül.
7. Ha egyéni dátumtartományhoz szeretne térképet létrehozni, válassza a **Dátumtartomány kiválasztása**lehetőséget. Adja meg azt a kezdő és záró dátumot, amelynek a Talajnedvesség hőtérképet létre kívánja hozni.
8. Válassza **a Térképek létrehozása**lehetőséget.
 Megjelenik egy megerősítő üzenet a feladat részleteivel.

   ![Talaj nedvességtérkép-megerősítő üzenet](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    A feladat állapotáról a **Feladatok megtekintése**című szakaszban talál további információt. Ha a feladat állapota *sikertelen,* akkor egy részletes hibaüzenet jelenik meg a *Sikertelen* állapot elemleírásában. Ebben az esetben ismételje meg az előző lépéseket, majd próbálkozzon újra.

    Ha a probléma továbbra is fennáll, tekintse meg a [Hibaelhárítás szakaszban,](troubleshoot-azure-farmbeats.md) vagy lépjen kapcsolatba az [Azure FarmBeats fórum a](https://aka.ms/FarmBeatsMSDN) megfelelő naplók támogatása.

### <a name="view-and-download-a-map"></a>Térkép megtekintése és letöltése

Kövesse az alábbi lépéseket.

1. A kezdőlapon nyissa meg a bal oldali navigációs menü **Térképek** lapján.

    ![Ugrás a Térképek re](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Válassza a **Szűrő** lehetőséget az ablak bal oldali navigációs sávján. A **Szűrő** ablak ban jelenik meg, ahonnan térképeket kereshet.

    ![Válassza a Szűrő lehetőséget a bal oldali navigációs sávról](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  A legördülő menükben válassza a **Típus**, **dátum**és **Név** értékek lehetőséget. Ezután válassza az **Alkalmaz** lehetőséget a megtekinteni kívánt térkép kereséséhez. A feladat létrehozásának dátuma type_farmname_YYYY-MM-DD formátumban jelenik meg.
4. A táblafejlécek melletti **Rendezés** ikonra a **Farm**, **Dátum**, **Létrehozás dátum,** **feladatazonosító**és feladattípus szerinti rendezéshez kattintson a Rendezés ikonra. **Job Type**
5. Görgessen végig a rendelkezésre álló térképek listáján az oldal végén található navigációs gombokkal.
6. Jelölje ki a megtekinteni kívánt térképet. Egy előugró ablak jeleníti meg a kijelölt térkép előnézetét.
7. A **Download** letöltési formátum kiválasztásához válassza a Letöltés lehetőséget a legördülő menüből. A térkép letöltése és tárolása a megadott mappában történik.

    ![Talaj nedvesség hőtérkép előnézet](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
