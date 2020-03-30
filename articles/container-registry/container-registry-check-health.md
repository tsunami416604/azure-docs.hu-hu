---
title: A rendszerleíró adatbázis állapotának ellenőrzése
description: Megtudhatja, hogy miként futtathat gyors diagnosztikai parancsot az Azure-tároló beállításjegyzékének használatakor felmerülő gyakori problémák azonosítására, beleértve a helyi Docker-konfigurációt és a rendszerleíró adatbázishoz való kapcsolódást
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456415"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Az Azure-tároló beállításjegyzékének ellenőrzése

Az Azure-tároló beállításjegyzékének használatakor előfordulhat, hogy problémák merülnek fel. Előfordulhat például, hogy a helyi környezetben a Docker-rel kapcsolatos probléma miatt nem tud lekérni egy tárolórendszerképet. Vagy egy hálózati probléma megakadályozhatja a beállításjegyzékhez való csatlakozást. 

Első diagnosztikai lépésként futtassa az [az acr check-health][az-acr-check-health] parancsot a környezet állapotára vonatkozó információk lefelvételéhez és a célbeállításjegyzékhez való hozzáféréshez. Ez a parancs az Azure CLI 2.0.67-es vagy újabb verziójában érhető el. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

## <a name="run-az-acr-check-health"></a>Futtassa az acr check-health-t

Az alábbi példák a `az acr check-health` parancs futtatásának különböző módjait mutatják be.

> [!NOTE]
> Ha a parancsot az Azure Cloud Shellben futtatja, a helyi környezet nincs ellenőrizve. A célbeállításjegyzékhez való hozzáférést azonban ellenőrizheti.

### <a name="check-the-environment-only"></a>Csak a környezet ellenőrzése

A helyi Docker démon, CLI-verzió és Helm ügyfélkonfiguráció ellenőrzéséhez futtassa a parancsot további paraméterek nélkül:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>A környezet és a célbeállításjegyzék ellenőrzése

A rendszerleíró adatbázishoz való hozzáférés ellenőrzéséhez és a helyi környezet ellenőrzéséhez adja át a célbeállításjegyzék nevét. Példa:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Hibajelentés

A parancs naplózza az adatokat a szabványos kimenetre. Probléma észlelése esetén hibakódot és leírást ad. A kódokról és a lehetséges megoldásokról további információt a [hibahivatkozásban talál.](container-registry-health-error-reference.md)

Alapértelmezés szerint a parancs leáll, ha hibát talál. A parancs futtatásával is futtathatja, hogy az minden állapot-ellenőrzéshez kimenetet biztosítjon, még akkor is, ha hibákat talál. Adja `--ignore-errors` hozzá a paramétert, ahogy az a következő példákban látható:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Példa a kimenetre:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>További lépések

Az [az acr check-health][az-acr-check-health] parancs által visszaadott hibakódokról az [Állapot-ellenőrzés hibahivatkozáscímű témakörben talál részleteket.](container-registry-health-error-reference.md)

Tekintse meg a [gyakran](container-registry-faq.md) ismételt kérdéseket és az Azure Container Registry egyéb ismert problémáit.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
