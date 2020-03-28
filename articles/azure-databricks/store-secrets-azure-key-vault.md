---
title: Oktatóanyag – Access blobstorage az Azure Databricks használatával a key vault használatával
description: Ez az oktatóanyag ismerteti, hogyan érheti el az Azure Blob Storage-ot az Azure Databricks egy key vaultban tárolt titkos kulcsok használatával.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 15399d5a00c13141877dcf44640df2c1f9b9ba5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889054"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Oktatóanyag: Access Azure Blob Storage az Azure Databricks az Azure Key Vault használatával

Ez az oktatóanyag ismerteti, hogyan érheti el az Azure Blob Storage-ot az Azure Databricks egy key vaultban tárolt titkos kulcsok használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tárfiók és blobtároló létrehozása
> * Hozzon létre egy Azure Key Vault ot, és adjon hozzá egy titkos
> * Hozzon létre egy Azure Databricks-munkaterületet, és adjon hozzá egy titkos hatókört
> * A blobtároló elérése az Azure Databricks-ből

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

> [!Note]
> Ez az oktatóanyag nem hajtható végre **az Azure ingyenes próba-előfizetésével.**
> Ha van ingyenes fiókja, nyissa meg a profilját, és módosítsa az előfizetését **a felosztó-kirovó szolgáltatásra.** További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. Ezután [távolítsa el a költségkeretet,](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)és kérjen [kvótanövelést](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) a régióban a vCPU-khoz. Az Azure Databricks-munkaterület létrehozásakor kiválaszthatja a **próbaverziós (prémium – 14 napos ingyenes dbári)** díjszabási szintet, hogy a munkaterület 14 napig hozzáférést biztosítson az ingyenes prémium szintű Azure Databricks KBB-khoz.

## <a name="create-a-storage-account-and-blob-container"></a>Tárfiók és blobtároló létrehozása

