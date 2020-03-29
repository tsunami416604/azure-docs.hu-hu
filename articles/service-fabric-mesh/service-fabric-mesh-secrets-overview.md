---
title: Az Azure Service Fabric Mesh alkalmazásttttantitkos kulcsok tárolása és használata
description: A Service Fabric Mesh támogatja a titkos kulcsokat Azure-erőforrásokként. A következőképpen tárolhatja és kezelheti a titkos kulcsokat a Service Fabric Mesh-alkalmazásokkal.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: da4eaa34840f14714616b85e24fd62cf65602b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277635"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh alkalmazás titkos titkai
A Service Fabric Mesh támogatja a titkos kulcsokat Azure-erőforrásokként. A Service Fabric Mesh titkos lehet bármilyen bizalmas szöveges információkat, például a tárolási kapcsolat karakterláncok, jelszavak vagy más értékeket, amelyeket biztonságosan kell tárolni és továbbítani.

![Hálótitkos ók – áttekintés][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Hálótitkos-objektumok erőforrásai
A Mesh alkalmazás titkos sága a következőkből áll:
* A **Titkos kulcsok** erőforrás, amely egy tároló, amely tárolja a szöveges titkokat. A Titkos **kulcsok** erőforrásban található titkos kulcsok biztonságosan tárolódnak és továbbítódnak.
* Egy vagy több **titkos kulcsok/értékek** erőforrások, amelyek a **Titkos kulcsok** erőforrás tárolóban tárolt. Minden **Titkos kulcsok/értékek** erőforrást verziószám különböztetmeg.

## <a name="next-steps"></a>További lépések 
A Service Fabric hálótitkos titkairól a következő témakörökben olvashat bővebben:
- [Szolgáltatásháló alkalmazásttkókainak kezelése](service-fabric-mesh-howto-manage-secrets.md)
- [Bevezetés a Service Fabric erőforrás-modell](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
