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
ms.date: 11/21/2017
ms.author: erikje
ms.openlocfilehash: 6ce8f86592ece59e338578be86c2cb673c35dbc1
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
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


## <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral

> [!NOTE]
> Ezeket a lépéseket egy számítógépről, amely hozzáfér a kiemelt végpont kell futtatni. Az Azure verem szoftverfejlesztői készlet a számítógép lenne. Integrált rendszer használata, lépjen kapcsolatba az Azure-verem operátor.
>

1. Nyissa meg rendszergazdaként a PowerShell-konzolban és [verem Azure PowerShell telepítése](azure-stack-powershell-install.md).  

2. Adja hozzá az Azure-fiók, amely a használni kívánt Azure verem regisztrálni. Ehhez futtassa a `Add-AzureRmAccount` parancsmag és a EnvironmentName paraméter "AzureCloud" értékre. Az Azure-fiók hitelesítő adatainak megadását kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2-factor authentication használatával. 

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. A AzureStack erőforrás-szolgáltató regisztrálása az Azure-előfizetése. Ehhez futtassa a következő parancsot:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. A Powershell-modulok regisztrációs megfelelő meglévő verzióinak törlésére és [töltse le a legújabb verzióját a Githubról](azure-stack-powershell-download.md).  

6. A könyvtárból "AzureStack-eszközök – master", amely az előző lépésben létrehozott keresse meg a "Nyilvántartási" mappát, és a ".\RegisterWithAzure.psm1" modul importálása:  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

7. Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsfájlt. Ha a hitelesítő adatok megadását kéri, adja meg a `azurestack\cloudadmin` a felhasználó és a jelszó megegyezik a használt a helyi rendszergazda a telepítés során.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.SubscriptionId `
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

8. Ha a parancsfájl lefutott, megjelenik egy üzenet "aktiválás Azure vermet (Ez a lépés akár 10 percet vehet)." 

## <a name="verify-the-registration"></a>A regisztráció-ellenőrzés

1. Jelentkezzen be a felügyeleti portálon (https://adminportal.local.azurestack.external).
2. Kattintson a **további szolgáltatások** > **piactér felügyeleti** > **adja hozzá az Azure-ból**.
3. Ha az Azure (például a WordPress) rendelkezésre álló elemek listája látható, az aktiválás sikeres volt.

> [!NOTE]
> Regisztráció befejezése után az aktív figyelmeztetés nincs regisztrálása nem fog megjelenni.

## <a name="next-steps"></a>Következő lépések

[Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md)

