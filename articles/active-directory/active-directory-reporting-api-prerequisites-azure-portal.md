---
title: Előfeltételek az Azure Active Directory reporting API eléréséhez |} Microsoft Docs
description: További tudnivalók az Azure AD reporting API eléréséhez Előfeltételek
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: a38447150dd283ff21a852690cfd48477472f7b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588760"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Az Azure Active Directory reporting API-val előfeltételei

Az [Azure Active Directory (Azure AD) Reporting API-k](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) REST-alapú API-kon keresztül biztosítják az adatok szoftveres elérését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

A jelentéskészítési API által használt [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) engedélyezéséhez a web API-k eléréséhez.

Készítse elő a reporting API a hozzáférést, meg kell:

1. Szerepkörök hozzárendelése
2. Egy alkalmazás regisztrálása
3. Engedélyek megadása
4. Konfigurációs beállítások összegyűjtése



## <a name="assign-roles"></a>Szerepkörök hozzárendelése

A jelentési adatokat juthatnak az API-n keresztül, egy a következő szerepkörrel kell:

- Biztonsági olvasó

- Biztonsági rendszergazda

- Globális rendszergazda




## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Kell regisztrálnia az alkalmazást, akkor is, ha a jelentéskészítési API parancsfájl segítségével éri el. Ez lehetővé teszi egy **Alkalmazásazonosító**, ami azonban szükséges engedélyezési-hívások, és lehetővé teszi, hogy a kód jogkivonatokat fogadni.

A címtár az Azure AD reporting API eléréséhez konfigurálásához be kell jelentkeznie az Azure portálra az Azure rendszergazdai fiókkal, amely tagja a **globális rendszergazda** az Azure AD-bérlő a címtár szerepkörrel.

> [!IMPORTANT]
> Ehhez hasonló "rendszergazda" jogosultságokkal credentials futó alkalmazások nagyon hatékonyak lehetnek, így az alkalmazás azonosítója/titkos hitelesítő adatok a biztonságos ellenőrizze.
> 


**Egy Azure Active Directory-alkalmazás regisztrálása:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Az a **Azure Active Directory** kattintson **App regisztrációk**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Az a **App regisztrációk** lapon, a felső eszköztáron kattintson **új alkalmazás regisztrációja**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Az a **létrehozása** lapon, a következő lépésekkel:

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. Az a **neve** szövegmezőhöz típus `Reporting API application`.

    b. Mint **alkalmazástípus**, jelölje be **Web app / API**.

    c. Az a **bejelentkezési URL-cím** szövegmezőhöz típus `https://localhost`.

    d. Kattintson a **Create** (Létrehozás) gombra. 


## <a name="grant-permissions"></a>Engedélyek megadása 

Attól függően, hogy az elérni kívánt API adja meg az alkalmazás a következő engedélyeket kell:  

| API | Engedély |
| --- | --- |
| Windows Azure Active Directoryban | Címtáradatok olvasása |
| Microsoft Graph | Olvassa el az összes naplózási adatainak naplózása |


![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


A következő rész lépéseit sorolja mindkét API-k esetében. Ha nem szeretné, az API-k elérésére, kihagyhatja a kapcsolódó lépések.
 

**Az alkalmazás engedélyt az API-k használata:**

1. Az a **App regisztrációk** lapon, az alkalmazások listájában kattintson **Reporting API-alkalmazás**.

2. Az a **Reporting API-alkalmazás** lapon, a felső eszköztáron kattintson **beállítások**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Az a **beállítások** kattintson **szükséges engedélyek**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Az a **szükséges engedélyek** lap a **API** listában, kattintson **Windows Azure Active Directory**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Az a **hozzáférés engedélyezése** lapon jelölje be **címtáradatok olvasása** és kijelölésének megszüntetése **jelentkezzen be a felhasználói profil olvasása és**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. A felső eszköztáron kattintson **mentése**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Az a **szükséges engedélyek** lapon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. Az a **hozzáadása API-hozzáférés** kattintson **API kiválasztása**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. A a **API kiválasztása** kattintson **Microsoft Graph**, és kattintson a **válasszon**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. A a **hozzáférés engedélyezése** lapon jelölje be **olvassa el az összes naplózási naplóadatok**, és kattintson a **válasszon**.  

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. Az a **hozzáadása API-hozzáférés** kattintson **végzett**.  

12. Az a **szükséges engedélyek** oldalon, a felső eszköztáron. Kattintson a **engedélyt adjon**, és kattintson a **Igen**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Konfigurációs beállítások összegyűjtése 

Ez a szakasz bemutatja, hogyan az a Directoryból olvassa ki az alábbi beállításokat:

- Tartománynév
- Ügyfél-azonosító
- Titkos ügyfélkulcs

A jelentéskészítési API hívásainak konfigurálásakor kell ezeket az értékeket. 

### <a name="get-your-domain-name"></a>A tartománynév beolvasása

**A tartománynév beolvasása:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Az a **Azure Active Directory** kattintson **egyéni tartománynevek**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Másolja ki a tartomány nevét a tartományok listáját.


### <a name="get-your-applications-client-id"></a>Az alkalmazás ügyfél-azonosító lekérése

**Az alkalmazás ügyfél-azonosító eléréséhez:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Az a **App regisztrációk** lapon, az alkalmazások listájában kattintson **Reporting API-alkalmazás**.

3. Az a **Reporting API-alkalmazás** lapon, a **Alkalmazásazonosító**, kattintson a **másolásához kattintson a**.

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Az alkalmazás ügyfél titkos kulcs beszerzése
Ahhoz, hogy az alkalmazás ügyfélkulcs, meg kell hozzon létre egy új kulcsot, és mentse az új kulcs mentése, mert nincs lehetőség a később többé lekérje ezt az értéket, az értékét.

**Az alkalmazás ügyfélkulcs beolvasása:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory**.
   
    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Az a **App regisztrációk** lapon, az alkalmazások listájában kattintson **Reporting API-alkalmazás**.


3. Az a **Reporting API-alkalmazás** lapon, a felső eszköztáron kattintson **beállítások**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. A a **beállítások** lap a **APIR hozzáférés** kattintson **kulcsok**. 

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Az a **kulcsok** lapon, a következő lépésekkel:

    ![Alkalmazás regisztrálása](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. Az a **leírás** szövegmezőhöz típus `Reporting API`.

    b. Mint **Expires**, jelölje be **2 évben**.

    c. Kattintson a **Save** (Mentés) gombra.

    d. Másolja a kulcs értékét.


## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory reporting API-val rendelkező tanúsítványok használatával adatok](active-directory-reporting-api-with-certificates.md).

- [Ismerkedés a Reporting API-k működésével](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Saját megoldás létrehozása](active-directory-reporting-api-getting-started-azure-portal.md#customize)

