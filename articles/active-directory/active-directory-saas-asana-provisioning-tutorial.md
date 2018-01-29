---
title: "Oktatóanyag: Asana konfigurálása az Azure Active Directoryval automatikus felhasználólétesítés |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti Asana felhasználói fiókokat."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 4f3bd11a99f43d6405ea285a7a283179d561f92a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Oktatóanyag: Automatikus felhasználólétesítés Asana konfigurálása

Ez az oktatóanyag célja a lépéseket kell elvégeznie a Asana és az Azure AD automatikus kiépítése és leépíti a felhasználói fiókok Azure ad-Asana mutatjuk be.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active Active directory-bérlő
*   Az Asana bérlő a [vállalati](https://www.asana.com/pricing) a terv és jobban engedélyezve 
*   A Asana rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

> [!NOTE] 
> Az Azure AD-integrációs kiépítés támaszkodik a [Asana API](https://app.asana.com/api/1.0/scim/Users) amely Asana rendelkezésére áll.

## <a name="assigning-users-to-asana"></a>Felhasználók hozzárendelése Asana

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapnia a kiválasztott alkalmazásokhoz való hozzáférés. Automatikus fiók felhasználókiépítése keretében csak a "hozzárendelt" az Azure AD-alkalmazáshoz való felhasználó szinkronizálásra kerül. 

A létesítési szolgáltatás engedélyezése és konfigurálása, előtt kell döntenie, hogy az Azure AD a felhasználók milyen határoz meg a felhasználók, akik az Asana alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az Asana alkalmazás:

[Felhasználó hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Felhasználók hozzárendelése Asana fontos tippek

*   Javasoljuk, hogy egyetlen Azure AD-felhasználó a kiépítési konfigurációjának tesztelése rendelendő Asana. További felhasználók rendelt később.

## <a name="configuring-user-provisioning-to-asana"></a>A felhasználók átadása a Asana konfigurálása 

Ez a szakasz végigvezeti az Azure AD kapcsolódás Asana felhasználói fiók kiépítése API, és a felhasználó-hozzárendelése az Azure AD-alapú Asana felhasználói fiókok létrehozásához, frissítéséhez és tiltsa le a létesítési szolgáltatás konfigurálása hozzárendelve.

> [!TIP]
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezést Asana engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Konfigurálása automatikus felhasználói fiók kiépítése Asana az Azure AD-ben:

1)  Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2) Ha már konfigurált Asana egyszeri bejelentkezést, keresse meg a keresési mező Asana példányát. Máskülönben válassza **Hozzáadás** keresse meg a **Asana** az alkalmazás katalógusában. Válassza ki **Asana** a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3)  Jelölje ki a Asana példányát, majd válassza ki a **kiépítési** fülre.

4)  Állítsa be a **kiépítési üzemmódját** való **automatikus**.

![Asana kiépítése](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5) A rendszergazdai hitelesítő adataival szakaszban, kövesse az alábbi utasításokat követve hozza létre a jogkivonatot, és írja be a a **titkos Token** szövegmező.

* Jelentkezzen be [Asana](https://app.asana.com) rendszergazda fiók használatával
* A profilképet, az a felső sávon kattintson, és válassza ki a jelenlegi szervezetnév beállítások
* A szolgáltatásfiókok lapjának megjelenítéséhez
* Kattintson a Hozzáadás szolgáltatásfiók
* Frissíteni készül a nevét, és fénykép profilhoz, igény szerint, másolja **Token** , majd kattintson a módosítások mentése

6) Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure AD csatlakozhat az Asana alkalmazást. Ha nem sikerül, győződjön meg arról, a Asana fiókja rendelkezik rendszergazdai jogosultságokkal, és próbálja meg a **"Kapcsolat tesztelése"** léptessen ismét.

7) Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be az alábbi jelölőnégyzetet.

8) Kattintson a **Save** (Mentés) gombra. 

9) A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat Asana**.

10) Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok szinkronizálva lesznek az Azure AD az Asana. Vegye figyelembe, hogy az attribútumok választotta **egyező** tulajdonságok felel meg a felhasználói fiókok Asana frissítés műveletekhez használható. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez. Lásd: [testreszabása felhasználói kiépítés attribútum-leképezésekhez](active-directory-saas-customizing-attribute-mappings.md) további részletekért

11) Az Azure AD szolgáltatás Asana kiépítés engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

12) Kattintson a **Save** (Mentés) gombra. 

Ezzel elindítja a kezdeti szinkronizálás bármely Asana a felhasználók szakaszban hozzárendelve felhasználók. A kezdeti végrehajtani ezt követő szinkronizálások, amely körülbelül 20 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentéseit, amelyek a létesítési szolgáltatás az Asana alkalmazás által végzett összes műveletet írják le.

Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-asana-tutorial.md)
