---
title: A portálban – Azure Search rendszergazdai hozzáféréshez az Azure RBAC-szerepkörök beállítása
description: Szerepkör alapú felügyeleti vezérlés (RBAC) szabályozása és az Azure Search management felügyeleti feladatok delegálása az Azure Portalon.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 38b8e8a0e413f367d34a4ccf5dbd87817891b8ea
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313020"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Állítsa be a rendszergazdai hozzáférés RBAC-szerepkörök

Az Azure biztosít egy [globális szerepkör-alapú engedélyezési modellt](../role-based-access-control/role-assignments-portal.md) a portálon vagy a Resource Manager API-k segítségével kezelt szolgáltatásokhoz. Tulajdonos, közreműködő és olvasó szerepkörök határozzák meg az *szolgáltatás felügyeletét tárgyaló* Active Directory felhasználók, csoportok és minden egyes szerepkörhöz hozzárendelt rendszerbiztonsági. 

> [!Note]
> Nincs nincsenek szerepköralapú hozzáférés-vezérlők az index egyes részei vagy dokumentumok egy részét. A keresési eredmények az identitásalapú hozzáférés létrehozhat által identitás, az eredmények azokat a biztonsági szűrők, amelynek a kérelmező nem szeretett volna dokumentumok eltávolítása. További információkért lásd: [biztonsági szűrők](search-security-trimming-for-azure-search.md) és [az Active Directory biztonságos](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Felügyeleti feladatok szerepkör szerint

Az Azure Search a szerepkörök társítva, amelyek támogatják az alábbi kezelési feladatok jogosultsági szintek:

| Szerepkör | Tevékenység |
| --- | --- |
| Tulajdonos |Hozzon létre, vagy a szolgáltatás vagy bármely objektumához a szolgáltatást, ideértve az api-kulcsok, indexek, indexelők, az indexelő az adatforrásokat és indexelő ütemezések törlése.<p>Szolgáltatás állapota, beleértve a számát, és a tároló mérete megtekintése.<p>Adjon hozzá vagy törlése (csak egy tulajdonosa kezelheti szerepkörtagság) szerepkör tagságát.<p>Előfizetések rendszergazdái és szolgáltatások tulajdonosai rendelkező automatikus tagsági tulajdonosai. |
| Közreműködő |Ugyanazt a hozzáférési szintet tulajdonosként, csökkentve az RBAC-szerepkörök kezeléséhez. Közreműködője létrehozhat vagy törölhet objektumok, például vagy megtekintéséhez, és hozza létre újra [api-kulcsainak](search-security-api-keys.md), de nem módosíthatják a szerepkör tagságát. |
| [Search szolgáltatás Közreműködője beépített szerepkörrel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Közreműködői szerepkörével egyenértékű. |
| Olvasó |Nézet szolgáltatás essentials és a metrikákat. Ezen szerepkör tagjai nem jelennek meg index, indexelő, az adatforrás vagy kulcsot.  |

Szerepkörök nem hozzáférési jogosultságokat biztosíthat a a szolgáltatásvégpontot. Keresési szolgáltatás műveletek, például index kezelését, az index feltöltése és a lekérdezések a keresési adatok api-kulcsok, nem szerepkörök szabályozza-e. További információkért lásd: [api-kulcsok kezelése](search-security-api-keys.md).

## <a name="see-also"></a>Lásd még

+ [Kezelés a PowerShell használatával](search-manage-powershell.md) 
+ [Teljesítmény és optimalizálás az Azure Search szolgáltatásban](search-performance-optimization.md)
+ [Ismerkedés a szerepköralapú hozzáférés-vezérlés az Azure Portalon](../role-based-access-control/overview.md).