---
title: 'Oktatóanyag: A DocuSign konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez| Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a DocuSign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88b65c8e8962ad8420ded47da1a343672123c589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058178"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Oktatóanyag: A DocuSign konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a DocuSign és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és az Azure AD-ről a DocuSign-ra való automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

*   Egy Azure Active Directory-bérlő.
*   A DocuSign egyszeri bejelentkezéssel rendelkező előfizetés.
*   A DocuSign felhasználói fiókja csapatadminisztrátori engedélyekkel.

## <a name="assigning-users-to-docusign"></a>Felhasználók hozzárendelése a DocuSign-hoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva vannak.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek szükségük van a DocuSign-alkalmazáshoz való hozzáférésre. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a DocuSign alkalmazáshoz az alábbi utasítások szerint:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Fontos tippek a felhasználók DocuSign hoz való hozzárendeléséhez

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a DocuSign a létesítési konfiguráció teszteléséhez. Később további felhasználók is hozzárendelhetők.

*   Amikor egy felhasználót a DocuSign-hoz rendel, érvényes felhasználói szerepkört kell választania. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

> [!NOTE]
> Az Azure AD nem támogatja a csoport kiépítését a Docusign alkalmazással, csak a felhasználók építhetők ki.

## <a name="enable-user-provisioning"></a>Felhasználói kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-t a DocuSign felhasználói fiók létesítési API-jával való csatlakoztatásával, valamint a kiépítési szolgáltatás konfigurálásával a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a DocuSignban az Azure AD-ben a felhasználói és csoport-hozzárendelés alapján.

> [!Tip]
> Az [Azure Portalon](https://portal.azure.com)megadott utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a DocuSign szolgáltatáshoz. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-user-account-provisioning"></a>A felhasználói fiókok kiépítésének konfigurálása:

Ez a szakasz célja, hogy körvonalazza, hogyan engedélyezheti az Active Directory felhasználói fiókok felhasználók számára történő kiépítését a DocuSign számára.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

1. Ha már konfigurálta a DocuSign-t egyszeri bejelentkezéshez, keresse meg a DocuSign példányát a keresőmező használatával. Ellenkező esetben válassza **a Hozzáadás** és keresés a **DocuSign elemet** az alkalmazásgyűjteményben. Válassza a DocuSign elemet a keresési eredmények között, és adja hozzá az alkalmazások listájához.

1. Válassza ki a DocuSign példányát, majd válassza a **Kiépítés** lapot.

1. Állítsa a **létesítési módot** **Automatikus**ra. 

    ![Kiépítés](./media/docusign-provisioning-tutorial/provisioning.png)

1. A **Rendszergazdai hitelesítő adatok csoportban** adja meg a következő konfigurációs beállításokat:
   
    a. A **Rendszergazdai felhasználónév** mezőbe írjon be egy DocuSign-fióknevet, amelyhez a **rendszergazdai** profil van rendelve DocuSign.com.
   
    b. A **Rendszergazdai jelszó** mezőbe írja be a fiók jelszavát.

1. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a DocuSign alkalmazáshoz.

1. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia, és jelölje be a jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**

1. A Leképezések csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a DocuSign.Under** The Mappings section, select Synchronize Azure Active Directory Users to DocuSign.

1. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD és a DocuSign között szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a DocuSign felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

1. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a DocuSign, módosítsa a **kiépítés állapota** **be a** Beállítások szakaszban

1. Kattintson a **Mentés gombra.**

Elindítja a DocuSign-hoz rendelt felhasználók kezdeti szinkronizálását a Felhasználók és csoportok szakaszban. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakaszban figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által a DocuSign alkalmazásban végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](docusign-tutorial.md)
