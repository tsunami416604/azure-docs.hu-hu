---
title: A Windows rendszerű virtuális asztali adatterületek 2019 – Azure
description: Röviden áttekintheti, hogy a Windows rendszerű virtuális asztali adatok és metaadatok mely helyein vannak tárolva.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fa998ddf8225475bbcf657ed9feee951263d2743
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077580"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Adatterületek a Windows rendszerű virtuális asztali környezethez

>[!IMPORTANT]
>Ez a tartalom a Fall 2019 kiadásra vonatkozik, amely nem támogatja a Windows rendszerű virtuális asztali objektumokat Azure Resource Manager. Ha a Spring 2020 Update szolgáltatásban bevezetett Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../data-locations.md).

A Windows virtuális asztal jelenleg minden földrajzi helyen elérhető. Kezdetben a szolgáltatás metaadatainak csak a Egyesült Államok (US) földrajzban tárolhatók. A rendszergazdák kiválaszthatják, hogy a virtuális gépek és a társított szolgáltatások, például a fájlkiszolgálók létrehozásakor hol tárolhatják a felhasználói adataikat. Tudjon meg többet az Azure földrajzi helyekről az [Azure Datacenter térképen](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft nem szabályozza és nem korlátozza azokat a régiókat, amelyeken Ön vagy felhasználói hozzáférhetnek a felhasználóhoz és az alkalmazásra vonatkozó adataihoz.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali szolgáltatás a globális metaadat-információkat, például a bérlői neveket, a gazdagépek nevét, az alkalmazások csoportjának nevét, valamint az egyszerű felhasználóneveket a Egyesült Államok található adatközpontban tárolja. A tárolt metaadatok a nyugalmi állapotban vannak titkosítva, és a Geo-redundáns tükrök a Egyesült Államokon belül maradnak. Az összes vásárlói adat, például az Alkalmazásbeállítások és a felhasználói adat, az ügyfél által választott helyen található, és nem a szolgáltatás kezeli.

A szolgáltatás metaadatait a rendszer a vész-helyreállítási célú Egyesült Államok replikálja.