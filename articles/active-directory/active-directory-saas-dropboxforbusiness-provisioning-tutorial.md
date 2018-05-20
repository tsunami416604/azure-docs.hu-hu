---
title: 'Oktatóanyag: Dropbox konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a vállalati Dropbox között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: bdac910b5011237e26a39f3e45f3e7ad6176bc83
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Oktatóanyag: A vállalati Dropbox konfigurálása automatikus a felhasználók átadása

Ez az oktatóanyag célja a lépéseket kell elvégeznie a Dropbox és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-Dropbox vállalati mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   A Dropbox az üzleti egyszeri bejelentkezés engedélyezve van az előfizetésben.
*   Egy felhasználói fiókot az üzleti Dropbox Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-dropbox-for-business"></a>A dropbox alkalmazásba vállalati felhasználók hozzárendelése

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik a dropbox-ba való hozzáférés üzleti alkalmazás. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat a Dropbox alkalmazás:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Fontos tippek a Dropbox vállalati felhasználók hozzárendelése

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Dropbox vállalati teszteli a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó rendel a Dropbox vállalati, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik a kiépítés...

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD az üzleti a felhasználói fiók kiépítése API dropbox-ba való csatlakozáshoz és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a Dropbox vállalati felhasználók és csoportok alapján az Azure AD-hozzárendelés.

>[!Tip]
>Dönthet úgy is, engedélyezze SAML-alapú egyszeri bejelentkezést a vállalati Dropbox, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurálása automatikus felhasználói fiók kiépítése:

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurálta a vállalati Dropbox az egyszeri bejelentkezést, keresse meg az üzleti használja a keresőmezőt Dropbox-példány. Máskülönben válassza **Hozzáadás** keresse meg a **vállalati Dropbox** az alkalmazás katalógusában. Válassza ki a Dropbox vállalati a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a Dropbox vállalati példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**. 

    ![kiépítés folyamatban](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. Az a **rendszergazdai hitelesítő adataival** kattintson **engedélyezés**. A Dropbox bejelentkezési párbeszédpanel üzleti egy új böngészőablakban nyílik meg.

6. Az a **jelentkezzen be az Azure ad-val csatolásához Dropbox** párbeszédpanel, jelentkezzen be a Dropbox üzleti bérlő számára.

     ![A felhasználók átadása](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769518.png "a felhasználók átadása")

7. Győződjön meg arról, hogy szeretné-e a Dropbox üzleti bérlői módosításokat Azure Active Directory engedélyt. Kattintson a **engedélyezése**.
    
      ![A felhasználók átadása](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769519.png "a felhasználók átadása")

8. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD alkalmazás a Dropbox kapcsolódhatnak. Ha a kapcsolódás sikertelen, a vállalati fiók Team rendszergazdai jogosultságokkal rendelkezik, győződjön meg arról, hogy a Dropbox, és próbálkozzon a **"Engedélyezés"** léptessen ismét.

9. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

10. Kattintson a **mentéséhez.**

11. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók a vállalati dropbox alkalmazásba.**

12. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumokat a dropbox alkalmazásba vállalati szinkronizált az Azure AD. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Dropbox vállalati a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

13. Az Azure AD vállalati Dropbox szolgáltatáshoz kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

14. Kattintson a **mentéséhez.**

A kezdeti szinkronizálás bármely felhasználói és/vagy a dropbox-ba, a felhasználók és csoportok szakaszban vállalati rendelt csoportok kezdődik. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amely alkalmazás a Dropbox a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-dropboxforbusiness-tutorial.md)