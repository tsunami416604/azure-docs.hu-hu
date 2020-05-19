---
title: Külső identitások identitás-szolgáltatói – Azure AD
description: Azure Active Directory B2B-együttműködés támogatja a többtényezős hitelesítést (MFA) a vállalati alkalmazások szelektív eléréséhez
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395473e70137ead2b7185d54d165f82e9b3f1f04
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598149"
---
# <a name="identity-providers-for-external-identities"></a>Külső identitások identitás-szolgáltatói

Az *identitás-szolgáltató* identitási adatokat hoz létre, tart karban és felügyel, miközben hitelesítési szolgáltatásokat biztosít az alkalmazásoknak. Az alkalmazások és az erőforrások külső felhasználókkal való megosztásakor az Azure AD a megosztás alapértelmezett identitás-szolgáltatója. Ez azt jelenti, hogy ha olyan külső felhasználókat nyit meg, akik már rendelkeznek Azure AD-vel vagy Microsoft-fiókval, automatikusan bejelentkezhetnek a rész további konfigurálása nélkül.

Ugyanakkor engedélyezheti a felhasználók számára, hogy különböző identitás-szolgáltatókkal jelentkezzenek be. Beállíthatja például az Azure AD által támogatott közösségi identitás-szolgáltatókkal való összevonást, beleértve a Google-t és a Facebookot is. Az SAML-vagy WS-fed protokollokat támogató külső összevonása is elvégezheti. A külső identitás-szolgáltató összevonásával külső felhasználók is bejelentkezhetnek az alkalmazásaiba meglévő közösségi vagy vállalati fiókjaikkal.

## <a name="how-it-works"></a>Működés

Az Azure AD külső identitások előre konfigurálva vannak a Google és a Facebook szolgáltatással való összevonáshoz. Ha ezeket az identitás-szolgáltatókat az Azure AD-bérlőben szeretné beállítani, hozzon létre egy alkalmazást minden identitás-szolgáltatónál, és konfigurálja a hitelesítő adatokat. Szerezze be az ügyfél vagy az alkalmazás AZONOSÍTÓját, valamint az ügyfél vagy az alkalmazás titkos kulcsát, amelyet aztán hozzáadhat az Azure AD-bérlőhöz.

Miután hozzáadta az identitás-szolgáltatót az Azure AD-bérlőhöz:

- Ha egy külső felhasználót meghív a szervezet alkalmazásaira vagy erőforrásaira, a külső felhasználó a saját fiókját használva tud bejelentkezni az adott identitás-szolgáltatóval.
- Ha engedélyezi az [önkiszolgáló regisztrációt](self-service-sign-up-overview.md) az alkalmazásaihoz, a külső felhasználók a saját fiókjaikat a hozzáadott Identity Providers használatával regisztrálhatják az alkalmazásaikban. 

Ha beváltja a meghívót, vagy regisztrál az alkalmazásra, a külső felhasználónak lehetősége van bejelentkeznie, és hitelesítenie magát a közösségi identitás-szolgáltatóval:

![A bejelentkezési képernyőt a Google és a Facebook lehetőségeivel ábrázoló képernyőkép](media/identity-providers/sign-in-with-social-identity.png)

Az optimális bejelentkezési élmény érdekében a összevonása, amikor csak lehetséges, a meghívott vendégeknek zökkenőmentes bejelentkezési élményt biztosíthat az alkalmazásai eléréséhez.  

## <a name="next-steps"></a>További lépések

Az alábbi cikkekből megtudhatja, hogyan adhat identitás-szolgáltatókat az alkalmazásaiba való bejelentkezéshez:

- A [Google hozzáadása](google-federation.md) a közösségi identitású szolgáltatók listájához
- [Facebook hozzáadása](facebook-federation.md) a közösségi identitású szolgáltatók listájához
- [Közvetlen összevonás beállítása](direct-federation.md) bármely olyan szervezettel, amelynek az identitás-szolgáltatója támogatja az SAML 2,0 vagy a WS-fed protokollt. Vegye figyelembe, hogy a közvetlen összevonás nem választható az önkiszolgáló bejelentkezési felhasználói folyamatok számára.
