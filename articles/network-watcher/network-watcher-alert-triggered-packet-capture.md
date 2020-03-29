---
title: A csomagrögzítés használata proaktív hálózati figyelésriasztásokhoz - Azure Functions
titleSuffix: Azure Network Watcher
description: Ez a cikk azt ismerteti, hogyan hozhat létre riasztást aktivált csomagrögzítés az Azure Network Watcher rel
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: ea506e137d71fc3124a4f93f1e97750a08dd4284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842937"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Csomagrögzítés használata a riasztások és az Azure Functions proaktív hálózati figyeléséhez

A Network Watcher csomagrögzítésrögzítési munkameneteket hoz létre a virtuális gépeken és a virtuális gépeken való forgalom nyomon követésére. A rögzítési fájl rendelkezhet egy szűrővel, amely úgy van definiálva, hogy csak a figyelni kívánt forgalmat kövesse nyomon. Ezeket az adatokat ezután egy tárolási blobban vagy helyileg a vendéggépen tárolja.

Ez a képesség távolról is elindítható más automatizálási forgatókönyvekből, például az Azure Functionsből. A csomagrögzítés lehetővé teszi a proaktív rögzítések futtatását meghatározott hálózati anomáliák alapján. Más felhasználások közé tartozik a hálózati statisztikák gyűjtése, a hálózati behatolásokkal kapcsolatos információk beszerzése, az ügyfél-kiszolgáló kommunikáció hibakeresése stb.

Az Azure-ban üzembe helyezett erőforrások a hét minden nap 24 órájában futnak. Ön és munkatársai nem tudják aktívan nyomon követni az összes erőforrás állapotát a hét minden évórájában. Például mi történik, ha hajnali 2 órakor probléma lép fel?

A Network Watcher, a riasztások és az Azure-ökoszisztémán belüli függvények használatával proaktív módon válaszolhat az adatokkal és az eszközökkel a hálózat problémáinak megoldásához.

