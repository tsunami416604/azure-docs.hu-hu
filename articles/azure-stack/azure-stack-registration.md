---
title: Azure-regisztráció az Azure Stack integrált rendszerek |} A Microsoft Docs
description: A több csomópontos, az Azure Stack Azure-kapcsolattal rendelkező üzemelő példányok az Azure-regisztráció folyamatát ismerteti.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: e73cd24064f2b0d6197a69251b55639d41e3212c
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39357748"
---
# <a name="register-azure-stack-with-azure"></a>Regisztráljon az Azure Stack az Azure-ral

Regisztrálás [Azure Stack](azure-stack-poc.md) lehetővé teszi, hogy az Azure marketplace-elemek letöltése, és megkezdheti a Microsoft kereskedelmi adatok beállítása az Azure-ral. Miután regisztrálta Azure Stack, az Azure kereskedelmi jelentett használati, és láthatja a regisztrációhoz használt előfizetés alatt.

> [!IMPORTANT]  
> Regisztráció teljes Azure Stack-funkciók, többek között a piactér tartalomtípus-gyűjtési támogatásához szükséges. Emellett fogja szabályzatsértéséről, a licencelési feltételeket, ha nem regisztrálja a használatalapú mint-akkor-használható számlázási modell használata esetén az Azure Stack. Azure Stack-licencelési modell kapcsolatos további információkért lásd: a [lap megvásárlása](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt regisztrálná az Azure Stack az Azure-ral, a következőkkel kell rendelkeznie:

- Azure-előfizetés előfizetés-azonosítója. Azonosítójának lekéréséhez jelentkezzen be az Azure-ba, kattintson a **további szolgáltatások** > **előfizetések**, kattintson arra az előfizetésre szeretné használni, majd a **Essentials** találja a Előfizetés-azonosítójára.

  > [!NOTE]
  > Németország és USA kormányzati felhőalapú előfizetések jelenleg nem támogatottak.

- A felhasználónevet és jelszót, amely az előfizetés tulajdonosa (MSA/2FA fiókok támogatottak).
- Az Azure Stack erőforrás-szolgáltató regisztrálva (lásd a regisztrálása az Azure Stack erőforrás-szolgáltató szakaszt alább részletekért).

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/free/?b=17.06). Az Azure-előfizetése ingyenes regisztrálása az Azure Stack tekintetében.

### <a name="powershell-language-mode"></a>PowerShell nyelvmód

Sikerült regisztrálni az Azure Stack, a PowerShell nyelv módba kell állítani **FullLanguageMode**.  Annak ellenőrzéséhez, hogy a jelenlegi nyelvmód beállítása teljes nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsokat:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, a kimenetet visszaadja **FullLanguageMode**. Ha bármilyen más nyelvmód adja vissza, egy másik gépen kell futtatnia kell a regisztrációs vagy a nyelvmód kell beállítani **FullLanguageMode** a folytatás előtt.

### <a name="bkmk_powershell"></a>Az Azure Stack PowerShell telepítése

Meg kell regisztrálni az Azure-ral az Azure stack PowerShell legújabb verzióját használja.

