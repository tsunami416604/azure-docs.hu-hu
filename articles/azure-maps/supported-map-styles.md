---
title: Támogatott térképstílusok | Microsoft Azure Maps
description: Ebben a cikkben a Microsoft Azure Maps által támogatott különböző térképmegjelenítési stílusokról olvashat.
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334043"
---
# <a name="azure-maps-supported-map-styles"></a>Az Azure Maps által támogatott térképstílusok
Az Azure Maps számos különböző beépített térképstílust támogat az alábbiakban leírtak szerint.

## <a name="road"></a>Road
Az **útiterv** egy szabványos térkép, amely megjeleníti az utakat, a természetes és a mesterséges jellemzőket, valamint a funkciók címkéit.

![útitérkép stílusa](./media/supported-map-styles/road.png)

**Alkalmazható API-k:**
* [Térkép kép](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Térkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK-térképvezérlő
* Android térkép vezérlés

## <a name="blank-and-blank_accessible"></a>üres és blank_accessible

Az **üres** és **blank_accessible** térképstílusok üres vásznat biztosítanak, amelyen az adatokat megjelenítheti. A **blank_accessible** stílus továbbra is biztosítja a képernyőolvasó frissítéseit a térkép helyadataival, még akkor is, ha az alaptérkép nem jelenik meg.

> [!Note]
> A weben SDK meg lehet változtatni a háttér színét a térkép beállításával a CSS `background-color` stílus térkép DIV elem.

**Alkalmazható API-k:**
* Web SDK-térképvezérlő

## <a name="satellite"></a>satellite 
A **műholdas** stílus kombinációja műholdas és légi felvételek.

![műholdas csempetérkép stílusa](./media/supported-map-styles/satellite.png)

**Alkalmazható API-k:**
* [Műholdas csempe](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK-térképvezérlő
* Android térkép vezérlés

## <a name="satellite_road_labels"></a>satellite_road_labels
Ez a térkép stílus egy hibrid utak és címkék eltakarja a tetején műholdas és légi képek.

![satellite_road_labels térképstílus](./media/supported-map-styles/satellite-road-labels.png)

**Alkalmazható API-k:**
* Web SDK-térképvezérlő
* Android térkép vezérlés

## <a name="grayscale_dark"></a>grayscale_dark
**szürkeárnyalatos sötét** egy sötét változata az útiterv stílus.

![gray_scale térképstílus](./media/supported-map-styles/grayscale-dark.png)

**Alkalmazható API-k:**
* [Térkép kép](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Térkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK-térképvezérlő 
* Android térkép vezérlés


## <a name="grayscale_light"></a>grayscale_light
**a szürkeárnyalatos fény** az útitérkép-stílus könnyű változata.

![szürkeárnyalatos világos térképstílus](./media/supported-map-styles/grayscale-light.png)

**Alkalmazható API-k:**
* Web SDK-térképvezérlő
* Android térkép vezérlés


## <a name="night"></a>Éjszaka
**éjszaka** egy sötét változata az útitérkép stílus színes utak és szimbólumok.

![éjszakai térkép stílus](./media/supported-map-styles/night.png)

**Alkalmazható API-k:**
* Web SDK-térképvezérlő
* Android térkép vezérlés

## <a name="road_shaded_relief"></a>road_shaded_relief
**az útárnyékolt dombormű** az Azure Maps főstílusa, amely a Föld körvonalaival van kiegészítve.

![árnyékolt dombormű térkép stílus](./media/supported-map-styles/shaded-relief.png)

**Alkalmazható API-k:**
* [Térkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK-térképvezérlő
* Android térkép vezérlés

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** egy sötét térképstílus, amely nek nagyobb a kontrasztja, mint a többi stílusnak.

![kontrasztos, sötét térképstílus](./media/supported-map-styles/high-contrast-dark.png)

**Alkalmazható API-k:**
* Web SDK-térképvezérlő

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan állíthat be térképstílust az Azure Mapsben:

> [!div class="nextstepaction"]
> [Térképstílus kiválasztása](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
