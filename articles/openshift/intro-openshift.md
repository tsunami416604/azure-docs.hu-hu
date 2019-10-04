---
title: Bevezetés az Azure Red Hat OpenShift | Microsoft Docs
description: A Microsoft Azure Red Hat OpenShift funkcióinak és előnyeinek megismerése a tároló alapú alkalmazások üzembe helyezéséhez és kezeléséhez.
services: container-service
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: f76f5d4dc84d1f8827248ab8399c1ae450a643a0
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620159"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

A Microsoft *Azure Red Hat OpenShift* szolgáltatás lehetővé teszi a teljes körűen felügyelt [OpenShift](https://www.openshift.com/) -fürtök üzembe helyezését.

Az Azure Red Hat OpenShift kiterjeszti a [Kubernetes](https://kubernetes.io/)-t. A Kubernetes éles környezetben futó tárolók további eszközöket és erőforrásokat igényelnek, mint például a rendszerkép-nyilvántartó, a tárolási felügyelet, a hálózati megoldások és a naplózási és figyelési eszközök, amelyek mindegyikét együtt kell telepíteni és tesztelni. A Container-alapú alkalmazások létrehozásához még nagyobb integrációra van szükség a köztes, a keretrendszerek, az adatbázisok és a CI/CD-eszközök esetében. Az Azure Red Hat OpenShift egyetlen platformon ötvözi az egészet, így megkönnyíti az IT-csapatok számára a működést.

Az Azure Red Hat OpenShift a Red Hat és a Microsoft közösen tervezték, üzemeltetik és támogatják, és integrált támogatási élményt biztosítanak. Nincsenek működő virtuális gépek, és nincs szükség javításra. A fő-, infrastruktúra-és alkalmazás-csomópontokat a Red Hat és a Microsoft által az Ön nevében javítottuk, frissítették és figyelik. Az Azure Red Hat OpenShift-fürtök üzembe helyezése Azure-előfizetésében történik, és az Azure-számlán is megtalálható.

Kiválaszthatja saját beállításjegyzék-, hálózatkezelési, tárolási és CI/CD-megoldásait, vagy használhatja a beépített megoldásait az automatizált forráskód-kezelés, a tárolók és az alkalmazások létrehozásához, üzembe helyezéséhez, méretezéséhez, az állapotadatok kezeléséhez és egyéb funkciókhoz. Az Azure Red Hat OpenShift integrált bejelentkezési élményt nyújt Azure Active Directoryon keresztül.

Az első lépésekhez fejezze be az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) című oktatóanyagot.

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A jobb biztonság és felügyelet érdekében az Azure Red Hat OpenShift lehetővé teszi a Azure Active Directory (Azure AD) integrálását és a Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) használatát. A fürtje és az erőforrásai állapotát is monitorozhatja.

## <a name="cluster-and-node"></a>Fürt és csomópont

Az Azure Red Hat OpenShift-csomópontjai Azure-beli virtuális gépeken futnak. Csatlakoztathat tárterületet csomópontokhoz és podokhoz, frissíthet fürtkomponenseket, és használhat GPU-kat.

## <a name="virtual-networks-and-ingress"></a>Virtuális hálózatok és bejövő forgalom

[Egy Azure Red Hat OpenShift-fürtöt összekapcsolhatók egy meglévő virtuális hálózattal](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) a peering használatával. Ebben a konfigurációban a hüvelyek kapcsolódhatnak más szolgáltatásokhoz egy társ virtuális hálózaton.

A részletekért lásd: [fürt virtuális hálózatának összekötése meglévő virtuális hálózathoz](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) .

## <a name="kubernetes-certification"></a>Kubernetes-tanúsítvány

Az Azure Red Hat OpenShift szolgáltatás CNCF minősítéssel rendelkezik, Kubernetes-megfelelőségként.

## <a name="next-steps"></a>További lépések

Ismerje meg az Azure Red Hat OpenShift előfeltételeit:

> [!div class="nextstepaction"]
> [A fejlesztési környezet beállítása](howto-setup-environment.md)
