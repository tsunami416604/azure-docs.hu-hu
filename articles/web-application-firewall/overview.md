---
title: Bevezetés az Azure webalkalmazás-tűzfalba
description: Ez a cikk áttekintést nyújt az Azure Web Application Firewall (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79475787"
---
# <a name="what-is-azure-web-application-firewall"></a>Mi az az Azure Web Application Firewall?

A Web Application Firewall (WAF) központosított védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen. A webalkalmazásokat egyre inkább rosszindulatú támadások célozzák meg, amelyek kihasználják az általánosan ismert biztonsági réseket. Az SQL-injektálás és a helyek közötti parancsfájlok a leggyakoribb támadások közé tartoznak.

![WAF – áttekintés](media/overview/wafoverview.png)

Az ilyen támadások megelőzése az alkalmazáskódban kihívást jelent. Szigorú karbantartást, javítást és figyelést igényelhet az alkalmazás topológia több rétegében. A központi webalkalmazás-tűzfal sokkal egyszerűbbé teszi a biztonságkezelést. A WAF emellett jobb védelmet nyújt az alkalmazásgazdáknak a fenyegetések és behatolások elleni védelemre.

A WAF-megoldások gyorsabban reagálhatnak a biztonsági fenyegetésekre, ha központilag javítegy ismert biztonsági rést, ahelyett, hogy minden egyes webalkalmazást biztosítanának.

## <a name="supported-service"></a>Támogatott szolgáltatás

A WAF telepíthető az Azure Application Gateway, az Azure Front Door és az Azure Content Delivery Network (CDN) szolgáltatással a Microsofttól. Waf az Azure CDN jelenleg nyilvános előzetes verzió alatt.  A WAF olyan funkciókkal rendelkezik, amelyek az egyes szolgáltatásokhoz vannak testreszabva. Az egyes szolgáltatások WAF-szolgáltatásairól az egyes szolgáltatások áttekintésében olvashat bővebben.

## <a name="next-steps"></a>További lépések

- Az Alkalmazásátjáró webalkalmazás-tűzfaláról további információt az [Azure Application Gateway webalkalmazás-tűzfalcímű témakörben talál.](./ag/ag-overview.md)
- Az Azure Bejárati ajtaján található webalkalmazás-tűzfalról további információt [a WebApplication Firewall on Azure Front Door Service című](./afds/afds-overview.md)témakörben talál.
- Az Azure CDN-szolgáltatás webalkalmazás-tűzfaláról további információt [az Azure CDN-szolgáltatás webalkalmazás-tűzfala című témakörben](./cdn/cdn-overview.md) talál.
