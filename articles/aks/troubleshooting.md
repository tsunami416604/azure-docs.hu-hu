---
title: Gyakori Azure Kubernetes Service-problémák hibaelhárítása
description: Útmutató a gyakori problémák elhárítására Azure Kubernetes Service (AKS) használata esetén
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 9f082c5f198ebd7123058bd250d3fef55494d553
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287539"
---
# <a name="aks-troubleshooting"></a>AKS-hibaelhárítás
Amikor hoz létre, vagy a kezelő az AKS-fürtök, előfordulhat, hogy időnként problémák merülnek fel. Ez a cikk részletesen néhány gyakori hibák és hibaelhárítási lépéseket.

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>Általánosságban elmondható, hol található információ a Kubernetes hibáinak?

[Itt] (https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/) a kubernetes-fürtök hibaelhárítási hivatalos hivatkozás.
[Itt](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) egy hibaelhárítási útmutató egy körül hibaelhárítási podok, csomópontok, fürtök és egyéb Microsoft-mérnök által közzétett mutató hivatkozás.

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>A kvótatúllépési hiba kapok létrehozása vagy frissítése során. Mit tegyek? 

Meg kell kérnie a magok [Itt](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>Mi az a maximális podok csomópontonkénti az aks-ben?

A csomópontonkénti maximális podok vannak állítva 30 alapértelmezés szerint ha telepít egy AKS-fürtöt az Azure Portalon.
A csomópontonkénti maximális podok vannak állítva 110 alapértelmezés szerint ha telepít egy AKS-fürtöt az Azure CLI-ben. (Győződjön meg arról, az Azure CLI legújabb verziójának használ). Ez az alapértelmezett beállítás módosítható a – maximális-csomópontok-per-pod jelzőt a az aks create paranccsal.

### <a name="i-am-getting-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>"InsufficientSubnetSize" hiba kapok speciális hálózatkezelés az AKS-fürt üzembe helyezése során. Mit tegyek?

Az egyéni VNET lehetőséget választja, a hálózatkezelés során AKS hoz létre az Azure CNI IP-Címkezelő szolgál. AKS-fürt a csomópontok száma 1 és 100 közötti bárhol lehet. 2 alapján) felett az alhálózat mérete nagyobb, mint a csomópontok száma és a maximális pod / alhálózat csomópontméret szorzatát kell > száma a fürtben található csomópontok a * podok csomópontonkénti maximális száma.

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>A pod elakadt "CrashLoopBackOff" módban. Mit tegyek?

Előfordulhat, hogy a pod folyamatban elakadt ebben a módban a különböző okok miatt. Érdemes megvizsgáljuk a 
* A pod maga használatával `kubectl describe pod <pod-name>`
* A naplók használatával  `kubectl log <pod-name>`

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>A tapasztalataimat szeretném RBAC ahhoz, hogy egy meglévő fürt. Állapítható meg velem hogyan tennem?

Sajnos engedélyezése az RBAC a meglévő fürtökön jelenleg nem támogatott. Szüksége lesz, explicit módon az új fürtök létrehozásához. Ha a parancssori Felületet használja, az RBAC alapértelmezés szerint engedélyezve van, mivel az AKS-portálon érhető el egy váltógomb az engedélyezéshez munkafolyamat létrehozásához.

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>Létrehozott egy fürtöt az Azure CLI használatával az alapértelmezett értékeket, vagy az Azure Portalon az RBAC engedélyezve van, és számos figyelmeztetések a kubernetes irányítópultot. Az irányítópulton, mielőtt bármilyen figyelmeztetés nélkül együttműködni. Mit tegyek?

Az irányítópult figyelmeztetés oka, hogy mostantól támogatják az RBAC'ed, és hozzáférést alapértelmezés szerint le van tiltva. Ez a megközelítés számít általában célszerű, mivel az alapértelmezett kitettség az irányítópult a fürt minden felhasználó számára a biztonsági fenyegetések vezethet. Ha továbbra is engedélyezni szeretné az irányítópulton, kövesse a [blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) engedélyezze azt.

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>Nem lehet csatlakozni az irányítópult szerepkörömhöz. Mit tegyek?

A legegyszerűbben úgy, hogy hozzáférhessen a szolgáltatáshoz a fürtön kívül a kubectl-proxyval a Kubernetes API-kiszolgálóhoz a localhost port 8001 proxykiszolgáló kérelmeket fogja futtatni. Itt a apiserver is a szolgáltatás proxy: http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#! / csomópont? névtér = default

Ha nem látja a kubernetes-irányítópultot, majd ellenőrizze a kube-proxy pod futásának a kube rendszer névtérben. Ha nem futó állapotban, törölje a pod, és újra fog.

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Tudok nem kérhetők le a Kubectl-naplók használatával a naplókat, vagy nem tud kapcsolódni az API-t server első az "kiszolgálótól érkező hiba: hiba hangtárcsázás háttér: tárcsázza a tcp...". Mit tegyek?

Győződjön meg róla, hogy az alapértelmezett NSG-t nem áll módosítás és a 22-es port nyitva, az API-kiszolgálóhoz való csatlakozáshoz. Ellenőrizze, hogy a tunnelfront pod fut-e a kube rendszer névtérben. Nem érhető el, ha kényszerített törlése, és újra fog.

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>I frissítésével, vagy méretezheti a tapasztalataimat és érkeznek meg hozzám a "message": "" imageReference"tulajdonság módosítása nem engedélyezett." Hiba.  Hogyan lehet kijavítani a hibát a probléma?

Ez a hiba a bevezetés, mert az ügynökcsomópontok az AKS-fürtben lévő címkéket módosította lehetőség. Módosítása és törlése a címkék és egyéb tulajdonságait a MC_ * erőforráscsoportban lévő erőforrásokat váratlan eredményekhez vezethet. A szolgáltatási szint Célkitűzésének az erőforrások mellett az MC_ * az AKS-fürtöt a módosítása működésképtelenné válik.


