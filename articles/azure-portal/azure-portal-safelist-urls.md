---
title: Safelist az Azure Portal URL-címeit a tűzfalon vagy a proxykiszolgálón
description: Adja hozzá ezeket az URL-címeket a proxykiszolgáló megkerüléséhez az Azure Portallal és annak szolgáltatásaival való kommunikációhoz
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900662"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist az Azure Portal URL-címeit a tűzfalon vagy a proxykiszolgálón

Konfigurálhatja a helyszíni biztonsági eszközöket, hogy megkerüljék az Azure Portal URL-címeire vonatkozó biztonsági korlátozásokat. Ez a konfiguráció javíthatja a helyi vagy nagy kiterjedésű hálózat és az Azure-felhő közötti teljesítményt és kapcsolatot.

A hálózati rendszergazdák gyakran telepítenek proxykiszolgálókat, tűzfalakat vagy más eszközöket. Ezek az eszközök segítenek a biztonságos és arról, hogy a felhasználók hogyan férnek hozzá az internethez. A felhasználók védelmét szolgáló szabályok néha blokkolhatják vagy lelassíthatják a jogszerű, üzleti vonatkozású internetes forgalmat. Ez a forgalom magában foglalja az Ön és az Azure közötti kommunikációt. A hálózat és az Azure Portal és szolgáltatásai közötti kapcsolat optimalizálása érdekében javasoljuk, hogy adja hozzá az Azure Portal URL-címeit a biztonságos listához.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Az Azure portal URL-címei a proxymegkerüléshez

Az Azure Portal biztonságos listájához tartozó URL-végpontok az Azure-felhőre vonatkoznak, ahol a szervezet telepítve van. Ha engedélyezni szeretné, hogy a hálózati forgalom ezekre a végpontokra megkerülje a korlátozásokat, válassza ki a felhőt. Ezután adja hozzá az URL-címek listáját a proxykiszolgálóhoz vagy a tűzfalhoz.

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

#### <a name="us-government-cloud"></a>[Az Egyesült Államok kormányzati felhője](#tab/us-government-cloud)

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

#### <a name="china-government-cloud"></a>[Kína kormány cloud](#tab/china-government-cloud)

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
> Az ezekre a végpontokra irányuló forgalom szabványos TCP-portokat használ http (80) és HTTPS (443) esetén.
>
>
## <a name="next-steps"></a>További lépések

Biztonságos listára kell tennie az IP-címeket? Töltse le a Microsoft Azure adatközpontOK IP-tartományainak listáját a felhőhöz:

* [Világszerte](https://www.microsoft.com/download/details.aspx?id=56519)
* [Az Egyesült Államok kormánya](https://www.microsoft.com/download/details.aspx?id=57063)
* [Németország](https://www.microsoft.com/download/details.aspx?id=57064)
* [Kína](https://www.microsoft.com/download/details.aspx?id=57062)

Más Microsoft-szolgáltatások további URL-címeket és IP-címeket használnak a kapcsolathoz. A Hálózati kapcsolat Optimalizálása a Microsoft 365-szolgáltatásokhoz a [Hálózat beállítása az Office 365-höz .For](/office365/enterprise/set-up-network-for-office-365)Optimize network connectivity for Microsoft 365 services, see Set Up your network for Office 365 .
