---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179066"
---
Miután létrehozta a rendelkezésre állási csoport figyelőjét, szükség lehet a RegisterAllProvidersIP és a HostRecordTTL-fürt paramétereinek módosítására a figyelő erőforráshoz. Ezek a paraméterek csökkenthetik a feladatátvételt követő újrakapcsolási időt, ami megelőzheti a kapcsolatok időtúllépését. További információ ezekről a paraméterekről, valamint a mintakód használatáról: [rendelkezésre állási csoport figyelő létrehozása vagy konfigurálása](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

