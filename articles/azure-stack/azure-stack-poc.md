---
title: Mi az Azure Stack? | Microsoft Docs
description: Az Azure verem lehetővé teszi, hogy az adatközpontban található Azure-szolgáltatások futtatásához.
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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 848df59b01cc0c03b9a5f3dae2644d469c8651bb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234886"
---
# <a name="what-is-azure-stack"></a>Mi az Azure Stack?

A Microsoft Azure verem egy hibrid felhő platform, amely lehetővé teszi az Azure-szolgáltatások biztosításához az adatközpontban található. Ehhez a platformhoz készült fenyegető üzleti igényei támogatásához. Azure verem engedélyezheti a modern alkalmazások, széle és a kapcsolat nélküli környezetekben, például új forgatókönyvek használhatók vagy egyéni biztonsági és megfelelőségi követelményeknek megfelelő.

Azure verem tartományregisztráció két központi telepítési beállításai az igényeinek.

## <a name="azure-stack-integrated-systems"></a>Az Azure Stackkel használható integrált rendszerek
Integrált rendszerek felkínált keresztül egy partneri kapcsolat áll fenn a Microsoft Azure verem és [hardverszállító partnerétől](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), megoldást kínál a felhőben programról ütemben elsajátítható innováció létrehozása és az informatika felügyeleti egyszerűség. Azure verem érhető el egy integrált hardver- és szoftver-, mert rendelkezik a rugalmasság és a vezérlés van szüksége, és innovációját annak a felhőből lehetőséget. Azure integrált verem rendszerek mérete a 4 – 12 csomópontok között, és a hardver partner és a Microsoft által közösen támogatott.  Integrált Azure verem rendszerek használata új forgatókönyvek létrehozása és telepítése az új megoldások a termelési számítási feladatokhoz.

## <a name="azure-stack-development-kit"></a>Azure Stack fejlesztői készlete

Microsoft [Azure verem Development Kit (ASDK)](.\asdk\asdk-what-is.md) egy egy csomópontos telepítés Azure-vermet, kiértékeléséhez és megismerése az Azure-verem használható.  ASDK a fejlesztői környezet, az API-kkal és tooling eszköz alkalmazások készítéséhez, amely megfelelő Azure is használható.

>[!Note]
>A ASDK nem készült, éles környezetben használható.

A ASDK rendelkezik a következő korlátozások vonatkoznak:

* ASDK egyetlen Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) identitásszolgáltató hozzá rendelve. Több felhasználó létrehozása a könyvtárban, és előfizetések hozzárendelése minden felhasználóhoz.
* Azure verem összetevőket telepít egy állomáson, mert nincsenek korlátozott fizikai erőforrások bérlői erőforrásokhoz. Ez a konfiguráció nem célja a skála vagy a teljesítmény kiértékelése.
* Hálózati forgatókönyvek a egyetlen gazdagép és a hálózati telepítési követelményei miatt korlátozódnak.

## <a name="next-steps"></a>További lépések

- [A legfontosabb jellemzők és fogalmak](azure-stack-key-features.md)
- [Azure verem: Az Azure (pdf) kiterjesztés](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
