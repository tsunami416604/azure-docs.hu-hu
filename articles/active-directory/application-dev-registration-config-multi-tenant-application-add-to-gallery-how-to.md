---
title: "Az Azure AD application gallery egy több-bérlős alkalmazás hozzáadása |} Microsoft Docs"
description: "Azt ismerteti, hogyan listázhatja a Azure AD Application Gallery a saját fejlesztésű több-bérlős alkalmazásához"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Az Azure AD application gallery egy több-bérlős alkalmazás hozzáadása

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az az Azure AD Application Gallery?

Az Azure AD egy olyan felhőalapú identitás-szolgáltatás. [Az Azure AD-alkalmazásgyűjtemény](https://azure.microsoft.com/marketplace/active-directory/all/) egy közös tároló, ahol egyszeri bejelentkezést és a felhasználók átadása közzétett összes alkalmazás-összekötő. A kölcsönös identitás-szolgáltatóként az Azure AD használó ügyfelek különböző SaaS alkalmazás összekötők, amelyek itt közzétett keresi. Rendszergazda összekötő hozzáadja az alkalmazás-galériából és konfigurálja, és az egyszeri bejelentkezés és üzembe helyezési használni. Az Azure AD összes fő összevonási protokollokkal SAML 2.0, az OpenID Connect, a OAuth és a WS-Fed az egyszeri bejelentkezést támogatja. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Ha az alkalmazás támogatja az SAML-alapú vagy OpenIDConnect
Ha egy több-bérlős alkalmazást szeretné az Azure AD Application Gallery listájában, először ellenőrizze, az alkalmazás támogatja a következő egyszeri bejelentkezési technológiák egyikét:

1. **OpenID Connect** – a több-bérlős alkalmazás létrehozása az Azure ad-ben, és végrehajtása [az Azure AD hozzájárulási keretrendszer](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) az alkalmazáshoz. Így minden ügyfél biztosítani tudja az alkalmazás hozzájárul a bejelentkezési kérelem elküldése közös végpontot. Azt is szabályozhatja a felhasználói hozzáférést a felhasználónak a bérlő azonosítója és a felhasználó egyszerű megkapta a jogkivonatot a alapján. Küldjön az alkalmazást, ahogy azt korábban említettük, az e [cikk](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

2. **SAML** – Ha az alkalmazás támogatja az SAML 2.0 majd az alkalmazás az oldalon megjelenő utasításokat felsorolt [Itt](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)

Ha az alkalmazás támogatja ezeket egyszeri bejelentkezési módok egyikét, és szeretné több-bérlős alkalmazás szerepeltetése az Azure AD Application Gallery, is hajtsa végre az említett [ez](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) cikk. 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Ha az alkalmazás nem támogatja az SAML-alapú vagy OpenIDConnect
Akkor is, ha az alkalmazás nem támogatja e két beállítás közül, azt továbbra is integrálható, a gyűjtemény, a jelszót az egyszeri bejelentkezés technológiával.

**Egyszeri jelszó** – hozzon létre egy webalkalmazást, amely rendelkezik egy lap HTML-bejelentkezés konfigurálása [jelszó-alapú egyszeri bejelentkezést](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis). Egyszeri jelszó alapú is hívják jelszó vaulting, lehetővé teszi a felhasználói hozzáférés és a webes alkalmazásokhoz, amelyek nem támogatják az identitás-összevonási jelszavak kezeléséhez. Akkor célszerű is forgatókönyvek, ahol több felhasználó meg szeretné osztani ugyanazt a fiókot, például a szervezet közösségi app fiókokhoz. 

Ha azt szeretné, az alkalmazás majd ezzel a technológiával listázásához, adjon a kérelmet a [ez](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) cikk.

## <a name="escalations"></a>Azok következményeinek

Az összes olyan dobja el az e-mailek [Azure AD SSO integrációs csoport](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) és azt visszaszerezheti korábban MIKO.

## <a name="next-steps"></a>További lépések
[Hogyan alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
