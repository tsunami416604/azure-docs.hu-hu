---
title: Az Azure Stack-előfizetés kezelése céljából Felhőszolgáltató engedélyezése |} A Microsoft Docs
description: Engedélyezze a service provider, az Azure Stack-előfizetés eléréséhez.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: fca08ef1d803c3bd47b0ae925c4dd12255175f2c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630715"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Az Azure Stack-előfizetés kezelése céljából Felhőszolgáltató engedélyezése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ha az Azure Stack-egy felhőalapú szolgáltatás Felhőszolgáltató (CSP) használ, választhatja erőforrások eléréséhez az Azure-ban, és az Azure Stack saját előfizetése kezelésére. A szolgáltató, az előfizetés kezelése is engedélyezheti. Ez a cikk bemutatja, hogyan való:

 * A service provider hozzáférést biztosít az előfizetés.
 * Ellenőrizze, hogy a szolgáltató kezelheti a szolgáltatást.

> [!Note]
>  A kriptográfiai Szolgáltató nem a fiók kezelésében, és a következő lépés kihagyható, ha a kriptográfiai Szolgáltató az Azure Stack-előfizetést, nem tudja kezelni.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>A Felhőszolgáltató az előfizetés kezelése

Adja hozzá a CSP, **felhasználói** az előfizetéshez.

1. A CSP a felhasználói szerepkörrel rendelkező Vendég felhasználóként hozzá a bérlő címtárát.  A felhasználók hozzáadásával lépéseiért lásd: [új felhasználók hozzáadása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. A CSP hoz létre az Ön számára a helyi Azure Stack-előfizetéssel.
3. Készen áll az Azure Stack használatának megkezdéséhez.
4. A CSP hozzon létre egy erőforrást az előfizetésében, győződjön meg arról, hogy is kezelhetik az erőforrásokat. Ha például a következőkre [Windows virtuális gép létrehozása az Azure Stack portal](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>A Felhőszolgáltató kezeléséhez az előfizetésben az RBAC-jogosultságok engedélyezése

Adja hozzá a CSP, **tulajdonos** az előfizetéshez.

1. A kriptográfiai Szolgáltató, a vendégfelhasználó hozzáadása a bérlő címtárát.  A felhasználók hozzáadásával lépéseiért lásd: [új felhasználók hozzáadása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. A CSP vendégfelhasználó hozzáadása a tulajdonosi szerepkör. A CSP-felhasználó hozzáadása az előfizetéséhez lépéseiért lásd: [Use Role-Based hozzáférés-vezérlés az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. A CSP hoz létre az Ön számára a helyi Azure Stack-előfizetéssel.
4. Készen áll az Azure Stack használatának megkezdéséhez.
5. A CSP hozzon létre egy erőforrást az előfizetésében, győződjön meg arról, hogy képesek kezelni az erőforrásokat.

## <a name="next-steps"></a>További lépések

Erőforrás-használati adatok lekérése az Azure Stack kapcsolatos további tudnivalókért lásd: [használat és számlázás az Azure Stackben](../azure-stack-billing-and-chargeback.md).
