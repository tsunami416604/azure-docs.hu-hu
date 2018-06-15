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
ms.openlocfilehash: f0cff8f575b87872c0032854f1916b140d7fd62b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357843"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>A Felhőbeli szolgáltatás szolgáltatója az Azure-verem előfizetés kezelése céljából engedélyezése

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ha Azure veremmel rendelkező egy Felhőszolgáltató (CSP) használ, akkor célszerű használni hozzáférését az erőforrásokhoz az Azure-ban, és az Azure-verem saját előfizetése kezelésére. A szolgáltató használhatja az Ön is engedélyezheti. Ez a cikk bemutatja, hogyan számára:

 * A szolgáltatás szolgáltató hozzáférést biztosít az előfizetéshez.
 * Győződjön meg arról, hogy a szolgáltató kezelheti a szolgáltatást.

> [!Note]
>  Ha a CSP a fiók nem kezelése, akkor hagyja ki a következő lépéseket, a kriptográfiai Szolgáltató az Azure-verem előfizetés nem tudja kezelni.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>A Felhőbeli szolgáltatás szolgáltatója előfizetés kezelése

Adja hozzá a kriptográfiai Szolgáltató, mint a **felhasználói** az előfizetéséhez.

1. A kriptográfiai Szolgáltató felhasználóként a Vendég a felhasználói szerepkör hozzáadása a bérlő címtárát.  A felhasználók hozzáadásával kapcsolatos utasítások: [új felhasználók hozzáadása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. A kriptográfiai Szolgáltató a helyi Azure verem előfizetést meg hoz létre.
3. Készen áll az Azure verem használatának megkezdéséhez.
4. A kriptográfiai Szolgáltató hozzon létre egy erőforrás ellenőrzése, hogy az erőforrások is kezelhetik az előfizetésben. Például a következőkre [Windows virtuális gép létrehozása Azure verem Portal](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>A Felhőbeli szolgáltatás szolgáltatója RBAC jogosultságokkal az előfizetés kezelése céljából engedélyezése

Adja hozzá a kriptográfiai Szolgáltató, mint a **tulajdonos** az előfizetéséhez.

1. A kriptográfiai Szolgáltató Vendég felhasználó hozzáadása a bérlő címtárát.  A felhasználók hozzáadásával kapcsolatos utasítások: [új felhasználók hozzáadása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)
2. A kriptográfiai Szolgáltató Vendég felhasználó hozzáadása a tulajdonosi szerepkört. A kriptográfiai Szolgáltató felhasználó hozzáadása az előfizetéséhez lépéseiért lásd: [Use Role-Based hozzáférés-vezérlés az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
3. A kriptográfiai Szolgáltató a helyi Azure verem előfizetést meg hoz létre.
4. Készen áll az Azure verem használatának megkezdéséhez.
5. A kriptográfiai Szolgáltató kell hozzon létre egy erőforrást az előfizetésében ellenőrzése, hogy az erőforrások kezelésére.

## <a name="next-steps"></a>További lépések

Hogyan lehet lekérni az erőforrás-használati adatait az Azure oszlopból kapcsolatos további információkért lásd: [használati és számlázási Azure verem](../azure-stack-billing-and-chargeback.md).
