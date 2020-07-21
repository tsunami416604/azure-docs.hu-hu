---
title: Alkalmazás hitelesítése az Azure Event Hubs-erőforrások eléréséhez
description: Ez a cikk a Azure Active Directory Azure Event Hubs-erőforrások eléréséhez való hitelesítésével kapcsolatos információkat tartalmaz.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 8cf51daef7298ff0ad487d5a072b6126557578c1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537292"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Alkalmazás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez
A Microsoft Azure Azure Active Directory (Azure AD) alapján integrált hozzáférés-vezérlési felügyeletet biztosít az erőforrásokhoz és alkalmazásokhoz. Az Azure AD és az Azure Event Hubs használatának egyik legfőbb előnye, hogy a hitelesítő adatait többé nem kell a kódban tárolnia. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformon. A jogkivonatot kérő erőforrás neve `https://eventhubs.azure.net/` (a Kafka-ügyfelek esetében a jogkivonat igénylésére szolgáló erőforrás `https://<namespace>.servicebus.windows.net` ). Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatás) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD egy hozzáférési jogkivonatot ad vissza az alkalmazásnak, és az alkalmazás a hozzáférési token használatával engedélyezheti az Azure Event Hubs-erőforrásokra vonatkozó kéréseket.

Ha egy szerepkört egy Azure AD-rendszerbiztonsági tag rendel hozzá, az Azure hozzáférést biztosít ezen erőforrásokhoz az adott rendszerbiztonsági tag számára. A hozzáférés hatóköre az előfizetés, az erőforráscsoport, a Event Hubs névtér vagy az alatta lévő erőforrás szintjére is kiterjed. Az Azure AD-biztonság szerepköröket rendelhet egy felhasználóhoz, egy csoporthoz, egy egyszerű alkalmazáshoz vagy egy [felügyelt identitáshoz az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> A szerepkör-definíció engedélyek gyűjteménye. A szerepköralapú hozzáférés-vezérlés (RBAC) szabályozza, hogy ezek az engedélyek hogyan legyenek kikényszerítve a szerepkör-hozzárendeléssel. A szerepkör-hozzárendelés három elemből áll: rendszerbiztonsági tagból, szerepkör-definícióból és hatókörből. További információ: [a különböző szerepkörök megismerése](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Az Azure Event Hubs beépített szerepkörei
Az Azure a következő beépített RBAC-szerepköröket biztosítja az Azure AD-vel és OAuth-vel Event Hubs való hozzáférés engedélyezéséhez:

- [Azure Event Hubs adattulajdonos](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): ezzel a szerepkörrel teljes hozzáférést biztosíthat Event Hubs erőforrásokhoz.
- [Azure Event Hubs adatfeladó](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): ezt a szerepkört használhatja a Event Hubs erőforrásokhoz való hozzáférés elküldéséhez.
- [Azure Event Hubs adatfogadó](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): használja ezt a szerepkört, hogy hozzáférést kapjon Event Hubs erőforrásaihoz.   

> [!IMPORTANT]
> Előzetes kiadásunk a tulajdonosi vagy közreműködői szerepkörhöz Event Hubs adathozzáférési jogosultságok hozzáadását támogatja. Azonban a tulajdonosi és a közreműködő szerepkörre vonatkozó adathozzáférési jogosultságok már nem teljesülnek. Ha a tulajdonos vagy közreműködő szerepkört használja, váltson át az Azure Event Hubs adat-tulajdonosi szerepkör használatára.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>RBAC szerepkörök kiosztása a Azure Portal használatával  
Ha többet szeretne megtudni az Azure-erőforrásokhoz való hozzáférés kezeléséről a RBAC és a Azure Portal használatával, tekintse meg [ezt a cikket](..//role-based-access-control/role-assignments-portal.md). 

Miután meghatározta a szerepkör-hozzárendelés megfelelő hatókörét, navigáljon az adott erőforráshoz a Azure Portal. Jelenítse meg az erőforrás hozzáférés-vezérlési (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> Az alábbi lépések az Event Hubs névterek alatt hozzárendelnek egy szerepkört az Event hub-hoz, de ugyanezen lépések végrehajtásával hozzárendelhet egy szerepkört egy Event Hubs erőforráshoz.

1. A [Azure Portal](https://portal.azure.com/)navigáljon a Event Hubs-névtérhez.
2. Az **Áttekintés** lapon válassza ki azt az Event hubot, amelyhez szerepkört szeretne hozzárendelni.

    ![Válassza ki az Event hub-t](./media/authenticate-application/select-event-hub.png)
1. Válassza a **Access Control (iam)** lehetőséget az Event hub hozzáférés-vezérlési beállításainak megjelenítéséhez. 
1. Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez. Kattintson a **Hozzáadás** gombra az eszköztáron, majd válassza a **szerepkör-hozzárendelés hozzáadása**elemet. 

    ![Hozzáadás gomb az eszköztáron](./media/authenticate-application/role-assignments-add-button.png)
1. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki a hozzárendelni kívánt **Event Hubs szerepkört** . 
    1. Keresse meg a **rendszerbiztonsági tag** (felhasználó, csoport, egyszerű szolgáltatásnév) megkeresését, amelyhez hozzá szeretné rendelni a szerepkört.
    1. A szerepkör-hozzárendelés mentéséhez válassza a **Mentés** lehetőséget. 

        ![Szerepkör társítása egy felhasználóhoz](./media/authenticate-application/assign-role-to-user.png)
    4. Az az identitás, akihez a szerepkört hozzárendelte, megjelenik az adott szerepkör alatt. Az alábbi képen például látható, hogy az Azure-felhasználók az Azure Event Hubs adattulajdonosi szerepkörben vannak. 
        
        ![A listában szereplő felhasználó](./media/authenticate-application/user-in-list.png)

A hasonló lépéseket követve rendelhet hozzá egy szerepkörhöz hatókört Event Hubs névtérhez, erőforráscsoporthoz vagy előfizetéshez. Miután definiálta a szerepkört és a hatókörét, tesztelheti ezt a [GitHub-helyen található](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)mintákkal.


## <a name="authenticate-from-an-application"></a>Hitelesítés egy alkalmazásból
Az Azure AD és a Event Hubs használatának egyik legfőbb előnye, hogy a hitelesítő adatait már nem kell a kódban tárolni. Ehelyett OAuth 2,0 hozzáférési tokent igényelhet a Microsoft Identity platformról. Az Azure AD az alkalmazást futtató rendszerbiztonsági tag (egy felhasználó, egy csoport vagy egy egyszerű szolgáltatásnév) hitelesítését végzi. Ha a hitelesítés sikeres, az Azure AD visszaadja a hozzáférési jogkivonatot az alkalmazásnak, és az alkalmazás a hozzáférési jogkivonatot használva engedélyezheti a kérelmeket az Azure Event Hubs számára.

A következő részben bemutatjuk, hogyan konfigurálhatja a natív alkalmazást vagy webalkalmazást a Microsoft Identity platform 2,0-alapú hitelesítéshez. A Microsoft Identity platform 2,0-es verziójával kapcsolatos további információkért lásd: [Microsoft Identity platform (v 2.0) – áttekintés](../active-directory/develop/v2-overview.md).

A OAuth 2,0 kód engedélyezési folyamatának áttekintését lásd: [hozzáférés engedélyezése Azure Active Directory webalkalmazásokhoz a OAuth 2,0 Code Grant flow használatával](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Alkalmazás regisztrálása Azure AD-Bérlővel
Az Azure AD Event Hubs erőforrások engedélyezésére való használatának első lépéseként regisztrálnia kell az ügyfélalkalmazás egy Azure AD-Bérlővel a [Azure Portal](https://portal.azure.com/). Az ügyfélalkalmazás regisztrálása után az alkalmazással kapcsolatos információkat adhat meg az AD szolgáltatáshoz. Az Azure AD egy ügyfél-azonosítót (más néven alkalmazás-azonosítót) biztosít, amellyel társíthatja az alkalmazást az Azure AD Runtime szolgáltatással. Az ügyfél-AZONOSÍTÓval kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md). 

A következő képek a webalkalmazások regisztrálásának lépéseit mutatják be:

![Alkalmazás regisztrálása](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Ha natív alkalmazásként regisztrálja az alkalmazást, megadhat bármely érvényes URI-t az átirányítási URI-hoz. Natív alkalmazások esetén ennek az értéknek nem kell valódi URL-címnek lennie. Webalkalmazások esetén az átirányítási URI azonosítónak érvényes URI-nak kell lennie, mert meghatározza azt az URL-címet, amelyhez a tokenek meg vannak határozva.

Az alkalmazás regisztrálását követően megjelenik az **alkalmazás (ügyfél) azonosítója** a **Beállítások**területen:

![A regisztrált alkalmazás alkalmazás-azonosítója](./media/authenticate-application/application-id.png)

Az alkalmazások Azure AD-vel való regisztrálásával kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Ügyfél titkos kulcsának létrehozása   
Az alkalmazásnak szüksége van egy ügyfél titkos kulcsára, hogy igazolja az identitását a jogkivonat kérésekor. Az ügyfél titkos kulcsának hozzáadásához kövesse az alábbi lépéseket.

1. Navigáljon az alkalmazás regisztrálásához a Azure Portal.
1. Válassza ki a **tanúsítványok & titkok** beállítást.
1. Az **ügyfél**titkos kulcsa területen válassza az **új** titkos kulcs lehetőséget egy új titok létrehozásához.
1. Adja meg a titkos kulcs leírását, és válassza ki a kívánt lejárati időközt.
1. Az új titok értékének azonnali másolása biztonságos helyre. A kitöltési érték csak egyszer jelenik meg.

    ![Titkos ügyfélkulcs](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Az ügyfél kódtárai a tokenek beszerzéséhez  
Miután regisztrálta az alkalmazást, és engedélyt kapott az Azure Event Hubsban tárolt adatai küldésére/fogadására, hozzáadhat programkódot az alkalmazáshoz egy rendszerbiztonsági tag hitelesítéséhez és a OAuth 2,0 token beszerzéséhez. A jogkivonat hitelesítéséhez és beszerzéséhez használhatja a [Microsoft Identity platform hitelesítési kódtárainak](../active-directory/develop/reference-v2-libraries.md) egyikét vagy egy olyan nyílt forráskódú függvénytárat, amely támogatja az OpenID vagy a Connect 1,0-et. Az alkalmazás ezután a hozzáférési token használatával engedélyezheti a kérést az Azure Event Hubs.

A jogkivonatok beszerzését támogató forgatókönyvek listáját a [Microsoft Authentication Library (MSAL) a .net GitHub-](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) adattárhoz [című szakaszában találja](https://aka.ms/msal-net-scenarios) .

## <a name="samples"></a>Példák
- [Microsoft. Azure. EventHubs-minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Ezek a minták a régi **Microsoft. Azure. EventHubs** könyvtárat használják, de egyszerűen frissítheti a legújabb **Azure. Messaging. EventHubs** könyvtár használatával. Ha át szeretné helyezni a mintát a régi könyvtárból az új verzióra, tekintse meg a [Microsoft. Azure. EventHubs-ről az Azure. Messaging. EventHubs-re való Migrálás útmutatóját](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Azure. Messaging. EventHubs-minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Ez a minta frissítve lett a legújabb **Azure. Messaging. EventHubs** könyvtár használatára.

## <a name="next-steps"></a>Következő lépések
- További információ a RBAC: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)?
- A következő cikkekből megtudhatja, hogyan rendelhet hozzá és kezelhet RBAC szerepkör-hozzárendeléseket Azure PowerShell, az Azure CLI vagy a REST API használatával:
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure CLI-vel](../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése a REST API](../role-based-access-control/role-assignments-rest.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése Azure Resource Manager-sablonokkal](../role-based-access-control/role-assignments-template.md)

Tekintse meg a következő kapcsolódó cikkeket:
- [Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md)
- [Kérelmek hitelesítése az Azure Event Hubs megosztott hozzáférési aláírások használatával](authenticate-shared-access-signature.md)
- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](authorize-access-azure-active-directory.md)
- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)
