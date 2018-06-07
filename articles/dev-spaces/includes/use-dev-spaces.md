---
title: fájl belefoglalása
description: fájl belefoglalása
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 7f4dced6f82622ba735b1b059f30d88830347fba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625848"
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Használhat Azure fejlesztői szóközöket a AKS fürt konfigurálása

Nyisson meg egy parancsablakot, és adja meg a következő Azure CLI-parancsot, az erőforráscsoport, amely tartalmazza a AKS fürt és a AKS fürt nevét használja. A parancs a fürt támogatja az Azure fejlesztői szóközöket konfigurálja.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

