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
ms.date: 03/21/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: e51a15b197e875c35997cfe2ac96d673c01a80f9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral
Regisztrálás [Azure verem](azure-stack-poc.md) az Azure-ral lehetővé teszi a Piactéri elemek letölteni az Azure-ból, és megkezdheti a Microsoft commerce adatok beállításához. Miután regisztrálta Azure-vermet, használati bejelentések Azure kereskedelmi, és megtekintheti az előfizetésben a regisztrációhoz használt. 

> [!IMPORTANT]
> Regisztrációs megadása kötelező, ha úgy dönt, hogy a fizetés,-akkor-használható számlázási modellt. Ellenkező esetben Ön lesz az Azure Alkalmazásveremben üzembe licencfeltételeit megsértését megfelelően használati ellenkező esetben nem fog szerepelni.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt regisztrálná az Azure-vermet az Azure-ral, rendelkeznie kell:

- Az előfizetés-azonosító az Azure-előfizetéssel. Ahhoz, hogy az azonosító, jelentkezzen be az Azure-ba, és kattintson **további szolgáltatások** > **előfizetések**, kattintson az előfizetést szeretné használni, és a **Essentials** megtalálhatja a Előfizetés-azonosító. 

  > [!NOTE]
  > Kínai, a Németországi és az Amerikai Egyesült államokbeli kormányzati felhőalapú előfizetések jelenleg nem támogatottak. 

- A felhasználónév és jelszó egy olyan fiók, amely az előfizetés tulajdonosa (a felügyelt Szolgáltatásfiók/2FA fiókok támogatottak)
- Az Azure-verem erőforrás-szolgáltató regisztrálva (lásd a regisztrálni Azure verem erőforrás-szolgáltató szakaszát alatt).

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, akkor [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/free/?b=17.06). Azure verem regisztrálása költséget nem áll, az Azure-előfizetése.

### <a name="bkmk_powershell"></a>A verem az Azure PowerShell telepítése
Szeretné használni a legújabb Azure verem PowerShell Azure regisztrálására.

Ha még nincs telepítve, [verem Azure PowerShell telepítése](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Töltse le az Azure-verem eszközök
Az Azure-verem eszközök GitHub-tárházban tartalmazza, amelyek támogatják az Azure-verem funkció; PowerShell-modulok beleértve a regisztrációs funkciót. A regisztráció során kell importálni és használni a RegisterWithAzure.psm1 PowerShell modul folyamat található, a Azure verem eszközök, az Azure-verem példányának regisztrálni Azure tárházban. 

Győződjön meg arról, a legújabb verzióját használja, törölnie kell az Azure-verem eszközök minden meglévő verzióját, és [a legújabb verzió letöltéséhez a Githubról](azure-stack-powershell-download.md) mielőtt regisztrálná az Azure-ral.

## <a name="register-azure-stack-in-connected-environments"></a>Regisztrálja az Azure verem csatlakoztatott környezetekben
Csatlakoztatott környezetekben az internetes és az Azure férhetnek hozzá. Ilyen környezetben kell az Azure-verem erőforrás-szolgáltató regisztrálása az Azure-ral, és adja meg a számlázási modellt.

> [!NOTE]
> Ezeket a lépéseket egy számítógépről, amely hozzáfér a kiemelt végpontot kell futtatni. 

### <a name="register-the-azure-stack-resource-provider"></a>Az Azure-verem erőforrás-szolgáltató regisztrálása
Az Azure-verem erőforrás-szolgáltató regisztrálása az Azure-ral, indítsa el a Powershell ISE rendszergazdaként, és a következő PowerShell-parancsokkal. Ezek a parancsok lesznek:
- Jelentkezzen be egy használt, állítsa be az Azure-előfizetés tulajdonosának kérik a `EnvironmentName` paramétert **AzureCloud**.
- Az Azure erőforrás-szolgáltató regisztrálása **Microsoft.AzureStack**.

1. Adja hozzá az Azure-fiókot regisztrálni Azure verem használó. A fiók hozzáadásához futtassa a **Add-AzureRmAccount** parancsmag. A Azure globális rendszergazdai fiók hitelesítő adatainak megadását kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2-factor authentication használatával.

   ```Powershell
      Add-AzureRmAccount -EnvironmentName AzureCloud
   ```

2. Ha több előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt válassza:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. A következő parancsot az Azure-előfizetéshez az Azure-verem erőforrás-szolgáltató regisztrálása:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>A fizetési,-akkor-használható számlázási modellt használó Azure Azure verem regisztrálása
Az alábbi lépéseket a fizetési,-akkor-használható számlázási modellt használó Azure regisztrálni Azure verem használható.

Indítsa el a PowerShell ISE rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack főkiszolgálós eszközök** létrehozta könyvtárat, [letöltötte az Azure-verem eszközök](#bkmk_tools). Importálás a **RegisterWithAzure.psm1** modul PowerShell használatával: 

A PowerShell futtatásához:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
Ezt követően a ugyanazon PowerShell-munkamenetben futtassa a **Set-AzsRegistration** parancsmag. Ha a hitelesítő adatok megadását kéri, adja meg az Azure-előfizetés tulajdonosának.  

A PowerShell futtatásához:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|Paraméter|Leírás|
|-----|-----|
|CloudAdminCredential|PowerShell-objektum, amely az Azure-előfizetés tulajdonosának (felhasználónév és jelszó) hitelesítő adatokat tartalmazza.|
|PrivilegedEndpoint|Egy előre konfigurált távoli PowerShell-konzolt, és szolgáltatásokat, például a naplógyűjtést és egyéb utáni feladatok végrehajtását. További tudnivalókért tekintse meg a [használatával a privilegizált végpont](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint) cikk.|
|BillingModel|A számlázási modellt, amely az előfizetés használ. Engedélyezett értékek a paraméter: kapacitás, PayAsYouUse és fejlesztésére.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>A kapacitás számlázási modellt használó Azure Azure verem regisztrálása
A fizetési,-akkor-használható számlázási modell használatával a regisztrációhoz használt ugyanezeket az utasításokat, de vegye fel a Szerződésszám kapacitás vásárlásának módjától, és módosítsa a `BillingModel` paramétert **kapacitás**. Minden más paraméter nem változnak.

A PowerShell futtatásához:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Kapcsolat nélküli környezetben Azure verem regisztrálása 
*Az itt olvasható információk az Azure verem 1712 frissített verzióra (180106.1) kezdődő vonatkozik, és korábbi verzióiban nem támogatott.*

Ha regisztrál Azure verem leválasztott környezetben (az internet-hozzáférés nélküli), szeretné-e regisztráció-token beszerzése a Azure verem környezetből, majd a jogkivonatot egy számítógépen, és csatlakozni tud-e Azure [PowerShell Azure-verem telepítve](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Regisztráció az Azure-verem környezetből token beszerzése
  1. Ahhoz, hogy a regisztrációs jogkivonatot, futtassa a következő PowerShell-parancsokat:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > A megadott fájlt mentette a regisztrációs jogkivonat: *$env:SystemDrive\RegistrationToken.txt*.

  2. Mentse a regisztrációs jogkivonat használni az Azure machine csatlakoztatva.


### <a name="connect-to-azure-and-register"></a>Csatlakozás Azure és regisztrálása
Indítsa el a PowerShell ISE rendszergazdaként, és keresse meg a **regisztrációs** mappájában a **AzureStack főkiszolgálós eszközök** létrehozta könyvtárat, [letöltötte az Azure-verem eszközök](#bkmk_tools). Importálás a **RegisterWithAzure.psm1** modul: 

A PowerShell futtatásához:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
A következő ugyanabban a PowerShell munkamenetben, adja meg Azure regisztrálása regisztrációs jogkivonatot:

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
> Mentse a regisztrációs erőforrás neve vagy a regisztrációs jogkivonat későbbi felhasználás céljából.

## <a name="verify-azure-stack-registration"></a>Azure verem regisztrációjának ellenőrzése
Ezen lépések segítségével győződjön meg arról, hogy Azure verem sikeresen regisztrálta az Azure-ral.
1. Jelentkezzen be a Azure verem [adminisztrációs portálhoz](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;régió >. &lt;teljesen minősített tartományneve >*.
2. Kattintson a **további szolgáltatások** > **piactér felügyeleti** > **adja hozzá az Azure-ból**.

Ha az Azure (például a WordPress) rendelkezésre álló elemek listája látható, az aktiválás sikeres volt.

> [!NOTE]
> Regisztráció befejezése után az aktív figyelmeztetés nincs regisztrálása nem fog megjelenni.

## <a name="renew-or-change-registration"></a>Újítsa meg, vagy módosítsa a regisztráció
Kell frissíteni vagy újítsa meg a regisztrációt, a következő esetekben:
- Miután kapacitás-alapú éves előfizetés megújítása.
- Ha módosítja a számlázási modellt.
- Ha méretezni a módosításokat (csomópontok hozzáadása) a kapacitás alapján történő számlázáshoz.

### <a name="change-the-subscription-you-use"></a>Az előfizetés használhatja módosítása
Ha szeretné módosítani az előfizetési használ, előbb futtatnia kell a **Remove-AzsRegistration** parancsmagot, majd ellenőrizze a jelentkezett be a megfelelő Azure PowerShell környezethez, és végül futtassa **Set-AzsRegistration**  sem módosította a paramétereket:

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>Módosítsa a számlázási modellt vagy az együttműködési szolgáltatások
Ha azt szeretné, a számlázási modellt vagy az együttműködési szolgáltatások, a telepítés módosítása, hívása regisztrációs funkció az új értékeinek beállításához. Nem kell először távolítsa el az aktuális regisztráció: 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```
## <a name="next-steps"></a>További lépések

[Töltse le a Piactéri elemek az Azure-ból](azure-stack-download-azure-marketplace-item.md)