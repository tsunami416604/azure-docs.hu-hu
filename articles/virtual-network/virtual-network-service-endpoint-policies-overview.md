---
title: Azure-beli virtuális hálózatok szolgáltatásvégpontjaira vonatkozó szabályzatok | Microsoft Docs
description: Tudnivalók az Azure-beli szolgáltatási erőforrásokba irányuló virtuális hálózati forgalom szolgáltatásvégpont-szabályzatokkal történő szűréséről
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumeet.mittal
ms.openlocfilehash: 619b9b68a5c4e897642e1f84c25c2822d8291400
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104488"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Virtuális hálózati szolgáltatásvégpont-szabályzatok (előzetes verzió)

A virtuális hálózati (VNet) szolgáltatásvégpont-szabályzatok lehetővé teszik az Azure-szolgáltatásokba irányuló virtuális hálózati forgalom szűrését, mivel csak bizonyos Azure-beli szolgáltatási erőforrásokat engednek át a szolgáltatásvégpontokon. A végpontszabályzatok lehetővé teszik az Azure-szolgáltatásokba irányuló virtuális hálózati forgalom részletes hozzáférés-vezérlését.

Ez a szolgáltatás __előzetes kiadásban__ érhető el a következő Azure-szolgáltatásokhoz és -régiókhoz:

__Az Azure Storage__: WestCentralUS, WestUS2.

