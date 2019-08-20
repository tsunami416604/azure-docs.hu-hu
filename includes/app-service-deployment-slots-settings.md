---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623707"
---
Ha másik üzembe helyezési pontról klónozott konfigurációt telepít, a klónozott konfiguráció szerkeszthető. Egyes konfigurációs elemek követik a tartalmat a swap (nem tárolóhely-specifikus), míg az egyéb konfigurációs elemek ugyanazon a tárolóhelyen maradnak a swap (tárolóhely-specifikus) után. Az alábbi listában a tárolóhelyek cseréjekor megjelenő beállítások láthatók.

**Felcserélt beállítások**:

* Általános beállítások, mint például a keretrendszer verziója, 32/64 bites, webes szoftvercsatornák
* Alkalmazásbeállítások (beállítható egy tárolóhelyre való ragasztásra)
* Kapcsolódási karakterláncok (beállítható egy tárolóhelyre való ragasztásra)
* Leírók leképezése
* Figyelési és diagnosztikai beállítások
* Nyilvános tanúsítványok
* Webjobs-tartalom
* Hibrid kapcsolatok *
* Virtuális hálózati integráció *
* Szolgáltatási végpontok *
* Azure Content Delivery Network *

A csillaggal (*) jelölt funkciók a tárolóhelyre való betöltésre vannak tervezve. 

**Nem felcserélt beállítások**:

* Közzétételi végpontok
* Egyéni tartománynevek
* Privát tanúsítványok és SSL-kötések
* Lépték beállításai
* Webjobs-ütemező
* IP-korlátozások
* Mindig bekapcsolva
* Protokoll beállításai (HTTPS, TLS-verzió, Ügyféltanúsítványok)
* Diagnosztikai napló beállításai
* Több eredetű erőforrás-megosztás (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->