---
title: Saját üzemeltetésű Azure API Management átjáró üzembe helyezése a Kubernetes számára | Microsoft dokumentumok
description: Ismerje meg, hogyan helyezhet üzembe saját üzemeltetésű Azure API Management átjárót a Kubernetes számára
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513834"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Saját üzemeltetésű Azure API Management átjáró üzembe helyezése a Kubernetes számára

Ez a cikk a saját üzemeltetésű Azure API Management átjáró kubernetes-fürtbe való üzembe helyezésének lépéseit ismerteti.

> [!NOTE]
> A saját üzemeltetésű átjárófunkció előzetes verzióban érhető el. Az előzetes verzió során a saját üzemeltetésű átjáró csak a Fejlesztői és prémium szintű csomagokban érhető el további díj nélkül. A fejlesztői szint egyetlen saját üzemeltetésű átjáró-telepítésre korlátozódik.


## <a name="prerequisites"></a>Előfeltételek

- A következő rövid útmutató befejezése: [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md)
- Hozzon létre egy Kubernetes-fürtöt. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) egy jó lehetőség a fejlesztési és értékelési célokra. Éles számítási feladatokhoz használhatja [az Azure Kubernetes-szolgáltatást](https://azure.microsoft.com/services/kubernetes-service/) vagy egy Kubernetes-fürtöt egy idegen felhőben.
- [Átjáró-erőforrás kiépítése az API Management-példányban.](api-management-howto-provision-self-hosted-gateway.md)

## <a name="deploy-the-gateway-to-kubernetes"></a>Az átjáró üzembe helyezése a Kubernetes-re

1. Válassza **az Átjárók lehetőséget** a Beállítások **csoportban.**
2. Válassza ki a telepíteni kívánt átjáró-erőforrást.
3. Válassza a **Központi telepítés**lehetőséget.
4. Vegye figyelembe, hogy egy új jogkivonatot a **Jogkivonat** szövegmezőben automatikusan létrehozták az alapértelmezett **lejárati** és **titkos kulcs** értékek használatával. Szükség esetén módosítsa az egyiket vagy mindkettőt, és válassza a **Létrehozás** lehetőséget új jogkivonat létrehozásához.
5. Győződjön meg arról, hogy a **Kubernetes** ki van jelölve **a Központi telepítési parancsfájlok**csoportban.
6. A fájl letöltéséhez válassza<a **telepítés** melletti **átjárónév>.yml** fájlhivatkozást.
7. Szükség szerint módosítsa a portleképezéseket és a tároló nevét az yml fájlban.
8. A `kubectl` parancs vágólapra való mentéséhez jelölje ki a **Szöveghely telepítése** párbeszédpanel jobb oldalán található **másolási** ikont. 
9. Illessze be a parancsot a terminál (vagy parancs) ablakba. Ne feledje, hogy a parancs arra számít, hogy a letöltött környezeti fájl megjelenik az aktuális könyvtárban.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Hajtsa végre a parancsot. A parancs arra utasítja a Kubernetes-fürtöt, hogy futtassa a tárolót a Microsoft Container Registry rendszeréből letöltött saját üzemeltetésű átjáró lemezképének használatával, és konfigurálja a tárolót a HTTP (8080) és HTTPS (443) portok elérhetővé.
11. Futtassa az alábbi parancsot, és ellenőrizze, hogy az átjárópod fut-e. Vegye figyelembe, hogy a pod neve eltérő lesz. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Futtassa az alábbi parancsot, és ellenőrizze, hogy fut-e az átjárószolgáltatás. Ne feledje, hogy a szolgáltatás neve eltérő lesz. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Lépjen vissza az Azure Portalra, és ellenőrizze, hogy az imént üzembe helyezett átjárócsomópont kifogástalan állapotot jelent-e.

![átjáró állapota](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> A <code>kubectl logs <gateway-pod-name></code> parancs segítségével megtekintheti a saját üzemeltetésű átjárónapló pillanatképét.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a saját üzemeltetésű átjáróról, olvassa el az [Azure API Management saját üzemeltetésű átjáró – áttekintés című témakört.](self-hosted-gateway-overview.md)
* További információ az [Azure Kubernetes szolgáltatásról](https://docs.microsoft.com/azure/aks/intro-kubernetes)


