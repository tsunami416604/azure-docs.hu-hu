---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása LucidChart |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének LucidChart felhasználói fiókokat.
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
ms.openlocfilehash: fc181625ead251480bb107fc59e3aae46afab1ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257688"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása LucidChart konfigurálása

Ez az oktatóanyag célja, a lépéseket kell elvégeznie a LucidChart és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből LucidChart mutatni. 

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

* Az Azure Active directory-bérlő
* Egy LucidChart bérlőt a [nagyvállalati csomag](https://www.lucidchart.com/user/117598685#/subscriptionLevel) vagy jobban engedélyezve
* LucidChart rendszergazdai engedélyekkel rendelkező felhasználói fiók

## <a name="assigning-users-to-lucidchart"></a>Felhasználók hozzárendelése LucidChart

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók és/vagy az Azure AD-csoportokat a felhasználók, akik hozzáférhetnek a LucidChart alkalmazását jelölik. Ha úgy döntött, utasításokat követve ezeket a felhasználókat rendelhet LucidChart alkalmazásához:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Felhasználók hozzárendelése LucidChart fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve LucidChart az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése LucidChart, akkor ki kell választania a **felhasználói** , vagy egy másik érvényes és alkalmazásspecifikus szerepkör (ha elérhető) a hozzárendelés párbeszédpanelen. A **alapértelmezett hozzáférési** szerepkör nem működik az üzembe helyezés, és ezeket a felhasználókat a rendszer kihagyja.

## <a name="configuring-user-provisioning-to-lucidchart"></a>LucidChart történő felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD-csatlakozás LucidChart a felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok Azure AD-ben a felhasználó és csoport-hozzárendelések alapján LucidChart a.

> [!TIP]
> Előfordulhat, hogy meg LucidChart SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Konfigurálja az automatikus felhasználói fiók kiépítése LucidChart az Azure AD-ben

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

2. Ha már konfigurált LucidChart egyszeri bejelentkezést, keresse meg a keresési mező használatával LucidChart példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **LucidChart** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a LucidChart, és adja hozzá az alkalmazások listáját.

3. Válassza ki a LucidChart példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![LucidChart Provisioning](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **titkos jogkivonat** a LucidChart fiók által létrehozott (a jogkivonatot a fiók alatt található: **Csapat** > **alkalmazásintegráció** > **SCIM**).

    ![LucidChart Provisioning](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat LucidChart alkalmazását. A csatlakozás sikertelen lesz, ha győződjön meg arról, LucidChart fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra az 5. lépés.

7. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet a "E-mail-értesítés küldése hiba esetén."

8. Kattintson a **Save** (Mentés) gombra.

9. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a LucidChart**.

10. Az a **attribútumleképezések** területen tekintse át a LucidChart az Azure AD-ből szinkronizált felhasználói attribútumok. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek LucidChart levő felhasználói fiókokat. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD létesítési szolgáltatás LucidChart engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakasz

12. Kattintson a **Save** (Mentés) gombra.

Ez a művelet elindítja a kezdeti szinkronizálás, a felhasználók és/vagy a felhasználók és csoportok szakaszban LucidChart hozzárendelt csoportokat. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)
