---
title: 'Oktatóanyag: A GoToMeeting konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a GoToMeeting között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0ac06fc3018b4230cbf32712067c48400599082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058263"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Oktatóanyag: A GoToMeeting konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a GoToMeeting és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és az Azure AD-ről a GoToMeeting-re való automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

*   Egy Azure Active Directory-bérlő.
*   A GoToMeeting egyszeri bejelentkezéssel rendelkező előfizetés.
*   A GoToMeeting felhasználói fiókja a Csoportfelügyeleti engedélyekkel.

## <a name="assigning-users-to-gotomeeting"></a>Felhasználók hozzárendelése a GoToMeeting hez

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva van.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek hozzáférésre van szükségük a GoToMeeting alkalmazáshoz. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a GoToMeeting alkalmazáshoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Fontos tippek a felhasználók GoToMeeting hez való hozzárendeléséhez

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a GoToMeeting a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

*   Amikor egy felhasználót a GoToMeeting programhoz rendel, érvényes felhasználói szerepkört kell választania. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítés.

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználói kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-t a GoToMeeting felhasználói fiók létesítési API-jához való csatlakoztatásával, valamint a kiépítési szolgáltatás konfigurálásával a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a GoToMeetingben az Azure AD-ben a felhasználói és csoport-hozzárendelés alapján.

> [!TIP]
> Az [Azure Portalon](https://portal.azure.com)található utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a GoToMeeting hez. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus kiépítésének konfigurálása:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory > Vállalati alkalmazások > az összes alkalmazás** szakaszt.

1. Ha már konfigurálta a GoToMeeting programot egyszeri bejelentkezéshez, keresse meg a GoToMeeting példányát a keresőmező segítségével. Ellenkező esetben válassza **a Hozzáadás** lehetőséget, és keresse meg a **GoToMeeting elemet** az alkalmazásgyűjteményben. Válassza a Keresés gombra a keresési eredmények között, és vegye fel az alkalmazások listájára.

1. Válassza ki a GoToMeeting példányát, majd válassza a **Kiépítés** lapot.

1. Állítsa a **létesítési** módot **Automatikus**ra. 

    ![Kiépítés](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. A Rendszergazdai hitelesítő adatok csoportban hajtsa végre az alábbi lépéseket:
   
    a. A **GoToMeeting Admin felhasználónév** mezőbe írja be a rendszergazda felhasználónevét.

    b. A **GoToMeeting Felügyeleti jelszó** szövegmezőben a rendszergazda jelszava.

1. Az Azure Portalon kattintson a **Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a GoToMeeting alkalmazáshoz. Ha a kapcsolat sikertelen, győződjön meg arról, hogy a GoToMeeting-fiók rendelkezik a Csoport felügyeleti engedélyével, és próbálja meg újra a **"Rendszergazdai hitelesítő adatok"** című lépést.

1. Adja meg annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be a jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**

1. A Leképezések csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a GoToMeeting szolgáltatásba lehetőséget.**

1. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD-ről a GoToMeeting szolgáltatásra szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a GoToMeeting program felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

1. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a GoToMeeting szolgáltatáshoz módosítsa a **Kiépítés állapotát** **be beállításra** a Beállítások szakaszban

1. Kattintson a **Mentés gombra.**

Elindítja a Felhasználók és csoportok szakaszban a GoToMeeting hez rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg a szolgáltatás fut. A Szinkronizálás **részletei** szakaszban figyelheti a folyamatot, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által a GoToMeeting alkalmazásban végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


