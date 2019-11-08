---
title: Engedélyek a Azure Container Registry adattárakhoz
description: Hozzon létre egy jogkivonatot, amely a beállításjegyzékben meghatározott adattárakra vonatkozik a képek lekéréséhez vagy leküldéséhez.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2019
ms.author: danlep
ms.openlocfilehash: 7b9d220ac7e507513458eab6b55276b3aa434739
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742745"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Tárház – hatókörön belüli engedélyek Azure Container Registry 

A Azure Container Registry számos [hitelesítési lehetőséget](container-registry-authentication.md) támogat olyan identitások használatával, amelyek [szerepköralapú hozzáféréssel](container-registry-roles.md) rendelkeznek a teljes beállításjegyzékhez. Bizonyos esetekben azonban előfordulhat, hogy csak a beállításjegyzékben lévő egyes *adattárakhoz* kell hozzáférést biztosítania. 

Ez a cikk bemutatja, hogyan hozhat létre és használhat olyan hozzáférési jogkivonatot, amely jogosultsággal rendelkezik a beállításjegyzékben csak bizonyos adattárakban végzett műveletek végrehajtásához. Hozzáférési jogkivonattal a felhasználók vagy szolgáltatások hatókörön belüli, időkorlátos hozzáférést biztosítanak a Tárházak számára a képek lekéréséhez vagy leküldéséhez, illetve egyéb műveletek végrehajtásához. 

A jelen cikk későbbi, a jogkivonat-fogalmakkal és-forgatókönyvekkel kapcsolatos hátterével kapcsolatos tudnivalókat lásd a [tárház hatókörű engedélyeiről](#about-repository-scoped-permissions).

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Ez a funkció csak a **Premium** Container registryben érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry SKU](container-registry-skus.md)-i.
* Jelenleg nem rendelhet tárház-hatókörű engedélyeket egy Azure Active Directory objektumhoz, például egy egyszerű szolgáltatásnév vagy egy felügyelt identitáshoz.

## <a name="prerequisites"></a>Előfeltételek

