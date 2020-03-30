---
title: Azure-beli csatlakoztatottgép-ügynök parancssori felülete
description: Az Azure Connected Machine CLI-ügynök referenciadokumentációja
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513197"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure-beli csatlakoztatottgép-ügynök parancssori felülete

Az `Azcmagent` (Azure Connected Machine Agent) eszköz konfigurálására és az Azure-hoz való nem azure-alapú gépek kapcsolatának konfigurálására és hibaelhárítására szolgál.

Maga az ügynök egy Linuxon `himdsd` megnevezett démonfolyamat, és `himds` egy Windows-szolgáltatás a Windows rendszeren.

Normál használat `azcmagent connect` esetén a gép és az Azure közötti `azcmagent disconnect` kapcsolat létrehozására szolgál, és ha úgy dönt, hogy már nem szeretné, hogy a kapcsolat. A többi parancs hibaelhárításra vagy más speciális esetekre van.

## <a name="options"></a>Beállítások

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>LÁSD MÉG:

* [azcmagent connect](#azcmagent-connect) - Csatlakoztatja a gépet az Azure-hoz
* [azcmagent kapcsolatbontása](#azcmagent-disconnect) – A gép leválasztása az Azure-ból
* [azcmagent reconnect](#azcmagent-reconnect) - Újracsatlakoztatja a gépet az Azure-hoz
* [azcmagent show](#azcmagent-show) - Gép metaadatainak és ügynökállapotának beírása. Ez elsősorban hibaelhárítási cél.
* [azcmagent verzió](#azcmagent-version) - A hibrid felügyeleti ügynök verziójának megjelenítése

## <a name="azcmagent-connect"></a>azcmagent csatlakozás

Csatlakoztatja a gépet az Azure-hoz

### <a name="synopsis"></a>Áttekintés

Létrehoz egy erőforrást az Azure-ban, amely ezt a gépet képviseli.

Ez a megadott hitelesítési beállításokat használja egy erőforrás létrehozásához az Azure Resource Manager ben, amely ezt a gépet képviseli. Az erőforrás a kért előfizetési és erőforráscsoportban található, és a gép adatait a helyparaméter által meghatározott Azure-régióban tárolja.
Az alapértelmezett erőforrásnév a számítógép állomásneve, ha nem bírálják felül.

A rendszer a számítógép rendszer-hozzárendelt identitásának megfelelő tanúsítványt ezután letölti és helyileg tárolja. Miután ez a lépés befejeződött az **Azure Connected Machine metaadat-szolgáltatás** és a vendég konfigurációs ügynök szinkronizálása az Azure-felhővel.

Hitelesítési beállítások:

* Hozzáférési jogkivonat`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Szolgáltatás névazonosítója és titkos`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Eszközbejelentkezés (interaktív)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Szintaxis

```none
azcmagent connect [flags]
```

### <a name="options"></a>Beállítások

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent kapcsolat bontása

Leválasztja a gépet az Azure-ról

### <a name="synopsis"></a>Áttekintés

Törli az erőforrást az Azure-ban, amely ezt a kiszolgálót képviseli.

Ez a parancs a megadott hitelesítési beállításokat használja a gépet képviselő Azure Resource Manager erőforrás eltávolításához. Ezt követően az **Azure Connected Machine metaadat-szolgáltatás** és a vendég konfigurációs ügynök lesz leválasztva. Ez a parancs nem állítja le vagy távolítja el a szolgáltatásokat: távolítsa el a csomagot ennek érdekében.

Ez a parancs magasabb jogosultságokat igényel, mint az "Azure Connected Machine Onboarding" szerepkör.

A számítógép leválasztása `azcmagent connect`után `azcmagent reconnect` használja a használatát, nem akkor, ha új erőforrást szeretne létrehozni az Azure-ban.

Hitelesítési beállítások:

* Hozzáférési jogkivonat`azcmagent disconnect --access-token <>`
* Szolgáltatás névazonosítója és titkos`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Interaktív eszköz bejelentkezés`azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Szintaxis

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Beállítások

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent reconnect

A számítógép újracsatlakoztatása az Azure-hoz

### <a name="synopsis"></a>Áttekintés

Csatlakoztassa újra a gépet érvénytelen hitelesítő adatokkal az Azure-hoz.

Ha egy gép már rendelkezik egy erőforrást az Azure-ban, de nem tudja hitelesíteni, akkor újra csatlakoztatható ezzel a paranccsal. Ez akkor lehetséges, ha egy gép kikapcsolta elég hosszú ahhoz, hogy a tanúsítvány lejár (legalább 45 nap).

Ha a gép nem, `azcmagent disconnect` `azcmagent connect` használja helyette.

Ez a parancs a megadott hitelesítési beállításokat használja az Azure Resource Manager-erőforrásnak megfelelő új hitelesítő adatok lekéréséhez.

Ez a parancs magasabb jogosultságokat igényel, mint az **Azure Connected Machine bevezetési** szerepkör.

Hitelesítési beállítások

* Hozzáférési jogkivonat`azcmagent reconnect --access-token <>`
* Szolgáltatás névazonosítója és titkos`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Interaktív eszköz bejelentkezés`azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Szintaxis

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Beállítások

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent show

A gép metaadatainak és ügynöki állapotának leése. Ez elsősorban hibaelhárítási cél.

### <a name="synopsis"></a>Áttekintés

A gép metaadatainak és ügynöki állapotának leése. Ez elsősorban hibaelhárítási cél.


### <a name="syntax"></a>Szintaxis

```
azcmagent show [flags]
```

### <a name="options"></a>Beállítások

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent verzió

A hibrid felügyeleti ügynök verziójának megjelenítése

### <a name="synopsis"></a>Áttekintés

A hibrid felügyeleti ügynök verziójának megjelenítése

### <a name="syntax"></a>Szintaxis

```none
azcmagent version [flags]
```

### <a name="options"></a>Beállítások

```none
  -h, --help   help for version
```
