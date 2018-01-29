---
title: "Az Azure verem integrált rendszerek kapcsolat modellek |} Microsoft Docs"
description: "Határozza meg a központi telepítési tervének kidolgozásához többcsomópontos Azure verem."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: ff56ced9f1d43780097cd65e58b492daaba9b61d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Az Azure verem integrált rendszerek kapcsolat modellek
Ha érdekli az integrált Azure verem rendszer, kell megérteni [datacenter integráció számos szempontot](azure-stack-datacenter-integration.md) határozza meg, hogyan a rendszer az Adatközpont elfér Azure verem telepítéshez. Továbbá szüksége döntse el, hogy pontosan hogyan integrálja Azure verem hibrid felhő környezetébe. Ez a cikk áttekintést nyújt a fő szempontokat Azure kapcsolat, identitás tárolására, beleértve, és a számlázási modell döntéseket.

Ha úgy dönt, hogy integrált rendszer beszerzési, a számítógépgyártó (OEM) hardver gyártójától segít a részletes útmutatót a tervezési folyamat részletesebben nagy része. Azokat a tényleges telepítési is végez.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Válasszon egy Azure verem telepítési kapcsolat modell
Ha szeretné, központi telepítése az Azure-verem csatlakozzon az internethez (és az Azure-bA), vagy nincs csatlakoztatva. Az Azure veremből, beleértve a hibrid forgatókönyvek között Azure verem és az Azure, a legjobb eredmény volna számára telepíteni kívánja kapcsolódik az Azure. Ez a beállítás határozza meg, milyen beállítások érhetők el az (Azure Active Directory vagy Active Directory összevonási szolgáltatások) identitás tárolására és a számlázási modellt (kell fizetnie, ha Ön a használat alapú számlázási vagy kapacitás-alapú számlázási) a következő ábra és táblázat foglalja össze: 

![Az Azure Alkalmazásveremben üzembe és számlázási forgatókönyvek](media/azure-stack-deployment-decisions/azure-stack-scenarios.png)   
  
> [!IMPORTANT]
> Ez az a fő döntési pontok! Válassza az Active Directory összevonási szolgáltatások (AD FS) vagy az Azure Active Directory (Azure AD)-e egy egyszeri döntés, hogy biztosítsa a központi telepítéskor. Ez már nem módosítható később a teljes rendszer újbóli telepítése nélkül.  


|Beállítások|Az Azure-bA csatlakoztatva|Az Azure-ból leválasztása|
|-----|-----|-----|
|Azure AD|![Támogatott](media/azure-stack-deployment-decisions/check.png)| |
|AD FS|![Támogatott](media/azure-stack-deployment-decisions/check.png)|![Támogatott](media/azure-stack-deployment-decisions/check.png)|
|Fogyasztás alapján történő számlázáshoz|![Támogatott](media/azure-stack-deployment-decisions/check.png)| |
|Kapacitás alapján történő számlázáshoz|![Támogatott](media/azure-stack-deployment-decisions/check.png)|![Támogatott](media/azure-stack-deployment-decisions/check.png)|
|Töltse le a frissítési csomagok közvetlenül az Azure-verem|![Támogatott](media/azure-stack-deployment-decisions/check.png)|  |

Úgy döntött, az Azure kapcsolat modellben használandó Azure Alkalmazásveremben üzembe, miután további, a kapcsolat-függő döntéseket kell meghozni a identitás tárolására és a számlázási módszer. 

## <a name="next-steps"></a>További lépések
- További információ [Azure csatlakoztatott Azure verem telepítési döntések](azure-stack-connected-deployment.md)
- További információ [Azure leválasztása Azure verem telepítési döntések](azure-stack-disconnected-deployment.md)
