---
title: Biztonságos hozzáférés a Azure Cosmos DB fiókkal az Azure Virtual Network szolgáltatás végpontjának használatával |} Microsoft Docs
description: Ez a dokumentum telepítő Azure Cosmos DB virtuális hálózati szolgáltatási végpont szükséges lépéseket ismerteti.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: 0bd31270ca67dc993cc7ac72ab2bab9bf70005ca
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293995"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Biztonságos hozzáférés a Azure Cosmos DB fiókkal az Azure Virtual Network szolgáltatás végpontjának használatával

Az Azure CosmosDB fiókok beállítható úgy, hogy engedélyezi a hozzáférést csak a megadott alhálózat az Azure-beli virtuális hálózathoz. Engedélyezése egy [szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) Azure CosmosDB a virtuális hálózat és az alhálózat, a forgalom egy optimális és biztonságos útvonal az Azure Cosmos DB biztosított.  

Az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás. Az adatok találhatók az Azure Cosmos DB fiók képes replikálni, több területre. Ha egy virtuális hálózati szolgáltatási végpont Azure Cosmos DB van beállítva, mindegyik virtuális hálózati hozzáférést nyújt a specicifc alhálózathoz tartozó IP-címek. Az alábbi ábrán egy Azure Cosmos DB, amelyen engedélyezve van a virtuális hálózati szolgáltatási végpont szemléltetésére:

