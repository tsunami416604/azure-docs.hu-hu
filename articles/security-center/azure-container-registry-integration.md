---
title: Azure Security Center és Azure Container Registry
description: Ismerje meg a Azure Security Center integrációját Azure Container Registry
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 4cc88e7c04d10907a9a6386b1266eb8031d60926
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552678"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry integráció a Security Center (előzetes verzió)

A Azure Container Registry (ACR) egy felügyelt, privát Docker beállításjegyzék-szolgáltatás, amely egy központi beállításjegyzékben tárolja és kezeli az Azure-beli központi telepítésekhez tartozó tároló lemezképeit. Ez a nyílt forráskódú Docker beállításjegyzék 2,0-es adatbázisán alapul.

A beállításjegyzék és a lemezképek biztonsági réseinak mélyebb láthatósága érdekében a Azure Security Center Standard csomagjának felhasználói lehetővé teszik a választható tárolói kibocsátásiegység-forgalmi csomagokat. A szolgáltatás használatára vonatkozó költséget képarányban, nem pedig vizsgálaton keresztül számítjuk fel. További információt a [díjszabás](security-center-pricing.md) tartalmaz. Ha a köteg engedélyezve van, Security Center automatikusan megkeresi a beállításjegyzékben lévő lemezképeket, amikor egy rendszerképet küld a beállításjegyzékbe.

> [!NOTE]
> Security Center a beállításjegyzék első vizsgálata csak azt követően történik meg, hogy engedélyezve van-e a Container registrys csomag, és a rendszerképet leküldi a beállításjegyzékbe.

Ha a vizsgálat befejeződik (általában körülbelül 10 percet követően), az eredmények a következőhöz hasonló Security Center-javaslatokban érhetők el:

[![minta Azure Security Center a Azure Container Registry (ACR) tárolt rendszerképben felderített biztonsági réseket](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Az integráció előnyei

Security Center az ACR-jegyzékeket az előfizetésében, és zökkenőmentesen biztosítja a következőket:

* **Azure-natív sebezhetőségi vizsgálat** az összes leküldött Linux-lemezképhez. Security Center megvizsgálja a képet az iparág vezető sebezhetőségét vizsgáló gyártótól, a Qualys. Ez a natív megoldás alapértelmezés szerint zökkenőmentesen integrálható.

* **Biztonsági javaslatok** az ismert biztonsági réseket tartalmazó Linux-lemezképekhez. Security Center részletesen ismerteti az egyes jelentett biztonsági réseket és a súlyossági besorolást. Emellett útmutatást nyújt a beállításjegyzékbe leküldett egyes rendszerbiztonsági rések javításához.

![Azure Security Center és Azure Container Registry (ACR) – magas szintű áttekintés](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

* [Azure Security Center és tárolók biztonsága](container-security.md)

* [Integráció az Azure Kubernetes szolgáltatással](azure-kubernetes-service-integration.md)

* [Virtuális gépek védelme](security-center-virtual-machine-protection.md) – a Security Center javaslatainak ismertetése
