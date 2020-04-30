---
title: Oktatóanyag – blob Storage elérése a Key Vault használatával Azure Databricks
description: Ez az oktatóanyag leírja, hogyan érheti el az Azure Blob Storaget a Azure Databricks a Key vaultban tárolt titkok használatával.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 15399d5a00c13141877dcf44640df2c1f9b9ba5c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75889054"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Oktatóanyag: az Azure Blob Storage elérése Azure Databricks használatával Azure Key Vault

Ez az oktatóanyag leírja, hogyan érheti el az Azure Blob Storaget a Azure Databricks a Key vaultban tárolt titkok használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Storage-fiók és blob-tároló létrehozása
> * Azure Key Vault létrehozása és titkos kód hozzáadása
> * Azure Databricks munkaterület létrehozása és titkos hatókör hozzáadása
> * A blob-tároló elérése Azure Databricks

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

> [!Note]
> Ez az oktatóanyag nem hajtható végre az **Azure ingyenes próbaverziós előfizetésével**.
> Ha ingyenes fiókkal rendelkezik, lépjen a profilba, és változtassa meg az előfizetését **az utólagos**elszámolású verzióra. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. Ezután [távolítsa el a](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)költségkeretet, és [igényeljen kvóta-növekedést](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) a régiójában lévő vCPU. A Azure Databricks munkaterületének létrehozásakor kiválaszthatja a **próbaverzió (prémium-14 napos ingyenes dBu)** díjszabását, hogy a munkaterület 14 napig elérhető legyen az ingyenes prémium Azure Databricks dBu.

## <a name="create-a-storage-account-and-blob-container"></a>Storage-fiók és blob-tároló létrehozása

