---
title: "Regisztrálja az Azure verem |} Microsoft Docs"
description: "Azure verem regisztrálása az Azure-előfizetéshez."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2018
ms.author: jeffgilb
ms.openlocfilehash: b58b3fc538d2237c12a860d268d550c4223155ba
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Az Azure verem regisztrálása az Azure-előfizetéshez

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Regisztrálhatja [Azure verem](azure-stack-poc.md) az Azure-ral Piactéri elemek letölteni az Azure-ból, és megkezdheti a Microsoft commerce adatok beállításához.

> [!NOTE]
>Regisztrációs használata ajánlott, mivel lehetővé teszi a fontos Azure verem funkciók, például a piactér szindikálási és használatai jelentések tesztelése. Miután regisztrálta Azure verem, használati Azure kereskedelmi bejelentések. Megtekintheti az előfizetésben a regisztrációhoz használt. Azure verem szoftverfejlesztői készlet felhasználók nem felszámított bármely használati jelentést.


## <a name="get-azure-subscription"></a>Az Azure előfizetés.

Mielőtt regisztrálná az Azure-vermet az Azure-ral, rendelkeznie kell:

- Az előfizetés-azonosító az Azure-előfizetéssel. Ahhoz, hogy az azonosító, jelentkezzen be az Azure-ba, és kattintson **további szolgáltatások** > **előfizetések**, kattintson az előfizetést szeretné használni, és a **Essentials** megtalálhatja a **Előfizetés-azonosító**. Kínai, német és US government felhőalapú előfizetések jelenleg nem támogatottak.
- A felhasználónév és jelszó egy olyan fiók, amely az előfizetés tulajdonosa (a felügyelt Szolgáltatásfiók/2FA fiókok támogatottak).
- *Nem kötelező Azure verem 1712 frissítés verziójától (180106.1):* az Azure Active Directory az Azure-előfizetéséhez. Ez a könyvtár az Azure-ban fölé a profilképet, az Azure-portál jobb felső sarkában található.

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, akkor [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/en-us/free/?b=17.06). Azure verem regisztrálása költséget nem áll, az Azure-előfizetése.

## <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral  
> [!NOTE]
> Ezeket a lépéseket egy számítógépről, amely hozzáfér a kiemelt végpont kell futtatni. Az Azure verem szoftverfejlesztői készlet a számítógép lenne. Integrált rendszer használata, lépjen kapcsolatba az Azure-verem operátor.
>

1. Nyissa meg rendszergazdaként a PowerShell-konzolban és [verem Azure PowerShell telepítése](azure-stack-powershell-install.md).  

2. Adja hozzá az Azure-fiókot regisztrálni Azure verem használó. A fiók hozzáadásához futtassa a `Add-AzureRmAccount` parancsmag és a EnvironmentName paraméter **AzureCloud**. Az Azure-fiók hitelesítő adatainak megadását kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2-factor authentication használatával.

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. A következő parancsot az Azure-előfizetéshez az Azure-verem erőforrás-szolgáltató regisztrálása:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. A PowerShell-modulok regisztrációs megfelelő meglévő verzióinak törlésére és [töltse le a legújabb verzióját a Githubról](azure-stack-powershell-download.md).  

6. A könyvtárból "AzureStack-eszközök – master", amely az előző lépésben létrehozott keresse meg a "Nyilvántartási" mappát, és a ".\RegisterWithAzure.psm1" modul importálása:  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsfájlt: Ha a hitelesítő adatok megadását kéri, adja meg a `azurestack\cloudadmin` a felhasználó és a jelszó megegyezik a használt a helyi rendszergazda a telepítés során.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | Paraméter | Leírás |  
   |--------|-------------|
   | CloudAdminCredential | A felhő tartományi hitelesítő adatok, amelyek használt [hozzáférni a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). A felhasználónév nem formátumú  **\<Azure verem tartomány\>\cloudadmin**. Szoftverfejlesztői készlet, a felhasználónév be az **azurestack\cloudadmin**. Integrált rendszer használata, lépjen kapcsolatba az Azure verem operátor ezt az értéket be kell olvasni.|  
   | PrivilegedEndpoint | Egy előre konfigurált távoli PowerShell-konzolt, és szolgáltatásokat, például a naplógyűjtést és egyéb utáni feladatok végrehajtását. A szoftverfejlesztői készlet a kiemelt végpont a "AzS-ERCS01" virtuális gép üzemelteti. Integrált rendszer használata, lépjen kapcsolatba az Azure verem operátor ezt az értéket be kell olvasni. További tudnivalókért tekintse meg a [használatával a privilegizált végpont](azure-stack-privileged-endpoint.md) cikk.|  
   | BillingModel | A számlázási modellt, amely az előfizetés használ. Megengedett értékek esetében ez a paraméter vannak - **kapacitás**, **PayAsYouUse**, és **fejlesztési**. Fejlesztői csomag a tulajdonság értéke **fejlesztési**. Integrált rendszer használata, lépjen kapcsolatba az Azure verem operátor ezt az értéket be kell olvasni. |

