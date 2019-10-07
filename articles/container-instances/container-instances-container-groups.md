---
title: Azure Container Instances tároló csoportok
description: A többtárolós csoportok működésének megismerése Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b17004e7821bcac61ca98afdbeaf87644da2a441
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68326058"
---
# <a name="container-groups-in-azure-container-instances"></a>Tároló-csoportok a Azure Container Instances

Azure Container Instances a legfelső szintű erőforrás a *tároló csoport*. Ez a cikk ismerteti, hogy milyen tároló-csoportokat és milyen típusú forgatókönyveket engedélyeznek.

## <a name="how-a-container-group-works"></a>A Container Group működése

A Container Group olyan tárolók gyűjteménye, amelyek ugyanazon a gazdagépen vannak ütemezve. A tárolók csoportjainak tárolói életciklussal, erőforrásokkal, helyi hálózattal és tárolási kötetekkel rendelkeznek. Ez a fogalom hasonló a [Kubernetes][kubernetes-pod]található *Pod* -hoz.

Az alábbi ábrán egy példa látható egy több tárolót tartalmazó tároló csoportra:

![Container groups diagram][container-groups-example]

Ez a példa tároló Csoport:

* Egy gazdagépen van ütemezve.
* A DNS-név címkéje hozzá van rendelve.
* Egyetlen nyilvános IP-cím közzététele egy elérhető porttal.
* Két tárolóból áll. Az egyik tároló figyeli az 80-es portot, míg a másik a 5000-es porton figyel.
* A két Azure-fájlmegosztást tartalmaz kötet-csatlakoztatásként, és mindegyik tároló helyileg csatlakoztatja az egyik megosztást.

