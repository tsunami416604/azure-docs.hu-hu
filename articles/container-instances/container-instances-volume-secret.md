---
title: Azure-tároló példányát titkos kötet csatlakoztatása
description: 'Útmutató: a hozzáférés a bizalmas adatokat tárol a tároló példányok titkos kötet csatlakoztatása'
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: e20dc3b37bc766b2a8a352eb62c03f5e2ee14c52
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Azure-tároló példányát titkos kötet csatlakoztatása

Megtudhatja, hogyan csatlakoztatása egy *titkos* kötet a tároló példányát, a tárolás és a tárolók a tárolócsoportok által a bizalmas adatok lekérését.

> [!NOTE]
> Csatlakoztatása egy *titkos* kötet korlátozódik jelenleg Linux tárolók. Arra törekszünk, hogy idővel az összes funkció elérhető legyen a Windows-tárolókon is. Az egyes platformok közötti aktuális eltérésekről a [Azure Container Instances-kvóták és -régiók rendelkezésre állása](container-instances-quotas.md) részben tájékozódhat.

## <a name="secret-volume"></a>titkos kötet

Használhatja a *titkos* kötet ahhoz, hogy a tároló csoport bizalmas információk megadására. A *titkos* kötet megadott titkos kulcsai tárol a köteten, amely ezután hozzáférhetnek a tárolók a tároló csoportjában található fájl. A titkos kulcsok használatával egy *titkos* kötet, elkerülheti a bizalmas adatokat, például az SSH-kulcsok vagy az adatbázis hitelesítő adatait az alkalmazás kódjában elhelyezéséhez.

Minden *titkos* által támogatott kötetek [tmpfs][tmpfs], RAM biztonsági fájlrendszer; nem felejtő soha nem kerülnek azok tartalmát.

## <a name="mount-a-secret-volume"></a>Titkos kötet csatlakoztatása

Csatlakoztassa a *titkos* kötet tároló példányt, ha már telepítette használatával egy [Azure Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups).

Először feltöltése a `volumes` a tároló csoport tömb `properties` a sablon szakasza. Minden egyes tároló, amelyben szeretné csatlakoztatni a tárolócsoport a következő a *titkos* kötet, feltöltése a `volumeMounts` tömb a a `properties` szakasz tároló-definícióban.

Például a következő Resource Manager-sablon csoportot hoz létre tárolót álló egyetlen tárolót. A tároló csatlakoztatások egy *titkos* kötet két Base64-kódolású titkos kulcsok.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Az Azure Resource Manager sablonnal tároló példány szemléltet, olvassa el [központi telepítése az Azure-tároló esetekben több tárolócsoportok](container-instances-multi-container-group.md).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan csatlakoztatása egyéb Azure tároló példányok kötet típusa:

* [Azure-tároló példányát az Azure fájlmegosztások csatlakoztatása](container-instances-volume-azure-files.md)
* [Az Azure-tároló példányát emptyDir kötet csatlakoztatása](container-instances-volume-emptydir.md)
* [Azure-tároló példányát gitRepo kötet csatlakoztatása](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs