---
title: "Azure-tárolót példányok hibaelhárítása"
description: "Ismerje meg az Azure-tároló példányaival kapcsolatos problémák elhárítása"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 561729e5e495500222ccec5b4b536a3152cb25e3
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Azure-tároló példányaival telepítési problémák elhárításához

Ez a cikk bemutatja, hogyan kapcsolatos problémák elhárítása az Azure-tároló példányokhoz tárolók telepítése során. Ismerteti az egyes a gyakori problémákat mutatjuk be.

## <a name="get-diagnostic-events"></a>Diagnosztikai események

Az alkalmazás kódjáról olyan tárolóban naplók megtekintéséhez használja a [az tároló naplók] [ az-container-logs] parancsot. De ha a tároló telepítése nem sikerült, akkor tekintse át a diagnosztikai adatokat az Azure-tároló példányok erőforrás-szolgáltató által biztosított. A tároló események megtekintéséhez futtassa a [az tároló megjelenítése] [ az-container-show] parancs:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A kimenet a tárolót, és telepítési eseményeket (Itt látható csonkolt) core tulajdonságait tartalmazza:

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>Gyakori telepítési problémákkal

Van néhány gyakori problémákat fiók a központi telepítésben lévő legtöbb hibákat.

## <a name="image-version-not-supported"></a>Lemezkép-verziója nem támogatott

Kép van megadva, hogy Azure tároló példányok nem támogatja, ha hiba visszatér űrlap `ImageVersionNotSupported`. A hiba az értéke megjelenik `The version of image '{0}' is not supported.`. Ez a hiba jelenleg vonatkozik Windows 1709 képek használatát mérséklése LTS Windows-lemezkép. Windows 1709 képek támogatása a feldolgozása folyamatban van.

## <a name="unable-to-pull-image"></a>Nem sikerült lekéréses kép

Nem sikerült a lemezkép kezdetben lekéréses Azure tároló példányok esetén újrapróbálja valamennyi ideje előtt esetleg sikertelenek lesznek. Ha nem kell húzni a lemezképet, a következő események jelennek meg kimenete [az tároló megjelenítése][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

Oldja meg, a tároló törlése, és ismételje meg a központi telepítés, fizető zárja be a figyelmet, hogy a lemezkép nevét helyesen adta.

## <a name="container-continually-exits-and-restarts"></a>Tároló folyamatosan kilép, és újraindul

Ha a tároló létrehozása után futtatott, és automatikusan újraindul, szükség lehet beállítani a [indítsa újra a házirend](container-instances-restart-policy.md) a **OnFailure** vagy **soha**. Ha megad **OnFailure** és továbbra is folyamatosan lásd újraindul, előfordulhat, hogy az alkalmazás vagy a parancsfájl végrehajtása a tárolóban kapcsolatos problémát.

A tároló példányok API tartalmaz egy `restartCount` tulajdonság. Egy tároló újraindítások számát ellenőrzéséhez használja a [az tároló megjelenítése] [ az-container-show] az Azure CLI 2.0 parancsot. A következő egy példa a kimenetre (amely kivonatosan mutatja csonkolódtak), hogy a `restartCount` tulajdonság a kimeneti végén.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> A legtöbb tároló képek Linux terjesztésekről, bash, például a rendszerhéj állítja be az alapértelmezett parancs. Mivel a rendszerhéj önmagában nem hosszan futó szolgáltatás, ezekhez a tárolókhoz azonnal való kilépéshez és az alapértelmezett konfigurálásakor újraindítás hurok sorolhatók **mindig** indítsa újra a házirendet.

## <a name="container-takes-a-long-time-to-start"></a>Tároló start hosszú időt vesz igénybe.

Ha a tároló indítása, de végül hosszú ideig tart a sikeres, indítsa el a tároló-lemezkép mérete alapján. Azure tároló példányok az igény szerinti kéri le a tároló lemezképet, mert az indítási idő tapasztal közvetlenül kapcsolódik a méretét.

A Docker parancssori felület használatával a tároló-lemezkép mérete tekintheti meg:

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

A kulcsot a lemezkép mérete kisebb tartása annak ellenőrzése, hogy a végső kép nem tartalmaz semmit, amelyre nincs szükség a futási időben. Ehhez a egyirányú azt a [többlépcsős buildek][docker-multi-stage-builds]. Többlépcsős épít, ellenőrizze, hogy a végső kép csak az alkalmazás van szüksége az összetevők tartalmazza, és nem egy, az új tartalom, amely egyszerűen volt szükség összeállítása során.

A más, csökken a kép lekéréses a tároló indítási idővel módja a tároló lemezkép az Azure-tároló beállításjegyzék ugyanabban a régióban, ahol az Azure tároló példányok használni kívánt üzemeltetéséhez. Ez lerövidíti a hálózati elérési útját, amelyet a tároló kép utaznak, jelentősen lerövidíteni a letöltési időt.

## <a name="resource-not-available-error"></a>Erőforrás nem érhető el hiba

Különböző területi erőforrás miatt betöltése az Azure-ban, és a következő hiba akkor fordulhat elő, amikor egy tároló-példány telepítése:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ez a hiba azt jelzi, hogy a régióban, amelyben telepíteni kívánt túl nagy terhelés miatt a tároló megadott erőforrások nem rendelhető hozzá jelenleg. Legalább egy, a megoldás lépések segítségével a probléma megoldása érdekében.

* Ellenőrizze a definiált paraméterek tartoznak a tároló központi telepítési beállítások [kvótái és az Azure-tároló példányok régiónkénti elérhetőség](container-instances-quotas.md#region-availability)
* A tároló alacsonyabb CPU és memória beállításainak megadása
* Egy másik Azure-régiót telepítése
* Egy későbbi időpontban telepítése

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/

<!-- LINKS - Internal -->
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show