---
title: Biztonságos hozzáférés az Azure Cosmos DB-fiókot az Azure virtuális hálózati szolgáltatásvégpont |} A Microsoft Docs
description: Ez a dokumentum ismerteti az Azure Cosmos DB-virtuális hálózati szolgáltatásvégpont beállítása szükséges lépéseket.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: cc64bc1af6eee505ccf3d38e214c49ffb7e3304c
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404460"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Biztonságos hozzáférés az Azure Cosmos DB-fiókot az Azure virtuális hálózati szolgáltatásvégpont

Az Azure cosmos DB-fiókokhoz beállítható úgy, hogy engedélyezze a hozzáférést csak adott, az Azure virtuális hálózat alhálózatán. Engedélyezésével egy [szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy virtuális hálózatot és az alhálózatot az Azure cosmos DB, a forgalom egy optimális és biztonságos útvonal az Azure Cosmos DB-hez biztosított.  

Az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás. Több régióban replikálhatja az Azure Cosmos DB-fiókban található adatokat. Amikor az Azure Cosmos DB egy virtuális hálózati szolgáltatásvégpont van beállítva, mindegyik virtuális hálózat specicifc alhálózathoz tartozó IP-címekről származó engedélyezi a hozzáférést. Az alábbi képen olyan bemutatásáért, amely rendelkezik a virtuális hálózati szolgáltatásvégpont engedélyezve van Azure Cosmos DB:

![virtuális hálózati végpont architektúrája](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

Miután az Azure Cosmos DB-fiók beállítása a virtuális hálózati szolgáltatásvégpont, csak az adott alhálózatról is elérhető, a rendszer eltávolítja az összes nyilvános/internet-hozzáféréssel. Ismerje meg a részletes információkat a szolgáltatásvégpontokról, tekintse meg az Azure-ban [virtuális hálózati Szolgáltatásvégpontok áttekintése](../virtual-network/virtual-network-service-endpoints-overview.md) cikk.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Szolgáltatásvégpont konfigurálása az Azure portal használatával
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>A meglévő Azure virtuális hálózat és alhálózat szolgáltatásvégpont beállítása

1. A **összes erőforrás** panelen keresse meg a virtuális hálózati szolgáltatásvégpont beállítása az Azure Cosmos DB szeretné. Keresse meg a **Szolgáltatásvégpontokat** panelen, és győződjön meg arról, hogy a virtuális hálózat alhálózatának engedélyezve van a "Azure.CosmosDB" szolgáltatásvégpontot.  

   ![Erősítse meg a szolgáltatásvégpont engedélyezve van](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. A **összes erőforrás** panelen keresse meg az Azure Cosmos DB-fiók szeretné biztonságossá tételéhez.  

3. Mielőtt engedélyezné a virtuális hálózati szolgáltatásvégpont, másolja a használatalapú Azure Cosmos DB-fiókjához társított IP-tűzfal információit. IP-tűzfalon szolgáltatásvégpont beállítása után újra engedélyezheti.  

4. Válassza ki **tűzfalak és virtuális hálózatok** beállítások menüjében, majd engedélyezze a hozzáférést **kiválasztott hálózatok**.  

3. Válassza ki a hozzáférést egy meglévő virtuális hálózat alhálózatához, virtuális hálózatok **meglévő Azure virtuális hálózat hozzáadása**.  

4. Válassza ki a **előfizetés** , amely az Azure virtual network hozzáadni kívánt. Válassza ki az Azure **virtuális hálózatok** és **alhálózatok** , amelyeket meg szeretne-e az Azure Cosmos DB-fiók eléréséhez. Ezután válassza ki **engedélyezése** kiválasztott hálózatokon a Szolgáltatásvégpontok engedélyezése "Microsoft.AzureCosmosDB". Ha elkészült, válassza ki a **Hozzáadás**.  

   ![Válassza ki a virtuális hálózatot és alhálózatot](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Azure Cosmos DB-hez készült service-végpont korábban nincs beállítva a kiválasztott Azure virtuális hálózatok és alhálózatok, ha ez a művelet részeként konfigurálható. Hozzáférés engedélyezése akár 15 percet igénybe vehet. Nagyon fontos, az IP-tűzfal letiltása után jegyezze fel a tartalmát renabling ACL-tűzfal őket később. 

   ![virtuális hálózat és alhálózat sikeresen konfigurálva](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Az Azure Cosmos DB-fiókot most csak akkor engedélyezi a kiválasztott alhálózat forgalmát. Ha korábban rendelkezett engedélyezett IP-tűzfalon, engedélyezze újból őket korábbi információk segítségével.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Egy új Azure virtuális hálózat és alhálózat szolgáltatásvégpont beállítása

1. A **összes erőforrás** panelen keresse meg az Azure Cosmos DB-fiók szeretné biztonságossá tételéhez.  

   > [!NOTE]
   > Ha rendelkezik egy meglévő IP-tűzfalon konfigurálva az Azure Cosmos DB-fiókot, vegye figyelembe a tűzfal-konfiguráció, távolítsa el az IP-tűzfalon, és engedélyeznie kell a szolgáltatásvégpontot. Ha a szolgáltatásvégpont disbling a tűzfal nem engedélyezi, adott ip-címtartomány továbbítani a forgalmat a virtuális IP-identitás elvész, és a egy IP-szűrő hibaüzenettel helyezés. Ez a hiba elkerülése érdekében minden esetben tiltsa le a tűzfalszabályokat, hogy másolja őket, az alhálózat és a végül ACL Cosmos DB-ből az alhálózat szolgáltatásvégpont engedélyezése. Adja hozzá az ACL és szolgáltatásvégpont beállítása után engedélyezheti újra az IP-tűzfal ismét szükség esetén.

2. Mielőtt engedélyezné a virtuális hálózati szolgáltatásvégpont, másolja a használatalapú Azure Cosmos DB-fiókjához társított IP-tűzfal információit. IP-tűzfalon szolgáltatásvégpont beállítása után újra engedélyezheti.  

3. Válassza ki **tűzfalak és az Azure virtuális hálózatok** beállítások menüjében, majd engedélyezze a hozzáférést **kiválasztott hálózatok**.  

4. Válassza ki a hozzáférést egy új Azure virtuális hálózatot, virtuális hálózatok **hozzáadása új virtuális hálózat**.  

5. Adja meg az új virtuális hálózat létrehozásához szükséges adatait, és kattintson a létrehozás. Az alhálózat "Microsoft.AzureCosmosDB" engedélyezett szolgáltatásvégponttal jön.

   ![Válassza ki a virtuális hálózatot és alhálózatot az új virtuális hálózat](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Engedélyezze a hozzáférést az Azure Portalon

Azure virtuális hálózati Szolgáltatásvégpontok engedélyezve vannak az Azure Cosmos DB-adatbázisfiók, miután a portál vagy más Azure-szolgáltatásokhoz való hozzáférés alapértelmezés szerint le van tiltva. A Azure Cosmos DB-fiókot a megadott alhálózati kívüli gépek való hozzáférés blokkolva vannak, beleértve a hozzáférést a portálról.

![Engedélyezi a hozzáférést a portálról](./media/vnet-service-endpoint/allow-access-from-portal.png)

Az Azure Cosmos DB-fiók más Azure-szolgáltatásokhoz hasonlóan az Azure Search által használt, vagy a Stream analytics vagy a Power bi-ban érhető el, ha engedélyezi a hozzáférést ellenőrzésével **Azure-szolgáltatásokhoz való hozzáférés engedélyezése**.

Annak érdekében, hogy hozzáférése van az Azure Cosmos DB-metrikák a portálról, engedélyeznie kell **engedélyezi a hozzáférést az Azure Portalra** beállítások. Ezek a beállítások kapcsolatos további információkért lásd: [kapcsolatok az Azure Portalról](firewall-support.md#connections-from-the-azure-portal) és [Azure PaaS-szolgáltatások kapcsolatait](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services) szakaszokat. Miután kijelölte a hozzáférést, jelölje ki **mentése** a beállítások mentéséhez.

## <a name="remove-a-virtual-network-or-subnet"></a>Egy virtuális hálózat vagy alhálózat törlése 

1. A **összes erőforrás** panelen, és keresse meg az Azure Cosmos DB-fiók, amelynek a Szolgáltatásvégpontok hozzárendelve.  

2. Válassza ki **tűzfalak és virtuális hálózatok** beállítások menüjében.  

3. Egy virtuális hálózat vagy alhálózat szabály eltávolításához válassza az "..." mellett a virtuális hálózat vagy alhálózat, majd válassza ki **eltávolítása**.

   ![Virtuális hálózat eltávolítása](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Kattintson a **mentése** alkalmazza a módosításokat.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Szolgáltatásvégpont konfigurálása az Azure PowerShell-lel 

A következő lépések segítségével-szolgáltatásvégpontot az Azure Cosmos DB-fiók konfigurálása az Azure PowerShell-lel:  

1. Telepítse a legújabb [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) és [bejelentkezési](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Győződjön meg arról, vegye figyelembe az IP-tűzfal beállításait, és az IP-tűzfalon teljesen törölni a fiók szolgáltatásvégpont engedélyezése előtt.

  > [!NOTE]
  > Ha rendelkezik egy meglévő IP-tűzfalon konfigurálva az Azure Cosmos DB-fiókot, vegye figyelembe a tűzfal-konfiguráció, távolítsa el az IP-tűzfalon, és engedélyeznie kell a szolgáltatásvégpontot. Ha a szolgáltatásvégpont disbling a tűzfal nem engedélyezi, adott ip-címtartomány továbbítani a forgalmat a virtuális IP-identitás elvész, és a egy IP-szűrő hibaüzenettel helyezés. Ez a hiba elkerülése érdekében minden esetben tiltsa le a tűzfalszabályokat, hogy másolja őket, az alhálózat és a végül ACL Cosmos DB-ből az alhálózat szolgáltatásvégpont engedélyezése. Adja hozzá az ACL és szolgáltatásvégpont beállítása után engedélyezheti újra az IP-tűzfal ismét szükség esetén.

2. Mielőtt engedélyezné a virtuális hálózati szolgáltatásvégpont, másolja a használatalapú Azure Cosmos DB-fiókjához társított IP-tűzfal információit. IP-tűzfal engedélyezze újra lesz szolgáltatásvégpont konfigurálása után.  

3. A virtuális hálózat egy meglévő alhálózat szolgáltatásvégpont engedélyezése.  

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

4. Felkészülés az ACL-engedélyezésre a cosmos DB-fiók azáltal, hogy a virtuális hálózatot és alhálózatot kell-e a szolgáltatásvégpont engedélyezve van az Azure Cosmos DB.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. Azure Cosmos DB-fiók tulajdonságainak lekéréséhez futtassa a következő parancsmagot:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. A későbbi felhasználás céljára változók inicializálása. Állítsa be összes változót a meglévő fiók definícióból, ha több helyen van, hozzá kell őket a tömb részeként. Ebben a lépésben konfigurálnia is virtuális hálózati szolgáltatásvégpont "accountVNETFilterEnabled" változó "True" értékre állításával. Ezt az értéket később "isVirtualNetworkFilterEnabled" paraméter van hozzárendelve.  

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. Azure Cosmos DB-fiók tulajdonságainak frissítése az új konfigurációval a következő parancsmag futtatásával: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. Győződjön meg arról, hogy a virtuális hálózati szolgáltatásvégpontot az előző lépésben beállított frissül az Azure Cosmos DB-fiókot az alábbi parancsot futtassa:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Az Azure Cosmos DB-fiókot, amely rendelkezik az IP-tűzfal engedélyezve van a virtuális hálózati szolgáltatásvégpont hozzáadása

1. Először tiltsa le az Azure Cosmos DB-fiók IP-tűzfal hozzáférést.  

2. A korábbi szakaszokban ismertetett módszerek egyikének használatával engedélyezze a virtuális hálózati végpontot, az Azure Cosmos DB-fiókot.  

3. Engedélyezze újra az IP-tűzfal hozzáférést. 

## <a name="test-the-access"></a>A hozzáférési tesztelni

Ha a várt módon konfigurált Szolgáltatásvégpontok az Azure Cosmos DB ellenőrzéséhez használja a következő lépéseket:

* Állítsa be a virtuális hálózat két alhálózattal, frontend és backend, a háttérrendszer alhálózatának Cosmos DB-szolgáltatásvégpont engedélyezése.  

* Engedélyezze a hozzáférést a Cosmos DB-fiókban a háttérrendszer alhálózatának.  

* Hozzon létre két virtuálisgép-egy háttérbeli alhálózatot, míg a másik az előtér-alhálózatot.  

* Próbálja ki a virtuális gépeket is az Azure Cosmos DB-fiók elérése. A háttérrendszer alhálózatának létrehozott virtuális gép azonban nem az előtérben levő alhálózathoz létrehozott kapcsolódni tud kell lennie. A kérelem a 404-es hiba fog, amikor megpróbál kapcsolódni az előtér-alhálózatot, amely megerősíti, hogy a hozzáférést az Azure Cosmos DB védik a virtuális hálózati szolgáltatásvégpont-hez. A gép a háttérrendszer alhálózatának fogja tudni jól működnek.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Mi történik, amikor fér hozzá az Azure Cosmos DB-fiókot, amely rendelkezik a virtuális hálózati hozzáférés-vezérlési lista (ACL) engedélyezve van?  

A HTTP 404-es hibát ad vissza.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Engedélyezi a hozzáférést az Azure Cosmos DB-fiók egy másik régióban található különböző régiókban létrehozott virtuális hálózat alhálózatain is? Például ha az Azure Cosmos DB-fiókot az USA nyugati RÉGIÓJA és USA keleti Régiójában és virtuális hálózati is több régióban, a virtuális hálózat hozzáférhet az Azure Cosmos DB?  

Igen, különböző régiókban létrehozott virtuális hálózatok érhetik el az új funkciót. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Tűzfal és a virtuális hálózati szolgáltatásvégpontot lehet Azure Cosmos DB-fiók?  

Igen, virtuális hálózati szolgáltatásvégpont és a egy tűzfal is elérhetőek. Általánosságban elmondható biztosítania kell, hogy portáljához való hozzáférésre, mindig engedélyezve van a virtuális hálózati szolgáltatásvégpont beállítása előtt ahhoz, hogy a tároló társítva a metrikákat tekinthet meg.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Is szeretnék "Accept-kapcsolatok a nyilvános Azure-adatközpontok belül" Ha a szolgáltatás-végponti hozzáférés engedélyezve van az Azure Cosmos DB?  

Erre azért szükség, ha azt szeretné, hogy az Azure Cosmos DB-fiók, Azure első felet elérésének services, Azure Data factory, az Azure Search vagy bármely szolgáltatás, amely csak a megadott Azure-régióban üzemel.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Hány virtuális hálózati Szolgáltatásvégpontok engedélyezettek az Azure Cosmos DB?  

64 virtuális hálózati Szolgáltatásvégpontok az Azure Cosmos DB-fiók használata engedélyezett.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Mi a kapcsolat végpontját, és a hálózati biztonsági csoport (NSG) szabályai között?  

Az NSG-szabályok az Azure Cosmos DB teszi lehetővé a hozzáférés korlátozása adott Azure Cosmos DB IP-címtartomány. Ha azt szeretné, hogy engedélyezze a hozzáférést egy Azure Cosmos DB-példányra, amely szerepel az adott [régió](https://azure.microsoft.com/global-infrastructure/regions/), megadhatja a régiót az alábbi formátumban: 

    AzureCosmosDB.<region name>

További NSG bővebben lásd a címkék [virtuális hálózat szolgáltatáscímkék](../virtual-network/security-overview.md#service-tags) cikk. 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Mi a kapcsolatát egy IP-tűzfal és a virtuális hálózati szolgáltatási végpont funkció?  

Ezen két funkció kiegészíti egymást, győződjön meg, hogy az Azure Cosmos DB eszközök elkülönítési és védelemmel ellátni. IP használata tűzfal biztosítja, hogy statikus IP-címek érhessék el az Azure Cosmos DB-fiókot.  

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Hozzáférhet egy helyszíni eszköz IP-cím, amely az Azure Virtual Network gateway(VPN) vagy Express route-átjárón keresztül csatlakozik az Azure Cosmos DB-fiókot?  

A helyszíni eszköz IP-cím vagy IP-címtartomány kell adni az IP-címek statikus listáját az Azure Cosmos DB-fiók eléréséhez.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Mi történik, ha töröl egy virtuális hálózatot, amely rendelkezik a szolgáltatás végpont beállítása az Azure Cosmos DB?  

Virtuális hálózat törlésekor az ACL-információk társított, hogy az Azure Cosmos DB törlődik, és eltávolítja a virtuális hálózat és az Azure Cosmos DB-fiók közötti kapcsolat. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Mi történik, ha az Azure Cosmos DB-fiókot, amely rendelkezik a virtuális hálózati szolgáltatásvégpontot engedélyezett törlődik?

A metaadatok az adott Azure Cosmos DB-fiókhoz társított törlődik az alhálózatról. És a felhasználó felelőssége, hogy az alhálózat és a használt virtuális hálózat törlése.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Használható a virtuális társhálózatba tartozó virtuális hálózati szolgáltatásvégpont létrehozása az Azure Cosmos DB?  

Nem, csak a közvetlen virtuális hálózat és az alhálózatokat hozhat létre Azure Cosmos DB-Szolgáltatásvégpontok.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Mi történik, a forrás IP-erőforrás, például az Azure Cosmos DB-hoz engedélyezett szolgáltatásvégponttal rendelkező alhálózaton lévő virtuális gép?

Ha virtuális hálózati Szolgáltatásvégpontok engedélyezve vannak, a forrás IP-címek a virtuális hálózat alhálózat erőforrásainak az Azure virtuális hálózat privát címeire nyilvános IPV4-címek az Azure Cosmos DB-forgalom fognak váltani.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Azure Cosmos DB az Azure virtuális hálózat, az ügyfél által biztosított található?  

Azure Cosmos DB a több-bérlős szolgáltatás, amely egy nyilvános IP-címet. Korlátozza a hozzáférést az Azure-beli virtuális hálózat alhálózatához a service-végpont funkció használatával, amikor hozzáférési korlátozódik Azure Cosmos DB-fiókja az adott Azure Virtual Network és az alhálózatán.  Azure Cosmos DB-fiók nem található a kiválasztott Azure virtuális hálózatban. 

### <a name="what-if-anything-will-be-logged-in-log-analytics-if-it-is-enabled"></a>Mi történik, ha bármi lesz naplózva a Log Analyticsben, ha engedélyezve van?  

Az Azure Cosmos DB fogja leküldeni a naplók a 403-as állapot kérelem ACL által blokkolt IP-cím (nem az utolsó oktettet).  

## <a name="next-steps"></a>További lépések
Az Azure Cosmos DB-hez lásd: a tűzfal konfigurálása [tűzfal-támogatás](firewall-support.md) cikk.

