---
title: Az Azure Media Services v3 kibocsátási megjegyzései |} Microsoft Docs
description: Maradjon naprakész, és a legújabb fejlemények, ez a cikk nyújt az Azure Media Services v3 a legújabb frissítéseket.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788115"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Az Azure Media Services v3 (előzetes verzió) kibocsátási megjegyzései 

Maradjon naprakész, és a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

* A legfrissebb verziókban
* Ismert problémák
* Hibajavítások
* Elavult funkciók
* Módosítások tervek

## <a name="may-07-2018"></a>2018. Előfordulhat, hogy 07.

### <a name="net-sdk"></a>.NET SDK

A következő funkciók válnak elérhetővé a .net SDK:

1. **Átalakítja az** és **feladatok** kódolására, vagy a médiatartalom elemzéséhez. Tekintse meg a [adatfolyam-fájlok](stream-files-tutorial-with-api.md) és [elemzés](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** közzétételéhez és adatfolyam-tartalom végfelhasználói eszközökhöz
3. **StreamingPolicies** és **ContentKeyPolicies** tartalomtovábbítás kulcs kézbesítési és tartalomvédelmi (DRM) konfigurálása.
4. **LiveEvents** és **LiveOutputs** a feldolgozást és élő adatfolyam-tartalmak archiválása konfigurálásához.
5. **Eszközök** tárolására és médiatartalmak közzététele az Azure Storage. 
6. **Streamvégpontok** konfigurálásához és a dinamikus Becsomagolás, a titkosítás és a működés közbeni és igény szerinti médiatartalom streaming méretezni.

### <a name="known-issues"></a>Ismert problémák

Ismert hiba:

Amikor elküld egy feladatot HTTPS URL-címet (JobInputHttp) a forrástartalom mutat, győződjön meg arról, hogy a HTTP-kiszolgáló támogatja-e a "HEAD" kérelem-e. Ellenkező esetben a feladat elutasításra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Áttekintés](media-services-overview.md)
