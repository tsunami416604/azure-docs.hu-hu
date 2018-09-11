---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a Slack |} A Microsoft Docs'
description: 'Útmutató: Azure Active Directory beállítása az Automatikus kiépítés és megszüntetni hozzárendeléseket felhasználói fiókok Slack.'
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.openlocfilehash: 7d6056987ee05f68eecf026e954327a2f62cf886
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348905"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Slack konfigurálása


Ebben az oktatóanyagban célja, hogy a lépéseket kell elvégeznie a Slack, és az Azure AD automatikus kiépítés és megszüntetni hozzárendeléseket felhasználói fiókokhoz Slack az Azure AD-ből. 

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Egy Azure Active Directory-bérlő
*   Egy Slack-bérlőben a [Plus csomag](https://aadsyncfabric.slack.com/pricing) vagy jobban engedélyezve 
*   A Slack csapat rendszergazdai engedélyekkel rendelkező felhasználói fiókkal 

Megjegyzés: Az Azure AD létesítési integrációs támaszkodik a [Slack SCIM API](https://api.slack.com/scim) Slack csoportjaink számára elérhető a plusz ikonra a terv, és még jobb.

## <a name="assigning-users-to-slack"></a>Felhasználók hozzárendelése Slack

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "" az Azure AD-alkalmazáshoz való szinkronizálásra kerül. 

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt kell dönthet arról, hogy mely felhasználók, illetve a csoportok az Azure ad-ben a felhasználók, akik hozzáférhetnek a Slack alkalmazást a jelölik. Ha úgy döntött, rendelhet ezeket a felhasználókat a Slack alkalmazást az alábbi utasításokat:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Felhasználók hozzárendelése Slack fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználót az üzembe helyezési konfiguráció teszteléséhez a Slack rendelni. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése a Slack, ki kell választania a **felhasználói** vagy a "Csoport" szerepkör-hozzárendelés párbeszédpanelen. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.


## <a name="configuring-user-provisioning-to-slack"></a>Slack történő felhasználókiépítés konfigurálása 

Ez a szakasz végigvezeti az Azure AD-csatlakozás Slack a felhasználói fiók üzembe helyezési API, és az eszközkiépítési szolgáltatás létrehozása, frissítése és letiltását konfigurálása hozzárendelt felhasználói fiókok a felhasználó és csoport-hozzárendelést az Azure ad-ben alapján Slack.

**Tipp:** is dönthet úgy, hogy engedélyezve van a SAML-alapú egyszeri bejelentkezés a Slack, a utasításait (az Azure Portalon) [https://portal.azure.com]. Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.


### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus fióklétesítés Slack, az Azure ad-ben:


1)  Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

2) Ha már konfigurált Slack egyszeri bejelentkezést, keresse meg a keresési mező használatával Slack-példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Slack** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a Slack, és adja hozzá az alkalmazások listáját.

3)  Válassza ki a Slack-példányát, majd válassza ki a **kiépítési** fülre.

4)  Állítsa be a **Kiépítési mód** való **automatikus**.

![Slack-kiépítés](./media/slack-provisioning-tutorial/Slack1.PNG)

5)  Alatt a **rendszergazdai hitelesítő adataival** területén kattintson **engedélyezés**. Ez egy Slack-engedélyezési párbeszédpanelt nyit meg egy új böngészőablakban. 

6) Az új ablakban jelentkezzen be a Slack, a csapat rendszergazdai fiók használatával. az eredményül kapott engedélyezési párbeszédpanelen a Slack csapatot, amely engedélyezi a kiépítést, majd válassza ki és **engedélyezés**. Miután végeztünk ezzel, térjen vissza az Azure Portalon, az üzembe helyezési konfiguráció befejezéséhez.

![Engedélyezési párbeszédpanel](./media/slack-provisioning-tutorial/Slack3.PNG)

7) Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat a Slack alkalmazást. Ha a kapcsolat hibája esetén, győződjön meg arról, a Slack-fiók csapat rendszergazdai engedélyekkel rendelkező, és ismételje meg az "Engedélyezés" lépés.

8) Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

9) Kattintson a **Save** (Mentés) gombra. 

10) A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a Slack**.

11) Az a **attribútumleképezések** területen tekintse át a felhasználói attribútumok, amelyek a rendszer szinkronizálja az Azure ad-ből Slack. Vegye figyelembe, hogy a kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok használandó felel meg a felhasználói fiókok, a Slack a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

12) Az Azure AD létesítési szolgáltatás a Slack engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakasz

13) Kattintson a **Save** (Mentés) gombra. 

Ekkor elindul a kezdeti szinkronizálás, a felhasználók és/vagy a felhasználók és csoportok szakaszban Slack hozzárendelt csoportokat. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint az ezt követő szinkronizálások, amely körülbelül 10 percenként történik, amíg a szolgáltatás fut hajtsa végre. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységre vonatkozó jelentések, amelyek leírják a Slack alkalmazást a kiépítési szolgáltatás által végrehajtott összes műveletet.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcionális] Csoport Slack kiépítés objektum konfigurálása 

Igény szerint engedélyezheti az Azure AD-ből származó objektumok csoport Slack kiépítését. Ez eltér a "felhasználói csoportok hozzárendelése", annak, hogy a tényleges csoport tagjai mellett objektum replikálja az Azure ad-ből Slack. Például ha az Azure ad-ben "My-Group" nevű csoport, egy "My-Group" nevű identitical csoportot az Slack belül létrejön.

### <a name="to-enable-provisioning-of-group-objects"></a>A csoport objektumok kiépítés engedélyezéséhez:

1) A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-csoportokat, Slack**.

2) Az attribútum hozzárendelése panelen beállítása engedélyezve igen.

3) Az a **attribútumleképezések** területen tekintse át a csoport attribútumok, amelyek a rendszer szinkronizálja az Azure ad-ből Slack. Vegye figyelembe, hogy a kiválasztott attribútumok **megfelelést kiváltó** tulajdonságait frissítési műveleteket a Slack csoportjai megfelelően használható. 

4) Kattintson a **Save** (Mentés) gombra.

Ez az eredmény a Slack, a hozzárendelt csoport objektumokat a **felhasználók és csoportok** szakasz a Slack, az Azure ad-ből teljesen szinkronizált. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a Slack alkalmazást a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
