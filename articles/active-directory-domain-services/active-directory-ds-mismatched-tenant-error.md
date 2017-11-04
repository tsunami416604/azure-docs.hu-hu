---
title: "Hárítsa el a meglévő Azure AD tartományi szolgáltatásokat a felügyelt tartományok nem egyező directory hibákat |} Microsoft Docs"
description: "Ismerje meg, és hárítsa el a meglévő Azure AD tartományi szolgáltatásokat a felügyelt tartományok nem egyező directory hibákat"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: maheshu
ms.openlocfilehash: 9c9a47e9b3050eb7f41202d6a4b9202ba0f379df
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Hárítsa el a meglévő Azure AD tartományi szolgáltatásokat a felügyelt tartományok nem egyező directory hibákat
Rendelkezik egy meglévő kezelt tartományban, amelyekre engedélyezve voltak a klasszikus Azure portál használatával. Keresse meg az új Azure-portálon, és megtekintheti a felügyelt tartományra, a következő hibaüzenet jelenik meg:

![Nem egyező könyvtár hibája](.\media\getting-started\mismatched-tenant-error.png)

A hiba kijavításáig nem felügyelheti a felügyelt tartományra.


## <a name="whats-causing-this-error"></a>Mi okozza ezt a hibát?
Ezt a hibát az okozza, ha a felügyelt tartományok és engedélyezve van a virtuális hálózat tartozik két különböző Azure AD-bérlő. Például egy felügyelt tartomány "contoso.com" nevezik, és a Contoso Azure AD-bérlő engedélyezve lett. Azonban az Azure virtuális hálózat, amelyen engedélyezve volt a felügyelt tartományra tartozik Fabrikam - egy másik Azure AD-bérlő.

Az új Azure-portálon (és kifejezetten az Azure AD tartományi szolgáltatások kiterjesztése) az Azure Resource Manager-t. A modern Azure Resource Manager-környezetben az egyes korlátozások elküldésére nagyobb biztonságot, és a szerepkör alapú hozzáférés szabályozásához (RBAC) erőforrások érvényesek lesznek. A bizalmas művelet engedélyezése az Azure AD tartományi szolgáltatásokat az Azure AD-bérlő azért, mert azt eredményezi, hogy a felügyelt tartományhoz szinkronizálandó hitelesítő kivonatokat. Ehhez a művelethez szükséges egy Bérlői rendszergazda a címtár adható meg, hogy. Emellett rendszergazdai jogosultságokkal kell rendelkeznie, amelyben a felügyelt tartományra engedélyezi a virtuális hálózaton. Az RBAC ellenőrzi, hogy következetesen működjenek, az a felügyelt tartományra, és a virtuális hálózati kell tartoznia a azonos Azure AD-bérlő.

Rövid nem engedélyezhető egy felügyelt tartomány a "contoso.com" Azure AD-bérlő tulajdonosa egy másik Azure AD-bérlő "fabrikam.com" Azure-előfizetéshez tartozó virtuális hálózatban. A klasszikus Azure portálon nem a Resource Manager platformra épül, és nem kényszeríti ki az ilyen korlátozások.

**Érvényes konfigurációs**: Ez a telepítési forgatókönyvben a Contoso kezelt tartomány engedélyezve van a Contoso Azure AD bérlői. A felügyelt tartomány fel van fedve egy virtuális hálózaton, a Contoso Azure AD bérlő tulajdonában Azure-előfizetéshez tartozó. Ezért mind a felügyelt tartományra, valamint a virtuális hálózat tartozik ugyanahhoz az Azure AD-bérlő. Ez a konfiguráció nincs érvényes és teljes mértékben támogatott.

![Érvényes bérlő konfigurációjához](./media/getting-started/valid-tenant-config.png)

**Nem egyező bérlő konfigurációjához**: Ez a telepítési forgatókönyvben a Contoso kezelt tartomány engedélyezve van a Contoso Azure AD bérlői. Azonban a felügyelt tartományra tesz elérhetővé a virtuális hálózat, amely egy Azure-előfizetés tulajdonosa a Fabrikam az Azure AD-bérlő tartozik. Emiatt a felügyelt tartományra és a virtuális hálózat tartozik két különböző Azure AD-bérlő. Ez a konfiguráció a nem egyező bérlői konfigurációt, és nem támogatott. A virtuális hálózaton át kell helyezni az azonos Azure AD-bérlő (Ez azt jelenti, hogy a Contoso), a felügyelt tartományra. Tekintse meg a [feloldási](#resolution) című szakaszban talál információt.

![Nem egyező bérlő konfigurációjához](./media/getting-started/mismatched-tenant-config.png)

Ezért, ha a felügyelt tartományra, és engedélyezve van a virtuális hálózat tartozik két különböző ezt a hibát látva az Azure AD-bérlő.

Az alábbi szabályok vonatkoznak a Resource Manager-környezetben:
- Előfordulhat, hogy az Azure AD-címtár több Azure-előfizetéssel.
- Előfordulhat, hogy az Azure-előfizetés több erőforrások, például virtuális hálózatokat.
- Azure AD tartományi szolgáltatásokat egyetlen felügyelt tartomány engedélyezve van az Azure AD-címtár.
- Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz engedélyezhető olyan virtuális hálózaton belül az azonos Azure AD-bérlő az Azure-előfizetések tartozó.


## <a name="resolution"></a>Megoldás:
A nem egyező directory hiba megoldása érdekében két választási lehetősége van. Előfordulhat:

- Kattintson a **törlése** gombbal pedig törölheti a meglévő felügyelt tartomány. Hozza létre a használatával a [Azure-portálon](https://portal.azure.com), hogy a felügyelt tartomány és a virtuális hálózat érhető el az Azure AD-címtár tartozik. Csatlakoztassa az összes gép korábban az újonnan létrehozott felügyelt tartományra törölt tartományhoz csatlakozik.

- Helyezze át az Azure-előfizetés tartalmazó a virtuális hálózatot az Azure AD-címtárral, a felügyelt tartomány, amelyhez tartozik. Kövesse a [egy másik fiókot az Azure-előfizetés tulajdonjogának átruházása](../billing/billing-subscription-transfer.md) cikk.


## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Hibaelhárítási útmutató – az Azure AD tartományi szolgáltatások](active-directory-ds-troubleshooting.md)
