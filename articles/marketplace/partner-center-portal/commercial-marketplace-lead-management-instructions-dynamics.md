---
title: A Dynamics 365 for Customer Engagement vezetőkezelése | Azure Piactér
description: Konfigurálja a Dynamics 365 for Customer Engagement érdeklődőkezelését.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: 8af6b3a451d20bcc9cab3fa4adb9643f82b85e49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288818"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Érdeklődőkezelés konfigurálása a Dynamics 365 for Customer Engagement alkalmazáshoz

Ez a cikk a Dynamics 365 for Customer Engagement (korábban Dynamics CRM Online) beállítását ismerteti, itt olvashat bővebben a piactéri ajánlatból származó értékesítési érdeklődők [feldolgozásához.](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) 

>[!Note]
>Ezek az utasítások a Microsoft által üzemeltetett Dynamics 365 for Customer Engagement környezetre vonatkoznak. Közvetlenül a Dynamics on-prem környezethez való közvetlen csatlakozás jelenleg nem támogatott, vannak más lehetőségek is az érdeklődők fogadására, például egy [https-végpont](./commercial-marketplace-lead-management-instructions-https.md) vagy egy [Azure-tábla](./commercial-marketplace-lead-management-instructions-azure-table.md) konfigurálása érdeklődők fogadására.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következő felhasználói engedélyek szükségesek:

* A megoldás telepítéséhez és az alábbi utasítások követéséhez rendszergazdaként kell eltekintenie a Dynamics 365 for Customer Engagement példányon.
* Bérlői rendszergazdának kell lennie ahhoz, hogy új szolgáltatásfiókot hozzon létre az érdeklődőszolgáltatáshoz, amely érdeklődőket küld a piactéri ajánlatokból.
* Hozzáféréssel kell rendelkeznie az Office 365 felügyeleti portáljához.
* Hozzáféréssel kell rendelkeznie az Azure Portalhoz.

## <a name="install-the-solution"></a>A megoldás telepítése

1.  Töltse le a [Microsoft Piactér érdeklődőírója megoldást,](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) és mentse helyileg a számítógépre.

2.  Nyissa meg a Dynamics 365 for Customer Engagement alkalmazást `https://tenant.crm.dynamics.com`úgy, hogy a Dynamics-példány (például) URL-címére navigál.

