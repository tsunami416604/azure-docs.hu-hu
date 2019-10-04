---
title: Felügyelt identitás hitelesítése Azure Active Directory
description: Ez a cikk az Azure Event Hubs-erőforrások eléréséhez Azure Active Directory felügyelt identitások hitelesítésével kapcsolatos információkat tartalmaz.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992834"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez
Az Azure Event Hubs támogatja a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../active-directory/managed-identities-azure-resources/overview.md). Az Azure-erőforrások felügyelt identitásai engedélyezhetik Event Hubs erőforrásokhoz való hozzáférést az Azure Virtual Machines (VM), a Function apps, a Virtual Machine Scale Sets és más szolgáltatások által futtatott alkalmazások Azure AD-beli hitelesítő adataival. Ha felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure AD-hitelesítéssel együtt, elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásaival.

Ez a cikk bemutatja, hogyan engedélyezheti az Azure-beli virtuális gépek felügyelt identitásával való hozzáférést az Event hub számára.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése egy virtuális gépen
Mielőtt felügyelt identitásokat használ az Azure-erőforrásokhoz a virtuális gép Event Hubs erőforrásainak engedélyezéséhez, először engedélyeznie kell az Azure-erőforrások felügyelt identitásait a virtuális gépen. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Engedélyek megadása felügyelt identitásnak az Azure AD-ben
Ahhoz, hogy az alkalmazásban egy felügyelt identitástól Event Hubs szolgáltatásra vonatkozó kérelmet engedélyezzen, először konfigurálja a felügyelt identitáshoz tartozó szerepköralapú hozzáférés-vezérlés (RBAC) beállításait. Az Azure Event Hubs meghatározza azokat a RBAC-szerepköröket, amelyek a Event Hubs küldésére és olvasására vonatkozó engedélyekkel rendelkeznek. Ha a RBAC szerepkör felügyelt identitáshoz van rendelve, a felügyelt identitás hozzáférést kap a megfelelő hatókörben Event Hubs adatokhoz.

A RBAC-szerepkörök hozzárendelésével kapcsolatos további információkért tekintse meg a [Azure Active Directory hitelesítés a Event Hubs erőforrásokhoz való hozzáféréshez](authorize-access-azure-active-directory.md)című témakört.

## <a name="use-event-hubs-with-managed-identities"></a>Event Hubs használata felügyelt identitásokkal
Ha a Event Hubs felügyelt identitásokkal szeretné használni, hozzá kell rendelnie a szerepkört és a megfelelő hatókört. Az ebben a szakaszban szereplő eljárás egy olyan egyszerű alkalmazást használ, amely felügyelt identitás alatt fut, és Event Hubs erőforrásokhoz fér hozzá.

Itt egy [Azure app Service](https://azure.microsoft.com/services/app-service/)-ban üzemeltetett minta webalkalmazást használunk. A webalkalmazások létrehozásával kapcsolatos részletes útmutatásért lásd: [ASP.net Core Webalkalmazás létrehozása az Azure-ban](../app-service/app-service-web-get-started-dotnet.md)

Az alkalmazás létrehozása után kövesse az alábbi lépéseket: 

1. Lépjen a **Beállítások** menüpontra, és válassza az **identitás**elemet. 
1. Válassza ki a bekapcsolni kívánt **állapotot** . 
1. A beállítás mentéséhez kattintson a **Mentés** gombra. 

    ![Webalkalmazás felügyelt identitása](./media/authenticate-managed-identity/identity-web-app.png)

Ha engedélyezte ezt a beállítást, a rendszer létrehoz egy új szolgáltatás-identitást a Azure Active Directory (Azure AD), és az App Service gazdagépre van konfigurálva.

Most rendeljen hozzá egy szerepkörhöz a szolgáltatás identitását a Event Hubs erőforrásaihoz szükséges hatókörben.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>RBAC szerepkörök kiosztása a Azure Portal használatával
Ha szerepkört szeretne hozzárendelni Event Hubs erőforrásokhoz, navigáljon az adott erőforráshoz a Azure Portal. Jelenítse meg az erőforrás Access Control (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> A következő lépések szolgáltatás-identitási szerepkört rendelnek a Event Hubs névterekhez. Ugyanezeket a lépéseket követve rendelhet hozzá egy szerepkört egy Event Hubs erőforráshoz. 

1. A Azure Portal navigáljon a Event Hubs névtérhez, és jelenítse meg a névtér áttekintését. 
1. A bal oldali menüben válassza a **Access Control (iam)** lehetőséget az Event hub hozzáférés-vezérlési beállításainak megjelenítéséhez.
1.  Válassza ki a **szerepkör** -hozzárendelések lapot a szerepkör-hozzárendelések listájának megtekintéséhez.
3.  Új szerepkör hozzáadásához válassza a **Hozzáadás** lehetőséget.
4.  A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a hozzárendelni kívánt Event Hubs-szerepköröket. Ezután keresse meg a szerepkör hozzárendeléséhez regisztrált szolgáltatás identitását.
    
    ![Szerepkör-hozzárendelési lap hozzáadása](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Kattintson a **Mentés** gombra. Az az identitás, akihez a szerepkört hozzárendelte, megjelenik az adott szerepkör alatt. Az alábbi képen például látható, hogy a szolgáltatás identitása Event Hubs adattulajdonost tartalmaz.
    
    ![Szerepkörhöz rendelt identitás](./media/authenticate-managed-identity/role-assigned.png)

A szerepkör hozzárendelése után a webalkalmazás hozzáférhet a megadott hatókörben lévő Event Hubs erőforrásokhoz. 

### <a name="test-the-web-application"></a>A webalkalmazás tesztelése
Most már elindíthatja a webalkalmazást, és irányíthatja a böngészőt az aspx-minta lapra. Megtalálhatja a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)-tárházban található Event Hubs-erőforrásokból adatokat küldő és fogadó minta webalkalmazást.

Telepítse a legújabb csomagot a [Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)-ből, és kezdje el küldeni és fogadni az Event huboktól származó adatokat az EventHubClient a következő kódban látható módon: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>További lépések
- Töltse le a [mintát](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp) a githubról.
- Az Azure-erőforrások felügyelt identitásának megismeréséhez tekintse meg a következő cikket: [Mit kell tudni az Azure-erőforrások felügyelt identitásairól?](../active-directory/managed-identities-azure-resources/overview.md)
- Tekintse meg a következő kapcsolódó cikkeket:
    - [Kérelmek hitelesítése az Azure Event Hubs alkalmazásból Azure Active Directory használatával](authenticate-application.md)
    - [Kérelmek hitelesítése az Azure Event Hubs megosztott hozzáférési aláírások használatával](authenticate-shared-access-signature.md)
    - [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](authorize-access-azure-active-directory.md)
    - [Hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)