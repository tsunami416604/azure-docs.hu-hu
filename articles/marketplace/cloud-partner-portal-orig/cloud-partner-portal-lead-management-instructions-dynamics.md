---
title: A Dynamics CRM |} A Microsoft Docs
description: Állítsa be a Dynamics CRM lead felügyelet.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 8c432146d33db992a0ae612dfc56ace9460ade17
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870859"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurálható a érdeklődő a Dynamics CRM online

Ez a cikk ismerteti, hogyan állítható be a Dynamics CRM Online értékesítési érdeklődők feldolgozásához.

## <a name="prerequisites"></a>Előfeltételek

A következő felhasználói engedélyekkel szükség ebben a cikkben ismertetett lépéseket követve:
- A Dynamics CRM Online-példány megoldás telepítése az rendszergazdájának lennie kell.
- Meg kell lennie a bérlői rendszergazda hozhat létre egy új érdeklődő service szolgáltatásfiókot.

<a name="install-the-solution"></a>A megoldás telepítése
--------------------

1.  Töltse le a [a Microsoft Marketplace vezethet író megoldás](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) , és mentse helyileg.

2.  Nyissa meg a Dynamics CRM Online-hoz, és nyissa meg a beállításokat.
    >[!NOTE]
    >Ha nem látja a következő képernyőfelvételen a beállításokat, majd, nem rendelkezik a szükséges engedélyekkel.
 
       ![Dynamics-beállítás megtekintése](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Válassza ki **importálás**, majd válassza ki a letöltött megoldást az 1. lépésben.
 
    ![Dynamics-importálási beállítás](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  A megoldás telepítésének befejezéséhez.

## <a name="configure-user-permissions"></a>Felhasználói engedélyek konfigurálása

Írhat érdeklődőket a Dynamics CRM-példányon megoszthatja velünk a kapcsolatot a szolgáltatásfiók és-engedélyek konfigurálása.

A következő lépések használatával létrehozni a szolgáltatásfiókot, és az engedélyeket. Használhat **Azure Active Directory** vagy **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Ajánlott ezt a lehetőséget, mivel a felhasználónév/jelszó frissítése annak érdekében, hogy továbbra is megkapja az érdeklődők soha nem kellene a további előnnyel kap. Használja az Azure Active Directory lehetőséget, adja meg az alkalmazás azonosítója, az Alkalmazáskulcs és a címtár-azonosító az Active Directory-alkalmazás.

Az alábbi lépések segítségével az Azure Active Directory konfigurálása a Dynamics CRM.

1.  Jelentkezzen be a [az Azure portal](https://portal.azure.com/) majd válassza ki az Azure Active Directory szolgáltatást.

2.  Válassza ki **tulajdonságok** , majd másolja a **címtár-azonosító**. Ez az a bérlő azonosítója, amely a Cloud Partner portálra használni kell.
    ![Címtár-azonosító beszerzése](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Válassza ki **alkalmazásregisztrációk**, majd válassza ki **új alkalmazásregisztráció**.
4.  Adja meg az alkalmazás nevét.
5.  Típus, válassza ki a **webalkalmazás / API**.
6.  Adjon meg egy URL-címet. Ez a mező nem szükséges, az érdeklődők, de szükség az alkalmazás létrehozása.
7. Kattintson a **Létrehozás** gombra.
8.  Most, hogy az alkalmazás regisztrálva lesz, válassza ki a **tulajdonságok** majd **az alkalmazásazonosító másolása**. A Cloud Partner portálra a kapcsolat ezt az információt fogja használni.
9.  A Tulajdonságok állítja be az alkalmazás több-bérlős, és válassza ki **mentése**.

10. Válassza ki **kulcsok** , és hozzon létre egy új kulcsot időtartamú *soha nem jár le*. Válassza ki **mentése** a kulcs létrehozásához. 
11. Válassza a kulcsok menü **kulcsérték mentése.** Mentse ezt az értéket másolatát, mert lesz szüksége a Cloud Partner portálra.
    
    ![Dynamics regisztrált kulcs lekérése](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
12. Válassza ki **szükséges engedélyek** majd **Hozzáadás**. 
13. Válassza ki **Dynamics CRM Online** az új API-ként, és ellenőrizze a szülőmappára vonatkozóan *hozzáférés CRM Online céges felhasználóként*.

14. A Dynamics CRM, a felhasználók területen, és válassza ki a váltva a "Engedélyezett felhasználók" legördülő **alkalmazás felhasználóinak**.
    
    ![Alkalmazás felhasználói számára](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Válassza ki **új** egy új felhasználó létrehozásához. Válassza ki a **felhasználói: alkalmazás felhasználói** legördülő listából.
    
    ![Új alkalmazás-felhasználó hozzáadása](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. A **új felhasználó**, adja meg a nevét, és ehhez a kapcsolathoz használni kívánt e-mail. Illessze be a **alkalmazásazonosító** az alkalmazás az Azure Portalon létrehozott.
     ![Új felhasználó konfigurálása](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Nyissa meg a "Biztonsági beállítások" Ez a cikk a kapcsolatot a felhasználó konfigurálásának befejezéséhez.

### <a name="office-365"></a>Office 365

Ha nem szeretné használni az Azure Active Directoryban, regisztrálhat egy új felhasználó az Office 365 felügyeleti portálon. Kötelező frissítéséhez a felhasználónév/jelszó 90 naponként továbbra is megkaphatja az érdeklődők fogja.

A következő lépések segítségével konfigurálhatja a Office 365-höz a Dynamics CRM.

1. Jelentkezzen be a [a Microsoft Office 365 felügyeleti portál](https://go.microsoft.com/fwlink/?LinkId=225975).

2. Válassza ki a **rendszergazdai** csempe ![Office Online felügyelet](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Válassza ki **felhasználó hozzáadása**.

    ![Felhasználó hozzáadása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Hozzon létre egy új felhasználót a az érdeklődő-író szolgáltatást. A következő beállításokat:

    -   Adjon meg egy jelszót, és a "Ellenőrizze a felhasználó első bejelentkezéskor jelszómódosításra" jelet.
    -   Válassza ki a "Felhasználó (nincs rendszergazdai hozzáférése)" a felhasználó az a szerepkörként.
    -   Válassza ki azt a terméklicencet, a következő képernyőfelvételen látható. Ön úgy dönt, a licenc kell fizetnie. A megoldás is a Dynamics CRM Online alapszintű licenc fog működni.
    ![Felhasználói engedélyek és a licenc konfigurálása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Biztonsági beállítások

Az utolsó lépés, hogy a felhasználó írhat az érdeklődők létrehozott.

1.  Bejelentkezés a Dynamics CRM Online-bA.
2.  A **beállítások**válassza **biztonsági**.
    
    ![Biztonsági beállítások](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Válassza ki a felhasználót, amelyet **felhasználói engedélyek**, majd válassza ki **felhasználói szerepkörök kezelése**. Ellenőrizze **a Microsoft Marketplace vezethet író** a szerepkör hozzárendelése.
    ![Felhasználói szerepkör hozzárendelése](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Ez a szerepkör jön létre a megoldással, hogy az importált, és csak jogosult írni az érdeklődőket és a megoldás verzióra való kompatibilitás érdekében nyomon követéséhez.

4.  A biztonság, válassza ki **biztonsági szerepkörök** , és keresse meg a szerepkört a Microsoft Marketplace vezethet író.
    ![Biztonsági vezető író konfigurálása](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Válassza ki a **fő rekordok** fülre. A felhasználó típusú entitás felhasználói felület létrehozása/olvasási/írási engedélyezése.

    ![Hozzon létre/olvasási/írási felhasználó engedélyezése](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Belefoglalni

Fejezze be a Dynamics CRM lead felügyelet konfigurálása a Cloud Partner portálra ad hozzá a létrehozott fiók adatait. Példa:

-   **Az Azure Active Directory** - **alkalmazásazonosító** (Példa: *23456052-aaaa-bbbb-8662-1234df56788f*), **címtár-azonosító** (Példa: *12345678-8af1-4asf-1234-12234d01db47*), és **Alkalmazáskulcsot** (Példa: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn ST122345nBc =*).
-   **Az Office 365** - **URL-cím** (Példa: *https://contoso.crm4.dynamics.com*), **felhasználónév** (Példa: *contoso\@ contoso.onmicrosoft.com*), és **jelszó** (Példa: *P\@ssw0rd*).
