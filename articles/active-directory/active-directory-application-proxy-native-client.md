---
title: "Natív ügyfél alkalmazások – az Azure AD közzététele |} Microsoft Docs"
description: "Bemutatja, hogyan adhat kommunikálni az Azure AD alkalmazásproxy-összekötő a helyszíni alkalmazások biztonságos távoli hozzáférést biztosítanak a natív ügyfél alkalmazások engedélyezése."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: de49a8343a3db6c8b890050e7791f77105a09a3a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Hogyan kommunikál a proxy alkalmazások natív ügyfél alkalmazások engedélyezése

Mellett a webes alkalmazásokhoz Azure Active Directory Alkalmazásproxyjával használhatók együtt az Azure AD Authentication Library (ADAL) konfigurált natív ügyfél-alkalmazások közzététele. Natív ügyfél alkalmazások eltérnek a webalkalmazások, mert telepítik őket egy eszközt, amíg a böngészőn keresztül elért webalkalmazások. 

Alkalmazásproxy által kiállított jogkivonatokat a fejlécben küldött fogadja el az Azure AD natív ügyfél alkalmazásokat támogatja. Az alkalmazásproxy-szolgáltatás a felhasználók nevében hitelesítést hajt végre. Ebben a megoldásban alkalmazás jogkivonatokat a hitelesítéshez használ. 

![A végfelhasználók, az Azure Active Directory és a közzétett alkalmazások közötti kapcsolat](./media/active-directory-application-proxy-native-client/richclientflow.png)

Az Azure AD Authentication Library, amely gondoskodik a hitelesítésről, és támogatja a legtöbb ügyfél környezetben, használja a natív alkalmazások közzétételét. Alkalmazásproxy hogyan illik bele a [webes API-forgatókönyvet natív alkalmazás](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). 

Ez a cikk végigvezeti a négy alkalmazásproxy és az Azure AD Authentication Library natív alkalmazás közzététele. 

## <a name="step-1-publish-your-application"></a>1. lépés: Az alkalmazás közzétételére.
A proxy alkalmazás közzététele, mint bármilyen más alkalmazást, és rendelje hozzá a felhasználók az alkalmazás eléréséhez. További információkért lásd: [alkalmazások közzétételére az alkalmazásproxy](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>2. lépés: Az alkalmazás konfigurálása
A natív alkalmazások konfigurálása az alábbiak szerint:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a **Azure Active Directory** > **App regisztrációk**.
3. Válassza ki **új alkalmazás regisztrációja**.
4. Adjon meg egy nevet az alkalmazáshoz, jelölje be **natív** az alkalmazás-típusként, és adjon meg az alkalmazás átirányítási URI-t. 

   ![Hozzon létre egy új alkalmazás regisztrálása](./media/active-directory-application-proxy-native-client/create.png)
5. Kattintson a **Létrehozás** gombra.

Új alkalmazás regisztrációjának létrehozásával kapcsolatos információért, lásd: [alkalmazások integrálása az Azure Active Directory](.//develop/active-directory-integrating-applications.md).


## <a name="step-3-grant-access-to-other-applications"></a>3. lépés: A hozzáférés engedélyezése az egyéb alkalmazások
Lehetővé teszik a natív alkalmazások számára elérhetővé tehető, hogy a címtárban lévő más alkalmazásokkal:

1. Még mindig **App regisztrációk**, válassza ki az imént létrehozott új natív alkalmazást.
2. Válassza ki **szükséges engedélyek**.
3. Válassza a **Hozzáadás** lehetőséget.
4. Nyissa meg az első lépés **API kiválasztása**.
5. A keresősáv segítségével megkeresheti az alkalmazásproxy-alkalmazást, az első szakaszban közzétett. Válassza ki, hogy az alkalmazást, majd kattintson az **válasszon**. 

   ![Keresse meg a proxy alkalmazást](./media/active-directory-application-proxy-native-client/select_api.png)
6. Nyissa meg a második lépést **engedélyként válassza**.
7. A natív alkalmazás hozzáférést biztosíthat a proxy-alkalmazást, majd kattintson a jelölőnégyzet segítségével **válasszon**.

   ![Hozzáférést biztosít proxy-alkalmazás](./media/active-directory-application-proxy-native-client/select_perms.png)
8. Válassza ki **végzett**.


## <a name="step-4-edit-the-active-directory-authentication-library"></a>4. lépés: Az Active Directory hitelesítési tár szerkesztése
A hitelesítési környezetet, az Active Directory Authentication Library (ADAL) az alábbi szöveget a natív alkalmazás kód szerkesztése:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = authContext.AcquireToken("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

A változók példakód ki kell cserélni az alábbiak szerint:

* **A bérlői azonosító** az Azure portálon található. Navigáljon a **Azure Active Directory** > **tulajdonságok** , és másolja a könyvtár-azonosító. 
* **Külső URL-cím** a Proxy alkalmazása a megadott előtér URL-címét. Ez az érték megkereséséhez nyissa meg a **alkalmazásproxy** a proxy app szakasza.
* **Alkalmazásazonosító** a natív alkalmazás megtalálható a **tulajdonságok** a natív alkalmazás lapján.
* **Átirányítási URI-címe a natív alkalmazás** található meg a **átirányítási URI-azonosítók** a natív alkalmazás lapján.

Ha az ADAL ezekkel a paraméterekkel szerkeszt, a felhasználók akkor is, ha a vállalati hálózaton kívülről fontosságúak natív ügyfél alkalmazásokhoz való hitelesítéshez kell lennie. 

## <a name="next-steps"></a>Következő lépések

A natív alkalmazás folyamattal kapcsolatos további információkért lásd: [natív alkalmazás webes API-hoz](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)

Beállításával kapcsolatos tudnivalók [egyszeri bejelentkezés az alkalmazásproxy](application-proxy-sso-overview.md)
