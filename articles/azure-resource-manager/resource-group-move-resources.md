---
title: Azure-erőforrások áthelyezése új vagy erőforráscsoportonként csoportba |} A Microsoft Docs
description: Azure Resource Manager segítségével az erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/23/2018
ms.author: tomfitz
ms.openlocfilehash: 27b41655d9a6c9000d9bc3cf98bf3246bb108104
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015551"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe

Ez a cikk bemutatja, hogyan kell helyeznie az erőforrásokat, vagy egy új előfizetést, vagy egy új erőforráscsoport ugyanabban az előfizetésben. A portal, PowerShell, az Azure CLI vagy a REST API segítségével erőforrás áthelyezése. Ez a cikk az áthelyezési műveletek lesznek elérhetők minden olyan segítség nélkül az Azure-támogatás.

Erőforrások áthelyezésekor mind a forrás és a cél csoport zárolva vannak a művelet során. Írási és törlési műveletek az áthelyezés befejezéséig az erőforráscsoportok elakad. A zárolás azt jelenti, hogy a nem hozzáadása, frissítése vagy törlése az erőforráscsoportok erőforrásaihoz, de ez nem jelenti azt, az erőforrások szüneteltetve legyenek. Ha például egy SQL Server és az adatbázis áthelyezése egy új erőforráscsoportot, ha nem az adatbázist használó alkalmazások teljesen állásidő nélkül. Továbbra is olvasni és írni az adatbázisba.

Az erőforrás helye nem módosítható. Erőforrások áthelyezése csak áthelyezi egy új erőforráscsoportot. Az új erőforráscsoportot egy másik helyre azonban lehet, hogy az erőforrás helye nem változik.

> [!NOTE]
> Ez a cikk ismerteti, hogyan helyezheti át egy meglévő Azure-erőforrások fiók ajánlatát. Ha valójában módosítani szeretné az Azure-fiókkal (például az ingyenes frissítése használatalapú fizetéses előfizetésre) kínál az előfizetés konvertálnia kell.
> * Ingyenes próbaverzió frissítése, lásd: [az ingyenes próba- vagy a Microsoft Imagine Azure-előfizetés frissítése használatalapú fizetésre](..//billing/billing-upgrade-azure-subscription.md).
> * Ha módosítani szeretné egy használatalapú fizetéses fiókra, lásd: [az Azure használatalapú fizetéses előfizetésre váltani egy másik ajánlatra](../billing/billing-how-to-switch-azure-offer.md).
> * Ha az előfizetést, nem konvertálhatóak [hozzon létre egy Azure-támogatáskérést](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki **előfizetés-kezelési** issue type számára.

## <a name="checklist-before-moving-resources"></a>Erőforrások áthelyezése előtti ellenőrzőlistát

Néhány fontos lépést végre kell hajtani az erőforrások áthelyezése előtt. Ezen feltételek ellenőrzésével a hibák elkerülhetőek.

1. A forrás- és az előfizetések léteznie kell az előfizetésen belül [Azure Active Directory-bérlő](../active-directory/develop/quickstart-create-new-tenant.md). Ellenőrizze, hogy mindkét előfizetéshez tartozik-e az azonos bérlő azonosítója, használja az Azure PowerShell vagy az Azure CLI.

  Azure PowerShell esetén használja:

  ```azurepowershell-interactive
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Azure CLI esetén használja az alábbi parancsot:

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Ha a bérlőazonosítók a forrás- és előfizetés esetében nem ugyanaz, a következő módszerek használatával a bérlőazonosítók egyeztetése:

  * [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md)
  * [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A cél előfizetést regisztrálni kell az áthelyezett erőforrás erőforrás-szolgáltatóján. Ha nem, akkor megjelenik egy hibaüzenet, arról, hogy a **az előfizetés nincs regisztrálva az erőforrástípushoz**. Ez a probléma erőforrások új előfizetésre történő áthelyezésekor fordulhat elő, ha az előfizetést még nem használták az adott erőforrástípushoz.

  A PowerShell a következő parancsok használatával a regisztrációs állapot lekérdezése:

  ```azurepowershell-interactive
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Erőforrás-szolgáltató regisztrálásához használja:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Azure CLI-hez a következő parancsok használatával a regisztrációs állapot lekérdezése:

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Erőforrás-szolgáltató regisztrálásához használja:

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

1. Erőforrások áthelyezése a fióknak legalább a következő engedélyekkel kell rendelkeznie:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** a a forrásként szolgáló erőforráscsoportot.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** a cél erőforráscsoport található.

1. Erőforrások áthelyezése előtt tekintse át az erőforrásokat az előfizetés az előfizetési kvóták. Erőforrások áthelyezése azt jelenti, hogy az előfizetés túl fogja lépni a teljesítménye korlátait, ha meg kell tekintenie a e kérheti a kvótájának növelését. Korlátok, és hogyan lehet növelni listáját lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).

1. Ha lehetséges, nagy break helyezi át a külön áthelyezési műveleteket. Erőforrás-kezelő azonnal egyetlen művelettel több mint 800 erőforrások áthelyezéséhez kísérlet sikertelen lesz. Azonban legalább 800 erőforrások áthelyezése is meghiúsulhat időtúllépés által.

1. A szolgáltatásnak lehetővé kell tennie az erőforrások áthelyezését. Határozza meg, hogy az áthelyezés sikeres lesz, hogy [ellenőrzése az áthelyezési kérelemben](#validate-move). Ebben a cikkben, amelyek szakaszokban [erőforrások áthelyezését engedélyező szolgáltatásokat](#services-that-can-be-moved) , és amely [erőforrások áthelyezését engedélyező szolgáltatásokat nem](#services-that-cannot-be-moved).

## <a name="when-to-call-support"></a>Ha az ügyfélszolgálat

Továbbléphet a legtöbb erőforrásai a jelen cikkben ismertetett önkiszolgáló műveletekhez. Az önkiszolgáló műveletekhez való használata:

* Resource Manager-erőforrások áthelyezése.
* Klasszikus erőforrások áthelyezése a [klasszikus üzembe helyezési korlátozásoknak](#classic-deployment-limitations).

Kapcsolattartó [támogatja](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) kell tennie:

* Erőforrások áthelyezése új Azure-fiókba (és az Azure Active Directory-bérlő), és az utasítások az előző szakaszban segítségre van szüksége.
* Klasszikus erőforrások áthelyezésekor merülnek fel a korlátozások.

## <a name="validate-move"></a>Áthelyezésének ellenőrzése

A [áthelyezési művelet érvényesítése](/rest/api/resources/resources/validatemoveresources) az áthelyezési forgatókönyv teszteléséhez ténylegesen az erőforrások áthelyezése nélkül teszi lehetővé. Ez a művelet segítségével határozhatja meg, ha az áthelyezés sikeres lesz. Ez a művelet végrehajtásához szükséges a:

* a forrás erőforráscsoport nevét
* erőforrás-azonosító a célként megadott erőforráscsoport
* erőforrás-azonosító az egyes erőforrások áthelyezése
* a [hozzáférési jogkivonat](/rest/api/azure/#acquire-an-access-token) fiókja

A következő kérelem küldése:

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

A kéréstörzs:

```json
{
 "resources": ['<resource-id-1>', '<resource-id-2>'],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Ha a kérés formátuma megfelelő, a művelet adja vissza:

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

A 202-es állapotkód jelzi az ellenőrzési kérést elfogadták, de még nem még meg ha az áthelyezés sikeres lesz. A `location` érték tartalmaz egy URL-címet, amellyel a hosszú ideig futó művelet állapotának ellenőrzéséhez.  

Ellenőrizheti az állapotot, a következő kérelem küldése:

```
GET <location-url>
Authorization: Bearer <access-token>
```

A művelet továbbra is fut, miközben továbbra is megjelenik a 202-es állapotkódot. Várjon jelzi a másodpercek számát a `retry-after` értéket, majd próbálkozzon újra. Ha az áthelyezés érvényesítése sikeres, a 204 állapotkód jelenik meg. Ha az áthelyezés érvényesítése sikertelen, kap egy hibaüzenet, például:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="services-that-can-be-moved"></a>Áthelyezhető szolgáltatások

Az alábbi lista egy új erőforráscsoportot és egy előfizetést is áthelyezhető Azure-szolgáltatások általános összegzését tartalmazza. Nagyobb részletességgel, lásd: [művelet támogatási erőforrások áthelyezése](move-support-resources.md).

* Analysis Services
* API Management
* Az App Service apps (webalkalmazások) – lásd: [App Service korlátai](#app-service-limitations)
* App Service-tanúsítvány
* Application Insights
* Automation
* Azure Active Directory B2C
* Azure Cosmos DB
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Az Azure DevOps - szervezetek számára az Azure DevOps nem Microsoft-bővítmény vásárlása kell [megszakítja a vásárlások](https://go.microsoft.com/fwlink/?linkid=871160) előtt azok is a fiók áthelyezése előfizetések között.
* Azure Maps
* Azure Relay
* Az Azure Stack - regisztrációk
* Batch
* BizTalk Services
* Bot Service
* Tartalomkézbesítési hálózat (CDN)
* Cloud Services – lásd: [klasszikus üzembe helyezési korlátozásoknak](#classic-deployment-limitations)
* Cognitive Services
* Container Registry – nem lehet áthelyezni egy tároló-beállításjegyzéket, ha a georeplikáció engedélyezve van.
* Tartalommoderátor
* Cost Management
* Customer Insights
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* Event Hubs
* Bejárati ajtajának
* Tekintse meg a HDInsight-fürtök – [HDInsight korlátozások](#hdinsight-limitations)
* Iot Central
* IoT Hubok
* Key Vault
* Terheléselosztók – lásd: [Load Balancer-korlátozások](#lb-limitations)
* Log Analytics
* Logic Apps
* Machine Learning - webszolgáltatások helyezheti át egy erőforráscsoport ugyanabban az előfizetésben, de nem egy másik előfizetésben található Machine Learning Studióban. Egyéb Machine Learning-erőforrások áthelyezhetők, előfizetések között.
* Tekintse meg a Managed Disks - [megkötések virtuális gépek korlátozásai](#virtual-machines-limitations)
* Felügyelt identitás - felhasználó által hozzárendelt
* Media Services
* Notification Hubs
* Operational Insights
* Operations Management
* Portál irányítópultok
* A Power BI - mind a Power BI Embedded és a Power BI-munkaterület-csoport
* Nyilvános IP-Címek – lásd: [nyilvános IP-korlátozások](#pip-limitations)
* Az Azure Cache redis - Ha az Azure Cache Redis-példány van konfigurálva, és a virtuális hálózat, a példány nem helyezhető át másik előfizetésbe. Lásd: [virtuális hálózatok korlátozások](#virtual-networks-limitations).
* Scheduler
* Keresés
* Service Bus
* Service Fabric
* Service Fabric Mesh
* SignalR szolgáltatás
* Storage - tárfiókok különböző régiókban lévő műveletben nem lehet áthelyezni. Ehelyett használjon minden régióhoz külön műveletnek.
* Tekintse meg a tároló (klasszikus) – [klasszikus üzembe helyezési korlátozásoknak](#classic-deployment-limitations)
* Stream Analytics - feladatok nem lehet áthelyezni, ha a futó Stream Analytics állapot.
* Az SQL Database server - adatbázis és a kiszolgáló ugyanabban az erőforráscsoportban kell lennie. Ha áthelyezi SQL-kiszolgáló, az összes hozzá tartozó adatbázisok is kerülnek. Ez a viselkedés az Azure SQL Database és az Azure SQL Data Warehouse-adatbázisok vonatkozik.
* Time Series Insights
* Traffic Manager
* Virtuális gépek – a virtuális gépek felügyelt lemezek, lásd: [virtuális gépek korlátozások](#virtual-machines-limitations)
* Tekintse meg a virtuális gépek (klasszikus) – [klasszikus üzembe helyezési korlátozásoknak](#classic-deployment-limitations)
* Tekintse meg a Virtual Machine Scale Sets – [virtuális gépek korlátozások](#virtual-machines-limitations)
* Tekintse meg a virtuális hálózatok - [virtuális hálózatok korlátozások](#virtual-networks-limitations)
* VPN Gateway

## <a name="services-that-cannot-be-moved"></a>Szolgáltatások, amelyek nem lehet áthelyezni

Az alábbi lista nem lehet áthelyezni egy új erőforráscsoportot és egy előfizetést az Azure-szolgáltatások általános összegzését tartalmazza. Nagyobb részletességgel, lásd: [művelet támogatási erőforrások áthelyezése](move-support-resources.md).

* AD Domain Services
* Hibrid AD Állapotfigyelő szolgáltatás
* Application Gateway
* Az Azure adatbázis-Migrálás
* Azure Databricks
* Azure Migrate
* Batch AI
* Tanúsítványok – App Service-tanúsítványok is áthelyezhetők, de a feltöltött tanúsítványok [korlátozások](#app-service-limitations).
* Tárolópéldányok
* Container Service
* Data Box
* Fejlesztői tárolóhelyek
* Dynamics LCS
* Express Route
* Kubernetes Service
* A Lab Services – áthelyezése új erőforráscsoportba ugyanahhoz az előfizetéshez engedélyezve van, de az előfizetés közötti áthelyezése nem engedélyezett.
* Terheléselosztók – lásd: [Load Balancer-korlátozások](#lb-limitations)
* Felügyelt alkalmazások
* Microsoft Genomics
* NetApp
* Nyilvános IP-Címek – lásd: [nyilvános IP-korlátozások](#pip-limitations)
* Recovery Services-tároló – még nincs társítva a Recovery Services-tároló a számítási, hálózati és tárolási erőforrások áthelyezése, lásd: [Recovery Servicesre vonatkozó korlátozásokat](#recovery-services-limitations).
* Azure-beli SAP HANA-szolgáltatás
* Biztonság
* Site Recovery
* StorSimple-Eszközkezelő
* Tekintse meg a virtuális hálózatok (klasszikus) – [klasszikus üzembe helyezési korlátozásoknak](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Virtuális gépek korlátozások

2018. szeptember 24 áthelyezése a felügyelt lemezek támogatottak. 

1. A forrás-előfizetés Ez a funkció regisztrálása.

  ```azurepowershell-interactive
  Register-AzureRmProviderFeature -FeatureName ManagedResourcesMove -ProviderNamespace Microsoft.Compute
  ```

  ```azurecli-interactive
  az feature register --namespace Microsoft.Compute --name ManagedResourcesMove
  ```

1. A regisztrációs kérés kezdetben adja vissza egy állapotát `Registering`. Ellenőrizheti, hogy a jelenlegi állapot:

  ```azurepowershell-interactive
  Get-AzureRmProviderFeature -FeatureName ManagedResourcesMove -ProviderNamespace Microsoft.Compute
  ```

  ```azurecli-interactive
  az feature show --namespace Microsoft.Compute --name ManagedResourcesMove
  ```

1. Várjon néhány percet, módosítsa az állapot `Registered`.

1. A funkció regisztrálása után regisztrálja a `Microsoft.Compute` erőforrás-szolgáltató. Hajtsa végre ezt a lépést, akkor is, ha az erőforrás-szolgáltató korábban regisztrált.

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
  ```

  ```azurecli-interactive
  az provider register --namespace Microsoft.Compute
  ```

Ez a támogatás azt jelenti, hogy is áthelyezheti:

* A felügyelt lemezekkel rendelkező virtuális gépek
* Felügyelt képek
* Felügyelt pillanatképek
* A felügyelt lemezekkel rendelkező virtuális gépek rendelkezésre állási csoportok

Az alábbiakban a korlátozásokat, amelyek még nem támogatottak:

* Egy új erőforráscsoport ugyanabban az előfizetésben, de az előfizetések között nem helyezheti át virtuális gépeket a Key Vault-tanúsítvánnyal.
* Az Azure Backup szolgáltatással konfigurált virtuális gépek. Használja az alábbi megkerülő megoldás e virtuális gépek áthelyezése
  * Keresse meg a virtuális gép helyét.
  * Keresse meg a következő elnevezési mintának egy erőforráscsoportot: `AzureBackupRG_<location of your VM>_1` például AzureBackupRG_westus2_1
  * Ha az Azure Portalon, majd ellenőrizze "rejtett típusok megjelenítése"
  * Ha a PowerShellben használja a `Get-AzureRmResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` parancsmag
  * Ha a CLI-t, használja a `az resource list -g AzureBackupRG_<location of your VM>_1`
  * Keresse meg az erőforrás-típussal `Microsoft.Compute/restorePointCollections` , amely rendelkezik az elnevezési minta `AzureBackup_<name of your VM that you're trying to move>_###########`
  * Ez az erőforrás törlése
  * Törlés befejezése után lesz a virtuális gép áthelyezése
* Virtual Machine Scale Sets Standard Termékváltozatú terheléselosztó vagy a Standard Termékváltozat nyilvános IP-cím nem lehet áthelyezni.
* A csatolt tervek Piactéri erőforrások alapján létrehozott virtuális gépeken nem lehet áthelyezni, erőforráscsoport vagy előfizetés között. Az aktuális előfizetésben a virtuális gép megszüntetése, és telepítse újra az új előfizetés.

## <a name="virtual-networks-limitations"></a>Virtuális hálózatok korlátozások

Virtuális hálózat áthelyezésekor is át kell helyeznie a tőle függő erőforrások. A VPN-átjárók IP-címek, virtuális hálózati átjárók és minden kapcsolódó kapcsolati erőforrás kell áthelyezni. Helyi hálózati átjárók egy másik erőforráscsoportban található is lehet.

Egy virtuális Társhálózat áthelyezni, előbb le kell tiltania a virtuális hálózatok közötti társviszony. Ha le van tiltva, áthelyezheti a virtuális hálózat. Az áthelyezés után a virtuális hálózatok közötti társviszony újraengedélyezni.

Virtuális hálózat nem helyezhetők át másik előfizetésbe való, ha a virtuális hálózati erőforrás-navigációs hivatkozásaival egy alhálózatot tartalmaz. Például ha az Azure Cache Redis erőforrás egy alhálózatában van üzembe helyezve, az alhálózatra, erőforrás-navigációs hivatkozást.

## <a name="app-service-limitations"></a>Az App Service korlátai

Az App Service-erőforrások áthelyezésére korlátozások eltérő Ha áthelyez egy előfizetésen belül vagy egy új előfizetést az erőforrások alapján.

Feltöltött tanúsítványok, a nem App Service-tanúsítványok az ezekben a szakaszokban ismertetett korlátozások vonatkoznak. Továbbléphet az App Service-tanúsítványok új erőforráscsoportba vagy előfizetésbe korlátozások nélkül. Ha több web apps, az azonos App Service-tanúsítványt használja, először helyezze át a web apps helyezze át a tanúsítványt.

### <a name="moving-within-the-same-subscription"></a>Az egy előfizetésen belüli áthelyezése

Ha a webalkalmazás áthelyezése _ugyanazon az előfizetésen belül_, a feltöltött SSL-tanúsítványok nem helyezhetők át. Azonban áthelyezheti egy webalkalmazást az új erőforráscsoport áthelyezése a feltöltött SSL-tanúsítvány nélkül, és az alkalmazás SSL funkció továbbra is működik.

Ha szeretné helyezni az SSL-tanúsítványt a webalkalmazással, kövesse az alábbi lépéseket:

1. Törölje a feltöltött tanúsítvány megjelenjen a webalkalmazásból.
2. Helyezze át a webes alkalmazás.
3. Töltse fel a tanúsítványt a áthelyezett webalkalmazáshoz.

### <a name="moving-across-subscriptions"></a>Áthelyezése előfizetések között

Ha a webalkalmazás áthelyezése _előfizetésekben_, az alábbi korlátozások érvényesek:

- A cél erőforráscsoport nem kell rendelkeznie minden olyan meglévő App Service-erőforrásokat. App Service-erőforrások a következők:
    - Web Apps
    - App Service-csomagok
    - Feltöltött vagy importált SSL-tanúsítványok
    - App Service-környezetek
- Az erőforráscsoportban lévő összes App Service-erőforrások együtt lehessen áthelyezni.
- App Service-erőforrások csak akkor helyezhető el az erőforráscsoportból, amelyben eredetileg létrehozták őket. Ha egy App Service erőforrás már nem az eredeti erőforráscsoportba, azt kell vissza kell helyezni az eredeti erőforráscsoport először, és ezután áthelyezhető előfizetések között.

## <a name="classic-deployment-limitations"></a>Klasszikus üzembe helyezési korlátozásoknak

A beállításokat a klasszikus modellben telepített erőforrások áthelyezéséhez eltérőek Ha áthelyez egy előfizetésen belül vagy egy új előfizetést az erőforrások alapján.

### <a name="same-subscription"></a>Ugyanabban az előfizetésben

Erőforrások erőforráscsoportok közötti áthelyezése ugyanazon az előfizetésen belül egy másik erőforráscsoportba, amikor a következő korlátozások vonatkoznak:

* Nem lehet áthelyezni a virtuális hálózatok (klasszikus).
* A felhőszolgáltatás virtuális gépek (klasszikus) kell áthelyezni.
* A felhőalapú szolgáltatás csak akkor helyezhető, ha az áthelyezés tartalmazza az összes virtuális gép.
* Csak egy felhőalapú szolgáltatás egyszerre áthelyezhető.
* Csak egy tárfiók (klasszikus) egyszerre áthelyezhető.
* A tárfiók (klasszikus) nem lehet áthelyezni a virtuális gép vagy felhőszolgáltatás műveletben.

Klasszikus erőforrások áthelyezése ugyanazon az előfizetésen belül egy új erőforráscsoportot, a standard szintű áthelyezési műveleteket keresztül használja a [portál](#use-portal), [Azure PowerShell-lel](#use-powershell), [Azure CLI-vel](#use-azure-cli), vagy [REST API-val](#use-rest-api). Resource Manager-erőforrások áthelyezése, ahogy használhatja ugyanazokat a műveleteket.

### <a name="new-subscription"></a>Új előfizetés

Ha az erőforrások áthelyezése új előfizetésre, a következő korlátozások vonatkoznak:

* Az előfizetés összes klasszikus erőforrást műveletben kell áthelyezni.
* A célként megadott előfizetés nem tartalmazhat bármilyen egyéb klasszikus erőforrások.
* Az áthelyezés csak igényelni lehet klasszikus áthelyezését a külön REST API-n keresztül. A standard szintű Resource Manager áthelyezés parancsok nem működnek, ha a klasszikus erőforrások áthelyezése új előfizetésre.

Klasszikus erőforrások áthelyezése új előfizetést, használja a REST-műveletek, konkrétan a klasszikus erőforrások. A REST használata, hajtsa végre az alábbi lépéseket:

1. Ellenőrizze, hogy ha a forrás-előfizetés részt vehetnek-e egy előfizetések közötti áthelyezés. Használja a következő műveletet:

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     A kérelem törzsében a következők:

  ```json
  {
    "role": "source"
  }
  ```

     A választ az érvényesítés művelet a következő formátumban kell megadni:

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Ellenőrizze, hogy ha a cél előfizetést részt vehetnek-e egy előfizetések közötti áthelyezés. Használja a következő műveletet:

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     A kérelem törzsében a következők:

  ```json
  {
    "role": "target"
  }
  ```

     A válasz a forrás-előfizetés érvényesítése ugyanebben a formátumban van.
3. Ha mindkét előfizetés érvényesítési sikeresek, minden hagyományos erőforrás áthelyezése egy előfizetésből egy másik előfizetéshez a következő műveletet:

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    A kérelem törzsében a következők:

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

A művelet több percig futtathatnak.

## <a name="recovery-services-limitations"></a>Recovery Services-korlátozások

Az Azure Site Recovery vész-helyreállítási beállításához használt tárolási, hálózati és számítási erőforrások áthelyezése nincs engedélyezve.

Például tegyük fel, hogy beállította-e a tárfiókhoz (Storage1) a helyszíni gépek replikálását, és szeretné a védett gép merülnek fel a feladatátvételt követően az Azure-ba (Network1) virtuális hálózathoz csatolt virtuális gépként (VM1 –). Ön nem helyezhetők át a következő Azure erőforrások - Storage1 VM1 és Network1 - ugyanazon az előfizetésen belüli erőforráscsoportok között vagy előfizetések között.

A regisztrált virtuális gép áthelyezése a **az Azure backup** erőforráscsoportok között:
 1. Ideiglenesen állítsa le a biztonsági mentés és a biztonsági másolatok adatainak megőrzése
 2. A virtuális gép áthelyezése a céloldali erőforráscsoport
 3. Állítsa a felhasználók visszaállíthatják a az áthelyezési művelet előtt létrehozott rendelkezésre álló helyreállítási pontokból azonos vagy új tároló alatt.
Ha a felhasználó a biztonsági másolatban szereplő virtuális gép előfizetések között, az 1 és 2. lépés ugyanaz marad. A 3. lépésben a felhasználónak kell csoportban található / a célul szolgáló előfizetésben létrehozott egy új tárolót a virtuális gép védelmét. Recovery Services-tároló közötti előfizetés biztonsági mentések nem támogatja.

## <a name="hdinsight-limitations"></a>HDInsight-korlátozások

HDInsight-fürtök áthelyezheti egy új előfizetést, vagy az erőforráscsoportot. Azonban nem helyezhetők át a hálózati erőforrások (például a virtuális hálózathoz, a hálózati adapter vagy a terheléselosztó) a HDInsight-fürthöz társított előfizetésekben. Emellett nem helyezhető át egy új erőforráscsoportot egy hálózati Adaptert, amely a fürt egy virtuális géphez van csatolva.

Amikor új előfizetésbe való áthelyezését egy HDInsight-fürtöt, először helyezze át más erőforrások (például a storage-fiók). Ezután helyezze át a HDInsight-fürt önmagában.

## <a name="search-limitations"></a>Keresés korlátozások

Egyszerre helyezni különböző régiókban lévő több keresési erőforrások nem helyezhetők át.
Ebben az esetben kell külön áthelyezni őket.

## <a name="lb-limitations"></a> Load Balancer korlátozások

Alapszintű Termékváltozatú terheléselosztó helyezhetők.
Standard Termékváltozatú terheléselosztó nem lehet áthelyezni.

## <a name="pip-limitations"></a> Nyilvános IP-korlátozások

Alapszintű Termékváltozat nyilvános IP-cím is áthelyezhetők.
Standard Termékváltozat nyilvános IP-cím nem lehet áthelyezni.

## <a name="use-portal"></a>A portál használatával

Erőforrások áthelyezése, válassza ki ezeket az erőforrásokat tartalmazó erőforráscsoportot, és válassza a **áthelyezése** gombra.

![erőforrások áthelyezése](./media/resource-group-move-resources/select-move.png)

Válassza ki, hogy az erőforrások telepít át egy új erőforráscsoportot, vagy egy új előfizetést.

Válassza ki az áthelyezni kívánt erőforrások és a cél erőforráscsoport. Tudomásul veszi, hogy szeretne-e frissíteni a következő ezeket az erőforrásokat a szkripteket, és válassza ki **OK**. Ha az előfizetés szerkesztési ikonra az előző lépésben, a cél előfizetést is választania kell.

![cél kiválasztása](./media/resource-group-move-resources/select-destination.png)

A **értesítések**, láthatja, hogy fut-e az áthelyezési művelet.

![Áthelyezés állapot megjelenítése](./media/resource-group-move-resources/show-status.png)

Ha befejeződött, értesítést kap arról, az eredmény.

![Áthelyezés eredmény megjelenítése](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>A PowerShell használata

Meglévő erőforrások áthelyezése egy másik erőforráscsoportba vagy előfizetésbe, használja a [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) parancsot. Az alábbi példa bemutatja, hogyan több erőforrást áthelyezése egy új erőforráscsoportot.

```azurepowershell-interactive
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Át egy új előfizetést, adjon meg egy értéket a `DestinationSubscriptionId` paraméter.

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

Meglévő erőforrások áthelyezése egy másik erőforráscsoportba vagy előfizetésbe, használja a [az erőforrás-áthelyezési](/cli/azure/resource?view=azure-cli-latest#az-resource-move) parancsot. Adja meg az erőforrás-azonosítókat az erőforrások áthelyezése. Az alábbi példa bemutatja, hogyan több erőforrást áthelyezése egy új erőforráscsoportot. Az a `--ids` paramétert, adja meg az erőforrás-azonosítók áthelyezése szóközzel elválasztott listáját.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Szeretne áthelyezni egy új előfizetést, adja meg a `--destination-subscription-id` paraméter.

## <a name="use-rest-api"></a>A REST API használata

Meglévő erőforrások áthelyezése egy másik erőforráscsoportba vagy előfizetésbe, futtassa:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

A kérelem törzsében szereplő adja meg a céloldali erőforráscsoport és erőforrások áthelyezéséhez. Az áthelyezési REST művelet kapcsolatos további információkért lásd: [erőforrások áthelyezése](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>További lépések

* Az előfizetés kezelése a PowerShell-parancsmagokkal kapcsolatos további információkért lásd: [Azure PowerShell használatával a Resource Managerrel](powershell-azure-resource-manager.md).
* Az előfizetés kezelése az Azure CLI-parancsokkal kapcsolatos további információkért lásd: [az Azure CLI használatával a Resource Manager](xplat-cli-azure-resource-manager.md).
* Az előfizetés kezelésére szolgáló portál funkciókkal kapcsolatos tudnivalókért lásd: [erőforrások kezelése az Azure portal használatával](resource-group-portal.md).
* Az erőforrások logikus alkalmazásával kapcsolatos tudnivalókért lásd: [az erőforrások rendszerezése címkék használatával](resource-group-using-tags.md).
