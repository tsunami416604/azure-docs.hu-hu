---
title: Több-bérlős alkalmazás hozzáadása az Azure AD Application Galleryhez | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy miként listázható a saját fejlesztésű több-bérlős alkalmazás az Azure AD Application Galleryben.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8024130b986d111abe3b470d6b06e86ed6419dc4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320917"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Több-bérlős alkalmazás hozzáadása az Azure AD Application Galleryhez

## <a name="what-is-the-azure-ad-application-gallery"></a>Mi az Azure AD Application Gallery?

A Azure Active Directory (Azure AD) egy felhőalapú identitás-szolgáltatás. Az [Azure ad-alkalmazás](https://azure.microsoft.com/marketplace/active-directory/all/) katalógusa az Azure Marketplace App Store-ban található, ahol az összes alkalmazás-összekötő közzé van téve az egyszeri bejelentkezéshez és a felhasználók üzembe helyezéséhez. Az Azure AD-t identitás-szolgáltatóként használó ügyfeleink megtalálják az itt közzétett különböző SaaS-alkalmazás-összekötőket. A rendszergazdák hozzáadhatnak összekötőket az alkalmazás-katalógusból, majd konfigurálhatják és használhatják az összekötőket az egyszeri bejelentkezéshez és az üzembe helyezéshez. Az Azure AD az összes jelentősebb összevonási protokollt támogatja, beleértve az SAML 2,0, az OpenID Connect, a OAuth és a WS-fed használatát az egyszeri bejelentkezéshez. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Ha az alkalmazás támogatja az SAML-t vagy a OpenIDConnect-t
Ha rendelkezik egy több-bérlős alkalmazással, amelyet az Azure AD-alkalmazás-katalógusban szeretne felvenni, először győződjön meg arról, hogy az alkalmazás támogatja a következő egyszeri bejelentkezési technológiák egyikét:

- **OpenID Connect**: Az alkalmazás listájának létrehozásához hozza létre a több-bérlős alkalmazást az Azure AD-ben, és implementálja az [Azure ad-beli engedélyezési keretrendszert](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) az alkalmazáshoz. Küldje el a bejelentkezési kérést egy közös végpontra, hogy bármely ügyfél beleegyezik az alkalmazásba. A felhasználó hozzáférését a bérlő azonosítója és a jogkivonatban kapott felhasználó egyszerű felhasználóneve alapján szabályozhatja. Küldje el az alkalmazást az [alkalmazás listázása az Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című részében ismertetett eljárással.

- **SAML**: Ha az alkalmazás támogatja az SAML 2,0-et, az alkalmazás a katalógusban is szerepelhet. Kövesse az [alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakör utasításait.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Ha az alkalmazás nem támogatja az SAML-t vagy a OpenIDConnect-t
Az SAML-vagy OpenIDConnect-t nem támogató alkalmazások továbbra is integrálhatók az alkalmazás-katalógusba a jelszó egyszeri bejelentkezési technológiájának használatával.

A jelszavas egyszeri bejelentkezés, más néven a jelszó-tároló, lehetővé teszi a felhasználói hozzáférés és jelszavak kezelését az identitás-összevonást nem támogató webalkalmazásokhoz. Olyan helyzetekben is hasznos, amikor több felhasználónak egyetlen fiókot kell megosztania, például a szervezete közösségi Media app-fiókjaival. 

Ha az alkalmazást a következő technológiával szeretné listázni:
1. Hozzon létre egy webalkalmazást, amely tartalmaz egy HTML-bejelentkezési oldalt a [jelszó-egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)konfigurálásához. 
2. Küldje el a kérelmet az [alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakörben leírtak szerint.

## <a name="escalations"></a>Azok következményeinek

Bármilyen eszkaláció esetén küldjön e-mailt az [Azure ad SSO integrációs csapatának](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) , és a lehető leghamarabb visszakerülünk Önnek.

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan listázhatja az [alkalmazást az Azure Active Directory alkalmazás-](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)katalógusban.
