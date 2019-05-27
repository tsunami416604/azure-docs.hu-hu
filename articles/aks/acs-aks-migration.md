---
title: Az Azure Kubernetes Service (AKS) az Azure Container Service (ACS) áttelepítése
description: Az Azure Container Service (ACS) áttelepíthetők az Azure Kubernetes Service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977717"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (AKS) az Azure Container Service (ACS) áttelepítése

Ezen cikk segítségével megtervezheti és az Azure Container Service (ACS) Kubernetes-szel és az Azure Kubernetes Service (AKS) között sikeres áttelepítés végrehajtásához. Könnyebben fontos döntéseket, ez az útmutató részletesen ACS és az AKS közötti különbségeket, és az áttelepítési folyamat áttekintést nyújt.

## <a name="differences-between-acs-and-aks"></a>ACS és az AKS közötti különbségek

ACS és az AKS eltérőek a néhány főbb területeken, amelyek befolyásolják a migrálás. Bármely áttelepítése előtt tekintse át és tervezi, hogy oldja meg a következő eltérésekkel:

* AKS-csomópontok használata [felügyelt lemezek](../virtual-machines/windows/managed-disks-overview.md).
    * Nem felügyelt lemezek őket csatolhat AKS-csomópontok előtt konvertálni kell.
    * Egyéni `StorageClass` objektumokat az Azure-lemezek kell módosítani a `unmanaged` való `managed`.
    * Bármely `PersistentVolumes` használjon `kind: Managed`.
