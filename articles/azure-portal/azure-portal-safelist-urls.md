---
title: Safelist a Azure Portal URL-címekkel | Microsoft Docs
description: Adja hozzá ezeket az URL-címeket a proxykiszolgáló megkerüléséhez a Azure Portal és annak szolgáltatásaival való kommunikációhoz
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 9618d99495e65231de22eb719355e2ca75a17600
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218038"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>A tűzfal vagy a proxykiszolgáló Azure Portal URL-címeinek safelist

A helyi vagy a nagy területű hálózat és az Azure-felhő közötti jó teljesítmény és kapcsolat érdekében konfigurálja a helyszíni biztonsági eszközöket a Azure Portal URL-címekre vonatkozó biztonsági korlátozások megkerülésére. A hálózati rendszergazdák gyakran proxykiszolgálót, tűzfalakat vagy más eszközöket helyeznek üzembe, így biztosítva a felhasználók hozzáférését az internethez. A felhasználók elleni védelemmel ellátott szabályok azonban időnként letilthatják vagy lelassítják a megbízható üzleti forgalomhoz kapcsolódó internetes forgalmat, beleértve az Ön és az Azure közötti kommunikációt is. A hálózat és a Azure Portal és a szolgáltatásai közötti kapcsolat optimalizálása érdekében javasoljuk, hogy Azure Portal URL-címeket adjon hozzá a safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>A proxy megkerülésének Azure Portal URL-címei

A Azure Portal safelist URL-végpontok kifejezetten az Azure-felhőre vonatkoznak, ahol a szervezet üzembe van helyezve. Válassza ki a felhőt, majd adja hozzá az URL-címek listáját a proxykiszolgálóhoz vagy a tűzfalhoz, hogy engedélyezze a végpontok számára a korlátozások megkerülését a hálózati forgalom számára.

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

#### <a name="us-government-cloudtabus-government-cloud"></a>[EGYESÜLT ÁLLAMOK Kormányzati felhő](#tab/us-government-cloud)
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
## <a name="next-steps"></a>További lépések

IP-címeket kell safelist? Töltse le Microsoft Azure Datacenter IP-címtartományok listáját a felhőhöz:

* [Világszerte](https://www.microsoft.com/download/details.aspx?id=56519)
* [EGYESÜLT ÁLLAMOK Kormány](https://www.microsoft.com/download/details.aspx?id=57063)
* [Németország](https://www.microsoft.com/download/details.aspx?id=57064)
* [Kína](https://www.microsoft.com/download/details.aspx?id=57062)

Más Microsoft-szolgáltatások további URL-címeket és IP-címeket használnak a kapcsolódáshoz. A Microsoft 365-szolgáltatások hálózati kapcsolatának optimalizálásához tekintse meg [a hálózat beállítása az Office 365](/office365/enterprise/set-up-network-for-office-365)-hoz című témakört.
