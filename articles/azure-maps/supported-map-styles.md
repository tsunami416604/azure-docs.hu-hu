---
title: Támogatott térképi stílusok a Azure Mapsban | Microsoft Docs
description: Azure Maps által támogatott Stílusok leképezése
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cd16ee87b213c2bfa4c541c4d3c453a4499855d0
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887734"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps támogatott Térkép stílusa
A Azure Maps az alább leírtak szerint számos különböző beépített térképi stílust támogat.

## <a name="road"></a>közúti
A **közúti** Térkép egy szabványos Térkép, amely az utakat, természetes és mesterséges funkciókat, valamint a funkciók címkéit jeleníti meg.

![közúti](./media/supported-map-styles/road.png)

**Alkalmazható API-k:**
* [Térkép képe](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Térkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem

## <a name="blank-and-blank_accessible"></a>üres és blank_accessible

Az **üres** és a **blank_accessible** Térkép stílusa üres vásznat biztosít az adatmegjelenítéshez. A **blank_accessible** stílusa továbbra is biztosítja a képernyőolvasó frissítéseinek helyét, ahol a Térkép helye található, még akkor is, ha az alap térkép nem jelenik meg.

> [!Note]
> A web SDK-ban megváltoztathatja a Térkép háttérszínét úgy, hogy beállítja a Térkép DIV elemének CSS-`background-color` stílusát.

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem

## <a name="satellite"></a>műholdas 
A **műhold** stílusa a szatellit és a légi felvételek kombinációja.

![műholdas](./media/supported-map-styles/satellite.png)

**Alkalmazható API-k:**
* [Műholdas csempe](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem

## <a name="satellite_road_labels"></a>satellite_road_labels
Ez a Térkép stílusa a műholdon és a légi képeken behelyezett utak és feliratok hibrid útvonala.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem

## <a name="grayscale_dark"></a>grayscale_dark
a **szürkeárnyalatos sötét** a közúti Térkép stílusának sötét változata.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem 
* Android Térkép vezérlőelem


## <a name="grayscale_light"></a>grayscale_light
a **szürkeárnyalatos fény** a közúti Térkép stílusának egyszerűsített változata.

![szürkeárnyalatos fény](./media/supported-map-styles/grayscale_light.png)

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem


## <a name="night"></a>éjszaka
a **Night** a közúti Térkép stílusa sötét változata, amely színes utakat és szimbólumokat is mutat.

![éjszaka](./media/supported-map-styles/night.png)

**Alkalmazható API-k:**
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem

## <a name="road_shaded_relief"></a>road_shaded_relief
a **közúti árnyékolt Relief** egy Azure Maps fő stílus, amelyet a föld körvonalával töltöttek be.

![árnyékolt megkönnyebbülés](./media/supported-map-styles/shaded-relief.png)

**Alkalmazható API-k:**
* [Térkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK Térkép vezérlőelem
* Android Térkép vezérlőelem


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan állíthat be leképezési stílust a Azure Mapsban:

> [!div class="nextstepaction"]
> [Válasszon egy leképezési stílust](https://docs.microsoft.com/azure/azure-maps/choose-map-style)