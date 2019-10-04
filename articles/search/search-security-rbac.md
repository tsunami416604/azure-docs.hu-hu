---
title: Az Azure rendszergazdai hozzáférés RBAC szerepköreinek beállítása a portálon – Azure Search
description: Szerepköralapú felügyeleti vezérlés (RBAC) a Azure Portal a Azure Search felügyeletéhez szükséges felügyeleti feladatok vezérlésére és delegálására.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 924d2529e3477c299d4a90c076fe9e6c8faf11f3
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647404"
---
# <a name="set-rbac-roles-for-administrative-access"></a>RBAC-szerepkörök beállítása rendszergazdai hozzáféréshez

Az Azure [globális szerepköralapú engedélyezési modellt](../role-based-access-control/role-assignments-portal.md) biztosít a portálon vagy a Resource Manager API-kon keresztül felügyelt összes szolgáltatáshoz. A tulajdonos, közreműködő és olvasó szerepkörök határozzák meg az egyes szerepkörökhöz rendelt Active Directory felhasználók, csoportok és rendszerbiztonsági tag *szolgáltatások felügyeletének* szintjét. 

> [!Note]
> Nincs szerepköralapú hozzáférés-vezérlés az index vagy a dokumentumok egy részhalmazának biztonságossá tételéhez. A keresési eredményeken alapuló, identitás-alapú hozzáféréshez biztonsági szűrőket hozhat létre, amelyek identitás alapján metszik az eredményeket, és eltávolíthatja azokat a dokumentumokat, amelyekhez a kérelmezőnek nincs hozzáférése. További információ: [biztonsági szűrők](search-security-trimming-for-azure-search.md) és [biztonságos a Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Felügyeleti feladatok szerepkör szerint

Azure Search esetén a szerepkörök a következő felügyeleti feladatokat támogató jogosultsági szintekhez vannak társítva:

| Role | Tevékenység |
| --- | --- |
| Tulajdonos |Hozza létre vagy törölje a szolgáltatást vagy a szolgáltatás bármely objektumát, beleértve az API-kulcsokat, az indexeket, az indexelő, az indexelő adatforrásokat és az indexelő-ütemterveket.<p>A szolgáltatás állapotának megtekintése, beleértve a darabszámot és a tárterület méretét.<p>Szerepkör-tagság hozzáadása vagy törlése (csak a tulajdonosok kezelhetik a szerepkör-tagságot).<p>Az előfizetés-rendszergazdák és a szolgáltatás tulajdonosai automatikus tagsággal rendelkeznek a tulajdonosi szerepkörben. |
| Közreműködő |Azonos szintű hozzáférés tulajdonosként, mínusz a RBAC szerepkör-kezelés. A közreműködők például létrehozhatnak vagy törölhetnek objektumokat, vagy megtekinthetik és újragenerálják az [API-kulcsokat](search-security-api-keys.md), de nem módosíthatják a szerepkör-tagságokat. |
| [Search Service közreműködő beépített szerepköre](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | A közreműködő szerepkörrel egyenértékű. |
| Olvasó |A szolgáltatás Essentials és metrikáinak megtekintése. Ennek a szerepkörnek a tagjai nem tekinthetik meg az indexet, az indexelő, az adatforrás vagy a kulcs információit.  |

A szerepkörök nem biztosítanak hozzáférési jogosultságot a szolgáltatási végpont számára. A keresési szolgáltatási műveleteket, például az indexelést, az indexelést és a keresési adatok lekérdezéseit az API-kulcsok vezérlik, nem pedig a szerepkörök. További információ: az [API-kulcsok kezelése](search-security-api-keys.md).

## <a name="see-also"></a>Lásd még

+ [Kezelés a PowerShell használatával](search-manage-powershell.md) 
+ [Teljesítmény és optimalizálás Azure Search](search-performance-optimization.md)
+ [Ismerkedjen meg a Azure Portal szerepköralapú Access Controlával](../role-based-access-control/overview.md).