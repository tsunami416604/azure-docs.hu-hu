---
title: Az Azure Red Hat OpenShift bemutatása |} A Microsoft Docs
description: Ismerje meg a funkciók és előnyök, a Microsoft Azure Red Hat OpenShift üzembe és kezelhet tárolóalapú alkalmazásokat.
services: container-service
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/06/2019
ms.custom: mvc
ms.openlocfilehash: 6121c0f654a61a147e84f0697f3ddb06b7c5db92
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65081044"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

A Microsoft *Azure Red Hat OpenShift* szolgáltatás segítségével helyezhet üzembe teljes körűen felügyelt [OpenShift](https://www.openshift.com/) fürtök.

Az Azure Red Hat OpenShift kibővíti [Kubernetes](https://kubernetes.io/). Futó tárolók éles környezetben az Kubernetes szükséges további eszközöket és erőforrásokat, például egy regisztrációs adatbázisba, tárolók kezelése, hálózatkezelési megoldások és naplózás, és monitorozási eszközökkel, amelyek mindegyike kell rendszerverzióval ellátott és tesztelt együtt. Még több integrációs együttműködnek a közbenső szoftverek, keretrendszerek, adatbázisok és CI/CD eszközökkel tárolóalapú alkalmazások létrehozásához szükséges. Az Azure Red Hat OpenShift egyesíti az összes Ez egyetlen platformban, miközben alkalmazás egyszerű műveleteket az IT-csoportoknak oszloptárat csapatok, mit kell végrehajtani.

Az Azure Red Hat OpenShift közösen fejthetők vissza, üzemeltetett, és adja meg az integrált támogatási megoldást a Red Hat és a Microsoft által támogatott. Nincsenek virtuális gépek megfelelően működjenek, és nem a javítás nem szükséges. Fő-, infrastruktúra- és az csomópontok javítani, frissítve, és Red Hat és a Microsoft felügyelje az Ön nevében. Az Azure Red Hat OpenShift-fürtök a rendszer üzembe helyezi az Azure-előfizetés és az Azure-elszámolások részét képezik.

Választhat saját beállításjegyzék, hálózati, tárolási, és a CI/CD-megoldások vagy buildek automatizált Forráskezelő kódot, a tároló és az alkalmazás a beépített megoldások használata, központi telepítések, méretezés, egészségügyi felügyeleti és több. Az Azure Red Hat OpenShift egy integrált bejelentkezési élményt nyújt az Azure Active Directoryn keresztül.

Első lépésként hajtsa végre a [hozzon létre egy Azure Red Hat OpenShift fürtöt](tutorial-create-cluster.md) oktatóanyag.

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A nagyobb biztonság és felügyelet Azure Red Hat OpenShift lehetővé teszi az Azure Active Directoryval (Azure AD) integrálása és Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC). A fürtje és az erőforrásai állapotát is monitorozhatja.

## <a name="cluster-and-node"></a>Fürt és csomópont

Az Azure Red Hat OpenShift csomópontok futtassa az Azure-beli virtuális gépeken. Csatlakoztathat tárterületet csomópontokhoz és podokhoz, frissíthet fürtkomponenseket, és használhat GPU-kat.

## <a name="virtual-networks-and-ingress"></a>Virtuális hálózatok és bejövő forgalom

Telepíthet egy Azure Red Hat OpenShift fürtöt egy meglévő virtuális hálózatban. Ebben a konfigurációban a fürt minden pod IP-címet a virtuális hálózatban van hozzárendelve, és közvetlenül kommunikálhatnak a fürt más podok és a virtuális hálózat más csomópontjaira. Podok is csatlakoztathat egy társított virtuális hálózaton található más szolgáltatásokkal, valamint a helyszíni hálózatokon keresztül [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) vagy site-to-site (S2S) VPN-kapcsolatok.

További információkért lásd: [Microsoft Red Hat OpenShift fürt létrehozása az Azure-ban](tutorial-create-cluster.md).

## <a name="kubernetes-certification"></a>Kubernetes-tanúsítvány

Az Azure Red Hat OpenShift service Kubernetes megfelelő igazoló minősítéssel CNCF lett.

## <a name="next-steps"></a>További lépések

További információ az Azure Red Hat OpenShift előfeltételei:

> [!div class="nextstepaction"]
> [A fejlesztési környezet beállítása](howto-setup-environment.md)