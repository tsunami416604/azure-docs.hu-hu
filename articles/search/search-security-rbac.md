---
title: RBAC-szerepkörök beállítása az Azure rendszergazdai hozzáféréshez
titleSuffix: Azure Cognitive Search
description: Szerepköralapú felügyeleti vezérlés (RBAC) az Azure Cognitive Search Management felügyeleti feladatainak vezérlésére és delegálására szolgáló Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 402fae5622219b14cfdab921ebe1a78ad5dd111e
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84462838"
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

## <a name="permissions-table"></a>Engedélyek tábla

Az alábbi táblázat összefoglalja az Azure Cognitive Searchban engedélyezett műveleteket, és egy adott művelethez hozzáférő kulcs feloldja azokat.

| Művelet | Engedélyek |
|-----------|-------------------------|
| Szolgáltatás létrehozása | Azure-előfizetés tulajdonosa |
| Szolgáltatás méretezése | Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson  |
| Szolgáltatás törlése | Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson |
| Objektumok létrehozása, módosítása és törlése a szolgáltatásban: <br>Indexek és összetevők (beleértve az analizátor-definíciókat, a pontozási profilokat, a CORS lehetőségeket), az indexelő, az adatforrások, a szinonimák, a javaslatok | Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson |
| Index lekérdezése | Rendszergazdai vagy lekérdezési kulcs (a RBAC nem alkalmazható) |
| A rendszerinformációk lekérdezése, például statisztikák, Darabszámok és az objektumok listája | Rendszergazdai kulcs, RBAC az erőforráson (tulajdonos, közreműködő, olvasó) |
| Rendszergazdai kulcsok kezelése | Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson |
| Lekérdezési kulcsok kezelése |  Rendszergazdai kulcs, RBAC tulajdonos vagy közreműködő az erőforráson  |

## <a name="see-also"></a>További információ

+ [Kezelés a PowerShell-lel](search-manage-powershell.md) 
+ [Teljesítmény és optimalizálás az Azure Cognitive Search](search-performance-optimization.md)
+ [Ismerkedjen meg a Azure Portal szerepköralapú Access Controlával](../role-based-access-control/overview.md).