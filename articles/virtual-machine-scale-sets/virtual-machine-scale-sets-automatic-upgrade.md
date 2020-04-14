---
title: Automatikus operációsrendszer-lemezkép-frissítések az Azure virtuálisgép-méretezési készleteivel
description: Ismerje meg, hogyan frissítheti automatikusan az operációsrendszer-lemezképet virtuálisgép-példányokon egy méretezési csoportban
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: mimckitt
ms.openlocfilehash: b1e5ad60041e9d3b902a06a4875206fa061c73e6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269907"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Az Azure virtuálisgép-méretezési készlet automatikus operációsrendszer-lemezkép-frissítések

A méretezési csoport automatikus operációsrendszer-lemezfrissítésének engedélyezése megkönnyíti a frissítéskezelést azáltal, hogy biztonságosan és automatikusan frissíti az operációsrendszer-lemezt a méretezési csoport összes példányára.

Az automatikus operációsrendszer-frissítés a következő jellemzőkkel rendelkezik:

- Konfigurálás után a rendszera lemezképközzétevők által közzétett legújabb operációsrendszer-lemezkép felhasználói beavatkozás nélkül automatikusan a méretezési csoportra lesz alkalmazva.
- A példányok kötegeit gördülő módon frissíti minden alkalommal, amikor a közzétevő új lemezképet tesz közzé.
- Integrálható az alkalmazásállapot-mintavételekkel és [az Alkalmazásállapot-bővítménysel.](virtual-machine-scale-sets-health-extension.md)
- Működik az összes virtuális gép mérete, valamint a Windows és linuxos rendszerképek.
- Az automatikus frissítéseket bármikor letilthatja (az operációs rendszer frissítése manuálisan is kezdeményezhető).
- A virtuális gép operációs rendszerlemezét a legújabb lemezverzióval létrehozott új operációsrendszer-lemez váltja fel. A konfigurált bővítmények és az egyéni adatparancsfájlok futnak, míg a megőrzött adatlemezek megmaradnak.
- [A bővítmény szekvenálása](virtual-machine-scale-sets-extension-sequencing.md) támogatott.
- Az operációs rendszer automatikus lemezkép-frissítése bármilyen méretű méretezési csoportban engedélyezhető.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hogyan működik az automatikus operációsrendszer-lemezkép-frissítés?

A frissítés úgy működik, hogy a virtuális gép operációsrendszer-lemezét lecseréli a legújabb lemezkép-verzióval létrehozott új lemezre. A konfigurált bővítmények és az egyéni adatparancsfájlok az operációs rendszer lemezén futnak, míg a megőrzött adatlemezek megmaradnak. Az alkalmazás állásidejének minimalizálása érdekében a frissítések kötegekben történnek, és a méretezési csoport frissítésének legfeljebb 20%-a bármikor történik. Az Azure Load Balancer alkalmazás állapotmintázatát vagy [az Alkalmazásállapot-bővítményt](virtual-machine-scale-sets-health-extension.md)is integrálhatja. Azt javasoljuk, hogy egy alkalmazás szívverését, és érvényesítse a frissítési sikeres frissítési folyamat minden egyes kötegek.

A frissítési folyamat a következőképpen működik:
1. A frissítési folyamat megkezdése előtt az orchestrator biztosítja, hogy a példányok legfeljebb 20%-a a teljes méretezési csoportban nem kifogástalan (bármilyen okból).
2. A frissítési vezénylő azonosítja a virtuálisgép-példányok frissítéséhez szükséges köteget, és bármely köteg, amelylegfeljebb 20%-a a teljes példányszám, egy virtuális gép minimális kötegméretétől függ.
3. A virtuálisgép-példányok kijelölt kötegének operációs rendszerlemezét lecseréli egy új operációsrendszer-lemez, amelyet a legújabb lemezképből hoztak létre. A méretezési csoport modelljében minden megadott bővítmény és konfiguráció a frissített példányra vonatkozik.
4. A konfigurált alkalmazásállapot-mintavételekkel vagy alkalmazásállapot-bővítményt kiszolgáló méretezési készletek esetén a frissítés legfeljebb 5 percet vár, amíg a példány kifogástalan állapotba lép, mielőtt továbblépne a következő köteg frissítéséhez. Ha egy példány a frissítés után 5 percen belül nem állítja helyre az állapotát, akkor alapértelmezés szerint a példány előző operációsrendszer-lemeze visszaáll.
5. A frissítés orchestrator is nyomon követi a százalékos példányok, amelyek nem megfelelő állapotúvá válnak a frissítés után. A frissítés leáll, ha a frissített példányok több mint 20%-a nem kifogástalanállapotúvá válik a frissítési folyamat során.
6. A fenti folyamat addig folytatódik, amíg a méretezési csoport összes példánya frissítve nem lett.

