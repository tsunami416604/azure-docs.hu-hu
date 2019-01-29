---
title: 'Az Azure Active Directory tartományi szolgáltatások: Hibaelhárítási szolgáltatásnév konfigurációja |} A Microsoft Docs'
description: Az Azure AD Domain Services egyszerű szolgáltatás konfigurációjának hibaelhárítása
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: 2c39e8f172283f512037e0d991b2c22eb816c8f6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191329"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>A felügyelt tartomány konfigurációja érvénytelen egyszerű szolgáltatás hibaelhárítása

Ez a cikk segítséget nyújt a hibaelhárításához és megoldásához szolgáltatás egyszerű kapcsolatos konfigurációs hibák, amelyek a következő figyelmeztető üzenet:

## <a name="alert-aadds102-service-principal-not-found"></a>Riasztási AADDS102: Egyszerű szolgáltatásnév nem található

**Riasztás jelenik meg:** *Az Azure AD Domain Services megfelelő működéséhez szükséges szolgáltatásnevet az Azure AD-címtár törölve lett. Ez a konfiguráció hatással van a Microsoft képes figyelése, kezelése, patch, és szinkronizálni a felügyelt tartományt.*

[Egyszerű szolgáltatások](../active-directory/develop/app-objects-and-service-principals.md) olyan alkalmazások, amelyek kezelése, frissítése és a felügyelt tartomány kezelése a Microsoft használja. Törlődnek, ha a Microsoft lehetővé teszi a tartományi szolgáltatás működésképtelenné válik.


## <a name="check-for-missing-service-principals"></a>Hiányzó szolgáltatásnevek keresése
A következő lépések segítségével határozza meg, melyik szolgáltatás rendszerbiztonsági tagok kell újra létrehozni:

1. Keresse meg a [nagyvállalati alkalmazások – minden alkalmazás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) oldal az Azure Portalon.
2. Az a **megjelenítése** legördülő menüben válassza **minden alkalmazás** kattintson **alkalmaz**.
3. A következő táblázat használatával, keresse meg a minden alkalmazás azonosítója, azonosítója beillesztése a keresési mezőbe, majd nyomja le az adja meg. Ha a keresési eredmények üresek, újra létre kell hoznia a "feloldás" oszlopban szereplő utasítások alapján az egyszerű szolgáltatás.

| Alkalmazásazonosító | Megoldás: |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Hozza létre újra a hiányzó szolgáltatásnév a PowerShell-lel](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Regisztrálja újra a Microsoft.AAD névtérhez](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Regisztrálja újra a Microsoft.AAD névtérhez](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Regisztrálja újra a Microsoft.AAD névtérhez](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Hozza létre újra a hiányzó szolgáltatásnév a PowerShell-lel
Kövesse az alábbi lépéseket, ha a Azonosítóval rendelkező szolgáltatásnév ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` hiányzik az Azure AD-címtárát.

**Megoldás:** Az Azure AD PowerShell, a lépések elvégzéséhez szüksége lesz. Az Azure AD PowerShell telepítésével kapcsolatos információkért lásd: [Ez a cikk](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

A probléma megoldására, egy PowerShell-ablakban írja be a következő parancsokat:
1. Az Azure AD PowerShell-modul telepítéséhez, és importálja azt.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Ellenőrizze, hogy az Azure AD tartományi szolgáltatásokhoz szükséges szolgáltatásnév hiányzik a címtárban a következő PowerShell-parancs végrehajtásával:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Az egyszerű szolgáltatás létrehozása a következő PowerShell-parancs beírásával:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Miután létrehozta a hiányzó szolgáltatás egyszerű, két óra várakozás, és a felügyelt tartomány állapotának ellenőrzése.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Regisztrálja újra a Microsoft AAD-névteret, az Azure portal használatával
Kövesse az alábbi lépéseket, ha a Azonosítóval rendelkező szolgáltatásnév ```443155a6-77f3-45e3-882b-22b3a8d431fb``` vagy ```abba844e-bc0e-44b0-947a-dc74e5d09022``` vagy ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` hiányzik az Azure AD-címtárát.

**Megoldás:** A következő lépések használatával állítsa vissza a tartományi szolgáltatások a címtárban:

1. Keresse meg a [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oldal az Azure Portalon.
2. Válassza ki az előfizetést a táblából, amely a felügyelt tartományhoz társított
3. Válassza a bal oldali navigációs használatával **erőforrás-szolgáltatók**
4. Keressen rá a "Microsoft.AAD" kifejezésre a tábla, és kattintson a **újbóli regisztrálásához**
5. Annak érdekében, hogy a riasztás fel lett oldva, megtekintheti az oldalt a felügyelt tartományhoz tartozó két órán belül.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Riasztási AADDS105: Jelszó-szinkronizálás alkalmazás nem naprakész

**Riasztás jelenik meg:** A szolgáltatásnév "d87dcbc6-a371-462e-88e3-28ad15ec4e64" Alkalmazásazonosítóval rendelkező lett törölve, és majd újra létrehozza. Az újbóli hagy hátra inkonzisztens engedélyeket a szükséges szolgáltatást a felügyelt tartományhoz az Azure AD tartományi szolgáltatások erőforrásait. A felügyelt tartományra a jelszó-szinkronizálás is hatással lehet.


**Megoldás:** Az Azure AD PowerShell, a lépések elvégzéséhez szüksége lesz. Az Azure AD PowerShell telepítésével kapcsolatos információkért lásd: [Ez a cikk](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

A probléma megoldására, egy PowerShell-ablakban írja be a következő parancsokat:
1. Az Azure AD PowerShell-modul telepítéséhez, és importálja azt.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Törölje a régi-alkalmazás és az objektum a következő PowerShell-parancsok használatával

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. A törölt is, a rendszer szervizelése magát, és hozza létre újból a jelszó-szinkronizáláshoz szükséges alkalmazások. Annak érdekében, hogy a riasztás sikeresen szervizelve lett, várjon két órát, és a tartomány állapotának ellenőrzése.


## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](active-directory-ds-contact-us.md).
