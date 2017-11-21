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
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
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

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, akkor [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/en-us/free/?b=17.06). Azure verem regisztrálása költséget nem áll, az Azure-előfizetése.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Azure verem erőforrás-szolgáltató regisztrálása az Azure-ban
> [!NOTE] 
> Ez a lépés csak egyszer Azure verem környezetben kell elvégezni.
>

1. Indítsa el egy Powershell-munkamenetet rendszergazdaként.
2. Jelentkezzen be az Azure-fiók, amely az Azure-előfizetés (segítségével a Login-AzureRmAccount parancsmag jelentkezzen be, és amikor bejelentkezik, ügyeljen arra, hogy a "AzureCloud" - EnvironmentName paraméter beállítása) tulajdonosa.
3. Regisztrálja az Azure erőforrás-szolgáltató "Microsoft.AzureStack."

**Példa** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral

> [!NOTE]
> Ezeket a lépéseket egy számítógépről, amely hozzáfér a kiemelt végpont kell futtatni. Az Azure verem szoftverfejlesztői készlet a számítógép lenne. Integrált rendszer használata, lépjen kapcsolatba az Azure-verem operátor.
>

1. Nyissa meg rendszergazdaként a PowerShell-konzolban és [verem Azure PowerShell telepítése](azure-stack-powershell-install.md).  

2. Adja hozzá az Azure-fiók, amely a használni kívánt Azure verem regisztrálni. Ehhez futtassa a `Add-AzureRmAccount` parancsmag paraméter nélkül. Az Azure-fiók hitelesítő adatainak megadását kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2-factor authentication használatával.  

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. A Powershell-modulok regisztrációs megfelelő meglévő verzióinak törlésére és [töltse le a legújabb verzióját a Githubról](azure-stack-powershell-download.md).  

5. A könyvtárból "AzureStack-eszközök – master", amely az előző lépésben létrehozott keresse meg a "Nyilvántartási" mappát, és a ".\RegisterWithAzure.psm1" modul importálása:  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsfájlt. Ha a hitelesítő adatok megadását kéri, adja meg a `azurestack\cloudadmin` a felhasználó és a jelszó megegyezik a használt a helyi rendszergazda a telepítés során.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | Paraméter | Leírás |
   | -------- | ------------- |
   | CloudAdminCredential | A felhő tartományi hitelesítő adatok, amelyek használt [hozzáférni a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). A felhasználónév nem formátumú "\<Azure verem tartomány\>\cloudadmin". A felhasználónév szoftverfejlesztői készlet, a "azurestack\cloudadmin" értéke. Integrált rendszer használata, lépjen kapcsolatba az Azure verem operátor ezt az értéket be kell olvasni.|
   | AzureSubscriptionId | Az Azure-előfizetés, amelyekkel regisztrálni Azure verem.|
   | AzureDirectoryTenantName | Az az Azure-előfizetéshez társított Azure-bérlőhöz könyvtár nevét. A regisztrációs erőforrás létrejön a directory-bérlő tartozik. |
   | PrivilegedEndpoint | Egy előre konfigurált távoli PowerShell-konzolt, és szolgáltatásokat, például a naplógyűjtést és egyéb utáni feladatok végrehajtását. A szoftverfejlesztői készlet a kiemelt végpont a "AzS-ERCS01" virtuális gép üzemelteti. Integrált rendszer használata, lépjen kapcsolatba az Azure verem operátor ezt az értéket be kell olvasni. További tudnivalókért tekintse meg a [használatával a privilegizált végpont](azure-stack-privileged-endpoint.md) témakör.|
   | BillingModel | A számlázási modellt, amely az előfizetés használ. Engedélyezett értékek a paraméter biztosan-"Kapacitás", "PayAsYouUse" és "Fejlesztési". A szoftverfejlesztői készlet az alapérték "Fejlesztés". Integrált rendszer használata, lépjen kapcsolatba az Azure verem operátor ezt az értéket be kell olvasni. |

7. Ha a parancsfájl lefutott, megjelenik egy üzenet "aktiválás Azure vermet (Ez a lépés akár 10 percet vehet)." 

## <a name="verify-the-registration"></a>A regisztráció-ellenőrzés

1. Jelentkezzen be a felügyeleti portálon (https://adminportal.local.azurestack.external).
2. Kattintson a **további szolgáltatások** > **piactér felügyeleti** > **adja hozzá az Azure-ból**.
3. Ha az Azure (például a WordPress) rendelkezésre álló elemek listája látható, az aktiválás sikeres volt.

> [!NOTE]
> Regisztráció befejezése után az aktív figyelmeztetés nincs regisztrálása nem fog megjelenni.

## <a name="next-steps"></a>Következő lépések

[Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md)

