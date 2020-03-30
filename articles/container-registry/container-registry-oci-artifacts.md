---
title: OcI műtárgy leküldése és húzása
description: Open Container Initiative (OCI) összetevők leküldése és lekérése az Azure-beli privát tárolóbeállítás-nyilvántartás használatával
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371052"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>OCI-összetevő leküldése és lekérése Azure-tároló beállításjegyzékhasználatával

Az Azure container-beállításjegyzék használatával tárolhatja és kezelheti [az Open Container Initiative (OCI) összetevőit,](container-registry-image-formats.md#oci-artifacts) valamint a Docker- és A Docker-kompatibilis tárolórendszerképeket.

Ennek a képességnek a bemutatásához ez a cikk bemutatja, hogyan használhatja az [OCI Registry storage (ORAS)](https://github.com/deislabs/oras) eszközt egy mintaösszetevő – egy szöveges fájl – leküldéses egy Azure-tároló beállításjegyzékébe. Ezután húzza ki a műtárgyat a rendszerleíró adatbázisból. Az Azure-tároló beállításjegyzékében számos OCI-összetevőt kezelhet az egyes összetevőknek megfelelő különböző parancssori eszközök használatával.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Használja például az [Azure Portalt](container-registry-get-started-portal.md) vagy az [Azure CLI-t.](container-registry-get-started-azure-cli.md)
* **ORAS eszköz** - Töltse le és telepítse az operációs rendszer aktuális ORAS-kiadását a [GitHub tártárból.](https://github.com/deislabs/oras/releases) Az eszköz megjelent, mint egy`.tar.gz` tömörített tarball ( fájl). Bontsa ki és telepítse a fájlt az operációs rendszer szabványos eljárásaival.
* **Az Azure Active Directory egyszerű szolgáltatás (nem kötelező)** – Közvetlenhitelesítéshez az ORAS, hozzon létre egy [egyszerű szolgáltatás](container-registry-auth-service-principal.md) a beállításjegyzék eléréséhez. Győződjön meg arról, hogy a szolgáltatásnév van rendelve egy szerepkör, például AcrPush, hogy rendelkezik engedélyekkel a leküldéses és lekéréses összetevők.
* **Azure CLI (nem kötelező)** – Egyéni identitás használatához az Azure CLI helyi telepítésére van szükség. A 2.0.71-es vagy újabb verzió ajánlott. Futtassa `az --version `a verzió megkereséséhez. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).
* **Docker (nem kötelező)** – egyéni identitás használatához a Docker helyileg telepítve is kell lennie a beállításjegyzékkel való hitelesítéshez. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.


## <a name="sign-in-to-a-registry"></a>Bejelentkezés a rendszerleíró adatbázisba

Ez a szakasz a használt identitástól függően két javasolt munkafolyamatot jelenít meg a beállításjegyzékbe való bejelentkezéshez. Válassza ki a környezetének megfelelő módszert.

### <a name="sign-in-with-oras"></a>Bejelentkezés az ORAS-szal

A [leküldéses](container-registry-auth-service-principal.md) jogosultságokkal rendelkező `oras login` egyszerű szolgáltatás használatával futtassa a parancsot a rendszerleíró adatbázisba való bejelentkezéshez az egyszerű szolgáltatásalkalmazás-azonosító és jelszó használatával. Ebben az esetben adja meg a teljesen minősített rendszerleíró adatbázis nevét (az összes kisbetű), ebben az esetben *myregistry.azurecr.io*. Az egyszerű szolgáltatásalkalmazás-azonosító a környezeti `$SP_APP_ID`változóban, a `$SP_PASSWD`jelszó pedig a változóban jelenik meg.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

A Stdin jelszavának beolvasásához használja a használatát. `--password-stdin`

### <a name="sign-in-with-azure-cli"></a>Bejelentkezés az Azure CLI használatával

[Jelentkezzen be](/cli/azure/authenticate-azure-cli) az Azure CLI-be az identitásával leküldéses és lekéréses összetevők a tároló beállításjegyzékből.

Ezután az Azure CLI parancs [az acr bejelentkezési](/cli/azure/acr?view=azure-cli-latest#az-acr-login) a beállításjegyzék eléréséhez. Például a *myregistry*nevű rendszerleíró adatbázisban való hitelesítéshez:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`a Docker-ügyfél segítségével állítsa be az `docker.config` Azure Active Directory-jogkivonatot a fájlban. Az egyes hitelesítési folyamat végrehajtásához telepíteni és futtatni kell a Docker-ügyfelet.

## <a name="push-an-artifact"></a>Leadott műtárgy leküldése

Hozzon létre egy szövegfájlt egy helyi munkakönyvtárban néhány mintaszöveggel. Például egy bash shell:

```bash
echo "Here is an artifact!" > artifact.txt
```

A `oras push` paranccsal a program leküldheti a szövegfájlt a rendszerleíró adatbázisba. A következő példa leküldi a `samples/artifact` mintaszövegfájlt a tárműtérbe. A rendszerleíró adatbázis a teljesen minősített rendszerleíró adatbázis névvel *myregistry.azurecr.io* (az összes kisbetű). A műtárgy `1.0`címkézve van. A műtermék alapértelmezés szerint nem definiált típusú, amelyet a `artifact.txt`fájlnevet követő *adathordozó-típuskarakterlánc* azonosít. További típusokat az [OCI-összetevők](https://github.com/opencontainers/artifacts) lásd. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

A sikeres leküldéses kimenet hasonló a következőhöz:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Az összetevők kezelése a beállításjegyzékben, ha az Azure `az acr` CLI használatával, szabványos parancsokat a lemezképek kezelésére. Például az [az acr repository show][az-acr-repository-show] paranccsal lekéri a műtermék attribútumait:

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

A kimenet a következőkhöz hasonló:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Egy műtárgy lekérése

Futtassa a `oras pull` parancsot a műtermék rendszerleíró adatbázisból való lehúzásához.

Először távolítsa el a szövegfájlt a helyi munkakönyvtárból:

```bash
rm artifact.txt
```

Futtassa `oras pull` a műtermék lekérése, és adja meg az adathordozó-típus tolásához használt műtermék:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Ellenőrizze, hogy a lekérése sikeres volt-e:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>A műtermék eltávolítása (nem kötelező)

A műtermék eltávolítása az Azure-tároló beállításjegyzékből, használja az [az acr repository delete][az-acr-repository-delete] parancsot. A következő példa eltávolítja az ott tárolt műtárgyat:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>További lépések

* További információ [az ORAS-tárról,](https://github.com/deislabs/oras/tree/master/docs)többek között a műtermék jegyzékfájljának konfigurálásáról
* Az új műterméktípusokra vonatkozó információkért látogasson el az [OCI-összetevők](https://github.com/opencontainers/artifacts) tártárjára



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
