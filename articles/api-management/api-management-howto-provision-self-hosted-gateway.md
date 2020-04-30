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
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: 2870a654faad4e760a9d022488cb2c4c406cbeab
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203131"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Saját üzemeltetésű átjáró kiépítése az Azure API Management

Az átjáró erőforrásának az Azure API Management-példányban való kiépítése a saját üzemeltetésű átjáró üzembe helyezésének előfeltétele. Ez a cikk végigvezeti az átjáró-erőforrások API Managementban való kiépítésének lépésein.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Saját üzemeltetésű átjáró üzembe helyezése

1. Válassza ki az **átjárókat** a **Beállítások**területen.
2. Kattintson a **+Hozzáadás** gombra.
3. Adja meg az átjáró **nevét** és **régióját** .
> [!TIP]
> A **régió** adja meg az átjáró-erőforráshoz társítandó átjáró-csomópontok kívánt helyét. Szemantikailag egyenértékű az Azure-erőforrásokhoz társított hasonló tulajdonsággal, de tetszőleges karakterlánc-értéket rendelhet hozzá.

4. Igény szerint megadhatja az átjáró erőforrásának **leírását** .
5. Ha szeretné, az **+** **API** -k területen jelölje be az adott átjáró erőforráshoz tartozó API-k egy vagy többhöz való hozzárendelését.
> [!IMPORTANT]
> Alapértelmezés szerint a meglévő API-k egyike sem lesz társítva az új átjáró erőforráshoz. Ezért az új átjárón keresztül történő meghívására tett kísérletek a `404 Resource Not Found` válaszokat is eredményezik.

6. Kattintson a **Hozzáadás** parancsra.

Most az átjáró-erőforrás kiépítve lett a API Management-példányban. Folytathatja az átjáró üzembe helyezését.

## <a name="next-steps"></a>További lépések

* További információ a saját üzemeltetésű átjáróról: [Azure API Management saját üzemeltetésű átjáró – áttekintés](self-hosted-gateway-overview.md)
* További információ a saját üzemeltetésű [átjárók Kubernetes történő üzembe helyezéséről](how-to-deploy-self-hosted-gateway-kubernetes.md)
* További információ a saját üzemeltetésű [átjárók Docker-ben történő üzembe helyezéséről](how-to-deploy-self-hosted-gateway-docker.md)
