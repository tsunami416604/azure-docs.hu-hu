---
title: Saját üzemeltetésű Azure API Management-átjáró üzembe helyezése a Kubernetes-ben | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy saját üzemeltetésű Azure API Management-átjárót a Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513834"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Saját üzemeltetésű Azure API Management-átjáró üzembe helyezése a Kubernetes-ben

Ez a cikk a saját üzemeltetésű Azure API Management-átjárók Kubernetes-fürtbe történő telepítésének lépéseit ismerteti.

> [!NOTE]
> A saját üzemeltetésű átjáró funkció előzetes verzióban érhető el. Az előzetes verzió ideje alatt a saját üzemeltetésű átjáró csak a fejlesztői és prémium szinteken érhető el, díjmentesen. A fejlesztői réteg egyetlen saját üzemeltetésű átjáróra korlátozódik.


## <a name="prerequisites"></a>Előfeltételek

- Végezze el a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
- Kubernetes-fürt létrehozása. A [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) jó megoldás fejlesztési és értékelési célokra. Éles számítási feladatokhoz használhatja az [Azure Kubernetes szolgáltatást](https://azure.microsoft.com/services/kubernetes-service/) vagy egy Kubernetes-fürtöt egy idegen felhőben.
- [Átjáró-erőforrás kiépítése a API Management-példányban](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Az átjáró üzembe helyezése a Kubernetes-ben

1. Válassza az **átjárók** lehetőséget a **Beállítások**területen.
2. Válassza ki azt az átjáró-erőforrást, amelyet telepíteni kíván.
3. Válassza a **telepítés**lehetőséget.
4. Vegye figyelembe, hogy a **jogkivonat** szövegmezőben lévő új jogkivonat automatikusan lett létrehozva az alapértelmezett **lejárati** és **titkos kulcs** értékeinek használatával. Ha szükséges, módosítsa a vagy mindkettőt, és válassza a **Létrehozás** lehetőséget egy új jogkivonat létrehozásához.
5. Győződjön meg arról, hogy az **üzembehelyezési parancsfájlok**területen a **Kubernetes** van kiválasztva.
6. Válassza ki **< Gateway-name >. YML-** fájl hivatkozását a **központi telepítés** mellett a fájl letöltéséhez.
7. Szükség szerint módosítsa a port-hozzárendeléseket és a tároló nevét a YML fájlban.
8. Válassza a **központi telepítés** szövegmező jobb oldalán található **Másolás** ikont, hogy a `kubectl` parancsot mentse a vágólapra. 
9. Illessze be a parancsot a terminál (vagy a parancs) ablakába. Vegye figyelembe, hogy a parancs elvárja, hogy a letöltött környezeti fájl megtalálható legyen az aktuális könyvtárban.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Hajtsa végre a parancsot. A parancs arra utasítja a Kubernetes-fürtöt, hogy futtassa a tárolót a saját üzemeltetésű átjáró a Microsoft Container Registryból letöltött rendszerképének használatával, és konfigurálja a tárolót a HTTP (8080) és a HTTPS (443) portok megjelenítéséhez.
11. Futtassa az alábbi parancsot, és győződjön meg róla, hogy az átjáró Pod fut. Vegye figyelembe, hogy a pod neve eltérő lesz. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Futtassa az alábbi parancsot az átjáró szolgáltatás futásának vizsgálatához. Vegye figyelembe, hogy a szolgáltatás neve eltérő lesz. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Térjen vissza a Azure Portalhoz, és ellenőrizze, hogy az imént telepített átjáró csomópontja kifogástalan állapotú-e.

![Átjáró állapota](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> A <code>kubectl logs <gateway-pod-name></code> parancs használatával megtekintheti a saját üzemeltetésű átjáró naplójának pillanatképét.

## <a name="next-steps"></a>További lépések

* További információ a saját üzemeltetésű átjáróról: [Azure API Management saját üzemeltetésű átjáró – áttekintés](self-hosted-gateway-overview.md)
* További információ az [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes) -ről


