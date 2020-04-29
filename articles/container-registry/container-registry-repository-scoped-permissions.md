---
title: Engedélyek a Azure Container Registry adattárakhoz
description: Hozzon létre egy jogkivonatot, amely a beállításjegyzék adott tárházára vonatkozik a képek lekéréséhez vagy leküldéséhez, illetve egyéb műveletek végrehajtásához.
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 9004c45401833d3070266055dd7eb99a2bb43bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618833"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Token létrehozása adattárral hatókörrel rendelkező engedélyekkel

Ez a cikk azt ismerteti, hogyan hozhatók létre tokenek és hatókör-leképezések az adattárra vonatkozó hatókörrel rendelkező engedélyek kezeléséhez a tároló-beállításjegyzékben. A jogkivonatok létrehozásával a beállításjegyzék tulajdonosa a felhasználók vagy szolgáltatások hatókörön belüli, időkorlátos hozzáférését biztosíthatja a Tárházak számára a képek lekéréséhez vagy leküldéséhez, illetve egyéb műveletek végrehajtásához. A tokenek részletesebb engedélyeket biztosítanak, mint a beállításjegyzék más [hitelesítési beállításai](container-registry-authentication.md), amelyek hatóköre a teljes beállításjegyzékre érvényes. 

Tokenek létrehozásához a következő forgatókönyvek tartoznak:

* IoT-eszközök egyéni jogkivonatokkal való lekérésének engedélyezése egy adattárból
* Adjon meg egy olyan külső szervezetet, amely rendelkezik engedélyekkel egy adott tárházhoz 
* Korlátozza a tárház hozzáférését a szervezet különböző felhasználói csoportjaihoz. Adjon meg például írási és olvasási hozzáférést azoknak a fejlesztőknek, akik adott adattárakra irányuló képeket készítenek, és olvasási hozzáférést biztosítanak az ezekből a tárházból üzembe helyezett csapatokhoz.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Ez a funkció csak a **Premium** Container registryben érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry SKU](container-registry-skus.md)-i.
* Jelenleg nem rendelhet hozzá tárház hatókörű engedélyeket egy Azure Active Directory identitáshoz, például egy egyszerű szolgáltatásnév vagy egy felügyelt identitáshoz.
* Nem hozhat létre hatókör-leképezést egy olyan beállításjegyzékben, amelyen engedélyezve van a [Névtelen lekéréses hozzáférés](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Alapelvek

A tárház hatókörű engedélyeinek konfigurálásához hozzon létre egy *jogkivonatot* egy társított *hatókör-térképpel*. 

* A generált jelszóval ellátott **tokenek** lehetővé teszi, hogy a felhasználó hitelesítse magát a beállításjegyzékben. Megadhat egy jogkivonat-jelszó lejárati dátumát, vagy bármikor letilthatja a tokent.  

  A jogkivonat használatával végzett hitelesítés után a felhasználó vagy a szolgáltatás egy *vagy több tárházra hatókört* is végrehajthat.

  |Műveletek  |Leírás  | Példa |
  |---------|---------|--------|
  |`content/delete`    | Adatok eltávolítása az adattárból  | Adattár vagy jegyzékfájl törlése |
  |`content/read`     |  Adatok beolvasása az adattárból |  Összetevő lekérése |
  |`content/write`     |  Adatgyűjtés az adattárba     | A és `content/read` a használata az összetevők leküldéséhez |
  |`metadata/read`    | Metaadatok olvasása a tárházból   | Címkék vagy jegyzékfájlok listázása |
  |`metadata/write`     |  Metaadatok írása a tárházba  | Olvasási, írási és törlési műveletek engedélyezése vagy letiltása |

* A **hatókör-hozzárendelési** csoportok a jogkivonatra alkalmazott adattár-engedélyeket, és más jogkivonatokra is vonatkozhatnak. Minden jogkivonat társítva van egyetlen hatókör-leképezéssel. 

   Hatókör-hozzárendeléssel:

    * Több, azonos engedélyekkel rendelkező token konfigurálása Tárházak számára
    * Jogkivonat-engedélyek frissítése a hatókör-leképezésben szereplő adattár-műveletek hozzáadásakor vagy eltávolításakor, vagy egy másik hatókör-hozzárendelés alkalmazása 

  A Azure Container Registry számos, a rendszeren definiált hatókör-leképezést is biztosít, amelyek a rögzített engedélyekkel rendelkeznek az összes tárházban.

Az alábbi képen a tokenek és a hatóköri térképek közötti kapcsolat látható. 

![Beállításjegyzék-tokenek és hatókör-leképezések](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Előfeltételek

* **Azure CLI** – a tokenek létrehozásához és kezeléséhez használható Azure CLI-parancsok az Azure CLI 2.0.76 vagy újabb verzióiban érhetők el. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).
* **Docker** – a rendszerképek lekéréséhez vagy leküldéséhez szükséges beállításjegyzékbeli hitelesítéshez helyi Docker-telepítésre van szükség. A Docker a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszerhez biztosít telepítési utasításokat.
* **Container Registry** – ha még nem rendelkezik ilyennel, hozzon létre egy prémium szintű tároló-beállításjegyzéket az Azure-előfizetésében, vagy frissítsen egy meglévőt. Használja például a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)-t. 

