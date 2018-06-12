---
title: Az SQL Azure veremben erőforrás-szolgáltató eltávolítása |} Microsoft Docs
description: Ismerje meg, hogyan eltávolíthatja az SQL erőforrás-szolgáltató az Azure Alkalmazásveremben üzembe.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294780"
---
# <a name="removing-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató eltávolítása  
Mielőtt eltávolítaná a SQL erőforrás-szolgáltató, létfontosságú, először távolítsa el a függőségeket.

## <a name="remove-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató eltávolítása 

1. Győződjön meg arról, hogy eltávolította a meglévő SQL erőforrás-szolgáltató függőségek.

  > [!NOTE]
  > Az SQL erőforrás-szolgáltató eltávolítása folytatódik, még akkor is, ha a tőle függő erőforrások jelenleg használ az erőforrás-szolgáltató. 
  
2. Győződjön meg arról, hogy rendelkezik-e az eredeti központi telepítési csomag ezen verzióját az SQL erőforrás-szolgáltató adapter letöltött.
3. Futtassa újra a telepítési parancsfájl a következő paraméterekkel:
    - Használja a - paraméter eltávolítása
    - Az IP-cím vagy a kiemelt végpont DNS-nevét.
    - A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait.
    - Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Azure verem telepítéséhez használt használja ugyanazokat a hitelesítő adatokat.

## <a name="next-steps"></a>További lépések
[Mint Platformszolgáltatási ajánlat App Service szolgáltatások](azure-stack-app-service-overview.md)
