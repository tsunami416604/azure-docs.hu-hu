---
title: Az Azure Active Directory jelentéskészítési API előfeltételei | Microsoft dokumentumok
description: Ismerje meg az Azure AD jelentéskészítési API elérésének előfeltételeit
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991262"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Az Azure Active Directory jelentéskészítési API elérésének előfeltételei

Az [Azure Active Directory (Azure AD) Reporting API-k](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Ezeket az API-kat programozási nyelvekből és eszközökből hívhatja.

A jelentéskészítő API [az OAuth segítségével](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) engedélyezi a webes API-khoz való hozzáférést.

A jelentéskészítési API-hoz való hozzáférés előkészítéséhez a következőket kell tenni:

1. [Szerepkörök hozzárendelése](#assign-roles)
2. [Licenckövetelmények](#license-requirements)
3. [Egy alkalmazás regisztrálása](#register-an-application)
4. [Engedélyek megadása](#grant-permissions)
5. [Konfigurációs beállítások összegyűjtése](#gather-configuration-settings)

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Ahhoz, hogy az API-n keresztül hozzáférhessen a jelentési adatokhoz, az alábbi szerepkörök egyikét kell hozzárendelnie:

- Biztonsági olvasó

- Biztonsági rendszergazda

- Globális rendszergazda

## <a name="license-requirements"></a>Licenckövetelmények

A bérlő bejelentkezési jelentésének eléréséhez egy Azure AD-bérlőnek rendelkeznie kell Azure AD Premium-licenccel. Az Azure AD Premium P1 (vagy újabb) licenc bármely Azure AD-bérlő bejelentkezési jelentésének eléréséhez szükséges. Másik lehetőségként, ha a címtár típusa Az Azure AD B2C, a bejelentkezési jelentések érhetők el az API-n keresztül további licenckövetelmény nélkül. 


## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Regisztrációra akkor is szükség van, ha parancsfájl használatával fér hozzá a jelentéskészítő API-hoz. A regisztráció ad egy **alkalmazás-azonosítót,** amely szükséges az engedélyezési hívások, és lehetővé teszi a kódot, hogy a jogkivonatok fogadása.

Konfigurálása a címtár az Azure AD jelentéskészítési API eléréséhez, be kell jelentkeznie az [Azure Portalon](https://portal.azure.com) egy Azure-rendszergazdai fiókkal, amely egyben az Azure AD-bérlő **globális rendszergazdai** címtárszerepkör tagja is.

> [!IMPORTANT]
> A rendszergazdai jogosultságokkal rendelkező hitelesítő adatokkal futó alkalmazások nagyon hatékonyak lehetnek, ezért ügyeljen arra, hogy az alkalmazás azonosítóját és titkos hitelesítő adatait biztonságos helyen tartsa.
> 

**Azure AD-alkalmazás regisztrálása:**

1. Az [Azure Portalon](https://portal.azure.com)válassza az **Azure Active Directory** a bal oldali navigációs ablakban.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Az **Azure Active Directory** lapon válassza **az Alkalmazásregisztrációk**lehetőséget.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Az **Alkalmazásregisztrációk** lapon válassza az **Új regisztráció**lehetőséget.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. A **jelentkezési lap regisztrálása:**

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. A **Név** mezőbe írja `Reporting API application`be a következőt:

    b. A **Támogatott fiókok típusnál**válassza a Fiókok lehetőséget **ebben a szervezeti beállításban.**

    c. Az **Átirányítás URL-címmezőjébe** `https://localhost`válassza a **Webes** szövegmező t, írja be a következőt:

    d. Kattintson a **Register** (Regisztrálás) elemre. 


## <a name="grant-permissions"></a>Engedélyek megadása 

Az elérni kívánt API-tól függően az alkalmazásnak a következő engedélyeket kell megadnia:  

| API | Engedély |
| --- | --- |
| Microsoft Azure Active Directory | Címtáradatok olvasása |
| Microsoft Graph | Az összes naplónapló-adat olvasása |


![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/36.png)

A következő szakasz mindkét API-k lépéseit sorolja fel. Ha nem szeretné elérni az API-k egyikét, kihagyhatja a kapcsolódó lépéseket.

**Az API-k használatára vonatkozó engedélyek megadása az alkalmazásnak:**


1. Válassza az **API-engedélyek et,** majd **adjon hozzá egy engedélyt.** 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Az **API-engedélyek kérése lapon**keresse meg **az Örökölt API-verzió** **támogatása az Active Directory graph-ot.** 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. A **Kötelező engedélyek** lapon válassza az **Alkalmazásengedélyek**lehetőséget, és válassza a **Directory** **jelölőnégyzetet.ReadAll**.  Válassza **az Engedélyek hozzáadása**lehetőséget.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. A **Reporting API Application - API Permissions (Jelentéskészítő API-alkalmazás – API-engedélyek)** lapon válassza **a Rendszergazdai jóváhagyás megadása**lehetőséget. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Megjegyzés: A **Microsoft Graph** alapértelmezés szerint hozzáadódik az API-regisztráció során.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Konfigurációs beállítások összegyűjtése 

Ez a rész bemutatja, hogyan szerezheti be a következő beállításokat a könyvtárból:

- Tartománynév
- Ügyfél-azonosító
- Titkos ügyfélkulcs

Ezekre az értékekre a jelentéskészítési API-hívások konfigurálásakor. 

### <a name="get-your-domain-name"></a>A tartománynév beszerezése

**A tartománynév beszerezése:**

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Az **Azure Active Directory** lapon válassza az **Egyéni tartománynevek**lehetőséget.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Másolja a tartománynevet a tartományok listájából.


### <a name="get-your-applications-client-id"></a>Az alkalmazás ügyfélazonosítójának beszereznie

**Az alkalmazás ügyfélazonosítójának lekért:**

1. Az [Azure Portalon](https://portal.azure.com)kattintson a bal oldali navigációs ablakban az **Azure Active Directory**elemre.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Válassza ki az alkalmazást az **Alkalmazásregisztrációk** lapon.

3. Az alkalmazás lapon keresse meg az **Alkalmazásazonosító** t, és válassza **a Másoláshoz gombra kattintva.**

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Az alkalmazás ügyféltitkos adatainak beszereznie
 Kerülje a hibákat próbál hozzáférni a naplókat, vagy jelentkezzen be az API-val.

**Az alkalmazás ügyféltitkos adatainak beszerezése:**

1. Az [Azure Portalon](https://portal.azure.com)kattintson a bal oldali navigációs ablakban az **Azure Active Directory**elemre.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Válassza ki az alkalmazást az **Alkalmazásregisztrációk** lapon.

3.  Válassza a **Tanúsítványok és titkos kulcsok** lehetőséget az **API-alkalmazás** lapon, az **Ügyféltitkok** csoportban kattintson a **+ Új ügyféltitok**gombra. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Az **Ügyféltitkos adatok hozzáadása** lapon adja hozzá a következőket:

    a. A **Leírás** mezőbe írja `Reporting API`be a következőt:

    b. Lejár **,** válassza **a 2 év alatt**lehetőséget.

    c. Kattintson a **Mentés** gombra.

    d. Másolja a kulcs értékét.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Hibaelhárítás a jelentéskészítő API-ban

Ez a szakasz felsorolja azokat a gyakori hibaüzeneteket, amelyek a Microsoft Graph API használatával történő tevékenységjelentések elérésekor, valamint a megoldásuk lépéseit sorathatják fel.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Hiba: Nem sikerült lepelni a felhasználói szerepköröket a Microsoft Graphprogramból

 Jelentkezzen be fiókjába a Graph Explorer felhasználói felületén lévő mindkét bejelentkezési gombbal, hogy elkerülje a hiba észlelését, amikor a Graph Explorer használatával próbál bejelentkezni. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Hiba: Nem sikerült a prémium licencellenőrzést végezni a Microsoft Graph-ból 

Ha a Graph Explorer használatával a bejelentkezések elérése közben a hibaüzenet befut, válassza az **Engedélyek módosítása parancsot** a bal navigációs navigációs eszköz alatt, majd válassza a **Tasks.ReadWrite** és **Directory.Read.All**lehetőséget. 

![Engedélyek felhasználói felületének módosítása](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: A bérlő nem B2C, vagy a bérlő nem rendelkezik prémium licenccel

A bejelentkezési jelentések eléréséhez Azure Active Directory prémium 1 (P1) licenc szükséges. Ha ez a hibaüzenet jelenik meg a bejelentkezések elérése közben, győződjön meg arról, hogy a bérlő rendelkezik egy Azure AD P1 licenccel.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Hiba: Az engedélyezett szerepkörök nem tartalmazzák a Felhasználót. 

 Kerülje a hibákat próbál hozzáférni a naplókat, vagy jelentkezzen be az API-val. Győződjön meg arról, hogy fiókja része a **Security Reader** vagy a **Report Reader** szerepkör az Azure Active Directory-bérlő.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: Az alkalmazásból hiányzik az AAD "Címtáradatok olvasása" engedélye 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Hiba: Az alkalmazásból hiányzik a Microsoft Graph API "Az összes naplóadat olvasása" engedély

Kövesse az Előfeltételek az [Azure Active Directory jelentéskészítési API eléréséhez,](howto-configure-prerequisites-for-reporting-api.md) győződjön meg arról, hogy az alkalmazás fut a megfelelő engedélyeket. 

## <a name="next-steps"></a>További lépések

* [Adatok lekérése az Azure Active Directory Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
* [Naplózási API-hivatkozás](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Bejelentkezési tevékenység jelentés API-jának hivatkozása](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
