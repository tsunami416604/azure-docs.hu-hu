---
title: Termeljen talaj nedvesség heatmap
description: A talajnedvesség-hőtérkép létrehozásának ismertetése az Azure FarmBeats szolgáltatásban
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482568"
---
# <a name="generate-soil-moisture-heatmap"></a>Termeljen talaj nedvesség heatmap

A talaj nedvessége az a víz, amelyet a talajrészecskék közötti terekben tartanak.A Soil Moisture Heatmap segít megérteni a nedvességadatokat bármilyen mélységben és nagy felbontásban a farmokon belül. A pontos és használható talajnedvesség-hőtérkép létrehozásához ugyanattól a szolgáltatótól származó érzékelők egységes telepítésére van szükség. A különböző szolgáltatók nak különbségei lesznek a talaj nedvességtartalmának mérésében, valamint a kalibrálási különbségekben. A heatmap jön létre egy adott mélységben az érzékelők telepített ebben a mélységben.

Ez a cikk ismerteti a folyamat a talaj nedvességhőtérkép a farm, az Azure FarmBeats gyorsító használatával. Ebben a cikkben megtudhatja, hogyan:

- [Farmok létrehozása](#create-a-farm)
- [Érzékelők hozzárendelése farmokhoz](#get-soil-moisture-sensor-data-from-partner)
- [Termeljen talaj nedvesség heatmap](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze a következőket:  

- Azure-előfizetés.
- Az Azure FarmBeats futó példánya.
- A gazdaságban legalább három talajnedvesség-érzékelő áll rendelkezésre.

## <a name="create-a-farm"></a>Farm létrehozása

A gazdaság olyan földrajzi terület, amelyhez talajnedvesség-hőtérképet szeretne létrehozni. Farmot a Farms [API vagy](https://aka.ms/FarmBeatsDatahubSwagger) a [FarmsBeats Accelerator felhasználói felületén](manage-farms-in-azure-farmbeats.md#create-farms) hozhat létre

## <a name="deploy-sensors"></a>Érzékelők telepítése

Fizikailag kell telepíteni a talaj nedvességérzékelőit a gazdaságban. Bármelyik jóváhagyott partnerünktől , a Davis [Instruments-től](https://www.davisinstruments.com/product/enviromonitor-gateway/) és a [Teralytic-tól](https://teralytic.com/)vásárolhat talajnedvesség-érzékelőket. A farm fizikai beállításához egyeztetnie kell az érzékelő szolgáltatójával.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>A talajnedvesség-érzékelő adatainak beszereznie a partnertől

Ahogy az érzékelők megkezdik a streamelést, az adatok a partneradatok irányítópultjára, engedélyezik az adatokat az Azure FarmBeats-be. Ezt a partneralkalmazásból teheti meg.

Ha például Davis-érzékelőket vásárolt, akkor jelentkezzen be az időjárási kapcsolat fiókjába, és adja meg a szükséges hitelesítő adatokat az adatok azure FarmBeats-be való streameléséhez. A szükséges hitelesítő adatok beszerezéséhez kövesse az [Érzékelő adatok betárolása](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)című útmutatóutasításait.

Miután megadta a hitelesítő adatait, és válassza a **Küldés** a partneralkalmazásban lehetőséget, az adatok az Azure FarmBeats-be áramlathatnak.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Rendeljen talajnedvesség-érzékelőket a gazdasághoz

Miután összekapcsolta érzékelőfiókját az Azure FarmBeats-hez, hozzá kell rendelnie a talajnedvesség-érzékelőket az érdeklődésre számot tartó farmhoz.

1.  A kezdőlapon válassza a **Farmok** elemet a menüből, és megjelenik a **Farms** lista.
2.  Válassza a **MyFarm** > **Eszközök hozzáadása**lehetőséget.
3.  Megjelenik **az Eszközök hozzáadása** ablak. Válassza ki azokat az eszközöket, amelyek kapcsolódnak a talaj nedvességérzékelőihez a gazdaságban.

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Válassza **az Eszközök hozzáadása**lehetőséget.     

## <a name="generate-soil-moisture-heatmap"></a>Termeljen talaj nedvesség heatmap

Ez a lépés az, hogy hozzon létre egy feladatot, vagy egy hosszú ideig futó művelet, amely létrehoz talaj nedvesség Heatmap a gazdaságban.

1.  A kezdőlapon a bal oldali navigációs menü **Farmjai** lapra lépve megtekintheti a farmok oldalát.
2.  Válassza a **MyFarm**lehetőséget.
3.  A **Farm részletei** lapon válassza **a Pontossági térkép létrehozása lehetőséget.**
4.  A legördülő menüben válassza a **Talajnedvesség lehetőséget.**
5.  A **Talajnedvesség** ablakban válassza **az Ezen a héten**lehetőséget.
6.  A **Talajnedvesség-érzékelő kiválasztása** **mértékterületen**adja meg a térképhez használni kívánt mértéket.
    Az érzékelő mértékének megkereséséhez az **Érzékelők**területen válassza ki a talaj nedvességérzékelőjét. Az **Érzékelő tulajdonságai párbeszédpanelen**használja a **Mértéknév** értéket.

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Válassza **a Térképek létrehozása**lehetőséget.
    Megjelenik egy megerősítő üzenet a feladat részleteivel. További információ: Job Status in Jobs.

    >[!NOTE]
    > A munka körülbelül 3-4 órát vesz igénybe.

### <a name="download-the-soil-moisture-heatmap"></a>A talajnedvesség hőtérképének letöltése

Ehhez a következő lépések szükségesek:

1. A **Feladatok** lapon ellenőrizze az utolsó eljárásban létrehozott feladat **feladatállapotát.**
2. Ha a feladat állapota **sikeres,** válassza a **menü Térképek** parancsát.
3. Keresse meg a térképet a létrehozása napján, <talaj-moisture_MyFarm_YYYY-MM-DD> formátumban.
4. Jelöljön ki egy térképet a **Név** oszlopban, és egy előugró ablak jelenik meg a kijelölt térkép előnézetével.
5. Válassza a **Download** (Letöltés) lehetőséget. A rendszer letölti és tárolja a számítógép helyi mappájában.

    ![Projekt Farm Beats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozta a talajnedvesség-hőtérképet, ismerje meg, hogyan [hozhat létre érzékelőelhelyezést](generate-maps-in-azure-farmbeats.md#sensor-placement-map) és [korábbi telemetriai adatokat.](ingest-historical-telemetry-data-in-azure-farmbeats.md) 
