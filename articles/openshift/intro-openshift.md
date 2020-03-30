---
title: Bevezetés az Azure Red Hat OpenShift szolgáltatásba
description: Ismerje meg a Microsoft Azure Red Hat OpenShift szolgáltatásait és előnyeit a tárolóalapú alkalmazások üzembe helyezéséhez és kezeléséhez.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76276069"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

A Microsoft *Azure Red Hat OpenShift* szolgáltatás lehetővé teszi a teljes körűen felügyelt [OpenShift-fürtök](https://www.openshift.com/) üzembe helyezését.

Az Azure Red Hat OpenShift kiterjeszti a [Kubernetes.](https://kubernetes.io/) A Kubernetes-szel éles környezetben futó tárolók további eszközöket és erőforrásokat igényelnek, például lemezkép-jegyzéket, tárolási felügyeletet, hálózati megoldásokat, valamint naplózási és figyelési eszközöket, amelyek mindegyikét együtt kell verziószámmal és tesztelésre használni. A tárolóalapú alkalmazások létrehozása még több integrációs munkát igényel a köztes szoftverekkel, keretrendszerekkel, adatbázisokkal és CI/CD-eszközökkel. Az Azure Red Hat OpenShift mindezt egyetlen platformba egyesíti, így az informatikai csapatok számára megkönnyíti a műveleteket, miközben megadja az alkalmazáscsapatoknak, amire szükségük van a végrehajtáshoz.

Az Azure Red Hat OpenShift-et a Red Hat és a Microsoft közösen tervezte, működteti és támogatja, hogy integrált támogatási élményt nyújtson. Nincsenek működő virtuális gépek, és nincs szükség javításra. A fő, az infrastruktúra és az alkalmazáscsomópontok javítása, frissítése és figyelése az Ön nevében a Red Hat és a Microsoft által. Az Azure Red Hat OpenShift-fürtjeit az Azure-előfizetésében üzembe helyezi, és szerepelnek az Azure-számlán.

Kiválaszthatja saját beállításjegyzékét, hálózatát, tárolását és CI/CD-megoldásait, vagy használhatja a beépített megoldásokat az automatikus forráskód-kezeléshez, a tároló- és alkalmazásbuildekhez, a központi telepítésekhez, a méretezéshez, az állapotkezeléshez és egyebekhez. Az Azure Red Hat OpenShift integrált bejelentkezési élményt nyújt az Azure Active Directoryn keresztül.

Első lépésekhez végezze el az [Azure Red Hat OpenShift fürt](tutorial-create-cluster.md) létrehozása oktatóanyagot.

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A nagyobb biztonság és felügyelet érdekében az Azure Red Hat OpenShift lehetővé teszi az Azure Active Directoryval (Azure AD) való integrációt, és a Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) használatát. A fürtje és az erőforrásai állapotát is monitorozhatja.

## <a name="cluster-and-node"></a>Fürt és csomópont

Az Azure Red Hat OpenShift-csomópontok Azure virtuális gépeken futnak. Csatlakoztathat tárterületet csomópontokhoz és podokhoz, frissíthet fürtkomponenseket, és használhat GPU-kat.

## <a name="virtual-networks-and-ingress"></a>Virtuális hálózatok és bejövő forgalom

Az [Azure Red Hat OpenShift-fürthöz](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) társviszony-létesítéssel csatlakoztatható egy meglévő virtuális hálózathoz. Ebben a konfigurációban a podok csatlakozhatnak más szolgáltatásokhoz egy társviszony-létesített virtuális hálózatban.

A részleteket a [Fürt virtuális hálózatának csatlakoztatása meglévő virtuális hálózathoz.See Connect a cluster's virtual network to a existing virtual network](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) for details.

## <a name="kubernetes-certification"></a>Kubernetes-tanúsítvány

Az Azure Red Hat OpenShift szolgáltatás CnCF tanúsítvánnyal rendelkezik Kubernetes konformt.

## <a name="next-steps"></a>További lépések

Ismerje meg az Azure Red Hat OpenShift előfeltételeit:

> [!div class="nextstepaction"]
> [A fejlesztési környezet beállítása](howto-setup-environment.md)
