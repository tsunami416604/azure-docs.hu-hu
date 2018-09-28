---
title: Adja hozzá a Kubernetes az Azure Stack piactéren |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá a Kubernetes az Azure Stack piactéren.
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
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 978cce4642dc61143bd829fcae03357fd8c969c2
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405512"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Adja hozzá a Kubernetes az Azure Stack piactéren

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban.

Elérhetővé teheti Kubernetes Piactéri elem, a felhasználók számára. A felhasználók egyetlen, koordinált műveletben telepítheti a Kubernetes.

A következő cikkben tekintse meg telepítése és jogosultságok kiosztása az erőforrások különálló Kubernetes-fürthöz tartozó Azure Resource Manager-sablon használatával. A Kubernetes-fürt Piactéri elem 0.3.0 1808 Azure Stack-verzió szükséges. A Kezdés előtt ellenőrizze az Azure Stack és az Azure-bérlő globális beállításokat. Az Azure Stack a szükséges információkat gyűjt. Adja hozzá a szükséges erőforrásokat a bérlőn, és az Azure Stack piactéren. A fürt egy Ubuntu-kiszolgálót, egyéni szkript és a Kubernetes elemeket kell lennie a Marketplace-en függ.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Csomag, ajánlat és előfizetés létrehozása

Hozzon létre egy csomag, ajánlat és a Kubernetes Piactéri elem előfizetést. Megadhatja egy meglévő csomagot használja, és elérhetővé teheti.

1. Jelentkezzen be a [felügyeleti portálján.](https://adminportal.local.azurestack.external)

1. Hozzon létre egy csomag az alapszintű csomag. Útmutatásért lásd: [csomag létrehozása az Azure Stack](azure-stack-create-plan.md).

1. Hozzon létre egy ajánlatot. Útmutatásért lásd: [ajánlat létrehozása az Azure Stack](azure-stack-create-offer.md).

1. Válassza ki **kínál**, és keresse meg a létrehozott ajánlat.

1. Válassza ki **áttekintése** az ajánlat panelen.

1. Válassza ki **Állapotváltozáshoz**. Válassza ki **nyilvános**.

1. Válassza ki **+ erőforrás létrehozása** > **ajánlatok és csomagok** > **előfizetés** , hozzon létre egy új előfizetést.

    a. Adjon meg egy **megjelenítendő név**.

    b. Adjon meg egy **felhasználói**. A tenanthoz társított Azure AD-fiókot használni.

    c. **Társzolgáltató leírása**

    d. Állítsa be a **címtárbérlő** az Azure stack az Azure AD-bérlőhöz. 

    e. Válassza ki **ajánlat**. Válassza ki a létrehozott ajánlat nevét. Jegyezze fel az előfizetés-azonosító.

## <a name="add-an-ubuntu-server-image"></a>Egy Ubuntu server-rendszerkép hozzáadása

Adja hozzá a következő Ubuntu Server-lemezképet a Marketplace-en:

1. Jelentkezzen be a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás**, majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Írja be a `UbuntuServer` (igen) kifejezést.

1. Válassza ki a kiszolgáló a következő profillal legújabb verziója:
    - **Közzétevő**: Canonical
    - **Ajánlat**: UbuntuServer
    - **TERMÉKVÁLTOZAT**: 16.04-LTS

1. Válassza ki **letöltése.**

## <a name="add-a-custom-script-for-linux"></a>Adjon hozzá egy egyéni parancsfájl linuxhoz

Adja hozzá a Kubernetes a Marketplace-ről:

1. Nyissa meg a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás** , majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Írja be a `Custom Script for Linux` (igen) kifejezést.

1. Válassza ki a parancsprogramot, a következő profilhoz:
    - **Ajánlat**: 2.0 Linuxhoz készült egyéni parancsfájl
    - **Verzió**: 2.0.6-os
    - **Közzétevő**: a Microsoft vállalati

    > [!Note]  
    > Egyéni parancsfájl Linux több verziója is szerepelhet. Adja hozzá a megfelelő verziót kell. A Kubernetes az elem pontos verziója szükséges.

1. Válassza ki **letöltése.**


## <a name="add-kubernetes-to-the-marketplace"></a>Kubernetes felvétele a Marketplace-en

1. Nyissa meg a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás** , majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Írja be a `Kubernetes` (igen) kifejezést.

1. Válassza a(z) `Kubernetes Cluster` lehetőséget.

1. Válassza ki **letöltése.**

    > [!note]  
    > A Piactéri elem jelenik meg a Marketplace-en öt percig is eltarthat.

    ![Kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Frissítheti vagy eltávolíthatja a Kubernetes 

A Kubernetes elem frissítésekor távolítsa el a cikket, amely a Marketplace-en kell. Ezután a utasítást ebben a cikkben a Kubernetes hozzáadása a Marketplace-en is követheti.

A Kubernetes-elem eltávolítása:

1. Csatlakozhat az Azure Stack PowerShell-kezelőként. További útmutatást lásd: [csatlakozhat az Azure Stack a PowerShell-lel kezelőként](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Keresse meg az aktuális Kubernetes-fürt elemet a katalógusban.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Vegye figyelembe például az aktuális elem neve `Microsoft.AzureStackKubernetesCluster.0.2.0`

4. A következő PowerShell-parancsmag segítségével eltávolítja az elemet:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>További lépések

[A Kubernetes az Azure Stack üzembe helyezése](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Az Azure Stackben szolgáltatásajánlások áttekintése](azure-stack-offer-services-overview.md)