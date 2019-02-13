---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directoryval Salesforce-tesztkörnyezet beállítása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Salesforce-tesztkörnyezet között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e0a4eed020728bea5de196eebe438947ae509e4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191988"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása a Salesforce-tesztkörnyezet konfigurálása

Ez az oktatóanyag célja, a lépéseket kell elvégeznie a Salesforce-tesztkörnyezet és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből Salesforce védőfal mutatni.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Azure Active directory-bérlő.
*   Salesforce védőfal for Work vagy a Salesforce védőfal for Education szolgáltatáshoz egy érvényes bérlőt. Mindkét szolgáltatáshoz használhatja egy ingyenes próbafiókot.
*   Egy felhasználói fiókot a Salesforce-tesztkörnyezet csapat rendszergazdai jogosultságokkal rendelkezik.

## <a name="assigning-users-to-salesforce-sandbox"></a>Felhasználók hozzárendelése a Salesforce-tesztkörnyezet

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus fióklétesítés kontextusában csak a felhasználók és csoportok rendelt "" az Azure AD-alkalmazáshoz való szinkronizálódnak.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók vagy csoportok Azure AD-ben a Salesforce-tesztkörnyezet alkalmazás hozzáférésre van szükségük. Miután végrehajtotta ezt a döntést, hozzárendelheti ezeket a felhasználókat a Salesforce-tesztkörnyezet alkalmazás utasításait követve [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Felhasználók hozzárendelése Salesforce védőfal fontos tippek

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Salesforce-tesztkörnyezet az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

* Amikor egy felhasználó hozzárendelése a Salesforce-tesztkörnyezet, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.

> [!NOTE]
> Ez az alkalmazás egyéni szerepkörök importál a Salesforce-tesztkörnyezet, amelyeket érdemes lehet az ügyfél akkor válassza, ha a felhasználók hozzárendelése a kiépítési folyamat részeként.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás Salesforce védőfal felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissíteni, és tiltsa le a hozzárendelt felhasználói fiókok a Salesforce védőfal alapján a felhasználók és csoportok az Azure AD-hozzárendelés.

>[!Tip]
>Választhatja azt is, Salesforce védőfal SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="configure-automatic-user-account-provisioning"></a>Automatikus felhasználói fiók üzembe helyezésének konfigurálása

Ez a szakasz célja engedélyezése a felhasználó kiépítése az Active Directory felhasználói fiókok és a Salesforce védőfal kidolgozására.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

1. Ha már konfigurálta a Salesforce-tesztkörnyezet az egyszeri bejelentkezés, keresse meg a Salesforce-tesztkörnyezet, a keresőmező használatával példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Salesforce védőfal** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a Salesforce-tesztkörnyezet, és adja hozzá az alkalmazások listáját.

1. Válassza ki a Salesforce-tesztkörnyezet példányát, majd válassza ki a **kiépítési** fülre.

1. Állítsa be a **Kiépítési mód** való **automatikus**.

    ![kiépítés folyamatban](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg a következő beállításokat:
   
    a. Az a **rendszergazdai felhasználónév** szövegmezőbe írja be a Salesforce-tesztkörnyezet-fiók, amelynek neve a **rendszergazda** a Salesforce.com-on hozzárendelt profil.
   
    b. Az a **rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.

1. A Salesforce-tesztkörnyezet biztonsági jogkivonatának beszerzéséhez, nyisson meg egy új lapon, és jelentkezzen be ugyanazt a Salesforce-tesztkörnyezet rendszergazdai fiókot. Az a lap jobb felső sarkában kattintson a nevére, és kattintson **beállítások**.

     ![Felhasználók automatikus kiépítés engedélyezése](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "engedélyezése a felhasználók automatikus átadása")

1. A bal oldali navigációs panelén kattintson **saját személyes adatok** bontsa ki a kapcsolódó csomópontot, majd **alaphelyzetbe a saját biztonsági jogkivonat**.
  
    ![Felhasználók automatikus kiépítés engedélyezése](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "engedélyezése a felhasználók automatikus átadása")

1. Az a **alaphelyzetbe állítása a biztonsági jogkivonat** lap, kattintson a **alaphelyzetbe állítása a biztonsági jogkivonat** gombra.

    ![Felhasználók automatikus kiépítés engedélyezése](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "engedélyezése a felhasználók automatikus átadása")

1. Ellenőrizze az e-mailben kapják a rendszergazdai fiókhoz rendelni. Keresse meg a Salesforce Sandbox.com az új biztonsági jogkivonatot tartalmazó e-mailt.

1. Jogkivonat másolása nyissa meg az Azure AD-ablakot, és illessze be azt a **titkos jogkivonat** mező.

1. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat Salesforce védőfal alkalmazását.

1. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket kapni, és jelölje be a jelölőnégyzetet.

1. Kattintson a **mentéséhez.**  
    
1.  A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók Salesforce védőfal.**

1. Az a **attribútumleképezések** területen tekintse át a Salesforce védőfal Azure AD-ből szinkronizált felhasználói attribútumok. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a Salesforce védőfal a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

1. Az Azure AD létesítési szolgáltatás Salesforce védőfalnál engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** beállítások szakaszában

1. Kattintson a **mentéséhez.**

Elindítja a kezdeti szinkronizálás, a felhasználók és/vagy a felhasználók és csoportok szakaszban Salesforce védőfal hozzárendelt csoportok. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a kiépítési szolgáltatást a Salesforce védőfal alkalmazás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