1. Az Azure Portalon válassza az > **Erőforrás-tárolás** **létrehozása**lehetőséget. Ezután válassza **a Tárfiók lehetőséget.**

   ![Az Azure storage-fiók erőforrásának megkeresése](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Válassza ki az előfizetést és az erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. Ezután adja meg a tárfiók nevét, és válasszon egy helyet. Válassza **a Véleményezés + Létrehozás lehetőséget.**

   ![Tárfiók tulajdonságainak beállítása](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Ha az ellenőrzés sikertelen, oldja meg a problémákat, és próbálkozzon újra. Ha az ellenőrzés sikeres, válassza **a Create and** wait for the storage account to be created lehetőséget.

4. Keresse meg az újonnan létrehozott tárfiókot, és válassza **a Blobok** a **Szolgáltatások** az **Áttekintés** lapon. Ezután válassza a **+ Tároló lehetőséget,** és adjon meg egy tárolónevet. Válassza **az OK gombot.**

   ![Új tároló létrehozása](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Keresse meg a blob storage-tárolóba feltölteni kívánt fájlt. Ha nem rendelkezik fájllal, szövegszerkesztővel hozzon létre egy új szövegfájlt bizonyos információkkal. Ebben a példában egy **hw.txt** nevű fájl tartalmazza a "hello world" szöveget. Mentse a szövegfájlt helyileg, és töltse fel a blob storage tárolóba.

   ![Fájl feltöltése a tárolóba](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Térjen vissza a tárfiókba, és válassza **az Access-kulcsok lehetőséget** a **Beállítások csoportban.** Másolja **a tárfiók nevét** és az **1-es kulcsot** egy szövegszerkesztőbe későbbi használatra ebben az oktatóanyagban.

   ![Tárfiók hozzáférési kulcsainak keresése](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Hozzon létre egy Azure Key Vault ot, és adjon hozzá egy titkos

1. Az Azure Portalon válassza **az Erőforrás létrehozása lehetőséget,** és írja be a **Key Vault** kifejezést a keresőmezőbe.

   ![Azure-erőforrás-keresőmező létrehozása](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. A Key Vault erőforrás automatikusan ki van jelölve. Kattintson a **Létrehozás** gombra.

   ![Key Vault-erőforrás létrehozása](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. A **Kulcstartó létrehozása** lapon adja meg a következő adatokat, és tartsa meg a többi mező alapértelmezett értékeit:

   |Tulajdonság|Leírás|
   |--------|-----------|
   |Név|A kulcstartó egyedi neve.|
   |Előfizetés|Válasszon előfizetést.|
   |Erőforráscsoport|Válasszon egy erőforráscsoportot, vagy hozzon létre egy újat.|
   |Hely|Válassza ki a helyet.|

   ![Az Azure key vault tulajdonságai](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. A fenti adatok megadása után válassza a **Létrehozás** elemet. 

4. Nyissa meg az újonnan létrehozott kulcstartót az Azure Portalon, és válassza a **Titkok**lehetőséget. Ezután válassza a **+ Generate/Import**lehetőséget. 

   ![Új kulcstartó titkos kulcsának létrehozása](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. A **Titkos kapcsolat létrehozása** lapon adja meg a következő információkat, és tartsa meg a többi mező alapértelmezett értékeit:

   |Tulajdonság|Érték|
   |--------|-----------|
   |Feltöltési beállítások|Kézi|
   |Név|A tárfiók kulcsának rövid neve.|
   |Érték|key1-et a tárfiókból.|

   ![Az új kulcstartó titkos kulcsának tulajdonságai](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Mentse a kulcsnevet egy szövegszerkesztőbe, hogy később használhassa az oktatóanyagban, és válassza a **Létrehozás gombot.** Ezután keresse meg a **Tulajdonságok menüt.** Másolja a **DNS-nevet** és **az erőforrás-azonosítót** egy szövegszerkesztőbe, és az oktatóanyag későbbi részében használható.

   ![Az Azure Key Vault DNS-nevének és erőforrás-azonosítójának másolása](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Hozzon létre egy Azure Databricks-munkaterületet, és adjon hozzá egy titkos hatókört

1. Az Azure Portalon válassza az**Analytics** > Erőforrás > létrehozása**Azure Databricks** **lehetőséget.**

    ![Databricks az Azure Portalon](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. Az **Azure Databricks Service alatt**adja meg a következő értékeket a Databricks-munkaterület létrehozásához.

   |Tulajdonság  |Leírás  |
   |---------|---------|
   |Munkaterület neve     | Adja meg a Databricks-munkaterület nevét.        |
   |Előfizetés     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
   |Erőforráscsoport     | Válassza ki ugyanazt az erőforráscsoportot, amely a key vaultot tartalmazza. |
   |Hely     | Válassza ki ugyanazt a helyet, mint az Azure Key Vault. Az összes elérhető régióról az [Azure-szolgáltatások régiónként érhetők el.](https://azure.microsoft.com/regions/services/)        |
   |Tarifacsomag     |  Válassza a **Standard** vagy a **Prémium** előfizetést. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Databricks munkaterület tulajdonságai](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Kattintson a **Létrehozás** gombra.

3. Nyissa meg az újonnan létrehozott Azure Databricks-erőforrást az Azure Portalon, és válassza **a Munkaterület indítása**lehetőséget.

   ![Az Azure Databricks munkaterületének elindítása](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Miután az Azure Databricks-munkaterület meg van nyitva egy külön ablakban, hozzáfűzheti **#secrets/createScope** az URL-címet. Az URL-cím formátumának a következőformátummal kell rendelkeznie: 

   **https://<\location>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**.
   

5. Adja meg a hatókör nevét, és adja meg az Azure Key Vault DNS-nevét és a korábban mentett erőforrás-azonosítót. Mentse a hatókör nevét egy szövegszerkesztőbe, hogy később az oktatóanyagban használhassa. Ezután kattintson a **Létrehozás** elemre.

   ![Titkos hatókör létrehozása az Azure Databricks-munkaterületen](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>A blobtároló elérése az Azure Databricks-ből

1. Az Azure Databricks-munkaterület kezdőlapján válassza az **Új fürt** lehetőséget a Gyakori **feladatok csoportban.**

   ![Új Azure Databricks-jegyzetfüzet létrehozása](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Adja meg a fürt nevét, és válassza **a Fürt létrehozása**lehetőséget. A fürt létrehozása néhány percet vesz igénybe.

3. A fürt létrehozása után keresse meg az Azure Databricks-munkaterület kezdőlapját, és válassza az **Új jegyzetfüzet** lehetőséget a Gyakori **feladatok csoportban.**

   ![Új Azure Databricks-jegyzetfüzet létrehozása](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Adjon meg egy jegyzetfüzet nevet, és állítsa a nyelvet Pythonra. Állítsa be a fürtöt az előző lépésben létrehozott fürt nevére.

5. Futtassa a következő parancsot a blob storage-tároló csatlakoztatásához. Ne felejtse el módosítani a következő tulajdonságok értékeit:

   * ön tárolóneve
   * a tárfiók neve
   * csatlakoztatási név
   * config-kulcs
   * hatókör neve
   * kulcsnév

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **a csatlakoztatási név** egy DBFS-elérési út, amely azt jelöli, hogy a Blob Storage-tároló vagy a tárolón belüli mappa (forrásban megadott) csatlakoztatva lesz.mount-name is a DBFS path representing where the Blob Storage container or a folder inside the container (specified in source) will be csatlakoztatva.
   * **conf-key** lehet `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` vagy vagy`fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **a hatókörnév** az előző szakaszban létrehozott titkos hatókör neve. 
   * **kulcsnév** a kulcstartóban a tárfiók kulcsához létrehozott titkos kulcsuk neve.

   ![Blob storage-csatlakoztatás létrehozása a jegyzetfüzetben](./media/store-secrets-azure-key-vault/command1.png)

6. Futtassa a következő parancsot a blob storage tárolójában lévő szövegfájl adatkeretbe olvasásához. Módosítsa a parancs ban lévő értékeket úgy, hogy azok megegyeznek a csatlakoztatás nevével és a fájlnevével.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Fájl olvasása adatkeretbe](./media/store-secrets-azure-key-vault/command2.png)

7. A következő paranccsal jelenítheti meg a fájl tartalmát.

   ```python
   df.show()
   ```
   ![Adatkeret megjelenítése](./media/store-secrets-azure-key-vault/command3.png)

8. A blobstorage leválasztásához futtassa a következő parancsot:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Tárfiók leválasztása](./media/store-secrets-azure-key-vault/command4.png)

9. Figyelje meg, hogy miután a csatlakoztatás le van szerelve, már nem tud olvasni a blob storage-fiókból.

   ![A tárfiók leválasztása hiba](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje a teljes erőforráscsoportot a következő lépésekkel:

1. Az Azure Portal bal oldali menüjében válassza **az Erőforráscsoportok** lehetőséget, és keresse meg az erőforráscsoportot.

2. Válassza **az Erőforráscsoport törlése lehetőséget,** és írja be az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet. 

## <a name="next-steps"></a>További lépések

A következő cikkhez, hogy megtudja, hogyan valósíthatja meg a virtuális hálózat által injektált Databricks-környezetben egy Service Endpoint engedélyezve van a Cosmos DB.
> [!div class="nextstepaction"]
> [Oktatóanyag: Valósítsa meg az Azure Databricks egy Cosmos DB-végpont](service-endpoint-cosmosdb.md)
