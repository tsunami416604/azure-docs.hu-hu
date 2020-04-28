---
title: RBAC-szerepkörök beállítása az Azure rendszergazdai hozzáféréshez
titleSuffix: Azure Cognitive Search
description: Szerepköralapú felügyeleti vezérlés (RBAC) az Azure Cognitive Search Management felügyeleti feladatainak vezérlésére és delegálására szolgáló Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112555"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>RBAC-szerepkörök beállítása az Azure Cognitive Searchhoz való rendszergazdai hozzáféréshez

Az Azure [globális szerepköralapú engedélyezési modellt](../role-based-access-control/role-assignments-portal.md) biztosít a portálon vagy a Resource Manager API-kon keresztül felügyelt összes szolgáltatáshoz. A tulajdonos, közreműködő és olvasó szerepkörök határozzák meg az egyes szerepkörökhöz rendelt Active Directory felhasználók, csoportok és rendszerbiztonsági tag *szolgáltatások felügyeletének* szintjét. 

> [!Note]
> Nincs szerepköralapú hozzáférés-vezérlés az index vagy a dokumentumok egy részhalmazának biztonságossá tételéhez. A keresési eredményeken alapuló, identitás-alapú hozzáféréshez biztonsági szűrőket hozhat létre, amelyek identitás alapján metszik az eredményeket, és eltávolíthatja azokat a dokumentumokat, amelyekhez a kérelmezőnek nincs hozzáférése. További információ: [biztonsági szűrők](search-security-trimming-for-azure-search.md) és [biztonságos a Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Felügyeleti feladatok szerepkör szerint

Az Azure Cognitive Search esetében a szerepkörök a következő felügyeleti feladatokat támogató jogosultsági szintekhez vannak társítva:

| Szerepkör | Tevékenység |
| --- | --- |
| Tulajdonos |Hozza létre vagy törölje a szolgáltatást vagy a szolgáltatás bármely objektumát, beleértve az API-kulcsokat, az indexeket, az indexelő, az indexelő adatforrásokat és az indexelő-ütemterveket.<p>A szolgáltatás állapotának megtekintése, beleértve a darabszámot és a tárterület méretét.<p>Szerepkör-tagság hozzáadása vagy törlése (csak a tulajdonosok kezelhetik a szerepkör-tagságot).<p>Az előfizetés-rendszergazdák és a szolgáltatás tulajdonosai automatikus tagsággal rendelkeznek a tulajdonosi szerepkörben. |
| Közreműködő |Azonos szintű hozzáférés tulajdonosként, mínusz a RBAC szerepkör-kezelés. A közreműködők például létrehozhatnak vagy törölhetnek objektumokat, vagy megtekinthetik és újragenerálják az [API-kulcsokat](search-security-api-keys.md), de nem módosíthatják a szerepkör-tagságokat. |
| [Search Service közreműködő beépített szerepköre](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | A közreműködő szerepkörrel egyenértékű. |
| Olvasó |A szolgáltatás Essentials és metrikáinak megtekintése. Ennek a szerepkörnek a tagjai nem tekinthetik meg az indexet, az indexelő, az adatforrás vagy a kulcs információit.  |

A szerepkörök nem biztosítanak hozzáférési jogosultságot a szolgáltatási végpont számára. A keresési szolgáltatási műveleteket, például az indexelést, az indexelést és a keresési adatok lekérdezéseit az API-kulcsok vezérlik, nem pedig a szerepkörök. További információ: az [API-kulcsok kezelése](search-security-api-keys.md).

## <a name="see-also"></a>Lásd még

+ [Kezelés a PowerShell-lel](search-manage-powershell.md) 
+ [Teljesítmény és optimalizálás az Azure Cognitive Search](search-performance-optimization.md)
+ [Ismerkedjen meg a Azure Portal szerepköralapú Access Controlával](../role-based-access-control/overview.md).