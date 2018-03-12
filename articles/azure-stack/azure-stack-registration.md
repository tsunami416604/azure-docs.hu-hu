---
title: "Az Azure regisztrációs Azure verem integrált rendszerek |} Microsoft Docs"
description: "Több csomópontos rendszerekhez. Azure-verem Azure csatlakoztatott Azure regisztrációs folyamatát mutatjuk be."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 27bd44f936e19890526c0834e14084647dcec086
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral
Azure verem regisztrálhatja az Azure-ral Piactéri elemek letölteni az Azure-ból, és megkezdheti a Microsoft commerce adatok beállításához. Miután regisztrálta Azure verem, használati Azure kereskedelmi bejelentések. Megtekintheti az előfizetésben a regisztrációhoz használt.

> [!IMPORTANT]
> Regisztrációs megadása kötelező, ha úgy dönt, hogy a fizetés,-akkor-használható számlázási modellt. Ellenkező esetben Ön lesz az Azure Alkalmazásveremben üzembe licencfeltételeit megsértését megfelelően használati ellenkező esetben nem fog szerepelni.

## <a name="before-you-register-azure-stack-with-azure"></a>Mielőtt regisztrálná Azure verem az Azure-ral
Mielőtt regisztrálná az Azure-vermet az Azure-ral, rendelkeznie kell:

- Az előfizetés-azonosító az Azure-előfizetéssel. Ahhoz, hogy az azonosító, jelentkezzen be az Azure-ba, és kattintson **további szolgáltatások** > **előfizetések**, kattintson az előfizetést szeretné használni, és a **Essentials** megtalálhatja a Előfizetés-azonosító. 

  > [!NOTE]
  > Kínai, német és US government felhőalapú előfizetések jelenleg nem támogatottak. 

- A felhasználónév és jelszó egy olyan fiók, amely az előfizetés tulajdonosa (a felügyelt Szolgáltatásfiók/2FA fiókok támogatottak)
- *Nem kötelező Azure verem 1712 frissítés verziójától (180106.1)*: az Azure ad-val az Azure-előfizetés. Ez a könyvtár az Azure-ban fölé a profilképet, az Azure-portál jobb felső sarkában található. 
- Az Azure-verem erőforrás-szolgáltató regisztrálva (lásd a regisztrálni Azure verem erőforrás-szolgáltató szakaszát alatt)

Ha nem rendelkezik Azure-előfizetéssel, amely megfelel ezeknek a követelményeknek, akkor [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/free/?b=17.06). Azure verem regisztrálása költséget nem áll, az Azure-előfizetése.

### <a name="bkmk_powershell"></a>A verem az Azure PowerShell telepítése
A legújabb Azure verem PowerShell segítségével a rendszer regisztrálja az Azure-ral szeretne.

Ha még nincs telepítve, [verem Azure PowerShell telepítése](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Töltse le az Azure-verem eszközök
Az Azure-verem eszközök GitHub-tárházban tartalmazza, amelyek támogatják az Azure-verem funkció; PowerShell-modulok beleértve a regisztrációs funkciót. A regisztráció során kell importálni és használni a RegisterWithAzure.psm1 PowerShell modul folyamat található, a Azure verem eszközök, az Azure-verem példányának regisztrálni Azure tárházban. 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>Regisztrálja az Azure verem csatlakoztatott környezetekben
Csatlakoztatott környezetekben az internetes és az Azure férhetnek hozzá. Ilyen környezetben kell az Azure-verem erőforrás-szolgáltató regisztrálása az Azure-ral, és adja meg a számlázási modellt.

### <a name="register-the-azure-stack-resource-provider"></a>Az Azure-verem erőforrás-szolgáltató regisztrálása
Az Azure-verem erőforrás-szolgáltató regisztrálása az Azure-ral, indítsa el a Powershell ISE rendszergazdaként, és a következő PowerShell-parancsokkal. Ezek a parancsok lesznek:
- Jelentkezzen be egy használt, állítsa be az Azure-előfizetés tulajdonosának kérik a `EnvironmentName` paramétert **AzureCloud**.
- Az Azure erőforrás-szolgáltató regisztrálása **Microsoft.AzureStack**.

A PowerShell futtatásához:

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
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

## <a name="remove-a-registered-resource"></a>Távolítsa el a regisztrált erőforrás
Ha el szeretné távolítani a regisztráció, akkor kell használnia **UnRegister-AzsEnvironment** parancsmag és a regisztrációs erőforrás neve vagy a regisztráció pass meg használt token **Register-AzsEnvironment**.

Egy erőforrás nevét-regisztrációk eltávolítása:

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
A regisztrációs tokent regisztráció eltávolítása:

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```

## <a name="next-steps"></a>További lépések

[Külső figyelési integráció](azure-stack-integrate-monitor.md)