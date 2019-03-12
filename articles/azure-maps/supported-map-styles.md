---
title: Térkép stílusok támogatott az Azure Maps |} A Microsoft Docs
description: Az Azure Maps által támogatott térképstílusok
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 76ab49c7f28260249483bf3bc4387e8cbaca13b2
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570538"
---
# <a name="azure-maps-supported-map-styles"></a>Az Azure Maps támogatott térkép stílusok
Az Azure Maps számos különféle beépített térkép stílusok támogatja, az alább ismertetett.

## <a name="road"></a>Közúti
A **közúti** térkép a szokásos természetes utak megjelenítő térképet és mesterséges szolgáltatások, a címkék esetében ezek a funkciók mellett.

![Közúti](./media/supported-map-styles/road.png)

**Megfelelő API-kat:**
* [Térkép képének](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Szolgáltatástérkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* A térkép vezérlőelem-JS
* Android térkép vezérlőelem

## <a name="satellite"></a>Szatellit 
A **műholdas** stílus műholdas és légi képanyag.

![Szatellit](./media/supported-map-styles/satellite.png)

**Megfelelő API-kat:**
* [Műholdas csempe](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* A térkép vezérlőelem-JS
* Android térkép vezérlőelem

## <a name="satelliteroadlabels"></a>satellite_road_labels
A térkép stílus egy hibrid utak és címkék, műholdas és a légi képanyag felett átfedésben.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Megfelelő API-kat:**
* A térkép vezérlőelem-JS
* Android térkép vezérlőelem

## <a name="grayscaledark"></a>grayscale_dark
**Szürkeárnyalatos sötét** közúti térképes styl sötét verziója.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Megfelelő API-kat:**
* A térkép vezérlőelem-JS 
* Android térkép vezérlőelem

## <a name="night"></a>az éjszakai
**az éjszakai** színes utak és szimbólumok közúti térképes styl sötét verziója.

![az éjszakai](./media/supported-map-styles/night.png)

**Megfelelő API-kat:**
* A térkép vezérlőelem-JS
* Android térkép vezérlőelem

## <a name="roadshadedrelief"></a>road_shaded_relief
**közúti színezve mentesség** egy az Azure Maps fő stílus a föld vonala befejeződött.

![Árnyékolt mentesség](./media/supported-map-styles/shaded-relief.png)

**Megfelelő API-kat:**
* [Szolgáltatástérkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* A térkép vezérlőelem-JS
* Android térkép vezérlőelem
