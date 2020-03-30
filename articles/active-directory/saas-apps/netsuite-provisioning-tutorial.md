---
title: 'Oktatóanyag: A Netsuite OneWorld konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Netsuite OneWorld között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9a823e6515c2bfe09e1ab7bcef471eb8169e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063295"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Oktatóanyag: A Netsuite konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a Netsuite OneWorld és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és a felhasználói fiókok azure AD-ről netsuite-re történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

*   Egy Azure Active Directory-bérlő.
*   Netsuite OneWorld előfizetés. Vegye figyelembe, hogy az automatikus felhasználói kiépítés jelenleg csak a NetSuite OneWorld-tel támogatott.
*   A Netsuite rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-netsuite-oneworld"></a>Felhasználók hozzárendelése a Netsuite OneWorld-höz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva vannak.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek hozzáférésre van szükségük a Netsuite alkalmazáshoz. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a Netsuite alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Fontos tippek a felhasználók Netsuite OneWorld-höz való hozzárendeléséhez

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van hozzárendelve a NetSuite a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

*   Amikor egy felhasználót a Netsuite-hez rendel, érvényes felhasználói szerepkört kell választania. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

## <a name="enable-user-provisioning"></a>Felhasználói kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-t a Netsuite felhasználói fiók létesítési API-jával való csatlakoztatásán, és konfigurálja a kiépítési szolgáltatást a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a Netsuite-ban az Azure AD-ben a felhasználó- és csoport-hozzárendelés alapján.

> [!TIP] 
> Az [Azure Portalon](https://portal.azure.com)található utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a Netsuite szolgáltatáshoz. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-user-account-provisioning"></a>A felhasználói fiókok kiépítésének konfigurálása:

Ez a szakasz célja, hogy körvonalazza, hogyan engedélyezheti az Active Directory felhasználói fiókok felhasználók általi kiépítését a Netsuite számára.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

1. Ha már konfigurálta a Netsuite-ot egyszeri bejelentkezéshez, keresse meg a Netsuite példányát a keresőmező használatával. Ellenkező esetben válassza **a Hozzáadás** és keresés a **Netsuite** elemet az alkalmazásgalériában. Válassza ki a Netsuite elemet a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

1. Válassza ki a Netsuite példányát, majd válassza **a Kiépítés** lapot.

1. Állítsa a **létesítési módot** **Automatikus**ra. 

    ![Kiépítés](./media/netsuite-provisioning-tutorial/provisioning.png)

1. A **Rendszergazdai hitelesítő adatok csoportban** adja meg a következő konfigurációs beállításokat:
   
    a. A **Rendszergazdai felhasználónév** mezőbe írjon be egy Netsuite-fióknevet, amelyhez a **rendszergazdai** profil Netsuite.com van rendelve.
   
    b. A **Rendszergazdai jelszó** mezőbe írja be a fiók jelszavát.
      
1. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Netsuite alkalmazáshoz.

1. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia, és jelölje be a jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**

1. A Leképezések csoportban válassza az Azure Active Directory-felhasználók szinkronizálása a Netsuite.Under Mappings ( Leképezések ) területen válassza **az Azure Active Directory-felhasználók szinkronizálása a Netsuite.**

1. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD-ről a Netsuite-re szinkronizált felhasználói attribútumokat. Vegye figyelembe, hogy az **egyező** tulajdonságokként kiválasztott attribútumok a Netsuite felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

1. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a NetSuite, módosítsa a **kiépítés állapota** **be a** Beállítások szakaszban

1. Kattintson a **Mentés gombra.**

Elindítja a Felhasználók és csoportok szakaszban a Netsuite-hoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. Vegye figyelembe, hogy a kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakaszban figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a hálózati alkalmazás létesítési szolgáltatása által végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](netsuite-tutorial.md)
