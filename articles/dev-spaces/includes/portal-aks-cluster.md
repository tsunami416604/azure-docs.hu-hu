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
ms.openlocfilehash: 05736495d0d4a0c3a5072d29ad27801b6d4a7241
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967762"
---
## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Azure Dev Spaceshez engedélyezett Kubernetes-fürt létrehozása

1. Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.
1. Válassza az **Erőforrás létrehozása** lehetőséget > keressen a **Kubernetes** kifejezésre > válassza a **Kubernetes Service** > **Létrehozás** elemet.

   Tegye a következőket az AKS-fürt létrehozására szolgáló űrlap címsorai alatt.

    - **PROJEKT ADATAI**: válasszon ki egy Azure-előfizetést és egy új vagy meglévő Azure-erőforráscsoportot.
    - **FÜRT ADATAI**: adjon meg egy nevet, régiót (jelenleg kötelező az EastUS, Central US, WestEurope, WestUS2, CanadaCentral vagy CanadaEast régiót választani), verziót és DNS-névelőtagot az AKS-fürthöz.
    - **MÉRET**: válassza ki a virtuálisgép-méretet az AKS-ügynökcsomópontok számára, és a csomópontok számát. Ha most kezdte el az Azure Dev Spaces használatát, egyetlen csomópont elegendő az összes funkció kipróbálásához. A csomópontok száma bármikor egyszerűen beállítható a fürt telepítése után. Vegye figyelembe, hogy a virtuálisgép-méret az AKS-fürt létrehozását követően nem módosítható. Az AKS-fürt telepítése után azonban egyszerűen létrehozhat egy új, nagyobb virtuális gépekkel rendelkező AKS-fürtöt, majd a Dev Spaces használatával újratelepíthet erre a nagyobb fürtre, ha felskálázásra van szükség.

   Ügyeljen rá, hogy a Kubernetes 1.10.3-as vagy újabb verzióját válassza.

   ![Kubernetes konfigurációs beállításai](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Ha kész, válassza a **Következő: Hálózat** elemet.

1. Győződjön meg róla, hogy a HTTP-alkalmazások útválasztása engedélyezve van.

   ![HTTP-alkalmazások útválasztásának engedélyezése](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > Az AKS-fürt létrehozásakor ne felejtse el engedélyezni a HTTP-alkalmazások útválasztását. Ez a beállítás később nem módosítható.

1. Válassza ki a Szerepköralapú hozzáférés-vezérlés (RBAC) kívánt beállítását. Az Azure Dev Spaces engedélyezett és letiltott RBAC esetén is támogatja a fürtöket.

    ![RBAC-beállítás](../media/common/k8s-RBAC.PNG)

1. Amikor végzett, válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget.
