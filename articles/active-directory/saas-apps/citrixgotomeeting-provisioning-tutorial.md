---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directoryval GoToMeeting konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a GoToMeeting között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 3d2dafd409c6c7b2be06a6f18c3d392aff681efe
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345777"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása GoToMeeting konfigurálása

Ez az oktatóanyag célja, a lépéseket kell elvégeznie a GoToMeeting és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből GoToMeeting mutatni.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Azure Active directory-bérlő.
*   Egy GoToMeeting egyszeri bejelentkezés engedélyezve van az előfizetés.
*   Egy felhasználói fiókot a GoToMeeting csapat rendszergazdai jogosultságokkal rendelkezik.

## <a name="assigning-users-to-gotomeeting"></a>Felhasználók hozzárendelése GoToMeeting

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók, illetve a csoportok az Azure ad-ben képviselik a felhasználók, akik hozzáférhetnek a GoToMeeting-alkalmazását. Ha úgy döntött, utasításokat követve ezeket a felhasználókat rendelhet GoToMeeting-alkalmazását:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Felhasználók hozzárendelése GoToMeeting fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve GoToMeeting az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése GoToMeeting, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás GoToMeeting a felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok Azure AD-ben a felhasználó és csoport-hozzárendelések alapján GoToMeeting a.

> [!TIP]
> Előfordulhat, hogy meg GoToMeeting SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurálása automatikus felhasználói fiók kiépítése:

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

1. Ha már konfigurált GoToMeeting egyszeri bejelentkezést, keresse meg a keresési mező használatával GoToMeeting-példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **GoToMeeting** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a GoToMeeting, és adja hozzá az alkalmazások listáját.

1. Válassza ki a GoToMeeting-példányát, majd válassza ki a **kiépítési** fülre.

1. Állítsa be a **kiépítés** mód **automatikus**. 

    ![kiépítés folyamatban](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. A rendszergazdai hitelesítő adatok szakaszban hajtsa végre az alábbi lépéseket:
   
    a. Az a **GoToMeeting-rendszergazda felhasználóneve** szövegmezőbe írja be a felhasználónevet, a rendszergazda.

    b. Az a **GoToMeeting rendszergazdai jelszó** szövegmezőbe a rendszergazdai jelszót.

1. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat GoToMeeting-alkalmazását. Ha létesített kapcsolat megszakad, győződjön meg, hogy a csapat rendszergazdai engedélyekkel rendelkező a GoToMeeting-fiókot, és próbálkozzon a **"Rendszergazdai hitelesítő adatok"** lépés újra.

1. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

1. Kattintson a **mentéséhez.**

1. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a GoToMeeting.**

1. Az a **attribútumleképezések** területen tekintse át a GoToMeeting, az Azure ad-ből szinkronizált felhasználói attribútumok. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek GoToMeeting levő felhasználói fiókokat. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

1. Az Azure AD létesítési szolgáltatás a GoToMeeting engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** beállítások szakaszában

1. Kattintson a **mentéséhez.**

Elindítja a kezdeti szinkronizálás, a felhasználók és/vagy a felhasználók és csoportok szakaszban GoToMeeting hozzárendelt csoportokat. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a GoToMeeting-alkalmazásban a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


