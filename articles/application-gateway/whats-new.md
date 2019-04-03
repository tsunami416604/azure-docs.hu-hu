---
title: What's new in Azure Application Gateway
description: Ismerje meg, mi az új Azure Application gatewayjel, például a legújabb kibocsátási megjegyzések, az ismert problémák, hibajavításokat tartalmaz, elavult funkció, és a közelgő változásokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: f686c8ac53db2d128cf5bb20f252c547348e5ac7
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863100"
---
# <a name="whats-new-in-azure-application-gateway"></a>Újdonságok, az Azure Application Gateway?

Az Azure Application Gateway folyamatosan frissül. Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók

## <a name="new-features"></a>Új funkciók

|Szolgáltatás  |Leírás  |Közzététel dátuma  |
|---------|---------|---------|
|Fejléc CRUD/Újraírások     |Mostantól módosíthatja a HTTP-fejléceket. Lásd: [oktatóanyag: Application gateway létrehozása, és a HTTP-fejlécek újraírási](tutorial-http-header-rewrite-powershell.md) további információt.|2018. december|
|WAF konfigurálása és a kizárási lista     |Hozzáadtunk további beállítások segítségével konfigurálhatja a WAF és a vakriasztások számának csökkentése érdekében. Lásd: [webes méretű alkalmazások tűzfal kérelmekre vonatkozó korlátok és kizárási listák](application-gateway-waf-configuration.md) további információt.|2018. december|
|Automatikus méretezés, a zone redudancy, statikus virtuális IP-CÍMEK támogatásának előzetes verziójával     |A v2 termékváltozatú nincsenek például az automatikus skálázás és jobb teljesítmény érdekében több különböző fejlesztéseit. Lásd: [Mi az Azure Application Gateway?](overview.md#autoscaling-public-preview) további információt.|2018. szeptember|
|Kapcsolatkiürítés     |Kapcsolat kiürítése lehetővé teszi, hogy szabályosan tagok eltávolításához a háttérkészletek. További információkért lásd: [kapcsolat kiürítése](overview.md#connection-draining).|2018. szeptember|
|Egyéni hibalapok     |Egyéni hibalapok létrehozhat egy hibalap belül a webhelyek a többi formátumát. Ennek engedélyezéséhez tekintse meg a [Application Gateway létrehozása egyéni hibalapok](custom-error.md).|2018. szeptember|
|Metrikák fejlesztései     |Az Application Gateway továbbfejlesztett metrikákkal állapotát jobb áttekintést kaphat. Engedélyezheti a metrikákat az Application Gateway-en [háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway](application-gateway-diagnostics.md).|2018. június|

## <a name="known-issues"></a>Ismert problémák

- [A v2 szintű Termékváltozatot ismert problémái](application-gateway-autoscaling-zone-redundant.md#known-issues-and-limitations)

## <a name="next-steps"></a>További lépések

Az Azure Application Gateway kapcsolatos további információkért lásd: [Mi az Azure Application Gateway?](overview.md)