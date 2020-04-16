---
title: Korlátozások
description: Az SDK-szolgáltatások kódkorlátai
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417691"
---
# <a name="limitations"></a>Korlátozások

Számos szolgáltatás mérete, száma vagy egyéb korlátai vannak.

## <a name="azure-frontend"></a>Azure előtér

* AzureFrontend-példányok összesen folyamatonként: 16.
* AzureSession-példányok összesen AzureFrontend: 16.

## <a name="objects"></a>Objektumok

* Egyetlen típusú engedélyezett objektumok összesen (Entity, CutPlaneComponent stb.): 16 777 215.
* Teljes megengedett aktív vágási síkok: 8.

## <a name="materials"></a>Anyagok

* Teljes engedélyezett anyagok egy eszköz: 65.535.

## <a name="overall-number-of-polygons"></a>A sokszögek teljes száma

Az összes betöltött modellhez engedélyezett sokszögek száma a [munkamenet-kezelés REST API-nak](../how-tos/session-rest-api.md#create-a-session)átadott virtuális gép méretétől függ:

| Virtuális gép mérete | Sokszögek maximális száma |
|:--------|:------------------|
|Standard| 20 millió |
|Prémium| nincs korlátozás |


## <a name="platform-limitations"></a>A platform korlátai

**Windows 10 asztal**

* A Unity "PC Standalone" telepítése nem támogatott. Használja inkább az UWP-t.
* Az UWP/x86 az egyetlen támogatott UWP platform. Az UWP/x64 nem támogatott.

**Hololens 2**

* A PV kamera funkció [renderelése](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) nem támogatott.
