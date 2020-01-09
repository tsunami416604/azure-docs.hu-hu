---
title: EmptyDir-kötet csatlakoztatása a tároló csoportjához
description: Megtudhatja, hogyan csatlakoztathat egy emptyDir-kötetet a tárolók közötti adatmegosztáshoz Azure Container Instances
ms.topic: article
ms.date: 02/08/2018
ms.openlocfilehash: 955423b685ebb3979271c7c2dc7e835a16100c2b
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552457"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>EmptyDir-kötet csatlakoztatása Azure Container Instances

Megtudhatja, hogyan csatlakoztathat egy *emptyDir* -kötetet a tárolók közötti adatmegosztáshoz Azure Container Instancesban. A *emptyDir* -kötetek ideiglenes gyorsítótárként használhatók a tároló munkaterhelésekhez.

> [!NOTE]
> A *emptyDir* -kötet csatlakoztatása jelenleg csak Linux-tárolók számára engedélyezett. Miközben dolgozunk a Windows-tárolók összes funkciójának bekapcsolásán, az [áttekintésben](container-instances-overview.md#linux-and-windows-containers)megtalálhatja az aktuális platformmal kapcsolatos különbségeket.

## <a name="emptydir-volume"></a>emptyDir-kötet

A *emptyDir* kötet egy írható könyvtárat biztosít a tárolók egyes tárolói számára. A csoportban található tárolók elolvashatják és leírják a köteten található fájlokat, és az egyes tárolók azonos vagy eltérő elérési útjaival is csatlakoztathatók.

Néhány példa a *emptyDir* -kötetek használatára:

* Üres terület
* Ellenőrzőpont a hosszan futó feladatok során
* Az oldalkocsis tároló által lekért és az alkalmazás-tároló által kiszolgált adattároló

A *emptyDir* -kötetben lévő adatmennyiséget a tároló összeomlik. Az újraindított tárolók azonban nem garantáltak, hogy az *emptyDir* -köteten tárolt adatmennyiséget megőrzik. Ha leállítja a tároló csoportot, a *emptyDir* kötet nem marad meg.

## <a name="mount-an-emptydir-volume"></a>EmptyDir-kötet csatlakoztatása

Ha emptyDir-kötetet szeretne csatlakoztatni egy tároló-példányban, [Azure Resource Manager sablonnal](/azure/templates/microsoft.containerinstance/containergroups)kell telepítenie.

Először töltse ki a `volumes` tömböt a sablon `properties` szakaszának tároló csoportjában. Ezután minden olyan tárolóhoz, amelyben a *emptyDir* -kötetet csatlakoztatni szeretné, töltse ki a `volumeMounts` tömböt a tároló definíciójának `properties` szakaszában.

A következő Resource Manager-sablon például egy két tárolóból álló tároló csoportot hoz létre, amelyek mindegyike a *emptyDir* -kötetet csatlakoztatja:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Ha egy Azure Resource Manager sablonnal szeretné megtekinteni a tároló példány központi telepítésének példáját, tekintse meg a [többtárolós csoportok telepítése Azure Container Instancesban](container-instances-multi-container-group.md)című témakört.

## <a name="next-steps"></a>Következő lépések

További mennyiségi típusok csatlakoztatása a Azure Container Instancesban:

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](container-instances-volume-azure-files.md)
* [Gitrepo típusú-kötet csatlakoztatása Azure Container Instances](container-instances-volume-gitrepo.md)
* [Titkos kötet csatlakoztatása Azure Container Instances](container-instances-volume-secret.md)