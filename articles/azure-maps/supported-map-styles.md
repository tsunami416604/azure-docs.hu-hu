---
title: Térkép stílusok támogatott az Azure Maps |} A Microsoft Docs
description: Az Azure Maps által támogatott térképstílusok
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2f426ab85d9ab15dd080a487337b8d48358ed040
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888460"
---
# <a name="azure-maps-supported-map-styles"></a>Az Azure Maps támogatott térkép stílusok
Az Azure maps számos különféle beépített térkép stílusok támogatja, az alább ismertetett.

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

## <a name="satelliteroadlabels"></a>satellite_road_labels
A térkép stílus egy hibrid utak és címkék, műholdas és a légi képanyag felett átfedésben.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Megfelelő API-kat:**
* A térkép vezérlőelem-JS

## <a name="grayscaledark"></a>grayscale_dark
**Szürkeárnyalatos sötét** közúti térképes styl sötét verziója.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Megfelelő API-kat:**
* A térkép vezérlőelem-JS 

## <a name="night"></a>az éjszakai
**az éjszakai** színes utak és szimbólumok közúti térképes styl sötét verziója.

![az éjszakai](./media/supported-map-styles/night.PNG)

**Megfelelő API-kat:**
* A térkép vezérlőelem-JS 