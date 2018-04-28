---
title: Vegye fel a több-bérlős alkalmazást az Azure AD application gallery |} Microsoft Docs
description: Ismerteti, hogyan listázhatja az egyéni fejlett több-bérlős alkalmazást az Azure AD application gallery.
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
ms.openlocfilehash: 51a088ccdcc018b85a70f72a5f88fab8de3c7363
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Az Azure AD application gallery több-bérlős alkalmazás hozzáadása

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az az Azure AD application gallery?

Azure Active Directory (Azure AD) egy olyan felhőalapú identitás-szolgáltatás. A [az Azure AD application gallery](https://azure.microsoft.com/marketplace/active-directory/all/) a Azure piactér app Store-ból, ahol egyszeri bejelentkezést és a felhasználók átadása közzétett összes alkalmazás-összekötő. Az ügyfelek, akik használhatja az Azure Active Directory identitás-szolgáltatóként a különböző SaaS alkalmazás összekötők közzétett itt találja. A rendszergazdák adja hozzá az alkalmazásgyűjtemény összekötők konfigurálása és az összekötők használata egyszeri bejelentkezést és üzembe helyezését. Az Azure AD összes fő összevonási protokollok, többek között a SAML 2.0, az OpenID Connect, a OAuth és a WS-Fed az egyszeri bejelentkezést támogatja. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Ha az alkalmazás támogatja az SAML-alapú vagy OpenIDConnect
Ha egy több-bérlős alkalmazást az Azure AD application gallery szerepel, először ellenőrizze, az alkalmazás támogatja a következő egyszeri bejelentkezési technológiák egyikét:

- **OpenID Connect**: ahhoz, hogy a felsorolt alkalmazások, a több-bérlős alkalmazás létrehozása az Azure ad-ben, és valósítja meg a [az Azure AD hozzájárulási keretrendszer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) az alkalmazáshoz. Így minden ügyfél biztosítani tudja az alkalmazás hozzájárul a bejelentkezési kérelem elküldése egy közös végpontot. Azt is szabályozhatja a felhasználó hozzáférését a bérlő azonosítója és a felhasználói UPN megkapta a jogkivonatot a alapján. Küldje el az alkalmazás ismertetett folyamatot követve [listázása az alkalmazást az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Ha az alkalmazás támogatja az SAML 2.0, az alkalmazás is listázva lehet a gyűjteményben. Kövesse az utasításokat a [listázása az alkalmazást az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Ha az alkalmazás nem támogatja az SAML-alapú vagy OpenIDConnect
Alkalmazások, amelyek nem támogatják az SAML-alapú vagy OpenIDConnect továbbra is integrálható az alkalmazásgyűjtemény jelszó egyszeri bejelentkezés technológia használatával.

Jelszó egyszeri bejelentkezést, más néven a jelszótárolást, lehetővé teszi a felhasználói hozzáférés és a webes alkalmazásokhoz, amelyek nem támogatják az identitás-összevonási jelszavak kezelése. Akkor célszerű is forgatókönyvek, amelyben több felhasználó meg szeretné osztani ugyanazt a fiókot, például a szervezet közösségi app fiókokhoz. 

Ha meg szeretné tekinteni a technológia az alkalmazásba:
1. Hozzon létre egy webalkalmazást, amely rendelkezik egy lap HTML-bejelentkezés konfigurálása [jelszó egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. A kérelmet a [listázása az alkalmazást az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Azok következményeinek

E-mail küldése a bármely olyan [Azure AD SSO integrációs csoport](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) és visszajelzünk Önnek a lehető leghamarabb.

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan [alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
