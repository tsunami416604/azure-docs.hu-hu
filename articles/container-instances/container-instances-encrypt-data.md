---
title: Üzembehelyezési adatok titkosítása
description: Információ a tárolópéldány-erőforrások on-mailben megőrzött adatok titkosításáról és az adatok ügyfél által felügyelt kulccsal való titkosításáról
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080360"
---
# <a name="encrypt-deployment-data"></a>Üzembehelyezési adatok titkosítása

Az Azure Container Instances (ACI) erőforrások felhőben futtatásakor az ACI-szolgáltatás gyűjti és megőrzi a tárolókhoz kapcsolódó adatokat. Az ACI automatikusan titkosítja ezeket az adatokat, ha azok megmaradnak a felhőben. Ez a titkosítás védi az adatokat, hogy segítsen teljesíteni a szervezet biztonsági és megfelelőségi kötelezettségvállalásait. Az ACI azt is lehetővé teszi, hogy ezeket az adatokat saját kulccsal titkosítsa, így nagyobb ellenőrzést biztosít az ACI-telepítésekkel kapcsolatos adatok felett.

## <a name="about-aci-data-encryption"></a>Az ACI adattitkosítása 

Az ACI-ban lévő adatok at 256 bites AES titkosítással titkosítják és visszafejtik. Minden ACI-telepítéshez engedélyezve van, és nem kell módosítania a központi telepítést vagy a tárolókat a titkosítás előnyeinek kihasználásához. Ez magában foglalja a központi telepítésmetaadatait, a környezeti változókat, a tárolókba átadott kulcsokat és a tárolók leállítása után megőrzött naplókat, így továbbra is láthatja őket. A titkosítás nincs hatással a tárolócsoport teljesítményére, és nincs további titkosítási költség.

## <a name="encryption-key-management"></a>Titkosítási kulcs kezelése

A tárolóadatok titkosításához a Microsoft által felügyelt kulcsokra támaszkodhat, vagy kezelheti a titkosítást a saját kulcsaival. Az alábbi táblázat a következő beállításokat hasonlítja össze: 

|    |    Microsoft által felügyelt kulcsok     |     Felhasználó által kezelt kulcsok     |
|----|----|----|
|    Titkosítási/visszafejtési műveletek    |    Azure    |    Azure    |
|    Kulcstárolás    |    Microsoft kulcstároló    |    Azure Key Vault    |
|    A legfontosabb rotációs felelősség    |    Microsoft    |    Ügyfél    |
|    Kulcshozzáférés    |    Csak Microsoft    |    Microsoft, Ügyfél    |

A dokumentum további része az ACI központi telepítési adatainak kulcs (ügyfél által felügyelt kulcs) titkosításához szükséges lépéseket ismerteti. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Adatok titkosítása ügyfél által kezelt kulccsal

### <a name="create-service-principal-for-aci"></a>Egyszerű szolgáltatás létrehozása az ACI-hoz

Az első lépés annak biztosítása, hogy az [Azure-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) rendelkezik egy egyszerű szolgáltatás az Azure Container Instances szolgáltatás engedélyek megadásához rendelt. 

> [!IMPORTANT]
> A következő parancs futtatásához és egy egyszerű szolgáltatás sikeres létrehozásához győződjön meg arról, hogy rendelkezik-e a bérlőben szolgáltatásnév létrehozásához szükséges engedélyekkel.
>

A következő CLI parancs az ACI SP-t állítja be az Azure-környezetben:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

A parancs futtatásának kimenete egy egyszerű szolgáltatást jelenít meg, amely a "displayName" beállítással van beállítva: "Azure Container Instance Service".

Abban az esetben, ha nem tudja sikeresen létrehozni a szolgáltatásnév:
* erősítse meg, hogy rendelkezik-e erre vonatkozó engedélyekkel a bérlőben
* ellenőrizze, hogy létezik-e már egyszerű szolgáltatás a bérlőben az ACI-ra való üzembe helyezéshez. Ezt úgy teheti `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` meg, hogy futtatja és használja azt a szolgáltatásnév

### <a name="create-a-key-vault-resource"></a>Key Vault-erőforrás létrehozása

