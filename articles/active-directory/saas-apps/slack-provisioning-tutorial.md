---
title: 'Oktatóanyag: Felhasználói kiépítés a Slack hez – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok slackbe való automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc912c2df435f9b7e591d7c5475e126e6b0aeb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062829"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Oktatóanyag: A tartalékidő konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a Slack és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és az Azure AD-ről a Tartalékba való kiépítésre.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* Tartalékbérlő a [Plus csomaggal](https://aadsyncfabric.slack.com/pricing) vagy a jobban engedélyezett
* A Tartalékidő felügyeleti jogosultságokkal rendelkező felhasználói fiók

Megjegyzés: Az Azure AD-kiépítési integráció a [Laza SCIM API-t,](https://api.slack.com/scim)amely a Tartalékcsomaggal vagy annál jobb csomaggal a Tartalékcsapatok számára érhető el.

## <a name="assigning-users-to-slack"></a>Felhasználók hozzárendelése a tartalékidőhöz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálódnak.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azokat a felhasználókat, akiknek hozzáférésre van szükségük a Slack alkalmazáshoz. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a Slack alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Fontos tippek a felhasználók Slack hez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve a Tartalékidő a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Tartalékidőhöz rendel, a hozzárendelési párbeszédpanelen ki kell **választania** a Felhasználó vagy a "Csoport" szerepkört. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

## <a name="configuring-user-provisioning-to-slack"></a>A felhasználók kiépítésének konfigurálása a tartalékidőhöz 

Ez a szakasz végigvezeti az Azure AD-t a Slack felhasználói fiók létesítési API-jával való csatlakoztatásán, és konfigurálja a kiépítési szolgáltatást a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a Slackben az Azure AD-ben a felhasználó- és csoport-hozzárendelés alapján.

**Tipp:** Az [Azure Portalon](https://portal.azure.com)megadott utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a Slack hez. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Az automatikus felhasználói fiók-kiépítés konfigurálása a Slack szolgáltatásba az Azure AD-ben:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

2. Ha már konfigurálta a Tartalékidő egyszeri bejelentkezést, keresse meg a Tartalékidő példányát a keresőmező használatával. Ellenkező esetben válassza **a Hozzáadás** és keresés a **Tartalékidő** lehetőséget az alkalmazásgyűjteményben. Válassza a Tartalékidő lehetőséget a keresési eredmények között, és vegye fel az alkalmazások listájára.

3. Válassza ki a Tartalékidő példányát, majd válassza a **Kiépítés** lapot.

4. Állítsa a **létesítési módot** **Automatikus**ra.

   ![Tartalékolás](./media/slack-provisioning-tutorial/slack1.png)

5. A **Rendszergazdai hitelesítő adatok csoportban** kattintson az **Engedélyezés gombra.** Ezzel megnyit egy Tartalékidő-engedélyezési párbeszédpanelt egy új böngészőablakban.

6. Az új ablakban jelentkezzen be a Slack be a csoport felügyeleti fiókjával. az eredményül kapott engedélyezési párbeszédpanelen jelölje ki azt a Tartalékidő-csapatot, amelyhez engedélyezni szeretné a kiépítést, majd válassza **az Engedélyezés**lehetőséget. Miután elkészült, térjen vissza az Azure Portalra a létesítési konfiguráció befejezéséhez.

    ![Engedélyezési párbeszédpanel](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Slack alkalmazáshoz. Ha a kapcsolat sikertelen, győződjön meg arról, hogy a Laza sági fiók rendelkezik a Csoport felügyeleti engedélyével, és próbálja meg újra az "Engedélyezés" lépést.

8. Adja meg annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be az alábbi jelölőnégyzetet.

9. Kattintson a **Mentés** gombra.

10. A Leképezések csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása a tartalékidővel**lehetőséget.

11. Az **Attribútum-leképezések** szakaszban tekintse át a felhasználói attribútumokat, amelyek szinkronizálva lesznek az Azure AD-ről a Tartalékidőre. Vegye figyelembe, hogy az **egyező** tulajdonságokként kijelölt attribútumok a Frissítési műveletek hez a Tartalékidő felhasználói fiókjainak egyeztetésére szolgálnak. A módosítások véglegesítéséhez kattintson a Mentés gombra.

12. Az Azure AD-kiépítési szolgáltatás slack engedélyezéséhez módosítsa a **Kiépítés állapotát** **be a** **Beállítások** szakaszban

13. Kattintson a **Mentés** gombra.

Ezzel elindítja a Felhasználók és csoportok szakasz laza ságához rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 10 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és követheti a kiépítési tevékenységjelentésekre mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által a Laza alkalmazásban végrehajtott összes műveletet ismertetik.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Nem kötelező] Csoportobjektum-kiépítés konfigurálása a Tartalékidő beállításhoz

Szükség esetén engedélyezheti a csoportobjektumok azure AD-ről tartalékba való kiépítést. Ez eltér a "felhasználói csoportok hozzárendelése", hogy a tényleges csoport objektum mellett a tagok replikálni fogják az Azure AD a tartalékidő. Például ha van egy "Saját csoport" nevű csoport az Azure AD-ben, egy azonos nevű csoport "Saját csoport" jön létre a Slack belül.

### <a name="to-enable-provisioning-of-group-objects"></a>A csoportobjektumok kiépítésének engedélyezése:

1. A Leképezések csoportban válassza **az Azure Active Directory-csoportok szinkronizálása a tartalékidővel**lehetőséget.

2. Az Attribútumleképezés panelen állítsa az Engedélyezve értéket Igen értékre.

3. Az **Attribútum-leképezések** szakaszban tekintse át a csoport attribútumok, amelyek szinkronizálva lesznek az Azure AD a tartalékidő. Vegye figyelembe, hogy az **egyező** tulajdonságokként kijelölt attribútumok a Frissítési műveletek hez a Tartalékidő csoportokkal egyeztethetők. 

4. Kattintson a **Mentés** gombra.

Ez azt eredményezi, hogy a **Felhasználók és csoportok** szakaszban a Tartalékidőhöz rendelt csoportobjektumok teljes szinkronizálása az Azure AD-ről a Tartalékidőre történik. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által a Laza alkalmazásban végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* A Slack **displayName** attribútumának konfigurálásakor vegye figyelembe az alábbi viselkedéseket:

  * Az értékek nem teljesen egyediek (pl. 2 felhasználónak ugyanaz a megjelenítendő neve)

  * Támogatja a nem angol karaktereket, szóközöket, nagybetűket. 
  
  * Az engedélyezett írásjelek közé tartoznak a pont, aláhúzás, kötőjel, aposztrófok, zárójelek (pl. **{ { } )**) és elválasztók (pl. / ; **).**
  
  * Csak akkor frissül, ha ez a két beállítás a Slack munkahelyén/szervezeténél van konfigurálva – **A profilszinkronizálás engedélyezve van,** és **a Felhasználók nem módosíthatják a megjelenítendő nevüket.**
  
* A Slack **userName** attribútumának 21 karakternél kisebbnek kell lennie, és egyedi értékkel kell rendelkeznie.

* A Tartalékidő csak a **felhasználónév** és **e-mail**attribútumokkal való egyeztetést teszi lehetővé.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
