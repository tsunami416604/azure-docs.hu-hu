---
title: Alkalmazás üzembe helyezése Service Fabric-háló által használt Azure Files kötet |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe Service Fabric háló az Azure CLI használatával az Azure Files kötet használó alkalmazások.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: fb9740efaa9a1033d6602180f5750f6fb550c048
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076103"
---
# <a name="deploy-a-service-fabric-mesh-application-that-uses-the-azure-files-volume"></a>Az Azure Files kötet használó Service Fabric-háló-alkalmazás üzembe helyezése
Ez a példa bemutatja egy tárolóban, az Azure Service Fabric-háló futtató tároló kötetek használatát. Ez a minta részeként:

- A fájlmegosztás létrehozása [Azure Files](/azure/storage/files/storage-files-introduction) 
- Tárolópéldány helyezünk üzembe egy kötet, megosztás hivatkozás
  - A tároló elindult, amikor azt csatlakoztatja, egy adott helyen belül a tároló megosztáson lévő
- A tárolón belül futó kódot egy szövegfájlba írja az adott helyhez
- Ellenőrizze, hogy a fájl megfelelően írták, amely a kötet a megosztásban

## <a name="example-json-templates"></a>Példa JSON-sablonok

Linux: [https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json](https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json)

Windows: [https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json](https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json)

## <a name="create-the-azure-files-file-share"></a>Az Azure Files-fájlmegosztás létrehozása

Kövesse az utasításokat a [dokumentáció az Azure Files](/azure/storage/files/storage-how-to-create-file-share) hozhat létre az alkalmazáshoz használandó fájlmegosztás.

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric háló parancssori felület beállítása
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

A lépések végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Ha helyi telepítése és használata a parancssori felület, telepítenie kell az Azure CLI verziója 2.0.35 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Telepíteni, vagy frissítse a CLI legújabb verzióját, lásd: [Azure CLI 2.0 telepítése] [azure-cli-telepítés].

Az Azure Service Fabric háló parancssori bővítmény modul telepítése. Az előzetes verzióra az Azure Service Fabric háló parancssori írása bővítményeként Azure parancssori felület.

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure-ba, és állítsa be az előfizetést.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot (RG) ebben a példában üzembe helyezéséhez, vagy használjon egy meglévő erőforráscsoportot, és ezt a lépést kihagyhatja. Az előzetes verzió érhető el, csak a `eastus` helyét.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése
Hozzon létre az alkalmazás és a kapcsolódó erőforrások az alábbi parancsok egyikével.

Linux esetén:

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json
```

Windows esetén:

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json
```

Kövesse az utasításokat követve adja meg a fájlmegosztás neve, a fiók nevét és a fiókkulcsot az Azure-fájlmegosztás, amely a kötet biztosít. Egy percen belül erre, a parancshoz adja vissza a `"provisioningState": "Succeeded"`.

A jelszó paraméter a sablonban `string` írja be a használat megkönnyítése érdekében. Megjelenik a képernyőn a tiszta szöveges és a központi telepítés állapota.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Győződjön meg arról, hogy az alkalmazás használhatja a kötet
Az alkalmazás létrehoz egy fájlt _data.txt_ a fájlmegosztásban (Ha még nem létezik már). Ez a fájl tartalma egy szám, amely 30 másodpercenként az alkalmazás növekszik. Győződjön meg arról, hogy a példában megfelelően működik-e, nyissa meg a _data.txt_ rendszeres időközönként fájlt, és győződjön meg arról, hogy hány frissítése folyamatban van.

A fájl letöltődik minden olyan eszköz, amely lehetővé teszi egy fájlmegosztás az Azure Files-böngészés használatával. A [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ilyen eszköz például.

## <a name="next-steps"></a>További lépések

- Az Azure Files kötet mintaalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/azurefiles-volume).
- Service Fabric Erőforrásmodell kapcsolatos további információkért lásd: [Service Fabric háló Erőforrásmodell](service-fabric-mesh-service-fabric-resources.md).
- Service Fabric-háló kapcsolatos további információkért olvassa el a [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md).
