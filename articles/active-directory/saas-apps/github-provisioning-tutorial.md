---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a GitHub |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének GitHub felhasználói fiókok.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: baac3ca65558f2a67a3aecabd4b253f23ea94ad9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261666"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása a GitHub beállítása

Ez az oktatóanyag célja mutatni, a lépéseket kell elvégeznie a GitHub és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből a Githubra.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

* Az Azure Active directory-bérlő
* Létrehozott egy GitHub-szervezethez [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), ami megköveteli a [GitHub Enterprise-zal számlázási csomag](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Egy felhasználói fiók rendszergazdai jogosultságokkal rendelkezik a szervezet számára a Githubon

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [GitHub SCIM API](https://developer.github.com/v3/scim/), érhető el, amely [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) használó ügyfeleknek a [GitHub Enterprise-zal számlázási csomag](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) .

## <a name="assigning-users-to-github"></a>Felhasználók hozzárendelése a Githubon

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása. 

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók, illetve a csoportok az Azure ad-ben képviselik a felhasználók, akik hozzáférhetnek a GitHub-alkalmazását. Ha úgy döntött, utasításokat követve ezeket a felhasználókat rendelhet a GitHub-alkalmazáshoz:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Felhasználók hozzárendelése GitHub fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Githubra, az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

* A felhasználó a Githubra hozzárendelésekor, ki kell választania a **felhasználói** , vagy egy másik érvényes és alkalmazásspecifikus szerepkör (ha elérhető) a hozzárendelés párbeszédpanelen. A **alapértelmezett hozzáférési** szerepkör nem működik az üzembe helyezés, és ezeket a felhasználókat a rendszer kihagyja.

## <a name="configuring-user-provisioning-to-github"></a>Githubról történő felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti a GitHub felhasználói fiók üzembe helyezési API az Azure AD-csatlakozás és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a Githubon, az Azure ad-ben a felhasználó és csoport-hozzárendelések alapján.

> [!TIP]
> Előfordulhat, hogy meg SAML-alapú egyszeri bejelentkezés engedélyezve van, a GitHub, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Automatikus felhasználói fiók kiépítése a Githubra, az Azure AD konfigurálása

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

2. Ha már konfigurálta GitHub az egyszeri bejelentkezés, keresse meg a keresési mező használatával GitHub-példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **GitHub** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a GitHub, és adja hozzá az alkalmazások listáját.

3. Válassza ki a GitHub-példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![GitHub-kiépítés](./media/github-provisioning-tutorial/GitHub1.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén kattintson **engedélyezés**. Ez a művelet egy GitHub-engedélyezési párbeszédpanelt nyit meg egy új böngészőablakban. 

6. Az új ablakban jelentkezzen be a GitHub, a rendszergazdai fiók használatával. Az eredményül kapott engedélyezési párbeszédpanelen a GitHub-csapat, amely engedélyezi a kiépítés, majd válassza ki és **engedélyezés**. Miután végeztünk ezzel, térjen vissza az Azure Portalon, az üzembe helyezési konfiguráció befejezéséhez.

    ![Engedélyezési párbeszédpanel](./media/github-provisioning-tutorial/GitHub2.png)

7. Az Azure Portalon adjon **bérlői URL-cím** kattintson **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat a GitHub-alkalmazás. Ha a kapcsolat hibája esetén, győződjön meg arról, a GitHub-fiók rendszergazdai engedélyekkel rendelkező és **bérlői URL-cím** megfelelően van-e képkockák, majd próbálkozzon újra az "Engedélyezés" lépés (is jelentenek **bérlői URL-cím** szabály: `https://api.github.com/scim/v2/organizations/<Organization_name>` , a szervezetek a GitHub-fiókja alatt található: **Beállítások** > **szervezetek**).

    ![Engedélyezési párbeszédpanel](./media/github-provisioning-tutorial/GitHub3.png)

8. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet a "E-mail-értesítés küldése hiba esetén."

9. Kattintson a **Save** (Mentés) gombra.

10. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a Githubra**.

11. Az a **attribútumleképezések** területen tekintse át a githubon az Azure ad-ből szinkronizált felhasználói attribútumok. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok felel meg a frissítési műveleteket a GitHub felhasználói fiókok használhatók. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

12. Az Azure AD létesítési szolgáltatás GitHub-engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakasz

13. Kattintson a **Save** (Mentés) gombra.

Ez a művelet elindítja a kezdeti szinkronizálás, a felhasználók és/vagy a felhasználók és csoportok szakaszban GitHub hozzárendelt csoportokat. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)
