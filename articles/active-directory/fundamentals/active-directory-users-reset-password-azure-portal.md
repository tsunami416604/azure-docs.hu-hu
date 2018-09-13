---
title: Új jelszó kérését az Azure Active Directoryban |} A Microsoft Docs
description: Rendszergazda által kezdeményezett jelszó-visszaállítást egy felhasználó Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
editor: ''
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: 689ab264e56bc8559db6237eee19566e9e3c429f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645420"
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Az Azure Active Directory-felhasználó jelszavának alaphelyzetbe állítása

A rendszergazdák a felhasználói jelszó azokban az esetekben, ahol azok elfelejtette, előfordulhat, hogy szükség kizárt kijelentkezés vagy más forgatókönyvekhez. A következő lépések végigvezetik a jelszó alaphelyzetbe állítása.

## <a name="how-to-reset-the-password-for-a-user"></a>A felhasználó jelszavának alaphelyzetbe állítása

1. Jelentkezzen be a [Azure AD felügyeleti központban](https://aad.portal.azure.com) egy olyan fiókkal, amely directory jogosult felhasználók új jelszavainak létrehozására.
2. Válassza ki **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki a felhasználó új jelszót szeretne.
2. Válassza ki a kiválasztott felhasználó **jelszó alaphelyzetbe állítása**.

    ![Az Azure ad-ben egy felhasználó a felhasználói profilt a jelszó alaphelyzetbe állítása](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. A **jelszó alaphelyzetbe állítása**válassza **jelszó alaphelyzetbe állítása**.
7. Egy ideiglenes jelszót, majd megadhatja a felhasználónak jelenik meg. A felhasználó, majd módosítsa a jelszavát a következő alkalommal, amikor azok bejelentkezési kell adnia. 

   > [!NOTE]
   > Ideiglenes jelszó nem rendelkezik lejárati időt, érvényes lesz mindaddig, amíg azok jelentkezzen be, és a rendszer majd kényszerítve a módosításhoz. 

## <a name="next-steps"></a>További lépések
* [Felhasználó hozzáadása](../add-users-azure-active-directory.md)
* [Rendszergazdai szerepkörök hozzárendelése egy felhasználóhoz](active-directory-users-assign-role-azure-portal.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [Felhasználó törlése az Azure ad-ben](../add-users-azure-active-directory.md)
