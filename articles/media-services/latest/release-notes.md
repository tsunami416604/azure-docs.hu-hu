---
title: Az Azure Media Services v3 kibocsátási megjegyzései |} A Microsoft Docs
description: A legújabb fejlemények az naprakész információkat, ez a cikk az Azure Media Services v3 legújabb frissítéseit.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/25/2018
ms.author: juliako
ms.openlocfilehash: ed2550c1df4645933fb968c54ee536995c810136
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219329"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Az Azure Media Services v3 (előzetes verzió) kibocsátási megjegyzései 

Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

* A legújabb kiadásaihoz.
* Ismert problémák
* Hibajavítások
* Elavult funkciók
* Módosítások tervek

## <a name="may-07-2018"></a>2018. május 07.

### <a name="net-sdk"></a>.NET SDK

A .net SDK-t a következő funkciók találhatók:

1. **Átalakítások** és **feladatok** kódolásához, vagy a médiatartalmak elemzése. Példák: [fájlok Stream](stream-files-tutorial-with-api.md) és [elemzés](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** közzétételéhez és a végfelhasználói eszközökön tartalmak online lejátszásához
3. **StreamingPolicies** és **ContentKeyPolicies** konfigurálása kulcskézbesítési és a tartalomvédelmi (DRM), amikor tartalmat továbbít.
4. **LiveEvents** és **LiveOutputs** betöltésre és élő adatfolyamok tartalmát archiválása konfigurálása.
5. **Eszközök** tárolására és médiatartalmak közzététele az Azure Storage-ban. 
6. **Streamvégpontok** konfigurálásához és a dinamikus csomagolás, titkosítási és folyamatos élő és igény szerinti médiatartalom méretezéséhez.

### <a name="known-issues"></a>Ismert problémák

* Küldjön el egy feladatot, megadhatja, hogy a forrásvideókat, HTTPS URL-címek, SAS URL-címek vagy elérési utak használata az Azure Blob storage-ban található fájlokat. Az AMS v3 jelenleg nem támogatja a darabolásos átvitel kódolási HTTPS URL-címek keresztül.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Áttekintés](media-services-overview.md)
