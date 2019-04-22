---
title: Az Azure Container Instances-tároló csoportok
description: Megismerheti, hogyan többtárolós csoportok munkahelyi az Azure Container Instances szolgáltatásban
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f4bbea8acd447a731cf5c56f9876baf9183735ea
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784993"
---
# <a name="container-groups-in-azure-container-instances"></a>Tárolócsoportok az Azure Container Instances szolgáltatásban

A legfelső szintű erőforrás az Azure Container Instances szolgáltatásban az *tárolócsoport*. Ez a cikk ismerteti a tárolócsoportok olyan és a használatukkal forgatókönyvek.

## <a name="how-a-container-group-works"></a>A tárolócsoport működése

A tárolócsoport gyűjteménye, tárolók, amelyek ugyanazon a gazdagépen ütemezhetők. A tárolók egy tárolócsoport megosztani egy életciklussal, erőforrások, helyi hálózati és tárolási köteteket. Az elve hasonló a *pod* a [Kubernetes][kubernetes-pod].

Az alábbi ábrán látható egy példa egy tárolócsoport, amely több tárolót tartalmaz:

![Tároló csoportok diagramja][container-groups-example]

Ebben a példában tárolócsoport:

* Egy gazdagépen van ütemezve.
* A DNS-névcímke hozzá van rendelve.
* Tesz elérhetővé egyetlen nyilvános IP-címet, és a egy elérhetővé tett port.
* Két tárolót áll. Egy tároló a 80-as porton, miközben a többi figyeli az 5000-es porton figyel.
* Tartalmaz két Azure-fájlmegosztások kötet csatlakoztatása, és a tárolók csatlakoztatja egy helyileg a megosztásokat.

