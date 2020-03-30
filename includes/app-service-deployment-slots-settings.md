---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129693"
---
Ha egy másik központi telepítési helyről klónozza a konfigurációt, a klónozott konfiguráció szerkeszthető. Egyes konfigurációs elemek követik a tartalmat a swap (nem bővítőhely-specifikus), míg más konfigurációs elemek marad ugyanabban a tárolóhelyen a csere után (slot-specifikus). Az alábbi listák ontják azokat a beállításokat, amelyek a tárolóhelyek cseréjekor változnak.

**Cserélt beállítások:**

* Általános beállítások, például keretrendszer-verzió, 32/64 bites, webszoftver-foglalat
* Alkalmazásbeállítások (beállítható úgy, hogy egy bővítőhelyhez ragadjon)
* Csatlakozási karakterláncok (beállíthatók úgy, hogy egy bővítőhelyhez tapuljanak)
* Kezelői hozzárendelések
* Nyilvános tanúsítványok
* WebJobs-tartalom
* Hibrid kapcsolatok *
* Virtuális hálózati integráció *
* Szolgáltatási végpontok *
* Azure content delivery network *

A csillaggal (*) jelölt funkciók at tervezik a csere nélküliítésre. 

**Nem cserélt beállítások:**

* Végpontok közzététele
* Egyéni tartománynevek
* Nem nyilvános tanúsítványok és TLS/SSL-beállítások
* Beállítások méretezése
* WebJobs ütemezők
* IP-korlátozások
* Mindig bekapcsolva
* Diagnosztikai napló beállításai
* Több forrásból származó erőforrás-megosztás (CORS)

> [!NOTE]
> A nem cserélt beállításokra vonatkozó egyes alkalmazásbeállítások szintén nem cserélődnek. Például, mivel a diagnosztikai napló beállításai nem `WEBSITE_HTTPLOGGING_RETENTION_DAYS` cserélődnek, a kapcsolódó alkalmazásbeállítások például, és `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` nem is cserélhetők, még akkor sem, ha nem jelennek meg helyhelybeállításokként.
>
