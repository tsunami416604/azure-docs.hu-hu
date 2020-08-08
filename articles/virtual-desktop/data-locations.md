---
title: Adatterületek a Windows rendszerű virtuális asztali környezethez – Azure
description: Röviden áttekintheti, hogy a Windows rendszerű virtuális asztali adatok és metaadatok mely helyein vannak tárolva.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e626b7e729e394b1012848904f5ce12279c3ef24
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010072"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>A Windows rendszerű virtuális asztali adatok és metaadatok helyei

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/data-locations-2019.md).

A Windows virtuális asztal jelenleg minden földrajzi helyen elérhető. A rendszergazdák kiválaszthatják, hogy a virtuális gépek és a társított szolgáltatások, például a fájlkiszolgálók létrehozásakor hol tárolhatják a felhasználói adataikat. Tudjon meg többet az Azure földrajzi helyekről az [Azure Datacenter térképen](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft nem szabályozza és nem korlátozza azokat a régiókat, amelyeken Ön vagy felhasználói hozzáférhetnek a felhasználóhoz és az alkalmazásra vonatkozó adataihoz.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali szolgáltatás a globális metaadat-információkat, például a bérlői neveket, az alkalmazáskészletek nevét, az alkalmazás-csoportok nevét és az egyszerű felhasználóneveket tárolja egy adatközpontban Amikor egy ügyfél létrehoz egy szolgáltatási objektumot, meg kell adnia a szolgáltatás objektumának helyét. A megadott hely határozza meg, hogy az objektum metaadatai hol lesznek tárolva. Az ügyfél kiválaszt egy Azure-régiót, és a metaadatokat a kapcsolódó földrajz fogja tárolni. Az összes Azure-régió és kapcsolódó földrajzi terület listáját itt tekintheti meg: [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)területek.

Jelenleg csak a metaadatok tárolása támogatott a Egyesült Államok (US) Azure-földrajzban. A tárolt metaadatok a nyugalmi állapotban vannak titkosítva, és a Geo-redundáns tükrözések a földrajzon belül maradnak. Az összes vásárlói adat, például az Alkalmazásbeállítások és a felhasználói adat, az ügyfél által választott helyen található, és nem a szolgáltatás kezeli. A szolgáltatás növekedésével több földrajzi terület is elérhetővé válik.

A szolgáltatás metaadatainak replikálását az Azure-ban, a katasztrófa utáni helyreállítás érdekében replikálja a rendszer.