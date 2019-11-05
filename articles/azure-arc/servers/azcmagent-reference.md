---
title: Azure-beli csatlakoztatott gépi ügynök parancssori felülete
description: Az Azure Connected Machine Agent parancssori felületének dokumentációja
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513197"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure-beli csatlakoztatott gépi ügynök parancssori felülete

A `Azcmagent` (Azure Connected Machine Agent) eszköz használatával konfigurálhatja és elháríthatja az Azure-hoz kapcsolódó nem Azure-beli gépeket.

Maga az ügynök egy `himdsd` Linux rendszeren nevű démoni folyamat, és egy `himds` nevű Windows-szolgáltatás, amely Windows rendszeren található.

Normál használat esetén `azcmagent connect` a gép és az Azure közötti kapcsolat létesítésére szolgál, és `azcmagent disconnect`, ha úgy dönt, hogy már nem kívánja használni a kapcsolatot. A többi parancs a hibaelhárításhoz és egyéb speciális esetekhez használható.

## <a name="options"></a>Beállítások

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>LÁSD MÉG:

* [azcmagent-csatlakozás](#azcmagent-connect) – a gép csatlakoztatása az Azure-hoz
* [azcmagent leválasztása – leválasztja](#azcmagent-disconnect) ezt a gépet az Azure-ból
* [azcmagent Újrakapcsolódás](#azcmagent-reconnect) – a gép újrakapcsolódása az Azure-hoz
* [azcmagent show](#azcmagent-show) – lekérdezi a gép metaadatait és az ügynök állapotát. Ez elsősorban hibaelhárításhoz hasznos.
* [azcmagent-verzió](#azcmagent-version) – a hibrid felügyeleti ügynök verziójának megjelenítése

## <a name="azcmagent-connect"></a>azcmagent-kapcsolat

A gép csatlakoztatása az Azure-hoz

### <a name="synopsis"></a>Áttekintés

Létrehoz egy erőforrást az Azure-ban, amely ezt a gépet jelképezi.

Ez a megadott hitelesítési beállítások használatával hoz létre egy erőforrást a gépet jelképező Azure Resource Managerban. Az erőforrás a kért előfizetésben és erőforráscsoporthoz van, és a gép adatait a Location paraméter által megadott Azure-régióban tárolja a rendszer.
Az alapértelmezett erőforrás neve a gép állomásneve, ha nincs felülbírálva.

Ezután a számítógép rendszerhez rendelt identitásához tartozó tanúsítvány letöltése és tárolása helyileg történik. Ha ez a lépés befejeződött, az **Azure Connected Machine metadata** szolgáltatás és a vendég konfigurációs ügynök megkezdi az Azure Cloud-val való szinkronizálást.

Hitelesítési beállítások:

* Hozzáférési jogkivonat `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Egyszerű szolgáltatás azonosítója és titkos `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Eszköz bejelentkezés (interaktív) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>azcmagent bontása

A gép leválasztása az Azure-ból

### <a name="synopsis"></a>Áttekintés

A kiszolgálót jelölő Azure-beli erőforrás törlése.

Ez a parancs a megadott hitelesítési beállításokat használja a gépet jelölő Azure Resource Manager erőforrás eltávolításához. Ezt követően az Azure-beli **csatlakoztatott számítógép metadata Service** és a vendég konfigurációs ügynök le lesz választva. Ez a parancs nem állítja le és nem távolítja el a szolgáltatásokat: távolítsa el a csomagot annak érdekében, hogy elvégezze.

Ehhez a parancshoz magasabb jogosultságok szükségesek, mint az "Azure Connected Machine bevezetésének" szerepkör.

Ha egy gép le van választva, akkor a `azcmagent connect`használata nem `azcmagent reconnect`, ha új erőforrást szeretne létrehozni az Azure-ban.

Hitelesítési beállítások:

* Hozzáférési jogkivonat `azcmagent disconnect --access-token <>`
* Egyszerű szolgáltatás azonosítója és titkos `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Interaktív eszköz bejelentkezési `azcmagent disconnect --tenant-id <>`

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

## <a name="azcmagent-reconnect"></a>azcmagent újracsatolása

A gép újrakapcsolódása az Azure-hoz

### <a name="synopsis"></a>Áttekintés

A számítógép újrakapcsolódása érvénytelen hitelesítő adatokkal az Azure-ba.

Ha egy gépnek már van erőforrása az Azure-ban, de a hitelesítése nem lehetséges, akkor a parancs használatával újra csatlakozhat. Ez akkor lehetséges, ha egy gép ki lett kapcsolva elég sokáig ahhoz, hogy a tanúsítványa lejárjon (legalább 45 nap).

Ha egy gép `azcmagent disconnect`kapcsolattal lett leválasztva, használja a `azcmagent connect` helyet.

Ez a parancs a megadott hitelesítési beállításokat használja a gépet jelképező Azure Resource Manager erőforrásnak megfelelő új hitelesítő adatok lekéréséhez.

Ehhez a parancshoz magasabb jogosultságok szükségesek, mint az Azure-beli **csatlakoztatott gép** bevezetési szerepköre.

Hitelesítési beállítások

* Hozzáférési jogkivonat `azcmagent reconnect --access-token <>`
* Egyszerű szolgáltatás azonosítója és titkos `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Interaktív eszköz bejelentkezési `azcmagent reconnect --tenant-id <>`

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

## <a name="azcmagent-show"></a>azcmagent megjelenítése

A számítógép metaadatainak és az ügynök állapotának beolvasása. Ez elsősorban hibaelhárításhoz hasznos.

### <a name="synopsis"></a>Áttekintés

A számítógép metaadatainak és az ügynök állapotának beolvasása. Ez elsősorban hibaelhárításhoz hasznos.


### <a name="syntax"></a>Szintaxis

```
azcmagent show [flags]
```

### <a name="options"></a>Beállítások

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent verziója

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
