---
title: Azure Database for MySQL adattitkosítása a portál használatával
description: Ismerje meg, hogyan állíthatja be és kezelheti a Azure Database for MySQL adattitkosítását Azure Portal használatával.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 6e07b82104e5c7ff2d817a90e2be4aa336cf47ce
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942022"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Azure Database for MySQL-kiszolgáló adattitkosítása Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be és kezelheti a Azure Database for MySQL adattitkosításának beállítását a Azure Portal használatával.

## <a name="prerequisites-for-cli"></a>A CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
* Hozzon létre egy Azure Key Vault és egy kulcsot, amelyet az ügyfél által felügyelt kulcshoz szeretne használni.
* A Key Vault a következő tulajdonságot kell használni ügyfél által felügyelt kulcsként:
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
  * Képes a _Get_, a _wrap Key_, a _dewrap Key_ Operations művelet végrehajtására

## <a name="setting-the-right-permissions-for-key-operations"></a>A megfelelő engedélyek beállítása a kulcsfontosságú műveletekhez

1. A Azure Key Vault válassza ki a hozzáférési házirendeket, majd **adja hozzá a hozzáférési** **szabályzatot**:

   ![Hozzáférési szabályzat – áttekintés](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Válassza ki a **kulcs engedélyeit**, majd válassza a **beolvasás**, **becsomagolás**, **kicsomagolás** és a **rendszerbiztonsági tag**lehetőséget, amely a MySQL-kiszolgáló neve. Ha a kiszolgáló rendszerbiztonsági tagja nem található a meglévő rendszerbiztonsági tag listájában, akkor regisztrálnia kell, ha első alkalommal állítja be az adattitkosítást, ami sikertelen lesz.

   ![Hozzáférési szabályzat – áttekintés](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **Mentse** a beállításokat.

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Adattitkosítás beállítása Azure Database for MySQLhoz

1. A **Azure Database for MySQL**válassza ki az **adattitkosítást** az ügyfél által felügyelt kulcs beállításának beállításához.

   ![Az adattitkosítás beállítása](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Kiválaszthat egy **Key Vault** **és egy kulcspárt, vagy** átadhat egy **kulcs-azonosítót**.

   ![Key Vault beállítása](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Mentse** a beállításokat.

4. Annak biztosítása érdekében, hogy az összes fájl (beleértve az **ideiglenes fájlokat**is) teljes mértékben titkosítva legyen, a kiszolgáló **újraindítása** **szükséges**.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Az adattitkosítást engedélyező kiszolgáló másodpéldányának visszaállítása vagy létrehozása

Ha egy Azure Database for MySQL az ügyfél felügyelt kulcsával van titkosítva a Key Vaultban, akkor a kiszolgáló újonnan létrehozott másolata, bár helyi vagy geo-visszaállítási művelet vagy replika (helyi/régiók) művelet. A titkosított MySQL-kiszolgálók esetében az alábbi lépéseket követve hozzon létre egy titkosított visszaállított kiszolgálót.

1. A kiszolgálón válassza az **Áttekintés**lehetőséget, majd válassza a **visszaállítás**lehetőséget.

   ![Indítás kezdeményezése – visszaállítás](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Vagy egy replikálásra alkalmas kiszolgáló esetén a **Beállítások** fejléc alatt válassza a **replikáció**lehetőséget, ahogy az itt látható:

   ![Kezdeményezés – replika](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. A visszaállítási művelet befejezése után az új kiszolgáló létrehozása az elsődleges kiszolgáló titkosításához használt kulccsal van titkosítva. A kiszolgáló szolgáltatásai és beállításai azonban le vannak tiltva, és a kiszolgáló **elérhetetlen** állapotban van megjelölve. Ez a viselkedés úgy van kialakítva, hogy megakadályozza az adatkezelést, mivel az új kiszolgáló identitása még nem kapott engedélyt a Key Vault elérésére.

   ![A kiszolgáló nem érhető el megjelölve](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. A nem elérhető állapot javításához újra kell érvényesíteni a kulcsot a visszaállított kiszolgálón. Válassza ki az **adattitkosítás** ablaktáblát, majd a **kulcs újraérvényesítése** gombot.

   > [!NOTE]
   > Az első újraellenőrzési kísérlet sikertelen lesz, mert az új kiszolgáló egyszerű szolgáltatásának hozzáférést kell adni a kulcstartóhoz. Az egyszerű szolgáltatásnév létrehozásához válassza a **kulcs újraérvényesítése**lehetőséget, amely hibaüzenetet ad, de létrehozza az egyszerű szolgáltatásnevet. Ezt követően tekintse át a fenti [2. szakaszban található](#setting-the-right-permissions-for-key-operations) lépéseket.

   ![kiszolgáló újraérvényesítése](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Hozzáférést kell biztosítania az új kiszolgálóhoz a Key Vaulthoz.

4. Az egyszerű szolgáltatás regisztrálását követően újra kell érvényesíteni a kulcsot, és a kiszolgáló folytatja a normál működést.

   ![Normál kiszolgáló visszaállítva](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Következő lépések

 További információ az adattitkosításról: [Mi az az Azure-adattitkosítás](concepts-data-encryption-mysql.md).
