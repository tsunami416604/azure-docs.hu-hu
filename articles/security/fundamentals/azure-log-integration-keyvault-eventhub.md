---
title: Naplók integrálása Azure Key Vaultból Event Hubs használatával | Microsoft Docs
description: Oktatóanyag, amely biztosítja a szükséges lépéseket ahhoz, hogy Key Vault naplók elérhetők legyenek a SIEM számára a Azure Log Integration használatával
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 15223c59f270dc562e521697186cfaf7f30073b9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004119"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure Log Integration oktatóanyag: Azure Key Vault események feldolgozása Event Hubs használatával

>[!IMPORTANT]
> Az Azure log-integrációs szolgáltatást a 06/15/2019-as rendszer elavulttá teszi. A AzLog letöltése a 2018. június 27-én le lett tiltva. Útmutatás a további lépések áttekintéséhez: az [Azure monitor használata az Siem-eszközökkel való integráláshoz](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

A Azure Log Integration segítségével beolvashatja a naplózott eseményeket, és elérhetővé teheti azokat a biztonsági információk és az Event Management (SIEM) rendszer számára. Ez az oktatóanyag azt szemlélteti, hogyan használhatók a Azure Log Integration az Azure Event Hubs által beszerzett naplók feldolgozására.

Az Azure-naplók integrálásának előnyben részesített módszere az SIEM-gyártó Azure Monitor-összekötő használata, és a következő [utasítások](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)követése. Ha azonban az SIEM-szállító nem biztosít összekötőt a Azure Monitorhoz, előfordulhat, hogy a Azure Log Integrationt ideiglenes megoldásként használhatja (ha a SIEM-et a Azure Log Integration támogatja), amíg az összekötő elérhetővé válik.

 
Ebből az oktatóanyagból megismerheti, hogy a Azure Log Integration és a Event Hubs hogyan működik együtt a példát követve, és megértette, hogyan támogatja az egyes lépések a megoldást. Ezután elvégezheti, hogy mit tanult itt, hogy saját lépéseket hozzon létre a vállalat egyedi követelményeinek támogatásához.

> [!WARNING]
> Az oktatóanyag lépései és parancsai nem másolhatók és illeszthetők be. Csak példák. Az élő környezetben ne használja a "as is" PowerShell-parancsokat. Az egyedi környezet alapján kell testreszabnia azokat.


Ez az oktatóanyag végigvezeti a folyamaton, amellyel Azure Key Vault tevékenység bejelentkezett egy Event hub-ba, és így JSON-fájlként elérhetővé válik az SIEM-rendszer számára. Ezután konfigurálhatja a SIEM-rendszerét a JSON-fájlok feldolgozásához.

>[!NOTE]
>Az oktatóanyagban szereplő lépések többsége magában foglalja a kulcstartók, a Storage-fiókok és az Event hubok konfigurálását. Az adott Azure Log Integration lépések az oktatóanyag végén találhatók. Ne hajtsa végre ezeket a lépéseket éles környezetben. Kizárólag laboratóriumi környezetekhez készültek. Az éles környezetben való használat előtt testre kell szabnia a lépéseket.

A módon biztosított információk segítenek megérteni az egyes lépések hátterében rejlő okokat. A többi cikkre mutató hivatkozások részletesebben ismertetik az egyes témaköröket.

Az oktatóanyag által megemlített szolgáltatásokról további információt a következő témakörben talál: 

- [Azure Key Vault](/azure/key-vault/key-vault-overview)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Azure Log Integration](azure-log-integration-overview.md)


## <a name="initial-setup"></a>Kezdeti beállítás

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A cikk lépéseinek elvégzéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetést és-fiókot, amely rendszergazdai jogosultságokkal rendelkezik az előfizetéshez. Ha nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
 
* Az internethez hozzáférő rendszer, amely megfelel a Azure Log Integration telepítésének követelményeinek. A rendszer felhőalapú szolgáltatáson vagy helyszíni környezetben is elvégezhető.

* Azure Log Integration telepítve. A telepítéshez:

   a. Távoli asztal használata a 2. lépésben említett rendszerhez való kapcsolódáshoz.   
   b. Másolja a Azure Log Integration telepítőt a rendszeren. c. Indítsa el a telepítőt, és fogadja el a Microsoft szoftverlicenc-feltételeit.

* Ha megadja a telemetria adatait, hagyja bejelölve a jelölőnégyzetet. Ha inkább nem szeretné elküldeni a használati adatokat a Microsoftnak, törölje a jelet a jelölőnégyzetből.

   További információ a Azure Log Integrationről és annak telepítéséről: [Azure Log Integration Azure Diagnostics naplózással és a Windows-események továbbításával](azure-log-integration-get-started.md).

