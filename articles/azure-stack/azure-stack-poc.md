---
title: Mi az Azure Stack? | Microsoft Docs
description: Azure verem lehetővé teszi, hogy az adatközpontban található Azure-szolgáltatások futtatásához.
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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 863c1ec562cd71af0df69ccc0547e16d02c7ee82
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-stack"></a>Mi az Azure Stack?

A Microsoft Azure verem egy hibrid felhő platform, amely lehetővé teszi az Azure-szolgáltatások letölteni a szervezet datacenter.  Az Azure verem lehetővé teszi, hogy a legfontosabb forgatókönyvek, például az edge és kapcsolat nélküli környezetekben, illetve értekezlet meghatározott biztonsági és megfelelőségi követelményeknek a modern alkalmazások új forgatókönyvek használhatók.  Azure verem tartományregisztráció két központi telepítési beállításai az igényeinek.

## <a name="azure-stack-integrated-systems"></a>Az Azure Stackkel használható integrált rendszerek
Integrált rendszerek felkínált keresztül egy partneri kapcsolat áll fenn a Microsoft Azure verem és [hardverszállító partnerétől](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), felügyeleti egyszerűség felhő programról ütemben elsajátítható innováció kínál, amelyek megoldás létrehozásával átgondolni.  Azure verem érhető el az integrált rendszer hardvereket és szoftvereket, mert a rugalmasság és a vezérlés, megfelelő mennyiségű felkínálja továbbra is az innováció a felhőből elfogadása közben.  Azure integrált verem rendszerek mérete a 4 – 12 csomópontok között, és a hardver partner és a Microsoft által közösen támogatott.  Integrált Azure verem rendszerek használatával engedélyezze a termelési számítási feladatokhoz új forgatókönyvek használhatók.    

## <a name="azure-stack-development-kit"></a>Azure Stack fejlesztői készlete
Microsoft [Azure verem Development Kit (ASDK)](.\asdk\asdk-what-is.md) egy egy csomópontos telepítés Azure vermet, amelyek segítségével értékelje ki és Azure verem megismerése.  Használhatja a ASDK egy fejlesztői környezetben, ahol API-k és az Azure konzisztens tooling fejleszthet. A ASDK nem kívánják éles környezetben használható.

A ASDK rendelkezik a következő korlátozások vonatkoznak:
* ASDK egyetlen Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) identitásszolgáltató hozzá rendelve. Több felhasználó létrehozása a könyvtárban, és előfizetések hozzárendelése minden felhasználóhoz.
* Az egyetlen gépen központilag telepített összes összetevőit korlátozott fizikai erőforrások az érhetők bérlői erőforrásokhoz. Ez a konfiguráció nem készült méretezési vagy a teljesítmény kiértékelése.
* Hálózati forgatókönyvek a egyetlen állomás hálózati követelmény miatt korlátozódnak.  

## <a name="next-steps"></a>További lépések
[A legfontosabb jellemzők és fogalmak](azure-stack-key-features.md)

[Azure verem: Az Azure (pdf) kiterjesztés](https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/)

