---
title: Áttelepítés Azure Container Serviceról (ACS) az Azure Kubernetes szolgáltatásba (ak)
description: Migrálás Azure Container Serviceról (ACS) az Azure Kubernetes szolgáltatásba (ak).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 84e0af89e2b3247bc922ab84286a79a0934323a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472994"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Áttelepítés Azure Container Serviceról (ACS) az Azure Kubernetes szolgáltatásba (ak)

Ez a cikk segítséget nyújt a Azure Container Service (ACS) és a Kubernetes és az Azure Kubernetes Service (ak) közötti sikeres áttelepítés megtervezéséhez és végrehajtásához. A legfontosabb döntések elvégzéséhez ez az útmutató ismerteti az ACS és az AK közötti különbségeket, és áttekintést nyújt az áttelepítési folyamatról.

## <a name="differences-between-acs-and-aks"></a>Az ACS és az AK közötti különbségek

Az ACS és az AK különbözik az áttelepítést befolyásoló kulcsfontosságú területeken. A Migrálás előtt tekintse át és tervezze meg a következő eltéréseket:

* Az AK-csomópontok [felügyelt lemezeket](../virtual-machines/windows/managed-disks-overview.md)használnak.
    * A nem felügyelt lemezeket át kell alakítani az AK-csomópontokhoz való csatolás előtt.
    * Az Azure-lemezekhez tartozó egyéni `StorageClass` objektumokat `unmanaged`ról `managed`re kell módosítani.
    * Minden `PersistentVolumes` a `kind: Managed`t kell használnia.
