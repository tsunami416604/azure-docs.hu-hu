---
title: Áttelepítés az Azure Container Service (ACS) az Azure Kubernetes Service (AKS)
description: Áttelepítés az Azure Container Service (ACS) az Azure Kubernetes Service (AKS)
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: e42b0e7bd1bce40b7c58d75cb07f5a3f8afa5836
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385041"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Áttelepítés az Azure Container Service (ACS) az Azure Kubernetes Service (AKS)

A jelen dokumentum célja, hogy megtervezése és végrehajtása sikeres migráláshoz kell az Azure Container Service Kubernetes-(ACS) és az Azure Kubernetes Service (AKS) között. Ez az útmutató részletesen ACS és az AKS közötti különbségeket, az áttelepítési folyamat áttekintést nyújt és segít fontos döntéseket.

## <a name="differences-between-acs-and-aks"></a>ACS és az AKS közötti különbségek

ACS és az AKS különböző néhány fő területe, amely hatással van a migrálás. Ekkor a felülvizsgálandó és tervezi, hogy az alábbi eltérések bármilyen migrálás előtt oldja meg.

* AKS-csomópontok használata [Managed Disks](../virtual-machines/windows/managed-disks-overview.md)
    * Nem felügyelt lemezeket kell konvertálni kell, mielőtt azok csatolható AKS-csomópontok
    * Egyéni `StorageClass` objektumokat az Azure-lemezek kell módosítható `unmanaged` , `managed`
    * Bármely `PersistentVolumes` kell használnia `kind: Managed`