![Forgatókönyv][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* Az Azure [PowerShell](/powershell/azure/install-Az-ps)legújabb verziója.
* A Network Watcher egy meglévő példánya. Ha még nem rendelkezik ilyen, [hozzon létre egy példányt a Network Watcher](network-watcher-create.md).
* Egy meglévő virtuális gép ugyanabban a régióban, mint a Network Watcher a [Windows-bővítmény](../virtual-machines/windows/extensions-nwa.md) vagy a [Linux virtuálisgép-bővítmény](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Forgatókönyv

Ebben a példában a virtuális gép a szokásosnál több TCP-szegmenst küld, és figyelmeztetést szeretne kapni. A TCP-szegmensek példaként szolgálnak itt, de bármilyen riasztási feltételt használhat.

Amikor értesítést kap, csomagszintű adatokat szeretne kapni, hogy megértse, miért nőtt a kommunikáció. Ezután lépéseket tehet a virtuális gép rendszeres kommunikációba való visszatéréséhez.

Ebben a forgatókönyvben azt feltételezi, hogy rendelkezik egy meglévő példányát a Network Watcher és egy erőforráscsoport egy érvényes virtuális gép.

Az alábbi lista áttekintést nyújt a munkafolyamatról:

1. Riasztás aktiválódik a virtuális gép.
1. A riasztás egy webhookon keresztül hívja meg az Azure-függvényt.
1. Az Azure-függvény feldolgozza a riasztást, és elindítja a Network Watcher csomagrögzítési munkamenetét.
1. A csomagrögzítés a virtuális számítógépen fut, és forgalmat gyűjt.
1. A csomagrögzítő fájl feltöltésre kerül egy tárfiókba véleményezésre és diagnózisra.

A folyamat automatizálásához hozzon létre és csatlakoztasson egy riasztást a virtuális gép, amely az incidens bekövetkezésekor aktiválódik. Mi is létrehozhat egy funkciót, hogy hívja be Network Watcher.

Ez a forgatókönyv a következőket teszi:

* Létrehoz egy Azure-függvényt, amely elindítja a csomagrögzítést.
* Riasztási szabályt hoz létre egy virtuális gépen, és konfigurálja a riasztási szabályt az Azure függvény hívására.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Az első lépés egy Azure-függvény létrehozása a riasztás feldolgozásához és egy csomagrögzítés létrehozásához.

1. Az [Azure portalon](https://portal.azure.com)válassza az **Erőforrás** > **számítási** > **függvény alkalmazás**létrehozása lehetőséget.

    ![Függvényalkalmazás létrehozása][1-1]

2. A **Function App** panelen adja meg a következő értékeket, majd az ALKALMAZÁS létrehozásához kattintson az **OK gombra:**

    |**Beállítás** | **Érték** | **Részletek** |
    |---|---|---|
    |**Alkalmazás neve**|PacketCaptureExample|A függvényalkalmazás neve.|
    |**Előfizetés**|[Az előfizetés] Az az előfizetés, amelyhez létre kell hoznia a függvényalkalmazást.||
    |**Erőforráscsoport**|PacketCaptureRG|A függvényalkalmazást tartalmazó erőforráscsoport.|
    |**Szolgáltatási csomag**|Használatalapú csomag| A függvényalkalmazás által használt terv típusa. A lehetőségek a felhasználás vagy az Azure App Service-csomag. |
    |**Helyen**|USA középső régiója| Az a régió, amelyben a függvényalkalmazást létre lehet hozni.|
    |**Tárfiók**|{automatikusan létrehozva}| Az Azure Functions által az általános célú tároláshoz szükséges tárfiók.|

3. A **PacketCaptureExample függvényalkalmazások** panelen válassza a **Functions** > **Custom függvény lehetőséget.** >**+**

4. Válassza a **HttpTrigger-Powershell**lehetőséget, majd adja meg a fennmaradó adatokat. Végül a függvény létrehozásához válassza a **Létrehozás gombot.**

    |**Beállítás** | **Érték** | **Részletek** |
    |---|---|---|
    |**Forgatókönyv**|Kísérleti|Forgatókönyv típusa|
    |**A függvény neve**|AlertPacketCapturePowerShell|A függvény neve|
    |**Authorization level (Engedélyszint)**|Függvény|A függvény engedélyezési szintje|

![Példa függvényekre][functions1]

> [!NOTE]
> A PowerShell-sablon kísérleti, és nem rendelkezik teljes körű támogatással.

Ehhez a példához testreszabásra van szükség, és a következő lépések ismertetik.

### <a name="add-modules"></a>Modulok hozzáadása

A Network Watcher PowerShell-parancsmagok használatához töltse fel a legújabb PowerShell-modult a függvényalkalmazásba.

1. A helyi számítógépen a legújabb Azure PowerShell-modulok telepítve, futtassa a következő PowerShell-parancsot:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Ebben a példában az Azure PowerShell-modulok helyi elérési útját adja meg. Ezek a mappák egy későbbi lépésben használatosak. Az ebben a forgatókönyvben használt modulok a következők:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![PowerShell-mappák][functions5]

1. Válassza a **Függvényalkalmazás beállításai** > **lehetőséget, nyissa meg az App Service Editor t.**

    ![A függvényalkalmazás beállításai][functions2]

1. Kattintson a jobb gombbal az **AlertPacketCapturePowershell** mappára, majd hozzon létre egy **azuremodules nevű mappát.** 

4. Hozzon létre egy almappát minden szükséges modulhoz.

    ![Mappa és almappák][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Kattintson a jobb gombbal az **Az.Network** almappára, és válassza **a Fájlok feltöltése parancsot.** 

6. Nyissa meg az Azure-modulokat. A helyi **Az.Network** mappában jelölje ki a mappában lévő összes fájlt. Ezután kattintson az **OK** gombra. 

7. Ismételje meg ezeket a lépéseket az **Az.Accounts** és **az Az.Resources esetében.**

    ![Fájlok feltöltése][functions6]

1. Miután befejezte, minden mappában rendelkeznie kell a PowerShell-modul fájlokat a helyi számítógépről.

    ![PowerShell-fájlok][functions7]

### <a name="authentication"></a>Hitelesítés

A PowerShell-parancsmagok használatához hitelesítenie kell magát. A hitelesítést a függvényalkalmazásban konfigurálhatja. A hitelesítés konfigurálásához konfigurálnia kell a környezeti változókat, és fel kell töltenie egy titkosított kulcsfájlt a függvényalkalmazásba.

> [!NOTE]
> Ez a forgatókönyv csak egy példa arra, hogyan valósítható meg a hitelesítés az Azure Functions. Van más módja is ennek.

#### <a name="encrypted-credentials"></a>Titkosított hitelesítő adatok

A következő PowerShell-parancsfájl létrehoz egy **PassEncryptKey.key**nevű kulcsfájlt. A megadott jelszó titkosított verzióját is biztosítja. Ez a jelszó ugyanaz a jelszó, amely a hitelesítéshez használt Azure Active Directory-alkalmazáshoz definiált.

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

A függvényalkalmazás App Service-szerkesztőjében hozzon létre egy **kulcsoknak** nevezett mappát az **AlertPacketCapturePowerShell**csoportban. Ezután töltse fel az előző PowerShell-mintában létrehozott **PassEncryptKey.key.key** fájlt.

![Funkciók kulcs][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Környezeti változók értékeinek beolvasása

A végső követelmény a hitelesítési értékek eléréséhez szükséges környezeti változók beállítása. Az alábbi lista a létrehozott környezeti változókat tartalmazza:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Az ügyfélazonosító az Azure Active Directoryban lévő alkalmazások alkalmazásazonosítója.

1. Ha még nem rendelkezik egy alkalmazást használni, futtassa a következő példát egy alkalmazás létrehozásához.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Az alkalmazás létrehozásakor használt jelszónak meg kell egyeznie a kulcsfájl mentésekor korábban létrehozott jelszóval.

1. Az Azure Portalon válassza az **Előfizetések**lehetőséget. Jelölje ki a használni kívánt előfizetést, majd válassza **a Hozzáférés-vezérlés (IAM)** lehetőséget.

    ![Funkciók IAM][functions9]

1. Válassza ki a használni kívánt fiókot, majd válassza a **Tulajdonságok lehetőséget.** Másolja az alkalmazásazonosítót.

    ![Funkciók alkalmazásazonosítója][functions10]

#### <a name="azuretenant"></a>AzureTenant

Szerezze be a bérlői azonosítót a következő PowerShell-minta futtatásával:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Az AzureCredPassword környezeti változó értéke az az érték, amely et a következő PowerShell-minta futtatásából kap. Ez a példa megegyezik az előző titkosított hitelesítő adatok szakaszban **láthatóval.** A szükséges érték a `$Encryptedpassword` változó kimenete.  Ez a szolgáltatás egyszerű jelszó, amely a PowerShell-parancsfájl használatával titkosított.

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

### <a name="store-the-environment-variables"></a>A környezeti változók tárolása

1. Nyissa meg a függvényalkalmazást. Ezután válassza **a Funkcióalkalmazás beállításai** > **Az alkalmazásbeállítások konfigurálása**lehetőséget.

    ![Alkalmazásbeállítások konfigurálása][functions11]

1. Adja hozzá a környezeti változókat és azok értékeit az alkalmazás beállításokhoz, majd válassza a **Mentés gombot.**

    ![Alkalmazásbeállítások][functions12]

### <a name="add-powershell-to-the-function"></a>PowerShell hozzáadása a függvényhez

Itt az ideje, hogy hívásokat kezdeményezzen a Network Watcher-be az Azure-függvényen belül. A követelményektől függően a funkció megvalósítása változhat. A kód általános áramlása azonban a következő:

1. A bemeneti paraméterek feldolgozása.
2. A meglévő csomagrögzítések lekérdezése a korlátok ellenőrzéséhez és a névütközések feloldásához.
3. Hozzon létre egy csomagrögzítést a megfelelő paraméterekkel.
4. Rendszeres időközönként lekell adni a csomagrögzítést, amíg be nem fejeződik.
5. Értesítse a felhasználót, hogy a csomagrögzítési munkamenet befejeződött.

A következő példa a PowerShell-kód, amely használható a függvényben. Vannak olyan értékek, amelyeket le kell cserélni az **subscriptionId**, **resourceGroupName**és **storageAccountName .**

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

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
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>A függvény URL-címének beolvasása 
1. Miután létrehozta a funkciót, konfigurálja a riasztást, hogy meghívja a függvényhez társított URL-címet. Ennek az értéknek a befiókjához másolja a függvény URL-címét a függvényalkalmazásból.

    ![A függvény URL-címének megkeresése][functions13]

2. Másolja a függvényalkalmazás függvényÉNEK URL-címét.

    ![A függvény URL-címének másolása][2]

Ha egyéni tulajdonságokra van szüksége a webhook POST-kérelem hasznos adatában, olvassa el [a Webhook konfigurálása egy Azure metrikariasztáson című webhelyet.](../azure-monitor/platform/alerts-webhooks.md)

## <a name="configure-an-alert-on-a-vm"></a>Riasztás konfigurálása virtuális számítógépen

Riasztások konfigurálható, hogy értesítse az egyéneket, ha egy adott metrika átlépi a küszöbértéket, amely hozzá van rendelve. Ebben a példában a riasztás az elküldött TCP-szegmensek, de a riasztás térhet el sok más metrikák. Ebben a példában egy riasztás van beállítva, hogy hívja meg a webhook a függvény hívására.

### <a name="create-the-alert-rule"></a>A riasztási szabály létrehozása

Lépjen egy meglévő virtuális gépre, majd adjon hozzá egy riasztási szabályt. A riasztások konfigurálásáról szóló részletesebb dokumentáció az Azure-szolgáltatásokhoz való riasztások létrehozása az [Azure-szolgáltatásokhoz – Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)című témakörben található. Írja be a következő értékeket a **Riasztási szabály** panelen, majd kattintson az **OK gombra.**

  |**Beállítás** | **Érték** | **Részletek** |
  |---|---|---|
  |**Név**|TCP_Segments_Sent_Exceeded|A riasztási szabály neve.|
  |**Leírás**|Az elküldött TCP-szegmensek túllépték a küszöbértéket|A riasztási szabály leírása.|
  |**Metrika**|Elküldött TCP-szegmensek| A riasztás aktiválásához használandó metrika. |
  |**Állapot**|Nagyobb, mint| A metrika kiértékelésekor használandó feltétel.|
  |**Küszöb**|100| A riasztást kiváltó metrika értéke. Ezt az értéket érvényes értékre kell állítani a környezetben.|
  |**Időszak**|Az elmúlt öt percben| Azt az időszakot határozza meg, amelyben meg kell keresni a küszöbértéket a metrika.|
  |**Webhook (webhook)**|[webhook URL-címe a függvényalkalmazásból]| A webhook URL-címe a függvényalkalmazásból, amely az előző lépésekben jött létre.|

> [!NOTE]
> A TCP-szegmensek metrikája alapértelmezés szerint nincs engedélyezve. További információ arról, hogyan engedélyezhetők további mérőszámok a [Figyelés és diagnosztika engedélyezése](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)című oldalon.

## <a name="review-the-results"></a>Az eredmények áttekintése

A riasztási eseményindítók feltételei után létrejön egy csomagrögzítés. Nyissa meg a Hálózatfigyelő t, és válassza **a Csomagrögzítés**lehetőséget. Ezen az oldalon kiválaszthatja a csomagrögzítő fájl hivatkozását a csomagrögzítés letöltéséhez.

![Csomagrögzítés megtekintése][functions14]

Ha a rögzítési fájl helyileg van tárolva, a virtuális gépre való bejelentkezéssel lekérheti azt.

A fájlok Azure storage-fiókokból való letöltéséről az Azure Blob storage használatával [– első lépések a .NET használatával](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Egy másik eszköz, amelyet használhat, a [Tárolókezelő](https://storageexplorer.com/).

A rögzítés letöltése után bármely **.cap** fájl olvasására használható eszközzel megtekintheti azt. Az alábbiakban az alábbi két eszközre mutató hivatkozások találhatók:

- [Microsoft üzenetelemző](https://technet.microsoft.com/library/jj649776.aspx)
- [Wireshark](https://www.wireshark.org/)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan tekintheti meg a csomagrögzítéseket a [Packet capture analízis ellátogatva a Wireshark](network-watcher-deep-packet-inspection.md)segítségével.


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
