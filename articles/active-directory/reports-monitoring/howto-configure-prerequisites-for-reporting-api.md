---
title: A Azure Active Directory jelentési API elérésének előfeltételei | Microsoft Docs
description: Tudnivalók az Azure AD Reporting API elérésének előfeltételeiről
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f925a86504d68fd08b83c63e4da8b37b4aa25f85
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989903"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>A Azure Active Directory jelentési API elérésének előfeltételei

Az [Azure Active Directory (Azure AD) Reporting API-k](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

A jelentéskészítési API a [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) használatával engedélyezi a hozzáférést a webes API-khoz.

A jelentéskészítési API-hoz való hozzáférés előkészítéséhez a következőket kell tennie:

1. [Szerepkörök hozzárendelése](#assign-roles)
2. [Egy alkalmazás regisztrálása](#register-an-application)
3. [Engedélyek megadása](#grant-permissions)
4. [Konfigurációs beállítások összegyűjtése](#gather-configuration-settings)

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Ha a jelentéskészítési adathoz az API-n keresztül szeretne hozzáférni, a következő szerepkörök egyikét kell hozzárendelni:

- Biztonsági olvasó

- Biztonsági rendszergazda

- Globális rendszergazda


## <a name="register-an-application"></a>Alkalmazás regisztrálása

Akkor is regisztrálnia kell egy alkalmazást, ha parancsfájl használatával éri el a jelentéskészítési API-t. Ez egy alkalmazás- **azonosítót**ad meg, amely szükséges az engedélyezési hívásokhoz, és lehetővé teszi a kód számára a jogkivonatok fogadását.

Ha úgy szeretné konfigurálni a címtárat, hogy hozzáférhessen az Azure AD Reporting API- [](https://portal.azure.com) hoz, be kell jelentkeznie a Azure Portalba egy olyan Azure-beli rendszergazdai fiókkal, amely az Azure ad-bérlő **globális rendszergazdai** címtár szerepkörének tagja is.

> [!IMPORTANT]
> A rendszergazdai jogosultságokkal rendelkező hitelesítő adatokkal működő alkalmazások nagyon hatékonyak lehetnek, ezért ügyeljen arra, hogy az alkalmazás AZONOSÍTÓját és a titkos kulcs hitelesítő adatait biztonságos helyen tárolja.
> 

**Azure AD-alkalmazás regisztrálása:**

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. A **Azure Active Directory** lapon válassza a **Alkalmazásregisztrációk**lehetőséget.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. A **Alkalmazásregisztrációk** lapon válassza az **új alkalmazás regisztrálása**lehetőséget.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. A **Létrehozás** oldalon hajtsa végre a következő lépéseket:

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. A **név** szövegmezőbe írja be `Reporting API application`a következőt:.

    b. Az **alkalmazás típusa**mezőben válassza a **webalkalmazás/API**lehetőséget.

    c. A **bejelentkezési URL** szövegmezőbe írja be a következőt `https://localhost`:.

    d. Kattintson a **Létrehozás** gombra. 


## <a name="grant-permissions"></a>Engedélyek megadása 

Az elérni kívánt API-tól függően a következő engedélyeket kell megadnia az alkalmazásnak:  

| API | Engedély |
| --- | --- |
| Windows Azure Active Directory | Címtáradatok olvasása |
| Microsoft Graph | Az összes naplózási napló adatolvasása |


![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/36.png)

A következő szakasz az API-k lépéseit sorolja fel. Ha nem szeretne hozzáférni valamelyik API-hoz, ugorja át a kapcsolódó lépéseket.

**Az API-k használatának engedélyezése az alkalmazás számára:**

1. Válassza ki az alkalmazást az alkalmazás regisztrációi lapján, és válassza a **Beállítások**lehetőséget. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. A **Beállítások** lapon válassza a **szükséges engedélyek**lehetőséget. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. A **szükséges engedélyek** lapon, az **API** listában kattintson a **Windows Azure Active Directory**elemre. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. A **hozzáférés engedélyezése** lapon válassza a **Címtáradatok olvasása** , majd a **Bejelentkezés és a felhasználói profil olvasása**lehetőséget. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. A felső eszköztáron kattintson a **Mentés**gombra.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. A **szükséges engedélyek** lapon, a felső eszköztáron kattintson a **Hozzáadás**gombra.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. Az **API-hozzáférés hozzáadása** lapon kattintson **az API kiválasztása**lehetőségre.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. Az **API kiválasztása** lapon kattintson a **Microsoft Graph**elemre, majd a **kiválasztás**elemre.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. A **hozzáférés engedélyezése** lapon válassza az **összes naplózási naplóbeli információ olvasása**lehetőséget, majd kattintson a **kiválasztás**gombra.  

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. Az **API-hozzáférés hozzáadása** lapon kattintson a **kész**gombra.  

11. A **szükséges engedélyek** lapon, a felül található eszköztáron. kattintson az **engedélyek megadása**elemre, majd az **Igen**gombra.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Konfigurációs beállítások összegyűjtése 

Ez a szakasz bemutatja, hogyan kérheti le a következő beállításokat a címtárból:

- Tartománynév
- Ügyfél-azonosító
- Titkos ügyfélkód

Ezekre az értékekre szüksége lesz a jelentéskészítési API-hoz való hívások konfigurálásakor. 

### <a name="get-your-domain-name"></a>Tartománynév beszerzése

**A tartománynév beszerzése:**

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs paneljén válassza a **Azure Active Directory**lehetőséget.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. A **Azure Active Directory** lapon válassza az **Egyéni tartománynevek**lehetőséget.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Másolja a tartománynevet a tartományok listájából.


### <a name="get-your-applications-client-id"></a>Az alkalmazás ügyfél-AZONOSÍTÓjának beolvasása

**Az alkalmazás ügyfél-AZONOSÍTÓjának beszerzése:**

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs ablaktábláján kattintson az **Azure Active Directory**elemre.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Válassza ki az alkalmazást az alkalmazás regisztrációi lapján.

3. Az alkalmazás oldalon navigáljon az **alkalmazás** -azonosítóhoz, és válassza a **Másolás gombra**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Az alkalmazás ügyfél-titkának beszerzése
Az alkalmazás ügyfél-titkának beszerzéséhez létre kell hoznia egy új kulcsot, és mentenie kell az értékét az új kulcs mentésekor, mert az értéket később már nem lehet lekérni.

**Az alkalmazás ügyfél-titkos kódjának beszerzése:**

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs ablaktábláján kattintson az **Azure Active Directory**elemre.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Válassza ki az alkalmazást az alkalmazás regisztrációi lapján.

3. Az alkalmazás lap felső eszköztárán válassza a **Beállítások**lehetőséget. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. A **Beállítások** lap **API-hozzáférés** szakaszában kattintson a **kulcsok**elemre. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. A **kulcsok** oldalon hajtsa végre a következő lépéseket:

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. A **Leírás** szövegmezőbe írja be `Reporting API`a következőt:.

    b. Ahogy **lejár**, válassza **a 2 év**lehetőséget.

    c. Kattintson a **Save** (Mentés) gombra.

    d. Másolja a kulcs értékét.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Hibák elhárítása a jelentéskészítési API-ban

Ez a szakasz azokat a gyakori hibaüzeneteket sorolja fel, amelyekkel az MS Graph API használatával és a megoldásuk lépéseivel férhet hozzá a tevékenységek jelentéseihez.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP belső kiszolgálóhiba a Microsoft Graph v2-végpont elérésekor

Jelenleg nem támogatjuk a Microsoft Graph v2-végpontot – ügyeljen arra, hogy a Microsoft Graph v1-végpont használatával hozzáférhessen a tevékenység naplóihoz.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Hiba: Nem sikerült beolvasni a felhasználói szerepköröket az AD Graph-ból

Ez a hibaüzenet akkor jelenik meg, amikor a Graph Explorer használatával próbál hozzáférni a bejelentkezésekhez. Győződjön meg arról, hogy a Graph Explorer felhasználói felületének mindkét bejelentkezési gombján bejelentkezett a fiókjába, ahogy az alábbi képen is látható. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Hiba: Nem sikerült a prémium szintű licenc-ellenőrzések végrehajtása az AD Graph-ból 

Ha ezt a hibaüzenetet akkor futtatja, amikor a Graph Explorer használatával próbál hozzáférni a bejelentkezésekhez, válassza a bal oldali navigációs menüben a fiók **módosítása** lehetőséget, majd válassza a **feladatok. READWRITE** és a **könyvtár. olvasás. összes**lehetőséget. 

![Engedélyek módosítása felhasználói felület](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: Sem a bérlő sem a B2C, sem a bérlő nem rendelkezik prémium szintű licenccel

A bejelentkezési jelentések eléréséhez Azure Active Directory Premium 1 (P1) licenc szükséges. Ha ez a hibaüzenet jelenik meg a bejelentkezések elérésekor, győződjön meg arról, hogy a bérlője Azure AD P1 licenccel rendelkezik.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hiba: A felhasználó nem szerepel az engedélyezett szerepkörökben 

Ha ez a hibaüzenet akkor jelenik meg, amikor az API-val megpróbál hozzáférni a naplókhoz vagy a bejelentkezésekhez, győződjön meg arról, hogy a fiókja a Azure Active Directory-bérlő **biztonsági olvasójának** vagy a **jelentéskészítő olvasó** szerepkörének része. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: Az alkalmazásból hiányzik a HRE olvasása engedély 

Kövesse az előfeltételekben ismertetett lépéseket a [Azure Active Directory jelentési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) , hogy az alkalmazás a megfelelő engedélyekkel legyen fut. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Hiba: Az alkalmazás hiányzó MSGraph API "a naplózási napló összes adatszolgáltatásának olvasása" engedély

Kövesse az előfeltételekben ismertetett lépéseket a [Azure Active Directory jelentési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) , hogy az alkalmazás a megfelelő engedélyekkel legyen fut. 

## <a name="next-steps"></a>További lépések

* [Adatlekérdezés a Azure Active Directory Reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
* [Naplózási API-referenciák](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [A bejelentkezési tevékenység jelentésének API-referenciája](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
