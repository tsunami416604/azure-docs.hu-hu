---
title: Vezető felügyelet a Dynamics 365 for Customer engagement szolgáltatásban | Azure piactér
description: A Dynamics 365-hez készült érdeklődők felügyeletének konfigurálása az ügyfelek szerepvállalásához.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 181a3f3a8d3cabb2fdf6caf79cef16201fab0c68
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177807"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Az érdeklődők felügyeletének konfigurálása a Dynamics 365-hez ügyfél-engagement esetén

Ez a [cikk azt ismerteti](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) , hogyan állítható be a Dynamics 365 for Customer engagement (korábban Dynamics CRM Online), további információ az értékesítési érdeklődők a Piactéri ajánlatból való feldolgozásának változásáról. 

>[!Note]
>Ezek az utasítások a Microsoft által üzemeltetett felhőalapú Dynamics 365-hez készült felhasználói engagement-környezetre vonatkoznak. A közvetlen Dynamics helyszíni környezethez való csatlakozás jelenleg nem támogatott, így olyan érdeklődőket is fogadhat, mint például egy [https-végpont](./commercial-marketplace-lead-management-instructions-https.md) vagy egy [Azure-tábla](./commercial-marketplace-lead-management-instructions-azure-table.md) konfigurálása az érdeklődők fogadásához.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következő felhasználói engedélyek szükségesek:

* Az ügyfelek bevonási példányához rendszergazdának kell lennie a Dynamics 365-as verzióban, hogy telepíteni lehessen egy megoldást, és követnie kell ezeket az utasításokat.
* Bérlői rendszergazdának kell lennie ahhoz, hogy új szolgáltatásfiókot hozzon létre az érdeklődők számára a Piactéri ajánlatokból való üzenetküldésre használt vezető szolgáltatáshoz.
* Hozzáféréssel kell rendelkeznie az Office 365 felügyeleti portálhoz.
* Hozzáféréssel kell rendelkeznie a Azure Portalhoz.

## <a name="install-the-solution"></a>A megoldás telepítése

1.  Töltse le a [Microsoft Marketplace vezető író megoldását](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) , és mentse helyileg a számítógépre.

2.  Nyissa meg a Dynamics 365 ügyfelet a Dynamics-példány URL-címére való navigálással (például `https://tenant.crm.dynamics.com`).

