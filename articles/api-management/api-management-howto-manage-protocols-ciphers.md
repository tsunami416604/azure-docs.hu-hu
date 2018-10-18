---
title: Protokollok és az Azure API Management Rejtjelek kezelése |} A Microsoft Docs
description: Megtudhatja, hogyan (TLS, SSL) protokollt és titkosítások (DES) az Azure API Management kezeléséhez.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 5d18b0265d2b1c114ed9ef326241ed531e015288
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385139"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Protokollok és az Azure API Management Rejtjelek kezelése

Az Azure API Management az ügyfél és a háttér-partnerek, valamint a 3DES titkosítás TLS protokollal több verzióit támogatja.

Ez az útmutató bemutatja, hogyan kezelheti a protokollok és titkosítások konfigurálása az Azure API Management-példány.

![Protokollok és titkosítások az APIM kezelése](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Előfeltételek

A jelen cikkben ismertetett lépések követéséhez rendelkeznie:

* API Management-példány

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>TLS-protokollok és a 3DES titkosítás kezelése

1. Keresse meg a **API Management-példány** az Azure Portalon.
2. Válassza ki **SSL** a menüből.  
    ![Protokollok és az APIM - titkosítások kezelése menü](./media/api-management-howto-manage-protocols-ciphers/api-management-menu.png)
3. Engedélyezi vagy letiltja a kívánt protokollok vagy Rejtjelek.
4. Kattintson a **Save** (Mentés) gombra. Módosítások egy órán belül lépnek érvénybe.  
    ![Mentés protokollok és az APIM - titkosítások kezelése](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers-save.png)

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Tekintse meg a további [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) az API Management ismertetése.