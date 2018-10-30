---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227475"
---
Miután létrehozta a rendelkezésre állási csoport figyelőjét, a figyelő erőforrás RegisterAllProvidersIP és HostRecordTTL fürt paraméterek beállítása szükség lehet. Ezek a paraméterek csökkentheti újrakapcsolódási idő kapcsolat időtúllépésének előfordulhat, hogy megakadályozza a feladatátvételt. Ezen paraméterek, valamint a mintakód kapcsolatos további információkért lásd: [létrehozása vagy egy rendelkezésre állási csoport kérésfigyelőjének konfigurálása](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

