---
title: Adattitkosítás - Azure portal - Az Azure Database for PostgreSQL - Egykiszolgálós
description: Ismerje meg, hogyan állíthatja be és kezelheti az azure-beli single server azure-adatbázis adattitkosítását az Azure Portal használatával.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 07e103c3e1f56e8a46ea24e750d83e719abab3d5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457977"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Az Azure Database for PostgreSQL Single server adattitkosítása az Azure Portal használatával

Ismerje meg, hogyan állíthatja be és kezelheti az Azure-adatbázis a PostgreSQL single kiszolgálóhoz az Azure-adatbázis adattitkosítását az Azure Portalon.

## <a name="prerequisites-for-azure-cli"></a>Az Azure CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és az adott előfizetés rendszergazdájának kell lennie.
* Az Azure Key Vaultban hozzon létre egy key vault ot és kulcsot az ügyfél által felügyelt kulcshoz.
* A kulcstartónak a következő tulajdonságokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * [Helyreállítható törlés](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Tisztítás védett](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A kulcsnak a következő attribútumokkal kell rendelkeznie az ügyfél által felügyelt kulcsként való használathoz:
  * Nincs lejárati dátum
  * Nincs letiltva
  * Képes végrehajtani bekapás, wrap kulcs, és kicsomagolni kulcs műveletek

## <a name="set-the-right-permissions-for-key-operations"></a>A kulcsműveletekhez szükséges engedélyek beállítása

1. A Key Vaultban válassza **az Access-házirendek** > **hozzáférési házirendhozzáadása lehetőséget.**

   ![Képernyőkép a Key Vaultról, kiemelve az Access-házirendek és a Hozzáférési szabályzat hozzáadása](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Válassza a **Kulcsengedélyek lehetőséget,** majd válassza a **Beszedés**, **Befolyás**, **Kicsomagolás**és a **Felhasználó**lehetőséget, amely a PostgreSQL kiszolgáló neve. Ha a kiszolgáló egyszerű nem található a meglévő egyszerű stanév listában, regisztrálnia kell azt. Amikor első alkalommal próbálja meg beállítani az adattitkosítást, a rendszer kéri a kiszolgáló egyszerű regisztrálását, és az sikertelen lesz.  

   ![Hozzáférési házirend – áttekintés](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Kattintson a **Mentés** gombra.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Adattitkosítás beállítása az Azure Database for PostgreSQL Single server számára

1. Az Azure Database for PostgreSQL-ben válassza **az Adattitkosítás** lehetőséget az ügyfél által felügyelt kulcs beállításához.

   ![Képernyőkép a PostgreSQL Azure Database for-ról, kiemelt adattitkosítással](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Választhat egy kulcstartót és kulcspárt, vagy megadhat egy kulcsazonosítót.

   ![Képernyőkép a PostgreSQL Azure Database szolgáltatásáról, kiemelt adattitkosítási beállításokkal](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Kattintson a **Mentés** gombra.

4. Annak érdekében, hogy az összes fájl (beleértve az ideiglenes fájlokat is) teljesen titkosítva legyen, indítsa újra a kiszolgálót.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Adattitkosítás használata visszaállítási vagy replikakiszolgálókhoz

Miután az Azure Database for PostgreSQL Single server titkosítva van az ügyfél által a Key Vaultban tárolt kezelt kulccsal, a kiszolgáló újonnan létrehozott példányai is titkosítva lesznek. Ezt az új másolatot helyi vagy geo-visszaállítási művelettel, vagy egy replika (helyi/régióközi) művelettel is elkészítheti. Így egy titkosított PostgreSQL szerver, akkor a következő lépéseket, hogy hozzon létre egy titkosított visszaállított szerver.

1. A kiszolgálón válassza az **Áttekintés** > **visszaállítása**lehetőséget.

   ![Képernyőkép a PostgreSQL Azure-adatbázisáról, kiemelve az Áttekintés és a Visszaállítás lehetőséggel](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Vagy replikációt támogató kiszolgáló esetén a **Beállítások** fejlécalatt válassza a **Replikáció**lehetőséget.

   ![Képernyőkép a PostgreSQL Azure Database szolgáltatásáról, kiemelve a replikáció](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. A visszaállítási művelet befejezése után a létrehozott új kiszolgáló az elsődleges kiszolgáló kulccsal lesz titkosítva. A kiszolgáló szolgáltatásai és beállításai azonban le vannak tiltva, és a kiszolgáló nem érhető el. Ez megakadályozza az adatok manipulálását, mert az új kiszolgáló identitása még nem kapott engedélyt a kulcstartó elérésére.

   ![Képernyőkép a PostgreSQL Azure Database szolgáltatásáról, kiemelve a Nem elérhető állapotgal](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. A kiszolgáló akadálymentessé tétele érdekében érvényesítse újra a kulcsot a visszaállított kiszolgálón. Válassza **az Adattitkosítás** > **újraérvényesítése kulcsot**lehetőséget.

   > [!NOTE]
   > Az első újraérvényesítési kísérlet sikertelen lesz, mert az új kiszolgáló egyszerű szolgáltatásának hozzáférést kell biztosítani a kulcstartóhoz. Az egyszerű szolgáltatás létrehozásához válassza az **Újraérvényesítés kulcsot**, amely hibát jelenít meg, de létrehozza az egyszerű szolgáltatást. Ezt követően olvassa el [ezeket](#set-the-right-permissions-for-key-operations) a lépéseket a cikk korábbi részén.

   ![Képernyőkép a PostgreSQL Azure Database szolgáltatásáról, kiemelve az újraérvényesítési lépéssel](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Meg kell adnia a key vault hozzáférést az új kiszolgálóhoz.

4. A szolgáltatásnév regisztrálása után újra érvényesítse a kulcsot, és a kiszolgáló folytatja a szokásos működését.

   ![Képernyőkép a PostgreSQL Azure Database szolgáltatásáról, amely a visszaállított funkciókat mutatja](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>További lépések

 Az adattitkosításról az [Azure Database for PostgreSQL Single server adattitkosítás ügyféláltal kezelt kulccsal című témakörben](concepts-data-encryption-postgresql.md)olvashat bővebben.