* Az AKS jelenleg csak egy ügynökkészlet támogatja
* A Windows Server-alapú csomópontokon jelenleg [privát előzetes verzió](https://azure.microsoft.com/blog/kubernetes-on-azure/)
* Az AKS listáját [támogatott régiók](https://docs.microsoft.com/azure/aks/container-service-quotas)
* Az AKS felügyelt szolgáltatás, amely egy üzemeltetett Kubernetes-vezérlősík. Szükség lehet az alkalmazások módosítását, ha korábban már módosította a konfigurációt az ACS főkiszolgálók

### <a name="differences-between-kubernetes-versions"></a>Kubernetes-verzió közötti különbségek

Ha migráláshoz Kubernetes egy újabb verziója (például: a 1.9.x 1.7.x), van néhány változtatást, hogy a k8s API-t a figyelmet igénylő.

* [Egy ThirdPartyResource CustomResourceDefinition áttelepítése](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [API-számítási feladatok módosítások verziókban 1.8-as és 1.9](https://kubernetes.io/docs/reference/workloads-18-19/).

## <a name="migration-considerations"></a>Az áttelepítés szempontjai

### <a name="agent-pools"></a>Ügynökkészletek

Az AKS kezeli a Kubernetes vezérlősík, miközben továbbra is megadhat méretét és az új fürtbe felvenni kívánt csomópontok száma. Ha azt szeretné, hogy az aks-ben az ACS-ből egy 1:1 megfeleltetését, szeretné rögzíteni a meglévő ACS csomópont adatait. Ezeket az adatokat az új AKS-fürt létrehozásakor fogja használni.

Példa:

| Name (Név) | Darabszám | Virtuális gép mérete | Operációs rendszer |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Mivel további virtuális gépek lesznek üzembe helyezve az előfizetés az áttelepítés során, ellenőrizze, hogy a kvóták és korlátozások elegendőek ezekhez az erőforrásokhoz. További áttekintésével [Azure-előfizetés- és Szolgáltatáskorlátok](https://docs.microsoft.com/azure/azure-subscription-service-limits). Az aktuális kvótái ellenőrzéséhez nyissa meg a [előfizetések paneljén](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) az Azure Portalon válassza ki az előfizetését, majd válassza ki `Usage + quotas`.

### <a name="networking"></a>Hálózat

Összetett alkalmazások idővel helyett egyszerre általában kell áttelepíteni. Ez azt jelenti, hogy a régi és új környezetben kell előfordulhat, hogy a hálózaton keresztül kommunikálnak. Az alkalmazásokat, amelyek korábban tudtak használandó `ClusterIP` szolgáltatások való kommunikációhoz kell típus elérhetővé tehető `LoadBalancer` és a megfelelő biztonságáról gondoskodni.

Az áttelepítéshez, érdemes-ügyfelek irányítása az új szolgáltatások, az aks-en futó. Az ajánlott módszer a forgalom átirányítása van DNS-t, mutasson a terheléselosztóhoz, amely helyezkedik el az AKS-fürt frissítésével.

### <a name="stateless-applications"></a>Állapot nélküli alkalmazások

Állapot nélküli alkalmazások áttelepítése a legegyszerűbb eset áll fenn. Fogja a YAML-definíciók alkalmazza az új fürtre, ellenőrizze, hogy minden a várt módon működik és az új fürt aktívvá forgalom átirányítása.

### <a name="stateful-applications"></a>Állapotalapú alkalmazások

Áttelepítése állapotalapú alkalmazások is adatvesztés vagy váratlan állásidő elkerülése érdekében alapos tervezést igényel.

#### <a name="highly-available-applications"></a>Magas rendelkezésre állású alkalmazások

Egyes állapotalapú alkalmazások magas rendelkezésre állású konfigurációban telepíthető, és a replikák közötti adatokat másolja. Ha ez igaz a jelenlegi üzemelő példány, lehetséges, hozzon létre egy új tag az új AKS-fürtön, és csak minimális hatással van a alsóbb rétegbeli hívókhoz áttelepítésére lehet. Az áttelepítési lépéseket ebben a forgatókönyvben általában a következők:

1. Hozzon létre egy új másodlagos replikára az aks-en
2. Várjon, amíg az adatok replikálásához
3. Nem tud több mint másodlagos replikát az új elsődleges
4. Forgalom átirányítása az AKS-fürtöt

#### <a name="migrating-persistent-volumes"></a>Állandó kötetek áttelepítése

Nincsenek számos tényező figyelembe venni, ha a meglévő állandó kötetek migráláshoz az aks-ben. A folyamat lépései általában a következők:

1. (Nem kötelező) Fokozatos leválasztási műveletének ír az alkalmazás (állásidő szükséges)
2. Pillanatkép-lemezek
3. Hozzon létre új felügyelt lemezek pillanatképekből
4. Állandó kötetek létrehozására az aks-ben
5. Frissítse a Pod-specifikációk [a meglévő kötetek](https://docs.microsoft.com/azure/aks/azure-disk-volume) helyett PersistentVolumeClaims (statikus kiépítés)
6. Az aks üzembe helyezése
7. Érvényesítés
8. Forgalom átirányítása az AKS-fürtöt

> **Fontos**: Ha nem kíván fokozatos leválasztási műveletének írások, kell replikálja az adatokat az új központi telepítés, nem állnak majd rendelkezésre adatokat arról, hogy a lemez-pillanatképből óta

Nyílt forráskódú eszközök állnak fenn, amelyek segítségével hozzon létre felügyelt lemezek és kötetek Kubernetes-fürtök közötti áttelepítése.

* [noelbundick/azure-cli-lemez-bővítmény](https://github.com/noelbundick/azure-cli-disk-copy-extension) – másolás és a lemezek konvertálása erőforrás-csoportokat és az Azure-régiók között
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli) - számba venni az ACS Kubernetes-kötet, és telepítheti át őket egy AKS-fürt

#### <a name="azure-files"></a>Azure Files

Lemezek, ellentétben az Azure Files csatlakoztathatók a több gazdagépen. Azure ANI Kubernetes megakadályozza hoz létre az AKS-fürt továbbra is az ACS-fürt által használt Podot. Megakadályozzák az adatvesztést, és nem várt viselkedést, biztosítania kell, hogy mindkét fürt nem adatraktárba történő írás során ugyanazokat a fájlokat egy időben.

Az alkalmazás üzemeltethető mutat a ugyanabban fájlmegosztásba több replikát, ha az állapot nélküli áttelepítés lépésekkel, és üzembe helyezése a YAML-definíciók az új fürtön.

Ha nem, egy lehetséges migrálási megközelítés az alábbi lépésekből áll:

1. Az alkalmazás telepítése az AKS-replika számot 0
2. Az alkalmazás méretezése az ACS 0 (állásidő szükséges)
3. Az alkalmazás méretezése az aks-en legfeljebb 1
4. Érvényesítés
5. Forgalom átirányítása az AKS-fürtöt

Kezdje egy üres megosztáshoz, majd készítsen másolatot a forrásadatok hova esetekben használhatja a [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) parancsokat az adatok áttelepítéséhez.

### <a name="deployment-strategy"></a>Üzembe helyezési stratégiához

Az ajánlott módszer, hogy a meglévő CI/CD-folyamat az aks-ben a helyes konfiguráció üzembe helyezése. Fogja klónozni a meglévő telepítés feladatok, és ellenőrizze, hogy a `kubeconfig` az új AKS-fürtöt mutat.

Olyan esetekben, ahol a nem lehetséges kell az erőforrás-definíció exportálása az ACS-ből, és alkalmazhatja őket az aks-ben. Használhat `kubectl` objektumok exportálása.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Számos nyílt forráskódú eszközök, amelyek segítségével igényeitől függően is van:

* [heptio/ark](https://github.com/heptio/ark) -k8s igényel 1.7-es
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Migrálási lépések

### <a name="1-create-an-aks-cluster"></a>1. AKS-fürt létrehozása

Kövesse a dokumentációt annak [AKS-fürt létrehozása](https://docs.microsoft.com/azure/aks/create-cluster) az Azure Portalon, az Azure CLI vagy a Resource Manager-sablon használatával.

> Minta Azure Resource Manager-sablonok, az AKS megtalálja a [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) tárházban a Githubon

### <a name="2-modify-applications"></a>2. Alkalmazások módosítása

Hajtsa végre a YAML-definíciók bármely szükséges módosításokat. Példa: cseréje `apps/v1beta1` a `apps/v1` számára `Deployments`

### <a name="3-optional-migrate-volumes"></a>3. (Nem kötelező) Kötetek áttelepítése

Kötetek át az ACS-fürtben az AKS-fürt. További részletek találhatók a [állandó kötetek áttelepítése](#Migrating-Persistent-Volumes) szakaszban.

### <a name="4-deploy-applications"></a>4. Alkalmazások üzembe helyezése

A CI/CD-rendszer használatával telepíthet központilag alkalmazásokat AKS vagy a YAML meghatározásokat a kubectl használatával.

### <a name="5-validate"></a>5. Érvényesítés

Ellenőrizze, hogy az alkalmazások várt módon működnek-e, és, hogy az áttelepített adatokat a rendszer átmásolt keresztül.

### <a name="6-redirect-traffic"></a>6. Forgalom átirányítása

Frissítse a DNS-ügyfelek irányítása az AKS üzembe helyezés.

### <a name="7-post-migration-tasks"></a>7. Áttelepítés utáni feladatok

Ha át a köteteket, és úgy döntött, nem fokozatos leválasztási műveletének írások, szüksége lesz az adatokat másolni az új fürtre.
