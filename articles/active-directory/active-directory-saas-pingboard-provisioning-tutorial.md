---
title: "Oktatóanyag: Pingboard konfigurálása az automatikus felhasználó-átadási az Azure Active Directoryval |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja az Azure Active Directory automatikusan ellátásához, majd leépíti Pingboard felhasználói fiókokat."
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
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 0492a59852a6a2dd424c98e44969eab1ac4697e8
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="tutorial-configuring-pingboard-for-automatic-user-provisioning"></a>Oktatóanyag: Pingboard konfigurálása az automatikus felhasználó létesítése

Ez az oktatóanyag célja mutatjuk be, a lépéseket kell elvégeznie ahhoz, hogy az automatikus üzembe helyezést és megszüntetést Pingboard az Azure Active Directory felhasználói fiókokat.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

*   Az Azure Active Directory-bérlő
*   A Pingboard bérlő [Pro-fiók](https://pingboard.com/pricing) 
*   A Pingboard rendszergazdai jogosultságokkal rendelkező felhasználói fiókot 

> [!NOTE] 
> Az Azure Active Directory integráció kiépítés támaszkodik a Pingboard API (`https://your_domain.pingboard.com/scim/v2`) elérhető fiókjába.

## <a name="assigning-users-to-pingboard"></a>Felhasználók hozzárendelése Pingboard

Az Azure Active Directory egy fogalom, más néven "hozzárendeléseket" használ annak meghatározásához, hogy mely felhasználók kell kapniuk az alkalmazások elérésére. Automatikus fiók felhasználókiépítése keretében csak a "hozzárendelt" egy alkalmazás az Azure Active Directory felhasználók szinkronizálása. 

A létesítési szolgáltatás engedélyezése és konfigurálása, előtt döntse el, az Azure Active Directory a felhasználók milyen határoz meg a felhasználók, akik az Pingboard alkalmazásához való hozzáférést. Ha úgy döntött, itt cikk utasításait követve hozzárendelheti ezeket a felhasználókat az Pingboard alkalmazás:

[Felhasználó hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Felhasználók hozzárendelése Pingboard fontos tippek

*   Ajánlott egy Azure Active Directory-egy felhasználóhoz rendelni Pingboard teszteli a telepítési konfigurációt. További felhasználók rendelt később.

## <a name="configuring-user-provisioning-to-pingboard"></a>A felhasználók átadása a Pingboard konfigurálása 

Ez a szakasz végigvezeti Önt a csatlakozás az Azure Active Directory Pingboard felhasználói fiókhoz kiépítés API és a létesítési szolgáltatás létrehozása, konfigurálása frissítése, és tiltsa le a felhasználó-hozzárendelése Azure alapján Pingboard hozzárendelt felhasználói fiókok Active Directory.

> [!TIP]
> Dönthet úgy is, SAML-alapú egyszeri bejelentkezést Pingboard engedélyezni, utasítások megadott [Azure-portálon](https://portal.azure.com). Egyszeri bejelentkezés konfigurálható függetlenül automatikus kiépítés, bár ez a két funkció egészítse ki egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>Konfigurálása automatikus felhasználói fiók kiépítése az Azure Active Directoryban Pingboard:

1)  Az a [Azure-portálon](https://portal.azure.com), keresse meg a **Azure Active Directory > Vállalati alkalmazások > összes alkalmazás** szakasz.

2) Ha már konfigurált Pingboard egyszeri bejelentkezést, keresse meg a keresési mező Pingboard példányát. Máskülönben válassza **Hozzáadás** keresse meg a **Pingboard** az alkalmazás katalógusában. Válassza ki **Pingboard** a keresési eredmények közül, és adja hozzá az alkalmazások listáját.

3)  Jelölje ki a Pingboard példányát, majd válassza ki a **kiépítési** fülre.

4)  Állítsa be a **kiépítési üzemmódját** való **automatikus**.

![Pingboard kiépítése](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) A rendszergazdai hitelesítő adataival szakaszban a következő lépésekkel:

* Az a **bérlői URL-cím** szövegmezőhöz meg `https://your_domain.pingboard.com/scim/v2` és adott_tartomány cserélje le a valós tartomány
* Jelentkezzen be [Pingboard](https://pingboard.com/) rendszergazda fiók használatával.
* Kattintson a bővítmények > Integrációk > az Azure Active Directoryban.
* Kattintson a konfigurálás fülre, és válassza ki **engedélyezése a felhasználók átadása az Azure-ból**.
* Másolás a **OAuth tulajdonosi jogkivonat** mezőben, és meg kell adnia **titkos Token** szövegmező.

6) Az Azure portálon kattintson **kapcsolat tesztelése** biztosításához az Azure Active Directoryhoz csatlakozni tud-e az Pingboard alkalmazást. Ha nem sikerül, győződjön meg arról, a Pingboard fiókja rendelkezik rendszergazdai jogosultságokkal, és próbálja meg a **"Kapcsolat tesztelése"** léptessen ismét.

7) Adja meg az e-mail címet vagy egy csoport, az üzembe helyezési hiba értesítéseket kapjanak a **értesítő e-mailt** mezőben, majd jelölje be a következő jelölőnégyzetet.

8) Kattintson a **Save** (Mentés) gombra. 

9) A hozzárendelések szakaszban válassza ki a **szinkronizálása Azure Active Directory-felhasználókat Pingboard**.

10) Az a **attribútum-leképezésekhez** szakaszban, tekintse át a felhasználói attribútumok Pingboard szinkronizálni az Azure Active Directoryból. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználói fiókokat a Pingboard a frissítési műveleteket. Válassza ki a **mentése** gombra a módosítások véglegesítéséhez. További információkért lásd: [testreszabása felhasználói kiépítés attribútum-leképezésekhez](active-directory-saas-customizing-attribute-mappings.md).

11) Az Azure Active Directory Pingboard a szolgáltatás kiépítését engedélyezéséhez módosítsa a **kiépítési állapot** való **a** a a **beállítások** szakasz

12) Kattintson a **mentése** Pingboard rendelt felhasználók a kezdeti szinkronizálást.

A kezdeti végrehajtani ezt követő szinkronizálások, amely körülbelül 20 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. Használhatja a **szinkronizálás részleteivel** szakasz figyelemmel az előrehaladást, és hivatkozásokat követve történő rendszerbe állításához tevékenység jelentéseit, amelyek a létesítési szolgáltatás az Pingboard alkalmazás által végzett összes műveletet írják le.

Olvassa el az Azure Active Directory, naplók telepítési módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók kiépítése vállalati alkalmazások kezelése](active-directory-enterprise-apps-manage-provisioning.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [Egyszeri bejelentkezés konfigurálása](active-directory-saas-pingboard-tutorial.md)