* A PowerShell legújabb verziója.

   Ha telepítve van a Windows Server 2016, akkor legalább a PowerShell 5,0. Ha a Windows Server bármely más verzióját használja, lehet, hogy a PowerShell korábbi verziója van telepítve. A verziót egy PowerShell-ablak beírásával ```get-host``` tekintheti meg. Ha nincs telepítve a PowerShell 5,0, [letöltheti azt](https://www.microsoft.com/download/details.aspx?id=50395).

   Ha legalább a PowerShell 5,0-es verziójával rendelkezik, folytassa a legújabb verzió telepítésével a [Azure PowerShell telepítésének](/powershell/azure/install-az-ps)utasításait követve.


## <a name="create-supporting-infrastructure-elements"></a>Támogató infrastruktúra-elemek létrehozása

1. Nyisson meg egy rendszergazda jogú PowerShell-ablakot, és lépjen a **C:\Program Files\Microsoft Azure log Integration**.
1. Importálja a AzLog-parancsmagokat a LoadAzLogModule. ps1 parancsfájl futtatásával. Adja meg `.\LoadAzLogModule.ps1` a parancsot. (Figyelje meg a következőt: ". \" ebben a parancsban.) Ennek nagyjából a következőképpen kell kinéznie:</br>

   ![Betöltött modulok listája](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Adja meg `Connect-AzAccount` a parancsot. A bejelentkezési ablakban adja meg az előfizetéshez tartozó hitelesítő adatokat, amelyeket az oktatóanyaghoz használni fog.

   >[!NOTE]
   >Ha első alkalommal jelentkezik be az Azure-ba a számítógépről, megjelenik egy üzenet arról, hogy engedélyezi a Microsoft számára a PowerShell-használati adatok gyűjtését. Javasoljuk, hogy engedélyezze ezt az adatgyűjtést, mivel az Azure PowerShell javítására lesz felhasználva.

1. A sikeres hitelesítés után be van jelentkezve. Jegyezze fel az előfizetés-azonosítót és az előfizetés nevét, mert a későbbi lépések végrehajtásához szükség lesz rájuk.

1. Hozzon létre változókat az olyan értékek tárolására, amelyek később lesznek felhasználva. Adja meg a következő PowerShell-vonalak mindegyikét. Előfordulhat, hogy módosítania kell az értékeket a környezetnek megfelelően.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'```(Az előfizetés neve eltérő lehet. Az előző parancs kimenetének részeként láthatja.)
    - ```$location = 'West US'```(Ezt a változót fogja használni a rendszer annak a helynek a továbbítására, ahol az erőforrásokat létre kell hozni. Ezt a változót beállíthatja úgy, hogy a választott hely legyen.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random```(A név lehet bármi, de csak kisbetűket és számokat tartalmazhat.)
    - ```$storageName = $name```(A rendszer ezt a változót fogja használni a Storage-fiók nevéhez.)
    - ```$rgname = $name```(Ezt a változót az erőforráscsoport neveként fogja használni a rendszer.)
    - ```$eventHubNameSpaceName = $name```(Ez az Event hub-névtér neve.)
1. Itt adhatja meg azt az előfizetést, amellyel dolgozni fog:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Hozzon létre egy erőforráscsoportot:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Ha ezt a `$rg` pontot adja meg, az ehhez a képernyőképhez hasonló kimenetnek kell megjelennie:

   ![Kimenet egy erőforráscsoport létrehozása után](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. Hozzon létre egy Storage-fiókot, amely az állapotadatok nyomon követésére szolgál majd:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Hozza létre az Event hub-névteret. Ez az Event hub létrehozásához szükséges.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Szerezze be az adatellenőrzési szolgáltató által használt szabály AZONOSÍTÓját:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Szerezze be az összes lehetséges Azure-helyet, és adja hozzá a neveket egy olyan változóhoz, amelyet később használhat egy későbbi lépésben:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Ha ezen a `$locations` ponton lép be, a Get-AzLocation által visszaadott további információk nélkül láthatja a helyek nevét.
1. Hozzon létre egy Azure Resource Manager log-profilt: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Az Azure log-profillal kapcsolatos további információkért lásd: [Az Azure-tevékenység naplójának áttekintése](../../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Előfordulhat, hogy a rendszer hibaüzenetet küld, amikor megpróbál létrehozni egy log-profilt. Ezután áttekintheti a Get-AzLogProfile és a Remove-AzLogProfile dokumentációját. Ha a Get-AzLogProfile-t futtatja, a rendszer információkat lát a napló profiljáról. A meglévő log-profilt a ```Remove-AzLogProfile -name 'Log Profile Name'``` parancs beírásával törölheti.
>
>![Resource Manager-profil hibája](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

1. A Key Vault létrehozása:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. A Key Vault naplózásának konfigurálása:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Naplózási tevékenység előállítása

A naplózási tevékenységek létrehozásához kérelmeket kell elküldeni Key Vault. Az olyan műveletek, mint a kulcs létrehozása, a titkok tárolása vagy a titkok beolvasása Key Vault a naplóbejegyzések létrehozásához.

1. Az aktuális tárolási kulcsok megjelenítése:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Új **key2**létrehozása:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Jelenítse meg újra a kulcsokat, és láthatja, hogy a **key2** más értéket tartalmaz:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Titkos kód beállítása és beolvasása További naplóbejegyzések létrehozásához:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Visszaadott titkos kód](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Azure Log Integration konfigurálása

Most, hogy konfigurálta az összes szükséges elemet, hogy Key Vault a naplózást egy Event hub-ba, konfigurálnia kell Azure Log Integration:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Futtassa a AzLog parancsot az egyes Event hub-hoz:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Egy perc elteltével, vagy az utolsó két parancs futtatását követően meg kell jelennie a generált JSON-fájloknak. A címtár **C:\users\AzLog\EventHubJson**figyelésével ellenőrizheti.

## <a name="next-steps"></a>További lépések

- [Azure Log Integration GYIK](azure-log-integration-faq.md)
- [Ismerkedés a Azure Log Integration](azure-log-integration-get-started.md)
- [Naplók integrálása az Azure-erőforrásokból a SIEM-rendszerbe](azure-log-integration-overview.md)
