---
title: Vállalati munkahelyi fiókok és a Partnerközpont
description: Ellenőrizze, hogy a vállalat rendelkezik-e egy munkahelyi fiókot állítsa be a Microsoft, hozzon létre egy új munkahelyi fiókot, vagy állítsa be a munkahelyi fiókok használata a Partner Center módja.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: df8ab370e8874e07f8985ecfb3a772848a2e2b21
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806281"
---
# <a name="company-work-accounts-and-partner-center"></a>Vállalati munkahelyi fiókok és a Partnerközpont

Partnerközpont vállalati munkahelyi fiókokat, más néven az Azure Active Directory (AD) bérlő, fiókhozzáférés egyszerre több felhasználó, szabályozhatja az engedélyeket, csoportok és alkalmazások felügyeletéhez és a profiladatok karbantartásához használja. A vállalati munkahelyi e-mail-fiók tartománya létrehozhatja, a Partnerközpont-fiókkal, a vállalat alkalmazottai jelentkezhetnek be a Partner Center marketplace-ajánlat a saját munkahelyi fiókok felhasználónevei és jelszavai használata kezelheti.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Ellenőrizze, hogy a vállalat már rendelkezik munkahelyi fiókkal

Ha a vállalat előfizetett egy Microsoft felhőszolgáltatásra, például Azure, Microsoft Intune vagy Office 365-höz, akkor már rendelkezik egy munkahelyi e-mail fiók tartománya (más néven az Azure Active Directory-bérlő), amelyek együtt Partner Center.

Ellenőrizze az alábbi lépéseket követve:
1. Jelentkezzen be az Azure felügyeleti portálon, a https://portal.azure.com.
2. Válassza ki **Azure Active Directory** a bal oldali navigációs menüben, és válassza ki a **egyéni tartománynevek**.
3. Ha már rendelkezik munkahelyi fiókkal, a tartománynév jelennek meg.

Ha a vállalat még nem rendelkezik munkahelyi fiókkal, egy létrejön az Ön számára a Partnerközpont regisztrációs folyamat során.

## <a name="set-up-multiple-work-accounts"></a>Állítsa be a munkahelyi fiókok

Mielőtt egy meglévő munkahelyi fiók használata mellett dönt, fontolja meg, hány felhasználó a munkahelyi fiók Partnerközpont elérhetőnek kell. Ha a felhasználók a munkahelyi fiók Partnerközpont elérni kívánó nem rendelkezik, érdemes több munkahelyi fiókokat, célszerű lehet, hogy csak az adott felhasználók, akik Partnerközpont elérhetőnek kell szerepelnek az adott fiókban.

## <a name="create-a-new-work-account"></a>Hozzon létre egy új munkahelyi fiókot

Hozzon létre egy új munkahelyi fiókot a vállalat számára, kövesse az alábbi lépéseket. Segítségkérés a személy, aki rendszergazdai jogosultságokkal rendelkezik a vállalata a Microsoft Azure-fiók szükségessé.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).
2. A bal oldali navigációs menüben válassza ki a **Azure Active Directory** -> **felhasználók**.
3. Válassza ki **új felhasználó** , és hozzon létre egy új Azure munkahelyi fiók és az e-mail-címét. Ügyeljen arra, hogy a **címtárbeli szerepkör** értékre van állítva **felhasználói** , és válassza ki a **jelszó megjelenítése** jelölőnégyzet alján megtekintheti, és jegyezze fel az automatikusan létrehozott jelszó.
4. Válassza ki **létrehozás** menteni az új felhasználót.

Az a felhasználói fiókhoz tartozó e-mail-cím egy ellenőrzött tartomány nevét, a címtárban kell lennie. Listázhatja az összes ellenőrzött tartomány a címtár kiválasztásával **Azure Active Directory** -> **egyéni tartománynevek** a bal oldali navigációs menüben.

Az Azure Active Directoryban egyéni tartományok hozzáadásával kapcsolatos további tudnivalókért lásd: [hozzáadása vagy az Azure AD-tartomány társítása](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Munkahelyi e-mail-bejelentkezési hibaelhárítása

Ha nem tud bejelentkezni a munkahelyi fiókjával (más néven az Azure AD-bérlő), keresse meg a forgatókönyvet a diagram alá, amely a legjobban megfelel az adott helyzethez, és kövesse a javasolt lépéseket.

![Munkahelyi fiók bejelentkezési hibaelhárítási diagramja](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>További lépések

- [A Partner Center piactér kereskedelmi fiók kezelése](./manage-account.md) 