A méretezési csoport operációsrendszer-frissítés ivénító ellenőrzi a teljes méretezési csoport állapotát, mielőtt minden köteg frissítése. Egy köteg frissítése közben lehetnek más egyidejű tervezett vagy nem tervezett karbantartási tevékenységek, amelyek hatással lehetnek a méretezési csoport példányainak állapotát. Ilyen esetekben, ha a méretezési csoport példányainak több mint 20%-a nem megfelelő állapotúvá válik, majd a méretezési csoport frissítése leáll az aktuális köteg végén.

## <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
Jelenleg csak bizonyos operációsrendszer-platformrendszerképek támogatottak. Az egyéni képtámogatás [előnézetben](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images-preview) érhető el az egyéni képekhez a [Megosztott képtáron](shared-image-galleries.md)keresztül.

Jelenleg a következő platformska-k támogatottak (és további adódik hozzá rendszeresen):

| Közzétevő               | Operációs rendszer ajánlata      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Hullám (OpenLogic)  | CentOS        | 7,5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Adatközpont    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-tárolókkal |
| Microsoft Corporation   | WindowsServer | 2019-Adatközpont |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-tárolókkal |
| Microsoft Corporation   | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Az operációs rendszer automatikus lemezkép-frissítésének konfigurálása szükséges követelmények

- A *lemezkép verziótulajdonságát* *a legújabbra*kell állítani.
- Alkalmazásállapot-mintavételek vagy [alkalmazásállapot-bővítmény](virtual-machine-scale-sets-health-extension.md) nem Service Fabric méretezési csoportok használata.
- Használja a Compute API 2018-10-01-es vagy újabb verzióját.
- Győződjön meg arról, hogy a méretezési modellben megadott külső erőforrások elérhetők és naprakészek. Ilyenek például a SAS URI a virtuálisgép-bővítmény tulajdonságainak rendszerindítási hasznos ideig, a tárfiókban lévő hasznos adat, a modell titkos kulcsokra való hivatkozás stb.
- A Windows virtuális gépeket használó méretezési készletek esetén a 2019-03-01-es számítási API-verziótól kezdve a *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* tulajdonságnak *hamisra* kell állítania a méretezési csoport modelldefiníciójában. A fenti tulajdonság lehetővé teszi a virtuális gép en-virtuális gép frissítése, ahol a "Windows Update" operációs rendszer javításokat alkalmaz az operációs rendszer lemezének cseréje nélkül. Ha a méretezési csoportban engedélyezve van az operációs rendszer automatikus lemezkép-frissítése, a "Windows Update" szolgáltatáson keresztül nem szükséges további frissítést frissíteni.

### <a name="service-fabric-requirements"></a>A Service Fabric követelményei

Ha a Service Fabric használata, győződjön meg arról, hogy az alábbi feltételek teljesülnek:
-   A Service Fabric [tartóssági szintje](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) ezüst vagy arany, és nem bronz.
-   A méretezési csoport modelldefiníciójának Service Fabric-bővítményének TypeHandlerVersion 1.1 vagy újabb verzióval kell rendelkeznie.
-   Tartóssági szint azonosnak kell lennie a Service Fabric-fürt és a Service Fabric-bővítmény a méretezési csoport modell definíciója.

Győződjön meg arról, hogy a tartóssági beállítások nem egyeznek a Service Fabric-fürt és a Service Fabric-bővítmény, mert egy eltérés eredményez frissítési hibákat. A tartóssági szintek az ezen az [oldalon](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)ismertetett irányelvek szerint módosíthatók.


