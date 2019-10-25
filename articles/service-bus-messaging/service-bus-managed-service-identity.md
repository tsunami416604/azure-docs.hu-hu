---
title: Felügyelt identitások az Azure-erőforrásokhoz Service Bus
description: Felügyelt identitások használata Azure-erőforrásokhoz Azure Service Bus
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
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 57c52640262854037420c1679804f611394230ef
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793156"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Felügyelt identitás hitelesítése Azure Active Directory használatával Azure Service Bus erőforrások eléréséhez
Az [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) egy Azure-beli szolgáltatás, amely lehetővé teszi, hogy az alkalmazás kódjának futtatásához használt központi telepítéshez tartozó biztonságos identitást hozzon létre. Ezután társíthatja az identitást hozzáférés-vezérlési szerepkörökkel, amelyek egyéni engedélyeket biztosítanak az alkalmazás által igényelt egyes Azure-erőforrások eléréséhez.

A felügyelt identitások esetében az Azure platform kezeli ezt a futásidejű identitást. Az alkalmazás kódjában vagy konfigurációjában nem kell tárolnia és védelemmel ellátnia a hozzáférési kulcsokat, vagy magát az identitást, vagy a hozzáféréshez szükséges erőforrásokat. Egy Azure App Service alkalmazáson belül vagy az Azure-erőforrások támogatását engedélyező felügyelt entitásokkal rendelkező virtuális gépen futó Service Bus ügyfélalkalmazás nem szükséges az SAS-szabályok és-kulcsok, illetve más hozzáférési jogkivonatok kezeléséhez. Az ügyfélalkalmazás csak az Service Bus üzenetküldési névtér végpontjának megfelelőre van szüksége. Amikor az alkalmazás csatlakozik, a Service Bus a cikk későbbi részében ismertetett művelettel köti össze a felügyelt entitás környezetét az ügyfélhez. Ha egy felügyelt identitáshoz van társítva, az Service Bus-ügyfél elvégezheti az összes jóváhagyott műveletet. Az engedélyezést egy felügyelt entitás Service Bus szerepkörökkel való társításával biztosítjuk. 

## <a name="overview"></a>Áttekintés
Ha egy rendszerbiztonsági tag (felhasználó, csoport vagy alkalmazás) egy Service Bus entitáshoz próbál hozzáférni, a kérést engedélyezni kell. Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat. 

 1. Először a rendszerbiztonsági tag identitása hitelesítve van, és a rendszer egy OAuth 2,0 tokent ad vissza. A jogkivonatot kérő erőforrás neve `https://servicebus.azure.net`.
 1. Ezután a jogkivonat a Service Bus szolgáltatásnak küldött kérelem részeként a megadott erőforráshoz való hozzáférés engedélyezéséhez lesz átadva.

