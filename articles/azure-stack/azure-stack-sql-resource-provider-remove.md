---
title: Az Azure Stacken SQL erőforrás-szolgáltató eltávolítása |} A Microsoft Docs
description: Ismerje meg, hogyan, hogy az SQL erőforrás-szolgáltató eltávolítása az Azure Stack üzemelő példányához.
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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: f5aa67ad0588e3f42e68056c8ffca97767975e8b
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361481"
---
# <a name="remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

Az SQL erőforrás-szolgáltató eltávolítása, előtt el kell távolítania a szolgáltató függőségeket. Emellett szüksége lesz egy példányát a központi telepítési csomag, amely az erőforrás-szolgáltató telepítéséhez használt.

Több karbantartási feladatot a futtatása előtt tegye a _DeploySqlProvider.ps1_ az erőforrás-szolgáltató eltávolítása parancsfájl.
A bérlők felelősek az alábbi karbantartási feladatokat:

* Törölje az összes adatbázisaikat az erőforrás-szolgáltató. (A bérlői adatbázisok törlésével nem törli az adatokat.)
* Az erőforrás-szolgáltató névtere regisztrációját.

A rendszergazda feladata a következő karbantartási feladatokat:

* Az üzemeltetési kiszolgáló törli az erőforrás-szolgáltató SQL.
* Törli azokat a csomagokat, amelyek az SQL erőforrás-szolgáltató hivatkoznak.
* Törli az erőforrás-szolgáltató SQL társított kvóták.

## <a name="to-remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

1. Győződjön meg arról, hogy eltávolította a meglévő SQL resource provider függőségeket.

   > [!NOTE]
   > Az SQL erőforrás-szolgáltató eltávolítása akkor is, ha a tőle függő erőforrások jelenleg használja az erőforrás-szolgáltató folytatódik.
  
2. Első bináris SQL erőforrás-szolgáltató egy példányát, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba.

3. Nyisson meg egy új emelt szintű PowerShell-konzolablakot, és váltson arra a könyvtárra, amelyben kibontotta az SQL resource provider bináris fájlokat.

4. Futtassa a DeploySqlProvider.ps1 parancsfájlt a következő paraméterekkel:

    * **Távolítsa el**. Eltávolítja az erőforrás-szolgáltató és az összes társított erőforrást.
    * **PrivilegedEndpoint**. Az IP-cím vagy a kiemelt végponthoz DNS-nevét.
    * **AzureEnvironment**. Az Azure-környezethez az Azure Stack üzembe helyezéséhez használt. Kizárólag az Azure AD központi telepítések esetén szükséges.
    * **CloudAdminCredential**. A felhő rendszergazdájához, a kiemelt végponthoz eléréséhez szükséges hitelesítő adatait.
    * **AzCredential**. Az Azure Stack szolgáltatás rendszergazdai fiók hitelesítő adatait. Használja az Azure Stack üzembe helyezéséhez használt hitelesítő adatokkal.

## <a name="next-steps"></a>További lépések

[Alkalmazás-szolgáltatások ajánlása PaaS-ként](azure-stack-app-service-overview.md)
