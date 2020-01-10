---
title: Bérlők közötti felügyeleti megoldások
description: Az Azure-beli delegált erőforrás-kezelés lehetővé teszi a több-bérlős felügyeleti élményt.
ms.date: 01/06/2020
ms.topic: conceptual
ms.openlocfilehash: 661ada4e5d620d4977290986f5649b91363202fe
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75706988"
---
# <a name="cross-tenant-management-experiences"></a>Bérlők közötti felügyeleti megoldások

Szolgáltatóként az Azure-beli [delegált erőforrás-kezelés](../concepts/azure-delegated-resource-management.md) használatával több ügyfél számára is kezelheti az Azure-erőforrásokat a [Azure Portal](https://portal.azure.com)a saját bérlőn belül. A legtöbb feladat és szolgáltatás elvégezhető delegált Azure-erőforrásokon a felügyelt bérlők között. Ez a cikk néhány olyan továbbfejlesztett forgatókönyvet ismertet, amelyekben az Azure-beli delegált erőforrás-kezelés hatékony lehet.

> [!NOTE]
> Az Azure-beli delegált erőforrás-kezelés [olyan vállalaton belül is felhasználható, amely több Azure ad-Bérlővel rendelkezik a több](enterprise.md) -bérlős felügyelet egyszerűsítése érdekében.

## <a name="understanding-customer-tenants"></a>Ügyfelek bérlők ismertetése

Egy Azure Active Directory (Azure AD) bérlő egy szervezet képviselete. Ez az Azure AD dedikált példánya, amelyet a szervezet akkor kap, amikor kapcsolatot hoz létre a Microsofttal az Azure-ra, Microsoft 365ra vagy más szolgáltatásokra való feliratkozással. Minden Azure AD-bérlő különálló, és elkülönül a többi Azure AD-bérlőtől, és saját bérlői AZONOSÍTÓval (GUID) rendelkezik. További információ: [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Az ügyfelekhez tartozó Azure-erőforrások kezeléséhez általában a szolgáltatóknak az ügyfél bérlője által hozzárendelt fiókkal kell bejelentkezniük a Azure Portalba, és a felhasználói fiókok létrehozásához és kezeléséhez az ügyfél bérlője rendszergazdájának kell megkövetelni a felhasználókat. a szolgáltatónál.

Az Azure delegált erőforrás-kezeléssel a bevezetési folyamat meghatározza a szolgáltató bérlője azon felhasználóit, akik hozzá tudnak férni és kezelhetnek előfizetéseket, erőforráscsoportokat és erőforrásokat az ügyfél bérlője számára. Ezek a felhasználók ezután a saját hitelesítő adataikkal jelentkezhetnek be a Azure Portalba. A Azure Portalon belül az összes olyan ügyfél erőforrásait kezelhetik, amelyhez hozzáféréssel rendelkeznek. Ezt úgy teheti meg, hogy felkeresi a [saját ügyfelek](../how-to/view-manage-customers.md) lapját a Azure Portalon, vagy közvetlenül az ügyfél előfizetésének kontextusában, akár a Azure Portal, akár API-n keresztül.

Az Azure-beli delegált erőforrás-kezelés nagyobb rugalmasságot biztosít a több ügyfél erőforrásainak kezeléséhez anélkül, hogy a különböző bérlők különböző fiókjaiba kellene bejelentkezniük. Előfordulhat például, hogy a szolgáltató három ügyfelet tartalmaz, amelyek eltérő felelősséggel és hozzáférési szinttel rendelkeznek, ahogy az itt látható:

![Három ügyfél-bérlő, amely a szolgáltatói feladatokat mutatja](../media/azure-delegated-resource-management-customer-tenants.jpg)

Az Azure delegált erőforrás-kezelés használatával a jogosult felhasználók bejelentkezhetnek a szolgáltató bérlője számára, hogy hozzáférjenek ezekhez az erőforrásokhoz, ahogy az itt is látható:

![Egy szolgáltatói bérlőn keresztül kezelt ügyfelek erőforrásai](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API-k és felügyeleti eszközök támogatása

A delegált erőforrásokon közvetlenül a portálon, vagy API-k és felügyeleti eszközök (például az Azure CLI és a Azure PowerShell) használatával is elvégezheti a felügyeleti feladatokat. A rendszer minden meglévő API-t felhasználhat a delegált erőforrások használata esetén, ha a funkció támogatott a több-bérlős felügyelet esetében, és a felhasználó rendelkezik a megfelelő engedélyekkel.

Az Azure-beli delegált erőforrás-kezelési feladatok végrehajtásához API-kat is biztosítunk. További információért lásd a **hivatkozási** szakaszt.

## <a name="enhanced-services-and-scenarios"></a>Továbbfejlesztett szolgáltatások és forgatókönyvek

A legtöbb feladat és szolgáltatás a felügyelt bérlők delegált erőforrásain végezhető el. Az alábbiakban néhány olyan főbb forgatókönyvet talál, ahol a több-bérlős felügyelet is hatásos lehet.

[Azure arc for Servers (előzetes verzió)](../../azure-arc/servers/overview.md):

- A [Windows Server vagy Linux rendszerű gépek összekötése az Azure](../../azure-arc/servers/quickstart-onboard-portal.md) -on kívül az Azure-ban delegált előfizetésekkel és/vagy erőforráscsoportok
- Csatlakoztatott gépek kezelése Azure-konstrukciók használatával, például Azure Policy és címkézés

[Azure Automation](../../automation/index.yml):

- Az Automation-fiókok használata a delegált ügyfelek erőforrásainak eléréséhez és használatához

[Azure Backup](../../backup/index.yml):

- Ügyféladatok biztonsági mentése és visszaállítása az ügyfelek bérlői számára

[Azure Kubernetes Service (AKS)](../../aks/index.yml):

- Üzemeltetett Kubernetes-környezetek kezelése, tárolón belüli alkalmazások üzembe helyezése és kezelése az ügyfelek bérlői között

[Azure monitor](../../azure-monitor/index.yml):

- Megtekintheti a delegált előfizetések riasztásait, és megtekintheti a riasztásokat az összes előfizetés között
- A delegált előfizetések tevékenységi naplójának részleteinek megtekintése
- Log Analytics: adatok lekérdezése távoli ügyfelek munkaterületeiről több bérlőn
- Hozzon létre riasztásokat az olyan ügyfelek bérlői számára, amelyek automatizálást indítanak, például Azure Automation runbookok vagy Azure Functionst a szolgáltató bérlője webhookok használatával

[Azure Policy](../../governance/policy/index.yml):

- A megfelelőségi Pillanatképek a delegált előfizetéseken belüli hozzárendelt szabályzatok részleteit mutatják
- Szabályzat-definíciók létrehozása és szerkesztése delegált előfizetésen belül
- Ügyfél által definiált szabályzat-definíciók kiosztása a delegált előfizetésen belül
- Az ügyfelek a szolgáltató által készített szabályzatokat a saját maguk által létrehozott szabályzatok mellett látják
- Javíthatja [a deployIfNotExists, vagy módosíthatja a hozzárendeléseket az ügyfél bérlőn belül](../how-to/deploy-policy-remediation.md)

[Azure-erőforrás gráf](../../governance/resource-graph/index.yml):

- A most már tartalmazza a bérlő AZONOSÍTÓját a visszaadott lekérdezés eredményei között, így azonosíthatja, hogy az előfizetés az ügyfél bérlője vagy a szolgáltatói bérlőhöz tartozik-e

[Azure Security Center](../../security-center/index.yml):

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

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Azure Sentinel-erőforrások kezelése [az ügyfelek bérlői számára](../../sentinel/multiple-tenants-service-providers.md)
- [Támadások követése és a biztonsági riasztások megtekintése több ügyfél bérlője között](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure Service Health](../../service-health/index.yml):

- Az ügyfelek erőforrásainak állapotának figyelése Azure Resource Health
- Az ügyfelek által használt Azure-szolgáltatások állapotának nyomon követése

[Azure Site Recovery](../../site-recovery/index.yml):

- Az Azure-beli virtuális gépek vész-helyreállítási lehetőségeinek kezelése az ügyfelek bérlői számára (ne feledje, hogy a futtató fiókokat nem lehet a virtuálisgép-bővítmények másolására használni)

[Azure Virtual Machines](../../virtual-machines/index.yml):

- Virtuálisgép-bővítmények használata az Azure-beli virtuális gépeken üzembe helyezés utáni konfigurációs és automatizálási feladatok biztosításához az ügyfelek bérlői számára
- Rendszerindítási diagnosztika használata az Azure-beli virtuális gépek az ügyfelek bérlői általi megoldásához
- Virtuális gépek elérése a soros konzollal az ügyfelek bérlői számára
- Vegye figyelembe, hogy nem használhatja a Azure Active Directory távoli bejelentkezéshez a virtuális géphez, és nem integrálhat egy virtuális Key Vault gépet a jelszavakhoz, titkokhoz vagy titkosítási kulcsokhoz a lemezes titkosításhoz.

[Azure Virtual Network](../../virtual-network/index.yml):

- Virtuális hálózatok és virtuális hálózati adapterek (Vnic-EK) üzembe helyezése és kezelése az ügyfelek bérlői között

Támogatási kérelmek:

- Nyissa meg a támogatási kérelmeket a delegált erőforrásokhoz a Azure Portal **Súgó + támogatás** paneljén (a delegált hatókörhöz elérhető támogatási csomag kiválasztásával)

## <a name="current-limitations"></a>Aktuális korlátozások
Az összes forgatókönyv esetén vegye figyelembe a következő korlátozásokat:

- Az Azure Resource Manager által kezelt kérelmeket az Azure-beli delegált erőforrás-kezelés használatával lehet elvégezni. A kérelmekhez tartozó műveleti URI-k a következővel kezdődnek: `https://management.azure.com`. Az Azure-beli delegált erőforrás-kezelés nem támogatja azonban az erőforrástípus egy példánya által kezelt kérelmeket (például a kulcstartó-titkok elérését vagy a tárolási adatok elérését). Ezeknek a kérelmeknek a műveleti URI-k jellemzően a példány egyedi címeivel kezdődnek, például `https://myaccount.blob.core.windows.net` vagy `https://mykeyvault.vault.azure.net/`. Az utóbbi általában az adatműveletek, nem pedig a felügyeleti műveletek. 
- A szerepkör-hozzárendeléseknek a szerepköralapú hozzáférés-vezérlés (RBAC) [beépített szerepköreit](../../role-based-access-control/built-in-roles.md)kell használniuk. Az Azure-beli delegált erőforrás-kezelés jelenleg minden beépített szerepkört támogat, kivéve a tulajdonost vagy a [DataActions](../../role-based-access-control/role-definitions.md#dataactions) engedéllyel rendelkező beépített szerepköröket. A felhasználói hozzáférés rendszergazdai szerepköre csak korlátozott használat esetén támogatott a [szerepkörök hozzárendeléséhez a felügyelt identitásokhoz](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Az egyéni szerepkörök és a [klasszikus előfizetés-rendszergazdai szerepkörök](../../role-based-access-control/classic-administrators.md) nem támogatottak.
- Az Azure-beli delegált erőforrás-kezeléshez jelenleg nem lehet előfizetést (vagy erőforráscsoportot) előkészíteni, ha az előfizetés Azure Databricks használ. Hasonlóképpen, ha regisztrálva van egy előfizetés a **Microsoft. ManagedServices** erőforrás-szolgáltatóval való bevezetéshez, jelenleg nem fog tudni Databricks-munkaterületet létrehozni az adott előfizetéshez.
- Míg az Azure-beli delegált erőforrás-kezeléshez az erőforrás-zárolással rendelkező előfizetések és erőforráscsoportok is bejelentkezhetnek, ezek a zárolások nem akadályozzák meg a felhasználók által végzett műveleteket a bérlők felügyeletében. A rendszer által felügyelt erőforrások, például az Azure által felügyelt alkalmazások vagy az Azure-tervrajzok (rendszer által hozzárendelt megtagadási hozzárendelések) által létrehozott [hozzárendelések megtagadása](../../role-based-access-control/deny-assignments.md) , hogy a bérlők ne tudják eljárni az adott erőforráson. Ugyanakkor az ügyfél bérlője jelenleg nem hozhat létre saját megtagadási hozzárendeléseket (felhasználó által hozzárendelt megtagadási hozzárendeléseket).

## <a name="next-steps"></a>Következő lépések

- Az ügyfeleket az Azure-beli delegált erőforrás-kezeléshez [Azure Resource Manager sablonok használatával](../how-to/onboard-customer.md) vagy [egy magán-vagy nyilvános felügyelt szolgáltatás Azure Marketplace-re való közzétételével](../how-to/publish-managed-services-offers.md)teheti közzé.
- [Megtekintheti és kezelheti az ügyfeleket](../how-to/view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
