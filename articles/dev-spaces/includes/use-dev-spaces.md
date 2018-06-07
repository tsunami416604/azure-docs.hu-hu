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
ms.openlocfilehash: 44ce986fcfdf079d3077c007a378f3467073d00d
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823180"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Használhat Azure fejlesztői szóközöket a AKS fürt konfigurálása

Nyisson meg egy parancsablakot, és adja meg a következő Azure CLI-parancsot, az erőforráscsoport, amely tartalmazza a AKS fürt és a AKS fürt nevét használja. A parancs a fürt támogatja az Azure fejlesztői szóközöket konfigurálja.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

