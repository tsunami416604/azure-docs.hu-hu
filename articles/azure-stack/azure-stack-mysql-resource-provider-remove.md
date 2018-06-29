---
title: A MySQL Azure veremben erőforrás-szolgáltató eltávolítása |} Microsoft Docs
description: Ismerje meg, hogyan eltávolíthatja a MySQL erőforrás-szolgáltató az Azure Alkalmazásveremben üzembe.
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: d3a615e3b92a62709a787d0463dfa3148f14d07e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085791"
---
# <a name="remove-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató eltávolítása

A MySQL erőforrás-szolgáltató eltávolítása előtt el kell távolítania a szolgáltató függőségeit. A központi telepítési csomag, amely az erőforrás-szolgáltató telepítéséhez használt másolatát is szüksége lesz.

## <a name="dependency-cleanup"></a>A függőségi karbantartása

Nincsenek több karbantartási feladatok ehhez az erőforrás-szolgáltató eltávolítása a DeployMySqlProvider.ps1 parancsfájl futtatása előtt.

A bérlők felelősek az alábbi karbantartási feladatokat:

* Az adatbázisok törlése az erőforrás-szolgáltató. (A bérlő adatbázisok törlése nem törli az adatokat.)
* A szolgáltató névterének regisztrációját.

A rendszergazda feladata a következő karbantartási feladatokat:

* Az üzemeltetési kiszolgáló törlése a MySQL-adaptert.
* Törli a MySQL Adapter hivatkozó bármely tervek.
* Törli a kvóták, amelyek a MySQL-Adapter társítva.

## <a name="to-remove-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató eltávolítása

1. Győződjön meg arról, hogy eltávolította az összes meglévő MySQL erőforrás szolgáltató függőségeit.

   >[!NOTE]
   >A MySQL erőforrás-szolgáltató eltávolítása folytatódik, még akkor is, ha a tőle függő erőforrások jelenleg használ az erőforrás-szolgáltató.
  
2. Első bináris MySQL erőforrás-szolgáltató egy példányát, és futtassa a egy ideiglenes könyvtárhoz tartalmának önkibontó.
3. Első bináris SQL erőforrás-szolgáltató egy példányát, és futtassa a egy ideiglenes könyvtárhoz tartalmának önkibontó.
4. Nyisson meg egy új emelt szintű PowerShell-konzolablakot, és váltson arra a könyvtárra, amelybe kibontotta a MySQL erőforrás szolgáltató bináris fájlokat.
5. Futtassa a DeployMySqlProvider.ps1 parancsfájlt a következő paraméterekkel:
    - **Távolítsa el**. Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrásokat.
    - **PrivilegedEndpoint**. Az IP-cím vagy a kiemelt végpont DNS-nevét.
    - **CloudAdminCredential**. A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait.
    - **DirectoryTenantID**
    - **AzCredential**. Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Az Azure-verem telepítéséhez használt hitelesítő használja.

## <a name="next-steps"></a>További lépések

[Mint Platformszolgáltatási ajánlat App Service szolgáltatások](azure-stack-app-service-overview.md)
