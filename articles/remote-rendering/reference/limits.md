---
title: Korlátozások
description: Az SDK-funkciók kódjainak korlátai
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 5c4c6be7c8a6287c5a8ec6a60121751ff9fc0dc9
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509227"
---
# <a name="limitations"></a>Korlátozások

Számos szolgáltatás mérete, száma vagy egyéb korlátai vannak.

## <a name="azure-frontend"></a>Azure-előtérbeli felület

* AzureFrontend-példányok összesen: 16.
* AzureSession-példányok összesen AzureFrontend: 16.

## <a name="objects"></a>Objektumok

* Egyetlen típus összes engedélyezett objektuma (entitás, CutPlaneComponent stb.): 16 777 215.
* Engedélyezett aktív kivágott síkok összesen: 8.

## <a name="geometry"></a>Geometria

* Az eszközön engedélyezett összes anyag: 65 535.
* Egyetlen textúra maximális mérete: 16 384 x 16 384. A nagyobb forrású textúrákat az átalakítási folyamat fogja lemérni.

## <a name="overall-number-of-polygons"></a>Sokszögek összesített száma

Az összes betöltött modell számára engedélyezett számú sokszög a virtuális gép méretétől függ a [munkamenet-felügyeleti REST API](../how-tos/session-rest-api.md#create-a-session):

| Kiszolgáló mérete | Sokszögek maximális száma |
|:--------|:------------------|
|Standard| 20 000 000 |
|prémium szintű| korlátlan |

A korlátozással kapcsolatos részletesebb információkért tekintse meg a [kiszolgáló mérete](../reference/vm-sizes.md) című fejezetet.

## <a name="platform-limitations"></a>A platformra vonatkozó korlátozások

**Windows 10 asztali verzió**

* A UWP/x86 az egyetlen támogatott UWP-platform. A UWP/x64 nem támogatott.
* A Win32/x64 az egyetlen támogatott Win32 platform. A Win32/x86 nem támogatott.

**2. Hololens**

* A [renderelés a PV-kamerából](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) funkció nem támogatott.
