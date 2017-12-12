---
title: "Előfeltételek az Azure AD reporting API eléréséhez |} Microsoft Docs"
description: "További tudnivalók az Azure AD reporting API eléréséhez Előfeltételek"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f1f4d79b59d44bc1c151391290e79026051fe1df
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Az Azure AD reporting API eléréséhez Előfeltételek

A [az Azure AD reporting API-k](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) keresztül REST-alapú API-készlet programozott hozzáférést biztosít. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

A jelentéskészítési API által használt [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) engedélyezéséhez a web API-k eléréséhez. 

A jelentési adatokat juthatnak az API-n keresztül, egy a következő szerepkörrel kell:

- Biztonsági olvasó
- Biztonsági rendszergazda
- Globális rendszergazda


Készítse elő a reporting API a hozzáférést, a következőket kell tennie:

1. Egy alkalmazás regisztrálása 
2. Engedélyek 
3. Konfigurációs beállítások összegyűjtése 

Kérdéseit, vagy visszajelzést, kérjük, [fájlt egy támogatási jegy](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="register-an-azure-active-directory-application"></a>Egy Azure Active Directory-alkalmazás regisztrálása

Kell regisztrálnia az alkalmazást, akkor is, ha a jelentéskészítési API parancsfájl segítségével fér hozzá. Ez lehetővé teszi egy **Alkalmazásazonosító**, ami azonban szükséges engedélyezési-hívások, és lehetővé teszi, hogy a kód jogkivonatokat fogadni.

A címtár az Azure AD reporting API eléréséhez konfigurálásához be kell jelentkeznie az Azure portálra az Azure rendszergazdai fiókkal, amely tagja a **globális rendszergazda** az Azure AD-bérlő a címtár szerepkörrel.

> [!IMPORTANT]
> Ehhez hasonló "rendszergazda" jogosultságokkal credentials futó alkalmazások nagyon hatékonyak lehetnek, így az alkalmazás azonosítója/titkos hitelesítő adatok a biztonságos ellenőrizze.
> 


**Egy Azure Active Directory-alkalmazás regisztrálása:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Az a **Azure Active Directory** panelen kattintson a **App regisztrációk**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Az a **App regisztrációk** panelen, a felső eszköztáron kattintson **új alkalmazás regisztrációja**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Az a **létrehozása** panelen végezze el a következő lépéseket:

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. Az a **neve** szövegmezőhöz típus `Reporting API application`.

    b. Mint **alkalmazástípus**, jelölje be **Web app / API**.

    c. Az a **bejelentkezési URL-cím** szövegmezőhöz típus `https://localhost`.

    d. Kattintson a **Create** (Létrehozás) gombra. 


## <a name="grant-permissions"></a>Engedélyek 

E lépés célja, hogy adja meg az alkalmazás **címtáradatok olvasása** engedélyeket a **Windows Azure Active Directory** API.

![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**Az alkalmazás engedélyezheti az API-t használja:**

1. Az a **App regisztrációk** panel, az alkalmazások listájában kattintson **Reporting API-alkalmazás**.

2. Az a **Reporting API-alkalmazás** panelen, a felső eszköztáron kattintson **beállítások**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Az a **beállítások** panelen kattintson a **szükséges engedélyek**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Az a **szükséges engedélyek** panelen, a a **API** listában, kattintson **Windows Azure Active Directory**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Az a **hozzáférés engedélyezése** panelen válassza **címtáradatok olvasása**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. A felső eszköztáron kattintson **mentése**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Kattintson a **engedélyt adjon**, és kattintson a **Igen**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Konfigurációs beállítások összegyűjtése 
Ez a szakasz bemutatja, hogyan az a Directoryból olvassa ki az alábbi beállításokat:

* Tartománynév
* Ügyfél-azonosító
* Titkos ügyfélkód

A jelentéskészítési API hívásainak konfigurálásakor kell ezeket az értékeket. 

### <a name="get-your-domain-name"></a>A tartománynév beolvasása

**A tartománynév beolvasása:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Az a **Azure Active Directory** panelen kattintson a **egyéni tartománynevek**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Másolja ki a tartomány nevét a tartományok listáját.


### <a name="get-your-applications-client-id"></a>Az alkalmazás ügyfél-azonosító lekérése

**Az alkalmazás ügyfél-azonosító eléréséhez:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Az a **App regisztrációk** panel, az alkalmazások listájában kattintson **Reporting API-alkalmazás**.

3. Az a **Reporting API-alkalmazás** panelen, a **Alkalmazásazonosító**, kattintson a **másolásához kattintson a**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Az alkalmazás ügyfél titkos kulcs beszerzése
Ahhoz, hogy az alkalmazás ügyfélkulcs, meg kell hozzon létre egy új kulcsot, és mentse az új kulcs mentése, mert nincs lehetőség a később többé lekérje ezt az értéket, az értékét.

**Az alkalmazás ügyfélkulcs beolvasása:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Az a **App regisztrációk** panel, az alkalmazások listájában kattintson **Reporting API-alkalmazás**.


3. Az a **Reporting API-alkalmazás** panelen, a felső eszköztáron kattintson **beállítások**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. A a **beállítások** panelen, a a **APIR hozzáférés** kattintson **kulcsok**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Az a **kulcsok** panelen végezze el a következő lépéseket:

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. Az a **leírás** szövegmezőhöz típus `Reporting API`.

    b. Mint **Expires**, jelölje be **2 évben**.

    c. Kattintson a **Save** (Mentés) gombra.

    d. Másolja a kulcs értékét.


## <a name="next-steps"></a>Következő lépések
* Szeretné az Azure AD reporting API-val programozott módon érheti el az adatait? Tekintse meg [Bevezetés az Azure Active Directory Reporting API használatába](active-directory-reporting-api-getting-started.md).
* Ha meg szeretne többet megtudni az Azure Active Directory reporting, tekintse meg a [Azure Active Directory-jelentéskészítés – útmutató](active-directory-reporting-guide.md).  

