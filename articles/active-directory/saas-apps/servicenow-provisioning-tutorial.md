---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása a ServiceNow |} A Microsoft Docs'
description: Ismerje meg, hogyan automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b3e4cc5ba4bc0173721947bd1e1a680ca7b3a3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217700"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Oktatóanyag: A ServiceNow konfigurálása a felhasználók automatikus átadása az Azure Active Directoryval

Ez az oktatóanyag célja, a lépéseket kell elvégeznie a ServiceNow és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből a ServiceNow mutatni.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-felhasználó Provisioning Service-ra épülő összekötők ismerteti. Ez a szolgáltatás leírása, hogyan működik és gyakran ismételt kérdések a fontos tudnivalókat tartalmaz [automatizálhatja a felhasználókiépítés és -átadás megszüntetése SaaS-alkalmazásokban az Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a servicenow segítségével, a következőkre van szükség:

- Azure AD-előfizetés
- ServiceNow, egy példány vagy a bérlő ServiceNow, Calgary verzió vagy újabb
- ServiceNow expressz, egy példányát a ServiceNow kifejezett, Helsinki verzió vagy újabb

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>Felhasználók hozzárendelése a ServiceNow

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók és/vagy az Azure AD-csoportokat a felhasználók, akik hozzáférhetnek a ServiceNow alkalmazás a jelölik. Ha úgy döntött, rendelhet ezek a felhasználók a ServiceNow alkalmazás a következő utasításokat: [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a ServiceNow az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.
>*   Amikor egy felhasználó hozzárendelése ServiceNow, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás a ServiceNow-felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissíteni, és tiltsa le a hozzárendelt felhasználói fiókok Azure AD-ben a felhasználó és csoport-hozzárendelések alapján ServiceNow a.

> [!TIP]
>Előfordulhat, hogy meg servicenow-hoz készült SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="configure-automatic-user-account-provisioning"></a>Automatikus felhasználói fiók üzembe helyezésének konfigurálása

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

1. Ha már konfigurálta a ServiceNow az egyszeri bejelentkezés, keresse meg a keresési mező használatával ServiceNow-példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **ServiceNow** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a ServiceNow, és adja hozzá az alkalmazások listáját.

1. Válassza ki a ServiceNow-példányát, majd válassza ki a **kiépítési** fülre.

1. Állítsa be a **kiépítés** mód **automatikus**. 

    ![kiépítés folyamatban](./media/servicenow-provisioning-tutorial/provisioning.png)

1. A rendszergazdai hitelesítő adatok szakaszban hajtsa végre az alábbi lépéseket:
   
    a. Az a **ServiceNow-példány neve** szövegmezőbe írja be a ServiceNow-példány neve.

    b. Az a **ServiceNow-rendszergazda felhasználóneve** szövegmezőbe írja be a felhasználónevet, a rendszergazda.

    c. Az a **ServiceNow rendszergazdai jelszó** szövegmezőbe a rendszergazdai jelszót.

1. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat a ServiceNow alkalmazás. A csatlakozás sikertelen lesz, ha a ServiceNow-fiók rendelkezzen a csapat rendszergazdai engedélyekkel, és próbálkozzon a **"Rendszergazdai hitelesítő adatok"** lépés újra.

1. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

1. Kattintson a **mentéséhez.**

1. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a ServiceNow.**

1. Az a **attribútumleképezések** területen tekintse át a ServiceNow az Azure AD-ből szinkronizált felhasználói attribútumok. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a ServiceNow a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

1. Az Azure AD létesítési szolgáltatás servicenow-hoz készült engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** beállítások szakaszában

1. Kattintson a **mentéséhez.**

Elindítja a kezdeti szinkronizálás, a felhasználók és/vagy a servicenow-hoz a felhasználók és csoportok szakaszban hozzárendelt csoportok. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a ServiceNow alkalmazás a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](servicenow-tutorial.md)


