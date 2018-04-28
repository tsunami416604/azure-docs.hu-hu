---
title: Ehhez a proaktív hálózatfigyelési riasztások és az Azure Functions csomagrögzítéssel használható |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan hozzon létre egy riasztási kiváltott csomagrögzítéssel Azure hálózati figyelőt
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 4c96ca70b9b6a82dcccec443ac0b1e06f96a2396
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Proaktív hálózatfigyelési riasztások és az Azure Functions csomagrögzítéssel használata

Hálózati figyelő csomagrögzítéssel hoz létre a rögzítési munkamenet forgalom mindkét virtuális gépek nyomon követéséhez. A rögzítési fájl lehet egy szűrő, amely csak a figyelni kívánt forgalom nyomon követéséhez van definiálva. Ezeket az adatokat a tárolási blob vagy helyileg, a vendég gépen majd tárolja.

Ez a funkció más automatizálási esetekben, például az Azure Functions távolról is indítható. Csomagrögzítéssel lehetővé teszi a funkció futtatásához a meghatározott hálózati rendellenességeket alapján proaktív rögzíti. Egyéb felhasználásra tartalmazzák a hálózati statisztikákat, első hálózati behatolások, a hibakeresési ügyfél-kiszolgáló közötti kommunikációt, és további információkat gyűjt.

Az Azure rendszerbe telepítendő erőforrásokat 24/7 fut. Alkalmazottak nem aktív figyelését az összes erőforrás 24/7 állapotát. Például mi történik, ha hiba fordul elő, hajnali 2 Órakor?

Használatával hálózati figyelőt, riasztások, és az Azure-ökoszisztémán belül funkciókat proaktív reagálhat az adatok és eszközök a hálózati problémák megoldására.

![Forgatókönyv][scenario]

## <a name="prerequisites"></a>Előfeltételek

