---
title: "Regisztrálja az Azure verem |} Microsoft Docs"
description: "Azure verem regisztrálása az Azure-előfizetéshez."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: erikje
ms.openlocfilehash: 24cde66a132ae2e1ba0eb9b1564915746e5ca448
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Az Azure verem regisztrálása az Azure-előfizetéshez

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Regisztrálhatja [Azure verem](azure-stack-poc.md) az Azure-ral Piactéri elemek letölteni az Azure-ból, és megkezdheti a Microsoft commerce adatok beállításához. 

> [!NOTE]
>Regisztrációs használata ajánlott, mivel lehetővé teszi a fontos Azure verem funkciók, például a piactér szindikálási és használatai jelentések tesztelése. Miután regisztrálta Azure verem, használati Azure kereskedelmi bejelentések. Megtekintheti az előfizetésben a regisztrációhoz használt. Azure verem szoftverfejlesztői készlet felhasználók nem felszámított bármely használati jelentést.
>


## <a name="get-azure-subscription"></a>Az Azure előfizetés.

Mielőtt regisztrálná az Azure-vermet az Azure-ral, rendelkeznie kell:

- Az előfizetés-azonosító az Azure-előfizetéssel. Ahhoz, hogy az azonosító, jelentkezzen be az Azure-ba, és kattintson **további szolgáltatások** > **előfizetések**, kattintson az előfizetést szeretné használni, és a **Essentials** megtalálhatja a **Előfizetés-azonosító**. Kínai, német és US government felhőalapú előfizetések jelenleg nem támogatottak.
- A felhasználónév és jelszó egy olyan fiók, amely az előfizetés tulajdonosa (a felügyelt Szolgáltatásfiók/2FA fiókok támogatottak).
- Az Azure Active Directoryból az Azure-előfizetést. Ez a könyvtár az Azure-ban fölé a profilképet, az Azure-portál jobb felső sarkában található. 
- [Az Azure-verem erőforrás-szolgáltató regisztrálva](#register-azure-stack-resource-provider-in-azure).

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, akkor [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/en-us/free/?b=17.06). Azure verem regisztrálása költséget nem áll, az Azure-előfizetése.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Azure verem erőforrás-szolgáltató regisztrálása az Azure-ban
> [!NOTE] 
> Ezt a lépést csak egyszer kell végrehajtani egy Azure verem környezetben kell.
>

1. Indítsa el a Powershell ISE rendszergazdaként.
2. Jelentkezzen be az Azure-fiók, amely az Azure-előfizetés "AzureCloud" paraméter - EnvironmentName tulajdonosa.
3. Regisztrálja az Azure erőforrás-szolgáltató "Microsoft.AzureStack".

Példa: 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral

> [!NOTE]
>Ezeket a lépéseket kell végrehajtania az állomáson.
>

1. [Telepítse a PowerShell Azure verem](azure-stack-powershell-install.md). 
2. Másolás a [RegisterWithAzure.psm1 parancsfájl](https://go.microsoft.com/fwlink/?linkid=842959) (például C:\Temp) mappába.
3. Indítsa el a PowerShell ISE rendszergazdaként, és importálja a RegisterWithAzure modult.    
4. Futtassa az Add-AzsRegistration modul RegisterWithAzure.psm1 parancsfájl. A helyőrzőket cserélje le: 
    - *YourCloudAdminCredential* egy PowerShell-objektum, amely tartalmazza a domain\cloudadmin helyi tartományi hitelesítő adatait (a csomag, ez pedig azurestack\cloudadmin).
    - *YourAzureSubscriptionID* regisztrálni Azure verem használni kívánt Azure-előfizetés azonosítója.
    - *YourAzureDirectoryTenantName* az Azure-előfizetéshez társított Azure-bérlőhöz könyvtár neve. A regisztrációs erőforrás létrejön a directory-bérlő tartozik. 
    - *YourPrivilegedEndpoint* neve a [kiemelt végpont](azure-stack-privileged-endpoint.md).

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. A bejelentkezési előugró ablakban írja be a Azure-előfizetés hitelesítő adatait.

## <a name="verify-the-registration"></a>A regisztráció-ellenőrzés

1. Jelentkezzen be a felügyeleti portálon (https://adminportal.local.azurestack.external).
2. Kattintson a **további szolgáltatások** > **piactér felügyeleti** > **adja hozzá az Azure-ból**.
3. Ha az Azure (például a WordPress) rendelkezésre álló elemek listája látható, az aktiválás sikeres volt.

> [!NOTE]
> Regisztráció befejezése után az aktív figyelmeztetés nincs regisztrálása nem fog megjelenni.

## <a name="next-steps"></a>Következő lépések

[Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md)

