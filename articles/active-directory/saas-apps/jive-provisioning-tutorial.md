---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a Jive |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Jive között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c38623bba4c15add9abae289fae97af33be4f393
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443466"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása a Jive konfigurálása

Ez az oktatóanyag célja a megjelenítése, hogy a lépéseket kell elvégeznie a jive-beli és az Azure AD automatikus kiépítés és megszüntetni hozzárendeléseket felhasználói fiókokhoz jive-beli az Azure AD-ből.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Azure Active directory-bérlő.
*   A jive-beli egyszeri bejelentkezéses engedélyezett előfizetéssel.
*   Egy felhasználói fiókot a Jive csapat rendszergazdai jogosultságokkal rendelkezik.

## <a name="assigning-users-to-jive"></a>Jive-beli felhasználók hozzárendelése

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók, illetve a csoportok az Azure ad-ben a felhasználók, akik hozzáférhetnek a Jive alkalmazását jelölik. Ha úgy döntött, rendelhet ezeket a felhasználókat a jive-beli alkalmazás utasításokat követve:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Felhasználók hozzárendelése a Jive fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználót az üzembe helyezési konfiguráció teszteléséhez a Jive rendelni. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Jive, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás Jive a felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok Azure AD-ben a felhasználó és csoport-hozzárendelések alapján Jive a.

> [!TIP]
> Előfordulhat, hogy meg az SAML-alapú egyszeri bejelentkezés a Jive engedélyezve van, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-user-account-provisioning"></a>Konfigurálása a felhasználói fiók kiépítése:

Ez a szakasz célja engedélyezése a felhasználó kiépítése az Active Directory felhasználói fiókoknak az Jive kidolgozására.
Ez az eljárás részeként a Ön a felhasználó biztonsági jogkivonat Jive.com igényelnie kell biztosítania.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

1. Ha már konfigurálta a Jive az egyszeri bejelentkezés, keresse meg a keresési mező használatával Jive-példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Jive** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a Jive, és adja hozzá az alkalmazások listáját.

1. Válassza ki a Jive-példányát, majd válassza ki a **kiépítési** fülre.

1. Állítsa be a **Kiépítési mód** való **automatikus**. 

    ![kiépítés folyamatban](./media/jive-provisioning-tutorial/provisioning.png)

1. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg a következő beállításokat:
   
    a. Az a **jive-beli rendszergazdai felhasználónév** szövegmezőbe írja be a Jive-fiók, amelynek neve a **rendszergazda** Jive.com hozzárendelni a profilt.
   
    b. Az a **jive-beli rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.
   
    c. Az a **jive-beli bérlői URL-cím** szövegmezőbe írja be a jive-beli bérlői URL-cím.
      
      > [!NOTE]
      > A jive-beli bérlői URL-cím Jive jelentkezzen be a szervezet által használt URL-cím.  
      > Általában az URL-cím formátuma a következő: **www.\< szervezet\>. jive.com**.          

1. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat a Jive alkalmazását.

1. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

1. Kattintson a **mentéséhez.**

1. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a Jive.**

1. Az a **attribútumleképezések** területen tekintse át a jive-beli az Azure AD-ből szinkronizált felhasználói attribútumok. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok felel meg a frissítési műveleteket a jive-beli felhasználói fiókok használhatók. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

1. Az Azure AD létesítési szolgáltatás a jive-beli engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** beállítások szakaszában

1. Kattintson a **mentéséhez.**

Elindítja a kezdeti szinkronizálás, a felhasználók és/vagy a felhasználók és csoportok szakaszban jive-beli hozzárendelt csoportokat. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a Jive alkalmazásban a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](jive-tutorial.md)