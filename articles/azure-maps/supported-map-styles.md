---
title: Térkép stílusok támogatott az Azure Maps |} A Microsoft Docs
description: Az Azure Maps által támogatott térképstílusok
author: walsehgal
ms.author: v-musehg
ms.date: 08/28/2018
ms.topic: concepts
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 04c8f43e3b484ceeb942ae13ea95baf7f0215b53
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344835"
---
# <a name="azure-maps-supported-map-styles"></a>Az Azure Maps támogatott térkép stílusok
Az Azure maps négy különböző beépített térkép stílusok támogatja. A stílusok, valamint a hozzájuk tartozó leírások alább láthatók.

## <a name="road"></a>Közúti
A **közúti** térkép a szokásos természetes utak megjelenítő térképet és mesterséges szolgáltatások, a címkék esetében ezek a funkciók mellett.

![Közúti](./media/supported-map-styles/road.png)

**Megfelelő API-kat:**
* [Térkép képének](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Szolgáltatástérkép csempe](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* A térkép vezérlőelem-JS

## <a name="satellite"></a>Szatellit 
A **műholdas** stílus műholdas és légi képanyag.

![Szatellit](./media/supported-map-styles/satellite.png)

**Megfelelő API-kat:**
* [Műholdas csempe](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* A térkép vezérlőelem-JS

## <a name="satelliteroadlabels"></a>Satellite_Road_Labels
A térkép stílus egy hibrid utak és címkék, műholdas és a légi képanyag felett átfedésben.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Megfelelő API-kat:**
* A térkép vezérlőelem-JS

## <a name="grayscaledark"></a>Grayscale_Dark
**Szürkeárnyalatos sötét** közúti térképes styl sötét verziója.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Megfelelő API-kat:**
* A térkép vezérlőelem-JS 