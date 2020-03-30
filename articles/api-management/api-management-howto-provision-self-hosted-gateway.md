---
title: Saját üzemeltetésű átjáró kiépítése az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan építhet ki saját üzemeltetésű átjárót az Azure API Managementben.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: d33c5f75234ad7165a9062ecc3bb2a00d502f8c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075290"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Saját üzemeltetésű átjáró kiépítése az Azure API Managementben

Egy átjáró-erőforrás kiépítése az Azure API Management-példányban előfeltétele egy saját üzemeltetett átjáró üzembe helyezéséhez. Ez a cikk bemutatja az API-kezelés átjáró-erőforrás kiépítésének lépéseit.

> [!NOTE]
> A saját üzemeltetésű átjárófunkció előzetes verzióban érhető el. Az előzetes verzió során a saját üzemeltetésű átjáró csak a Fejlesztői és prémium szintű csomagokban érhető el további díj nélkül. A fejlesztői szint egyetlen saját üzemeltetésű átjáró-telepítésre korlátozódik.

## <a name="prerequisites"></a>Előfeltételek

A következő rövid útmutató befejezése: [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Saját üzemeltetésű átjáró üzembe helyezése

1. Válassza ki az **Átjárók elemet** a **Beállítások csoportban.**
2. Kattintson a **+Hozzáadás** gombra.
3. Adja meg az átjáró **nevét** és **régióját.**
> [!TIP]
> **A régió** az átjáró-erőforráshoz társított átjárócsomópontok tervezett helyét határozza meg. Szemantikailag egyenértékű bármely Azure-erőforráshoz társított hasonló tulajdonsággal, de tetszőleges karakterlánc-értékrendelhető hozzá.

4. Adja meg az átjáró erőforrás **leírását.**
5. Ha szükséges, **+** válassza az **API-k alatt** egy vagy több API-k társításához az átjáró erőforrás.
> [!TIP]
> Az API-k at az API **Beállítások** lapján társíthatja és eltávolíthatja egy átjáróról.

> [!IMPORTANT]
> Alapértelmezés szerint egyik meglévő API-k sem lesznek társítva az új átjáró-erőforráshoz. Ezért az új átjárón `404 Resource Not Found` keresztül i.

6. Kattintson a **Hozzáadás** gombra.

Most az átjáró-erőforrás ki van építve az API Management-példányban. Folytathatja az átjáró telepítését.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a saját üzemeltetésű átjáróról, olvassa el az [Azure API Management saját üzemeltetésű átjáró – áttekintés című témakört.](self-hosted-gateway-overview.md)
* További információ a saját [üzemeltetésű átjáró kubernetesi üzembe helyezéséről](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* További információ a saját [üzemeltetésű átjáró kitakarásáról a Dockerben](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
