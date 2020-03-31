---
title: Az Azure Event Hubs-erőforrások eléréséhez szükséges alkalmazás hitelesítése
description: Ez a cikk az Azure Event Hubs-erőforrások eléréséhez az Azure Active Directoryval való hitelesítéséről nyújt tájékoztatást
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: a242da8cc98a21248c48a1b3981fa713706028ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064942"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Alkalmazás hitelesítése az Azure Active Directoryval az Event Hubs-erőforrások eléréséhez
A Microsoft Azure integrált hozzáférés-vezérlési felügyeletet biztosít az Azure Active Directory (Azure AD) alapú erőforrások és alkalmazások számára. Az Azure AD azure-beli Event Hubs használatával való használatának egyik fő előnye, hogy már nem kell tárolnia a hitelesítő adatait a kódban. Ehelyett kérhet egy OAuth 2.0 hozzáférési jogkivonatot a Microsoft Identity platformról. A jogkivonat igényléséhez `https://eventhubs.azure.net/` szükséges erőforrás neve (A Kafka-ügyfelek `https://<namespace>.servicebus.windows.net`esetében a jogkivonatot kérő erőforrás a). Az Azure AD hitelesíti az alkalmazást futtató rendszerbiztonsági tag (egy felhasználó, csoport vagy egyszerű szolgáltatás). Ha a hitelesítés sikeres, az Azure AD egy hozzáférési jogkivonatot ad vissza az alkalmazásnak, és az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Event Hubs-erőforrások kérésének engedélyezéséhez.

Ha egy szerepkör egy Azure AD rendszerbiztonsági taghoz van rendelve, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz az adott rendszerbiztonsági tag számára. Az Access hatóköre az előfizetés, az erőforráscsoport, az Event Hubs névtér vagy bármely erőforrás szintje. Az Azure AD-biztonság szerepköröket rendelhet egy felhasználóhoz, egy csoporthoz, egy egyszerű alkalmazásszolgáltatáshoz vagy egy [felügyelt identitáshoz az Azure-erőforrásokhoz.](../active-directory/managed-identities-azure-resources/overview.md) 

> [!NOTE]
> A szerepkör-definíció engedélyek gyűjteménye. A szerepköralapú hozzáférés-vezérlés (RBAC) szabályozza, hogy ezeket az engedélyeket hogyan kényszeríti ki a szerepkör-hozzárendelés. A szerepkör-hozzárendelés három elemből áll: rendszerbiztonsági tagból, szerepkör-definícióból és hatókörből. További információt [a Különböző szerepkörök ismertetése című témakörben talál.](../role-based-access-control/overview.md)

## <a name="built-in-roles-for-azure-event-hubs"></a>Beépített szerepkörök az Azure Event Hubs-hoz
Az Azure a következő beépített RBAC-szerepköröket biztosítja az Event Hubs-adatokhoz való hozzáférés engedélyezéséhez az Azure AD és az OAuth használatával:

