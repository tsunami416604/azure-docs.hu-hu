---
title: "Az Azure Service Fabric és az Azure CLI 2.0 használatának első lépései"
description: "Ez a dokumentum ismerteti az Azure Service Fabric-parancsmodul használatát az Azure CLI 2.0-s verziójában. Megtudhatja, hogyan csatlakozhat fürtökhöz, és hogyan kezelheti alkalmazásait."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: hu-hu
ms.lasthandoff: 07/14/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Az Azure Service Fabric és az Azure CLI 2.0

Az Azure parancssori felület (Azure CLI) 2.0-s verziója olyan parancsokat is tartalmaz, amelyekkel kezelheti Azure Service Fabric-fürtjeit. Megismerheti az Azure CLI és a Service Fabric használatának első lépéseit.

## <a name="install-azure-cli-20"></a>Az Azure CLI 2.0 telepítése

Az Azure CLI 2.0 parancsaival kezelheti a Service Fabric-fürtöket. Az Azure CLI legújabb verziójának beszerzéséhez kövesse az [Azure CLI 2.0 normál telepítési folyamatát](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

További információ: [Az Azure CLI 2.0 áttekintése](https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="azure-cli-syntax"></a>Azure CLI-szintaxis

Az Azure CLI-ben minden Service Fabric-parancs `az sf` előtaggal van ellátva. A használható parancsokkal kapcsolatos általános információkért használja az `az sf -h` parancsot. Ha egyetlen paranccsal kapcsolatban van szüksége segítségre, használja az `az sf <command> -h` parancsot.

Az Azure CLI-ben található Service Fabric-parancsok az alábbi elnevezési mintázatot követik:

```azurecli
az sf <object> <action>
```

az `<object>` az `<action>` művelet célpontja.

## <a name="select-a-cluster"></a>Fürt kiválasztása

A műveletek végrehajtása előtt ki kell választania egy fürtöt, amelyhez csatlakozni kíván. Példaként tekintse meg az alábbi kódot. A kód egy nem védett fürthöz csatlakozik.

> [!WARNING]
> Éles környezetben ne használjon nem védett Service Fabric-fürtöket.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

A fürt végpontját `http` vagy `https` előtaggal kell ellátni. Tartalmaznia kell a HTTP-átjáróhoz tartozó portot. A port és a cím megegyezik a Service Fabric Explorer URL-címével.

A tanúsítvánnyal védett fürtök esetében nem titkosított .pem, vagy .crt és .key fájlokat használhat. Példa:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

További információ: [Csatlakozás védett Azure Service Fabric-fürthöz](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> A `select` parancs egy kérést sem hajt végre a visszatérése előtt. A megadott fürt helyességének ellenőrzéséhez használjon az alábbihoz hasonló parancsot: `az sf cluster health`. Győződjön meg róla, hogy a parancs nem ad vissza hibát.

## <a name="basic-operations"></a>Alapszintű műveletek

A fürt kapcsolatadatai több Azure CLI-munkamenetben is megmaradnak. Egy Service Fabric-fürt kiválasztása után bármilyen Service Fabric-parancsot futtathat a fürtön.

A Service Fabric-fürt állapotának lekérdezéséhez például használja az alábbi parancsot:

```azurecli
az sf cluster health
```

A parancs az alábbi kimenetet eredményezi (feltéve, hogy az Azure CLI konfigurációjában JSON formátumú kimenet van megadva):

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Tippek és hibaelhárítás

Az alábbi információkat hasznosnak találhatja, ha hibába ütközne a Service Fabric-parancsok Azure CLI-ben történő használata során.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Tanúsítvány konvertálása PFX formátumról PEM formátumra

Az Azure CLI PEM- (.pem kiterjesztésű) fájlok formájában támogatja az ügyféloldali tanúsítványokat. Ha Windows rendszerből származó PFX-fájlokat lát, át kell alakítania a tanúsítványokat PEM formátumba. A PFX-fájlok PEM-fájlokká történő konvertálásához használja a következő parancsot:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

További információt az [OpenSSL-dokumentációban](https://www.openssl.org/docs/) találhat.

### <a name="connection-issues"></a>Kapcsolódási problémák

Egyes műveletek az alábbi üzenetet hozhatják létre:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Ellenőrizze, hogy a megadott fürtvégpont elérhető-e és figyel-e. Továbbá ellenőrizze, hogy a Service Fabric Explorer felhasználói felülete elérhető-e a gazdagépen és a porton. A végpont frissítéséhez használja az `az sf cluster select` parancsot.

### <a name="detailed-logs"></a>Részletes naplók

A részletes naplók gyakran hasznosak a hibák javításához vagy jelentéséhez. Az Azure CLI kínál egy globális `--debug` jelzőt, amely növeli a naplófájlok részletességét.

### <a name="command-help-and-syntax"></a>Parancsok súgója és szintaxisa

A Service Fabric-parancsok ugyanazokat a szabályokat követik, mint az Azure CLI. Ha segítségre van szüksége egy bizonyos paranccsal vagy parancscsoporttal kapcsolatban, használja a `-h` jelzőt:

```azurecli
az sf application -h
```

Egy további példa:

```azurecli
az sf application create -h
```

