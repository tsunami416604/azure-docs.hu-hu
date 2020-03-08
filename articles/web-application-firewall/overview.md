---
title: Az Azure webalkalmazási tűzfal bemutatása
description: Ez a cikk áttekintést nyújt az Azure webalkalmazási tűzfal (WAF) szolgáltatásról
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 44bc8db5d8ada0378f8f9d0911ed398ba491d289
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851186"
---
# <a name="what-is-azure-web-application-firewall"></a>Mi az az Azure Web Application Firewall?

A Web Application Firewall (WAF) központosított védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen. A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. Az SQL-injektálás és a helyek közötti parancsfájlkezelés a leggyakoribb támadások közé tartozik.

![A WAF áttekintése](media/overview/wafoverview.png)

Az ilyen támadások megelőzése az alkalmazás kódjában kihívást jelent. Az alkalmazás topológiájának több rétegében szigorú karbantartási, javítási és figyelési funkciókra lehet szükség. A központosított webalkalmazási tűzfal sokkal egyszerűbbé teszi A biztonsági felügyeletet. A WAF lehetővé teszi, hogy az alkalmazás-rendszergazdák jobban meglegyenek a fenyegetésekkel és a behatolásokkal szembeni védelem.

Az egyes webalkalmazások biztonságossá tétele helyett a WAF-megoldás gyorsabban reagálhat a biztonsági fenyegetésekre, ha központilag javít egy ismert biztonsági rést.

## <a name="supported-services"></a>Támogatott szolgáltatások

A WAF üzembe helyezhető az [azure Application Gateway](../application-gateway/overview.md) és az [Azure bejárati ajtó szolgáltatásával](../frontdoor/front-door-overview.md). Mindkét szolgáltatás 7. rétegbeli (HTTP/S) terheléselosztó, de Application Gateway regionális szolgáltatás, és a bejárati ajtó egy globális szolgáltatás. A WAF az egyes szolgáltatásokhoz testreszabott funkciókat tartalmaz.

További információkért tekintse meg az egyes szolgáltatások WAF áttekintését.

## <a name="next-steps"></a>Következő lépések

- A Application Gateway webalkalmazási tűzfallal kapcsolatos további információkért lásd: [webalkalmazási tűzfal az Azure Application Gateway](./ag/ag-overview.md).
- A webalkalmazási tűzfallal kapcsolatos további információkért lásd: [webalkalmazási tűzfal az Azure bejárati ajtó szolgáltatásban](./afds/afds-overview.md).
