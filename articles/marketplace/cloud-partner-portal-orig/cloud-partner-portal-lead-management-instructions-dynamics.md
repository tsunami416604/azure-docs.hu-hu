---
title: Dynamics CRM | Azure piactér
description: Állítsa be a Dynamics CRM-beli érdeklődők felügyeletét.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416323"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>A Dynamics CRM Online-hoz készült érdeklődői felügyelet konfigurálása

Ez a cikk bemutatja, hogyan állíthatja be a Dynamics CRM Online-t az értékesítési érdeklődők feldolgozásához.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következő felhasználói engedélyek szükségesek:
- A megoldás telepítéséhez rendszergazdának kell lennie a Dynamics CRM Online-példányon.
- Ahhoz, hogy új szolgáltatásfiókot hozzon létre az érdeklődő szolgáltatáshoz, bérlői rendszergazdának kell lennie.

<a name="install-the-solution"></a>A megoldás telepítése
--------------------

1.  Töltse le a [Microsoft Marketplace vezető író megoldását](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) , és mentse helyileg.

2.  Nyissa meg a Dynamics CRM Online-t, és lépjen a beállítások menüpontra.
    >[!NOTE]
    >Ha nem látja a beállításokat a következő képernyőfelvételen, akkor nem rendelkezik a szükséges engedélyekkel.
 
       ![Dynamics telepítési nézet](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Válassza az **Importálás**lehetőséget, majd válassza ki az 1. lépésben letöltött megoldást.
 
    ![Dynamics importálási beállítás](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Fejezze be a megoldás telepítését.

## <a name="configure-user-permissions"></a>Felhasználói engedélyek konfigurálása

Ha a Dynamics CRM-példányba szeretne érdeklődőket írni, meg kell osztania egy szolgáltatásfiókot az USA-ban, és konfigurálnia kell a fiók engedélyeit.

A szolgáltatásfiók létrehozásához és az engedélyek hozzárendeléséhez kövesse az alábbi lépéseket. **Azure Active Directory** vagy **Office 365**-et is használhat.

### <a name="azure-active-directory"></a>Azure Active Directory

Javasoljuk, hogy ezt a beállítást használja, mivel a további előnyöket nem kell frissítenie a felhasználónevének/jelszavának a beszerzése érdekében. A Azure Active Directory beállítás használatához adja meg az alkalmazás azonosítóját, az alkalmazás kulcsát és a könyvtár azonosítóját a Active Directory alkalmazásból.

A Dynamics CRM Azure Active Directory konfigurálásához kövesse az alábbi lépéseket.

1.  Jelentkezzen be [Azure Portal](https://portal.azure.com/) , majd válassza ki a Azure Active Directory szolgáltatást.

2.  Válassza a **Tulajdonságok** lehetőséget, majd másolja a **címtár-azonosítót**. Ezt a bérlői fiók azonosítását kell használni a Cloud Partner Portalban.

    ![Könyvtár AZONOSÍTÓjának beolvasása](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza az **új alkalmazás regisztrálása**lehetőséget.
4.  Adja meg az alkalmazás nevét.
5.  A típus mezőben válassza a **Web App/API**lehetőséget.
6.  Adjon meg egy URL-címet. Ez a mező nem szükséges az érdeklődők számára, de az alkalmazás létrehozásához szükséges.
7. Kattintson a **Létrehozás** gombra.
8.  Most, hogy regisztrálta az alkalmazást, válassza a **Tulajdonságok** lehetőséget, majd válassza **az alkalmazás azonosítójának másolása**lehetőséget. Ezt a kapcsolatfelvételi információt a Cloud Partner Portal fogja használni.
9.  A tulajdonságok területen állítsa az alkalmazást több-bérlős értékre, majd válassza a **Mentés**lehetőséget.

10. Válassza a **kulcsok** lehetőséget, és hozzon létre egy új kulcsot, amelynek időtartamát *soha nem jár le*. A kulcs létrehozásához válassza a **Mentés** lehetőséget. 
11. A kulcsok menüben válassza **a kulcs értékének másolása lehetőséget.** Mentse az érték másolatát, mert szüksége lesz rá a Cloud Partner Portal.
    
    ![A Dynamics beolvassa a regisztrált kulcsot](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Válassza a **szükséges engedélyek** lehetőséget, majd válassza a **Hozzáadás**lehetőséget. 
13. Válassza ki a **Dynamics CRM Online** -t az új API-ként, és tekintse meg a *vállalati felhasználók hozzáférésének engedélyezése a CRM Online*-hoz lehetőséget.

14. A Dynamics CRM-ben lépjen a felhasználók elemre, és válassza az "engedélyezett felhasználók" legördülő menüt az **alkalmazás felhasználóinak**való átváltáshoz.
    
    ![Alkalmazás felhasználói](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Új felhasználó létrehozásához válassza az **új** lehetőséget. Válassza ki a **felhasználót: alkalmazás felhasználó** legördülő menüje.
    
    ![Új alkalmazás felhasználójának hozzáadása](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Az **új felhasználó**mezőben adja meg a kapcsolódáshoz használni kívánt nevet és e-mail-címet. Illessze be a Azure Portalban létrehozott alkalmazás **azonosítóját** .

     ![Új felhasználó konfigurálása](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. A jelen cikk "biztonsági beállítások" részében találja a felhasználóhoz való kapcsolódás konfigurálásának befejezését.

### <a name="office-365"></a>Office 365

Ha nem kívánja használni a Azure Active Directoryt, regisztrálhat egy új felhasználót a *Microsoft 365 felügyeleti központban*. Az érdeklődők folytatásához 90 naponta frissítenie kell a felhasználónevét/jelszavát.

A Dynamics CRM-hez készült Office 365 konfigurálásához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Microsoft 365 Felügyeleti központba](https://admin.microsoft.com).

2. Válassza a **rendszergazda** csempét.

    ![Office Online-rendszergazda](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Válassza **a felhasználó hozzáadása**elemet.

    ![Felhasználó hozzáadása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Hozzon létre egy új felhasználót az ólom-író szolgáltatáshoz. Adja meg az alábbi beállításokat:

    -   Adja meg a jelszót, és törölje a jelet a "felhasználói jelszó módosítása az első bejelentkezéskor" lehetőségre.
    -   A felhasználó szerepköre válassza a "felhasználó (nincs rendszergazdai hozzáférés)" lehetőséget.
    -   Válassza ki a következő képernyőfelvételen látható termék licencét. A választott licencért díjat számítunk fel. A megoldás a Dynamics CRM Online alapszintű licenccel is együttműködik.
    
    ![Felhasználói engedélyek és licencek konfigurálása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Biztonsági beállítások

Az utolsó lépés az, hogy lehetővé teszi a létrehozott felhasználó számára az érdeklődők írását.

1.  Jelentkezzen be a Dynamics CRM Online-ba.
2.  A **Beállítások**területen válassza a **Biztonság**elemet.
    
    ![Biztonsági beállítások](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Válassza ki a felhasználói **engedélyekben**létrehozott felhasználót, majd válassza a **felhasználói szerepkörök kezelése**lehetőséget. A szerepkör hozzárendeléséhez keresse meg **Microsoft Marketplace vezető írójat** .

    ![Felhasználói szerepkör kiosztása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Ezt a szerepkört az importált megoldás hozza létre, és csak a megfelelő jogosultsággal rendelkezik az érdeklődők írásához és a megoldás verziójának nyomon követéséhez, hogy biztosítsa a kompatibilitást.

4.  A biztonság területen válassza a **biztonsági szerepkörök** lehetőséget, és keresse meg Microsoft Marketplace érdeklődő-író szerepkörét.
    
    ![Biztonsági vezető író konfigurálása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Válassza az **alaprekordok** fület. engedélyezze a felhasználói entitás felhasználói felületének létrehozás/olvasás/írás funkcióját.

    ![Létrehozás/olvasás/írás engedélyezése a felhasználó számára](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Becsomagolás

Fejezze be a Dynamics CRM-beli érdeklődők felügyeletének konfigurálását úgy, hogy hozzáadja a létrehozott fiók adatait a Cloud Partner Portalhoz. Például:

-   **Azure Active Directory** - az **alkalmazás azonosítóját** (például: *23456052-AAAA-bbbb-8662-1234df56788f*), **könyvtár-azonosító** (példa: *12345678-8af1-4asf-1234-12234d01db47*), és az **alkalmazás kulcsa** (példa: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc =*).
-   **Office 365** - **URL-cím** ( **`https://contoso.crm4.dynamics.com`** például:), **Felhasználónév** (példa **`contoso\@contoso.onmicrosoft.com`**:) és **jelszó** (példa: *P\@ssw0rd*).
