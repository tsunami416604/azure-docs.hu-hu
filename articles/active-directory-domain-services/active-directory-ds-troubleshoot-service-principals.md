---
title: "Azure Active Directory tartományi szolgáltatások: Az egyszerű szolgáltatás beállításának hibakeresésére |} Microsoft Docs"
description: "Hibakeresés egyszerű szolgáltatásnév az Azure AD tartományi szolgáltatásokhoz"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2018
ms.author: ergreenl
ms.openlocfilehash: 7388bb291f665f195355a01d19a82cba9ed453eb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>A felügyelt tartományok érvénytelen egyszerű konfiguráció hibaelhárítása

Ez a cikk segít hibaelhárításához és megoldásához egyszerű konfigurációs hibákat, amelyek a következő figyelmeztető üzenet:

## <a name="alert-aadds102-service-principal-not-found"></a>Riasztási AADDS102: Egyszerű szolgáltatásnév nem található
**Figyelmeztető üzenet:** *az Azure AD-címtár az Azure AD tartományi szolgáltatások megfelelő működéséhez szükséges A szolgáltatás egyszerű törölve lett. Ez a konfiguráció hatással van a Microsoft képes figyeléséhez, kezeléséhez, a javítás, és a felügyelt tartományok szinkronizálása.*

[Rendszerbiztonsági tagok szolgáltatás](../active-directory/develop/active-directory-application-objects.md) kezelését, frissítése és karbantartása a felügyelt tartományok használó Microsoft-alkalmazások. Törlődnek, ha megszakítja a Microsoft a tartományi szolgáltatás képessége. 


## <a name="check-for-missing-service-principals"></a>A hiányzó szolgáltatásnevekről ellenőrzése
Az alábbi lépések segítségével határozza meg, melyik szolgáltatás rendszerbiztonsági tagoknak kell újból létre kell hozni:

1. Keresse meg a [vállalati alkalmazások – összes alkalmazás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) oldal az Azure portálon.
2. Az a **megjelenítése** legördülő menüből válassza **összes alkalmazás** kattintson **alkalmaz**.
3. Az alábbi táblázatban keresse meg a azonosító beillesztése a keresési mezőbe, majd nyomja le az egyes Alkalmazásazonosító adja meg. Ha a keresési eredmények üres, újra létre kell hoznia a "megoldási" oszlopban szereplő lépések a szolgáltatásnevet.

| Alkalmazásazonosító | Megoldás: |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Hozza létre újra a hiányzó egyszerű PowerShell használatával](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Regisztrálja újra a Microsoft.AAD névtérhez](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Regisztrálja újra a Microsoft.AAD névtérhez](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Szolgáltatás rendszerbiztonsági self javítása](#service-principals-that-self-correct) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Hozza létre újra a hiányzó egyszerű PowerShell használatával
Kövesse az alábbi lépéseket, ha a szolgáltatás egyszerű azonosítójú ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` hiányzik az Azure AD-címtárát.

**Szervizelés:** Azure AD PowerShell lépések elvégzéséhez szüksége. Azure AD PowerShell telepítésével kapcsolatos információkért lásd: [Ez a cikk](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

A probléma megoldására PowerShell ablakban írja be a következő parancsokat:
1. Telepítse az Azure AD PowerShell modult, és importálja azt.
    
    ```powershell 
    Install-Module AzureAD
    Import-Module AzureAD
    ```
    
2. Ellenőrizze, hogy az Azure AD tartományi szolgáltatások számára szükséges egyszerű hiányzik a címtárban hajtja végre a következő PowerShell-parancsot:
    
    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```
    
3. Az egyszerű szolgáltatás létrehozása a következő PowerShell-parancs beírásával:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```
    
4. A létrehozást követően a hiányzó szolgáltatás egyszerű, két óra várakozás után ellenőrizze a felügyelt tartományok állapotát.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Regisztrálja újra a Microsoft AAD névtérhez az Azure portál használatával
Kövesse az alábbi lépéseket, ha a szolgáltatás egyszerű azonosítójú ```443155a6-77f3-45e3-882b-22b3a8d431fb``` vagy ```abba844e-bc0e-44b0-947a-dc74e5d09022``` hiányzik az Azure AD-címtárát.

**Szervizelés:** tartományi szolgáltatásokban a címtár-visszaállítás az alábbi lépések segítségével:

1. Keresse meg a [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oldal az Azure portálon.
2. Válassza ki az előfizetést a táblából, a felügyelt tartományok társított
3. A bal oldali navigációs sáv segítségével **erőforrás-szolgáltató**
4. Keresse meg "Microsoft.AAD" a táblázatban, és kattintson a **regisztrálja újra**
5. Annak érdekében, hogy a riasztás fel lett oldva, az egészségügyi a lapnak a megtekintésére a felügyelt tartományok két órában.


## <a name="service-principals-that-self-correct"></a>Szolgáltatás rendszerbiztonsági self javítása
Kövesse az alábbi lépéseket, ha a szolgáltatás egyszerű azonosítójú ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` hiányzik az Azure AD-címtárát.

**Szervizelés:** Azure AD tartományi szolgáltatások észleli, ha a megadott szolgáltatásnév hiányzik, nincs megfelelően konfigurálva, vagy törölték. A szolgáltatás automatikusan létrehozza a szolgáltatásnevet. A felügyelt tartományok állapotának ellenőrzése után győződjön meg arról, hogy a szolgáltatás egyszerű újból létrejött két órát.


## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport a [visszajelzés fájlmegosztás vagy a támogatáshoz](active-directory-ds-contact-us.md).
