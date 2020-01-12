---
title: Központi telepítési adataik titkosítása
description: Ismerje meg a tároló-példány erőforrásainak megőrzött adatainak titkosítását, valamint az adattitkosítást az ügyfél által felügyelt kulccsal
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 146effd7f1a7ad1ddd94886d1a79e2914bd1c94b
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904210"
---
# <a name="encrypt-deployment-data"></a>Központi telepítési adataik titkosítása

Amikor a felhőben Azure Container Instances (ACI) erőforrásait futtatja, az ACI szolgáltatás összegyűjti és megőrzi a tárolókkal kapcsolatos adatokat. Az ACI automatikusan titkosítja ezeket az adatfájlokat, amikor a felhőben is megmarad. Ezzel a titkosítással megvédheti adatait, hogy megfeleljen a szervezete biztonsági és megfelelőségi kötelezettségvállalásainak. Az ACI lehetővé teszi az adatok saját kulccsal történő titkosítását is, így az ACI-környezetekhez kapcsolódó adatok nagyobb mértékben szabályozhatók.

## <a name="about-aci-data-encryption"></a>Tudnivalók az ACI-adattitkosításról 

Az ACI-ban tárolt adatai titkosítva vannak, és a 256 bites AES-titkosítással lettek visszafejtve. Az összes ACI üzemelő példány esetében engedélyezve van, és nem kell módosítania az üzemelő példányt vagy tárolókat a titkosítás kihasználása érdekében. Ez magában foglalja az üzembe helyezésre vonatkozó metaadatokat, a környezeti változókat, a tárolókban átadott kulcsokat, valamint a tárolók leállítása után megőrzött naplókat, így továbbra is megtekintheti őket. A titkosítás nem befolyásolja a tároló csoport teljesítményét, és a titkosításhoz nincs további díj.

## <a name="encryption-key-management"></a>Titkosítási kulcsok kezelése

A Microsoft által felügyelt kulcsokat a tároló adatai titkosítására használhatja, vagy a titkosítást a saját kulcsaival is kezelheti. A következő táblázat összehasonlítja ezeket a beállításokat: 

|    |    Microsoft által felügyelt kulcsok     |     Ügyfél által felügyelt kulcsok     |
|----|----|----|
|    Titkosítási/visszafejtési műveletek    |    Azure    |    Azure    |
|    Kulcstároló    |    Microsoft Key Store    |    Azure Key Vault    |
|    Kulcs rotációs felelőssége    |    Microsoft    |    Ügyfél    |
|    Kulcs elérése    |    Csak Microsoft    |    Microsoft, ügyfél    |

A dokumentum többi része ismerteti azokat a lépéseket, amelyek szükségesek az ACI üzembe helyezési adatoknak a kulccsal való titkosításához (az ügyfél által felügyelt kulcs). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Az adattitkosítás ügyfél által felügyelt kulccsal

### <a name="create-service-principal-for-aci"></a>Egyszerű szolgáltatásnév létrehozása az ACI-hoz

Első lépésként győződjön meg arról, hogy az [Azure-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) rendelkezik a Azure Container instances szolgáltatás engedélyeinek megadásához hozzárendelt egyszerű szolgáltatással. 

Az alábbi CLI-parancs beállítja az ACI SP-t az Azure-környezetben:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

A parancs futtatásának kimenete olyan egyszerű szolgáltatásnevet mutat be, amely a "displayName": "Azure Container instance Service" beállítással lett beállítva.

### <a name="create-a-key-vault-resource"></a>Key Vault erőforrás létrehozása

Hozzon létre egy Azure Key Vaultt a [Azure Portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault), a [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)vagy a [PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell)használatával. 

A Key Vault tulajdonságaihoz használja a következő irányelveket: 
* Név: egyedi nevet kell megadni. 
* Előfizetés: válasszon egy előfizetést.
* Az erőforráscsoport területen válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre újat, és adjon meg egy erőforráscsoport-nevet.
* A hely legördülő menüben válassza ki a kívánt helyet.
* A többi beállítást az alapértelmezett értékekre vagy a további követelmények alapján is elhagyhatja.

