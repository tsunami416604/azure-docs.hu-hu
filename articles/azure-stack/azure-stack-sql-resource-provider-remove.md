---
title: Az SQL Azure veremben erőforrás-szolgáltató eltávolítása |} Microsoft Docs
description: Megtudhatja, hogyan, hogy az SQL erőforrás-szolgáltató eltávolítása a Azure Alkalmazásveremben üzembe.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 150d1c40463aa04527bdd6e356a4c24ef68b02ef
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301898"
---
# <a name="remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

Az SQL erőforrás-szolgáltató eltávolítása előtt el kell távolítania a szolgáltató függőségeit. A központi telepítési csomag, amely az erőforrás-szolgáltató telepítéséhez használt másolatát is szüksége lesz.

## <a name="to-remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

1. Győződjön meg arról, hogy eltávolította az összes meglévő SQL erőforrás szolgáltató függőségeit.

   > [!NOTE]
   > Az SQL erőforrás-szolgáltató eltávolítása folytatódik, még akkor is, ha a tőle függő erőforrások jelenleg használ az erőforrás-szolgáltató.
  
2. Első bináris SQL erőforrás-szolgáltató egy példányát, és futtassa a egy ideiglenes könyvtárhoz tartalmának önkibontó.

3. Nyisson meg egy új emelt szintű PowerShell-konzolablakot, és nyissa meg azt a könyvtárat, ahová kicsomagolta az SQL erőforrás szolgáltató bináris fájlok.

4. Futtassa a DeploySqlProvider.ps1 parancsfájlt a következő paraméterekkel:

    - **Távolítsa el**. Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrásokat.
    - **PrivilegedEndpoint**. Az IP-cím vagy a kiemelt végpont DNS-nevét.
    - **CloudAdminCredential**. A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait.
    - **AzCredential**. Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Az Azure-verem telepítéséhez használt hitelesítő használja.

## <a name="next-steps"></a>További lépések

[Mint Platformszolgáltatási ajánlat App Service szolgáltatások](azure-stack-app-service-overview.md)