## <a name="create-token---cli"></a>Jogkivonat létrehozása – parancssori felület

### <a name="create-token-and-specify-repositories"></a>Jogkivonat létrehozása és adattárak meghatározása

Hozzon létre egy jogkivonatot az az [ACR token Create][az-acr-token-create] parancs használatával. Token létrehozásakor megadhat egy vagy több tárházat és a hozzájuk társított műveleteket az egyes adattárakban. A Tárházak még nem szükségesek a beállításjegyzékben. Ha egy meglévő hatókör-hozzárendelés megadásával szeretne tokent létrehozni, tekintse meg a következő szakaszt.

A következő példa egy jogkivonatot hoz létre a beállításjegyzékben a *myregistry* a következő `samples/hello-world` engedélyekkel `content/write` : `content/read`és. Alapértelmezés szerint a parancs az alapértelmezett jogkivonat-állapotot állítja `enabled`be, de bármikor frissítheti az állapotot `disabled` .

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

A kimenet a token részleteit jeleníti meg, beleértve a két létrehozott jelszót is. Javasoljuk, hogy a jelszavakat biztonságos helyen mentse, hogy később is használhassa a hitelesítést. A jelszavakat nem lehet újból beolvasni, de újakat lehet létrehozni.

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

A kimenet tartalmazza a parancs által létrehozott hatókör-hozzárendelés részleteit. A (z) nevű `MyToken-scope-map`hatókör-leképezést használhatja arra, hogy ugyanazokat a tárház-műveleteket alkalmazza más jogkivonatokra. Vagy frissítse a hatókör-leképezést később, hogy megváltoztassa a társított jogkivonatok engedélyeit.

### <a name="create-token-and-specify-scope-map"></a>Jogkivonat létrehozása és a hatókör-hozzárendelés meghatározása

A jogkivonatok létrehozásának másik módja egy meglévő hatókör-hozzárendelés megadására szolgál. Ha még nem rendelkezik hatókör-leképezéssel, először hozzon létre egyet a Tárházak és a társított műveletek megadásával. Ezután adja meg a hatókör-leképezést jogkivonat létrehozásakor. 

Hatókör-hozzárendelés létrehozásához használja az az [ACR scope-Map Create][az-acr-scope-map-create] parancsot. A következő parancs egy hatókör-hozzárendelést hoz létre ugyanazzal az engedélyekkel a korábban használt `samples/hello-world` adattárhoz. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Az [az ACR token Create][az-acr-token-create] paranccsal hozzon létre egy jogkivonatot, és adja meg a *MyScopeMap* hatókör-leképezését. Ahogy az előző példában is látható, a parancs az alapértelmezett jogkivonat- `enabled`állapotot állítja be értékre.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

A kimenet a token részleteit jeleníti meg, beleértve a két létrehozott jelszót is. Javasoljuk, hogy a jelszavakat biztonságos helyen mentse, hogy később is használhassa a hitelesítést. A jelszavakat nem lehet újból beolvasni, de újakat lehet létrehozni.

## <a name="create-token---portal"></a>Jogkivonat létrehozása – portál

