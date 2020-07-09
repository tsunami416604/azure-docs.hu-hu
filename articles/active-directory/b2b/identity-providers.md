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
ms.openlocfilehash: 2284d015b451872753dd0855cac42e6f1926545c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83712162"
---
# <a name="identity-providers-for-external-identities"></a>Külső identitások identitás-szolgáltatói

Az *identitás-szolgáltató* identitási adatokat hoz létre, tart karban és felügyel, miközben hitelesítési szolgáltatásokat biztosít az alkalmazásoknak. Az alkalmazások és az erőforrások külső felhasználókkal való megosztásakor az Azure AD a megosztás alapértelmezett identitás-szolgáltatója. Ez azt jelenti, hogy ha olyan külső felhasználókat nyit meg, akik már rendelkeznek Azure AD-vel vagy Microsoft-fiókval, automatikusan bejelentkezhetnek a rész további konfigurálása nélkül.

Ugyanakkor engedélyezheti a felhasználók számára, hogy különböző identitás-szolgáltatókkal jelentkezzenek be.

- **Google**: a Google Federation lehetővé teszi, hogy a külső felhasználók meghívókat váltsanak be az alkalmazásaiba saját Gmail-fiókkal való bejelentkezéssel. A Google Federation is használható az önkiszolgáló bejelentkezési felhasználói folyamatokban.
   > [!NOTE]
   > Ha a jelenlegi önkiszolgáló regisztrációs előzetes verzióban felhasználói folyamat van társítva egy alkalmazáshoz, és a felhasználónak egy meghívót küld az alkalmazásnak, a felhasználó nem fog tudni Gmail-fiókot használni a meghívó beváltásához. Megkerülő megoldásként a felhasználó átléphet az önkiszolgáló bejelentkezési folyamaton. Vagy a meghívást beválthatják egy másik alkalmazáshoz való hozzáféréssel vagy a saját alkalmazások portálján a következő címen: https://myapps.microsoft.com .

- **Facebook**: egy alkalmazás létrehozásakor konfigurálhatja az önkiszolgáló regisztrációt, és engedélyezheti a Facebook-összevonás használatát, hogy a felhasználók saját Facebook-fiókjaik használatával regisztrálhatják az alkalmazást. A Facebook csak önkiszolgáló bejelentkezési felhasználói folyamatokhoz használható, és nem érhető el bejelentkezési lehetőségként, ha a felhasználók meghívókat váltanak ki.

- **Közvetlen összevonás**: az SAML-vagy ws-fed protokollokat támogató külső identitás-szolgáltatóval is beállíthat közvetlen összevonást. A közvetlen összevonás lehetővé teszi, hogy a külső felhasználók meghívókat váltsanak be az alkalmazásaiba a meglévő közösségi vagy vállalati fiókkal való bejelentkezéssel. 
   > [!NOTE]
   > A közvetlen összevonási azonosítók nem használhatók önkiszolgáló bejelentkezési felhasználói folyamatokban.


## <a name="how-it-works"></a>Működés

Az Azure AD külső identitások önkiszolgáló regisztrációs funkciója lehetővé teszi a felhasználók számára, hogy regisztráljanak az Azure AD-, Google-vagy Facebook-fiókjával. Ha az Azure AD-bérlőben szeretné beállítani a közösségi identitás-szolgáltatókat, hozzon létre egy alkalmazást minden identitás-szolgáltatónál, és konfigurálja a hitelesítő adatokat. Szerezze be az ügyfél vagy az alkalmazás AZONOSÍTÓját, valamint az ügyfél vagy az alkalmazás titkos kulcsát, amelyet aztán hozzáadhat az Azure AD-bérlőhöz.

Miután hozzáadta az identitás-szolgáltatót az Azure AD-bérlőhöz:

- Ha egy külső felhasználót meghív a szervezet alkalmazásaira vagy erőforrásaira, a külső felhasználó a saját fiókját használva tud bejelentkezni az adott identitás-szolgáltatóval.
- Ha engedélyezi az [önkiszolgáló regisztrációt](self-service-sign-up-overview.md) az alkalmazásaihoz, a külső felhasználók a saját fiókjaikat a hozzáadott Identity Providers használatával regisztrálhatják az alkalmazásaikban.

> [!NOTE]
> Az Azure AD alapértelmezés szerint engedélyezve van az önkiszolgáló regisztrációhoz, így a felhasználóknak mindig lehetősége van regisztrálni egy Azure AD-fiók használatával.

Ha beváltja a meghívót, vagy regisztrál az alkalmazásra, a külső felhasználónak lehetősége van bejelentkeznie, és hitelesítenie magát a közösségi identitás-szolgáltatóval:

![A bejelentkezési képernyőt a Google és a Facebook lehetőségeivel ábrázoló képernyőkép](media/identity-providers/sign-in-with-social-identity.png)

Az optimális bejelentkezési élmény érdekében a összevonása, amikor csak lehetséges, a meghívott vendégeknek zökkenőmentes bejelentkezési élményt biztosíthat az alkalmazásai eléréséhez.  

## <a name="next-steps"></a>További lépések

Az alábbi cikkekből megtudhatja, hogyan adhat identitás-szolgáltatókat az alkalmazásaiba való bejelentkezéshez:

- A [Google hozzáadása](google-federation.md) a közösségi identitású szolgáltatók listájához
- [Facebook hozzáadása](facebook-federation.md) a közösségi identitású szolgáltatók listájához
- [Közvetlen összevonás beállítása](direct-federation.md) bármely olyan szervezettel, amelynek az identitás-szolgáltatója támogatja az SAML 2,0 vagy a WS-fed protokollt. Vegye figyelembe, hogy a közvetlen összevonás nem választható az önkiszolgáló bejelentkezési felhasználói folyamatok számára.
