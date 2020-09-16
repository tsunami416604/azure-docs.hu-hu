---
title: Oktatóanyag a Azure Data Box rendeléséhez | Microsoft Docs
description: Ebben az oktatóanyagban megismerheti a Azure Data Box, egy hibrid megoldást, amely lehetővé teszi a helyszíni információk importálását az Azure-ba, valamint a Azure Data Box sorrendjét.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: alkohli
ms.openlocfilehash: c2d971c2c9375f58fd5f41a46716fac4bff29f88
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604308"
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

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

Jelentkezzen be az Azure-ba, és futtassa az Azure CLI-parancsokat kétféleképpen:

* Telepítheti a CLI-t, és helyileg is futtathatja a CLI-parancsokat.
* A CLI-parancsokat a Azure Cloud Shell Azure Portal belül is futtathatja.

Az oktatóanyaghoz az Azure CLI-t használjuk a Windows PowerShellen keresztül, de bármelyik lehetőséget szabadon választhat.

### <a name="for-azure-cli"></a>Azure CLI esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

#### <a name="install-the-cli-locally"></a>A parancssori felület helyi telepítése

* Telepítse az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -verziót 2.0.67 vagy újabb verzióra. Másik megoldásként az MSI-t is [telepítheti](https://aka.ms/installazurecliwindows).

**Bejelentkezés az Azure-ba**

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

**A Azure Data Box CLI bővítmény telepítése**

A Azure Data Box CLI-parancsok használata előtt telepítenie kell a bővítményt. Az Azure CLI-bővítmények hozzáférést biztosítanak azokhoz a kísérleti és kiadás előtti parancsokhoz, amelyek az alap CLI-vel még nincsenek szállítva. További információ a bővítményekről: [bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).

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

#### <a name="use-azure-cloud-shell"></a>Az Azure Cloud Shell használata

Az Azure által üzemeltetett interaktív rendszerhéj-környezetet [Azure Cloud Shell](https://shell.azure.com/)használhatja a BÖNGÉSZŐBEN a CLI-parancsok futtatásához. Azure Cloud Shell támogatja a bash vagy a Windows PowerShellt az Azure-szolgáltatásokkal. Az Azure CLI előre telepítve és konfigurálva van a fiókjával való használatra. Kattintson a Cloud Shell gombra a Azure Portal jobb felső részén található menüben:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

A gomb egy interaktív felületet indít el, amelyet a jelen útmutatóban ismertetett lépések futtatására használhat.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Azure PowerShell

Mielőtt elkezdené, győződjön meg róla, hogy:

* Telepítse a Windows PowerShell 6.2.4 vagy újabb verzióját.
* Telepítse a Azure PowerShell (AZ) modult.
* Telepítse a Azure Data Box (az. DataBox) modult.
* Jelentkezzen be az Azure-ba.

#### <a name="install-azure-powershell-and-modules-locally"></a>Azure PowerShell és modulok helyi telepítése

**A Windows PowerShell telepítése vagy frissítése**

A Windows PowerShell 6.2.4 vagy újabb verziójára lesz szükség. Ha szeretné megtudni, hogy a PowerShell melyik verzióját telepítette, futtassa a következőt: `$PSVersionTable` .

A következő kimenet jelenik meg:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Ha a verzió alacsonyabb, mint a 6.2.4, frissítenie kell a Windows PowerShell verzióját. A Windows PowerShell legújabb verziójának telepítéséhez tekintse meg a következőt: [install Azure PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7).

**Azure PowerShell és Data Box modulok telepítése**

A Azure Data Box megrendeléséhez telepítenie kell a Azure PowerShell modulokat a Azure PowerShell használatához. A Azure PowerShell modulok telepítése:

1. Telepítse a [Azure PowerShell az modult](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).
2. Ezután telepítse az az. DataBox parancsot a parancs használatával `Install-Module -Name Az.DataBox` .

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Nyisson meg egy Windows PowerShell-parancssorablakot, és jelentkezzen be az Azure-ba a [Kapcsolódás-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) paranccsal:

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

A sikeres bejelentkezés kimenete:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Az Azure-ba a Windows PowerShell használatával történő bejelentkezéssel kapcsolatos részletes információkért lásd: [bejelentkezés Azure PowerShellsal](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>A Data Box megrendelése

# <a name="portal"></a>[Portál](#tab/portal)

Az eszköz megrendeléséhez hajtsa végre a következő lépéseket a Azure Portal.

1. Microsoft Azure hitelesítő adatai használatával jelentkezzen be ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com).
2. Válassza az **+ Erőforrás létrehozása** lehetőséget, és keressen rá az *Azure Data Box* kifejezésre. Válassza az **Azure Data Box** lehetőséget.

   ![Azure Data Box kiválasztása](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Kattintson a **Létrehozás** gombra.

   ![Azure Data Box kiválasztása](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg vagy válassza ki a következő információkat, majd válassza az **Alkalmaz** lehetőséget.

    |Beállítás  |Érték  |
    |---------|---------|
    |Átvitel típusa     | Válassza az **Importálás az Azure-ba** lehetőséget.        |
    |Előfizetés     | Válasszon egy EA-, CSP- vagy Azure Sponsorship-előfizetést a Data Box szolgáltatáshoz. <br> Az előfizetés az Ön számlázási fiókjához lesz társítva.       |
    |Erőforráscsoport | Válasszon ki egy meglévő erőforráscsoportot. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. |
    |Forrásország/-régió    |    Válassza ki azt az országot vagy régiót, ahol az adatok jelenleg találhatók.         |
    |Azure-beli célrégió     |     Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat. <br> További információt a [regionális elérhetőséget](data-box-overview.md#region-availability) tárgyaló témakörben talál.            |

    [![Azure Data Box importálási sorrend](media/data-box-deploy-ordered/select-data-box-import-04b.png)](media/data-box-deploy-ordered/select-data-box-import-04b.png#lightbox)

5. Válassza a **Data Box** lehetőséget. Egy megrendelés maximális felhasználható kapacitása 80 TB. Nagyobb mennyiségű adat esetén több rendelést is létrehozhat.

    ![Az 1-es Data Box lehetőség kiválasztása](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. A **sorrendben**válassza az **alapok** lapot. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Tovább: adatok célhelye>** elemet.

    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés      | Az előfizetés automatikusan kitöltődik a korábbi kiválasztás alapján.|
    |Erőforráscsoport    | A korábban kiválasztott erőforráscsoport. |
    |Importálási rendelés neve | Adjon meg egy rövid nevet a megrendelés nyomon követéséhez. <br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.    |

    ![Az 1-es Data Box lehetőség kiválasztása](media/data-box-deploy-ordered/select-data-box-import-06.png)

    Alapértelmezés szerint az eszköz zárolásának feloldására szolgáló jelszó Microsoft által felügyelt kulccsal van titkosítva. A megrendelés befejezése után hozzáadhat egy ügyfél által felügyelt kulcsot. Az ügyfél által felügyelt kulcs lehetővé teszi, hogy az Azure Key Vault-kulcsból saját kulcsot használjon az eszköz feloldási jelszavának a megvédése érdekében. További információ: [az ügyfél által felügyelt kulcsok használata Azure Key Vaultban Azure Data Box](data-box-customer-managed-encryption-key-portal.md).

7. Az **adatcélhely** lapon válassza az **adatcél**lehetőséget.

    Ha a Storage- **fiók (ok)** tároló célhelyként használja, a következő képernyőkép jelenik meg:

    ![Azure Data Box adatcélhely](media/data-box-deploy-ordered/select-data-box-import-07.png)

    A megadott Azure-régió alapján válasszon ki egy vagy több tárfiókot a meglévő tárfiókok szűrt listájából. A Data Box legfeljebb 10 tárfiókkal köthető össze. Létrehozhat egy új **Általános célú v1**, **Általános célú v2** fiókot vagy egy **Blob Storage-fiókot** is.

    A virtuális hálózattal rendelkező tárfiókok támogatottak. Ahhoz, hogy a Data Box szolgáltatás működjön a biztonságos tárfiókok esetében, engedélyezze a megbízható szolgáltatásokat a tárfiók hálózati tűzfalának beállításai között. További információ: [Azure Data Box hozzáadása megbízható szolgáltatásként](../storage/common/storage-network-security.md#exceptions).

    Ha Data Box használatával **felügyelt lemezeket** hoz létre a helyszíni virtuális merevlemezekről (VHD-k), akkor a következő információkat is meg kell adnia:

    |Beállítás  |Érték  |
    |---------|---------|
    |Erőforráscsoportok     | Hozzon létre új erőforráscsoportokat, ha felügyelt lemezeket szeretne létrehozni helyszíni VHD-kből. Meglévő erőforráscsoportot csak akkor használhat, ha az erőforráscsoportot korábban hozták létre a felügyelt lemez Data Box szolgáltatás általi Data Box rendelésének létrehozásakor. <br> Adjon meg több erőforráscsoportot, pontosvesszővel elválasztva. Legfeljebb 10 erőforráscsoport használata támogatott.|

    ![Felügyelt lemez Data Box sorrendje](media/data-box-deploy-ordered/select-data-box-import-07b.png)

    A felügyelt lemezekhez megadott tárfiókot előkészítési tárfiókként használja a rendszer. A Data Box szolgáltatás lapblobként tölti fel a VHD-ket az előkészítési tárfiókba, majd felügyelt lemezekké konvertálja, és áthelyezi az erőforráscsoportba. További információ: [Az Azure-ba történő adatfeltöltés ellenőrzése](data-box-deploy-picked-up.md#verify-data-upload-to-azure).
   > [!NOTE]
   > Ha egy oldal blobját nem sikerült felügyelt lemezre átalakítani, a Storage-fiókban marad, és a tárterületért kell fizetnie.

    Kattintson a **Tovább gombra: a biztonság** lehetőségre.

1. Ha a **Biztonság**területen engedélyezni szeretné a szoftveres kettős titkosítást, válassza **a dupla titkosítás engedélyezése lehetőséget a rendeléshez**. 

   A szoftveres titkosítás a Data Box lévő adatok AES-256 bites titkosításán felül történik.

   > [!NOTE]
   > Ha engedélyezi ezt a beállítást, megteheti, hogy a rendelés feldolgozása és az Adatmásolás tovább tart. Ez a beállítás a megrendelés létrehozása után nem módosítható.

   ![Adatmező importálásának biztonsági képernyője, dupla titkosítás](media/data-box-deploy-ordered/select-data-box-import-07c.png)

   A folytatáshoz válassza a **tovább lehetőséget: kapcsolattartási adatok** .

8. A **kapcsolattartási adatok**területen válassza a **+ szállítási címek hozzáadása**elemet.

    ![Felügyelt lemez Data Box sorrendje](media/data-box-deploy-ordered/select-data-box-import-08a.png)

9. A **Szállítási cím** területen adja meg a vezeték- és utónevét, a vállalata nevét és postai címét, valamint egy érvényes telefonszámot. Válassza a **Cím ellenőrzése** lehetőséget. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről.

   ![Felügyelt lemez Data Box sorrendje](media/data-box-deploy-ordered/select-data-box-import-10.png)

   Ha az önfelügyelt szállítást választotta, a megrendelés sikeres elhelyezése után e-mailben értesítést fog kapni. Az önfelügyelt szállítással kapcsolatos további információkért lásd: [saját üzemeltetésű szállítás használata](data-box-portal-customer-managed-shipping.md).

10. Válassza a **szállítási címek hozzáadása** lehetőséget a szállítási adatok sikeres ellenőrzése után. Vissza fog térni a **kapcsolattartási adatok** lapra.

11. Miután visszatért a **kapcsolattartási adatokhoz** , adjon hozzá egy vagy több e-mail-címet. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre.

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

    ![Felügyelt lemez Data Box sorrendje](media/data-box-deploy-ordered/select-data-box-import-08c.png)

12. Tekintse át a **felülvizsgálati** és a megrendeléssel kapcsolatos információkat a rendeléssel, kapcsolatfelvételsel, értesítéssel és adatvédelmi feltételekkel kapcsolatban. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

13. Válassza a **Megrendelés** lehetőséget. A megrendelés létrehozása néhány percet vesz igénybe.

    ![Felügyelt lemez Data Box sorrendje](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi lépéseket követve rendeljen egy eszközt az Azure CLI használatával:

1. Írja le a Data Box sorrendjének beállításait. A beállítások közé tartozik a személyes/üzleti adatok, az előfizetés neve, az eszköz adatai és a szállítási információk. Ezeket a beállításokat paraméterekként kell használni, amikor a CLI-parancs futtatásával hozza létre a Data Box sorrendet. A következő táblázat a paraméterek beállításait tartalmazza `az databox job create` :

   | Beállítás (paraméter) | Leírás |  Mintaérték |
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
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
   |részletes| Adja meg a részletes naplózást. | --verbose |

2. A választott vagy terminálos parancssorban használja az az [adatmező-feladatsort](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) a Azure Data Box rendelés létrehozásához.

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

   Az alábbi parancs ugyanezt a parancsot futtatta egy kis csípéssel a formázás módosításához:

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

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Az eszköz megrendeléséhez hajtsa végre az alábbi lépéseket Azure PowerShell használatával:

1. Az importálási sorrend létrehozása előtt be kell szereznie a Storage-fiókot, és mentenie kell a Storage-fiók objektumot egy változóban.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Írja le a Data Box sorrendjének beállításait. A beállítások közé tartozik a személyes/üzleti adatok, az előfizetés neve, az eszköz adatai és a szállítási információk. Ezeket a beállításokat paraméterekként kell használni, amikor futtatja a PowerShell-parancsot a Data Box sorrend létrehozásához. A következő táblázat a [New-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/New-AzDataBoxJob)használt paraméterek beállításait mutatja be.

    | Beállítás (paraméter) | Leírás |  Mintaérték |
    |---|---|---|
    |ResourceGroupName [kötelező]| Meglévő erőforráscsoport használata. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
    |Név [kötelező]| A létrehozandó rendelés neve. | "mydataboxorder"|
    |ContactName [kötelező]| A szállítási címtől társított név. | "Gus Lengyelország"|
    |Telefonszám [kötelező]| Annak a személynek vagy vállalatnak a telefonszáma, aki megkapja a rendelést.| "14255551234"
    |Hely [kötelező]| A legközelebbi Azure-régió, amely az eszköz szállítására kerül.| WestUS|
    |DataBoxType [kötelező]| A megrendeléshez megadott Data Box eszköz. Az érvényes értékek a következők: "DataBox", "DataBoxDisk" és "DataBoxHeavy"| "DataBox" |
    |EmailId [kötelező]| A rendeléshez társított e-mail-címek.| "gusp@contoso.com" |
    |StreetAddress1 [kötelező]| Az utca címe, ahová a rendelés szállítása történik. | "15700 NE 39. St" |
    |StreetAddress2| A másodlagos címek adatai, például az apartman száma vagy az épület száma. | "Bld 123" |
    |StreetAddress3| A harmadlagos címen lévő információk. | |
    |Város [kötelező]| Az a város, ahová az eszközt el fogja szállítani. | Redmond |
    |StateOrProvinceCode [kötelező]| Az az állapot, amelybe az eszközt szállítják.| WA |
    |Országhívószám [kötelező]| Az az ország, amelyet az eszköz el fog szállítani. | "Egyesült Államok" |
    |Irányítószám [kötelező]| A szállítási címnek megfelelő irányítószám vagy postai irányítószám.| "98052"|
    |CompanyName| Annak a vállalatnak a neve, amelyhez dolgozni szeretne.| "Contoso, LTD" |
    |StorageAccountResourceId [kötelező]| Az Azure Storage-fiók azonosítója, amelyből az adatok importálására kerül.| <AzStorageAccount>. azonosító |

3. A parancssorban válassza a választás vagy a terminál lehetőséget, a [New-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/New-AzDataBoxJob) használatával hozza létre a Azure Data Box sorrendjét.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   A parancs futtatásának kimenete:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
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
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
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
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
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
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Egyetlen megrendelés nyomon követése

Egy meglévő Azure Data Box-rendelés nyomkövetési információinak lekéréséhez futtassa a [Get-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/Get-AzDataBoxJob)parancsot. A parancs információkat jelenít meg a rendeléssel kapcsolatban, például: név, Erőforráscsoport, követési információ, előfizetés-azonosító, kapcsolattartási adatok, szállítási típus és eszköz SKU.

> [!NOTE]
> `Get-AzDataBoxJob` egy és több megrendelés megjelenítésére szolgál. A különbség az, hogy megadja a rendelés nevét az egyes rendelésekhez.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `Get-AzDataBoxJob` :

   | Paraméter | Leírás |  Mintaérték |
   |---|---|---|
   |ResourceGroup [kötelező]| A rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
   |Név [kötelező]| Annak a rendelésnek a neve, amelyre vonatkozóan információt szeretne kapni. | "mydataboxorder"|
   |ResourceId| A rendeléshez társított erőforrás azonosítója. |  |

   Íme egy példa a kimenettel rendelkező parancsra:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   A parancs futtatásának kimenete:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Az összes megrendelés listázása

Ha több eszközt is megrendelt, a [Get-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/Get-AzDataBoxJob) futtatásával megtekintheti az összes Azure Data Box-rendelést. A parancs felsorolja az adott erőforráscsoporthoz tartozó összes rendelést. Emellett a kimenet: megrendelés neve, a szállítási állapot, az Azure-régió, a kézbesítés típusa és a rendelés állapota is látható. A megszakított megrendelések is szerepelnek a listában.
A parancs az egyes sorrendek időbélyegeit is megjeleníti.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Íme egy példa a parancsra:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

A parancs futtatásának kimenete:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
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
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
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
   |lekérdezés| A JMESPath lekérdezési karakterlánca. További információ: [JMESPath](http://jmespath.org/). | – lekérdezés <string>|
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

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Rendelés visszavonása

Azure Data Box rendelés megszakításához futtassa a [stop-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/stop-azdataboxjob)parancsot. Meg kell adnia a megrendelés megszakításának okát.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `Stop-AzDataBoxJob` :

| Paraméter | Leírás |  Mintaérték |
|---|---|---|
|ResourceGroup [kötelező]| A megszakítani kívánt rendeléshez tartozó erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
|Név [kötelező]| A törlendő megrendelés neve. | "mydataboxorder"|
|Ok [kötelező]| A megrendelés megszakításának oka. | "Hibás adatokat adtam meg, és a megrendelés megszakításához szükséges." |
|Force | Azt kényszeríti, hogy a parancsmag felhasználói megerősítés nélkül fusson. | -Force |

Íme egy példa a kimenettel rendelkező parancsra:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

A parancs futtatásának kimenete:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Megrendelés törlése

Ha megszakított egy Azure Data Box rendelést, a [Remove-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/remove-azdataboxjob) parancsot futtatva törölheti a sorrendet.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

A következő táblázat a paraméterekkel kapcsolatos információkat tartalmazza `Remove-AzDataBoxJob` :

| Paraméter | Leírás |  Mintaérték |
|---|---|---|
|ResourceGroup [kötelező]| A törlendő rendeléshez társított erőforráscsoport neve. Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. | myresourcegroup|
|Név [kötelező]| A törlendő megrendelés neve. | "mydataboxorder"|
|Force | Azt kényszeríti, hogy a parancsmag felhasználói megerősítés nélkül fusson. | -Force |

Íme egy példa a kimenettel rendelkező parancsra:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

A parancs futtatásának kimenete:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>További lépések

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
