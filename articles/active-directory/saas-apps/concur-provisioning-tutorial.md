---
title: 'Oktatóanyag: Konfigurálása, beleértve a felhasználók automatikus átadása az Azure Active Directory |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és beleértve között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: f3ee9b2ce11dea1e0490521cf72f509ef2e0db8d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151532"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Oktatóanyag: Konfigurálása, beleértve a felhasználók automatikus átadása

Ez az oktatóanyag célja mutatni, a lépéseket kell elvégeznie a beleértve és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből, beleértve.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Azure Active directory-bérlő.
*   Egy beleértve az egyszeri bejelentkezés engedélyezve van az előfizetésben.
*   Egy felhasználói fiók beleértve a csapat rendszergazdai jogosultságokkal rendelkezik.

## <a name="assigning-users-to-concur"></a>Felhasználók hozzárendelése beleértve

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók és/vagy az Azure AD-csoportokat a felhasználók, akik hozzáférhetnek a beleértve alkalmazását jelölik. Ha úgy döntött, utasításokat követve ezeket a felhasználókat rendelhet beleértve alkalmazásához:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Felhasználók hozzárendelése beleértve fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználót kell hozzárendelni, beleértve az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése beleértve, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás, beleértve a felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok Azure AD-ben a felhasználó és csoport-hozzárendelések alapján beleértve a.

> [!Tip] 
> Választhatja azt is, beleértve SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-user-account-provisioning"></a>Konfigurálása a felhasználói fiók kiépítése:

Ez a szakasz célja, beleértve az Active Directory felhasználói fiókok kiépítését lehetővé tevő ismertetik.

A költségelszámolás szolgáltatásban van alkalmazások nem megfelelő beállítása és használata a Web Service felügyeleti profil lehet. A rendszergazda profil használja az i & E felügyeleti funkciók nem adja hozzá a WS-felügyeleti szerepkört.

Concur tanácsadók, vagy az ügyfél rendszergazdának létre kell hoznia egy különálló webes szolgáltatás-rendszergazda profil, és az ügyfél-rendszergazda ezt a profilt kell használnia a webes szolgáltatások rendszergazdai funkciókat (például engedélyezésével alkalmazások). Ezeket a profilokat az ügyfél rendszergazdai napi i & E felügyeleti profil (a T & E felügyeleti profil nem rendelkezhet a hozzárendelt WSAdmin szerepkör) külön kell tartani.

Használható az alkalmazás engedélyezése a profil létrehozásakor adja meg az ügyfél rendszergazdanevet a felhasználói profil mezőkbe. Ez hozzárendeli a tulajdonjogát a profilhoz. Egy vagy több profil létrehozása után az ügyfél kell bejelentkeznie a profilt, kattintson a "*engedélyezése*" gombra egy Partner alkalmazás a Web Services menü belül.

A következő okok miatt ez a művelet nem a szokásos T & használata felügyeleti célokra használják profillal kell végrehajtani.

* Az ügyfél rendelkezik az, hogy rákattint az egy "*Igen*" párbeszédet alkalmazás engedélyezése után megjelenő ablakban. Kattintson elismeri, hogy az ügyfél nem hajlandó az adataikhoz való hozzáférésre, így Ön vagy a Partner nem kattintson az Igen gomb Partner alkalmazáshoz.

* Ha egy ügyfél felügyeleti alkalmazás engedélyezve van a T & E rendszergazdai profil távozik a szervezettől (eredő inaktivált folyamatban van a profilban található), engedélyezve van, hogy a profil nem működik addig, amíg az alkalmazás engedélyezve van egy másik aktív WS-felügyeleti profilt használó alkalmazások. Ezért lehet a különböző WS-felügyeleti profilt készíteni.

* Ha egy rendszergazda elhagyja a vállalatot, a neve, a WS-felügyeleti profil társított módosítható a csere rendszergazdájának szükség befolyásolása nélkül az engedélyezett alkalmazás, mert a profilnak nem kell inaktivált.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **beleértve** bérlő.

2. Az a **felügyeleti** menüjében válassza **webszolgáltatások**.
   
    ![Beleértve bérlői](./media/concur-provisioning-tutorial/IC721729.png "beleértve bérlő")

3. A bal oldalon a a **webszolgáltatások** ablaktáblán válassza **Partner kérelem engedélyezéséhez**.
   
    ![Partner alkalmazás](./media/concur-provisioning-tutorial/ic721730.png "Partner alkalmazás engedélyezése")

4. Az a **kérelem engedélyezéséhez** listáról válassza ki **Azure Active Directory**, és kattintson a **engedélyezése**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Kattintson a **Igen** gombra kattintva zárja be a **művelet megerősítése** párbeszédpanel.
   
    ![A művelet megerősítéséhez](./media/concur-provisioning-tutorial/ic721732.png "művelet megerősítése")

6. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

7. Ha már konfigurált beleértve az egyszeri bejelentkezés, keresse meg a példány, beleértve a keresési mező használatával. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **beleértve** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a beleértve, és adja hozzá az alkalmazások listáját.

8. Válassza ki azt a példányt, beleértve, majd válassza ki a **kiépítési** fülre.

9. Állítsa be a **Kiépítési mód** való **automatikus**. 
 
    ![kiépítés folyamatban](./media/concur-provisioning-tutorial/provisioning.png)

10. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg a **felhasználónév** és a **jelszó** beleértve a rendszergazda azon.

11. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat beleértve alkalmazását. Ha a kapcsolat hibája esetén, győződjön meg arról, beleértve fiókja csapat rendszergazdai engedélyekkel rendelkező.

12. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

13. Kattintson a **mentéséhez.**

14. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók, beleértve.**

15. Az a **attribútumleképezések** területen tekintse át a felhasználói attribútumok, beleértve az Azure AD-ből szinkronizált. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, beleértve a frissítési műveletek. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

16. Az Azure AD létesítési szolgáltatás, beleértve a engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakasz

17. Kattintson a **mentéséhez.**

Mostantól létrehozhat egy olyan fiókot. Győződjön meg arról, hogy a szinkronizált fiókkal rendelkező, beleértve a várja meg, akár 20 percig.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](concur-tutorial.md)

