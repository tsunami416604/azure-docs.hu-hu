---
title: Szolgáltatásegyszerű riasztások feloldása az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatja el az Azure Active Directory tartományi szolgáltatások egyszerű konfigurációs riasztásait
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257959"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Ismert problémák: Egyszerű szolgáltatásriasztások az Azure Active Directory tartományi szolgáltatásokban

[Egyszerű szolgáltatásolyan](../active-directory/develop/app-objects-and-service-principals.md) alkalmazások, amelyeket az Azure platform az Azure AD DS felügyelt tartományának kezelésére, frissítésére és karbantartására használ. Ha egy egyszerű szolgáltatás törlése, az Azure AD DS felügyelt tartományban funkciók hatással van.

Ez a cikk segítséget nyújt az egyszerű szolgáltatásokkal kapcsolatos konfigurációs riasztások hibaelhárításában és megoldásában.

## <a name="alert-aadds102-service-principal-not-found"></a>Riasztás AADDS102: A szolgáltatásnév nem található

### <a name="alert-message"></a>Figyelmeztető üzenet

*Az Azure AD tartományi szolgáltatások megfelelő működéséhez szükséges egyszerű szolgáltatás törlődött az Azure AD-címtárból. Ez a konfiguráció hatással van a Microsoft felügyelt tartomány figyelésére, kezelésére, javítására és szinkronizálására.*

Ha egy szükséges egyszerű szolgáltatás törlődik, az Azure platform nem tud automatizált felügyeleti feladatokat végrehajtani. Előfordulhat, hogy az Azure AD DS felügyelt tartománya nem megfelelően alkalmazza a frissítéseket, és nem készít biztonsági mentéseket.

### <a name="check-for-missing-service-principals"></a>Hiányzó szolgáltatásnévi tagok ellenőrzése

Annak ellenőrzéséhez, hogy mely egyszerű szolgáltatás hiányzik, és újra létre kell hozni, hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon válassza az **Azure Active Directory** a bal oldali navigációs menüben.
1. Válassza **a Vállalati alkalmazások lehetőséget**. Válassza az **Alkalmazástípusa** legördülő menü *Minden alkalmazás* lehetőséget, majd válassza az **Alkalmaz parancsot.**
1. Keresse meg az egyes alkalmazásazonosítókat. Ha nem található meglévő alkalmazás, kövesse a *megoldás lépéseit* a szolgáltatásnév létrehozásához vagy a névtér újbóli regisztrálásához.

    | Alkalmazásazonosító | Megoldás: |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Hiányzó egyszerű szolgáltatáslétrehozása](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [A Microsoft.AAD névtér újbóli regisztrálása](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [A Microsoft.AAD névtér újbóli regisztrálása](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [A Microsoft.AAD névtér újbóli regisztrálása](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Hiányzó egyszerű szolgáltatás létrehozása

Ha a *2565bd9d-da50-47d4-8b85-4c97f669dc36* alkalmazásazonosító hiányzik az Azure AD-címtárból, az Azure AD PowerShell segítségével hajtsa végre a következő lépéseket. További információt az [Azure AD PowerShell telepítése című témakörben talál.](/powershell/azure/active-directory/install-adv2)

1. Telepítse az Azure AD PowerShell-modult, és importálja az alábbiak szerint:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Most hozza létre újra a szolgáltatásnév a [New-AzureAdServicePrincipal][New-AzureAdServicePrincipal] parancsmag használatával:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Az Azure AD DS felügyelt tartomány állapota automatikusan frissíti magát két órán belül, és eltávolítja a riasztást.

### <a name="re-register-the-microsoft-aad-namespace"></a>A Microsoft AAD névtér újbóli regisztrálása

Ha alkalmazás ID *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947 a-dc74e5d09022*vagy *d87dcbc6-a371-462e-88e3-28ad15ec4e64* hiányzik az Azure AD könyvtárából, Hajtsa végre az alábbi lépéseket a *Microsoft.AAD* erőforrás-szolgáltató újbóli regisztrálásához:

1. Az Azure Portalon keressen és válassza **az Előfizetések**lehetőséget.
1. Válassza ki az Azure AD DS felügyelt tartományához társított előfizetést.
1. A bal oldali navigációs sávon válassza az **Erőforrás-szolgáltatók**lehetőséget.
1. Keresse meg a *Microsoft.AAD*elemet, majd válassza **az Újraregisztrálás**lehetőséget.

Az Azure AD DS felügyelt tartomány állapota automatikusan frissíti magát két órán belül, és eltávolítja a riasztást.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Riasztás AADDS105: A jelszó-szinkronizálási alkalmazás elavult

### <a name="alert-message"></a>Figyelmeztető üzenet

*A "d87dcbc6-a371-462e-88e3-28ad15ec4e64" alkalmazásazonosítóval rendelkező szolgáltatásnév törlődött, majd újra létrejött. A rekreációs hagy inkonzisztens engedélyeket az Azure AD tartományi szolgáltatások erőforrások kiszolgálásához szükséges a felügyelt tartomány. Ez hatással lehet a jelszavak szinkronizálására a felügyelt tartományban.*

Az Azure AD DS automatikusan szinkronizálja a felhasználói fiókokat és hitelesítő adatokat az Azure AD-ből. Ha probléma van a folyamathoz használt Azure AD-alkalmazással, az Azure AD DS és az Azure AD közötti hitelesítő adatok szinkronizálása sikertelen lesz.

### <a name="resolution"></a>Megoldás:

A hitelesítő adatok szinkronizálásához használt Azure AD-alkalmazás újbóli létrehozásához használja az Azure AD PowerShell t a következő lépések végrehajtásához. További információt az [Azure AD PowerShell telepítése című témakörben talál.](/powershell/azure/active-directory/install-adv2)

1. Telepítse az Azure AD PowerShell-modult, és importálja az alábbiak szerint:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Most törölje a régi alkalmazást és objektumot a következő PowerShell-parancsmagokkal:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

A két alkalmazás törlése után az Azure platform automatikusan újralétrehozza őket, és megpróbálja folytatni a jelszó-szinkronizálást. Az Azure AD DS felügyelt tartomány állapota automatikusan frissíti magát két órán belül, és eltávolítja a riasztást.

## <a name="next-steps"></a>További lépések

Ha továbbra is problémák merülnek fel, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
