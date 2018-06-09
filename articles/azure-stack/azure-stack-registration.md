---
title: Az Azure regisztrációs Azure verem integrált rendszerek |} Microsoft Docs
description: Több csomópontos rendszerekhez. Azure-verem Azure csatlakoztatott Azure regisztrációs folyamatát mutatjuk be.
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: 7d14b246220264641a3bb726d5505c25dc25bbbd
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248142"
---
# <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral

Regisztrálás [Azure verem](azure-stack-poc.md) az Azure-ral lehetővé teszi a Piactéri elemek letölteni az Azure-ból, és megkezdheti a Microsoft commerce adatok beállításához. Miután regisztrálta Azure-vermet, használati bejelentések Azure kereskedelmi, és megtekintheti az előfizetésben a regisztrációhoz használt.

> [!IMPORTANT]
> Regisztrációs teljes Azure verem funkciókat, beleértve a Piactéri szindikálási támogatásához szükséges. Emellett a licencelési időszakonként, ha nem regisztrálja a fizetési,-akkor-használható számlázási modellt használatakor Azure verem megsértését fog szerepelni. Azure verem modellek licencelési kapcsolatos további tudnivalókért tekintse meg a [lap megvásárlása](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt regisztrálná az Azure-vermet az Azure-ral, rendelkeznie kell:

- Az előfizetés-azonosító az Azure-előfizetéssel. Ahhoz, hogy az azonosító, jelentkezzen be az Azure-ba, és kattintson **további szolgáltatások** > **előfizetések**, kattintson az előfizetést szeretné használni, és a **Essentials** megtalálhatja a Előfizetés-azonosító.

  > [!NOTE]
  > Németország és az USA szövetségi kormányzatának felhőalapú előfizetések jelenleg nem támogatottak.

- A felhasználónév és jelszó egy olyan fiók, amely az előfizetés tulajdonosa (a felügyelt Szolgáltatásfiók/2FA fiókok támogatottak).
- Az Azure-verem erőforrás-szolgáltató regisztrálva (lásd a regisztrálni Azure verem erőforrás-szolgáltató szakaszát alatt).

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, akkor [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/free/?b=17.06). Azure verem regisztrálása költséget nem áll, az Azure-előfizetése.

### <a name="powershell-language-mode"></a>PowerShell nyelvi mód

Sikeresen regisztrálni Azure-vermet, a PowerShell nyelvi mód értékre kell állítani **FullLanguageMode**.  Győződjön meg arról, hogy a jelenlegi nyelvi mód állítsa teljes nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsokat:

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, a kimenetet visszaadja **FullLanguageMode**. Ha más nyelv módot ad vissza, regisztrációs egy másik számítógépen kell futtatnia kell majd vagy a nyelvi módot kell beállítani **FullLanguageMode** folytatása előtt.

### <a name="bkmk_powershell"></a>A verem az Azure PowerShell telepítése

Szeretné használni a legújabb Azure verem PowerShell Azure regisztrálására.

Ha még nincs telepítve, [verem Azure PowerShell telepítése](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="bkmk_tools"></a>Töltse le az Azure-verem eszközök

Az Azure-verem eszközök GitHub-tárházban tartalmazza, amelyek támogatják az Azure-verem funkció; PowerShell-modulok beleértve a regisztrációs funkciót. A regisztráció során kell importálni és használni a RegisterWithAzure.psm1 PowerShell modul Azure verem eszközök, az Azure-verem példányának regisztrálni Azure tárházban található.

Győződjön meg arról, a legújabb verzióját használja, törölnie kell az Azure-verem eszközök minden meglévő verzióját, és [a legújabb verzió letöltéséhez a Githubról](azure-stack-powershell-download.md) mielőtt regisztrálná az Azure-ral.

## <a name="register-azure-stack-in-connected-environments"></a>Regisztrálja az Azure verem csatlakoztatott környezetekben

Csatlakoztatott környezetekben az internetes és az Azure férhetnek hozzá. Ilyen környezetben kell az Azure-verem erőforrás-szolgáltató regisztrálása az Azure-ral, és adja meg a számlázási modellt.

> [!NOTE]
> Ezeket a lépéseket egy számítógépről, amely hozzáfér a kiemelt végpontot kell futtatni.

### <a name="register-the-azure-stack-resource-provider"></a>Az Azure-verem erőforrás-szolgáltató regisztrálása

Az Azure-verem erőforrás-szolgáltató regisztrálása az Azure-ral, indítsa el a PowerShell ISE rendszergazdaként, és használja a következő PowerShell-parancsokat a **EnvironmentName** paraméter értéke a megfelelő Azure-előfizetés típusa (lásd: Paraméterek).

1. Adja hozzá az Azure-fiókot regisztrálni Azure verem használó. A fiók hozzáadásához futtassa a **Add-AzureRmAccount** parancsmag. A Azure globális rendszergazdai fiók hitelesítő adatainak megadását kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2-factor authentication használatával.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Paraméter | Leírás |  
   |-----|-----|
   | EnvironmentName | Az Azure felhőalapú előfizetési környezet neve. Támogatott környezet neve **AzureCloud** vagy, ha Kína Azure-előfizetést használ **AzureChinaCloud**.  |
   |  |  |

2. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza:  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. A következő parancsot az Azure-előfizetéshez az Azure-verem erőforrás-szolgáltató regisztrálása:

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>A fizetési,-akkor-használható számlázási modellt használó Azure Azure verem regisztrálása

Ezen lépések segítségével Azure verem regisztrálni Azure-ban a fizetési,-akkor-használható számlázási modellt.

1. Indítsa el a PowerShell ISE rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack főkiszolgálós eszközök** létrehozta könyvtárat, [letöltötte az Azure-verem eszközök](#bkmk_tools). Importálás a **RegisterWithAzure.psm1** modul PowerShell használatával:

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Az ugyanazon PowerShell-munkamenetben, ellenőrizze, a megfelelő Azure PowerShell környezetre van bejelentkezve. Ez az a fenti Azure verem erőforrás-szolgáltató regisztrálása használt azure-fiók. A PowerShell futtatásához:

   ```powershell
   Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>"
   ```

3. Az ugyanazon PowerShell-munkamenetben futtassa a **Set-AzsRegistration** parancsmag. A PowerShell futtatásához:  

   ```powershell
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
   ```

  |Paraméter|Leírás|
  |-----|-----|
  |PrivilegedEndpointCredential|A használt hitelesítő adatok [hozzáférni a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). A felhasználónév nem formátumú **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Egy előre konfigurált távoli PowerShell-konzolt, és szolgáltatásokat, például a naplógyűjtést és egyéb utáni feladatok végrehajtását. További tudnivalókért tekintse meg a [használatával a privilegizált végpont](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) cikk.|
  |BillingModel|A számlázási modellt, amely az előfizetés használ. Engedélyezett értékek a paraméter: kapacitás, PayAsYouUse és fejlesztésére.|
  |  |  |

  A folyamat között 10 – 15 percet vesz igénybe. A parancs befejeződésekor üzenet jelenik meg a **"a környezet most már regisztrálva van-e, és a megadott paraméterek aktiválva."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>A kapacitás számlázási modellt használó Azure Azure verem regisztrálása

A fizetési,-akkor-használható számlázási modell használatával a regisztrációhoz használt ugyanezeket az utasításokat, de vegye fel a Szerződésszám kapacitás vásárlásának módjától, és módosítsa a **BillingModel** paramétert **kapacitás**. Minden más paraméter nem változnak.

A PowerShell futtatásához:

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Kapcsolat nélküli környezetben Azure verem regisztrálása

*Az itt olvasható információk az Azure verem 1712 frissített verzióra (180106.1) kezdődő vonatkozik, és korábbi verzióiban nem támogatott.*

Ha regisztrál Azure verem leválasztott környezetben (az internet-hozzáférés nélküli), szeretné-e regisztráció-token beszerzése a Azure verem környezetből, majd a jogkivonatot egy számítógépen, és csatlakozni tud-e Azure [PowerShell Azure-verem telepítve](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Regisztráció az Azure-verem környezetből token beszerzése

1. Indítsa el a PowerShell ISE rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack főkiszolgálós eszközök** létrehozta könyvtárat, [letöltötte az Azure-verem eszközök](#bkmk_tools). Importálás a **RegisterWithAzure.psm1** modul:  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Ahhoz, hogy a regisztrációs jogkivonatot, futtassa a következő PowerShell-parancsokat:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!TIP]  
   > A megadott fájlt mentette a regisztrációs jogkivonat: *$FilePathForRegistrationToken*. Módosíthatja a fájl elérési útja vagy fájlnév közül.

3. Mentse a regisztrációs jogkivonat használni az Azure machine csatlakoztatva. A fájl vagy a szöveg $FilePathForRegistrationToken másolhatja.

### <a name="connect-to-azure-and-register"></a>Csatlakozás Azure és regisztrálása

A számítógépen, amely az internetre csatlakozó, a lépésekkel azonos bejelentkezési és a RegisterWithAzure.psm1 modul importálása a megfelelő Azure Powershell környezethez. Ezután hívja a Register-AzsEnvironment, és adja meg az Azure-ral regisztrálása regisztrációs jogkivonatot:

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```

A Get-tartalom parancsmag segítségével szükség esetén a regisztrációs jogkivonatot tartalmazó fájlra mutat:

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```

  > [!NOTE]  
  > Mentse a regisztrációs erőforrás nevének és a regisztrációs jogkivonat későbbi felhasználás céljából.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Az aktiválási kulcs lekérése az Azure regisztrációs erőforrás

Ezt követően kell az aktiválási kulcs lekérése a Register-AzsEnvironment során az Azure-ban létrehozott regisztrációs erőforrás.

Ahhoz, hogy a termékaktiválási kulcsot, futtassa a következő PowerShell-parancsokat:  

  ```Powershell
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!TIP]  
  > A megadott fájlt mentette az aktiválási kulcsot: *$KeyOutputFilePath*. Módosíthatja a fájl elérési útja vagy fájlnév közül.

### <a name="create-an-activation-resource-in-azure-stack"></a>Az Azure-készletben aktiválási erőforrás létrehozása

Vissza az Azure-verem környezetbe a fájl vagy a szöveg a Get-AzsActivationKey alapján létrehozott aktiválási kulcs. Ezután létrehozhat egy aktiválási erőforrás Azure verem aktiválási kulccsal. A következő PowerShell-parancsokat egy aktiválási-erőforrás létrehozása:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

A Get-tartalom parancsmag segítségével szükség esetén a regisztrációs jogkivonatot tartalmazó fájlra mutat:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Azure verem regisztrációjának ellenőrzése

Ezen lépések segítségével győződjön meg arról, hogy Azure verem sikeresen regisztrálva lett az Azure-ral.

1. Jelentkezzen be a Azure verem [adminisztrációs portálhoz](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;régió >. &lt;teljesen minősített tartományneve >*.
2. Válassza ki **további szolgáltatások** > **piactér felügyeleti** > **adja hozzá az Azure-ból**.

Ha az Azure (például a WordPress) rendelkezésre álló elemek listája látható, az aktiválás sikeres volt. Azonban a kapcsolat nélküli környezetekben nem látják az Azure piactéren elemek a verem Azure piactéren.

> [!NOTE]
> Regisztráció befejezése után az aktív figyelmeztetés nincs regisztrálása nem fog megjelenni.

## <a name="renew-or-change-registration"></a>Újítsa meg, vagy módosítsa a regisztráció

### <a name="renew-or-change-registration-in-connected-environments"></a>Újítsa meg, vagy módosítsa a csatlakoztatott környezetekben való regisztráció

Kell frissíteni vagy újítsa meg a regisztrációt, a következő esetekben:

- Miután kapacitás-alapú éves előfizetés megújítása.
- Ha módosítja a számlázási modellt.
- Ha méretezni a módosításokat (csomópontok hozzáadása) a kapacitás alapján történő számlázáshoz.

#### <a name="change-the-subscription-you-use"></a>Az előfizetés használhatja módosítása

Ha szeretné módosítani az előfizetési használ, előbb futtatnia kell a **Remove-AzsRegistration** parancsmagot, majd ellenőrizze a jelentkezett be a megfelelő Azure PowerShell környezethez, és végül futtassa **Set-AzsRegistration**  sem módosította a paramétereket:

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Módosítsa a számlázási modellt vagy az együttműködési szolgáltatások

Ha azt szeretné, a számlázási modellt vagy az együttműködési szolgáltatások, a telepítés módosítása, hívása regisztrációs funkció az új értékeinek beállításához. Nem kell először távolítsa el az aktuális regisztráció:

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Újítsa meg, vagy módosítsa a kapcsolat nélküli környezetekben való regisztráció

Kell frissíteni vagy újítsa meg a regisztrációt, a következő esetekben:

- Miután kapacitás-alapú éves előfizetés megújítása.
- Ha módosítja a számlázási modellt.
- Ha méretezni a módosításokat (csomópontok hozzáadása) a kapacitás alapján történő számlázáshoz.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Távolítsa el az aktiválási erőforrás Azure verem

Először az aktiválási erőforrás eltávolítása Azure-vermet, és a regisztrációs erőforrást az Azure-ban.  

Az aktiválás erőforrás Azure verem eltávolításához futtassa a következő PowerShell-parancsokat a verem Azure környezetben:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Ezután távolítsa el a regisztrációs erőforrást az Azure-ban, hogy, hogy az egy csatlakoztatott Azure számítógép, jelentkezzen be a megfelelő Azure PowerShell-környezetben, és futtassa a megfelelő PowerShell-parancsokat, az alább ismertetett.

A regisztrációs jogkivonatot, az erőforrás létrehozásához használt használhatja:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Vagy a regisztráció nevet:

  ```Powershell
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Regisztrálja újra a leválasztott lépéseket követve

Most már megszüntette teljesen leválasztott forgatókönyv esetében, és ismételje a regisztrációhoz Azure verem környezet leválasztott esetén.

## <a name="next-steps"></a>További lépések

[Töltse le a Piactéri elemek az Azure-ból](azure-stack-download-azure-marketplace-item.md)
