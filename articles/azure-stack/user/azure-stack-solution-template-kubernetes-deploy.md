---
title: Az Azure Stack üzembe helyezése Kubernetes |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe Kubernetes az Azure Stackhez.
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
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: c96fdbfb56fe5274de941c0f9ab1a12cbcc241b6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658419"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Az Azure Stack üzembe helyezése Kubernetes

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió.

Ebben a cikkben üzembe helyezéséhez és a Kubernetes esetében egyetlen, koordinált műveletben állítsa be az erőforrásokat a lépésekkel. A lépések egy Azure Resource Manager-megoldássablon használják. Ön lesz kell az Azure Stack-telepítés, a szükséges információkat gyűjthet a hozza létre a sablont, és majd üzembe helyezése a felhőben. Az Azure Stack-sablon nem használ felügyelt AKS szolgáltatásának ugyanazt a globális Azure-ban érhető el.

## <a name="kubernetes-and-containers"></a>Kubernetes és a tárolók

Kubernetes ACS-motor az Azure Stacken által létrehozott Azure Resource Manager-sablonok használatával is telepítheti. [Kubernetes](https://kubernetes.io) üzembe helyezés automatizálásához egy nyílt forráskódú rendszer méretezés, és a tárolókban található alkalmazások felügyeletét. A [tároló](https://www.docker.com/what-container) van a képet. A tároló rendszerképét hasonló egy virtuális géphez, azonban egy virtuális gép eltérően, a tároló csak az alkalmazás, például a kódot, a kód, az adott könyvtárakat és a beállítások végrehajtásához futásidejű futtatásához szükséges erőforrásokat tartalmazza.

A Kubernetes használhatja:

- Rugalmasan méretezhető, bővíthető, másodpercek alatt telepíthető alkalmazásokat fejleszthet. 
- Leegyszerűsítheti az alkalmazást, és a megbízhatóság javításához különböző Helm-alkalmazások. [Helm](https://github.com/kubernetes/helm) egy nyílt forráskódú csomagolás eszköz, amely segít telepítése és a Kubernetes-alkalmazások életciklusának kezelését.
- Könnyedén figyelheti és diagnosztizálhatja a révén az alkalmazások állapotát, és frissítés funkcióit.

Ön csak kell fizetnie a számítási feladatok használatáért támogatása a fürt csomópontjai által igényelt. További információkért lásd: [használat és számlázás az Azure Stackben](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="deploy-kubernetes"></a>Kubernetes üzembe helyezése

A lépéseket az Azure Stacken Kubernetes-fürt üzembe helyezése az identity management-szolgáltatás függ. Ellenőrizze az identitáskezelési megoldás az Azure Stack telepítése által használt. Lépjen kapcsolatba az Azure Stack rendszergazdai az identity management szolgáltatás ellenőrzése.

- **Az Azure Active Directory (Azure AD)**  
A fürtön telepíteni, ha az Azure AD-vel kapcsolatos utasításokért lásd: [Kubernetes üzembe helyezése az Azure Stackhez az Azure Active Directory (Azure AD) használatával](azure-stack-solution-template-kubernetes-azuread.md).

- **Az Active Directory összevont szolgáltatások (AD FS)**  
A fürtön telepíteni, ha az AD FS használatával, lásd: [Kubernetes üzembe helyezése az Azure Stackhez az Active Directory összevont szolgáltatásokat (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Csatlakozás a fürthöz

Most már készen áll a fürthöz való csatlakozáshoz. A fő tekintheti meg az erőforráscsoportot, és nevű `k8s-master-<sequence-of-numbers>`. Használja az SSH-ügyfelet a főcsomóponthoz való kapcsolódáshoz. A fő használhatja **kubectl**, a fürt kezeléséhez a Kubernetes parancssori ügyfelét. Útmutatásért lásd: [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Azt is tapasztalhatja a **Helm** Csomagkezelő hasznos telepítéséhez és alkalmazások telepítése a fürtön. Telepítéséről és használatáról a Helm a fürttel kapcsolatos utasításokért lásd: [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>További lépések

[Kubernetes felvétele a Marketplace-en (az Azure Stack-operátorokról)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure Active Directory (Azure AD) használatával az Azure Stack üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-azuread.md)

[Az Active Directory összevonási szolgáltatásokban (AD FS) használatával az Azure Stack üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
