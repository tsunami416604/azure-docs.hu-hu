---
title: EmptyDir-kötet csatlakoztatása Azure Container Instances
description: Megtudhatja, hogyan csatlakoztathat egy emptyDir-kötetet a tárolók közötti adatmegosztáshoz Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 0dbe26ff1e00e1912cfd63e8383695ca794dd037
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325465"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>EmptyDir-kötet csatlakoztatása Azure Container Instances

Megtudhatja, hogyan csatlakoztathat egy *emptyDir* -kötetet a tárolók közötti adatmegosztáshoz Azure Container Instancesban.

> [!NOTE]
> A *emptyDir* -kötet csatlakoztatása jelenleg csak Linux-tárolók számára engedélyezett. Miközben dolgozunk a Windows-tárolók összes funkciójának bekapcsolásán, az [áttekintésben](container-instances-overview.md#linux-and-windows-containers)megtalálhatja az aktuális platformmal kapcsolatos különbségeket.

## <a name="emptydir-volume"></a>emptyDir-kötet

A *emptyDir* kötet egy írható könyvtárat biztosít a tárolók egyes tárolói számára. A csoportban található tárolók elolvashatják és leírják a köteten található fájlokat, és az egyes tárolók azonos vagy eltérő elérési útjaival is csatlakoztathatók.

Néhány példa a *emptyDir* -kötetek használatára:

* Üres terület
* Ellenőrzőpont a hosszan futó feladatok során
* Az oldalkocsis tároló által lekért és az alkalmazás-tároló által kiszolgált adattároló

A *emptyDir* -kötetben lévő adatmennyiséget a tároló összeomlik. Az újraindított tárolók azonban nem garantáltak, hogy az *emptyDir* -köteten tárolt adatmennyiséget megőrzik.

## <a name="mount-an-emptydir-volume"></a>EmptyDir-kötet csatlakoztatása

Ha emptyDir-kötetet szeretne csatlakoztatni egy tároló-példányban, [Azure Resource Manager sablonnal](/azure/templates/microsoft.containerinstance/containergroups)kell telepítenie.

Először töltse `volumes` fel a tömböt a sablon tároló csoport `properties` szakaszában. Ezután minden olyan tárolóhoz, amelyben a *emptyDir* -kötetet csatlakoztatni szeretné, töltse `volumeMounts` ki a tömböt `properties` a tároló definíciójának szakaszában.

A következő Resource Manager-sablon például egy két tárolóból álló tároló csoportot hoz létre, amelyek mindegyike a *emptyDir* -kötetet csatlakoztatja:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Ha egy Azure Resource Manager sablonnal szeretné megtekinteni a tároló példány központi telepítésének példáját, tekintse meg a többtárolós [csoportok telepítése Azure Container Instancesban](container-instances-multi-container-group.md)című témakört.

## <a name="next-steps"></a>További lépések

További mennyiségi típusok csatlakoztatása a Azure Container Instancesban:

* [Azure-tároló példányát az Azure fájlmegosztások csatlakoztatása](container-instances-volume-azure-files.md)
* [Azure-tároló példányát gitRepo kötet csatlakoztatása](container-instances-volume-gitrepo.md)
* [Titkos kötet csatlakoztatása Azure Container Instances](container-instances-volume-secret.md)