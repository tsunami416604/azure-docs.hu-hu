---
title: A csomagrögzítés használatával proaktív hálózatfigyelés a riasztások és az Azure Functions |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre Azure Network Watcher egy aktivált riasztás csomagrögzítés
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
ms.openlocfilehash: c7bfd36bb4e36b10487edbbaa40421f067c9ed3e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048758"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Proaktív hálózatmonitorozás riasztások és az Azure Functions használata a csomagrögzítést

Network Watcher csomagrögzítés nyomon a virtuális gépek és gépből irányuló forgalom munkamenetet hoz létre. A rögzítési fájl lehet egy szűrőt, amely csak a forgalmat, amely a figyelni kívánt nyomon követéséhez van definiálva. Majd tárolja ezeket az adatokat, egy storage-blobba, vagy helyileg, a vendég gépen.

Ez a képesség más automatizálási forgatókönyvek, például az Azure Functions távolról is indítható. A csomagrögzítés lehetővé teszi a szoftveralapú hálózati rendellenességek alapuló proaktív rögzíti futtatásához. Más használati módjai többek között, hálózati statisztika, hálózati behatolásokat, hibakeresési ügyfél – kiszolgáló kommunikáció és egyéb információk kérése a összegyűjtéséhez.

Az Azure-ban üzembe helyezett erőforrások 24/7 rendszerrel. Alkalmazottak nem aktív monitorozása a minden erőforrás 24/7 állapotát. Például mi történik, ha hiba fordul elő, hogy 2 Órakor?

A Network watcherrel, riasztási és függvények a belül az Azure-ökoszisztéma proaktív módon reagálhat az adatok és megoldani a problémákat a hálózaton lévő eszközöket.

