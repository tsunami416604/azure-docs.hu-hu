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
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 65894ccd9514bce1d429b336f8bd5e6674048e65
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820263"
---
# <a name="what-is-azure-stack"></a>Mi az Azure Stack?

A Microsoft Azure Stack egy hibrid felhő platform, amely lehetővé teszi az Azure-szolgáltatások biztosításához az adatközpontban található. Erre a platformra készült fejlődő üzleti igényei támogatásához. Azure Stack engedélyezheti a modern alkalmazások, széle és a kapcsolat nélküli környezetekben, például új forgatókönyvek használhatók vagy egyéni biztonsági és megfelelőségi követelményeknek megfelelő.

Azure Stack tartományregisztráció két központi telepítési beállításai az igényeinek.


## <a name="azure-stack-development-kit"></a>Azure Stack fejlesztői készlete

Microsoft [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) egy egy csomópontos telepítés Azure Stacket, kiértékeléséhez és megismerése az Azure Stack használható.  Emellett használhatja a ASDK egy fejlesztői környezet használatával az API-k és eszközök alkalmazásokat hozhat létre, amely egységes Azure-ral.

>[!Note]
>A ASDK használata nem javasolt éles környezetben használható.

A ASDK a következő korlátozások vonatkoznak:

* A ASDK egy egyetlen Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) identitásszolgáltató társítva. Több felhasználó létrehozása ebben a könyvtárban, és az előfizetések hozzárendelése minden felhasználóhoz.
* Az Azure Stack-összetevők egy egyetlen számítógépen van telepítve, mert érhetők el korlátozott fizikai erőforrások bérlői erőforrásokhoz. Ez a konfiguráció nem javasolt a méretezési csoport vagy a teljesítmény értékeléséhez.
* Hálózati forgatókönyvek a egyetlen gazdagép és a hálózati adapter központi telepítésére vonatkozó követelmények miatt korlátozva.

## <a name="azure-stack-integrated-systems"></a>Az Azure Stackkel használható integrált rendszerek
Integrált rendszerek felkínált keresztül egy partneri kapcsolat áll fenn a Microsoft Azure Stack és [hardverszállító partnerétől](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), megoldást kínál a felhőben programról ütemben elsajátítható innováció létrehozása és az informatika felügyeleti egyszerűség. Azure Stack érhető el egy integrált hardver- és szoftver-, mert rendelkezik a rugalmasság és a vezérlés van szüksége, és innovációját annak a felhőből lehetőséget. Az Azure Stack integrált rendszerek mérete 4 és 16 közötti csomópontjáról tartományt, és közösen a hardver partner és a Microsoft által támogatott.  Integrált Azure Stack rendszerek használata új forgatókönyvek létrehozása és telepítése az új megoldások a termelési számítási feladatokhoz.

## <a name="next-steps"></a>További lépések

[A legfontosabb jellemzők és fogalmak](azure-stack-key-features.md)

[Azure Stack: Azure (pdf) bővítmény](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
