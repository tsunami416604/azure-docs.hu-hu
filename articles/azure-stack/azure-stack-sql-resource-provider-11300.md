---
title: Az Azure Stack SQL resource provider 1.1.30.0 kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg, mi az a legújabb Azure Stack SQL resource provider frissítések, beleértve az olyan ismert problémákat és helyét, ahonnan letöltheti azt.
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
ms.openlocfilehash: d2dda25c63a6e4a73205b9e4a830a211d025b3ed
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688143"
---
# <a name="sql-resource-provider-11300-release-notes"></a>SQL erőforrás-szolgáltató 1.1.30.0 kibocsátási megjegyzései

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések a javításokat és a SQL-erőforrás-szolgáltató verziója 1.1.30.0 ismert problémákat ismertetik.

## <a name="build-reference"></a>Hivatkozás létrehozása
Töltse le az SQL erőforrás-szolgáltató bináris, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba. Az erőforrás-szolgáltató rendelkezik egy minimális megfelelő Azure Stack hozhat létre. Ezen verziója az SQL erőforrás-szolgáltató telepítéséhez szükséges minimális Azure Stack-verzió alább:

> |Azure Stack minimális verziója|Erőforrás-szolgáltató az SQL verziója|
> |-----|-----|
> |Verzió 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> A minimális támogatott az Azure Stack-frissítés alkalmazása az Azure Stackkel integrált rendszereknél, vagy üzembe helyezése a legújabb Azure Stack Development Kit (ASDK) az erőforrás-szolgáltató az SQL legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal
Az Azure Stack SQL erőforrás-szolgáltató ezen verziója tartalmazza a következő fejlesztések és javítások:

- **Engedélyezve van az erőforrás-szolgáltató telepítések SQL telemetriai**. Eszköztelemetria-gyűjtést SQL erőforrás-szolgáltató telepítések engedélyezve van. Gyűjtött telemetriai tartalmazza az erőforrás-szolgáltató üzembe helyezési, indítsa el záró időpontját, lépjen ki a állapot, a kilépési üzenetet és a hiba részletei (ha van).

- **A TLS 1.2-es titkosítási frissítés**. Engedélyezve a TLS 1.2-es csak támogatja a belső Azure Stack-összetevők erőforrás-szolgáltató kommunikál. 

### <a name="fixes"></a>Javítások

- **SQL erőforrás-szolgáltató az Azure Stack PowerShell tesztkiszolgálókon**. Az SQL erőforrás-szolgáltató frissítve lett az Azure Stack 2018-03-01-hibrid PowerShell-profillal, és kompatibilitást biztosítanak AzureRM 1.3.0 a munkahelyi és a későbbi.

- **Az SQL bejelentkezési Módosítsa jelszavát panel**. Javítva lett egy probléma, ahol a jelszó nem lehet módosítani a jelszó módosítása panelen. Jelszó eltávolított hivatkozások fájlmódosítási értesítések.

- **Az SQL futtató kiszolgálót a beállítások panelen frissítése**. Javítva lett egy probléma, ahol a beállítások panelen helytelenül címe, a "Password".

## <a name="known-issues"></a>Ismert problémák 

- **SQL-termékváltozatok órát is igénybe vehet egy lesznek láthatók a portálon**. Is igénybe vehet egy órát újonnan létrehozott termékváltozatok lesznek láthatók a használatra, amikor új SQL-adatbázisok létrehozásához. 

    **Megkerülő megoldás**: nincs.

- **Fel újra az SQL-bejelentkezésekben**. Próbál létrehozni egy új SQL jelentkezzen be a felhasználónevet, egy előfizetésen belül egy meglévő bejelentkezést eredményez szakember újból felhasználja a meglévő jelszót, és ugyanazokat a bejelentkezési adatokat. 

    **Megkerülő megoldás**: azonos előfizetéshez tartozó új bejelentkezéseket létrehozásakor különböző felhasználónevet használ, vagy hozzon létre bejelentkezések a különböző előfizetéseknél felhasználónevet.

- **Megosztott SQL-bejelentkezésekben adatinkonzisztenciát okozhat**. Ha az egy előfizetésen belül több SQL-adatbázis egy SQL-bejelentkezési megosztott, a bejelentkezési jelszó módosítása adatinkonzisztenciát okoz.

    **Megkerülő megoldás**: mindig használja a különböző bejelentkezések azonos előfizetéshez tartozó különböző adatbázisai számára.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Azure Stack működő felhő rendszergazdái számára ismert problémák
A dokumentáció a [Azure Stack kibocsátási megjegyzések](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>További lépések
[További információ az SQL erőforrás-szolgáltató](azure-stack-sql-resource-provider.md).

[Az SQL erőforrás-szolgáltató telepítésének előkészítéséhez](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Az erőforrás-szolgáltató SQL frissítés egy korábbi verziójáról](azure-stack-sql-resource-provider-update.md). 