* Támogatja az AKS [több csomópontkészletek](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (jelenleg előzetes verzióban érhető el).
* A Windows Server-alapú csomópontokon jelenleg [az AKS előzetes](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* Az AKS támogatja a korlátozott számú [régiók](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* Az AKS felügyelt szolgáltatás, amely egy üzemeltetett Kubernetes-vezérlősík. Szüksége lehet az alkalmazások módosítását, ha korábban már módosította a konfigurációt az ACS főkiszolgálók.

## <a name="differences-between-kubernetes-versions"></a>Kubernetes-verzió közötti különbségek

Ha (például az a 1.9.x 1.7.x) kubernetes újabb verzióra való migráláshoz, tekintse át a tudni, hogy néhány változtatást, hogy a Kubernetes API-t a következő erőforrások:

* [Áttelepítés egy ThirdPartyResource CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [API-számítási feladatok változásai 1.8-as és 1.9 verziója](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>Migrálási szempontok

### <a name="agent-pools"></a>Ügynökkészletek

Bár az AKS kezeli a Kubernetes vezérlősík, továbbra is megadhat a méretét és az új fürtbe felvenni kívánt csomópontokat. Ha azt szeretné, hogy az aks-ben az ACS-ből egy 1:1 megfeleltetését, szeretné rögzíteni a meglévő ACS csomópont adatait. Az új AKS-fürt létrehozásakor, használja ezeket az adatokat.

Példa:

| Name (Név) | Count | Virtuális gép mérete | Operációs rendszer |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1. | Standard_D2_v2 | Windows |

Mivel további virtuális gépek lesznek üzembe helyezve az előfizetés az áttelepítés során, ellenőrizze, hogy a kvóták és korlátozások elegendőek ezekhez az erőforrásokhoz. 

További információkért lásd: [Azure-előfizetés- és Szolgáltatáskorlátok](https://docs.microsoft.com/azure/azure-subscription-service-limits). Ellenőrizze a jelenlegi kvótákat, az Azure Portalon lépjen a [előfizetések paneljén](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), válassza ki az előfizetését, és válassza **használat + kvóták**.

### <a name="networking"></a>Hálózat

Összetett alkalmazások idővel helyett egyszerre általában kell áttelepíteni. Ez azt jelenti, hogy a régi és új környezetben kell előfordulhat, hogy a hálózaton keresztül kommunikálnak. Az alkalmazásokat, amelyek korábban használt `ClusterIP` szolgáltatásokat való kommunikációhoz kell előfordulhat, hogy elérhetővé tehető típus `LoadBalancer` és a megfelelő biztonságáról.

Az áttelepítéshez, érdemes-ügyfelek irányítása az új az aks-en futó szolgáltatásokat. Azt javasoljuk, hogy DNS-t, mutasson a terheléselosztóhoz, amely helyezkedik el az AKS-fürt frissítésével irányítja a forgalmat.

### <a name="stateless-applications"></a>Állapot nélküli alkalmazások

Állapot nélküli alkalmazások áttelepítése a legegyszerűbb eset áll fenn. Fogja a YAML-definíciók alkalmazza az új fürtre, győződjön meg arról, hogy minden a várt módon működik, és aktiválni az új fürt forgalom átirányítása.

### <a name="stateful-applications"></a>Állapotalapú alkalmazások

Gondosan tervezze meg a migrálást, az állapotalapú alkalmazások adatvesztés vagy váratlan állásidő elkerülése érdekében.

#### <a name="highly-available-applications"></a>Magas rendelkezésre állású alkalmazások

Magas rendelkezésre állású konfigurációban néhány állapotalapú alkalmazásokat helyezhet üzembe. Ezek az alkalmazások adatokat tud másolni különböző replikák. Ha jelenleg használja a központi telepítés a rendezés, akkor előfordulhat, hogy tudni hozzon létre egy új tag az új AKS-fürtöt, és majd telepítse át az alsóbb rétegbeli hívóit minimális hatással. Az áttelepítési lépéseket ebben a forgatókönyvben általában a következők:

1. Az aks-en, hozzon létre egy új másodlagos replikára.
2. Várjon, amíg az adatok replikálásához.
3. Nem tud több mint egy másodlagos replikát az új elsődleges.
4. Forgalom átirányítása az AKS-fürtöt.

#### <a name="migrating-persistent-volumes"></a>Állandó kötetek áttelepítése

Ha meglévő állandó kötetek migráláshoz az aks-ben, általában végrehajtásával fogjuk ezeket a lépéseket:

1. Az alkalmazás fokozatos leválasztási műveletének ír. (Ez a lépés nem kötelező, és kell leállítani.)
2. A lemezek pillanatképek készítése.
3. Hozzon létre új felügyelt lemezek a pillanatképeket.
4. Állandó köteteket hozhat létre az aks-ben.
5. Frissítse a pod-specifikációk [a meglévő kötetek](https://docs.microsoft.com/azure/aks/azure-disk-volume) PersistentVolumeClaims (statikus kiépítés) helyett.
6. Telepítse az alkalmazást az aks-ben.
7. Ellenőrzése.
8. Forgalom átirányítása az AKS-fürtöt.

> [!IMPORTANT]
> Ha nem kíván fokozatos leválasztási műveletének írások, szüksége replikálja az adatokat az új központi telepítés. Ellenkező esetben lesz hagyja ki az adatokat arról, hogy Miután megtette a pillanatképeit.

Néhány nyílt forráskódú eszközök segíthetnek a felügyelt lemez gyors létrehozásához és a Kubernetes-fürtök közötti kötetek áttelepítése:

* [Az Azure CLI-lemezmásolás bővítmény](https://github.com/noelbundick/azure-cli-disk-copy-extension) másolja, és konvertálja a lemezeket erőforráscsoportok és az Azure-régiók között.
* [Az Azure Kube CLI bővítmény](https://github.com/yaron2/azure-kube-cli) ACS Kubernetes-kötetek enumerálása és áttelepíti a azokat egy AKS-fürtöt.

#### <a name="azure-files"></a>Azure Files

Lemezek, ellentétben az Azure Files csatlakoztathatók a több gazdagépen. Az AKS-fürt az Azure és a Kubernetes nem akadályozzák meg, hozzon létre egy pod, amely az ACS-fürt továbbra is használja. Az adatvesztéssel és a váratlan viselkedés elkerülése érdekében győződjön meg arról, hogy a fürtök nem írni ugyanazokat a fájlokat egy időben.

Az alkalmazás üzemeltethető, amelyek az ugyanabban fájlmegosztásba több replikát, ha az állapot nélküli áttelepítési lépések és üzembe helyezése a YAML-definíciók az új fürtön. Ha nem, egy lehetséges migrálási megközelítés az alábbi lépésekből áll:

1. Az alkalmazás telepítése az AKS 0 replika számával együtt.
2. Az alkalmazás méretezése az ACS 0-ra. (Ebben a lépésben kell leállítani.)
3. Legfeljebb 1 skálázzuk az alkalmazást az aks-en.
4. Ellenőrzése.
5. Forgalom átirányítása az AKS-fürtöt.

Ha el szeretné indítani egy üres megosztás és a forrásadatok egy másolat készítése, használhatja a [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) parancsokat az adatok áttelepítéséhez.

### <a name="deployment-strategy"></a>Üzembe helyezési stratégiához

Azt javasoljuk, hogy a meglévő CI/CD-folyamat használatával telepít egy helyes konfiguráció az aks-ben. Klónozza a meglévő üzembe helyezési feladatok, és ellenőrizze, hogy `kubeconfig` az új AKS-fürtöt mutat.

Ha ez nem lehetséges, erőforrás-definíciókban exportálása az ACS-ből, és alkalmazhatja azokat az aks-ben. Használhat `kubectl` objektumok exportálása.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Számos nyílt forráskódú eszközök segítségével, az üzembe helyezésben függően:

* [Velero](https://github.com/heptio/ark) (Ez az eszköz használatához Kubernetes 1.7-es.)
* [Az Azure Kube CLI-bővítmény](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Migrálási lépések

1. [AKS-fürt létrehozása](https://docs.microsoft.com/azure/aks/create-cluster) az Azure portal, Azure CLI-vel vagy az Azure Resource Manager-sablon használatával.

   > [!NOTE]
   > Keresse meg a mintát az Azure Resource Manager-sablonok az aks-ben, a [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) tárházban a Githubon.

2. A YAML-definíciók, végezze el a szükséges módosításokat. Helyettesítse be például `apps/v1beta1` a `apps/v1` a `Deployments`.

3. [Telepítsen át köteteket](#migrating-persistent-volumes) (nem kötelező), az ACS-fürtben az AKS-fürt.

4. A CI/CD-rendszer használatával helyezhet üzembe alkalmazásokat az aks-ben. Vagy a YAML-definíciókat a alkalmazni a kubectl használatával.

5. Ellenőrzése. Győződjön meg arról, hogy az alkalmazások a várt módon működik-e, és, hogy az áttelepített adatokat a rendszer átmásolt keresztül.

6. Forgalom átirányítása. Frissítse a DNS-ügyfelek irányítása az AKS üzembe helyezés.

7. Áttelepítés utáni feladatok befejezéséhez. Ha át a köteteket, és úgy döntött, nem fokozatos leválasztási műveletének írások, másolja az adatokat az új fürtön.
