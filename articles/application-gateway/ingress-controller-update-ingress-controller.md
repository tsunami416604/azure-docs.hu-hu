---
title: A be- és a be- és éleszti vezérlő frissítése a Helm segítségével
description: Ez a cikk az application gateway-be- és átjáró helm használatával történő frissítésével kapcsolatos információkat ismerteti.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795907"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Az Application Gateway bejövőforgalom-vezérlőjének frissítése a Helm használatával 

Az Azure Application Gateway Ingress Controller for Kubernetes (AGIC) az Azure Storage-ban üzemeltetett Helm-tárház használatával frissíthető.

A frissítési eljárás megkezdése előtt győződjön meg arról, hogy hozzáadta a szükséges tárházat:

- Tekintse meg a jelenleg hozzáadott Helm adattárak:

    ```bash
    helm repo list
    ```

- Adja hozzá az AGIC repo-t a következővel:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Frissítés

1. Frissítse az AGIC Helm adattárat a legújabb kiadáshoz:

    ```bash
    helm repo update
    ```

1. A diagram elérhető `application-gateway-kubernetes-ingress` verzióinak megtekintése:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Mintaválasz:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    A legfrissebb elérhető verzió a fenti listából:`0.7.0-rc1`

1. A jelenleg telepített Helm-diagramok megtekintése:

    ```bash
    helm list
    ```

    Mintaválasz:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    A helm diagram telepítése a fenti `odd-billygoat`mintaválaszból a . Ezt a nevet fogjuk használni a többi parancshoz. A tényleges központi telepítés neve valószínűleg eltérő lesz.

1. Frissítse a Helm központi telepítést egy új verzióra:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Visszagurítás

Ha a Helm központi telepítés sikertelen, visszaállíthatja egy korábbi kiadásra.

1. Az utolsó ismert kifogástalan kiadási szám beszerezése:

    ```bash
    helm history odd-billygoat
    ```

    Példa a kimenetre:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    A parancs mintakimenetéből úgy `helm history` tűnik, `odd-billygoat` hogy a mi verziónk utolsó sikeres telepítése`1`

1. Visszaállítás az utolsó sikeres verzióra:

    ```bash
    helm rollback odd-billygoat 1
    ```
