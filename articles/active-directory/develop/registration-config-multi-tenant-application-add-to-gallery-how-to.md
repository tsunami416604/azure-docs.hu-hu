---
title: Több-bérlős alkalmazás hozzáadása az Azure AD-alkalmazásgyűjteményhez
description: Bemutatja, hogyan sorolhatja fel az egyéni fejlesztésű több-bérlős alkalmazást az Azure AD-alkalmazásgyűjteményben.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.openlocfilehash: 90f4d22ba782fd71460595f69a52e70be87a3047
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883253"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Több-bérlős alkalmazás hozzáadása az Azure AD-alkalmazásgyűjteményhez

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az Azure AD alkalmazáskatalógus?

Az Azure Active Directory (Azure AD) egy felhőalapú identitásszolgáltatás. Az [Azure AD-alkalmazáskatalógus](https://azure.microsoft.com/marketplace/active-directory/all/) az Azure Marketplace-alkalmazásboltban található, ahol az összes alkalmazás-összekötő közzé van téve az egyszeri bejelentkezéshez és a felhasználói kiépítéshez. Az Azure AD-t identitásszolgáltatóként használó ügyfelek megtalálják az itt közzétett különböző SaaS-alkalmazás-összekötőket. A rendszergazdák az alkalmazáskatalógusból adnak hozzá összekötőket, majd konfigurálják és használják az összekötőket egyszeri bejelentkezéshez és kiépítéshez. Az Azure AD támogatja az összes főbb összevonási protokollt, beleértve az SAML 2.0, az OpenID Connect, az OAuth és a WS-Fed egyszeri bejelentkezést. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Ha az alkalmazás támogatja az SAML vagy az OpenIDConnect
Ha egy több-bérlős alkalmazás, amely szeretne szerepelni az Azure AD-alkalmazáskatalógusban, először meg kell győződnie arról, hogy az alkalmazás támogatja az alábbi egyszeri bejelentkezési technológiák egyikét:

- **OpenID Connect:** Az alkalmazás listázásához hozza létre a több-bérlős alkalmazást az Azure AD-ben, és valósítsa meg az [Azure AD jóváhagyási keretrendszert](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) az alkalmazáshoz. Küldje el a bejelentkezési kérelmet egy közös végpontra, hogy bármely ügyfél beleegyezését adja az alkalmazáshoz. Szabályozhatja a felhasználó hozzáférését a bérlői azonosító és a jogkivonatban kapott felhasználói upn alapján. Küldje el az alkalmazást az [alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című listában ismertetett folyamat használatával.

- **SAML**: Ha az alkalmazás támogatja az SAML 2.0-s, az alkalmazás felsorolható a galériában. Kövesse az [alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című útmutató utasításait.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Ha az alkalmazás nem támogatja az SAML vagy az OpenIDConnect
Az SAML-t vagy OpenIDConnect-et nem támogató alkalmazások továbbra is integrálhatók az alkalmazásgalériába a jelszó egyszeri bejelentkezési technológiával.

Jelszó egyszeri bejelentkezés, más néven jelszó tároló, lehetővé teszi, hogy kezelje a felhasználói hozzáférés és a jelszavakat a webes alkalmazások, amelyek nem támogatják az identitás-összevonás. Olyan esetekben is hasznos, amikor több felhasználónak kell megosztania egyetlen fiókot, például a szervezet közösségi média alkalmazásfiókjaival. 

Ha ezzel a technológiával szeretné felsorolni az alkalmazást:
1. Hozzon létre egy HTML bejelentkezési lapot tartalmazó webalkalmazást a [jelszó egyszeri bejelentkezésének](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)konfigurálásához. 
2. Küldje el a kérelmet az [alkalmazás listázása az Azure Active Directory alkalmazásgalériában](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című dokumentumban leírtak szerint.

## <a name="escalations"></a>Torkolljon

Az esetleges eszkalációk esetén küldjön e-mailt [az Azure AD SSO-integrációs csapatnak,](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) és a lehető leghamarabb visszahívjuk.

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan [listázhatja az alkalmazást az Azure Active Directory alkalmazásgalériában.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