> [!IMPORTANT]
> Ha ügyfél által felügyelt kulcsokat használ egy ACI-telepítési sablon titkosításához, akkor a következő két tulajdonságot érdemes beállítani a kulcstartón, a Soft delete parancsot, és ne törölje a kiürítést. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de a PowerShell vagy az Azure CLI használatával is engedélyezhető egy új vagy meglévő kulcstartón.

### <a name="generate-a-new-key"></a>Új kulcs létrehozása 

Miután létrehozta a kulcstartót, navigáljon a Azure Portal található erőforráshoz. Az erőforrás panel bal oldali navigációs menüjének beállítások területén kattintson a **kulcsok**elemre. A "kulcsok" nézetben kattintson a "Létrehozás/Importálás" lehetőségre új kulcs létrehozásához. A kulcs egyedi nevét és egyéb beállításait a követelmények alapján használhatja. 

![Új kulcs létrehozása](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Hozzáférési szabályzat beállítása

Hozzon létre egy új hozzáférési szabályzatot, amely lehetővé teszi, hogy az ACI szolgáltatás hozzáférjen a kulcshoz.

* Miután létrehozta a kulcsot, a Key Vault erőforrás-paneljén a beállítások területen kattintson a **hozzáférési házirendek**elemre.
* A Key vaulthoz tartozó hozzáférési szabályzatok lapon kattintson a **hozzáférési házirend hozzáadása**lehetőségre.
* Állítsa be a kulcs *engedélyeit* a **beolvasás** és a **kicsomagolás kulcsának** megadása ![a kulcs engedélyeinek beállítása](./media/container-instances-encrypt-data/set-key-permissions.png)
* Válassza ki a *rendszerbiztonsági tag*lehetőséget, majd válassza az **Azure Container instance Service** elemet.
* Kattintson a **Hozzáadás** gombra a lap alján 

A hozzáférési szabályzatnak ekkor meg kell jelennie a Key Vault hozzáférési házirendjeiben.

![Új hozzáférési szabályzat](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>A JSON-telepítési sablon módosítása

> [!IMPORTANT]
> A központi telepítési adatai ügyfél által felügyelt kulccsal történő titkosítása a legújabb API-verzióban (2019-12-01) érhető el, amely jelenleg ki van vezetve. Adja meg ezt az API-verziót a telepítési sablonban. Ha problémája merül fel, forduljon az Azure ügyfélszolgálatához.

A Key Vault-kulcs és a hozzáférési házirend beállítása után adja hozzá a következő tulajdonságot az ACI telepítési sablonhoz. További információ az ACI-erőforrások üzembe helyezéséről az oktatóanyagban található sablonnal [: többtárolós csoport üzembe helyezése Resource Manager-sablonnal](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Konkrétan a telepítési sablon Container Group (tároló csoport) tulajdonságai szakaszában adjon hozzá egy "encryptionProperties" értéket, amely a következő értékeket tartalmazza:
* vaultBaseUrl: a Key Vault DNS-neve a Key Vault-erőforrás áttekintés paneljén található a portálon
* Kulcsnév: a korábban generált kulcs neve
* főverzió: a kulcs jelenlegi verziója. Ez úgy érhető el, ha magára a kulcsra kattint (a Key Vault-erőforrás beállítások szakaszában a "kulcsok" alatt)


```json
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "containers": {
                [...]
            }
        }
    }
]
```

### <a name="deploy-your-resources"></a>Az erőforrások üzembe helyezése

Ha létrehozta és szerkesztette a sablonfájlt az asztalon, feltöltheti azt a Cloud Shell könyvtárba a fájl húzásával. 

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a sablont az az [Group Deployment Create][az-group-deployment-create] paranccsal.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát. Miután az üzembe helyezés befejeződött, az ACI szolgáltatás által megőrzött összes adat titkosítva lesz a megadott kulccsal.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create