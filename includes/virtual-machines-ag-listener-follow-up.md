---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179066"
---
A rendelkezésre állási csoport figyelőjének létrehozása után szükség lehet a RegisterAllProvidersIP és a HostRecordTTL fürt paramétereinek módosítására a figyelő erőforráshoz. Ezek a paraméterek csökkenthetik az újrakapcsolódási időt a feladatátvétel után, ami megakadályozhatja a kapcsolat időtúlterhelését. Ezekről a paraméterekről, valamint a mintakódról további információt a [Rendelkezésre állási csoportfigyelő létrehozása vagy konfigurálása](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)című témakörben talál.

