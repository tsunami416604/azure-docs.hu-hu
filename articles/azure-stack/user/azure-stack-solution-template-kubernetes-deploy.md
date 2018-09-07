---
title: Kubernetes-fürt üzembe helyezése az Azure Stackhez |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy Kubernetes-fürtöt az Azure Stackhez.
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
ms.openlocfilehash: 5df8104907e30337cdf47e5a9489ffdb50451a94
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055216"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Kubernetes-fürt üzembe helyezése az Azure Stackhez

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az AKS (az Azure Kubernetes Service) motor, az Azure Stacken private preview verzióban van. Az Azure Stack-operátorokról kell kérnie a Kubernetes Piactéri elem ebben a cikkben az utasítások végrehajtásához szükséges hozzáférést.

A következő cikkben megvizsgál egy megoldás Azure Resource Manager-sablon használatával történő üzembe helyezése és a Kubernetes-erőforrások kiépítése egyetlen, koordinált műveletben. Fogja kell az Azure Stack-telepítés, a szükséges információkat gyűjthet, létrehozni a sablont, és ezután üzembe helyezése a felhőben.

## <a name="kubernetes-and-containers"></a>Kubernetes és a tárolók

A Kubernetes Azure Stack az Azure Kubernetes-szolgáltatások (AKS)-motor által létrehozott Azure Resource Manager-sablonok használatával is telepítheti. [Kubernetes](https://kubernetes.io) üzembe helyezés automatizálásához egy nyílt forráskódú rendszer méretezés, és a tárolókban található alkalmazások felügyeletét. A [tároló](https://www.docker.com/what-container) az a képen hasonló virtuális gép szerepel. Ellentétben a virtuális gép, a tároló rendszerképét csak az olyan erőforrások tartoznak az alkalmazás, például a kódot, a kód, az adott könyvtárakat és a beállítások végrehajtásához futásidejű futtatásához.

A Kubernetes használhatja:

- Rugalmasan méretezhető, bővíthető, másodpercek alatt telepíthető alkalmazásokat fejleszthet. 
- Leegyszerűsítheti az alkalmazást, és a megbízhatóság javításához különböző Helm-alkalmazások. [Helm](https://github.com/kubernetes/helm) egy nyílt forráskódú csomagolás eszköz, amely segít telepítése és a Kubernetes-alkalmazások életciklusának kezelését.
- Könnyedén figyelheti és diagnosztizálhatja a révén az alkalmazások állapotát, és frissítés funkcióit.

## <a name="prerequisites"></a>Előfeltételek 

Első lépésként ellenőrizze, hogy a megfelelő engedélyekkel rendelkezik, és, hogy készen áll-e az Azure Stack.

1. Győződjön meg arról, hogy hozhat létre alkalmazásokat az Azure Active Directory (Azure AD) bérlő. A Kubernetes üzembe helyezési ezekkel az engedélyekkel kell rendelkeznie.

    Engedélyek ellenőrzése kapcsolatos utasításokért lásd: [ellenőrizze az Azure Active Directory-engedélyek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Hozzon létre nyilvános és titkos ssh-kulcs, jelentkezzen be a Linux rendszerű virtuális gép az Azure Stacken. A fürt létrehozásakor kell a nyilvános kulcsot.

    -Kulcs létrehozásával kapcsolatos utasításokért lásd: [SSH kulcs generálása](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Ellenőrizze, hogy az Azure Stack-bérlői portálon érvényes előfizetéssel rendelkezik, és, hogy rendelkezik-e elegendő nyilvános IP-címek adhatók hozzá az új alkalmazások.

    A fürt nem telepíthető az Azure Stackkel **rendszergazda** előfizetés. Egy felhasználó ** előfizetést kell használnia. 

## <a name="create-a-service-principal-in-azure-ad"></a>Egyszerű szolgáltatás létrehozása az Azure ad-ben

1. Jelentkezzen be globális [az Azure portal](http://portal.azure.com).
1. Ellenőrizze, hogy Ön az Azure Stack-példányhoz társított Azure AD-bérlő jelentkezzen be.
1. Hozzon létre egy Azure AD-alkalmazást.

    a. Válassza ki **az Azure Active Directory** > **+ Alkalmazásregisztrációk** > **új Alkalmazásregisztráció**.

    b. Adjon meg egy **neve** az alkalmazás.

    c. Válassza ki **webalkalmazás / API**.

    d. Adja meg `http://localhost` számára a **bejelentkezési URL-**.

    c. Kattintson a **Create** (Létrehozás) gombra.

1. Jegyezze fel az **alkalmazás azonosítóját**. A fürt létrehozásakor kell a azonosítója. Az azonosító néven van hivatkozott **egyszerű szolgáltatás ügyfél-azonosító**.

1. Válassza ki **beállítások** > **kulcsok**.

    a. Adja meg a **leírás**.

    b. Válassza ki **soha nem jár le** a **lejárat**.

    c. Kattintson a **Mentés** gombra. Győződjön meg, vegye figyelembe a kulcs karakterláncát. Szüksége lesz a kulcs karakterláncát a fürt létrehozásakor. A kulcs hivatkozik a **egyszerű szolgáltatás titkos Ügyfélkód**.



## <a name="give-the-service-principal-access"></a>A szolgáltatás egyszerű hozzáférést

A szolgáltatás egyszerű hozzáférést biztosít az előfizetéshez, hogy a rendszerbiztonsági tag erőforrásokat hozhat létre.

1.  Jelentkezzen be a [Azure Stack portálon](https://portal.local.azurestack.external/).

1. Válassza ki **további szolgáltatások** > **előfizetések**.

1. Válassza ki az előfizetést, amelyhez a létrehozott.

1. Válassza ki **hozzáférés-vezérlés (IAM)** > Válasszon **+ Hozzáadás**.

1. Válassza ki a **tulajdonosa** szerepkör.

1. Válassza ki az egyszerű szolgáltatásnév a szolgáltatás számára létrehozott alkalmazás nevét. A Keresés mezőbe írja be a név lehet.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="deploy-a-kubernetes-cluster"></a>Kubernetes-fürt üzembe helyezése

1. Nyissa meg a [Azure Stack portálon](https://portal.local.azurestack.external).

1. Válassza ki **+ új** > **számítási** > **Kubernetes-fürt**. Kattintson a **Create** (Létrehozás) gombra.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

1. Válassza ki **alapjai** a a Kubernetes-fürt létrehozása.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Adja meg a **Linux rendszerű virtuális gép rendszergazdai felhasználónevét**. A Linux rendszerű virtuális gépek, a Kubernetes-fürt részét képező és a DVM felhasználóneve.

1. Adja meg a **SSH Public Key** használt a hitelesítéshez a Kubernetes-fürt és a DVM részeként létrehozott összes Linux rendszerű gépen.

1. Adja meg a **fő profil DNS-előtagja** , amely egyedi a régióban. Ez egy régió egyedi nevet, például kell lennie `k8s-12345`. Próbálja meg úgy döntött, hogy ugyanaz, mint az erőforráscsoport neve ajánlott eljárás.

    > [!Note]  
    > Ha mindegyik fürthöz egy új és egyedi fő profil DNS-előtagot használja.

1. Adja meg a **készlet profil ügynökeinek**. A száma a fürtben található ügynökök számát tartalmazza. Lehet 1-4.

1. Adja meg a **szolgáltatásnév ClientId** ezt használja a Kubernetes Azure felhőszolgáltató.

1. Adja meg a **egyszerű szolgáltatás titkos Ügyfélkód** szolgáltatás egyszerű alkalmazás létrehozásakor létrehozott.

1. Adja meg a **Kubernetes az Azure Cloud szolgáltató verziója**. Ez az a verzió a Kubernetes Azure-szolgáltatóhoz. Az Azure Stack kiad egy egyéni Kubernetes-build minden egyes Azure Stack-verzió.

1. Válassza ki a **előfizetés** azonosítóját.

1. Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket.

1. Válassza ki a **hely** az erőforráscsoport. Ez az a régió úgy dönt, az Azure Stack-telepítés.

### <a name="specify-the-azure-stack-settings"></a>Az Azure Stack-beállításainak megadása

1. Válassza ki a **blokk beállításait az Azure Stack**.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. Adja meg a **bérlői Arm-végpont**. Ez az az Azure Resource Manager-végpont hozza létre az erőforráscsoportot, a Kubernetes-fürthöz való kapcsolódáshoz. A végpont kérhet le az Azure Stack-operátorokról integrált rendszer kell. Az az Azure Stack Development Kit (ASDK), használhatja `https://management.local.azurestack.external`.

1. Adja meg a **Bérlőazonosító** a bérlő számára. Ha az érték megkeresésével segítségre van szüksége, tekintse meg [Bérlőazonosító beszerzése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

## <a name="connect-to-your-cluster"></a>Csatlakozás a fürthöz

Most már készen áll a fürthöz való csatlakozáshoz. A fő tekintheti meg az erőforráscsoportot, és nevű `k8s-master-<sequence-of-numbers>`. Használja az SSH-ügyfelet a főcsomóponthoz való kapcsolódáshoz. A fő használhatja **kubectl**, a fürt kezeléséhez a Kubernetes parancssori ügyfelét. Útmutatásért lásd: [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Azt is tapasztalhatja a **Helm** Csomagkezelő hasznos telepítéséhez és alkalmazások telepítése a fürtön. Telepítéséről és használatáról a Helm a fürttel kapcsolatos utasításokért lásd: [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>További lépések

[Kubernetes-fürt hozzáadása a Marketplace-en (az Azure Stack-operátorokról)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
