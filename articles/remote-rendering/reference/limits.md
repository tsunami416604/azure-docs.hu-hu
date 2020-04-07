---
title: Korlátok
description: Az SDK-szolgáltatások kódkorlátai
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680335"
---
# <a name="limits"></a>Korlátok

Számos funkció nak van méret- vagy számkorlátai a futó rendszer belső részletei miatt.

## <a name="azure-frontend"></a>Azure előtér

* AzureFrontend-példányok összesen folyamatonként: 16.
* AzureSession-példányok összesen AzureFrontend: 16.

## <a name="objects"></a>Objektumok

* Egyetlen típusú engedélyezett objektumok összesen (Entity, CutPlaneComponent stb.): 16 777 215.
* Teljes megengedett aktív vágási síkok: 8.

## <a name="materials"></a>Nyersanyagok

* Teljes engedélyezett anyagok egy eszköz: 65.535.

## <a name="overall-number-of-polygons"></a>A sokszögek teljes száma

Az összes betöltött modellhez engedélyezett sokszögek száma a [munkamenet-kezelés REST API-nak](../how-tos/session-rest-api.md#create-a-session)átadott virtuális gép méretétől függ:

| Virtuális gép mérete | Sokszögek maximális száma |
|:--------|:------------------|
|Standard| 20 millió |
|Prémium| nincs korlátozás |



