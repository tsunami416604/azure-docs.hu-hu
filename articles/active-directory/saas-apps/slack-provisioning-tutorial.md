---
title: 'Oktatóanyag: felhasználó kiépítés a Slackhez – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok Tartalékidőre való automatikus kiépítéséhez és üzembe helyezéséhez.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062829"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Oktatóanyag: a tartalékidő konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy megmutassa, milyen lépéseket kell elvégeznie a Slack és az Azure AD-ben, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Slackbe.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* Slack-bérlő a [plusz csomaggal](https://aadsyncfabric.slack.com/pricing) vagy jobb engedélyezéssel
* Felhasználói fiók a Slackben a csapat rendszergazdai engedélyeivel

Megjegyzés: az Azure AD-kiépítés integrációja a [SLACK scim API](https://api.slack.com/scim)-ra támaszkodik, amely a plusz csomag Slack csapatainak számára érhető el.

## <a name="assigning-users-to-slack"></a>Felhasználók kiosztása a Slackhez

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználók és/vagy csoportok szerepelnek az Azure AD-ben azon felhasználók számára, akiknek hozzáférésre van szükségük a Slack-alkalmazáshoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a Slack-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Fontos Tippek a felhasználók Tartalékidőhöz rendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Slackhez a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználót a Slackhez rendel, ki kell választania a **felhasználó** vagy a csoport szerepkört a hozzárendelés párbeszédpanelen. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="configuring-user-provisioning-to-slack"></a>A felhasználók üzembe helyezésének beállítása a Slackre 

Ez a szakasz végigvezeti az Azure AD-nek a Slack felhasználói fiók létesítési API-val való összekapcsolásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és csoport-hozzárendelésen alapuló elosztott felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

**Tipp:** Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri bejelentkezést a Slackhez, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>A felhasználói fiókok automatikus üzembe helyezésének beállítása az Azure AD-ben:

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

2. Ha már konfigurálta a tartalékidőt az egyszeri bejelentkezéshez, keresse meg a Slack-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **Slack** elemet az alkalmazás-katalógusban. Válassza a tartalékidő lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a Slack-példányt, majd válassza a **kiépítés** lapot.

4. Állítsa a **kiépítési módot** **automatikus**értékre.

   ![Tartalékidő kiépítés](./media/slack-provisioning-tutorial/slack1.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés**elemre. Ekkor megnyílik a tartalékidő-engedélyezési párbeszédpanel egy új böngészőablakban.

6. Az új ablakban jelentkezzen be a Slack használatával a csapat rendszergazdai fiókjával. az eredményül kapott hitelesítés párbeszédpanelen válassza ki azt a Slack-csoportot, amely számára engedélyezni szeretné a kiépítés beállítást, majd válassza az **Engedélyezés**lehetőséget. Ha elkészült, térjen vissza a Azure Portal a létesítési konfiguráció befejezéséhez.

    ![Engedélyezési párbeszédpanel](./media/slack-provisioning-tutorial/slackauthorize.png)

7. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a Slack-alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Slack-fiókja rendelkezik rendszergazdai jogosultságokkal, majd próbálja megismételni az "engedélyezés" lépést.

8. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítési e-mail** mezőben a kiépítési hibaüzeneteket, és jelölje be az alábbi jelölőnégyzetet.

9. Kattintson a **Save** (Mentés) gombra.

10. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a slackhez**lehetőséget.

11. Az **attribútum-hozzárendelések** szakaszban tekintse át azokat a felhasználói attribútumokat, amelyeket szinkronizálni fog az Azure ad-ből a slackbe. Vegye figyelembe, hogy a **megfeleltetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez a Slack felhasználói fiókjainak megfelelően lesznek felhasználva. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

12. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Slack számára, módosítsa a **kiépítési állapotot** a következőre a **Beállítások** **szakaszban:**

13. Kattintson a **Save** (Mentés) gombra.

Ezzel elindítja a felhasználók és csoportok szakaszban a Slackhez rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, ami körülbelül 10 percenként történik, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységekre mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a Slack-alkalmazáson végrehajtott összes műveletet.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>Választható A csoport objektum kiépítés beállítása a Slackre

Lehetőség van arra is, hogy engedélyezheti a csoport objektumainak az Azure AD-ből a Slack-re való kihelyezését. Ez különbözik a "felhasználói csoportok kiosztása" beállítástól, abban az esetben, ha a tagok mellett a tényleges csoport objektum is replikálódik az Azure AD-ről a Slackre. Ha például egy "saját csoport" nevű csoporttal rendelkezik az Azure AD-ben, akkor egy "saját csoport" nevű csoport jön létre a tartalékidőn belül.

### <a name="to-enable-provisioning-of-group-objects"></a>A csoport objektumainak kiépítés engedélyezése:

1. A leképezések szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a slackhez**lehetőséget.

2. Az attribútum-hozzárendelés panelen az Igen értékre van állítva.

3. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből a slackbe szinkronizálni kívánt csoport attribútumait. Vegye figyelembe, hogy a **megfeleltetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletek során a tartalékidőben lévő csoportoknak megfelelően lesznek felhasználva. 

4. Kattintson a **Save** (Mentés) gombra.

Ez azt eredményezi, hogy a **felhasználók és csoportok** szakasz tartalékidőhöz rendelt összes csoport objektuma teljesen szinkronizálva van az Azure ad-ből a Slack-be. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a Slack alkalmazáson végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Slack **DisplayName** attribútumának konfigurálásakor vegye figyelembe a következő viselkedéseket:

  * Az értékek nem teljesen egyediek (például 2 felhasználó rendelkezhet ugyanazzal a megjelenítendő névvel)

  * Támogatja a nem angol nyelvű karaktereket, szóközöket és nagybetűket. 
  
  * Az engedélyezett írásjelek közé tartoznak az időszakok, az aláhúzások, a kötőjelek, az aposztrófok, a zárójelek (például **([{}])** ) és az elválasztók (például: **/;** ).
  
  * Csak akkor frissül, ha ez a két beállítás konfigurálva van a Slack munkahelyi/szervezeti **profiljának szinkronizálásához** , és a **felhasználók nem változtathatják meg a megjelenítendő nevüket**.
  
* A Slack **username** attribútumának 21 karakternél rövidebbnek kell lennie, és egyedi értékkel kell rendelkeznie.

* A Slack csak a **felhasználónévvel** és az **e-mail-címmel**való megfeleltetést engedélyezi.  

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
