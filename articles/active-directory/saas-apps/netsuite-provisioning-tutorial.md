---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása Netsuite OneWorld |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Netsuite OneWorld között.
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
ms.openlocfilehash: fefa90b638f194cc0554b4d4da2188f5748ca32f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180279"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Netsuite konfigurálása

Ez az oktatóanyag célja, a lépéseket kell elvégeznie a Netsuite OneWorld és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből Netsuite mutatni.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Azure Active directory-bérlő.
*   Netsuite OneWorld előfizetés. Vegye figyelembe, hogy a felhasználók automatikus átadása jelenleg csak az NetSuite OneWorld támogatott.
*   Egy felhasználói fiókot a Netsuite rendszergazdai jogosultságokkal.

## <a name="assigning-users-to-netsuite-oneworld"></a>Felhasználók hozzárendelése Netsuite OneWorld

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus fióklétesítés kontextusában csak a felhasználók és csoportok rendelt "" az Azure AD-alkalmazáshoz való szinkronizálódnak.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók és/vagy az Azure AD-csoportokat a felhasználók, akik hozzáférhetnek a Netsuite alkalmazását jelölik. Ha úgy döntött, utasításokat követve ezeket a felhasználókat rendelhet Netsuite alkalmazásához:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Felhasználók hozzárendelése Netsuite OneWorld fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Netsuite az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Netsuite, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás Netsuite a felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok Azure AD-ben a felhasználó és csoport-hozzárendelések alapján Netsuite a.

> [!TIP] 
> Előfordulhat, hogy meg Netsuite SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-user-account-provisioning"></a>Konfigurálása a felhasználói fiók kiépítése:

Ez a szakasz célja engedélyezése a felhasználó kiépítése az Active Directory felhasználói fiókoknak az Netsuite kidolgozására.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

1. Ha már konfigurált Netsuite egyszeri bejelentkezést, keresse meg a keresési mező használatával Netsuite példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Netsuite** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a Netsuite, és adja hozzá az alkalmazások listáját.

1. Válassza ki a Netsuite példányát, majd válassza ki a **kiépítési** fülre.

1. Állítsa be a **Kiépítési mód** való **automatikus**. 

    ![kiépítés folyamatban](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg a következő beállításokat:
   
    a. Az a **rendszergazdai felhasználónév** szövegmezőbe írja be a Netsuite fióknév, amely rendelkezik a **rendszergazda** Netsuite.com hozzárendelni a profilt.
   
    b. Az a **rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.
      
1. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat Netsuite alkalmazását.

1. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket kapni, és jelölje be a jelölőnégyzetet.

1. Kattintson a **mentéséhez.**

1. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a Netsuite.**

1. Az a **attribútumleképezések** területen tekintse át a Netsuite az Azure AD-ből szinkronizált felhasználói attribútumok. Vegye figyelembe, hogy a kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a frissítési műveletek Netsuite levő felhasználói fiókokat. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

1. Az Azure AD létesítési szolgáltatás Netsuite engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** beállítások szakaszában

1. Kattintson a **mentéséhez.**

Elindítja a kezdeti szinkronizálás, a felhasználók és/vagy a felhasználók és csoportok szakaszban Netsuite hozzárendelt csoportokat. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut, mint végrehajtásához. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a Netsuite alkalmazásban a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](netsuite-tutorial.md)
