---
title: Kubernetes fürt hozzáadása a verem Azure piactérről |} Microsoft Docs
description: Megtudhatja, hogyan Kubernetes fürt hozzáadása a verem Azure piactéren.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 0ba0e1f3d9e0f1cbb6ba4109a21fc29dc41df5fc
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603453"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>A verem Azure piactér Kubernetes fürt hozzáadása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

> [!note]  
> Az Azure-tároló szolgáltatás (ACS) Kubernetes Azure veremben van private Preview verziójára. Az ebben a cikkben az utasítások végrehajtásához szükség Kubernetes Piactéri elemet hozzáférési kérelmek [hozzáférés kérelmezéséhez](https://aka.ms/azsk8).

Piactér elemként Kubernetes fürt kínálhat, a felhasználók számára. A felhasználók egyetlen, koordinált műveletben telepítheti Kubernetes.

A következő cikkben tekintse meg az Azure Resource Manager-sablon használatával telepítése és jogosultságok kiosztása a egy önálló Kubernetes fürt erőforrásait. Mielőtt nekilátna, ellenőrizze a Azure verem és a Azure-bérlőhöz globális beállítások. Az Azure-verem a szükséges információkat gyűjt. Szükséges erőforrások hozzáadása a bérlő és a verem Azure piactéren. A fürt egy Ubuntu server, egyéni parancsfájlt és a Kubernetes fürt elemek kell lennie a piactéren függ.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>A terv, egy ajánlatot és előfizetés létrehozása

Hozzon létre egy csomagot, egy ajánlatot és a Kubernetes fürt Piactéri elemet előfizetést. Egy meglévő csomag használata is, és kínálnak.

1. Jelentkezzen be a [felügyeleti portálján.](https://adminportal.local.azurestack.external)

2. Hozzon létre egy csomagot, a referenciaterv. Útmutatásért lásd: [terv létrehozása az Azure-készletben](azure-stack-create-plan.md).

3. Hozzon létre egy ajánlatot. Útmutatásért lásd: [hozzon létre egy ajánlatot Azure verem](azure-stack-create-offer.md).

4. Válassza ki **kínál**, és keresse meg a létrehozott ajánlat.

5. Válassza ki **áttekintése** az ajánlat panelen.

6. Válassza ki **Állapotváltozáshoz**. Válassza ki **nyilvános**.

7. Válassza ki **+ új** > **nyújt, és tervek** > **előfizetés** hozhat létre egy új előfizetést.

    a. Adjon meg egy **megjelenítendő név**.

    b. Adjon meg egy **felhasználói**. A tenanthoz társított Azure AD-fiókot használni.

    c. **Társzolgáltató leírása**

    d. Állítsa be a **Directory-bérlő** az Azure-verem az Azure AD-bérlő. 

    e. Válassza ki **kínálnak**. Válassza ki a létrehozott ajánlat nevét. Jegyezze fel az előfizetés-azonosító.

## <a name="add-an-ubuntu-server-image"></a>Ubuntu server lemezkép hozzáadása

A következő Ubuntu Server lemezképének hozzáadása a piactéren:

1. Jelentkezzen be a [felügyeleti portál](https://adminportal.local.azurestack.external).

2. Válassza ki **további szolgáltatások** > **piactér felügyeleti**.

3. Válassza ki **+ adja hozzá az Azure-ból**.

4. Adja meg `UbuntuServer`.

5. Válassza ki a kiszolgálót, a következő profilhoz:
    - **A Publisher**: kanonikus
    - **Ajánlat**: UbuntuServer
    - **SKU**: 16.04-ES LTS VERZIÓ
    - **Verzió**: 16.04.201802220

    > [!Note]  
    > Ubuntu Server 16.04 LTS egynél több verzióját is szerepelhet. Akkor adja hozzá a megfelelő verziót. A Kubernetes fürt elem pontos verziója szükséges.

6. Válassza ki **letöltése.**

## <a name="add-a-custom-script-for-linux"></a>Egyéni parancsfájl hozzáadása a Linux rendszerekhez

Adja hozzá a Kubernetes fürtöt a piactérről:

1. Nyissa meg a [felügyeleti portál](https://adminportal.local.azurestack.external).

2. Válassza ki **további szolgáltatások** > **piactér felügyeleti**.

3. Válassza ki **+ adja hozzá az Azure-ból**.

4. Adja meg `Custom Script for Linux`.

5. Válassza ki a parancsfájl a következő profilhoz:
    - **Ajánlat**: 2.0 Linux egyéni parancsfájl
    - **Verzió**: 2.0.3
    - **A Publisher**: Microsoft Corp

    > [!Note]  
    > Egyéni parancsfájl a Linux több verziója is szerepelhet. Akkor adja hozzá a megfelelő verziót. A Kubernetes fürt elem pontos verziója szükséges.

6. Válassza ki **letöltése.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Adja hozzá a Kubernetes fürtöt a piactér

1. Nyissa meg a [felügyeleti portál](https://adminportal.local.azurestack.external).

2. Válassza ki **további szolgáltatások** > **piactér felügyeleti**.

3. Válassza ki **+ adja hozzá az Azure-ból**.

4. Adja meg `Kubernetes Cluster`.

5. Válassza a(z) `Kubernetes Cluster` lehetőséget.

6. Válassza ki **letöltése.**

    > [!note]  
    > A Piactéri elemet a piactér megjelenő öt percet is igénybe vehet.

    ![Kubernetes fürt](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Frissítheti vagy eltávolíthatja a Kubernetes fürt 

A fürt Kubernetes elem frissítésekor szüksége lesz a eltávolítja az elemet, amely a piactéren. Majd akkor is kövesse a cikkben a Kubernetes fürt hozzáadása a piactéren.

A fürt Kubernetes elem eltávolítása:

1. Jegyezze fel, mint a jelenlegi elem neve `Microsoft.AzureStackKubernetesCluster.0.1.0`

2. Csatlakozás Azure verem a PowerShell használatával.

3. A következő PowerShell-parancsmag segítségével távolítsa el az elemet:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>További lépések

[Az Azure verem Kubernetes fürt központi telepítése](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Az ajánlat Azure verem szolgáltatások áttekintése](azure-stack-offer-services-overview.md)