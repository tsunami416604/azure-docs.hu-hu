---
title: OpenID Connect-hitelesítés Azure Active Directory
description: A hitelesítési minta megvalósítására szolgáló építészeti útmutató
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f2ad3b5e86eebfc2d6f1f42f8a2ab0520144b5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114207"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>OpenID Connect-hitelesítés Azure Active Directory

Az OpenID Connect (OIDC) a OAuth2 protokollon alapuló hitelesítési protokoll (amely az engedélyezéshez használatos). A OIDC a OAuth2 szabványosított üzeneteit használja az Identity Services biztosításához. 

A OIDC tervezési célja, hogy egyszerű és bonyolult dolgok legyenek. A OIDC lehetővé teszi a fejlesztők számára, hogy a jelszavakat a felhasználók és a fájlok kezelése nélkül hitelesítsék a webhelyeken és az alkalmazásokban. Ez biztosítja az App Builder számára, hogy biztonságos módon ellenőrizze annak a személynek az identitását, aki jelenleg az alkalmazáshoz kapcsolódó böngészőt vagy natív alkalmazást használja.

A felhasználó hitelesítését olyan identitás-szolgáltatón kell végrehajtani, ahol a felhasználó munkamenetét vagy hitelesítő adatait ellenőrizni fogja. Ehhez megbízható ügynökre van szükség. A natív alkalmazások általában erre a célra indítják a rendszerböngészőt. A beágyazott nézetek nem tekinthetők megbízhatónak, mivel nem lehet megakadályozni, hogy az alkalmazás a felhasználói jelszó megadását meggátolja. 

A hitelesítésen kívül a felhasználót is megkérheti a felhasználótól. A hozzájárulás a felhasználó explicit engedélye, amely lehetővé teszi az alkalmazások számára a védett erőforrások elérését. A beleegyezett hozzáférés nem azonos a hitelesítéssel, mert az adott erőforráshoz csak egyszer kell megadnia az engedélyt. A jóváhagyás addig érvényes marad, amíg a felhasználó vagy a rendszergazda manuálisan vissza nem vonja a jogosultságot. 

## <a name="use-when"></a>A következő esetekben használja

Felhasználói hozzáférésre és webes bejelentkezésre van szükség.

![építészeti diagram](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: szolgáltatás kérése az alkalmazástól.

* **Megbízható ügynök**: az az összetevő, amellyel a felhasználó kommunikál. Ez a megbízható ügynök általában egy webböngésző.

* **Application**(alkalmazás): az alkalmazás vagy az erőforrás-kiszolgáló, ahol az erőforrás vagy az adat található. Megbízhatónak tartja az identitás-szolgáltatót a megbízható ügynök biztonságos hitelesítéséhez és engedélyezéséhez. 

* **Azure ad**: a OIDC-szolgáltató, más néven az identitás-szolgáltató, biztonságosan felügyeli a felhasználó adatait, hozzáférését, valamint a folyamat részes felei közötti megbízhatósági kapcsolatokat. Hitelesíti a felhasználó identitását, engedélyezi és visszavonja az erőforrásokhoz való hozzáférést, és jogkivonatokat állít ki. 

## <a name="implement-oidc-with-azure-ad"></a>A OIDC megvalósítása az Azure AD-vel

* [Alkalmazások integrálása az Azure AD segítségével](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Microsoft Identity platform és OpenID Connect protokoll](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 

* [Webes bejelentkezés OpenID-kapcsolattal Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/openid-connect) 

* [Alkalmazás biztonságossá tétele az OpenID Connect és az Azure AD használatával](https://docs.microsoft.com/learn/modules/secure-app-with-oidc-and-azure-ad/) 

 
