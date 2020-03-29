---
title: Egyéni altartományok
titleSuffix: Azure Cognitive Services
description: Az egyes Cognitive Service-erőforrások egyéni altartománynevei az Azure Portalon, az Azure Cloud Shellen vagy az Azure CLI-n keresztül jönnek létre.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647696"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Egyéni altartománynevek a Cognitive Services szolgáltatáshoz

Az Azure Cognitive Services egyéni altartományneveket használ az [Azure Portalon,](https://portal.azure.com)az [Azure Cloud Shellen](https://azure.microsoft.com/features/cloud-shell/)vagy az [Azure CLI-n](https://docs.microsoft.com/cli/azure/install-azure-cli)keresztül létrehozott minden egyes erőforráshoz. A regionális végpontokkal ellentétben, amelyek egy adott Azure-régió összes ügyfele számára gyakoriak voltak, az egyéni altartománynevek egyediek az erőforrásszámára. Egyéni altartománynevek szükségesek az Olyan funkciók engedélyezéséhez, mint az Azure Active Directory (Azure AD) a hitelesítéshez.

## <a name="how-does-this-impact-existing-resources"></a>Milyen hatással van ez a meglévő erőforrásokra?

2019. július 1-je előtt létrehozott Cognitive Services-erőforrások a társított szolgáltatás regionális végpontjait fogják használni. Ezek a végpontok a meglévő és az új erőforrásokkal fognak működni.

Ha egy meglévő erőforrást szeretne áttelepíteni az egyéni altartománynevek kihasználásához, hogy engedélyezhesse az olyan funkciókat, mint az Azure AD, kövesse az alábbi utasításokat:

1. Jelentkezzen be az Azure Portalon, és keresse meg azt a Cognitive Services-erőforrást, amelyhez egyéni altartománynevet szeretne hozzáadni.
2. Az **Áttekintés** panelen keresse meg és válassza **az Egyéni tartománynév létrehozása lehetőséget.**
3. Ezzel megnyit egy panelt, amely utasításokat tartalmaz az erőforrás egyedi egyéni altartományának létrehozásához.
   > [!WARNING]
   > Az egyéni altartománynév létrehozása után **nem** módosítható.

## <a name="do-i-need-to-update-my-existing-resources"></a>Frissítenem kell a meglévő erőforrásokat?

Nem. A regionális végpont továbbra is működik az új és a meglévő Cognitive Services és az egyéni altartomány neve nem kötelező. Még ha egyéni altartománynevet is hozzáad, a területi végpont továbbra is működni fog az erőforrással.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Mi a teendő, ha egy SDK megkérdezi, hogy a régió egy erőforrás?

> [!WARNING]
> A beszédszolgáltatások **jelenleg nem** támogatják az egyéni altartományokat. Kérjük, használja a regionális végpontok a beszédszolgáltatások és a kapcsolódó SDK-k használatakor.

A regionális végpontok és az egyéni altartománynevek egyaránt támogatottak, és felcserélhetők. Azonban a teljes végpont szükséges.

A régióadatok az [Azure Portalon](https://portal.azure.com)az erőforrás **áttekintése** panelen érhetők el. A regionális végpontok teljes listájáért lásd: [Vannak regionális végpontok listája?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Az egyéni altartománynevek regionálisak?

Igen. Egyéni altartománynév használata nem módosítja a Cognitive Services-erőforrás egyik regionális aspektusát sem.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Milyen követelmények vonatkoznak az egyéni altartománynevekre?

Az egyéni altartománynév az erőforrás egyedi. A név csak alfanumerikus `-` karaktereket és karaktert tartalmazhat; 2 és 64 karakter közötti hosszúságúnak kell `-`lennie, és nem végződhet .

## <a name="can-i-change-a-custom-domain-name"></a>Módosíthatom az egyéni tartománynevet?

Nem. Egyéni altartománynév létrehozása és az erőforráshoz való hozzárendelése után nem módosítható.

## <a name="can-i-reuse-a-custom-domain-name"></a>Használhatom újra az egyéni tartománynevet?

Minden egyéni altartománynév egyedi, ezért a Cognitive Services-erőforráshoz rendelt egyéni altartománynév újbóli felhasználásához törölnie kell a meglévő erőforrást. Az erőforrás törlése után újra felhasználhatja az egyéni altartománynevet.

## <a name="is-there-a-list-of-regional-endpoints"></a>Van lista a regionális végpontokról?

Igen. Ez a lista a regionális végpontok, amelyek az Azure Cognitive Services-erőforrásokkal használható.

> [!NOTE]
> A Translator Text API és a Bing Search API-k globális végpontokat használnak.

| Végpont típusa | Régió | Végpont |
|---------------|--------|----------|
| Nyilvános | Globális (fordítószöveg & Bing) | `https://api.cognitive.microsoft.com` |
| | Kelet-Ausztrália | `https://australiaeast.api.cognitive.microsoft.com` |
| | Dél-Brazília | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Közép-Kanada | `https://canadacentral.api.cognitive.microsoft.com` |
| | USA középső régiója | `https://centralus.api.cognitive.microsoft.com` |
| | Kelet-Ázsia | `https://eastasia.api.cognitive.microsoft.com` |
| | USA keleti régiója | `https://eastus.api.cognitive.microsoft.com` |
| | USA 2. keleti régiója | `https://eastus2.api.cognitive.microsoft.com` |
| | Közép-Franciaország | `https://francecentral.api.cognitive.microsoft.com` |
| | Közép-India | `https://centralindia.api.cognitive.microsoft.com` |
| | Kelet-Japán | `https://japaneast.api.cognitive.microsoft.com` |
| | Dél-Korea középső régiója | `https://koreacentral.api.cognitive.microsoft.com` |
| | USA északi középső régiója | `https://northcentralus.api.cognitive.microsoft.com` |
| | Észak-Európa | `https://northeurope.api.cognitive.microsoft.com` |
| | Dél-Afrika Észak-Afrika | `https://southafricanorth.api.cognitive.microsoft.com` |
| | USA déli középső régiója | `https://southcentralus.api.cognitive.microsoft.com` |
| | Délkelet-Ázsia | `https://southeastasia.api.cognitive.microsoft.com` |
| | Az Egyesült Királyság déli régiója | `https://uksouth.api.cognitive.microsoft.com` |
| | USA nyugati középső régiója | `https://westcentralus.api.cognitive.microsoft.com` |
| | Nyugat-Európa | `https://westeurope.api.cognitive.microsoft.com` |
| | USA nyugati régiója | `https://westus.api.cognitive.microsoft.com` |
| | USA nyugati régiója, 2. | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | USA-beli államigazgatás – Virginia | `https://virginia.api.cognitive.microsoft.us` |
| Kína | Kína Keleti 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Észak-Kína | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Lásd még

* [Mik azok a Cognitive Services?](Welcome.md)
* [Hitelesítés](authentication.md)
