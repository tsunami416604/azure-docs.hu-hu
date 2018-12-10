---
title: Natív ügyfélalkalmazások – Azure ad-ben közzététele |} A Microsoft Docs
description: Bemutatja, hogyan kommunikáljon az Azure AD alkalmazásproxy-összekötő a helyszíni alkalmazások biztonságos távoli elérést biztosíthat a natív ügyfélalkalmazások engedélyezéséhez.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: a73e652c74f9d88f1e066de190834fc033c13cf0
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135526"
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Együttműködhet a proxy-alkalmazások natív ügyfélalkalmazások engedélyezése

Webes alkalmazások, kívül az Azure Active Directory Application Proxy is segítségével közzététele a natív ügyfélalkalmazások, amelyek az az Azure AD Authentication Library (ADAL) vannak konfigurálva. Natív ügyfélalkalmazások eltérnek a webalkalmazások, mert a telepítéskor az eszközön, amíg a böngészőn keresztül elért webalkalmazások. 

Az alkalmazásproxy által kiállított jogkivonatokban fejlécében átvevő Azure AD natív ügyfélalkalmazások támogatja. Az alkalmazásproxy-szolgáltatás a felhasználók nevében hitelesítést hajt végre. Ez a megoldás nem használ alkalmazás jogkivonatokat a hitelesítéshez. 

![A végfelhasználók, az Azure Active Directory és a közzétett alkalmazások közötti kapcsolat](./media/application-proxy-configure-native-client-application/richclientflow.png)

Használja az Azure AD Authentication Library, amely gondoskodik a hitelesítési és sok ügyfél környezet támogatja, natív alkalmazásokat tehet közzé. Az alkalmazásproxy hogyan illik bele a [natív alkalmazás webes API-forgatókönyv](../develop/native-app.md). 

Ez a cikk végigvezeti az Application Proxy és az Azure AD Authentication Library natív alkalmazás közzététele a négy lépést. 

## <a name="step-1-publish-your-application"></a>1. lépés: Az alkalmazás közzététele
A proxy-alkalmazások közzététele, mint bármely más alkalmazás, és rendelje hozzá a felhasználók elérhetik az alkalmazást. További információkért lásd: [alkalmazásait közzéteheti az alkalmazásproxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-configure-your-application"></a>2. lépés: Az alkalmazás konfigurálása
A natív alkalmazás a következőképpen konfigurálja:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a **Azure Active Directory** > **alkalmazásregisztrációk**.
3. Válassza az **Új alkalmazás regisztrálása** elemet.
4. Adjon meg egy nevet az alkalmazáshoz, jelölje be **natív** az alkalmazás típusát, és adja meg az átirányítási URI-t az alkalmazáshoz. 

   ![Hozzon létre egy új alkalmazás regisztrálása](./media/application-proxy-configure-native-client-application/create.png)
5. Kattintson a **Létrehozás** gombra.

További részletes információ egy új alkalmazásregisztráció létrehozásával kapcsolatban lásd: [alkalmazások integrálása az Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).


## <a name="step-3-grant-access-to-other-applications"></a>3. lépés: A hozzáférés biztosítása más alkalmazásokhoz
A címtárban lévő további alkalmazásokat ki vannak téve a natív alkalmazás engedélyezése:

1. Még mindig **alkalmazásregisztrációk**, válassza ki az imént létrehozott új natív alkalmazást.
2. Válassza ki **API-engedélyek**.
3. Válassza ki **adjon hozzá egy engedélyt**.
4. Nyissa meg az első lépés **API kiválasztása**.
5. A Keresősáv használatával keresse meg az első szakaszban közzétett alkalmazásproxy alkalmazást. Válassza ki az alkalmazást, majd kattintson a **kiválasztása**. 

   ![Keresse meg a proxy alkalmazást](./media/application-proxy-configure-native-client-application/select_api.png)
6. Nyissa meg a második lépést **engedélyek kiválasztása**.
7. A natív alkalmazás hozzáférést biztosítani az alkalmazásproxy-alkalmazáshoz, majd kattintson a jelölőnégyzet segítségével **kiválasztása**.

   ![Proxy-alkalmazáshoz való hozzáférés biztosítása](./media/application-proxy-configure-native-client-application/select_perms.png)
8. Válassza a **Done** (Kész) lehetőséget.


## <a name="step-4-edit-the-active-directory-authentication-library"></a>4. lépés: Az Active Directory Authentication Library szerkesztése
A hitelesítési környezetet, az Active Directory Authentication Library (ADAL) az alábbi szöveget a natív alkalmazás kód szerkesztése:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

A mintakód a változókat kell helyébe a következő:

* **Bérlőazonosító** az Azure Portalon tekintheti meg. Navigáljon a **Azure Active Directory** > **tulajdonságok** , és másolja a címtár-azonosító. 
* **Külső URL-cím** az alkalmazásproxy-alkalmazáshoz megadott előtér-URL-címe. Ez az érték megkereséséhez nyissa meg a **alkalmazásproxy** szakaszában proxy.
* **Alkalmazásazonosító** a natív alkalmazás találhatók a **tulajdonságok** a natív alkalmazás lapján.
* **Átirányítási URI-t a natív alkalmazás** találhatók a **átirányítási URI-k** a natív alkalmazás lapján.

Ezekkel a paraméterekkel szerkeszti az adal-t, miután a felhasználók tudni hitelesíteni a natív ügyfélalkalmazások, még ha a vállalati hálózaton kívül kell lennie. 

## <a name="next-steps"></a>További lépések

A natív alkalmazás folyamat kapcsolatos további információkért lásd: [natív alkalmazás webes API-hoz](../develop/native-app.md)

Ismerje meg beállítása [egyszeri bejelentkezést az alkalmazásproxy](what-is-single-sign-on.md#single- sign-on-methods)