* A legújabb [Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Hálózati figyelőt meglévő példányát. Ha még nem rendelkezik egy, [hálózati figyelőt példányt létrehozni](network-watcher-create.md).
* Egy meglévő virtuális gép hálózati figyelőt az ugyanabban a régióban a [Windows bővítmény](../virtual-machines/windows/extensions-nwa.md) vagy [Linux virtuálisgép-bővítmény](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Forgatókönyv

Ebben a példában a szokásosnál több TCP-szegmens küld, és szeretne riasztást kapni. TCP-szegmens az itt példaként szolgálnak, de a figyelmeztetési állapotot használhatja.

Amikor a rendszer megkérdezi, szeretné tudni, miért kommunikációs növekedett csomagszintű adatok fogadására. Ezután térjen vissza a virtuális gép rendszeres kommunikációt lépéseket is igénybe vehet.

Ez a forgatókönyv feltételezi, hogy rendelkezik-e hálózati figyelőt, és egy erőforráscsoport, érvényes virtuális gépet a meglévő példányát.

Az alábbi lista a részben áttekintjük a munkafolyamatot, amely akkor történik meg:

1. Figyelmeztetés jelenik meg a virtuális Gépen.
1. A riasztás meghívja az Azure-függvény olyan webhook keresztül.
1. Az Azure-függvény dolgozza fel a riasztást, és a hálózati figyelőt csomag rögzítési munkamenet indítása.
1. A csomagrögzítéssel a virtuális gép fut, és összegyűjti a forgalmat.
1. A csomag rögzítésfájl áttekintésre és diagnosztikai tárfiók van feltöltve.

Ez a folyamat automatizálására azt hozzon létre és riasztást csatlakozni a virtuális Gépen indul el, ha az esemény akkor következik be. Azt is hozzon létre egy függvényt hálózati figyelőt belülre hívni.

Ebben a forgatókönyvben a következőket teszi:

* Egy Azure függvény, amely elindítja a csomagrögzítéssel hoz létre.
* Egy riasztási szabályt hoz létre a virtuális gépen, és konfigurálja a riasztási szabály Azure függvény.

## <a name="create-an-azure-function"></a>Egy Azure-függvény létrehozása

Az első lépés, ha a riasztás feldolgozni, és hozzon létre egy csomagrögzítéssel egy Azure függvényben.

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **hozzon létre egy erőforrást** > **számítási** > **függvény App**.

    ![A függvény-alkalmazás létrehozása][1-1]

2. Az a **függvény App** panelen adja meg a következő értékeket, és válassza ki **OK** az alkalmazás létrehozásához:

    |**Beállítás** | **Érték** | **Részletek** |
    |---|---|---|
    |**Alkalmazás neve**|PacketCaptureExample|A függvény alkalmazás nevét.|
    |**Előfizetés**|[Az előfizetés] Az előfizetés, amelyre a függvény alkalmazás létrehozásához.||
    |**Erőforráscsoport**|PacketCaptureRG|A függvény alkalmazást tartalmazó erőforráscsoportot.|
    |**Szolgáltatási csomag**|Használatalapú csomag| Milyen típusú tervezze meg a függvény alkalmazás használ. Ezek fogyasztás vagy az Azure App Service-csomag. |
    |**Hely**|USA középső régiója| A régió, ahol a függvény alkalmazás létrehozásához.|
    |**Tárfiók**|{autogenerated}| A storage-fiók, amelyet az Azure Functions általános célú tárfiókok esetében.|

3. Az a **PacketCaptureExample függvény alkalmazások** panelen válassza **funkciók** > **egyéni függvény**  >  **+**.

4. Válassza ki **HttpTrigger-Powershell**, majd adja meg a fennmaradó adatokat. Végezetül, a függvény létrehozásához válassza **létrehozása**.

    |**Beállítás** | **Érték** | **Részletek** |
    |---|---|---|
    |**A forgatókönyv**|Kísérleti|Forgatókönyv típusa|
    |**A függvény neve**|AlertPacketCapturePowerShell|A függvény neve|
    |**Jogosultsági szint**|Függvény|A függvény jogosultsági szint|

![Funkciók – példa][functions1]

> [!NOTE]
> A PowerShell sablon kísérleti, és nem rendelkezik teljes körű támogatása.

Testreszabások szükséges ehhez a példához, és a következő lépéseket ismerteti.

### <a name="add-modules"></a>Modulok hozzáadása

Hálózati figyelő PowerShell-parancsmagok használatához töltse fel a legújabb PowerShell-modul a függvény alkalmazást.

1. A legújabb Azure PowerShell-modulok telepítése a helyi gépén futtassa a következő PowerShell-parancsot:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Ez a példa lehetővé teszi az Azure PowerShell-modulok helyi elérési útja. Ezek a mappák egy későbbi lépésben használt. Az ebben a forgatókönyvben használt modulokra:

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![PowerShell mappák][functions5]

1. Válassza ki **Alkalmazásbeállítások működéséhez** > **nyissa meg az alkalmazás-szerkesztőt**.

    ![Függvényalkalmazás beállításai][functions2]

1. Kattintson a jobb gombbal a **AlertPacketCapturePowershell** mappát, majd hozzon létre egy nevű és **azuremodules**. 

4. Hozzon létre egy almappát minden modul, amelyekre szüksége van.

    ![Mappa- és almappákkal együtt][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Kattintson a jobb gombbal a **AzureRM.Network** almappát, és válassza **fájl feltöltése**. 

6. Nyissa meg az Azure-modulok. A helyi **AzureRM.Network** mappát, válassza ki az összes fájl a mappában. Ezután kattintson az **OK** gombra. 

7. Ismételje meg ezeket a lépéseket **AzureRM.Profile** és **AzureRM.Resources**.

    ![Fájlok feltöltése][functions6]

1. Miután elkészült, minden egyes a PowerShell modul fájlok a helyi számítógépről kell rendelkeznie.

    ![PowerShell-fájlok][functions7]

### <a name="authentication"></a>Hitelesítés

A PowerShell-parancsmagok használatához hitelesítenie kell. A függvény alkalmazás állítsa be a hitelesítést. A hitelesítést, konfigurálja a környezeti változók, és egy titkosított kulcs fájlt feltölteni a függvény alkalmazást.

> [!NOTE]
> Ebben a forgatókönyvben az Azure Functions hitelesítési megvalósításának csak egy példát biztosít. Ehhez más módja van.

#### <a name="encrypted-credentials"></a>Titkosított hitelesítő adatokat

A következő PowerShell-parancsfájl létrehoz egy kulcs nevű **PassEncryptKey.key**. A megadott jelszó egy titkosított verzióját is tartalmazza. Ez a jelszó nem ugyanazt a jelszót, amely a hitelesítéshez használt Azure Active Directory-alkalmazás van definiálva.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

A App Service-szerkesztőben a függvény alkalmazás, hozzon létre egy nevű **kulcsok** alatt **AlertPacketCapturePowerShell**. Majd töltse fel a **PassEncryptKey.key** az előző PowerShell minta létrehozott fájlt.

![Funkciók kulcs][functions8]

### <a name="retrieve-values-for-environment-variables"></a>A környezeti változók értékeit beolvasása

A végső vonatkozó követelmény akkor állíthatja be a környezeti változók értékeit hitelesítési eléréséhez szükséges. Az alábbi lista tartalmazza a környezeti változókat, amelyek jönnek létre:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Az ügyfél-azonosító, az alkalmazás Azonosítóját a kérelmet az Azure Active Directoryban.

1. Ha még nem rendelkezik az alkalmazás, futtassa az alábbi példát követve hozzon létre egy alkalmazást.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Az alkalmazás létrehozásakor használt jelszót kell ugyanazt a jelszót, amely a korábban létrehozott a fájl mentése során.

1. Válassza ki az Azure-portálon **előfizetések**. Válassza ki az előfizetést, és válassza a **hozzáférés-vezérlés (IAM)**.

    ![IAM funkciók][functions9]

1. Válassza ki a fiókot, és válassza a **tulajdonságok**. Másolja át az azonosítót.

    ![Funkciók Alkalmazásazonosító][functions10]

#### <a name="azuretenant"></a>AzureTenant

A következő PowerShell-példa futtatásával beszerezni a bérlő azonosítója:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

A AzureCredPassword környezeti változó értéke a értéket, hogy fut a következő PowerShell-példa. Ez a példa értéke megegyezik az előző megjelenített **titkosított hitelesítő adatok** szakasz. A szükséges érték a kimenetét a `$Encryptedpassword` változó.  Ez az a szolgáltatás egyszerű jelszót, amely korábban a PowerShell parancsfájl használatával titkosított.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>A környezeti változók tárolásához

1. Nyissa meg a függvény alkalmazásba. Válassza ki **Alkalmazásbeállítások működéséhez** > **Alkalmazásbeállítások konfigurálása**.

    ![Alkalmazásbeállítások konfigurálása][functions11]

1. A környezeti változókhoz és értékeikhez vegye fel az alkalmazás beállításaiban, és válassza ki **mentése**.

    ![Alkalmazásbeállítások][functions12]

### <a name="add-powershell-to-the-function"></a>A függvény PowerShell hozzáadása

Az Azure függvényen belül a hálózati figyelőt a hívásokat idő már. Ez a függvény végrehajtása a követelményektől függően eltérőek lehetnek. Azonban az általános folyamat a kód a következőképpen történik:

1. Folyamat-bemeneti paraméterekhez.
2. Meglévő csomag lekérdezési korlátok ellenőrzése és a névütközések feloldásához rögzíti.
3. Hozzon létre egy csomagrögzítéssel megfelelő paraméterekkel.
4. A lekérdezési csomag rögzítése rendszeres időközönként, amíg nem fejeződik be.
5. Értesítse a felhasználót, hogy a csomag rögzítési munkamenet befejeződik.

A következő példa a függvényben használható PowerShell-kódjába. Le kell cserélni a igénylő érték **subscriptionId**, **resourceGroupName**, és **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzureRmAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
                $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

                #Get existing packetCaptures
                $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>A függvény URL-cím beolvasása 
1. A függvény létrehozása után konfigurálja a riasztás hívni az URL-címet, a függvény van társítva. Ahhoz, hogy ezt az értéket, a függvény app másolja a függvény URL-címet.

    ![A függvény URL-cím keresése][functions13]

2. Másolja a függvény app függvény URL-CÍMÉT.

    ![A függvény URL-cím másolása][2]

Ha a webhook POST kérelem hasznos az egyéni tulajdonságok van szüksége, tekintse meg a [olyan webhook konfigurálása Azure metrika riasztást](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Olyan riasztást konfigurálhat a virtuális gép

Riasztások beállítható úgy, hogy egyéni felhasználók értesítése, ha egy adott metrika áthalad küszöb alá van rendelve. Ebben a példában a riasztás a TCP-szegmens küldött, de az is lehet figyelmeztetés, ha sok más metrikákkal. Ebben a példában a riasztást egy webhook függvény hívása van konfigurálva.

### <a name="create-the-alert-rule"></a>A riasztási szabály létrehozása

Ugrás a meglévő virtuális gépből, és adja hozzá a riasztási szabályt. Riasztások konfigurálása vonatkozó részletes dokumentációt a helyen találhatók [hozzon létre riasztásokat Azure figyelése az Azure-szolgáltatások - Azure-portálon](../monitoring-and-diagnostics/insights-alerts-portal.md). Adja meg a következő értékeket a **riasztási szabály** panelt, és válassza **OK**.

  |**Beállítás** | **Érték** | **Részletek** |
  |---|---|---|
  |**Name (Név)**|TCP_Segments_Sent_Exceeded|A riasztási szabály neve.|
  |**Leírás**|TCP-szegmens küldött meghaladja a küszöbértéket|A riasztási szabály leírása.||
  |**Metrika**|Küldött TCP-szegmens| A metrika a riasztás aktiválásához használatára. |
  |**Az állapot**|Nagyobb mint| Az állapotot, amelyet használni a mérték kiértékelése során.|
  |**Küszöbérték**|100| A metrika a riasztást kiváltó értéke. Ez az érték a környezet érvényes értékre kell állítani.|
  |**Időtartam**|Az elmúlt öt percben| Meghatározza, hogy az időszak, amelyben a küszöbértéket a metrika a(z) keres.|
  |**Webhook**|[webhook URL-CÍMÉT függvény alkalmazásból]| A webhook URL-CÍMÉT az előző lépésben létrehozott függvény alkalmazásból.|

> [!NOTE]
> A TCP-szegmens metrika alapértelmezés szerint nincs engedélyezve. További tudnivalókért látogasson el további metrikák engedélyezésével kapcsolatos [figyelés engedélyezésekor és diagnosztikai](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Az eredmények áttekintése

A riasztási eseményindítók feltételeit, miután a csomagrögzítéssel jön létre. Ugrás a hálózati figyelőt, és válassza **csomagrögzítéssel**. Ezen a lapon kiválaszthatja a csomag-rögzítési fájl a csomagrögzítéssel letöltésére mutató hivatkozás.

![A csomagrögzítéssel megtekintése][functions14]

Ha a rögzítési fájlt helyileg van tárolva, a virtuális géphez a bejelentkezéssel kérheti le.

Az Azure storage-fiókok fájlok letöltésére vonatkozó utasításokért lásd: [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Használhat egy másik eszköz [Tártallózó](http://storageexplorer.com/).

A rögzítési letöltése után tekintheti által olvasható eszközzel egy **.cap** fájlt. Az alábbiakban két eszközökre mutató hivatkozásokat:

- [Microsoft Message Analyzert](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>További lépések

Útmutató a csomag rögzítésekre megtekintéséhez látogasson el [csomag eseményrögzítés elemzésének rendelkező Wireshark](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png
