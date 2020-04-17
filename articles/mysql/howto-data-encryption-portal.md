---
title: Adattitkosítás - Azure portal – Azure Database for MySQL
description: Megtudhatja, hogyan állíthatja be és kezelheti az Azure Database for MySQL adattitkosítását az Azure Portal használatával.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 9d1e89919647d9d94b287618da2f9a77278425a5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459083"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Az Azure Database for MySQL adattitkosítása az Azure Portal használatával

Ismerje meg, hogyan használhatja az Azure Portalon az Azure-adatbázis mySQL-hez történő adattitkosításának beállításához és kezeléséhez.

## <a name="prerequisites-for-azure-cli"></a>Az Azure CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és az adott előfizetés rendszergazdájának kell lennie.
* Az Azure Key Vaultban hozzon létre egy key vaultot és egy kulcsot, amelyet egy ügyfél által felügyelt kulcshoz használhat.
* A kulcstartónak a következő tulajdonságokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * [Helyreállítható törlés](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Tisztítás védett](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A kulcsnak a következő attribútumokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * Nincs lejárati dátum
  * Nincs letiltva
  * Képes végrehajtani kap, wrap kulcs, kicsomagolni kulcs műveletek

## <a name="set-the-right-permissions-for-key-operations"></a>A kulcsműveletekhez szükséges engedélyek beállítása

1. A Key Vaultban válassza **az Access-házirendek** > **hozzáférési házirendhozzáadása lehetőséget.**

   ![Képernyőkép a Key Vaultról, kiemelve az Access-házirendek és a Hozzáférési szabályzat hozzáadása](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Válassza a **Kulcsengedélyek lehetőséget,** majd a **Befolyás**, **Befolyás**, **Kicsomagolás**és a **Típus**lehetőséget, amely a MySQL-kiszolgáló neve. Ha a kiszolgáló egyszerű nem található a meglévő egyszerű stanév listában, regisztrálnia kell azt. Amikor első alkalommal próbálja meg beállítani az adattitkosítást, a rendszer kéri a kiszolgáló egyszerű regisztrálását, és az sikertelen lesz.

   ![Hozzáférési házirend – áttekintés](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Kattintson a **Mentés** gombra.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Adattitkosítás beállítása a MySQL-hez készült Azure Database számára

1. Az Azure Database for MySQL,válassza **az adatok titkosítása** az ügyfél által felügyelt kulcs beállítása.

   ![Képernyőkép a MySQL Azure Database szolgáltatásáról, kiemelve az adattitkosítással](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Választhat egy kulcstartót és kulcspárt, vagy megadhat egy kulcsazonosítót.

   ![Képernyőkép a MySQL Azure Database szolgáltatásáról, kiemelt adattitkosítási beállításokkal](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Kattintson a **Mentés** gombra.

4. Annak érdekében, hogy az összes fájl (beleértve az ideiglenes fájlokat is) teljesen titkosítva legyen, indítsa újra a kiszolgálót.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Adattitkosítás használata visszaállítási vagy replikakiszolgálókhoz

Miután az Azure Database for MySQL titkosítva van az ügyfél által a Key Vaultban tárolt kezelt kulccsal, a kiszolgáló újonnan létrehozott példányais titkosítva lesz. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel, vagy egy replika (helyi/régióközi) művelettel is elkészítheti. Így egy titkosított MySQL szerver, akkor a következő lépéseket, hogy hozzon létre egy titkosított visszaállított szerver.

1. A kiszolgálón válassza az **Áttekintés** > **visszaállítása**lehetőséget.

   ![Képernyőkép a MySQL Azure Database szolgáltatásáról, kiemelve az Áttekintés és a Visszaállítás lehetőséggel](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Vagy replikációt támogató kiszolgáló esetén a **Beállítások** fejlécalatt válassza a **Replikáció**lehetőséget.

   ![Képernyőkép a MySQL Azure Database szolgáltatásáról, kiemelve a replikáció](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. A visszaállítási művelet befejezése után a létrehozott új kiszolgáló az elsődleges kiszolgáló kulccsal lesz titkosítva. A kiszolgáló szolgáltatásai és beállításai azonban le vannak tiltva, és a kiszolgáló nem érhető el. Ez megakadályozza az adatok manipulálását, mert az új kiszolgáló identitása még nem kapott engedélyt a kulcstartó elérésére.

   ![Képernyőkép a MySQL Azure Database szolgáltatásáról, kiemelve a Nem elérhető állapotgal](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. A kiszolgáló akadálymentessé tétele érdekében érvényesítse újra a kulcsot a visszaállított kiszolgálón. Válassza **az Adattitkosítás** > **újraérvényesítése kulcs**lehetőséget.

   > [!NOTE]
   > Az első újraérvényesítési kísérlet sikertelen lesz, mert az új kiszolgáló egyszerű szolgáltatásának hozzáférést kell biztosítani a kulcstartóhoz. Az egyszerű szolgáltatás létrehozásához válassza az **Újraérvényesítés kulcsot**, amely hibát jelenít meg, de létrehozza az egyszerű szolgáltatást. Ezt követően olvassa el [ezeket](#set-the-right-permissions-for-key-operations) a lépéseket a cikk korábbi részén.

   ![Képernyőkép a MySQL Azure Database szolgáltatásáról, kiemelve az újraérvényesítési lépéssel](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Meg kell adnia a key vault hozzáférést az új kiszolgálóhoz.

4. A szolgáltatásnév regisztrálása után újra érvényesítse a kulcsot, és a kiszolgáló folytatja a szokásos működését.

   ![Képernyőkép a MySQL Azure Database szolgáltatásáról, amely a visszaállított funkciókat mutatja](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>További lépések

 Az adattitkosításról az [Azure Database for MySQL adattitkosítás ügyféláltal kezelt kulccsal című](concepts-data-encryption-mysql.md)témakörben olvashat bővebben.
