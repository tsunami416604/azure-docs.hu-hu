---
title: Adattitkosítás – Azure Portal – Azure Database for MySQL
description: Megtudhatja, hogyan állíthatja be és kezelheti a Azure Database for MySQL adattitkosítását a Azure Portal használatával.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.openlocfilehash: c3bb15e494638d543795ac5b95e2513cb5871a2a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118630"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure Database for MySQL adattitkosítása a Azure Portal használatával

Megtudhatja, hogyan állíthatja be és kezelheti a Azure Database for MySQL adattitkosítását a Azure Portal használatával.

## <a name="prerequisites-for-azure-cli"></a>Az Azure CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
* A Azure Key Vault-ban hozzon létre egy kulcstartót és egy, az ügyfél által felügyelt kulcshoz használandó kulcsot.
* A Key vaultnak a következő tulajdonságokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * [Helyreállítható törlés](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Védett kiürítés](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A kulcsnak a következő attribútumokkal kell rendelkeznie, amelyeket ügyfél által felügyelt kulcsként kell használni:
  * Nincs lejárati dátum
  * Nincs letiltva
  * Képes a Get, a wrap Key, a dewrap Key Operations művelet végrehajtására

## <a name="set-the-right-permissions-for-key-operations"></a>A megfelelő engedélyek beállítása a kulcsfontosságú műveletekhez

1. A Key Vault területen válassza a **hozzáférési szabályzatok**  >  **hozzáférési házirend hozzáadása**lehetőséget.

   ![Képernyőkép a Key Vaultről, hozzáférési házirendekkel, és Kiemelt hozzáférési szabályzat hozzáadása](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Válassza a **kulcs engedélyei**lehetőséget, majd válassza a **beolvasás**, **becsomagolás**, **kicsomagolás**és a **rendszerbiztonsági tag**lehetőséget, amely a MySQL-kiszolgáló neve. Ha a kiszolgáló rendszerbiztonsági tagja nem található a meglévő rendszerbiztonsági tag listájában, regisztrálnia kell. A rendszer arra kéri, hogy regisztrálja a kiszolgálói rendszerbiztonsági tag-t, amikor első alkalommal kísérli meg az adattitkosítás beállítását, és sikertelen lesz.

   ![Hozzáférési szabályzat – áttekintés](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Kattintson a **Mentés** gombra.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Adattitkosítás beállítása Azure Database for MySQLhoz

1. Az ügyfél által felügyelt kulcs beállításához Azure Database for MySQL válassza az **adattitkosítás** lehetőséget.

   ![Képernyőkép a Azure Database for MySQLről, az adattitkosítás kiemelésével](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Kijelölhet egy kulcstartót és egy kulcspárt, vagy megadhatja a kulcs azonosítóját is.

   ![Képernyőkép a Azure Database for MySQLről, az adattitkosítási lehetőségek kiemelésével](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Kattintson a **Mentés** gombra.

4. Annak biztosítása érdekében, hogy az összes fájl (beleértve az ideiglenes fájlokat is) teljes mértékben titkosítva legyen, indítsa újra a kiszolgálót.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Adattitkosítás használata visszaállítási vagy replika kiszolgálók esetén

Miután Azure Database for MySQL titkosítása megtörténik a Key Vault tárolt ügyfél felügyelt kulcsával, a kiszolgáló minden újonnan létrehozott példánya is titkosítva lesz. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel vagy replika (helyi/régió) művelettel teheti meg. A titkosított MySQL-kiszolgálók esetében a következő lépésekkel hozhat létre titkosított visszaállított kiszolgálót.

1. A kiszolgálón válassza az **Áttekintés**  >  **visszaállítás**lehetőséget.

   ![Képernyőkép a Azure Database for MySQLről, áttekintés és visszaállítás kiemelve](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Vagy replikálásra alkalmas kiszolgáló esetén a **Beállítások** fejléc alatt válassza a **replikálás**lehetőséget.

   ![Képernyőkép a Azure Database for MySQLról, a replikálás kiemelve](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. A visszaállítási művelet befejezése után a létrehozott új kiszolgáló az elsődleges kiszolgáló kulcsával lesz titkosítva. A kiszolgáló szolgáltatásai és beállításai azonban le vannak tiltva, és a kiszolgáló nem érhető el. Ez megakadályozza az adatkezelést, mert az új kiszolgáló identitása még nem kapott engedélyt a kulcstartó elérésére.

   ![Képernyőkép a Azure Database for MySQLről, a nem elérhető állapot kiemelésével](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. A kiszolgáló elérhetővé tételéhez érvényesítse újra a kulcsot a visszaállított kiszolgálón. Válassza **az adattitkosítás**  >  **újraellenőrzése kulcsot**.

   > [!NOTE]
   > Az első újraellenőrzési kísérlet sikertelen lesz, mert az új kiszolgáló egyszerű szolgáltatásának hozzáférést kell adni a kulcstartóhoz. Az egyszerű szolgáltatásnév létrehozásához válassza a **kulcs újraérvényesítése**lehetőséget, amely hibaüzenetet jelenít meg, de létrehozza az egyszerű szolgáltatásnevet. Ezt követően tekintse meg a jelen cikk korábbi részében [ismertetett lépéseket](#set-the-right-permissions-for-key-operations) .

   ![Képernyőkép a Azure Database for MySQLról, az újraérvényesítési lépés kiemelve](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   A Key vaultnak hozzáférést kell adnia az új kiszolgálóhoz.

4. Az egyszerű szolgáltatás regisztrálását követően ismét ellenőrizze újra a kulcsot, és a kiszolgáló folytatja a normál működést.

   ![Képernyőkép a Azure Database for MySQLről, amely a visszaállított funkciókat mutatja](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Következő lépések

 Az adattitkosítással kapcsolatos további tudnivalókért tekintse meg az [adattitkosítás Azure Database for MySQL az ügyfél által felügyelt kulccsal](concepts-data-encryption-mysql.md)című témakört.
