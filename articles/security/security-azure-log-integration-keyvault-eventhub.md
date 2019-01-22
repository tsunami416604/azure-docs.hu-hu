---
title: Az Azure Key Vault naplóinak integrálása az Event Hubs használatával |} A Microsoft Docs
description: Ez az oktatóanyag elérhetővé Key Vault-naplók egy siem-nek az Azure Log Integration szükséges lépéseket ismerteti
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 01/14/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: f9eb4489894632502b7df97cd1149bd027164d19
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437419"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Az Azure Log Integration-oktatóanyag: Az Azure Key Vault események feldolgozása az Event Hubs használatával

>[!IMPORTANT]
> Az Azure Log integration szolgáltatás 06/01/2019 elavulttá válik. 2018. június 27. AzLog letöltések letiltottuk. Mi a teendő mozgatása előre tekintse át a hozzászólás útmutatást [SIEM-eszközök integrálása az Azure monitor](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Használhatja az Azure Log Integration naplózott események beolvasásához, és elérhetővé teheti azokat a biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti rendszer. Ez az oktatóanyag azt szemlélteti, hogyan Azure Log Integration használható-e feldolgozni az Azure Event Hubs keretében beszerzett naplókat.

Az előnyben részesített módja az Azure-naplók integrálása a SIEM gyártója által biztosított Azure Monitor-összekötő használatával, és ezek a következő van [utasításokat](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Azonban a SIEM gyártója által biztosított az Azure monitornak nem biztosít egy összekötőt, ha, előfordulhat, hogy tudni használni az Azure Log Integration ideiglenes megoldás (ha az Azure Log Integration támogatja a siem-nek) mindaddig, amíg elérhető ilyen egy összekötőt.

 
Ebben az oktatóanyagban használatával leckén keresztül az Azure Log Integration és az Event Hubs együttműködésének példa lépések, és hogyan támogatja az egyes lépések a a megoldás ismertetése. Kipróbálhatja a tanultak Itt hozhat létre a saját vállalata egyedi igényeinek támogatásához lépéseket.

>[!WARNING]
A lépéseket, és ebben az oktatóanyagban szereplő parancsok nem tartozhat másolni és beilleszteni. Még csak példák. Az élő környezetben ne használja az "adott állapotában" PowerShell-parancsokat. Kell testre szabnia egyedi környezete alapján.


Ez az oktatóanyag végigvezeti egy eseményközpontba naplózza az Azure Key Vault tevékenység elkészítéséhez, és elérhetővé JSON-fájlok formájában a SIEM rendszerhez. Konfigurálhatja a SIEM-rendszerbe, a JSON-fájlok feldolgozására.

>[!NOTE]
>A jelen oktatóanyagban szereplő lépések a legtöbb magában foglalja a kulcstartók, a storage-fiókok és az event hubs konfigurálása. Az adott Azure Log Integration lépései is, ez az oktatóanyag végén. Éles környezetben ne hajtsa végre ezeket a lépéseket. Csak tesztkörülmények szolgálnak. Az éles használat előtt testre kell szabnia a lépéseket.

Követhető megadott információk segítségével megismerheti az egyes lépések okaival. Más cikkek hivatkozásainak biztosíthatja a további részleteket az egyes témaköröket.

Az alkalmazásfrissítés ebben az oktatóanyagban, amely a szolgáltatásokkal kapcsolatos további információkért lásd: 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure-naplók integrációja](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Kezdeti beállítás

Mielőtt elvégezhetné a jelen cikkben ismertetett lépések, a következők szükségesek:

1. Azure-előfizetések és az előfizetés rendszergazdai jogosultságokkal rendelkező fiókot. Ha nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
 
1. A rendszer, amely megfelel a az Azure Log Integration telepítése internet-hozzáféréssel rendelkező. A rendszer a felhőalapú szolgáltatásként vagy a helyszínen üzemeltetett.

1. Az Azure Log Integration telepítve. A telepítéshez:

   a. A távoli asztal használatával csatlakozhat a rendszer a 2. lépésben említett.   
   b. Másolja a rendszer az Azure Log Integration telepítőt. c. Indítsa el a telepítőt, és fogadja el a Microsoft szoftverlicenc-feltételeket.

1. Telemetriai adatok fog megadása esetén hagyja meg a jelölőnégyzet be van jelölve. Ha szeretné inkább nem küld használati adatokat a Microsoft, törölje a jelölőnégyzet jelölését.

   További információ az Azure Log Integration, és a telepítésének módjáról lásd: [Azure Log Integration az Azure diagnosztikai naplózás és a Windows-Eseménytovábbítást](security-azure-log-integration-get-started.md).

1. A PowerShell legújabb verziója.

   Ha Windows Server 2016 telepítve van, akkor legalább rendelkezik PowerShell 5.0-s. Ha Windows Server bármilyen más verziót használ, előfordulhat, hogy rendelkezik PowerShell telepítve van egy korábbi verzióját. A verzió ellenőrzéséhez írja be ```get-host``` egy PowerShell-ablakban. Ha még nincs telepítve a PowerShell 5.0, [letöltheti](https://www.microsoft.com/download/details.aspx?id=50395).

   Miután legalább PowerShell 5.0-, folytathatja a legfrissebb verzió telepítéséhez:

   a. A PowerShell ablakban írja be a ```Install-Module Azure``` parancsot. A telepítési lépéseket.    
   b. Adja meg a ```Install-Module AzureRM``` parancsot. A telepítési lépéseket.

   További információkért lásd: [Azure PowerShell telepítése](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Támogató infrastruktúra-elemek létrehozása

1. Nyisson meg egy rendszergazda jogú PowerShell-ablakban, majd **C:\Program Files\Microsoft Azure Log Integration**.
1. Importálja a AzLog parancsmagok a LoadAzLogModule.ps1 szkript futtatásával. Adja meg a `.\LoadAzLogModule.ps1` parancsot. (Közlemény a ". \" , hogy a parancsban.) Ennek nagyjából a következőképpen kell kinéznie:</br>

   ![A betöltött modulok listája](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Adja meg a `Connect-AzureRmAccount` parancsot. A bejelentkezési ablakban írja be a hitelesítő adatokat az ehhez az oktatóanyaghoz használt előfizetés.

   >[!NOTE]
   >Ha először, akkor már bejelentkezett az Azure-bA erről a gépről, látni fogja a PowerShell-használati adatok gyűjtéséhez lehetővé teszi a Microsoft szóló üzenetet. Azt javasoljuk, hogy engedélyezze az adatgyűjtést, mivel lesz használható Azure PowerShell-lel javítása érdekében.

1. Sikeres hitelesítés után jelentkezett be, és az adatok az alábbi képernyőfelvételen láthatók. Jegyezze fel a előfizetése Azonosítóját és az előfizetés nevét, mert kell őket a későbbi lépések végrehajtásához.

   ![PowerShell-ablakban](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
1. Hozzon létre változókat a értékeket, amelyeket később fogja használni. Adja meg a következő PowerShell-parancsokat minden egyes. Előfordulhat, hogy a értékeit, hogy megfeleljenek a környezet módosítani kell.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (Az előfizetés neve eltérő lehet. Tekintheti meg az előző parancs részeként.)
    - ```$location = 'West US'``` (Ezt a változót használja át a helyet, ahol erőforrásokat kell létrehozni. Módosíthatja ezt a változót kell bármely tetszőleges helyre.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (A neve bármi lehet, de ez csak kisbetűket és számokat kell tartalmaznia.)
    - ``` $storageName = $name``` (Ezt a változót használja a storage-fiók neve.)
    - ```$rgname = $name ``` (Ezt a változót használja az erőforráscsoport neveként.)
    - ``` $eventHubNameSpaceName = $name``` (Ez a az eseményközpont-névtér nevét.)
1. Adja meg az előfizetést, amelyhez a működik:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
1. Hozzon létre egy erőforráscsoportot:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Ha megad `$rg` ezen a ponton kimenetnek kell megjelennie alábbi képernyőképhez hasonlóan:

   ![Az erőforráscsoport létrehozása után a kimenet](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. Hozzon létre egy tárfiókot, amelyet állapotinformációkat nyomon követéséhez használható:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Az eseményközpont-névtér létrehozása. Ez egy eseményközpont létrehozásához szükséges.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Insights-szolgáltatóval használandó szabály Azonosítójának lekéréséhez:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
1. Első minden lehetséges Azure-helyen, és vegye fel a neveket, amely egy későbbi lépésben használható:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Ha megad `$locations` ezen a ponton az adatgyűjtés kiterjed a hely a Get-AzureRmLocation által visszaadott további adatok nélkül.
1. Hozzon létre egy Azure Resource Manager naplóprofil: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Az Azure log-profillal kapcsolatos további információkért lásd: [áttekintése az Azure-tevékenységnapló](../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Előfordulhat, hogy kapott hibaüzenetet, amikor megpróbál létrehozni egy naplóprofil. Ezután a Get-AzureRmLogProfile és a Remove-AzureRmLogProfile dokumentációjában tekintheti meg. Ha futtatja a Get-AzureRmLogProfile, láthatja a napló-profillal kapcsolatos információkat. Írja be a meglévő naplóprofil törölheti a ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` parancsot.
>
>![Resource Manager-profil hiba](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

1. A key vault létrehozása:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

1. A key vault naplózásának konfigurálása:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Naplózási tevékenység létrehozása

Kérelmek kell küldeni a Key Vault naplózása létrehozásához szükséges. Műveletek, például kulcslétrehozás, titkos kódok, tárolásához, vagy titkos kulcsok a Key Vault olvasásakor naplóbejegyzések hoz létre.

1. Megjeleníti az aktuális tárkulcsok:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Hozzon létre egy új **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Nyissa meg ismét a kulcsokat, és itt látható, hogy **key2** egy másik értéket tartalmazza:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Adja meg, és olvassa el a titkos kulcs létrehozásához további bejegyzései:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Visszaadott titkos](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Az Azure Log-integráció konfigurálása

Most, hogy konfigurálta a szükséges elemeket, hogy a Key Vault-naplózás az eseményközpontba, kell konfigurálni az Azure Log Integration:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ``` $storagekey = $storagekeys[0].Value```

Minden event hubs AzLog parancsot:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Egy percet vesz, az utolsó két parancsok futtatása, ha létrehozott JSON-fájlok jelenik meg. Azt is ellenőrizze, hogy a címtár-figyelési szolgáltatás által **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>További lépések

- [Az Azure Log Integration – gyakori kérdések](security-azure-log-integration-faq.md)
- [Ismerkedés az Azure Log Integration](security-azure-log-integration-get-started.md)
- [Azure-erőforrások naplóinak integrálása a SIEM rendszerekhez](security-azure-log-integration-overview.md)