* Az AK [több csomópontos készletet](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)is támogat.
* A Windows Serveren alapuló csomópontok jelenleg előzetes verzióban érhetők [el az AK-ban](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* Az AK támogatja a [régiók](https://docs.microsoft.com/azure/aks/quotas-skus-regions)korlátozott készletét.
* Az AK felügyelt szolgáltatás egy üzemeltetett Kubernetes-vezérlési síkon. Előfordulhat, hogy módosítania kell az alkalmazásait, ha korábban már módosította az ACS-főkiszolgálók konfigurációját.

## <a name="differences-between-kubernetes-versions"></a>Kubernetes-verziók közötti különbségek

Ha a Kubernetes egy újabb verziójára végez áttelepítést, tekintse át a következő forrásokat a Kubernetes verziószámozási stratégiáinak megismeréséhez:

* [Kubernetes-verzió és-verzió – támogatási szabályzat](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>Migrálási szempontok

### <a name="agent-pools"></a>Ügynök készletek

Bár az AK kezeli a Kubernetes-vezérlő síkot, az új fürtben található csomópontok méretét és számát is meghatározza. Feltételezve, hogy a 1:1-es ACS-ről az AK-ra szeretne leképezést készíteni, rögzítenie kell a meglévő ACS-csomópont adatait. Használja ezeket az adatait az új AK-fürt létrehozásakor.

Példa:

| Name (Név) | Mennyiség | Virtuális gép mérete | Operációs rendszer |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Mivel az áttelepítés során további virtuális gépek lesznek telepítve az előfizetésben, ellenőriznie kell, hogy a kvóták és a korlátozások elegendőek-e az adott erőforrásokhoz. 

További információ: Azure- [előfizetés és-szolgáltatási korlátok](https://docs.microsoft.com/azure/azure-subscription-service-limits). Az aktuális kvóták megtekintéséhez a Azure Portal lépjen az [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)panelre, válassza ki az előfizetését, majd válassza a **használat + kvóták**lehetőséget.

### <a name="networking"></a>Hálózat

Összetett alkalmazások esetében általában az idő múlásával, nem pedig egyszerre kell áttérnie. Ez azt jelenti, hogy a régi és az új környezetnek kommunikálnia kell a hálózaton keresztül. Előfordulhat, hogy a korábban `ClusterIP` szolgáltatásokkal való kommunikációra használt alkalmazásokat `LoadBalancer` kell tenni, és a megfelelő védelemmel kell rendelkeznie.

Az áttelepítés befejezéséhez az ügyfeleket az AK-on futó új szolgáltatásokra kell irányítani. Javasoljuk, hogy a forgalom átirányításához frissítse a DNS-t úgy, hogy az AK-fürt előtt található Load Balancer mutasson.

### <a name="stateless-applications"></a>Állapot nélküli alkalmazások

Az állapot nélküli alkalmazások migrálása a legegyszerűbb eset. Alkalmazza a YAML-definíciókat az új fürtre, győződjön meg róla, hogy minden a várt módon működik-e, és irányítsa át a forgalmat az új fürt aktiválásához.

### <a name="stateful-applications"></a>Állapot-nyilvántartó alkalmazások

Gondosan tervezze meg az állapot-nyilvántartó alkalmazások áttelepítését, hogy elkerülje az adatvesztést vagy a váratlan állásidőt.

#### <a name="highly-available-applications"></a>Magasan elérhető alkalmazások

Egy magas rendelkezésre állású konfigurációban üzembe helyezhet néhány állapot-nyilvántartó alkalmazást. Ezek az alkalmazások az adatmásolt replikák között másolhatók. Ha jelenleg ezt a fajta központi telepítést használja, lehet, hogy létre tud hozni egy új tagot az új AK-fürtön, majd az áttelepítést minimális hatással kell lennie az alsóbb rétegbeli hívókra. A forgatókönyv áttelepítési lépései általában a következők:

1. Hozzon létre egy új másodlagos replikát az AK-on.
2. Várjon, amíg a rendszer replikálja az adatforrást.
3. Feladatátvétel az új elsődleges replika másodlagos replikájának létrehozásához.
4. Mutasson forgalmat az AK-fürtre.

#### <a name="migrating-persistent-volumes"></a>Állandó kötetek áttelepítése

Ha a meglévő állandó köteteket AK-ra telepíti át, akkor általában az alábbi lépéseket kell követnie:

1. A fokozatos leválasztása az alkalmazásba ír. (Ez a lépés nem kötelező, és leállást igényel.)
2. Pillanatképek készítése a lemezekről.
3. Hozzon létre új felügyelt lemezeket a pillanatképek közül.
4. Állandó kötetek létrehozása az AK-ban.
5. A pod-specifikációk frissítése a [meglévő kötetek használatára](https://docs.microsoft.com/azure/aks/azure-disk-volume) a PersistentVolumeClaims helyett (statikus kiépítés).
6. Telepítse az alkalmazást az AK-ra.
7. Érvényesít.
8. Mutasson forgalmat az AK-fürtre.

> [!IMPORTANT]
> Ha úgy dönt, hogy nem fokozatos leválasztása az írásokat, replikálnia kell az új központi telepítésre. Ellenkező esetben kihagyhatja a lemez-Pillanatképek elkészítése után írt adatfájlokat.

Egyes nyílt forráskódú eszközök segítségével felügyelt lemezeket hozhat létre és telepíthet át köteteket a Kubernetes-fürtök között:

* Az [Azure CLI lemez másolási bővítménye](https://github.com/noelbundick/azure-cli-disk-copy-extension) lemásolja és átalakítja a lemezeket az erőforráscsoportok és az Azure-régiók között.
* Az [Azure KUBE CLI-bővítménye](https://github.com/yaron2/azure-kube-cli) az ACS-Kubernetes köteteket sorolja fel, és egy AK-fürtbe telepíti őket.

#### <a name="azure-files"></a>Azure Files

A lemezekkel ellentétben Azure Files egyidejűleg több gazdagéphez is csatlakoztatható. Az AK-fürtben az Azure és a Kubernetes nem akadályozza meg, hogy olyan Pod-t hozzon létre, amelyet az ACS-fürt továbbra is használ. Az adatvesztés és a nem várt viselkedés elkerülése érdekében győződjön meg arról, hogy a fürtök nem írnak egyszerre ugyanarra a fájlra.

Ha az alkalmazás több replikát is képes tárolni, amelyek ugyanarra a megosztásra mutatnak, kövesse az állapot nélküli áttelepítési lépéseket, és telepítse a YAML-definíciókat az új fürtre. Ha nem, az egyik lehetséges áttelepítési módszer a következő lépéseket foglalja magában:

1. Az alkalmazás üzembe helyezése az AK-ban 0 értékű replika-számmal.
2. Az alkalmazás méretezése az ACS-ben 0-ra. (Ehhez a lépéshez leállás szükséges.)
3. Az alkalmazás méretezése az AK-ra legfeljebb 1.
4. Érvényesít.
5. Mutasson forgalmat az AK-fürtre.

Ha üres megosztást szeretne használni, és másolatot készít a forrásadatok másolatáról, akkor a [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) parancsokkal áttelepítheti az adatait.

### <a name="deployment-strategy"></a>Központi telepítési stratégia

Javasoljuk, hogy a meglévő CI/CD-folyamat használatával helyezzen üzembe egy jól ismert konfigurációt az AK-ban. A meglévő üzembe helyezési feladatok klónozásával ellenőrizze, hogy `kubeconfig` mutat-e az új AK-fürtre.

Ha ez nem lehetséges, exportálja az erőforrás-definíciókat az ACS-ből, majd alkalmazza őket az AK-ra. Az objektumok exportálásához `kubectl` is használhatja.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Számos nyílt forráskódú eszköz segíthet a telepítési igényektől függően:

* [Velero](https://github.com/heptio/ark) (ehhez az eszközhöz Kubernetes 1,7 szükséges.)
* [Azure Kube CLI-bővítmény](https://github.com/yaron2/azure-kube-cli)
* [Újraeltolás](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>A migrálás lépései

1. [Hozzon létre egy AK-fürtöt](https://docs.microsoft.com/azure/aks/create-cluster) az Azure Portal, az Azure CLI vagy a Azure Resource Manager sablonnal.

   > [!NOTE]
   > A GitHubon található [Azure/AK-](https://github.com/Azure/AKS/tree/master/examples/vnet) tárházban talál minta Azure Resource Manager sablonokat.

2. Végezze el a szükséges módosításokat a YAML-definíciókban. Például cserélje le a `apps/v1beta1`t a `Deployments``apps/v1`.

3. [Kötetek migrálása](#migrating-persistent-volumes) (nem kötelező) az ACS-fürtről az AK-fürtbe.

4. A CI/CD rendszer használatával alkalmazásokat telepíthet az AK-ba. Vagy használja a kubectl-t a YAML-definíciók alkalmazásához.

5. Érvényesít. Győződjön meg arról, hogy az alkalmazások a várt módon működnek, és hogy az áttelepített összes adat át lett másolva.

6. A forgalom átirányítása. Frissítse a DNS-t arra, hogy az ügyfelek az AK-beli üzemelő példányra

7. Az áttelepítés utáni feladatok befejezése. Ha áttelepítette a köteteket, és úgy döntött, hogy nem fokozatos leválasztása az írásokat, másolja az adatot az új fürtre.
