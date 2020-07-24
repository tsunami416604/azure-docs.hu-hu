---
title: Támogatott térképi stílusok | Microsoft Azure térképek
description: Ebben a cikkben megismerheti a Microsoft Azure Maps által támogatott különböző leképezési stílusokat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c22363b3b1e0b8283de572ff578e6d78f0627486
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128063"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps támogatott Térkép stílusa
A Azure Maps az alább leírtak szerint számos különböző beépített térképi stílust támogat.

## <a name="road"></a>közúti
A **közúti** Térkép egy szabványos Térkép, amely az utakat, természetes és mesterséges funkciókat, valamint a funkciók címkéit jeleníti meg.

![közúti Térkép stílusa](./media/supported-map-styles/road.png)

**Alkalmazható API-k:**
* [Térkép képe](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Térkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem
* Power BI-vizualizáció

## <a name="blank-and-blank_accessible"></a>üres és blank_accessible

Az **üres** és a **blank_accessible** Térkép stílusa üres vásznat biztosít az adatmegjelenítéshez. A **blank_accessible** stílusa továbbra is biztosítja a képernyőolvasó frissítéseit a Térkép helyének részleteivel, noha az alaptérkép nem jelenik meg.

> [!Note]
> A web SDK-ban módosíthatja a Térkép háttérszínét úgy, hogy beállítja a `background-color` Térkép div elemének CSS-stílusát.

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem

## <a name="satellite"></a>satellite 
A **műhold** stílusa a szatellit és a légi felvételek kombinációja.

![műholdas csempe Térkép stílusa](./media/supported-map-styles/satellite.png)

**Alkalmazható API-k:**
* [Műholdas csempe](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem
* Power BI-vizualizáció

## <a name="satellite_road_labels"></a>satellite_road_labels
Ez a Térkép stílusa a műholdon és a légi képeken behelyezett utak és feliratok hibrid útvonala.

![satellite_road_labels Térkép stílusa](./media/supported-map-styles/satellite-road-labels.png)

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem
* Power BI-vizualizáció

## <a name="grayscale_dark"></a>grayscale_dark
a **szürkeárnyalatos sötét** a közúti Térkép stílusának sötét változata.

![gray_scale Térkép stílusa](./media/supported-map-styles/grayscale-dark.png)

**Alkalmazható API-k:**
* [Térkép képe](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Térkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK Térkép vezérlőelem 
* Android Térkép vezérlőelem
* Power BI-vizualizáció


## <a name="grayscale_light"></a>grayscale_light
a **szürkeárnyalatos fény** a közúti Térkép stílusának egyszerűsített változata.

![szürkeárnyalatos világos Térkép stílusa](./media/supported-map-styles/grayscale-light.png)

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem
* Power BI-vizualizáció


## <a name="night"></a>éjszaka
a **Night** a közúti Térkép stílusa sötét változata, amely színes utakat és szimbólumokat is mutat.

![éjszakai Térkép stílusa](./media/supported-map-styles/night.png)

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem
* Power BI-vizualizáció

## <a name="road_shaded_relief"></a>road_shaded_relief
a **közúti árnyékolt Relief** egy Azure Maps fő stílus, amelyet a föld körvonalával töltöttek be.

![árnyékolt domborzati Térkép stílusa](./media/supported-map-styles/shaded-relief.png)

**Alkalmazható API-k:**
* [Térkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem
* Power BI-vizualizáció

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** egy sötét Térkép stílusa, amelynek nagyobb a kontrasztja, mint a többi stílus.

![kontrasztos sötét Térkép stílusa](./media/supported-map-styles/high-contrast-dark.png)

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem
* Power BI-vizualizáció

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan állíthat be leképezési stílust a Azure Mapsban:

> [!div class="nextstepaction"]
> [Térképstílus kiválasztása](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
