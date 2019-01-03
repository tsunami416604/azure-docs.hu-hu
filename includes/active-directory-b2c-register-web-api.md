---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: fcd71f74e0b00934958828024094773e42496b66
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797975"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Webes API-ja regisztrálásához használja a táblázatban megadott beállításokat.

![Példák az új webes API regisztrációs beállításaira](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Beállítás      | Mintaérték  | Leírás                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name (Név)** | Contoso B2C API | Adjon meg egy olyan **nevet**, amely ismerteti az API-t a felhasználók számára. | 
| **Webalkalmazás vagy webes API szerepeltetése** | Igen | Válassza az **Igen** lehetőséget a webes API-k esetén. |
| **Implicit folyamat engedélyezése** | Igen | Válassza az **Igen** lehetőséget, ha az alkalmazása [OpenID Connect bejelentkezést](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) használ. |
| **Válasz URL-cím** | `https://localhost:44316/` | A válasz URL-címek olyan végpontok, amelyeken keresztül az Azure AD B2C visszaadja az alkalmazás által kért jogkivonatokat. Ebben a példában a webes API helyi, és a 44316-os porton figyel. |
| **Alkalmazásazonosító URI** | api-t | Az alkalmazásazonosító URI a webes API-hoz használt azonosító. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. |

Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.

Az újonnan regisztrált alkalmazás a B2C-bérlő alkalmazásainak listájában jelenik meg. Válassza ki webes API-ját a listáról. Ekkor megjelenik az API tulajdonságpanelje.

![Webes API – Tulajdonságok](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Jegyezze fel az alkalmazás globálisan egyedi **ügyfél-azonosítóját**. Ezt az azonosítót használja az alkalmazás kódjában.