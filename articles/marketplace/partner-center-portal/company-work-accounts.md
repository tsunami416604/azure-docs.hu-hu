---
title: Vállalati munkaszámlák és partnerközpont
description: Annak ellenőrzése, hogy vállalata rendelkezik-e a Microsofttal létrehozott munkahelyi fiókkal, hozzon-e létre új munkahelyi fiókot, vagy állítson be több munkahelyi fiókot a Partnerközponttal való használatra.
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: c4e7427d87c5f88d8c686b867ef88ceb05f28286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281425"
---
# <a name="company-work-accounts-and-partner-center"></a>Vállalati munkaszámlák és partnerközpont

A Partnerközpont vállalati munkafiókokat, más néven Az Azure Active Directory (AD) bérlőit használja a fiókhozzáférés kezelésére több felhasználó számára, az engedélyek szabályozására, a gazdacsoportokra és alkalmazásokra, valamint a profiladatok karbantartására. Ha a vállalat munkahelyi e-mail-fiókjának tartományát a Partnerközpont-fiókhoz kapcsolja, a vállalat alkalmazottai bejelentkezhetnek a Partnerközpontba, hogy saját munkahelyi fiókjuk felhasználóneveivel és jelszavaikkal kezeljék a piactéri ajánlatokat.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Annak ellenőrzése, hogy vállalata rendelkezik-e már munkahelyi fiókkal

Ha vállalata előfizetett egy Microsoft felhőszolgáltatásra, például az Azure-ra, a Microsoft Intune-ra vagy az Office 365-re, akkor már rendelkezik egy munkahelyi e-mail fiók-tartománnyal (más néven Azure Active Directory-bérlőként), amely használható a PartnerCenterrel.

Az ellenőrzéshez kövesse az alábbi lépéseket:
1. Jelentkezzen be az Azure https://portal.azure.comfelügyeleti portálra a.
2. Válassza az **Azure Active Directory** lehetőséget a bal oldali navigációs menüből, majd válassza az Egyéni **tartománynevek lehetőséget**.
3. Ha már rendelkezik munkahelyi fiókkal, a tartományneve megjelenik a listában.

Ha a vállalat még nem rendelkezik munkahelyi fiókkal, akkor a Partnerközpont regisztrációs folyamata során létrejön egy.

## <a name="set-up-multiple-work-accounts"></a>Több munkaszámla beállítása

Mielőtt úgy dönt, hogy egy meglévő munkahelyi fiókot használ, fontolja meg, hogy a munkahelyi fiók hány felhasználójának kell hozzáférnie a Partnerközponthoz. Ha a munkahelyi fiókban olyan felhasználók vannak, akiknek nem kell hozzáférniük a Partnerközponthoz, érdemes megfontolnia több munkahelyi fiók létrehozását, hogy csak azok a felhasználók jelenjenek meg egy adott fiókban, akiknek hozzá kell férniük a Partnerközponthoz.

## <a name="create-a-new-work-account"></a>Új munkahelyi fiók létrehozása

Ha új munkafiókot szeretne létrehozni a vállalatszámára, kövesse az alábbi lépéseket. Előfordulhat, hogy segítséget kell kérnie avállalat Microsoft Azure-fiókjához rendszergazdai jogosultságokkal rendelkező kit.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).
2. A bal oldali navigációs menüben válassza az **Azure Active Directory** -> **felhasználói**lehetőséget.
3. Válassza az **Új felhasználó** lehetőséget, és hozzon létre egy új Azure-munkahelyi fiókot egy név és e-mail cím megadásával. Győződjön meg arról, hogy a **Címtár szerepkör** **felhasználóra** van állítva, és jelölje be a **Jelszó megjelenítése** jelölőnégyzetet az alján az automatikusan létrehozott jelszó megtekintéséhez és jegyzeteléséhez.
4. Az új felhasználó mentéséhez válassza a **Létrehozás** gombot.

A felhasználói fiók e-mail címének ellenőrzött tartománynévnek kell lennie a címtárban. A bal oldali navigációs menüben az **Azure Active Directory** -> egyéni tartománynevek kiválasztásával listázhatja a címtárban lévő összes ellenőrzött**tartományt.**

Ha többet szeretne tudni arról, hogy miként vehet fel egyéni tartományokat az Azure Active Directoryban, olvassa [el a Tartomány hozzáadása vagy társítása az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)lehetőséget.

## <a name="troubleshoot-work-email-sign-in"></a>Munkahelyi e-mail bejelentkezés – problémamegoldás

Ha nem sikerül bejelentkeznia a munkahelyi fiókjába (más néven az Azure AD-bérlőbe), keresse meg az alábbi ábrán látható forgatókönyvet, amely a legjobban megfelel a helyzetnek, és kövesse az ajánlott lépéseket.

![Munkahelyi fiókba való bejelentkezés hibaelhárítási diagram](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>További lépések

- [Kereskedelmi piactér-fiók kezelése a Partnerközpontban](./manage-account.md) 
