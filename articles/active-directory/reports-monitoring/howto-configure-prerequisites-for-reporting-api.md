---
title: Az Azure Active Directory reporting API elérésének előfeltételeit |} A Microsoft Docs
description: Ismerje meg a az Azure AD reporting API elérésének előfeltételeit
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f72d15707d9f56b9e9b5a5d527d1204007c40afa
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621972"
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


## <a name="next-steps"></a>További lépések

* [Adatok lekérése az Azure Active Directory reporting API és tanúsítványok használatával](tutorial-access-api-with-certificates.md)
* [Naplózási referencia API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Bejelentkezési tevékenységek jelentésének API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
