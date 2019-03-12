---
title: Az Azure Container Registry használata Helm-adattárak
description: Ismerje meg, hogyan egy Helm-adattárhoz használata az Azure Container Registryvel alkalmazások diagramok tárolására
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551993"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Azure Container Registry vagy a Helm használata az alkalmazás diagramok

Gyorsan üzembe helyezheti és kezelheti a Kubernetes alkalmazásokat, használhatja a [nyílt forráskódú Helm Csomagkezelő][helm]. A Helm, alkalmazások vannak meghatározva *diagramok* egy Helm-diagram adattárban tárolt. Ezekbe a diagramokba konfigurációk és függőségek meghatározása, és lehetnek rendszerverzióval ellátott alkalmazás életciklusa során. Az Azure Container Registry a gazdagépként Helm diagramtárakban használható.

Az Azure Container Registryvel egy saját, biztonságos Helm diagramadattár, hozhat létre folyamatokat, vagy más Azure-szolgáltatásokkal integrálható, rendelkezik. Helm diagramtárakban az Azure Container Registry georeplikációs funkciókat, így a diagramokat megközelíti a központi telepítések és a redundancia biztosítása érdekében tartalmazzák. Csak a-diagramok által felhasznált tárhelyért kell fizetnie, és minden Azure Container Registry ár csomag érhető el.

Ez a cikk bemutatja, hogyan használható egy Helm-diagram az Azure Container Registryben tárolt adattár.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

A jelen cikkben ismertetett lépések végrehajtásához a következő előfeltételeknek kell teljesülniük:

- **Az Azure Container Registry** -tároló-beállításjegyzék létrehozása az Azure-előfizetésében. Például a [az Azure portal](container-registry-get-started-portal.md) vagy a [Azure CLI-vel](container-registry-get-started-azure-cli.md).
- **Helm-ügyfélverzió 2.11.0 (nem az RC verzióra) vagy újabb** – futtatási `helm version` aktuális verziójának megkereséséhez. Emellett egy Helm-kiszolgáló (a tiller valóban) inicializálása egy Kubernetes-fürtön belül. Ha szükséges, [Azure Kubernetes Service-fürt létrehozása][aks-quickstart]. Hogyan kell telepíteni, és frissítse a Helm további információkért lásd: [telepítése Helm][helm-install].
- **Az Azure CLI 2.0.46 verzió vagy újabb** – futtatási `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>A Helm-ügyfél-adattár hozzáadása

A Helm-adattárhoz HTTP-kiszolgálót, amely a Helm-diagramok is tárolhatja. Az Azure Container Registry a tárolási megoldás biztosítása a Helm-diagramok, és beállíthatja az index definícióját adjon hozzá vagy távolít el a diagramok a tárházba.

Az Azure Container Registry egy Helm-diagram adattárhoz hozzáadni, az Azure CLI használatával. Ezzel a módszerrel a Helm-ügyfél frissül az URI és az Azure Container Registry által támogatott adattár hitelesítő adatait. Adja meg manuálisan az adattár információit, ezért a hitelesítő adatok nem érhetőek el a parancs előzményeiben például nem kell.

Ha szükséges, jelentkezzen be az Azure CLI-vel, és kövesse az utasításokat:

```azurecli
az login
```

Az Azure CLI-vel alapértelmezett konfigurálása az Azure Container Registry használatával nevére a [az konfigurálása] [ az-configure] parancsot. A következő példában cserélje le a `<acrName>` a tárolójegyzék nevére:

```azurecli
az configure --defaults acr=<acrName>
```

Az Azure Container Registry Helm-diagram adattár adhat hozzá a Helm-ügyfél használatával a [az acr helm-adattár hozzáadása] [ az-acr-helm-repo-add] parancsot. Ez a parancs lekéri a hitelesítést az Azure container registry a Helm-ügyfél által használt token. A hitelesítési jogkivonat a érvényes 1 óra. Hasonló `docker login`, ezzel a paranccsal futtathatja a jövőbeli CLI-munkamenetre, a Helm-ügyfél a Azure Container Registry Helm-diagram tárházzal hitelesítéséhez:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Diagram hozzáadása a tárház

Ebben a cikkben folytassuk egy meglévő Helm-diagramot, a nyilvános Helm *stabil* adattárat. A *stabil* tárház egy válogatott, nyilvános tárház, amely tartalmazza a közös alkalmazás-diagramok. Csomag maintainers küldhet a diagramok a *stabil* ugyanolyan módon, hogy a Docker Hub biztosít közös tárolórendszerképekhez nyilvános beállításjegyzék-tárban. A diagram a nyilvános webhelyről letöltött *stabil* tárház majd lehet leküldeni a privát Azure Container Registry-tárházhoz. A legtöbb esetben ehhez hozhat létre, és töltse fel az alkalmazások fejlesztése a saját diagramok. Hogyan hozhat létre saját Helm-diagramok további információkért lásd: [Helm-diagramok fejlesztése][develop-helm-charts].

Először hozzon létre egy könyvtárban található *~/acr-helm*, majd töltse le a meglévő *stable/wordpress* diagram:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

A letöltött diagram listában, és jegyezze fel a Wordpress-verzió a fájlnévben. A `helm fetch stable/wordpress` parancs nem adott meg egy adott verzióját, ezért a *legújabb* verzió olvasott be. Helm-diagramok a következő fájlnév egy verziószámot közé tartozik a [SemVer 2] [ semver2] standard. Az alábbi példa kimenetében a Wordpress diagram verziója *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Most küldje le a diagram a Helm-diagram adattárba, az Azure Container Registryben az Azure CLI-vel [az acr helm leküldéses] [ az-acr-helm-push] parancsot. Adja meg a nevét, a Helm-diagramot, mint például az előző lépésben letöltött *wordpress-2.1.10.tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Néhány pillanat múlva az Azure CLI jelenti, hogy a diagram mentése megtörtént, az alábbi példa kimenetében látható módon:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>A tárházban található lista diagramok

A Helm-ügyfél egy helyi gyorsítótárazott másolatának távoli tárházak tartalmát tárolja. Egy távoli tárházba módosítások automatikusan nem érhető el diagram a Helm-ügyfél által helyileg ismert listájának frissítéséhez. Diagramokat keres adattárak között, ha a Helm, a helyi gyorsítótárban tárolt index használja. A diagram az előző lépésben feltöltött használatához a helyi tárház Helm index frissíteni kell. A Helm-ügyfél adattárait újraindexelés, vagy az adattár index frissítése az Azure CLI használatával. Minden alkalommal, amikor a diagram hozzáadása a tárház ebben a lépésben kell elvégezni:

```azurecli
az acr helm repo add
```

A diagram a tárház és a rendelkezésre álló index frissítése helyileg tárolja a rendszeres Helm ügyfél parancsok használatával kereshet, vagy telepítse. A tárházban minden diagram megjelenítéséhez használja `helm search <acrName>`. Adja meg a saját Azure Container Registry neve:

```console
helm search <acrName>
```

A Wordpress-diagram az előző lépésben leküldött szerepel, az alábbi példa kimenetében látható módon:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Az Azure CLI-vel a diagramok is listázhatja használatával [az acr helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Adatait egy Helm-diagram megjelenítése

Egy adott diagramra információinak megtekintése a tárházban, újra használhatja a szokásos Helm-ügyfél. A diagram nevű információkat *wordpress*, használjon `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Ha a verziószám nem áll rendelkezésre, a *legújabb* verziót használja. Helm a diagram részletes adatait adja vissza, a következő sűrített példához kimenetben látható módon:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Az Azure CLI-vel a diagram adatait is megjelenítheti [az acr helm show] [ az-acr-helm-show] parancsot. Ismét a *legújabb* alapértelmezés szerint egy diagram verzióját adja vissza. Hozzáfűzheti `--version` például listázhatja egy adott verzióját egy diagramot, *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Telepítse a Helm-diagramot a tárházból

