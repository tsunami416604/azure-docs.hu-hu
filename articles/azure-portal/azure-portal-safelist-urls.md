---
title: Az Azure Portal URL-címeinek megbízhatósági listára helyezése a tűzfalon vagy a proxykiszolgálón
description: Adja hozzá ezeket az URL-címeket a proxykiszolgáló megkerüléséhez a Azure Portal és annak szolgáltatásaival való kommunikációhoz
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 67ccf6e2f113c0ec72cfcbccedf88017e452ec4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361659"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Az Azure Portal URL-címeinek megbízhatósági listára helyezése a tűzfalon vagy a proxykiszolgálón

A helyszíni biztonsági eszközöket a Azure Portal URL-címekre vonatkozó biztonsági korlátozások megkerülésére állíthatja be. Ez a konfiguráció növelheti a teljesítményt és a kapcsolatot a helyi vagy a széles körű hálózat és az Azure-felhő között.

A hálózati rendszergazdák gyakran proxykiszolgálót, tűzfalakat vagy más eszközöket helyeznek üzembe. Ezek az eszközök segítenek a felhasználók hozzáférésének biztosításában és felügyeletében. A felhasználók biztosítására tervezett szabályok időnként letilthatják vagy lelassítják az üzleti kapcsolatban álló internetes forgalmat. Ez a forgalom magában foglalja az Ön és az Azure közötti kommunikációt. A hálózat és a Azure Portal és a szolgáltatásai közötti kapcsolat optimalizálása érdekében javasoljuk, hogy Azure Portal URL-címeket adjon hozzá a safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>A proxy megkerülésének Azure Portal URL-címei

A Azure Portal safelist URL-végpontok kifejezetten az Azure-felhőre vonatkoznak, ahol a szervezet üzembe van helyezve. Ha engedélyezni szeretné a végpontok felé irányuló hálózati forgalmat a korlátozások megkerüléséhez, válassza ki a felhőt. Ezután adja hozzá az URL-címek listáját a proxykiszolgálóhoz vagy a tűzfalhoz.

#### <a name="public-cloud"></a>[Nyilvános felhő](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Egyesült államokbeli kormányzati felhő](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Kínai kormányzati felhő](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> A végpontok felé irányuló forgalom szabványos TCP-portokat használ a HTTP (80) és a HTTPS (443) számára.
>
>
