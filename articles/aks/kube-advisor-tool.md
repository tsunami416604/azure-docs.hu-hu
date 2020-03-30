---
title: Ellenőrizze a Kubernetes-telepítéseket az Azure-ban az ajánlott eljárások megvalósításához
description: Ismerje meg, hogyan ellenőrizheti az azure Kubernetes-szolgáltatás ban az azure Kubernetes-szolgáltatásban a kube-advisor használatával telepített telepítések gyakorlati bevált gyakorlatainak megvalósítását
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 29ea7dba1df8bc7c68e3d17563a51b784ce4a561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595433"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Ajánlott Kubernetes-eljárások keresése a fürtben

Számos ajánlott eljárás, amelyet követnie kell a Kubernetes-telepítések az alkalmazások legjobb teljesítményének és rugalmasságának biztosítása érdekében. A kube-advisor eszközzel megkeresheti azokat a központi telepítéseket, amelyek nem követik ezeket a javaslatokat.

## <a name="about-kube-advisor"></a>Körülbelül kube-tanácsadó

A [kube-advisor eszköz][kube-advisor-github] egyetlen tároló, amelyet a fürtön való futtatásra terveztek. Lekérdezi a Kubernetes API-kiszolgáló a központi telepítések információkat, és visszaadja a javasolt fejlesztések egy sor javasolt.

A kube-advisor eszköz jelentést tehet az erőforrás-kérelemről, és a PodSpecs for Windows alkalmazások, valamint a Linux-alkalmazások hiányzó korlátairól, de maga a kube-advisor eszköz egy Linux podon kell ütemezve. Ütemezheti a pod egy adott operációs rendszerrel rendelkező csomópontkészleten a pod konfigurációjában lévő [csomópontválasztó][k8s-node-selector] használatával.

> [!NOTE]
> A kube-advisor eszközt a Microsoft a lehető legjobb anamtalán támogatja. Problémák at és javaslatokat kell benyújtani a GitHubon.

## <a name="running-kube-advisor"></a>Futás kube-tanácsadó

Ha az eszközt olyan fürtön szeretné futtatni, amely [szerepköralapú hozzáférés-vezérlésre (RBAC)](azure-ad-integration.md)van konfigurálva, a következő parancsokkal. Az első parancs létrehoz egy Kubernetes szolgáltatásfiókot. A második parancs futtatja az eszközt egy pod használatával, hogy a szolgáltatásfiók és konfigurálja a pod törlésután kilép. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Ha nem használja az RBAC-ot, a parancs futtatásához a következőképpen:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Néhány másodpercen belül meg kell jelennie egy táblázatot, amely ismerteti a központi telepítések lehetséges fejlesztéseit.

![Kube-tanácsadó kimenet](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Elvégzett ellenőrzések

Az eszköz több Kubernetes ajánlott eljárását ellenőrzi, mindegyik saját javasolt szervizeléssel.

### <a name="resource-requests-and-limits"></a>Erőforrásigények és korlátozások

A Kubernetes támogatja az [erőforrás-kérelmek és a pod specifikációk korlátozásának meghatározását.][kube-cpumem] A kérelem határozza meg a minimális CPU és a memória futtatásához szükséges a tároló. A korlát határozza meg a maximális CPU és a memória, amely engedélyezni kell.

Alapértelmezés szerint nincs enek kérés vagy korlát pod specifikációk. Ez ahhoz vezethet, hogy a csomópontok túlütemezettak, és a tárolók éhen halnak. A kube-advisor eszköz kérések és korlátok nélkül emeli ki a podokat.

## <a name="cleaning-up"></a>Takarítás

Ha a fürtrBAC engedélyezve van, `ClusterRoleBinding` az eszköz futtatása után a következő paranccsal is megtisztíthatja a következő parancsot:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Ha az eszközt olyan fürtön futtatja, amely nem RBAC-kompatibilis, nincs szükség karbantartásra.

## <a name="next-steps"></a>További lépések

- [Az Azure Kubernetes szolgáltatással kapcsolatos problémák elhárítása](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors