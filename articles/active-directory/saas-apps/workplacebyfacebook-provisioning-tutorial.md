---
title: 'Oktatóanyag: a munkahelyi konfiguráció konfigurálása a Facebookban a felhasználók automatikus kiépítés Azure Active Directory használatával | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a munkahely között a Facebook használatával.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee091d1c8f0f477354f6bb422d041278ec5668e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574262"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Oktatóanyag: a munkahelyi környezet konfigurálása a Facebook használatával a felhasználók automatikus üzembe helyezéséhez

Ennek az oktatóanyagnak a célja, hogy megmutassa, milyen lépéseket kell elvégeznie a munkahelyen a Facebook és az Azure AD használatával, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a munkahelyi Facebook-ban.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációnak a munkahelyi Facebook-nal való konfigurálásához a következő elemek szükségesek:

- Azure AD-előfizetés
- A Facebook egyszeri bejelentkezéses előfizetést használó munkaterülete

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha még nem rendelkezik Azure AD-próbaverzióval, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos próbaverziót.

## <a name="assigning-users-to-workplace-by-facebook"></a>Felhasználók kiosztása a munkahelyhez Facebook használatával

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok milyen felhasználók számára férhetnek hozzá a munkahelyi Facebook-alkalmazáshoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a munkahelyéhez a Facebook-alkalmazásban:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Fontos Tippek a felhasználók munkahelyhez való hozzárendeléséhez a Facebook használatával

*   Azt javasoljuk, hogy az üzembe helyezési konfiguráció teszteléséhez egyetlen Azure AD-felhasználó legyen hozzárendelve a munkahelyhez a Facebookban. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

*   Ha a Facebook által a munkahelyhez rendeli a felhasználót, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="enable-user-provisioning"></a>A felhasználó üzembe helyezésének engedélyezése

Ez a szakasz végigvezeti az Azure AD munkahelyhez való csatlakoztatásának folyamatán a Facebook felhasználói fiókjának kiosztási API-jával, valamint a kiépítési szolgáltatás konfigurálásával a felhasználók és a csoport alapján a Facebookhoz rendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához hozzárendelés az Azure AD-ben.

>[!Tip]
>Azt is megteheti, hogy a Facebook által biztosított SAML-alapú egyszeri bejelentkezést is engedélyezte a [Azure Portalban](https://portal.azure.com)található utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Felhasználói fiók üzembe helyezésének beállítása a Facebookban az Azure AD-ben:

Ennek a szakasznak a célja annak ismertetése, hogyan engedélyezhető Active Directory felhasználói fiókok üzembe helyezése a munkahelyen a Facebookban.

Az Azure AD lehetővé teszi, hogy a Facebook használatával automatikusan szinkronizálja a hozzárendelt felhasználók fiókjának adatait a munkahelyhez. Ez az automatikus szinkronizálás lehetővé teszi a Facebook számára a munkahelyen a felhasználók számára a hozzáférés engedélyezéséhez szükséges adatgyűjtést, mielőtt az első alkalommal megpróbál bejelentkezni. Az Azure AD-ben való hozzáférés visszavonása után a Facebook a munkahelyről is kiépíti a felhasználókat.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** szakaszt.

2. Ha már konfigurálta a munkahelyét a Facebookban az egyszeri bejelentkezéshez, keresse meg a munkahelyi példányát a Facebook használatával a keresőmezőbe. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **munkahelyet a Facebook használatával** az alkalmazás-katalógusban. A keresési eredmények közül válassza ki a munkahely a Facebook alapján lehetőséget, és adja hozzá az alkalmazások listájához.

3. Válassza ki a munkahelyi példányát a Facebookban, majd válassza a **kiépítés** fület.

4. Állítsa a **kiépítési módot** **automatikus**értékre. 

    ![kiépítési](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a munkahelyi hozzáférési jogkivonatot a Facebook rendszergazdája, és állítsa a bérlői URL-címet `https://www.facebook.com/scim/v1/` értékre. A munkahelyi hozzáférési jogkivonat létrehozásával kapcsolatos [utasításokat](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) itt találhatja meg. 

6. A Azure Portalban kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad a Facebook-alkalmazás segítségével csatlakozhasson a munkahelyhez. Ha a kapcsolat meghiúsul, győződjön meg róla, hogy a munkahelyi Facebook-fiók rendelkezik a csoport rendszergazdai engedélyeivel.

7. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítő e-mailek** mezőben, majd jelölje be a jelölőnégyzetet.

8. Kattintson a **Mentés gombra.**

9. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a munkahelyhez a Facebook lehetőséget.**

10. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből szinkronizált felhasználói attribútumokat a Facebook használatával. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Facebook által a frissítési műveletekhez használt felhasználói fiókokkal egyeznek meg. A módosítások elvégzéséhez kattintson a Save (Mentés) gombra.

11. Az Azure AD-kiépítési szolgáltatás a Facebook számára történő engedélyezéséhez módosítsa a **kiépítési állapotot** a következőre a **Beállítások** **szakaszban:**

12. Kattintson a **Mentés gombra.**

További információ az automatikus kiépítés konfigurálásáról: [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Most már létrehozhat egy teszt fiókot is. Várjon akár 20 percet, és ellenőrizze, hogy a fiók szinkronizálva lett-e a munkahelyi Facebook-nal.

> [!NOTE]
> A Facebook csapata szorosan együttműködik a munkahelytel, hogy az Azure AD-alkalmazás jóvá legyen hagyva, és megfeleljen az új irányelveknek. A munkahelyen Facebook-határidők december 16-iak, és várhatóan megfelelünk ennek. Ebben az időben nem várható az ügyfelek munkája. 28 – február-2020 között az ügyfeleknek át kell térniük az új integrációra. Az áttelepítési útvonal elérhetővé tételét azonnal itt tesszük közzé.    

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](workplacebyfacebook-tutorial.md)
