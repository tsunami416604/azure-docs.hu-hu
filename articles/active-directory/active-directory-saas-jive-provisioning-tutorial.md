---
title: "Oktatóanyag: Azure Active Directoryval integrált Jive |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Jive között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 957b152fdd40d08a867e788b0cb9f7d57ed481e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-jive-for-user-provisioning"></a>Oktatóanyag: A felhasználók átadása Jive konfigurálása

Ez az oktatóanyag célja mutatjuk be, végezze el a Jive és az Azure AD automatikus kiépítés és deaktiválás rendelkezés felhasználói fiókok Azure AD-Jive lépéseket.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   Egy Jive egyszeri bejelentkezés engedélyezve van az előfizetésben.
*   Egy felhasználói fiókot az Jive Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-jive"></a>Felhasználók hozzárendelése Jive

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik az Jive alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az Jive alkalmazás:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Felhasználók hozzárendelése Jive fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó a kiépítési konfigurációjának tesztelése rendelendő Jive. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Jive, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódás Jive a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a felhasználók és csoportok hozzárendelése az Azure AD-alapú Jive hozzárendelt felhasználói fiókok.

> [!TIP]
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezés Jive számára engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-user-account-provisioning"></a>Konfigurálhatja a felhasználói fiók kiépítése:

Ez a szakasz célja engedélyezése a felhasználók átadása, az Active Directory felhasználói fiókoknak az Jive felvázoló.
Ez az eljárás részeként meg kell beszereznie Jive.com felhasználó biztonsági jogkivonat szükség.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált Jive egyszeri bejelentkezést, keresse meg a keresési mező Jive példányát. Máskülönben válassza **Hozzáadás** keresse meg a **Jive** az alkalmazás katalógusában. Válassza ki a Jive a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Jive példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**. 

    ![Kiépítés](./media/active-directory-saas-jive-provisioning-tutorial/provisioning.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adja meg a következő konfigurációs beállításokat:
   
    a. Az a **Jive rendszergazda felhasználóneve** szövegmezőhöz egy Jive a fióknevet, amelynek típusa a **rendszergazda** Jive.com rendelt profillal.
   
    b. Az a **Jive rendszergazdai jelszó** szövegmező, írja be a fiókhoz tartozó jelszót.
   
    c. Az a **Jive bérlői URL-cím** szövegmezőhöz Jive bérlői URL-címét.
      
      > [!NOTE]
      > A Jive bérlői URL-cím bejelentkezni Jive a szervezet által használt URL-cím.  
      > Általában az URL-cím formátuma a következő: **www.\< szervezet\>. jive.com**.          

6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az Jive alkalmazást.

7. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

8. Kattintson a **mentéséhez.**

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat Jive.**

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok, az Azure AD Jive lettek szinkronizálva. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Jive a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD szolgáltatás Jive kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

12. Kattintson a **mentéséhez.**

A kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban Jive rendelt csoportok kezdődik. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 20 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentéseit, amelyek a létesítési szolgáltatás az Jive alkalmazás által végzett összes műveletet írják le.

Mostantól létrehozhat egy olyan fiókot. Akár 20 percig várjon győződjön meg arról, hogy a fiók Jive lett-e szinkronizálva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-jive-tutorial.md)