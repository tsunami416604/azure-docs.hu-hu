---
title: Bérlők közötti felügyeleti megoldások
description: Az Azure-beli delegált erőforrás-kezelés lehetővé teszi a több-bérlős felügyeleti élményt.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 10/18/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 8d7b1f24d5dcf3d66ffd04704c79a284c4810365
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598453"
---
# <a name="cross-tenant-management-experiences"></a>Bérlők közötti felügyeleti megoldások

Ez a cikk azokat a forgatókönyveket ismerteti, amelyeket Ön a szolgáltatóként használhat az [Azure-beli delegált erőforrás-kezeléssel](../concepts/azure-delegated-resource-management.md) , amellyel több ügyfél számára felügyelheti az Azure-erőforrásokat a [Azure Portal](https://portal.azure.com)saját bérlőn belülről.

> [!NOTE]
> Az Azure-beli delegált erőforrás-kezelés egy olyan vállalaton belül is felhasználható, amely a saját több Bérlővel rendelkezik a több-bérlős felügyelet egyszerűsítése érdekében.

## <a name="understanding-customer-tenants"></a>Ügyfelek bérlők ismertetése

Egy Azure Active Directory (Azure AD) bérlő egy szervezet képviselete. Ez az Azure AD dedikált példánya, amelyet a szervezet akkor kap, amikor kapcsolatot hoz létre a Microsofttal az Azure-ra, Microsoft 365ra vagy más szolgáltatásokra való feliratkozással. Minden Azure AD-bérlő különálló, és elkülönül a többi Azure AD-bérlőtől, és saját bérlői AZONOSÍTÓval (GUID) rendelkezik. További információ: [Mi az Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

Az ügyfelekhez tartozó Azure-erőforrások kezeléséhez általában a szolgáltatóknak az ügyfél bérlője által hozzárendelt fiókkal kell bejelentkezniük a Azure Portalba, és a felhasználói fiókok létrehozásához és kezeléséhez az ügyfél bérlője rendszergazdájának kell megkövetelni a felhasználókat. a szolgáltatónál.

Az Azure delegált erőforrás-kezeléssel a bevezetési folyamat meghatározza a szolgáltató bérlője azon felhasználóit, akik hozzá tudnak férni és kezelhetnek előfizetéseket, erőforráscsoportokat és erőforrásokat az ügyfél bérlője számára. Ezek a felhasználók ezután a saját hitelesítő adataikkal jelentkezhetnek be a Azure Portalba. A Azure Portalon belül az összes olyan ügyfél erőforrásait kezelhetik, amelyhez hozzáféréssel rendelkeznek. Ezt úgy teheti meg, hogy felkeresi a [saját ügyfelek](../how-to/view-manage-customers.md) lapját a Azure Portalon, vagy közvetlenül az ügyfél előfizetésének kontextusában, akár a Azure Portal, akár API-n keresztül.

Az Azure-beli delegált erőforrás-kezelés nagyobb rugalmasságot biztosít a több ügyfél erőforrásainak kezeléséhez anélkül, hogy a különböző bérlők különböző fiókjaiba kellene bejelentkezniük. Előfordulhat például, hogy a szolgáltató három ügyfelet tartalmaz, amelyek eltérő felelősséggel és hozzáférési szinttel rendelkeznek, ahogy az itt látható:

![Három ügyfél-bérlő, amely a szolgáltatói feladatokat mutatja](../media/azure-delegated-resource-management-customer-tenants.jpg)

Az Azure delegált erőforrás-kezelés használatával a jogosult felhasználók bejelentkezhetnek a szolgáltató bérlője számára, hogy hozzáférjenek ezekhez az erőforrásokhoz, ahogy az itt is látható:

![Egy szolgáltatói bérlőn keresztül kezelt ügyfelek erőforrásai](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="supported-services-and-scenarios"></a>Támogatott szolgáltatások és forgatókönyvek

A több-bérlős felügyeleti felület jelenleg a következő forgatókönyveket támogatja a delegált ügyfél-erőforrásokkal kapcsolatban:

[Azure Automation](https://docs.microsoft.com/azure/automation/):

- Az Automation-fiókok használata a delegált ügyfelek erőforrásainak eléréséhez és használatához

[Azure Backup](https://docs.microsoft.com/azure/backup/):

- Ügyféladatok biztonsági mentése és visszaállítása az ügyfelek bérlői számára

[Azure Kubernetes szolgáltatás (ak)](https://docs.microsoft.com//azure/aks/):

- Üzemeltetett Kubernetes-környezetek kezelése, tárolón belüli alkalmazások üzembe helyezése és kezelése az ügyfelek bérlői között

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/):

- Megtekintheti a Azure Portal delegált előfizetésekre vonatkozó riasztásokat, vagy programozott módon REST API-hívásokon keresztül, amelyekkel megtekintheti a riasztásokat az összes előfizetésben
- A delegált előfizetések tevékenységi naplójának részleteinek megtekintése
- Log Analytics: adatok lekérdezése távoli ügyfelek munkaterületeiről több bérlőn
- Hozzon létre riasztásokat az olyan ügyfelek bérlői számára, amelyek automatizálást indítanak, például Azure Automation runbookok vagy Azure Functionst a szolgáltató bérlője webhookok használatával

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/):

- A megfelelőségi Pillanatképek a delegált előfizetéseken belüli hozzárendelt szabályzatok részleteit mutatják
- Szabályzat-definíciók létrehozása és szerkesztése delegált előfizetésen belül
- Ügyfél által definiált szabályzat-definíciók kiosztása a delegált előfizetésen belül
- Az ügyfelek a szolgáltató által készített szabályzatokat a saját maguk által létrehozott szabályzatok mellett látják
- Javíthatja [a deployIfNotExists, vagy módosíthatja a hozzárendeléseket az ügyfél bérlőn belül](../how-to/deploy-policy-remediation.md)

[Azure-erőforrás gráf](https://docs.microsoft.com/azure/governance/resource-graph/):

- A most már tartalmazza a bérlő AZONOSÍTÓját a visszaadott lekérdezés eredményei között, így azonosíthatja, hogy az előfizetés az ügyfél bérlője vagy a szolgáltatói bérlőhöz tartozik-e

[Azure Security Center](https://docs.microsoft.com/azure/security-center/):

- Több-bérlős láthatóság
  - A biztonsági szabályzatoknak való megfelelőség monitorozása és a biztonsági lefedettség biztosítása az összes bérlő erőforrásai között
  - Folyamatos szabályozási megfelelőség monitorozása több ügyfél között egyetlen nézetben
  - Gyakorlati biztonsági javaslatok monitorozása, osztályozása és rangsorolása biztonságos pontszámok számításával
- Több-bérlős biztonsági testhelyzet kezelése
  - Biztonsági házirendek kezelése
  - Tegyen lépéseket olyan erőforrásokra, amelyek nem felelnek meg a végrehajtható biztonsági javaslatoknak
  - Biztonsággal kapcsolatos adatok gyűjtése és tárolása
- Több-bérlős fenyegetések észlelése és védelme
  - Fenyegetések észlelése a bérlők erőforrásai között
  - Komplex veszélyforrások elleni védelem szabályozása, például igény szerinti (JIT) virtuálisgép-hozzáférés alkalmazása
  - Hálózati biztonsági csoport konfigurációjának megerősítése adaptív hálózati megerősítéssel
  - Győződjön meg arról, hogy a kiszolgálók csak azokat az alkalmazásokat és folyamatokat futtatják, amelyeknek adaptív alkalmazás-vezérlőkkel kell rendelkezniük
  - Fontos fájlok és beállításjegyzék-bejegyzések változásainak figyelése a fájlok integritásának figyelésével (FIM)

[Azure Sentinel](https://docs.microsoft.com/azure/sentinel/multiple-tenants-service-providers):

- Azure Sentinel-erőforrások kezelése az ügyfelek bérlői számára

[Azure Service Health](https://docs.microsoft.com/azure/service-health/):

- Az ügyfelek erőforrásainak állapotának figyelése Azure Resource Health
- Az ügyfelek által használt Azure-szolgáltatások állapotának nyomon követése

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Az Azure-beli virtuális gépek vész-helyreállítási lehetőségeinek kezelése az ügyfelek bérlői számára (ne feledje, hogy a futtató fiókokat nem lehet a virtuálisgép-bővítmények másolására használni)

[Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/):

- Virtuálisgép-bővítmények használata az Azure-beli virtuális gépeken üzembe helyezés utáni konfigurációs és automatizálási feladatok biztosításához az ügyfelek bérlői számára
- Rendszerindítási diagnosztika használata az Azure-beli virtuális gépek az ügyfelek bérlői általi megoldásához
- Virtuális gépek elérése a soros konzollal az ügyfelek bérlői számára
- Vegye figyelembe, hogy nem használhatja a Azure Active Directory távoli bejelentkezéshez a virtuális géphez, és nem integrálhat egy virtuális Key Vault gépet a jelszavakhoz, titkokhoz vagy titkosítási kulcsokhoz a lemezes titkosításhoz.

[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/):

- Virtuális hálózatok és virtuális hálózati adapterek (Vnic-EK) üzembe helyezése és kezelése az ügyfelek bérlői között

Támogatási kérelmek:

- Nyissa meg a támogatási kérelmeket a delegált erőforrásokhoz a Azure Portal **Súgó + támogatás** paneljén (a delegált hatókörhöz elérhető támogatási csomag kiválasztásával)

## <a name="current-limitations"></a>Aktuális korlátozások
Az összes forgatókönyv esetén vegye figyelembe a következő korlátozásokat:

- Az Azure Resource Manager által kezelt kérelmeket az Azure-beli delegált erőforrás-kezelés használatával lehet elvégezni. A kérések műveleti URI-je `https://management.azure.com` értékkel kezdődik. Az Azure-beli delegált erőforrás-kezelés nem támogatja azonban az erőforrástípus egy példánya által kezelt kérelmeket (például a kulcstartó-titkok elérését vagy a tárolási adatok elérését). Ezeknek a kérelmeknek a műveleti URI-k jellemzően a példány egyedi címeivel kezdődnek, például `https://myaccount.blob.core.windows.net` vagy `https://mykeyvault.vault.azure.net/`. Az utóbbi általában az adatműveletek, nem pedig a felügyeleti műveletek. 
- A szerepkör-hozzárendeléseknek a szerepköralapú hozzáférés-vezérlés (RBAC) [beépített szerepköreit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)kell használniuk. Az Azure-beli delegált erőforrás-kezelés jelenleg az összes beépített szerepkört támogatja, kivéve a tulajdonosi, a felhasználói hozzáférési rendszergazdai vagy a [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) engedéllyel rendelkező beépített szerepköröket. Az egyéni szerepkörök és a [klasszikus előfizetés-rendszergazdai szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) szintén nem támogatottak.
- Az Azure-beli delegált erőforrás-kezeléshez jelenleg nem lehet előfizetést (vagy erőforráscsoportot) előkészíteni, ha az előfizetés Azure Databricks használ. Hasonlóképpen, ha regisztrálva van egy előfizetés a **Microsoft. ManagedServices** erőforrás-szolgáltatóval való bevezetéshez, jelenleg nem fog tudni Databricks-munkaterületet létrehozni az adott előfizetéshez.
- Míg az Azure-beli delegált erőforrás-kezeléshez az erőforrás-zárolással rendelkező előfizetések és erőforráscsoportok is bejelentkezhetnek, ezek a zárolások nem akadályozzák meg a felhasználók által végzett műveleteket a bérlők felügyeletében. A rendszer által felügyelt erőforrások, például az Azure által felügyelt alkalmazások vagy az Azure-tervrajzok (rendszer által hozzárendelt megtagadási hozzárendelések) által létrehozott [hozzárendelések megtagadása](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) , hogy a bérlők ne tudják eljárni az adott erőforráson. Ugyanakkor az ügyfél bérlője jelenleg nem hozhat létre saját megtagadási hozzárendeléseket (felhasználó által hozzárendelt megtagadási hozzárendeléseket).

## <a name="using-apis-and-management-tools-with-cross-tenant-management"></a>API-k és felügyeleti eszközök használata a több-bérlős felügyelettel

A fent felsorolt támogatott szolgáltatások és forgatókönyvek esetében közvetlenül a portálon, vagy API-k és felügyeleti eszközök (például az Azure CLI és a Azure PowerShell) használatával is elvégezheti a felügyeleti feladatokat. A rendszer minden meglévő API-t használhat a delegált erőforrások (támogatott szolgáltatások) használata esetén.

Vannak olyan API-k is, amelyek az Azure-beli delegált erőforrás-kezelési feladatok végrehajtásához szükségesek. További információért lásd a **hivatkozási** szakaszt.


## <a name="next-steps"></a>Következő lépések

- Az ügyfeleket az Azure-beli delegált erőforrás-kezeléshez [Azure Resource Manager sablonok használatával](../how-to/onboard-customer.md) vagy [egy magán-vagy nyilvános felügyelt szolgáltatás Azure Marketplace-re való közzétételével](../how-to/publish-managed-services-offers.md)teheti közzé.
- [Megtekintheti és kezelheti az ügyfeleket](../how-to/view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
