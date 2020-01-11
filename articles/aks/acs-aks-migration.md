---
title: Migrálás az Azure Kubernetes szolgáltatásba (ak)
description: Migrálás az Azure Kubernetes szolgáltatásba (ak).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/07/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 68c50d3455c0ed240fa62532818a0b07b39ec772
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889525"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrálás az Azure Kubernetes szolgáltatásba (ak)

Ez a cikk segítséget nyújt az Azure Kubernetes szolgáltatásba (ak) való sikeres áttelepítés megtervezéséhez és végrehajtásához. A legfontosabb döntések elvégzéséhez ez az útmutató részletesen ismerteti az AK jelenleg javasolt konfigurációját. Ez a cikk nem fedi le az összes forgatókönyvet, és ha szükséges, a cikk a sikeres áttelepítés megtervezésével kapcsolatos részletesebb információkra mutató hivatkozásokat tartalmaz.

Ez a dokumentum a következő forgatókönyvek támogatásához használható:

* [Rendelkezésre állási készletek](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) által támogatott AK-fürt áttelepítése [Virtual Machine Scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* AK-fürt áttelepítése [szabványos SKU Load Balancer](https://docs.microsoft.com/azure/aks/load-balancer-standard) használatára
* Migrálás [Azure Container Serviceról (ACS) – 2020 és AK között, január 31-ig](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)
* Migrálás az [AK-ból motorról](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) AK-ra
* Áttelepítés nem Azure-alapú Kubernetes-fürtökről AK-ra

Az áttelepítés során győződjön meg arról, hogy a cél Kubernetes verziója az AK-hoz támogatott ablakon belül van. Ha régebbi verziót használ, előfordulhat, hogy nem a támogatott tartományon belül van, és az AK által támogatott verziófrissítési verzióra van szükség. További információért lásd: [AK támogatott Kubernetes-verziók](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) .

Ha a Kubernetes egy újabb verziójára végez áttelepítést, tekintse át a [Kubernetes verziójának és verziójának döntési támogatási szabályzatát](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Számos nyílt forráskódú eszköz segíthet az áttelepítés során a forgatókönyvtől függően:

* [Velero](https://velero.io/) (Kubernetes 1.7 +) szükséges
* [Azure Kube CLI-bővítmény](https://github.com/yaron2/azure-kube-cli)
* [Újraeltolás](https://github.com/mhausenblas/reshifter)

Ebben a cikkben a következő áttelepítési adatokat összegzi:

> [!div class="checklist"]
> * AK standard Load Balancer és Virtual Machine Scale Sets
> * Meglévő csatolt Azure-szolgáltatások
> * Érvényes kvóták biztosítása
> * Magas rendelkezésre állás és Üzletmenet-folytonosság
> * Állapot nélküli alkalmazások szempontjai
> * A állapotalapú-alkalmazásokkal kapcsolatos megfontolások
> * A fürt konfigurációjának üzembe helyezése

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AK standard Load Balancer és Virtual Machine Scale Sets

Az AK egy felügyelt szolgáltatás, amely az alacsonyabb felügyeleti terhelést biztosító egyedi képességeket kínál. A felügyelt szolgáltatásnak köszönhetően olyan [régiókból](https://docs.microsoft.com/azure/aks/quotas-skus-regions) kell kiválasztania, amelyek az AK által támogatottak. A meglévő fürtről az AK-ra való áttérés szükségessé teheti a meglévő alkalmazások módosítását, hogy azok egészségesek maradjanak az AK felügyelt vezérlési síkon.

Javasoljuk, hogy [Virtual Machine Scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets) és az [Azure standard Load BALANCER](https://docs.microsoft.com/azure/aks/load-balancer-standard) által támogatott AK-fürtöket használjon, így biztosítva a [több csomópontos készlet](https://docs.microsoft.com/azure/aks/use-multiple-node-pools), a [Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview), az [engedélyezett IP-címtartományok](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), a [fürt autoskálázása](https://docs.microsoft.com/azure/aks/cluster-autoscaler), [a Azure Policy for AK](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)és más új funkciók használatát.   

A [virtuális gépek rendelkezésre állási csoportjai](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) által támogatott AK-fürtök számos ilyen funkció esetében nem támogatottak.

A következő példa létrehoz egy AK-fürtöt egyetlen, virtuálisgép-méretezési csoporttal támogatott egycsomópontos készlettel. Standard Load balancert használ. Emellett lehetővé teszi a fürthöz tartozó automéretezőt a fürt csomópont-készletén, és legalább *1* és legfeljebb *3* csomópontot állít be:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Meglévő csatolt Azure-szolgáltatások

Előfordulhat, hogy a fürtök áttelepítésekor külső Azure-szolgáltatásokat csatlakoztatott. Ezek nem igénylik az erőforrás-használatot, de az előzőtől az új fürtökig frissíteniük kell a működés fenntartásához szükséges kapcsolatokat.

* Azure Container Registry
* Log Analytics
* Application Insights
* Forgalomkezelő
* Tárfiók
* Külső adatbázisok

## <a name="ensure-valid-quotas"></a>Érvényes kvóták biztosítása

Mivel az áttelepítés során további virtuális gépek lesznek telepítve az előfizetésben, ellenőriznie kell, hogy a kvóták és a korlátozások elegendőek-e az adott erőforrásokhoz. Előfordulhat, hogy a [vCPU-kvóta](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)növelését kell kérnie.

Előfordulhat, hogy a [hálózati kvóták](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) növelését kell megadnia, hogy ne legyenek kimerítve az IP-címek. További információért lásd: [hálózati és IP-címtartományok](https://docs.microsoft.com/azure/aks/configure-kubenet) .

További információ: Azure- [előfizetés és-szolgáltatási korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Az aktuális kvóták megtekintéséhez a Azure Portal lépjen az [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)panelre, válassza ki az előfizetését, majd válassza a **használat + kvóták**lehetőséget.

## <a name="high-availability-and-business-continuity"></a>Magas rendelkezésre állás és Üzletmenet-folytonosság

Ha az alkalmazás nem tudja kezelni az állásidőt, a magas rendelkezésre állású áttelepítési forgatókönyvekhez ajánlott eljárásokat kell követnie.  Az összetett üzletmenet folytonosságának megtervezése, a vész-helyreállítás és a hasznos üzemidő maximalizálása a jelen dokumentum hatókörén kívül esik.  További információ az [üzletmenet folytonosságával és a vész-helyreállítással kapcsolatos ajánlott eljárásokról az Azure Kubernetes szolgáltatásban (ak)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) .

Összetett alkalmazások esetében általában az idő múlásával, nem pedig egyszerre kell áttérnie. Ez azt jelenti, hogy a régi és az új környezetnek kommunikálnia kell a hálózaton keresztül. Előfordulhat, hogy a korábban `ClusterIP` szolgáltatásokkal való kommunikációra használt alkalmazásokat `LoadBalancer` kell tenni, és a megfelelő védelemmel kell rendelkeznie.

Az áttelepítés befejezéséhez az ügyfeleket az AK-on futó új szolgáltatásokra kell irányítani. Javasoljuk, hogy a forgalom átirányításához frissítse a DNS-t úgy, hogy az AK-fürt előtt található Load Balancer mutasson.

Az [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) az ügyfeleket a kívánt Kubernetes-fürtre és alkalmazás-példányra irányíthatja.  Traffic Manager egy DNS-alapú forgalom terheléselosztó, amely a régiók közötti hálózati forgalmat terjesztheti.  A legjobb teljesítmény és redundancia érdekében a Traffic Manageron keresztül irányítsa az összes alkalmazás forgalmát, mielőtt az AK-fürtre kerül.  Többfürtes telepítés esetén az ügyfeleknek csatlakozniuk kell egy Traffic Manager DNS-névhez, amely az egyes AK-fürtök szolgáltatásaira mutat. Adja meg ezeket a szolgáltatásokat Traffic Manager végpontok használatával. Mindegyik végpont a *szolgáltatás terheléselosztó IP-címe*. Ezt a konfigurációt használva irányíthatja a hálózati forgalmat az egyik régióban lévő Traffic Manager végpontról a végpontra egy másik régióban.

![AK Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Az [Azure bejárati ajtó szolgáltatás](https://docs.microsoft.com/azure/frontdoor/front-door-overview) egy másik lehetőség az AK-fürtök forgalmának útválasztására.  Az Azure Front Door Service segítségével meghatározhatja, felügyelheti és monitorozhatja a webes forgalmának globális forgalomirányítását, optimalizálhatja annak teljesítményét, és azonnali globális feladatátvétellel magas rendelkezésre állást biztosíthat. 

### <a name="considerations-for-stateless-applications"></a>Állapot nélküli alkalmazások szempontjai 

Az állapot nélküli alkalmazások migrálása a legegyszerűbb eset. Alkalmazza az erőforrás-definíciókat (YAML vagy Helm) az új fürtre, győződjön meg róla, hogy minden a várt módon működik-e, és irányítsa át a forgalmat az új fürt aktiválásához.

### <a name="considers-for-stateful-applications"></a>Az állapot-nyilvántartó alkalmazások megítélése

Gondosan tervezze meg az állapot-nyilvántartó alkalmazások áttelepítését, hogy elkerülje az adatvesztést vagy a váratlan állásidőt.

Ha Azure Files használ, a fájlmegosztást kötetként csatlakoztathatja az új fürthöz:
* [Statikus Azure Files csatlakoztatása kötetként](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Ha az Azure Managed Disks-t használja, csak akkor csatlakoztathatja a lemezt, ha az nincs csatlakoztatva a virtuális géphez:
* [Statikus Azure-lemez csatlakoztatása kötetként](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Ha egyik módszer sem működik, akkor biztonsági mentési és visszaállítási lehetőségeket is használhat:
* [Velero az Azure-ban](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure Files

A lemezekkel ellentétben Azure Files egyidejűleg több gazdagéphez is csatlakoztatható. Az AK-fürtben az Azure és a Kubernetes nem akadályozza meg, hogy olyan Pod-t hozzon létre, amelyet az ACS-fürt továbbra is használ. Az adatvesztés és a nem várt viselkedés elkerülése érdekében győződjön meg arról, hogy a fürtök nem írnak egyszerre ugyanarra a fájlra.

Ha az alkalmazás több replikát is képes tárolni, amelyek ugyanarra a megosztásra mutatnak, kövesse az állapot nélküli áttelepítési lépéseket, és telepítse a YAML-definíciókat az új fürtre. Ha nem, az egyik lehetséges áttelepítési módszer a következő lépéseket foglalja magában:

* Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
* Mutasson az élő adatforgalomra az új AK-fürtre.
* Válassza le a régi fürtöt.

Ha üres megosztást szeretne használni, és másolatot készít a forrásadatok másolatáról, akkor a [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) parancsokkal áttelepítheti az adatait.


#### <a name="migrating-persistent-volumes"></a>Állandó kötetek áttelepítése

Ha a meglévő állandó köteteket AK-ra telepíti át, akkor általában az alábbi lépéseket kell követnie:

* A fokozatos leválasztása az alkalmazásba ír. (Ez a lépés nem kötelező, és leállást igényel.)
* Pillanatképek készítése a lemezekről.
* Hozzon létre új felügyelt lemezeket a pillanatképek közül.
* Állandó kötetek létrehozása az AK-ban.
* A pod-specifikációk frissítése a [meglévő kötetek használatára](https://docs.microsoft.com/azure/aks/azure-disk-volume) a PersistentVolumeClaims helyett (statikus kiépítés).
* Az alkalmazás üzembe helyezése az AK-ban.
* Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
* Mutasson az élő adatforgalomra az új AK-fürtre.

> [!IMPORTANT]
> Ha úgy dönt, hogy nem fokozatos leválasztása az írásokat, replikálnia kell az új központi telepítésre. Ellenkező esetben kihagyhatja a lemez-Pillanatképek elkészítése után írt adatfájlokat.

Egyes nyílt forráskódú eszközök segítségével felügyelt lemezeket hozhat létre és telepíthet át köteteket a Kubernetes-fürtök között:

* Az [Azure CLI lemez másolási bővítménye](https://github.com/noelbundick/azure-cli-disk-copy-extension) lemásolja és átalakítja a lemezeket az erőforráscsoportok és az Azure-régiók között.
* Az [Azure KUBE CLI-bővítménye](https://github.com/yaron2/azure-kube-cli) az ACS-Kubernetes köteteket sorolja fel, és egy AK-fürtbe telepíti őket.


### <a name="deployment-of-your-cluster-configuration"></a>A fürt konfigurációjának üzembe helyezése

Javasoljuk, hogy a meglévő folyamatos integrációs (CI) és a folyamatos kézbesítés (CD) folyamat használatával helyezzen üzembe egy ismert, jó konfigurációt az AK-ban. Az Azure-folyamatokkal alkalmazásokat hozhat [létre és helyezhet üzembe az AK](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops) -klónok számára a meglévő üzembe helyezési feladatokhoz, és gondoskodhat arról, hogy `kubeconfig` az új AK-fürtre mutasson.

Ha ez nem lehetséges, exportálja az erőforrás-definíciókat a meglévő Kubernetes-fürtből, majd alkalmazza őket az AK-ra. Az objektumok exportálásához `kubectl` is használhatja.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Ebben a cikkben a következő áttelepítési adatokat összesítettük:

> [!div class="checklist"]
> * AK standard Load Balancer és Virtual Machine Scale Sets
> * Meglévő csatolt Azure-szolgáltatások
> * Érvényes kvóták biztosítása
> * Magas rendelkezésre állás és Üzletmenet-folytonosság
> * Állapot nélküli alkalmazások szempontjai
> * A állapotalapú-alkalmazásokkal kapcsolatos megfontolások
> * A fürt konfigurációjának üzembe helyezése
