---
title: 'Oktatóanyag: A felhasználók automatikus átadása az Azure Active Directory konfigurálása a Dropbox |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Dropbox Business és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 3ae85b31e85ecc0f0df6500bc7cfda1719537f02
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814189"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Oktatóanyag: Felhasználók automatikus átadása Dropbox Business konfigurálása

Ez az oktatóanyag célja, a lépéseket kell elvégeznie, a Dropbox és az Azure ad-ben való automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ból a Dropbox Business mutatni.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

*   Azure Active directory-bérlő.
*   A Dropbox, az üzleti egyszeri bejelentkezéses engedélyezett előfizetéssel.
*   Egy felhasználói fiókot a Dropbox Business csapat rendszergazdai jogosultságokkal rendelkezik.

## <a name="assigning-users-to-dropbox-for-business"></a>Felhasználók hozzárendelése Dropbox Business

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók, illetve a csoportok az Azure ad-ben képviselik a felhasználók, akik hozzáférhetnek a Dropbox-alkalmazásra vonatkozóan. Ha úgy döntött, utasításokat követve hozzárendelheti ezeket a felhasználókat Dropbox-alkalmazásra vonatkozóan:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Felhasználók hozzárendelése Dropbox Business fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó hozzá van rendelve Dropbox Business az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

*   Amikor egy felhasználó hozzárendelése Dropbox Business, ki kell választania egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik az üzembe helyezés...

## <a name="enable-automated-user-provisioning"></a>Az automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás a Dropboxba üzleti felhasználói fiók üzembe helyezési API és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a hozzárendelt felhasználói fiókok a Dropbox Business felhasználó és csoport alapján az Azure AD-hozzárendelés.

>[!Tip]
>Választhatja azt is, Dropbox Business SAML-alapú egyszeri bejelentkezés engedélyezve, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurálása automatikus felhasználói fiók kiépítése:

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

2. Ha már konfigurált Dropbox Business az egyszeri bejelentkezés, keresse meg a Dropbox Business keresési mező használatával példányát. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **Dropbox Business** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a Dropbox Business, és adja hozzá az alkalmazások listáját.

3. Válassza ki a Dropbox Business példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **Kiépítési mód** való **automatikus**. 

    ![kiépítés folyamatban](./media/dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. Alatt a **rendszergazdai hitelesítő adataival** területén kattintson **engedélyezés**. A Dropbox bejelentkezési párbeszédpanel egy új böngészőablakban nyílik meg.

6. Az a **jelentkezzen be a Dropbox összekapcsolása az Azure ad-vel** párbeszédpanelen, a Dropbox Business bérlő bejelentkezni.

     ![Felhasználók létrehozásának](./media/dropboxforbusiness-provisioning-tutorial/ic769518.png "felhasználók átadása")

7. Győződjön meg arról, hogy szeretné-e el a Dropbox Business bérlőhöz tartozó Azure Active Directory engedélyt. Kattintson a **engedélyezése**.
    
      ![Felhasználók létrehozásának](./media/dropboxforbusiness-provisioning-tutorial/ic769519.png "felhasználók átadása")

8. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozhat a Dropboxban alkalmazásra vonatkozóan. A csatlakozás sikertelen lesz, ha a csapata rendszergazdai engedélyekkel rendelkező fiókot üzleti, győződjön meg arról, hogy a Dropbox, és próbálkozzon a **"Engedélyezés"** lépés újra.

9. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

10. Kattintson a **mentéséhez.**

11. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók a Dropbox Business.**

12. Az a **attribútumleképezések** területen tekintse át a Dropbox Business az Azure AD-ből szinkronizált felhasználói attribútumok. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználói fiókok, a Dropbox Business a frissítési műveleteket. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

13. Az Azure AD létesítési szolgáltatás, a Dropbox Business engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** beállítások szakaszában

14. Kattintson a **mentéséhez.**

Elindítja a kezdeti szinkronizálás, minden felhasználó és/vagy a Dropbox Business a felhasználók és csoportok szakaszban hozzárendelt csoportokat. A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz előrehaladásának figyeléséhez, és kövesse a hivatkozásokat kiépítés tevékenységeket tartalmazó naplók, amelyek leírják a kiépítési szolgáltatást, a Dropbox-üzleti alkalmazás által végrehajtott összes műveletet.

Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](dropboxforbusiness-tutorial.md)