![virtuális hálózati szolgáltatási végpont architektúrája](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Egy Azure Cosmos DB fiók konfigurációja egy virtuális hálózati végpontot, amennyiben az elérhető csak a megadott alhálózat, a rendszer eltávolítja az összes nyilvános/internet-hozzáféréssel. Ismerje meg a végpontok vonatkozó részletes, tekintse meg az Azure [virtuális hálózati szolgáltatás végpontok áttekintése](../virtual-network/virtual-network-service-endpoints-overview.md) cikk.

> [!NOTE]
> Virtuális hálózati szolgáltatási végpont jelenleg Azure Cosmos DB SQL API-t vagy a Mongo API fiókok konfigurálhatók. API-k és szuverén felhők, például a Németországi Azure vagy az Azure Government szolgáltatás végpontok konfigurálása lehessen hamarosan elérhető lesz. Ha egy meglévő IP-tűzfal, a Azure Cosmos DB fiókjához beállított, vegye figyelembe a tűzfal konfigurációját, távolítsa el az IP-tűzfal, és válassza ki a szolgáltatási végpont ACL. Miután konfigurálta a szolgáltatási végpont, engedélyezheti újra az IP-tűzfal szükség esetén.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Szolgáltatásvégpont konfigurálása az Azure portál használatával
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Egy meglévő Azure virtuális hálózat és alhálózat szolgáltatásvégpont konfigurálása

1. A **összes erőforrás** panelen található a virtuális hálózati szeretne szolgáltatásvégpont konfigurálása Azure Cosmos DB. Keresse meg a **szolgáltatás végpontjait** panelt, és győződjön meg arról, hogy a virtuális hálózat alhálózatában engedélyezve van a "Azure.CosmosDB" szolgáltatáshoz végpontot.  

   ![Erősítse meg a szolgáltatási végpont engedélyezve](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. A **összes erőforrás** panelen található az Azure Cosmos DB fiók, akkor szeretné védeni.  

3. Ahhoz, hogy a virtuális hálózati szolgáltatási végpont, másolja a későbbi használat Azure Cosmos DB-fiókjához társított IP-tűzfal adatait. IP-tűzfal szolgáltatási végpont beállítása után újra engedélyezheti.  

4. Válassza ki **tűzfalak és a virtuális hálózatok** beállítások menüjében válassza engedélyezzék **hálózatok kijelölt**.  

3. Hozzáférést biztosít egy meglévő virtuális hálózati alhálózat virtuális hálózatok, válassza ki a **meglévő Azure virtuális hálózat hozzáadása**.  

4. Válassza ki a **előfizetés** származó, amelyet hozzá szeretne adni az Azure-beli virtuális hálózat. Válassza ki az Azure **virtuális hálózatok** és **alhálózatok** , hogy szeretné-e az Azure Cosmos DB fiók eléréséhez. Ezután válasszon **engedélyezése** kijelölt hálózatokat és a végpontok engedélyezésére vonatkozó "Microsoft.AzureCosmosDB". Amikor elkészült, válassza ki a **Hozzáadás**.  

   ![Válassza ki a virtuális hálózat és alhálózat](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Ha az Azure Cosmos DB szolgáltatásvégpont korábban nincs beállítva a kijelölt Azure virtuális hálózatok és alhálózatok, ez a művelet részeként beállítható. Hozzáférés engedélyezése akár 15 percet igénybe vehet. Nagyon fontos az IP-tűzfal letiltása után megjegyezni renabling tűzfalán hozzáférés-vezérlési lista tartalmát le azokat később. 

   ![virtuális hálózati és alhálózati konfigurálása sikeresen megtörtént](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Azure Cosmos DB fiókja most csak akkor engedélyezi a kiválasztott alhálózat forgalmát. Ha korábban rendelkezett engedélyezve IP tűzfal, újra engedélyezze azokat korábbi információk segítségével.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Egy új Azure virtuális hálózat és alhálózat szolgáltatásvégpont konfigurálása

1. A **összes erőforrás** panelen található az Azure Cosmos DB fiók, akkor szeretné védeni.  

> [!NOTE]
> Ha egy meglévő IP-tűzfal, a Azure Cosmos DB fiókjához beállított, vegye figyelembe a tűzfal konfigurációját, távolítsa el az IP-tűzfal, és engedélyeznie kell a szolgáltatási végpont. Ha engedélyezi a szolgáltatási végpont nélkül disbling a tűzfal, a forgalom adott IP-címtartomány a virtuális IP-identitás elvész, és azt egy IP-szűrő hibaüzenettel megszakad. Ez a hiba megelőzése érdekében mindig tiltsa le a tűzfalszabályok, másolja a fájlokat, az alhálózati, és végül a Cosmos-Adatbázisból az alhálózat ACL szolgáltatásvégpont engedélyezése. Szolgáltatásvégpont konfigurálása és a hozzáférés-vezérlési lista hozzáadása után újra engedélyezheti az IP-tűzfal újra szükség esetén.

2. Ahhoz, hogy a virtuális hálózati szolgáltatási végpont, másolja a későbbi használat Azure Cosmos DB-fiókjához társított IP-tűzfal adatait. IP-tűzfal szolgáltatási végpont beállítása után újra engedélyezheti.  

3. Válassza ki **tűzfalak és az Azure virtuális hálózatok** beállítások menüjében válassza engedélyezzék **hálózatok kijelölt**.  

4. Hozzáférést biztosít egy új Azure virtuális hálózatot, virtuális hálózatok, válassza ki a **hozzáadása új virtuális hálózat**.  

5. Adja meg egy új virtuális hálózat létrehozásához szükséges adatait, és válassza a létrehozása. Az alhálózat "Microsoft.AzureCosmosDB" engedélyezve van a szolgáltatásvégpont jön létre.

   ![Válassza ki a virtuális hálózat és az új virtuális hálózati alhálózat](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Engedélyezi a hozzáférést az Azure portálról

Miután az Azure Virtual Network Szolgáltatásvégpontok engedélyezve vannak az Azure Cosmos DB adatbázisfiók, a portál vagy más Azure-szolgáltatásokhoz való hozzáférést alapértelmezés szerint le van tiltva. A számítógépek a megadott alhálózati kívül hozzáfér a Azure Cosmos DB adatbázis fiókjához sem, beleértve a portálról.

![Engedélyezi a hozzáférést a portálról](./media/vnet-service-endpoint/allow-access-from-portal.png)

Azure Cosmos DB fiókja más Azure-szolgáltatásokhoz hasonlóan az Azure Search által használt, vagy a Stream analytics vagy a Power bi-ban érhető el, ha engedélyezi a hozzáférést ellenőrzésével **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**.

Ellenőrizze, hogy access Azure Cosmos DB csatlakozva a portálról, engedélyeznie kell **engedélyezi a hozzáférést az Azure-portálhoz** beállítások. A beállításokkal kapcsolatos további információkért lásd: [Azure-portálon kapcsolatok](firewall-support.md#connections-from-the-azure-portal) és [Azure PaaS szolgáltatások közötti kapcsolatok](firewall-support.md#connections-from-public-azure-datacenters-or-azure-paas-services) szakaszok. Miután kijelölte a hozzáférést, jelölje ki **mentése** menti a beállításokat.

## <a name="remove-a-virtual-network-or-subnet"></a>Távolítsa el a virtuális hálózat vagy az alhálózat 

1. A **összes erőforrás** panelen, a Keresés az Azure Cosmos-adatbázis fiók, amelynek Szolgáltatásvégpontok rendelt.  

2. Válassza ki **tűzfalak és a virtuális hálózatok** beállítások menüjében.  

3. A virtuális hálózati és alhálózati szabály eltávolításához válassza az "..." mellett a virtuális hálózat vagy alhálózat, és válasszon **eltávolítása**.

   ![Távolítsa el a virtuális hálózatot](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Kattintson a **mentése** a módosítások alkalmazásához.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Szolgáltatási végpont konfigurálása az Azure PowerShell használatával 

A következő lépésekkel konfigurálhatja a szolgáltatási végpont Azure Cosmos DB fiókhoz Azure PowerShell használatával:  

1. Telepítse a legújabb [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) és [bejelentkezési](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Győződjön meg arról, vegye figyelembe az IP-tűzfal beállításait, és törli az IP-tűzfal teljesen ahhoz, hogy a szolgáltatási végpont a fiókhoz.


> [!NOTE]
> Ha egy meglévő IP-tűzfal, a Azure Cosmos DB fiókjához beállított, vegye figyelembe a tűzfal konfigurációját, távolítsa el az IP-tűzfal, és engedélyeznie kell a szolgáltatási végpont. Ha engedélyezi a szolgáltatási végpont nélkül disbling a tűzfal, a forgalom adott IP-címtartomány a virtuális IP-identitás elvész, és azt egy IP-szűrő hibaüzenettel megszakad. Ez a hiba megelőzése érdekében mindig tiltsa le a tűzfalszabályok, másolja a fájlokat, az alhálózati, és végül a Cosmos-Adatbázisból az alhálózat ACL szolgáltatásvégpont engedélyezése. Szolgáltatásvégpont konfigurálása és a hozzáférés-vezérlési lista hozzáadása után újra engedélyezheti az IP-tűzfal újra szükség esetén.

2. Ahhoz, hogy a virtuális hálózati szolgáltatási végpont, másolja a későbbi használat Azure Cosmos DB-fiókjához társított IP-tűzfal adatait. Újra IP tűzfal szolgáltatás végpontjának beállítását követően engedélyezi.  

3. Engedélyezze a szolgáltatási végpont a virtuális hálózat egy létező alhálózatot.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

4. Felkészülés az ACL-engedélyezésre a CosmosDB fiók azáltal, hogy meg arról, hogy a virtuális hálózati és alhálózati szolgáltatási végpont Azure Cosmos DB engedélyezve.

   ```powershell
   $subnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName  | Get-AzureRmVirtualNetworkSubnetConfig -Name $sname
   $vnProp = Get-AzureRmVirtualNetwork `-Name $vnName  -ResourceGroupName $rgName
   ```

5. Azure Cosmos DB fiók tulajdonságainak beolvasása a következő parancsmagot:  

   ```powershell
   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. A változók későbbi használatra inicializálni. Állítsa be a változók a meglévő fiók definícióból, ha több hellyel rendelkezik, kell adja hozzá a tömb részeként. Ebben a lépésben konfigurálnia virtuális hálózati szolgáltatási végpont a "accountVNETFilterEnabled" változó "True" értékre állításával. Ez az érték a "isVirtualNetworkFilterEnabled" paraméter később hozzá van rendelve.  

   ```powershell
   $locations = @(@{})
   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $subnetName  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. Azure Cosmos DB fiók tulajdonságainak frissítése az új beállításokkal a következő parancsmag futtatásával: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource ``
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. A következő parancs futtatásával ellenőrizze, hogy a virtuális hálózati végpontot, amelyhez az előző lépésben konfigurált Azure Cosmos DB fiókja frissült:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Virtuális hálózati szolgáltatási végpont IP tűzfal engedélyezett Azure Cosmos DB fiók hozzáadása

1. Először tiltsa le az Azure Cosmos DB fiók IP-tűzfal elérésére.  

2. Engedélyezze a virtuális hálózati végpont Azure Cosmos DB fiókhoz a korábbi szakaszokban ismertetett módszerek valamelyikét.  

3. Engedélyezze újra az IP-tűzfal elérést. 

## <a name="test-the-access"></a>A hozzáférés tesztelése

Ellenőrizze, hogy a várt módon Azure Cosmos DB szolgáltatás végpontjainak konfigurációja, tegye a következőket:

* Állítani egy virtuális hálózatban két alhálózat előtér- és háttérszolgáltatások, engedélyezze a a backend alhálózathoz Cosmos DB végpont.  

* Engedélyezze a hozzáférést a Cosmos DB fiók a backend alhálózathoz.  

* Hozzon létre két virtuális gépek egy az egyhez backend alhálózathoz, majd egy másikat a frontend alhálózaton.  

* Próbálja meg az Azure Cosmos DB fiókjához fér hozzá mindkét virtuális gépekről. Kapcsolódhatnak a backend alhálózathoz létrehozott virtuális gépről, de nem létrehozott frontend alhálózaton kell lennie. A kérelem a 404-es hiba lesz, amikor megpróbál csatlakozni a frontend alhálózatból, amely megerősíti, hogy a hozzáférés az Azure Cosmos-Adatbázishoz a virtuális hálózati szolgáltatási végpont használatával lett biztonságossá. A gép a backend alhálózathoz fog tudni jól működnek.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Mi történik, ha egy Azure Cosmos DB fiók, amely a virtuális hálózati hozzáférés-vezérlési lista (ACL) engedélyezve van?  

HTTP 404 hibaüzenet.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Olyan alhálózatok, a különböző régiókban hozzáférhetnek az Azure Cosmos adatbázis fiók egy másik régióban létrehozott virtuális hálózatban? Például ha Azure Cosmos DB fiók USA nyugati régiója vagy a USA keleti régiója és a virtuális hálózati több régióba, a virtuális hálózati férhetnek hozzá Azure Cosmos DB?  

Igen, különböző régiókban létrehozott virtuális hálózatokat férhetnek hozzá az új funkció. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Virtuális hálózati szolgáltatási végpont és a tűzfalon lehet egy Azure Cosmos DB fiókot?  

Igen, virtuális hálózati szolgáltatási végpont és a tűzfalon is létezhetnek. Általánosságban elmondható ellenőrizze, hogy portálhoz való hozzáférés mindig engedélyezve van a virtuális hálózati szolgáltatási végpont konfigurálása előtt ahhoz, hogy a metrikák a tárolóhoz társított megtekintése.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>I "hozzáférést biztosíthat más Azure-szolgáltatásokhoz való egy adott Azure-régió, a" Ha engedélyezve van a szolgáltatási végpont access Azure Cosmos DB?  

Erre azért szükség, ha azt szeretné, hogy az Azure Cosmos DB fiókját, az egyéb Azure első fél érhető el, szolgáltatások, például az Azure Data factory, az Azure Search vagy bármely szolgáltatás, amely csak a megadott Azure-régióban van telepítve.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Hány virtuális hálózati szolgáltatási végpont Azure Cosmos DB engedélyezett?  

64 virtuális hálózati szolgáltatási végpont Azure Cosmos DB fiók használata engedélyezett.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Mi az, hogy a kapcsolat végpontját, és a hálózati biztonsági csoport (NSG) szabályok között?  

Az Azure Cosmos Adatbázisba az NSG-szabályok lehetővé teszik a hozzáférés korlátozása adott Azure Cosmos DB IP-címtartomány. Ha azt szeretné, hogy megtalálható-e egy adott Azure Cosmos DB példány férhessen [régió](https://azure.microsoft.com/global-infrastructure/regions/), megadhatja a terület a következő formátumban: 

    AzureCosmosDB.<region name>

További NSG bővebben lásd: címkéket [virtuális hálózati szolgáltatás címkék](../virtual-network/security-overview.md#service-tags) cikk. 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Mi az az IP-tűzfal és a virtuális hálózati szolgáltatási végpont képességet közötti kapcsolat?  

Ez a két funkció kiegészíteni egymáshoz az Azure Cosmos DB eszközök-elszigetelés érdekében, és azokat biztonságos. IP használata tűzfal biztosítja, hogy statikus IP-címek is el Azure Cosmos DB fiók.  

### <a name="can-an-on-premise-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Egy helyi eszköz IP-címet, amely az Azure Virtual Network gateway(VPN) vagy Express route-átjárón keresztül csatlakozik Azure Cosmos-adatbázis fiók hozzáférhet?  

A helyszíni eszközök IP-cím vagy IP-címtartomány hozzá kell adni a statikus IP-címek listájához a Azure Cosmos DB fiók eléréséhez.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Mi történik, ha törli a virtuális hálózat az Azure Cosmos DB végpont telepítése tartalmaz?  

Virtuális hálózat törlése után az ACL-információk társított Azure Cosmos DB törlődik, és eltávolítja a virtuális hálózat és az Azure Cosmos DB fiók közötti kapcsolat. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Mi történik, ha engedélyezve van a virtuális hálózati szolgáltatási végpont Azure Cosmos DB fiókkal törölve van?

A metaadatok az adott Azure Cosmos DB fiókhoz társított töröltek, amelyek az alhálózat. És a végfelhasználó felelőssége, hogy az alhálózat és a használt virtuális hálózat törlése.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Peered virtuális hálózat használatával szolgáltatási végpont létre Azure Cosmos DB?  

Nem, csak közvetlenül a virtuális hálózati és az alhálózatok Azure Cosmos DB Szolgáltatásvégpontok hozhatnak létre.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Mi történik a forrás IP-címe például a virtuális gép, amelyen engedélyezve van Azure Cosmos DB szolgáltatásvégpont alhálózaton erőforrás?

Ha engedélyezve vannak a virtuális hálózati szolgáltatási végpont, a forrás IP-címek a virtuális hálózati alhálózat erőforrásainak vált, az Azure Virtual Network Magáncímeket nyilvános IPv4-címek az Azure Cosmos DB-forgalom.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Azure Cosmos DB az Azure virtuális hálózat, az ügyfél által megadott találhatók?  

Azure Cosmos-adatbázis egy olyan több-bérlős szolgáltatás nyilvános IP-címmel. Korlátozza a hozzáférést az Azure virtuális hálózat alhálózatának a szolgáltatási végpont szolgáltatással, ha elérése történik a Azure Cosmos DB fiókhoz az adott Azure virtuális hálózathoz és az alhálózatot.  Azure Cosmos DB-fiók nem található Azure virtuális hálózatban. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Mi történik, ha semmit lesz naplózva a napló Analytics/OMS Ha engedélyezve van?  

Azure Cosmos DB fogja leküldeni a naplók és IP-cím 403 esetén kérelem letiltotta a hozzáférés-vezérlési lista (nélkül az utolsó bájt).  

## <a name="next-steps"></a>További lépések
Azure Cosmos DB lásd: a tűzfal beállítása [támogatási tűzfal](firewall-support.md) cikk.