3.  Hozzáférési beállítások: válassza a fogaskerék ikont és a **speciális beállításokat** a felső navigációs sávon.
 
    ![Dynamics – speciális beállítások](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  A beállítások lapon kattintson a felső navigációs sávon a beállítás menüre, és válassza a **megoldások**elemet.

    >[!Note]
    >Ha nem látja a beállításokat a következő képernyőfelvételen, akkor nem rendelkezik a folytatáshoz szükséges engedélyekkel. Forduljon a Dynamics 365-beli rendszergazdához a Customer engagement-példányhoz.

    ![Dynamics 365 – megoldások](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. A megoldások lapon válassza az **Importálás** lehetőséget, majd Navigáljon arra a helyre, ahová az 1. lépésben letöltött *Microsoft Marketplace vezető író* megoldást mentette.

    ![Dynamics 365 for Customer engagement – importálás](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Fejezze be a megoldás importálását a megoldás importálása varázsló segítségével.

## <a name="configure-user-permissions"></a>Felhasználói engedélyek konfigurálása

Ha érdeklődőket szeretne írni a Dynamics 365 for Customer engagement-példányra, meg kell osztania egy szolgáltatásfiókot az Egyesült Államokban, és konfigurálnia kell a fiók engedélyeit.

A szolgáltatásfiók létrehozásához és az engedélyek hozzárendeléséhez kövesse az alábbi lépéseket. **Azure Active Directory** vagy **Office 365**-et is használhat.

>[!Note]
>A kiválasztott hitelesítési lehetőség alapján kihagyhatja a megfelelő utasításokat a választott beállítások alapján. Lásd: [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) vagy [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Javasoljuk, hogy ezt a beállítást használja, mivel a további előnyöket nem kell frissítenie a Felhasználónév/jelszó megszerzéséhez. A Azure Active Directory beállítás használatához meg kell adnia az alkalmazás AZONOSÍTÓját, az alkalmazás kulcsát és a könyvtár AZONOSÍTÓját a Active Directory alkalmazásból.

Az alábbi lépésekkel konfigurálhatja a Dynamics 365-hez készült Azure Active Directory a Customer engagement szolgáltatáshoz.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/), majd válassza ki a Azure Active Directory szolgáltatást a bal oldali navigációs sávon.

2. Válassza a **Tulajdonságok** lehetőséget a Azure Active Directory bal oldali navigációs sávon, és másolja a **címtár-azonosító** értékét a lapon. Mentse ezt az értéket, mivel ez az a *címtár-azonosító* érték, amelyet meg kell adnia a közzétételi portálon, hogy fogadja az érdeklődőket a Piactéri ajánlathoz.

    ![Azure Active Directory – tulajdonságok](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Válassza a **Alkalmazásregisztrációk** lehetőséget a Azure Active Directory bal oldali navigációs menüben, majd válassza az **új regisztráció** lehetőséget az oldalon.
4. Adja meg az alkalmazás nevének nevét. Adjon meg egy értelmes alkalmazásnév-nevet.
5. A támogatott fiókok típusai területen válassza **a fiókok lehetőséget bármely szervezeti címtárban**.
6. Az átirányítási URI területen válassza a **web** lehetőséget, és adjon meg egy URI-t (például `https://contosoapp1/auth`). 
7. Kattintson a **Register** (Regisztrálás) elemre.

    ![Egy alkalmazás regisztrálása](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Most, hogy regisztrálta az alkalmazást, nyissa meg az alkalmazás áttekintés lapját, és másolja az **alkalmazás (ügyfél) azonosító** értékét az adott oldalra. Mentse ezt az értéket, mivel a közzétételi Portálon és a Dynamics-ben a Piactéri ajánlathoz szükséges *alkalmazások (ügyfél-) azonosító* értéknek kell megadnia.

    ![Alkalmazás (ügyfél) azonosítója](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Válassza ki a **tanúsítványok és titkos kulcsok** elemet az alkalmazás bal oldali navigációs sávján, és válassza az **új ügyfél titka** lehetőséget az oldalon. Adjon meg egy értelmes leírást az ügyfél titkos kódjához, és válassza a lejárat alatt lévő **soha** lehetőséget. A **Hozzáadás** gombra kattintva hozza létre az ügyfél titkos kulcsát.

    ![Alkalmazás – minősítés és titkok](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Az ügyfél titkos kulcsának sikeres létrehozása után másolja ki **az ügyfél titkos értékét**. Az értéket nem fogja tudni lekérni az oldalról való elmozdulás után. Mentse ezt az értéket, mivel az *ügyfél titkos* értéke, amelyet a közzétételi portálon kell megadnia, hogy fogadja az érdeklődőket a Piactéri ajánlathoz. 
11. Válassza az **API-engedélyek** lehetőséget az alkalmazások bal oldali navigációs sávján, majd válassza az **engedély hozzáadása**lehetőséget.
12. Válassza a Microsoft API-k lehetőséget, majd válassza a **Dynamics CRM** lehetőséget API-ként.
13. Az *alkalmazás által igényelt engedélyek típusa*beállításnál ellenőrizze, hogy a **delegált engedélyek** van-e kiválasztva. Győződjön meg arról, hogy a **user_impersonation** *hozzáférési Common Data Service szervezeti felhasználóként*van engedélyezve. Válassza az **engedélyek hozzáadása**lehetőséget.

    ![Engedélyek hozzáadása](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Miután befejezte a 1-13-es lépéseket a Azure Portalon, navigáljon a Dynamics 365 for Customer engagement-példányhoz az URL-címhez való navigálással (például `https://tenant.crm.dynamics.com`).
15. Hozzáférési beállítások a fogaskerék ikonra kattintva és a **Speciális beállítások** a felső navigációs sávon.
16. A beállítások lapon kattintson a felső navigációs sávon a beállítás menüre, és válassza a **Biztonság**elemet.
17. A biztonság lapon válassza a **felhasználók**lehetőséget.  A felhasználók lapon válassza az "engedélyezett felhasználók" legördülő menüt az **alkalmazás felhasználóinak**való átváltáshoz.
18. Új felhasználó létrehozásához válassza az **új** lehetőséget. 

    ![Új felhasználó létrehozása](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. Az **új felhasználó**területen győződjön meg arról, hogy a felhasználó: Application User (felhasználó) van kiválasztva. Adja meg annak a felhasználónak a felhasználónevét, teljes nevét és e-mail-címét, amelyet ezzel a kapcsolatban szeretne használni. Illessze be az **alkalmazás azonosítóját** is a 8. lépésben a Azure Portalban létrehozott alkalmazáshoz. A felhasználó hozzáadásának befejezéséhez válassza a **Mentés és bezárás** lehetőséget.

    ![Új felhasználó](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. A jelen cikk "biztonsági beállítások" részében találja a felhasználóhoz való kapcsolódás konfigurálásának befejezését.

### <a name="office-365"></a>Office 365

Ha nem kívánja használni a Azure Active Directoryt, regisztrálhat egy új felhasználót a *Microsoft 365 felügyeleti központban*. Az érdeklődők folytatásához 90 naponta frissítenie kell a felhasználónevét/jelszavát.

A következő lépésekkel konfigurálhatja a Dynamics 365-hez készült Office 365-et az ügyfelek engagement szolgáltatásához.

1. Jelentkezzen be a [Microsoft 365 felügyeleti központba](https://admin.microsoft.com).

2. Válassza **a felhasználó hozzáadása**elemet.

    ![Microsoft 365 felügyeleti központ – felhasználó hozzáadása](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Hozzon létre egy új felhasználót az ólom-író szolgáltatáshoz. Adja meg a következő beállításokat:

    * Adja meg a felhasználónevet
    * Adja meg a jelszót, és törölje a jelet a "felhasználói jelszó módosítása az első bejelentkezéskor" beállításnál.
    * A felhasználó szerepköre válassza a "felhasználó (nincs rendszergazdai hozzáférés)" lehetőséget.
    * Válassza a "Dynamics 365 Customer engagement-terv" lehetőséget a következő képernyőfelvételen látható termék-licencként. A választott licencért díjat számítunk fel. 

Mentse ezeket az értékeket, mivel azok a *felhasználónevet és a jelszót* adja meg, amelyet a közzétételi portálon kell megadnia a Piactéri ajánlathoz tartozó érdeklődők fogadásához.

![Microsoft 365 felügyeleti központ – új felhasználó](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Biztonsági beállítások

Az utolsó lépés az, hogy lehetővé teszi a létrehozott felhasználó számára az érdeklődők írását.

1. Nyissa meg a Dynamics 365 ügyfelet a Dynamics-példány URL-címére való navigálással (például `https://tenant.crm.dynamics.com`).
2. Hozzáférési beállítások: válassza a fogaskerék ikont és a **speciális beállításokat** a felső navigációs sávon.
3. A beállítások lapon kattintson a felső navigációs sávon a beállítás menüre, és válassza a **Biztonság**elemet.
4. A biztonság lapon válassza a **felhasználók** lehetőséget, majd válassza ki a dokumentum felhasználói engedélyek konfigurálása szakaszában létrehozott felhasználót, majd válassza a **Szerepkörök kezelése**lehetőséget. 

    ![Szerepkörök kezelése](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Keressen rá a "Microsoft Marketplace Lead Writer" nevű szerepkörre, és válassza ki a szerepkört a felhasználó hozzárendeléséhez.

    ![Felhasználói szerepkörök kezelése](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Ezt a szerepkört az importált megoldás hozza létre, és csak a megfelelő jogosultsággal rendelkezik az érdeklődők írásához és a megoldás verziójának nyomon követéséhez, hogy biztosítsa a kompatibilitást.

6. Váltson vissza a Biztonság lapra, és válassza a **biztonsági szerepkörök**elemet. Keresse meg a "Microsoft Marketplace érdeklődő író" szerepkört, és jelölje ki.

    ![Biztonsági szerepkörök](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. A biztonsági szerepkörben válassza az **alaprekordok** fület. Keresse meg a "felhasználói entitás felhasználói felületének beállításai" entitást, és engedélyezze a létrehozási, olvasási és írási engedélyeket a felhasználóhoz (1/4 sárga kör) az adott entitáshoz, ehhez kattintson egyszer a megfelelő körökre.

    ![Microsoft Marketplace vezető író – Core rekordok](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Most lépjen a **Testreszabás** lapra. keressen rá a "rendszerfeladat" entitásra, és engedélyezheti az olvasási, írási és AppendTo engedélyeket az entitás szervezetének (tömör zöld) a megfelelő körökre kattintva.

    ![Microsoft Marketplace vezető írója – Testreszabás](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Mentés és bezárás**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Az ajánlat beállítása az érdeklődők a Dynamics 365-be való küldésére az ügyfelek engagement szolgáltatásához

Ha készen áll az ajánlathoz tartozó érdeklődői felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Navigáljon az ajánlat **telepítési** lapjára.
2. Válassza a **kapcsolat** lehetőséget az érdeklődő felügyelete szakaszban.

    ![Kapcsolódás az érdeklődők felügyeletéhez](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. A kapcsolat részletei előugró ablakban válassza a Dynamics 365 lehetőséget az érdeklődői célhoz tartozó **ügyfél-részvételhez**

    ![Kapcsolat részletei – érdeklődő célhelye](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Adja meg a **Dynamics 365-példány URL-címét** , például `https://contoso.crm4.dynamics.com`.
5. Válassza ki a **hitelesítés**, Azure Active Directory vagy Office 365 metódust. 
6. Ha a Azure Active Directory lehetőséget választotta, adja meg az **alkalmazás (ügyfél) azonosítóját** (például: `23456052-aaaa-bbbb-8662-1234df56788f`), a **könyvtár azonosítóját** (például: `12345678-8af1-4asf-1234-12234d01db47`) és az **ügyfél titkos kulcsát** (például: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Kapcsolat részletei – Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Ha az Office 365 lehetőséget választotta, adja meg a **felhasználónevet** (például: `contoso@contoso.onmicrosoft.com`) és a jelszót (például: `P@ssw0rd`).

    ![Kapcsolat részletei – Felhasználónév](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.
