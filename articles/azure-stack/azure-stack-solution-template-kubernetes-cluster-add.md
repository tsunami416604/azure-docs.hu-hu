---
title: Adjon hozzá egy Kubernetes-fürtöt az Azure Stack piactéren |} A Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá egy Kubernetes-fürtöt az Azure Stack piactéren.
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
ms.date: 09/06/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 9287eb0925556d382410f95caf16a9ca478ca2da
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054417"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Adjon hozzá egy Kubernetes-fürtöt az Azure Stack piactéren

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!note]  
> Az AKS (az Azure Kubernetes Service) motor, az Azure Stacken private preview verzióban van. A Kubernetes Piactéri elem ebben a cikkben szereplő utasítások végrehajtásához szükséges hozzáférési kérelmek [hozzáférés beolvasására irányuló kérelem elküldése](https://aka.ms/azsk8).

Elérhetővé teheti egy Kubernetes-fürtöt, a Piactéri elem a felhasználók számára. A felhasználók egyetlen, koordinált műveletben telepítheti a Kubernetes.

A következő cikkben tekintse meg telepítése és jogosultságok kiosztása az erőforrások különálló Kubernetes-fürthöz tartozó Azure Resource Manager-sablon használatával. A Kezdés előtt ellenőrizze az Azure Stack és az Azure-bérlő globális beállításokat. Az Azure Stack a szükséges információkat gyűjt. Adja hozzá a szükséges erőforrásokat a bérlőn, és az Azure Stack piactéren. A fürt egy Ubuntu-kiszolgálót, egyéni szkript és a Kubernetes-fürt elemeket kell lennie a Marketplace-en függ.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Csomag, ajánlat és előfizetés létrehozása

Hozzon létre egy csomag, ajánlat és a Kubernetes-fürt Piactéri elem előfizetést. Megadhatja egy meglévő csomagot használja, és elérhetővé teheti.

1. Jelentkezzen be a [felügyeleti portálján.](https://adminportal.local.azurestack.external)

1. Hozzon létre egy csomag az alapszintű csomag. Útmutatásért lásd: [csomag létrehozása az Azure Stack](azure-stack-create-plan.md).

1. Hozzon létre egy ajánlatot. Útmutatásért lásd: [ajánlat létrehozása az Azure Stack](azure-stack-create-offer.md).

1. Válassza ki **kínál**, és keresse meg a létrehozott ajánlat.

1. Válassza ki **áttekintése** az ajánlat panelen.

1. Válassza ki **Állapotváltozáshoz**. Válassza ki **nyilvános**.

1. Válassza ki **+ új** > **ajánlatok és csomagok** > **előfizetés** , hozzon létre egy új előfizetést.

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

1. Adja meg `UbuntuServer`.

1. Válassza ki a kiszolgálót, az ehhez a profilhoz:
    - **Közzétevő**: Canonical
    - **Ajánlat**: UbuntuServer
    - **TERMÉKVÁLTOZAT**: 16.04-LTS
    - **Verzió**: 16.04.201802220

    > [!Note]  
    > Ubuntu Server 16.04 LTS egynél több verzióját megjelenhet. Adja hozzá a megfelelő verziót kell. A Kubernetes-fürt az elem pontos verziója szükséges.

1. Válassza ki **letöltése.**

## <a name="add-a-custom-script-for-linux"></a>Adjon hozzá egy egyéni parancsfájl linuxhoz

A Kubernetes-fürt hozzáadása a Marketplace-ről:

1. Nyissa meg a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **minden szolgáltatás** , majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Adja meg `Custom Script for Linux`.

1. Válassza ki a parancsprogramot, a következő profilhoz:
    - **Ajánlat**: 2.0 Linuxhoz készült egyéni parancsfájl
    - **Verzió**: 2.0.3
    - **Közzétevő**: a Microsoft vállalati

    > [!Note]  
    > Egyéni parancsfájl Linux több verziója is szerepelhet. Adja hozzá a megfelelő verziót kell. A Kubernetes-fürt az elem pontos verziója szükséges.

1. Válassza ki **letöltése.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>A Kubernetes-fürt hozzáadása a Marketplace-en

1. Nyissa meg a [felügyeleti portálján](https://adminportal.local.azurestack.external).

1. Válassza ki **A; szolgáltatások** , majd a a **felügyeleti** kategória, jelölje be **Marketplace felügyeleti**.

1. Válassza ki **+ hozzáadása az Azure-ból**.

1. Adja meg `Kubernetes Cluster`.

1. Válassza a(z) `Kubernetes Cluster` lehetőséget.

1. Válassza ki **letöltése.**

    > [!note]  
    > A Piactéri elem jelenik meg a Marketplace-en öt percig is eltarthat.

    ![Kubernetes-fürt](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Frissítheti vagy eltávolíthatja a Kubernetes-fürt 

A Kubernetes-fürt elem frissítésekor távolítsa el a cikket, amely a Marketplace-en kell. Ezután a utasítást ebben a cikkben a Kubernetes-fürt hozzáadása a Marketplace-en is követheti.

A Kubernetes-fürt elem eltávolítása:

1. Vegye figyelembe például az aktuális elem neve `Microsoft.AzureStackKubernetesCluster.0.1.0`

1. Csatlakozás az Azure Stack PowerShell használatával.

1. A következő PowerShell-parancsmag segítségével eltávolítja az elemet:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>További lépések

[Kubernetes-fürt üzembe helyezése az Azure Stackhez](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Az Azure Stackben szolgáltatásajánlások áttekintése](azure-stack-offer-services-overview.md)