---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 70ab0b5c70e94c4784a7ab260b3304107bcb1175
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096423"
---
Ha másik üzembe helyezési pontról klónozott konfigurációt telepít, a klónozott konfiguráció szerkeszthető. Egyes konfigurációs elemek követik a tartalmat a swap (nem tárolóhely-specifikus), míg az egyéb konfigurációs elemek ugyanazon a tárolóhelyen maradnak a swap (tárolóhely-specifikus) után. Az alábbi listában a tárolóhelyek cseréjekor megjelenő beállítások láthatók.

**Felcserélt beállítások**:

* Általános beállítások, mint például a keretrendszer verziója, 32/64 bites, webes szoftvercsatornák
* Alkalmazásbeállítások (beállítható egy tárolóhelyre való ragasztásra)
* Kapcsolódási karakterláncok (beállítható egy tárolóhelyre való ragasztásra)
* Kezelő-hozzárendelések
* Nyilvános tanúsítványok
* Webjobs-tartalom
* Hibrid kapcsolatok *
* Szolgáltatási végpontok *
* Azure Content Delivery Network *

A csillaggal (*) jelölt funkciók nem lesznek lecserélve. 

**Nem felcserélt beállítások**:

* Közzétételi végpontok
* Egyéni tartománynevek
* Nem nyilvános tanúsítványok és TLS/SSL-beállítások
* Méretezési beállítások
* Webjobs-ütemező
* IP-korlátozások
* Always on
* Diagnosztikai beállítások
* Eltérő eredetű erőforrások megosztása (CORS)
* Virtuális hálózat integrációja

> [!NOTE]
> A nem cserélt beállításokra vonatkozó bizonyos Alkalmazásbeállítások nem lesznek felcserélve. Mivel például a diagnosztikai beállítások nincsenek felcserélve, a kapcsolódó Alkalmazásbeállítások, például `WEBSITE_HTTPLOGGING_RETENTION_DAYS` a és a nem lesznek `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` lecserélve, még akkor is, ha nem jelennek meg tárolóhely-beállításokként.
>
