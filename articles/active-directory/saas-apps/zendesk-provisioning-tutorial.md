---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a Zendesk |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének felhasználói fiókokat a Zendeskhez.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: d8d6df221a8c520cea5e6e938447ffd881e7374c
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322772"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Zendesk konfigurálása

Ez az oktatóanyag célja a lépéseket kell végrehajtania a Zendesk és Azure Active Directory (Azure AD) konfigurálása az Azure AD automatikus kiépítésének és megszüntetésének felhasználók és csoportok a Zendeskhez bemutatásához. 

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek vonatkoznak:

*   Az Azure AD-bérlő
*   Egy Zendesk bérlőt a [vállalati](https://www.zendesk.com/product/pricing/) a terv és jobban engedélyezve 
*   Egy felhasználói fiók rendszergazdai jogosultságokkal rendelkezik a Zendeskben 

> [!NOTE]
> Az Azure AD létesítési integrációs támaszkodik a [Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction), a vállalati csomag Zendesk-csapatok számára elérhető vagy jobb.

## <a name="adding-zendesk-from-the-gallery"></a>Zendesk hozzáadása a katalógusból
Konfigurálja az Azure AD-felhasználók automatikus Zendesk, mielőtt szüksége az Azure AD alkalmazáskatalógusában Zendesk hozzáadása a felügyelt SaaS-alkalmazások listája.

**Az Azure AD alkalmazáskatalógusában Zendesk hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások szakasz][2]
    
3. Zendesk hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Zendesk**.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk6.png)

5. Az eredmények panelen válassza ki a **Zendesk**, majd kattintson a **Hozzáadás** gombra kattintva adhat hozzá a Zendeskhez a SaaS-alkalmazások listájára.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Felhasználók hozzárendelése Zendesk

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus átadása kontextusában csak a felhasználók, illetve "rendelt" egy alkalmazás az Azure AD-csoportok szinkronizálódnak. 

Felhasználók automatikus kiépítés engedélyezése és konfigurálása, mielőtt, meg kell határoznia, melyik felhasználók, illetve a csoportok az Azure ad-ben a Zendesk hozzáférésre van szükségük. Ha úgy döntött, rendelhet a felhasználók és csoportok Zendesk utasításokat követve:

*   [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Felhasználók hozzárendelése Zendesk fontos tippek

*    Zendesk-szerepköröket a rendszer dinamikusan és automatikusan kitölti az Azure Portal felhasználói felület még ma. Zendesk-szerepkörök hozzárendelése a felhasználókhoz, mielőtt biztosítására, hogy egy kezdeti szinkronizálást ellen a legújabb szerepköröket, a Zendesk-bérlő lekéréséhez Zendesk befejeződjön.

*    Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Zendeskhez a kezdeti felhasználók automatikus konfiguráció tesztelése. További felhasználók és csoportok rendelhetők később követően a tesztek sikere esetén.
  
*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Zendeskhez a felhasználók automatikus konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

*   Való hozzárendelésekor a felhasználó a Zendeskhez, a hozzárendelés párbeszédpanelen jelölje ki bármely érvényes alkalmazás-specifikus szerepkört (ha elérhető). A felhasználók a **alapértelmezett hozzáférési** szerepkör nem tartoznak kiépítése.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Felhasználók automatikus átadása a Zendeskhez konfigurálása 

Ez a szakasz végigvezeti az Azure AD létesítési szolgáltatás létrehozása, frissítése és tiltsa le a felhasználók konfigurálásáról és/vagy az Azure AD-felhasználói és/vagy a csoport-hozzárendelések alapján csoportosítja a Zendeskben.

> [!TIP]
> Előfordulhat, hogy is engedélyezését választja SAML-alapú egyszeri bejelentkezés a Zendesk, a biztonsági utasítások megadott a [Zendesk egyszeri bejelentkezéses oktatóanyag](zendesk-tutorial.md). Egyszeri bejelentkezés konfigurálható függetlenül, hogy a felhasználók automatikus átadása, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurálhatja a felhasználók automatikus átadása Zendesk az Azure AD-ben:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) és keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás**.

2. Válassza ki a Zendesk a SaaS-alkalmazások listájában.
 
    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk3.png)

3. Válassza ki a **kiépítési** fülre.
    
    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Alatt a **rendszergazdai hitelesítő adataival** szakaszban adjon meg a **rendszergazdai felhasználónév**, **titkos jogkivonat**, és **tartomány** , a Zendesk-fiókjába. Példák a következő értékek a következők:

    *   Az a **rendszergazdai felhasználónév** mezőben töltse fel a rendszergazdai fiók, a Zendesk-bérlő felhasználóneve. Példa: admin@contoso.com.

    *   Az a **titkos jogkivonat** mezőben töltse fel a titkos jogkivonat, 6. lépésben leírtak szerint.

    *   Az a **tartomány** mezőt, a Zendesk-bérlőjének altartomány feltöltéséhez.
    Példa: A bérlői URL-címet egy olyan fiók https://my-tenant.zendesk.com, az altartomány lenne **saját bérlő**.

6. A **titkos jogkivonat** a Zendesk fiók találhatók **Admin > API > Beállítások**. 

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk4.png) ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 5. lépésben megjelenő mezők feltöltése, után kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD képes csatlakozás a Zendeskhez. Ha a kapcsolódás sikertelen, ellenőrizze a Zendesk-fiókja rendszergazdai engedélyekkel rendelkező, és próbálkozzon újra.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket fogadni, és jelölje be a jelölőnégyzetet - **e-mail-értesítés küldése, ha hiba történik**.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Kattintson a **Save** (Mentés) gombra.

10. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-felhasználók a Zendeskhez**.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Tekintse át a a Zendeskhez, az Azure AD-ből szinkronizált felhasználói attribútumok a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a Zendesk a frissítési műveleteket. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Alatt a **leképezések** szakaszban jelölje be **szinkronizálása az Azure Active Directory-csoportok a Zendeskhez**.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Tekintse át a csoportattribútumok szinkronizált Azure AD-ből, a Zendesk a **attribútumleképzés** szakaszban. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságait frissítési műveleteket a Zendeskben csoportjai megfelelően szolgálnak. Válassza ki a **mentése** gombra kattintva véglegesítse a módosításokat.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Hatókörszűrő konfigurálásához tekintse meg a következő utasításokat a [Scoping szűrő oktatóanyag](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Az Azure AD létesítési szolgáltatás a Zendesk engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakaszban.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Határozza meg a felhasználók és/vagy a csoportok, adja meg a Zendeskhez kiépítéséhez válassza ki a kívánt értékeket a **hatókör** a a **beállítások** szakaszban.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Ha készen áll rendelkezésre, kattintson a **mentése**.

    ![Zendesk-kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk18.png)


Ez a művelet elindítja a kezdeti szinkronizálás, az összes olyan felhasználó és/vagy meghatározott csoportoknak **hatókör** a a **beállítások** szakaszban. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amennyiben az Azure AD létesítési szolgáltatás fut-e több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységgel kapcsolatos jelentés, amely a Zendesk-kiszolgáló üzembe helyezése az Azure AD által végrehajtott összes műveletet ismerteti.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Összekötő-korlátozások
* Zendesk csoportok használatát a felhasználók számára csak az ügynök szerepkörökkel támogatja. További információkért tekintse meg [Zendesk a dokumentáció](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