Hozzon létre egy Azure Key Vault az [Azure Portalon,](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) [CLI,](https://docs.microsoft.com/azure/key-vault/quick-create-cli)vagy [PowerShell.](https://docs.microsoft.com/azure/key-vault/quick-create-powershell) 

A kulcstartó tulajdonságaihoz kövesse az alábbi irányelveket: 
* Név: Egy egyedi nevet kell megadnia. 
* Előfizetés: Válassza ki az előfizetést.
* Az Erőforráscsoport csoportban válasszon egy meglévő erőforráscsoportot, vagy hozzon létre újat, és adjon meg egy erőforráscsoport nevét.
* A Hely legördülő menüből válassza ki a helyet.
* A többi beállítást az alapértelmezett beállításokra hagyhatja, vagy további követelmények alapján választhat.

> [!IMPORTANT]
> Ha az Ügyfél által felügyelt kulcsok at titkosítja az ACI központi telepítési sablon, ajánlott, hogy a következő két tulajdonság van beállítva a key vault, Ideiglenes törlés és A Ne ürítés. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de egy új vagy meglévő kulcstartón a PowerShell vagy az Azure CLI használatával engedélyezhetők.

### <a name="generate-a-new-key"></a>Új kulcs létrehozása 

A kulcstartó létrehozása után keresse meg az erőforrást az Azure Portalon. Az erőforráspanel bal oldali navigációs menüjének Beállítások csoportban kattintson a **Kulcsok gombra.** A "Kulcsok" nézetben kattintson a "Létrehozás/importálás" gombra az új kulcs létrehozásához. A kulcshoz bármilyen egyedi nevet és egyéb beállításokat használhat az Ön igényei alapján. 

![Új kulcs létrehozása](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Hozzáférési házirend beállítása

Hozzon létre egy új hozzáférési szabályzatot, amely lehetővé teszi az ACI-szolgáltatás számára a kulcs elérését.

* A kulcs létrehozása után a kulcstároló erőforráspaneljének Beállítások területén kattintson az **Access Policies**elemre.
* A kulcstartó "Hozzáférési házirendek" lapján kattintson a **Hozzáférési házirend hozzáadása gombra.**
* A *kulcsengedélyek* beállítása a **Kulcskészlet bekerülési** és **kicsomagolása** ![kulcsengedélyekre](./media/container-instances-encrypt-data/set-key-permissions.png)
* Az *Egyszerű kijelölés e*esetben válassza az Azure Container Instance Service **lehetőséget.**
* Kattintson alul a **Hozzáadás** gombra. 

A hozzáférési szabályzat most meg kell jelennie a kulcstartó hozzáférési szabályzatok.

![Új hozzáférési szabályzat](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>A JSON-telepítési sablon módosítása

> [!IMPORTANT]
> A központi telepítési adatok ügyfél által felügyelt kulccsal történő titkosítása a jelenleg bevezetésre kerülő legújabb API-verzióban (2019-12-01) érhető el. Adja meg ezt az API-verziót a központi telepítési sablonban. Ha bármilyen problémája van ezzel, kérjük, forduljon az Azure-támogatáshoz.

Miután beállította a key vault-kulcsot és a hozzáférési szabályzatot, adja hozzá a következő tulajdonságokat az ACI központi telepítési sablonjához. További információ az ACI-erőforrások sablonnal történő központi telepítéséről az [oktatóanyagban: Többtárolós csoport telepítése Erőforrás-kezelő sablon használatával.](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group) 
* A `resources`területen `apiVersion` `2019-12-01`a beállítás a.
* A telepítési sablon tárolócsoport tulajdonságai szakasza `encryptionProperties`alatt adjon hozzá egy t, amely a következő értékeket tartalmazza:
  * `vaultBaseUrl`: a kulcstartó DNS-neve a Portal key vault erőforrásának áttekintő paneljén található
  * `keyName`: a korábban létrehozott kulcs neve
  * `keyVersion`: a kulcs aktuális verziója. Ez megtalálható kattintva a kulcs maga (a "Kulcsok" a Beállítások szakaszban a key vault erőforrás)
* A tárolócsoport tulajdonságai alatt `sku` adjon `Standard`hozzá egy értékű tulajdonságot. A `sku` tulajdonság szükséges az API 2019-12-01-es verziójában.

A következő sablonkódrészlet a telepítési adatok titkosításához a következő további tulajdonságokat jeleníti meg:

```json
[...]
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
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

A következőkben egy teljes sablon t, amely az oktatóanyag sablonjából lett kiigazítva: [Többtárolós csoport telepítése Erőforrás-kezelő sablon használatával.](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group) 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
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
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Az erőforrások üzembe helyezése

Ha létrehozta és szerkesztette a sablonfájlt az asztalon, feltöltheti azt a Cloud Shell könyvtárába, ha a fájlt belehúzza. 

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a sablont az [az csoport telepítési létrehozási][az-group-deployment-create] parancsával.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát. A központi telepítés befejezése után az ACI-szolgáltatás által megőrzött összes hozzá kapcsolódó adat a megadott kulccsal lesz titkosítva.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
