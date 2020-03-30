---
title: Az Azure Container Registry adattárakra vonatkozó engedélyek
description: A rendszerleíró adatbázis adott tárolóihoz tartozó engedélyekkel rendelkező jogkivonat létrehozása lemezképek lekérése vagy leküldése, illetve egyéb műveletek végrehajtása érdekében
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444295"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Token létrehozása tárház hatókörrel rendelkező engedélyekkel

Ez a cikk ismerteti, hogyan hozhat létre jogkivonatokat és hatókörleképezéseket a tároló beállításjegyzékében lévő tárház hatókörrel rendelkező engedélyek kezeléséhez. Jogkivonatok létrehozásával a beállításjegyzék tulajdonosa a felhasználók vagy szolgáltatások hatókörrel rendelkező, időben korlátozott hozzáférést biztosíthatnak az adattárakhoz a lemezképek lekérése vagy leküldése, illetve más műveletek végrehajtása érdekében. A jogkivonat részletesebb engedélyeket biztosít, mint a rendszerleíró adatbázis egyéb [hitelesítési beállításai,](container-registry-authentication.md)amelyek a teljes beállításjegyzékengedélyeit hatókörbe tartozó engedélyeket. 

A jogkivonat létrehozásának forgatókönyvei a következők:

* Az egyes jogkivonatokkal rendelkező IoT-eszközök lekérik a lemezképet egy tárházból
* Egy adott tárházhoz engedéllyel rendelkező külső szervezet biztosítása 
* Korlátozza a tárház-hozzáférést a szervezet különböző felhasználói csoportjaihoz. Például írási és olvasási hozzáférést biztosíthat azoknak a fejlesztőknek, akik adott tárolókat célzó lemezképeket hoznak létre, és olvasási hozzáférést biztosítnak az ilyen tárolókból üzembe helyezett csapatokhoz.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek.](#preview-limitations) Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előnézeti korlátozások

* Ez a funkció csak **prémium szintű** tárolók beállításjegyzékében érhető el. A beállításjegyzék-szolgáltatási szintekről és -korlátozásokról az [Azure Container Registry ska](container-registry-skus.md)című témakörben talál további információt.
* Jelenleg nem rendelhet hozzá tárházhatókör-engedélyeket egy Azure Active Directory-identitáshoz, például egy egyszerű szolgáltatáshoz vagy felügyelt identitáshoz.

## <a name="concepts"></a>Alapelvek

A tárház hatókörrel rendelkező engedélyek konfigurálásához hozzon létre egy *jogkivonatot* egy társított *hatókörleképezéssel.* 

* A **jogkivonat** és a létrehozott jelszó lehetővé teszi, hogy a felhasználó hitelesítse magát a beállításjegyzékkel. Beállíthatja a jogkivonat-jelszó lejárati dátumát, vagy bármikor letilthatja a jogkivonatot.  

  A jogkivonattal való hitelesítés után a felhasználó vagy a szolgáltatás egy vagy több adattárba tartozó *műveleteket* hajthat végre.

  |Műveletek  |Leírás  | Példa |
  |---------|---------|--------|
  |`content/delete`    | Adatok eltávolítása az adattárból  | Tárház vagy jegyzékfájl törlése |
  |`content/read`     |  Adatok olvasása az adattárból |  Egy műtárgy lekérése |
  |`content/write`     |  Adatok írása a tárházba     | Egy `content/read` műtárgy lelökése |
  |`metadata/read`    | Metaadatok olvasása a tárházból   | Címkék vagy jegyzékek listázása |
  |`metadata/write`     |  Metaadatok írása a tárházba  | Olvasási, írási és törlési műveletek engedélyezése vagy letiltása |

* A **hatókörleképezés** csoportosítja a tárház egy jogkivonatra alkalmazott engedélyeket, és újra alkalmazhatja más jogkivonatokra. Minden jogkivonat egyetlen hatókörtérképhez van társítva. 

   Hatókörtérképpel:

    * Több token konfigurálása azonos engedélyekkel egy adattárházkészlettel
    * Tokenengedélyek frissítése, ha tárházműveleteket ad hozzá vagy távolít el a hatókörtérképen, vagy más hatókörleképezést alkalmaz 

  Az Azure Container Registry számos rendszer által definiált hatókör-leképezést is biztosít, amelyek rögzített engedélyekkel rendelkeznek az összes adattárakban.

Az alábbi képen a jogkivonatok és a hatókörleképezések közötti kapcsolat látható. 

![Rendszerleíró tokenek és hatókörtérképek](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Előfeltételek

* **Azure CLI** – Az Azure CLI-parancsok a tokenek létrehozásához és kezeléséhez az Azure CLI 2.0.76-os vagy újabb verziójában érhetők el. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).
* **Docker** – a beállításjegyzékkel való hitelesítéshez lekell állítani vagy lekell adni a rendszerképeket, helyi Docker-telepítésre van szükség. A Docker a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszerhez biztosít telepítési utasításokat.
* **Tároló beállításjegyzék** – Ha nem rendelkezik, hozzon létre egy Prémium szintű tároló beállításjegyzékaz Azure-előfizetésben, vagy frissítsen egy meglévő beállításjegyzéket. Használja például az [Azure Portalt](container-registry-get-started-portal.md) vagy az [Azure CLI-t.](container-registry-get-started-azure-cli.md) 

## <a name="create-token---cli"></a>Token létrehozása - CLI

### <a name="create-token-and-specify-repositories"></a>Jogkivonat létrehozása és adattárak megadása

Hozzon létre egy tokent az [az acr token create][az-acr-token-create] paranccsal. Jogkivonat létrehozásakor megadhat egy vagy több adattárat és a kapcsolódó műveleteket az egyes tárházakon. Az adattáraknak még nem kell a rendszerleíró adatbázisban lenniük. Ha meglévő hatókörtérkép megadásával szeretne jogkivonatot létrehozni, tekintse meg a következő szakaszt.

A következő példa létrehoz egy jogkivonatot a *rendszerleíró* adatbázisban `content/write` `content/read`a következő engedélyekkel a `samples/hello-world` tárházban: és . Alapértelmezés szerint a parancs az alapértelmezett `enabled`jogkivonat állapotát a `disabled` értékre állítja, de az állapot bármikor frissíthető.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

A kimenet a jogkivonat részleteit jeleníti meg, köztük két generált jelszót. Javasoljuk, hogy mentse a jelszavakat egy biztonságos helyre, hogy később használható a hitelesítéshez. A jelszavak nem olvashatók be újra, de újak is létrehozhatók.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

A kimenet a parancs által létrehozott hatókörleképezés részleteit tartalmazza. A hatókörleképezés itt `MyToken-scope-map`nevezett használatával ugyanazokat a tárház-műveleteket alkalmazhatja más jogkivonatokra is. Vagy frissítse a hatókörleképezést később a társított jogkivonatok engedélyeinek módosításához.

### <a name="create-token-and-specify-scope-map"></a>Token létrehozása és hatókörtérkép megadása

Egy másik módja a jogkivonat létrehozásának, hogy adjon meg egy meglévő hatókörleképezést. Ha még nem rendelkezik hatókörtérképpel, először hozzon létre egyet az adattárak és a kapcsolódó műveletek megadásával. Ezután adja meg a hatókörleképezést egy jogkivonat létrehozásakor. 

Hatókörtérkép létrehozásához használja az [az acr scope-map create][az-acr-scope-map-create] parancsot. A következő parancs létrehoz egy hatókörleképezést a korábban használt `samples/hello-world` tárházban azonos engedélyekkel. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Futtassa [az acr token létrehozása][az-acr-token-create] egy jogkivonat létrehozásához, megadva a *MyScopeMap* hatókör leképezés. Az előző példához, a parancs az `enabled`alapértelmezett jogkivonat állapotát állítja .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

A kimenet a jogkivonat részleteit jeleníti meg, köztük két generált jelszót. Javasoljuk, hogy mentse a jelszavakat egy biztonságos helyre, hogy később használható a hitelesítéshez. A jelszavak nem olvashatók be újra, de újak is létrehozhatók.

## <a name="create-token---portal"></a>Token létrehozása - portál

Az Azure Portal segítségével jogkivonatok és hatókör-leképezések létrehozásához. A `az acr token create` CLI parancshoz ugyanúgy alkalmazhat egy meglévő hatókörleképezést, vagy létrehozhat egy hatókörleképezést, amikor egy vagy több tárház és a kapcsolódó műveletek megadásával hoz létre jogkivonatot. Az adattáraknak még nem kell a rendszerleíró adatbázisban lenniük. 

A következő példa létrehoz egy jogkivonatot, és létrehoz `samples/hello-world` egy `content/write` hatókörtérképet a következő engedélyekkel a tárházban: és `content/read`.

1. A portálon keresse meg a tároló beállításjegyzékét.
1. A **Szolgáltatások csoportban**válassza **a Tokenek (előzetes verzió) lehetőséget > a +Add**lehetőséget.
  ![Token létrehozása a portálon](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Adjon meg egy token nevet.
1. A **Hatókör leképezés csoportban**válassza **az Új létrehozása lehetőséget.**
1. A hatókörtérkép konfigurálása:
    1. Adja meg a hatókörtérkép nevét és leírását. 
    1. Az **Adattárak**csoportban adja meg a be című `samples/hello-world`részt, és az **Engedélyek**csoportban válassza ki a és `content/read` `content/write`a lehetőséget. Ezután válassza a **+Hozzáadás lehetőséget.**  
    ![Hatókörtérkép létrehozása a portálon](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Az adattárak és engedélyek hozzáadása után válassza a **Hozzáadás** lehetőséget a hatókörtérkép hozzáadásához.
1. Fogadja el az **Engedélyezve** alapértelmezett **tokenállapotát,** majd válassza a **Létrehozás gombot.**

A jogkivonat érvényesítése és létrehozása után a jogkivonat részletei megjelennek a **Jogkivonatok** képernyőn.

### <a name="add-token-password"></a>Token jelszó hozzáadása

Hozzon létre egy jelszót, miután létrehozott egy jogkivonatot. A beállításjegyzékkel való hitelesítéshez a jogkivonatot engedélyezni kell, és érvényes jelszóval kell rendelkeznie.

Létrehozhat egy vagy két jelszót, és mindegyikhez beállíthat egy lejárati dátumot. 

1. A portálon keresse meg a tároló beállításjegyzékét.
1. A **Szolgáltatások csoportban**válassza a **Tokenek (előzetes verzió)** lehetőséget, és válasszon ki egy jogkivonatot.
1. A token részleteiben válassza a **password1** vagy **password2 elemet,** majd a Létrehozás ikont.
1. A jelszó képernyőn tetszés szerint adja meg a jelszó lejárati dátumát, és válassza a **Létrehozás gombot.**
1. A jelszó létrehozása után másolja és mentse biztonságos helyre. A képernyő bezárása után nem lehet letölteni a létrehozott jelszót, de létrehozhat egy újat.

    ![Tokenjelszó létrehozása a portálon](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Hitelesítés tokentel

Ha egy felhasználó vagy szolgáltatás jogkivonatot használ a célbeállításjegyzékkel való hitelesítéshez, a jogkivonat nevét felhasználónévként és annak egyik létrehozott jelszavát adja meg. A hitelesítési módszer a jogkivonathoz társított konfigurált művelettől vagy műveletektől függ.

|Műveletek  |Hogyan lehet hitelesíteni  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`az Azure CLI-ben |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`az Azure CLI-ben  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`az Azure CLI-ben     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`az Azure CLI-ben   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`az Azure CLI-ben |

## <a name="examples-use-token"></a>Példák: Token használata

A következő példák a cikkben korábban létrehozott jogkivonatot használják a tárház közös műveleteinek végrehajtásához: leküldéses és lekéréses képek, képek törlése és listatárcímkék. A jogkivonat kezdetben leküldéses`content/write` `content/read` engedélyekkel (és műveletekkel) lett beállítva a `samples/hello-world` tárházban.

### <a name="pull-and-tag-test-images"></a>Tesztképek lekérése és címkézése

Az alábbi példákat, `alpine` lekérése és a képek a `hello-world` Docker Hub, és címkézze meg őket a beállításjegyzék és a tárház.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Hitelesítés token használatával

Futtassa `docker login` a beállításjegyzékkel való hitelesítéshez, adja meg a jogkivonat nevét felhasználónévként, és adja meg az egyik jelszavát. A tokenállapotának `Enabled` állapotúnak kell lennie.

A következő példa a bash shell formátumban van formázva, és környezeti változók használatával biztosítja az értékeket.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A kimenetnek sikeres hitelesítést kell mutatnia:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Rendszerképek leküldése a regisztrációs adatbázisba

Sikeres bejelentkezés után próbálja meg leadni a címkézett képeket a rendszerleíró adatbázisba. Mivel a jogkivonat rendelkezik `samples/hello-world` a lemezképek adattárba való leküldése, a következő leküldéses sikeres:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

A jogkivonat nem rendelkezik `samples/alpine` a tártár engedélyével, így a `requested access to the resource is denied`következő leküldéses kísérlet a következőhöz hasonló hibával sikertelen:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Leküldéses/lekéréses engedélyek módosítása

A jogkivonat engedélyeinek frissítéséhez frissítse az engedélyeket a társított hatókörleképezésben. A frissített hatókörleképezés azonnal alkalmazza az összes társított jogkivonatot. 

Például frissítse `MyToken-scope-map` `content/write` na `content/read` és `samples/alpine` műveleteket a `content/write` tárházon, és távolítsa el a műveletet a `samples/hello-world` tárházban.  

Az Azure CLI használatához futtassa az [acr hatókör-térkép frissítést][az-acr-scope-map-update] a hatókörtérkép frissítéséhez:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

Az Azure Portalon:

1. Nyissa meg a tároló beállításjegyzékét.
1. A **Szolgáltatások csoportban**válassza **a Hatókörleképezések (előnézet)** lehetőséget, és válassza ki a frissíteni kívánt hatókörleképezést.
1. Az **Adattárak**csoportban adja meg a be című `samples/alpine`részt, és az **Engedélyek**csoportban válassza ki a és `content/read` `content/write`a lehetőséget. Ezután válassza a **+Hozzáadás lehetőséget.**
1. Az **Adattárak csoportban**jelölje `content/write`be az **Engedélyek**csoportban `samples/hello-world` a jelölőnégyzet jelölését. Ezután válassza a **Save** (Mentés) lehetőséget.

A hatókörtérkép frissítése után a következő leküldéses művelet sikeres:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Mivel a hatókörtérkép `content/read` csak a `samples/hello-world` tárház engedélyével rendelkezik, a `samples/hello-world` tárház leküldéses kísérlete most sikertelen:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Mindkét tárház képeinek lehúzása sikeres, `content/read` mivel a hatókörtérkép mindkét tárházhoz biztosít engedélyeket:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Rendszerképek törlése

Frissítse a hatókörleképezést `alpine` a művelet nek a `content/delete` tárházhoz való hozzáadásával. Ez a művelet lehetővé teszi a képek törlését a tárházban, vagy a teljes tárház törlését.

A rövidség, azt mutatják, csak az [acr hatókör-térkép frissítési][az-acr-scope-map-update] parancsot, hogy frissítse a hatókör térkép:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

A hatókörtérkép portál használatával történő frissítéséhez tekintse meg az előző szakaszt.

Használja a következő [az acr repository delete][az-acr-repository-delete] parancsot a `samples/alpine` tárház törléséhez. A képek vagy adattárak törléséhez a `docker login`token nem hitelesíti a t. Ehelyett adja át a jogkivonat nevét és jelszavát a parancsnak. A következő példa a cikk korábbi részén létrehozott környezeti változókat használja:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Tárpéha-címkék megjelenítése 

Frissítse a hatókörleképezést `hello-world` a művelet nek a `metadata/read` tárházhoz való hozzáadásával. Ez a művelet lehetővé teszi a jegyzékfájl- és címkeadatok olvasását a tárházban.

A rövidség, azt mutatják, csak az [acr hatókör-térkép frissítési][az-acr-scope-map-update] parancsot, hogy frissítse a hatókör térkép:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

A hatókörtérkép portál használatával történő frissítéséhez tekintse meg az előző szakaszt.

A tárházban lévő `samples/hello-world` metaadatok olvasásához futtassa az az [acr repository show-manifests][az-acr-repository-show-manifests] vagy az [acr repository show-tags][az-acr-repository-show-tags] parancsot. 

A metaadatok olvasásához a token `docker login`nem hitelesíti a t. Ehelyett adja át a jogkivonat nevét és jelszavát bármelyik parancsnak. A következő példa a cikk korábbi részén létrehozott környezeti változókat használja:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Példa a kimenetre:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>Tokenek és hatókörtérképek kezelése

### <a name="list-scope-maps"></a>Hatókörtérképek listázása

Használja az [az acr hatókör-térkép lista][az-acr-scope-map-list] parancs, vagy a **hatókör leképezések (Előzetes)** képernyő a portálon, a beállításjegyzékben konfigurált összes hatókörleképezések. Példa:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

A kimenet a definiált hatókör-leképezéseket és a jogkivonatok konfigurálásához használható, rendszer által definiált hatókör-leképezéseket jeleníti meg:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Token részleteinek megjelenítése

Egy jogkivonat részleteinek megtekintéséhez, például az állapotát és a jelszó lejárati dátumokat, futtassa az [az acr token show][az-acr-token-show] parancsot, vagy válassza ki a jogkivonatot a **jogkivonatok (előzetes verzió)** képernyőn a portálon. Példa:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Használja az [az acr token lista][az-acr-token-list] parancs, vagy a **jogkivonatok (előzetes verzió)** képernyő a portálon, a beállításjegyzékben konfigurált összes tokenek listázásához. Példa:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Jelszavak létrehozása a tokenhez

Ha nem rendelkezik jogkivonat-jelszóval, vagy új jelszavakat szeretne létrehozni, futtassa az [az acr token hitelesítő adatok generálása][az-acr-token-credential-generate] parancsot. 

A következő példa egy új értéket hoz létre a Jelszó1 a *MyToken* tokenhez, 30 napos lejárati idővel. Ez tárolja a jelszót `TOKEN_PWD`a környezeti változó . Ez a példa a bash shell formátumban van formázva.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Az Azure Portal segítségével jogkivonat-jelszó létrehozásához tekintse meg a [jogkivonat létrehozása - portál](#create-token---portal) korábbi ebben a cikkben leírt lépéseket.

### <a name="update-token-with-new-scope-map"></a>Token frissítése új hatókörtérképpel

Ha egy másik hatókörtérképpel rendelkező jogkivonatot szeretne frissíteni, futtassa [az acr token frissítést,][az-acr-token-update] és adja meg az új hatókörleképezést. Példa:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

A portálon a **jogkivonatok (előnézet)** képernyőn válassza ki a jogkivonatot, és a **Scope map**csoportban válasszon egy másik hatókörtérképet.

> [!TIP]
> Egy token frissítése után egy új hatókör térkép, érdemes lehet új jogkivonat jelszavakat. Használja az [az acr token hitelesítő adatok generálása][az-acr-token-credential-generate] parancsot, vagy újragenerálja a jogkivonat-jelszót az Azure Portalon.

## <a name="disable-or-delete-token"></a>Token letiltása vagy törlése

Előfordulhat, hogy ideiglenesen le kell tiltania a jogkivonat-hitelesítő adatok használatát egy felhasználó vagy szolgáltatás számára. 

Az Azure CLI használatával futtassa az [az acr token update][az-acr-token-update] parancsot a `status` következő beállításához: `disabled`

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

A portálon jelölje ki a jogkivonatot a **Jogkivonatok (Előzetes verzió)** képernyőn, és válassza **a Letiltva** lehetőséget az **Állapot csoportban.**

Ha törölni szeretne egy jogkivonatot, hogy véglegesen érvénytelenítse a hozzáférést bárki a hitelesítő adatait, futtassa az [az acr token delete][az-acr-token-delete] parancsot. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

A portálon jelölje ki a jogkivonatot a **Tokenek (Előzetes verzió)** képernyőn, és válassza az **Elvetés**lehetőséget.

## <a name="next-steps"></a>További lépések

* Hatókör-leképezések és -jogkivonatok kezeléséhez használjon további parancsokat az [az acr hatókör-leképezés][az-acr-scope-map] és [az acr token][az-acr-token] parancscsoportokban.
* Tekintse meg a [hitelesítési áttekintést](container-registry-authentication.md) az Azure-tároló beállításjegyzékkel való hitelesítéshez, beleértve az Azure Active Directory-identitás, az egyszerű szolgáltatás vagy a rendszergazdai fiók használatát.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
