---
title: Az Azure Key Vault naplók integrálni az Event Hubs használatával |} Microsoft Docs
description: Oktatóanyag, amely biztosítja a szükséges lépéseket, ha egy SIEM szeretné elérhetővé tenni az Key Vault naplóinak, az Azure napló integrálása révén
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 06/06/2018
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 9065d237057bf47d469b6f6fbf5b588c7e348232
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839357"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure napló integrációs Útmutató: az Event Hubs használatával folyamat az Azure Key Vault-események


>[!IMPORTANT]
> Az Azure Naplóelemzés integrációs szolgáltatás 06/01/2019 elavulttá válik.  AzLog letöltések 2018 jún 27 letiltásra kerül. Mi a teendő áthelyezése előre tekintse át a feladás egy vagy több útmutatót [integrálható a meglévő SIEM-eszközök használata Azure-figyelő](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/preview/?cdn=disable) 

Használhatja az Azure napló integrációs naplózott események beolvasásához, és elérhetővé teszi azokat a biztonsági információk és az esemény (SIEM) felügyeleti rendszerbe. Ez az oktatóanyag azt szemlélteti, hogyan Azure napló integrációs használható az Azure Event Hubs beszerzett naplók feldolgozása.

Az előnyben részesített módszere integrálása az Azure naplókat, hogy a SIEM gyártója által biztosított Azure figyelő összekötővel, és ezeket a következő [utasításokat](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Azonban ha a SIEM-szállító Azure figyelése nem biztosít egy összekötőt, akkor előfordulhat, hogy fogja tudni használni Azure napló integrációs ideiglenes megoldásként (Ha a SIEM Azure napló integrációs támogatja) addig, amíg az ilyen összekötő nem érhető el.

 
Ez az oktatóanyag segítségével Ismerkedjen meg a hogyan Azure napló integráció és az Event Hubs működik együtt a példa a lépéseket követve, és hogyan minden lépés támogatja a megoldást. Majd is eltarthat, mire hogy megismerte itt létrehozása a saját lépéseket a vállalat egyedi igényeinek támogatására.

>[!WARNING]
A lépéseket és az oktatóanyagban szereplő parancsok nem célja, hogy másolni és beilleszteni. Példák csak fontosságúak. Ne használja a PowerShell-parancsok "adott állapotban" élő környezetében. Kell testre szabnia a saját egyedi környezete alapján.


Ez az oktatóanyag bemutatja, hogyan véve az eseményközpontok a rendszer naplózza az Azure Key Vault tevékenység és hozzáférhető legyen JSON-fájlok formájában a SIEM rendszerhez. Konfigurálhatja a SIEM-rendszerben a JSON-fájlok feldolgozásához.

>[!NOTE]
>A jelen oktatóanyagban szereplő lépések a legtöbb tartalmaz, amely kulcstárolót, storage-fiókok és az event hubs konfigurálása. Az adott Azure napló integrációs lépésekre az oktatóanyag végén. Éles környezetben ne hajtsa végre ezeket a lépéseket. Csak egy tesztkörnyezetet szolgálnak. A lépések testre kell szabnia az éles használat előtt.

Információk menet segít megérteni a minden lépés okaival. Más cikkek hivatkozásainak nyújtanak további információkhoz juthat az egyes témakörök.

A szolgáltatásokat, amelyeket az oktatóanyag említi kapcsolatos további információkért lásd: 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Azure Naplóelemzés integráció](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Kezdeti telepítés

Ebben a cikkben szereplő lépések végrehajtásához, a következők szükségesek:

1. Azure-előfizetések és az adott előfizetéshez, rendszergazdai jogosultságokkal rendelkező fiókot. Ha nem rendelkezik előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
 
2. A rendszer, amely megfelel a Azure napló-integráció telepítése Internet-hozzáféréssel rendelkező. A rendszer lehet egy felhőalapú szolgáltatás, vagy helyben tárolt.

3. [Azure napló-integráció](https://www.microsoft.com/download/details.aspx?id=53324) telepítve. A telepítéséhez:

   a. A távoli asztal segítségével a rendszer a 2. lépésben említett csatlakozni.   
   b. Másolja a rendszer a Azure napló integráció telepítője. Is [a telepítési fájlok letöltési](https://www.microsoft.com/download/details.aspx?id=53324).   
   c. A telepítő elindításához, és fogadja el a Microsoft szoftverlicenc-szerződést.   
   d. Ha telemetriai tájékoztatást fogunk adni, hagyja bejelölve a jelölőnégyzetet. Ha szeretné ahelyett, hogy nem küldött használati adatait a Microsoft, törölje a jelet a jelölőnégyzetből.
   
   További információ a Azure napló integráció és telepítéséről: [Azure napló integráció az Azure diagnosztikai naplózás és a Windows-Eseménytovábbítást](security-azure-log-integration-get-started.md).

4. A legújabb PowerShell-verzió.
 
   Ha a Windows Server 2016 telepítve van, akkor legalább rendelkezik PowerShell 5.0. A Windows Server bármely verziója használata, lehetséges, hogy egy korábbi telepített PowerShell. A verzió beírásával ellenőrizheti ```get-host``` egy PowerShell-ablakban. Ha nincs PowerShell 5.0 verziója, akkor [le is](https://www.microsoft.com/download/details.aspx?id=50395).

   Miután legalább PowerShell 5.0, folytathatja a legújabb verzió telepítése:
   
   a. A PowerShell-ablakot, írja be a ```Install-Module Azure``` parancsot. A telepítés végrehajtásához.    
   b. Adja meg a ```Install-Module AzureRM``` parancsot. A telepítés végrehajtásához.

   További információkért lásd: [Azure PowerShell telepítése](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Támogató infrastruktúra elemeinek létrehozása

1. Nyisson meg egy emelt szintű PowerShell ablakot, és navigáljon **C:\Program Files\Microsoft Azure napló integrációs**.
2. Importálja a AzLog parancsmagok a parancsfájl LoadAzLogModule.ps1 való futtatásával. Adja meg a `.\LoadAzLogModule.ps1` parancsot. (Figyelje meg a ". \" parancsnak a.) Ennek nagyjából a következőképpen kell kinéznie:</br>

   ![A betöltött modulok listáján](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Adja meg a `Connect-AzureRmAccount` parancsot. A bejelentkezési ablakban adja meg a hitelesítő adatok az előfizetés használni ehhez az oktatóanyaghoz.

   >[!NOTE]
   >Ha ez az első alkalommal meg most jelentkezik be Azure erről a gépről, látni fogja a PowerShell adatgyűjtés engedélyezése a Microsoft üzenet. Azt javasoljuk, hogy engedélyezze az adatgyűjtés, mert lesz használható Azure PowerShell javítása érdekében.

4. Sikeres hitelesítést követően jelentkezett be, és meg az alábbi képernyőfelvételen szereplő információkat. Jegyezze fel az előfizetés-azonosító és az előfizetés nevét, mert szükség lesz a későbbi lépésekben befejeződnek.

   ![PowerShell-ablakot](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Hozzon létre változókat értékeket, amelyeket a rendszer később tárolásához. Adja meg a következő PowerShell-sorok. Szükség lehet az értékeket a környezet beállításához.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (Az előfizetés neve eltérő lehet. Tekintheti meg az előző parancs részeként.)
    - ```$location = 'West US'``` (Ezt a változót használja felelt meg a helyet, ahol erőforrásokat kell létrehozni. Módosíthatja ezt a változót a bárhol lehet.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (A neve bármi lehet, de ez csak kisbetűket és számokat kell tartalmaznia.)
    - ``` $storageName = $name``` (Ezt a változót használja a tárfiók nevének.)
    - ```$rgname = $name ``` (Ez a változó lesz használható az erőforráscsoport neve.)
    - ``` $eventHubNameSpaceName = $name``` (Ez az esemény hub névtér nevét.)
6. Adja meg az előfizetést, hogy a működik:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Hozzon létre egy erőforráscsoportot:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Ha beírja `$rg` ezen a ponton ezen a képernyőfelvételen látható hasonló kimenetnek kell megjelennie:

   ![Kimeneti erőforráscsoport létrehozása után](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Hozzon létre egy tárfiókot, állapotadatokat nyomon követéséhez használható:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Az event hub névtér létrehozása. Ez az eseményközpontok létrehozásához szükséges.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Töltse le az elemzések szolgáltatóval használható Szabályazonosító:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Minden lehetséges az Azure-helyeinek lekéréséhez, és vegye fel a neveket egy változó, amely egy későbbi lépésben használható:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Ha beírja `$locations` ezen a ponton a helynevek lásd a Get-AzureRmLocation által visszaadott további adatok nélkül.
12. Azure Resource Manager napló profil létrehozása: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Az Azure naplóelemzés profillal kapcsolatos további információkért lásd: [áttekintése Azure tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

> [!NOTE]
> Egy hibaüzenetet kaphat napló-profil létrehozása közben. Majd a Get-AzureRmLogProfile és eltávolítása-AzureRmLogProfile dokumentációjában tekintheti meg. Ha futtatja a Get-AzureRmLogProfile, tekintse meg a napló-profillal kapcsolatos információk. Írja be a meglévő napló-profil törlése a ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` parancsot.
>
>![Erőforrás-kezelő profil hiba](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

1. A kulcstároló létrehozása:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. A key vault naplózásának konfigurálása:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Napló tevékenység létrehozása

Kérelmek kell küldeni a Key Vault naplózása létrehozásához. Műveletek, például Kulcslétrehozási, titkok, tárolása, vagy titkos kulcsok beolvasása a Key Vault naplóbejegyzések hoz létre.

1. Megjeleníti az aktuális tároló kulcsait:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Egy új készítése **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Jelenjen meg többé a kulcsokat, és tekintse meg, amely **key2** egy másik értéket tartalmazza:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Állítsa be, és olvassa el a titkos kulcs további naplóbejegyzések létrehozásához:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Visszaadott titkos](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Azure Naplóelemzés-integráció konfigurálása

Most, hogy minden szükséges elemeket kell rendelkeznie a Key Vault naplózásával az eseményközpontba van beállítva, kell Azure napló-integráció konfigurálása:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Minden event hubs AzLog parancsot:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Egy perc vagy, a két legutóbbi parancsok futtatására megtekintheti az JSON-fájlok létrehozása folyamatban. A könyvtár figyelésével ellenőrizheti, hogy **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>További lépések

- [Azure Naplóelemzés integrációs – gyakori kérdések](security-azure-log-integration-faq.md)
- [Ismerkedés az Azure napló integráció](security-azure-log-integration-get-started.md)
- [Az Azure-erőforrások naplók integrálja a SIEM-rendszerekről](security-azure-log-integration-overview.md)
