---
title: Nézetek - Azure költség vállalati hibaelhárítása |} Microsoft Docs
description: Megtudhatja, hogyan lehetséges, hogy az Azure-portálon belül szervezeti költség nézetekkel kapcsolatos problémák megoldásához.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: rithorn
ms.openlocfilehash: 527e12cdcc4eaebf98708c389dd21e8ac6ae4ac7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-enterprise-cost-views"></a>Nézetek költség vállalati hibaelhárítása 

Vállalati regisztrációkat belül beállításokat a rendszer több, amely a felhasználót a regisztráció belül nem tudják megjeleníteni a költségeket.  Ezek a beállítások kezelik a beléptetési rendszergazdája, vagy a partner által a tagság nem vásárolt közvetlenül a Microsofttal.  Ez a cikk segít megérteni a Mik azok a beállítások, és milyen hatással lesznek a regisztrációt. Ezek a beállítások függetlenek a [Azure RBAC-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 


## <a name="enabling-access-to-costs"></a>Költségek való hozzáférés engedélyezése

Ön jelent meg, egy üzenet nem engedélyezett, vagy *"költség nézetek le vannak tiltva a regisztrációs."* Amikor költség információkat keres? ![nem engedélyezett](media/billing-enterprise-mgmt-groups/unauthorized.png)

Lehet, hogy a következő okok egyikéből adódóan:

1. Azure egy vállalati partner keresztül vásárolt, és a partner nem kiadásakor még árképzési. Árképzési feloldásához forduljon a partneréhez belül beállítás frissítése a [vállalati portál](https://ea.azure.com).
2. Másik lehetőségként Ha az ügyfél egy EA közvetlen, többféle lehetőségeket:
    * A fiók tulajdonosa és a beléptetési rendszergazda letiltotta a "AO költségek megtekintése" beállítást.  
    * Szervezeti egység rendszergazdája, és a beléptetési rendszergazda letiltotta a "DA költségek megtekintése" beállítást.
    * A beléptetési rendszergazdától is elérheti. A beléptetési Admin is keresheti a [vállalati portál](https://ea.azure.com/manage/enrollment) , és frissítse a beállítást, ahogy az képen látható:

![Vállalati portál beállításai](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>Nem érhető el az eszköz? 
Ha azért kapta, egy hibaüzenet "Ez az eszköz nem érhető el" Amikor megpróbál hozzáférni egy előfizetés vagy egy felügyeleti csoportot, majd nincs a megfelelő szerepkör ezen elem megtekintéséhez.  

![eszköz nem található](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Kérje a rendszergazda az előfizetés vagy felügyeleti csoportok hozzáférést.  
* Előfizetések hivatkozhat [átruházásához hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) dokumentum segítséget, amelyen a szerepkör szükséges.
