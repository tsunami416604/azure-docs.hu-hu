---
title: Cookie-definíciók – Azure Active Directory B2C |} A Microsoft Docs
description: Az Azure Active Directory B2C-vel használt cookie-kra vonatkozó definíciókat biztosítja.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ac422a00a919903063c96ac096882036b99a63e3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786744"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Cookie-k Azure Active Directory B2C-definíciói

Az alábbi táblázat a cookie-kat az Azure Active Directory B2C-t használja.

| Name (Név) | Domain | Lejárat | Cél |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md) | Tárolja a felhasználó csoporttagsági adatai bérlők között. A bérlők egy felhasználó tagja, és a szolgáltatói tagságot (a rendszergazda vagy a felhasználó). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, branded domain | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md) | Használja a megfelelő üzemi példány átirányíthatja a kéréseket. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, branded domain | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md) | A tranzakciók (Azure AD B2C a hitelesítési kérések száma) és az aktuális tranzakció nyomon követésére használt. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, branded domain | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md) | Használja az egyszeri bejelentkezési munkamenetek fenntartását. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, branded domain | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md), a sikeres hitelesítés | A kérés állapot fenntartására használható. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, branded domain | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md) | Webhelyközi kérések hamisításának megakadályozása token CRSF védelemhez használt. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, branded domain | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md) | Azure AD B2C-hálózati útválasztáshoz használt. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, branded domain | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md) | Környezet |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, branded domain | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md) | Az erőforrás-szolgáltató bérlő tagsági adatai tárolására szolgál. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, branded domain | Az End [böngésző-munkamenet](active-directory-b2c-token-session-sso.md) | A relay cookie-k tárolására szolgál. |

