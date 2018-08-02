---
title: Az Azure Service Fabric Mesh parancssori felületének beállítása | Microsoft Docs
description: Ez a dokumentum az Azure Service Fabric Mesh parancssori felület beállítását ismerteti.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 567f2afdea44f439779212c61fb3a129f4f979be
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281572"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása
A Service Fabric Mesh parancssori felülete az erőforrások a Service Fabric Meshben való üzembe helyezéséhez és felügyeletéhez szükséges. 

Az előzetes verzióban az Azure Service Fabric Mesh parancssori felülete az Azure CLI bővítményeként van megírva. Telepítheti az Azure Cloud Shellbe vagy az Azure CLI helyileg telepített példányába. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0.35-ös vagy újabb verzióját kell telepítenie. A verzió azonosításához futtassa a következőt: `az --version`. A parancssori felület (CLI) telepítéséhez vagy legújabb verzióra történő frissítéséhez lásd: [Az Azure CLI 2.0 telepítése][azure-cli-install].

Az Azure Service Fabric Mesh parancssori felület moduljának előzetesen telepített példányait távolítsa el.

```azurecli-interactive
az extension remove --name mesh
```

Az Azure Service Fabric Mesh parancssori felület bővítménymodulját az alábbi paranccsal telepítheti. 

```azurecli-interactive
az extension add --source https://meshcli.blob.core.windows.net/cli/mesh-0.9.1-py2.py3-none-any.whl
```

A [Windows fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md) is beállíthatja.

[azure-cli-install]: /cli/azure/install-azure-cli