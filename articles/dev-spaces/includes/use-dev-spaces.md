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
ms.openlocfilehash: 2563f7c36283521541562bcd88f973d86a6f672a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Használhat Azure fejlesztői szóközöket a AKS fürt konfigurálása

Nyisson meg egy parancsablakot, és adja meg a következő Azure CLI-parancsok, az erőforráscsoport, amely tartalmazza a AKS fürt és a AKS fürt nevét használja:

   ```cmd
   az extension add --name dev-spaces-preview 
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
Az első parancs bővítmény telepíti az Azure CLI Azure fejlesztői tárolóhelyek támogatásához, és a második konfigurálja a fürt támogatja az Azure fejlesztői szóközöket.
