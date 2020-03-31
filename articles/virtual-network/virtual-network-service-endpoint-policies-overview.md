---
title: Azure-beli virtuális hálózati szolgáltatásvégpontokra vonatkozó szabályzatok | Microsoft Docs
description: Tudnivalók az Azure-beli szolgáltatási erőforrásokba irányuló virtuális hálózati forgalom szolgáltatásvégpont-szabályzatokkal történő szűréséről
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 926da07ffaf0c61ca2a7fd02351ef3635ec4d73b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651294"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Virtuális hálózati szolgáltatás végpontszabályzatai az Azure Storage-hoz

Virtuális hálózat (VNet) szolgáltatás végpontszabályzatok lehetővé teszi, hogy szűrje a kimenő virtuális hálózati forgalmat az Azure Storage-fiókok szolgáltatás végponton keresztül, és lehetővé teszi az adatok kiszivárgását csak adott Azure Storage-fiókok. A végpontszabályzatok részletes hozzáférés-vezérlést biztosítanak az Azure Storage virtuális hálózati forgalmához, amikor szolgáltatásvégponton keresztül csatlakozik.

![Virtuális hálózati kimenő forgalom biztonságossá tétele az Azure Storage-fiókokba](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Ez a funkció általánosan elérhető az __Azure Storage-ban__ __az összes globális Azure-régióban.__

## <a name="key-benefits"></a>Főbb előnyök

A virtuális hálózati szolgáltatásvégpont-szabályzatok a következő előnyöket biztosítják:

- __Az Azure Storage virtuális hálózati forgalmának fokozott biztonsága__

  [A hálózati biztonsági csoportok Azure szolgáltatáscímkéi](https://aka.ms/servicetags) lehetővé teszik, hogy a virtuális hálózati kimenő forgalmat az Azure Storage adott régióira korlátozza. Ez azonban lehetővé teszi a forgalmat a kiválasztott Azure Storage-régió bármely fiókjába.
  
  A végpontszabályzatok lehetővé teszik, hogy adja meg az Azure Storage-fiókok, amelyek számára engedélyezett a virtuális hálózati kimenő hozzáférés, és korlátozza a hozzáférést az összes többi tárfiókok. Ez sokkal részletesebb biztonsági ellenőrzést biztosít az adatok virtuális hálózatról való kiszivárgásának védelméhez.

- __Méretezhető, magas rendelkezésre állású szabályzatok az Azure-szolgáltatások adatforgalmának szűréséhez__

   A végpontszabályzatok horizontálisan skálázható, magas rendelkezésre állású megoldásokat biztosítanak a virtuális hálózatokból a szolgáltatásvégpontokon keresztül az Azure-szolgáltatásokba érkező forgalom szűrésére. Nincs szükség további erőforrásokra, hogy központi hálózati berendezéseket kelljen fenntartani a virtuális hálózat ezen forgalmához.

## <a name="json-object-for-service-endpoint-policies"></a>JSON-objektum a szolgáltatásvégpont-házirendekhez
Vessünk egy gyors pillantást a Service Endpoint policy objektum.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Konfiguráció

-   Konfigurálhatja a végpontszabályzatokat, hogy korlátozza a virtuális hálózati forgalmat adott Azure Storage-fiókokra.
-   A végpontszabályzatot a virtuális hálózat egy alhálózatán kell konfigurálni. Az Azure Storage szolgáltatásvégpontjait engedélyezni kell az alhálózaton a szabályzat alkalmazásához.
-   Endpoint-szabályzat lehetővé teszi, hogy adott Azure Storage-fiókok engedélyezése lista, a resourceID formátum használatával. Korlátozhatja a hozzáférést a
    - az összes tárfiók egy előfizetésben<br>
      `E.g. /subscriptions/subscriptionId`

    - erőforráscsoport összes tárfiókja<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - egyéni tárfiók a megfelelő Azure Resource Manager resourceId listázásával. Ez lefedi a blobokba, táblákba, üzenetsorokba, fájlokba és az Azure Data Lake Storage Gen2-be irányuló forgalmat. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Alapértelmezés szerint ha nincs enek házirendek végpontokkal rendelkező alhálózathoz csatolva, a szolgáltatás összes tárfiókjához hozzáférhet. Amint konfigurált egy szabályzatot az alhálózaton, kizárólag a szabályzatban meghatározott erőforrásokhoz lehet hozzáférni az adott alhálózat számítási példányairól. Az összes többi tárfiókhoz való hozzáférés megtagadva.
-   A Service Endpoint-szabályzatok alhálózaton történő alkalmazásakor az Azure *Storage-szolgáltatás végponthatóköre* regionálisról **globálisra**frissül. Ez azt jelenti, hogy az Azure Storage-ba irányuló összes forgalom a szolgáltatás végpontja felett van biztosítva. A szolgáltatás végpontszabályzatai globálisan is alkalmazhatók, így minden olyan tárfiók, amely nem kifejezetten engedélyezett, megtagadva a hozzáférést. 
-   Egy alhálózaton több szabályzatot is életbe léptethet. Ha több házirend van társítva az alhálózathoz, a házirendek bármelyikében megadott erőforrásokvirtuális hálózati forgalma engedélyezett lesz. A szabályzatokban nem meghatározott szolgáltatási erőforrásokhoz való hozzáférést a rendszer megtagadja.

    > [!NOTE]  
    > A szolgáltatásvégpont-házirendek **engedélyezési házirendek,** így a megadott erőforrásokon kívül minden más erőforrás korlátozott. Győződjön meg arról, hogy az alkalmazások összes szolgáltatáserőforrás-függősége azonosítható és szerepel a házirendben.

- Csak az Azure Resource-modellt használó tárfiókokat lehet megadni a végpontszabályzatban. A klasszikus Azure Storage-fiókok nem támogatja az Azure Service Endpoint szabályzatok.
- Az RA-GRS másodlagos hozzáférést automatikusan engedélyezi a rendszer, ha az elsődleges fiók szerepel a listán.
- A tárfiókok a virtuális hálózattal megegyező vagy attól eltérő előfizetéshez vagy Azure Active Directory-bérlőhöz is tartozhatnak.

## <a name="scenarios"></a>Forgatókönyvek

- **Társviszonyban álló, csatlakoztatott vagy többszörös virtuális hálózatok**: A társviszonyban álló virtuális hálózatok forgalmának szűréséhez a végpontszabályzatokat egyenként kell érvényesíteni az egyes virtuális hálózatokon.
- **Internetes forgalom szűrése hálózati eszközökkel vagy az Azure tűzfallal:** Szűrje az Azure szolgáltatásforgalmát szabályzatokkal, szolgáltatásvégpontokon keresztül, és szűrje az internet vagy az Azure-forgalom többi részét készülékeken vagy az Azure tűzfalon keresztül.
- **A virtuális hálózatokba üzembe helyezett Azure-szolgáltatások forgalmának szűrése:** Jelenleg az Azure Service Endpoint-szabályzatok nem támogatottak a virtuális hálózatba telepített felügyelt Azure-szolgáltatások esetében. 
- **A helyszínről az Azure-szolgáltatásokba irányuló forgalom szűrése**: A szolgáltatásvégpont-szabályzatok csak a szabályzatokhoz társított alhálózatokról érkező forgalomra vonatkoznak. Az egyes Azure-beli szolgáltatási erőforrásokhoz való helyszíni hozzáférés engedélyezéséhez a forgalmat virtuális hálózati berendezésekkel vagy tűzfalakkal szűrheti.

## <a name="logging-and-troubleshooting"></a>Naplózás és hibaelhárítás
Szolgáltatásvégpont-szabályzatokhoz a központi naplózás nem érhető el. A szolgáltatásdiagnosztikai naplókkal kapcsolatos további részletekért tekintse meg a [szolgáltatásvégpontok naplózásával](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting) foglalkozó témakört.

### <a name="troubleshooting-scenarios"></a>Hibaelhárítási forgatókönyvek
- Hozzáférés megtagadva az előzetes verzióban (nem földrajzilag párosított régióban) működő tárfiókokhoz
  - Az Azure Storage globális szolgáltatáscímkék használatára való frissítésével a Szolgáltatásvégpont és így a Szolgáltatásvégpont-szabályzatok hatóköre globális. Így az Azure Storage-ba irányuló forgalom a szolgáltatásvégpontokon keresztül titkosítva van, és csak a szabályzatban kifejezetten felsorolt storage-fiókok férhetnek hozzá.
  - Explicit explicit explicit explicit allow list all the required Storage accounts to restore access.  
  - Forduljon az Azure ügyfélszolgálatához.
- A végpontszabályzatokban szereplő fiókokhoz való hozzáférés meg van tagadva
  - A hálózati biztonsági csoportok vagy a tűzfalak használatával történő szűrés blokkolhatja a hozzáférést
  - Ha a szabályzat eltávolítása/újbóli életbe léptetése miatt megszűnik a kapcsolat:
    - Ellenőrizze, hogy az Azure-szolgáltatás úgy van-e beállítva, hogy a virtuális hálózatról hozzáférést engedélyezze a végpontokon keresztül, vagy hogy az erőforrás alapértelmezett házirendje az Összes engedélyezése értékre *van-e*állítva.
    - Ellenőrizze, hogy a szolgáltatásdiagnosztika megjeleníti-e a végpontokon átmenő forgalmat.
    - Ellenőrizze, hogy a hálózati biztonsági csoport forgalmának naplói és a tárolási naplók a várakozásoknak megfelelően a végpontokon keresztül történő hozzáférést mutatják-e.
    - Forduljon az Azure ügyfélszolgálatához.
- A szolgáltatásvégpont-szabályzatokban nem szereplő fiókok hozzáférése meg van tagadva
  - Ellenőrizze, hogy az Azure Storage úgy van-e beállítva, hogy engedélyezze a hozzáférést a virtuális hálózatról a végpontokon keresztül, vagy hogy az erőforrás alapértelmezett házirendje az *Összes engedélyezése*értékre van-e állítva.
  - Győződjön meg arról, hogy a fiókok nem **klasszikus tárfiókok** szolgáltatásvégpont-szabályzatok az alhálózaton.
- Egy felügyelt Azure-szolgáltatás leállt, miután egy szolgáltatásvégpont-szabályzatot alkalmazott az alhálózaton keresztül
  - A felügyelt szolgáltatások jelenleg nem támogatottak a szolgáltatásvégpont-házirendek. *Nézd meg ezt a helyet a frissítéseket*.

## <a name="provisioning"></a>Kiépítés

A szolgáltatásvégpont-szabályzatokat az alhálózatokon egy olyan felhasználó konfigurálhatja, akinek írási hozzáférése van egy virtuális hálózathoz. További információkat is megtudhat az Azure [beépített szerepköreiről](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és az egyes engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről.

Virtuális hálózatok és az Azure Storage-fiókok lehetnek az azonos vagy különböző előfizetések, vagy az Azure Active Directory-bérlők.

## <a name="limitations"></a>Korlátozások

- Szolgáltatásvégpont-szabályzatokat csak az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális hálózatokon helyezhet üzembe.
- A virtuális hálózatoknak és a szolgáltatásvégpont-szabályzatnak ugyanabban a régióban kell lenniük.
- Csak akkor alkalmazhat szolgáltatásvégpont-szabályzatokat egy alhálózaton, ha a szolgáltatásvégpontok a szabályzatban szereplő Azure-szolgáltatásokhoz vannak konfigurálva.
- A saját helyszíni hálózatáról az Azure-szolgáltatásokba irányuló forgalomhoz nem használhat szolgáltatásvégpont-szabályzatokat.
- Az Azure által felügyelt szolgáltatások jelenleg nem támogatják az Endpoint-szabályzatokat. Ez magában foglalja a megosztott alhálózatokba (pl. *Azure HDInsight, Azure Batch, Azure ADDS, Azure APplication Gateway, Azure VPN-átjáró, Azure Firewall)* vagy a dedikált alhálózatokba (pl. *Azure App Service Environment, Azure Redis Cache, Azure API Management, Azure SQL MI, klasszikus felügyelt szolgáltatások)* telepített felügyelt szolgáltatásokat.

 > [!WARNING]
 > A virtuális hálózatokban üzembe helyezett Azure-szolgáltatások, mint például az Azure HDInsight, más Azure-szolgáltatásokhoz, például az Azure Storage-hoz férnek hozzá, hogy lekérjék az infrastruktúrával kapcsolatos követelményeket. A végpontszabályzatok bizonyos erőforrásokra történő korlátozása meggátolhatja, hogy a virtuális hálózatában üzembe helyezett Azure-szolgáltatások hozzáférjenek ezekhez az infrastruktúra-erőforrásokhoz.

- Klasszikus tárfiókok nem támogatottak a végpontszabályzatok. Alapértelmezés szerint a szabályzatok minden klasszikus tárfiókhoz megtagadják a hozzáférést. Ha az alkalmazásának hozzá kell férnie az Azure Resource Managerhez és a klasszikus tárfiókokhoz, ne használjon végpontszabályzatokat a köztük zajló forgalom vezérléséhez.

## <a name="pricing-and-limits"></a>Díjszabás és korlátok

A szolgáltatásvégpont-szabályzatok használata nem jár további költségekkel. A szolgáltatásvégpontok esetében az Azure-szolgáltatások (például az Azure Storage) aktuális díjszabási modellje az irányadó.

A következő korlátozásokat érvényesíti a rendszer a szolgáltatásvégpont-szabályokon: 

 |Erőforrás | Alapértelmezett korlát |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Következő lépések

- Tudnivalók [a virtuális hálózati szolgáltatásvégpont-szabályzatok konfigurálásáról](virtual-network-service-endpoint-policies-portal.md)
- További információk a [virtuális hálózati szolgáltatásvégpontokról](virtual-network-service-endpoints-overview.md)
