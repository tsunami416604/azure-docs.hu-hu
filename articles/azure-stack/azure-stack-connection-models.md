---
title: Az Azure Stackkel integrált rendszerek kapcsolati modellek |} A Microsoft Docs
description: Tervezési megfontolások az Azure Stack több csomópontos központi telepítés határozza meg.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 7509d00815f56dc46bd276ffc67c4c607c54070a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338896"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Az Azure Stackkel integrált rendszerek kapcsolati modellek
Ha érdeklik az Azure Stackkel integrált rendszereknél, kell megérteni [több adatközpont integrációja szempontok](azure-stack-datacenter-integration.md) való határozza meg, hogyan elférnek a rendszer a helyi adatközpontban Azure Stack üzembe helyezéshez. Emellett szüksége dönthet arról, hogy pontosan hogyan integrálja az Azure Stack a hibridfelhő-környezet be. Ez a cikk a főbb kérdései, többek között az Azure-kapcsolat, ügyfélidentitás-tárolóval, és a számlázási modell döntések áttekintést nyújt.

Ha úgy dönt, hogy vásárol egy integrált rendszer, a számítógépgyártó (OEM) hardvergyártójához segítségével vezeti végig a tervezési folyamat további részleteket a jelentős részét. Azok a tényleges telepítési is elvégzi.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Az Azure Stack üzembe helyezési kapcsolat modell kiválasztása
Kiválaszthatja, hogy csatlakozik az internethez (és az Azure-bA), vagy csatlakoztatva az Azure Stack üzembe helyezése. Hozhatja ki a legnagyobb előnnyel az Azure Stack, hibrid forgatókönyvek között az Azure Stacket és Azure-ban, beleértve a szeretne üzembe helyezése az Azure-hoz csatlakoztatva. Ez a választás határozza meg, melyik lehetőség áll rendelkezésre az ügyfélidentitás-tárolóval (Azure Active Directory vagy Active Directory összevonási szolgáltatások) és a számlázási modellt (kell fizetnie, a használat alapú számlázási vagy a kapacitás-alapú számlázási) a következő ábra és táblázat foglalja össze: 

![Az Azure Stack üzemelő példányához és számlázási forgatókönyvek](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Ez a fő döntési pont! Az Active Directory összevonási szolgáltatások (AD FS) vagy az Azure Active Directory (Azure AD)-e egy egyszeri döntés üzembe helyezéskor kell végrehajtania. Nem módosíthatja ezt később ismételt üzembe helyezése a teljes rendszer nélkül.  


|Beállítások|Az Azure-hoz csatlakoztatva|Az Azure-ból leválasztva|
|-----|-----|-----|
|Azure AD|![Támogatott](media/azure-stack-connection-models/check.png)| |
|AD FS|![Támogatott](media/azure-stack-connection-models/check.png)|![Támogatott](media/azure-stack-connection-models/check.png)|
|Fogyasztás alapú számlázáshoz|![Támogatott](media/azure-stack-connection-models/check.png)| |
|Kapacitás-alapú számlázás|![Támogatott](media/azure-stack-connection-models/check.png)|![Támogatott](media/azure-stack-connection-models/check.png)|
|Töltse le a frissítési csomagok közvetlenül az Azure Stackhez|![Támogatott](media/azure-stack-connection-models/check.png)|  |

Miután kiválasztotta az az Azure-kapcsolat modell használható az Azure Stack üzemelő példányához, további, a kapcsolat-függő döntéseket kell meghozni az ügyfélidentitás-tárolóval és a számlázási mód. 

## <a name="next-steps"></a>További lépések

[Az Azure csatlakoztatott Azure Stack telepítési döntések](azure-stack-connected-deployment.md)

[Az Azure leválasztott Azure Stack telepítési döntések](azure-stack-disconnected-deployment.md)
