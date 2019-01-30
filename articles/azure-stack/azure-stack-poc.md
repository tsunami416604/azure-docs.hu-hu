---
title: Mi az Azure Stack? | Microsoft Docs
description: Az Azure Stack lehetővé teszi, hogy az adatközpontban található Azure-szolgáltatások futtatásához.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 9341eefdbe1eaa8e27394fd4b10f50b1a48bd4c6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247323"
---
# <a name="what-is-azure-stack"></a>Mi az Azure Stack?

A Microsoft Azure Stack egy hibrid felhő platform, amely lehetővé teszi az Azure-szolgáltatások biztosításához az adatközpontban található. Erre a platformra készült fejlődő üzleti igényei támogatásához. Azure Stack engedélyezheti a modern alkalmazások, széle és a kapcsolat nélküli környezetekben, például új forgatókönyvek használhatók vagy egyéni biztonsági és megfelelőségi követelményeknek megfelelő.

Azure Stack tartományregisztráció két központi telepítési beállításai az igényeinek.


## <a name="azure-stack-development-kit"></a>Azure Stack fejlesztői készlete

Microsoft [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) egy egy csomópontos telepítés Azure Stacket, kiértékeléséhez és megismerése az Azure Stack használható.  ASDK egy fejlesztői környezet használatával az API-k és eszközök alkalmazásokat hozhat létre, amely összhangban is használhatja.

>[!Note]
>A ASDK használata nem javasolt éles környezetben használható.

A ASDK a következő korlátozások vonatkoznak:

* ASDK egy egyetlen Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) identitásszolgáltató társítva. Több felhasználó létrehozása ebben a könyvtárban, és az előfizetések hozzárendelése minden felhasználóhoz.
* Azure Stack összetevőket telepít egy állomáson, mert nincsenek korlátozott fizikai erőforrások bérlői erőforrásokhoz. Ez a konfiguráció nem célja a méretezési csoport vagy a teljesítmény kiértékelése.
* Hálózati forgatókönyvek a egyetlen gazdagép és a hálózati adapter központi telepítésére vonatkozó követelmények miatt korlátozva.

## <a name="azure-stack-integrated-systems"></a>Az Azure Stackkel használható integrált rendszerek
Integrált rendszerek felkínált keresztül egy partneri kapcsolat áll fenn a Microsoft Azure Stack és [hardverszállító partnerétől](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), megoldást kínál a felhőben programról ütemben elsajátítható innováció létrehozása és az informatika felügyeleti egyszerűség. Azure Stack érhető el egy integrált hardver- és szoftver-, mert rendelkezik a rugalmasság és a vezérlés van szüksége, és innovációját annak a felhőből lehetőséget. Az Azure Stack integrált rendszerek mérete 4 és 16 közötti csomópontjáról tartományt, és közösen a hardver partner és a Microsoft által támogatott.  Integrált Azure Stack rendszerek használata új forgatókönyvek létrehozása és telepítése az új megoldások a termelési számítási feladatokhoz.

## <a name="next-steps"></a>További lépések

[A legfontosabb jellemzők és fogalmak](azure-stack-key-features.md)

[Azure Stack: Azure (pdf) bővítmény](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
