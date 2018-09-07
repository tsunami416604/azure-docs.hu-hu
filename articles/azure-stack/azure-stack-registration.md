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
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: 52d0706177482e162d1f4bc038c967a4596fd3b3
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024858"
---
# <a name="register-azure-stack-with-azure"></a>Regisztráljon az Azure Stack az Azure-ral

Regisztrálás az Azure Stack az Azure-ral lehetővé teszi, hogy az Azure marketplace-elemek letöltése, és megkezdheti a Microsoft kereskedelmi adatok beállítása. Miután regisztrálta Azure Stack, az Azure kereskedelmi jelentett használati, és láthatja a regisztrációhoz használt előfizetés alatt.

Ez a cikk ismerteti az Azure-ban való regisztrálásáról Azure Stack integrált rendszerek. A ASDK regisztrálása az Azure-ral kapcsolatos információkért lásd: [Azure Stack-regisztráció](.\asdk\asdk-register.md) ASDK dokumentációjában.

> [!IMPORTANT]  
> Regisztráció szükséges teljes körű Azure Stack funkciót, beleértve az ajánlatot a piactéren elemek. Emellett fogja szabályzatsértéséről, a licencelési feltételeket, ha nem regisztrálja a használatalapú mint-akkor-használható számlázási modell használata esetén az Azure Stack. Azure Stack-licencelési modell kapcsolatos további információkért lásd: a [lap megvásárlása](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Előfeltételek

Regisztráció előtt szüksége lesz a következő helyen:

 - A hitelesítő adatok ellenőrzéséhez
 - A PowerShell nyelvmód beállítása
 - Az Azure Stack PowerShell telepítése
 - Az Azure Stack-eszközök letöltése
 - A regisztrációs forgatókönyv meghatározása

### <a name="verify-your-credentials"></a>A hitelesítő adatok ellenőrzéséhez

Mielőtt regisztrálná az Azure Stack az Azure-ral, a következőkkel kell rendelkeznie:

- Azure-előfizetés előfizetés-azonosítója. Az azonosító lekéréséhez jelentkezzen be az Azure-ba, kattintson a **minden szolgáltatás**. Ezt követően a **általános** kategória, válassza **előfizetések**, kattintson arra az előfizetésre szeretné használni, majd a **Essentials** is megtalálhatja, hogy az előfizetés-azonosító.

  > [!Note]  
  > A Németországi felhőalapú előfizetések jelenleg nem támogatottak.

- A felhasználónevet és jelszót, amely az előfizetés tulajdonosa (MSA/2FA fiókok támogatottak).

- A felhasználói fióknak kell lennie az Azure AD-bérlővel, amely az Azure Stack regisztrálva van, például a rendszergazda `yourazurestacktenant.onmicrosoft.com`.

- Az Azure Stack erőforrás-szolgáltató regisztrálva (lásd a regisztrálása az Azure Stack erőforrás-szolgáltató szakaszt alább részletekért).

  Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/free/?b=17.06). Az Azure-előfizetése ingyenes regisztrálása az Azure Stack tekintetében.

### <a name="powershell-language-mode"></a>PowerShell nyelvmód

Sikerült regisztrálni az Azure Stack, a PowerShell nyelv módba kell állítani **FullLanguageMode**.  Annak ellenőrzéséhez, hogy a jelenlegi nyelvmód beállítása teljes nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsmagokat:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, a kimenetet visszaadja **FullLanguageMode**. Ha bármilyen más nyelvmód adja vissza, egy másik gépen kell futtatnia kell a regisztrációs vagy a nyelvmód kell beállítani **FullLanguageMode** a folytatás előtt.

### <a name="install-powershell-for-azure-stack"></a>Az Azure Stack PowerShell telepítése

Meg kell regisztrálni az Azure-ral az Azure stack PowerShell legújabb verzióját használja.

