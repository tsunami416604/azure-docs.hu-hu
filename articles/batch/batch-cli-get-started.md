---
title: A Batch Azure CLI használatának első lépései | Microsoft Docs
description: Gyors bevezetést olvashat Azure Batch szolgáltatás erőforrásainak kezelésére szolgáló Batch parancsokhoz az Azure CLI-ben
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 09/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: df808078ffe6eedf8abaa32a94edaaf1355b7fc6
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129904"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Batch-erőforrássok kezelése az Azure CLI-vel

Az Azure CLI 2.0 az Azure új parancssori felülete, amely Azure-erőforrások felügyeletére szolgál. A szolgáltatás macOS, Linux és Windows rendszereken használható. Az Azure CLI 2.0-t az Azure erőforrások parancssorból történő kezelésére és felügyeletére optimalizáltuk. Az Azure CLI használatával felügyelheti Azure Batch-fiókját, valamint erőforrásokat kezelhet, például készleteket, feladatokat és tevékenységeket is. Az Azure CLI-vel a Batch API-k, az Azure Portal és a Batch PowerShell-parancsmagok használatával végrehajtott műveletek közül is sokat elvégezhet.

Ez a cikk betekintést nyújt az [Azure CLI 2.0-ás verziójának](https://docs.microsoft.com/cli/azure) Batch-csel történő használatába. A CLI és az Azure együttes használatával kapcsolatban további információt a [Bevezetés az Azure CLI 2.0 használatába](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) című cikkben talál.

A Microsoft az Azure CLI legújabb, 2.0-ás verziójának használatát ajánlja. A 2.0-ás verzióval kapcsolatban további információt az [Mostantól általánosan elérhető az Azure parancssori felületének 2.0-s verziója](https://azure.microsoft.com/blog/announcing-general-availability-of-vm-storage-and-network-azure-cli-2-0/) című blogbejegyzésben talál.

## <a name="set-up-the-azure-cli"></a>Az Azure CLI beállítása

Az Azure CLI telepítéséhez kövesse a [Telepítse az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli) című cikk lépéseit.

> [!TIP]
> Ajánlott gyakran frissíteni az Azure CLI telepítést a szolgáltatásfrissítések és -fejlesztések kihasználása érdekében.
> 
> 

## <a name="command-help"></a>Segítség a parancsokhoz

Az Azure CLI-ben minden parancshoz megjeleníthet súgószöveget, ha a parancshoz hozzáfűzi a `-h` utótagot. Az egyéb beállításokat hagyja változatlanul. Például:

* Ha segítséget szeretne kérni az `az` parancshoz, írja be a következőt: `az -h`
* Ha le szeretné kérni az összes Batch parancs listáját a parancssori felületen, használja a következőt: `az batch -h`
* Ha segítséget szeretne kérni Batch-fiók létrehozásához, írja be a következőt: `az batch account create -h`

Ha bizonytalan, a `-h` parancssori kapcsoló használatával az Azure CLI bármely parancsához segítséget kérhet.

> [!NOTE]
> Az Azure CLI korábbi verziói az `azure` előtagot használták a CLI-parancsok megkülönböztetéséhez. A 2.0-ás verzióban minden parancs az `az` előtagot használja. Gondoskodjon róla, hogy szkriptjei a 2.0-ás verziónak megfelelő új szintaxist használják.
>
>  

További részleteket az [Azure CLI Batch-parancsaival](https://docs.microsoft.com/cli/azure/batch) kapcsolatban az Azure CLI használati útmutatójában talál. 

## <a name="log-in-and-authenticate"></a>Bejelentkezés és hitelesítés

Az Azure CLI Batch-csel történő használatához be kell jelentkeznie és hitelesítenie kell fiókját. Ehhez két egyszerű lépést kell követnie:

1. **Bejelentkezés az Azure-ba.** Az Azure-ba történő bejelentkezés hozzáférést biztosít az Azure Resource Manager parancsaihoz, beleértve a [Batch Management szolgáltatás](batch-management-dotnet.md) parancsait is.  
2. **Bejelentkezés a Batch-fiókjába.** A Batch-fiókba történő bejelentkezés hozzáférést biztosít a Batch szolgáltatás parancsaihoz.   

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Az Azure-szolgáltatásba több módon is bejelentkezhet, ezeket részletesen a [Bejelentkezés az Azure CLI 2.0 használatával](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) című cikk ismerteti:

1. [Interaktív bejelentkezés](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_interactive_log_in). Az Interaktív bejelentkezést akkor használja, ha személyesen szeretne Azure CLI-parancsokat futtatni a parancssor használatával.
2. [Bejelentkezés szolgáltatásnévvel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_logging_in_with_a_service_principal). Jelentkezzen be szolgáltatásnévvel, ha szkript vagy alkalmazás használatával kíván Azure CLI-parancsokat futtatni.

Ebben a cikkben az Interaktív bejelentkezést fogjuk használni az Azure-ba történő belépéshez. Írja be az [az login](https://docs.microsoft.com/cli/azure/reference-index#az_login) utasítást a parancssorba:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

Ahogyan az itt látható, az `az login` parancs egy tokent ad vissza, amely a hitelesítéshez használható. A megjelenő utasításoknak megfelelően nyisson meg egy weblapot és küldje el a tokent az Azure-nak:

![Jelentkezzen be az Azure-ba](./media/batch-cli-get-started/az-login.png)

A [Shell-szkript minták](#sample-shell-scripts) részben felsorolt példákban is az interaktív bejelentkezés segítségével indítunk majd Azure CLI-munkamenetet. Bejelentkezés után parancshívások használatával elkezdheti a munkát a különböző Batch Management-erőforrásokkal, többek között a Batch-fiókokkal, kulcsokkal, alkalmazáscsomagokkal és kvótákkal.  

### <a name="log-in-to-your-batch-account"></a>Bejelentkezés a Batch-fiókjába

Ahhoz, hogy az Azure CLI segítségével kezelhessen Batch-erőforrásokat, például készleteket, feladatokat és tevékenységeket, be kell jelentkeznie Batch-fiókjába, és azt hitelesítenie kell. A Batch szolgáltatásba történő bejelentkezéshez használja a [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) parancsot. 

A Batch-fiók hitelesítését két módon is elvégezheti:

- **Hitelesítés az Azure Active Directory (Azure AD) használatával.** 

    Az Azure AD-val történő hitelesítés az alapértelmezett beállítás az Azure CLI és a Batch együttes használatánál, és a legtöbb esethez ezt a hitelesítési módot ajánljuk. 
    
    Amikor az interaktív bejelentkezés segítségével lép be az Azure-ba, ahogyan azt az előző részben is említettük, a hitelesítő adatait a rendszer a gyorsítótárba helyezi, így az Azure CLI ugyanezeket a hitelesítő adatokat használva be tud jelentkezni Batch-fiókjába is. Ha szolgáltatásnév használatával jelentkezik be az Azure-ba, a rendszer a szolgáltatásnév hitelesítő adatait használva fog bejelentkezni Batch-fiókjába.

    Az Azure AD előnye a szerepköralapú hozzáférés-vezérlés (RBAC) használatában rejlik. Szerepköralapú hozzáférés-vezérlés használatával a felhasználók hozzáférése a kiosztott szerepkörtől függ, nem pedig attól, hogy rendelkeznek-e a fiókhoz tartozó hitelesítőkulccsal. Így ahelyett, hogy hozzáférési kulcsokat kellene kezelnie, elég ha a szerepköröket kezeli, a hozzáférést és a hitelesítést pedig az Azure AD-ra bízhatja.  

     Ahhoz, hogy az Azure AD használatával jelentkezzen be Batch-fiókjába, használja a [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) parancsot: 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Megosztott kulcsos hitelesítés.**

    A [megosztott kulcsos hitelesítés](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) a fiókja hozzáférési kulcsait használja, hogy hitelesítse az Azure CLI-parancsokat a Batch szolgáltatás felé.

    Ha Batch-parancsok hívását szeretné automatizálni Azure CLI-szkriptek segítségével, használhatja a megosztott kulcsos hitelesítést, vagy létrehozhat egy Azure AD szolgáltatásnevet is. Bizonyos esetekben viszont a megosztott kulcsos hitelesítés használata egyszerűbb lehet, mint létrehozni egy szolgáltatásnevet.  

    A megosztott kulcsos hitelesítéssel történő bejelentkezéshez a `--shared-key-auth` kapcsolót is adja meg a parancssorban:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

A [Shell-szkript minták](#sample-shell-scripts) rész példákon keresztül mutatja be, hogyan jelentkezhet be az Azure CLI-vel Batch-fiókjába mind az Azure AD, mind a megosztott kulcsos hitelesítés használatával.

## <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Az Azure Batch parancssori felületi sablonjainak és fájlátviteli funkciójának (előzetes verzió) használata

Az Azure parancssori felületén végpontok között futtathat Batch-feladatokat kódírás nélkül. A Batch sablonfájljai támogatják készletek, feladatok és tevékenységek létrehozását az Azure parancssori felületéről. Az Azure parancssori felületéről feltöltheti a feladatok bemeneti fájljait a Batch-fiókhoz társított Azure Storage-fiókba, és onnan letöltheti a feladat kimeneti fájljait. További információk: [Az Azure Batch parancssori felületi sablonjainak és fájlátviteli funkciójának (előzetes verzió) használata](batch-cli-templates.md).

## <a name="script-examples"></a>Példaszkriptek

A gyakori feladatok végrehajtásával kapcsolatban tekintse meg a Batch [parancssori példaszkriptjeit](cli-samples.md). A példák az Azure CLI-ben és a Batchben a fiókok, készletek, feladatok és tevékenységek létrehozásához és felügyeletéhez elérhetők közül számos parancsot bemutatnak. 

## <a name="json-files-for-resource-creation"></a>Erőforrás létrehozása JSON-fájlok használatával

Amikor olyan Batch-erőforrásokat hoz létre, mint a készletek és a feladatok, megadhat egy JSON-fájlt, amely az új erőforrás konfigurációját tartalmazza, ahelyett, hogy parancssori kapcsolókként adná át a paramétereket. Például:

```azurecli
az batch pool create my_batch_pool.json
```

Míg a legtöbb Batch-erőforrás létrehozását végre lehet hajtani mindössze parancssori kapcsolókkal, néhány szolgáltatáshoz egy olyan JSON-formátumú fájlt kell megadnia, amely tartalmazza az erőforrás adatait. Például JSON-fájlt kell használnia, ha erőforrásfájlokat szeretne meghatározni egy indítási tevékenységhez.

Az erőforrás létrehozásához szükséges JSON-szintaxissal kapcsolatban az MSDN webhelyén található, [Batch – REST API-referencia][rest_api] című dokumentáció nyújt segítséget. A REST API-referenciában mindegyik „*Erőforrástípus* hozzáadása” témakör tartalmaz egy-egy JSON-szkript mintát is az adott erőforrás létrehozásához. Ezeket a JSON-mintaszkripteket sablonként használhatja az Azure CLI-vel használandó JSON-fájlok írásához. Például, ha a készletek létrehozásához használt JSON-szintaxisra kíváncsi, tekintse meg a [Készlet hozzáadása a fiókhoz][rest_add_pool] című témakört.

JSON-fájlra hivatkozó szkriptre példát a [Feladatok és tevékenységek futtatása a Batch-csel](./scripts/batch-cli-sample-run-job.md) című témakörben talál.

> [!NOTE]
> Ha az erőforrás létrehozásakor megad egy JSON-fájlt, a rendszer figyelmen kívül hagyja a parancssoron meghatározott többi paramétert.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Hatékony lekérdezések Batch-erőforrásokhoz

Minden Batch erőforrástípus támogat egy `list` parancsot, amely lekérdezi a Batch-fiókját, és listázza az adott típusú erőforrásokat. Például listázhatja a fiókja készleteit és egy feladat tevékenységeit:

```azurecli
az batch pool list
az batch task list --job-id job001
```

Ha a `list` művelettel küld lekérdezést a Batch-szolgáltatásnak, megadhat egy OData-záradékot, amellyel korlátozhatja a visszaküldött adatok mennyiségét. Mivel a szűrés kiszolgálói oldalon történik, csak a kívánt adatok kerülnek továbbításra. Ezekkel a záradékokkal a listaműveletek végrehajtásakor sávszélességet (és ezáltal időt) takaríthat meg.

A következő táblázat bemutatja azokat, az OData-záradékokat, amelyeket a Batch szolgáltatás támogat:

| Záradék | Leírás |
|---|---|
| `--select-clause [select-clause]` | A tulajdonságok egy részét adja vissza minden entitás esetében. |
| `--filter-clause [filter-clause]` | Csak olyan entitásokat ad vissza, amelyek megfelelnek a megadott OData-kifejezésnek. |
| `--expand-clause [expand-clause]` | Az entitásadatokat egy mögöttes REST-hívással szerzi meg. A bővítés záradék jelenleg csak a `stats` tulajdonságot támogatja. |

Az OData-záradékok használatát bemutató mintaszkript megtekintéséhez lásd a [Feladatok és tevékenységek futtatása a Batch-csel](./scripts/batch-cli-sample-run-job.md) című témakört.

További információt az OData-záradékokkal létrehozható hatékony lista-lekérdezésekkel kapcsolatban a [Az Azure Batch szolgáltatás hatékony lekérdezése](batch-efficient-list-queries.md) című témakörben talál.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Az következő tippek segíthetnek az Azure CLI használata során felmerülő problémák elhárításakor:

* A `-h` segítségével **súgószöveget** kérhet bármely CLI parancshoz
* A `-v` és a `-vv` segítségével **részletes** parancskimenetet jeleníthet meg. Ha a parancs tartalmazza a `-vv` jelzőt, az Azure CLI megjeleníti a tényleges REST-kérelmeket és válaszokat is. Ezek a kapcsolók jól jönnek a teljes hibakimenet megjelenítéséhez.
* A **kapcsolóval megtekintheti a**parancskimenetet JSON-fájlként`--json`. Például az `az batch pool show pool001 --json` JSON-formátumban jeleníti meg a pool001 tulajdonságait. Ezt követően másolhatja és módosíthatja ezt a kimenetet, hogy felhasználhassa egy `--json-file` kapcsolóval (lásd a[JSON-fájlok](#json-files) szakaszt a jelen cikk korábbi részében).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->


## <a name="next-steps"></a>További lépések

* További információt az Azure CLI-vel kapcsolatban az [Azure CLI dokumentációjában](https://docs.microsoft.com/cli/azure) talál.
* További információt a Batch-erőforrásokkal kapcsolatban a [Az Azure Batch áttekintése fejlesztők számára](batch-api-basics.md) című cikkben talál.
* További információkat készletek, feladatok és tevékenységek Batch-sablonokkal, kódírás nélkül történő létrehozásáról [az Azure Batch parancssori felületi sablonjainak és (előzetes verziójú) fájlátviteli funkciójának használatával kapcsolatos](batch-cli-templates.md) cikkben talál.

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
