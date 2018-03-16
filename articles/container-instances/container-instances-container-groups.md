---
title: "Azure-tárolót példányok tárolócsoportok"
description: "Tárolócsoportok működése az Azure-tároló példányok ismertetése"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/19/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 7e8a5014ce9168ba3d67d175935649bfd9fec511
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Azure-tároló példányát tárolócsoportok

A legfelső szintű erőforrás Azure tároló példányát a *a tárolócsoport*. A cikkből megtudhatja, Mik azok a tárolócsoportok és lehetővé teszik az forgatókönyvek.

## <a name="how-a-container-group-works"></a>Egy tároló csoport működése

A tárolócsoport, ugyanazon gazdagép ütemezhetők tárolókat gyűjteménye. A tárolók egy tárolócsoport megosztani egy életciklussal, helyi hálózati és tárolási köteteket. Hasonló, amelynek a *pod* a [Kubernetes] [ kubernetes-pod] és [DC/OS][dcos-pod].

Az alábbi ábrán látható egy példa a tároló-csoportot, amely több tároló tartalmazza.

![Tároló csoportok diagramja][container-groups-example]

Ez a példa tárolócsoport:

* Van ütemezve, egy önálló gazdagépen.
* Elérhetővé teszi a egyetlen nyilvános IP-cím, egy kitett port.
* Két tárolók áll. Egy tároló 80-as porton, a további figyeli közben 5000-es porton figyel.
* Kettő Azure fájlmegosztás használja, a kötet csatlakoztatások, és minden tárolót csatlakoztatja egy helyileg a megosztásokat.

> [!NOTE]
> Több tárolócsoportok jelenleg csak Linux tárolók. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

### <a name="deployment"></a>Környezet

**Tárolócsoportok** a minimális erőforrás-elosztás 1 vCPU, és 1 GB memóriával rendelkeznek. Egyes **tárolók** létesíthetők kisebb, mint 1 vCPU, és 1 GB memóriát használ. Egy tároló csoporton belüli erőforrások terjesztési létrejött a tároló szintjén keretein belül több tároló testre szabható. Például két tárolók minden egyes tároló csoporton belül található 0,5 vCPU lefoglalt 1 vCPU.

### <a name="networking"></a>Hálózat

Tárolócsoportok ossza meg IP-cím és port névtér az IP-címet. Ahhoz, hogy a külső ügyfelek számára a csoporton belül tárolója, fel kell fednie a portot, az IP-cím és a tárolóból. Mert a csoport tárolókra port névtér port hozzárendelése nem támogatott. Tárolók egy csoporton belüli képes elérni egymást localhost ki van téve, portokon keresztül akkor is, ha azokat a portokat nem érhetők el kívülről a csoporthoz tartozó IP-címe.

### <a name="storage"></a>Tárolás

Megadhatja, hogy külső kötetek csatlakoztatni a tároló csoporton belül. A megadott elérési utak belül az egyes tárolók egy csoport leképezheti a köteteket.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Több tároló csoportok olyan hasznos olyan esetekben, ahol kis számú tároló képek, amely különböző csapatok által kézbesítése, és külön erőforrás követelményei egyetlen működési feladat feloszthatja szeretné.

Példa használati lehetnek:

* Egy alkalmazás-tárolót és a naplózási tároló. A naplózási tároló gyűjti a naplókat, valamint a metrikák kimeneti a fő alkalmazás, és hosszú távú tárolás írja őket.
* Az alkalmazások és a figyelési tároló. A figyelési tároló rendszeres időközönként egy kérést küld annak érdekében, hogy fut, valamint helytelenül válaszol, és riasztást küld, ha nem az alkalmazás.
* Egy tároló kiszolgáló webalkalmazás és húzza a legújabb tartalom a verziókövetésből tárolója.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [a több tároló csoport telepítése](container-instances-multi-container-group.md) az Azure Resource Manager sablonnal.

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
