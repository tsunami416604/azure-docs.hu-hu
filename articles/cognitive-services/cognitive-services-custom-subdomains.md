---
title: Egyéni altartományok
titleSuffix: Azure Cognitive Services
description: Az egyes kognitív szolgáltatások erőforrásaihoz tartozó egyéni tartománynevek a Azure Portalon, Azure Cloud Shellon vagy az Azure CLI-n keresztül jönnek létre.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73647696"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Cognitive Services egyéni tartománynevei

Az Azure Cognitive Services a [Azure Portal](https://portal.azure.com), a [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)használatával létrehozott összes erőforráshoz egyéni tartományneveket használhat. A regionális végpontok eltérően, amelyek egy adott Azure-régióban lévő összes ügyfél esetében közösek, az egyéni altartományok nevei egyediek az erőforrás számára. Az egyéni altartományok neve szükséges az olyan funkciók engedélyezéséhez, mint a Azure Active Directory (Azure AD) a hitelesítéshez.

## <a name="how-does-this-impact-existing-resources"></a>Hogyan befolyásolja ez a meglévő erőforrásokat?

A 2019. július 1. előtt létrehozott erőforrások Cognitive Services a társított szolgáltatás regionális végpontját fogják használni. Ezek a végpontok a meglévő és az új erőforrásokkal is működni fognak.

Ha egy meglévő erőforrást szeretne áttelepíteni az egyéni altartománynevek kihasználása érdekében, hogy az Azure AD-hez hasonló szolgáltatásokat lehessen engedélyezni, kövesse az alábbi utasításokat:

1. Jelentkezzen be a Azure Portalba, és keresse meg azt a Cognitive Services-erőforrást, amelyhez hozzá szeretné adni az egyéni altartomány nevét.
2. Az **Áttekintés** panelen keresse meg és válassza ki az **Egyéni tartománynév előállítása**lehetőséget.
3. Ekkor megnyílik egy panel, amely útmutatást ad az erőforrás egyedi egyéni altartományának létrehozásához.
   > [!WARNING]
   > Miután létrehozta az egyéni altartomány nevét, **nem** módosítható.

## <a name="do-i-need-to-update-my-existing-resources"></a>Frissíteni kell a meglévő erőforrásokat?

Nem. A regionális végpont továbbra is működni fog az új és a meglévő Cognitive Services, és az egyéni altartomány neve nem kötelező. Még akkor is, ha hozzá van adva egy egyéni altartomány neve, a regionális végpont továbbra is együttműködik az erőforrással.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Mi a teendő, ha egy SDK megkérdezi a régiót egy erőforráshoz?

> [!WARNING]
> A Speech Services jelenleg **nem** támogatja az egyéni altartományokat. A Speech Services és a társított SDK-k használata esetén használja a regionális végpontokat.

A regionális végpontok és az egyéni altartományok nevei egyaránt támogatottak, és használhatók szinonimaként. A teljes végpontot azonban kötelező megadni.

A régióval kapcsolatos információk a [Azure Portal](https://portal.azure.com)erőforrásának **Áttekintés** paneljén érhetők el. A regionális végpontok teljes listájáért lásd: a [regionális végpontok listája?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Az egyéni altartománynevek területi neve?

Igen. Az egyéni altartománynevek használata nem változtatja meg a Cognitive Services erőforrás regionális aspektusait.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Milyen követelmények vonatkoznak az egyéni altartomány nevére?

Az erőforrás egyedi altartományának neve egyéni. A név csak alfanumerikus karaktereket és `-` karaktert tartalmazhat. az értéknek 2 és 64 karakter közöttinek kell lennie, és nem `-`végződhet a végződéssel.

## <a name="can-i-change-a-custom-domain-name"></a>Módosíthatom az egyéni tartománynevet?

Nem. Egy egyéni altartomány nevének létrehozása és egy erőforráshoz való társítása után nem módosítható.

## <a name="can-i-reuse-a-custom-domain-name"></a>Felhasználhatok egy egyéni tartománynevet?

Minden egyéni altartománynév egyedi, ezért a Cognitive Services erőforráshoz hozzárendelt egyéni altartománynév-név újbóli felhasználása érdekében törölnie kell a meglévő erőforrást. Az erőforrás törlése után újra felhasználhatja az egyéni altartomány nevét.

## <a name="is-there-a-list-of-regional-endpoints"></a>Van a regionális végpontok listája?

Igen. Az Azure Cognitive Services erőforrásaival használható regionális végpontok listája.

> [!NOTE]
> A Translator Text API és a Bing Search API-k globális végpontokat használ.

| Végpont típusa | Régió | Végpont |
|---------------|--------|----------|
| Nyilvános | Globális (Translator Text & Bing) | `https://api.cognitive.microsoft.com` |
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
| | Dél-Afrika északi régiója | `https://southafricanorth.api.cognitive.microsoft.com` |
| | USA déli középső régiója | `https://southcentralus.api.cognitive.microsoft.com` |
| | Délkelet-Ázsia | `https://southeastasia.api.cognitive.microsoft.com` |
| | Az Egyesült Királyság déli régiója | `https://uksouth.api.cognitive.microsoft.com` |
| | USA nyugati középső régiója | `https://westcentralus.api.cognitive.microsoft.com` |
| | Nyugat-Európa | `https://westeurope.api.cognitive.microsoft.com` |
| | USA nyugati régiója | `https://westus.api.cognitive.microsoft.com` |
| | USA nyugati régiója, 2. | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | USA-beli államigazgatás – Virginia | `https://virginia.api.cognitive.microsoft.us` |
| Kína | Kelet-Kína 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Észak-Kína | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Lásd még

* [Mi a Cognitive Services?](Welcome.md)
* [Hitelesítés](authentication.md)
