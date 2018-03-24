---
title: Az Azure tároló példányok tárolócsoportok
description: Tárolócsoportok működése az Azure-tároló példányok ismertetése
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 3b1eeebacb55ffc7af4e2014f26dd9d5643f5478
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Azure-tároló példányát tárolócsoportok

A legfelső szintű erőforrás Azure tároló példányát a *a tárolócsoport*. A cikkből megtudhatja, Mik azok a tárolócsoportok és lehetővé teszik az forgatókönyvek.

## <a name="how-a-container-group-works"></a>Egy tároló csoport működése

A tárolócsoport, ugyanazon gazdagép ütemezhetők tárolókat gyűjteménye. A tárolók egy tárolócsoport megosztani egy életciklussal, helyi hálózati és tárolási köteteket. Az elve hasonló a *pod* a [Kubernetes] [ kubernetes-pod] és [DC/OS][dcos-pod].

Az alábbi ábrán látható egy példa a tároló-csoportot, amely több tároló tartalmazza:

![Tároló csoportok diagramja][container-groups-example]

Ez a példa tárolócsoport:

* Van ütemezve, egy önálló gazdagépen.
* A DNS-névcímke hozzá van rendelve.
* Elérhetővé teszi a egyetlen nyilvános IP-cím, egy kitett port.
* Két tárolók áll. Egy tároló 80-as porton, a további figyeli közben 5000-es porton figyel.
* Kettő Azure fájlmegosztás használja, a kötet csatlakoztatások, és minden tárolót csatlakoztatja egy helyileg a megosztásokat.

> [!NOTE]
> Több tárolócsoportok jelenleg csak Linux tárolók. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="deployment"></a>Környezet

Tároló *csoportok* a minimális erőforrás-elosztás 1 vCPU, és 1 GB memóriával rendelkeznek. Egyes *tárolók* kisebb, mint 1 vCPU, és 1 GB memória azokhoz csoport olyan tárolóban. Egy tároló csoporton belüli erőforrások terjesztési testre szabható több tároló szintjén a tároló által létrehozott keretein belül. Például két tárolók minden 0,5 vCPU 1 vCPU által lefoglalt tárolót csoportban található.

## <a name="networking"></a>Hálózat

Tárolócsoportok ossza meg IP-cím és port névtér az IP-címet. Ahhoz, hogy a külső ügyfelek számára a csoporton belül tárolója, fel kell fednie a portot, az IP-cím és a tárolóból. Mert a csoport tárolókra port névtér port hozzárendelése nem támogatott. Tárolók egy csoporton belüli képes elérni egymást localhost ki van téve, portokon keresztül akkor is, ha azokat a portokat nem érhetők el kívülről a csoporthoz tartozó IP-címe.

## <a name="storage"></a>Tárolás

Megadhatja, hogy külső kötetek csatlakoztatni a tároló csoporton belül. A megadott elérési utak belül az egyes tárolók egy csoport leképezheti a köteteket.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Több tároló csoportok olyan hasznos olyan esetekben, ahol egyetlen működési feladat felosztani tároló képek kis számú szeretné. Ezek a lemezképek különböző csapatok által majd kézbesítése, és külön erőforrás követelményekkel rendelkezik.

Példa használati lehetnek:

* Egy alkalmazás-tárolót és a naplózási tároló. A naplózási tároló gyűjti a naplókat, valamint a metrikák kimeneti a fő alkalmazás, és hosszú távú tárolás írja őket.
* Egy alkalmazás-tárolót és figyelési tárolója. A figyelési tároló rendszeres időközönként egy kérést küld annak érdekében, hogy fut, valamint helytelenül válaszol, és riasztást küld, ha nem az alkalmazás.
* Egy tároló kiszolgáló webalkalmazás és húzza a legújabb tartalom a verziókövetésből tárolója.

## <a name="next-steps"></a>További lépések

Útmutató: a több tároló tároló csoport az Azure Resource Manager-sablon telepítése:

> [!div class="nextstepaction"]
> [A tároló csoport telepítése](container-instances-multi-container-group.md)

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
