---
title: Hitelesítő adatok létrehozása és kezelése a vizsgálatokhoz
description: Ez a cikk a hitelesítő adatok Azure-ban való létrehozásának és kezelésének lépéseit ismerteti.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 4c964f3661e120026189a75d331e6db975b41c70
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2020
ms.locfileid: "97756075"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Az Azure hatáskörébe tartozó forrás-hitelesítés hitelesítő adatai

Ez a cikk azt ismerteti, hogyan hozhat létre hitelesítő adatokat az Azure-ban, hogy gyorsan újrahasznosítsa az adatokat, és alkalmazza a mentett hitelesítési adatokat az adatforrások vizsgálatára.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Key Vault. A létrehozásával kapcsolatos információkért lásd: rövid útmutató [: kulcstartó létrehozása a Azure Portal használatával](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Bevezetés
A hitelesítő adatok olyan hitelesítési információk, amelyeket az Azure hatáskörébe használhat a regisztrált adatforrásokhoz való hitelesítéshez. Egy hitelesítőadat-objektum hozható létre különféle hitelesítési forgatókönyvekhez (például a Felhasználónév/jelszó megkövetelése egyszerű hitelesítéshez), és a kiválasztott hitelesítési módszer alapján rögzíti a szükséges információkat. A hitelesítő adatok a meglévő Azure Key Vault-titkokat használják a bizalmas hitelesítési információk beolvasására a hitelesítő adatok létrehozásakor.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>A hatáskörébe tartozó felügyelt identitás használata a vizsgálatok beállításához
Ha a hatáskörébe tartozó felügyelt identitást használja a vizsgálatok beállításához, akkor nem kell explicit módon létrehoznia a hitelesítő adatokat, és a Key vaultot csatolnia kell a hatáskörébe, hogy tárolja őket. A hatáskörébe tartozó felügyelt identitásnak az adatforrások vizsgálatához való hozzáadásával kapcsolatos részletes utasításokért tekintse meg az alábbi, az adatforrás-specifikus hitelesítési szakaszt alább:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [1. generációs Azure Data Lake Storage](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Felügyelt Azure SQL Database-példány](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure Key Vault-kapcsolatok létrehozása az Azure hatáskörébe tartozó fiókban

A hitelesítő adatok létrehozása előtt először hozzá kell rendelnie egy vagy több meglévő Azure Key Vault példányát az Azure-beli hatáskörébe-fiókjához.

1. Az Azure hatáskörébe navigációs menüben navigáljon a felügyeleti központhoz, majd navigáljon a hitelesítő adatokhoz.

2. A hitelesítő adatok parancssáv lapon válassza a Key Vault kapcsolatok kezelése lehetőséget.

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="AKV-kapcsolatok kezelése":::

3. Válassza az + új elemet a kezelés Key Vault kapcsolatok panelen 

4. Adja meg a szükséges adatokat, majd válassza a létrehozás lehetőséget.

5. Győződjön meg arról, hogy a Key Vault sikeresen hozzá lett rendelve az Azure-beli hatáskörébe-fiókjához

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="AKV-kapcsolatok megtekintése":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Adja meg a hatáskörébe tartozó felügyelt identitás hozzáférést a Azure Key Vault

Navigáljon a Key vaulthoz – > hozzáférési szabályzatok – > hozzáférési házirend hozzáadása lehetőségre. Adja meg a Get engedélyt a Secrets engedély legördülő menüben, és válassza az elsődleges elemet a hatáskörébe MSI. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="A hatáskörébe MSI hozzáadása a AKV-hez":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Hozzáférési szabályzat hozzáadása":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Hozzáférési szabályzat mentése":::

## <a name="create-a-new-credential"></a>Új hitelesítő adat létrehozása

A hatáskörébe jelenleg a hitelesítő adatok típusa támogatott:
* Alapszintű hitelesítés: a **jelszót** a Key Vault titkos kulcsaként fogja felvenni
* Egyszerű szolgáltatásnév: az **egyszerű szolgáltatásnév kulcsát** hozzáadja a Key Vault titkos kódjához 
* SQL-hitelesítés: a **jelszót** a Key vaultban titkos kulcsként fogja felvenni
* Fiók kulcsa: a Key Vault titkos kulcsaként adja hozzá a **fiók kulcsát**

További információ: [Secret hozzáadása Key Vaulthoz](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

A titkos kulcsoknak a kulcstartóban való tárolása után hozza létre az új hitelesítő adatokat a hitelesítő adatokkal rendelkező parancssáv + új elemének kiválasztásával. Adja meg a szükséges információkat, beleértve a hitelesítési módszer kiválasztását és egy Key Vault példányt, amelyből ki szeretne választani egy titkos kulcsot. Az összes adat kitöltése után kattintson a Létrehozás gombra.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Új hitelesítő adat":::

Ellenőrizze, hogy az új hitelesítő adatok megjelennek-e a hitelesítőadat-lista nézetben, és készen áll-e a használatra

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Hitelesítő adat megtekintése":::

## <a name="manage-your-key-vault-connections"></a>Key Vault-kapcsolatok kezelése

1. Keresés/keresés Key Vault kapcsolatok név szerint

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Keresés a Key vaultban":::

1. Egy vagy több Key Vault kapcsolat törlése
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Key Vault törlése":::

## <a name="manage-your-credentials"></a>Hitelesítő adatok kezelése

1. Hitelesítő adatok keresése/keresése név szerint
  
2. Meglévő hitelesítő adatok kiválasztása és frissítése

3. Egy vagy több hitelesítő adat törlése
