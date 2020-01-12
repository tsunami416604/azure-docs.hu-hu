---
title: Azure Database for MySQL adattitkosítása a portál használatával
description: Ismerje meg, hogyan állíthatja be és kezelheti a Azure Database for MySQL adattitkosítását a Azure Portal használatával
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 10af869a631b620c2c75aa69722dc03df15f8539
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903846"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Azure Database for MySQL-kiszolgáló adattitkosítása Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan állíthatja be és kezelheti a Azure Portal a Azure Database for MySQL adattitkosításának beállításához.

## <a name="prerequisites-for-powershell"></a>A PowerShell előfeltételei

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
* A Azure PowerShell telepítése és futtatása szükséges.
* Hozzon létre egy Azure Key Vault és egy kulcsot, amelyet az ügyfél által felügyelt kulcshoz szeretne használni.
* A Key Vault a következő tulajdonságot kell használnia ügyfél által felügyelt kulcsként való használatra
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

2. Válassza ki a **kulcs engedélyeit** a **Get**, a **wrap**, a **dewrap** és a **Principal** , amely a MySQL-kiszolgáló neve.

   ![Hozzáférési szabályzat – áttekintés](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Mentse** a beállításokat.

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Adattitkosítás beállítása Azure Database for MySQLhoz

1. A **Azure Database for MySQL**válassza ki az **adattitkosítást** az ügyfél által felügyelt kulcs beállításának beállításához.

   ![Az adattitkosítás beállítása](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Kiválaszthat egy **Key Vault** **és egy kulcspárt, vagy** átadhat egy **kulcs-azonosítót**.

   ![Key Vault beállítása](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Mentse** a beállításokat.

4. Annak biztosítása érdekében, hogy az összes fájl (beleértve az ideiglenes fájlokat is) teljes mértékben titkosítva legyen, a kiszolgáló újraindítása szükséges.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Az adattitkosítást engedélyező kiszolgáló replikájának visszaállítása vagy létrehozása

Ha egy Azure Database for MySQL az ügyfél felügyelt kulcsával van titkosítva a Key Vaultban, akkor a kiszolgáló újonnan létrehozott másolata, bár helyi vagy geo-visszaállítási művelet vagy replika (helyi/régiók) művelet. A titkosított MySQL-kiszolgálók esetében az alábbi lépéseket követve hozzon létre egy titkosított visszaállított kiszolgálót.

1. A kiszolgálón válassza az **Áttekintés**lehetőséget, majd válassza a **visszaállítás**lehetőséget.

   ![Indítás kezdeményezése – visszaállítás](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Vagy egy replikálásra alkalmas kiszolgáló esetén a **Beállítások** fejléc alatt válassza a **replikálás** elemet.

   ![Kezdeményezés – replika](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. A visszaállítási művelet befejezése után az új kiszolgáló létrehozása az elsődleges kiszolgáló titkosításához használt kulccsal van titkosítva. A kiszolgáló szolgáltatásai és beállításai azonban le vannak tiltva, és a kiszolgáló **elérhetetlen** állapotban van megjelölve. Ez megakadályozza az adatkezelést, mivel az új kiszolgáló identitása még nem kapott engedélyt a Key Vault elérésére.

   ![A kiszolgáló nem érhető el megjelölve](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. A nem elérhető állapot javításához újra kell érvényesíteni a kulcsot a visszaállított kiszolgálón.

   ![kiszolgáló újraérvényesítése](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Hozzáférést kell biztosítania az új kiszolgálóhoz a Key Vaulthoz. 

4. A kulcs újbóli ellenőrzése után a kiszolgáló folytatja a normál működést.

   ![Normál kiszolgáló visszaállítva](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="next-steps"></a>Következő lépések

 További információ az adattitkosításról: [Mi az az Azure-adattitkosítás](concepts-data-encryption-mysql.md).