## <a name="automatic-os-image-upgrade-for-custom-images-preview"></a>Az operációs rendszer automatikus képfrissítése egyéni képekhez (előnézet)

> [!IMPORTANT]
> Az egyéni lemezképek automatikus operációsrendszer-lemezkép-frissítése jelenleg nyilvános előzetes verzióban van. Az alábbiakban ismertetett nyilvános előzetes verzió használatához engedélyezési eljárás szükséges.
> Ez az előzetes verzió szolgáltatásszint-szerződés nélkül érhető el, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az operációs rendszer automatikus lemezkép-frissítése előzetes verzióban érhető el a [Megosztott képtáron](shared-image-galleries.md)keresztül telepített egyéni lemezképekhez. Más egyéni lemezképek nem támogatottak az operációs rendszer automatikus lemezkép-frissítéseihez.

Az előzetes verzió engedélyezéséhez előfizetésenként egyszeri opt-in szükséges az *AutomaticOSUpgradeWithGalleryImage* funkcióhoz, az alábbiakban részletezett módon.

### <a name="rest-api"></a>REST API
A következő példa bemutatja, hogyan engedélyezheti az előfizetés előzetes verzióját:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage/register?api-version=2015-12-01`
```

A funkcióregisztráció akár 15 percet is igénybe vehet. A regisztrációs állapot ellenőrzése:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage?api-version=2015-12-01`
```

