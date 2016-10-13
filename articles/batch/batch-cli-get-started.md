<properties
   pageTitle="Az Azure Batch CLI használatának első lépései | Microsoft Azure"
   description="Gyors bevezetést olvashat Azure Batch szolgáltatás erőforrásainak kezelésére szolgáló Batch parancsokhoz az Azure CLI-ben"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>


# Az Azure Batch CLI használatának első lépései

A platformfüggetlen Azure parancssori felület (Azure CLI) segítségével kezelheti a Batch-fiókokat és -erőforrásokat, például a készleteket, a feladatokat és a tevékenységeket a Linux, Mac és Windows parancsrendszerhéjakban. Az Azure CLI-vel a Batch API-k, az Azure Portal és a Batch PowerShell-parancsmagok használatával végrehajtott műveletek közül sokat elvégezhet.

Ez a cikk az Azure CLI 0.10.5-ös verzióján alapul.

## Előfeltételek

* [Telepítse az Azure CLI-t](../xplat-cli-install.md)

* [Csatlakoztassa az Azure CLI-t az Azure-előfizetéshez](../xplat-cli-connect.md)

* Váltson **Resource Manager módra**: `azure config mode arm`

>[AZURE.TIP] Ajánlott gyakran frissíteni az Azure CLI telepítést a szolgáltatásfrissítések és -fejlesztések kihasználása érdekében.

## Segítség a parancsokhoz

Az Azure CLI-ben minden parancshoz megjeleníthet súgószöveget, ha csak a `-h` kapcsolót fűzi hozzá a parancshoz. Példa:

* Ha segítséget szeretne kérni az `azure` parancshoz, írja be a következőt: `azure -h`
* Ha le szeretné kérni az összes Batch parancs listáját a parancssori felületen, használja a következőt: `azure batch -h`
* Ha segítséget szeretne kérni Batch-fiók létrehozásához, írja be a következőt: `azure batch account create -h`

Ha bizonytalan, a `-h` parancssori kapcsoló használatával az Azure CLI bármely parancsához segítséget kérhet.

## Batch-fiók létrehozása

Használat

    azure batch account create [options] <name>

Példa:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Létrehoz egy új Batch-fiókot a megadott paraméterekkel. Meg kell adnia legalább egy helyet, egy erőforráscsoportot és egy fióknevet. Ha még nem rendelkezik erőforráscsoporttal, hozzon létre egyet az `azure group create` parancs futtatásával, és adjon meg egy Azure-régiót (például az „West US”) a `--location` kapcsolóhoz. Példa:

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] A Batch-fiók nevének egyedinek kell lennie azon Azure-régióban, ahol a fiók létrejön. Csak kisbetűs alfanumerikus karaktereket tartalmazhat, és 3-24 karakter hosszúnak kell lennie. A Batch-fiók nevekben nem használhat olyan különleges karaktereket, mint a `-` vagy a `_`.

### Társított tárfiók (autostorage)

Dönthet úgy (opcionális), hogy a Batch-fiók létrehozáskor egy **általános célú** Storage-fiókot társít hozzá. A Batch [alkalmazáscsomagok](batch-application-packages.md) funkciója társított általános célú tárfiókban található Blob-tárolót használ, ahogyan a [Batch File Conventions .NET](batch-task-output.md) könyvtár is. Ezek a választható funkciók segítik a Batch-feladatok által futtatott alkalmazások üzembe helyezését és az általuk létrehozott adatok megőrzését.

Ha az új Batch-fiók létrehozáskor egy létező Azure Storage-fiókot szeretne hozzá társítani, adja meg a `--autostorage-account-id` kapcsolót. Ehhez a kapcsolóhoz szükség van a tárfiók teljes erőforrás-azonosítójára.

Először jelenítse meg a tárfiók részleteit:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Ezután használja az **Url** értéket a `--autostorage-account-id` kapcsolóhoz. Az Url érték "/subscriptions/" előtaggal kezdődik, és tartalmazza az előfizetés-azonosítót és a Storage-fiók erőforrásának elérési útját:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## Batch-fiók törlése

Használat

    azure batch account delete [options] <name>

Példa:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Törli a megadott Batch-fiókot. A felugró kérdésre erősítse meg, hogy valóban el kívánja távolítani a fiókot (a fiókeltávolítás eltarthat egy ideig).