Ha nem a legújabb verziót nem telepítette, [PowerShell telepítése az Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="download-the-azure-stack-tools"></a>Az Azure Stack-eszközök letöltése

Az Azure Stack eszközök GitHub-adattár tartalmaz, amelyek támogatják az Azure Stack funkció; PowerShell-modulok beleértve a regisztrációs funkciót. A regisztráció során kell importálni és használni a **RegisterWithAzure.psm1** PowerShell-modul az Azure Stack eszközök regisztrálása az Azure Stack-példány az Azure-ral adattárában található.

Annak érdekében, hogy a legújabb verzióját használja, törölnie kell minden meglévő verziói az Azure Stack-eszközök és [legújabb verziójának letöltése a Githubról](azure-stack-powershell-download.md) mielőtt regisztrálná az Azure-ral.

### <a name="determine-your-registration-scenario"></a>A regisztrációs forgatókönyv meghatározása

Lehet, hogy az Azure Stack üzemelő példányához *csatlakoztatott* vagy *leválasztott*.

 - **Csatlakoztatva**  
 A kapcsolat azt jelenti, hogy telepítette az Azure Stack, hogy képes csatlakozni az internethez, és az Azure-bA. Az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) számára az ügyfélidentitás-tárolóval, vagy rendelkezik. Egy csatlakoztatott üzembe helyezéssel két számlázási modellek közül választhat: használatalapú mint-akkor-használható, vagy kapacitás-alapú.
    - [A csatlakoztatott Azure Stack regisztrálása az Azure-ban a **használatalapú mint-akkor-használható** számlázási modell](#register-connected-with-pay-as-you-go-billing)
    - [A csatlakoztatott Azure Stack regisztrálása az Azure-ban a **kapacitás** számlázási modell](#register-connected-with-capacity-billing)

 - **Leválasztva**  
 A leválasztott az Azure üzembe helyezési lehetőséget, üzembe helyezése és használata az Azure Stack az internethez való csatlakozás nélkül. Azonban egy kapcsolat nélküli telepítés pedig csak az AD FS ügyfélidentitás-tárolóval, és a kapacitás-alapú számlázási modell.
    - [A leválasztott Azure Stack használatával regisztrálja a **kapacitás** számlázási modell ](#register-disconnected-with-capacity-billing)

## <a name="register-connected-with-pay-as-you-go-billing"></a>Regisztráció a használatalapú számlázás csatlakoztatva

Ezeket a lépéseket segítségével regisztrálja az Azure Stack az Azure-ral a használatalapú mint-akkor-használható számlázási modell használatával.

> [!Note]  
> Ezek a lépések egy számítógépről, amely hozzáfér a kiemelt végponthoz (EGP) kell futnia. További információk az EGP: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md).

Csatlakoztatott környezetek hozzáférhet az interneten és az Azure. Ilyen környezetben kell az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-ral, és állítsa be a számlázási modellt.

1. Az Azure-ral az Azure Stack erőforrás-szolgáltató regisztrálásához indítsa el a PowerShell ISE-t rendszergazdaként, és a következő PowerShell-parancsmag használatával a **EnvironmentName** (lásd a megfelelő Azure előfizetés-típus paraméter beállítása Paraméterek).

2. Adja hozzá az Azure-fiók, amely az Azure Stack regisztrálhat. A fiók hozzáadásához futtassa a **Add-AzureRmAccount** parancsmagot. Az Azure globális rendszergazdai fiók hitelesítő adatait kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2 többtényezős hitelesítés használatára.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure-felhő előfizetés környezet neve. Támogatott környezeti nevek **AzureCloud**, **AzureUSGovernment**, vagy a China Azure-előfizetéssel, használatakor **AzureChinaCloud**.  |

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza ki a:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Futtassa a következő parancsot az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-előfizetésben:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack-Tools-master** akkor könyvtárat kell létrehozni, [letöltött az Azure Stack eszközök](#bkmk_tools). Importálás a **RegisterWithAzure.psm1** a modul PowerShell-lel:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Ezután ugyanazon PowerShell-munkamenetben, győződjön meg arról, a megfelelő Azure PowerShell-környezet van bejelentkezve. Ez az azure-fiók, amellyel a fenti Azure Stack erőforrás-szolgáltató regisztrálásához. PowerShell futtatásához:

   ```PowerShell  
   Add-AzureRmAccount -Environment "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

7. Az ugyanazon PowerShell-munkamenetben futtassa a **Set-AzsRegistration** parancsmagot. PowerShell futtatásához:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   A Set-AzsRegistration parancsmagról további információkért lásd: [Alkalmazásregisztráció-referencia](#registration-reference).

  A folyamat között 10 – 15 percet vesz igénybe. A parancs befejeződésekor megjelenik az üzenet **"a környezet már regisztrálva van, és aktiválta a megadott paraméterek használatával."**

## <a name="register-connected-with-capacity-billing"></a>A kapacitás számlázással csatlakoztatott regisztrálása

Ezeket a lépéseket segítségével regisztrálja az Azure Stack az Azure-ral a használatalapú mint-akkor-használható számlázási modell használatával.

> [!Note]  
> Ezek a lépések egy számítógépről, amely hozzáfér a kiemelt végponthoz (EGP) kell futnia. További információk az EGP: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md).

Csatlakoztatott környezetek hozzáférhet az interneten és az Azure. Ilyen környezetben kell az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-ral, és állítsa be a számlázási modellt.

1. Az Azure-ral az Azure Stack erőforrás-szolgáltató regisztrálásához indítsa el a PowerShell ISE-t rendszergazdaként, és a következő PowerShell-parancsmag használatával a **EnvironmentName** (lásd a megfelelő Azure előfizetés-típus paraméter beállítása Paraméterek).

2. Adja hozzá az Azure-fiók, amely az Azure Stack regisztrálhat. A fiók hozzáadásához futtassa a **Add-AzureRmAccount** parancsmagot. Az Azure globális rendszergazdai fiók hitelesítő adatait kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2 többtényezős hitelesítés használatára.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<AzureCloud, AzureChinaCloud, or AzureUSGovernment>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure-felhő előfizetés környezet neve. Támogatott környezeti nevek **AzureCloud**, **AzureUSGovernment**, vagy a China Azure-előfizetéssel, használatakor **AzureChinaCloud**.  |

3. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza ki a:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Futtassa a következő parancsot az Azure Stack erőforrás-szolgáltató regisztrálása az Azure-előfizetésben:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Indítsa el a PowerShell ISE-t rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack-Tools-master** akkor könyvtárat kell létrehozni, [letöltött az Azure Stack eszközök](#bkmk_tools). Importálás a **RegisterWithAzure.psm1** a modul PowerShell-lel:

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > Használatra vonatkozó jelentések készítéséhez a UsageReportingEnabled paraméterrel letilthatja a **Set-AzsRegistration** parancsmagot. A paraméter értéke hamis. Például: "UsageReportingEnabled
   
  A Set-AzsRegistration parancsmagról további információkért lásd: [Alkalmazásregisztráció-referencia](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>A kapacitás számlázással leválasztott regisztrálása

Ha regisztrál az Azure Stack kapcsolat nélküli környezetben (az internet-hozzáférés nélküli), szerezze be egy regisztrációs tokent az Azure Stack-környezet, majd ezt a jogkivonatot, amely képes csatlakozni az Azure, és egy számítógépen kell [PowerShell az Azure Stack telepítése](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Szerezze be egy regisztrációs tokent az Azure Stack-környezet

1. Indítsa el a PowerShell ISE-t rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack-Tools-master** akkor könyvtárat kell létrehozni, [letöltött az Azure Stack eszközök](#bkmk_tools). Importálás a **RegisterWithAzure.psm1** modul:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. A regisztrációs jogkivonatot, futtassa a következő PowerShell-parancsmagokat:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   A Get-AzsRegistrationToken parancsmagról további információkért lásd: [Alkalmazásregisztráció-referencia](#registration-reference).

   > [!Tip]  
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

  > [!Note]  
  > A regisztrációs erőforrás neve és a regisztrációs jogkivonatot, a későbbiekben takaríthat meg.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Azure-regisztráció erőforrás egy aktiválási kulcs lekérése

Ezt követően kell az aktiválási kulcs lekérése a Register-AzsEnvironment során az Azure-ban létrehozott regisztrációs erőforrás.

Az aktiválási kulcs lekéréséhez futtassa a következő PowerShell-parancsmagokat:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Az aktiválási kulcsot a rendszer menti a megadott fájl *$KeyOutputFilePath*. A fájl elérési útja vagy fájlnév szabadon módosíthatja.

### <a name="create-an-activation-resource-in-azure-stack"></a>Hozzon létre egy aktiválási erőforrást az Azure Stackben

Térjen vissza az Azure Stack-környezet a fájl vagy a szöveget a Get-AzsActivationKey alapján létrehozott aktiválási kulcs. Ezután létrehozhat egy aktiválási erőforrást az Azure Stack használatával a aktiválási kulcs. Hozzon létre egy aktiválási erőforrást, futtassa a következő PowerShell-parancsmagokat:  

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
2. Válassza ki **minden szolgáltatás**, majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti** > **Azurehozzáadása**.

Ha látja az Azure (például WordPress) rendelkezésre álló elemek listáját, az aktiválás sikeres volt. Azonban a kapcsolat nélküli környezetben nem látják az Azure Stack piactéren Azure marketplace-elemek.

> [!Note]  
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

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Módosítsa a számlázási modellt vagy a szolgáltatásokkal

Ha szeretné módosítani a számlázási modellt vagy a megfelelő telepítési szolgáltatásokkal, meghívhatja a regisztrációs függvény, amely az új értékeinek megadására. Nem kell először távolítsa el a jelenlegi regisztrációs:

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

Az Azure Stack az aktiválási erőforrás eltávolításához futtassa a következő PowerShell-parancsmagok az Azure Stack környezettel:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Ezután távolítsa el a regisztrációs erőforrást az Azure-ban, hogy ellenőrizze, hogy a csatlakoztatott Azure-beli számítógép, jelentkezzen be a megfelelő Azure PowerShell-környezetben, és a megfelelő PowerShell-parancsmagok futtatásához az alább ismertetett.

A regisztrációs jogkivonatot, használja az erőforrás létrehozásához is használhatja:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Vagy használhatja a regisztrációs név:

  ```Powershell
  $registrationName = "AzureStack-<Cloud ID of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Regisztrálja újra a leválasztott lépésekkel

A leválasztott forgatókönyvek esetében már rendelkezik teljesen regisztrációját, és meg kell ismételnie a lépéseket az Azure Stack-környezet regisztrálásához a leválasztott forgatókönyvek esetében.

### <a name="disable-or-enable-usage-reporting"></a>Használati jelentések engedélyezése vagy letiltása

Azure Stack-környezetekben, amelyek egy kapacitás-számlázási modell, kapcsolja ki a jelentéskészítés a használati a **UsageReportingEnabled** paraméter használatával a **Set-AzsRegistration** vagy a  **Get-AzsRegistrationToken** parancsmagok. Az Azure Stack használati metrikai jelentések alapértelmezés szerint. Kapacitás használt vagy leválasztott környezetben támogató kezelők kell kikapcsolni a használati jelentések készítése.

#### <a name="with-a-connected-azure-stack"></a>A csatlakoztatott Azure Stack segítségével

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Egy kapcsolat nélküli Azure Stack segítségével

1. Ha módosítani szeretné a regisztrációs jogkivonatot, futtassa a következő PowerShell-parancsmagokat:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > A regisztrációs jogkivonatot a rendszer menti a fájlt a megadott *$FilePathForRegistrationToken*. A fájl elérési útja vagy fájlnév szabadon módosíthatja.

2. A regisztrációs jogkivonatot az Azure-beli használatra mentse géphez csatlakoztatva. $FilePathForRegistrationToken átmásolhatja a fájlt vagy a szöveget.

## <a name="move-a-registration-resource"></a>Regisztrációs erőforrás áthelyezése
Regisztrációs erőforrás ugyanahhoz az előfizetéshez tartozó erőforráscsoportok közötti áthelyezése **van** minden környezet esetében támogatott. Azonban regisztrációs erőforrások áthelyezése előfizetések között csak a támogatott CSP-k amikor mindkét előfizetés oldja fel a ugyanezzel az azonosítóval Partner. Erőforrások áthelyezése új erőforráscsoport kapcsolatos további információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Alkalmazásregisztráció-referencia

### <a name="set-azsregistration"></a>Set-AzsRegistration

Set-AzsRegistration segítségével regisztrálja az Azure Stack az Azure-ral és engedélyezése vagy letiltása az ajánlatot a piactéren, és használati jelentések készítése elemek.

A parancsmag futtatásához szüksége:
- Globális Azure-előfizetés bármilyen típusú.
- Akkor is kell bejelentkeznie az Azure PowerShell egy olyan fiókkal, amely tulajdonosi vagy közreműködői előfizetéshez.

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| Paraméter | Típus | Leírás |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | A használt hitelesítő adatok [az emelt szintű végpontot](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). A felhasználónevet a következő formátumban kell **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Sztring | Egy előre konfigurált távoli PowerShell-konzolt, amely biztosít, mint például a naplógyűjtés és egyéb post telepítési feladatokat. További tudnivalókért tekintse meg a [használatával a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) cikk. |
| AzureContext | PSObject |  |
| ResourceGroupName | Sztring |  |
| ResourceGroupLocation | Sztring |  |
| BillingModel | Sztring | A számlázási modell, amely az előfizetés használja. Engedélyezett, ez a paraméter értékei a következők: kapacitás, PayAsYouUse és fejlesztésére. |
| MarketplaceSyndicationEnabled |  |  |
| UsageReportingEnabled | Igaz/hamis | Az Azure Stack használati metrikai jelentések alapértelmezés szerint. Kapacitás használt vagy leválasztott környezetben támogató kezelők kell kikapcsolni a használati jelentések készítése. Engedélyezett, ez a paraméter értékei a következők: True, False. |
| AgreementNumber | Sztring |  |
| registrationName | Sztring | Állítsa be egy egyedi nevet a regisztráció, ha futtatja a regisztrációs szkriptet az Azure Stack több példányának használatával az ugyanabban az Azure előfizetés-azonosító. A paraméter alapértelmezett értéke van **AzureStackRegistration**. Azonban, ha ugyanazzal a névvel egynél több Azure Stack-példányt használ, a parancsfájl futtatása sikertelen lesz. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken létrehoz egy regisztrációs tokent a bemeneti paraméterek közül.

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Paraméter | Típus | Leírás |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | A használt hitelesítő adatok [az emelt szintű végpontot](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). A felhasználónevet a következő formátumban kell **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Sztring |  Egy előre konfigurált távoli PowerShell-konzolt, amely biztosít, mint például a naplógyűjtés és egyéb post telepítési feladatokat. További tudnivalókért tekintse meg a [használatával a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) cikk. |
| AzureContext | PSObject |  |
| ResourceGroupName | Sztring |  |
| ResourceGroupLocation | Sztring |  |
| BillingModel | Sztring | A számlázási modell, amely az előfizetés használja. Engedélyezett, ez a paraméter értékei a következők: kapacitás, PayAsYouUse és fejlesztésére. |
| MarketplaceSyndicationEnabled | Igaz/hamis |  |
| UsageReportingEnabled | Igaz/hamis | Az Azure Stack használati metrikai jelentések alapértelmezés szerint. Kapacitás használt vagy leválasztott környezetben támogató kezelők kell kikapcsolni a használati jelentések készítése. Engedélyezett, ez a paraméter értékei a következők: True, False. |
| AgreementNumber | Sztring |  |


## <a name="next-steps"></a>További lépések

[Az Azure marketplace-elemek letöltése](azure-stack-download-azure-marketplace-item.md)
