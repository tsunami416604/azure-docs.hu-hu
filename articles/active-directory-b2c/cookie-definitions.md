---
title: Cookie-definíciók – Azure Active Directory B2C |} A Microsoft Docs
description: Az Azure Active Directory B2C-vel használt cookie-kra vonatkozó definíciókat biztosítja.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.component: B2C
ms.openlocfilehash: 1de1734d791608f3262d2af70becc2e082c9f317
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511152"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Cookie-k Azure Active Directory B2C-definíciói

Az alábbi táblázat a cookie-kat az Azure Active Directory B2C-t használja.

| Name (Név) | Domain | lejárati | Cél |
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

