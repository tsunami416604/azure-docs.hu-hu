---
title: 'Oktatóanyag: A Jive konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Jive között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602eed65745eea1fd9096508c442a27ea79bcba9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057734"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Oktatóanyag: A Jive konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a Jive és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és a felhasználói fiókok azure AD-ről a Jive-re történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

*   Egy Azure Active Directory-bérlő.
*   A Jive egyszeri bejelentkezés engedélyezett előfizetés.
*   A Jive felhasználói fiókja csapatadminisztrátori engedélyekkel.

## <a name="assigning-users-to-jive"></a>Felhasználók hozzárendelése a Jive-hez

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva van.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek szükségük van a Jive-alkalmazáselérésére. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a Jive alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Fontos tippek a felhasználók Jive-hez való hozzárendeléséhez

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Jive-hez a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

*   Amikor egy felhasználót a Jive-hez rendel, érvényes felhasználói szerepkört kell választania. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

## <a name="enable-user-provisioning"></a>Felhasználói kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-t a Jive felhasználói fiók létesítési API-jához való csatlakoztatásával, és konfigurálja a kiépítési szolgáltatást a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a Jive-ben az Azure AD-ben a felhasználó- és csoport-hozzárendelés alapján.

> [!TIP]
> Az [Azure Portalon](https://portal.azure.com)található utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a Jive számára. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-user-account-provisioning"></a>A felhasználói fiókok kiépítésének konfigurálása:

Ez a szakasz célja, hogy körvonalazza, hogyan engedélyezheti az Active Directory felhasználói fiókok felhasználói kiépítését a Jive számára.
Ennek az eljárásnak a részeként meg kell adnia egy felhasználói biztonsági jogkivonatot, amelyet Jive.com kell kérnie.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

1. Ha már konfigurálta a Jive-t egyszeri bejelentkezéshez, keresse meg a Jive példányát a keresőmező használatával. Ellenkező esetben válassza **a Hozzáadás** és keresés a **Jive** elemet az alkalmazásgyűjteményben. Válassza a Jive elemet a keresési eredmények között, és adja hozzá az alkalmazások listájához.

1. Válassza ki a Jive példányát, majd válassza **a Kiépítés** lapot.

1. Állítsa a **létesítési módot** **Automatikus**ra. 

    ![Kiépítés](./media/jive-provisioning-tutorial/provisioning.png)

1. A **Rendszergazdai hitelesítő adatok csoportban** adja meg a következő konfigurációs beállításokat:
   
    a. A **Jive Admin User Name (Típus: Jive-fiók** neve) mezőbe írja be azt a Jive-fióknevet, amelyhez a **rendszergazdai** profil Jive.com van rendelve.
   
    b. A **Jive Admin Password** mezőbe írja be a fiók jelszavát.
   
    c. A **Jive tenant URL-cím** mezőbe írja be a Jive-bérlő URL-címét.
      
      > [!NOTE]
      > A Jive-bérlői URL-cím a szervezet által a Jive-be való bejelentkezéshez használt URL-cím.  
      > Az URL-cím általában a következő formátumú: **www.\< szervezet\>.jive.com**.          

1. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Jive alkalmazáshoz.

1. Adja meg annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be az alábbi jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**

1. A leképezések szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Jive lehetőséget.**

1. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD-ről a Jive-re szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kijelölt attribútumok a Jive felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

1. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Jive, módosítsa a **kiépítés állapota** **be a** Beállítások szakaszban

1. Kattintson a **Mentés gombra.**

Elindítja a Kezdeti szinkronizálás minden felhasználó és /vagy csoportok rendelt Jive a Felhasználók és csoportok szakaszban. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által a Jive alkalmazásban végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](jive-tutorial.md)
