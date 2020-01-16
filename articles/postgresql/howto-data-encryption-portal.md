---
title: Adattitkosítás Azure Database for PostgreSQL egyetlen kiszolgálón a portál használatával
description: Megtudhatja, hogyan állíthatja be és kezelheti a Azure Database for PostgreSQL önálló kiszolgáló adattitkosítását Azure Portal használatával.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 9c935ad8f77e2f8a6198a8ac095e0cc60c025a72
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028635"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Adattitkosítás Azure Database for PostgreSQL egyetlen kiszolgálón a portál használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be és kezelheti a Azure Portal a Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosításának beállításához.

## <a name="prerequisites-for-cli"></a>A CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
* Hozzon létre egy Azure Key Vault és egy kulcsot, amelyet az ügyfél által felügyelt kulcshoz szeretne használni.
* A Key vaultnak a következő tulajdonsággal kell rendelkeznie, amelyet ügyfél által felügyelt kulcsként kell használni:
  * [Helyreállítható törlés](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Védett kiürítés](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A kulcsnak a következő attribútumokat kell használnia az ügyfél által felügyelt kulcshoz.
  * Nincs lejárati dátum
  * Nincs letiltva
  * Képes a _Get_, a _wrap Key_és a _dewrap Key_ művelet végrehajtására

## <a name="setting-the-right-permissions-for-key-operations"></a>A megfelelő engedélyek beállítása a kulcsfontosságú műveletekhez

1. A Azure Key Vault válassza ki a hozzáférési házirendeket, majd **adja hozzá a hozzáférési** **szabályzatot**.

   ![Hozzáférési szabályzat – áttekintés](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. A **kulcs engedélyei**területen válassza a **beolvasás**, **becsomagolás**, **kicsomagolás** és a **rendszerbiztonsági tag**lehetőséget, amely a PostgreSQL-kiszolgáló neve. Ha a kiszolgáló rendszerbiztonsági tagja nem található a meglévő rendszerbiztonsági tag listájában, akkor regisztrálnia kell, ha első alkalommal állítja be az adattitkosítást, ami sikertelen lesz.  

   ![Hozzáférési szabályzat – áttekintés](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **Mentse** a beállításokat.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Adattitkosítás beállítása Azure Database for PostgreSQL önálló kiszolgálóhoz

1. A **Azure Database for PostgreSQL**válassza ki az **adattitkosítást** az ügyfél által felügyelt kulcs beállításának beállításához.

   ![Az adattitkosítás beállítása](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Kiválaszthat egy **Key Vault** **és egy kulcspárt, vagy** átadhat egy **kulcs-azonosítót**.

   ![Key Vault beállítása](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Mentse** a beállításokat.

4. Annak biztosítása érdekében, hogy az összes fájl (beleértve az **ideiglenes fájlokat**is) teljes mértékben titkosítva legyen, a kiszolgáló **újraindítása** **szükséges**.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Az adattitkosítást engedélyező kiszolgáló másodpéldányának visszaállítása vagy létrehozása

Ha egy Azure Database for PostgreSQL önálló kiszolgálót az ügyfél felügyelt kulcsával titkosítanak a Key Vaultban, akkor a kiszolgáló újonnan létrehozott másolata, bár helyi vagy geo-visszaállítási művelet, vagy replika (helyi/régiók) művelet. Ezért a titkosított PostgreSQL-kiszolgáló esetén az alábbi lépésekkel hozhat létre titkosított visszaállított kiszolgálót.

1. A kiszolgálón válassza az **Áttekintés**lehetőséget, majd válassza a **visszaállítás**lehetőséget.

   ![Indítás kezdeményezése – visszaállítás](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Vagy egy replikálásra alkalmas kiszolgáló esetén a **Beállítások** fejléc alatt válassza a **replikáció**lehetőséget, ahogy az itt látható:

   ![Kezdeményezés – replika](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. A visszaállítási művelet befejezése után a létrehozott új kiszolgáló az elsődleges kiszolgáló kulcsával titkosított adatforgalom. A kiszolgáló szolgáltatásai és beállításai azonban le vannak tiltva, és a kiszolgáló **elérhetetlen** állapotban van megjelölve. Ez a viselkedés úgy van kialakítva, hogy megakadályozza az adatkezelést, mivel az új kiszolgáló identitása még nem kapott engedélyt a Key Vault elérésére.

   ![A kiszolgáló nem érhető el megjelölve](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. A nem elérhető állapot javításához újra kell érvényesíteni a kulcsot a visszaállított kiszolgálón. Válassza ki az **adattitkosítás** ablaktáblát, majd a **kulcs újraérvényesítése** gombot.

   > [!NOTE]
   > Az első újraellenőrzési kísérlet sikertelen lesz, mert az új kiszolgáló egyszerű szolgáltatásának hozzáférést kell adni a kulcstartóhoz. Az egyszerű szolgáltatásnév létrehozásához válassza a **kulcs újraérvényesítése**lehetőséget, amely hibaüzenetet ad, de létrehozza az egyszerű szolgáltatásnevet. Ezt követően tekintse át a fenti [2. szakaszban található](#setting-the-right-permissions-for-key-operations) lépéseket.

   ![kiszolgáló újraérvényesítése](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Hozzáférést kell biztosítania az új kiszolgálóhoz a Key Vaulthoz.

4. Az egyszerű szolgáltatás regisztrálását követően újra kell érvényesíteni a kulcsot, és a kiszolgáló folytatja a normál működést.

   ![Normál kiszolgáló visszaállítva](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Következő lépések

 További információ az adattitkosításról: [Mi az az Azure-adattitkosítás](concepts-data-encryption-postgresql.md).