8. Ha a parancsfájl lefutott, megjelenik egy üzenet "aktiválás Azure vermet (Ez a lépés akár 10 percet vehet)."




## <a name="verify-the-registration"></a>A regisztráció-ellenőrzés  

1. Jelentkezzen be a felügyeleti portálon (https://adminportal.local.azurestack.external).

2. Kattintson a **további szolgáltatások** > **piactér felügyeleti** > **adja hozzá az Azure-ból**.

3. Ha az Azure (például a WordPress) rendelkezésre álló elemek listája látható, az aktiválás sikeres volt.

> [!NOTE]
> Regisztráció befejezése után az aktív figyelmeztetés nincs regisztrálása nem fog megjelenni.


## <a name="modify-the-registration"></a>A regisztrációs módosítása

### <a name="change-the-subscription-you-use"></a>Az előfizetés használhatja módosítása
Ha azt szeretné, használhatja a módosítása, meg kell először futtassa a Remove-AzsRegistration, ellenőrizze a megfelelő Azure PowerShell környezethez bejelentkezve és majd futtassa a Set-AzsRegistration módosított paramétereket.

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>Módosítsa a számlázási modellt vagy az együttműködési szolgáltatások
Ha azt szeretné, a számlázási modellt vagy az együttműködési szolgáltatások, a telepítés módosítása, hívása regisztrációs funkció az új értékeinek beállításához. Nem kell először távolítsa el az aktuális regisztrációt.  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>Kapcsolat nélküli regisztráció
*Az itt olvasható információk az Azure verem 1712 frissített verzióra (180106.1) kezdődő vonatkozik, és korábbi verzióiban nem támogatott.*

Ha Azure verem leválasztott környezetben regisztrál, regisztráció-token beszerzése a Azure verem környezetből, majd a jogkivonatot, amely csatlakozni tudna Azure regisztrálási gépen szüksége.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Regisztráció az Azure-verem környezetből token beszerzése
  1. A regisztrációs jogkivonat beszerzéséhez futtassa az alábbi parancsot:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > A megadott fájlt mentette a regisztrációs jogkivonat: *$env:SystemDrive\RegistrationToken.txt*.

  2. Mentse a regisztrációs jogkivonat használni az Azure machine csatlakoztatva.


### <a name="connect-to-azure-and-register"></a>Csatlakozás Azure és regisztrálása
Ez az eljárás lépéseinek futtatása, amely képes csatlakozni az Azure gépen.

  1. [Töltse le a legfrissebb PowerShell-modulok, amelyek megfelelnek a regisztrációhoz a Githubról](azure-stack-powershell-download.md).  

  2. A könyvtárból "AzureStack-eszközök – master", amely az előző lépésben létrehozott keresse meg a "Nyilvántartási" mappát, és a ".\RegisterWithAzure.psm1" modul importálása:  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. Másolás [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) mappába, például a *C:\Temp*.

  4. Indítsa el a PowerShell ISE rendszergazdaként, és importálja a RegisterWithAzure modul.  

  5. Győződjön meg arról, a megfelelő Azure PowerShell környezetben rögzítheti a verem Azure környezetben kívánt bejelentkezett:  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Adja meg az Azure-ral regisztrálása regisztrációs jogkivonatot:

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    A Get-tartalom parancsmag segítségével szükség esetén a regisztrációs jogkivonatot tartalmazó fájlra mutat:

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> Mentse a regisztrációs erőforrás neve vagy a regisztrációs jogkivonat későbbi felhasználás céljából.

### <a name="remove-a-registered-resource"></a>Távolítsa el a regisztrált erőforrás
Ha szeretné eltávolítani a regisztrációs erőforrás, majd akkor UnRegister-AzsEnvironment használja, és regisztrációs erőforrás nevét vagy a Register-AzsEnvironment használt regisztrációs jogkivonat adjon át.
- **Regisztrációs erőforrás neve:**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **Regisztrációs jogkivonat:**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>További lépések
[Csatlakozás az Azure Stackhez](azure-stack-connect-azure-stack.md)
