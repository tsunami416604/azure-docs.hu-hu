---
title: Az Azure verem Kubernetes fürt központi telepítése |} Microsoft Docs
description: 'Útmutató: Azure verem Kubernetes fürt központi telepítése.'
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
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 71d920ea1c143ed65510b77e1804e9c5e38cc180
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Azure verem Kubernetes fürt központi telepítése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

> [!note]  
> Az Azure-tároló szolgáltatás (ACS) Kubernetes Azure veremben van private Preview verziójára. Az Azure-verem operátort kell igényelnie az ebben a cikkben az utasítások végrehajtásához szükség Kubernetes Piactéri elemet a hozzáférést.

A következő cikk megvizsgálja egy Azure Resource Manager-megoldás sablon használatával történő telepítése és jogosultságok kiosztása a erőforrások Kubernetes az egyetlen, koordinált műveletben. Ön lesz kell a szükséges adatok összegyűjtése az Azure-verem telepítésével kapcsolatos hozza létre a sablont, és ezután telepíti a felhőbe.

## <a name="kubernetes-and-containers"></a>Kubernetes és tárolók

Telepítheti az Azure tároló szolgáltatás (ACS) Kubernetes Azure veremben. [Kubernetes](https://kubernetes.io) automatizálhatja a központi telepítés, nyílt forráskódú rendszer méretezés és a tárolókban lévő alkalmazások kezelése. A [tároló](https://www.docker.com/what-container) képeket hasonló egy virtuális gép található. Ellentétben a tároló kép csak van virtuális gép a az alkalmazás, például a kódot, a kódot, az adott könyvtárakat és a beállítások végrehajtásához futásidejű futtatása szükséges erőforrásokat is magában foglalja.

A Kubernetes használhatja:

- Amely másodpercben telepíthető nagymértékben méretezhető, frissíthető, alkalmazások fejlesztéséhez. 
- Leegyszerűsítheti az alkalmazás tervét, és a megbízhatóság javításához különböző Helm alkalmazások. [Helm](https://github.com/kubernetes/helm) nyílt forráskódú csomagolás eszköz, amely segít telepíteni, és Kubernetes alkalmazások életciklusának kezelését.
- Könnyen figyelése és diagnosztizálása, amely a futó alkalmazások állapotát, és funkciók frissítése.

## <a name="prerequisites"></a>Előfeltételek 

A kezdéshez, győződjön meg arról, a megfelelő engedélyekkel rendelkezik, és, hogy készen áll-e az Azure-verem.

1. Győződjön meg arról, hogy az Azure Active Directory (Azure AD-) bérlőben hozhatók létre alkalmazások. Ezeket az engedélyeket a Kubernetes telepítési van szükség.

    Az engedélyek ellenőrzése, lásd: [ellenőrizze Azure Active Directory-engedélyek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

2. Az SSH nyilvános és titkos kulcsból álló kulcspárt bejelentkezni a Linux virtuális gép Azure veremben készítése. A fürt létrehozásakor kell a nyilvános kulcsot.

    Kulcs létrehozása, lásd: [SSH kulcs generálása](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

3. Ellenőrizze, hogy rendelkezik érvényes előfizetéssel a Azure verem bérlői portálon, és, hogy rendelkezik-e elegendő nyilvános IP-címek adhatók hozzá új alkalmazások.

## <a name="create-a-service-principal-in-azure-ad"></a>Egy egyszerű szolgáltatás létrehozása az Azure ad-ben

1. Jelentkezzen be globális [Azure-portálon](http://www.poartal.azure.com).
2. Ellenőrizze, hogy a fiók be van jelentkezve az Azure AD-bérlő társított Azure verem használatával.
3. Hozzon létre egy Azure AD-alkalmazást.

    a. Válassza ki **az Azure Active Directory** > **+ alkalmazás regisztrációk** > **új alkalmazás regisztrációja**.

    b. Adjon meg egy **neve** az alkalmazás.

    c. Válassza ki **Web app / API**

    d. Adja meg `http://localhost` a a **bejelentkezési URL-cím**.

    c. Kattintson a **Create** (Létrehozás) gombra

4. Jegyezze fel a **Alkalmazásazonosító**. A fürt létrehozásakor kell a azonosítója. Az azonosító hivatkozott **egyszerű szolgáltatás ügyfél-azonosító**.

5. Válassza ki **beállítások** > **kulcsok**.

    a. Adja meg a **leírás**.

    b. Válassza ki **soha nem jár le** a **Expires**.

    c. Kattintson a **Mentés** gombra. Ellenőrizze, jegyezze fel a kulcs karakterlánc. A fürt létrehozásakor kell a kulcs karakterlánc. A kulcs hivatkozik, mint a **egyszerű Ügyfélkulcs**.



## <a name="give-the-service-principal-access"></a>A szolgáltatás egyszerű hozzáférést

A szolgáltatás egyszerű hozzáférés biztosítása az előfizetését, így a megbízó erőforrások.

1.  Jelentkezzen be a [felügyeleti portál](https://adminportal.local.azurestack.external).

2. Válassza ki **további szolgáltatások** > **felhasználói előfizetések** > **+ Hozzáadás**.

3. Válassza ki a létrehozott előfizetést.

4. Válassza ki **hozzáférés-vezérlés (IAM)** > Válasszon **+ Hozzáadás**.

5. Válassza ki a **tulajdonos** szerepkör.

6. Válassza ki az egyszerű szolgáltatás létrehozása alkalmazásnév. Előfordulhat, hogy a keresőmezőbe írja be a nevét.

7. Kattintson a **Save** (Mentés) gombra.

## <a name="deploy-a-kubernetes-cluster"></a>Kubernetes fürt központi telepítése

1. Nyissa meg a [verem Azure portal](https://portal.local.azurestack.external).

2. Válassza ki **+ új** > **számítási** > **Kubernetes fürt**.

    ![Megoldássablon telepítése](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template.png)

3. Válassza ki **paraméterek** a a megoldás-sablon üzembe helyezése.

    ![Megoldássablon telepítése](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template-parameters.png)

2. Adja meg a **Linux felügyeleti felhasználónév**. A Linux virtuális gépek a Kubernetes fürt részét képező és a DVM felhasználónév.

3. Adja meg a **nyilvános SSH-kulcs** az összes Linux-gépekhez hozza létre a Kubernetes fürt és a DVM részeként engedélyezési műveletekben használatos.

4. Adja meg a **végpont bérlői**. Ez az az Azure Resource Manager végpont hozzon létre az erőforráscsoportot a Kubernetes fürthöz való csatlakozáshoz. Szüksége lesz az Azure-verem operátor integrált rendszert a végpont lekérése. Az az Azure verem Development Kit (ASDK), használhat `https://management.local.azurestack.external`.

5. Adja meg a **bérlői azonosító** a bérlő számára. Ha ez az érték segítségre van szüksége, tekintse meg [-bérlőazonosító beszerzése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

6. Adja meg a **fő profil DNS-előtagja** a régió egyedi. Régió egyedi nevet, például a kell `k8s-12345`. Próbálja meg választott azt ugyanaz, mint az erőforráscsoport neve ajánlott eljárás.

    > [!note]  
    > Minden fürthöz egy új és egyedi fő profil DNS-előtagot használja.

7. Adja meg az ügynökök száma a fürtben. Ez az érték a neve a **ügynökök készlet profil száma**. Lehet 1-32 karakter

8. Adja meg a **szolgáltatás egyszerű Alkalmazásazonosító** Kubernetes Azure felhőszolgáltató használja.

9. Adja meg a **szolgáltatás egyszerű ügyfélkulcs** szolgáltatás egyszerű alkalmazás létrehozásakor létrehozott.

10. Adja meg a **Kubernetes Azure Cloud szolgáltatóverzió**. Ez az a verzió a Kubernetes Azure Provider. Az Azure verem minden Azure verem verzió egyéni Kubernetes build kiadását.

12. Kattintson az **OK** gombra.

### <a name="specify-the-solution-values"></a>Adja meg a megoldást értékeket

1. Válassza ki a **előfizetés**.

2. Adjon meg egy új erőforráscsoport neve, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevének kell lennie a alfanumerikus- és nagybetűket.

3. Adja meg például az erőforráscsoport helye **helyi**.

4. Válassza ki **létrehozása.**

## <a name="connect-to-your-cluster"></a>Csatlakozzon a fürthöz

Most már készen áll a fürthöz való csatlakozáshoz. Szüksége lesz a **kubectl**, a Kubernetes parancssori ügyfél. Utasításokat csatlakoztatásának és kezelésének a fürt Azure tároló Services dokumentációjában találja.   

Útmutatásért lásd: [csatlakozás a fürthöz](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough#connect-to-the-cluster).

## <a name="next-steps"></a>További lépések

[A piactér (az Azure-verem operátor) Kubernetes fürt hozzáadása](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Az Azure-on Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)