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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 43c0b7c87f9ee1cd33da3d617747c11dc120e51a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823622"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Azure verem Kubernetes fürt központi telepítése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

> [!Note]  
> Az Azure-tároló szolgáltatás (ACS) Kubernetes Azure veremben van private Preview verziójára. Az Azure-verem operátort kell igényelnie az ebben a cikkben az utasítások végrehajtásához szükség Kubernetes Piactéri elemet a hozzáférést.

A következő cikk megvizsgálja egy Azure Resource Manager-megoldás sablon használatával történő telepítése és jogosultságok kiosztása a erőforrások Kubernetes az egyetlen, koordinált műveletben. Ön lesz kell a szükséges adatok összegyűjtése az Azure-verem telepítésével kapcsolatos hozza létre a sablont, és ezután telepíti a felhőbe.

## <a name="kubernetes-and-containers"></a>Kubernetes és tárolók

Az Azure-veremben Azure tároló szolgáltatás (ACS) motor által létrehozott Azure Resource Manager-sablonok használatával Kubernetes telepítése. [Kubernetes](https://kubernetes.io) automatizálhatja a központi telepítés, nyílt forráskódú rendszer méretezés és a tárolókban lévő alkalmazások kezelése. A [tároló](https://www.docker.com/what-container) képeket hasonló egy virtuális gép található. Ellentétben a virtuális gépek a tároló kép csak erőforrásokat is magában foglalja a egy alkalmazás, például a kódot, a kódot, az adott könyvtárakat és a beállítások végrehajtásához futásidejű futtatásához.

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

    A fürt nem telepíthető az Azure-verem **rendszergazda** előfizetés. Egy felhasználó ** előfizetést kell használnia. 

## <a name="create-a-service-principal-in-azure-ad"></a>Egy egyszerű szolgáltatás létrehozása az Azure ad-ben

1. Jelentkezzen be globális [Azure-portálon](http://portal.azure.com).
2. Ellenőrizze, hogy a fiók be van jelentkezve az Azure-verem-példányhoz társított Azure AD-bérlő segítségével.
3. Hozzon létre egy Azure AD-alkalmazást.

    a. Válassza ki **az Azure Active Directory** > **+ alkalmazás regisztrációk** > **új alkalmazás regisztrációja**.

    b. Adjon meg egy **neve** az alkalmazás.

    c. Válassza ki **Web app / API**.

    d. Adja meg `http://localhost` a a **bejelentkezési URL-cím**.

    c. Kattintson a **Create** (Létrehozás) gombra.

4. Jegyezze fel a **Alkalmazásazonosító**. A fürt létrehozásakor kell a azonosítója. Az azonosító hivatkozott **egyszerű szolgáltatás ügyfél-azonosító**.

5. Válassza ki **beállítások** > **kulcsok**.

    a. Adja meg a **leírás**.

    b. Válassza ki **soha nem jár le** a **Expires**.

    c. Kattintson a **Mentés** gombra. Ellenőrizze, jegyezze fel a kulcs karakterlánc. A fürt létrehozásakor kell a kulcs karakterlánc. A kulcs hivatkoznak, mint a **egyszerű Ügyfélkulcs**.



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

2. Válassza ki **+ új** > **számítási** > **Kubernetes fürt**. Kattintson a **Create** (Létrehozás) gombra.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

3. Válassza ki **alapjai** a a Kubernetes fürt létrehozása.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

2. Adja meg a **Linux virtuális gép rendszergazdai felhasználónév**. A Linux virtuális gépek a Kubernetes fürt részét képező és a DVM felhasználónév.

3. Adja meg a **SSH Public Key** az összes Linux-gépekhez hozza létre a Kubernetes fürt és a DVM részeként engedélyezési műveletekben használatos.

4. Adja meg a **fő profil DNS-előtagja** a régió egyedi. Régió egyedi nevet, például a kell `k8s-12345`. Próbálja meg választott azt ugyanaz, mint az erőforráscsoport neve ajánlott eljárás.

    > [!Note]  
    > Minden fürthöz egy új és egyedi fő profil DNS-előtagot használja.

5. Adja meg a **ügynökök készlet profil száma**. A szám a fürtben lévő ügynökök számát tartalmazza. Nem lehet 1-4.

6. Adja meg a **egyszerű ClientId** Kubernetes Azure felhőszolgáltató használja.

7. Adja meg a **egyszerű Ügyfélkulcs** szolgáltatás egyszerű alkalmazás létrehozásakor létrehozott.

8. Adja meg a **Kubernetes Azure Cloud szolgáltatóverzió**. Ez az a verzió a Kubernetes Azure Provider. Az Azure verem minden Azure verem verzió egyéni Kubernetes build kiadását.

9. Válassza ki a **előfizetés** azonosítóját.

10. Adjon meg egy új erőforráscsoport neve, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevének kell lennie a alfanumerikus- és nagybetűket.

11. Válassza ki a **hely** az erőforráscsoportban. Ez az a régió, az Azure-verem telepítés választja.

### <a name="specify-the-azure-stack-settings"></a>Az Azure-verem beállításainak megadása

1. Válassza ki a **Azure verem Stamp beállítások**.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

2. Adja meg a **Arm végpont bérlői**. Ez az az Azure Resource Manager végpont hozzon létre az erőforráscsoportot a Kubernetes fürthöz való csatlakozáshoz. Szüksége lesz az Azure-verem operátor integrált rendszert a végpont lekérése. Az az Azure verem Development Kit (ASDK), használhat `https://management.local.azurestack.external`.

3. Adja meg a **Bérlőazonosító** a bérlő számára. Ha ez az érték segítségre van szüksége, tekintse meg [-bérlőazonosító beszerzése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

## <a name="connect-to-your-cluster"></a>Csatlakozzon a fürthöz

Most már készen áll a fürthöz való csatlakozáshoz. A fő itt található: a fürterőforrás-csoportot, és nevű `k8s-master-<sequence-of-numbers>`. Egy SSH-ügyfél segítségével a fő kapcsolódik. A fő használható **kubectl**, a Kubernetes parancssori ügyfél, a fürt kezeléséhez. Útmutatásért lásd: [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Azt is tapasztalhatja a **Helm** Csomagkezelő telepítéséhez és alkalmazások központi telepítéséhez a fürt hasznos. Útmutatás a telepítéssel és használattal Helm a fürthöz: [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>További lépések

[A piactér (az Azure-verem operátor) Kubernetes fürt hozzáadása](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Az Azure-on Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
