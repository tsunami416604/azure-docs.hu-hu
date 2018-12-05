---
title: Tárolásához és az Azure Service Fabric használatával a titkos Alkalmazáskulcsok háló |} A Microsoft Docs
description: Tárolására, és a Service Fabric háló titkos kulcsok használatával.
services: service-fabric-mesh
keywords: titkos kódok
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 63ac34e184d537eba2b915d4d108c7c1d8368aba
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893512"
---
# <a name="service-fabric-mesh-application-secrets"></a>Titkos alkalmazáskulcsok Service Fabric-háló
Titkos kódok Service Fabric-háló támogatja az Azure-erőforrások. Service Fabric-háló titkos lehet például a storage kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek tárolása és biztonságosan továbbítani kell bizalmas információkat.

![Háló titkos kulcsok – áttekintés][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Háló titkok erőforrások
A háló alkalmazás titkos kulcs áll:
* A **titkok** erőforrás, amely egy tároló, amely tárolja a szöveg titkos kulcsok. Tárolt titkos kulcsok a **titkok** erőforrás tárolása és biztonságosan továbbítani.
* Egy vagy több **titkos kulcsok/értékek** tárolt erőforrások a **titkok** az erőforrás-tárolónak. Minden egyes **titkos kulcsok/értékek** erőforrás van megkülönböztető verziószámmal.

## <a name="inline-or-stored-in-azure-key-vault"></a>A beágyazott vagy tárolt, az Azure Key Vault
- Háló alkalmazások és a szolgáltatási erőforrások közé tartozik a Felügyeltszolgáltatás-identitás (MSI) az Azure Active Directory (AAD) ahhoz, hogy az Azure Key Vault titkos eléréséhez.
- Titkos kulcsok és tanúsítványok automatikus állítva feladatátvételi házirendekkel lehet.

## <a name="next-steps"></a>További lépések 
Service Fabric-háló titkok kapcsolatos további információkért lásd:
- [Service Fabric háló titkos Alkalmazáskulcsok kezelése](service-fabric-mesh-howto-manage-secrets.md)
- [Bevezetés a Service Fabric Erőforrásmodell](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
