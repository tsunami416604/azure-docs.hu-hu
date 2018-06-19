---
title: 'Oktatóanyag: Concur konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Concur között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: e96d0772700a3974cd76055db9c4af0099e1dc04
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923405"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Concur konfigurálása

Ez az oktatóanyag célja a lépéseket kell elvégeznie a Concur és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-Concur mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   Egy Concur egyszeri bejelentkezés engedélyezve van az előfizetés.
*   Egy felhasználói fiókot az Concur Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-concur"></a>Felhasználók hozzárendelése Concur

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik az Concur alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az Concur alkalmazás:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Felhasználók hozzárendelése Concur fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó a kiépítési konfigurációjának tesztelése rendelendő Concur. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Concur, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódás Concur a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a felhasználók és csoportok hozzárendelése az Azure AD-alapú Concur hozzárendelt felhasználói fiókok.

> [!Tip] 
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezést Concur engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-user-account-provisioning"></a>Konfigurálhatja a felhasználói fiók kiépítése:

Ez a szakasz célja helyzeteit vázolják fel, az Active Directory felhasználói fiókoknak az Concur kiépítés engedélyezése.

Lehetővé teszi az alkalmazások kiadás szolgáltatási, nem rendelkezik megfelelő telepítő és a webes szolgáltatás-rendszergazdák profil használatát. A WS-rendszergazdai szerepkör nem tölti fel a rendszergazda profil, amelyekkel a T & E felügyeleti funkcióihoz.

Tanácsadók egyetért vagy az ügyfél rendszergazdának létre kell hoznia egy különálló webes szolgáltatás-rendszergazda profil, és az ügyfél rendszergazda ezt a profilt kell használnia a webes szolgáltatások felügyeleti funkciók (például, amely lehetővé teszi alkalmazások). Ezeket a profilokat kell különíteni az ügyfél rendszergazda napi T & E felügyeleti profil (a T & E felügyeleti profil nem szerepkörrel kell rendelkeznie a WSAdmin rendelt).

Az alkalmazás engedélyezésének használandó a profil létrehozásakor adja meg az ügyfél-rendszergazdai nevet a felhasználói profil mezőkben. Ez a profil tulajdonjoga rendeli hozzá. Egy vagy több profilok létrehozása után az ügyfél jelentkezzen be ezt a profilt kattintson a "*engedélyezése*" gombra a Web Services menü belül Partner alkalmazások.

A következő okok miatt ez a művelet nem a normál T & E felügyeleti használ a profillal kell végrehajtani.

* Az ügyfél rendelkezik, amely kattint kell "*Igen*" a párbeszéd ablak akkor jelenik meg, egy alkalmazás engedélyezése után. Kattintson elfogadja, hogy az ügyfél nem kész a Partner alkalmazás számára az adatok elérését, így Ön vagy a Partner nem gombra, hogy Igen.

* Ha egy ügyfél rendszergazda alkalmazás engedélyezve van a T & E rendszergazdai profil elhagyja a céget (ami azt eredményezi, a profil alatt inaktivált), engedélyezve van, hogy a profil nem működik addig, amíg az alkalmazás engedélyezve van egy másik aktív WS-felügyeleti profilt használó alkalmazások. Ezért profilok létrehozása a különböző WS-felügyeleti kellene.

* Ha egy rendszergazda elhagyja a vállalatot, WS-felügyeleti profilt tartozó név módosíthatja a csere rendszergazdájának igény befolyásolása nélkül az engedélyezett alkalmazás, mert a profilt nem kell inaktivált.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Concur** bérlő.

2. Az a **felügyeleti** menü **webszolgáltatások**.
   
    ![Concur bérlői](./media/concur-provisioning-tutorial/IC721729.png "Concur bérlői")

3. A bal oldalon a a **webszolgáltatások** ablaktáblán válassza előbb **Partner alkalmazás engedélyezése**.
   
    ![Partner alkalmazás engedélyezése](./media/concur-provisioning-tutorial/ic721730.png "Partner alkalmazás engedélyezése")

4. Az a **alkalmazás engedélyezése** listáról válassza ki **Azure Active Directory**, és kattintson a **engedélyezése**.
   
    ![A Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directoryban")

5. Kattintson a **Igen** bezárásához a **a művelet megerősítéséhez** párbeszédpanel.
   
    ![Erősítse meg a műveletet](./media/concur-provisioning-tutorial/ic721732.png "erősítse meg a műveletet")

6. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

7. Ha már konfigurált Concur egyszeri bejelentkezést, keresse meg a keresési mező Concur példányát. Máskülönben válassza **Hozzáadás** keresse meg a **Concur** az alkalmazás katalógusában. Válassza ki a Concur a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

8. Jelölje ki a Concur példányát, majd válassza ki a **kiépítési** fülre.

9. Állítsa be a **kiépítési üzemmódját** való **automatikus**. 
 
    ![kiépítés folyamatban](./media/concur-provisioning-tutorial/provisioning.png)

10. Az a **rendszergazdai hitelesítő adataival** területen adja meg a **felhasználónév** és a **jelszó** Concur rendszergazdától.

11. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az Concur alkalmazást. Ha nem sikerül, győződjön meg arról, Concur fiókja Team rendszergazdai jogosultságokkal rendelkezik.

12. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

13. Kattintson a **mentéséhez.**

14. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat Concur.**

15. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok, az Azure AD Concur lettek szinkronizálva. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Concur a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

16. Az Azure AD szolgáltatás Concur kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

17. Kattintson a **mentéséhez.**

Mostantól létrehozhat egy olyan fiókot. Akár 20 percig várjon győződjön meg arról, hogy a fiók Concur lett-e szinkronizálva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](concur-tutorial.md)