## A fiók hozzáférési kulcsainak kezelése

A Batch-fiókban található [erőforrások létrehozásához és módosításához](#create-and-modify-batch-resources) szüksége van egy hozzáférési kulcsra.

### Hozzáférési kulcsok listája

Használat

    azure batch account keys list [options] <name>

Példa:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Listázza a fiókkulcsokat a megadott Batch-fiókhoz.

### Új hozzáférési kulcs létrehozása

Használat

    azure batch account keys renew [options] --<primary|secondary> <name>

Példa:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Újra létrehozza a megadott fiókkulcsokat az adott Batch-fiókhoz.

## Batch-erőforrások létrehozása és módosítása

Az Azure CLI-vel olyan Batch-erőforrásokat hozhat létre, olvashat, frissíthet vagy törölhet (CRUD), mint például a készletek, a számítási csomópontok, a feladatok és a tevékenységek. Ezekhez a CRUD-műveletekhez szükség van a Batch-fiók nevére, a hozzáférési kulcsra és a végpontra. Ezeket a `-a`, a `-k` és a `-u` kapcsolókkal adhatja meg vagy állíthatja be a [környezeti változókat](#credential-environment-variables), amelyeket a CLI automatikusan használ (ha ki vannak töltve).

### A hitelesítő adatok környezeti változói

Beállíthatja az `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY` és `AZURE_BATCH_ENDPOINT` környezeti változókat is ahelyett, hogy a parancssoron minden végrehajtandó parancshoz megadná a `-a`, `-k`és `-u` kapcsolókat. A Batch CLI ezeket a változókat használja (ha be vannak állítva), ezért kihagyhatja a `-a`, `-k` és `-u` kapcsolókat. A cikk többi része ezen környezeti változók használatát feltételezi.

>[AZURE.TIP] A kulcsokat az `azure batch account keys list` paranccsal, a fiók végpontját pedig az `azure batch account show` paranccsal lehet megjeleníteni.

### JSON-fájlok

Amikor olyan Batch-erőforrásokat hoz létre, mint a készletek és a feladatok, megadhat egy JSON-fájlt, amely az új erőforrás konfigurációját tartalmazza, ahelyett, hogy parancssori kapcsolókként adná át a paramétereket. Példa:

`azure batch pool create my_batch_pool.json`

Míg számos erőforrás-létrehozási műveletet végre lehet hajtani mindössze parancssori kapcsolókkal, néhány szolgáltatáshoz JSON-formátumú fájl szükséges, amely tartalmazza az erőforrás részleteit. Például JSON-fájlt kell használnia, ha erőforrásfájlokat szeretne meghatározni egy indítási tevékenységhez.

Az erőforrás létrehozásához szükséges JSON-fájl megkereséséhez tekintse meg az MSDN webhelyén található [A Batch REST API-jainak leírása][rest_api] című dokumentációt. Minden „*Erőforrástípus* hozzáadása” témakör tartalmaz egy példa JSON-t az erőforrás létrehozásához, amelyet sablonként használhat a JSON-fájlokhoz. A készlet létrehozásához tartozó JSON például a [Készlet hozzáadása fiókhoz][rest_add_pool] című témakörben található meg.

>[AZURE.NOTE] Ha az erőforrás létrehozásakor megad egy JSON-fájlt, a rendszer figyelmen kívül hagyja a parancssoron meghatározott többi paramétert.

## Készlet létrehozása

Használat

    azure batch pool create [options] [json-file]

Példa (Virtuálisgép-konfiguráció):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Példa (Cloud Services-konfiguráció):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Létrehozza a számítási csomópontok készletét a Batch szolgáltatásban.

[A Batch funkcióinak áttekintésében](batch-api-basics.md#pool) említetteknek megfelelően két lehetősége van, amikor operációs rendszert választ a készletben található csomópontokhoz: **Virtuálisgép-konfiguráció** és **Cloud Services-konfiguráció**. Használja az `--image-*` kapcsolókat a Virtuálisgép-konfigurációs készletek létrehozásához, és az `--os-family` kapcsolót a Cloud Services-konfigurációs készletek létrehozásához. Nem adhatja meg egyszerre az `--os-family` és az `--image-*` kapcsolókat.

A készlethez megadhat [alkalmazáscsomagokat](batch-application-packages.md) és az [indítási tevékenységhez](batch-api-basics.md#start-task) tartozó parancssort. Ha erőforrásfájlokat szeretne meghatározni az indítási tevékenységhez, ehelyett egy [JSON-fájlt](#json-files) kell használnia.

Készletet a következővel törölhet:

    azure batch pool delete [pool-id]

>[AZURE.TIP] Ellenőrizze, hogy a [virtuálisgép-rendszerképek listáján](batch-linux-nodes.md#list-of-virtual-machine-images) az értékek megfelelnek-e az `--image-*` kapcsolóknak.

## Feladat létrehozása

Használat

    azure batch job create [options] [json-file]

Példa:

    azure batch job create --id "job001" --pool-id "pool001"

Hozzáad egy feladatot a Batch-fiókhoz, és megadja a készletet, amelyen a tevékenységei futnak.

Feladatot a következővel törölhet:

    azure batch job delete [job-id]

## Készletek, feladatok, tevékenységek és egyéb erőforrások listázása

Minden Batch erőforrástípus támogat egy `list` parancsot, amely lekérdezi a Batch-fiókját, és listázza az adott típusú erőforrásokat. Például listázhatja a fiókja készleteit és egy feladat tevékenységeit:

    azure batch pool list
    azure batch task list --job-id "job001"

### Az erőforrások hatékony listázása

A lekérdezés gyorsítása érdekében megadhatja a **select**, **filter** és az **expand** záradékkapcsolókat a `list` műveletekhez. Ezekkel a kapcsolókkal korlátozhatja a Batch szolgáltatás által visszaadott anyagok mennyiségét. Mivel minden szűrés a kiszolgálói oldalon történik, csak az Önt érdeklő adatok lesznek továbbítva. Ezekkel a záradékokkal a listaműveletek végrehajtásakor sávszélességet (és ezáltal időt) takaríthat meg.

Például ez csak olyan készleteket fog visszaadni, amelyeknek az azonosítói „renderTask” előtaggal kezdődnek:

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

A Batch CLI a Batch szolgáltatás által támogatott mindhárom záradékot támogatja:

* `--select-clause [select-clause]`  A tulajdonságok egy részének visszaadása minden entitásnál
* `--filter-clause [filter-clause]`  Csak olyan entitások visszaadása, amelyek megfelelnek a megadott OData-kifejezésnek
* `--expand-clause [expand-clause]`  Az entitásadatok megszerzése egy mögöttes REST-hívással. A bővítés záradék jelenleg csak a `stats` tulajdonságot támogatja.

A három záradékkal és a listázó lekérdezések végrehajtására való használatukkal kapcsolatos részletekért tekintse meg a [Query the Azure Batch service efficiently](batch-efficient-list-queries.md) (Az Azure Batch szolgáltatás hatékony lekérdezése) című cikket.

## Alkalmazáscsomagok kezelése

Az alkalmazáscsomagok egyszerű módot kínálnak az alkalmazások üzembe helyezésére a készletek számítási csomópontjain. Az Azure CLI-vel feltöltheti az alkalmazáscsomagokat, kezelheti a csomagverziókat és törölheti a csomagokat.

Új alkalmazás létrehozása és csomagverzió hozzáadása:

Alkalmazás **létrehozása**:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

Alkalmazáscsomag **hozzáadása**:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

A csomag **aktiválása**:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Állítsa be az alkalmazás **alapértelmezett verzióját** :

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### Alkalmazáscsomag üzembe helyezése

Új készlet létrehozásakor megadhat egy vagy több alkalmazáscsomagot az üzembe helyezéshez. Ha a készlet létrehozásakor megad egy csomagot, az a csomópont készlethez való csatlakoztatásakor minden csomóponton üzembe lesz helyezve. A csomagok akkor is üzembe lesznek helyezve, ha a csomópontot újraindítják vagy alaphelyzetbe állítják.

Adja meg az `--app-package-ref` kapcsolót, ha készletet hoz létre egy alkalmazáscsomag üzembe helyezéséhez a készlet csomópontjain, amikor azok csatlakoznak a készlethez. Az `--app-package-ref` kapcsoló elfogadja a számítási csomópontokon üzembe helyezni kívánt alkalmazások azonosítóinak pontosvesszővel elválasztott listáját.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Ha parancssori kapcsolók használatával hoz létre készletet, akkor nem tudja megadni, hogy *melyik* alkalmazáscsomag-verzió legyen üzembe helyezve a számítási csomópontokon, például „1.10-beta3”. Ezért a készlet létrehozása előtt meg kell adnia az alkalmazás alapértelmezett verzióját az `azure batch application set [options] --default-version <version-id>` kapcsolóval (lásd az előző szakaszt). Megadhat azonban egy csomagverziót is a készlet számára akkor, ha a készlet létrehozásakor a parancssori kapcsolók helyett egy [JSON-fájlt](#json-files) használ.

Az alkalmazáscsomagok használatával kapcsolatban további információkat az [Application deployment with Azure Batch application packages](batch-application-packages.md) (Alkalmazások üzembe helyezése az Azure Batch-alkalmazáscsomagokkal) című cikk tartalmazza.

>[AZURE.IMPORTANT] Az alkalmazáscsomagok használatához [hozzá kell kapcsolnia egy Azure Storage-fiókot](#linked-storage-account-autostorage) a Batch-fiókjához.

### Készlet alkalmazáscsomagjainak frissítése

Egy meglévő készlethez rendelt alkalmazások frissítéséhez adja ki az `azure batch pool set` parancsot az `--app-package-ref` kulccsal:

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Ha egy már meglévő készlet számítási csomópontjain szeretné üzembe helyezni az új alkalmazáscsomagot, újra kell indítania vagy alaphelyzetbe kell állítania az adott csomópontok rendszerképét:

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] Az `azure batch node list` utasítással beszerezheti a készletben lévő csomópontok és azonosítóik listáját.

Ne feledje, hogy még az üzembe helyezése előtt konfigurálnia kell az alkalmazást az alapértelmezett verziójával (`azure batch application set [options] --default-version <version-id>`).

## Hibaelhárítási tippek

Ebben a szakaszban az Azure CLI-vel kapcsolatos problémák elhárításához használható forrásokat találja. Ez az információ nem feltétlenül fogja megoldani az összes problémát, de segíthet leszűkíteni a hibák okát, és javasolhat néhány olyan forrást, amely segíti a hibák megoldását.

* A `-h` segítségével **súgószöveget** kérhet bármely CLI parancshoz

* A `-v` és a `-vv` segítségével **részletes**, a `-vv` segítségével pedig még részletesebb parancskimenetet jeleníthet meg, emellett megjeleníti a tényleges REST-kérelmeket és -válaszokat. Ezek a kapcsolók jól jönnek a teljes hibakimenet megjelenítéséhez.

* A ** kapcsolóval megtekintheti a **parancskimenetet JSON-fájlként`--json`. Például az `azure batch pool show "pool001" --json` JSON-formátumban jeleníti meg a pool001 tulajdonságait. Ezt követően másolhatja és módosíthatja ezt a kimenetet, hogy felhasználhassa egy `--json-file` kapcsolóval (lásd a[JSON-fájlok](#json-files) szakaszt a jelen cikk korábbi részében).

* A [Batch fórum az MSDN-en][batch_forum] egy nagyszerű forrás, amelyet a Batch csapatának tagjai folyamatosan figyelnek. Mindenképp ott tegye fel a kérdéseit, ha problémákba ütközne, vagy segítségre lenne szüksége egy adott művelethez.

* Az Azure CLI jelenleg nem támogatja az összes Batch-erőforrásműveletet. Például jelenleg nem határozhatja meg egy készletnél az alkalmazáscsomag *verzióját*, csak a csomagazonosítót. Ilyen esetekben előfordulhat, hogy a parancssori kapcsolók használata helyett meg kell adnia egy `--json-file` kapcsolót a parancshoz. Ügyeljen rá, hogy a CLI legújabb verzióját használja a jövőbeli fejlesztések használatba vétele érdekében.

## Következő lépések

*  A szolgáltatás Batch számítási csomópontokon futtatott alkalmazások kezelésére és üzembe helyezésére való használatával kapcsolatban lásd: [Application deployment with Azure Batch application packages](batch-application-packages.md) (Alkalmazástelepítés Azure Batch-alkalmazáscsomagokkal).

* További információ az elemek számának csökkentéséről és a Batch lekérdezésiről visszaadott információk típusáról: [Query the Batch service efficiently](batch-efficient-list-queries.md) (A Batch szolgáltatás hatékony lekérdezése).

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx


<!--HONumber=Oct16_HO1-->


