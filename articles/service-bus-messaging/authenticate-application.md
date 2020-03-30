---
title: Az Azure Service Bus-entitások eléréséhez szükséges alkalmazás hitelesítése
description: Ez a cikk az Azure Service Bus-entitások (várólisták, témakörök stb.) azure Service Bus-entitások (várólisták, témakörök stb.) eléréséhez az Azure Active Directoryval történő hitelesítéséről nyújt tájékoztatást.
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259291"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Az Azure Active Directoryval egy alkalmazás hitelesítése és engedélyezése az Azure Service Bus-entitások eléréséhez
Az Azure Service Bus támogatja az Azure Active Directory (Azure AD) használatát a Service Bus-entitások (várólisták, témakörök, előfizetések vagy szűrők) kéréseinek engedélyezéséhez. Az Azure AD használatával szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket adhat egy rendszerbiztonsági tagnak, amely lehet egy felhasználó, csoport vagy egyszerű alkalmazásszolgáltatás. Ha többet szeretne megtudni a szerepkörökről és a szerepkör-hozzárendelésekről, olvassa [el a Különböző szerepkörök ismertetése című témakört.](../role-based-access-control/overview.md)

## <a name="overview"></a>Áttekintés
Amikor egy rendszerbiztonsági tag (egy felhasználó, csoport vagy alkalmazás) megpróbál hozzáférni egy Service Bus entitáshoz, a kérelmet engedélyezni kell. Az Azure AD-vel az erőforráshoz való hozzáférés két lépésből áll. 

 1. Először a rendszerbiztonsági tag identitása hitelesítve van, és egy OAuth 2.0-s jogkivonatot ad vissza. A jogkivonat ot kérő `https://servicebus.azure.net`erőforrás neve a.
 1. Ezután a jogkivonat a Service Bus szolgáltatásnak a megadott erőforráshoz való hozzáférés engedélyezésére irányuló kérelem részeként kerül átadásra.

