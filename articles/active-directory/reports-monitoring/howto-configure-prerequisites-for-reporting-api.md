---
title: Az Azure Active Directory reporting API elérésének előfeltételeit |} A Microsoft Docs
description: Ismerje meg a az Azure AD reporting API elérésének előfeltételeit
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab30dfeccc4ae1c7b09a8f48846ffb9e71cfc23
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436744"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Az Azure Active Directory reporting API elérésének előfeltételeit

Az [Azure Active Directory (Azure AD) Reporting API-k](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

A jelentéskészítő API által használt [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) a webes API-khoz való hozzáférés engedélyezésére.

A jelentéskészítő API hozzáférési előkészítéseként kell tennie:

1. [Szerepkörök hozzárendelése](#assign-roles)
2. [Egy alkalmazás regisztrálása](#register-an-application)
3. [Engedélyek megadása](#grant-permissions)
4. [Konfigurációs beállítások összegyűjtése](#gather-configuration-settings)

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

A jelentési adatok eléréséhez az API-n keresztül, szüksége lesz hozzárendelve a következő szerepkörök egyikét:

- Biztonsági olvasó

- Biztonsági rendszergazda

- Globális rendszergazda


## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Szeretne regisztrálni egy alkalmazást, akkor is, ha a jelentéskészítő API-parancsfájl használatával éri el. Ez lehetővé teszi egy **Alkalmazásazonosító**, amelynek szükség, a hívások és az lehetővé teszi, hogy a kód jogkivonatokat fogadni.

A címtár az Azure AD reporting API eléréséhez konfigurálásához, jelentkezzen be a a [az Azure portal](https://portal.azure.com) egy Azure-rendszergazdai fiókkal, amely tagja is a **globális rendszergazdai** címtárbeli szerepkört az Azure AD-bérlőben.

> [!IMPORTANT]
> Rendszergazdai jogosultságokkal rendelkező hitelesítő adatokat a futó alkalmazások nagyon hatékonyak lehetnek, ezért ellenőrizze, hogy az alkalmazás-azonosító és titkos hitelesítő adatok biztonságos helyen.
> 

**Az Azure AD alkalmazás regisztrálása:**

1. Az a [az Azure portal](https://portal.azure.com)válassza **Azure Active Directory** a bal oldali navigációs ablaktáblán.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Az a **Azure Active Directory** lapon jelölje be **alkalmazásregisztrációk**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Az a **alkalmazásregisztrációk** lapon jelölje be **új alkalmazásregisztráció**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Az a **létrehozás** lapon, a következő lépésekkel:

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Az a **neve** szövegmezőbe írja be `Reporting API application`.

    b. Mint **alkalmazástípus**válassza **webalkalmazás / API**.

    c. Az a **bejelentkezési URL-** szövegmezőbe írja be `https://localhost`.

    d. Kattintson a **Létrehozás** gombra. 


## <a name="grant-permissions"></a>Engedélyek megadása 

API-t szeretné elérni, attól függően kell biztosítania az alkalmazás a következő engedélyekkel:  

| API | Engedély |
| --- | --- |
| Windows Azure Active Directory | Címtáradatok olvasása |
| Microsoft Graph | Olvassa el az összes naplózási adatainak naplózása |


![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/36.png)

Az alábbi szakasz lépéseit ismerteti a mindkét API-k. Ha nem szeretne hozzáférni az API-k valamelyikét, a kapcsolódó lépést kihagyhatja.

**Az API-k használata az alkalmazás engedélyek megadására:**

1. Válassza ki az alkalmazás a **Alkalmazásregisztrációk** lapon, és válassza **beállítások**. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Az a **beállítások** lapon jelölje be **szükséges engedélyek**. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. A a **szükséges engedélyek** lap a **API** listáról **Windows Azure Active Directory**. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. A a **hozzáférés engedélyezése** lapon jelölje be **címtáradatok olvasása** és kijelölésének megszüntetéséhez **jelentkezzen be és felhasználói profil olvasása**. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. A felső eszköztáron kattintson **mentése**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. Az a **szükséges engedélyek** oldalon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. Az a **API-hozzáférés hozzáadása** kattintson **API kiválasztása**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. Az a **API kiválasztása** kattintson **Microsoft Graph**, és kattintson a **válassza**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. A a **hozzáférés engedélyezése** lapon jelölje be **olvassa el az összes naplózási naplóadatok**, és kattintson a **válassza**.  

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. Az a **API-hozzáférés hozzáadása** kattintson **kész**.  

11. Az a **szükséges engedélyek** oldalon, a felső eszköztáron. Kattintson a **engedélyek megadása**, és kattintson a **Igen**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Konfigurációs beállítások összegyűjtése 

Ez a szakasz bemutatja, hogyan tehet szert az alábbi beállításokat a címtárban lévő:

- Tartománynév
- Ügyfél-azonosító
- Titkos ügyfélkulcs

A jelentéskészítő API-hívások konfigurálásakor kell ezeket az értékeket. 

### <a name="get-your-domain-name"></a>A tartománynév beolvasása

**A tartománynév lekérése:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Az a **Azure Active Directory** lapon jelölje be **egyéni tartománynevek**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. A tartománynév átmásolni a tartományok listáját.


### <a name="get-your-applications-client-id"></a>Az alkalmazás ügyfél-azonosító beszerzése

**Az alkalmazás ügyfél-azonosító lekéréséhez:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs panelén kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Válassza ki az alkalmazás a **Alkalmazásregisztrációk** lapot.

3. Lépjen az alkalmazás oldaláról **Alkalmazásazonosító** válassza **kattintson a másoláshoz**.

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Az alkalmazás titkos ügyfélkulcsát beolvasása
Az alkalmazás titkos ügyfélkulcsát lekéréséhez szüksége, hozzon létre egy új kulcsot, és mentse az új kulcs mentése, mert nem lehetséges, hogy lekérje ezt az értéket később már után az értékét.

**Az alkalmazás titkos ügyfélkulcsát lekérése:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs panelén kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Válassza ki az alkalmazás a **Alkalmazásregisztrációk** lapot.

3. Az alkalmazás oldalán, az a felső eszköztáron válassza **beállítások**. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. Az a **beállítások** lap a **API-hozzáférés** területén kattintson **kulcsok**. 

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Az a **kulcsok** lapon, a következő lépésekkel:

    ![Alkalmazás regisztrálása](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. Az a **leírás** szövegmezőbe írja be `Reporting API`.

    b. Mint **lejárat**válassza **2 évben**.

    c. Kattintson a **Save** (Mentés) gombra.

    d. Másolja a kulcs értékét.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>A jelentéskészítő API hibáinak elhárítása

Ez a szakasz ismerteti a Gyakori hibaüzenetek felmerülhet tevékenységre vonatkozó jelentések az MS Graph API-t és a lépések használatával a névfeloldáshoz elérése közben.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500-as HTTP-belső hiba a Microsoft Graph V2 végpont használata közben

Jelenleg nem támogatja a Microsoft Graph-v2 végpont – ellenőrizze, hogy a vizsgálati naplók használata a Microsoft Graph v1 végpont elérésére.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Hiba: Nem sikerült beolvasni a felhasználói szerepköröket a AD Graph

Ezt a hibaüzenetet kaphat bejelentkezések elérésére tett kísérlet során Graph Explorer használatával. Ellenőrizze, hogy jelentkezett be a fiókjába mind a bejelentkezési gomb a Graph Explorer felhasználói felületén, a következő képen látható módon. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Hiba: Az AD Graph premium licenc ellenőrzése nem sikerült 

Ha ez a hibaüzenet bejelentkezések elérése közben Graph Explorer használatával, válassza a **módosítási hozzáférést** alul a bal oldali navigációs, és válassza a fiók **Tasks.ReadWrite** és **Directory.Read.All**. 

![Felhasználói felület engedélyek módosítása](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: Sem bérlő az B2C vagy a bérlő nem rendelkezik premium-licenc

Egy Azure Active Directory premium 1 (P1) jelentkezzen be a jelentések eléréséhez szükséges licencet. Ha ez a hibaüzenet jelenik meg közben: bejelentkezések, ügyeljen arra, hogy a bérlő Azure AD P1 szintű licenccel rendelkezik licenccel.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hiba: Felhasználó nem szerepel az engedélyezett szerepkörök 

Ha ezt a hibaüzenetet látja miközben megpróbált hozzáférni a naplókhoz és a bejelentkezések a API-val, győződjön meg arról, hogy a fiók tagja a **biztonsági olvasó** vagy **jelentés olvasó** szerepkört az Azure Active Directoryban bérlő. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: Az alkalmazás AAD "Címtáradatok olvasása" engedéllyel hiányzik 

Kövesse a lépéseket a [az Azure Active Directory reporting API elérésének előfeltételeit](howto-configure-prerequisites-for-reporting-api.md) annak érdekében, hogy az alkalmazás fut, a megfelelő engedélyekkel együtt. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Hiba: Hiányzik az "Összes naplózási Teljesítménynapló-adatok olvasási" engedélyt MSGraph API alkalmazás

Kövesse a lépéseket a [az Azure Active Directory reporting API elérésének előfeltételeit](howto-configure-prerequisites-for-reporting-api.md) annak érdekében, hogy az alkalmazás fut, a megfelelő engedélyekkel együtt. 

## <a name="next-steps"></a>További lépések

* [Adatok lekérése az Azure Active Directory reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
* [Naplózási referencia API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Bejelentkezési tevékenységek jelentésének API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
