---
title: Farmok kezelése
description: A farmok kezelésének leírása
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271719"
---
# <a name="manage-farms"></a>Farmok kezelése

A farmok az Azure FarmBeats kezelésében kezelhetők. Ez a cikk a farmok létrehozásáról, az eszközök, érzékelők és drónok telepítéséről nyújt tájékoztatást, amelyek segítenek a farmok kezelésében.

## <a name="create-farms"></a>Farmok létrehozása

Ehhez a következő lépések szükségesek:

1. Jelentkezzen be a Farm Accelerator, a **Gazdaságok** oldal jelenik meg.
    A **Farms** lap megjeleníti a farmok listáját abban az esetben, ha már létrehozták őket előfizetésben.

    Itt van a minta kép:

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. Válassza **a Farm létrehozása** és a **Név,** a **Növények** és **a Cím**lehetőséget.
3. A **Farmhatár meghatározása**(kötelező mező) mezőben válassza **a Megjelölés a térképen** vagy a **GeoJSON-kód beillesztése lehetőséget.**

A farmhatár két módját a következőképpen határozhatja meg:

1. **Megjelölés a térképen:** A térképvezérlő eszközzel rajzolhatja meg és jelölheti meg a farm határvonalát. A határok jelöléséhez ![a](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) Project Farm Beats és a pontos határok megjelölése.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **GeoJson-kód beillesztése**: A GeoJSON a földrajzi adatstruktúrák kódolásának formátuma, JavaScript objektumjelöléssel (JSON). Ez a beállítás egy olyan szövegmezőt jelenít meg, ahol a farm határainak jelöléséhez geoJSON karakterlánc ot lehet megadni. GeoJSON-kódot is létrehozhat GeoJSON.io.
Az elemleírások segítségével kitöltheti az adatokat.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  Farm létrehozásához válassza a **Küldés** lehetőséget. Egy új farm jön létre, és megjelenik a **Gazdaságok** oldalon.

## <a name="view-farm"></a>Farm megtekintése

A Farm lista lap a létrehozott farmok listáját jeleníti meg. Válasszon ki egy farmot a következők listájának megtekintéséhez:

 - **Eszközszám** – a farmon üzembe helyezett eszközök számát és állapotát jeleníti meg.
 - **Térkép** – a farm térképe a farmban telepített eszközökkel.
 - **Telemetria –** megjeleníti a telemetriai adatokat a farmban telepített érzékelők.
 - **Legújabb precíziós térképek** — megjeleníti a legújabb műholdas indexek térképét (EVI, NDWI), Soil Moisture Heatmap és Sensor Placement térképet.

## <a name="edit-farm"></a>Farm szerkesztése

A **Farmok** lap megjeleníti a létrehozott farmok listáját.

1.  Jelölje ki a farm megtekintéséhez és szerkesztéséhez.
2.  A farmadatainak szerkesztéséhez válassza a **Farm szerkesztése** lehetőséget. A **Farm részletei** ablakban szerkesztheti **a Nevet,** **a Növényeket**, **a Címet,** és megadhatja a **Farm határmezőket.**

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. A szerkesztett részletek mentéséhez válassza a **Küldés** lehetőséget.

## <a name="delete-farm"></a>Farm törlése

A **Farmok** lap megjeleníti a létrehozott farmok listáját. Farm törléséhez kövesse az alábbi lépéseket:

1.  Válasszon ki egy farmot a listából a farm részleteinek törléséhez.
2.  A farm törléséhez válassza a **Farm törlése** lehetőséget.

    ![Projekt Farm Beats](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > Farm törlésekor a farmhoz társított eszközök és térképek nem törlődnek. Az eszközhöz és a térképekhez kapcsolódó farmadatok nem lesznek relevánsak. Továbbra is megtekintheti az eszközöket, a telemetriai adatokat és a leképezéseket a FarmBeats szolgáltatásból.


## <a name="next-steps"></a>További lépések

Most, hogy létrehozta a gazdaság, megtanulják, hogyan [juthat érzékelő adatok](get-sensor-data-from-sensor-partner.md) áramlik be a gazdaságban.
