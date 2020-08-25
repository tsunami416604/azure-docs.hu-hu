---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a whoIam használatával
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálható Azure AD B2C hitelesítés a whoIam használatával a felhasználók ellenőrzéséhez
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817570"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Oktatóanyag a WhoIAM konfigurálásához a Azure Active Directory B2C

Ebben a példában útmutatást nyújtunk a [WhoIAM](https://www.whoiam.ai/brims/) márkás Identitáskezelés rendszerének (BRIMS) konfigurálásához a környezetben, és integrálni azt a Azure ad B2Cával.

A WhoIAM BRIMS a környezetében üzembe helyezett alkalmazások és szolgáltatások összessége. Ez biztosítja a felhasználói bázis hang-, SMS-és e-mail-ellenőrzését. A BRIMS a meglévő identitás-és hozzáférés-kezelési megoldással együtt működik, és a platform agnosztikus.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Egy Azure ad B2C bérlő](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , amely az Azure-előfizetéshez van csatolva.

- WhoIAM [próbaverziós fiók](https://www.whoiam.ai/contact-us/)

## <a name="scenario-description"></a>Forgatókönyv leírása

A WhoIAM-integráció a következő összetevőket tartalmazza:

- Azure AD B2C bérlő – az engedélyezési kiszolgáló, amelynek feladata a felhasználó hitelesítő adatainak ellenőrzése a bérlőben definiált egyéni házirendek alapján. Más néven identitás-szolgáltató.

- Felügyeleti portál ügyfelek és azok konfigurációinak kezeléséhez

- Egy API-szolgáltatás, amely különböző szolgáltatásokat tesz elérhetővé végpontokon keresztül  

- A BRIMS felügyeleti portál és az API szolgáltatás háttereként használt Azure Cosmos DB

A következő architektúra-diagram a megvalósítást mutatja be.

![képernyőkép a whoiam-Architecture-diagramról](media/partner-whoiam/whoiam-architecture-diagram.png)

|Lépés | Leírás |
|:-----| :-----------|
| 1. | A felhasználó egy bejelentkezési oldalon érkezik. A felhasználó elindít egy regisztrációs vagy bejelentkezési kérést egy olyan alkalmazásnak, amely a Azure AD B2C identitás-szolgáltatóként használja.
| 2. | A hitelesítés részeként a felhasználó a saját e-mail-címe vagy telefonja tulajdonjogának ellenőrzésére, illetve a hangjuk biometrikus ellenőrzési tényezőként való felhasználására kéri.  
| 3. | Azure AD B2C kezdeményezi a BRIMS API szolgáltatásnak a felhasználó e-mail-címét, telefonszámát és hangfelvételét.
| 4. | A BRIMS előre definiált konfigurációkat használ, például teljes mértékben testreszabható e-maileket és SMS-sablonokat, hogy az alkalmazás megjelenésével és működésével összhangban legyenek a felhasználóval a megfelelő nyelven.
| 5. | A felhasználó személyazonosságának ellenőrzése után a BRIMS egy tokent ad vissza, amely az ellenőrzés eredményét Azure AD B2C. Azure AD B2C ezután engedélyezi a felhasználónak az alkalmazáshoz való hozzáférést, vagy a hitelesítési kísérletük meghiúsul.  

## <a name="onboard-with-whoiam"></a>WhoIAM

1. Forduljon a [WhoIAM](https://www.whoiam.ai/contact-us/) -hez, és hozzon létre egy BRIMS-fiókot.

2. A fiók létrehozása után használja az elérhetővé tett bevezetési irányelveket, és konfigurálja a következő Azure-szolgáltatásokat:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) – a jelszó biztonságos tárolásához használható, például:, levelezési szolgáltatás jelszava.

    - [Azure app Service](https://azure.microsoft.com/services/app-service/) – a BRIMS API és a felügyeleti portál szolgáltatásainak üzemeltetésére szolgál

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) – a rendszergazda felhasználók hitelesítésére használható a felügyeleti portálon

    - [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) – beállítások tárolására és lekérésére szolgál

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (nem kötelező) – az API-és a felügyeleti portálra való bejelentkezéshez használatos

3. Telepítse a BRIMS API-t és a BRIMS felügyeleti portált az Azure-környezetben.

4. A BRIMS-előkészítési dokumentációban Azure AD B2C egyéni házirend-minták érhetők el. Kövesse a dokumentumot az alkalmazás konfigurálásához, és használja a BRIMS platformot a felhasználói azonosító ellenőrzéséhez.  

További információ a WhoIAM BRIMS: [termék dokumentációja](https://www.whoiam.ai/brims/)

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a házirendek területen válassza az **identitási élmény keretrendszere**elemet.

2. Válassza ki a korábban létrehozott **SignUpSignIn**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   a. **Alkalmazás**: válassza ki a regisztrált alkalmazást (minta a JWT)

   b. **Válasz URL-címe**: válassza ki az **átirányítási URL-címet**

   c. Válassza a **felhasználói folyamat futtatása**lehetőséget.

4. Ugorjon a regisztrációs folyamatra, és hozzon létre egy fiókot

5. A BRIMS szolgáltatás a folyamat során lesz meghívva, a felhasználói attribútum létrehozása után. Ha a folyamat hiányos, ellenőrizze, hogy a felhasználó nincs-e mentve a címtárban.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
