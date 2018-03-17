---
title: "Speciális Azure verem értékelési feladatok |} Microsoft Docs"
description: "Ez a cikk ismerteti a speciális Azure verem értékelésével kapcsolatos feladatok."
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
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: c4bf76aa07ec5025d9e53b5518929199ace27e18
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Speciális Azure verem szoftverfejlesztői készlet értékelésével kapcsolatos feladatok
Alapszintű Azure verem Development Kit (ASDK) szolgáltatás szolgáltatásainak és funkcióinak ismerete szereztek, miután a további Azure-vermet megértését is elmélyítsék a által speciális forgatókönyvek tesztelését. Speciális értékelési feladatok teljes leírása a verem operátor Azure dokumentációjában.

> [!NOTE]
> Számos operátor feladat támogatottak ASDK és a termelési, több csomópontos rendszerekhez. Azure-vermet, nem minden használati forgatókönyvek ASDK központi telepítések támogatottak. Lásd: [ASDK és több csomópontos Azure verem különbségek](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) további információt.

## <a name="delegate-offers-in-azure-stack"></a>Ajánlat delegálása az Azure Stackben
Az Azure verem operátorként érdemes, amelyre az ajánlatok létrehozása és a felhasználók regisztráljanak mások. Például ha egy szolgáltató, érdemes viszonteladók ügyfél regisztrálhat, és kezelheti azokat az Ön nevében. Vagy ha a vállalat egy központi informatikai csoport része, érdemes lehet a leányvállalatai a beavatkozás nélküli felhasználók regisztrálni.

[Azure-készletben ajánlatok delegálása](.\.\azure-stack-delegated-provider.md) segít éri el és felügyeli a felhasználók több, mint közvetlenül lehetővé teszi a feladatok. 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>SQL-adatbázisok Azure verem felhasználók számára történő elérhetővé
Kezelőként Azure verem is létrehozhat, amelyek segítségével a felhasználók ajánlatok (bérlőkkel), amely a felhő-natív alkalmazások, webhelyek és a munkaterhelések használhatják SQL-adatbázisok létrehozására. Egyéni, igény szerinti, felhőalapú adatbázist biztosít a felhasználók számára, mentheti azokat időt és erőforrásokat. 

Használja az SQL Server erőforrás szolgáltató adapter [SQL-adatbázisok Azure verem felhasználók számára történő elérhetővé](.\.\azure-stack-tutorial-sql-server.md) Azure verem szolgáltatásként. Az erőforrás-szolgáltató telepítését követően csatlakoztatja egy vagy több SQL Server-példányokat.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Webes és API-alkalmazások felhasználók számára elérhetővé az Azure-verem
Kezelőként Azure verem is létrehozhat, amelyek segítségével a felhasználók ajánlatok (bérlőkkel) az Azure Functions és a webes és API-alkalmazások létrehozása. Igény szerint a felhőalapú alkalmazások elérhetőségének biztosításával a felhasználók számára, mentheti azokat időt és erőforrásokat.

Az App Service az erőforrás-szolgáltató telepítéséhez [web- és API-alkalmazások felhasználók számára elérhetővé az Azure-verem](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>További lépések
[További tudnivalók az integrált Azure verem rendszerekkel szolgáltatásokat kínáló](.\.\azure-stack-offer-services-overview.md)