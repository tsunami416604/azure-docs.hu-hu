---
title: Dynamics CRM | Azure Piactér
description: A Dynamics CRM vezetőkezelésének konfigurálása.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 524ae203a311d538431205bf8c6498de45aeb4d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280303"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Érdeklődőkezelés konfigurálása a Dynamics CRM online rendszeréhez

Ez a cikk azt ismerteti, hogyan állítható be a Dynamics CRM Online az értékesítési érdeklődők feldolgozásához.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következő felhasználói engedélyek szükségesek:
- A megoldás telepítéséhez rendszergazdaként kell eltekintenie a Dynamics CRM Online-példányon.
- Az érdeklődőszolgáltatás új szolgáltatásfiókjának létrehozásához bérlői rendszergazdának kell lennie.

<a name="install-the-solution"></a>A megoldás telepítése
--------------------

1.  Töltse le a [Microsoft Piactér érdeklődőírója megoldást,](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) és mentse helyileg.

2.  Nyissa meg a Dynamics CRM Online alkalmazást, és nyissa meg a Beállítások lapot.
    >[!NOTE]
    >Ha a következő képernyőfelvételen nem láthatók a beállítások, akkor nem rendelkezik a szükséges engedélyekkel.
 
       ![Dynamics beállítási nézete](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Válassza **az Importálás**lehetőséget, majd válassza ki az 1.
 
    ![Dynamics importálási beállítás](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Fejezze be a megoldás telepítését.

## <a name="configure-user-permissions"></a>Felhasználói engedélyek konfigurálása

Ahhoz, hogy érdeklődőket írjon a Dynamics CRM-példányba, meg kell osztania velünk egy szolgáltatási fiókot, és konfigurálnia kell a fiók engedélyeit.

Az alábbi lépésekkel hozhatja létre a szolgáltatásfiókot, és engedélyeket rendelhet hozzá. Használhatja **az Azure Active Directoryt** vagy az **Office 365-öt.**

### <a name="azure-active-directory"></a>Azure Active Directory

Azért javasoljuk ezt a lehetőséget, mert a felhasználónév/jelszó frissítésének további előnye, hogy folyamatosan érdeklődik. Az Azure Active Directory beállítás használatához adja meg az alkalmazásazonosítót, az alkalmazáskulcsot és a címtárazonosítót az Active Directory-alkalmazásból.

Az alábbi lépésekkel konfigurálhatja az Azure Active Directory dynamics CRM-hez szolgáltatását.

1.  Jelentkezzen be az [Azure Portalra,](https://portal.azure.com/) és válassza ki az Azure Active Directory-szolgáltatást.

2.  Válassza **a Tulajdonságok lehetőséget,** majd másolja a **címtárazonosítót**. Ez a bérlői fiók azonosítója, amelyet a Felhőpartner-portálon kell használnia.

    ![Könyvtárazonosító beszerezése](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Válassza **az Alkalmazásregisztrációk**lehetőséget, majd az **Új alkalmazás regisztrációja**lehetőséget.
4.  Adja meg az alkalmazás nevét.
5.  A Típus mezőben válassza a **Web app / API**lehetőséget.
6.  Adja meg az URL-címet. Ez a mező nem szükséges az érdeklődőkhöz, de szükséges egy alkalmazás létrehozásához.
7. Kattintson a **Létrehozás** gombra.
8.  Most, hogy az alkalmazás regisztrálva van, válassza **a Tulajdonságok** lehetőséget, majd válassza **az alkalmazásazonosító másolása**lehetőséget. Ezeket a kapcsolati adatokat a Felhőpartner-portálon fogja használni.
9.  A Tulajdonságok csoportban állítsa be az alkalmazást több-bérlősként, majd válassza a **Mentés lehetőséget.**

10. Válassza a **Kulcsok lehetőséget,** és hozzon létre egy új kulcsot úgy, hogy az Időtartam beállítás *soha nem jár le.* A kulcs létrehozásához válassza a **Mentés** gombot. 
11. A Billentyűk menüben válassza **a Kulcs értékének másolása parancsot.** Mentse az érték egy példányát, mert szüksége lesz rá a Cloud Partner Portalon.
    
    ![Dynamics kap regisztrált kulcs](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Válassza **a Szükséges engedélyek,** majd **a Hozzáadás**lehetőséget. 
13. Válassza a **Dynamics CRM Online** lehetőséget új API-ként, és ellenőrizze az *Access CRM Online szervezeti felhasználóként való engedélyét.*

14. A Dynamics CRM rendszerben nyissa meg a Felhasználók lapot, és válassza az "Engedélyezve lévő felhasználók" legördülő menüt az **Alkalmazásfelhasználók**elemre való váltáshoz.
    
    ![Alkalmazás felhasználói](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Új felhasználó létrehozásához válassza az **Új** lehetőséget. Válassza ki a **USER: APPLICATION USER** legördülő menüt.
    
    ![Új alkalmazásfelhasználó hozzáadása](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Az **Új felhasználó**csoportban adja meg a kapcsolathoz használni kívánt nevet és e-mailt. Illessze be az Azure Portalon létrehozott alkalmazás **alkalmazásazonosítóját.**

     ![Új felhasználó konfigurálása](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. A felhasználó kapcsolatának konfigurálásának befejezéséhez nyissa meg a jelen cikk "Biztonsági beállítások" című témakörét.

### <a name="office-365"></a>Office 365

Ha nem szeretné használni az Azure Active Directoryt, regisztrálhat egy új felhasználót a *Microsoft 365 Felügyeleti központban.* 90 naponta frissítenie kell felhasználónevét/jelszavát, hogy továbbra is érdeklődjön.

Az Office 365 for Dynamics CRM konfigurálásához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Microsoft 365 Felügyeleti központba](https://admin.microsoft.com).

2. Válassza a **Rendszergazda** csempét.

    ![Office Online-rendszergazda](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Válassza **a Felhasználó hozzáadása**lehetőséget.

    ![Felhasználó hozzáadása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Hozzon létre egy új felhasználót az érdeklődőíró szolgáltatáshoz. Adja meg az alábbi beállításokat:

    -   Adjon meg egy jelszót, és törölje a jelet a "A felhasználó jelszómódosítása az első bejelentkezéskor" jelölőnégyzetből.
    -   A felhasználó szerepköreként válassza a "Felhasználó (rendszergazdai hozzáférés)" lehetőséget.
    -   Válassza ki a következő képernyőfelvételen látható terméklicencet. A választott licencért díjat számítunk fel. A megoldás a Dynamics CRM Online Basic licenccel is működni fog.
    
    ![Felhasználói engedélyek és licenc konfigurálása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Biztonsági beállítások

Az utolsó lépés az, hogy a felhasználó létrehozott írni az érdeklődőket.

1.  Jelentkezzen be a Dynamics CRM rendszerbe online.
2.  A **Beállítások párbeszédpanelen**válassza a **Biztonság**lehetőséget.
    
    ![Biztonsági beállítások](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Jelölje ki a Felhasználói engedélyek ben létrehozott **felhasználót,** majd válassza **a Felhasználói szerepkörök kezelése**lehetőséget. A szerepkör hozzárendeléséhez jelölje be a **Microsoft Piactér érdeklődőíróját.**

    ![Felhasználói szerepkör hozzárendelése](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Ezt a szerepkört az importált megoldás hozta létre, és csak az érdeklődők írásához és a megoldás verziójának a kompatibilitás biztosításához történő nyomon követéséhez szükséges engedélyekkel rendelkezik.

4.  A Biztonság csoportban válassza a **Biztonsági szerepkörök** lehetőséget, és keresse meg a Microsoft Piactér érdeklődőírójának szerepkörét.
    
    ![Biztonsági vezető írójának konfigurálása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Válassza a **Core Records (Alapvető rekordok)** lapot.

    ![Létrehozás/olvasás/írás engedélyezése a felhasználó számára](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Letekerés

Fejezze be a Dynamics CRM konfigurálását az érdeklődőkezeléshez a létrehozott fiókadatok felhőpartneri portálhoz való hozzáadásával. Példa:

-   **Azure Active Directory** - **alkalmazásazonosító** (például: *23456052-aaaa-bbbb-8662-1234df56788f*), **címtárazonosító** (például: *12345678-8af1-4as f-1234-12234d01db47*), valamint **alkalmazáskulcs** (például: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **Office 365** - **url-címe** (például: *https://contoso.crm4.dynamics.com*), **Felhasználónév** (például *contoso\@contoso.onmicrosoft.com*) és **Jelszó** (például: P *\@ssw0rd*).
