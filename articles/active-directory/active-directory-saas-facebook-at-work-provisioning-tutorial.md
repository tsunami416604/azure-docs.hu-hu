---
title: "Oktatóanyag: A felhasználók átadása által Facebook munkahelyi konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan automatikusan ellátásához, majd leépíti a felhasználói fiókok Azure ad-munkahelyi Facebook által."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a347eedbf5511dc83e1bc7721667441cfb87cb59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>Oktatóanyag: A felhasználók átadása által Facebook munkahelyi konfigurálása

Ez az oktatóanyag a lépéseket az Azure Active Directory (Azure AD) a munkahelyhez Facebook által automatikusan rendelkezni és deaktiválás rendelkezés felhasználói fiók szükséges.

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs munkahelyi által Facebook, a következőkre lesz szüksége:

- Az Azure AD szolgáltatásra
- A munkahelyi Facebook egyszeri bejelentkezés (SSO) által engedélyezett előfizetés

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, beszerezheti a [egy hónapos próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-workplace-by-facebook"></a>Felhasználók hozzárendelése munkahelyi által Facebook-on

Az Azure AD egy fogalom, más néven "hozzárendeléseket" alapján határozza meg, mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és a csoportokat, amelyek számára az Azure AD alkalmazás szinkronizálva.

A létesítési szolgáltatás engedélyezése és konfigurálása, előtt döntse el, mely felhasználók és csoportok az Azure AD határoz meg a felhasználókat, akik a Facebook-alkalmazást a munkahelyi hozzáférés szükséges. Majd rendelhet ezeket a felhasználókat a munkahelyi Facebook-alkalmazást a utasításait követve [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

>[!IMPORTANT]
>*   A létesítési konfiguráció tesztelése egyetlen hozzárendelésével által Facebook munkahelyi Azure AD-felhasználó. További felhasználók és csoportok hozzárendelése a később.
>*   A felhasználó által Facebook munkahelyi rendel, ki kell választania egy érvényes felhasználói szerepkörnek. Az alapértelmezett szerepkör nem működik történő üzembe helyezéséhez.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD csatlakozik a felhasználói fiók kiépítése munkahelyi API által a Facebook-on. Azt is megtudhatja, hogyan a létesítési szolgáltatás létrehozása, frissítése és tiltsa le a munkahelyi Facebook által hozzárendelt felhasználói fiókjainak konfigurálása. Ez a felhasználó és az Azure AD-csoport-hozzárendelés alapul.

>[!Tip]
>Másik lehetőségként engedélyezze SAML-alapú egyszeri bejelentkezés a munkahelyi által Facebook, az a megjelenő utasításokat követve a [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés automatikus kiépítés, függetlenül konfigurálhatók, abban az esetben, ha ez a két funkció egészítik ki egymást.

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Az Azure AD által Facebook munkahelyi kiépítés felhasználói fiók beállítása

Az Azure AD képes való automatikus szinkronizálása a munkahelyi Facebook által hozzárendelt felhasználói fiók adatait. Az automatikus szinkronizálás lehetővé teszi, hogy a munkahelyi hozzáférés előtt megpróbálja az első alkalommal jelentkeznek be a felhasználók hitelesítéséhez szükséges adatok megszerzéséhez Facebook által. Azt is deszerializálni látja el felhasználók a munkahely által Facebook amikor hozzáférés az Azure AD vissza lett vonva.

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás**.

2. Ha már beállította az egyszeri bejelentkezés munkahelyi által Facebook, keresse meg a munkahely által Facebook példányát keresési mező használatával. Máskülönben válassza **Hozzáadás** keresse meg a **által Facebook munkahelyi** az alkalmazás katalógusában. Válassza ki **által Facebook munkahelyi** a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. A munkahely által Facebook példányát, majd válassza ki és a **kiépítési** fülre.

4. Állítsa be **kiépítési üzemmódját** való **automatikus**. 

    ![Képernyőfelvétel a munkahelyi által Facebook üzembe helyezési lehetőségek](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. A a **rendszergazdai hitelesítő adataival** területen adja meg a **titkos Token** és a **bérlői URL-cím** a munkahelyének Facebook-rendszergazda.

6. Válassza ki az Azure-portálon **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat a munkahelyi Facebook-alkalmazást. Ha nem sikerül, győződjön meg arról, hogy a munkahelyi Facebook-fiókkal Team rendszergazdai jogosultságokkal rendelkezik.

7. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

8. Kattintson a **Mentés** gombra.

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók által Facebook munkahelyi**.

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok az Azure AD munkahelyi által szinkronizált Facebook-on. A kiválasztott attribútumok **egyező** tulajdonságok kell egyeznie a felhasználói fiókok, a munkahely által használt Facebook a frissítési műveletek. Véglegesítse a módosításokat, jelölje be **mentése**.

11. Ahhoz, hogy a munkahely által Facebook, a szolgáltatás kiépítését az Azure AD a **beállítások** területen módosítsa a **kiépítési állapot** való **a**.

12. Kattintson a **Mentés** gombra.

Automatikus kiépítés konfigurálásával kapcsolatos további információkért lásd: [a Facebook-dokumentáció](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

Mostantól létrehozhat egy olyan fiókot. Várjon 20 percig győződjön meg arról, hogy a munkahelyi fiók lett szinkronizálva a Facebook által.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-facebook-at-work-tutorial.md)