* **Azure CLI** – ez a cikk az Azure CLI (2.0.76 vagy újabb verzió) helyi telepítését igényli. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).
* **Docker** – a beállításjegyzékben való hitelesítéshez szükség van egy helyi Docker-telepítésre is. A Docker a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszerhez biztosít telepítési utasításokat.
* **Tároló beállításjegyzéke adattárakkal** – ha nem rendelkezik ilyennel, hozzon létre egy tároló-beállításjegyzéket az Azure-előfizetésében. Használja például a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)-t. 

  Tesztelési célból [leküldheti](container-registry-get-started-docker-cli.md) vagy [importálhatja](container-registry-import-images.md) egy vagy több minta lemezképét a beállításjegyzékbe. A jelen cikkben szereplő példák a következő rendszerképekre mutatnak két tárházban: `samples/hello-world:v1` és `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Hozzáférési jogkivonat létrehozása

Hozzon létre egy jogkivonatot az az [ACR token Create][az-acr-token-create] parancs használatával. Token létrehozásakor meg kell adnia egy vagy több adattárat és kapcsolódó műveletet az egyes adattárokon, vagy egy meglévő hatókör-hozzárendelést is meg kell adnia ezekkel a beállításokkal.

### <a name="create-access-token-and-specify-repositories"></a>Hozzáférési jogkivonat létrehozása és adattárak meghatározása

Az alábbi példa egy hozzáférési jogkivonatot hoz létre, amely a `content/write` és `content/read` műveletek végrehajtásához szükséges engedélyekkel rendelkezik a `samples/hello-world` adattáron, valamint a `content/read` műveletet a `samples/nginx` adattáron. Alapértelmezés szerint a parancs két jelszót hoz létre. 

Ez a példa a jogkivonat állapotát `enabled` (az alapértelmezett beállítás) állítja be, de bármikor frissítheti a tokent, és beállíthatja az állapotot `disabled`re.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

A kimenet a token részleteit jeleníti meg, beleértve a generált jelszavakat és a hatókör-leképezést. Javasoljuk, hogy a jelszavakat biztonságos helyen mentse, hogy később a `docker login`használhassa. A jelszavakat nem lehet újból beolvasni, de újakat lehet létrehozni.

A kimenet azt is jelzi, hogy a rendszer automatikusan létrehoz egy hatókör-leképezést `MyToken-scope-map`. A hatókör-hozzárendelés használatával ugyanazon adattárbeli műveleteket is alkalmazhatja más jogkivonatokra. Vagy frissítse a hatókör-leképezést később, hogy megváltoztassa a jogkivonat-engedélyeket.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
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

### <a name="create-a-scope-map-and-associated-token"></a>Hatókör-hozzárendelés és társított jogkivonat létrehozása

Másik lehetőségként megadhatja a hatókör-hozzárendelést a Tárházak és a társított műveletek között a jogkivonat létrehozásakor. Hatókör-hozzárendelés létrehozásához használja az az [ACR scope-Map Create][az-acr-scope-map-create] parancsot.

A következő példában a parancs létrehoz egy hatókör-leképezést az előző példában használt engedélyekkel. Lehetővé teszi `content/write` és `content/read` műveleteket a `samples/hello-world` adattáron, valamint a `content/read` műveletet a `samples/nginx` adattáron:

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

A kimenet a következőkhöz hasonló:

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

Az az [ACR token Create][az-acr-token-create] paranccsal hozzon létre egy jogkivonatot, amely társítva van a *MyScopeMap* hatókör-leképezéséhez. Alapértelmezés szerint a parancs két jelszót hoz létre. Ez a példa a jogkivonat állapotát `enabled` (az alapértelmezett beállítás) állítja be, de bármikor frissítheti a tokent, és beállíthatja az állapotot `disabled`re.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

A kimenet a token részleteit jeleníti meg, beleértve a generált jelszavakat és az alkalmazott hatókör-térképet. Javasoljuk, hogy a jelszavakat biztonságos helyen mentse, hogy később a `docker login`használhassa. A jelszavakat nem lehet újból beolvasni, de újakat lehet létrehozni.

## <a name="generate-passwords-for-token"></a>Jelszavak előállítása jogkivonat számára

Ha a jogkivonat létrehozásakor létrehozott jelszavakat, folytassa a [hitelesítést a beállításjegyzékkel](#authenticate-using-token).

Ha nem rendelkezik jogkivonat-jelszóval, vagy új jelszavakat szeretne előállítani, futtassa az az [ACR token hitelesítő adatok létrehozása][az-acr-token-credential-generate] parancsot.

Az alábbi példa új jelszót hoz létre a létrehozott jogkivonathoz, amelynek lejárati időtartama 30 nap. A jelszót a környezeti változó TOKEN_PWD tárolja. Ez a példa a bash-rendszerhéjhoz van formázva.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Hitelesítés token használatával

`docker login` futtatásával hitelesítheti magát a beállításjegyzékben a jogkivonat hitelesítő adatainak használatával. Adja meg a jogkivonat nevét felhasználónévként, és adja meg az egyik jelszavát. A következő példa a bash-rendszerhéjhoz van formázva, és környezeti változók használatával biztosítja az értékeket.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A kimenetnek sikeres hitelesítést kell mutatnia:

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Hatókörön belüli hozzáférés ellenőrzése

Azt is ellenőrizheti, hogy a jogkivonat hatókörön belüli engedélyeket biztosít-e a beállításjegyzékben található adattárakhoz. Ebben a példában a következő `docker pull` parancsok sikeresen elvégezték a `samples/hello-world` és `samples/nginx` adattárakban elérhető rendszerképek lekérését:

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Mivel a példa tokenje csak a `samples/hello-world` adattáron engedélyezi a `content/write` műveletet, `docker push` a tárház sikeres, de `samples/nginx`esetén sikertelen:

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Hatókör-leképezés és jogkivonat frissítése

A jogkivonat engedélyeinek frissítéséhez frissítse az engedélyeket a társított hatókör-térképen az [az ACR scope-Map Update][az-acr-scope-map-update]paranccsal. Ha például frissíteni szeretné a *MyScopeMap* , hogy eltávolítsa a `content/write` műveletet a `samples/hello-world` adattáron:

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Ha a hatókör-hozzárendelés egynél több tokenhez van társítva, a parancs frissíti az összes társított jogkivonat engedélyét.

Ha másik hatókör-térképpel rendelkező tokent szeretne frissíteni, futtassa [az az ACR token Update][az-acr-token-update]parancsot. Például:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

A tokenek frissítése vagy a tokenhez társított hatókör-hozzárendelések módosítása után az engedély a következő `docker login` vagy más, a tokent használó hitelesítésre lép érvénybe.

A token frissítése után létrehozhat új jelszavakat a beállításjegyzék eléréséhez. Futtatás [az ACR token hitelesítőadat-létrehozás][az-acr-token-credential-generate]. Például:

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>Az adattár – hatókörön belüli engedélyek

### <a name="concepts"></a>Alapelvek

A tárház hatókörű engedélyeinek konfigurálásához létre kell hoznia egy *hozzáférési jogkivonatot* és egy hozzárendelt *hatókör-leképezést* az Azure CLI parancsainak használatával.

* A **hozzáférési jogkivonat** a beállításjegyzékben való hitelesítéshez jelszóval használt hitelesítő adat. Az egyes jogkivonatokhoz társított *műveletek* egy vagy több tárházra terjednek ki. Megadhatja az egyes tokenek lejárati idejét. 

* Az egyes adattárakon **végrehajtott műveletek** az alábbiak közül egyet vagy többet tartalmaznak.

  |Műveletek  |Leírás  |
  |---------|---------|
  |`content/read`     |  Adatok beolvasása az adattárból. Például lehívhat egy összetevőt.  |
  |`metadata/read`    | Metaadatok olvasása az adattárból. Például a címkék listázása vagy a jegyzékfájl metaadatainak megjelenítése.   |
  |`content/write`     |  Az adattárban lévő adatbevitel. A with `content/read` használatával leküldheti az adott összetevőt.    |
  |`metadata/write`     |  Metaadatok írása a tárházba. Például frissítse a jegyzékfájl attribútumait.  |
  |`content/delete`    | Adatok eltávolítása az adattárból. Törölheti például a tárházat vagy a jegyzékfájlt. |

* A **hatókör-hozzárendelés** egy olyan beállításjegyzék-objektum, amely a jogkivonatra alkalmazott adattár-engedélyeket csoportosítja, vagy más jogkivonatokra is alkalmazható. Ha nem alkalmaz hatókör-leképezést jogkivonat létrehozásakor, a rendszer automatikusan létrehozza a hatókör-leképezést az engedély beállításainak mentéséhez. 

  A hatókör-térkép segítségével több, azonos hozzáféréssel rendelkező felhasználó konfigurálható a Tárházak készletéhez. A Azure Container Registry a rendszer által meghatározott hatókör-térképeket is biztosít, amelyeket a hozzáférési jogkivonatok létrehozásakor alkalmazhat.

Az alábbi képen összefoglalja a tokenek és a hatóköri térképek közötti kapcsolatot. 

![A beállításjegyzék hatókörének leképezései és jogkivonatai](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Forgatókönyvek

Hozzáférési token használatára vonatkozó forgatókönyvek a következők:

* Egyéni tokenekkel rendelkező IoT-eszközök biztosítása a rendszerkép lekéréséhez adattárból
* Adjon meg egy olyan külső szervezetet, amely rendelkezik engedélyekkel egy adott tárházhoz 
* Korlátozza a tárház hozzáférését a szervezet adott felhasználói csoportjaihoz. Adjon meg például írási és olvasási hozzáférést azoknak a fejlesztőknek, akik adott adattárakra irányuló képeket készítenek, és olvasási hozzáférést biztosítanak az ezekből a tárházból üzembe helyezett csapatokhoz.

### <a name="authentication-using-token"></a>Hitelesítés token használatával

Használja a jogkivonat nevét felhasználónévként és a hozzá tartozó jelszavak egyikét a célként megadott beállításjegyzékben való hitelesítéshez. A hitelesítési módszer a konfigurált művelettől függ.

### <a name="contentread-or-contentwrite"></a>tartalom/olvasás vagy tartalom/írás

Ha a jogkivonat csak `content/read` vagy `content/write` műveletet engedélyez, adja meg a jogkivonat hitelesítő adatait a következő hitelesítési folyamatok bármelyikében:

* Hitelesítés a Docker használatával `docker login`
* Hitelesítés a beállításjegyzék használatával az az [ACR login][az-acr-login] paranccsal az Azure CLI-ben

A hitelesítés után a jogkivonat engedélyezi a konfigurált műveleteket a hatókörön belüli adattáron vagy adattárakon. Ha például a jogkivonat engedélyezi a `content/read` műveletet egy adattáron, `docker pull` műveleteket a tárházban lévő lemezképeken engedélyezheti.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>metaadatok/olvasás, metaadatok/írás, vagy tartalom/törlés

Ha a jogkivonat engedélyezi `metadata/read`, `metadata/write`vagy `content/delete` műveleteit egy adattáron, a jogkivonat hitelesítő adatait paraméterként kell megadni az Azure CLI-ben a kapcsolódó az [ACR adattár][az-acr-repository] parancsaival.

Ha például `metadata/read` műveletek engedélyezve vannak egy adattáron, adja át a jogkivonat hitelesítő adatait az az [ACR repository show-Tags][az-acr-repository-show-tags] parancsnak a címkék listázásához való futtatásakor.

## <a name="next-steps"></a>További lépések

* A hatóköri térképek és a hozzáférési tokenek kezeléséhez használja az az [ACR scope-Map][az-acr-scope-map] és [az ACR token][az-acr-token] Command groups további parancsait.
* Tekintse meg a [hitelesítés áttekintését](container-registry-authentication.md) olyan forgatókönyvek esetén, amelyek egy rendszergazdai fiók vagy egy Azure Active Directory identitás használatával hitelesítik magukat az Azure Container registryben.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
