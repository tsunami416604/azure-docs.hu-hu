---
title: Felügyelt identitások az Azure Signaler szolgáltatásban
description: Ismerje meg, hogyan működnek a felügyelt identitások az Azure Signaler szolgáltatásban, és hogyan használhatók felügyelt identitások kiszolgáló nélküli forgatókönyvekben.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: abe7503e7eb73d533ae901af21de001960173fb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559405"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Felügyelt identitások az Azure Signaler szolgáltatáshoz

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást az Azure Signaler szolgáltatáshoz, és hogyan használhatja azt kiszolgáló nélküli forgatókönyvekben.

> [!Important] 
> Az Azure Signaler szolgáltatás csak egy felügyelt identitást tud támogatni. Ez azt jelenti, hogy a rendszerhez hozzárendelt identitást vagy felhasználó által hozzárendelt identitást adhat hozzá. 

## <a name="add-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

A Azure Portal felügyelt identitásának beállításához először létre kell hoznia egy Azure Signal Service-példányt, majd engedélyeznie kell a szolgáltatást.

1. A szokásos módon hozzon létre egy Azure Signaler Service-példányt a portálon. Tallózással keresse meg a portálon.

2. Válassza az **identitás**lehetőséget.

4. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva **értékre**. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Rendszerhez rendelt identitás hozzáadása a portálon":::

## <a name="add-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

A felhasználó által hozzárendelt identitással rendelkező Azure Signaler szolgáltatás-példány létrehozásához létre kell hoznia az identitást, majd hozzá kell adnia annak erőforrás-azonosítóját a szolgáltatáshoz.

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitási erőforrást az [utasításoknak](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)megfelelően.

2. A szokásos módon hozzon létre egy Azure Signaler Service-példányt a portálon. Tallózással keresse meg a portálon.

3. Válassza az **identitás**lehetőséget.

4. A **felhasználó által hozzárendelt** lapon válassza a **Hozzáadás**lehetőséget.

5. Keresse meg a korábban létrehozott identitást, és válassza ki. Válassza a **Hozzáadás** elemet.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Felhasználó által hozzárendelt identitás hozzáadása a portálon":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Felügyelt identitás használata kiszolgáló nélküli forgatókönyvekben

Az Azure Signaler szolgáltatás egy teljes körűen felügyelt szolgáltatás, ezért nem használhat felügyelt identitást a jogkivonatok manuális lekéréséhez. Ehelyett az Azure Signaler szolgáltatás a hozzáférési token beszerzéséhez beállított felügyelt identitást használja. A szolgáltatás ezután a hozzáférési jogkivonatot egy `Authorization` felsőbb rétegbeli kérelemben lévő fejlécbe állítja be kiszolgáló nélküli forgatókönyvekben.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Felügyelt identitás hitelesítésének engedélyezése a felsőbb rétegbeli beállításokban

1. Rendszerhez rendelt identitás vagy felhasználó által hozzárendelt identitás hozzáadása.

2. Konfigurálja a felsőbb rétegbeli beállításokat, és használja az **ManagedIdentity** -t **hitelesítési** beállításokként. A felsőbb rétegbeli beállítások hitelesítéssel történő létrehozásával kapcsolatos további információkért lásd: [felsőbb rétegbeli beállítások](concept-upstream.md).

3. A felügyelt identitás hitelesítési beállításai **erőforrás**esetében megadhatja a cél erőforrást. Az erőforrás a `aud` beszerzett hozzáférési jogkivonat jogcíme lesz, amely a felsőbb rétegbeli végpontok érvényesítésének részeként használható. Az erőforrás a következők egyike lehet:
    - Üres
    - Az egyszerű szolgáltatásnév alkalmazás-(ügyfél-) azonosítója
    - Az egyszerű szolgáltatásnév alkalmazásspecifikus AZONOSÍTÓjának URI-ja
    - [Azure-szolgáltatás erőforrás-azonosítója](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Ha a szolgáltatásban saját maga érvényesít egy hozzáférési jogkivonatot, kiválaszthatja az egyik erőforrás-formátumot is. Győződjön meg arról, hogy az **erőforrás** -érték a **hitelesítési** beállításokban és az érvényesítés konzisztens. Ha szerepköralapú hozzáférés-vezérlést (RBAC) használ egy adatsíkon, akkor a szolgáltató által igényelt erőforrást kell használnia.

### <a name="validate-access-tokens"></a>Hozzáférési tokenek ellenőrzése

A fejlécben szereplő jogkivonat a `Authorization` [Microsoft Identity platform hozzáférési jogkivonata](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

A hozzáférési jogkivonatok érvényesítéséhez az alkalmazásnak ellenőriznie kell a célközönséget és az aláíró jogkivonatokat is. Ezeket érvényesíteni kell az OpenID-felderítési dokumentum értékeivel. Tekintse meg például a [dokumentum bérlői független verzióját](https://login.microsoftonline.com/common/.well-known/openid-configuration).

A Azure Active Directory (Azure AD) middleware beépített képességekkel rendelkezik a hozzáférési jogkivonatok érvényesítéséhez. A [minták](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) között böngészhet, és megkeresheti az Ön által választott nyelven.

Olyan kódtárakat és kódokat biztosítunk, amelyek bemutatják a jogkivonat-érvényesítés kezelését. A JSON Web Token (JWT) érvényesítéséhez több nyílt forráskódú partneri függvénytár is elérhető. Szinte minden platformhoz és nyelvhez legalább egy lehetőség van. További információ az Azure AD-hitelesítési könyvtárakról és a mintakód-mintákról: [Microsoft Identity platform Authentication librarys](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>További lépések

- [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](signalr-concept-serverless-development-config.md)
