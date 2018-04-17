---
title: A portálon beállított RBAC-szerepkörök az Azure Search rendszergazdai hozzáférés |} Microsoft Docs
description: Szerepkör alapú felügyelet az Azure portálon.
services: search
documentationcenter: ''
author: HeidiSteen
manager: cgronlun
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: d14bf9b154c450d863d4d365c215e9712694a767
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="set-rbac-roles-for-administrative-access"></a>A rendszergazdai hozzáférés RBAC-szerepkörök beállítása

Azure biztosít egy [globális szerepkör-alapú engedélyezési modellt](../role-based-access-control/role-assignments-portal.md) a portál vagy a Resource Manager API-k használatával kezeli az összes szolgáltatáshoz. Tulajdonos, közreműködő, és ahhoz való olvasóra szerepkörök határozzák meg azt a szintjét *felügyeleti szolgáltatás* Active Directory-felhasználók, csoportok és minden egyes szerepkörhöz hozzárendelt rendszerbiztonsági. 

> [!Note]
> Nincs nem szerepköralapú hozzáférés-vezérléssel biztonságossá tétele a dokumentumok egy részhalmazát vagy az index egyes részei. A keresési eredmények azonosító-alapú hozzáférés létrehozhat lehet levágni eredményeket e identitása, a biztonsági szűrők eltávolítása a dokumentumok, amelynek a kérelmező nem rendelkeznek hozzáféréssel. További információkért lásd: [biztonsági szűrők](search-security-trimming-for-azure-search.md) és [biztonságos és az Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Szerepkör felügyeleti feladatok

Az Azure Search szerepkörök társítva a jogosultsági szintek, amelyek támogatják az alábbi felügyeleti feladatokat:

| Szerepkör | Tevékenység |
| --- | --- |
| Tulajdonos |Hozzon létre, vagy a szolgáltatás vagy a szolgáltatás, így az api-kulcsokat, indexek, indexelők, indexelő adatforrások és az indexelő ütemezések bármely objektum törlése.<p>Szolgáltatás állapotának, beleértve a számát, valamint a tárhely méretét megtekintéséhez.<p>Adja hozzá, vagy törölje a szerepköri tagság (csak egy fiók tulajdonosa felügyelheti szerepköri tagság).<p>Az előfizetés rendszergazdáihoz és a szolgáltatások tulajdonosait tartoznia automatikus a tulajdonosok szerepkör. |
| Közreműködő |Ugyanazt a hozzáférési szintet tulajdonosaként RBAC szerepkörkezelés csökkentve. Például egy munkatárs létrehozása vagy törölje annak objektumát, vagy megtekintheti és újragenerálja [api-kulcsokat](search-security-api-keys.md), de nem módosíthatja a szerepkörtagságok. |
| [Keresési szolgáltatás közreműködői beépített szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | A közreműködői szerepkör egyenértékű. |
| Olvasó |Nézet szolgáltatás essentials és metrikákat. A szerepkör tagjai nem lehet megtekinteni, index, az indexelő, az adatforrás vagy kapcsolatos információkat.  |

Szerepkörök nem a szolgáltatási végpont hozzáférési jogot biztosít. Keresési szolgáltatás műveletek, például a Indexkezelés, index feltöltése és lekérdezések keresési adatokon api-kulcsokat, a szerepkörök nem szabályozza. További információkért lásd: [api-kulcsok kezelése](search-security-api-keys.md).

## <a name="see-also"></a>Lásd még

+ [Kezelés a PowerShell használatával](search-manage-powershell.md) 
+ [Teljesítmény- és az Azure Search optimalizálása](search-performance-optimization.md)
+ [Ismerkedés a szerepköralapú hozzáférés-vezérlés az Azure portálon](../role-based-access-control/overview.md).