3.  A beállításokeléréséhez a felső navigációs sávon a fogaskerék ikon és a **Speciális beállítások lehetőséget** választva érheti el.
 
    ![Dynamics – Speciális beállítások](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Miután a Beállítások lapon a felső navigációs sávon a Beállítás menüt érheti el, és válassza a **Megoldások**lehetőséget.

    >[!Note]
    >Ha a következő képernyőfelvételen nem láthatók a beállítások, akkor nem rendelkezik a folytatáshoz szükséges engedélyekkel. A Dynamics 365 for Customer Engagement példány adminisztrátorát is megszólíthatja.

    ![Dynamics 365 - Megoldások](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Miután a Megoldások lapon válassza az **Importálás** lehetőséget, és keresse meg *azt* a helyt, ahová az 1.

    ![Dynamics 365 az ügyfelek aktivitására – importálás](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. A megoldás importálásának befejezése a Megoldás importálása varázsló val.

## <a name="configure-user-permissions"></a>Felhasználói engedélyek konfigurálása

Ahhoz, hogy érdeklődőket írjon a Dynamics 365 for Customer Engagement példányba, meg kell osztania velünk egy szolgáltatási fiókot, és konfigurálnia kell a fiók engedélyeit.

Az alábbi lépésekkel hozhatja létre a szolgáltatásfiókot, és engedélyeket rendelhet hozzá. Használhatja **az Azure Active Directoryt** vagy az **Office 365-öt.**

>[!Note]
>A kiválasztott hitelesítési beállítás alapján a választás alapján átugorhatja a megfelelő utasításokat. Lásd: [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) vagy Office [365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Ezt a lehetőséget azért javasoljuk, mert a felhasználónév/jelszó frissítésének további előnye, hogy nem kell frissítenie az érdeklődőket. Az Azure Active Directory beállítás használatához adja meg az alkalmazásazonosítót, az alkalmazáskulcsot és a címtárazonosítót az Active Directory-alkalmazásból.

Az alábbi lépésekkel konfigurálhatja az Azure Active Directory for Dynamics 365 for Customer Engagement szolgáltatást.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/)majd válassza ki az Azure Active Directory-szolgáltatást a bal oldali navigációs sávon.

2. Válassza ki a **tulajdonságok** az Azure Active Directory bal navigációs, és másolja a **címtárazonosító** értékét az adott lapon. Mentse ezt az értéket, mivel ez az a *címtárazonosító* érték, amelyet meg kell adnia a közzétételi portálon ahhoz, hogy érdeklődőket kapjon a piactéri ajánlathoz.

    ![Azure Active Directory – tulajdonságok](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Válassza ki **az alkalmazásregisztrációk** az Azure Active Directory bal navigációs, majd válassza az **új regisztráció** az adott lapon.
4. Adja meg az alkalmazás nevének nevét. Adjon meg értelmes alkalmazásnevet.
5. A Támogatott fióktípusok csoportban válassza **a Fiókok lehetőséget bármely szervezeti címtárban.**
6. Az Átirányítás URI-csoportban válassza a `https://contosoapp1/auth` **Web** lehetőséget, és adjon meg egy URI-t (például). 
7. Kattintson a **Register** (Regisztrálás) elemre.

    ![Egy alkalmazás regisztrálása](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Most, hogy az alkalmazás regisztrálva van, hozzáférés az alkalmazás áttekintő oldalához, és másolja az **alkalmazás (ügyfél) azonosító** értékét az adott oldalon. Mentse ezt az értéket, mivel ez az *alkalmazás (ügyfél) azonosító* értéke, amelyet meg kell adnia a közzétételi portálon és a Dynamics rendszerben az érdeklődők fogadásához a piactéri ajánlathoz.

    ![Alkalmazás (ügyfél) azonosítója](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Válassza **a Tanúsítványok és titkok** lehetőséget az alkalmazás bal oldali navigációs sávján, és válassza az Új **ügyféltitok** lehetőséget az adott lapon. Adjon meg egy értelmes leírást az ügyféltitkos címhez, és válassza a **Soha** ne lehetőséget a Lejár. Válassza a **Hozzáadás** lehetőséget az ügyféltitok létrehozásához.

    ![Alkalmazás - Tanúsítás és titkok](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Amint az ügyféltitok sikeresen létrejött, **másolja az ügyfél titkos értékét.** Az oldaltól való eligazodás után nem tudja beolvasni az értéket. Mentse ezt az értéket, mivel ez az *ügyfél titkos* értéket meg kell adnia a közzétételi portálon, hogy érdeklődőket kapjon a piactéri ajánlathoz. 
11. Válassza ki az **API-engedélyeket** az alkalmazások bal oldali navigációs sávján, majd válassza **az Engedély hozzáadása**lehetőséget.
12. Válassza a Microsoft API-k, majd a **Dynamics CRM** API-k lehetőséget.
13. A *Milyen típusú engedélyekre van szüksége az alkalmazásnak,* győződjön meg arról, hogy **a Delegált engedélyek** ki vannak jelölve. Ellenőrizze, **hogy user_impersonation** Access Common *Data Service-t szervezeti felhasználóként.* Válassza **az Engedélyek hozzáadása**lehetőséget.

    ![Engedélyek hozzáadása](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Miután elvégezte az `https://tenant.crm.dynamics.com`1-13.
15. A Beállítások eléréséhez válassza a fogaskerék ikont és a **Speciális beállításokat** a felső navigációs sávon.
16. Miután a Beállítások lapon a felső navigációs sávon a Beállítás menüt érheti el, és válassza a **Biztonság**lehetőséget.
17. Miután a Biztonság lapon válassza a **Felhasználók**lehetőséget.  A Felhasználók lapon válassza az "Engedélyezett felhasználók" legördülő menüt az **Alkalmazásfelhasználók**elemre való váltáshoz.
18. Új felhasználó létrehozásához válassza az **Új** lehetőséget. 

    ![Új felhasználó létrehozása](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. Az **Új felhasználó**alkalmazásban győződjön meg arról, hogy a USER: APPLICATION USER beállítás van kiválasztva. Adja meg a kapcsolathoz használni kívánt felhasználó felhasználónevét, teljes nevét és e-mail címét. Emellett illessze be az **alkalmazásazonosítót** az Azure Portalon létrehozott alkalmazás8. A felhasználó hozzáadásához válassza a **Mentés és a Bezárás** lehetőséget.

    ![Új felhasználó](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. A felhasználó kapcsolatának konfigurálásának befejezéséhez nyissa meg a jelen cikk "Biztonsági beállítások" című témakörét.

### <a name="office-365"></a>Office 365

Ha nem szeretné használni az Azure Active Directoryt, regisztrálhat egy új felhasználót a *Microsoft 365 Felügyeleti központban.* 90 naponta frissítenie kell felhasználónevét/jelszavát, hogy továbbra is érdeklődjön.

Az alábbi lépésekkel konfigurálhatja az Office 365 for Dynamics 365 for Customer Engagement alkalmazást.

1. Jelentkezzen be a [Microsoft 365 Felügyeleti központba](https://admin.microsoft.com).

2. Válassza **a Felhasználó hozzáadása**lehetőséget.

    ![Microsoft 365 Felügyeleti központ – felhasználó hozzáadása](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Hozzon létre egy új felhasználót az érdeklődőíró szolgáltatáshoz. Adja meg az alábbi beállításokat:

    * Felhasználónév megadására
    * Adjon meg egy jelszót, és törölje a jelet a "A felhasználó jelszómódosítása az első bejelentkezéskor" jelölőnégyzet ből.
    * A felhasználó szerepköreként válassza a "Felhasználó (rendszergazdai hozzáférés)" lehetőséget.
    * Válassza a "Dynamics 365 Customer Engagement terv" lehetőséget a következő képernyőfelvételen látható terméklicencként. A választott licencért díjat számítunk fel. 

Mentse ezeket az értékeket, mivel azok a *felhasználónév és jelszó* értékeket kell megadnia a közzétételi portálon, hogy megkapja az érdeklődőket a piactéri ajánlathoz.

![Microsoft 365 Felügyeleti központ - új felhasználó](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Biztonsági beállítások

Az utolsó lépés az, hogy a felhasználó létrehozott írni az érdeklődőket.

1. Nyissa meg a Dynamics 365 for Customer Engagement alkalmazást `https://tenant.crm.dynamics.com`úgy, hogy a Dynamics-példány (például) URL-címére navigál.
2. A beállításokeléréséhez a felső navigációs sávon a fogaskerék ikon és a **Speciális beállítások lehetőséget** választva érheti el.
3. Miután a Beállítások lapon a felső navigációs sávon a Beállítás menüt érheti el, és válassza a **Biztonság**lehetőséget.
4. Miután a Biztonság lapon válassza a **Felhasználók** lehetőséget, és jelölje ki a dokumentum Felhasználói engedélyek konfigurálása szakaszában létrehozott felhasználót, majd válassza a **Szerepkörök kezelése**lehetőséget. 

    ![Szerepkörök kezelése](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Keresse meg a "Microsoft Marketplace lead Writer" szerepkörnevet, és jelölje ki a szerepkör tanévhez.

    ![Felhasználó szerepkörök kezelése](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Ezt a szerepkört az importált megoldás hozta létre, és csak az érdeklődők írásához és a megoldás verziójának a kompatibilitás biztosításához történő nyomon követéséhez szükséges engedélyekkel rendelkezik.

6. Lépjen vissza a Biztonság lapra, és válassza a **Biztonsági szerepkörök lehetőséget.** Keresse meg a "Microsoft Marketplace lead writer" szerepkört, és jelölje ki.

    ![Biztonsági szerepkörök](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Miután a biztonsági szerepkör, válassza a **Core Records** fülre. Keresés a "Felhasználói entitás felhasználói felületének beállításai" entitás, és engedélyezze a létrehozása, olvasása és írása engedélyeket felhasználó (1/4 sárga kör) az adott entitás kattintva egyszer az egyes megfelelő körökben.

    ![A Microsoft Piactér vezető írója – Core Records](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Most keresse meg a **Testreszabás** lapot. Keresés a "Rendszerfeladat" entitásban, és engedélyezi az olvasási, írási és hozzáfűzési engedélyeket az adott entitáshoz (folyamatos zöld) az entitáshoz, ha négyszer kattint a megfelelő körökmindegyikére.

    ![Microsoft Piactér vezető írója – testreszabás](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Mentés és bezárás**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Az ajánlat konfigurálása az érdeklődők nek a Dynamics 365 for Customer Engagement alkalmazásba küldésére

Ha készen áll az ajánlat érdeklődőkezelési adatainak konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Nyissa meg az **ajánlat beállítási** oldalát.
2. Válassza a **Csatlakozás** lehetőséget az Érdeklődőkezelés szakaszban.

    ![Csatlakozás az érdeklődőkezeléshez](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. A Kapcsolat részletei előugró ablakban válassza a **Dynamics 365 for Customer Engagement** lehetőséget az érdeklődő célhelyéhez.

    ![Kapcsolat részletei - érdeklődő célállomása](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Adja meg a **Dynamics 365 példány URL-címét,** például `https://contoso.crm4.dynamics.com`.
5. Válassza ki a **hitelesítési**módszert , az Azure Active Directoryt vagy az Office 365-öt. 
6. Ha az Azure Active Directoryt választotta, adja meg `23456052-aaaa-bbbb-8662-1234df56788f`az **alkalmazás (ügyfél) azonosítóját** (például: ), a **címtárazonosítót** (például: `12345678-8af1-4asf-1234-12234d01db47`) és **az ügyféltitkos kulcsot** (például: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![A kapcsolat részletei – Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Ha az Office 365-öt választotta, `contoso@contoso.onmicrosoft.com`adja meg a `P@ssw0rd` **Felhasználónevet** (például: ) és a Jelszót (például: ).

    ![Kapcsolat részletei - Felhasználónév](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie, mielőtt érdeklődőket kapna az ajánlathoz.
