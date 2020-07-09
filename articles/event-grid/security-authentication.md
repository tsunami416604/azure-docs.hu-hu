---
title: Események kézbesítésének hitelesítése az eseménykezelők számára (Azure Event Grid)
description: Ez a cikk bemutatja, hogyan hitelesíthető a kézbesítés az eseménykezelők számára a Azure Event Gridban.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d48930ac9cfdd1ecd3e7d6c64067d5389323f8bc
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119939"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Események kézbesítésének hitelesítése az eseménykezelők számára (Azure Event Grid)
Ez a cikk tájékoztatást nyújt az események az eseménykezelők számára történő hitelesítéséről. Azt is bemutatja, hogyan védheti meg az Event Grid események fogadására használt webhook-végpontokat Azure Active Directory (Azure AD) vagy közös titok használatával.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Rendszerhez rendelt identitások használata az események kézbesítéséhez
Engedélyezheti a rendszerhez rendelt felügyelt identitást egy témakörhöz vagy tartományhoz, és az identitás használatával továbbíthatja az eseményeket olyan támogatott célhelyekre, mint például a Service Bus várólisták és témakörök, az Event hubok és a Storage-fiókok.

A lépések a következők: 

1. Hozzon létre egy témakört vagy tartományt egy rendszer által hozzárendelt identitással, vagy frissítsen egy meglévő témakört vagy tartományt az identitás engedélyezéséhez. 
1. Adja hozzá az identitást egy megfelelő szerepkörhöz (például Service Bus adatfeladóhoz) a célhelyen (például egy Service Bus üzenetsor).
1. Esemény-előfizetések létrehozásakor engedélyezze az identitás használatát, hogy az eseményeket a célhelyre kézbesítse. 

Részletes útmutatásért lásd: [esemény kézbesítése felügyelt identitással](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Az események kézbesítésének hitelesítése webhook-végpontokra
Az alábbi szakaszok azt ismertetik, hogyan hitelesíthető az események kézbesítése a webhook-végpontokra. A használt módszertől függetlenül egy érvényesítési kézfogási mechanizmust kell használnia. Részletekért lásd: [webhook-esemény kézbesítése](webhook-event-delivery.md) . 


### <a name="using-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) használata
Az Azure AD segítségével biztonságossá teheti a webhook-végpontot, amely a Event Grid eseményeinek fogadására szolgál. Létre kell hoznia egy Azure AD-alkalmazást, létre kell hoznia egy szerepkört és egy egyszerű szolgáltatásnevet az alkalmazásban Event Grid engedélyezéséhez, és az esemény-előfizetést az Azure AD-alkalmazás használatára kell beállítania. Megtudhatja, hogyan [konfigurálhatja a Azure Active Directoryt Event Grid](secure-webhook-delivery.md)használatával.

### <a name="using-client-secret-as-a-query-parameter"></a>Az ügyfél titkos kulcsa lekérdezési paraméterként
A webhook-végpontot úgy is biztonságossá teheti, ha lekérdezési paramétereket ad hozzá a webhook cél URL-címéhez, amely az esemény-előfizetés létrehozása részeként van megadva. Állítsa be az egyik lekérdezési paramétert, hogy az ügyfél titka legyen, például egy [hozzáférési jogkivonat](https://en.wikipedia.org/wiki/Access_token) vagy egy közös titok. Event Grid szolgáltatás tartalmazza az összes lekérdezési paramétert minden esemény kézbesítési kérelmében a webhookban. A webhook szolgáltatás lekérheti és érvényesítheti a titkos kulcsot. Ha az ügyfél titkos kulcsát frissíti, a rendszer az esemény-előfizetést is frissítenie kell. Ha el szeretné kerülni a kézbesítési hibákat a titkos rotáció során, akkor a webhook a régi és az új titkos kulcsot is elfogadja korlátozott időtartamra, mielőtt frissíti az esemény-előfizetést az új titokkal. 

Mivel a lekérdezési paraméterek tartalmazhatják az ügyfél titkos kulcsait, a rendszer extra gondossággal kezeli őket. A rendszer titkosított formában tárolja őket, és nem érhető el a szolgáltatási operátorok számára. Nincsenek naplózva a szolgáltatási naplók/Nyomkövetések részeként. Az esemény-előfizetés tulajdonságainak beolvasása során a rendszer alapértelmezés szerint nem adja vissza a cél lekérdezési paramétereket. Például: [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) paramétert kell használni az Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)-ben.

További információ az események webhookok általi kézbesítéséről: [webhook-esemény kézbesítése](webhook-event-delivery.md)

> [!IMPORTANT]
A Azure Event Grid csak a **https** webhook-végpontokat támogatja. 


## <a name="next-steps"></a>Következő lépések
Tekintse meg a [közzétételi ügyfelek hitelesítése](security-authenticate-publishing-clients.md) című témakört, amelyből megtudhatja, hogyan hitelesítheti az ügyfeleket a témakörök vagy tartományok számára 
