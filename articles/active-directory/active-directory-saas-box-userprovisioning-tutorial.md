---
title: 'Oktatóanyag: Az Azure Active Directoryval történő üzembe helyezéséhez automatikus felhasználói beállítása használata |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a mező között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.openlocfilehash: d9c615e3d0f144080a02f6f8d95603ecac4341f0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Oktatóanyag: Az automatikus felhasználó lépnek beállítása használata

Ez az oktatóanyag célja szemlélteti a lépéseket kell elvégeznie a mezőbe, és az Azure AD automatikus kiépítés és deaktiválás rendelkezés felhasználói fiókok Azure ad-mezőben.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   A mezőben egyszeri bejelentkezés engedélyezve van az előfizetésben.
*   Egy felhasználói fiókot mezőben Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-box"></a>Felhasználók hozzárendelése mezőbe 

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok "hozzárendelt" az Azure AD-alkalmazáshoz való szinkronizálása.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik a Box alkalmazásához a hozzáférést. Ha úgy döntött, itt utasításokat követve hozzárendelheti ezeket a felhasználókat a Box alkalmazásához:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Felhasználók és csoportok hozzárendelése
A **mezőben > felhasználók és csoportok** az Azure-portálon állíthatja be, hogy mely felhasználók és csoportok kell hozzáférést adjon meg. Egy felhasználó vagy csoport hozzárendelése hatására megtörténik a következő műveleteket:

* Az Azure AD való hitelesítésre szolgáló mezőben lehetővé teszi a kijelölt felhasználó (akár közvetlen címhozzárendelési vagy csoporttagság). Ha a felhasználó nem tartozik, az Azure AD engedi jelentkezzen be a mezőbe, és az Azure AD bejelentkezési lapon hibát ad vissza.
* Az alkalmazás csempéjére a hozzá tartozó mezőben kerül a felhasználó [alkalmazásindító](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users).
* Ha automatikus kiépítés engedélyezve van, majd a kijelölt felhasználók és/vagy csoportok kerülnek a kiépítési várólista automatikusan építeni.
  
  * Ha csak a felhasználói objektumok be lett állítva, úgy kell létrehozni, majd kerülnek, a telepítési várólistán lévő összes közvetlenül hozzárendelt felhasználó, és minden olyan felhasználó, amelyek bármely hozzárendelt csoportok tagjai kerülnek be a létesítési várólistába. 
  * Objektumok be lett állítva, úgy kell létrehozni, ha majd hozzárendelt csoportobjektumok törlődnek, és minden olyan felhasználó, hogy ezek a csoportok tagjai. A csoport és a felhasználó tagságát után mezőbe írt megmaradnak.

Használhatja a **attribútumok > egyszeri bejelentkezés** lapján adja meg, melyik felhasználói attribútumok (vagy jogcímeket) mutatnak be mezőben SAML-alapú hitelesítés során és a **attribútumok > kiépítési** lap segítségével konfigurálhatja, hogyan felhasználói és csoportattribútum flow az Azure AD mezőben műveletek kiépítése során.

### <a name="important-tips-for-assigning-users-to-box"></a>Felhasználók hozzárendelése mezőben fontos tippek 

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó mezőhöz rendelt tesztelje a telepítési konfigurációt. További felhasználók és/vagy csoportok később is rendelhető.

*   Amikor egy felhasználó rendel a mezőbe, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti a csatlakozás az Azure AD lista felhasználói fiókhoz kiépítés API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok alapján, az Azure AD-felhasználók és csoportok hozzárendelése a.

Ha automatikus kiépítés engedélyezve van, majd a kijelölt felhasználók és/vagy csoportok kerülnek a kiépítési várólista automatikusan építeni.
    
 * Ha csak a felhasználói objektumok úgy kell létrehozni, akkor közvetlenül hozzárendelt felhasználók kerülnek be a létesítési várólistába, és minden olyan felhasználó, amelyek bármely hozzárendelt csoportok tagjai kerülnek be a létesítési várólistába vannak konfigurálva. 
    
 * Objektumok be lett állítva, úgy kell létrehozni, ha majd hozzárendelt csoportobjektumok törlődnek, és minden olyan felhasználó, hogy ezek a csoportok tagjai. A csoport és a felhasználó tagságát után mezőbe írt megmaradnak.

> [!TIP] 
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezés a Box engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurálása automatikus felhasználói fiók kiépítése:

Ez a szakasz célja felvázoló engedélyezése az Active Directory felhasználói létesítése mezőben.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált be egyszeri bejelentkezést, keresse meg a példányát használja a keresőmezőt párbeszédpanel. Máskülönben válassza **Hozzáadás** keresse meg a **mezőben** az alkalmazás katalógusában. Jelölje be a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Válassza ki azt a példányt mezőben, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**. 

    ![kiépítés folyamatban](./media/active-directory-saas-box-userprovisioning-tutorial/provisioning.png)

5. Az a **rendszergazdai hitelesítő adataival** területén kattintson **engedélyezés** egy új böngészőablakban mezőben bejelentkezési párbeszédpanel megnyitásához.

6. Az a **hozzáférési jogot mezőben bejelentkezési** lapon adja meg a szükséges hitelesítő adatokat, és kattintson a **engedélyezés**. 
   
    ![Az automatikus felhasználó-kiépítés engedélyezése](./media/active-directory-saas-box-userprovisioning-tutorial/IC769546.png "automatikus felhasználó-kiépítés engedélyezése")

7. Kattintson a **be a hozzáférési jogot** engedélyezi ezt a műveletet, és térjen vissza az Azure-portálon. 
   
    ![Az automatikus felhasználó-kiépítés engedélyezése](./media/active-directory-saas-box-userprovisioning-tutorial/IC769549.png "automatikus felhasználó-kiépítés engedélyezése")

8. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat a Box alkalmazásához. Ha nem sikerül, gondoskodjon be fiókjába Team rendszergazdai jogosultságokkal, majd próbálkozzon a **"Engedélyezés"** léptessen ismét.

9. Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

10. Kattintson a **mentéséhez.**

11. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználók mezőben.**

12. Az a **attribútum-leképezésekhez** szakaszban, tekintse át az Azure ad-be szinkronizált felhasználói attribútumok. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a mezőbe a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

13. Az Azure AD a Box szolgáltatáshoz kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

14. Kattintson a **mentéséhez.**

Amely elindítja a kezdeti szinkronizálás bármely felhasználói és/vagy csoportok rendelt felhasználók és csoportok részén mezőbe. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenységi naplóit, amely a Box alkalmazásához a létesítési szolgáltatás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).

Az mezőbe-bérlőben szinkronizált felhasználók találhatók **felügyelt felhasználók** a a **felügyeleti konzol**.

![Integráció állapotát](./media/active-directory-saas-box-userprovisioning-tutorial/IC769556.png "integrációs állapota")


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-box-tutorial.md)