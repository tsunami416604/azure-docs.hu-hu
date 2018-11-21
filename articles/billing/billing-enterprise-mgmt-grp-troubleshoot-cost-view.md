---
title: Azure vállalati költségek nézet hibaelhárítása |} A Microsoft Docs
description: Megtudhatja, hogyan lehetséges, hogy az Azure Portalon szervezeti költség nézetekkel kapcsolatos problémák megoldásához.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2017
ms.author: cwatson
ms.openlocfilehash: be74714667c2fed57a797a972ddcd7949cae5933
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52274670"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Vállalati költségek nézet hibaelhárítása 

Vállalati belépéseket, belül több beállítást, amely a felhasználót a regisztrációs belül nem fogja tudni megtekinteni a költségek tartoznak.  Ezek a beállítások kezelik a beléptetés adminisztrátora, vagy a partner által, ha a regisztráció nem vásárolta meg közvetlenül a Microsofttól.  Ezen cikk segítségével megtudhatja, Mik azok a beállítások, és hogy azok hatással a regisztráció. Ezek a beállítások függetlenek a [Azure RBAC-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 


## <a name="enabling-access-to-costs"></a>Költségek való hozzáférés engedélyezése

Nem engedélyezett, üzenetet látja, vagy *"költség nézetek le vannak tiltva a regisztrációs."* Ha a költségadatok keres? ![nem engedélyezett](media/billing-enterprise-mgmt-groups/unauthorized.png)

Az alábbi okok egyike miatt lehet:

1. Vásárolt Azure enterprise-partneren keresztül, és a partner nem kiadott, még díjszabása. Díjszabás feloldásához forduljon a partneréhez frissíteni a beállítást belül a [az Enterprise portal](https://ea.azure.com).
2. Azt is megteheti Ha már a közvetlen nagyvállalati szerződéssel rendelkező ügyfelek, van néhány lehetőségeket:
    * Fiók tulajdonosa, és a beléptetés adminisztrátora letiltotta a "AO költségek megtekintése" beállítást.  
    * Részleg rendszergazdaként, és a beléptetés adminisztrátora letiltotta a "DA költségek megtekintése" beállítást.
    * Lépjen kapcsolatba a regisztrációs rendszergazdától kérhet hozzáférést. A regisztrációs adminisztrátoránál keresse fel a [az Enterprise portal](https://ea.azure.com/manage/enrollment) és frissíteni a beállítást, ahogy az képen látható:

![A vállalati portál beállításai](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>Eszköz nem érhető el? 
Ha kap hibaüzenetet "az eszköz nem érhető el" Amikor megpróbál hozzáférni egy előfizetést vagy felügyeleti csoportot, akkor nem rendelkezik a megfelelő szerepkört ezen elem megtekintéséhez.  

![az eszközintelligencia nem található](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Kérje a rendszergazda hozzáférést az előfizetés vagy a felügyeleti csoportok.  
* Az előfizetések, hivatkozhat [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) a dokumentum segítséget, amelyen a szerepkör szükséges.
