---
title: RBAC-szerepkörök beállítása az Azure felügyeleti hozzáféréséhez
titleSuffix: Azure Cognitive Search
description: Szerepköralapú felügyeleti vezérlés (RBAC) az Azure Portalon az Azure Cognitive Search felügyeleti feladatok vezérléséhez és delegálásához.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112555"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>RBAC-szerepkörök beállítása az Azure Cognitive Search felügyeleti hozzáféréséhez

Az Azure globális szerepköralapú engedélyezési modellt biztosít a portálon vagy az [erőforrás-kezelő API-kon](../role-based-access-control/role-assignments-portal.md) keresztül kezelt összes szolgáltatáshoz. A tulajdonosi, közreműködői és olvasói szerepkörök határozzák meg az egyes szerepkörökhöz rendelt Active Directory-felhasználók, csoportok és rendszerbiztonsági tagok *szolgáltatásfelügyeletének* szintjét. 

> [!Note]
> Nincs szerepköralapú hozzáférés-vezérlés az index vagy a dokumentumok egy részhalmazának védelmére. Az identitásalapú hozzáférés a keresési eredményeken keresztül, biztonsági szűrőket hozhat létre az eredmények identitás szerint való levágására, eltávolítva azokat a dokumentumokat, amelyekhez a kérelmezőnek nem kellene hozzáférnie. További információt a [Biztonsági szűrők](search-security-trimming-for-azure-search.md) és az Active [Directoryval való védelem című témakörben talál.](search-security-trimming-for-azure-search-with-aad.md)

## <a name="management-tasks-by-role"></a>Felügyeleti feladatok szerepkör szerint

Az Azure Cognitive Search esetében a szerepkörök olyan jogosultsági szintekkel vannak társítva, amelyek a következő felügyeleti feladatokat támogatják:

| Szerepkör | Tevékenység |
| --- | --- |
| Tulajdonos |Hozza létre vagy törölje a szolgáltatást vagy bármely objektumot a szolgáltatásban, beleértve az api-kulcsokat, indexeket, indexelőket, indexelőadatforrásokat és indexelő ütemezéseket.<p>A szolgáltatás állapotának megtekintése, beleértve a számlálókat és a tárhely méretét.<p>Szerepkör-tagság hozzáadása vagy törlése (csak egy tulajdonos kezelheti a szerepkör-tagságot).<p>Az előfizetés-rendszergazdák és a szolgáltatástulajdonosok automatikus tagsággal rendelkeznek a Tulajdonosok szerepkörben. |
| Közreműködő |Ugyanolyan szintű hozzáférés, mint a tulajdonos, mínusz RBAC szerepkör-kezelés. Egy közreműködő például objektumokat hozhat létre vagy törölhet, illetve megtekintheti és újragenerálhatja [az API-kulcsokat,](search-security-api-keys.md)de nem módosíthatja a szerepkör-tagságokat. |
| [Keresési szolgáltatás közreműködője beépített szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | A közreműködői szerepkörrel egyenértékű. |
| Olvasó |Tekintse meg a szolgáltatás alapvető és metrikák. A szerepkör tagjai nem tekinthetik meg az indexet, az indexelőt, az adatforrást vagy a kulcsadatokat.  |

A szerepkörök nem adnak hozzáférési jogokat a szolgáltatás végpontjához. A keresési szolgáltatás műveleteit, például az indexkezelést, az indexsokakadáfiát és a keresési adatok lekérdezéseit api-kulcsok, nem szerepkörök szabályozzák. További információ: [Api-keys kezelése](search-security-api-keys.md).

## <a name="see-also"></a>Lásd még

+ [Kezelés a PowerShell-lel](search-manage-powershell.md) 
+ [Teljesítmény és optimalizálás az Azure Cognitive Search szolgáltatásban](search-performance-optimization.md)
+ [Első lépések az Azure Portalon a szerepköralapú hozzáférés-vezérléssel.](../role-based-access-control/overview.md)