Az előzetes verzióval kapcsolatos legfrissebb értesítésekért tekintse meg az [Azure virtuális hálózati frissítésekkel kapcsolatos](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.

> [!NOTE]  
> Az előzetes verzió során a virtuális hálózati szolgáltatásvégpontok rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadásétól. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Főbb előnyök

A virtuális hálózati szolgáltatásvégpont-szabályzatok a következő előnyöket biztosítják:

- __Nagyobb biztonság az Azure-szolgáltatásokba irányuló virtuális hálózati adatforgalomnak__

  [A hálózati biztonság csoportokhoz elérhető Azure-szolgáltatáscímkék](https://aka.ms/servicetags) lehetővé teszik, hogy a virtuális hálózatok kimenő forgalmát egyes Azure-szolgáltatásokra korlátozza, ez a forgalom azonban egy adott Azure-szolgáltatás bármely erőforrására irányulhat. 
  
  A szolgáltatásvégpont-szabályzatokkal most megteheti, hogy a virtuális hálózat kimenő forgalmának hozzáférését egyes Azure-erőforrásokra korlátozza. Így sokkal inkább a kezében tarthatja a virtuális hálózatban elért adatok védelmét. 

- __Méretezhető, magas rendelkezésre állású szabályzatok az Azure-szolgáltatások adatforgalmának szűréséhez__

   A végpontszabályzatok horizontálisan skálázható, magas rendelkezésre állású megoldásokat biztosítanak a virtuális hálózatokból a szolgáltatásvégpontokon keresztül az Azure-szolgáltatásokba érkező forgalom szűrésére. Nincs szükség további erőforrásokra, hogy központi hálózati berendezéseket kelljen fenntartani a virtuális hálózat ezen forgalmához.

## <a name="configuration"></a>Konfiguráció

- A végpontszabályzatokat konfigurálhatja úgy, hogy a virtuális hálózatok forgalmát egyes Azure-szolgáltatásokra korlátozzák. Az előzetes verzió az Azure Storage végpontszabályzatait támogatja. 
- A végpontszabályzatot a virtuális hálózat egy alhálózatán kell konfigurálni. A szolgáltatásvégpontok számára az alhálózaton kell engedélyezni a szabályzat alkalmazását a szabályzatban szerelő összes Azure-szolgáltatásra.
- A végpontszabályzat lehetővé teszi bizonyos Azure-beli szolgáltatási erőforrások engedélyezését az erőforrás-azonosító formátum használatával. Dönthet úgy, hogy egy adott előfizetés vagy erőforráscsoport összes erőforrásának hozzáférését korlátozza, de a korlátozás vonatkozhat egy-egy erőforrásra is. 
- Alapértelmezett esetben, ha nincsenek szabályzatok csatolva egy végponttal rendelkező alhálózathoz, akkor a szolgáltatásban megtalálható összes erőforráshoz hozzáférhet. Amint konfigurált egy szabályzatot az alhálózaton, kizárólag a szabályzatban meghatározott erőforrásokhoz lehet hozzáférni az adott alhálózat számítási példányairól. A rendszer az érintett szolgáltatás minden más erőforrásának hozzáférését megtagadja. 
- Az Azure-beli szolgáltatási erőforrásokba irányuló forgalmat azokban a régiókban szűrheti, amelyekben a szolgáltatásvégpont konfigurálva van. A más régiókban található szolgáltatás-erőforrásokhoz való hozzáférést a rendszer alapértelmezés szerint engedélyezi. 

  > [!NOTE]  
  > Annak érdekében, hogy a virtuális hálózatok csakis a szolgáltatásvégpont-régiókban található Azure-erőforrásokhoz férjenek hozzá, úgy kell konfigurálnia a hálózati biztonsági csoportok szabályait, hogy azok kizárólag a szolgáltatásvégpont-régiókba irányuló forgalmat engedélyezzék (ezt [szolgáltatáscímkékkel](security-overview.md#service-tags) lehet beállítani).

- Egy alhálózaton több szabályzatot is életbe léptethet. Ha több szabályzat van egy alhálózathoz rendelve, minden olyan virtuális hálózati forgalom engedélyezve lesz, amely az ugyanazon szolgáltatáshoz tartozó, a szabályzatok valamelyikében szereplő erőforrásra irányul. A szabályzatokban nem meghatározott szolgáltatási erőforrásokhoz való hozzáférést a rendszer megtagadja. 

  > [!NOTE]  
  > A szabályzat csak a virtuális hálózatról engedélyezi a felsorolt szolgáltatási erőforrásokhoz való hozzáférést. A rendszer minden más, a szolgáltatásba irányuló forgalmat megtagad, ha bizonyos erőforrásokat hozzáad a szabályzathoz. Győződjön meg arról, hogy az alkalmazások szolgáltatási erőforrásokhoz kapcsolódó összes függősége azonosítható és felsorolható a szabályzatban.

  > [!WARNING]  
  > A virtuális hálózatokban üzembe helyezett Azure-szolgáltatások, mint például az Azure HDInsight, más Azure-szolgáltatásokhoz, például az Azure Storage-hoz férnek hozzá, hogy lekérjék az infrastruktúrával kapcsolatos követelményeket. A végpontszabályzatok bizonyos erőforrásokra történő korlátozása meggátolhatja, hogy a virtuális hálózatában üzembe helyezett szolgáltatások hozzáférjenek ezekhez az infrastruktúra-erőforrásokhoz. Az adott szolgáltatásokra vonatkozó [korlátozásokat itt találja](#limitations). Az előzetes verzióban a szolgáltatásvégpont-szabályzatok nem támogatják a virtuális hálózatban üzembe helyezett felügyelt Azure-szolgáltatásokat.

- Azure Storage esetén: 
  -  A hozzáférést a tárfiók Azure Resource Manager *resourceId* értékének felsorolásával korlátozhatja. Ez lefedi a blobokba, táblákba, üzenetsorokba, fájlokba és az Azure Data Lake Storage Gen2-be irányuló forgalmat.

     Az Azure Storage-fiókok például a következő módon is felsorolhatók a végpontszabályzat definíciójában:
    
     Adott tárfiók engedélyezéséhez:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Adott előfizetésben és erőforráscsoportban található összes fiók engedélyezéséhez: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Egy előfizetésben található összes fiók engedélyezéséhez: `/subscriptions/subscriptionId`
    
- Csak az Azure Resource-modellt használó tárfiókokat lehet megadni a végpontszabályzatban.  

  > [!NOTE]  
  > A klasszikus tárfiókokhoz való hozzáférést a végpontszabályzatok meggátolják.

- A felsorolásban szereplő fiók elsődleges helyének a szolgáltatásvégpont alhálózathoz földrajzilag párosított régiójában kell lennie. 

  > [!NOTE]  
  > A szabályzatok lehetővé teszik más régiókból származó szolgáltatás-erőforrások meghatározását. A virtuális hálózat Azure-szolgáltatásokhoz való hozzáférését csak a földrajzilag párosított régiókra szűri a rendszer. Ha a hálózati biztonsági csoportok nincsenek az Azure Storage földrajzilag párosított régióira korlátozva, a virtuális hálózat az összes földrajzilag párosított régión kívüli tárfiókhoz hozzáférhet.

- Az RA-GRS másodlagos hozzáférést automatikusan engedélyezi a rendszer, ha az elsődleges fiók szerepel a listán. 
- A tárfiókok a virtuális hálózattal megegyező vagy attól eltérő előfizetéshez vagy Azure Active Directory-bérlőhöz is tartozhatnak. 

## <a name="limitations"></a>Korlátozások

- Szolgáltatásvégpont-szabályzatokat csak az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális hálózatokon helyezhet üzembe.
- A virtuális hálózatoknak és a szolgáltatásvégpont-szabályzatnak ugyanabban a régióban kell lenniük.
- Csak akkor alkalmazhat szolgáltatásvégpont-szabályzatokat egy alhálózaton, ha a szolgáltatásvégpontok a szabályzatban szereplő Azure-szolgáltatásokhoz vannak konfigurálva.
- A saját helyszíni hálózatáról az Azure-szolgáltatásokba irányuló forgalomhoz nem használhat szolgáltatásvégpont-szabályzatokat.
- A végpontszabályzatokat nem lehet alkalmazni olyan, felügyelt Azure-szolgáltatásokkal rendelkező alhálózatokon, amelyeknek Azure-szolgáltatásoktól kell lekérniük az infrastruktúrára vonatkozó követelményeket. 

  > [!WARNING]  
  > A virtuális hálózatokban üzembe helyezett Azure-szolgáltatások, mint például az Azure HDInsight, más Azure-szolgáltatásokhoz, például az Azure Storage-hoz férnek hozzá, hogy lekérjék az infrastruktúrával kapcsolatos követelményeket. A végpontszabályzatok bizonyos erőforrásokra történő korlátozása meggátolhatja, hogy a virtuális hálózatában üzembe helyezett Azure-szolgáltatások hozzáférjenek ezekhez az infrastruktúra-erőforrásokhoz.
  
  - Egyes Azure-szolgáltatások más számítási példányokkal rendelkező alhálózatokon is üzembe helyezhetők. Bizonyosodjon meg róla, hogy a rendszer nem alkalmaz végpontszabályzatokat az alhálózaton, ha azon az alább felsorolt felügyelt szolgáltatások vannak üzembe helyezve.
   
    - Azure HDInsight
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Azure Application Gateway (Azure Resource Manager)
    - Azure VPN Gateway (Azure Resource Manager)
    - Azure Firewall

  - Bizonyos Azure-szolgáltatások dedikált alhálózatokon kerülnek üzembe helyezésre. Az előzetes verzióban az alábbiakban felsorolt összes ilyen szolgáltatás esetében blokkolva vannak a végpontszabályzatok. 

     - Azure App Service Environment
     - Azure Rediscache
     - Azure API Management
     - Felügyelt Azure SQL-példány
     - Azure Active Directory tartományi szolgáltatások
     - Azure Application Gateway (klasszikus)
     - Azure VPN Gateway (klasszikus)

- Azure Storage: A szolgáltatásvégpont-szabályzatra a klasszikus tárfiókok nem támogatottak. Alapértelmezés szerint a szabályzatok minden klasszikus tárfiókhoz megtagadják a hozzáférést. Ha az alkalmazásának hozzá kell férnie az Azure Resource Managerhez és a klasszikus tárfiókokhoz, ne használjon végpontszabályzatokat a köztük zajló forgalom vezérléséhez. 

## <a name="nsgs-with-service-endpoint-policies"></a>Szolgáltatásvégpont-szabályzatokkal rendelkező NSG-k
- Alapértelmezés szerint az NSG-k engedélyezik a kimenő internetforgalmat, beleértve az Azure-szolgáltatások felé irányuló virtuális hálózati forgalmat is.
- Ha szeretne megtagadni minden kimenő internetforgalmat, és csak a meghatározott Azure-beli szolgáltatási erőforrások felé irányuló forgalmat szeretné engedélyezni: 

  1. lépés: NSG-ket csak Azure-szolgáltatások végpont régiókban kimenő forgalom engedélyezésére konfigurálja *Azure szolgáltatáscímkék*. További információért lásd az [NSG-khez elérhető szolgáltatáscímkéket](https://aka.ms/servicetags) ismertető szakaszt.
      
  A hálózati biztonsági csoportok olyan szabályai, amelyek a végpontrégiókra korlátozzák a hozzáférést, a következőképpen néznek ki:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  2. lépés: A szolgáltatásvégpont-szabályzatra való hozzáférést csak adott Azure-szolgáltatási erőforrások vonatkoznak.

  > [!WARNING]  
  > Ha a hálózati biztonsági csoport nem úgy van konfigurálva, hogy a végpontrégiókra korlátozza egy virtuális hálózat Azure-szolgáltatásokhoz való hozzáférését, más régiókban is hozzáférhet a szolgáltatási erőforrásokhoz, még akkor is, ha a szolgáltatásvégpont-szabályzat életbe lépett.

## <a name="scenarios"></a>Forgatókönyvek

- **Társviszonyban álló, csatlakoztatott vagy többszörös virtuális hálózatok**: A forgalom a társviszonyban álló virtuális hálózatba szűréséhez, szolgáltatásvégpont-szabályzatra kell alkalmazni egyenként ezeket a virtuális hálózatokat.
- **Szűrés az internetes forgalmat a hálózati berendezések vagy az Azure-tűzfal**: Azure-szolgáltatások forgalmára, a házirendek szűrése végpontok keresztül, és rest berendezések vagy az Azure-tűzfalon keresztül Internet vagy az Azure hálózati forgalom szűrése. 
- **Forgalom az Azure-szolgáltatások virtuális hálózatokon üzembe helyezett szűrés**: Az előzetes verzióban szolgáltatásvégpont-szabályzat nem támogatottak a felügyelt Azure-szolgáltatásokért, hogy a rendszer üzembe helyezi a virtuális hálózat. 
 Tekintse meg az egyes szolgáltatásokra vonatkozó [korlátozásokat.](#Limitations)
- **Forgalom szűrése az Azure-szolgáltatások helyszíni**: Szolgáltatásvégpont-szabályzat csak alkalmazása a forgalomra a házirendek társított alhálózatokat. Az egyes Azure-beli szolgáltatási erőforrásokhoz való helyszíni hozzáférés engedélyezéséhez a forgalmat virtuális hálózati berendezésekkel vagy tűzfalakkal szűrheti.

## <a name="logging-and-troubleshooting"></a>Naplózás és hibaelhárítás
Szolgáltatásvégpont-szabályzatokhoz a központi naplózás nem érhető el. A szolgáltatásdiagnosztikai naplókkal kapcsolatos további részletekért tekintse meg a [szolgáltatásvégpontok naplózásával](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting) foglalkozó témakört.

### <a name="troubleshooting-scenarios"></a>Hibaelhárítási forgatókönyvek
- A végpontszabályzatokban nem szereplő tárfiókokhoz való hozzáférés engedélyezése
  - A hálózati biztonsági csoportok más régiókban is lehetővé tehetik az internethez vagy Azure Storage-fiókokhoz való hozzáférést.
  - A hálózati biztonsági csoportokat úgy kell konfigurálni, hogy megtagadjanak minden kimenő internetes forgalmat, és csak az egyes Azure Storage-régiókba irányuló forgalmat engedélyezzék. További információkért lásd: a hálózati biztonsági csoportok.
- A végpontszabályzatokban szereplő fiókokhoz való hozzáférés meg van tagadva
  - A hálózati biztonsági csoportok vagy a tűzfalak használatával történő szűrés blokkolhatja a hozzáférést
  - Ha a szabályzat eltávolítása/újbóli életbe léptetése miatt megszűnik a kapcsolat:
    - Ellenőrizze, hogy az Azure-szolgáltatás úgy van-e konfigurálva, hogy engedélyezze a virtuális hálózatokról való hozzáférést a végpontokon keresztül, illetve hogy az erőforrás alapértelmezett szabályzatában az *összes engedélyezésének* lehetősége van-e megadva.
      > [!NOTE]      
      > A szolgáltatás-erőforrásokat virtuális hálózatokhoz kell kötni, hogy hozzáférést lehessen biztosítani a végpontszabályzatokkal. Biztonságra vonatkozó ajánlott eljárásként azt javasoljuk, hogy a szolgáltatás-erőforrásokat a megbízható hálózataihoz, például az Azure-beli virtuális hálózatokhoz a végpontokon keresztül kösse le, vagy a helyszínen egy IP-tűzfalon keresztül.
  
    - Ellenőrizze, hogy a szolgáltatásdiagnosztika megjeleníti-e a végpontokon átmenő forgalmat.
    - Ellenőrizze, hogy a hálózati biztonsági csoport forgalmának naplói és a tárolási naplók a várakozásoknak megfelelően a végpontokon keresztül történő hozzáférést mutatják-e.
    - Forduljon az Azure ügyfélszolgálatához.
- A szolgáltatásvégpont-szabályzatokban nem szereplő fiókok hozzáférése meg van tagadva
  - A hálózati biztonsági csoportok vagy a tűzfalak használatával történő szűrés blokkolhatja a hozzáférést. Bizonyosodjon meg róla, hogy az *Azure Storage* szolgáltatáscímkéje engedélyezve van a végpontrégiókban. A szabályzatok korlátozásaival kapcsolatos részletekért tekintse meg a [korlátozásokkal](#limitations) foglalkozó témakört.
  A rendszer például megtagadja a klasszikus tárfiókok általi hozzáférést, ha egy szabályzat érvényben van.
  - Ellenőrizze, hogy az Azure-szolgáltatás úgy van-e konfigurálva, hogy engedélyezze a virtuális hálózatokról való hozzáférést a végpontokon keresztül, illetve hogy az erőforrás alapértelmezett szabályzatában az *összes engedélyezésének* lehetősége van-e megadva.

## <a name="provisioning"></a>Kiépítés

A szolgáltatásvégpont-szabályzatokat az alhálózatokon egy olyan felhasználó konfigurálhatja, akinek írási hozzáférése van egy virtuális hálózathoz. További információkat is megtudhat az Azure [beépített szerepköreiről](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és az egyes engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről.

A virtuális hálózatok és az Azure-beli szolgáltatási erőforrások tartozhatnak ugyanahhoz az előfizetéshez vagy Azure Active Directory-bérlőkhöz, de különböző előfizetésekhez és bérlőkhöz is. 

## <a name="pricing-and-limits"></a>Díjszabás és korlátok

A szolgáltatásvégpont-szabályzatok használata nem jár további költségekkel. A szolgáltatásvégpontok esetében az Azure-szolgáltatások (például az Azure Storage) aktuális díjszabási modellje az irányadó.

A következő korlátozásokat érvényesíti a rendszer a szolgáltatásvégpont-szabályokon: 

 |Erőforrás | Alapértelmezett korlát |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>További lépések

- Tudnivalók [a virtuális hálózati szolgáltatásvégpont-szabályzatok konfigurálásáról](virtual-network-service-endpoint-policies-portal.md)
- További információk a [virtuális hálózati szolgáltatásvégpontokról](virtual-network-service-endpoints-overview.md)