A hitelesítési lépés megköveteli, hogy egy alkalmazáskérelem tartalmazzon egy OAuth 2.0 hozzáférési jogkivonatot futásidőben. Ha egy alkalmazás fut egy Azure-entitás, például egy Azure virtuális gép, egy virtuális gép méretezési csoport, vagy egy Azure Function-alkalmazás, egy felügyelt identitás használatával hozzáférhet az erőforrásokhoz. A felügyelt identitás által a Service Bus szolgáltatásba felhasználó felügyelt identitás által benyújtott kérelmek [hitelesítése: Hozzáférés hitelesítése az Azure Service Bus-erőforrásokhoz az Azure Active Directoryval és az Azure Resources felügyelt identitásaival.](service-bus-managed-service-identity.md) 

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC szerepkörök legyenek hozzárendelve a rendszerbiztonsági taghoz. Az Azure Service Bus rbac-szerepköröket biztosít, amelyek a Service Bus-erőforrások engedélykészleteit foglalják magukban. A rendszerbiztonsági taghoz rendelt szerepkörök határozzák meg a rendszerbiztonsági tag engedélyeit. Ha többet szeretne tudni az RBAC-szerepkörök Azure Service Bushoz való hozzárendeléséről, olvassa [el a Beépített RBAC-szerepkörök az Azure Service Bushoz című témakört.](#built-in-rbac-roles-for-azure-service-bus) 

Natív alkalmazások és webalkalmazások, amelyek kéréseket a Service Bus is engedélyezheti az Azure AD.Native applications and web applications that make requests to Service Bus can also authorize with Azure AD. Ez a cikk bemutatja, hogyan kérhet egy hozzáférési jogkivonatot, és használja a Service Bus-erőforrások ra vonatkozó kérelmek engedélyezéséhez. 


## <a name="assigning-rbac-roles-for-access-rights"></a>RBAC-szerepkörök hozzárendelése hozzáférési jogokhoz
Az Azure Active Directory (Azure AD) [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)révén engedélyezi a védett erőforrásokhoz való hozzáférési jogokat. Az Azure Service Bus egy beépített RBAC-szerepkörkészletet határoz meg, amely a Service Bus-entitások eléréséhez használt közös engedélykészleteket foglalja magában, és egyéni szerepköröket is definiálhat az adatok eléréséhez.

Ha egy RBAC-szerepkör egy Azure AD rendszerbiztonsági taghoz van rendelve, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz az adott rendszerbiztonsági tag számára. Az Access hatóköre az előfizetés, az erőforráscsoport vagy a Service Bus névtér szintjére terjed ki. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrások.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Beépített RBAC-szerepkörök az Azure Service Bushoz
Az Azure Service Bus esetében a névterek és az összes kapcsolódó erőforrás kezelése az Azure Portalon és az Azure erőforrás-kezelési API-n keresztül már védett a *szerepköralapú hozzáférés-vezérlési* (RBAC) modell használatával. Az Azure az alábbi beépített RBAC-szerepköröket biztosítja a Service Bus-névtérhez való hozzáférés engedélyezéséhez:

- [Azure Service Bus-adatok tulajdonosa:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)Lehetővé teszi az adatok elérését a Service Bus névtér és annak entitásai (várólisták, témakörök, előfizetések és szűrők)
- [Azure Service Bus data sender:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)ezzel a szerepkörrel küldési hozzáférést biztosít a Service Bus névtér és az entitások.
- [Azure Service Bus-adatfogadó:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)Ezzel a szerepkörrel fogadási hozzáférést biztosít a Service Bus névtér és az entitások. 

## <a name="resource-scope"></a>Erőforrás hatóköre 
Mielőtt rbac szerepkört rendelne egy rendszerbiztonsági taghoz, határozza meg a rendszerbiztonsági tag hozzáférési tartományát. A legjobb gyakorlatok azt diktálják, hogy mindig a legjobb, ha csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyeken a legszűkebb hatókörtől kezdve a Service Bus-erőforrásokhoz való hozzáférést hatókörbe lehet érni:

- **Várólista**, **témakör**vagy **előfizetés:** A szerepkör-hozzárendelés az adott Service Bus entitásra vonatkozik. Jelenleg az Azure Portal nem támogatja a felhasználók/csoportok/felügyelt identitások hozzárendelését a Service Bus RBAC-szerepkörökhöz az előfizetés szintjén. 
- **Service Bus névtér:** Szerepkör-hozzárendelés a teljes topológia a Service Bus a névtér és a hozzá társított fogyasztói csoport.
- **Erőforráscsoport**: A szerepkör-hozzárendelés az erőforráscsoport összes Service Bus erőforrására vonatkozik.
- **Előfizetés:** A szerepkör-hozzárendelés az előfizetés összes erőforráscsoportjában lévő összes Service Bus-erőforrásra vonatkozik.

> [!NOTE]
> Ne feledje, hogy az RBAC szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet. 

A beépített szerepkörök definiálásáról a [Szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md#management-and-data-operations)című témakörben talál további információt. Az egyéni RBAC-szerepkörök létrehozásáról az [Egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérléshez című](../role-based-access-control/custom-roles.md)témakörben talál további információt.


## <a name="assign-rbac-roles-using-the-azure-portal"></a>RBAC-szerepkörök hozzárendelése az Azure Portal használatával  
Ha többet szeretne megtudni az Azure-erőforrásokhoz való hozzáférés RBAC és az Azure Portal használatával történő kezeléséről, olvassa el [ezt a cikket.](..//role-based-access-control/role-assignments-portal.md) 

Miután megállapította a szerepkör-hozzárendelés megfelelő hatókörét, keresse meg az adott erőforrást az Azure Portalon. Az erőforrás hozzáférés-vezérlési (IAM) beállításainak megjelenítése, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> Az alábbiakban ismertetett lépések szerepkört rendelnek a Service Bus névtérhez. Ugyanezeket a lépéseket követve szerepkört rendelhet más támogatott hatókörökhöz (erőforráscsoport, előfizetés stb.).

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg a Service Bus-névteret. A bal oldali menü hozzáférés-vezérlési beállításainak megjelenítéséhez válassza a bal oldali menü **Hozzáférés-vezérlési (IAM)** parancsát. Ha létre kell hoznia egy Service Bus névteret, kövesse az alábbi cikk ben található utasításokat: [Szolgáltatásbusz-üzenetek névtere létrehozása](service-bus-create-namespace-portal.md).

    ![Válassza a bal oldali menü Hozzáférés-vezérlés parancsát](./media/authenticate-application/select-access-control-menu.png)
1. A **szerepkör-hozzárendelések** listájának megtekintéséhez válassza a Szerepkör-hozzárendelések lapot. Jelölje ki az eszköztár **Hozzáadás** gombját, majd a **Szerepkör-hozzárendelés hozzáadása**lehetőséget. 

    ![A Hozzáadás gomb az eszköztáron](./media/authenticate-application/role-assignments-add-button.png)
1. A **Szerepkör-hozzárendelés hozzáadása** lapon tegye a következő lépéseket:
    1. Válassza ki a hozzárendelni kívánt **Service Bus szerepkört.** 
    1. Keresés a **rendszerbiztonsági tag** (felhasználó, csoport, egyszerű szolgáltatás) megkereséséhez, amelyhez a szerepkört hozzá kívánja rendelni.
    1. A **szerepkör-hozzárendelés** mentéséhez válassza a Mentés lehetőséget. 

        ![Szerepkör hozzárendelése felhasználóhoz](./media/authenticate-application/assign-role-to-user.png)
    4. A szerepkörhöz rendelt identitás megjelenik a szerepkör alatt. Az alábbi képen például látható, hogy az Azure-felhasználók az Azure Service Bus-adattulajdonosi szerepkörben vannak. 
        
        ![Felhasználó a listában](./media/authenticate-application/user-in-list.png)

Hasonló lépéseket követve rendeljen egy erőforráscsoporthoz vagy előfizetéshez hatókörrel ellátott szerepkört. Miután definiálja a szerepkört és hatókörét, tesztelheti ezt a viselkedést a [Mintákkal a GitHubon.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)


## <a name="authenticate-from-an-application"></a>Hitelesítés egy alkalmazásból
Az Azure AD és a Service Bus használatának egyik fő előnye, hogy a hitelesítő adatokat már nem kell tárolni a kódban. Ehelyett egy OAuth 2.0 hozzáférési jogkivonatot kérhet a Microsoft identity platformról. Az Azure AD hitelesíti az alkalmazást futtató rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatás). Ha a hitelesítés sikeres, az Azure AD visszaadja a hozzáférési jogkivonatot az alkalmazásnak, és az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Service Bus kérelmek engedélyezéséhez.

A következő szakaszok bemutatják, hogyan konfigurálhatja a natív alkalmazást vagy webalkalmazást a Microsoft identity platform 2.0-s hitelesítéséhez. A Microsoft identity platform 2.0-s platformjáról a [Microsoft identity platform (2.0-s verzió) – áttekintés című témakörben olvashat bővebben.](../active-directory/develop/v2-overview.md)

Az OAuth 2.0-s kódtámogatási folyamat áttekintését az [Azure Active Directory webalkalmazásokhoz való hozzáférés engedélyezése az OAuth 2.0-s kódtámogatási folyamat használatával című témakörben találja.](../active-directory/develop/v2-oauth2-auth-code-flow.md)

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Regisztrálja az alkalmazást egy Azure AD-bérlővel
Az első lépés az Azure AD szolgáltatásbusz-entitások engedélyezéséhez az ügyfélalkalmazás regisztrálása egy Azure AD-bérlővel az [Azure Portalról.](https://portal.azure.com/) Az ügyfélalkalmazás regisztrálásakor az alkalmazással kapcsolatos információkat adja meg az AD-nek. Az Azure AD ezután egy ügyfél-azonosítót (más néven egy alkalmazásazonosítót) biztosít, amely segítségével társíthatja az alkalmazást az Azure AD futásidejéhez. Az ügyfélazonosítóról az [Alkalmazás- és egyszerű szolgáltatásobjektumok az Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md)című témakörben olvashat bővebben. 

A következő képek a webalkalmazások regisztrálásának lépéseit mutatják be:

![Egy alkalmazás regisztrálása](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Ha natív alkalmazásként regisztrálja az alkalmazást, megadhatja az átirányítási URI bármely érvényes URI-ját. Natív alkalmazások esetén ennek az értéknek nem kell valódi URL-címnek lennie. Webalkalmazások esetén az átirányítási URI-nak érvényes URI-nak kell lennie, mert megadja azt az URL-címet, amelyhez a jogkivonatok rendelkezésre állnak.

Miután regisztrálta az alkalmazást, a Beállítások csoportban megjelenik az **alkalmazás (ügyfél)** **azonosítója:**

![A regisztrált kérelem kérelemazonosítója](./media/authenticate-application/application-id.png)

Az alkalmazások Azure AD-vel való regisztrálásáról az [Alkalmazások integrálása](../active-directory/develop/quickstart-v2-register-an-app.md)az Azure Active Directoryval című témakörben talál további információt.

> [!IMPORTANT]
> Jegyezze fel a **TenantId** és az **ApplicationId**. Az alkalmazás futtatásához ezekre az értékekre lesz szüksége.

### <a name="create-a-client-secret"></a>Ügyféltitok létrehozása   
Az alkalmazásnak szüksége van egy ügyféltitokra, hogy bizonyítsa a személyazonosságát, amikor jogkivonatot kér. Az ügyféltitok hozzáadásához kövesse az alábbi lépéseket.

1. Keresse meg az alkalmazás regisztrációját az Azure Portalon, ha még nem volt az oldalon.
1. A bal oldali menüben válassza a **Tanúsítványok & titkos kulcsok** at.
1. Az **Ügyféltitkok csoportban**válassza az **Új ügyféltitok** lehetőséget új titkos titok létrehozásához.

    ![Új ügyféltitok - gomb](./media/authenticate-application/new-client-secret-button.png)
1. Adja meg a titkos kulcsot, és válassza ki a kívánt lejárati időközt, majd válassza a **Hozzáadás**lehetőséget.

    ![Ügyféltitkos lap hozzáadása](./media/authenticate-application/add-client-secret-page.png)
1. Azonnal másolja az új titok értékét egy biztonságos helyre. A kitöltési érték csak egyszer jelenik meg.

    ![Titkos ügyfélkulcs](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>A Service Bus API engedélyei
Ha az alkalmazás egy konzolalkalmazás, regisztrálnia kell egy natív alkalmazást, és hozzá kell adnia a **Microsoft.ServiceBus** API-engedélyeit a **szükséges engedélyekhez.** A natív alkalmazásoknak is szükségük van egy **átirányítási-URI-ra** az Azure AD-ben, amely azonosítóként szolgál; az URI-nak nem kell hálózati célnak lennie. Használja `https://servicebus.microsoft.com` ezt a példát, mert a mintakód már használja ezt az URI-t.

### <a name="client-libraries-for-token-acquisition"></a>Ügyfélkódtárak tokenbeszerzéshez  
Miután regisztrálta az alkalmazást, és megadta az Azure Service Busban az adatok küldésére/fogadására vonatkozó engedélyeket, kódot adhat hozzá az alkalmazáshoz egy rendszerbiztonsági tag hitelesítéséhez és az OAuth 2.0-s jogkivonat beszerzéséhez. A jogkivonat hitelesítéséhez és beszerzéséhez használhatja a [Microsoft identitásplatform hitelesítési kódtárait](../active-directory/develop/reference-v2-libraries.md) vagy egy másik nyílt forráskódú könyvtárat, amely támogatja az OpenID vagy a Connect 1.0-t. Az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Service Bus elleni kérelem engedélyezéséhez.

Azokról az esetekről, amelyekesetében a tokenek beolvasása támogatott, tekintse meg a [Forgatókönyvek](https://aka.ms/msal-net-scenarios) szakasza a [Microsoft Authentication Library (MSAL) a .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub tárház.

## <a name="sample-on-github"></a>Minta a GitHubon
Tekintse meg a következő mintát a GitHubon: [Szerepkör-alap hozzáférés-vezérlés a Service Bus.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl) 

Az **Ügyféltitkos bejelentkezés** beállítást használja, ne az **Interaktív felhasználó bejelentkezési** beállítást. Az ügyféltitkos beállítás használatakor nem jelenik meg előugró ablak. Az alkalmazás a bérlői azonosítót és az alkalmazásazonosítót használja a hitelesítéshez. 

### <a name="run-the-sample"></a>Minta futtatása

A minta futtatása előtt szerkesztheti az **app.config** fájlt, és a forgatókönyvtől függően állítsa be a következő értékeket:

- `tenantId`: TenantId értékre van **állítva.**
- `clientId`: Állítsa **az ApplicationId** értékre.
- `clientSecret`: Ha az ügyféltitok használatával szeretne bejelentkezni, hozza létre az Azure AD-ben. Emellett használjon webalkalmazást vagy API-t natív alkalmazás helyett. Adja hozzá az alkalmazást a **hozzáférés-vezérlés (IAM)** csoportban a korábban létrehozott névtérben.
- `serviceBusNamespaceFQDN`: Állítsa be az újonnan létrehozott Service Bus névtér teljes DNS-nevét; például `example.servicebus.windows.net`.
- `queueName`: Állítsa be a létrehozott várólista nevét.
- Az alkalmazásban az előző lépésekben megadott átirányítási URI.The redirect URI you specified in your app in the previous steps.

A konzolalkalmazás futtatásakor a rendszer kéri, hogy válasszon ki egy forgatókönyvet. Válassza **az Interaktív felhasználói bejelentkezés** lehetőséget a szám beírásával és az ENTER billentyű lenyomásával. Az alkalmazás megjelenít egy bejelentkezési ablakot, a Beleegyezését kéri a Service Bus eléréséhez, majd a szolgáltatás segítségével futtassa végig a küldési/fogadási forgatókönyvet a bejelentkezési identitás használatával.


## <a name="next-steps"></a>További lépések
- Ha többet szeretne megtudni az RBAC-ról, olvassa el [a Mi a szerepköralapú hozzáférés-vezérlés (RBAC) című témakört?](../role-based-access-control/overview.md)
- Az RBAC-szerepkör-hozzárendelések Azure PowerShell, Az Azure CLI vagy a REST API használatával történő hozzárendelése és kezelése című témakörből megtudhatja:
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure PowerShell segítségével](../role-based-access-control/role-assignments-powershell.md)  
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure CLI-vel](../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése a REST API-val](../role-based-access-control/role-assignments-rest.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure Resource Manager-sablonokkal](../role-based-access-control/role-assignments-template.md)

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

- [Service Bus RBAC-minták](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
- [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
- [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
