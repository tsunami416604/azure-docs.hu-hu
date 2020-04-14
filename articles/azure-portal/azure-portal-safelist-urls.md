---
title: Safelist az Azure Portal URL-címeit a tűzfalon vagy a proxykiszolgálón
description: Adja hozzá ezeket az URL-címeket a proxykiszolgáló megkerüléséhez az Azure Portallal és annak szolgáltatásaival való kommunikációhoz
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 04/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 399c64c88e78079432fcf7c09dafd199da83358b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255048"
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
