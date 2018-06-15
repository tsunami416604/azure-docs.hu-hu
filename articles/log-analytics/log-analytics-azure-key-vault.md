---
title: Az Azure Key Vault megoldás Naplóelemzési |} Microsoft Docs
description: Log Analytics az Azure Key Vault megoldás használatával tekintse át az Azure Key Vault naplóinak.
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
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
ms.openlocfilehash: 9c4b16ec11d1990de687014c5385314f0e0c602a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30238392"
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Log Analytics az Azure Key Vault Analytics megoldás

![Key Vault szimbólum](./media/log-analytics-azure-keyvault/key-vault-analytics-symbol.png)

A Log Analyticsben az Azure Key Vault megoldással áttekintheti az Azure Key Vault AuditEvent-naplókat.

A megoldás használatához meg kell az Azure Key Vault diagnosztikai naplózás engedélyezése és a Naplóelemzési munkaterület diagnosztika közvetlen. Nincs szükség a naplók írni az Azure Blob Storage tárolóban.

> [!NOTE]
> 2017. január, a Key Vault naplókat küldeni Naplóelemzési támogatott módon módosítani. A Key Vault megoldás használata mutatja *(elavult)* a cím vonatkozik [áttelepítése a régi Key Vault megoldás](#migrating-from-the-old-key-vault-solution) lépéseit kövesse.
>
>

## <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
A következő utasításokat követve telepítse és konfigurálja az Azure Key Vault megoldást:

1. Engedélyezze az Azure Key Vault megoldást [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).
2. Segítségével a Key Vault-erőforrások figyelése, diagnosztikai naplózás engedélyezése a [portal](#enable-key-vault-diagnostics-in-the-portal) vagy [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>A portál Key Vault diagnosztika engedélyezése

1. Az Azure-portálon lépjen a figyelheti a Key Vault erőforrás
2. Válassza ki *diagnosztikai naplók* a következő lap megnyitása

   ![az Azure Key Vault csempe képe](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kattintson a *a diagnosztika bekapcsolásához* a következő lap megnyitása

   ![az Azure Key Vault csempe képe](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics02.png)
4. A diagnosztika bekapcsolásához kattintson *a* alatt *állapota*
5. Kattintson a jelölőnégyzetbe *Naplóelemzési küldése*
6. Válasszon ki egy meglévő Naplóelemzési munkaterület, vagy egy munkaterület létrehozása
7. Ahhoz, hogy *AuditEvent* naplókat, kattintson a jelölőnégyzetbe, a napló
8. Kattintson a *mentése* szolgáltatáshoz diagnosztikai naplózás engedélyezése

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Engedélyezze a Key Vault diagnosztika PowerShell használatával
A következő PowerShell-parancsfájl egy példát használata `Set-AzureRmDiagnosticSetting` Key vault diagnosztikai naplózás engedélyezése:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Tekintse át az Azure Key Vault az gyűjtemény adatait
Az Azure Key Vault megoldás közvetlenül a Key Vault a diagnosztikai naplók gyűjti.
Nincs szükség a naplók az Azure Blob storage írni, és nincs ügynök szükséges adatok gyűjtését.

A következő táblázat adatgyűjtési módszerek és egyéb adatok gyűjtése hogyan Azure Key Vault részleteit.

| Platform | Közvetlen ügynök | Systems Center Operations Manager-ügynök | Azure | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | érkezésükkor |

## <a name="use-azure-key-vault"></a>Az Azure Key Vault használata
Miután [a megoldás telepítése](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), a Key Vault adatok megtekintéséhez kattintson a **Azure Key Vault** a csempére a **áttekintése** Naplóelemzési oldalán.

![az Azure Key Vault csempe képe](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Miután rákattintott a **áttekintése** csempe, a naplók összegzéseinek megtekintése, és ezután adatpontra az alábbi kategóriákban:

* Az összes kulcstároló művelet időbeli kötet
* Nem sikerült a művelet kötetek adott idő alatt
* Átlagos műveleti várakozási művelet
* A műveletek, amelyek több mint 1000 ms-műveletek száma és a listáját, amelyek több mint 1000 ms műveletek szolgáltatásminőség

![az Azure Key Vault irányítópult képe](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![az Azure Key Vault irányítópult képe](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Bármely művelet részleteinek megtekintése
1. Az a **áttekintése** lapján kattintson a **Azure Key Vault** csempére.
2. Az a **Azure Key Vault** irányítópult, ellenőrizze az összefoglaló információkat a paneleken valamelyikével, és kattintson egy, a napló lapon részletes információkat megtekintéséhez.

    A naplófájl-keresési lapok egyikén tekintheti eredmények idő, illetve részletes leírást és a keresési korábbi naplók. Az eredmények szűkítéséhez értékkorlátozással is szűrhet.

## <a name="log-analytics-records"></a>Log Analytics-rekordok
Az Azure Key Vault megoldás elemzi az azt jelzi, hogy rendelkezik olyan típusú **KeyVaults** , hogy a rendszer begyűjti az [AuditEvent naplók](../key-vault/key-vault-logging.md) az Azure Diagnostics.  Ezeket a rekordokat tulajdonságainak vannak a következő táblázatban:  

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |A kérést leadó ügyfél IP-címe |
| Kategória | *AuditEvent* |
| CorrelationId |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| DurationMs |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Most nem tartalmazza a hálózati késés, így az ügyféloldalon mérni idő nem egyeznek meg a megadott idő. |
| httpStatusCode_d |A kérelem által visszaadott HTTP-állapotkódot (például *200*) |
| id_s |A kérelem egyedi azonosítója |
| identity_claim_appid_g | Az alkalmazásazonosító GUID azonosítója |
| OperationName |A művelet, ahogy neve [Azure Key Vault naplózása](../key-vault/key-vault-logging.md) |
| OperationVersion |Az ügyfél által kért REST API-verzió (például *2015-06-01*) |
| requestUri_s |A kérelem URI-val |
| Erőforrás |A kulcstároló neve |
| ResourceGroup |A key vault erőforrás-csoport |
| ResourceId |Az Azure Resource Manager szerinti erőforrás-azonosító. Key Vault naplóinak ez pedig a Key Vault erőforrás-azonosító. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |HTTP-állapot (például *OK*) |
| ResultType |REST API-kérelem eredménye (például *sikeres*) |
| SubscriptionId |Az előfizetés, amely tartalmazza a Key Vault Azure-előfizetése Azonosítóját |

## <a name="migrating-from-the-old-key-vault-solution"></a>A régi Key Vault megoldás áttelepítése
2017. január, a Key Vault naplókat küldeni Naplóelemzési támogatott módon módosítani. Ezeket a változásokat a következő előnyöket biztosítja:
+ Írja a naplókat közvetlenül Naplóelemzési használja a storage-fiók nélkül
+ A naplók hozzájuk legyenek elérhetők a Naplóelemzési létrehozásának óta kevesebb késés
+ Kevesebb konfigurációs lépések
+ Az összes Azure diagnostics közös formátum

A frissített megoldás használata:

1. [Key Vault közvetlenül a Log Analyticshez való küldésének diagnosztika konfigurálása](#enable-key-vault-diagnostics-in-the-portal)  
2. Engedélyezze az Azure Key Vault-megoldás a ismertetett folyamatot [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md)
3. Bármely lekérdezések, irányítópultok vagy használni az új adattípusra riasztások frissítése
  + Típus: változás: az AzureDiagnostics KeyVaults. A Key Vault naplóinak szűrése használhatja az erőforrástípus.
  - Ahelyett, hogy: `KeyVaults`, használata `AzureDiagnostics | where ResourceType'=="VAULTS"`
  + Mezők: (mezőnevek számítanak a kis-és nagybetűket)
  - Bármely mezőhöz, amely rendelkezik egy utótagja \_s, \_d, vagy \_nevét, a g kisbetű módosítsa az első karakter
  - Bármely mezőhöz, amely rendelkezik egy utótagja \_o a neve, az adatok egy egyéni mezők beágyazott mező neve alapján van osztva. Például az egyszerű felhasználónév a hívó mező tárolva van `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - A mező CallerIpAddress CallerIPAddress változott
   - A mező RemoteIPCountry már nincs jelen
4. Távolítsa el a *Key Vault Analytics (elavult)* megoldás. Ha a PowerShell használata esetén `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Mielőtt a változás nem jelenik meg az új megoldás összegyűjtött adatok. Továbbra is a régi típusú és mezőnevek ezeket az adatokat lekérdezni.

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>További lépések
* Használjon [Log Analytics-e jelentkezni a keresések](log-analytics-log-searches.md) Azure Key Vault részletes adatainak megtekintéséhez.
