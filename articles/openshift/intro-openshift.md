---
title: Bevezetés az Azure Red Hat OpenShift
description: A Microsoft Azure Red Hat OpenShift funkcióinak és előnyeinek megismerése a tároló alapú alkalmazások üzembe helyezéséhez és kezeléséhez.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: a3bdc3673474b778aa7c1003e48e215bac6d05bf
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628520"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

A Microsoft *Azure Red Hat OpenShift* szolgáltatás lehetővé teszi a teljes körűen felügyelt [OpenShift](https://www.openshift.com/) -fürtök üzembe helyezését.

Az Azure Red Hat OpenShift kiterjeszti a [Kubernetes](https://kubernetes.io/)-t. Az éles környezetben futó tárolók Kubernetes további eszközöket és erőforrásokat igényel. Ez gyakran magában foglalja a képnyilvántartások, a tárolási felügyelet, a hálózati megoldások és a naplózási és figyelési eszközök összegyűjtését. A Container-alapú alkalmazások létrehozásához még nagyobb integrációra van szükség a köztes, a keretrendszerek, az adatbázisok és a CI/CD-eszközök esetében. Az Azure Red Hat OpenShift egyetlen platformon ötvözi az egészet, így megkönnyíti az IT-csapatok számára a működést.

Az Azure Red Hat OpenShift a Red Hat és a Microsoft közösen tervezték, üzemeltetik és támogatják, és integrált támogatási élményt biztosítanak. Nincsenek működő virtuális gépek, és nincs szükség javításra. A fő-, infrastruktúra-és alkalmazás-csomópontok a Red Hat és a Microsoft által az Ön nevében javítottak, frissíthetők és figyelhetők. Az Azure Red Hat OpenShift-fürtök üzembe helyezése Azure-előfizetésében történik, és az Azure-számlán is megtalálható.

Kiválaszthatja saját beállításjegyzék-, hálózatkezelési, tárolási és CI/CD-megoldásait, vagy használhatja a beépített megoldásait az automatizált forráskód-kezelés, a tárolók és az alkalmazások létrehozásához, üzembe helyezéséhez, méretezéséhez, az állapotadatok kezeléséhez és egyéb funkciókhoz. Az Azure Red Hat OpenShift integrált bejelentkezési élményt nyújt Azure Active Directoryon keresztül.

Az első lépésekhez fejezze be az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) című oktatóanyagot.

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A jobb biztonság és felügyelet érdekében az Azure Red Hat OpenShift lehetővé teszi a Azure Active Directory (Azure AD) integrálását és a Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) használatát. A fürtje és az erőforrásai állapotát is monitorozhatja.

## <a name="cluster-and-node"></a>Fürt és csomópont

Az Azure Red Hat OpenShift-csomópontjai Azure-beli virtuális gépeken futnak. A tárolót csatlakoztathatja a csomópontokhoz és a hüvelyekhez, és frissítheti a fürt összetevőit.

## <a name="next-steps"></a>További lépések

Ismerje meg az Azure Red Hat OpenShift előfeltételeit:

> [!div class="nextstepaction"]
> [A fejlesztési környezet beállítása](tutorial-create-cluster.md)
