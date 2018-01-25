---
title: "Az alkalmazást az Azure Active Directory alkalmazáskatalógusában listázása"
description: "Hogyan listázhat egy alkalmazás, amely támogatja az egyszeri bejelentkezés az Azure Active Directory-katalógus |} A Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Az alkalmazást az Azure Active Directory alkalmazáskatalógusában listázása


##  <a name="what-is-azure-ad-app-gallery"></a>Mi az Azure AD-alkalmazásgyűjtemény?

Az Azure AD egy olyan felhőalapú identitás-szolgáltatás. [Az Azure AD-alkalmazásgyűjtemény](https://azure.microsoft.com/marketplace/active-directory/all/) egy közös tároló, ahol egyszeri bejelentkezést és a felhasználók átadása közzétett összes alkalmazás-összekötő. A kölcsönös identitás-szolgáltatóként az Azure AD használó ügyfelek különböző SaaS alkalmazás összekötők, amelyek itt közzétett keresi. Rendszergazda összekötő hozzáadja az alkalmazás-galériából és konfigurálja, és az egyszeri bejelentkezés és üzembe helyezési használni. Az Azure AD összes fő összevonási protokollokkal SAML 2.0, a OpenID Connect, a OAuth és a WS-Fed az egyszeri bejelentkezést támogatja. 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>Milyen előnyökkel listázása az alkalmazás a katalógusban?

*  Adja meg a legjobban lehetséges egyszeri bejelentkezéses felhasználói élmény biztosítása az ügyfeleknek.

*  Az alkalmazás egyszerű és minimális konfigurációját.

*  Ügyfelek keresése az alkalmazás és megtalálható a gyűjteményben. 

*  Minden ügyfél használhat ez az integráció Azure AD-SKU ingyenes, a Basic vagy a prémium szintű.

*  Lépés lépés konfigurációs-oktatóanyagban a kölcsönös ügyfelek esetén.

*  A felhasználó-kiépítés engedélyezése ugyanahhoz az alkalmazáshoz tartozó SCIM használata.


##  <a name="what-are-the-pre-requisites"></a>Mik azok a szükséges előfeltételek?

Egy alkalmazás az Azure AD-katalógus listázásához, az alkalmazás első kell megvalósítania az Azure AD által támogatott összevonási protokollok egyikét. Olvassa el a feltételeket és kikötéseket, az Azure AD application gallery itt. Ha használ: 

*   **OpenID Connect** – a több-bérlős alkalmazás létrehozása az Azure ad-ben, és végrehajtása [az Azure AD hozzájárulási keretrendszer](active-directory-integrating-applications.md#overview-of-the-consent-framework) az alkalmazáshoz. Így minden ügyfél biztosítani tudja az alkalmazás hozzájárul a bejelentkezési kérelem elküldése közös végpontot. Azt is szabályozhatja a felhasználói hozzáférést a felhasználónak a bérlő azonosítója és a felhasználó egyszerű megkapta a jogkivonatot a alapján. Az alkalmazás integrálja az Azure AD-val, kövesse a [fejlesztői útmutatás](active-directory-authentication-scenarios.md).

*   **SAML 2.0-s vagy a WS-Fed** – az alkalmazás egy olyan képességet, ehhez az SAML/WS-Fed SSO integráció SP vagy a kiállító terjesztési hely módban kell rendelkeznie. Bármely alkalmazás, amely támogatja az SAML 2.0 integrálható közvetlenül az Azure AD-bérlő használata a [egyéni alkalmazás hozzáadása utasításokat](../active-directory-saas-custom-apps.md).

*   **Egyszeri jelszó** – hozzon létre egy webalkalmazást, amely rendelkezik egy lap HTML-bejelentkezés konfigurálása [jelszó-alapú egyszeri bejelentkezést](../active-directory-appssoaccess-whatis.md). Egyszeri jelszó alapú is hívják jelszó vaulting, lehetővé teszi a felhasználói hozzáférés és a webes alkalmazásokhoz, amelyek nem támogatják az identitás-összevonási jelszavak kezeléséhez. Akkor célszerű is forgatókönyvek, ahol több felhasználó meg szeretné osztani ugyanazt a fiókot, például a szervezet közösségi app fiókokhoz. 

## <a name="process-for-submitting-the-request-in-the-portal"></a>A portál a kérelem elküldésekor folyamata

Miután ellenőrizte, hogy működik-e az alkalmazások integrálása az Azure ad-vel, szeretné-e a hozzáférési kérelem elküldeni a [alkalmazás hálózati Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Ha rendelkezik Office 365-fiókkal, használhatja, amelyek a bejelentkezés a portálra ellenkező esetben használja a Microsoft ID (Live ID, az Outlook, Hotmail stb.) használatát a bejelentkezéshez. A hozzáférés kéréséhez lásd a következő lap. Adja meg a beviteli mezőben szereplő üzleti indoklásának, majd kattintson a **hozzáférés kérése**. A csapat fogja az adatokat, és hozzáférést, a ennek megfelelően. Ezt követően jelentkezzen be a portálra, és küldje el a részletes kérést az alkalmazáshoz.

Ha szembesülhetnek a hozzáférés a probléma, forduljon a [Azure AD SSO integrációs csoport](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Hozzáférési kérelem SharePoint-portál](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Ütemtervek
    
*   Listaelem SAML 2.0 és a WS-Fed alkalmazást a galéria - **7 – 10 nap**

   ![Ütemterv a gyűjtemény SAML-alapú alkalmazást listázása](./media/active-directory-app-gallery-listing/timeline.png)

*   Folyamat listázása az OpenID Connect alkalmazás azokat a galéria - **2-5 munkanapon**

   ![Ütemterv a gyűjtemény SAML-alapú alkalmazást listázása](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Azok következményeinek

Az összes olyan dobja el az e-mailek [Azure AD SSO integrációs csoport](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) és azt visszaszerezheti korábban MIKO.

