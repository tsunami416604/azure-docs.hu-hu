---
title: Az Azure Portalon Safelist URL-címek |} A Microsoft Docs
description: Az URL-címek hozzáadása az Azure portal és a hozzá tartozó szolgáltatások folytatott kommunikációhoz proxykiszolgáló megkerülése
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305049"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Az Azure Portalon Safelist a tűzfal vagy proxy-kiszolgáló URL-címek

A jó teljesítmény és a helyi vagy a nagy kiterjedésű hálózat és az Azure-felhő közötti kapcsolatot, konfigurálja a helyszíni biztonsági eszközök az Azure Portalra vonatkozó biztonsági korlátozások megkerülésére URL-címeket. A hálózati rendszergazdák gyakran üzembe helyezése, proxy kiszolgálók, tűzfalak, vagy más eszközök, biztonságos és szabályozható, hogy a felhasználók hozzáférnek az internethez. Felhasználók védelmét szolgálja szabályok azonban néha blokkolhatják vagy jogszerű üzleti internetes forgalmat, többek között, és az Azure közötti kommunikáció lelassulnak. Optimalizálható a hálózat és az Azure portal és a hozzá tartozó szolgáltatások közötti kapcsolatot, javasoljuk, hogy az Azure portál hozzáadása a safelist URL-címmel.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Az Azure portal URL-címek Proxy megkerülése

Az alábbi listában szereplő URL-címek hozzáadása a proxykiszolgálót vagy tűzfalat, hogy engedélyezze ezeket a végpontokat,-korlátozások megkerülésére irányuló hálózati forgalom:

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.wpc.azureedge.net

> [!NOTE]
> Ezen végpontokra érkező forgalom szabványos TCP-portot (80-as) HTTP a protokolu HTTPS (443) használja.
>
>
## <a name="next-steps"></a>További lépések

* IP-címek safelist kell? Töltse le a [Microsoft Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653).
* Egyéb Microsoft szolgáltatások használatát további URL-címek és IP-címek a hálózati kapcsolatot. Optimális hálózati kapcsolathoz a Microsoft 365-szolgáltatásokhoz, lásd: [a hálózat beállítása az Office 365-höz](/office365/enterprise/set-up-network-for-office-365).
