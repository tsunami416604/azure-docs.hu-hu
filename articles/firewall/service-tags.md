---
title: Azure-tűzfal szolgáltatáscímkék áttekintése
description: Ez a cikk az Azure-tűzfal szolgáltatáscímkék áttekintést.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/21/2019
ms.author: victorh
ms.openlocfilehash: 9ecc5d3779ac6632f4a5c05914cbb0e756c79e91
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458006"
---
# <a name="azure-firewall-service-tags"></a>Az Azure tűzfal szolgáltatáscímkék

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

A hálózati szabályok célmező Azure tűzfal szolgáltatáscímkék is használható. Adott IP-címek helyett használhatja őket.

> [!NOTE]
> Szolgáltatás címkék jelennek meg régiók növekményes és lesz elérhető az összes régióban a közeljövőben.

## <a name="supported-service-tags"></a>Támogatott szolgáltatáscímkék

Az Azure hálózati tűzfalszabályok a következő szolgáltatáscímkék érhetőek:

* **AzureCloud** (csak Resource Manager): Ez a címke az IP-címteret például az összes Azure- [adatközpont nyilvános IP-címek](https://www.microsoft.com/download/details.aspx?id=41653). Ha az *AzureCloud* értéket adja meg, a nyilvános Azure IP-címekre irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az AzureCloud szolgáltatáshoz, megadhat egy régiót. Ha például csak az USA keleti régiójában szeretné engedélyezni a hozzáférést az Azure AzureCloud szolgáltatáshoz, megadhatja az *AzureCloud.EastUS* szolgáltatáscímkét. 
* **AzureTrafficManager** (csak Resource Manager): Ez a címke azt jelzi, hogy az Azure Traffic Manager mintavételi IP-címeket az IP-címterét. További információk a Traffic Manager mintavételezési IP-címeiről: [Azure Traffic Manager – Gyakori kérdések](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Tárolási** (csak Resource Manager): Ez a címke azt jelzi, hogy az Azure Storage szolgáltatás IP-címterét. Ha a *Storage* értéket adja meg, a Storage szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést a Storage szolgáltatáshoz, megadhat egy régiót. Ha például csak az USA keleti régiójában szeretné engedélyezni a hozzáférést az Azure Storage szolgáltatáshoz, megadhatja a *Storage.EastUS* szolgáltatáscímkét. A címke a szolgáltatást jelöli, annak adott példányait azonban nem. Például a címke az Azure Storage szolgáltatást jelöli, de nem egy adott Azure Storage-fiókot.
* **SQL** (csak Resource Manager): Ez a címke az Azure SQL Database és az Azure SQL Data Warehouse szolgáltatás címelőtagjait. Ha az *SQL* értéket adja meg, az SQL szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést az SQL szolgáltatáshoz, megadhat egy régiót. Ha például csak az USA keleti régiójában szeretné engedélyezni a hozzáférést az Azure SQL Database-hez, megadhatja az *Sql.EastUS* szolgáltatáscímkét. A címke a szolgáltatást jelöli, annak adott példányait azonban nem. Például a címke az Azure SQL Database szolgáltatást jelöli, de nem egy adott SQL-adatbázist vagy -kiszolgálót.
* **AzureCosmosDB** (csak Resource Manager): Ez a címke az Azure Cosmos Database szolgáltatás címelőtagjait. Ha az *AzureCosmosDB* értéket adja meg, az AzureCosmosDB szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha szeretné egy adott AzureCosmosDB való hozzáférés engedélyezése [régió](https://azure.microsoft.com/regions), megadhatja, hogy a régió, a következő formátumban: AzureCosmosDB. [ régió neve].
* **Azurekeyvault értékre van** (csak Resource Manager): Ez a címke az Azure KeyVault szolgáltatás címelőtagjait. Ha az *AzureKeyVault* értéket adja meg, az AzureKeyVault szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha szeretné egy adott azurekeyvault értékre van való hozzáférés engedélyezése [régió](https://azure.microsoft.com/regions), megadhatja, hogy a régió, a következő formátumban: azurekeyvault értékre van. [ régió neve].
* **Az EventHub** (csak Resource Manager): Ez a címke az Azure Event hubhoz szolgáltatás címelőtagjait. Ha az *EventHub* értéket adja meg, az EventHub szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha szeretné egy adott Eventhubhoz való hozzáférés engedélyezése [régió](https://azure.microsoft.com/regions), megadhatja, hogy a régió, a következő formátumban: EventHub. [ régió neve]. 
* **A ServiceBus** (csak Resource Manager): Ez a címke az Azure Szolgáltatásbusz-szolgáltatás címelőtagjait. Ha a *ServiceBus* értéket adja meg, a ServiceBus szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha szeretné egy adott Szolgáltatásbuszhoz való hozzáférés engedélyezése [régió](https://azure.microsoft.com/regions), megadhatja, hogy a régió, a következő formátumban: a ServiceBus. [ régió neve].
* **MicrosoftContainerRegistry** (csak Resource Manager): Ez a címke a Microsoft Container Registry szolgáltatás címelőtagjait. Ha a *MicrosoftContainerRegistry* értéket adja meg, a MicrosoftContainerRegistry felé irányuló forgalom engedélyezhető vagy letiltható. Ha szeretné egy adott MicrosoftContainerRegistry való hozzáférés engedélyezése [régió](https://azure.microsoft.com/regions), megadhatja, hogy a régió, a következő formátumban: MicrosoftContainerRegistry. [ régió neve].
* **AzureContainerRegistry** (csak Resource Manager): Ez a címke az Azure Container Registry szolgáltatás címelőtagjait. Ha az *AzureContainerRegistry* értéket adja meg, az AzureContainerRegistry felé irányuló forgalom engedélyezhető vagy letiltható. Ha szeretné egy adott AzureContainerRegistry való hozzáférés engedélyezése [régió](https://azure.microsoft.com/regions), megadhatja, hogy a régió, a következő formátumban: AzureContainerRegistry. [ régió neve]. 
* **Az App Service** (csak Resource Manager): Ez a címke az Azure App Service szolgáltatás címelőtagjait. Ha az *AppService* értéket adja meg, az AppService szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Csak szeretné-e hozzáférést az App Service egy adott [régió](https://azure.microsoft.com/regions), megadhatja, hogy a régió, a következő formátumban: AppService. [ régió neve]. 
* **AppServiceManagement** (csak Resource Manager): Ez a címke az Azure App Service Management szolgáltatás címelőtagjait. Ha az *AppServiceManagement* értéket adja meg, az AppServiceManagement szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. 
* **Az ApiManagement** (csak Resource Manager): Ez a címke az Azure API Management szolgáltatás címelőtagjait. Ha az *ApiManagement* értéket adja meg, az ApiManagement szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható.  
* **AzureConnectors** (csak Resource Manager): Ez a címke az Azure-összekötő szolgáltatás címelőtagjait. Ha az *AzureConnectors* értéket adja meg, az AzureConnectors szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha szeretné egy adott AzureConnectors való hozzáférés engedélyezése [régió](https://azure.microsoft.com/regions), megadhatja, hogy a régió, a következő formátumban: AzureConnectors. [ régió neve].
* **AzureDataLake** (csak Resource Manager): Ez a címke az Azure Data Lake szolgáltatás címelőtagjait. Ha az *AzureDataLake* értéket adja meg, az AzureDataLake szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható.
* **AzureActiveDirectory** (csak Resource Manager): Ez a címke az AzureActiveDirectory szolgáltatás címelőtagjait. Ha az *AzureActiveDirectory* értéket adja meg, az AzureActiveDirectory szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható.
* **AzureMonitor** (csak Resource Manager): Ez a címke a AzureMonitor szolgáltatás címelőtagjait. Ha megad *AzureMonitor* értékénél, engedélyezett vagy tiltott forgalmat a AzureMonitor.
* **ServiceFabric** (csak Resource Manager): Ez a címke a ServiceFabric-szolgáltatás címelőtagjait. Ha megad *ServiceFabric* értékénél, engedélyezett vagy tiltott forgalmat ServiceFabric való.
* **AzureMachineLearning** (csak Resource Manager): Ez a címke a AzureMachineLearning szolgáltatás címelőtagjait. Ha megad *AzureMachineLearning* értékénél, engedélyezett vagy tiltott forgalmat a AzureMachineLearning.

## <a name="next-steps"></a>További lépések

Azure tűzfalszabályokkal kapcsolatos további tudnivalókért lásd: [Azure tűzfalszabály feldolgozó logika](rule-processing.md).