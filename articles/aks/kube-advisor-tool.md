---
title: Ellenőrizze a Kubernetes-üzembehelyezés az Azure-ban az ajánlott eljárások végrehajtásához
description: Ismerje meg, hogyan ellenőrizheti az üzembe helyezéseket az Azure Kubernetes Service kube-tanácsadó használatáról az ajánlott eljárások végrehajtása
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 01095ac4ed8e362f1a89a53b10b5da6a547feb57
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218634"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Kubernetes-ajánlott eljárások a fürtben lévő keresése

Nincsenek számos ajánlott eljárásokat, amelyek segítséget a legjobb teljesítmény és rugalmasság, az alkalmazások biztosításához a Kubernetes üzemelő példányok esetében. A kube-advisor eszköz segítségével keressen üzemelő példánya, amely nem a következő ezeket a javaslatokat.

## <a name="about-kube-advisor"></a>Kube-advisor szolgáltatásról

A [kube-advisor eszköz] [ kube-advisor-github] egy egyetlen tároló lehet a fürtön futnak. További információ a központi telepítések Kubernetes API-kiszolgálót és a javasolt javítások készletét adja vissza.

> [!NOTE]
> A kube-advisor eszköz támogatja a Microsoft legjobb történik. Hibák és javaslatok kell benyújtani a Githubon.

## <a name="running-kube-advisor"></a>Az advisor-kube fut

Az eszköz futtatásához egy konfigurált fürtön [szerepköralapú hozzáférés-vezérlés (RBAC)](aad-integration.md), az alábbi parancsok használatával. Az első parancs létrehoz egy Kubernetes-szolgáltatásfiókot. A második parancs az eszköz fut egy pod a szolgáltatási fiókot használva, és konfigurálja a pod a törlés után kilép. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Az RBAC nem használ, ha módon futtathatja a parancsot:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Néhány másodpercen belül megtekintheti az egy táblázat megtalálhatja a javítási lehetőségeket az üzemelő példányokhoz.

![Az advisor Kube kimenet](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Végrehajtott ellenőrzések

Az eszköz többféle Kubernetes ajánlott, amelyek mindegyike saját javasolt szervizelés érvényesíti.

### <a name="resource-requests-and-limits"></a>Erőforrás-kérelmek és korlátozások

Kubernetes támogatja meghatározása [erőforrás-kérelmek, és korlátozza a pod-specifikációk][kube-cpumem]. A kérelem határozza meg a minimális CPU és memória, a tároló futtatásához szükséges. A korlát határozza meg, a maximális CPU és memória forgalomként kell engedélyezni.

Alapértelmezés szerint nincs kérések vagy a korlátok a pod-specifikációk vannak beállítva. Ez a csomópont alatt overscheduled és a tárolók folyamatban fogy ki vezethet. A kube-advisor eszköz kiemeli a podok kérelmek nélkül, és a set-korlátok.

## <a name="cleaning-up"></a>Takarítás

Ha a fürt RBAC engedélyezve van, törölheti is a `ClusterRoleBinding` után az eszköz a következő parancs futtatása után:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Ha az eszköz egy fürtöt, amely nem az RBAC-t futtat, nem karbantartása nem szükséges.

## <a name="next-steps"></a>További lépések

- [Az Azure Kubernetes Service szolgáltatással kapcsolatos problémák elhárítása](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor