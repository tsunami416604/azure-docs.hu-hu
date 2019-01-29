---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a DocuSign |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a DocuSign között.
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
ms.openlocfilehash: a4fecf760f60f9e81385d9e11387cf8d38c29a03
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198231"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása DocuSign konfigurálása

Ez az oktatóanyag célja, a lépéseket kell elvégeznie a DocuSign és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből DocuSign mutatni.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Azure Active directory-bérlő.
*   A DocuSign az egyszeri bejelentkezés engedélyezve van az előfizetés.
*   Egy felhasználói fiókot a Docusignban csapat rendszergazdai jogosultságokkal rendelkezik.

## <a name="assigning-users-to-docusign"></a>Felhasználók hozzárendelése DocuSign

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Felhasználók automatikus fióklétesítés kontextusában csak a felhasználók és csoportok rendelt "" az Azure AD-alkalmazáshoz való szinkronizálódnak.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók, illetve a csoportok az Azure ad-ben képviselik a felhasználók, akik hozzáférhetnek a DocuSign-alkalmazását. Ha úgy döntött, utasításokat követve ezeket a felhasználókat rendelhet DocuSign-alkalmazását:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Felhasználók hozzárendelése DocuSign fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó DocuSign azt az üzembe helyezési konfiguráció tesztelése rendelve. További felhasználók később is rendelhető.

*   Amikor egy felhasználó hozzárendelése a DocuSign, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.

> [!NOTE]
> Az Azure AD nem támogatja a csoportos kiépítését, a Docusign-alkalmazáshoz, csak a felhasználók bővítheti.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás DocuSign a felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a DocuSign az Azure ad-ben a felhasználó és csoport-hozzárendelések alapján.

> [!Tip]
> Választhatja azt is, SAML-alapú egyszeri bejelentkezés engedélyezve van, a DocuSign, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-user-account-provisioning"></a>Konfigurálása a felhasználói fiók kiépítése:

Ez a szakasz célja engedélyezése a felhasználó kiépítése az Active Directory felhasználói fiókoknak az DocuSign kidolgozására.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

1. Ha már konfigurált DocuSign az egyszeri bejelentkezés, keresse meg a keresési mező használatával DocuSign-példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **DocuSign** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a DocuSign, és adja hozzá az alkalmazások listáját.

1. Válassza ki a DocuSign-példányát, majd válassza ki a **kiépítési** fülre.

1. Állítsa be a **Kiépítési mód** való **automatikus**. 

    ![kiépítés folyamatban](./media/docusign-provisioning-tutorial/provisioning.png)

1. Alatt a **rendszergazdai hitelesítő adataival** területén adja meg a következő beállításokat:
   
    a. Az a **rendszergazdai felhasználónév** szövegmezőbe írja be a DocuSign-fiók, amelynek neve a **rendszergazda** DocuSign.com hozzárendelni a profilt.
   
    b. Az a **rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.

1. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat a DocuSign-alkalmazás.

1. Az a **értesítő e-mailt** mezőbe írja be az e-mail-címét egy személyt vagy csoportot, akik kell üzembe helyezési hiba értesítéseket kapni, és jelölje be a jelölőnégyzetet.

1. Kattintson a **mentéséhez.**

1. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a DocuSign.**

1. Az a **attribútumleképezések** területen tekintse át a DocuSign az Azure AD-ből szinkronizált felhasználói attribútumok. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a DocuSign a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

1. Az Azure AD létesítési szolgáltatás, a DocuSign engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** beállítások szakaszában

1. Kattintson a **mentéséhez.**

Elindítja a kezdeti szinkronizálás, a DocuSign, a felhasználók és csoportok szakaszban hozzárendelve felhasználók. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a DocuSign az alkalmazást a kiépítési szolgáltatás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](docusign-tutorial.md)
