---
title: A felügyelt identitások megadása az Azure Synapse-munkaterületen
description: Egy cikk, amely bemutatja, hogyan konfigurálhatja a felügyelt identitás engedélyeit az Azure Synapse-munkaterületen.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428016"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Engedélyek megadása munkaterület által felügyelt identitáshoz (előzetes verzió)

Ez a cikk bemutatja, hogyan adhat engedélyeket a felügyelt identitás az Azure synapse munkaterületen. Az engedélyek viszont lehetővé teszik az SQL-készletek elérését a munkaterületen és az ADLS gen2 tárfiókhoz az Azure Portalon keresztül.

>[!NOTE]
>Ezt a munkaterület által felügyelt identitást a dokumentum többi részén felügyelt identitásként nevezzük.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>A felügyelt identitásengedélyek megadása az SQL-készletnek

A felügyelt identitás engedélyeket ad a munkaterület SQL-készleteinek. A megadott engedélyekkel az SQL-készlettel kapcsolatos tevékenységeket végző folyamatokat vezényelheti. Amikor létrehoz egy Azure Synapse-munkaterületet az Azure Portal használatával, a felügyelt identitás-control engedélyeket az SQL-készletek.

Válassza **a Biztonság + hálózatkezelés** lehetőséget az Azure Synapse-munkaterület létrehozásakor. Ezután válassza **a Vezérlés megadása a munkaterület felügyelt identitásához az SQL-készleteken**lehetőséget.

![VEZÉRLÉSi engedély SQL-készletekhez](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>A felügyelt identitásengedélyek megadása az ADLS gen2 tárfióknak

Az Azure Synapse-munkaterület létrehozásához ADLS gen2 tárfiók szükséges. A Spark-készletek sikeres elindításához az Azure Synapse-munkaterületen az Azure Synapse felügyelt identitásának szüksége van a *Storage Blob Data Contributor* szerepkörezen a tárfiókon. Az Azure Synapse-ban a folyamatfolyamatok vezénylése is profitál ebből a szerepkörből.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Engedélyek megadása felügyelt identitáshoz a munkaterület létrehozása során

Az Azure Synapse megpróbálja megadni a Storage Blob Data Contributor szerepkört a felügyelt identitásnak, miután létrehozta az Azure Synapse munkaterületet az Azure Portalhasználatával. Az ADLS gen2 tárfiók adatait az **Alapok** lapon adja meg.

![Az Alapok lap a munkaterület-létrehozási folyamatban](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Válassza az ADLS gen2 tárfiókot és fájlrendszert a **Fiók név** és **a Fájlrendszer neve területen.**

![ADLS gen2 tárfiók adatainak megadása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Ha a munkaterület létrehozója is **tulajdonosa** az ADLS gen2 tárfiók, majd az Azure Synapse hozzárendeli a *Storage Blob Data Contributor* szerepkört a felügyelt identitáshoz. A következő üzenet jelenik meg a megadott tárfiók adatai alatt.

![Sikeres Storage Blob Data Contributor-hozzárendelés](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Ha a munkaterület létrehozója nem az ADLS gen2 storage-fiók tulajdonosa, majd az Azure Synapse nem rendeli hozzá a *Storage Blob Data Contributor* szerepkört a felügyelt identitáshoz. A tárfiók részletei alatt megjelenő üzenet értesíti a munkaterület készítőjét, hogy nem rendelkeznek megfelelő engedélyekkel ahhoz, hogy a *Storage Blob Data Contributor* szerepkört a felügyelt identitásnak adják.

![Sikertelen Storage Blob Data Contributor-hozzárendelés](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Ahogy az üzenet kimondja, nem hozhat létre Spark-készletek, kivéve, ha a *storage blob adatközreműködő* van rendelve a felügyelt identitáshoz.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Engedélyek megadása felügyelt identitáshoz a munkaterület létrehozása után

A munkaterület létrehozása során, ha nem rendeli hozzá a *Storage Blob Data contributor a* felügyelt identitás, majd a **tulajdonos** az ADLS gen2 tárfiók manuálisan hozzárendeli ezt a szerepkört az identitáshoz. A következő lépések segítenek a manuális hozzárendelés végrehajtásában.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>1. lépés: Keresse meg az ADLS gen2 tárfiókot az Azure Portalon

Az Azure Portalon nyissa meg az ADLS gen2 tárfiókot, és válassza **a bal** oldali navigációs áttekintés lehetőséget. Csak a Storage Blob *Data Contributor* szerepkört kell hozzárendelnie a tároló vagy a fájlrendszer szintjén. Válassza a **Tárolók**lehetőséget.  
![ADLS gen2 tárfiók – áttekintés](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>2. lépés: Válassza ki a tárolót

A felügyelt identitásnak adathozzáféréssel kell rendelkeznie a munkaterület létrehozásakor megadott tárolóhoz (fájlrendszerhez). Ez a tároló vagy a fájlrendszer az Azure Portalon található. Nyissa meg az Azure Synapse munkaterületet az Azure Portalon, és válassza az **Áttekintés** lapot a bal oldali navigációs sávon.
![ADLS gen2 tárfiók tároló](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Válassza ki ugyanazt a tárolót vagy fájlrendszert a *Storage Blob Data Contributor* szerepkör nek a felügyelt identitásnak.
![ADLS gen2 tárfiók tárolójának kiválasztása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>3. lépés: Navigálás a Hozzáférés-vezérlésre

Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.**

![Hozzáférés-vezérlés (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>4. lépés: Új szerepkör-hozzárendelés hozzáadása

Válassza a **+ Hozzáadás** lehetőséget.

![Új szerepkör-hozzárendelés hozzáadása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>5. lépés: Válassza ki az RBAC szerepkört

Válassza ki a **Storage Blob Data Contributor** szerepkört.

![Az RBAC szerepkör kiválasztása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>6. lépés: Válassza ki az Azure AD rendszerbiztonsági tag

Válassza ki az **Azure AD-felhasználó, -csoport vagy egyszerű szolgáltatás** a Hozzáférés **hozzárendelése legördülő** menüből.

![AAD rendszerbiztonsági tag kiválasztása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>7. lépés: A felügyelt identitás keresése

A felügyelt identitás neve egyben a munkaterület neve is. Keresse meg a felügyelt identitást az Azure Synapse munkaterület nevének megadásával a **Select területen.** Meg kell jelennie a felügyelt identitás szerepel.

![A felügyelt identitás megkeresése](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>8. lépés: Válassza ki a felügyelt identitást

Válassza ki a felügyelt identitást a **kijelölt tagok számára.** A **szerepkör-hozzárendelés** hozzáadásához válassza a Mentés lehetőséget.

![A felügyelt identitás kiválasztása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>9. lépés: Ellenőrizze, hogy a Storage Blob Data Contributor szerepkör hozzá van-e rendelve a felügyelt identitáshoz

Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget,** majd a **Szerepkör-hozzárendelések**lehetőséget.

![Szerepkör-hozzárendelés ellenőrzése](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

A felügyelt identitás nak a **Storage Blob Data Contributor** szakaszban kell szerepelnie, és hozzá kell rendelnie a Storage Blob Data *Contributor* szerepkört. 
![ADLS gen2 tárfiók tárolójának kiválasztása](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>További lépések

További információ a [Munkaterület által felügyelt identitásról](./synapse-workspace-managed-identity.md)