> [!NOTE]
> Többtárolós csoportok jelenleg csak a Linux-tárolók támogatja. Windows-tárolókhoz az Azure Container Instances csak egyetlen példány üzembe helyezési támogatja. Amíg folyamatban van a Windows-tárolók idővel az összes funkció, a szolgáltatás jelenlegi platform különbségek található [áttekintése](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Környezet

Többtárolós csoport üzembe helyezése két gyakori módszerrel: használja a [Resource Manager-sablon] [ resource-manager template] vagy egy [YAML-fájl][yaml-file]. Resource Manager-sablonnal ajánlott, amikor üzembe kell helyeznie a további Azure-szolgáltatási erőforrások (például egy [megosztása az Azure Files][azure-files]) telepítésekor meg kell adnia a container Instances szolgáltatásban. Miatt a YAML formátumú tömörebb jellegű egy YAML-fájlt ajánlott, amikor a telepítés csak a container Instances szolgáltatásban is tartalmaz.

Meg szeretné őrizni egy tárolócsoport konfigurálása, exportálhatja a konfigurációt egy YAML-fájlt az Azure CLI-paranccsal [az tároló exportálási][az-container-export]. Exportálás lehetővé teszi, hogy a tároló csoport konfigurációk tárolása verziókövetés "konfiguráció kódként." Másik lehetőségként használhatja kiindulási pontként az exportált fájlt, egy új konfigurációt a YAML fejlesztése során.

## <a name="resource-allocation"></a>Erőforrás-elosztás

Az Azure Container Instances lefoglalja az erőforrásokat, például a processzorok, memória, és igény szerint [gpu-k] [ gpus] (előzetes verzió) egy tárolócsoporthoz hozzáadásával a [erőforrás-kérelmek] [ resource-requests] a csoport-példánya. Például Processzor-erőforrások véve, ha egy tárolócsoportot hoz létre két példánnyal, minden egyes kérő 1 Processzor, akkor a tárolócsoport 2 processzorra van lefoglalva.

A tárolócsoport elérhető maximális erőforrások függenek a [az Azure-régióban] [ region-availability] az üzembe helyezéshez használt.

### <a name="container-resource-requests-and-limits"></a>Tároló-erőforrás-kérelmek és korlátozások

* Alapértelmezés szerint a csoportban található tárolópéldányok ossza meg a kért erőforrások a csoport. A két csoport példányai minden kérést küldő 1 Processzor, 2 processzorra hozzáféréssel rendelkezik a teljes csoport. Minden példány lehet használni őket a 2 processzorra és a futás közben versenyezzen Processzor-erőforrás a példányok is.

* Erőforrás-használat korlátozásához által egy csoportban lévő példány igény szerint hozzáadhat egy [erőforráskorlátot] [ resource-limits] -példány. Egy csoport két példánnyal kérő 1 Processzor, a tárolók egyik előfordulhat, hogy több processzort igényelnek, mint a többi.

  Ebben a forgatókönyvben egy erőforrás felső korlát pedig 0,5 beállíthat egy példány és a egy legfeljebb 2 processzorra, a második Processzor. Ez a konfiguráció korlátozza az első tároló erőforrás-használat 0,5 CPU-használat, lehetővé téve a második tárolót használni őket a teljes 2 processzorra ha rendelkezésre áll.

További információkért lásd: a [ResourceRequirements] [ resource-requirements] tulajdonság a tárolóban lévő csoportok REST API-t.

### <a name="minimum-and-maximum-allocation"></a>Minimális és maximális felosztás

* Foglaljon le egy **minimális** 1 Processzor és a egy tárolócsoport memória 1 GB. Csoporton belül az egyes tárolópéldányok üzembe lehet helyezni 1-nél kisebb CPU és memória 1 GB. 

* Az a **maximális** erőforrásokat egy tárolócsoport, tekintse meg a [Erőforrás rendelkezésre állási] [aci-régió – rendelkezésre állási] az Azure Container Instances üzembe helyezés a régióban.

## <a name="networking"></a>Hálózat

Tárolócsoportok ossza meg IP-cím és port névtér az IP-címet. Ahhoz, hogy a külső ügyfelek egy tárolót a csoporton belüli elérni, fel kell fednie a port, az IP-cím és a tárolóból. A csoporton belüli tárolók osztoznak egy port névteret, mert a port hozzárendelése nem támogatott. Csoporton belüli tárolók elérheti egymással localhost, amely ki van téve, portokon keresztül akkor is, ha ezeket a portokat nem érhetőek el kívülről a csoporthoz tartozó IP-címen.

Igény szerint központilag telepítik a tárolócsoportok be egy [az Azure virtual network] [ virtual-network] (előzetes verzió), hogy a virtuális hálózat más erőforrások biztonságosan kommunikálnak a tárolókban.

## <a name="storage"></a>Storage

Megadhatja, hogy külső kötetek tároló csoporton belül. Egyedi elérési utak belül az egyes tárolókat egy csoport, leképezheti ezeket a köteteket.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Többtárolós csoportok olyan hasznos olyan esetekben, ahol a egy működési feladat felosztása tárolórendszerképek kis számú szeretné. Ezek a lemezképek majd továbbítható a különböző csapatok és külön erőforrás-követelményekkel rendelkeznek.

Példa használati lehetnek:

* Egy tároló, egy webalkalmazás szolgáltató és a egy tárolót, a legújabb tartalom a forráskezelőből lekérése.
* Egy alkalmazás-tárolót és a egy naplózási tároló. A naplózási tároló összegyűjti a naplókat és mérőszámokat kimeneti a fő alkalmazás, és hosszú távú tárolás írja őket.
* Egy alkalmazás-tárolót és a egy figyelési tárolót. A figyelési tároló rendszeres időközönként kérést küld annak érdekében, hogy fut, és megfelelően reagál, és riasztást küld, ha nincs az alkalmazást.
* Egy előtér-tároló és a egy háttér-tárolót. Az előtér egy webalkalmazás, jelenleg a háttérrendszer használ szolgáltatások futtatásának adatok lekéréséhez. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan helyezhet üzembe Azure Resource Manager-sablon egy többtárolós tárolócsoportot:

> [!div class="nextstepaction"]
> [Egy tároló-csoport központi telepítése][resource-manager template]

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