![Forgatókönyv][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* A legújabb [Azure PowerShell-lel](/powershell/azure/install-Az-ps).
* A Network Watcher egy meglévő példányát. Ha még nem rendelkezik ilyennel, [a Network Watcher-példány létrehozása](network-watcher-create.md).
* A Network Watcher ugyanabban a régióban egy meglévő virtuális gépet a [Windows bővítmény](../virtual-machines/windows/extensions-nwa.md) vagy [Linux virtuálisgép-bővítmény](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Forgatókönyv

Ebben a példában a virtuális Gépre küldi a szokásosnál több TCP-szegmens, és szeretné kapni. TCP-szegmens az itt példaként szolgálnak, de bármely riasztási feltételt is használhat.

Amikor a rendszer megkérdezi, érdemes tudni, miért növekedett a kommunikációt a csomag szintjén adatok fogadására. Ezután térjen vissza a virtuális gép normál kommunikáció lépéseket is igénybe vehet.

Ez a forgatókönyv feltételezi, hogy a Network Watcher és a egy érvényes virtuális gépet egy erőforráscsoportot egy meglévő példányát.

Az alábbi lista a a munkafolyamat, amely áttekintést:

1. Riasztást vált ki a virtuális Gépen.
1. A riasztás az Azure-függvényt egy webhook használatával hívja meg.
1. Az Azure-függvény feldolgozza a riasztást, és a egy Network Watcher packet rögzítési munkamenet indítása.
1. A csomagrögzítés a virtuális gépen fut, és összegyűjti a forgalmat.
1. A csomag rögzítési feltölti egy tárfiókba, tekintse át és elemzés céljából.

Ez a folyamat automatizálására hozunk létre, és riasztást csatlakozzon a virtuális gépen indítható, ha az esemény akkor következik be. Azt is létrehozhat egy függvényt hívni a Network Watcher.

Ebben a forgatókönyvben a következőket teszi:

* Létrehoz egy Azure-függvényt, amely elindítja a csomagrögzítés.
* Létrehoz egy riasztási szabályt a virtuális gépen, és konfigurálja a riasztási szabály az Azure-függvény meghívásához.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Az első lépés, ha a riasztás feldolgozása és csomagrögzítés hozzon létre egy Azure-függvényt.

1. Az a [az Azure portal](https://portal.azure.com)válassza **erőforrás létrehozása** > **számítási** > **Függvényalkalmazás**.

    ![Függvényalkalmazás létrehozása][1-1]

2. Az a **Függvényalkalmazás** panelen adja meg a következő értékeket, és válassza ki **OK** az alkalmazás létrehozásához:

    |**Beállítás** | **Érték** | **Részletek** |
    |---|---|---|
    |**Alkalmazás neve**|PacketCaptureExample|A függvényalkalmazás nevére.|
    |**Előfizetés**|[Az Ön előfizetése] Az előfizetés, amelynek a függvényalkalmazás létrehozásához.||
    |**Erőforráscsoport**|PacketCaptureRG|Az erőforráscsoport tartalmazza a függvényalkalmazást.|
    |**Szolgáltatási csomag**|Használatalapú csomag| A típusú tervezze meg a függvény alkalmazást használ. Lehetőségek a következők használat vagy az Azure App Service-csomag. |
    |**Hely**|USA középső régiója| A régió, amelyben létrehozza a függvényalkalmazást.|
    |**Tárfiók**|{autogenerated}| A storage-fiók, amelyet az Azure Functions az általános célú storage.|

3. Az a **PacketCaptureExample Függvényalkalmazások** panelen válassza ki **funkciók** > **egyéni függvény**  >  **+**.

4. Válassza ki **HttpTrigger-Powershell**, majd adja meg a további adatokat. Végül, a függvény létrehozásához válassza **létrehozás**.

    |**Beállítás** | **Érték** | **Részletek** |
    |---|---|---|
    |**Forgatókönyv**|Kísérleti|Forgatókönyv típusa|
    |**A függvény neve**|AlertPacketCapturePowerShell|A függvény neve|
    |**Engedélyszint**|Függvény|A függvény a jogosultsági szint|

![Functions-példa][functions1]

> [!NOTE]
> A PowerShell-sablon kísérleti, és nem rendelkezik teljes körű támogatást.

Testreszabások szükségesek ehhez a példához, és mutatjuk be az alábbi lépéseket.

### <a name="add-modules"></a>Modulok hozzáadása

Network Watcher PowerShell-parancsmagok használatához töltse fel a legújabb PowerShell-modult a függvényalkalmazást.

1. A a legújabb Azure PowerShell-modulok telepítése a helyi gépén futtassa a következő PowerShell-parancsot:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Ebben a példában biztosít az Azure PowerShell-modulok helyi elérési útja. Ezek a mappák egy későbbi lépésben használt. Az ebben a forgatókönyvben használt modulokra:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![PowerShell-mappák][functions5]

1. Válassza ki **Alkalmazásbeállítások függvény** > **nyissa meg az App Service Editor**.

    ![A függvényalkalmazás beállításai][functions2]

1. Kattintson a jobb gombbal a **AlertPacketCapturePowershell** mappát, majd hozzon létre egy nevű mappába **azuremodules**. 

4. Hozzon létre egy almappát, az egyes modulok szükséges.

    ![Mappa és annak almappáiban][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Kattintson a jobb gombbal a **Az.Network** almappát, és válassza ki **fájlok feltöltése**. 

6. Nyissa meg az Azure-modulokat. A helyi **Az.Network** mappát, válassza ki az összes fájl a mappában. Ezután kattintson az **OK** gombra. 

7. Ismételje meg ezeket a lépéseket **Az.Accounts** és **Az.Resources**.

    ![Fájlok feltöltése][functions6]

1. Ha végzett, minden mappa rendelkeznie kell a PowerShell-modul fájlok a helyi gépen.

    ![PowerShell fájlok][functions7]

### <a name="authentication"></a>Authentication

A PowerShell-parancsmagok használatához hitelesítenie kell. A függvényalkalmazásban állítsa be a hitelesítést. Hitelesítés konfigurálása környezeti változók konfigurálása, és töltse fel a függvényalkalmazás egy titkosított kulcsfájl.

> [!NOTE]
> Ebben a forgatókönyvben csupán egy példa a megvalósítása az Azure Functions használatával hitelesítést nyújt. Ehhez más módja van.

#### <a name="encrypted-credentials"></a>Titkosított hitelesítő adatokat

A következő PowerShell-szkript létrehoz egy fő nevű **PassEncryptKey.key**. A megadott jelszó titkosított verzióját is tartalmazza. Ez a jelszó, ugyanazt a jelszót, amely az Azure Active Directory-alkalmazás, amely a hitelesítéshez használt van definiálva.

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

Az App Service Editorban függvényalkalmazás, hozzon létre egy nevű **kulcsok** alatt **AlertPacketCapturePowerShell**. Ezután töltse fel a **PassEncryptKey.key** , amelyet az előző PowerShell-példa fájlt.

![Functions-kulcs][functions8]

### <a name="retrieve-values-for-environment-variables"></a>A környezeti változók értékeit beolvasása

A végső követelmény, hogy állítsa be a környezeti változókat, amelyek eléréséhez az értékek a hitelesítéshez szükséges. Az alábbi lista tartalmazza a létrehozott környezeti változókat:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Az ügyfél-azonosító az Azure Active Directory-alkalmazás Alkalmazásazonosítója.

1. Ha még nem rendelkezik az alkalmazás használ, futtassa az alábbi példa egy alkalmazás létrehozására.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > A jelszó, amelyet az alkalmazás létrehozásakor használja ugyanazt a jelszót, amely a korábban létrehozott a kulcsfájl mentésekor kell lennie.

1. Az Azure Portalon válassza ki a **előfizetések**. Válassza ki az előfizetést, és válassza a **hozzáférés-vezérlés (IAM)**.

    ![Functions IAM][functions9]

1. Válassza ki a fiókot használja, és válassza ki a **tulajdonságok**. Másolja az alkalmazás azonosítója.

    ![Functions-alkalmazás azonosítója][functions10]

#### <a name="azuretenant"></a>AzureTenant

A Bérlőazonosító beszerzése futtassa a következő PowerShell-mintát:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

A AzureCredPassword környezeti változó értéke az érték a következő PowerShell-minta futtatása képest. Ebben a példában ugyanarra a számítógépre, amely az előző látható **titkosított hitelesítő adatokat** szakaszban. A szükséges érték a kimenetét a `$Encryptedpassword` változó.  Ez az a szolgáltatásnév jelszava, amely akkor titkosítja a PowerShell-parancsfájl használatával.

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

### <a name="store-the-environment-variables"></a>A környezeti változók Store

1. Nyissa meg a függvényalkalmazást. Válassza ki **Alkalmazásbeállítások függvény** > **beállításainak konfigurálása**.

    ![Alkalmazásbeállítások konfigurálása][functions11]

1. A környezeti változókhoz és értékeikhez ad hozzá az alkalmazás beállításaiban, és válassza **mentése**.

    ![Alkalmazásbeállítások][functions12]

### <a name="add-powershell-to-the-function"></a>PowerShell a függvény hozzáadása

Most ideje, hogy az Azure-függvény hívásai a Network Watcher be. Ez a funkció megvalósítását a követelményeitől függően eltérőek lehetnek. Azonban általános folyamata a kódot a következőképpen történik:

1. Folyamat-bemeneti paraméterekhez.
2. Lekérdezés meglévő csomagrögzítéseket gyűjtsenek a korlátokat, és névütközések feloldásához.
3. Hozzon létre egy csomagrögzítés megfelelő paraméterekkel.
4. Lekérdezési csomagrögzítés rendszeres időközönként, amíg nem fejeződik be.
5. A felhasználó értesítése arról, hogy a csomag rögzítési munkamenet befejeződik.

Az alábbi példában PowerShell-kódot, amely a függvényben használható. Vannak értékek, amelyeket ki kell cserélni a **subscriptionId**, **resourceGroupName**, és **storageAccountName**.

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
                $nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
                $networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

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
#### <a name="retrieve-the-function-url"></a>A függvény URL-Címének lekéréséhez 
1. Miután létrehozta a függvényt, konfigurálja a riasztás az URL-cím, a függvény társított meghívásához. Ez az érték beszerzéséhez a függvényalkalmazás a függvény URL-Címének másolása.

    ![A függvény URL-Címének keresése][functions13]

2. Másolja a függvényalkalmazás a függvény URL-CÍMÉT.

    ![A függvény URL-Címének másolása][2]

Ha a webhook POST kérés az adattartalomban szereplő egyéni tulajdonságok van szüksége, tekintse meg [webhook konfigurálása az Azure metrikariasztás](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurálhat egy riasztást a virtuális gép

Riasztások beállítható úgy, hogy a felhasználók értesítése, ha egy adott metrika átlépi a küszöbértéket, amely hozzá van rendelve. Ebben a példában a riasztás a a TCP-szegmens küldött, de számos más metrikákkal aktiválhatja a riasztást. Ebben a példában egy riasztás úgy van konfigurálva, a webhook függvény meghívásához.

### <a name="create-the-alert-rule"></a>A riasztási szabály létrehozása

Ugrás a meglévő virtuális gépet, és adja hozzá a riasztási szabályt. Értesítések konfigurálásával kapcsolatos részletes dokumentáció itt található [riasztások létrehozása az Azure monitorban az Azure-szolgáltatások – Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md). Adja meg a következő értékeket a **riasztási szabály** panelen, és válassza ki **OK**.

  |**Beállítás** | **Érték** | **Részletek** |
  |---|---|---|
  |**Name (Név)**|TCP_Segments_Sent_Exceeded|A riasztási szabály neve.|
  |**Leírás**|TCP-szegmens küldött túllépte a határértéket|A riasztási szabály leírását.|
  |**Metrika**|Elküldött TCP-szegmens| A metrika a riasztást kiváltó használatára. |
  |**a feltétel**|Nagyobb, mint| A feltétel a metrika értékeléséhez.|
  |**Küszöbérték**|100| A riasztást kiváltó a mérőszám értéke. Ez az érték a környezetnek érvényes értékre kell állítani.|
  |**Időszak**|Az elmúlt öt percben| Meghatározza, hogy az időszak, amelyben a küszöbértéket, a metrika a keresett.|
  |**Webhook**|[a webhook URL-cím függvényalkalmazásból]| A webhook URL-CÍMÉT az előző lépésekben létrehozott függvényalkalmazás.|

> [!NOTE]
> A TCP-szegmensek metrika alapértelmezés szerint nincs engedélyezve. További tudnivalók a további metrikák engedélyezése funkcionáló [engedélyezze a megfigyelést és a diagnosztika](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Az eredmények áttekintése

A riasztási eseményindítók feltételeit, miután csomagrögzítés jön létre. Nyissa meg a Network Watcher, és válassza ki **csomagrögzítés**. Ezen az oldalon válassza ki a csomagot rögzítési fájl hivatkozásra a csomagrögzítés letöltéséhez.

![Csomagrögzítés megtekintése][functions14]

Ha a rögzítési fájlt helyben van tárolva, jelentkezzen be a virtuális gép kérheti.

Az Azure storage-fiókok fájlok letöltésére vonatkozó utasításokért lásd: [.NET használatával az Azure Blob storage használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Van egy másik eszköz, amellyel [Tártallózó](https://storageexplorer.com/).

A rögzítési letöltése után megtekintheti azt minden olyan eszközzel, amely képes olvasni a **.cap** fájlt. Az alábbiakban két ezekkel az eszközökkel mutató hivatkozásokat:

- [A Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan megtekintéséhez látogasson el a csomagrögzítés [csomag eseményrögzítés elemzésének a Wireshark](network-watcher-deep-packet-inspection.md).


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
