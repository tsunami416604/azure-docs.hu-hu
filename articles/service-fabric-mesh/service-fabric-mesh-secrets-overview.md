---
title: Azure Service Fabric Mesh alkalmazás-titkok tárolása és használata | Microsoft Docs
description: Service Fabric Mesh-titkok tárolása és használata.
services: service-fabric-mesh
keywords: titkok
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: b5cfa93298222d914069b6ab63deb8ba8a9b59c3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875347"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh-alkalmazás titkai
Service Fabric Mesh a titkokat Azure-erőforrásokként támogatja. A Service Fabric Mesh titkos kulcsa lehet bármilyen bizalmas szöveges információ, például a tárolási kapcsolatok sztringje, jelszava vagy más olyan érték, amelyet biztonságosan kell tárolni és továbbítani.

![A Mesh titkainak áttekintése][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh titkok erőforrásai
A háló alkalmazás titkos kulcsa a következőkből áll:
* A **titkok** erőforrása, amely egy olyan tároló, amely a szöveges titkokat tárolja. A **titkok** erőforrásban található titkos kódok tárolása és továbbítása biztonságos.
* Egy vagy több **titok/érték** erőforrás, amely a **titkok** erőforrás-tárolóban van tárolva. Az egyes **titkok/értékek** erőforrásait a verziószáma különbözteti meg.

## <a name="next-steps"></a>További lépések 
A Service Fabric Mesh-titkokkal kapcsolatos további tudnivalókért tekintse meg a következőt:
- [Service Fabric Mesh-alkalmazás titkainak kezelése](service-fabric-mesh-howto-manage-secrets.md)
- [A Service Fabric Resource Model bemutatása](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
