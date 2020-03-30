---
title: Protokollok és titkosítások kezelése az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan kezelheti a protokollokat (TLS) és a titkosításokat (DES) az Azure API Managementben.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 7f87389016286c9f1b91abc77155be9e94005371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335872"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Protokollok és titkosítások kezelése az Azure API Managementben

Az Azure API Management támogatja a TLS protokoll több verzióját az ügyfél- és háttéroldalakon, valamint a 3DES-titkosítást.

Ez az útmutató bemutatja, hogyan kezelheti a protokollok és a titkosítási konfiguráció egy Azure API Management-példány.

![Protokollok és titkosítások kezelése az APIM-ben](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések végrehajtásához a következőkre van szüksége:

* API-felügyeleti példány

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>A TLS protokollok és a 3DES titkosítás kezelése

1. Keresse meg az **API Management-példányt** az Azure Portalon.
2. Válassza a menü **Protokollbeállításai lehetőséget.**  
3. A kívánt protokollok vagy titkosítások engedélyezése vagy letiltása.
4. Kattintson a **Mentés** gombra. A módosítások egy órán belül érvénybe lépnek.  

## <a name="next-steps"></a>További lépések

* További információ a [TLS -ről (Transport Layer Security).](https://docs.microsoft.com/dotnet/framework/network-programming/tls)
* Tekintse n le az API Managementről szóló további [videókat.](https://azure.microsoft.com/documentation/videos/index/?services=api-management)