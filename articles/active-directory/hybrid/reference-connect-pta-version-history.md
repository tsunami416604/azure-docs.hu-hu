---
title: 'Azure AD átmenő hitelesítés: verziók kiadásának előzményei | Microsoft Docs'
description: Ez a cikk az Azure AD átmenő hitelesítési ügynök összes kiadását felsorolja
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786452"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD átmenő hitelesítési ügynök: verzió kiadási előzményei 
 
Az átmenő hitelesítést engedélyező, a helyszínen telepített ügynökök rendszeresen frissülnek, és új képességeket biztosítanak. Ez a cikk felsorolja az új funkciók bevezetésekor hozzáadott verziókat és szolgáltatásokat. Az áteresztő hitelesítési ügynökök automatikusan frissülnek, amikor megjelent egy új verzió. 

A kapcsolódó témakörök a következők: 

- [Felhasználói bejelentkezés az Azure AD átmenő hitelesítéssel](how-to-connect-pta.md) 
- [Az Azure AD átmenő hitelesítési ügynök telepítése](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Kiadás állapota 
1/22/2019: kiadva a letöltéshez  
### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok 
- Service Bus megbízható csatornák támogatása a kimenő kapcsolatok újabb csatlakozási rétegének hozzáadásához 
- A TLS 1,2 betartatása az ügynök regisztrálása során 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Kiadás állapota 
4/10/2018: kiadva a letöltéshez  
### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok 
- Webes szoftvercsatorna-kapcsolat támogatása 
- A TLS 1,2 beállítása az ügynök alapértelmezett protokollja 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Kiadás állapota 
1/31/2018: kiadva a letöltéshez  
### <a name="fixed-issues"></a>Hibák kijavítva: 

- Kijavítva egy olyan hibát, amely valamilyen memóriavesztés okozta az ügynököt. 
- Frissült a Azure Service Bus verziója, amely tartalmazza az összekötő időtúllépési hibáinak elhárítását. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Kiadás állapota 
11/26/2017: kiadva a letöltéshez  
### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok 
- WebSocket-alapú kapcsolatok támogatása az ügynök és az Azure AD szolgáltatások között a kapcsolat rugalmasságának növelése érdekében 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Kiadás állapota 
11/25/2017: kiadva a letöltéshez  
### <a name="fixed-issues"></a>Hibák kijavítva: 
- Az alapértelmezett proxy-forgatókönyvek DNS-gyorsítótárával kapcsolatos kijavított hibák 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Kiadás állapota 
10/17/2017: kiadva a letöltéshez  
### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok 
- DNS-gyorsítótárbeli funkció hozzáadva a kimenő kapcsolatokhoz a DNS-hibák rugalmasságának hozzáadásához 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Kiadás állapota 
08/31/2017: kiadva a letöltéshez  
### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok 
- Az Azure AD átmenő hitelesítési ügynök GA-verziója 

## <a name="next-steps"></a>Következő lépések

- [Felhasználói bejelentkezés az Azure Active Directory átmenő hitelesítésével](how-to-connect-pta.md)