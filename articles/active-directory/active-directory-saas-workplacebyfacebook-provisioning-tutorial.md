---
title: 'Oktatóanyag: Által Facebook munkahelyi konfigurálása az automatikus felhasználó-átadási az Azure Active Directoryval |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a munkahely által Facebook között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: d42a6b63833bffc3cce1830a728ec21083b25dc9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés által Facebook munkahelyi konfigurálása

Ez az oktatóanyag célja a lépéseket kell elvégeznie a Facebook-on és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad által Facebook munkahelyi munkaterületen mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs munkahelyi által Facebook, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A munkahelyi által Facebook egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Felhasználók hozzárendelése a munkahely által Facebook-on

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználókat, akik a Facebook-alkalmazást a munkahelyi hozzáférés szükséges. Ha úgy döntött, rendelhet ezeket a felhasználókat a munkahelyi Facebook alkalmazást itt cikk utasításait követve:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Felhasználók hozzárendelése a munkahely által Facebook fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó a munkahelyhez által hozzárendelt Facebook teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   A felhasználó által Facebook munkahelyi hozzárendelésekor ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD munkahelyi csatlakozás Facebook a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a munkahelyi Facebook alapján a felhasználók és csoportok hozzárendelése az Azure AD által hozzárendelt felhasználói fiókok.

>[!Tip]
>Is választhatja, hogy a munkahely által Facebook SAML-alapú egyszeri bejelentkezés engedélyezése, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Konfigurálhatja a felhasználói fiók kiépítése munkahelyi Facebook által az Azure ad-ben:

Ez a szakasz célja felvázoló Active Directory felhasználói fiókok által Facebook munkahelyi kiépítés engedélyezése.

Az Azure AD képes való automatikus szinkronizálása a munkahelyi Facebook által hozzárendelt felhasználói fiók adatait. Az automatikus szinkronizálás lehetővé teszi, hogy a munkahelyi hozzáférés előtt megpróbálja az első alkalommal jelentkeznek be a felhasználók hitelesítéséhez szükséges adatok megszerzéséhez Facebook által. Azt is deszerializálni látja el felhasználók a munkahely által Facebook amikor hozzáférés az Azure AD vissza lett vonva.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás** szakasz.

2. Ha már beállította az egyszeri bejelentkezés munkahelyi által Facebook, keresse meg a munkahely által használja a keresőmezőt Facebook példányát. Máskülönben válassza **Hozzáadás** keresse meg a **által Facebook munkahelyi** az alkalmazás katalógusában. Válassza ki a munkahelyi Facebook által a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Válassza ki a munkahely által Facebook példányt, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**. 

    ![kiépítés folyamatban](./media/active-directory-saas-workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Az a **rendszergazdai hitelesítő adataival** szakaszt, írja be a hozzáférési jogkivonat a Facebook-rendszergazda által a munkahelyhez, és adja meg a bérlői URL-cím értéket `https://www.facebook.com/scim/v1/` . Tapasztalja [utasításokat](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) munkahelyi fiókhoz egy hozzáférési jogkivonat létrehozásával. 

6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat a munkahelyi Facebook-alkalmazást. Ha nem sikerül, győződjön meg arról, a Facebook-fiókkal munkahelyi Team rendszergazdai jogosultságokkal rendelkezik.

7. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

8. Kattintson a **mentéséhez.**

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók által Facebook munkahelyhez.**

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok az Azure AD munkahelyi által szinkronizált Facebook-on. A kiválasztott attribútumok **egyező** tulajdonságok kell egyeznie a felhasználói fiókok, a munkahely által használt Facebook a frissítési műveletek. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD szolgáltatás által Facebook munkahelyi fiókhoz kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

12. Kattintson a **mentéséhez.**

Automatikus kiépítés konfigurálásával kapcsolatos további információkért lásd: [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Mostantól létrehozhat egy olyan fiókot. Várjon 20 percig győződjön meg arról, hogy a munkahelyi fiók lett szinkronizálva a Facebook által.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-workplacebyfacebook-tutorial.md)