A Azure Portal a tokenek és a hatókör-leképezések létrehozására használható. A `az acr token create` CLI-parancshoz hasonlóan alkalmazhat meglévő hatókör-leképezést is, vagy létrehozhat egy hatókör-hozzárendelést, ha tokent hoz létre egy vagy több tárház és társított művelet megadásával. A Tárházak még nem szükségesek a beállításjegyzékben. 

A következő példa létrehoz egy jogkivonatot, és létrehoz egy hatókör-leképezést a következő `samples/hello-world` engedélyekkel `content/write` az `content/read`adattárhoz: és.

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **szolgáltatások**területen válassza a **tokenek (előzetes verzió) > + Hozzáadás**lehetőséget.
  ![Token létrehozása a portálon](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Adja meg a jogkivonat nevét.
1. A **hatókör-hozzárendelés**területen válassza az **új létrehozása**lehetőséget.
1. A hatókör-leképezés konfigurálása:
    1. Adja meg a hatókör-hozzárendelés nevét és leírását. 
    1. A **Tárházak**területen adja `samples/hello-world`meg a és az **engedélyek**területen `content/read` a `content/write`és a elemet. Ezután válassza a **+ Hozzáadás**lehetőséget.  
    ![Hatókör-hozzárendelés létrehozása a portálon](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. A Tárházak és engedélyek hozzáadása után válassza a **Hozzáadás** lehetőséget a hatókör-hozzárendelés hozzáadásához.
1. Fogadja el az **engedélyezett** alapértelmezett jogkivonat- **állapotot** , majd válassza a **Létrehozás**lehetőséget.

A jogkivonat ellenőrzése és létrehozása után a jogkivonat részletei megjelennek a **jogkivonatok képernyőjén** .

### <a name="add-token-password"></a>Jogkivonat-jelszó hozzáadása

Hozzon létre egy jelszót a jogkivonat létrehozása után. A beállításjegyzékben való hitelesítéshez engedélyezni kell a tokent, és érvényes jelszót kell tartalmaznia.

Létrehozhat egy vagy két jelszót, és beállíthat egy lejárati dátumot. 

1. A portálon navigáljon a tároló-beállításjegyzékhez.
1. A **szolgáltatások**területen válassza a **tokenek (előzetes verzió)** lehetőséget, és válasszon ki egy jogkivonatot.
1. A jogkivonat részletei között válassza a **jelszó1** vagy a **password2**lehetőséget, majd válassza a létrehozás ikont.
1. A jelszó képernyőn opcionálisan beállíthat egy lejárati dátumot a jelszóhoz, és válassza a **Létrehozás**lehetőséget.
1. A jelszó létrehozása után másolja és mentse egy biztonságos helyre. A képernyő bezárása után nem kérhető le generált jelszó, de létrehozhat egy újat.

    ![Jogkivonat-jelszó létrehozása a portálon](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Hitelesítés jogkivonat használatával

Ha egy felhasználó vagy szolgáltatás jogkivonatot használ a célként megadott beállításjegyzékben való hitelesítéshez, a jogkivonat nevét felhasználóneveként és annak egy generált jelszavával adja meg. A hitelesítési módszer a tokenhez társított konfigurált művelettől vagy művelettől függ.

|Műveletek  |Hitelesítés  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`Az Azure CLI-ben |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`Az Azure CLI-ben  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`Az Azure CLI-ben     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`Az Azure CLI-ben   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`Az Azure CLI-ben |

## <a name="examples-use-token"></a>Példák: token használata

Az alábbi példák a cikkben korábban létrehozott jogkivonatot használják a gyakori műveletek végrehajtásához egy adattáron: leküldéses és lekéréses lemezképek, lemezképek törlése és a lista tára címkék. A token kezdetben a leküldéses engedélyekkel (`content/write` és `content/read` műveletekkel) lett `samples/hello-world` beállítva a tárházban.

### <a name="pull-and-tag-test-images"></a>Tesztelési lemezképek lekérése és címkézése

Az alábbi példákban lekérheti `hello-world` a `alpine` és a lemezképeket a Docker hub-ból, és címkézheti azokat a beállításjegyzékben és a tárházban.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Hitelesítés token használatával

Futtassa `docker login` a parancsot a beállításjegyzékben való hitelesítéshez, adja meg a jogkivonat nevét felhasználónévként, és adja meg az egyik jelszavát. A tokennek rendelkeznie kell `Enabled` az állapottal.

A következő példa a bash-rendszerhéjhoz van formázva, és környezeti változók használatával biztosítja az értékeket.

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

A sikeres bejelentkezést követően próbálja meg leküldeni a címkézett képeket a beállításjegyzékbe. Mivel a jogkivonat jogosult a lemezképek `samples/hello-world` adattárba való leküldésére, a következő leküldéses művelet sikeres:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

A jogkivonat nem rendelkezik jogosultsággal a `samples/alpine` tárházhoz, ezért a következő leküldéses kísérlet egy hasonló hibával meghiúsul `requested access to the resource is denied`:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Leküldéses/lekérési engedélyek módosítása

Egy jogkivonat engedélyeinek frissítéséhez frissítse az engedélyeket a társított hatókör-térképen. A frissített hatókör-leképezést a rendszer azonnal alkalmazza az összes társított jogkivonatra. 

`MyToken-scope-map` Például frissítsen a `content/write` és `content/read` a műveletekkel az `samples/alpine` adattáron, és távolítsa el a `samples/hello-world` `content/write` műveletet az adattáron.  

Az Azure CLI használatához futtassa az az [ACR scope-Map Update][az-acr-scope-map-update] parancsot a hatókör-hozzárendelés frissítéséhez:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

Az Azure Portalon:

1. Navigáljon a tároló-beállításjegyzékhez.
1. A **szolgáltatások**területen válassza a **hatókör-térképek (előzetes verzió)** lehetőséget, majd válassza ki a frissítendő hatókör-leképezést.
1. A **Tárházak**területen adja `samples/alpine`meg a és az **engedélyek**területen `content/read` a `content/write`és a elemet. Ezután válassza a **+ Hozzáadás**lehetőséget.
1. A **Tárházak**területen válassza `samples/hello-world` az **engedélyek**, majd a kijelölés `content/write`elemet. Ezután válassza a **Save** (Mentés) lehetőséget.

A hatókör-hozzárendelés frissítése után a következő leküldése sikeres:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Mivel a hatókör-hozzárendelés csak az `content/read` `samples/hello-world` adattárhoz rendelkezik engedéllyel, a tárház leküldéses `samples/hello-world` kísérlete most meghiúsul:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

A lemezképek mindkét adattárakból való kihúzása sikeres, `content/read` mert a hatókör-hozzárendelés mindkét adattárhoz biztosít engedélyeket:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Rendszerképek törlése

Frissítse a hatókör-leképezést úgy `content/delete` , hogy hozzáadja `alpine` a műveletet a tárházhoz. Ez a művelet lehetővé teszi a lemezképek törlését a tárházban, vagy a teljes tárház törlését.

Rövid ideig csak az [az ACR scope-Map Update][az-acr-scope-map-update] parancs jelenik meg a hatókör-hozzárendelés frissítéséhez:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

A hatókör-leképezés a portál használatával történő frissítéséhez tekintse meg az előző szakaszt.

A `samples/alpine` tárház törléséhez használja a következő az [ACR repository delete][az-acr-repository-delete] parancsot. Képek vagy adattárak törléséhez a jogkivonat nem végzi el a hitelesítést `docker login`. Ehelyett adja át a token nevét és jelszavát a parancsnak. A következő példa a cikkben korábban létrehozott környezeti változókat használja:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Tárház-címkék megjelenítése 

Frissítse a hatókör-leképezést úgy `metadata/read` , hogy hozzáadja `hello-world` a műveletet a tárházhoz. Ez a művelet lehetővé teszi a jegyzékfájlok és a címkézett adattárban lévő információk olvasását.

Rövid ideig csak az [az ACR scope-Map Update][az-acr-scope-map-update] parancs jelenik meg a hatókör-hozzárendelés frissítéséhez:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

A hatókör-leképezés a portál használatával történő frissítéséhez tekintse meg az előző szakaszt.

A `samples/hello-world` tárház metaadatainak olvasásához futtassa az az [ACR repository show-Manifests][az-acr-repository-show-manifests] vagy [az ACR repository show-Tags][az-acr-repository-show-tags] parancsot. 

A metaadatok olvasásához a jogkivonat nem végzi el a hitelesítést `docker login`. Ehelyett adja át a token nevét és jelszavát bármelyik parancsnak. A következő példa a cikkben korábban létrehozott környezeti változókat használja:

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
## <a name="manage-tokens-and-scope-maps"></a>Jogkivonatok és hatóköri leképezések kezelése

### <a name="list-scope-maps"></a>Lista hatókörének leképezése

Használja az az [ACR scope-Térkép List][az-acr-scope-map-list] parancsot, vagy a portál **hatókör-térképek (előzetes verzió)** képernyőjét a beállításjegyzékben konfigurált összes hatókör-leképezés listázásához. Például:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

A kimenet megjeleníti a megadott hatókör-leképezéseket, és több, a rendszeren definiált hatókör-leképezést is konfigurálhat a tokenek konfigurálásához:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Jogkivonat részleteinek megjelenítése

Ha meg szeretné tekinteni a token részleteit, például az állapotát és a jelszó lejárati dátumát, futtassa az az [ACR token show][az-acr-token-show] parancsot, vagy válassza ki a tokent a portál **tokenek (előzetes verzió)** képernyőjén. Például:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

A portálon az az [ACR token List][az-acr-token-list] parancs vagy a **tokens (előnézet)** képernyő használatával listázhatja a beállításjegyzékben konfigurált összes jogkivonatot. Például:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Jelszavak előállítása jogkivonat számára

Ha nem rendelkezik jogkivonat-jelszóval, vagy új jelszavakat szeretne előállítani, futtassa az az [ACR token hitelesítő adatok létrehozása][az-acr-token-credential-generate] parancsot. 

Az alábbi példa új értéket hoz létre a *MyToken* -token jelszó1, amelynek lejárati időtartama 30 nap. A jelszót a környezeti változóban `TOKEN_PWD`tárolja. Ez a példa a bash-rendszerhéjhoz van formázva.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Ha a Azure Portalt szeretné használni a jogkivonat jelszavának létrehozásához, tekintse meg a jelen cikk a [Jogkivonat létrehozása – portál](#create-token---portal) című szakaszának lépéseit.

### <a name="update-token-with-new-scope-map"></a>Jogkivonat frissítése új hatókör-hozzárendeléssel

Ha másik hatókör-leképezéssel rendelkező tokent szeretne frissíteni, futtassa az [az ACR token Update][az-acr-token-update] parancsot, és adja meg az új hatókör-leképezést. Például:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

A portálon a **tokenek (előzetes verzió)** képernyőn válassza ki a tokent, és a **hatókör térképe**területen válasszon egy másik hatókör-leképezést.

> [!TIP]
> Miután frissített egy jogkivonatot egy új hatókör-térképpel, új jogkivonat-jelszavakat szeretne előállítani. Használja az az [ACR token regenerált][az-acr-token-credential-generate] parancsot, vagy a Azure Portalban regenerált jogkivonat-jelszót.

## <a name="disable-or-delete-token"></a>Jogkivonat letiltása vagy törlése

Előfordulhat, hogy átmenetileg le kell tiltania a token hitelesítő adatainak használatát egy adott felhasználóhoz vagy szolgáltatáshoz. 

Az Azure CLI használatával az az [ACR token Update][az-acr-token-update] paranccsal állíthatja be a `status` következőt: `disabled`

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

A portálon válassza ki a jogkivonatot a **tokenek (előzetes verzió)** képernyőn, és válassza a **Letiltva** **állapotot**.

Ha törölni szeretne egy jogkivonatot, hogy a hitelesítő adatai alapján bárki véglegesen érvénytelenítse a hozzáférést, futtassa az az [ACR token delete][az-acr-token-delete] parancsot. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

A portálon válassza ki a tokent a **jogkivonatok (előzetes verzió)** képernyőn, és válassza az **Elvetés**lehetőséget.

## <a name="next-steps"></a>További lépések

* A hatóköri térképek és tokenek kezeléséhez használja az az [ACR scope-Map][az-acr-scope-map] és [az ACR token][az-acr-token] Command groups további parancsait.
* Az Azure Container Registry szolgáltatással történő hitelesítéssel kapcsolatos további beállításokért tekintse meg a [hitelesítés áttekintését](container-registry-authentication.md) , beleértve a Azure Active Directory identitást, egy szolgáltatásnevet vagy egy rendszergazdai fiókot.


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
