---
title: "Oktatóanyag: Azure Active Directoryval integrált DocuSign |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és DocuSign között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: e8ba836a021a06c017defee2c5b7379d200ac642
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configuring-docusign-for-user-provisioning"></a>Oktatóanyag: A felhasználók átadása DocuSign konfigurálása

Ez az oktatóanyag célja a lépéseket kell elvégeznie a DocuSign és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-DocuSign mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active directory-bérlő.
*   Egy DocuSign egyszeri bejelentkezés engedélyezve van az előfizetés.
*   Egy felhasználói fiókot az DocuSign Team rendszergazdai engedélyekkel.

## <a name="assigning-users-to-docusign"></a>Felhasználók hozzárendelése DocuSign

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a felhasználók és csoportok számára "rendelt" az Azure AD alkalmazás szinkronizálva.

A létesítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége döntse el, hogy mely felhasználók és/vagy az Azure AD-csoportok határoz meg a felhasználók, akik az DocuSign alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az DocuSign alkalmazás:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Felhasználók hozzárendelése DocuSign fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve DocuSign teszteli a telepítési konfigurációt. További felhasználók rendelt később.

*   Amikor egy felhasználó hozzárendelése DocuSign, ki kell választania egy érvényes felhasználói szerepkörnek. A "Default" szerepkör nem működik történő üzembe helyezéséhez.

> [!NOTE]
> Az Azure AD nem támogatja a Docusign alkalmazás csoport létrehozását, csak a felhasználók létesíthetők.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD kapcsolódás DocuSign a felhasználói fiók kiépítése API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a felhasználók és csoportok hozzárendelése az Azure AD-alapú DocuSign hozzárendelt felhasználói fiókok.

> [!Tip]
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezést DocuSign engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-user-account-provisioning"></a>Konfigurálhatja a felhasználói fiók kiépítése:

Ez a szakasz célja engedélyezése a felhasználók átadása, az Active Directory felhasználói fiókoknak az DocuSign felvázoló.

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2. Ha már konfigurált DocuSign egyszeri bejelentkezést, keresse meg a keresési mező DocuSign példányát. Máskülönben válassza **Hozzáadás** keresse meg a **DocuSign** az alkalmazás katalógusában. Válassza ki a DocuSign a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3. Jelölje ki a DocuSign példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **kiépítési üzemmódját** való **automatikus**. 

    ![Kiépítés](./media/active-directory-saas-docusign-provisioning-tutorial/provisioning.png)

5. Az a **rendszergazdai hitelesítő adataival** területen adja meg a következő konfigurációs beállításokat:
   
    a. Az a **rendszergazda felhasználóneve** szövegmezőhöz egy DocuSign a fióknevet, amelynek típusa a **rendszergazda** DocuSign.com rendelt profillal.
   
    b. Az a **rendszergazdai jelszó** szövegmező, írja be a fiókhoz tartozó jelszót.

6. Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az DocuSign alkalmazást.

7. Az a **értesítő e-mailt** mezőbe írja be az e-mail cím vagy egy csoportot ki kell üzembe helyezési hiba értesítéseket, és jelölje be a jelölőnégyzetet.

8. Kattintson a **mentéséhez.**

9. A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat DocuSign.**

10. Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok, az Azure AD DocuSign lettek szinkronizálva. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a DocuSign a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD szolgáltatás DocuSign kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** beállításai szakaszában

12. Kattintson a **mentéséhez.**

A felhasználók és csoportok szakaszban DocuSign rendelt felhasználók kezdeti szinkronizálásának megkezdése. A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 20 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentéseit, amelyek a létesítési szolgáltatás az DocuSign alkalmazás által végzett összes műveletet írják le.

Mostantól létrehozhat egy olyan fiókot. Akár 20 percig várjon győződjön meg arról, hogy a fiók DocuSign lett-e szinkronizálva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-docusign-tutorial.md)