- [Azure Event Hubs data owner](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Ezzel a szerepkörral teljes hozzáférést biztosít az Event Hubs-erőforrásokhoz.
- [Azure Event Hubs data sender:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)Ezzel a szerepkörral küldési hozzáférést az Event Hubs-erőforrásokhoz.
- [Azure Event Hubs data receiver:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)Ezzel a szerepkörral hozzáférést biztosít az Event Hubs-erőforrásokhoz.   

> [!IMPORTANT]
> Előzetes verziónk támogatta az Event Hubs adathozzáférési jogosultságok hozzáadását a tulajdonosi vagy közreműködői szerepkörhöz. A tulajdonosi és közreműködői szerepkör adathozzáférési jogosultságai azonban már nem teljesülnek. Ha a tulajdonos vagy közreműködőszerepkört használja, váltson az Azure Event Hubs-adatközpontok adattulajdonosi szerepkörhasználatára.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>RBAC-szerepkörök hozzárendelése az Azure Portal használatával  
Ha többet szeretne megtudni az Azure-erőforrásokhoz való hozzáférés RBAC és az Azure Portal használatával történő kezeléséről, olvassa el [ezt a cikket.](..//role-based-access-control/role-assignments-portal.md) 

Miután megállapította a szerepkör-hozzárendelés megfelelő hatókörét, keresse meg az adott erőforrást az Azure Portalon. Az erőforrás hozzáférés-vezérlési (IAM) beállításainak megjelenítése, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> Az alábbiakban ismertetett lépések szerepkört rendelaz eseményközponthoz az Event Hubs névterek alatt, de ugyanazokat a lépéseket követve rendelhet hozzá egy szerepkört bármely Event Hubs erőforráshoz.

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg az Event Hubs névterét.
2. Az **Áttekintés** lapon jelölje ki azt az eseményközpontot, amelyhez szerepkört szeretne rendelni.

    ![Az eseményközpont kiválasztása](./media/authenticate-application/select-event-hub.png)
1. Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget** az eseményközpont hozzáférés-vezérlési beállításainak megjelenítéséhez. 
1. A **szerepkör-hozzárendelések** listájának megtekintéséhez válassza a Szerepkör-hozzárendelések lapot. Jelölje ki az eszköztár **Hozzáadás** gombját, majd a **Szerepkör-hozzárendelés hozzáadása**lehetőséget. 

    ![A Hozzáadás gomb az eszköztáron](./media/authenticate-application/role-assignments-add-button.png)
1. A **Szerepkör-hozzárendelés hozzáadása** lapon tegye a következő lépéseket:
    1. Válassza ki a hozzárendelni kívánt **Eseményközpontok szerepkört.** 
    1. Keresés a **rendszerbiztonsági tag** (felhasználó, csoport, egyszerű szolgáltatás) megkereséséhez, amelyhez a szerepkört hozzá kívánja rendelni.
    1. A **szerepkör-hozzárendelés** mentéséhez válassza a Mentés lehetőséget. 

        ![Szerepkör hozzárendelése felhasználóhoz](./media/authenticate-application/assign-role-to-user.png)
    4. A szerepkörhöz rendelt identitás megjelenik a szerepkör alatt. Az alábbi képen például látható, hogy az Azure-felhasználók az Azure Event Hubs data owner szerepkörben. 
        
        ![Felhasználó a listában](./media/authenticate-application/user-in-list.png)

Hasonló lépéseket követve az Event Hubs névtérhez, erőforráscsoporthoz vagy előfizetéshez hatókörrel ellátott szerepkörhozzárendeléséhez. Miután definiálja a szerepkört és hatókörét, tesztelheti ezt a viselkedést a [GitHub-helyen lévő](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)mintákkal.


## <a name="authenticate-from-an-application"></a>Hitelesítés egy alkalmazásból
Az Azure AD és az Event Hubs használatának egyik fő előnye, hogy a hitelesítő adatokat már nem kell tárolni a kódban. Ehelyett egy OAuth 2.0 hozzáférési jogkivonatot kérhet a Microsoft identity platformról. Az Azure AD hitelesíti az alkalmazást futtató rendszerbiztonsági tag (felhasználó, csoport vagy egyszerű szolgáltatás). Ha a hitelesítés sikeres, az Azure AD visszaadja a hozzáférési jogkivonatot az alkalmazásnak, és az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Event Hubs-kérelmek engedélyezéséhez.

A következő szakaszok bemutatják, hogyan konfigurálhatja a natív alkalmazást vagy webalkalmazást a Microsoft identity platform 2.0-s hitelesítéséhez. A Microsoft identity platform 2.0-s platformjáról a [Microsoft identity platform (2.0-s verzió) – áttekintés című témakörben olvashat bővebben.](../active-directory/develop/v2-overview.md)

Az OAuth 2.0-s kódtámogatási folyamat áttekintését az [Azure Active Directory webalkalmazásokhoz való hozzáférés engedélyezése az OAuth 2.0-s kódtámogatási folyamat használatával című témakörben találja.](../active-directory/develop/v2-oauth2-auth-code-flow.md)

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Regisztrálja az alkalmazást egy Azure AD-bérlővel
Az első lépés az Azure AD használatával az Event Hubs-erőforrások engedélyezéséhez az ügyfélalkalmazás regisztrálása egy Azure AD-bérlővel az [Azure Portalról.](https://portal.azure.com/) Az ügyfélalkalmazás regisztrálásakor az alkalmazással kapcsolatos információkat adja meg az AD-nek. Az Azure AD ezután egy ügyfél-azonosítót (más néven egy alkalmazásazonosítót) biztosít, amely segítségével társíthatja az alkalmazást az Azure AD futásidejéhez. Az ügyfélazonosítóról az [Alkalmazás- és egyszerű szolgáltatásobjektumok az Azure Active Directoryban](../active-directory/develop/app-objects-and-service-principals.md)című témakörben olvashat bővebben. 

A következő képek a webalkalmazások regisztrálásának lépéseit mutatják be:

![Egy alkalmazás regisztrálása](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Ha natív alkalmazásként regisztrálja az alkalmazást, megadhatja az átirányítási URI bármely érvényes URI-ját. Natív alkalmazások esetén ennek az értéknek nem kell valódi URL-címnek lennie. Webalkalmazások esetén az átirányítási URI-nak érvényes URI-nak kell lennie, mert megadja azt az URL-címet, amelyhez a jogkivonatok rendelkezésre állnak.

Miután regisztrálta az alkalmazást, a Beállítások csoportban megjelenik az **alkalmazás (ügyfél)** **azonosítója:**

![A regisztrált kérelem kérelemazonosítója](./media/authenticate-application/application-id.png)

Az alkalmazások Azure AD-vel való regisztrálásáról az [Alkalmazások integrálása](../active-directory/develop/quickstart-v2-register-an-app.md)az Azure Active Directoryval című témakörben talál további információt.


### <a name="create-a-client-secret"></a>Ügyféltitok létrehozása   
Az alkalmazásnak szüksége van egy ügyféltitokra, hogy bizonyítsa a személyazonosságát, amikor jogkivonatot kér. Az ügyféltitok hozzáadásához kövesse az alábbi lépéseket.

1. Nyissa meg az alkalmazásregisztrációt az Azure Portalon.
1. Válassza ki a **Tanúsítványok & titkos kulcsok** beállítást.
1. Az **Ügyféltitkok csoportban**válassza az **Új ügyféltitok** lehetőséget új titkos titok létrehozásához.
1. Adja meg a titkos kulcsot, és válassza ki a kívánt lejárati időközt.
1. Azonnal másolja az új titok értékét egy biztonságos helyre. A kitöltési érték csak egyszer jelenik meg.

    ![Titkos ügyfélkulcs](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Ügyfélkódtárak tokenbeszerzéshez  
Miután regisztrálta az alkalmazást, és megadta számára az adatok küldésére/fogadására vonatkozó engedélyeket az Azure Event Hubs-ban, kódot adhat hozzá az alkalmazáshoz egy rendszerbiztonsági tag hitelesítéséhez és az OAuth 2.0-s jogkivonat beszerzéséhez. A jogkivonat hitelesítéséhez és beszerzéséhez használhatja a [Microsoft identitásplatform hitelesítési kódtárait](../active-directory/develop/reference-v2-libraries.md) vagy egy másik nyílt forráskódú könyvtárat, amely támogatja az OpenID vagy a Connect 1.0-t. Az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Event Hubs elleni kérelem engedélyezéséhez.

Azokról az esetekről, amelyekesetében a tokenek beolvasása támogatott, tekintse meg a [Forgatókönyvek](https://aka.ms/msal-net-scenarios) szakasza a [Microsoft Authentication Library (MSAL) a .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub tárház.

## <a name="samples"></a>Példák
- [Microsoft.Azure.EventHubs mintákat](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Ezek a minták a régi **Microsoft.Azure.EventHubs-könyvtárat** használják, de könnyedén frissítheti azt a legújabb **Azure.Messaging.EventHubs-könyvtár** használatával. Ha át szeretné helyezni a mintát a régi könyvtár használatából az újkönyvtárba, olvassa el a [Microsoft.Azure.EventHubs-ról az Azure.Messaging.EventHubs szolgáltatásba való áttelepítés útmutatóját.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)
- [Az Azure.Messaging.EventHubs-minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Ez a minta frissítve lett a legújabb **Azure.Messaging.EventHubs-könyvtár** használatával.

## <a name="next-steps"></a>További lépések
- Ha többet szeretne megtudni az RBAC-ról, olvassa el [a Mi a szerepköralapú hozzáférés-vezérlés (RBAC) című témakört?](../role-based-access-control/overview.md)
- Az RBAC-szerepkör-hozzárendelések Azure PowerShell, Az Azure CLI vagy a REST API használatával történő hozzárendelése és kezelése című témakörből megtudhatja:
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure PowerShell segítségével](../role-based-access-control/role-assignments-powershell.md)  
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure CLI-vel](../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése a REST API-val](../role-based-access-control/role-assignments-rest.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure Resource Manager-sablonokkal](../role-based-access-control/role-assignments-template.md)

Lásd a következő kapcsolódó cikkeket:
- [Felügyelt identitás hitelesítése az Azure Active Directoryval az Event Hubs-erőforrások eléréséhez](authenticate-managed-identity.md)
- [Az Azure Event Hubs-ra irányuló kérelmek hitelesítése megosztott hozzáférésű aláírásokkal](authenticate-shared-access-signature.md)
- [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz az Azure Active Directory használatával](authorize-access-azure-active-directory.md)
- [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz megosztott hozzáférési aláírásokkal](authorize-access-shared-access-signature.md)

