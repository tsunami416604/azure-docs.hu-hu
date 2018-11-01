---
title: Az Azure Key Vault megoldás a Log Analyticsben |} A Microsoft Docs
description: Az Azure Key Vault megoldás a Log Analytics segítségével áttekintheti az Azure Key Vault-naplók.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: richrund
ms.component: ''
ms.openlocfilehash: be0a12bbb84db1e4016bd7126a38e750e6c0c3ad
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412822"
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Az Azure Key Vault Analytics megoldás a Log Analyticsben

![A Key Vault szimbólum](media/log-analytics-azure-key-vault/key-vault-analytics-symbol.png)

A Log Analyticsben az Azure Key Vault megoldással áttekintheti az Azure Key Vault AuditEvent-naplókat.

A megoldás használatához meg kell az Azure Key Vault diagnosztikai naplózás engedélyezése és a Log Analytics-munkaterület diagnosztika közvetlen. Nem kell írni a naplókat az Azure Blob storage.

> [!NOTE]
> A 2017 január a naplók küldésére a Key Vaultból Log Analytics támogatott módon módosítani. Ha a Key Vault megoldás segítségével jeleníti meg *(elavult)* a cím tekintse meg [való migrálás a régi Key Vault megoldásról](#migrating-from-the-old-key-vault-solution) a lépéseket kell követnie.
>
>

## <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
Kövesse az alábbi utasításokat, telepítése és konfigurálása az Azure Key Vault megoldásról:

1. Engedélyezze az Azure Key Vault megoldás a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) vagy leírt folyamatot követve [adja hozzá a Log Analytics solutions kövesse a megoldástárban](log-analytics-add-solutions.md).
2. Használatával a Key Vault erőforrások monitorozási, diagnosztikai célú naplózásának engedélyezése a [portál](#enable-key-vault-diagnostics-in-the-portal) vagy [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>A Key Vault-diagnosztika használata a portálon

1. Az Azure Portalon lépjen a Key Vault erőforrás figyelése
2. Válassza ki *diagnosztikai naplók* nyissa meg a következő

   ![az Azure Key Vault csempét ábrázoló kép](media/log-analytics-azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kattintson a *diagnosztika bekapcsolása* nyissa meg a következő

   ![az Azure Key Vault csempét ábrázoló kép](media/log-analytics-azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Engedélyezze a diagnosztikát, kattintson a *a* alatt *állapota*
5. Kattintson a jelölőnégyzetre a *Küldés a Log Analyticsnek*
6. Válasszon ki egy meglévő Log Analytics-munkaterületet, vagy hozzon létre egy munkaterületet
7. Ahhoz, hogy *AuditEvent* naplókat, kattintson a jelölőnégyzetre a napló
8. Kattintson a *mentése* a Log Analytics diagnosztikai naplózás engedélyezése

### <a name="enable-key-vault-diagnostics-using-powershell"></a>A diagnosztika a Key Vault PowerShell-lel
A következő PowerShell-parancsprogram azt szemlélteti, hogyan használható `Set-AzureRmDiagnosticSetting` a Key vault diagnosztikai naplózás engedélyezése:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Tekintse át az Azure Key Vault adatok gyűjtése
Az Azure Key Vault megoldás gyűjti a diagnosztikai naplók közvetlenül a Key vaultból.
Nem szükséges a naplók írni az Azure Blob storage és nem az ügynök nem szükséges az adatgyűjtés.

Az alábbi táblázat adatgyűjtési módszerek és egyéb hogyan adatgyűjtés az Azure Key Vault részleteit.

| Platform | Közvetlen ügynök | Systems Center Operations Manager-ügynök | Azure | Az Operations Manager szükséges? | A felügyeleti csoport Operations Manager-ügynök adatok küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | a beérkezéskor |

## <a name="use-azure-key-vault"></a>Az Azure Key Vault használata
Miután [a megoldás telepítése](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), a Key Vault adatainak megtekintéséhez kattintson a **Azure Key Vault** a csempe a **áttekintése** Log Analytics lapján.

![az Azure Key Vault csempét ábrázoló kép](media/log-analytics-azure-key-vault/log-analytics-keyvault-tile.png)

Miután rákattintott a **áttekintése** csempét, a naplók összegzéseinek megtekintése, és ezután jelenítse meg a részleteket az alábbi kategóriákban:

* Idővel az összes key vault-műveletek mennyiségét
* Nem sikerült a művelet kötetek időbeli alakulása
* Művelet átlagos műveleti késés
* A műveletek, amelyek több mint 1000 ms műveletek száma és a műveletek, amelyek több mint 1000 ms listáját a szolgáltatásminőség

![az Azure Key Vault-irányítópult képe](media/log-analytics-azure-key-vault/log-analytics-keyvault01.png)

![az Azure Key Vault-irányítópult képe](media/log-analytics-azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Bármely művelet részleteinek megtekintése
1. Az a **áttekintése** lap, kattintson a **Azure Key Vault** csempére.
2. Az a **Azure Key Vault** irányítópult, tekintse át az összefoglaló adatokat az egyik a paneleket, majd kattintson az egyik kapcsolatos részletes információk megtekintéséhez a keresési oldalon.

    Bármelyik, log search, az eredmények megtekintéséhez idő, a részletes eredmények és a napló keresési előzmények. Értékkorlátozással szűkítheti az eredmények alapján is szűrheti.

## <a name="log-analytics-records"></a>Log Analytics-rekordok
Az Azure Key Vault megoldás elemzi a rekord, amelynek típusa a **KeyVaults** , hogy a rendszer begyűjti az [AuditEvent-naplókat](../key-vault/key-vault-logging.md) az Azure Diagnostics.  Ezek a rekordok tulajdonságait az alábbi táblázatban a következők:  

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |A kérést leadó ügyfél IP-címe |
| Kategória | *AuditEvent* |
| CorrelationId |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| durationMs |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ezúttal nem tartalmazza a hálózati késés, így előfordulhat, hogy az idő az ügyféloldalon mérő nem egyezik a most. |
| httpStatusCode_d |A kérelem által visszaadott HTTP-állapotkódot (például *200*) |
| id_s |A kérelem egyedi azonosítója |
| identity_claim_appid_g | GUID Azonosítóját az alkalmazás azonosítója. |
| OperationName |A művelet, dokumentált módon neve [Azure Key Vault naplózása](../key-vault/key-vault-logging.md) |
| operationVersion |Az ügyfél által kért REST API-verzió (például *2015-06-01*) |
| requestUri_s |A kérelem URI azonosítója |
| Erőforrás |A kulcstároló nevét |
| ResourceGroup |A kulcstároló erőforráscsoport |
| ResourceId |Az Azure Resource Manager szerinti erőforrás-azonosító. A Key Vault naplóihoz Ez az a Key Vault erőforrás-azonosítója. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |HTTP-állapot (például *OK*) |
| ResultType |REST API-kérelem eredménye (például *sikeres*) |
| SubscriptionId |A Key Vault tartalmazó előfizetés Azure-előfizetés azonosítója |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrálás a régi Key Vault megoldásról
A 2017 január a naplók küldésére a Key Vaultból Log Analytics támogatott módon módosítani. Ezek a módosítások a következő előnyöket biztosítják:
+ Naplók írt közvetlenül a Log Analytics használata a storage-fiók nélkül
+ Az idő, amikor jönnek létre számukra legyenek elérhetők a Log Analytics naplók a kisebb késés
+ Kevesebb konfigurációs lépéssel
+ Az Azure diagnostics minden alkalmazástípus esetében egy gyakran alkalmazott formátum

A frissített megoldás használata:

1. [A Key Vaultból közvetlenül a Log Analyticshez való küldésének diagnosztika konfigurálása](#enable-key-vault-diagnostics-in-the-portal)  
2. Az Azure Key Vault megoldás engedélyezése leírt folyamatot követve [kövesse a megoldástárban adja hozzá a Log Analytics-megoldások](log-analytics-add-solutions.md)
3. Bármely mentett lekérdezések, az irányítópultok vagy a riasztások az új adattípus használandó frissítése
  + Típus módosítása a: AzureDiagnostics való KeyVaults. Az erőforrástípus segítségével szűrheti a Key Vault-naplók.
  - Helyett: `KeyVaults`, használata `AzureDiagnostics | where ResourceType'=="VAULTS"`
  + Mezők: (mezőnevek számítanak a kis-és nagybetűket)
  - Bármely mező, amely rendelkezik, amik utótagja \_s, \_d, vagy \_a nevét, a g módosítása az első karakter kisbetűsre
  - Bármely mező, amely rendelkezik, amik utótagja \_o a neve, az adatok az egyes mezők a beágyazott mezők neve alapján van felosztva. Például az egyszerű felhasználónév a hívó mező tárolva van `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - A mező CallerIpAddress CallerIPAddress értékre módosult
   - A mező RemoteIPCountry már nem található
4. Távolítsa el a *Key Vault Analytics (elavult)* megoldás. Ha a Powershellt használ, használja a `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Összegyűjtött adatok, mielőtt a változás nem jelenik meg az új megoldásba. Továbbra is a régi típusú és a mezőnevek ezen adatok lekérdezéséhez.

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>További lépések
* Használat [Log Analytics naplóbeli kereséseivel](log-analytics-log-searches.md) Azure Key Vault részletes adatainak megtekintéséhez.
