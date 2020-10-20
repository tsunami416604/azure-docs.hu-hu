---
title: Hitelesítés
description: Elmagyarázza, hogyan működik a hitelesítés?
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: e61767b9b8d904eae9a247f48d2d781fd0c95192
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202718"
---
# <a name="configure-authentication"></a>A hitelesítés konfigurálása

Az Azure távoli renderelés ugyanazt a hitelesítési mechanizmust használja, mint az [Azure térbeli horgonyok (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Az ügyfeleknek a következők *egyikét* kell megadniuk a REST API-k sikeres meghívásához:

* **AccountKey**: a Azure Portal távoli renderelési fiókjához tartozó Keys (kulcsok) lapon szerezhető be. A fiók kulcsai csak fejlesztési/prototípus-készítéshez ajánlottak.
    ![Fiókazonosító](./media/azure-account-primary-key.png)

* **AuthenticationToken**: egy Azure ad-jogkivonat, amelyet a [MSAL-könyvtár](../../active-directory/develop/msal-overview.md)használatával lehet beszerezni. Több különböző folyamat is elérhető a felhasználói hitelesítő adatok elfogadásához és a hitelesítő adatok használatával hozzáférési jogkivonat beszerzéséhez.

* **MRAccessToken**: egy Mr token, amely az Azure Mixed Reality biztonságijogkivonat-szolgáltatás (STS) használatával szerezhető be. A `https://sts.mixedreality.azure.com` végpontról a következő híváshoz hasonló Rest-hívással lehet lekérni:

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Ahol az engedélyezési fejléc a következőképpen van formázva: `Bearer <Azure_AD_token>` vagy `Bearer <accoundId>:<accountKey>` . Az előző a biztonság számára ajánlott. A REST-hívás által visszaadott jogkivonat az MR hozzáférési jogkivonat.

## <a name="authentication-for-deployed-applications"></a>Hitelesítés telepített alkalmazásokhoz

A fiók kulcsai a gyors prototípusokhoz ajánlottak, csak a fejlesztés során. Azt javasoljuk, hogy az alkalmazást az éles környezetben lévő beágyazott fiók használatával ne szállítsa az éles környezetbe. Az ajánlott módszer a felhasználó-vagy a szolgáltatáson alapuló Azure AD-alapú hitelesítési módszer használata.

 Az Azure AD-hitelesítést az Azure [térbeli horgonyok (ASA)](../../spatial-anchors/index.yml) szolgáltatás [Azure ad felhasználói hitelesítés](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) szakasza ismerteti.

 További információkért tekintse meg a következő [oktatóanyagot: az Azure Remote rendering és a Model Storage biztonságossá tétele – Azure Active Directory hitelesítés](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A szolgáltatáshoz biztosított hozzáférés szintjének szabályozása érdekében a szerepköralapú hozzáférés megadásakor használja az alábbi szerepköröket:

* **Távoli renderelési rendszergazda**: lehetővé teszi a felhasználó számára a konverziót, a munkamenetek kezelését, a renderelést és a diagnosztikai képességeket az Azure távoli rendereléshez.
* **Távoli renderelési ügyfél**: a felhasználók számára biztosít munkamenet-, renderelési és diagnosztikai képességeket az Azure távoli rendereléshez.

## <a name="next-steps"></a>Következő lépések

* [Fiók létrehozása](create-an-account.md)
* [Az Azure frontend API-k használata a hitelesítéshez](frontend-apis.md)
* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)