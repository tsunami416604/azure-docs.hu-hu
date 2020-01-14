---
title: Adattitkosítás Azure Database for PostgreSQL egyetlen kiszolgálón a portál használatával
description: Megtudhatja, hogyan állíthatja be és kezelheti az Azure Database for PostgreSQL egy kiszolgáló adattitkosítását Azure Portal használatával
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: e579866b1df6d32dc2b90a19ac001c381da625a6
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922803"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Adattitkosítás Azure Database for PostgreSQL egyetlen kiszolgálón a portál használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be és kezelheti a Azure Portal a Azure Database for PostgreSQL egyetlen kiszolgáló adattitkosításának beállításához.

## <a name="prerequisites-for-cli"></a>A CLI előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
* Hozzon létre egy Azure Key Vault és egy kulcsot, amelyet az ügyfél által felügyelt kulcshoz szeretne használni.
* A Key vaultnak a következő tulajdonsággal kell rendelkeznie, amelyet ügyfél által felügyelt kulcsként kell használni
    * [Helyreállítható törlés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [Védett kiürítés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```

* A kulcsnak a következő attribútumokat kell használnia az ügyfél által felügyelt kulcshoz.
    * Nincs lejárati dátum
    * Nincs letiltva
    * Képes a Get, a wrap Key, a dewrap Key Operations művelet végrehajtására

## <a name="setting-the-right-permissions-for-key-operations"></a>A megfelelő engedélyek beállítása a kulcsfontosságú műveletekhez

1. A Azure Key Vault válassza ki a hozzáférési házirendeket, és **adja hozzá a hozzáférési** **szabályzatot** . 

   ![Hozzáférési szabályzat – áttekintés](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. A **kulcs engedélyei** területen válassza **a beolvasás**, **becsomagolás**, **kicsomagolás** és a **résztvevő**, a PostgreSQL-kiszolgáló neve lehetőséget. Ha a kiszolgáló rendszerbiztonsági tagja nem található a meglévő rendszerbiztonsági tag listájában, akkor regisztrálnia kell, ha első alkalommal állítja be az adattitkosítást, ami sikertelen lesz.  

   ![Hozzáférési szabályzat – áttekintés](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Mentse** a beállításokat.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Adattitkosítás beállítása Azure Database for PostgreSQL önálló kiszolgálóhoz

1. A **Azure Database for PostgreSQL**válassza ki az **adattitkosítást** a beállított ügyfél által felügyelt kulcs beállításához.

   ![Az adattitkosítás beállítása](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Kiválaszthat egy **Key Vault** **és egy kulcspárt, vagy** átadhat egy **kulcs-azonosítót**.

   ![Key Vault beállítása](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Mentse** a beállításokat.

4. Annak biztosítása érdekében, hogy az összes fájl (beleértve az **ideiglenes fájlokat**is) teljes mértékben titkosítva legyen, a kiszolgáló **újraindítása** **szükséges**.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Az adattitkosítást engedélyező kiszolgáló másodpéldányának visszaállítása vagy létrehozása

Ha egy Azure Database for PostgreSQL önálló kiszolgálót az ügyfél felügyelt kulcsával titkosítanak a Key Vaultban, akkor a kiszolgáló újonnan létrehozott másolata, bár helyi vagy geo-visszaállítási művelet, vagy replika (helyi/régiók) művelet. Ezért a titkosított PostgreSQL-kiszolgáló esetén az alábbi lépésekkel hozhat létre titkosított visszaállított kiszolgálót.

1. A kiszolgálón válassza az **Áttekintés**lehetőséget, majd válassza a **visszaállítás**lehetőséget.

   ![Indítás kezdeményezése – visszaállítás](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Vagy egy replikálásra alkalmas kiszolgáló esetén a **Beállítások** fejléc alatt válassza a **replikálás** elemet.

   ![Kezdeményezés – replika](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. A visszaállítási művelet befejezése után a létrehozott új kiszolgáló az elsődleges kiszolgáló kulcsával titkosított adatforgalom. A kiszolgáló szolgáltatásai és beállításai azonban le vannak tiltva, és a kiszolgáló **elérhetetlen** állapotban van megjelölve. Ez megakadályozza az adatkezelést, mivel az új kiszolgáló identitása még nem kapott engedélyt a Key Vault elérésére.

   ![A kiszolgáló nem érhető el megjelölve](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)


3. A nem elérhető állapot javításához újra kell érvényesíteni a kulcsot a visszaállított kiszolgálón. Kattintson az **adattitkosítás** panelre, majd a **kulcs újraérvényesítése** gombra.

   > [!NOTE]
   > Az első újraellenőrzési kísérlet sikertelen lesz, mert az új kiszolgáló egyszerű szolgáltatásának hozzáférést kell adni a kulcstartóhoz. Az egyszerű szolgáltatásnév létrehozásához kattintson a **kulcs újraellenőrzése**gombra, amely hibát jelez, de létrehozza az egyszerű szolgáltatásnevet. Ezt követően tekintse át a fenti [2. szakaszban található](https://docs.microsoft.com/azure/postgresql/howto-data-encryption-portal#setting-the-right-permissions-for-key-operations) lépéseket.

   ![kiszolgáló újraérvényesítése](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Hozzáférést kell biztosítania az új kiszolgálóhoz a Key Vaulthoz. 

4. Az egyszerű szolgáltatás regisztrálását követően újra kell érvényesíteni a kulcsot, és a kiszolgáló folytatja a normál működést.

   ![Normál kiszolgáló visszaállítva](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Következő lépések

 További információ az adattitkosításról: [Mi az az Azure-adattitkosítás](concepts-data-encryption-postgresql.md).