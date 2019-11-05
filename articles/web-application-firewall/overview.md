---
title: Az Azure webalkalmazási tűzfal bemutatása
description: Ez a cikk áttekintést nyújt az Azure webalkalmazási tűzfal (WAF) szolgáltatásról
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 7b43a6bdac254493da8693b55158e15746e76dc3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502043"
---
# <a name="what-is-azure-web-application-firewall"></a>Mi az Azure Webalkalmazási tűzfal?

A webalkalmazási tűzfal (WAF) lehetővé teszi a webalkalmazások központosított védelmét a gyakori támadások és biztonsági rések ellen. A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. Az SQL-injektálás és a helyek közötti parancsfájlkezelés a leggyakoribb támadások közé tartozik.

![A WAF áttekintése](media/overview/wafoverview.png)

Az ilyen támadások megelőzése az alkalmazás kódjában kihívást jelent. Az alkalmazás topológiájának több rétegében szigorú karbantartási, javítási és figyelési funkciókra lehet szükség. A központosított webalkalmazási tűzfal sokkal egyszerűbbé teszi A biztonsági felügyeletet. A WAF lehetővé teszi, hogy az alkalmazás-rendszergazdák jobban meglegyenek a fenyegetésekkel és a behatolásokkal szembeni védelem.

Az egyes webalkalmazások biztonságossá tétele helyett a WAF-megoldás gyorsabban reagálhat a biztonsági fenyegetésekre, ha központilag javít egy ismert biztonsági rést.

A WAF üzembe helyezhető az Azure Application Gateway és az Azure bejárati ajtó szolgáltatásával. A WAF jelenleg minden egyes szolgáltatáshoz testre szabott funkciókkal rendelkezik. Az egyes szolgáltatások WAF funkcióival kapcsolatos további információkért tekintse meg az egyes szolgáltatások áttekintését.

## <a name="next-steps"></a>További lépések

- A Application Gateway webalkalmazási tűzfallal kapcsolatos további információkért lásd: [webalkalmazási tűzfal az Azure Application Gateway](./ag/ag-overview.md).
- További információ a webalkalmazási tűzfalról az Azure bejárati ajtó szolgáltatásban: [webalkalmazási tűzfal az Azure bejárati ajtó szolgáltatásban](./afds/afds-overview.md).
