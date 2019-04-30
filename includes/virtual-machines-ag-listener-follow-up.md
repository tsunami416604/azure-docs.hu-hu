---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097668"
---
Miután létrehozta a rendelkezésre állási csoport figyelőjét, a figyelő erőforrás RegisterAllProvidersIP és HostRecordTTL fürt paraméterek beállítása szükség lehet. Ezek a paraméterek csökkentheti újrakapcsolódási idő kapcsolat időtúllépésének előfordulhat, hogy megakadályozza a feladatátvételt. Ezen paraméterek, valamint a mintakód kapcsolatos további információkért lásd: [létrehozása vagy egy rendelkezésre állási csoport kérésfigyelőjének konfigurálása](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

<!-- Update_Description: update meta properties -->