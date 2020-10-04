---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711274"
---
| Dimenzió neve | Description |
| ------------------- | ----------------- |
| **GeoType** | Az elsődleges vagy a másodlagos fürtből származó tranzakció. Az elérhető értékek közé tartozik az **elsődleges** és a **másodlagos**. Az olvasási hozzáférés földrajzi redundáns tárolási (RA-GRS) szolgáltatásra vonatkozik, amikor objektumokat olvas a másodlagos bérlőről. |
| **ResponseType** | Tranzakció válaszának típusa Az elérhető értékek közé a következők tartoznak: <br/><br/> <li>**ServerOtherError**: Minden egyéb kiszolgálóoldali hiba, kivéve az ismertetett hibákat </li> <li>**ServerBusyError**: Hitelesített kérés, amely HTTP 503-as állapotkódot adott vissza. </li> <li>**ServerTimeoutError**: Hitelesített kérés, amely túllépte az időkorlátot, és HTTP 500-as állapotkódot adott vissza. Az időtúllépés egy kiszolgálóhiba miatt lépett fel. </li> <li>**AuthorizationError**: Hitelesített kérés, amely jogosulatlan adathozzáférés vagy egy engedélyezési hiba miatt hiúsult meg. </li> <li>**NetworkError**: Hitelesített kérés, amely hálózati hibák miatt hiúsult meg. Leggyakrabban akkor fordul elő, ha egy ügyfél idő előtt, az időkorlát letelte előtt zár be egy kapcsolatot. </li><li>**ClientAccountBandwidthThrottlingError**: a kérés sávszélességét a rendszer a [tárolási fiók skálázhatósági korlátainak](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json)túllépése miatt szabályozza.</li><li>**ClientAccountRequestThrottlingError**: a kérést a rendszer a [tárolási fiók skálázhatósági korlátait](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json)meghaladó kérések díjszabása szerint szabályozza.<li>**ClientThrottlingError**: más ügyféloldali sávszélesség-szabályozási hiba. A ClientAccountBandwidthThrottlingError és a ClientAccountRequestThrottlingError ki vannak zárva.</li> <li>**ClientTimeoutError**: Hitelesített kérés, amely túllépte az időkorlátot, és HTTP 500-as állapotkódot adott vissza. Ha az ügyfél hálózati időkorlátja vagy a kérés időkorlátja a tárolási szolgáltatás által várt értéknél alacsonyabbra van állítva, akkor ez egy várt időtúllépés. Máskülönben a rendszer ServerTimeoutError hibát jelent. </li> <li>**ClientOtherError**: Minden egyéb ügyféloldali hiba, kivéve az ismertetett hibákat. </li> <li>**Success**: Sikeres kérés</li> <li> **SuccessWithThrottling**: sikeres kérés, ha az SMB-ügyfél az első kísérlet (ek) során leszabályozza az újrapróbálkozásokat, de az újrapróbálkozások után sikeres lesz.</li> |
| **ApiName** | A művelet neve. 
| **Hitelesítés** | A tranzakciókban használt hitelesítési típus. Az elérhető értékek közé a következők tartoznak: <br/> <li>**AccountKey**: a tranzakciót a Storage-fiók kulcsa hitelesíti.</li> <li>**Sas**: a tranzakció megosztott hozzáférési aláírásokkal van hitelesítve.</li> <li>**OAuth**: a tranzakció OAuth hozzáférési jogkivonatokkal van hitelesítve.</li> <li>**Névtelen**: a rendszer névtelenül kéri a tranzakciót. Nem tartalmaz elővizsgálati kérelmeket.</li> <li>**AnonymousPreflight**: a tranzakció elővizsgálati kérelem.</li> |