> [!NOTE]
> A több tárolóból álló csoportok jelenleg csak a Linux-tárolókat támogatják. A Windows-tárolók esetében Azure Container Instances csak egyetlen példány üzembe helyezését támogatja. Miközben dolgozunk a Windows-tárolók összes funkciójának bekapcsolásán, megkeresheti a platform aktuális [](container-instances-overview.md#linux-and-windows-containers)eltéréseit a szolgáltatás áttekintésében.

## <a name="deployment"></a>Környezet

A többtárolós csoportok üzembe helyezésének két gyakori módja van: egy [Resource Manager-sablon][resource-manager template] vagy egy [YAML-fájl][yaml-file]használata. A Resource Manager-sablonok használata akkor ajánlott, ha további Azure-szolgáltatási erőforrásokat (például [Azure Files megosztást][azure-files]) kell üzembe helyeznie a tároló példányainak telepítésekor. A YAML formátumának tömörebb jellege miatt a YAML-fájlok használata akkor ajánlott, ha a központi telepítés csak tároló példányokat tartalmaz.

A tároló csoport konfigurációjának megőrzése érdekében exportálhatja a konfigurációt egy YAML-fájlba az Azure CLI-parancs az [Container export][az-container-export]paranccsal. Az Exportálás lehetővé teszi, hogy a "konfiguráció as code" verzióban tárolja a tároló csoport konfigurációit. Vagy használja kiindulási pontként az exportált fájlt, amikor új konfigurációt fejleszt a YAML-ben.

## <a name="resource-allocation"></a>Erőforrás-kiosztás

A Azure Container Instances erőforrásokat, például CPU-t, memóriát és opcionálisan [GPU][gpus] -t (előzetes verzió) foglal le egy tároló-csoportba a csoportban lévő példányok [erőforrás-kérelmeinek][resource-requests] hozzáadásával. Ha például két példánnyal rendelkező tároló csoportot hoz létre a CPU-erőforrások számára, a tároló csoport 2 CPU-t foglal le.

A tárolók csoportjai számára elérhető maximális erőforrások az üzembe helyezéshez használt [Azure][region-availability] -régiótól függenek.

### <a name="container-resource-requests-and-limits"></a>Tároló-erőforrásokra vonatkozó kérelmek és korlátozások

* Alapértelmezés szerint a csoport tároló példányai megosztják a csoport kért erőforrásait. Az 1 CPU-t kérő két példánnyal rendelkező csoportban a csoport teljes hozzáféréssel rendelkezik 2 processzorhoz. Az egyes példányok akár a 2 processzort is használhatják, a példányok pedig a CPU-erőforrással versenyeznek.

* Egy csoportban lévő példány erőforrás-felhasználásának korlátozásához opcionálisan beállíthat egy [erőforrás][resource-limits] -korlátot a példányhoz. Az 1 CPU-t kérő két példánnyal rendelkező csoportban az egyik tárolónak több processzort kell futtatnia, mint a másik.

  Ebben a forgatókönyvben egy 0,5 CPU-korlátot állíthat be egy példányhoz, és legfeljebb 2 processzort használhat a másodikhoz. Ez a konfiguráció az első tároló erőforrás-felhasználását az 0,5 CPU-ra korlátozza, így a második tároló a teljes 2 CPU-t használhatja, ha van ilyen.

További információ: [ResourceRequirements][resource-requirements] tulajdonság a Container groups REST API.

### <a name="minimum-and-maximum-allocation"></a>Minimális és maximális kiosztás

* **Legalább** 1 processzor és 1 GB memória lefoglalása egy tároló csoportba. Egy csoporton belüli egyes tároló-példányok kevesebb mint 1 PROCESSZORral és 1 GB memóriával is üzembe helyezhetők. 

* A tárolói csoportok **maximális** erőforrásaival kapcsolatban tekintse meg az [Erőforrás rendelkezésre állását][region-availability] Azure Container instances a telepítési régióban.

## <a name="networking"></a>Hálózat

A Container groups egy IP-címet és egy port névteret oszt meg az adott IP-címen. Ahhoz, hogy a külső ügyfelek elérjék a csoporton belüli tárolókat, ki kell jelölnie a portot az IP-címen és a tárolóból. Mivel a csoportba tartozó tárolók a portok névterét használják, a port megfeleltetése nem támogatott. A csoportokba tartozó tárolók a localhost-on keresztül érhetik el egymástól az általuk kitett portokon, még akkor is, ha ezek a portok nem a csoport IP-címén kívül vannak kitéve.

A tároló-csoportok üzembe helyezése egy Azure-beli [virtuális hálózatban][virtual-network] (előzetes verzió), amely lehetővé teszi, hogy a tárolók biztonságosan kommunikáljanak a virtuális hálózat más erőforrásaival.

## <a name="storage"></a>Storage

Külső köteteket is megadhat a tároló csoportba való csatlakoztatáshoz. Ezeket a köteteket meghatározott elérési utakra is leképezheti egy csoport egyes tárolói között.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A többtárolós csoportok olyan esetekben hasznosak, amikor egyetlen funkcionális feladatot szeretne megosztani kis számú tároló-lemezképbe. Ezeket a lemezképeket ezután különböző csapatok továbbítják, és külön erőforrás-követelményekkel rendelkezhetnek.

A használati példa a következőket tartalmazhatja:

* Egy webalkalmazást és egy tárolót kiszolgáló tároló a legújabb tartalmat húzza a forrás vezérlőelemből.
* Egy alkalmazás-tároló és egy naplózási tároló. A naplózási tároló gyűjti a naplók és a metrikák kimenetét a fő alkalmazásból, és a hosszú távú tárolásra írja azokat.
* Egy alkalmazás-tároló és egy figyelési tároló. A megfigyelési tároló rendszeres időközönként egy kérést küld az alkalmazásnak, hogy ellenőrizze, hogy fut-e, és hogy megfelelően válaszol-e, és riasztást küld, ha nem.
* Egy előtér-tároló és egy háttér-tároló. Az előtér egy webalkalmazást is kiszolgálhat, és a szolgáltatás futtatásával lekérheti az adatgyűjtést. 

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan helyezhet üzembe egy több tárolóból álló tároló csoportot Azure Resource Manager sablonnal:

> [!div class="nextstepaction"]
> [Tároló-csoport üzembe helyezése][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
