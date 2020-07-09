---
title: Adatterületek a Windows rendszerű virtuális asztali környezethez – Azure
description: Röviden áttekintheti, hogy a Windows rendszerű virtuális asztali adatok és metaadatok mely helyein vannak tárolva.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 853f5766c4fd6344eecfd7be1d7911163133a8a5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82611534"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Adatterületek a Windows rendszerű virtuális asztali környezethez

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/data-locations-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Windows virtuális asztal jelenleg minden földrajzi helyen elérhető. A rendszergazdák kiválaszthatják, hogy a virtuális gépek és a társított szolgáltatások, például a fájlkiszolgálók létrehozásakor hol tárolhatják a felhasználói adataikat. Tudjon meg többet az Azure földrajzi helyekről az [Azure Datacenter térképen](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft nem szabályozza és nem korlátozza azokat a régiókat, amelyeken Ön vagy felhasználói hozzáférhetnek a felhasználóhoz és az alkalmazásra vonatkozó adataihoz.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali szolgáltatás a globális metaadat-információkat, például a bérlői neveket, az alkalmazáskészletek nevét, az alkalmazás-csoportok nevét és az egyszerű felhasználóneveket tárolja egy adatközpontban Amikor egy ügyfél létrehoz egy szolgáltatási objektumot, meg kell adnia a szolgáltatás objektumának helyét. A megadott hely határozza meg, hogy az objektum metaadatai hol lesznek tárolva. Az ügyfél kiválaszt egy Azure-régiót, és a metaadatokat a kapcsolódó földrajz fogja tárolni. Az összes Azure-régió és kapcsolódó földrajzi terület listáját itt tekintheti meg: [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)területek.

Jelenleg csak a metaadatok tárolása támogatott a Egyesült Államok (US) Azure-földrajzban. A tárolt metaadatok a nyugalmi állapotban vannak titkosítva, és a Geo-redundáns tükrözések a földrajzon belül maradnak. Az összes vásárlói adat, például az Alkalmazásbeállítások és a felhasználói adat, az ügyfél által választott helyen található, és nem a szolgáltatás kezeli. A szolgáltatás növekedésével több földrajzi terület is elérhetővé válik.

A szolgáltatás metaadatainak replikálását az Azure-ban, a katasztrófa utáni helyreállítás érdekében replikálja a rendszer.