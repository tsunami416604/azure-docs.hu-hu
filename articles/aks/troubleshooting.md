---
title: Gyakori Azure Kubernetes Service-problémák hibaelhárítása
description: Útmutató a gyakori problémák elhárítására Azure Kubernetes Service (AKS) használata esetén
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 17f6971cfa2dcd8c8988edc063c89859abec5367
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468835"
---
# <a name="aks-troubleshooting"></a>AKS-hibaelhárítás

Amikor hoz létre, vagy az Azure Kubernetes Service (AKS)-fürtök kezelése, előfordulhat, hogy időnként tapasztal. Ez a cikk részletesen néhány gyakori probléma és a hibaelhárítási lépéseket.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Általánosságban elmondható, hol található információ a Kubernetes hibakeresésével?

Próbálja ki a [hivatalos útmutató a Kubernetes-fürtök hibaelhárítási](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Emellett van egy [hibaelhárítási útmutató](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), egy Microsoft-mérnök podok, csomópontok, fürtök és egyéb funkciókkal kapcsolatos hibaelhárításhoz tett közzé.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Egy "kvóta túllépve" hiba kapok létrehozása vagy frissítése során. Mit tegyek? 

Kell [magok kérelem](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Mi az a maximális podok száma csomópontonként beállítás az aks-ben?

A maximális podok száma csomópontonként értéke alapértelmezés szerint 30, ha telepít egy AKS-fürtöt az Azure Portalon.
A maximális podok száma csomópontonként értéke 110 alapértelmezés szerint ha telepít egy AKS-fürtöt az Azure CLI-ben. (Ügyeljen rá, hogy az Azure CLI legújabb verzióját használja). Ez az alapértelmezett beállítás használatával módosítható a `–-max-pods` a jelzőt a `az aks create` parancsot.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>InsufficientSubnetSize hibaüzenetet kapok speciális hálózatkezelés az AKS-fürt üzembe helyezése során. Mit tegyek?

Az egyéni Azure Virtual Network beállítást hálózati AKS létrehozása során, az Azure Container hálózati adapter (CNI) IP-címkezelés (IPAM) használják. AKS-fürt a csomópontok száma 1 és 100 közötti bárhol lehet. Az előző szakaszban alapján, az alhálózat méretét nagyobbnak kell lennie a termék a csomópontok és a csomópontonkénti maximális podok számát. A kapcsolat ezzel a módszerrel lehet kifejezni: alhálózat méretét > a fürtben található csomópontok száma * csomópontonkénti maximális podok.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>A pod CrashLoopBackOff módban elakadt. Mit tegyek?

Előfordulhat, hogy a pod folyamatban elakadt ebben a módban a különböző okok miatt. Az nézhet ki:

* Maga a pod használatával `kubectl describe pod <pod-name>`.
* A naplók segítségével `kubectl log <pod-name>`.

A pod-problémák elhárításáról további információk: [-alkalmazások hibakeresését](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>A tapasztalataimat szeretném RBAC ahhoz, hogy egy meglévő fürt. Hogyan tehetem ezt?

Sajnos engedélyezése szerepköralapú hozzáférés-vezérlés (RBAC) a meglévő fürtökön nem támogatott. Explicit módon létre kell hoznia az új fürtök. Ha a parancssori Felületet használja, az RBAC alapértelmezés szerint engedélyezve van. Az AKS portal használatakor egy váltógomb engedélyezése az RBAC a létrehozási munkafolyamat érhető el.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Az RBAC engedélyezve van az alapértelmezett értékeket, vagy az Azure Portalon az Azure CLI használatával létrehozott egy fürtöt, és most már sok figyelmeztetések jelennek meg a Kubernetes-irányítópultot. Az Irányítópult segítségével megvalósítható, ha figyelmeztetéseket. Mit tegyek?

A figyelmeztetések az irányítópulton az oka, hogy a fürt engedélyezve van az RBAC, és hozzáférést alapértelmezés szerint le van tiltva. Ez a megközelítés általában célszerű, mert az alapértelmezett kitettség az irányítópult a fürt minden felhasználó számára a biztonsági fenyegetések vezethet. Ha továbbra is engedélyezni szeretné az irányítópulton, kövesse a [ebben a blogbejegyzésben](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nem lehet csatlakozni az irányítópulton. Mit tegyek?

A legegyszerűbben úgy, hogy hozzáférhessen a szolgáltatáshoz a fürtön kívül, hogy futtassa `kubectl proxy`, mely proxyk kéréseket küldeni a a localhost-8001 a Kubernetes API-kiszolgálóhoz. Itt az API-kiszolgáló is a szolgáltatás proxy: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Ha nem látja a Kubernetes-irányítópult, ellenőrizze-e a `kube-proxy` pod fut a `kube-system` névtér. Ha nem futó állapotban, törölje a pod, és újra fog.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Naplók nem jelenik meg a kubectl-naplók használatával, vagy nem tud kapcsolódni az API-kiszolgálóhoz. Érkeznek meg hozzám a "kiszolgálótól érkező hiba: hiba hangtárcsázás háttér: tárcsázza a tcp..." Mit tegyek?

Győződjön meg arról, hogy az alapértelmezett hálózati biztonsági csoport (NSG) nem módosul, és, hogy 22-es port meg nyitva, az API-kiszolgálóhoz való csatlakozáshoz. Ellenőrizze, hogy a `tunnelfront` futtatja a pod a `kube-system` névtér. Ha nem, a pod és kényszerített törlése újraindul.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-problem"></a>I frissítésével, vagy méretezheti a tapasztalataimat és érkeznek meg hozzám a "üzenet: Hiba a "ImageReference" tulajdonság módosítása nem engedélyezett".  Hogyan lehet kijavítani a hibát a probléma?

Előfordulhat, hogy lehet első ezt a hibát, mert az ügynökcsomópontok az AKS-fürtben lévő címkéket módosította. Módosítása és törlése a címkék és egyéb tulajdonságait a MC_ * erőforráscsoportban lévő erőforrásokat váratlan eredményekhez vezethet. Az AKS MC_ * tartozó az erőforrások módosítását a fürt a szolgáltatásiszint-célkitűzés (SLO) működésképtelenné válik.
