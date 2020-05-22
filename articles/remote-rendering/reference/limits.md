---
title: Korlátozások
description: Az SDK-funkciók kódjainak korlátai
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6a1a51ee09422607ae1392704add4d49d3367d57
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759047"
---
# <a name="limitations"></a>Korlátozások

Számos szolgáltatás mérete, száma vagy egyéb korlátai vannak.

## <a name="azure-frontend"></a>Azure-előtérbeli felület

* AzureFrontend-példányok összesen: 16.
* AzureSession-példányok összesen AzureFrontend: 16.

## <a name="objects"></a>Objektumok

* Egyetlen típus összes engedélyezett objektuma (entitás, CutPlaneComponent stb.): 16 777 215.
* Engedélyezett aktív kivágott síkok összesen: 8.

## <a name="materials"></a>Anyagok

* Az eszközön engedélyezett összes anyag: 65 535.

## <a name="overall-number-of-polygons"></a>Sokszögek összesített száma

Az összes betöltött modell számára engedélyezett számú sokszög a virtuális gép méretétől függ a [munkamenet-felügyeleti REST API](../how-tos/session-rest-api.md#create-a-session):

| Virtuális gép mérete | Sokszögek maximális száma |
|:--------|:------------------|
|Standard| 20 000 000 |
|prémium szintű| korlátlan |


## <a name="platform-limitations"></a>A platformra vonatkozó korlátozások

**Windows 10 asztali verzió**

* A UWP/x86 az egyetlen támogatott UWP-platform. A UWP/x64 nem támogatott.

**2. Hololens**

* A [renderelés a PV-kamerából](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) funkció nem támogatott.