Miután a szolgáltatás regisztrálva lett az előfizetéshez, fejezze be az opt-in folyamatot a módosítás nak a számítási erőforrás-szolgáltatóba való propagálásával.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
A [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsmag segítségével engedélyezze az előfizetés előzetes verzióját.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

A funkcióregisztráció akár 15 percet is igénybe vehet. A regisztrációs állapot ellenőrzése:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Miután a szolgáltatás regisztrálva lett az előfizetéshez, fejezze be az opt-in folyamatot a módosítás nak a számítási erőforrás-szolgáltatóba való propagálásával.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az [szolgáltatásregisztráció](/cli/azure/feature#az-feature-register) használatával engedélyezheti az előfizetés előzetes verzióját.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

A funkcióregisztráció akár 15 percet is igénybe vehet. A regisztrációs állapot ellenőrzése:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Miután a szolgáltatás regisztrálva lett az előfizetéshez, fejezze be az opt-in folyamatot a módosítás nak a számítási erőforrás-szolgáltatóba való propagálásával.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="additional-requirements-for-custom-images"></a>Az egyéni lemezképekre vonatkozó további követelmények
- A fent leírt opt-in folyamatot előfizetésenként csak egyszer kell elvégezni. Engedélyezés befejezése után az automatikus operációsrendszer-frissítések engedélyezhetők az adott előfizetés bármely méretezési készletéhez.
- A megosztott képtár bármely előfizetésben lehet, és nem igényel külön-külön leválasztást. Csak a méretezési készlet előfizetéshez szükséges a funkció opt-in.
- Az automatikus operációsrendszer-lemezkép-frissítés konfigurációs folyamata megegyezik az összes méretezési csoportesetében, ahogy azt a lap [konfigurációs szakaszában](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) részletezi.
- Az automatikus operációsrendszer-lemezkép-frissítésekhez konfigurált méretezési példányok a megosztott képtár-lemezkép legújabb verziójára frissülnek, amikor a lemezkép új verzióját közzéteszik és [replikálják](shared-image-galleries.md#replication) a méretezési csoport régiójába. Ha az új lemezkép nincs replikálva arra a régióra, ahol a méretezési csoport telepítve van, a méretezési csoport példányai nem frissülnek a legújabb verzióra. A regionális képreplikáció lehetővé teszi az új kép bevezetésének szabályozását a méretezési csoportokhoz.
- Az új lemezkép-verziót nem szabad kizárni az adott galériakép legújabb verziójából. A galériakép legújabb verziójából kizárt képverziók nem kerülnek a méretezési csoportba az operációs rendszer automatikus lemezkép-frissítése révén.

> [!NOTE]
>Akár 2 órát is igénybe vehet egy méretezési csoport, hogy az első kép bevezetése után a méretezési csoport automatikus operációs rendszer frissítése van konfigurálva. Ez egy egyszeri késleltetés méretezési készletenként. Az ezt követő képbevezetéseket a rendszer ilyen késleltetés nélkül alkalmazza a méretezési készletre.


## <a name="configure-automatic-os-image-upgrade"></a>Az operációs rendszer automatikus lemezkép-frissítésének konfigurálása
Az operációs rendszer automatikus lemezkép-frissítésének konfigurálásához győződjön meg arról, hogy az *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* tulajdonság *értéke igaz* a méretezési csoport modelldefiníciójában.

### <a name="rest-api"></a>REST API
A következő példa bemutatja, hogyan állíthatók be az operációs rendszer automatikus frissítései egy méretezési csoport modelljén:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag segítségével konfigurálhatja a méretezési csoport automatikus operációsrendszer-lemezkép-frissítéseit. A következő példa a myResourceGroup nevű erőforráscsoport ban a *myScaleSet* nevű méretezési csoport automatikus frissítéseit *konfigurálja:*

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
A [virtuális gépek frissítése](/cli/azure/vmss#az-vmss-update) segítségével konfigurálja a méretezési csoport automatikus operációsrendszer-lemezkép-frissítéseit. Használja az Azure CLI 2.0.47 vagy újabb. A következő példa a myResourceGroup nevű erőforráscsoport ban a *myScaleSet* nevű méretezési csoport automatikus frissítéseit *konfigurálja:*

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Alkalmazásállapot-mintavételek használata

Az operációs rendszer frissítése során a méretezési csoport virtuálisgép-példányai egyszerre egy köteget frissítenek. A frissítés csak akkor folytatódik, ha az ügyfélalkalmazás kifogástalan állapotban van a frissített virtuálisgép-példányokon. Azt javasoljuk, hogy az alkalmazás biztosítja az állapotjelzéseket a méretezési készlet operációsrendszer-frissítési motor. Alapértelmezés szerint az operációs rendszer frissítése során a platform a virtuális gép energiagazdálkodási állapotát és a bővítmény kiépítési állapotát veszi figyelembe annak megállapítására, hogy egy virtuálisgép-példány kifogástalan állapotú-e a frissítés után. A virtuálisgép-példány operációs rendszerfrissítése során a virtuálisgép-példány operációs rendszerlemezét egy új lemez váltja fel a legújabb lemezkép-verzió alapján. Az operációs rendszer frissítése befejezése után a konfigurált bővítmények ezeken a virtuális gépeken futnak. Az alkalmazás csak akkor tekinthető kifogástalannak, ha a példány összes bővítménye sikeresen ki van építve.

A méretezési csoport opcionálisan konfigurálható alkalmazásállapot-mintavételekkel, hogy a platform pontos információkat nyújtson az alkalmazás folyamatban lévő állapotáról. Az alkalmazásállapot-mintavételek egyéni terheléselosztó-mintavételek, amelyek állapotjelzésként használatosak. A méretezési csoport virtuálisgép-példányon futó alkalmazás válaszolhat a külső HTTP- vagy TCP-kérelmekre, jelezve, hogy kifogástalan állapotú-e. Az egyéni terheléselosztó-mintavételek működéséről a [Terheléselosztó-mintavételek ismertetése](../load-balancer/load-balancer-custom-probe-overview.md)című témakörben talál további információt. Alkalmazásállapot-mintavételek nem támogatottak a Service Fabric méretezési csoportok. A nem szolgáltatásból álló fabric-méretezési csoportokhoz terheléselosztó alkalmazás állapotminta vagy [alkalmazásállapot-bővítmény](virtual-machine-scale-sets-health-extension.md)szükséges.

Ha a méretezési csoport több elhelyezési csoport használatára van konfigurálva, [szabványos terheléselosztót](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) használó mintavételeket kell használni.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Egyéni terheléselosztó-mintavétel konfigurálása alkalmazásállapot-mintavételként méretezési készleten
Ajánlott eljárásként hozzon létre egy terheléselosztó mintavétel kifejezetten a méretezési csoport állapota. Ugyanaz a végpont egy meglévő HTTP-mintavétel vagy TCP-minta használható, de egy állapotminta lehet szükség a hagyományos terheléselosztó mintavétel eltérő viselkedést. Például egy hagyományos terheléselosztó mintavétel idoszaka nem megfelelő állapotú, ha a terhelés a példány túl magas, de ez nem lenne megfelelő a példány állapotának meghatározásához egy automatikus operációsrendszer-frissítés során. Állítsa be a szondát úgy, hogy a szondának két percnél rövidebb a vizsgálati sebessége.

A terheléselosztó szonda a méretezési halmaz *networkProfile-jában* is használható, és az alábbiak szerint társítható egy belső vagy nyilvános néző terheléselosztóhoz:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Automatikus operációsrendszer-frissítések használataesetén a Service Fabric, az új operációsrendszer-lemezkép van gördült ki frissítési tartomány frissítési tartomány a Service Fabric futó szolgáltatások magas rendelkezésre állásának fenntartása érdekében. Az automatikus operációsrendszer-frissítések szolgáltatásban a Service Fabric a fürt konfigurálni kell a Silver tartóssági szint vagy magasabb használata. A Service Fabric-fürtök tartóssági jellemzőiről további információt ebben a [dokumentációban](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)talál.

### <a name="keep-credentials-up-to-date"></a>A hitelesítő adatok naprakészen tartása
Ha a méretezési csoport bármilyen hitelesítő adatokat használ a külső erőforrások eléréséhez, például egy Virtuálisgép-bővítmény, amely sas-jogkivonat használatára konfigurálva van a tárfiókhoz, győződjön meg arról, hogy a hitelesítő adatok frissülnek. Ha a hitelesítő adatok, beleértve a tanúsítványokat és a jogkivonatokat, lejárt, a frissítés sikertelen lesz, és a virtuális gépek első kötege sikertelen állapotban marad.

A virtuális gépek helyreállításának és az automatikus operációsrendszer-frissítés újbóli engedélyezésének ajánlott lépései, ha erőforrás-hitelesítési hiba történt:

* A bővítmény(ek)nek átadott jogkivonat (vagy bármely más hitelesítő adat) újragenerálása.
* Győződjön meg arról, hogy a virtuális gépen belül a külső entitásokkal való kapcsolatra használt hitelesítő adatok naprakészek.Ensure that any credential used from inside the VM to talk to external entitis to date.
* Frissítse a bővítmény(eke)t a méretezési modellben bármely új jogkivonattal.
* Telepítse a frissített méretezési csoport, amely frissíti az összes virtuálisgép-példányok, beleértve a sikertelen is.

## <a name="using-application-health-extension"></a>Alkalmazásállapot-bővítmény használata
Az Alkalmazásállapot-bővítmény egy virtuálisgép-méretezési csoport példányában van telepítve, és a méretezési csoport példányán belülről jelentést tesz a virtuális gép állapotáról. Konfigurálhatja a bővítményt egy alkalmazásvégpont mintavételére, és frissítheti az alkalmazás állapotát az adott példányon. Ezt a példányállapotot az Azure ellenőrzi annak megállapításához, hogy egy példány jogosult-e a frissítési műveletekre.

Mivel a bővítmény egy virtuális gépállapot-jelentést, a bővítmény olyan helyzetekben használható, ahol a külső mintavételek, például az Application Health Mintavételek (amelyek egyéni Azure Load Balancer [mintavételeket](../load-balancer/load-balancer-custom-probe-overview.md)használnak) nem használhatók.

Az alkalmazásállapot-bővítmény méretezési csoportba való üzembe helyezésének több módja is van, ahogy azt a [cikkben](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)szereplő példák részletezik.

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Az operációs rendszer automatikus lemezkép-frissítéseinek előzményei
Ellenőrizheti a legújabb operációsrendszer-frissítés előzményeit a méretezési készleten az Azure PowerShell, az Azure CLI 2.0 vagy a REST API-k használatával. Az elmúlt két hónapban az operációs rendszer utolsó öt frissítési kísérletének előzményeit kaphatja meg.

### <a name="rest-api"></a>REST API
A következő példa a [REST API-t](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) használja a *myResourceGroup*nevű erőforráscsoport *myScaleSet* nevű méretezési csoportjának állapotának ellenőrzésére:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

A GET hívás a következő példakimenethez hasonló tulajdonságokat ad vissza:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
A [Get-AzVmss](/powershell/module/az.compute/get-azvmss) parancsmag segítségével ellenőrizze az operációs rendszer frissítési előzményeit a méretezési készlethez. A következő példa bemutatja, hogyan tekintse át a *myResourceGroup*nevű erőforráscsoport *myScaleSet* nevű méretezési csoportjának operációsrendszer-frissítési állapotát:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Használja [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) az operációs rendszer frissítési előzményei a méretezési rendszer a méretezési előzményei. Használja az Azure CLI 2.0.47 vagy újabb. A következő példa bemutatja, hogyan tekintse át a *myResourceGroup*nevű erőforráscsoport *myScaleSet* nevű méretezési csoportjának operációsrendszer-frissítési állapotát:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hogyan juthat el a platform operációs rendszer lemezképének legújabb verziójához?

Az alábbi példák segítségével beszerezheti az automatikus operációsrendszer-frissítés által támogatott frissítéshez rendelkezésre álló lemezkép-verziókat:

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Operációs rendszer lemezkép-frissítésének manuális aktiválása
Ha az operációs rendszer automatikus lemezkép-frissítése engedélyezve van a méretezési csoportban, nem kell manuálisan elindítania a képfrissítéseket a méretezési csoportban. Az operációs rendszer frissítése orchestrator automatikusan alkalmazza a legújabb elérhető lemezkép-verzió a méretezési csoport példányai manuális beavatkozás nélkül.

Olyan konkrét esetekben, amikor nem szeretné megvárni, hogy az orchestrator alkalmazza a legújabb lemezképet, manuálisan is aktiválhatja az operációs rendszer lemezkép-frissítést az alábbi példák használatával.

> [!NOTE]
> Az operációs rendszer lemezkép-frissítéseinek manuális aktiválása nem biztosít automatikus visszaállítási képességeket. Ha egy példány nem állítja helyre az állapotát egy frissítési művelet után, az előző operációsrendszer-lemez nem állítható vissza.

### <a name="rest-api"></a>REST API
Az [operációs rendszer frissítésének indítása](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) API-hívással indítsa el a működés közbeni frissítést, és helyezze át az összes virtuálisgép-méretezési példányt a legújabb elérhető lemezkép-operációs rendszer verziójára. A legújabb elérhető operációsrendszer-verziót már futtató példányokat ez nem érinti. A következő példa bemutatja, hogyan indíthatja el az operációs rendszer folyamatos frissítését a *myResourceGroup*nevű erőforráscsoportban a *myScaleSet* nevű méretezési csoporton:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
A [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) parancsmag segítségével ellenőrizze az operációs rendszer frissítési előzményeit a méretezési készlethez. A következő példa bemutatja, hogyan indíthatja el az operációs rendszer folyamatos frissítését a *myResourceGroup*nevű erőforráscsoportban a *myScaleSet* nevű méretezési csoporton:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Használja [az vmss működés közbeni frissítés iindítása](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) az operációs rendszer frissítési előzményei a méretezési készlet. Használja az Azure CLI 2.0.47 vagy újabb. A következő példa bemutatja, hogyan indíthatja el az operációs rendszer folyamatos frissítését a *myResourceGroup*nevű erőforráscsoportban a *myScaleSet* nevű méretezési csoporton:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Üzembe helyezés sablonnal

A sablonok segítségével automatikus operációsrendszer-frissítésekkel telepíthet méretezési készletet a támogatott lemezképekhez, például az [Ubuntu 16.04-LTS rendszerhez.](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>További lépések
További példákért megtudhatja, hogyan használhatja az automatikus operációsrendszer-frissítéseket méretezési csoportokkal, tekintse át a [GitHub-tártaát.](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)
