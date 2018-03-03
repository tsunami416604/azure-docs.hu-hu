---
title: "Adja hozzá az Azure Active Directory B2B együttműködés felhasználó egy szerepkörhöz |} Microsoft Docs"
description: "A Vendég felhasználó hozzáadása egy szerepkört az Azure Active Directoryban"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b788b003299ff11bcbdcb11a2c7a21ac5081d634
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Felhasználói engedélyek kiosztása a fiókpartner-szervezetek, az Azure Active Directory-bérlőben

Az Azure Active Directory (Azure AD) B2B együttműködés felhasználók hozzá szeretné adni vendégfelhasználók számára a könyvtárba, és a címtárban Vendég engedélye korlátozott alapértelmezés szerint. A vállalat esetleg néhány vendégfelhasználók a szervezet magasabb szintű szerepkörök betöltésére. Magasabb jogosultsági szerepkörök definiálása támogatásához vendégfelhasználók egyetlen szerepkörhöz sem felügyelni, a szervezet igényei alapján is hozzáadhatók.

## <a name="default-role"></a>alapértelmezett szerepkör

![alapértelmezett szerepkör](./media/active-directory-b2b-add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Globális rendszergazdai szerepkörrel

![globális rendszergazdai szerepkörrel](./media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Korlátozott rendszergazdai szerepkör

![korlátozott rendszergazdai szerepkörrel](./media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>További lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B együttműködés felhasználó tulajdonságai](active-directory-b2b-user-properties.md)
* [B2bB együttműködés meghívókat delegálása](active-directory-b2b-delegate-invitations.md)
* [Dinamikus csoportok és a B2B együttműködés](active-directory-b2b-dynamic-groups.md)
* [B2B együttműködés kód és a PowerShell-példák](active-directory-b2b-code-samples.md)
* [B2B együttműködés SaaS-alkalmazások konfigurálása](active-directory-b2b-configure-saas-apps.md)
* [B2B együttműködés felhasználói jogkivonatokhoz](active-directory-b2b-user-token.md)
* [B2B együttműködés felhasználói jogcímek leképezése](active-directory-b2b-claims-mapping.md)
* [Külső Office 365-megosztás](active-directory-b2b-o365-external-user.md)
* [B2B együttműködés aktuális korlátozásai](active-directory-b2b-current-limitations.md)
