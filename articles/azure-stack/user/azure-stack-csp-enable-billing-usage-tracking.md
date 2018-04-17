---
title: A Felhőbeli szolgáltatás szolgáltatója az Azure-verem előfizetés kezelése céljából engedélyezése |} Microsoft Docs
description: Azure-készletben előfizetés eléréséhez a szolgáltató engedélyezése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 26ba68be6d4932da77befaf7c968525393c0a033
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>A Felhőbeli szolgáltatás szolgáltatója az Azure-verem előfizetés kezelése céljából engedélyezése

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ha Azure veremmel rendelkező egy Felhőszolgáltató (CSP) használ, a az Azure-előfizetés és Azure verem erőforrásokhoz való hozzáférés a szolgáltató által kezelhető. Vagy a saját előfizetésének is kezelheti. Ez a cikk ellenőrzi, hogy az a szolgáltató hozzáférhet az előfizetéséhez az Ön nevében, vagy győződjön meg arról, hogy a szolgáltató kezelheti a szolgáltatás engedélyezése.

> [!Note]  
>  Ha a következő kimarad, és a CSP már nem kezelése a fiókját, a CSP nem lehet az Ön nevében Azure verem előfizetése kezeléséhez.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>A Felhőbeli szolgáltatás szolgáltatója előfizetés kezelése

1. A kriptográfiai Szolgáltató felhasználóként a Vendég a felhasználói szerepkör hozzáadása a bérlő címtárát.  A felhasználók hozzáadásával kapcsolatos utasítások: [új felhasználók hozzáadása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. A kriptográfiai Szolgáltató majd hoz létre a helyi Azure verem előfizetési meg.
3. Készen áll az Azure verem használatának megkezdéséhez.
3. A kriptográfiai Szolgáltató kell majd hozzon létre egy erőforrást a előfizetés győződjön meg arról, hogy az erőforrások is kezelhetik. Például a következőkre [Windows virtuális gép létrehozása Azure verem Portal](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>A Felhőbeli szolgáltatás szolgáltatója RBAC jogosultságokkal az előfizetés kezelése céljából engedélyezése

A kriptográfiai Szolgáltató tulajdonosa hozzá az előfizetéséhez. 

1. Adja hozzá a kriptográfiai Szolgáltató Vendég felhasználó. a bérlő címtár tulajdonosa szerepkörrel.  A felhasználók hozzáadásával kapcsolatos utasítások: [új felhasználók hozzáadása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. Tulajdonosi szerepkör hozzáadása a CSP Vendég felhasználó. A kriptográfiai Szolgáltató felhasználó hozzáadása az előfizetéséhez lépéseiért lásd: [Use Role-Based hozzáférés-vezérlés az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. A kriptográfiai Szolgáltató majd hoz létre a helyi Azure verem előfizetési meg.
4. Készen áll az Azure verem használatának megkezdéséhez.
5. A kriptográfiai Szolgáltató kell majd hozzon létre egy erőforrást a előfizetés győződjön meg arról, hogy az erőforrások kezelésére. 

## <a name="next-steps"></a>További lépések

  - Hogyan lehet lekérni az erőforrás-használati adatait az Azure oszlopból kapcsolatos további információkért lásd: [használati és számlázási Azure verem](../azure-stack-billing-and-chargeback.md).
