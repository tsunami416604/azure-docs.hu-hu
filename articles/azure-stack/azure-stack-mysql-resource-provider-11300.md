---
title: Az Azure Stack MySQL erőforrás szolgáltató 1.1.30.0 kibocsátási megjegyzései |} A Microsoft Docs
description: További tudnivalók a a legújabb Azure Stack MySQL erőforrás szolgáltató frissítése, beleértve az olyan ismert problémákat, és a helyét, ahonnan letöltheti azt.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 6354212eb95fbefb217dd5339613d050da55f4ba
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688139"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>MySQL erőforrás-szolgáltató 1.1.30.0 kibocsátási megjegyzései

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések a fejlesztéseket és a MySQL erőforrás-szolgáltató verziója 1.1.30.0 az ismert problémákat ismertetik.

## <a name="build-reference"></a>Hivatkozás létrehozása
Töltse le a MySQL erőforrás-szolgáltató bináris, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba. Az erőforrás-szolgáltató rendelkezik egy minimális megfelelő Azure Stack hozhat létre. Ez a verzió, a MySQL erőforrás-szolgáltató telepítéséhez szükséges minimális Azure Stack-verzió alább:

> |Azure Stack minimális verziója|MySQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |Azure Stack 1808 frissítése (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> A minimális támogatott az Azure Stack-frissítés alkalmazása az Azure Stackkel integrált rendszereknél, vagy a legújabb Azure Stack Development Kit (ASDK) telepíteni a MySQL erőforrás-szolgáltató legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal
Az Azure Stack MySQL erőforrás-szolgáltató ezen verziója tartalmazza a következő fejlesztések és javítások:

- **MySQL erőforrás-szolgáltató telepítések engedélyezve telemetriai**. Eszköztelemetria-gyűjtést MySQL erőforrás-szolgáltató telepítések engedélyezve van. Gyűjtött telemetriai tartalmazza az erőforrás-szolgáltató üzembe helyezési, indítsa el záró időpontját, lépjen ki a állapot, a kilépési üzenetet és a hiba részletei (ha van).

- **A TLS 1.2-es titkosítási frissítés**. Engedélyezve a TLS 1.2-es csak támogatja a belső Azure Stack-összetevők erőforrás-szolgáltató kommunikál. 

### <a name="fixes"></a>Javítások

- **MySQL erőforrás-szolgáltató az Azure Stack PowerShell tesztkiszolgálókon**. A MySQL erőforrás-szolgáltató frissítve lett az Azure Stack 2018-03-01-hibrid PowerShell-profillal, és kompatibilitást biztosítanak AzureRM 1.3.0 a munkahelyi és a későbbi.

- **MySQL bejelentkezési Módosítsa jelszavát panel**. Javítva lett egy probléma, ahol a jelszó nem lehet módosítani a jelszó módosítása panelen. Jelszó eltávolított hivatkozások fájlmódosítási értesítések.

## <a name="known-issues"></a>Ismert problémák 

- **MySQL termékváltozatot órát is igénybe vehet egy lesznek láthatók a portálon**. Is igénybe vehet egy órát újonnan létrehozott termékváltozatok lesznek láthatók a használatra, amikor új MySQL-adatbázisok létrehozásához. 

    **Megkerülő megoldás**: nincs.

- **Újra felhasználhatók a MySQL-bejelentkezések**. Hozzon létre egy új MySQL próbál jelentkezzen be a felhasználónevet, egy meglévő bejelentkezést egy előfizetésen belül eredményez újbóli felhasználása ugyanazokat a bejelentkezési adatokat és a meglévő jelszót. 

    **Megkerülő megoldás**: azonos előfizetéshez tartozó új bejelentkezéseket létrehozásakor különböző felhasználónevet használ, vagy hozzon létre bejelentkezések a különböző előfizetéseknél felhasználónevet.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Azure Stack működő felhő rendszergazdái számára ismert problémák
A dokumentáció a [Azure Stack kibocsátási megjegyzések](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>További lépések
[További információ a MySQL erőforrás-szolgáltató](azure-stack-mysql-resource-provider.md).

[A MySQL erőforrás-szolgáltató telepítésének előkészítéséhez](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[A MySQL erőforrás-szolgáltató frissítés egy korábbi verziójáról](azure-stack-mysql-resource-provider-update.md). 