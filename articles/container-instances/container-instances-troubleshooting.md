---
title: "Azure-tárolót példányok hibaelhárítása"
description: "Ismerje meg az Azure-tároló példányaival kapcsolatos problémák elhárítása"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 78bd45f7f71fd25e351d4e9b922a6a3f171437fd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Azure-tároló példányaival telepítési problémák elhárításához

Ez a cikk bemutatja, hogyan kapcsolatos problémák elhárítása az Azure-tároló példányokhoz tárolók telepítése során. Ismerteti az egyes a gyakori problémákat mutatjuk be.

## <a name="get-diagnostic-events"></a>Diagnosztikai események

Az alkalmazás kódjáról olyan tárolóban naplók megtekintéséhez használja a [az tároló naplók](/cli/azure/container#logs) parancsot. De ha a tároló telepítése nem sikerült, akkor tekintse át a diagnosztikai adatokat az Azure-tároló példányok erőforrás-szolgáltató által biztosított. A tároló események megtekintéséhez futtassa a következő parancsot:

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

A kimenet a tárolót, és telepítési eseményeket core tulajdonságait tartalmazza:

```bash
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
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>Gyakori telepítési problémákkal

Van néhány gyakori problémákat fiók a központi telepítésben lévő legtöbb hibákat.

## <a name="unable-to-pull-image"></a>Nem sikerült lekéréses kép

Nem sikerült a lemezkép kezdetben lekéréses Azure tároló példányok esetén újrapróbálja valamennyi ideje előtt esetleg sikertelenek lesznek. Ha nem kell húzni a lemezképet, a következőhöz hasonló események jelennek meg:

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

Oldja meg, a tároló törlése, és ismételje meg a központi telepítés, fizető zárja be a figyelmet, hogy a lemezkép nevét helyesen adta.

## <a name="container-continually-exits-and-restarts"></a>Tároló folyamatosan kilép, és újraindul

Ha a tároló létrehozása után futtatott, és automatikusan újraindul, szükség lehet beállítani a [indítsa újra a házirend](container-instances-restart-policy.md) a **OnFailure** vagy **soha**. Ha megad **OnFailure** és továbbra is folyamatosan lásd újraindul, előfordulhat, hogy az alkalmazás vagy a parancsfájl végrehajtása a tárolóban kapcsolatos problémát.

A tároló példányok API tartalmaz egy `restartCount` tulajdonság. Egy tároló újraindítások számát ellenőrzéséhez használja a [az tároló megjelenítése](/cli/azure/container#az_container_show) az Azure CLI 2.0 parancsot. A következő egy példa a kimenetre (amely kivonatosan mutatja csonkolódtak), hogy a `restartCount` tulajdonság a kimeneti végén.

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

A kulcsot a lemezkép mérete kisebb tartása annak ellenőrzése, hogy a végső kép nem tartalmaz semmit, amelyre nincs szükség a futási időben. Ehhez a egyirányú azt a [többlépcsős buildek](https://docs.docker.com/engine/userguide/eng-image/multistage-build/). Többlépcsős épít, ellenőrizze, hogy a végső kép csak az alkalmazás van szüksége az összetevők tartalmazza, és nem egy, az új tartalom, amely egyszerűen volt szükség összeállítása során.

A más, csökken a kép lekéréses a tároló indítási idővel módja a tároló lemezkép az Azure-tároló beállításjegyzék ugyanabban a régióban, ahol az Azure tároló példányok használni kívánt üzemeltetéséhez. Ez lerövidíti a hálózati elérési útját, amelyet a tároló kép utaznak, jelentősen lerövidíteni a letöltési időt.

## <a name="resource-not-available-error"></a>Erőforrás nem érhető el hiba

Különböző területi erőforrás miatt betöltése az Azure-ban, és a következő hiba akkor fordulhat elő, amikor egy tároló-példány telepítése:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ez a hiba azt jelzi, hogy a régióban, amelyben telepíteni kívánt túl nagy terhelés miatt a tároló megadott erőforrások nem rendelhető hozzá jelenleg. Egy vagy több, az a probléma megoldása érdekében a következő megoldás lépéseit.

* Ellenőrizze a definiált paraméterek tartoznak a tároló központi telepítési beállítások [Azure-tároló példányok régiónkénti elérhetőség](container-instances-region-availability.md)
* A tároló alacsonyabb CPU és memória beállításainak megadása
* Egy másik Azure-régiót telepítése
* Egy későbbi időpontban telepítése
