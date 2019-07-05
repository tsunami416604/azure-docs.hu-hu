---
title: Az Azure Container Registry beállításjegyzék állapotának ellenőrzése
description: Ismerje meg, hogyan azonosíthatja a gyakori problémákat, egy Azure container registryt, beleértve a helyi Docker-konfigurációját és a beállításjegyzék kapcsolat használata esetén a gyors diagnosztikai parancs végrehajtása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555097"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Egy Azure container registry állapotának ellenőrzése

Azure container registry használata esetén előfordulhat, hogy időnként tapasztal. Például hogy nem tudja lekérdezni egy tárolórendszerképet a docker használatával a helyi környezetben probléma miatt. Vagy hálózati probléma előfordulhat, hogy megakadályozzák a csatlakozást a regisztrációs adatbázisba. 

Diagnosztikai az első lépés, futtassa a [az acr jelölőnégyzet-állapot][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Az acr-ellenőrzés-állapotfigyelő futtatása

A következő példákban futtatni különböző módszereket mutatnak a `az acr check-health` parancsot.

> [!NOTE]
> Ha az Azure Cloud Shellben futtassa a parancsot, a rendszer nem ellenőrzi a helyi környezetben. Azonban a hozzáférést egy tároló-beállításjegyzéket ellenőrizheti.

### <a name="check-the-environment-only"></a>Csak a környezet

Ellenőrizze a helyi Docker démon, a parancssori felület verziójának és a Helm ügyfél-konfigurációt, futtassa a parancsot további paraméterek nélkül:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Ellenőrizze a környezet és a egy tároló-beállításjegyzéket

Ellenőrizze a beállításjegyzék elérését, valamint a helyi környezetben-ellenőrzést, át kell adnia egy tároló-beállításjegyzék nevére. Példa:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Hibajelentés

A parancs információkat naplózza a normál a kimenetbe. Ha probléma merül fel, biztosít egy hibakódot és egy leírást. A kódok és a lehetséges megoldások kapcsolatos további információkért lásd: a [hibaútmutató](container-registry-health-error-reference.md).

Alapértelmezés szerint a parancs leállítja az, ha hibát talált. Is futtathatja a parancsot, hogy a kimeneti biztosít az összes állapot-ellenőrzések, még akkor is, ha hibák találhatók. Adja hozzá a `--ignore-errors` paramétert, a következő példákban szemléltetett módon:

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

Által visszaadott hibakódok részleteit a [az acr jelölőnégyzet-állapot][az-acr-check-health] parancshoz, tekintse meg a [állapotának ellenőrzése hibaútmutató](container-registry-health-error-reference.md).

Tekintse meg a [– gyakori kérdések](container-registry-faq.md) vonatkozó gyakori kérdések és ismert problémákkal kapcsolatban az Azure Container Registrybe.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
