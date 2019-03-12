---
title: A Batch Azure CLI használatának első lépései | Microsoft Docs
description: Gyors bevezetést olvashat Azure Batch szolgáltatás erőforrásainak kezelésére szolgáló Batch parancsokhoz az Azure CLI-ben
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 07/24/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2bd67ebb977a37c75631f16fbbf4c7dbd6bf250
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765370"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Batch-erőforrássok kezelése az Azure CLI-vel

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. A szolgáltatás macOS, Linux és Windows rendszereken használható. Az Azure CLI-t az Azure erőforrások parancssorból történő kezelésére és felügyeletére optimalizáltuk. Az Azure CLI használatával felügyelheti Azure Batch-fiókját, valamint erőforrásokat kezelhet, például készleteket, feladatokat és tevékenységeket is. Az Azure CLI-vel a Batch API-k, az Azure Portal és a Batch PowerShell-parancsmagok használatával végrehajtott műveletek közül is sokat elvégezhet.

Ez a cikk betekintést nyújt az [Azure CLI 2.0-ás verziójának](https://docs.microsoft.com/cli/azure) Batch-csel történő használatába. A CLI és az Azure együttes használatával kapcsolatban további információt a [Bevezetés az Azure CLI használatába](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) című cikkben talál.

## <a name="set-up-the-azure-cli"></a>Az Azure CLI beállítása

A legújabb Azure CLI-t az [Azure Cloud Shellben](../cloud-shell/overview.md) futtathatja. Az Azure CLI helyi telepítéséhez kövesse az [Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli) ismertető cikk lépéseit.

> [!TIP]
> Ajánlott gyakran frissíteni az Azure CLI telepítést a szolgáltatásfrissítések és -fejlesztések kihasználása érdekében.
> 
> 

## <a name="command-help"></a>Segítség a parancsokhoz

Az Azure CLI-ben minden parancshoz megjeleníthet súgószöveget, ha a parancshoz hozzáfűzi a `-h` utótagot. Az egyéb beállításokat hagyja változatlanul. Példa:

* Ha segítséget szeretne kérni az `az` parancshoz, írja be a következőt: `az -h`
* Ha le szeretné kérni az összes Batch parancs listáját a parancssori felületen, használja a következőt: `az batch -h`
* Ha segítséget szeretne kérni Batch-fiók létrehozásához, írja be a következőt: `az batch account create -h`

Ha bizonytalan, a `-h` parancssori kapcsoló használatával az Azure CLI bármely parancsához segítséget kérhet.



További részleteket az [Azure CLI Batch-parancsaival](/cli/azure/batch) kapcsolatban az Azure CLI használati útmutatójában talál. 

## <a name="log-in-and-authenticate"></a>Bejelentkezés és hitelesítés

Az Azure CLI Batch-csel történő használatához be kell jelentkeznie és hitelesítenie kell fiókját. Ehhez két egyszerű lépést kell követnie:

1. **Bejelentkezés az Azure-ba.** Az Azure-ba történő bejelentkezés hozzáférést biztosít az Azure Resource Manager parancsaihoz, beleértve a [Batch Management szolgáltatás](batch-management-dotnet.md) parancsait is.  
2. **Bejelentkezés a Batch-fiókjába.** A Batch-fiókba történő bejelentkezés hozzáférést biztosít a Batch szolgáltatás parancsaihoz.   

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Az Azure-szolgáltatásba több módon is bejelentkezhet, ezeket részletesen a [Bejelentkezés az Azure használatával](/cli/azure/authenticate-azure-cli) című cikk ismerteti:

1. [Interaktív bejelentkezés](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Az Interaktív bejelentkezést akkor használja, ha személyesen szeretne Azure CLI-parancsokat futtatni a parancssor használatával.
2. [Bejelentkezés szolgáltatásnévvel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Jelentkezzen be szolgáltatásnévvel, ha szkript vagy alkalmazás használatával kíván Azure CLI-parancsokat futtatni.

Ebben a cikkben az Interaktív bejelentkezést fogjuk használni az Azure-ba történő belépéshez. Írja be az [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) utasítást a parancssorba:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

Ahogyan az itt látható, az `az login` parancs egy tokent ad vissza, amely a hitelesítéshez használható. A megjelenő utasításoknak megfelelően nyisson meg egy weblapot és küldje el a tokent az Azure-nak:

![Jelentkezzen be az Azure-ba](./media/batch-cli-get-started/az-login.png)

A minta rendszerhéj-parancsfájlok szakaszban felsorolt példák azt is bemutatják, hogyan indítunk majd indítsa el az Azure CLI-munkamenetet. Bejelentkezés után parancshívások használatával elkezdheti a munkát a különböző Batch Management-erőforrásokkal, többek között a Batch-fiókokkal, kulcsokkal, alkalmazáscsomagokkal és kvótákkal.  

### <a name="log-in-to-your-batch-account"></a>Bejelentkezés a Batch-fiókjába

Ahhoz, hogy az Azure CLI segítségével kezelhessen Batch-erőforrásokat, például készleteket, feladatokat és tevékenységeket, be kell jelentkeznie Batch-fiókjába, és azt hitelesítenie kell. A Batch szolgáltatásba történő bejelentkezéshez használja a [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login) parancsot. 

A Batch-fiók hitelesítését két módon is elvégezheti:

- **Hitelesítés az Azure Active Directory (Azure AD) használatával** 

    Az Azure AD-val történő hitelesítés az alapértelmezett beállítás az Azure CLI és a Batch együttes használatánál, és a legtöbb esethez ezt a hitelesítési módot ajánljuk. 
    
    Amikor az interaktív bejelentkezés segítségével lép be az Azure-ba, ahogyan azt az előző részben is említettük, a hitelesítő adatait a rendszer a gyorsítótárba helyezi, így az Azure CLI ugyanezeket a hitelesítő adatokat használva be tud jelentkezni Batch-fiókjába is. Ha szolgáltatásnév használatával jelentkezik be az Azure-ba, a rendszer a szolgáltatásnév hitelesítő adatait használva fog bejelentkezni Batch-fiókjába.

    Az Azure AD előnye a szerepköralapú hozzáférés-vezérlés (RBAC) használatában rejlik. Szerepköralapú hozzáférés-vezérlés használatával a felhasználók hozzáférése a kiosztott szerepkörtől függ, nem pedig attól, hogy rendelkeznek-e a fiókhoz tartozó hitelesítőkulccsal. Így ahelyett, hogy hozzáférési kulcsokat kellene kezelnie, elég ha a szerepköröket kezeli, a hozzáférést és a hitelesítést pedig az Azure AD-ra bízhatja.  

     Ahhoz, hogy az Azure AD használatával jelentkezzen be Batch-fiókjába, használja a [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login) parancsot: 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Megosztott kulcsos hitelesítés**

    A [megosztott kulcsos hitelesítés](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) a fiókja hozzáférési kulcsait használja, hogy hitelesítse az Azure CLI-parancsokat a Batch szolgáltatás felé.

    Ha Batch-parancsok hívását szeretné automatizálni Azure CLI-szkriptek segítségével, használhatja a megosztott kulcsos hitelesítést, vagy létrehozhat egy Azure AD szolgáltatásnevet is. Bizonyos esetekben viszont a megosztott kulcsos hitelesítés használata egyszerűbb lehet, mint létrehozni egy szolgáltatásnevet.  

    A megosztott kulcsos hitelesítéssel történő bejelentkezéshez a `--shared-key-auth` kapcsolót is adja meg a parancssorban:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

A minta rendszerhéj-parancsfájlok szakaszban felsorolt példák bemutatják, hogyan bejelentkezni az Azure CLI-vel a Batch-fiók-egyaránt az Azure AD és a megosztott kulcsot.

## <a name="use-azure-batch-cli-extension-commands"></a>Az Azure Batch CLI-bővítmény parancsainak használata

Ha telepíti az Azure Batch CLI-bővítményt, az Azure parancssori felületén végpontok között futtathat Batch-feladatokat kódírás nélkül. A bővítmény által támogatott Batch-parancsokkal JSON-sablonok alapján hozhat létre készleteket, feladatokat és tevékenységeket az Azure parancssori felületén. A bővítmény parancssori felületéről ezenkívül feltöltheti a feladatok bemeneti fájljait a Batch-fiókhoz társított Azure Storage-fiókba, és letöltheti a feladat kimeneti fájljait is. További információkért tekintse át [az Azure Batch parancssori felületi sablonjainak és fájlátviteli funkciójának használatával](batch-cli-templates.md) foglalkozó témakört.

## <a name="script-examples"></a>Példaszkriptek

A gyakori feladatok végrehajtásával kapcsolatban tekintse meg a Batch [parancssori példaszkriptjeit](cli-samples.md). A példák az Azure CLI-ben és a Batchben a fiókok, készletek, feladatok és tevékenységek létrehozásához és felügyeletéhez elérhetők közül számos parancsot bemutatnak. 

## <a name="json-files-for-resource-creation"></a>Erőforrás létrehozása JSON-fájlok használatával

Amikor olyan Batch-erőforrásokat hoz létre, mint a készletek és a feladatok, megadhat egy JSON-fájlt, amely az új erőforrás konfigurációját tartalmazza, ahelyett, hogy parancssori kapcsolókként adná át a paramétereket. Példa:

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
* A **kapcsolóval megtekintheti a**parancskimenetet JSON-fájlként`--json`. Például az `az batch pool show pool001 --json` JSON-formátumban jeleníti meg a pool001 tulajdonságait. Ezután másolja ki és módosíthatja ezt a kimenetet a használatára egy `--json-file` (lásd a cikk korábbi részeiben JSON-fájlok).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->

## <a name="next-steps"></a>További lépések

* További információt az Azure CLI-vel kapcsolatban az [Azure CLI dokumentációjában](https://docs.microsoft.com/cli/azure) talál.
* További információt a Batch-erőforrásokkal kapcsolatban a [Az Azure Batch áttekintése fejlesztők számára](batch-api-basics.md) című cikkben talál.
* További információkat a készletek, feladatok és tevékenységek Batch-sablonokkal, kódírás nélkül történő létrehozásáról [az Azure Batch parancssori felületi sablonjainak és fájlátviteli funkciójának használatával kapcsolatos](batch-cli-templates.md) cikkben talál.

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