A Helm-diagramot a tárházban a tárház nevének megadásával telepítve van, és ezután diagramterület neve. A Wordpress diagram telepítéséhez használja a Helm-ügyfél:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Küldje le a az Azure Container Registry Helm-diagram adattárba, és később visszatérhet egy új CLI-munkamenetben, ha a helyi Helm-ügyfélnek kell egy frissített hitelesítési tokent. Egy új hitelesítési jogkivonat beszerzéséhez használja a [az acr helm-adattár hozzáadása] [ az-acr-helm-repo-add] parancsot.

A következő lépéseket a telepítés során:

- A Helm a helyi tárház index keresi.
- A megfelelő diagram az Azure Container Registry-tárházból letölti.
- A diagram helyezünk üzembe a tiller valóban a Kubernetes-fürtben.

A következő sűrített példához kimenetet jeleníti meg a Kubernetes-erőforrásokat a Helm-diagram használatával telepített:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Egy Helm-diagram törölni a tárházból

A diagram a tárházból törléséhez használja a [az acr helm delete] [ az-acr-helm-delete] parancsot. Adja meg például a nevét, a diagram *wordpress*, és törölni, mint például a verzió *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Ha szeretné, a nevesített diagram minden verzió törlése, hagyja ki a `--version` paraméter.

A diagram továbbra is adja vissza a `helm search <acrName>`. A Helm-ügyfél újra, automatikusan nem frissíti a tárházban elérhető diagramok listáját. A Helm ügyfél tárház index frissítéséhez használja a [az acr helm-adattár hozzáadása] [ az-acr-helm-repo-add] újra a parancsot:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>További lépések

Ez a cikk egy meglévő Helm-diagramot a köz használt *stabil* tárház. Hogyan hozhat létre, és a Helm-diagramok központi telepítése a további információkért lásd: [fejlesztésének Helm-diagramok][develop-helm-charts].

Helm-diagramok a tároló-létrehozási folyamat részeként is használható. További információkért lásd: [használata Azure Container Registry feladatok][acr-tasks].

Használata és kezelése az Azure Container Registry vonatkozó további információkért lásd: a [ajánlott eljárások][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
