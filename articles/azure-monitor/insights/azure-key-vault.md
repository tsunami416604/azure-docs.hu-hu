---
title: Azure Key Vault megoldás a Azure Monitorban | Microsoft Docs
description: Azure Key Vault naplók áttekintéséhez használhatja a Azure Monitor Azure Key Vault megoldását.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: bwren
ms.openlocfilehash: 1e0e9a0d76e644ec48ecd423a105dd89629d290c
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997692"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault elemzési megoldás a Azure Monitor

![Key Vault szimbólum](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Azure Monitor Azure Key Vault megoldásával áttekintheti Azure Key Vault AuditEvent naplóit.

A megoldás használatához engedélyeznie kell a Azure Key Vault diagnosztika naplózását, és Log Analytics munkaterületre kell irányítani a diagnosztikát. A naplókat nem szükséges az Azure Blob Storage-ba írni.

> [!NOTE]
> Január 2017-án a naplók Key Vaultból való küldésének támogatott módja Log Analytics megváltozott. Ha az Ön által használt Key Vault megoldás a címben *(elavult)* jelenik meg, tekintse át a [Migrálás a régi Key Vault megoldásból](#migrating-from-the-old-key-vault-solution) című témakört a követendő lépésekhez.
>
>

## <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
A Azure Key Vault megoldás telepítéséhez és konfigurálásához kövesse az alábbi utasításokat:

1. A Azure Key Vault megoldás Log Analytics-munkaterülethez való hozzáadásához használja a [Solutions Gallery Azure monitor-megoldások hozzáadása](../../azure-monitor/insights/solutions.md) a-ból című témakörben leírt eljárást.
2. Diagnosztikai naplózás engedélyezése a figyelni kívánt Key Vault-erőforrások számára a [portál](#enable-key-vault-diagnostics-in-the-portal) vagy a [PowerShell](#enable-key-vault-diagnostics-using-powershell) használatával

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Key Vault diagnosztika engedélyezése a portálon

1. A Azure Portal navigáljon a figyelni kívánt Key Vault erőforráshoz
2. Válassza a *diagnosztikai beállítások* elemet a következő oldal megnyitásához

   ![Azure Key Vault csempe képe](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kattintson a *diagnosztika* bekapcsolása elemre a következő oldal megnyitásához

   ![Azure Key Vault csempe képe](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Adjon nevet a diagnosztikai beállításnak.
5. Kattintson a *küldés log Analyticsre* jelölőnégyzetre.
6. Válasszon ki egy meglévő Log Analytics munkaterületet, vagy hozzon létre egy munkaterületet
7. A *AuditEvent* -naplók engedélyezéséhez kattintson a log (napló) alatt lévő jelölőnégyzetre.
8. A *Mentés* gombra kattintva engedélyezheti a diagnosztika naplózását log Analytics munkaterületre.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Key Vault diagnosztika engedélyezése a PowerShell használatával
A következő PowerShell-szkript bemutatja, hogyan `Set-AzDiagnosticSetting` engedélyezhető a Key Vault diagnosztikai naplózása:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Azure Key Vault adatgyűjtési adatok áttekintése
Azure Key Vault a megoldás közvetlenül a Key Vaultból gyűjt diagnosztikai naplókat.
A naplókat nem szükséges az Azure Blob Storage-ba írni, és nem szükséges ügynök az adatgyűjtés során.

Az alábbi táblázat az adatgyűjtés módszereit és a Azure Key Vault adatok gyűjtésének egyéb részleteit mutatja be.

| Platform | Közvetlen ügynök | System Center Operations Manager Agent | Azure | Operations Manager kötelező? | A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | érkezéskor |

## <a name="use-azure-key-vault"></a>Az Azure Key Vault használata
A [megoldás telepítése](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)után tekintse meg a Key Vault adatait az Azure monitor **áttekintés** oldalának **Key Vault Analytics** csempére kattintva. Nyissa meg ezt a lapot a **Azure monitor** menüjéből, és kattintson a **továbbiak** elemre az **áttekintések** szakaszban. 

![Azure Key Vault csempe képe](media/azure-key-vault/log-analytics-keyvault-tile.png)

Miután rákattintott a **Key Vault Analytics** csempére, megtekintheti a naplók összefoglalóit, és a következő kategóriákban részletezheti a részleteket:

* Az összes Key Vault-művelet mennyisége az idő múlásával
* Sikertelen művelet-kötetek az idő függvényében
* Átlagos működési késés a művelet szerint
* A szolgáltatás minősége a 1000 MS-nál nagyobb mennyiségű műveletekkel és a több mint 1000 MS-nál nagyobb műveletek listájának üzemeltetéséhez

![Azure Key Vault irányítópult képe](media/azure-key-vault/log-analytics-keyvault01.png)

![Azure Key Vault irányítópult képe](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Bármely művelet részleteinek megtekintése
1. Az **Áttekintés** lapon kattintson a **Key Vault Analytics** csempére.
2. Az **Azure Key Vault** irányítópulton tekintse át az egyik penge összefoglaló adatait, majd kattintson az egyikre a részletes információk megtekintéséhez a naplóbeli keresés oldalon.

    Bármelyik naplóbeli keresési oldalon megtekintheti az eredményeket idő szerint, részletes eredményekkel és a naplóbeli keresési előzményekkel. Az eredmények szűkítéséhez az aspektusok alapján is szűrheti az eredményeket.

## <a name="azure-monitor-log-records"></a>Naplóbejegyzések Azure Monitor
A Azure Key Vault megoldás elemzi azokat a rekordokat, amelyek a Azure Diagnostics [AuditEvent](../../key-vault/key-vault-logging.md) -naplóiból gyűjtött típusú kulcstartókkal rendelkeznek.  A rekordok tulajdonságai a következő táblázatban találhatók:  

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |A kérést küldő ügyfél IP-címe |
| `Category` | *AuditEvent* |
| `CorrelationId` |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| `DurationMs` |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ez az idő nem tartalmazza a hálózati késést, így előfordulhat, hogy az ügyfél oldalán mért idő nem egyezik meg ezzel az idővel. |
| `httpStatusCode_d` |A kérelem által visszaadott HTTP-állapotkód (például *200*) |
| `id_s` |A kérelem egyedi azonosítója |
| `identity_claim_appid_g` | Az alkalmazás AZONOSÍTÓjának GUID azonosítója |
| `OperationName` |A művelet neve [Azure Key Vault naplózásban](../../key-vault/key-vault-logging.md) dokumentálva |
| `OperationVersion` |Az ügyfél által kért REST API-verzió (például *2015-06-01*) |
| `requestUri_s` |A kérelem URI-ja |
| `Resource` |A Key Vault neve |
| `ResourceGroup` |A Key Vault erőforráscsoport |
| `ResourceId` |Az Azure Resource Manager szerinti erőforrás-azonosító. Key Vault naplók esetében ez a Key Vault erőforrás-azonosító. |
| `ResourceProvider` |*MICROSOFT.KEYVAULT* |
| `ResourceType` | *VAULTS* |
| `ResultSignature` |HTTP-állapot (például *OK*) |
| `ResultType` |REST API kérelem eredménye (például *sikeres*) |
| `SubscriptionId` |Az Key Vaultt tartalmazó előfizetés Azure-előfizetési azonosítója |

## <a name="migrating-from-the-old-key-vault-solution"></a>Áttelepítés a régi Key Vault megoldásból
Január 2017-án a naplók Key Vaultból való küldésének támogatott módja Log Analytics megváltozott. Ezek a változások a következő előnyöket nyújtják:
+ A naplók közvetlenül egy Log Analytics munkaterületre íródnak, és nem kell használni a Storage-fiókot
+ Kevesebb késés attól az időponttól kezdve, amikor a naplók generálva lesznek Log Analytics
+ Kevesebb konfigurációs lépés
+ Az Azure Diagnostics összes típusának általános formátuma

A frissített megoldás használata:

1. [A diagnosztika konfigurálása egy Log Analytics munkaterületre való közvetlen elküldése Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Engedélyezze a Azure Key Vault megoldást az [Solutions Gallery Azure monitor-megoldások hozzáadása](../../azure-monitor/insights/solutions.md) című témakörben ismertetett eljárás használatával.
3. A mentett lekérdezések, irányítópultok vagy riasztások frissítése az új adattípus használatára
   + A típus a következőtől változik: Kulcstartók a AzureDiagnostics. A ResourceType használatával szűrheti Key Vault naplókat.
   + A: `KeyVaults`helyett használja a`AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Mezők (A mezők nevei megkülönböztetik a kis-és nagybetűket)
   + Minden olyan mezőnél, amely a névben \_s, \_d vagy \_g utótaggal rendelkezik, módosítsa az első karaktert a kisbetű értékre.
   + Minden olyan mezőnél, amelynél a \_név utótagja szerepel, az adat a beágyazott mezők nevei alapján egyedi mezőkbe van bontva. A hívó egyszerű felhasználóneve például egy mezőben tárolódik.`identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + A mező CallerIpAddress CallerIPAddress módosult
   + A RemoteIPCountry mező már nem létezik
4. Távolítsa el a *Key Vault Analytics (elavult)* megoldást. Ha a PowerShellt használja, használja a`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

A módosítás előtt gyűjtött adatok nem láthatók az új megoldásban. Továbbra is lekérdezheti ezeket az adattípusokat a régi típusú és mezőnevek használatával.

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>További lépések
* A részletes Azure Key Vault-információk megtekintéséhez használja [a Azure monitor](../../azure-monitor/log-query/log-query-overview.md) a naplózási lekérdezéseket.
