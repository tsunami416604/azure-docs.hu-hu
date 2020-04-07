---
title: Bérlők közötti felügyeleti megoldások
description: Az Azure delegált erőforrás-kezelés lehetővé teszi a bérlők közötti felügyeleti élményt.
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0ac5d62fbf6b6ee418cd4b2f2b00dfc12e05f809
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754130"
---
# <a name="cross-tenant-management-experiences"></a>Bérlők közötti felügyeleti megoldások

Szolgáltatóként az [Azure delegált erőforrás-kezelésével](../concepts/azure-delegated-resource-management.md) kezelheti az Azure-erőforrásokat több ügyfél számára a saját bérlőjéből az [Azure Portalon.](https://portal.azure.com) A legtöbb feladat és szolgáltatás elvégezhető delegált Azure-erőforrások on felügyelt bérlők között. Ez a cikk néhány továbbfejlesztett forgatókönyvet ismertet, ahol az Azure delegált erőforrás-kezelése hatékony lehet.

> [!NOTE]
> Az Azure delegált erőforrás-kezelés is használható [egy olyan vállalaton belül, amely több Azure AD-bérlők saját,](enterprise.md) hogy egyszerűsítse a több bérlőközötti felügyelet.

## <a name="understanding-customer-tenants"></a>Az ügyfelek bérlőinek ismertetése

Az Azure Active Directory (Azure AD) bérlő egy szervezet reprezentációja. Az Azure AD egy dedikált példánya, amelyet egy szervezet akkor kap, amikor kapcsolatot hoz létre a Microsofttal az Azure, a Microsoft 365 vagy más szolgáltatásokra való feliratkozás során. Minden Egyes Azure AD-bérlő elkülönül, és elkülönül a többi Azure AD-bérlőktől, és saját bérlőazonosítóval (GUID) rendelkezik. További információ: [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Általában annak érdekében, hogy az Azure-erőforrások kezelése egy ügyfél számára, a szolgáltatók kellene bejelentkezni az Azure Portalon egy fiókkal társított, hogy az ügyfél bérlője, amely megköveteli, hogy az ügyfél bérlője rendszergazda hozzon létre és kezelje a szolgáltató felhasználói fiókok.

Az Azure delegált erőforrás-kezelése, a bevezetési folyamat meghatározza a felhasználók a szolgáltató bérlője, akik képesek lesznek elérni és kezelni előfizetések, erőforráscsoportok és erőforrások az ügyfél bérlője. Ezek a felhasználók ezután jelentkezzen be az Azure Portalsaját hitelesítő adataikkal. Az Azure Portalon belül kezelhetik az összes olyan ügyfélhez tartozó erőforrásokat, amelyekhez hozzáféréssel rendelkeznek. Ez az Azure Portalon található [Ügyfélei](../how-to/view-manage-customers.md) lapon, vagy közvetlenül az adott ügyfél előfizetésének környezetében, akár az Azure Portalon, akár API-kon keresztül végezhető el.

Az Azure delegált erőforrás-kezelése nagyobb rugalmasságot tesz lehetővé több ügyfél erőforrásainak kezeléséhez anélkül, hogy különböző bérlők különböző fiókjaiba kellene bejelentkeznie. Egy szolgáltatónak például három ügyfele lehet, különböző felelősségi körökkel és hozzáférési szintekkel, ahogy az itt látható:

![Három ügyfélbérlő, amelyek szolgáltatói felelősségeket mutatnak](../media/azure-delegated-resource-management-customer-tenants.jpg)

Az Azure delegált erőforrás-kezelése használatával a jogosult felhasználók bejelentkezhetnek a szolgáltató bérlőjébe, hogy hozzáférjenek ezekhez az erőforrásokhoz, ahogy az itt látható:

![Egy szolgáltató bérlőn keresztül kezelt ügyfélforrások](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API-k és felügyeleti eszközök támogatása

Felügyeleti feladatokat a delegált erőforrásokon közvetlenül a portálon, vagy API-k és felügyeleti eszközök (például az Azure CLI és az Azure PowerShell) használatával. Az összes meglévő API-k használhatók delegált erőforrásokkal végzett munka során, feltéve, hogy a funkció támogatja a bérlők közötti felügyelet, és a felhasználó rendelkezik a megfelelő engedélyekkel.

Az Azure PowerShell [Get-AzSubscription parancsmag](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) az egyes előfizetések **bérlői azonosítóját** jeleníti meg, így azonosíthatja, hogy egy visszaadott előfizetés a szolgáltató bérlőjéhez vagy egy felügyelt ügyfél-bérlőhöz tartozik-e.

Hasonlóképpen az Azure CLI-parancsok, például [az az-fiók lista](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) a **homeTenantId** és **managedByTenants attribútumok** megjelenítése.

> [!TIP]
> Ha az Azure CLI használatakor nem látja ezeket az `az account clear` értékeket, próbálja meg törölni a gyorsítótárat a futással. `az login --identity`

Olyan API-kat is biztosítunk, amelyek kifejezetten az Azure delegált erőforrás-kezelési feladatainak elvégzésére vonatkoznak. További információt a Referencia szakaszban **talál.**

## <a name="enhanced-services-and-scenarios"></a>Továbbfejlesztett szolgáltatások és forgatókönyvek

A legtöbb feladat és szolgáltatás elvégezhető a felügyelt bérlők delegált erőforrásain. Az alábbiakban néhány kulcsfontosságú forgatókönyvek, ahol a bérlők közötti felügyelet hatékony lehet.

[Azure Arc kiszolgálókhoz (előzetes verzió):](../../azure-arc/servers/overview.md)

- [Az Azure-on kívüli Windows Server- vagy Linux-gépek csatlakoztatása](../../azure-arc/servers/quickstart-onboard-portal.md) delegált előfizetésekhez és/vagy erőforráscsoportokhoz az Azure-ban
- Csatlakoztatott gépek kezelése Azure-konstrukciók, például az Azure-szabályzat és a címkézés használatával

[Azure Automatizálás:](../../automation/index.yml)

- Automatizálási fiókok használata delegált ügyfél-erőforrások eléréséhez és használatához

[Azure biztonsági mentés:](../../backup/index.yml)

- Ügyféladatok biztonsági és visszaállítása az ügyfélbérlőkben
- A [Biztonsági másolat intézővel](../../backup/monitor-azure-backup-with-backup-explorer.md) megtekintheti a biztonsági mentési elemek (beleértve a biztonsági mentéshez még nem konfigurált Azure-erőforrásokat) működési információit, valamint figyelheti a delegált előfizetések adatait (feladatok at és riasztásokat). A Biztonsági másolat kezelője jelenleg csak az Azure virtuális gép adataihoz érhető el.
- A delegált előfizetések [biztonsági másolatkészítési jelentésekkel](../../backup/configure-reports.md) nyomon követheti a korábbi trendeket, elemezheti a biztonsági mentési tárhely felhasználását, valamint naplózhatja a biztonsági mentéseket és a visszaállításokat.

[Azure Kubernetes szolgáltatás (AKS):](../../aks/index.yml)

- Üzemeltetett Kubernetes-környezetek kezelése, valamint tárolóba helyezett alkalmazások telepítése és kezelése az ügyfél-bérlőkön belül

[Azure-figyelő:](../../azure-monitor/index.yml)

- Delegált előfizetések riasztásainak megtekintése az összes előfizetésben lévő riasztások megtekintésével
- Delegált előfizetések tevékenységnapló-részleteinek megtekintése
- Naplóelemzés: Adatok lekérdezése több bérlő távoli ügyfél-munkaterületeiről
- Riasztások létrehozása az ügyfél-bérlőkben, amelyek az automatizálást, például az Azure Automation runbookokat vagy az Azure Functions-t aktiválják a szolgáltató bérlőjében webhookokon keresztül

[Azure-szabályzat:](../../governance/policy/index.yml)

- A megfelelőségi pillanatképek a delegált előfizetéseken belüli hozzárendelt szabályzatok részleteit jelenítik meg
- Házirend-definíciók létrehozása és szerkesztése delegált előfizetésen belül
- Ügyfél által definiált házirend-definíciók hozzárendelése a delegált előfizetésen belül
- Az ügyfelek a szolgáltató által készített irányelveket is láthatják az általuk saját maguk által készített irányelvek mellett.
- Az [ügyfél-bérlőn belüli üzembe helyezésIfNotExists vagy hozzárendelések módosítása](../how-to/deploy-policy-remediation.md)

[Azure Erőforrás grafikon:](../../governance/resource-graph/index.yml)

- Most már tartalmazza a bérlőazonosítót a visszaadott lekérdezési eredményekben, így azonosíthatja, hogy egy előfizetés az ügyfél-bérlőhöz vagy a szolgáltató bérlőhöz tartozik-e.

[Azure Biztonsági Központ:](../../security-center/index.yml)

- Több-bérlő láthatósága
  - A biztonsági házirendeknek való megfelelés figyelése és a biztonsági lefedettség biztosítása az összes bérlő erőforrásai között
  - Folyamatos szabályozási megfelelőség-ellenőrzés több ügyfél számára egyetlen nézetben
  - A biztonságos pontszámszámítással nyomon követheti, triage-elheti és rangsorolja a végrehajtható biztonsági javaslatokat
- Bérlőközi biztonsági testtartás-kezelés
  - Biztonsági házirendek kezelése
  - A végrehajtható biztonsági ajánlásoknak nem megfelelő erőforrásokkal kapcsolatos műveletek
  - A biztonsággal kapcsolatos adatok gyűjtése és tárolása
- Több-bérlős fenyegetés észlelése és védelme
  - Fenyegetések észlelése a bérlők erőforrásai között
  - Speciális veszélyforrások elleni védelem, például just-in-time (JIT) virtuális gép-hozzáférés alkalmazása
  - A hálózati biztonsági csoport konfigurációjának megerősítése az Adaptive Network Hardening segítségével
  - Győződjön meg arról, hogy a kiszolgálók csak azokat az alkalmazásokat és folyamatokat futtatják, amelyeknek adaptív alkalmazásvezérlőkkel kell lenniük
  - A fontos fájlok és rendszerleíró bejegyzések változásainak figyelése a FÁJLintegritás figyelésével (FIM)

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Azure Sentinel-erőforrások kezelése [az ügyfelek bérlőiben](../../sentinel/multiple-tenants-service-providers.md)
- [Támadások nyomon követése és biztonsági riasztások megtekintése több ügyfél-bérlő között](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Az Azure szolgáltatás állapota:](../../service-health/index.yml)

- Az ügyfélerőforrások állapotának figyelése az Azure Resource Health segítségével
- Az ügyfelek által használt Azure-szolgáltatások állapotának nyomon követése

[Azure site helyreállítás:](../../site-recovery/index.yml)

- Az Azure-beli virtuális gépek vész-helyreállítási lehetőségeinek kezelése az ügyfél-bérlőkben (vegye figyelembe, hogy a RunAs-fiókok nem használhatók virtuálisgép-bővítmények másolására)

[Azure virtuális gépek:](../../virtual-machines/index.yml)

- Virtuálisgép-bővítmények használatával üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosíthat az azure-beli virtuális gépeken az ügyfélbérlőkön
- Az azure-beli virtuális gépek ügyfél-bérlőkben lévő rendszerindítási diagnosztika használata
- Virtuális gépek elérése soros konzollal az ügyfélbérlőkben
- Ne feledje, hogy az Azure Active Directory nem használható távoli bejelentkezéshez egy virtuális gépbe, és nem integrálható virtuális gép a Key Vault jelszavak, titkos kulcsok vagy titkosítási kulcsok lemeztitkosításhoz

[Azure virtuális hálózat:](../../virtual-network/index.yml)

- Virtuális hálózatok és virtuális hálózati csatolókártyák (vNIC-k) telepítése és kezelése az ügyfél-bérlőkön belül

Támogatási kérelmek:

- A delegált erőforrások támogatási kérelmeinek megnyitása az Azure Portal **Súgó + támogatási** paneljén (a delegált hatókör számára elérhető támogatási terv kiválasztása)

## <a name="current-limitations"></a>Aktuális korlátozások
Kérjük, vegye figyelembe az alábbi aktuális korlátozásokat:

- Az Azure Resource Manager által kezelt kérelmek az Azure delegált erőforrás-kezelésével hajthatók végre. A kérelmek hez kapcsolódó művelet `https://management.azure.com`URI-k a következővel kezdődnek, hogy . Azonban az erőforrástípus példánya által kezelt kérelmek (például a KeyVault titkos kulcsok elérése vagy a tárolási adatok elérése) nem támogatottaz Azure delegált erőforrás-kezelése. Ezekhez a kérelmekhez a művelet URI-k általában egy olyan `https://myaccount.blob.core.windows.net` címmel `https://mykeyvault.vault.azure.net/`kezdődnek, amely egyedi a példányon, például vagy . Ez utóbbiak általában adatműveletek, nem pedig kezelési műveletek. 
- A szerepkör-hozzárendeléseknek szerepköralapú hozzáférés-vezérlési (RBAC) [beépített szerepköröket kell használniuk.](../../role-based-access-control/built-in-roles.md) Az összes beépített szerepkör jelenleg támogatott az Azure delegált erőforrás-kezelése, kivéve a tulajdonos vagy a [DataActions](../../role-based-access-control/role-definitions.md#dataactions) engedéllyel rendelkező beépített szerepkörök. A Felhasználói hozzáférés rendszergazdája szerepkör csak korlátozott ideig használható [a szerepkörök felügyelt identitásokhoz való hozzárendeléséhez.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)  Egyéni szerepkörök és [a klasszikus előfizetés-rendszergazdai szerepkörök](../../role-based-access-control/classic-administrators.md) nem támogatottak.
- Az Azure Databricks szolgáltatást használó előfizetések be- és alaplapi előfizetései azonban jelenleg nem indíthatnak Azure Databricks-munkaterületeket delegált előfizetésen.
- Bár az Azure delegált erőforrás-kezelése, amely erőforrás-zárolással rendelkezik, az azure-beli delegált erőforrás-kezelés, ezek a zárolások nem akadályozza meg, hogy a kezelő bérlő felhasználói műveleteket hajtsanak végre. A rendszer által felügyelt erőforrásokat védő [hozzárendelések megtagadása,](../../role-based-access-control/deny-assignments.md) például az Azure által felügyelt alkalmazások vagy az Azure Blueprints (rendszer-hozzárendelt megtagadási hozzárendelések) által létrehozott hozzárendelések megakadályozhatják, hogy a felügyelt bérlő felhasználói az adott erőforrásokra reagálnak; jelenleg azonban az ügyfél-bérlő felhasználói nem hozhatnak létre saját megtagadási hozzárendeléseket (a felhasználó által hozzárendelt megtagadási hozzárendeléseket).
- A kezelő bérlő ben lévő felhasználók nem férhetnek hozzá a delegált ügyfél-előfizetés számlázási adatainak megtekintéséhez, még akkor sem, ha olyan beépített szerepkörrel rendelkeznek, amely általában hozzáférést engedélyez. Ennek az az oka, hogy a számlázási adatokhoz való hozzáférés további lépéseket igényel, amelyek jelenleg csak az ugyanazon a bérlőn belüli felhasználók számára támogatottak.

## <a name="next-steps"></a>További lépések

- Az azure-beli delegált erőforrás-kezeléshez az [Azure Resource Manager-sablonjainak használatával,](../how-to/onboard-customer.md) vagy [egy privát vagy nyilvánosan felügyelt szolgáltatások azure Piactérre való közzétételével.](../how-to/publish-managed-services-offers.md)
- Az Azure Portalon az **Ügyfeleimmel** tekintheti meg és kezelheti az [ügyfeleket.](../how-to/view-manage-customers.md)
