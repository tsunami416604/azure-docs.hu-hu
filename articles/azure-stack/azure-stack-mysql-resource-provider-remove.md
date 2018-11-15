---
title: Az Azure Stacken MySQL erőforrás-szolgáltató eltávolítása |} A Microsoft Docs
description: Ismerje meg, hogyan távolíthat el a MySQL erőforrás-szolgáltató az Azure Stack üzemelő példányához.
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
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 7d3b0e179972464a1ed857c576ca8a7c8fc2e162
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686804"
---
# <a name="remove-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató eltávolítása

A MySQL erőforrás-szolgáltató eltávolítása, előtt el kell távolítania a szolgáltató függőségeket. Emellett szüksége lesz egy példányát a központi telepítési csomag, amely az erőforrás-szolgáltató telepítéséhez használt.

  |Azure Stack minimális verziója|MySQL-RP-verzió|
  |-----|-----|
  |Verzió 1808 (1.1808.0.97)|[MySQL-RP 1.1.30.0 verzió](https://aka.ms/azurestacksqlrp11300)|
  |Verzió 1804 (1.0.180513.1)|[MySQL-RP 1.1.24.0 verzió](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

## <a name="dependency-cleanup"></a>Függőségi karbantartása

Nincsenek ehhez az erőforrás-szolgáltató eltávolítása a DeployMySqlProvider.ps1 parancsfájl futtatása előtt több karbantartási feladatot.

Az Azure Stack-bérlő felhasználók felelősek az alábbi karbantartási feladatokat:

* Törölje az összes adatbázisaikat az erőforrás-szolgáltató. (A bérlői adatbázisok törlésével nem törli az adatokat.)
* A szolgáltató névterének neve regisztrációját.

Az Azure Stack – operátor felelős az alábbi karbantartási feladatokat:

* Az üzemeltetési kiszolgáló törlése a MySQL-adaptert.
* Törli azokat a csomagokat, amelyek a MySQL-Adapter hivatkoznak.
* Törli a MySQL-adapterhez társított kvóták.

## <a name="to-remove-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató eltávolítása

1. Győződjön meg arról, hogy eltávolította a meglévő MySQL erőforrás szolgáltató függőségeket.

   >[!NOTE]
   >A MySQL erőforrás-szolgáltató eltávolítása akkor is, ha a tőle függő erőforrások jelenleg használja az erőforrás-szolgáltató folytatódik.
  
2. A MySQL erőforrás-szolgáltató egy példányának bináris beszerzéséhez, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba.
3. Első bináris SQL erőforrás-szolgáltató egy példányát, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba.
4. Nyisson meg egy új emelt szintű PowerShell-konzolablakot, és váltson arra a könyvtárra, amelyben kibontotta a MySQL erőforrás szolgáltató bináris fájlokat.
5. Futtassa a DeployMySqlProvider.ps1 parancsfájlt a következő paraméterekkel:
    - **Távolítsa el**. Eltávolítja az erőforrás-szolgáltató és az összes társított erőforrást.
    - **PrivilegedEndpoint**. Az IP-cím vagy a kiemelt végponthoz DNS-nevét.
    - **AzureEnvironment**. Az Azure-környezethez az Azure Stack üzembe helyezéséhez használt. Kizárólag az Azure AD központi telepítések esetén szükséges.
    - **CloudAdminCredential**. A felhő rendszergazdájához, a kiemelt végponthoz eléréséhez szükséges hitelesítő adatait.
    - **DirectoryTenantID**
    - **AzCredential**. Az Azure Stack szolgáltatás rendszergazdai fiók hitelesítő adatait. Használja az Azure Stack üzembe helyezéséhez használt hitelesítő adatokkal.

## <a name="next-steps"></a>További lépések

[Alkalmazás-szolgáltatások ajánlása PaaS-ként](azure-stack-app-service-overview.md)
