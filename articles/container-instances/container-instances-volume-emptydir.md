---
title: "Az Azure-tároló példányát emptyDir kötet csatlakoztatása"
description: "Útmutató: a tárolók egy Azure-tároló példányát a tárolócsoport közötti adatmegosztásra egy emptyDir kötet csatlakoztatása"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 01b44f357d160a48852ee843cd8bebd138e95dfc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Az Azure-tároló példányát emptyDir kötet csatlakoztatása

Megtudhatja, hogyan csatlakoztatása egy *emptyDir* kötet a tárolókat az Azure-tároló példányát egy tárolócsoport közötti adatmegosztásra.

> [!NOTE]
> Csatlakoztatása egy *emptyDir* kötet korlátozódik jelenleg Linux tárolók. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="emptydir-volume"></a>emptyDir kötet

A *emptyDir* kötet biztosít egy írható directory érhető el a tárolócsoport minden tárolóját. A csoport a tárolók is fájlok olvasása és írása az ugyanazon a köteten, és az ugyanazon vagy másik elérési az egyes tárolókban használatával csatlakoztatható.

Néhány példa használ egy *emptyDir* kötet:

* Ideiglenes terület
* Ellenőrzőpontok használata során hosszan futó feladatokat
* Egy oldalkocsi tároló által lekért és egy alkalmazás tároló által kiszolgált adatok tárolásához

Az adatok egy *emptyDir* kötet tároló összeomlások keresztül is megőrződjenek. Tárolókat újraindul, azonban nem garantált, hogy az adatok megőrzéséhez egy *emptyDir* kötet.

## <a name="mount-an-emptydir-volume"></a>Egy emptyDir kötet csatlakoztatása

Egy tároló példányt emptyDir kötet csatlakoztatása, ha már telepítette használatával egy [Azure Resource Manager sablon](/azure/templates/microsoft.containerinstance/containergroups).

Először feltöltése a `volumes` a tároló csoport tömb `properties` a sablon szakasza. Minden egyes tároló, amelyben szeretné csatlakoztatni a tárolócsoport a következő a *emptyDir* kötet, feltölti a `volumeMounts` tömb a a `properties` szakasz tároló-definícióban.

Például a következő Resource Manager-sablon két tárolók álló tároló csoportot hoz létre minden egyes mely csatlakoztatástípus a *emptyDir* kötet:

[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Az Azure Resource Manager sablonnal tároló példány szemléltet, olvassa el [központi telepítése az Azure-tároló esetekben több tárolócsoportok](container-instances-multi-container-group.md).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan csatlakoztatása egyéb Azure tároló példányok kötet típusa:

* [Azure-tároló példányát az Azure fájlmegosztások csatlakoztatása](container-instances-volume-azure-files.md)
* [Azure-tároló példányát gitRepo kötet csatlakoztatása](container-instances-volume-gitrepo.md)
* [Azure-tároló példányát titkos kötet csatlakoztatása](container-instances-volume-secret.md)