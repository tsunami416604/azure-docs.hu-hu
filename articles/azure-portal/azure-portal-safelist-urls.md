---
title: A tűzfal vagy a proxykiszolgáló Azure Portal URL-címeinek safelist
description: Adja hozzá ezeket az URL-címeket a proxykiszolgáló megkerüléséhez a Azure Portal és annak szolgáltatásaival való kommunikációhoz
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900662"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>A tűzfal vagy a proxykiszolgáló Azure Portal URL-címeinek safelist

A helyszíni biztonsági eszközöket a Azure Portal URL-címekre vonatkozó biztonsági korlátozások megkerülésére állíthatja be. Ez a konfiguráció növelheti a teljesítményt és a kapcsolatot a helyi vagy a széles körű hálózat és az Azure-felhő között.

A hálózati rendszergazdák gyakran proxykiszolgálót, tűzfalakat vagy más eszközöket helyeznek üzembe. Ezek az eszközök segítenek a felhasználók hozzáférésének biztosításában és felügyeletében. A felhasználók biztosítására tervezett szabályok időnként letilthatják vagy lelassítják az üzleti kapcsolatban álló internetes forgalmat. Ez a forgalom magában foglalja az Ön és az Azure közötti kommunikációt. A hálózat és a Azure Portal és a szolgáltatásai közötti kapcsolat optimalizálása érdekében javasoljuk, hogy Azure Portal URL-címeket adjon hozzá a safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>A proxy megkerülésének Azure Portal URL-címei

A Azure Portal safelist URL-végpontok kifejezetten az Azure-felhőre vonatkoznak, ahol a szervezet üzembe van helyezve. Ha engedélyezni szeretné a végpontok felé irányuló hálózati forgalmat a korlátozások megkerüléséhez, válassza ki a felhőt. Ezután adja hozzá az URL-címek listáját a proxykiszolgálóhoz vagy a tűzfalhoz.

#### <a name="public-cloudtabpublic-cloud"></a>[Nyilvános felhő](#tab/public-cloud)

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

#### <a name="us-government-cloudtabus-government-cloud"></a>[Egyesült államokbeli kormányzati felhő](#tab/us-government-cloud)

```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Kínai kormányzati felhő](#tab/china-government-cloud)

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
## <a name="next-steps"></a>Következő lépések

IP-címeket kell safelist? Töltse le Microsoft Azure Datacenter IP-címtartományok listáját a felhőhöz:

* [Világszerte](https://www.microsoft.com/download/details.aspx?id=56519)
* [Egyesült Államok kormánya](https://www.microsoft.com/download/details.aspx?id=57063)
* [Németország](https://www.microsoft.com/download/details.aspx?id=57064)
* [Kína](https://www.microsoft.com/download/details.aspx?id=57062)

Más Microsoft-szolgáltatások további URL-címeket és IP-címeket használnak a kapcsolódáshoz. A Microsoft 365-szolgáltatások hálózati kapcsolatának optimalizálásához tekintse meg [a hálózat beállítása az Office 365](/office365/enterprise/set-up-network-for-office-365)-hoz című témakört.
