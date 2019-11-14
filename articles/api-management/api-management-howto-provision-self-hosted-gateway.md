---
title: Saját üzemeltetésű átjáró kiépítése az Azure API Managementban | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy saját üzemeltetésű átjárót az Azure API Managementban.
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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075290"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Saját üzemeltetésű átjáró kiépítése az Azure API Management

Az átjáró erőforrásának az Azure API Management-példányban való kiépítése a saját üzemeltetésű átjáró üzembe helyezésének előfeltétele. Ez a cikk végigvezeti az átjáró-erőforrások API Managementban való kiépítésének lépésein.

> [!NOTE]
> A saját üzemeltetésű átjáró funkció előzetes verzióban érhető el. Az előzetes verzió ideje alatt a saját üzemeltetésű átjáró csak a fejlesztői és prémium szinteken érhető el, díjmentesen. A fejlesztői réteg egyetlen saját üzemeltetésű átjáróra korlátozódik.

## <a name="prerequisites"></a>Előfeltételek

Végezze el a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Saját üzemeltetésű átjáró üzembe helyezése

1. Válassza ki az **átjárókat** a **Beállítások**területen.
2. Kattintson a **+Hozzáadás** gombra.
3. Adja meg az átjáró **nevét** és **régióját** .
> [!TIP]
> A **régió** adja meg az átjáró-erőforráshoz társítandó átjáró-csomópontok kívánt helyét. Szemantikailag egyenértékű az Azure-erőforrásokhoz társított hasonló tulajdonsággal, de tetszőleges karakterlánc-értéket rendelhet hozzá.

4. Igény szerint megadhatja az átjáró erőforrásának **leírását** .
5. Ha szeretné, az **API** -k területen válassza a **+** lehetőséget, hogy egy vagy több API-t társítson ehhez az átjáró-erőforráshoz.
> [!TIP]
> Az API **Beállítások** lapján hozzárendelhet és eltávolíthat API-kat az átjáróról.

> [!IMPORTANT]
> Alapértelmezés szerint a meglévő API-k egyike sem lesz társítva az új átjáró erőforráshoz. Ezért az új átjárón keresztül történő meghívására tett kísérletek `404 Resource Not Found` választ fognak eredményezni.

6. Kattintson az **Hozzáadás** parancsra.

Most az átjáró-erőforrás kiépítve lett a API Management-példányban. Folytathatja az átjáró üzembe helyezését.

## <a name="next-steps"></a>További lépések

* További információ a saját üzemeltetésű átjáróról: [Azure API Management saját üzemeltetésű átjáró – áttekintés](self-hosted-gateway-overview.md)
* További információ a saját üzemeltetésű [átjárók Kubernetes történő üzembe helyezéséről](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
* További információ a saját üzemeltetésű [átjárók Docker-ben történő üzembe helyezéséről](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
