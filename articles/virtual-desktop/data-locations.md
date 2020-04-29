---
title: Adatterületek a Windows rendszerű virtuális asztali környezethez – Azure
description: Röviden áttekintheti, hogy a Windows rendszerű virtuális asztali adatok és metaadatok mely helyein vannak tárolva.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128057"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Adatterületek a Windows rendszerű virtuális asztali környezethez

A Windows virtuális asztal jelenleg minden földrajzi helyen elérhető. Kezdetben a szolgáltatás metaadatainak csak a Egyesült Államok (US) földrajzban tárolhatók. A rendszergazdák kiválaszthatják, hogy a virtuális gépek és a társított szolgáltatások, például a fájlkiszolgálók létrehozásakor hol tárolhatják a felhasználói adataikat. Tudjon meg többet az Azure földrajzi helyekről az [Azure Datacenter térképen](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft nem szabályozza és nem korlátozza azokat a régiókat, amelyeken Ön vagy felhasználói hozzáférhetnek a felhasználóhoz és az alkalmazásra vonatkozó adataihoz.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali szolgáltatás a globális metaadat-információkat, például a bérlői neveket, a gazdagépek nevét, az alkalmazások csoportjának nevét, valamint az egyszerű felhasználóneveket a Egyesült Államok található adatközpontban tárolja. A tárolt metaadatok a nyugalmi állapotban vannak titkosítva, és a Geo-redundáns tükrök a Egyesült Államokon belül maradnak. Az összes vásárlói adat, például az Alkalmazásbeállítások és a felhasználói adat, az ügyfél által választott helyen található, és nem a szolgáltatás kezeli.

A szolgáltatás metaadatait a rendszer a vész-helyreállítási célú Egyesült Államok replikálja.