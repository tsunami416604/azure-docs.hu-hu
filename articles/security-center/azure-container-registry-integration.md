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
ms.openlocfilehash: 7601a5f8abefd88de0f9a3682341c5366eec3fb0
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431061"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry integráció a Security Center (előzetes verzió)

A Azure Container Registry (ACR) egy felügyelt, privát Docker beállításjegyzék-szolgáltatás, amely egy központi beállításjegyzékben tárolja és kezeli az Azure-beli központi telepítésekhez tartozó tároló lemezképeit. Ez a nyílt forráskódú Docker beállításjegyzék 2,0-es adatbázisán alapul.

Ha Azure Security Center standard szintű csomaggal rendelkezik, hozzáadhatók a Container nyilvántartók csomagja. Ez a választható funkció mélyebb betekintést nyújt az ARM-alapú nyilvántartásokban lévő rendszerképek biztonsági rései között. Engedélyezheti vagy letilthatja az előfizetés szintjén lévő köteget, hogy az előfizetéshez tartozó összes regisztrációs adatbázisra vonatkozzon. A szolgáltatás díját a [díjszabás lapon](security-center-pricing.md)látható módon számítjuk fel. A Container registrys csomag engedélyezésével biztosíthatja, hogy Security Center készen álljon a beállításjegyzékbe leküldeni kívánt rendszerképek vizsgálatára. 

Ha egy rendszerképet küld a beállításjegyzékbe, Security Center automatikusan megvizsgálja a képet. A rendszerkép vizsgálatának elindításához küldje le az adattárba.

Ha a vizsgálat befejeződik (általában körülbelül 10 percet követően), az eredmények a következőhöz hasonló Security Center-javaslatokban érhetők el:

[![minta Azure Security Center a Azure Container Registry (ACR) tárolt rendszerképben felderített biztonsági réseket](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Az integráció előnyei

A Security Center az előfizetésében található ARM-alapú ACR-nyilvántartásokat azonosítja, és zökkenőmentesen biztosítja a következőket:

* **Azure-natív sebezhetőségi vizsgálat** az összes leküldött Linux-lemezképhez. Security Center megvizsgálja a képet az iparág vezető sebezhetőségét vizsgáló gyártótól, a Qualys. Ez a natív megoldás alapértelmezés szerint zökkenőmentesen integrálható.

* **Biztonsági javaslatok** az ismert biztonsági réseket tartalmazó Linux-lemezképekhez. Security Center részletesen ismerteti az egyes jelentett biztonsági réseket és a súlyossági besorolást. Emellett útmutatást nyújt a beállításjegyzékbe leküldett egyes rendszerbiztonsági rések javításához.

![Azure Security Center és Azure Container Registry (ACR) – magas szintű áttekintés](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Security Center tárolójának biztonsági funkcióiról, tekintse meg a következő témakört:

* [Azure Security Center és tárolók biztonsága](container-security.md)

* [Integráció az Azure Kubernetes szolgáltatással](azure-kubernetes-service-integration.md)

* [Virtuális gépek védelme](security-center-virtual-machine-protection.md) – a Security Center javaslatainak ismertetése
