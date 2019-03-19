---
title: Tárolásához és az Azure Service Fabric használatával a titkos Alkalmazáskulcsok háló |} A Microsoft Docs
description: Tárolására, és a Service Fabric háló titkos kulcsok használatával.
services: service-fabric-mesh
keywords: titkos kódok
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 4268356db5f46e92862e19d6391cfe5a94511270
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58007486"
---
# <a name="service-fabric-mesh-application-secrets"></a>Titkos alkalmazáskulcsok Service Fabric-háló
Titkos kódok Service Fabric-háló támogatja az Azure-erőforrások. Service Fabric-háló titkos lehet például a storage kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek tárolása és biztonságosan továbbítani kell bizalmas információkat.

![Háló titkos kulcsok – áttekintés][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Háló titkok erőforrások
A háló alkalmazás titkos kulcs áll:
* A **titkok** erőforrás, amely egy tároló, amely tárolja a szöveg titkos kulcsok. Tárolt titkos kulcsok a **titkok** erőforrás tárolása és biztonságosan továbbítani.
* Egy vagy több **titkos kulcsok/értékek** tárolt erőforrások a **titkok** az erőforrás-tárolónak. Minden egyes **titkos kulcsok/értékek** erőforrás van megkülönböztető verziószámmal.

## <a name="next-steps"></a>További lépések 
Service Fabric-háló titkok kapcsolatos további információkért lásd:
- [Service Fabric háló titkos Alkalmazáskulcsok kezelése](service-fabric-mesh-howto-manage-secrets.md)
- [Bevezetés a Service Fabric Erőforrásmodell](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
