---
title: Felügyelt identitások az Azure-erőforrásokhoz a Service Bus segítségével
description: Ez a cikk ismerteti, hogyan használható felügyelt identitások eléréséhez az Azure Service Bus entitások (várólisták, témakörök és előfizetések) eléréséhez.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 46a1db94d576174b837a40c646fcf9e082e339c8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461616"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Felügyelt identitás hitelesítése az Azure Active Directoryval az Azure Service Bus erőforrásainak eléréséhez
[Felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md) egy Azure-közi funkció, amely lehetővé teszi, hogy hozzon létre egy biztonságos identitást társított központi telepítés, amely alatt az alkalmazáskód fut. Ezután társíthatja az identitást olyan hozzáférés-vezérlési szerepkörökkel, amelyek egyéni engedélyeket adnak az alkalmazás által igényelt speciális Azure-erőforrások eléréséhez.

Felügyelt identitások használatával az Azure platform kezeli ezt a futásidejű identitást. Nem kell tárolnia és védenie a hozzáférési kulcsokat az alkalmazáskódban vagy konfigurációban, sem magához az identitáshoz, sem az eléréshez szükséges erőforrásokhoz. Az Azure App Service-alkalmazáson belül vagy az Azure-erőforrások támogatásához engedélyezett felügyelt entitásokkal rendelkező virtuális gépen futó Service Bus-ügyfélalkalmazásnak nem kell kezelnie a SAS-szabályokat és -kulcsokat, illetve bármely más hozzáférési jogkivonatot. Az ügyfélalkalmazásnak csak a Service Bus Messaging névtér végpontcímére van szüksége. Amikor az alkalmazás csatlakozik, a Service Bus a felügyelt entitás környezetét az ügyfélhez köti egy olyan műveletben, amely a cikk későbbi részében egy példában látható. Miután egy felügyelt identitáshoz van társítva, a Service Bus-ügyfél minden engedélyezett műveletet elvégezhet. Az engedélyt egy felügyelt entitás Service Bus-szerepkörökkel való társítása adják meg. 

## <a name="overview"></a>Áttekintés
Amikor egy rendszerbiztonsági tag (egy felhasználó, csoport vagy alkalmazás) megpróbál hozzáférni egy Service Bus entitáshoz, a kérelmet engedélyezni kell. Az Azure AD-vel az erőforráshoz való hozzáférés két lépésből áll. 

 1. Először a rendszerbiztonsági tag identitása hitelesítve van, és egy OAuth 2.0-s jogkivonatot ad vissza. A jogkivonat ot kérő `https://servicebus.azure.net`erőforrás neve a.
 1. Ezután a jogkivonat a Service Bus szolgáltatásnak a megadott erőforráshoz való hozzáférés engedélyezésére irányuló kérelem részeként kerül átadásra.

