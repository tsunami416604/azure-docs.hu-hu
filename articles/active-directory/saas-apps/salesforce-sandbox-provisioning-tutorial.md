---
title: 'Oktatóanyag: A Salesforce sandbox konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez| Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Salesforce sandbox között.
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
ms.openlocfilehash: 48df954f680c6774b0aa189453615156f4f0b4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063271"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Oktatóanyag: A Salesforce sandbox konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a Salesforce sandbox és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és az Azure AD-ről a Salesforce sandboxra való automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

*   Egy Azure Active Directory-bérlő.
*   Érvényes bérlő a Salesforce Sandbox for Work vagy a Salesforce Sandbox for Education számára. Bármelyik szolgáltatáshoz használhat ingyenes próbafiókot.
*   A Salesforce sandbox ban lévő felhasználói fiók csapatadminisztrátori engedélyekkel.

## <a name="assigning-users-to-salesforce-sandbox"></a>Felhasználók hozzárendelése a Salesforce sandboxhoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva vannak.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Salesforce sandbox alkalmazáshoz. Miután meghozta ezt a döntést, ezeket a felhasználókat hozzárendelheti a Salesforce Sandbox alkalmazáshoz a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) című útmutató utasításainak megfelelően.

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Fontos tippek a felhasználók Salesforce sandboxhoz rendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Salesforce sandbox a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Salesforce sandboxhoz rendel, érvényes felhasználói szerepkört kell választania. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

> [!NOTE]
> Ez az alkalmazás a kiépítési folyamat részeként importálja az egyéni szerepköröket a Salesforce sandboxból, amelyet az ügyfél a felhasználók hozzárendelésekor szeretne kiválasztani.

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználói kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a Salesforce Sandbox felhasználói fiók létesítési API-jával való összekapcsolásával, valamint a kiépítési szolgáltatás konfigurálásával a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a Salesforce sandboxban a felhasználó és a csoport alapján hozzárendelést az Azure AD-ben.

>[!Tip]
>Az [Azure Portalon](https://portal.azure.com)található utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a Salesforce sandboxhoz. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus kiépítésének konfigurálása

Ez a szakasz azt ismerteti, hogy miként engedélyezheti az Active Directory felhasználói fiókok szolgáltatását a Salesforce sandboxba.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

1. Ha már konfigurálta a Salesforce sandboxot egyszeri bejelentkezéshez, keresse meg a Salesforce Sandbox példányát a keresőmező használatával. Ellenkező esetben válassza a **Salesforce sandbox** **hozzáadása** és keresése lehetőséget az alkalmazásgyűjteményben. Válassza ki a Salesforce sandboxot a keresési eredmények között, és vegye fel az alkalmazások listájára.

1. Válassza ki a Salesforce sandbox példányát, majd válassza a **Kiépítés** lapot.

1. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. A **Rendszergazdai hitelesítő adatok csoportban** adja meg a következő konfigurációs beállításokat:
   
    a. A **Rendszergazdai felhasználónév** mezőbe írjon be egy Salesforce sandbox-fióknevet, amelyhez a **rendszergazdai** profil van rendelve Salesforce.com.
   
    b. A **Rendszergazdai jelszó** mezőbe írja be a fiók jelszavát.

1. A Salesforce sandbox biztonsági jogkivonatának beszerzéséhez nyisson meg egy új lapot, és jelentkezzen be ugyanabba a Salesforce sandbox felügyeleti fiókba. A lap jobb felső sarkában kattintson a nevére, majd a **Beállítások parancsra.**

     ![Automatikus felhasználói kiépítés engedélyezése](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Automatikus felhasználói kiépítés engedélyezése")

1. A bal oldali navigációs ablakban kattintson a **Személyes adatok** elemre a kapcsolódó szakasz kibontásához, majd kattintson a Saját **biztonsági jogkivonat visszaállítása parancsra.**
  
    ![Automatikus felhasználói kiépítés engedélyezése](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Automatikus felhasználói kiépítés engedélyezése")

1. A **Biztonsági jogkivonat visszaállítása** lapon kattintson a **Biztonsági jogkivonat visszaállítása** gombra.

    ![Automatikus felhasználói kiépítés engedélyezése](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Automatikus felhasználói kiépítés engedélyezése")

1. Ellenőrizze az ehhez a rendszergazdai fiókhoz társított e-mail postafiókot. Keressen egy e-mailt a Salesforce Sandbox.com, amely tartalmazza az új biztonsági jogkivonatot.

1. Másolja a jogkivonatot, nyissa meg az Azure AD-ablakot, és illessze be a **titkos jogkivonat** mezőbe.

1. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Salesforce sandbox alkalmazáshoz.

1. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia, és jelölje be a jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**  
    
1.  A Leképezések szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Salesforce sandbox.**

1. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD és a Salesforce sandbox szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a Salesforce sandbox felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

1. Az Azure AD-s létesítési szolgáltatás engedélyezéséhez a Salesforce sandbox, módosítsa a **kiépítés állapota** **be a** Beállítások szakaszban

1. Kattintson a **Mentés gombra.**

Elindítja a Salesforce sandboxhoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását a Felhasználók és csoportok szakaszban. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakaszban figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a Salesforce sandbox alkalmazás létesítési szolgáltatása által végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
