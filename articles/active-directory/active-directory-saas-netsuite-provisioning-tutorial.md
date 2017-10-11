---
title: "Oktatóanyag: Azure Active Directoryval integrált Netsuite |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Netsuite között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 277c393536615fc8bfe8af0bc6d487115f04776c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Oktatóanyag: Netsuite konfigurálása az automatikus felhasználó létesítése

Ez az oktatóanyag célja a lépéseket kell elvégeznie a Netsuite és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-Netsuite mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   Egy Netsuite egyszeri bejelentkezés engedélyezve van az előfizetésben.
*   Egy felhasználói fiókot az Netsuite Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-netsuite"></a>Felhasználók hozzárendelése Netsuite

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok számára "rendelt" az Azure AD alkalmazás szinkronizálva.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik az Netsuite alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az Netsuite alkalmazás:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite"></a>Felhasználók hozzárendelése Netsuite fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Netsuite teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Netsuite, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódás Netsuite a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a felhasználók és csoportok hozzárendelése az Azure AD-alapú Netsuite hozzárendelt felhasználói fiókok.

> [!TIP] 
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezést Netsuite engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-user-account-provisioning"></a>Konfigurálhatja a felhasználói fiók kiépítése:

Ez a szakasz célja engedélyezése a felhasználók átadása, az Active Directory felhasználói fiókoknak az Netsuite felvázoló.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált Netsuite egyszeri bejelentkezést, keresse meg a keresési mező Netsuite példányát. Máskülönben válassza **Hozzáadás** keresse meg a **Netsuite** az alkalmazás katalógusában. Válassza ki a Netsuite a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Netsuite példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**. 

    ![Kiépítés](./media/active-directory-saas-netsuite-provisioning-tutorial/provisioning.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adja meg a következő konfigurációs beállításokat:
   
    a. Az a **rendszergazda felhasználóneve** szövegmezőhöz egy Netsuite a fióknevet, amelynek típusa a **rendszergazda** Netsuite.com rendelt profillal.
   
    b. Az a **rendszergazdai jelszó** szövegmező, írja be a fiókhoz tartozó jelszót.
      
6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az Netsuite alkalmazást.

7. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot ki kell üzembe helyezési hiba értesítéseket, és jelölje be a jelölőnégyzetet.

8. Kattintson a **mentéséhez.**

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat Netsuite.**

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok, az Azure AD Netsuite lettek szinkronizálva. Vegye figyelembe, hogy az attribútumok választotta **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Netsuite a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD szolgáltatás Netsuite kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

12. Kattintson a **mentéséhez.**

A kezdeti szinkronizálás bármely felhasználói és/vagy a felhasználók és csoportok szakaszban Netsuite rendelt csoportok kezdődik. Figyelje meg, hogy a kezdeti szinkronizálás végrehajtásához bekövetkező körülbelül 20 percenként, mindaddig, amíg a szolgáltatás fut. ezt követő szinkronizálások hosszabb időbe telik. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentéseit, amelyek a létesítési szolgáltatás az Netsuite alkalmazás által végzett összes műveletet írják le.

Mostantól létrehozhat egy olyan fiókot. Akár 20 percig várjon győződjön meg arról, hogy a fiók Netsuite lett-e szinkronizálva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-netsuite-tutorial.md)