---
title: 'Azure Active Directory Domain Services: Egyszerű szolgáltatásnév – problémamegoldás | Microsoft Docs'
description: A szolgáltatás egyszerű konfigurációjának hibaelhárítása Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 9e5fa8c84f5e7ca58117666846b603a118826150
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234144"
---
# <a name="troubleshoot-invalid-service-principal-configurations-for-azure-active-directory-domain-services"></a>A Azure Active Directory Domain Serviceshoz tartozó egyszerű szolgáltatásnév-konfigurációk hibaelhárítása

Ez a cikk segítséget nyújt az egyszerű szolgáltatásokkal kapcsolatos olyan konfigurációs hibák elhárításához és megoldásához, amelyek a következő riasztási üzenetet eredményezik:

## <a name="alert-aadds102-service-principal-not-found"></a>Riasztás AADDS102: Az egyszerű szolgáltatásnév nem található

**Riasztási üzenet:** *Az Azure AD-címtárból törölte a Azure AD Domain Services megfelelő működéséhez szükséges egyszerű szolgáltatást. Ez a konfiguráció befolyásolja a Microsoft képességét a felügyelt tartomány figyelésére, kezelésére, javítására és szinkronizálására.*

Az [egyszerű szolgáltatások](../active-directory/develop/app-objects-and-service-principals.md) olyan alkalmazások, amelyeket a Microsoft a felügyelt tartomány felügyeletére, frissítésére és karbantartására használ. Ha törli őket, megszakítja a Microsoft számára a tartomány kiszolgálásának lehetőségét.


## <a name="check-for-missing-service-principals"></a>Hiányzó egyszerű szolgáltatások keresése
A következő lépésekkel meghatározhatja, hogy mely egyszerű szolgáltatásokat kell újból létrehozni:

1. Navigáljon a Azure Portal [vállalati alkalmazások – minden alkalmazás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) lapjára.
2. A **Megjelenítés** legördülő menüben válassza a **minden alkalmazás** elemet, majd kattintson az **alkalmaz**gombra.
3. Az alábbi táblázat segítségével keresse meg az egyes alkalmazás-AZONOSÍTÓkat úgy, hogy beillesztette az azonosítót a keresőmezőbe, és lenyomja az ENTER billentyűt. Ha a keresési eredmények üresek, újra létre kell hoznia a szolgáltatásnevet a "megoldás" oszlopban szereplő lépéseket követve.

| Alkalmazásazonosító | Megoldás: |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Hiányzó egyszerű szolgáltatásnév újbóli létrehozása a PowerShell-lel](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Regisztrálja újra a Microsoft. HRE névtérben](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Regisztrálja újra a Microsoft. HRE névtérben](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Regisztrálja újra a Microsoft. HRE névtérben](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Hiányzó egyszerű szolgáltatásnév újbóli létrehozása a PowerShell-lel
Kövesse az alábbi lépéseket, ha az azonosítóval ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` rendelkező egyszerű szolgáltatás hiányzik az Azure ad-címtárból.

**Megoldás:** A lépések végrehajtásához az Azure AD PowerShell szükséges. Az Azure AD PowerShell telepítésével kapcsolatos információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

A probléma megoldásához írja be a következő parancsokat egy PowerShell-ablakban:
1. Telepítse az Azure AD PowerShell-modult, és importálja.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. A következő PowerShell-parancs végrehajtásával győződjön meg arról, hogy a Azure AD Domain Serviceshoz szükséges egyszerű szolgáltatásnév hiányzik-e a címtárból:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Hozza létre a szolgáltatásnevet a következő PowerShell-parancs beírásával:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Miután létrehozta a hiányzó szolgáltatásnevet, várjon két órát, és tekintse meg a felügyelt tartomány állapotát.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Regisztrálja újra a Microsoft HRE-névteret a Azure Portal használatával
Kövesse az alábbi lépéseket, ha az azonosítóval ```443155a6-77f3-45e3-882b-22b3a8d431fb``` ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` vagy ```abba844e-bc0e-44b0-947a-dc74e5d09022``` az Azure ad-címtárból hiányzik egy egyszerű szolgáltatásnév.

**Megoldás:** A következő lépésekkel állíthatja vissza a tartományi szolgáltatásokat a címtárban:

1. Navigáljon a [](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Azure Portal előfizetések lapjára.
2. Válassza ki az előfizetést a felügyelt tartományhoz társított táblából.
3. A bal oldali navigációs sávon válassza az **erőforrás-szolgáltatók** lehetőséget.
4. Keresse meg a "Microsoft. HRE" kifejezést a táblázatban, és kattintson az **újbóli regisztrálás** gombra.
5. A riasztás feloldása érdekében két órán belül megtekintheti a felügyelt tartomány állapot lapját.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Riasztás AADDS105: A jelszó-szinkronizálási alkalmazás elavult

**Riasztási üzenet:** A "d87dcbc6-a371-462e-88e3-28ad15ec4e64" AZONOSÍTÓJÚ szolgáltatásnevet törölték, majd újból létrehozták. A szabadidő a felügyelt tartomány kiszolgálásához szükséges Azure AD Domain Services erőforrásokon inkonzisztens engedélyek mögött hagy. A felügyelt tartomány jelszavainak szinkronizálása hatással lehet.


**Megoldás:** A lépések végrehajtásához az Azure AD PowerShell szükséges. Az Azure AD PowerShell telepítésével kapcsolatos információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

A probléma megoldásához írja be a következő parancsokat egy PowerShell-ablakban:
1. Telepítse az Azure AD PowerShell-modult, és importálja.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Törölje a régi alkalmazást és objektumot a következő PowerShell-parancsok használatával

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. A mindkettő törlése után a rendszer szervizelni fogja magát, és újból létrehozza a jelszó-szinkronizáláshoz szükséges alkalmazásokat. A riasztás szervizelésének biztosításához várjon két órát, és ellenőrizze a tartomány állapotát.


## <a name="contact-us"></a>Kapcsolatfelvétel
A [visszajelzések megosztásához és](contact-us.md)a támogatáshoz forduljon a Azure Active Directory Domain Services termék csapatához.
