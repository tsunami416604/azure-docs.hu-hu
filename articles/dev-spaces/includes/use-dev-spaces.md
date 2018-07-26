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
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991012"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>AKS-fürt konfigurálása az Azure Dev Spaces használatára

Nyissa meg a parancs ablakot, és írja be az alábbi Azure CLI-parancsot, és használja hozzá az AKS-fürtjét tartalmazó erőforráscsoportot, valamint az AKS-fürt nevét. A parancs konfigurálni fogja a fürtöt az Azure Dev Spaces támogatására.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

