---
title: Mi az Azure Application Gateway?
description: Megtudhatja, hogyan kezelheti az alkalmazása webes forgalmát az Azure Application Gateway segítségével.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78302576"
---
# <a name="what-is-azure-application-gateway"></a>Mi az Azure Application Gateway?

Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. A hagyományos terheléselosztók az átviteli rétegen működnek (OSI 4. réteg – TCP és UDP), és a forrás IP-címe és portja alapján irányítják a forgalmat a célállomás IP-címére és portjára.

Az Application Gateway egy HTTP-kérelem további attribútumai, például az URI elérési útja vagy a gazdagépfejlécek alapján hozhat útválasztási döntéseket. A forgalmat például a bejövő URL-cím alapján irányíthatja. Tehát ha a bejövő URL-cím a `/images` kifejezést tartalmazza, a forgalmat adott, képekre konfigurált kiszolgálókra irányíthatja (azaz egy készletbe). Ha `/video` az URL-cím, hogy a forgalom van irányítva egy másik készlet, amely optimalizált videók.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ezt a fajta útválasztást alkalmazásrétegbeli (OSI 7. réteg) terheléselosztásnak nevezzük. Az Azure Application Gateway URL-alapú és egyéb útválasztásra is képes.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. Ha nagy teljesítményű, alacsony késleltetésű, Layer-4-es terheléselosztásra van szüksége, olvassa el [a Mi az Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Ha globális DNS-terheléselosztást keres, olvassa el [a Mi a Forgalomkezelő?](../traffic-manager/traffic-manager-overview.md) A végpontok között forgatókönyvek hasznos lehet ezek a megoldások kombinálása.
>
> Az Azure terheléselosztási beállítások összehasonlításáról [az Azure terheléselosztási lehetőségeinek áttekintése](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)című témakörben olvashat.

## <a name="features"></a>Szolgáltatások

Az Application Gateway szolgáltatásairól az [Azure Application Gateway szolgáltatásai című témakörben olvashat.](features.md)

## <a name="pricing-and-sla"></a>Árképzés és SLA

Az Alkalmazásátjáró díjszabási információiról az [Application Gateway díjszabása](https://azure.microsoft.com/pricing/details/application-gateway/)című témakörben talál.

Az Alkalmazásátjáró SLA-val kapcsolatos információiaz [Application Gateway SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)című témakörben találnak információt.

## <a name="next-steps"></a>További lépések

A követelményektől és a környezettől függően létrehozhat egy teszt alkalmazásátjárót az Azure Portal, az Azure PowerShell vagy az Azure CLI használatával.

- [Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal](quick-create-portal.md)
- [Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure PowerShell](quick-create-powershell.md)
- [Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI](quick-create-cli.md)
