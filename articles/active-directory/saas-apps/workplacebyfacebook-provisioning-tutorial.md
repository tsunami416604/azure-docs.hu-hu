---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directory konfigurálása a Facebook, a munkahelyi |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a munkahely által Facebook között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 6544143feccb3fe24460d6f8a369b241181b5646
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194508"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Oktatóanyag: A Facebook, a munkahelyi felhasználók automatikus átadása konfigurálása

Ez az oktatóanyag célja, mutatni, a lépéseket kell elvégeznie a Facebook és az Azure AD automatikus kiépítésének és megszüntetésének felhasználói fiókok Azure AD-ből a munkahelyhez, a Facebook, a munkahelyen.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a munkahely által Facebook, a következőkre van szükség:

- Azure AD-előfizetés
- A munkahelyi hálózathoz Facebook egyszeri bejelentkezéses engedélyezett előfizetés szerint

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>A Facebook, a munkahelyi felhasználók hozzárendelése

Az Azure Active Directory "-hozzárendelések" nevű fogalma használatával határozza meg, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus felhasználói fiók kiépítése kontextusában csak a felhasználók és csoportok rendelt "", az alkalmazások az Azure AD szinkronizálása.

A kiépítési szolgáltatás engedélyezése és konfigurálása, mielőtt szüksége dönthet arról, hogy mely felhasználók és/vagy az Azure AD-csoportokat a felhasználók, akik hozzáférhetnek a munkahelyen a Facebook-alkalmazás által jelölik. Ha úgy döntött, rendelhet ezeket a felhasználókat a munkahelyhez, Facebook-alkalmazást az alábbi utasításokat:

[Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Felhasználók hozzárendelése a munkahely által Facebook fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználót a munkahelyhez által hozzárendelt Facebook az üzembe helyezési konfiguráció tesztelése. További felhasználók és csoportok később is rendelhető.

*   A felhasználó által Facebook munkahelyhez hozzárendelésekor jelöljön ki egy érvényes felhasználói szerepkörnek. Az "Alapértelmezett hozzáférés" szerepkör nem működik a kiépítéshez.

## <a name="enable-user-provisioning"></a>Felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-csatlakozás munkahelyi Facebook a felhasználói fiók üzembe helyezési API által és az eszközkiépítési szolgáltatás létrehozása, konfigurálása frissíteni, és tiltsa le a munkahelyi felhasználó és csoport alapján Facebook által hozzárendelt felhasználói fiókok az Azure AD-hozzárendelés.

>[!Tip]
>Előfordulhat, hogy meg SAML-alapú egyszeri bejelentkezés előkészítése a munkahely által Facebook, a biztonsági utasítások megadott [az Azure portal](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül az automatikus kiépítést, abban az esetben, ha e két szolgáltatás segítőosztályok egymással.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Konfigurálása a felhasználói fiók kiépítése a munkahelyhez Facebook által az Azure ad-ben:

Ez a szakasz célja, az Active Directory felhasználói fiókoknak a munkahelyhez, a Facebook-kiépítés engedélyezése kidolgozására.

Az Azure AD automatikusan szinkronizálhatók a munkahelyhez Facebook által hozzárendelt felhasználói fiók adatainak támogatja. Az automatikus szinkronizálás lehetővé teszi a munkahelyi Facebook, a hozzáférés, először bejelentkezési kísérlet előtt felhasználók hitelesítéséhez szükséges adatok alapján. Is függeszthetők helyezi üzembe a Facebook, a munkahelyi felhasználók amikor hozzáférés az Azure AD vissza lett vonva.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a **Azure Active Directory** > **vállalati alkalmazások** > **mindenalkalmazás** szakaszban.

2. Ha már konfigurálta a Facebook, a munkahelyi az egyszeri bejelentkezés, keresse meg a munkahely által Facebook, a keresőmező használatával példány. Ellenkező esetben válassza **Hozzáadás** és keressen rá a **munkahelyi által Facebook** az alkalmazás-katalógusában. A keresési eredmények közül válassza ki a munkahelyi Facebook szerint, és adja hozzá az alkalmazások listáját.

3. Válassza ki a munkahely által Facebook-példányát, majd válassza ki a **kiépítési** fülre.

4. Állítsa be a **Kiépítési mód** való **automatikus**. 

    ![kiépítés folyamatban](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Alatt a **rendszergazdai hitelesítő adataival** részen adja meg a hozzáférési jogkivonat Facebook-rendszergazda által a munkahelyen és a bérlői URL-cím értékét állítsa `https://www.facebook.com/scim/v1/` . Tekintse át ezeket [utasításokat](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) létrehozásával egy hozzáférési tokent a munkahelyen. 

6. Az Azure Portalon kattintson a **kapcsolat tesztelése** annak biztosítása érdekében az Azure AD csatlakozni tud a munkahelyhez, Facebook-alkalmazást. Ha a kapcsolat hibája esetén, győződjön meg arról, a Facebook-fiók munkahelyi csapat rendszergazdai engedélyekkel rendelkező.

7. Adja meg az e-mail-címét egy személyt vagy csoportot, amelyre az üzembe helyezési hiba értesítéseket szeretné kapni a **értesítő e-mailt** mezőben, majd jelölje be a jelölőnégyzetet.

8. Kattintson a **mentéséhez.**

9. A leképezések szakasz alatt válassza ki a **szinkronizálása az Azure Active Directory-felhasználók által Facebook munkahelyhez.**

10. Az a **attribútumleképezések** területen tekintse át az Azure AD-ből a munkahelyhez által szinkronizált Facebook felhasználói attribútumok. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságai megfelelően a felhasználói fiókok, a munkahely által használt Facebook frissítési műveletek. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

11. Az Azure AD létesítési szolgáltatás a munkahely által Facebook engedélyezéséhez módosítsa a **üzembe helyezési állapotra** való **a** a a **beállítások** szakasz

12. Kattintson a **mentéséhez.**

Automatikus üzembe helyezés beállításáról további információkért lásd: [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Mostantól létrehozhat egy olyan fiókot. Ellenőrizze, hogy a munkahelyi fiók lett szinkronizálva a Facebook által várja meg, akár 20 percig.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése a vállalati alkalmazások kezelése](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](workplacebyfacebook-tutorial.md)
