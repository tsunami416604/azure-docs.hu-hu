---
title: What's new in Azure Application Gateway
description: Ismerje meg, mi az új Azure Application gatewayjel, például a legújabb kibocsátási megjegyzések, az ismert problémák, hibajavításokat tartalmaz, elavult funkció, és a közelgő változásokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: cdf2a1a730be657b41c7a4b2daf2f178661394b4
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947109"
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
|Automatikus méretezés, a zone redudancy, statikus VIP-támogatás általánosan elérhető |Az újraírási fejléc mely támogatja az automatikus skálázást, a zone redudancy, javíthatja a teljesítményt, a statikus virtuális IP-címek, a Key Vault v2 Termékváltozat általánosan elérhetővé vált. Lásd: [Application Gateway az automatikus skálázás dokumentációja](application-gateway-autoscaling-zone-redundant.md). |Április 2019 |
|Key Vault-integráció |Az Application Gateway mostantól támogatja a Key Vault integration (a nyilvános előzetes verzió) a HTTPS-kompatibilis figyelői csatolt kiszolgálói tanúsítványok. Lásd: [Key Vault tanúsítványokkal rendelkező SSL-lezárást](key-vault-certs.md). |Április 2019 |
|Fejléc CRUD/Újraírások     |Mostantól módosíthatja a HTTP-fejléceket. Lásd: [oktatóanyag: Application gateway létrehozása, és a HTTP-fejlécek újraírási](tutorial-http-header-rewrite-powershell.md) további információt.|2018. december|
|WAF konfigurálása és a kizárási lista     |Hozzáadtunk további beállítások segítségével konfigurálhatja a WAF és a vakriasztások számának csökkentése érdekében. Lásd: [webes méretű alkalmazások tűzfal kérelmekre vonatkozó korlátok és kizárási listák](application-gateway-waf-configuration.md) további információt.|2018. december|
|Automatikus méretezés, a zone redudancy, statikus virtuális IP-CÍMEK támogatása      |A v2 termékváltozatú nincsenek például az automatikus skálázás és jobb teljesítmény érdekében több különböző fejlesztéseit. Lásd: [Mi az Azure Application Gateway?](overview.md) további információt.|2018. szeptember|
|Kapcsolatkiürítés     |Kapcsolat kiürítése lehetővé teszi, hogy szabályosan tagok eltávolításához a háttérkészletek. További információkért lásd: [kapcsolat kiürítése](overview.md#connection-draining).|2018. szeptember|
|Egyéni hibalapok     |Egyéni hibalapok létrehozhat egy hibalap belül a webhelyek a többi formátumát. Ennek engedélyezéséhez tekintse meg a [Application Gateway létrehozása egyéni hibalapok](custom-error.md).|2018. szeptember|
|Metrikák fejlesztései     |Az Application Gateway továbbfejlesztett metrikákkal állapotát jobb áttekintést kaphat. Engedélyezheti a metrikákat az Application Gateway-en [háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway](application-gateway-diagnostics.md).|2018. június|

## <a name="next-steps"></a>További lépések

Az Azure Application Gateway kapcsolatos további információkért lásd: [Mi az Azure Application Gateway?](overview.md)