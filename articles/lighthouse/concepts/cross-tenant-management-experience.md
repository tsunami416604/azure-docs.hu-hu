---
title: Több-bérlős kezelési élményt biztosítsunk Azure világítótoronyig
description: Delegált Azure erőforrás-kezelés lehetővé teszi, hogy egy több-bérlős megoldást.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 15454d4b3f0abad6166c4b163df6c8652669d649
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809903"
---
# <a name="cross-tenant-management-experiences"></a>Több-bérlős felhasználói élmény

Ez a cikk ismerteti, hogy, egy szolgáltató használható [Azure erőforrás-kezelés delegált](../concepts/azure-delegated-resource-management.md) Azure-erőforrások kezelése a saját bérlőn belül több ügyfeleink számára a [Azure Portalon ](https://portal.azure.com).

> [!NOTE]
> Delegált Azure resource Managerrel egy több-bérlős adminisztrációjának egyszerűsítésére saját több bérlővel rendelkező vállalaton belül is használható.

## <a name="understanding-customer-tenants"></a>Ügyfél bérlők ismertetése

Az Azure Active Directory (Azure AD) bérlő egy szervezet reprezentációját. Fontos, hogy egy szervezet megkap és a egy kapcsolatot a Microsoft Azure, Microsoft 365 vagy más szolgáltatások regisztrál létrehozásához az Azure AD olyan dedikált példányának. Minden egyes Azure AD-bérlő, és külön, a más Azure AD-bérlőt, és a saját bérlő azonosítója (GUID). További információ: [Mi az Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

Általában annak érdekében, hogy az ügyfél Azure-erőforrások kezeléséhez, a szolgáltatók lenne, jelentkezzen be az Azure portal használatával egy adott ügyfél-bérlőjéhez, hozhat létre és kezelhet felhasználói fiókokat az ügyfél Bérlői rendszergazda igénylő társított fiók a szolgáltató.

Az Azure delegált resource Managerrel a bevezetési folyamat meghatározza a felhasználók, akik hozzá és felügyelhesse az előfizetések, erőforráscsoportok és az ügyfél bérlői erőforrások képesek lesznek a szolgáltató bérlőn belül. Ezek a felhasználók majd bejelentkezhet az Azure Portalra, a saját hitelesítő adataival. Az Azure Portalon felügyelheti az erőforrásokhoz, amelyekhez hozzáféréssel rendelkeznek minden ügyfél számára. Ezt megteheti meglátogatják a [ügyfeleim](../how-to/view-manage-customers.md) oldal az Azure Portalon, vagy közvetlenül az adott ügyfél által előfizetett, az Azure Portalon vagy API-kon keresztül keretén belül működő.

Delegált Azure erőforrás-kezelés lehetővé teszi, hogy nagyobb rugalmasságot biztosít a több ügyfél-erőforrások kezeléséhez jelentkezzen be a különböző bérlők különböző fiókok nélkül. A szolgáltató lehet például három ügyfelek különböző feladatokat, és a hozzáférési szintekkel, itt látható módon:

![Három vevő bérlők megjelenítése a service provider feladatkörei](../media/azure-delegated-resource-management-customer-tenants.jpg)

Delegált Azure erőforrás-kezelést használ, jogosult felhasználók bejelentkezhet a szolgáltató bérlőhöz hozzáférni ezekhez az erőforrásokhoz, itt látható módon:

![Egy szolgáltatás szolgáltató bérlői szolgáltatáson keresztül felügyelt ügyfél erőforrások](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="supported-services-and-scenarios"></a>Támogatott szolgáltatások és alkalmazási helyzetek

A több-bérlős környezetet jelenleg támogatja a delegált ügyfél erőforrásaival az alábbi esetekben:

[Az Azure Automation](https://docs.microsoft.com/azure/automation/):

- Az automation-fiókok használatával és a delegált ügyfél erőforrásaival

[Az Azure Backup](https://docs.microsoft.com/azure/backup/):

- Biztonsági mentése és visszaállítása a vásárlói adatokat az ügyfél-bérlők

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com//azure/aks/):

- Üzemeltetett Kubernetes-környezetek felügyeletét és üzembe helyezése és kezelhet tárolóalapú alkalmazásokat belül vevő bérlők számára

[Az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/):

- Delegált előfizetések az Azure Portalon vagy programozott módon REST API-hívás, és megtekintheti a riasztásokat az összes előfizetés lehetővé teszi a riasztások megtekintése
- Delegált előfizetések tevékenység napló részleteinek megtekintése
- A log analytics: A távoli ügyfél-munkaterületekről több bérlő számára az adatok lekérdezése

[Az Azure Policy](https://docs.microsoft.com/azure/governance/policy/):

- Megfelelőségi pillanatképek delegált előfizetések belül hozzárendelt szabályzata részletek megjelenítése
- Hozhat létre és módosíthat egy meghatalmazott előfizetésen belüli szabályzatdefiníciók
- Rendelje hozzá a felhasználó által meghatározott szabályzatdefiníciók delegált az előfizetésen belül
- Ügyfelek, tekintse meg magukat, korábban létrehozott bármely szabályzat mellett a szolgáltató által létrehozott házirendek
- DeployIfNotExists hozzárendelések belül az ügyfél bérlők javítani tudja, ha az ügyfél konfigurálva van a felügyelt identitás és *roleDefinitionIds* a szabályzat-hozzárendelés

[Azure-erőforrás Graph](https://docs.microsoft.com/azure/governance/resource-graph/):

- Mostantól tartalmazza a bérlő Azonosítóját visszaadott lekérdezési eredmények között, így annak megállapítására, hogy egy előfizetés tartozik, az ügyfél vagy szolgáltató bérlőhöz szolgáltatás

[Azure Security Center](https://docs.microsoft.com/azure/security-center/):

- Több-bérlős láthatósága
  - A biztonsági házirendek megfelelőség figyelése és a biztonsági lefedettség érdekében az összes bérlőre erőforrások között
  - Folyamatos előírásoknak való megfelelés figyelése egyetlen nézetben több ügyfél között
  - Figyelheti, osztályozhatja és rangsorolhatja a gyakorlatban hasznosítható biztonsági javaslatok az biztonságos pontszámának fejlettebb kiszámítása
- Több-bérlős biztonsági rendszer kialakításához kezelése
  - Biztonsági házirendek kezelése
  - Művelet végrehajtása a végrehajtható biztonsági ajánlásokat nem megfelelő erőforrások
  - Gyűjti össze és tárolja a biztonsági adatok
- Több-bérlős fenyegetésészlelés és -védelem
  - Bérlők erőforrásoknak fenyegetések észlelése
  - A alkalmazni a komplex veszélyforrások elleni védelmi vezérlők, például – igény (szerinti JIT) Virtuálisgép-hozzáférés
  - Az adaptív hálózati korlátozások a hálózati biztonsági csoport konfigurálásának megerősítése
  - Győződjön meg arról, csak az alkalmazások és a folyamatok lehetnek, az adaptív alkalmazásvezérlők kiszolgálón fut
  - Fontos fájlok és beállításjegyzék-bejegyzések a fájl fájlintegritási Monitorozás (FIM) fájlmódosulások figyelése

[Az Azure Service Health](https://docs.microsoft.com/azure/service-health/):

- Az Azure Resource Health ügyfél-erőforrások állapotának figyelése
- Az ügyfelek által használt Azure-szolgáltatások állapotának nyomon követése

[Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Vész-helyreállítási lehetőségeket vevő bérlők számára (vegye figyelembe, hogy nem használhat futtató fiókok másolása a Virtuálisgép-bővítmények) Azure-beli virtuális gépek kezelése

[Az Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/):

- Üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosít az Azure virtuális gépeken vevő bérlők virtuálisgép-bővítmények használata
- A rendszerindítási diagnosztika használata Azure virtuális gépek a vevő bérlők
- Hozzáférés virtuális gépek, a vevő bérlők számára a soros konzol
- Vegye figyelembe, hogy a távoli bejelentkezéshez egy virtuális géphez nem használható az Azure Active Directory, és a egy virtuális gép nem integrálható a jelszavakat, titkos kódok és lemeztitkosításhoz kriptográfiai kulcsok a Key vaulttal

[Az Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/):

- Üzembe helyezése és kezelése a virtuális hálózatok és virtuális hálózati adapterrel (Vnic) belül vevő bérlők számára

Támogatási kérések:

- Nyissa meg a támogatási kéréseket a delegált erőforrásait a **súgó + támogatás** panelen (a támogatási csomag elérhető a delegált hatókör kiválasztása) az Azure Portalon

Az összes forgatókönyv vegye figyelembe, a következő aktuális korlátozások:

- Azure Resource Manager által kezelt kérések delegált Azure erőforrás-kezelés használatával hajtható végre. Kezdje a műveletet URI-k ezeket a kérelmeket `https://management.azure.com`. Az erőforrástípus (például a KeyVault titkos hozzáférési vagy a storage-adatelérés) példány által kezelt kérések azonban az Azure delegált felhőerőforrás-kezelés nem támogatottak. Általában megkezdi a művelet URI-k ezeket a kérelmeket, mint például a példányhoz egyedi címmel `https://myaccount.blob.core.windows.net` vagy `https://mykeyvault.vault.azure.net/`. Az utóbbi is jellemzően felügyeleti műveletek helyett az üzemeltetés. 
- Szerepkör-hozzárendelések kell használnia a szerepköralapú hozzáférés-vezérlés (RBAC) [beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Az Azure delegált resource Managerrel, kivéve a tulajdonos, a felhasználói hozzáférés rendszergazdája vagy a beépített szerepköröket a jelenleg támogatott összes beépített szerepkört [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) engedéllyel. Egyéni szerepkörök és [hagyományos előfizetés-Rendszergazda szerepkörhöz](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) még nem támogatott.
- Jelenleg, akkor nem lehet előkészíteni egy előfizetést (vagy erőforráscsoportot egy előfizetésen belül) az Azure delegált erőforrás-kezelés, ha az előfizetés az Azure Databricks használ. Hasonlóképpen ha az előfizetés regisztrálva van a bevezetése az **Microsoft.ManagedServices** erőforrás-szolgáltató, akkor létre tudja hozni egy Databricks-munkaterület előfizetés esetében jelenleg.

## <a name="using-apis-and-management-tools-with-cross-tenant-management"></a>A több-bérlős felügyeleti API-k és a felügyeleti eszközök használatával

A támogatott szolgáltatások és a fent felsorolt forgatókönyvek vagy közvetlenül a portálon, vagy API-k és a felügyeleti eszközök (például az Azure CLI-vel és az Azure PowerShell) segítségével végezheti felügyeleti feladatokat. Minden meglévő API-k is használható, ha delegált erőforrások (a támogatott szolgáltatások) használata.

Is találhatók API-k Azure delegált erőforráshoz kezelési feladatainak végrehajtása jellemző. További információ: a **referencia** szakaszban.


## <a name="next-steps"></a>További lépések

- Készítse elő a ügyfelei számára, hogy az Azure erőforrás-kezelést, vagy a meghatalmazott [Azure Resource Manager-sablonok használatával](../how-to/onboard-customer.md) vagy [egy nyilvános vagy privát felügyelt szolgáltatások ajánlat Azure Marketplace-en való közzétételének](../how-to/publish-managed-services-offers.md).
- [Megtekintheti és kezelheti az ügyfelek](../how-to/view-manage-customers.md) a **ügyfeleim** az Azure Portalon.