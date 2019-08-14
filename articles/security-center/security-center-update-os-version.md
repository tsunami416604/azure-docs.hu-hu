---
title: Operációs rendszer verziójának frissítése Azure Security Centerban | Microsoft Docs
description: Ez a cikk bemutatja, hogyan implementálhatja a Azure Security Center ajánlás **frissítésének operációs rendszerének verzióját**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "60905862"
---
# <a name="update-os-version-in-azure-security-center"></a>Operációs rendszer verziójának frissítése Azure Security Center
A Cloud Servicesben a virtuális gépek (VM-EK) esetében Azure Security Center azt javasolja, hogy az operációs rendszer (OS) frissítése megtörténjen, ha újabb verzió érhető el.  Csak az üzemi tárolóhelyeken futó Cloud Services-webszolgáltatások és feldolgozói szerepkörök figyelhetők meg.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
> 
> 

## <a name="implement-the-recommendation"></a>A javaslat implementálása
1. A **javaslatok** panelen válassza az **operációs rendszer verziójának frissítése**elemet.
   ![Operációs rendszer verziójának frissítése][1]
2. Ekkor megnyílik a panel **frissítési operációs rendszerének verziója**. Az operációs rendszer verziójának frissítéséhez kövesse az ebben a panelen található lépéseket.

## <a name="see-also"></a>Lásd még
Ez a cikk bemutatja, hogyan valósítja meg az "operációs rendszer verziójának frissítése" Security Center ajánlást. A Cloud Services szolgáltatással kapcsolatos további információkért és a felhőalapú szolgáltatás operációsrendszer-verziójának frissítéséhez tekintse meg a következőt:

* [A Cloud Services áttekintése](../cloud-services/cloud-services-choose-me.md)
* [Felhőalapú szolgáltatás frissítése](../cloud-services/cloud-services-update-azure-service.md)
* [A Cloud Services Konfigurálása](../cloud-services/cloud-services-how-to-configure-portal.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági Hírek és információk beszerzése.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