1. A Azure Portal válassza az **erőforrás** > létrehozása**tároló**elemet. Ezután válassza a **Storage-fiók**lehetőséget.

   ![Azure Storage-fiók erőforrásának keresése](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Válassza ki az előfizetést és az erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. Ezután adja meg a Storage-fiók nevét, és válasszon egy helyet. Válassza a **felülvizsgálat + létrehozás**lehetőséget.

   ![A Storage-fiók tulajdonságainak beállítása](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Ha az ellenőrzés sikertelen, oldja meg a problémákat, és próbálkozzon újra. Ha az ellenőrzés sikeres, válassza a **Létrehozás** lehetőséget, és várja meg a Storage-fiók létrehozását.

4. Navigáljon az újonnan létrehozott Storage-fiókhoz, **és kattintson a** **szolgáltatások** elemre az **Áttekintés** oldalon. Ezután válassza a **+ tároló** lehetőséget, és adja meg a tároló nevét. Kattintson az **OK** gombra.

   ![Új tároló létrehozása](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Keresse meg azt a fájlt, amelyet fel szeretne tölteni a blob Storage-tárolóba. Ha nem rendelkezik fájllal, egy szövegszerkesztővel hozzon létre egy új szövegfájlt, amely tartalmaz néhány információt. Ebben a példában a **hw. txt** nevű fájl a "Helló világ" szöveget tartalmazza. Mentse helyileg a szövegfájlt, és töltse fel a blob Storage-tárolóba.

   ![Fájl feltöltése a tárolóba](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Térjen vissza a Storage-fiókhoz, és válassza a **hozzáférési kulcsok** lehetőséget a **Beállítások**területen. Másolja a **Storage-fiók nevét** és az **1. kulcsot** egy szövegszerkesztőbe, hogy később használhassa az oktatóanyagot.

   ![Storage-fiók elérési kulcsainak keresése](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Azure Key Vault létrehozása és titkos kód hozzáadása

1. A Azure Portal válassza az **erőforrás létrehozása** elemet, és írja be a **Key Vault** kifejezést a keresőmezőbe.

   ![Azure-erőforrás keresési mező létrehozása](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. A Key Vault erőforrás automatikusan ki van választva. Kattintson a **Létrehozás** gombra.

   ![Key Vault erőforrás létrehozása](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. A **Key Vault létrehozása** lapon adja meg a következő információkat, és tartsa meg a fennmaradó mezők alapértelmezett értékeit:

   |Tulajdonság|Leírás|
   |--------|-----------|
   |Name (Név)|A Key Vault egyedi neve.|
   |Előfizetés|Válasszon előfizetést.|
   |Erőforráscsoport|Válasszon egy erőforráscsoportot, vagy hozzon létre egy újat.|
   |Hely|Válassza ki a helyet.|

   ![Az Azure Key Vault tulajdonságai](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. A fenti adatok megadása után válassza a **Létrehozás** elemet. 

4. Navigáljon az újonnan létrehozott kulcstartóhoz a Azure Portalban, és válassza a **titkok**lehetőséget. Ezután válassza a **+ előállítás/importálás**lehetőséget. 

   ![Új Key Vault-titok előállítása](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. A **titkos kulcs létrehozása** lapon adja meg a következő információkat, és tartsa meg a fennmaradó mezők alapértelmezett értékeit:

   |Tulajdonság|Érték|
   |--------|-----------|
   |Feltöltési beállítások|Kézi|
   |Name (Név)|A Storage-fiók kulcsa rövid neve.|
   |Érték|key1.|

   ![Új Key Vault-titok tulajdonságai](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Mentse a kulcs nevét egy szövegszerkesztőben az oktatóanyag későbbi részében való használatra, majd válassza a **Létrehozás**lehetőséget. Ezután navigáljon a **Tulajdonságok** menüre. Másolja a **DNS-nevet** és az **erőforrás-azonosítót** egy szövegszerkesztőbe, hogy később használhassa az oktatóanyagot.

   ![Azure Key Vault DNS-név és erőforrás-azonosító másolása](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Azure Databricks munkaterület létrehozása és titkos hatókör hozzáadása

1. A Azure Portal válassza az **erőforrás** > létrehozása**elemzési** > **Azure Databricks**lehetőséget.

    ![Databricks Azure Portal](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. A **Azure Databricks szolgáltatás**területen adja meg a következő értékeket egy Databricks-munkaterület létrehozásához.

   |Tulajdonság  |Leírás  |
   |---------|---------|
   |Munkaterület neve     | Adja meg a Databricks-munkaterület nevét.        |
   |Előfizetés     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
   |Erőforráscsoport     | Válassza ki ugyanazt az erőforráscsoportot, amely tartalmazza a kulcstartót. |
   |Hely     | Válassza ki ugyanazt a helyet, mint a Azure Key Vault. Az összes elérhető régióval kapcsolatban tekintse meg a [régiók által elérhető Azure-szolgáltatások](https://azure.microsoft.com/regions/services/)című témakört.        |
   |Tarifacsomag     |  Válassza a **Standard** vagy a **Prémium** előfizetést. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Databricks-munkaterület tulajdonságai](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Kattintson a **Létrehozás** gombra.

3. Navigáljon az újonnan létrehozott Azure Databricks erőforráshoz a Azure Portal, és válassza a **munkaterület elindítása**lehetőséget.

   ![Azure Databricks munkaterület elindítása](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Ha a Azure Databricks munkaterület egy külön ablakban van megnyitva, fűzze hozzá **#secrets/createscope** az URL-címhez. Az URL-címnek a következő formátumúnak kell lennie: 

   **https://< \location>. azuredatabricks.net/?o =< \orgid> # Secrets/createScope**.
   

5. Adja meg a hatókör nevét, és adja meg a korábban mentett Azure Key Vault DNS-nevet és erőforrás-azonosítót. Mentse a hatókör nevét egy szövegszerkesztőben az oktatóanyag későbbi részében való használatra. Ezután kattintson a **Létrehozás** elemre.

   ![Titkos hatókör létrehozása a Azure Databricks munkaterületen](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>A blob-tároló elérése Azure Databricks

1. A Azure Databricks munkaterület kezdőlapján válassza az **új fürt** elemet a **gyakori feladatok**területen.

   ![Új Azure Databricks jegyzetfüzet létrehozása](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Adja meg a fürt nevét, és válassza a **fürt létrehozása**lehetőséget. A fürt létrehozása néhány percet vesz igénybe.

3. A fürt létrehozása után navigáljon a Azure Databricks munkaterület kezdőlapján, és válassza az **új jegyzetfüzet** lehetőséget a **gyakori feladatok**területen.

   ![Új Azure Databricks jegyzetfüzet létrehozása](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Adja meg a jegyzetfüzet nevét, és állítsa be a nyelvet a Python értékre. Állítsa a fürtöt az előző lépésben létrehozott fürt nevére.

5. Futtassa a következő parancsot a blob Storage-tároló csatlakoztatásához. Ne felejtse el módosítani a következő tulajdonságok értékeit:

   * saját tároló neve
   * saját tároló-fiók neve
   * csatlakoztatási név
   * config-Key
   * hatókör – név
   * kulcs neve

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * a **Mount-Name** egy DBFS elérési út, amely azt jelöli, hogy a blob Storage tároló vagy a tárolóban lévő mappa (a forrásban megadott) hol lesz csatlakoztatva.
   * a **conf-Key** a következők `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` egyike lehet: vagy`fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **hatókör – a név** az előző szakaszban létrehozott titkos hatókör neve. 
   * a **kulcs neve** annak a titkos kulcsnak a neve, amelyet a Key vaultban a Storage-fiók kulcsa hozott létre.

   ![BLOB Storage-csatlakoztatás létrehozása jegyzetfüzetben](./media/store-secrets-azure-key-vault/command1.png)

6. A következő parancs futtatásával olvassa be a blob Storage-tárolóban lévő szövegfájlt egy dataframe. Módosítsa a parancs értékeit, hogy azok megfeleljenek a csatlakoztatási névnek és a fájl nevének.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Fájl olvasása a dataframe](./media/store-secrets-azure-key-vault/command2.png)

7. A fájl tartalmának megjelenítéséhez használja a következő parancsot.

   ```python
   df.show()
   ```
   ![Dataframe megjelenítése](./media/store-secrets-azure-key-vault/command3.png)

8. A blob Storage leválasztásához futtassa a következő parancsot:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Storage-fiók leválasztása](./media/store-secrets-azure-key-vault/command4.png)

9. Figyelje meg, hogy a csatlakoztatás leválasztása után már nem lehet beolvasni a blob Storage-fiókból.

   ![Storage-fiók hibájának leválasztása](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a teljes erőforráscsoportot a következő lépésekkel:

1. Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget, és navigáljon az erőforráscsoporthoz.

2. Válassza az **erőforráscsoport törlése** elemet, és írja be az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet. 

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan implementálhat egy VNet befecskendezett Databricks-környezetet egy Cosmos DB számára engedélyezett szolgáltatási végponttal.
> [!div class="nextstepaction"]
> [Oktatóanyag: Azure Databricks implementálása Cosmos DB-végponttal](service-endpoint-cosmosdb.md)
