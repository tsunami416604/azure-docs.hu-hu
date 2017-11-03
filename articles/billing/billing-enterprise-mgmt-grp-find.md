---
title: "Egy Azure-előfizetés vagy a felügyeleti csoport – Azure található |} Microsoft Docs"
description: "Hogyan nyithatja meg a felügyeleti csoport megtekintéshez több könyvtárak és előfizetések között"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: df4d85d0556a62311c112f24431b54d042333c7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>Az Azure-előfizetés vagy felügyeleti csoportok keresése

Ha nem találjuk egy előfizetés vagy a felügyeleti csoport az Azure-ban, előfordulhat, hogy keresünk a megfelelő könyvtárban. Ez a helyzet akkor fordulhat elő, ha a fiók létezik-e több aktív Azure-könyvtárak. Minden egyes [nem függ az active directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-directory-independence) és könyvtárak között nem örökli a hozzáférést.      

![Kapcsoló Directory menü](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>Kapcsoló könyvtárak 
Könyvtárak egyszerűen válthat az Azure portálon.
1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  Jelölje ki a nevét a felső sarkában a képernyőn. 
3.  A menü alján rendelkezik hozzáféréssel az összes könyvtárak listája.
4.  Válassza ki a könyvtár eléréséhez. 

![Kapcsoló Directory menü](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>Nem érhető el az eszköz? 
Ha azért kapta, egy hibaüzenet "Ez az eszköz nem érhető el" Amikor megpróbál hozzáférni egy előfizetés vagy egy felügyeleti csoportot, majd nincs a megfelelő szerepkör ezen elem megtekintéséhez.  

![eszköz nem található](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Kérje a rendszergazda az előfizetés vagy felügyeleti csoportok hozzáférést.  
* Előfizetések hivatkozhat [átruházásához hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) dokumentum segítséget, amelyen a szerepkör szükséges.
* A felügyeleti csoportok a Szerepalapú hozzáférés nem érhető el, és. hamarosan már az. Forduljon a vállalati portál felügyeletéhez hozzárendelt elérhető legyen.   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>A felhasználói élmény javítása a felügyeleti csoportok és előfizetések ugyanabban a könyvtárban 
Az előfizetések vagy a felügyeleti csoportok vihetők át a könyvtárba, ha úgy dönt, hogy minden szerepel ugyanazon a helyen.  Előfizetések és a felügyeleti csoportok egyesítse könyvtárába segít csökkenteni az örökölt házirendek könyvtárak váltson, és lehetővé teszik a szükséges.  


### <a name="transfer-your-subscriptions"></a>Az előfizetések átvitele 
A felügyeleti csoportok társított címtár, áthelyezheti egy előfizetést. Az áthelyezés érheti el, azzal, hogy a beléptetési rendszergazda az előfizetés átvitele a célkönyvtár a forráskönyvtárban található. További információkért jelentkezzen be a(z) a [vállalati portál](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription).


 






