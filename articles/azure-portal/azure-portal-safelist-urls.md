---
title: Safelist a Azure Portal URL-címekkel | Microsoft Docs
description: Adja hozzá ezeket az URL-címeket a proxykiszolgáló megkerüléséhez a Azure Portal és annak szolgáltatásaival való kommunikációhoz
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667476"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>A tűzfal vagy a proxykiszolgáló Azure Portal URL-címeinek safelist

A helyi vagy a nagy területű hálózat és az Azure-felhő közötti jó teljesítmény és kapcsolat érdekében konfigurálja a helyszíni biztonsági eszközöket a Azure Portal URL-címekre vonatkozó biztonsági korlátozások megkerülésére. A hálózati rendszergazdák gyakran proxykiszolgálót, tűzfalakat vagy más eszközöket helyeznek üzembe, így biztosítva a felhasználók hozzáférését az internethez. A felhasználók elleni védelemmel ellátott szabályok azonban időnként letilthatják vagy lelassítják a megbízható üzleti forgalomhoz kapcsolódó internetes forgalmat, beleértve az Ön és az Azure közötti kommunikációt is. A hálózat és a Azure Portal és a szolgáltatásai közötti kapcsolat optimalizálása érdekében javasoljuk, hogy Azure Portal URL-címeket adjon hozzá a safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>A proxy megkerülésének Azure Portal URL-címei

Adja hozzá az alábbi URL-címeket a proxykiszolgálóhoz vagy a tűzfalhoz, hogy engedélyezze a végpontok számára a korlátozások megkerülését a hálózati forgalom számára:

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
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> A végpontok felé irányuló forgalom szabványos TCP-portokat használ a HTTP (80) és a HTTPS (443) számára.
>
>
## <a name="next-steps"></a>További lépések

* IP-címeket kell safelist? Töltse le az [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653)listáját.
* Más Microsoft-szolgáltatások további URL-címeket és IP-címeket használnak a kapcsolódáshoz. A Microsoft 365-szolgáltatások hálózati kapcsolatának optimalizálásához tekintse meg [a hálózat beállítása az Office 365](/office365/enterprise/set-up-network-for-office-365)-hoz című témakört.
