---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Velpic |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Velpic felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16c302fbe151d6cd8c2198240bc31a2bd69dbd7b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270912"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Velpic konfigurálása

Ez az oktatóanyag célja, a lépéseket kell elvégeznie a Velpic és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből Velpic mutatni.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

* Egy Azure Active Directory-bérlő
* Egy Velpic bérlőt a [nagyvállalati csomag](https://www.velpic.com/pricing.html) vagy jobban engedélyezve
* Velpic rendszergazdai engedélyekkel rendelkező felhasználói fiók

## <a name="assigning-users-to-velpic"></a>Felhasználók hozzárendelése Velpic

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "" az Azure AD-alkalmazáshoz való szinkronizálásra kerül. 

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt kell döntenie, hogy mely felhasználók, illetve a csoportok az Azure ad-ben képviselik a felhasználók, akik hozzáférhetnek a Velpic alkalmazását. Ha úgy döntött, utasításokat követve ezeket a felhasználókat rendelhet Velpic alkalmazásához:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Felhasználók hozzárendelése Velpic fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználót az üzembe helyezési konfiguráció teszteléséhez Velpic rendelni. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése Velpic, akkor ki kell választania a **felhasználói** , vagy egy másik érvényes és alkalmazásspecifikus szerepkör (ha elérhető) a hozzárendelés párbeszédpanelen. Vegye figyelembe, hogy a **alapértelmezett hozzáférési** szerepkör nem működik az üzembe helyezés, és ezeket a felhasználókat a rendszer kihagyja.

## <a name="configuring-user-provisioning-to-velpic"></a>Velpic történő felhasználókiépítés konfigurálása

Ez a szakasz végigvezeti az Azure AD-csatlakozás Velpic a felhasználói fiók üzembe helyezési API, és az eszközkiépítési szolgáltatás létrehozása, frissítése és letiltását konfigurálása hozzárendelt felhasználói fiókok a felhasználó és csoport-hozzárendelést az Azure ad-ben alapján Velpic.

> [!TIP]
> Előfordulhat, hogy meg Velpic SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Konfigurálása automatikus felhasználói fiók kiépítése Velpic az Azure AD-ben:

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

2. Ha már konfigurált Velpic egyszeri bejelentkezést, keresse meg a keresési mező használatával Velpic példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Velpic** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a Velpic, és adja hozzá az alkalmazások listáját.

3. Válassza ki a Velpic példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Velpic kiépítése](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **bérlői URL-cím & titkos jogkivonat** Velpic a. () Ezeket az értékeket a Velpic fiók alatt található: **Kezelése** > **integrációs** > **beépülő modul** > **SCIM**)

    ![Engedélyezési értékek](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat Velpic alkalmazását. A csatlakozás sikertelen lesz, ha győződjön meg arról, Velpic fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra az 5. lépés.

7. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

8. Kattintson a **Save** (Mentés) gombra.

9. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a Velpic**.

10. Az a **attribútumleképezések** területen tekintse át a felhasználói attribútumok, amelyek a rendszer szinkronizálja az Azure ad-ből Velpic. Vegye figyelembe, hogy a kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok használandó felel meg a frissítési műveletek Velpic levő felhasználói fiókokat. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD létesítési szolgáltatás Velpic engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakasz

12. Kattintson a **Save** (Mentés) gombra.

Ekkor elindul a kezdeti szinkronizálás, a felhasználók és/vagy a felhasználók és csoportok szakaszban Velpic hozzárendelt csoportokat. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hajtsa végre. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységre vonatkozó jelentések, amelyek leírják a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)