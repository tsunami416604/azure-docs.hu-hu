---
title: Több vállalat kiszolgálása Azure verem engedélyezése |} Microsoft Docs
description: Megtudhatja, hogyan támogatja a több Azure Active Directory címtárral Azure verem
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 9ee54827ae9e8a803a5e3d7464559aec62c67119
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Több vállalat kiszolgálása Azure verem engedélyezése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Konfigurálhatja az Azure verem szolgáltatások használatához Azure-készletben több Azure Active Directory (Azure AD) bérlő felhasználók támogatására. Tegyük fel vegye figyelembe a következő forgatókönyvet:

 - A szolgáltatás rendszergazdaként a contoso.onmicrosoft.com, amelyen telepítve van-e az Azure-verem.
 - Mária a címtár rendszergazdájának fabrikam.onmicrosoft.com, ahol vendégfelhasználók találhatók. 
 - Mária vállalati IaaS és PaaS szolgáltatások kap a vállalat, és a felhasználók a Vendég könyvtárból (fabrikam.onmicrosoft.com) jelentkezzen be, és Azure verem erőforrásainak használata contoso.onmicrosoft.com kell.

Ez az Útmutató lépéseit, ebben az esetben a környezetében lévő konfigurálásához szükséges több-bérlős Azure verem.  Ebben a forgatókönyvben, és Mária lépések szükségesek ahhoz, hogy a bejelentkezéshez és a Contoso Azure verem telepítését szolgáltatásait Fabrikam felhasználók.  

## <a name="before-you-begin"></a>Előkészületek
Van néhány előfeltétel, hogy több-bérlős Azure verem konfigurálása előtt figyelembe:
  
 - Ön és Mária mind az Azure-verem telepítve van-e (Contoso), és a Vendég könyvtárat (Fabrikam) keresztül kell összehangolják felügyeleti lépéseket.  
 - Győződjön meg arról, hogy már [telepített](azure-stack-powershell-install.md) és [konfigurált](azure-stack-powershell-configure-admin.md) PowerShell Azure verem.
 - [Töltse le az Azure-verem eszközök](azure-stack-powershell-download.md), és a csatlakozás és identitás modul importálása:

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Mária szükséges [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) Azure verem elérésére. 

## <a name="configure-azure-stack-directory"></a>Azure verem directory konfigurálása
Ebben a szakaszban Azure verem bejelentkezések engedélyezéséhez a Fabrikam az Azure Active directory-bérlő konfigurálja.

### <a name="onboard-guest-directory-tenant"></a>A bevezetni Vendég directory-bérlő
Következő, a bevezetni a Vendég Directory-bérlő (Fabrikam) Azure verem.  Ez a lépés konfigurál az Azure Resource Manager-felhasználók és a Vendég directory-bérlőhöz szolgáltatásnevekről fogadásához.

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local" `
 -ResourceGroupName $ResourceGroupName
````



## <a name="configure-guest-directory"></a>Vendég directory konfigurálása
Miután elvégezte a Azure verem könyvtár lépéseit, Mária Azure verem a Vendég directory elérése hozzájárulási adja majd Azure verem regisztrálják a Vendég címtárban. 

### <a name="registering-azure-stack-with-the-guest-directory"></a>A Vendég directory Azure verem regisztrálása
Miután a Vendég directory rendszergazda által a Fabrikam könyvtár eléréséhez Azure verem hozzájárulási, akkor regisztrálnia kell a Azure verem Fabrikam directory-bérlő.

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>Közvetlen felhasználók jelentkezhetnek be
Most, hogy és Mária végzett előkészítésére Mária könyvtár lépéseit, Mária utasíthatja a Fabrikam felhasználók jelentkezhetnek be.  Fabrikam felhasználók (Ez azt jelenti, hogy a fabrikam.onmicrosoft.com utótagjával rendelkező felhasználók) jelentkezzen be ellátogatva https://portal.local.azurestack.external.  

Mária átirányítja a bármely [idegen rendszerbiztonsági tagok](../role-based-access-control/rbac-and-directory-admin-roles.md) a Fabrikam könyvtárban (Ez azt jelenti, hogy a Fabrikam könyvtárban nélkül fabrikam.onmicrosoft.com utótagja felhasználók) is bejelentkezhet https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Ha nem használják az URL-cím, azok küldi a program az alapértelmezett címtár (Fabrikam), és arról, hogy a rendszergazda nem hozzájárult hibaüzenetet kap.

## <a name="next-steps"></a>További lépések

- [Delegált szolgáltatók kezelése](azure-stack-delegated-provider.md)
- [Az Azure verem kulcsfogalmak](azure-stack-key-features.md)
