---
title: Oktatóanyag a Azure Data Box rendeléséhez | Microsoft Docs
description: Megismerheti az Azure Data Box üzembe helyezési követelményeit és a megrendelésének folyamatát
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392479"
---
# <a name="tutorial-order-azure-data-box"></a>Oktatóanyag: Az Azure Data Box megrendelése

Az Azure Data Box egy hibrid megoldás, amellyel gyorsan, könnyen és megbízhatóan importálhat helyszíni adatokat az Azure-ba. Az adatokat egy, a Microsoft által biztosított 80 TB (hasznos) kapacitású tárolóeszközre helyezheti át, amelyet aztán visszapostáz a Microsoftnak. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag leírja, hogyan rendelheti meg az Azure Data Box szolgáltatást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Data Box üzembe helyezésének előfeltételei
> * A Data Box megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/portal)

Az eszköz telepítése előtt végezze el a következő konfigurálási előfeltételeket az Data Box szolgáltatáshoz és eszközhöz:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

Jelentkezzen be az Azure-ba, és futtassa az Azure CLI-parancsokat kétféleképpen:

* Telepítheti a CLI-t, és helyileg is futtathatja a CLI-parancsokat.
* A CLI-parancsokat a Azure Cloud Shell Azure Portal belül is futtathatja.

Az oktatóanyaghoz az Azure CLI-t használjuk a Windows PowerShellen keresztül, de bármelyik lehetőséget szabadon választhat.

### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

* Telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -verziót 2.0.67 vagy újabb verzióra. Másik megoldásként az MSI-t is [telepítheti](https://aka.ms/installazurecliwindows).

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyisson meg egy Windows PowerShell-parancssorablakot, és jelentkezzen be az Azure-ba az az [login](/cli/azure/reference-index#az-login) paranccsal:

```azurecli
PS C:\Windows> az login
```

A sikeres bejelentkezés kimenete:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>A Azure Data Box CLI bővítmény telepítése

A Azure Data Box CLI-parancsok használata előtt telepítenie kell a bővítményt. Az Azure CLI-bővítmények hozzáférést biztosítanak olyan kísérleti és előzetes kiadású parancsokhoz, amelyeket még nem szállítottak el az alapszintű CLI részeként. További információ a bővítményekről: [bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).

A Azure Data Box-bővítmény telepítéséhez futtassa a következő `az extension add --name databox` parancsot:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Ha a bővítmény telepítése sikeresen megtörtént, a következő kimenet jelenik meg:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure által üzemeltetett interaktív rendszerhéj-környezetet [Azure Cloud Shell](https://shell.azure.com/)használhatja a BÖNGÉSZŐBEN a CLI-parancsok futtatásához. Azure Cloud Shell támogatja a bash vagy a Windows PowerShellt az Azure-szolgáltatásokkal. Az Azure CLI előre telepítve és konfigurálva van a fiókjával való használatra. Kattintson a Cloud Shell gombra a Azure Portal jobb felső részén található menüben:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

A gomb egy interaktív felületet indít el, amelyet a jelen útmutatóban ismertetett lépések futtatására használhat.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>A Data Box megrendelése

# <a name="portal"></a>[Portál](#tab/portal)

Az eszköz megrendeléséhez hajtsa végre a következő lépéseket a Azure Portal.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).
2. Kattintson a **+ Erőforrás létrehozása** gombra, és keressen rá az *Azure Data Box* kifejezésre. Kattintson az **Azure Data Box** lehetőségre.

   [![Az Azure Data Box 1 keresése](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Kattintson a **Létrehozás** lehetőségre.

4. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg vagy válassza ki a következő információkat, majd válassza az **Alkalmaz** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés     | Válasszon egy EA-, CSP- vagy Azure Sponsorship-előfizetést a Data Box szolgáltatáshoz. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Átvitel típusa     | Válassza az **Importálás az Azure-ba** lehetőséget.        |
    |Forrásország/-régió    |    Válassza ki azt az országot vagy régiót, ahol az adatok jelenleg találhatók.         |
    |Azure-beli célrégió     |     Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat.        |

5. Válassza a **Data Box**lehetőséget. Egy megrendelés maximális felhasználható kapacitása 80 TB. Nagyobb mennyiségű adat esetén több rendelést is létrehozhat.

      [![Válasszon Data Box 1. lehetőséget](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. A **Megrendelés** területen adja meg a **Megrendelés részleteit**. Adja meg vagy válassza ki a következő információkat, majd válassza a **Tovább** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Name (Név)     |  Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.      |
    |Erőforráscsoport     |    Használjon egy már létezőt, vagy hozzon létre újat. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója.         |
    |Azure-beli célrégió     | Válasszon ki egy régiót a tárfiókhoz. <br> További információt a [regionális elérhetőséget](data-box-overview.md#region-availability) tárgyaló témakörben talál.        |
    |Tárolási célhely     | Válassz a Tárfiók vagy a Felügyelt lemezek lehetőséget, vagy mindkettőt. <br> A megadott Azure-régió alapján válasszon ki egy vagy több tárfiókot a meglévő tárfiókok szűrt listájából. A Data Box legfeljebb 10 tárfiókkal köthető össze. <br> Létrehozhat egy új **Általános célú v1**, **Általános célú v2** fiókot vagy egy **Blob Storage-fiókot** is. <br>A virtuális hálózattal rendelkező tárfiókok támogatottak. Ahhoz, hogy a Data Box szolgáltatás működjön a biztonságos tárfiókok esetében, engedélyezze a megbízható szolgáltatásokat a tárfiók hálózati tűzfalának beállításai között. További információ: [Azure Data Box hozzáadása megbízható szolgáltatásként](../storage/common/storage-network-security.md#exceptions).|

    Ha tárfiókot használ tárolási célhelyként, a következő képernyőképet fogja látni:

    ![A Storage-fiók Data Box sorrendje](media/data-box-deploy-ordered/order-storage-account.png)

    Ha Data Box használatával felügyelt lemezeket hoz létre a helyszíni virtuális merevlemezekről (VHD-k), akkor a következő információkat is meg kell adnia:

    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforráscsoportok     | Hozzon létre új erőforráscsoportokat, ha felügyelt lemezeket szeretne létrehozni helyszíni VHD-kből. Meglévő erőforráscsoportot csak akkor használhat, ha az erőforráscsoportot korábban hozták létre a felügyelt lemez Data Box szolgáltatás általi Data Box rendelésének létrehozásakor. <br> Adjon meg több erőforráscsoportot, pontosvesszővel elválasztva. Legfeljebb 10 erőforráscsoport használata támogatott.|

    ![Felügyelt lemez Data Box sorrendje](media/data-box-deploy-ordered/order-managed-disks.png)

    A felügyelt lemezekhez megadott tárfiókot előkészítési tárfiókként használja a rendszer. A Data Box szolgáltatás lapblobként tölti fel a VHD-ket az előkészítési tárfiókba, majd felügyelt lemezekké konvertálja, és áthelyezi az erőforráscsoportba. További információ: [Az Azure-ba történő adatfeltöltés ellenőrzése](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. A **Szállítási cím** területen adja meg a vezeték- és utónevét, a vállalata nevét és postai címét, valamint egy érvényes telefonszámot. Kattintson a **Cím ellenőrzése** elemre. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről.

   Ha az önfelügyelt szállítást választotta, a megrendelés sikeres elhelyezése után e-mailben értesítést fog kapni. Az önfelügyelt szállítással kapcsolatos további információkért lásd: [saját üzemeltetésű szállítás használata](data-box-portal-customer-managed-shipping.md).

8. Kattintson a **tovább** gombra, ha a szállítási adatok érvényesítése sikeres volt.

9. Az **Értesítési adatok** területen adja meg az e-mail-címeket. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

10. Tekintse át a megrendeléshez kapcsolódó **Összefoglalót** a kapcsolattartási adatokról, az értesítésekről és az adatvédelmi feltételekről. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

11. Kattintson a **Megrendelés** elemre. A megrendelés létrehozása néhány percet vesz igénybe.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi lépéseket követve rendeljen egy eszközt az Azure CLI használatával:

1. Írja le a Data Box sorrendjének beállításait. A beállítások közé tartozik a személyes/üzleti adatok, az előfizetés neve, az eszköz adatai és a szállítási információk. Ezeket a beállításokat paraméterekként kell használni, amikor a CLI-parancs futtatásával hozza létre a Data Box sorrendet. A következő táblázat a paraméterek beállításait tartalmazza `az databox job create` :

   | Beállítás (paraméter) | Description |  Mintaérték |
   |---|---|---|
   |resource-group| Használjon egy már létezőt, vagy hozzon létre újat. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |name| A létrehozandó rendelés neve. | "mydataboxorder"|
   |Kapcsolattartó neve| A szállítási címtől társított név. | "Gus Lengyelország"|
   |telefon| Annak a személynek vagy vállalatnak a telefonszáma, aki megkapja a rendelést.| "14255551234"
   |location| A legközelebbi Azure-régió, amely az eszköz szállítására kerül.| "USA nyugati régiója"|
   |SKU| A megrendeléshez megadott Data Box eszköz. Az érvényes értékek a következők: "DataBox", "DataBoxDisk" és "DataBoxHeavy"| "DataBox" |
   |e-mail-lista| A rendeléshez társított e-mail-címek.| "gusp@contoso.com" |
   |utca – cím 1| Az utca címe, ahová a rendelés szállítása történik. | "15700 NE 39. St" |
   |utca – Cím2| A másodlagos címek adatai, például az apartman száma vagy az épület száma. | "Bld 123" |
   |city| Az a város, ahová az eszközt el fogja szállítani. | Redmond |
   |állam vagy tartomány| Az az állapot, amelybe az eszközt szállítják.| WA |
   |ország| Az az ország, amelyet az eszköz el fog szállítani. | "Egyesült Államok" |
   |postai irányítószám| A szállítási címnek megfelelő irányítószám vagy postai irányítószám.| "98052"|
   |Vállalat neve| Annak a vállalatnak a neve, amelyhez dolgozni szeretne.| "Contoso, LTD" |
   |tárfiók| Az az Azure Storage-fiók, ahonnan importálni kívánja az adatait.| mystorageaccount|
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba  | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés<string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

2. A parancssorban válassza a választás vagy a terminál lehetőséget, az az [databox Job Create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) paranccsal hozza létre a Azure Data Box rendelését.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Íme egy példa a parancs használatára:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   A parancs futtatásának kimenete:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Az Azure CLI összes parancsa alapértelmezés szerint a JSON-t fogja használni kimeneti formátumként, hacsak nem módosítja. A kimeneti formátum a globális paraméter használatával módosítható `--output <output-format>` . A formátum a "Table" értékre való módosítása javítja a kimenet olvashatóságát.

   Az alábbi parancs egy kis csípéssel lett lefuttatva, hogy megváltoztassa a formázást:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   A parancs futtatásának kimenete:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>A megrendelés nyomon követése

# <a name="portal"></a>[Portál](#tab/portal)

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Lépjen a Data Box sorrendbe, majd az állapot megtekintéséhez lépjen az **Áttekintés** gombra. A portálon a megrendelés **Megrendelve** állapotban látható.

Ha nem áll rendelkezésre eszköz, értesítést fog kapni. Ha van elérhető eszköz, a Microsoft kiválaszt egyet a szállításhoz, és előkészíti a csomagot. Az eszköz előkészítése során a következő műveletek lesznek végrehajtva:

* SMB-megosztások létrehozása az eszközzel társított mindegyik tárfiókhoz.
* Hozzáférési hitelesítő adatok (felhasználónév és jelszó) kiosztása mindegyik megosztáshoz.
* Az eszköz zárolásának feloldásához szükséges jelszó létrehozása.
* A Data Box zárolásának célja, hogy megakadályozza az eszközhöz való illetéktelen hozzáférést a teljes eljárás során.

Az eszköz előkészítésének befejeztével a portálon a megrendelés **Feldolgozott** állapotban jelenik meg.

![Data Box rendelés feldolgozva](media/data-box-overview/data-box-order-status-processed.png)

A Microsoft ezután előkészíti, majd feladja a csomagot egy regionális fuvarozónál. Az eszköz feladását követően Ön megkapja a fuvarlevélszámot. A portál a **Feladva** állapotot mutatja.

![Data Box rendelés feladva](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Egyetlen megrendelés nyomon követése

Egy meglévő Azure Data Boxi rendelés nyomkövetési információinak lekéréséhez futtassa [az az databox Job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show)parancsot. A parancs információkat jelenít meg a rendeléssel kapcsolatban, például: név, Erőforráscsoport, követési információ, előfizetés-azonosító, kapcsolattartási adatok, szállítási típus és eszköz SKU.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `az databox job show` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforrás-csoport [kötelező]| A rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |név [kötelező]| A megjelenítendő sorrend neve. | "mydataboxorder"|
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés<string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

   Íme egy példa a parancsra, amelynek kimeneti formátuma a "Table" (táblázat) értékre van állítva:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   A parancs futtatásának kimenete:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> A lista sorrendje támogatott az előfizetés szintjén, és az erőforráscsoport egy opcionális paraméter (nem pedig egy szükséges paraméter).

### <a name="list-all-orders"></a>Az összes megrendelés listázása

Ha több eszközt is megrendelt, az [az databox Job List](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) parancs futtatásával megtekintheti az összes Azure Data Box rendelést. A parancs felsorolja az adott erőforráscsoporthoz tartozó összes rendelést. Emellett a kimenet: megrendelés neve, a szállítási állapot, az Azure-régió, a kézbesítés típusa és a rendelés állapota is látható. A megszakított megrendelések is szerepelnek a listában.
A parancs az egyes sorrendek időbélyegeit is megjeleníti.

```azurecli
az databox job list --resource-group <resource-group>
```

A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `az databox job list` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforrás-csoport [kötelező]| A rendeléseket tartalmazó erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés<string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

   Íme egy példa a parancsra, amelynek kimeneti formátuma a "Table" (táblázat) értékre van állítva:

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   A parancs futtatásának kimenete:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>A rendelés lemondása

# <a name="portal"></a>[Portál](#tab/portal)

A megrendelés megszakításához a Azure Portal lépjen az **Áttekintés** elemre, és válassza a **Mégse** lehetőséget a parancssáv alatt.

A megrendelést annak feladását követően bármikor megszakíthatja, mielőtt az Feldolgozott állapotba lép.

Egy megszakított megrendelés törléséhez lépjen az **Áttekintés** elemre, és válassza a **Törlés** elemet a parancssorból.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Rendelés visszavonása

Azure Data Box rendelés megszakításához futtassa [az az databox Job Cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel)parancsot. Meg kell adnia a megrendelés megszakításának okát.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `az databox job cancel` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforrás-csoport [kötelező]| A törlendő rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |név [kötelező]| A törlendő megrendelés neve. | "mydataboxorder"|
   |ok [kötelező]| A megrendelés megszakításának oka. | "Hibás adatokat adtam meg, és a megrendelés megszakításához szükséges." |
   |igen| Ne kérjen megerősítést. | – Igen (-y)| – igen – y |
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés<string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

   Íme egy példa a kimenettel rendelkező parancsra:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   A parancs futtatásának kimenete:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Megrendelés törlése

Ha megszakította Azure Data Box rendelést, az az [databox Job delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) paranccsal törölheti a sorrendet.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `az databox job delete` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |erőforrás-csoport [kötelező]| A törlendő rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |név [kötelező]| A törlendő megrendelés neve. | "mydataboxorder"|
   |előfizetést| Az Azure-előfizetés neve vagy azonosítója (GUID). | "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" |
   |igen| Ne kérjen megerősítést. | – Igen (-y)| – igen – y |
   |debug| Hibakeresési információk belefoglalása a részletes naplózásba | – hibakeresés |
   |segítség| Súgó megjelenítése ehhez a parancshoz. | --Help-h |
   |csak a-show-hibák| Csak a hibák megjelenítése, a figyelmeztetések letiltása. | – csak megjelenítés – hibák |
   |kimenet – o| Beállítja a kimeneti formátumot.  Megengedett értékek: JSON, jsonc, none, Table, TSV, YAML, yamlc. Az alapértelmezett érték a JSON. | --output "JSON" |
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés<string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

Íme egy példa a kimenettel rendelkező parancsra:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   A parancs futtatásának kimenete:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a Azure Data Box cikkeket, például a következőket:

> [!div class="checklist"]
>
> * A Data Box üzembe helyezésének előfeltételei
> * A Data Box megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

A következő oktatóanyag a Data Box beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box beállítása](./data-box-deploy-set-up.md)
