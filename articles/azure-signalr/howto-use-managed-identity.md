---
title: Felügyelt identitások az Azure Signaler szolgáltatásban
description: Ismerje meg, hogyan működnek a felügyelt identitások az Azure Signaler szolgáltatásban, hogyan konfigurálható a felügyelt identitások kiszolgáló nélküli helyzetekben való használatára.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: 8e122be74d623c7f1ea5576a5fe527dfa538d1d1
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988659"
---
# <a name="how-to-use-managed-identities-for-azure-signalr-service"></a>Felügyelt identitások használata az Azure Signaler szolgáltatásban

Ez a témakör bemutatja, hogyan hozhat létre felügyelt identitást az Azure Signaler szolgáltatáshoz, és hogyan használhatja azt kiszolgáló nélküli forgatókönyvekben.

> [!Important] 
> Az Azure Signaler szolgáltatás csak egy felügyelt identitást tud támogatni. Ez azt jelenti, hogy a rendszerhez hozzárendelt identitást vagy egy felhasználó által hozzárendelt identitást is hozzáadhat. 

## <a name="add-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha felügyelt identitást szeretne beállítani a portálon, először hozzon létre egy szignáló szolgáltatást a szokásos módon, majd engedélyezze a szolgáltatást.

1. A szokásos módon hozzon létre egy Signaler szolgáltatást a portálon. Navigáljon a portálon.

2. Válassza az **identitás**lehetőséget.

4. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva **értékre**. Kattintson a **Save** (Mentés) gombra.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Rendszerhez rendelt identitás hozzáadása a portálon":::

## <a name="add-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

A felhasználó által hozzárendelt identitással rendelkező szignáló szolgáltatás létrehozásához létre kell hoznia az identitást, majd hozzá kell adnia annak erőforrás-azonosítóját a szolgáltatáshoz.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Először létre kell hoznia egy felhasználó által hozzárendelt identitás-erőforrást.

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitási erőforrást az [utasításoknak](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)megfelelően.

2. A szokásos módon hozzon létre egy Signaler szolgáltatást a portálon. Navigáljon a portálon.

3. Válassza az **identitás**lehetőséget.

4. A **felhasználó által hozzárendelt** lapon kattintson a **Hozzáadás**gombra.

5. Keresse meg a korábban létrehozott identitást, és válassza ki. Kattintson a **Hozzáadás** parancsra.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Felhasználó-assigened identitás hozzáadása a portálon":::

## <a name="use-managed-identity-in-serverless-scenarios"></a>Felügyelt identitás használata kiszolgáló nélküli forgatókönyvekben

Mivel a Signaler szolgáltatás egy teljes körűen felügyelt szolgáltatás, ezért nem használhatja a felügyelt identitást a jogkivonatok manuális lekéréséhez. Ehelyett a Signaler szolgáltatás a hozzáférési token beszerzéséhez beállított felügyelt identitást használja, és `Authorization` egy felsőbb rétegbeli kérelemben a fejlécre van beállítva, kiszolgáló nélküli forgatókönyvekben.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Felügyelt identitás hitelesítésének engedélyezése a felsőbb rétegbeli beállításokban

1. Rendszerhez rendelt identitás vagy felhasználó által hozzárendelt identitás hozzáadása

2. Konfigurálja a felsőbb rétegbeli beállításokat, és használja az *ManagedIdentity* -t *hitelesítési* beállításokként. Megtudhatja, hogyan hozhat létre felsőbb szintű beállításokat hitelesítéssel a [felsőbb rétegbeli beállításokban](concept-upstream.md#create-upstream-settings).

3. A felügyelt identitás-hitelesítés beállításainál megadhatja a cél *erőforrást*. Az *erőforrás* a `aud` beszerzett hozzáférési jogkivonatban lesz jogcím, amely az ellenőrzés részeként használható a felsőbb rétegbeli végpontokban. Az *erőforrás* a következők egyike lehet
    - Üres
    - Az egyszerű szolgáltatásnév alkalmazás-(ügyfél-) azonosítója
    - Az egyszerű szolgáltatásnév alkalmazásspecifikus AZONOSÍTÓjának URI-ja
    - Az Azure-szolgáltatások erőforrás-azonosítói: [Az Azure-szolgáltatások erőforrás-azonosítója](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Ha a hozzáférési jogkivonatot a szolgáltatásában saját maga érvényesíti, kiválaszthatja az egyik *erőforrás* -formátumot is. Ha biztosítani szeretné, hogy az *erőforrás* a *hitelesítési* beállításokban és az érvényesítés konzisztens legyen. Ha az adatsík RBAC használja, akkor a szolgáltató által igényelt *erőforrást* kell használnia.

### <a name="validate-access-token"></a>Hozzáférési jogkivonat ellenőrzése

A `Authorization` fejlécben található jogkivonat a [Microsoft Identity platform hozzáférési jogkivonata](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

A hozzáférési jogkivonatok érvényesítéséhez az alkalmazásnak ellenőriznie kell a célközönséget és az aláírási jogkivonatokat is. Ezeket érvényesíteni kell az OpenID-felderítési dokumentum értékeivel. A dokumentum bérlőtől független verziója például a következő helyen található: [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

Az Azure AD-alapú middleware beépített funkciókat biztosít a hozzáférési jogkivonatok érvényesítéséhez, és böngészhet a [minták](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) között, és megkeresheti az Ön által választott nyelven.

Olyan kódtárakat és kódokat biztosítunk, amelyek bemutatják a jogkivonat-érvényesítés kezelését. Az alábbi információk azokra a felhasználókra vonatkoznak, akik meg szeretnék ismerni a mögöttes folyamatot. A JWT ellenőrzéséhez több harmadik féltől származó nyílt forráskódú kódtár is rendelkezésre áll – legalább egy lehetőség van a szinte minden platformra és nyelvre. További információ az Azure AD hitelesítési könyvtárairól és a kódokról: [v 2.0 hitelesítési kódtárak](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>További lépések

- [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](signalr-concept-serverless-development-config.md)