Ha még nem telepítette, [PowerShell telepítése az Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="bkmk_tools"></a>Az Azure Stack-eszközök letöltése

Az Azure Stack eszközök GitHub-adattár tartalmaz, amelyek támogatják az Azure Stack funkció; PowerShell-modulok beleértve a regisztrációs funkciót. A regisztráció során kell importálni és használni a RegisterWithAzure.psm1 PowerShell-modult az Azure Stack eszközök regisztrálása az Azure Stack-példány az Azure-ral adattárában található.

Annak érdekében, hogy a legújabb verzióját használja, törölnie kell minden meglévő verziói az Azure Stack-eszközök és [legújabb verziójának letöltése a Githubról](azure-stack-powershell-download.md) mielőtt regisztrálná az Azure-ral.

## <a name="register-azure-stack-in-connected-environments"></a>Regisztráljon az Azure Stack csatlakoztatott környezetekben

Csatlakoztatott környezetek hozzáférhet az interneten és az Azure. Ilyen környezetben kell az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-ral, és állítsa be a számlázási modellt.

> [!NOTE]
> Ezeket a lépéseket minden olyan számítógépen, amelyen a kiemelt végponthoz való hozzáférést adatbázisból kell futtatni.

### <a name="register-the-azure-stack-resource-provider"></a>Az Azure Stack erőforrás-szolgáltató regisztrálása

Az Azure-ral az Azure Stack erőforrás-szolgáltató regisztrálásához indítsa el a PowerShell ISE-t rendszergazdaként, és használja a következő PowerShell-parancsok használata a **EnvironmentName** (lásd a megfelelő Azure előfizetés-típus paraméter beállítása Paraméterek).

1. Adja hozzá az Azure-fiók, amely az Azure Stack regisztrálhat. A fiók hozzáadásához futtassa a **Add-AzureRmAccount** parancsmagot. Az Azure globális rendszergazdai fiók hitelesítő adatait kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2 többtényezős hitelesítés használatára.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure-felhő előfizetés környezet neve. Támogatott környezeti nevek **AzureCloud** vagy a China Azure-előfizetéssel, használatakor **AzureChinaCloud**.  |
   |  |  |

2. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza ki a:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Futtassa a következő parancsot az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-előfizetésben:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Regisztrálja az Azure Stack az Azure-ral a használatalapú mint-akkor-használható számlázási modell használatával

Ezeket a lépéseket segítségével regisztrálja az Azure Stack az Azure-ral a használatalapú mint-akkor-használható számlázási modell használatával.

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack-Tools-master** akkor könyvtárat kell létrehozni, [letöltött az Azure Stack eszközök](#bkmk_tools). Importálás a **RegisterWithAzure.psm1** a modul PowerShell-lel:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Ezután ugyanazon PowerShell-munkamenetben, győződjön meg arról, a megfelelő Azure PowerShell-környezet van bejelentkezve. Ez az azure-fiók, amellyel a fenti Azure Stack erőforrás-szolgáltató regisztrálásához. PowerShell futtatásához:

   ```PowerShell  
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. Az ugyanazon PowerShell-munkamenetben futtassa a **Set-AzsRegistration** parancsmagot. PowerShell futtatásához:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```

  |Paraméter|Leírás|
  |-----|-----|
  |PrivilegedEndpointCredential|A használt hitelesítő adatok [az emelt szintű végpontot](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). A felhasználónevet a következő formátumban kell **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Egy előre konfigurált távoli PowerShell-konzolt, amely biztosít, mint például a naplógyűjtés és egyéb post telepítési feladatokat. További tudnivalókért tekintse meg a [használatával a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) cikk.|
  |BillingModel|A számlázási modell, amely az előfizetés használja. Engedélyezett, ez a paraméter értékei a következők: kapacitás, PayAsYouUse és fejlesztésére.|
  | registrationName | Állítsa be egy egyedi nevet a regisztráció, ha futtatja a regisztrációs szkriptet az Azure Stack több példányának használatával az ugyanabban az Azure előfizetés-azonosító. A paraméter alapértelmezett értéke van **AzureStackRegistration**. Azonban, ha ugyanazzal a névvel egynél több Azure Stack-példányt használ, a parancsfájl futtatása sikertelen lesz. |

  A folyamat között 10 – 15 percet vesz igénybe. A parancs befejeződésekor megjelenik az üzenet **"a környezet már regisztrálva van, és aktiválta a megadott paraméterek használatával."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Regisztrálja az Azure Stack az Azure-ral a kapacitás számlázási modell használatával

Ugyanezeket az utasításokat a használatalapú mint-akkor-használható számlázási modellt használja, a regisztrációhoz használt, de a szerződés száma, amelyek alapján kapacitást vásárolt hozzáadása és módosítása a **BillingModel** paramétert **kapacitás**. Az összes többi paraméter nem változik.

PowerShell futtatásához:

```PowerShell  
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
$RegistrationName = "<unique-registration-name>"
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity `
    -RegistrationName $RegistrationName
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Regisztráljon az Azure Stack kapcsolat nélküli környezetben
Ha regisztrál az Azure Stack kapcsolat nélküli környezetben (az internet-hozzáférés nélküli), szerezze be egy regisztrációs tokent az Azure Stack-környezet, majd ezt a jogkivonatot, amely képes csatlakozni az Azure, és egy számítógépen kell [PowerShell az Azure Stack telepítése](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Szerezze be egy regisztrációs tokent az Azure Stack-környezet

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack-Tools-master** akkor könyvtárat kell létrehozni, [letöltött az Azure Stack eszközök](#bkmk_tools). Importálás a **RegisterWithAzure.psm1** modul:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. A regisztrációs jogkivonatot, futtassa a következő PowerShell-parancsokat:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > A regisztrációs jogkivonatot a rendszer menti a fájlt a megadott *$FilePathForRegistrationToken*. A fájl elérési útja vagy fájlnév szabadon módosíthatja.

3. A regisztrációs jogkivonatot az Azure-beli használatra mentse géphez csatlakoztatva. $FilePathForRegistrationToken átmásolhatja a fájlt vagy a szöveget.

### <a name="connect-to-azure-and-register"></a>Csatlakozás az Azure és a regisztráció

Azon a számítógépen, amely az internethez csatlakozik hajtsa végre a RegisterWithAzure.psm1 modul importálása, és jelentkezzen be a megfelelő Azure Powershell környezetben ugyanazokat a lépéseket. Majd hívja a Register-AzsEnvironment. Adja meg a regisztrációs jogkivonatot, regisztrálni az Azure-ral. Ha regisztrál az Azure Stack segítségével az ugyanazon Azure-előfizetési azonosító egynél több példányát, adjon meg egy egyedi regisztrációs nevet. Futtassa a következő parancsmagot:

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

A Get-tartalom parancsmag segítségével igény szerint, a regisztrációs jogkivonatot tartalmazó fájlra mutat:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!NOTE]  
  > A regisztrációs erőforrás neve és a regisztrációs jogkivonatot, a későbbiekben takaríthat meg.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Azure-regisztráció erőforrás egy aktiválási kulcs lekérése

Ezt követően kell az aktiválási kulcs lekérése a Register-AzsEnvironment során az Azure-ban létrehozott regisztrációs erőforrás.

Az aktiválási kulcs lekéréséhez futtassa a következő PowerShell-parancsokat:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > Az aktiválási kulcsot a rendszer menti a megadott fájl *$KeyOutputFilePath*. A fájl elérési útja vagy fájlnév szabadon módosíthatja.

### <a name="create-an-activation-resource-in-azure-stack"></a>Hozzon létre egy aktiválási erőforrást az Azure Stackben

Térjen vissza az Azure Stack-környezet a fájl vagy a szöveget a Get-AzsActivationKey alapján létrehozott aktiválási kulcs. Ezután létrehozhat egy aktiválási erőforrást az Azure Stack használatával a aktiválási kulcs. Hozzon létre egy aktiválási erőforrást, futtassa a következő PowerShell-parancsokat:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

A Get-tartalom parancsmag segítségével igény szerint, a regisztrációs jogkivonatot tartalmazó fájlra mutat:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Azure Stack-regisztráció ellenőrzése

Használja ezeket a lépéseket, győződjön meg arról, hogy az Azure Stack sikeresen regisztrálva van az Azure-ral.

1. Jelentkezzen be az Azure Stack [adminisztrátori portál](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;régió >. &lt;teljesen minősített tartományneve >*.
2. Válassza ki **további szolgáltatások** > **Marketplace felügyeleti** > **hozzáadása az Azure-ból**.

Ha látja az Azure (például WordPress) rendelkezésre álló elemek listáját, az aktiválás sikeres volt. Azonban a kapcsolat nélküli környezetben nem látják az Azure Stack piactéren Azure marketplace-elemek.

> [!NOTE]
> Regisztráció befejezése után az aktív figyelmeztetés nem regisztrálásához már nem jelenik meg.

## <a name="renew-or-change-registration"></a>Újítsa meg vagy módosítsa a regisztráció

### <a name="renew-or-change-registration-in-connected-environments"></a>Újítsa meg vagy módosítsa a csatlakoztatott környezetekben regisztrációs

Frissítés vagy újítania regisztrációját a következő körülmények között szüksége:

- Miután kapacitásalapú éves előfizetés megújítása.
- Ha módosítja a számlázási modellt.
- Módosítások (csomópont hozzáadása/eltávolítása) Ha a kapacitás-alapú számlázáshoz méretezése.

#### <a name="change-the-subscription-you-use"></a>Az előfizetést használ

Ha szeretné az előfizetést használ, előbb futtatnia kell a **Remove-AzsRegistration** parancsmagot, majd győződjön meg arról, a megfelelő Azure PowerShell-környezet van bejelentkezve, és végül futtassa **Set-AzsRegistration**  bármelyik módosítani a Paraméterek:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Módosítsa a számlázási modell, vagy az együttműködési funkciók

Ha szeretné a számlázási modellt vagy az együttműködési funkciókat a telepítés módosítása, meghívhatja a regisztrációs függvény, amely az új értékeinek megadására. Nem kell először távolítsa el a jelenlegi regisztrációs:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Újítsa meg vagy módosítsa a kapcsolat nélküli környezetekben regisztrációs

Frissítés vagy újítania regisztrációját a következő körülmények között szüksége:

- Miután kapacitásalapú éves előfizetés megújítása.
- Ha módosítja a számlázási modellt.
- Módosítások (csomópont hozzáadása/eltávolítása) Ha a kapacitás-alapú számlázáshoz méretezése.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Az aktiválási erőforrás eltávolítása az Azure Stackben

Először az aktiválási erőforrás eltávolítása az Azure Stack, és a regisztrációs erőforrás az Azure-ban.  

Az Azure Stack az aktiválási erőforrás eltávolításához futtassa a következő PowerShell-parancsok az Azure Stack környezettel:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Ezután távolítsa el a regisztrációs erőforrást az Azure-ban, hogy ellenőrizze, hogy a csatlakoztatott Azure-beli számítógép, jelentkezzen be a megfelelő Azure PowerShell-környezet, és a megfelelő PowerShell-parancsok futtatását, az alább ismertetett.

A regisztrációs jogkivonatot, használja az erőforrás létrehozásához is használhatja:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Vagy használhatja a regisztrációs név:

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Regisztrálja újra a leválasztott lépésekkel

A leválasztott forgatókönyvek esetében már rendelkezik teljesen regisztrációját, és meg kell ismételnie a lépéseket az Azure Stack-környezet regisztrálásához a leválasztott forgatókönyvek esetében.

## <a name="next-steps"></a>További lépések

[Az Azure marketplace-elemek letöltése](azure-stack-download-azure-marketplace-item.md)
