---
title: Az Azure Container Instances szolgáltatásban az emptyDir kötet csatlakoztatási
description: Ismerje meg, hogyan csatlakoztathatja az emptyDir köteten az adatmegosztást a tárolókat az Azure Container Instances szolgáltatásban egy tárolócsoport
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 98a72123a05fa7d8dc16be7ddb787f2a2cf7e4d1
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857652"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Az Azure Container Instances szolgáltatásban az emptyDir kötet csatlakoztatási

Ismerje meg, hogyan csatlakoztathat egy *emptyDir* kötet egy tárolócsoportban, az Azure Container Instances a tárolók közötti.

> [!NOTE]
> Csatlakoztatási egy *emptyDir* kötet Linux-tárolók jelenleg korlátozva. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="emptydir-volume"></a>az emptyDir kötet

A *emptyDir* kötet elérhető-e az egyes tárolók egy tárolócsoport írható könyvtár biztosít. A csoportban lévő tárolókat olvashat és írhat ugyanazokat a fájlokat a kötet, és az egyes tárolókban az ugyanazon vagy különböző elérési utak segítségével csatlakoztathatók.

Néhány példa a egy *emptyDir* kötet:

* Ideiglenes terület
* Ellenőrzőpontok használata során hosszú ideig futó feladatok
* Egy oldalkocsi tároló által lekért és a egy alkalmazástárolót által kiszolgált data Store

Az adatok egy *emptyDir* kötet rendszer megőrzi a tároló szoftverleállások keresztül. Tárolók, amelyek újra lesz indítva, azonban nem garantált, hogy az adatok megőrzése egy *emptyDir* kötet.

## <a name="mount-an-emptydir-volume"></a>Az emptyDir kötet csatlakoztatási

A tárolópéldány az emptyDir kötet csatlakoztatása, telepítenie kell használatával egy [Azure Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups).

Első lépésként töltse fel a `volumes` tömb tárolócsoportban `properties` szakaszában a sablont. Minden tároló, amelyben szeretné csatlakoztatni a tárolócsoport esetén tovább a *emptyDir* kötet, töltse fel a `volumeMounts` Pole a `properties` a tároló-definíció szakasza.

Például a következő Resource Manager-sablont hoz létre egy tárolócsoportot, két tárolót, amely minden egyes melyik csatlakoztatása a *emptyDir* kötet:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json --> [!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Tárolópéldány üzembe helyezésének Azure Resource Manager-sablonnal egy példát, olvassa el [többtárolós csoportok az Azure Container Instancesben üzembe helyezése](container-instances-multi-container-group.md).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan csatlakoztathat egyéb kötettípusok az Azure Container Instances szolgáltatásban:

* [Azure-tároló példányát az Azure fájlmegosztások csatlakoztatása](container-instances-volume-azure-files.md)
* [Azure-tároló példányát gitRepo kötet csatlakoztatása](container-instances-volume-gitrepo.md)
* [Az Azure Container Instances szolgáltatásban titkos kötet csatlakoztatása](container-instances-volume-secret.md)