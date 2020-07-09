---
title: Ajánlott eljárások az üzemelő példányok vizsgálatához
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan tekintheti meg az Azure Kubernetes Service-ben üzemelő példányok ajánlott eljárásainak megvalósítását az Kube-Advisor használatával
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 9dc5a38a05ef73863f85e4dbe92d52eb94b2715f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83773806"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Ajánlott Kubernetes-eljárások keresése a fürtben

Az Kubernetes üzemelő példányok esetében számos ajánlott eljárás szükséges, így biztosítható az alkalmazások legjobb teljesítménye és rugalmassága. Az Kube-Advisor eszközzel olyan központi telepítéseket kereshet, amelyek nem követik ezeket a javaslatokat.

## <a name="about-kube-advisor"></a>Tudnivalók a Kube-Advisor szolgáltatásról

A [Kube-Advisor eszköz][kube-advisor-github] egyetlen tároló, amely a fürtön való futtatásra lett tervezve. Lekérdezi a Kubernetes API-kiszolgálót az üzemelő példányokkal kapcsolatos információkért, és a javasolt fejlesztéseket adja vissza.

Az Kube-Advisor eszköz jelentést készíthet az erőforrás-kérésekről, valamint a Windows-alkalmazások és a Linux-alkalmazások PodSpecs hiányzó korlátairól, de a Kube-Advisor eszköznek egy linuxos Pod-on kell ütemeznie. Egy Pod-t úgy ütemezhet, hogy egy adott operációs rendszert futtató csomópont-készleten fusson a pod konfigurációjában a [csomópont-választó][k8s-node-selector] használatával.

> [!NOTE]
> A Kube-Advisor eszközt a Microsoft a legjobb erőfeszítésekkel támogatja. Problémákat és javaslatokat kell benyújtani a GitHubon.

## <a name="running-kube-advisor"></a>Kube-Advisor futtatása

Az eszköz [szerepköralapú hozzáférés-vezérléshez (RBAC)](azure-ad-integration.md)konfigurált fürtön való futtatásához használja a következő parancsokat. Az első parancs létrehoz egy Kubernetes szolgáltatásfiókot. A második parancs futtatja az eszközt az adott szolgáltatásfiókot használó Pod-ban, és a kilépést követően beállítja a pod-t a törléshez. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }" --namespace default
```

Ha nem használja a RBAC-t, a parancsot a következőképpen futtathatja:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Néhány másodpercen belül megjelenik egy táblázat, amely leírja az üzemelő példányok lehetséges fejlesztéseit.

![Kube – Advisor kimenet](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Végrehajtott ellenőrzések

Az eszköz számos Kubernetes ajánlott eljárást érvényesít, amelyek mindegyike saját javasolt szervizeléssel rendelkezik.

### <a name="resource-requests-and-limits"></a>Erőforrásigények és korlátozások

A Kubernetes támogatja [az erőforrás-kérelmek definiálását és a pod-specifikációk korlátozásait][kube-cpumem]. A kérelem meghatározza a tároló futtatásához szükséges minimális PROCESSZORt és memóriát. A korlát határozza meg a maximálisan engedélyezett PROCESSZORt és memóriát.

Alapértelmezés szerint a pod-specifikációk nincsenek megadva kérelmek vagy korlátok. Ez vezethet a csomópontok túlütemezett és a tárolók éheznek. A Kube-Advisor eszköz kiemeli a hüvelyeket kérelmek és korlátok nélkül.

## <a name="cleaning-up"></a>Takarítás

Ha a fürtön engedélyezve van a RBAC, a következő paranccsal törölheti az `ClusterRoleBinding` eszközt, miután futtatta azt:

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
