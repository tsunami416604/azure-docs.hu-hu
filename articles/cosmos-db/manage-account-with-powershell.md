---
title: Az Azure Cosmos DB Automation - felügyelet a PowerShell használatával |} Microsoft Docs
description: Azure Powershell használata az Azure Cosmos DB fiókok kezelése.
services: cosmos-db
author: dmakwana
manager: jhubbard
editor: ''
tags: azure-resource-manager
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: 12c7800ee2a823cca5394841cae86eeacc20fb1b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>PowerShell-lel Azure Cosmos DB-fiók létrehozása

Ez az útmutató ismerteti a parancsok automatizált felügyelete az Azure Powershell Azure Cosmos DB adatbázis fiók. Parancsok kulcsait és a feladatátvételi prioritások kezelésére is tartalmaz [több területi adatbázis fiókok][scaling-globally]. Az adatbázisfiók frissítése lehetővé teszi a konzisztencia-házirendek módosíthatók és régiók hozzáadása/eltávolítása. A platformok közötti felügyeleti Azure Cosmos DB-fiókja, választhat [Azure CLI](cli-samples.md), a [erőforrás-szolgáltató REST API][rp-rest-api], vagy a [Azure-portálon ](create-sql-api-dotnet.md#create-account).

## <a name="getting-started"></a>Kezdeti lépések

Kövesse az utasításokat a [telepítése és konfigurálása az Azure PowerShell] [ powershell-install-configure] telepítéséhez, és jelentkezzen be az Azure Resource Manager fiókjába a PowerShellben.

### <a name="notes"></a>Megjegyzések

* Ha szeretné hajtható végre az alábbi parancsokat a felhasználó jóváhagyásának kérése nélkül, hozzáfűző a `-Force` jelzőjét, hogy a parancs.
* A következő parancsok szinkronizáltak.

## <a id="create-documentdb-account-powershell"></a> Az Azure Cosmos DB-fiók létrehozása

Ez a parancs egy Azure Cosmos DB adatbázisfiók létrehozása teszi lehetővé. Az új adatbázis-fiók beállítása vagy egyetlen területi vagy [több területi] [ scaling-globally] az egy bizonyos [konzisztencia házirend](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Az adatbázisfiók írási régiója hely neve. Ezen a helyen szükség van a feladatátvételi prioritási értéke 0. Másodpercenkénti adatbázis-fiók pontosan egy írási régióban kell lennie.
* `<read-region-location>` Az adatbázisfiók írásvédett régió hely neve. Ez a hely szükséges feladatátvevő prioritás értéke csak 0-nál nagyobb. Másodpercenkénti adatbázis-fiók több mint egy olvasási régiók lehet.
* `<ip-range-filter>` Megadja az IP-címek vagy IP-címtartományok része, mint az engedélyezett bővítmények listájához ügyfél IP-címek egy adott adatbázis fiókjához tartozó CIDR formátumban. IP-címeken/tartományokon vesszővel elválasztott, és nem tartalmazhat szóközt kell lennie. További információkért lásd: [Azure Cosmos DB-Tűzfaltámogatás](firewall-support.md)
* `<default-consistency-level>` A Azure Cosmos DB-fiók alapértelmezett konzisztencia szintjét. További információkért lásd: [Azure Cosmos DB-ben Konzisztenciaszintek](consistency-levels.md).
* `<max-interval>` Kötött elavulási konzisztencia használata esetén ezt az értéket jelenti idő (másodpercben) elavulási megengedett. Ez az érték elfogadható tartománya 1-100.
* `<max-staleness-prefix>` Kötött elavulási konzisztencia használata esetén ezt az értéket jelöli megengedett elavult kérelmek számát jelenti. Ez az érték elfogadható tartománya 1 – 2 147 483 647.
* `<resource-group-name>` Neve a [Azure erőforráscsoport] [ azure-resource-groups] , amelyhez az új Azure Cosmos DB adatbázisfiók való tartozik.
* `<resource-group-location>` Az Azure-erőforráscsoportot, amelyhez az új Azure Cosmos DB adatbázis fiók tartozik helyét.
* `<database-account-name>` A létrehozandó Azure Cosmos DB fiók neve. Csak kisbetűket, számokat, képes használni a "-" karakter, és 3 – 50 karakter közé kell esnie.

Példa: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Megjegyzések
* A fenti példában egy adatbázis-fiók két régió hoz létre. Akkor is egy régió tartozik (amely a írási régió van kijelölve, és a feladatátvételi prioritási értéke csak 0) vagy a több mint két régiók adatbázis-fiók létrehozásához. További információkért lásd: [több területi adatbázis fiókok][scaling-globally].
* A helyek, amelyben Azure Cosmos DB általánosan elérhető régiók kell lennie. Az aktuális területek listája a a [Azure-régiókat lap](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Egy Azure Cosmos DB adatbázisfiók frissítése

Ez a parancs lehetővé teszi az Azure Cosmos DB adatbázis fiók tulajdonságainak módosítása. Ez magában foglalja a konzisztencia-házirend és a helyek, amely az adatbázis-fiók létezik-e.

> [!NOTE]
> Ez a parancs lehetővé teszi a hozzáadása és eltávolítása a régiókban, de nem engedélyezi feladatátvételi prioritások módosíthatja. Feladatátvételi prioritások módosításához lásd [alatt](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Az adatbázisfiók írási régiója hely neve. Ezen a helyen szükség van a feladatátvételi prioritási értéke 0. Másodpercenkénti adatbázis-fiók pontosan egy írási régióban kell lennie.
* `<read-region-location>` Az adatbázisfiók írásvédett régió hely neve. Ez a hely szükséges feladatátvevő prioritás értéke csak 0-nál nagyobb. Másodpercenkénti adatbázis-fiók több mint egy olvasási régiók lehet.
* `<default-consistency-level>` A Azure Cosmos DB-fiók alapértelmezett konzisztencia szintjét. További információkért lásd: [Azure Cosmos DB-ben Konzisztenciaszintek](consistency-levels.md).
* `<ip-range-filter>` Megadja az IP-címek vagy IP-címtartományok része, mint az engedélyezett bővítmények listájához ügyfél IP-címek egy adott adatbázis fiókjához tartozó CIDR formátumban. IP-címeken/tartományokon vesszővel elválasztott, és nem tartalmazhat szóközt kell lennie. További információkért lásd: [Azure Cosmos DB-Tűzfaltámogatás](firewall-support.md)
* `<max-interval>` Kötött elavulási konzisztencia használata esetén ezt az értéket jelenti idő (másodpercben) elavulási megengedett. Ez az érték elfogadható tartománya 1-100.
* `<max-staleness-prefix>` Kötött elavulási konzisztencia használata esetén ezt az értéket jelöli megengedett elavult kérelmek számát jelenti. Ez az érték elfogadható tartománya 1 – 2 147 483 647.
* `<resource-group-name>` Neve a [Azure erőforráscsoport] [ azure-resource-groups] , amelyhez az új Azure Cosmos DB adatbázisfiók való tartozik.
* `<resource-group-location>` Az Azure-erőforráscsoportot, amelyhez az új Azure Cosmos DB adatbázis fiók tartozik helyét.
* `<database-account-name>` Frissíteni kell az Azure Cosmos DB adatbázis fiók nevét.

Példa: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Egy Azure Cosmos DB adatbázisfiók törlése

Ez a parancs lehetővé teszi egy meglévő Azure Cosmos DB adatbázisfiók törlése.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` Neve a [Azure erőforráscsoport] [ azure-resource-groups] , amelyhez az új Azure Cosmos DB adatbázisfiók való tartozik.
* `<database-account-name>` A törlendő Azure Cosmos DB fiók neve.

Példa:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Egy Azure Cosmos DB adatbázis fiók tulajdonságainak beolvasása

A parancs lehetővé teszi, hogy egy meglévő Azure Cosmos DB adatbázisfiók tulajdonságainak beolvasása.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Neve a [Azure erőforráscsoport] [ azure-resource-groups] , amelyhez az új Azure Cosmos DB adatbázisfiók való tartozik.
* `<database-account-name>` Az Azure Cosmos adatbázis adatbázis-fiók nevét.

Példa:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Egy Azure Cosmos DB adatbázis fiók címkék frissítése

Az alábbi példa ismerteti, hogyan lehet beállítani [Azure erőforráscímkék] [ azure-resource-tags] az Azure Cosmos DB az adatbázis-fiók.

> [!NOTE]
> Ez a parancs kombinálva, a létrehozás vagy frissítés parancsok hozzáfűzésével a `-Tags` jelzőt mellékel a megfelelő paraméter.

Példa:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Fiók listázása

Egy Azure Cosmos DB fiók létrehozásakor a szolgáltatás két fő kulcsot, amely az Azure Cosmos DB fiók elérésekor hitelesítéshez használt állít elő. Két tárelérési kulcsok megadásával Azure Cosmos DB teszi Azure Cosmos DB fiókjába megszakítás nélkül a kulcsok újragenerálását. Az olvasási műveletek csak olvasható kulcsokat is elérhetők. Nincsenek két írható-olvasható (elsődleges és másodlagos) és két írásvédett kulcsokat (elsődleges és másodlagos).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Neve a [Azure erőforráscsoport] [ azure-resource-groups] , amelyhez az új Azure Cosmos DB adatbázisfiók való tartozik.
* `<database-account-name>` Az Azure Cosmos adatbázis adatbázis-fiók nevét.

Példa:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Lista kapcsolati karakterláncok

A MongoDB-fiókok a kapcsolati karakterláncot a MongoDB-alkalmazás csatlakoztatása a következő adatbázisfiókot lehet beolvasni a következő parancsot.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Neve a [Azure erőforráscsoport] [ azure-resource-groups] , amelyhez az új Azure Cosmos DB adatbázisfiók való tartozik.
* `<database-account-name>` Az Azure Cosmos adatbázis adatbázis-fiók nevét.

Példa:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Fiók kulcs újragenerálása

Módosítsa a tárelérési kulcsok rendszeres időközönként a Azure Cosmos DB fiókját biztonságosabbá a kapcsolatokat. Engedélyezi, hogy az egyik kulcs, amíg a többi hozzáférési kulcs újragenerálása Azure Cosmos DB fiókhoz kapcsolatok karbantartása két tárelérési kulcsok vannak hozzárendelve.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` Neve a [Azure erőforráscsoport] [ azure-resource-groups] , amelyhez az új Azure Cosmos DB adatbázisfiók való tartozik.
* `<database-account-name>` Az Azure Cosmos adatbázis adatbázis-fiók nevét.
* `<key-kind>` A négy típusú kulcsok: ["Elsődleges" |} " Másodlagos "|}" PrimaryReadonly "|}" SecondaryReadonly"], amely szeretné generálni.

Példa:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Egy Azure Cosmos DB adatbázisfiók feladatátvételi prioritásának módosítása

Több területi adatbázis fiókok módosíthatja a különböző régiókban, amelyek az Azure Cosmos DB adatbázis fiók létezik-e a feladatátvételi prioritását. Feladatátvétel az Azure Cosmos DB adatbázis fiókban további információkért lásd: [adatok globálisan Azure Cosmos DB terjesztése][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` Az adatbázisfiók írási régiója hely neve. Ezen a helyen szükség van a feladatátvételi prioritási értéke 0. Másodpercenkénti adatbázis-fiók pontosan egy írási régióban kell lennie.
* `<read-region-location>` Az adatbázisfiók írásvédett régió hely neve. Ez a hely szükséges feladatátvevő prioritás értéke csak 0-nál nagyobb. Másodpercenkénti adatbázis-fiók több mint egy olvasási régiók lehet.
* `<resource-group-name>` Neve a [Azure erőforráscsoport] [ azure-resource-groups] , amelyhez az új Azure Cosmos DB adatbázisfiók való tartozik.
* `<database-account-name>` Az Azure Cosmos adatbázis adatbázis-fiók nevét.

Példa:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>További lépések

* A csatlakozás, .NET használatával, lásd: [kapcsolódás és lekérdezés .NET](create-sql-api-dotnet.md).
* A csatlakozás, Node.js használatával, lásd: [kapcsolódás és lekérdezés a Node.js és a MongoDB-alkalmazás](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