A hitelesítési lépés megköveteli, hogy egy alkalmazás-kérelem OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben. Ha egy alkalmazás egy Azure-entitáson, például egy Azure-beli virtuális gépen, egy virtuálisgép-méretezési csoporton vagy egy Azure Function-alkalmazáson belül fut, akkor a felügyelt identitás használatával férhet hozzá az erőforrásokhoz. 

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. A Azure Service Bus olyan RBAC-szerepköröket biztosít, amelyek magukban foglalják Service Bus erőforrásokra vonatkozó engedélyek készleteit. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg, hogy a résztvevő milyen engedélyeket fog tartalmazni. Ha többet szeretne megtudni a RBAC szerepköreinek Azure Service Bushoz való hozzárendeléséről, tekintse meg a [Azure Service Bus beépített RBAC-szerepkörei](#built-in-rbac-roles-for-azure-service-bus)című témakört. 

A Service Busra irányuló kérelmeket használó natív alkalmazások és webalkalmazások is engedélyezhetik az Azure AD-t. Ez a cikk bemutatja, hogyan kérhet hozzáférési jogkivonatot, és hogyan engedélyezheti a kérelmeket Service Bus erőforrásokhoz. 


## <a name="assigning-rbac-roles-for-access-rights"></a>RBAC-szerepkörök kiosztása hozzáférési jogosultságokhoz
Azure Active Directory (Azure AD) a [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Azure Service Bus olyan beépített RBAC-szerepköröket határoz meg, amelyek a Service Bus entitások eléréséhez használt engedélyek közös készleteit foglalják magukban, és egyéni szerepköröket is meghatározhat az adatokhoz való hozzáféréshez.

Ha egy Azure AD-rendszerbiztonsági tag egy RBAC-szerepkört rendel hozzá, az Azure hozzáférést biztosít ezen rendszerbiztonsági tag erőforrásaihoz. A hozzáférés az előfizetés, az erőforráscsoport vagy a Service Bus névtér szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy felügyelt identitás az Azure-erőforrásokhoz.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>A Azure Service Bus beépített RBAC szerepkörei
Azure Service Bus esetében a névterek és az összes kapcsolódó erőforrás a Azure Portal és az Azure Resource Management API segítségével való kezelése már védett a *szerepköralapú hozzáférés-vezérlési* (RBAC) modell használatával. Az Azure az alábbi beépített RBAC szerepköröket biztosítja a Service Bus névtérhez való hozzáférés engedélyezéséhez:

- [Azure Service Bus adattulajdonos](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): lehetővé teszi az adathozzáférést Service Bus névterek és az entitások számára (várólisták, témakörök, előfizetések és szűrők)
- [Azure Service Bus adatfeladó](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): ezt a szerepkört használva hozzáférést biztosíthat a Service Bus névtérhez és az entitásokhoz.
- [Azure Service Bus adatfogadó](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): használja ezt a szerepkört, hogy fogadja a hozzáférést Service Bus névtérhez és az entitásokhoz. 

## <a name="resource-scope"></a>Erőforrás hatóköre 
Mielőtt RBAC-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférésének hatókörét. Az ajánlott eljárások azt diktálják, hogy mindig csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyekkel a Service Bus erőforrásaihoz férhet hozzá, a legszűkebb hatókörtől kezdve:

- **Üzenetsor**, **témakör**vagy **előfizetés**: a szerepkör-hozzárendelés az adott Service Bus entitásra vonatkozik. Jelenleg a Azure Portal nem támogatja a felhasználók/csoportok/felügyelt identitások hozzárendelését az előfizetési szinten lévő Service Bus RBAC-szerepkörökhöz. Íme egy példa az Azure CLI-parancs használatára: [az-role-hozzárendelés-Create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) , hogy identitást rendeljen egy Service Bus RBAC szerepkörhöz: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus névtér**: a szerepkör-hozzárendelés a névtér és a hozzá társított fogyasztói csoport teljes Service Bus topológiáját fedi le.
- **Erőforráscsoport**: a szerepkör-hozzárendelés az erőforráscsoport összes Service Bus erőforrására vonatkozik.
- **Előfizetés**: a szerepkör-hozzárendelés az előfizetés összes erőforrás-csoportjának összes Service Bus erőforrására vonatkozik.

> [!NOTE]
> Ne feledje, hogy a RBAC szerepkör-hozzárendelések akár öt percet is igénybe vehetnek. 

További információ a beépített szerepkörök meghatározásáról: a [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md#management-and-data-operations). Az egyéni RBAC-szerepkörök létrehozásával kapcsolatos információkért tekintse meg [Az Azure szerepköralapú Access Control egyéni szerepköreinek létrehozása](../role-based-access-control/custom-roles.md)című témakört.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése egy virtuális gépen
Mielőtt felügyelt identitásokat használ az Azure-erőforrásokhoz a virtuális gép Service Bus erőforrásainak engedélyezéséhez, először engedélyeznie kell az Azure-erőforrások felügyelt identitásait a virtuális gépen. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portalra](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Engedélyek megadása felügyelt identitásnak az Azure AD-ben
Ha az alkalmazásban felügyelt identitással kívánja engedélyezni a Service Bus szolgáltatást, először konfigurálja a felügyelt identitáshoz tartozó szerepköralapú hozzáférés-vezérlés (RBAC) beállításait. Azure Service Bus meghatározza azokat a RBAC-szerepköröket, amelyek a Service Bus küldésére és olvasására vonatkozó engedélyeket foglalnak magukban. Ha a RBAC szerepkör felügyelt identitáshoz van rendelve, a felügyelt identitás hozzáférést kap a Service Bus entitásokhoz a megfelelő hatókörben.

A RBAC szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [hitelesítés és engedélyezés Azure Active Directory Service Bus erőforrásokhoz való hozzáféréshez](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Service Bus használata felügyelt identitásokkal az Azure-erőforrásokhoz
Ha a Service Bus felügyelt identitásokkal szeretné használni, hozzá kell rendelnie a szerepkört és a megfelelő hatókört. Az ebben a szakaszban szereplő eljárás egy olyan egyszerű alkalmazást használ, amely felügyelt identitás alatt fut, és Service Bus erőforrásokhoz fér hozzá.

Itt egy [Azure app Service](https://azure.microsoft.com/services/app-service/)-ban üzemeltetett minta webalkalmazást használunk. A webalkalmazások létrehozásával kapcsolatos részletes útmutatásért lásd: [ASP.net Core Webalkalmazás létrehozása az Azure-ban](../app-service/app-service-web-get-started-dotnet.md)

Az alkalmazás létrehozása után kövesse az alábbi lépéseket: 

1. Lépjen a **Beállítások** menüpontra, és válassza az **identitás**elemet. 
1. Válassza ki a **bekapcsolni**kívánt **állapotot** . 
1. A beállítás mentéséhez kattintson a **Mentés** gombra. 

    ![Webalkalmazás felügyelt identitása](./media/service-bus-managed-service-identity/identity-web-app.png)

Ha engedélyezte ezt a beállítást, a rendszer létrehoz egy új szolgáltatás-identitást a Azure Active Directory (Azure AD), és az App Service gazdagépre van konfigurálva.

Most rendeljen hozzá egy szerepkörhöz a szolgáltatás identitását a Service Bus erőforrásaihoz szükséges hatókörben.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>RBAC szerepkörök kiosztása a Azure Portal használatával
Ha egy szerepkört Service Bus névtérhez szeretne rendelni, akkor navigáljon a Azure Portal névtérhez. Jelenítse meg az erőforrás Access Control (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> A következő lépések szolgáltatás-identitási szerepkört rendelnek a Service Bus névterekhez. Ugyanezeket a lépéseket követve rendelhet hozzá szerepköröket más támogatott hatókörökhöz (erőforráscsoport és előfizetés). 
> 
> Ha nem rendelkezik ilyennel, [hozzon létre egy Service Bus üzenetküldési névteret](service-bus-create-namespace-portal.md) . 

1. A Azure Portal navigáljon a Service Bus névtérhez, és jelenítse meg a névtér **áttekintését** . 
1. A bal oldali menüben válassza a **Access Control (iam)** lehetőséget a Service Bus névtér hozzáférés-vezérlési beállításainak megjelenítéséhez.
1.  Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez.
3.  Új szerepkör hozzáadásához válassza a **Hozzáadás** lehetőséget.
4.  A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a hozzárendelni kívánt Azure Service Bus-szerepköröket. Ezután keresse meg a szerepkör hozzárendeléséhez regisztrált szolgáltatás identitását.
    
    ![Szerepkör-hozzárendelési lap hozzáadása](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Kattintson a **Mentés** gombra. Az az identitás, akihez a szerepkört hozzárendelte, megjelenik az adott szerepkör alatt. Az alábbi képen például látható, hogy a szolgáltatás identitása Azure Service Bus adattulajdonost tartalmaz.
    
    ![Szerepkörhöz rendelt identitás](./media/service-bus-managed-service-identity/role-assigned.png)

Miután hozzárendelte a szerepkört, a webalkalmazás hozzáfér a megadott hatókörben lévő Service Bus entitásokhoz. 

### <a name="run-the-app"></a>Az alkalmazás futtatása

Most módosítsa a létrehozott ASP.NET-alkalmazás alapértelmezett oldalát. A webalkalmazás kódját a [GitHub-adattárból](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)is használhatja.  

Az alapértelmezett. aspx lap a kezdőlapja. A kód a Default.aspx.cs fájlban található. Az eredmény egy minimális webalkalmazás, amely néhány beviteli mezővel rendelkezik, valamint a **küldési** és **fogadási** gombokkal, amelyek a Service Bushoz csatlakoznak a küldési és fogadási üzenetekhez.

Figyelje meg, hogy a [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum inicializálva van. A megosztott hozzáférési jogkivonat (SAS) jogkivonat-szolgáltató használata helyett a kód létrehoz egy jogkivonat-szolgáltatót a felügyelt identitáshoz a `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` hívással. Így nem kell megőrizni és használni a titkokat. A felügyelt identitás környezetének Service Busre való áramlását, valamint az engedélyezési kézfogást a jogkivonat-szolgáltató automatikusan kezeli. Ez egy egyszerűbb modell, mint az SAS használata.

A módosítások elvégzése után tegye közzé és futtassa az alkalmazást. A Visual Studióban a közzétételi profil letöltésével és importálásával egyszerűen hozzájuthat a megfelelő közzétételi adatokhoz:

![Közzétételi profil beolvasása](./media/service-bus-managed-service-identity/msi3.png)
 
Üzenetek küldéséhez vagy fogadásához adja meg a névtér nevét és a létrehozott entitás nevét. Ezután kattintson a **Küldés** vagy a **fogadás**lehetőségre.


> [!NOTE]
> - A felügyelt identitás csak az Azure-környezetben, az App Servicesben, az Azure-beli virtuális gépeken és a méretezési csoportokon belül működik. A .NET-alkalmazások esetében a Service Bus NuGet-csomag által használt Microsoft. Azure. Services. AppAuthentication függvénytár egy absztrakciót biztosít a protokollhoz, és támogatja a helyi fejlesztési élményt. Ez a könyvtár lehetővé teszi a kód helyi tesztelését a fejlesztői gépen a Visual studióból, az Azure CLI 2,0-ből vagy Active Directory integrált hitelesítésből származó felhasználói fiók használatával. A könyvtár helyi fejlesztési lehetőségeivel kapcsolatos további információkért lásd: [szolgáltatások közötti hitelesítés Azure Key Vault .NET használatával](../key-vault/service-to-service-authentication.md).  
> 
> - A felügyelt identitások jelenleg nem működnek App Service üzembe helyezési pontokkal.

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