A hitelesítési lépés megköveteli, hogy egy alkalmazáskérelem tartalmazzon egy OAuth 2.0 hozzáférési jogkivonatot futásidőben. Ha egy alkalmazás fut egy Azure-entitás, például egy Azure virtuális gép, egy virtuális gép méretezési csoport, vagy egy Azure Function-alkalmazás, egy felügyelt identitás használatával hozzáférhet az erőforrásokhoz. 

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC szerepkörök legyenek hozzárendelve a rendszerbiztonsági taghoz. Az Azure Service Bus rbac-szerepköröket biztosít, amelyek a Service Bus-erőforrások engedélykészleteit foglalják magukban. A rendszerbiztonsági taghoz rendelt szerepkörök határozzák meg a rendszerbiztonsági tag engedélyeit. Ha többet szeretne tudni az RBAC-szerepkörök Azure Service Bushoz való hozzárendeléséről, olvassa [el a Beépített RBAC-szerepkörök az Azure Service Bushoz című témakört.](#built-in-rbac-roles-for-azure-service-bus) 

Natív alkalmazások és webalkalmazások, amelyek kéréseket a Service Bus is engedélyezheti az Azure AD.Native applications and web applications that make requests to Service Bus can also authorize with Azure AD. Ez a cikk bemutatja, hogyan kérhet egy hozzáférési jogkivonatot, és használja a Service Bus-erőforrások ra vonatkozó kérelmek engedélyezéséhez. 


## <a name="assigning-rbac-roles-for-access-rights"></a>RBAC-szerepkörök hozzárendelése hozzáférési jogokhoz
Az Azure Active Directory (Azure AD) [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)révén engedélyezi a védett erőforrásokhoz való hozzáférési jogokat. Az Azure Service Bus egy beépített RBAC-szerepkörkészletet határoz meg, amely a Service Bus-entitások eléréséhez használt közös engedélykészleteket foglalja magában, és egyéni szerepköröket is definiálhat az adatok eléréséhez.

Ha egy RBAC-szerepkör egy Azure AD rendszerbiztonsági taghoz van rendelve, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz az adott rendszerbiztonsági tag számára. Az Access hatóköre az előfizetés, az erőforráscsoport vagy a Service Bus névtér szintjére terjed ki. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy felügyelt identitás az Azure-erőforrások.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Beépített RBAC-szerepkörök az Azure Service Bushoz
Az Azure Service Bus esetében a névterek és az összes kapcsolódó erőforrás kezelése az Azure Portalon és az Azure erőforrás-kezelési API-n keresztül már védett a *szerepköralapú hozzáférés-vezérlési* (RBAC) modell használatával. Az Azure az alábbi beépített RBAC-szerepköröket biztosítja a Service Bus-névtérhez való hozzáférés engedélyezéséhez:

- [Azure Service Bus-adatok tulajdonosa:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)Lehetővé teszi az adatok elérését a Service Bus névtér és annak entitásai (várólisták, témakörök, előfizetések és szűrők)
- [Azure Service Bus data sender:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)ezzel a szerepkörrel küldési hozzáférést biztosít a Service Bus névtér és az entitások.
- [Azure Service Bus-adatfogadó:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)Ezzel a szerepkörrel fogadási hozzáférést biztosít a Service Bus névtér és az entitások. 

## <a name="resource-scope"></a>Erőforrás hatóköre 
Mielőtt rbac szerepkört rendelne egy rendszerbiztonsági taghoz, határozza meg a rendszerbiztonsági tag hozzáférési tartományát. A legjobb gyakorlatok azt diktálják, hogy mindig a legjobb, ha csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyeken a legszűkebb hatókörtől kezdve a Service Bus-erőforrásokhoz való hozzáférést hatókörbe lehet érni:

- **Várólista**, **témakör**vagy **előfizetés:** A szerepkör-hozzárendelés az adott Service Bus entitásra vonatkozik. Jelenleg az Azure Portal nem támogatja a felhasználók/csoportok/felügyelt identitások hozzárendelését a Service Bus RBAC-szerepkörökhöz az előfizetés szintjén. Íme egy példa az Azure CLI parancs használatára: [az-szerepkör-hozzárendelés-létrehozás](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) egy identitás hozzárendeléséhez egy Service Bus RBAC szerepkörhöz: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus névtér:** Szerepkör-hozzárendelés a teljes topológia a Service Bus a névtér és a hozzá társított fogyasztói csoport.
- **Erőforráscsoport**: A szerepkör-hozzárendelés az erőforráscsoport összes Service Bus erőforrására vonatkozik.
- **Előfizetés:** A szerepkör-hozzárendelés az előfizetés összes erőforráscsoportjában lévő összes Service Bus-erőforrásra vonatkozik.

> [!NOTE]
> Ne feledje, hogy az RBAC szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet. 

A beépített szerepkörök definiálásáról a [Szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md#management-and-data-operations)című témakörben talál további információt. Az egyéni RBAC-szerepkörök létrehozásáról az [Egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérléshez című](../role-based-access-control/custom-roles.md)témakörben talál további információt.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése virtuális számítógépen
Mielőtt az Azure Resources felügyelt identitások használatával engedélyezheti a Service Bus-erőforrásokat a virtuális gépről, először engedélyeznie kell az Azure Resources felügyelt identitásait a virtuális gépen. Ha tudni szeretné, hogyan engedélyezheti az Azure Resources felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféltárak](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Engedélyek megadása felügyelt identitáshoz az Azure AD-ben
Ha engedélyezni szeretne egy kérelmet a Service Bus szolgáltatásegy felügyelt identitás az alkalmazásban, először konfigurálja a szerepköralapú hozzáférés-vezérlési (RBAC) beállításokat, hogy a felügyelt identitás. Az Azure Service Bus olyan RBAC-szerepköröket határoz meg, amelyek a Service Busból történő küldéshez és olvasáshoz szükséges engedélyeket foglalják magukban. Ha az RBAC szerepkör egy felügyelt identitáshoz van rendelve, a felügyelt identitás hozzáférést kap a Service Bus-entitásokhoz a megfelelő hatókörön.

Az RBAC-szerepkörök hozzárendeléséről a Hitelesítés és engedélyezés az Azure Active Directoryval című témakörben talál a [Service Bus-erőforrásokhoz való hozzáféréshez.](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus)

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>A Service Bus használata felügyelt identitásokkal az Azure-erőforrásokhoz
A Service Bus felügyelt identitásokkal való használatához hozzá kell rendelnie az identitást a szerepkörhöz és a megfelelő hatókörhöz. Az ebben a szakaszban szereplő eljárás egy egyszerű alkalmazást használ, amely felügyelt identitás alatt fut, és hozzáfér a Service Bus erőforrásaihoz.

Itt az [Azure App Service-ben](https://azure.microsoft.com/services/app-service/)üzemeltetett mintawebalkalmazást használjuk. A webalkalmazások létrehozásának lépésenkénti útmutatója: [ASP.NET Core webalkalmazás létrehozása az Azure-ban](../app-service/app-service-web-get-started-dotnet.md)

Az alkalmazás létrehozása után kövesse az alábbi lépéseket: 

1. Nyissa meg a **Beállítások lapot,** és válassza **az Identitás**lehetőséget. 
1. Válassza ki a bekapcsolt **állapotot.** **Status** 
1. A beállítás mentéséhez kattintson a **Mentés** gombra. 

    ![Felügyelt identitás egy webalkalmazáshoz](./media/service-bus-managed-service-identity/identity-web-app.png)

Miután engedélyezte ezt a beállítást, egy új szolgáltatásidentitás jön létre az Azure Active Directoryban (Azure AD), és az App Service-gazdagépkonfigurálása.

Most rendelje hozzá ezt a szolgáltatásidentitást a Service Bus-erőforrások szükséges hatókörében lévő szerepkörhöz.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>RBAC-szerepkörök hozzárendelése az Azure Portalhasználatával
Ha szerepkört szeretne hozzárendelni egy Service Bus-névtérhez, keresse meg a névteret az Azure Portalon. Jelenítse meg az erőforrás hozzáférés-vezérlési (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> A következő lépések egy szolgáltatásidentitás-szerepkört rendelnek a Service Bus-névterekhez. Ugyanezeket a lépéseket hajthatja végre a szerepkör hozzárendeléséhez más támogatott hatókörökhöz (erőforráscsoport hoz és előfizetéshez). 
> 
> [Hozzon létre egy Service Bus Messaging névteret,](service-bus-create-namespace-portal.md) ha nem rendelkezik ilyensel. 

1. Az Azure Portalon keresse meg a Service Bus-névteret, és **jelenítse meg** a névtér áttekintése. 
1. A service bus névtér hozzáférés-vezérlési beállításainak megjelenítéséhez válassza a bal oldali menü **hozzáférés-vezérlési (IAM)** parancsát.
1.  A **szerepkör-hozzárendelések** listájának megtekintéséhez válassza a Szerepkör-hozzárendelések lapot.
3.  Új szerepkör hozzáadásához válassza a **Hozzáadás** lehetőséget.
4.  A **Szerepkör-hozzárendelés hozzáadása** lapon válassza ki a hozzárendelni kívánt Azure Service Bus-szerepköröket. Ezután keresse meg a szerepkör hozzárendeléséhez regisztrált szolgáltatásidentitást.
    
    ![Szerepkör-hozzárendelés hozzáadása lap](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Kattintson a **Mentés** gombra. A szerepkörhöz rendelt identitás megjelenik a szerepkör alatt. Az alábbi képen például látható, hogy a szolgáltatás identitása rendelkezik az Azure Service Bus Data tulajdonosa.
    
    ![Szerepkörhöz rendelt identitás](./media/service-bus-managed-service-identity/role-assigned.png)

Miután hozzárendelte a szerepkört, a webalkalmazás hozzáférhet a Service Bus entitások a megadott hatókör alatt. 

### <a name="run-the-app"></a>Az alkalmazás futtatása

Most módosítsa a létrehozott ASP.NET alkalmazás alapértelmezett lapját. Használhatja a webalkalmazás kódját ebből a [GitHub-tárházból.](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)  

A Default.aspx oldal a céloldal. A kód megtalálható a Default.aspx.cs fájlban. Az eredmény egy minimális webalkalmazás néhány beviteli mezők, és **a küldési** és **fogadási** gombok, amelyek kapcsolódnak a Service Bus üzenetek küldéséhez vagy fogadásához.

Figyelje meg, hogyan inicializálódik a [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum. A megosztott hozzáférési jogkivonat (SAS) jogkivonat-szolgáltató használata helyett a kód `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` létrehoz egy jogkivonat-szolgáltatót a felügyelt identitáshoz a hívással. Mint ilyen, nincsenek titkok megőrzése és használata. A felügyelt identitás környezetben a Service Bus és az engedélyezési kézfogás automatikusan kezeli a jogkivonat-szolgáltató. Ez egy egyszerűbb modell, mint a SAS használata.

A módosítások elvégzése után tegye közzé és futtassa az alkalmazást. A megfelelő közzétételi adatokat egyszerűen beszerezheti, ha letölt egy közzétételi profilt a Visual Studio alkalmazásban:

![Közzétételi profil beszereznie](./media/service-bus-managed-service-identity/msi3.png)
 
Üzenetek küldéséhez és fogadásához adja meg a névtér nevét és a létrehozott entitás nevét. Ezután kattintson a **Küldés** vagy **fogadás gombra.**


> [!NOTE]
> - A felügyelt identitás csak az Azure-környezetben, az alkalmazásszolgáltatásokon, az Azure-beli virtuális gépeken és a méretezési csoportokon belül működik. A .NET-alkalmazások esetében a Service Bus NuGet csomag által használt Microsoft.Azure.Services.AppAuthentication könyvtár absztrakciót biztosít ezen a protokollon keresztül, és támogatja a helyi fejlesztési élményt. Ez a könyvtár azt is lehetővé teszi, hogy tesztelje a kódot helyileg a fejlesztői gépen, a Visual Studio, az Azure CLI 2.0 vagy az Active Directory integrált hitelesítés felhasználói fiókjának használatával. A tár helyi fejlesztési lehetőségeiről a [Szolgáltatás-szolgáltatás hitelesítés az Azure Key Vault számára a .NET használatával című](../key-vault/general/service-to-service-authentication.md)témakörben található.  
> 
> - A felügyelt identitások jelenleg nem működnek az App Service központi telepítési helyekkel.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
