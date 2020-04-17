---
title: Azure Key Vault-megoldás az Azure Monitorban | Microsoft dokumentumok
description: Az Azure Key Vault-megoldás az Azure Monitorban az Azure Key Vault-naplók áttekintéséhez használható.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 7da2fa2ddfbd9c71563dd8bd2e17b14c6dee62b3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455444"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault Analytics-megoldás az Azure Monitorban

![Key Vault szimbólum](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Key Vault-megoldás az Azure Monitorban az Azure Key Vault AuditEvent naplók áttekintéséhez használhatja.

A megoldás használatához engedélyeznie kell az Azure Key Vault-diagnosztika naplózását, és a diagnosztikát egy Log Analytics-munkaterületre kell irányítania. Nem szükséges a naplók at azure blob storage.It is not necessary to write the logs to Azure Blob storage.

> [!NOTE]
> 2017 januárjában megváltozott a Key Vaultból a Log Analytics szolgáltatásba küldött naplók támogatott módja. Ha a key vault-megoldás a címben megjelenik *(elavult),* tekintse meg [a régi Key Vault-megoldásból való áttelepítést a](#migrating-from-the-old-key-vault-solution) követendő lépésekért.
>
>

## <a name="install-and-configure-the-solution"></a>A megoldás telepítése és konfigurálása
Az Alábbi utasításokat követve telepítse és konfigurálja az Azure Key Vault-megoldást:

1. Az [Azure Monitor-megoldások hozzáadása a Megoldások galériából](../../azure-monitor/insights/solutions.md) című eljárásban ismertetett folyamat segítségével hozzáadhatja az Azure Key Vault-megoldást a Log Analytics-munkaterülethez.
2. A Key Vault-erőforrások diagnosztikai naplózásának engedélyezése a [portál](#enable-key-vault-diagnostics-in-the-portal) vagy a [PowerShell](#enable-key-vault-diagnostics-using-powershell) használatával

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Key Vault-diagnosztika engedélyezése a portálon

1. Az Azure Portalon keresse meg a Key Vault-erőforrást a
2. A Következő lap megnyitásához válassza a *Diagnosztikai beállítások lehetőséget*

   ![Az Azure Key Vault csempéjének képe](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. A következő lap megnyitásához kattintson *a Diagnosztika bekapcsolása gombra*

   ![Az Azure Key Vault csempéjének képe](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Adjon nevet a diagnosztikai beállításnak.
5. Kattintson a *Küldés* a Log Analytics szolgáltatásba jelölőnégyzetre
6. Meglévő Log Analytics-munkaterület kiválasztása vagy munkaterület létrehozása
7. Az *AuditEvent* naplók engedélyezéséhez kattintson a Napló csoport jelölőnégyzetére.
8. Kattintson a *Mentés* gombra a diagnosztika naplózásának engedélyezéséhez a Log Analytics-munkaterületre.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Key Vault-diagnosztika engedélyezése a PowerShell használatával
A következő PowerShell-parancsfájl példát `Set-AzDiagnosticSetting` mutat be a Key Vault erőforrás-naplózásának engedélyezéséhez:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Tekintse át az Azure Key Vault adatgyűjtésének részleteit
Az Azure Key Vault-megoldás közvetlenül a Key Vaultból gyűjti a diagnosztikai naplókat.
Nem szükséges a naplókat írni az Azure Blob storage-ba, és nincs szükség ügynökre az adatgyűjtéshez.

Az alábbi táblázat az adatgyűjtési módszereket és az Azure Key Vault adatainak gyűjtésének egyéb részleteit mutatja be.

| Platform | Közvetlen ügynök | Systems Center Operations Manager ügynök | Azure | A műveleti vezetőre van szükség? | Az Operations Manager ügynöke által küldött adatok a felügyeleti csoporton keresztül | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | érkezéskor |

## <a name="use-azure-key-vault"></a>Az Azure Key Vault használata
A [megoldás telepítése](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)után tekintse meg a Key Vault-adatokat az Azure Monitor **áttekintése** lapon a **Key Vault Analytics** csempére kattintva. Nyissa meg ezt a lapot az **Azure Monitor** menüben az Insights szakasz **Egyebek** szakasza További elemre kattintva. **Insights** 

![Az Azure Key Vault csempéjének képe](media/azure-key-vault/log-analytics-keyvault-tile.png)

Miután a **Key Vault Analytics** csempére kattintott, megtekintheti a naplók összegzését, majd részletezheti a következő kategóriák részleteit:

* Az összes key vault-művelet mennyisége az idő múlásával
* Sikertelen műveletkötetek az idő múlásával
* Átlagos működési késés működés szerint
* A szolgáltatás minősége az 1000 ms-ot meghaladó számú művelettel és az 1000 ms-nál több műveletet elhasználó műveletek listájával

![Az Azure Key Vault irányítópultjának képe](media/azure-key-vault/log-analytics-keyvault01.png)

![Az Azure Key Vault irányítópultjának képe](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Bármely művelet részleteinek megtekintése
1. Az **Áttekintés** lapon kattintson a **Key Vault Analytics** csempére.
2. Az **Azure Key Vault** irányítópultján tekintse át az összefoglaló információkat az egyik panelen, majd kattintson az egyikre a naplókeresési lapon a részletes információk megtekintéséhez.

    Bármelyik naplókeresési oldalon megtekintheti az eredményeket az idő, a részletes eredmények és a naplókeresési előzmények szerint. Az eredmények szűkítéséhez a kis- és nagyátaránát is szűrheti.

## <a name="azure-monitor-log-records"></a>Az Azure Monitor naplórekordjai
Az Azure Key Vault-megoldás elemzi azokat a rekordokat, amelyek az Azure Diagnostics [auditevent naplóiból](../../key-vault/general/logging.md) gyűjtött **KeyVaults** típusú rekordokat.  A rekordok tulajdonságai a következő táblázatban találhatók:  

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |A kérelmet bekérő ügyfél IP-címe |
| `Category` | *AuditEvent* |
| `CorrelationId` |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| `DurationMs` |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ez az idő nem tartalmazza a hálózati késést, így előfordulhat, hogy az ügyféloldalon mérhető idő ezúttal nem egyezik. |
| `httpStatusCode_d` |A kérelem által visszaadott HTTP-állapotkód (például *200*) |
| `id_s` |A kérelem egyedi azonosítója |
| `identity_claim_appid_g` | Az alkalmazásazonosító GUID azonosítója |
| `OperationName` |A művelet neve az [Azure Key Vault naplózásában](../../key-vault/general/logging.md) dokumentált módon |
| `OperationVersion` |AZ ügyfél által kért REST API-verzió (például *2015-06-01*) |
| `requestUri_s` |Uri a kérelem |
| `Resource` |A kulcstartó neve |
| `ResourceGroup` |A kulcstartó erőforráscsoportja |
| `ResourceId` |Az Azure Resource Manager szerinti erőforrás-azonosító. A Key Vault-naplók esetében ez a Key Vault erőforrás-azonosítója. |
| `ResourceProvider` |*Microsoft. KEYVAULT KÖZÖTT* |
| `ResourceType` | *Boltívek* |
| `ResultSignature` |HTTP-állapot (például *OK*) |
| `ResultType` |REST API-kérelem eredménye (például *Sikeres*) |
| `SubscriptionId` |A Key Vaultot tartalmazó előfizetés Azure-előfizetés-azonosítója |

## <a name="migrating-from-the-old-key-vault-solution"></a>Áttelepítés a régi Key Vault-megoldásból
2017 januárjában megváltozott a Key Vaultból a Log Analytics szolgáltatásba küldött naplók támogatott módja. Ezek a változások a következő előnyöket biztosítják:
+ A naplók közvetlenül a Log Analytics-munkaterületre kerülnek anélkül, hogy tárfiókot kellene használniuk
+ Kevesebb késés attól az időponttól kezdve, amikor a naplók jönnek létre, hogy elérhetők a Log Analytics
+ Kevesebb konfigurációs lépés
+ Az Azure-diagnosztika minden típusának közös formátuma

A frissített megoldás használata:

1. [A key vaultból közvetlenül a Log Analytics-munkaterületre küldendő diagnosztika konfigurálása](#enable-key-vault-diagnostics-in-the-portal)  
2. Az Azure Key Vault-megoldás engedélyezése az [Azure Monitor-megoldások hozzáadása a Megoldások galériából című](../../azure-monitor/insights/solutions.md) albumban ismertetett folyamat használatával
3. A mentett lekérdezések, irányítópultok és riasztások frissítése az új adattípus használatához
   + Típus módosítása: KeyVaults az AzureDiagnostics. A ResourceType segítségével a Key Vault-naplókszűrés.
   + A következő `KeyVaults`helyett: , használja`AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Mezők: (A mezőnevek ben nincs kis- és nagybetű)
   + Minden olyan mező esetében, amelynek \_ \_neve s, d vagy \_g utótag, módosítsa az első karaktert kisbetűsre
   + Minden olyan mező esetében, amelynek \_neve o utótag, az adatok a beágyazott mezőnevek alapján különálló mezőkre vannak felosztva. Például a hívó fél upn-je egy mezőben`identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + A Field CallerIpAddress címe CallerIPAddress-re változott
   + Field RemoteIPCountry már nincs jelen
4. Távolítsa el a *Key Vault Analytics (elavult)* megoldás. Ha PowerShellt használ, használja a`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

A módosítás előtt gyűjtött adatok nem láthatók az új megoldásban. Az adatok lekérdezése a régi Típus- és mezőnevek használatával folytatódhat.

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>További lépések
* Az [Azure Monitor naplólekérdezései](../../azure-monitor/log-query/log-query-overview.md) segítségével részletes Azure Key Vault-adatokat tekinthet meg.
