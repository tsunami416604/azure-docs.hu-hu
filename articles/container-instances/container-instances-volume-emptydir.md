---
title: EmptyDir kötet csatlakoztatása tárolócsoportba
description: Megtudhatja, hogyan csatlakoztathat egy emptyDir kötetet az Azure Container Instances tárolócsoportban lévő tárolók közötti adatok megosztásához
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117740"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>EmptyDir kötet csatlakoztatása az Azure Container-példányokban

Megtudhatja, hogyan csatlakoztathat egy *emptyDir* kötetet az Azure Container Instances tárolócsoportban lévő tárolók közötti adatok megosztásához. A tárolóba helyezett számítási feladatok hoz hatja *üreskönyvtári* kötetek ként használja az üres köteteket.

> [!NOTE]
> Az *emptyDir* kötet csatlakoztatása jelenleg Linux-tárolókra korlátozódik. Miközben azon dolgozunk, hogy az összes funkciót a Windows tárolók, megtalálja a jelenlegi platform különbségek az [áttekintést](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>emptyDir kötet

Az *emptyDir* kötet egy írható könyvtárat biztosít, amely a tárolócsoport minden tárolója számára elérhető. A csoportban lévő tárolók ugyanazokat a fájlokat olvashatják és írhatják a köteten, és az egyes tárolókban lévő azonos vagy különböző elérési utak használatával csatlakoztathatók.

Néhány példa *emptyDir* kötethez használatos:

* Szabad hely
* Ellenőrzőpontok hosszú ideig futó feladatok során
* Oldalkocsis tároló által beolvasott és alkalmazástároló által kiszolgált adatok tárolása

Az *üresen* megadott köteten lévő adatok a tároló összeomlásai során megmaradnak. Az újraindított tárolók azonban nem garantált, hogy egy *emptyDir* köteten is megmaradnak. Ha leállít egy tárolócsoportot, az *emptyDir* kötet nem marad meg.

A Linux *emptyDir* kötet maximális mérete 50 GB.

## <a name="mount-an-emptydir-volume"></a>EmptyDir kötet csatlakoztatása

Egy üresKönyvtári kötet csatlakoztatása egy tárolópéldányban, telepítheti egy [Azure Resource Manager sablon](/azure/templates/microsoft.containerinstance/containergroups), egy [YAML-fájl,](container-instances-reference-yaml.md)vagy más programozott módszerek egy tárolócsoport üzembe helyezéséhez.

Először a fájl `volumes` tárolócsoport-szakaszában `properties` népesítse be a tömböt. Ezután a tárolócsoport minden olyan tárolójához, amelyben csatlakoztatni szeretné az *emptyDir* kötetet, a tárolódefiníció szakaszában feltölti a `volumeMounts` `properties` tömböt.

A következő Erőforrás-kezelő sablon például létrehoz egy tárolócsoportot, amely két tárolóból áll, amelyek mindegyike az *üresDir* kötetet csatlakoztatja:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

A tárolócsoportok telepítésére példa: [Többtárolós csoport telepítése Erőforrás-kezelő sablonhasználatával](container-instances-multi-container-group.md) és [Többtárolós csoport telepítése YAML-fájlhasználatával.](container-instances-multi-container-yaml.md)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan csatlakoztathat más kötettípusokat az Azure Container-példányokban:

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](container-instances-volume-azure-files.md)
* [GitRepo-kötet csatlakoztatása az Azure Container-példányokban](container-instances-volume-gitrepo.md)
* [Titkos kötet csatlakoztatása az Azure Container-példányokban](container-instances-volume-secret.md)