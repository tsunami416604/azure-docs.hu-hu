---
title: Áttelepítés az Azure Kubernetes szolgáltatásra (AKS)
description: Áttelepíti az Azure Kubernetes szolgáltatás (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624812"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Áttelepítés az Azure Kubernetes szolgáltatásra (AKS)

Ez a cikk segít megtervezni és végrehajtani az Azure Kubernetes-szolgáltatásba (AKS) történő sikeres áttelepítést. A legfontosabb döntések meghozatalának segítése érdekében ez az útmutató az AKS aktuális ajánlott konfigurációjának részleteit tartalmazza. Ez a cikk nem terjed ki minden forgatókönyvre, és adott esetben a cikk a sikeres áttelepítés megtervezéséhez részletesebb információkra mutató hivatkozásokat tartalmaz.

Ez a dokumentum a következő esetek támogatására használható:

* [Rendelkezésre állási készletek](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) által támogatott AKS-fürt áttelepítése [virtuálisgép-méretezési csoportokba](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* AKS-fürt áttelepítése [szabványos termékváltozat-terheléselosztó használatára](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migrálás [az Azure Container Service -ból (ACS) – 2020.](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)
* Áttelepítés [AKS-motorról](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) AKS-re
* Nem Azure-alapú Kubernetes-fürtökről AKS-re való áttérés

Áttelepítésekor győződjön meg arról, hogy a cél Kubernetes-verzió az AKS támogatott ablakában található. Ha régebbi verziót használ, előfordulhat, hogy nem esik a támogatott tartományba, és az AKS által támogatott frissítési verziókat kell támogatnia. További információkért lásd [az AKS által támogatott Kubernetes-verziókat.](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)

Ha a Kubernetes újabb verziójára való áttéréskor tekintse át a [Kubernetes verzióját és a verziódöntés támogatási házirendjét.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

A forgatókönyvtől függően számos nyílt forráskódú eszköz segíthet az áttelepítésben:

* [Velero](https://velero.io/) (Kubernetes 1.7+-t igényel)
* [Az Azure Kube CLI bővítmény](https://github.com/yaron2/azure-kube-cli)
* [Újraváltó](https://github.com/mhausenblas/reshifter)

Ebben a cikkben összefoglaljuk az áttelepítés részleteit:

> [!div class="checklist"]
> * AKS standard terheléselosztóval és virtuálisgép-méretezési készletekkel
> * Meglévő csatolt Azure-szolgáltatások
> * Érvényes kvóták biztosítása
> * Magas rendelkezésre állás és üzletmenet-folytonosság
> * Az állapotnélküli alkalmazásokra vonatkozó szempontok
> * Az állapotalapú alkalmazásokkal kapcsolatos szempontok
> * A fürtkonfiguráció telepítése

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS standard terheléselosztóval és virtuálisgép-méretezési készletekkel

Az AKS egy felügyelt szolgáltatás, amely egyedi képességeket kínál alacsonyabb felügyeleti terheléssel. A felügyelt szolgáltatás eredményeképpen ki kell választania az AKS által támogatott [régiók](https://docs.microsoft.com/azure/aks/quotas-skus-regions) közül. A meglévő fürtről az AKS-re való áttérés szükségessé teheti a meglévő alkalmazások módosítását, hogy azok kifogástalanállapotban maradjanak az AKS felügyelt vezérlősíkján.

Javasoljuk, hogy a [virtuálisgép-méretezési készletek](https://docs.microsoft.com/azure/virtual-machine-scale-sets) és az Azure standard terheléselosztó által támogatott AKS-fürtök és az [Azure standard terheléselosztó](https://docs.microsoft.com/azure/aks/load-balancer-standard) használatával biztosítsa, hogy olyan funkciókat kapjon, mint például [több csomópontkészlet,](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) [rendelkezésre állási zónák,](https://docs.microsoft.com/azure/availability-zones/az-overview) [engedélyezett IP-tartományok,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges) [fürt automatikus skálázó,](https://docs.microsoft.com/azure/aks/cluster-autoscaler) [Azure-szabályzat a KS-hez](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)és más új funkciók, ahogy azok felszabadulnak.

AKS-fürtök által támogatott [virtuális gép rendelkezésre állási készletek](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) nem támogatja a sok ilyen funkciók.

A következő példa létrehoz egy AKS-fürt egy csomópont készlet egy virtuális gép méretezési csoport. Szabványos terheléselosztót használ. Emellett engedélyezi a fürt automatikus skálázóját a fürt csomópontkészletén, és legalább *1* és legfeljebb *3* csomópontot állít be:

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

Fürtök áttelepítésekor előfordulhat, hogy külső Azure-szolgáltatások csatlakoztatása. Ezek nem igényelnek erőforrás-kikapcsolódást, de a működés fenntartása érdekében frissíteni kell a korábbi és az új fürtökből származó kapcsolatokat.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Tárfiók
* Külső adatbázisok

## <a name="ensure-valid-quotas"></a>Érvényes kvóták biztosítása

Mivel az áttelepítés során további virtuális gépek lesznek telepítve az előfizetésben, ellenőrizze, hogy a kvóták és a korlátok elegendőek-e ezekhez az erőforrásokhoz. Előfordulhat, hogy a [vCPU-kvóta](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)növelését kell kérnie.

Előfordulhat, hogy növelnie kell a [hálózati kvótákat](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) annak érdekében, hogy ne merítse ki az IP-ket. További információt az [AKS hálózati és IP-tartományai](https://docs.microsoft.com/azure/aks/configure-kubenet) ban talál.

További információ: [Azure-előfizetési és szolgáltatási korlátok.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) Az aktuális kvóták ellenőrzéséhez az Azure Portalon nyissa meg az [előfizetések panelt,](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)válassza ki az előfizetést, és válassza a **Használat + kvóták**lehetőséget.

## <a name="high-availability-and-business-continuity"></a>Magas rendelkezésre állás és üzletmenet-folytonosság

Ha az alkalmazás nem tudja kezelni az állásidőt, a magas rendelkezésre állású áttelepítési forgatókönyvek ajánlott eljárásokat kell követnie.  Az összetett üzletmenet-folytonossági tervezés, a vészhelyreállítás és az állásidő maximalizálása ajánlott eljárások túlmutatnak a jelen dokumentum hatókörén.  További információ [az üzletmenet folytonosságával és a vészhelyreállítással kapcsolatos gyakorlati tanácsokról az Azure Kubernetes-szolgáltatásban (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) további információért.

Összetett alkalmazások esetén általában idővel, nem pedig egyszerre fog áttérni. Ez azt jelenti, hogy a régi és az új környezetben lehet, hogy kommunikálni a hálózaton keresztül. Előfordulhat, hogy `ClusterIP` a korábban a kommunikációhoz `LoadBalancer` használt alkalmazásokat típusként kell elérhetővé tenni, és megfelelően kell biztosítani őket.

Az áttelepítés befejezéséhez az ügyfeleket az AKS-en futó új szolgáltatásokra kell mutatnia. Azt javasoljuk, hogy irányítsa át a forgalmat a DNS frissítésével, hogy az AKS-fürt előtt lévő terheléselosztóra mutasson.

[Az Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) a kívánt Kubernetes-fürthöz és alkalmazáspéldányhoz irányíthatja az ügyfeleket.  A Traffic Manager egy DNS-alapú terheléselosztó, amely képes elosztani a hálózati forgalmat a régiók között.  A legjobb teljesítmény és redundancia érdekében irányítsa az összes alkalmazásforgalmat a Traffic Manageren keresztül, mielőtt az AKS-fürthöz jutna.  Többfürtös telepítés esetén az ügyfeleknek csatlakozniuk kell egy Traffic Manager DNS-névhez, amely az egyes AKS-fürtök szolgáltatásaira mutat. Ezeket a szolgáltatásokat a Traffic Manager-végpontok használatával határozhatja meg. Minden végpont a *szolgáltatás terheléselosztó IP.* Ezzel a konfigurációval irányíthatja a hálózati forgalmat a Traffic Manager-végpontegy régióban a végpont egy másik régióban.

![AKS a Traffic Managerrel](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Az Azure Bejárati ajtó szolgáltatás](https://docs.microsoft.com/azure/frontdoor/front-door-overview) egy másik lehetőség az AKS-fürtök forgalmának útválasztására.  Az Azure Front Door Service segítségével meghatározhatja, felügyelheti és monitorozhatja a webes forgalmának globális forgalomirányítását, optimalizálhatja annak teljesítményét, és azonnali globális feladatátvétellel magas rendelkezésre állást biztosíthat. 

### <a name="considerations-for-stateless-applications"></a>Az állapotnélküli alkalmazásokra vonatkozó szempontok

Az állapotnélküli alkalmazásáttelepítés a legegyszerűbb eset. Alkalmazza az erőforrás-definíciókat (YAML vagy Helm) az új fürtre, győződjön meg arról, hogy minden a várt módon működik, és irányítsa át a forgalmat az új fürt aktiválásához.

### <a name="considerations-for-stateful-applications"></a>Az állapotalapú alkalmazásokkal kapcsolatos szempontok

Gondosan tervezze meg az állapotalapú alkalmazások áttelepítését az adatvesztés vagy a váratlan leállás elkerülése érdekében.

Ha Azure Files-t használ, a fájlmegosztást kötetként csatlakoztathatja az új fürthöz:
* [Statikus Azure-fájlok csatlakoztatása kötetként](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Ha Azure felügyelt lemezeket használ, csak akkor csatlakoztathatja a lemezt, ha nincs csatlakoztatva valamelyik virtuális géphez:
* [Statikus Azure-lemez csatlakoztatása kötetként](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Ha egyik módszer sem működik, biztonsági mentési és visszaállítási beállításokat használhat:
* [Velero az Azure-ban](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure Files

A lemezekkel ellentétben az Azure Files egyidejűleg több állomáshoz is csatlakoztatható. Az AKS-fürtben az Azure és a Kubernetes nem akadályozza meg, hogy hozzon létre egy pod, amely az ACS-fürt továbbra is használja. Az adatvesztés és a váratlan viselkedés elkerülése érdekében győződjön meg arról, hogy a fürtök nem írnak egyszerre ugyanabba a fájlba.

Ha az alkalmazás több replikát is üzemeltethet, amelyek ugyanarra a fájlmegosztásra mutatnak, kövesse az állapotnélküli áttelepítési lépéseket, és telepítse a YAML-definíciókat az új fürtbe. Ha nem, az egyik lehetséges áttelepítési megközelítés a következő lépéseket foglalja magában:

* Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
* Irányítsa az élő forgalmat az új AKS-fürtre.
* Válassza le a régi fürtöt.

Ha üres megosztással szeretne kezdeni, és másolatot szeretne készíteni a [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) forrásadatokról, a parancsokkal áttelepítheti az adatokat.


#### <a name="migrating-persistent-volumes"></a>Állandó kötetek áttelepítése

Ha meglévő állandó köteteket telepít át az AKS-be, általában az alábbi lépéseket kell végrehajtania:

* Quiesce ír az alkalmazásba. (Ez a lépés nem kötelező, és állásidőt igényel.)
* Pillanatképek készítése a lemezekről.
* Hozzon létre új felügyelt lemezeket a pillanatképekből.
* Állandó kötetek létrehozása az AKS-ben.
* Frissítse a pod specifikációit a [meglévő kötetek használatára](https://docs.microsoft.com/azure/aks/azure-disk-volume) persistentVolumeClaims (statikus kiépítés) helyett.
* Telepítse az alkalmazást az AKS-be.
* Ellenőrizze, hogy az alkalmazás megfelelően működik-e.
* Irányítsa az élő forgalmat az új AKS-fürtre.

> [!IMPORTANT]
> Ha úgy dönt, hogy nem dönt az írások megszüntetéséről, adatokat kell replikálnia az új központi telepítésbe. Ellenkező esetben hiányozni fog nak az adatok, amelyeket a lemez pillanatképeinek elkészülte után írt.

Néhány nyílt forráskódú eszköz segítségével felügyelt lemezeket hozhat létre, és köteteket telepíthet át a Kubernetes-fürtök között:

* [Az Azure CLI Disk Copy bővítmény](https://github.com/noelbundick/azure-cli-disk-copy-extension) átmásolja és konvertálja a lemezeket az erőforráscsoportok és az Azure-régiók között.
* [Az Azure Kube CLI-bővítmény](https://github.com/yaron2/azure-kube-cli) számba veszi az ACS Kubernetes-köteteket, és áttelepíti őket egy AKS-fürtbe.


### <a name="deployment-of-your-cluster-configuration"></a>A fürtkonfiguráció telepítése

Azt javasoljuk, hogy a meglévő folyamatos integráció (CI) és a folyamatos kézbesítés (CD) folyamat használatával telepítsen egy ismert-jó konfigurációt az AKS-re. Az Azure Pipelines segítségével [hozhat létre és helyezhet üzembe alkalmazásokat az AKS-re.](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops) Klónozza a meglévő telepítési `kubeconfig` feladatokat, és győződjön meg arról, hogy az új AKS-fürtre mutat.

Ha ez nem lehetséges, exportálja az erőforrás-definíciókat a meglévő Kubernetes-fürtből, majd alkalmazza őket az AKS-re. Objektumok exportálására is használható. `kubectl`

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Meglévő erőforrások áthelyezése másik régióba

Előfordulhat, hogy az AKS-fürtöt egy [másik, az AKS által támogatott régióba][region-availability]szeretné áthelyezni. Azt javasoljuk, hogy hozzon létre egy új fürtöt a másik régióban, majd telepítse az erőforrásokat és alkalmazásokat az új fürtbe. Emellett ha rendelkezik olyan szolgáltatásokkal, mint például az [Azure Dev Spaces][azure-dev-spaces] az AKS-fürtön futó, akkor is telepítenie kell és konfigurálnia kell ezeket a szolgáltatásokat a fürtön az új régióban.


Ebben a cikkben összefoglaltuk az áttelepítés részleteit:

> [!div class="checklist"]
> * AKS standard terheléselosztóval és virtuálisgép-méretezési készletekkel
> * Meglévő csatolt Azure-szolgáltatások
> * Érvényes kvóták biztosítása
> * Magas rendelkezésre állás és üzletmenet-folytonosság
> * Az állapotnélküli alkalmazásokra vonatkozó szempontok
> * Az állapotalapú alkalmazásokkal kapcsolatos szempontok
> * A fürtkonfiguráció telepítése


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/