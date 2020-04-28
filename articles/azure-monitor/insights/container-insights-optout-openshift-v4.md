---
title: Az Azure és a Red Hat OpenShift v4-fürt figyelésének leállítása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állíthatja le az Azure Red Hat-OpenShift és a Red Hat OpenShift 4-es verziójú, valamint a Azure Monitor for containers szolgáltatással történő figyelését.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 768c4db8d72778b555a4f343cf2e23b8fa861991
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196438"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Az Azure-és Red Hat OpenShift v4-fürt figyelésének leállítása

Miután engedélyezte az Azure Red Hat-OpenShift és a Red Hat OpenShift 4. x verziójú fürt figyelését, leállíthatja a fürt figyelését Azure Monitor for containers használatával, ha úgy dönt, hogy már nem szeretné figyelni. Ez a cikk bemutatja, hogyan valósítható meg.  

## <a name="how-to-stop-monitoring-using-helm"></a>A figyelés leállítása a Helm használatával

1. A fürtön telepített containers Helm-diagram Azure Monitor azonosításához futtassa a következő Helm-parancsot.

    ```
    helm list
    ```

    A kimenet a következőhöz hasonló lesz:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers – a Release-1* a Azure monitor for containers esetében a Helm diagram kiadását jelenti.

2. A diagram kiadásának törléséhez futtassa a következő Helm-parancsot.

    `helm delete <releaseName>`

    Példa:

    `helm delete azmon-containers-release-1`

    Ezzel törli a kiadást a fürtből. A parancs futtatásával ellenőrizheti a `helm list` következőket:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A konfiguráció módosítása több percet is igénybe vehet. Mivel a Helm a törlése után is nyomon követi a kiadásokat, naplózhatja a fürt előzményeit, és visszavonhatja a kiadás törlését `helm rollback`is.

## <a name="next-steps"></a>További lépések

Ha a Log Analytics munkaterület csak a fürt figyelésének támogatására lett létrehozva, és már nincs rá szükség, manuálisan kell törölnie. Ha nem ismeri a munkaterület törlésének módját, tekintse meg [Az Azure log Analytics munkaterület törlése](../../log-analytics/log-analytics-manage-del-workspace.md)című témakört.
