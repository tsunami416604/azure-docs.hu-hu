---
title: "Az Azure Service Fabric parancssori felület (sfctl) használatának első lépései"
description: "Ez a dokumentum ismerteti az Azure Service Fabric parancssori felület használatát. Megtudhatja, hogyan csatlakozhat fürtökhöz, és hogyan kezelheti alkalmazásait."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 5ce9adf6c82e3a5521883c5de1e0689d5bf0d94e
ms.contentlocale: hu-hu
ms.lasthandoff: 08/24/2017

---
# <a name="azure-service-fabric-command-line"></a>Azure Service Fabric-parancssor

Az Azure Service Fabric parancssori felület (sfctl) egy parancssori eszköz az Azure Service Fabric-entitások használatához és kezeléséhez. Az Sfctl Windows- vagy Linux-fürtökön is használható. Az Sfctl bármilyen platformon fut, amely támogatja a Pythont.

## <a name="prerequisites"></a>Előfeltételek

A telepítés előtt győződjön meg arról, hogy a környezetben a Python és a pip is telepítve van. További információkért olvassa el a [pip rövid útmutatót dokumentációját](https://pip.pypa.io/en/latest/quickstart/) és a hivatalos [Python-telepítési dokumentációt](https://wiki.python.org/moin/BeginnersGuide/Download).

A Python 2.7-es és 3.6-os verziója is támogatott, de javasoljuk a Python 3.6 használatát.

## <a name="install"></a>Telepítés

Az Azure Service Fabric parancssori felület (sfctl) egy Python-csomagként van csomagolva. A legfrissebb verzió telepítéséhez futtassa a következőt:

```bash
pip install sfctl
```

A telepítés után az `sfctl -h` futtatásával kaphat további információkat az elérhető parancsokról.

## <a name="cli-syntax"></a>A parancssori felület szintaxisa

A parancsok előtt mindig a következő előtag áll: `sfctl`. Az összes használható paranccsal kapcsolatos általános információkért használja az `sfctl -h` parancsot. Ha egyetlen paranccsal kapcsolatban van szüksége segítségre, használja az `sfctl <command> -h` parancsot.

A parancsok ismételhető szerkezetűek, és a parancs célja mindig megelőzi a műveletet:

```azurecli
sfctl <object> <action>
```

Ebben a példában az `<object>` az `<action>` művelet célpontja.

## <a name="select-a-cluster"></a>Fürt kiválasztása

A műveletek végrehajtása előtt ki kell választania egy fürtöt, amelyhez csatlakozni kíván. A következő parancs futtatásával például kiválaszthatja a `testcluster.com` nevű fürtöt, és csatlakozhat hozzá.

> [!WARNING]
> Éles környezetben ne használjon nem védett Service Fabric-fürtöket.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

A fürt végpontját `http` vagy `https` előtaggal kell ellátni. Tartalmaznia kell a HTTP-átjáróhoz tartozó portot. A port és a cím megegyezik a Service Fabric Explorer URL-címével.

A tanúsítvánnyal védett fürtök esetében megadhat egy PEM-kódolású tanúsítványt. Ez a tanúsítvány megadható egyetlen fájlként vagy egy tanúsítványból és egy kulcsból álló párként.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

További információ: [Csatlakozás védett Azure Service Fabric-fürthöz](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Alapszintű műveletek

A fürt kapcsolatadatai több sfctl-munkamenetben is megmaradnak. Egy Service Fabric-fürt kiválasztása után bármilyen Service Fabric-parancsot futtathat a fürtön.

A Service Fabric-fürt állapotának lekérdezéséhez például használja az alábbi parancsot:

```azurecli
sfctl cluster health
```

A parancs az alábbi kimenetet eredményezi:

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

Néhány javaslat és tipp a gyakori problémák megoldásához.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Tanúsítvány konvertálása PFX formátumról PEM formátumra

A Service Fabric parancssori felület PEM- (.pem kiterjesztésű) fájlok formájában támogatja az ügyféloldali tanúsítványokat. Ha Windows rendszerből származó PFX-fájlokat lát, át kell alakítania a tanúsítványokat PEM formátumba. A PFX-fájlok PEM-fájlokká történő konvertálásához használja a következő parancsot:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

További információt az [OpenSSL-dokumentációban](https://www.openssl.org/docs/) találhat.

### <a name="connection-issues"></a>Kapcsolódási problémák

Egyes műveletek az alábbi üzenetet hozhatják létre:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Ellenőrizze, hogy a megadott fürtvégpont elérhető-e és figyel-e. Továbbá ellenőrizze, hogy a Service Fabric Explorer felhasználói felülete elérhető-e a gazdagépen és a porton. A végpont frissítéséhez használja az `sfctl cluster select` parancsot.

### <a name="detailed-logs"></a>Részletes naplók

A részletes naplók gyakran hasznosak a hibák javításához vagy jelentéséhez. Létezik egy globális `--debug` jelző, amely növeli a naplófájlok részletességét.

### <a name="command-help-and-syntax"></a>Parancsok súgója és szintaxisa

Ha segítségre van szüksége egy bizonyos paranccsal vagy parancscsoporttal kapcsolatban, használja a `-h` jelzőt:

```azurecli
sfctl application -h
```

Egy másik példa:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Következő lépések

* [Alkalmazás üzembe helyezése az Azure Service Fabric parancssori felülettel](service-fabric-application-lifecycle-sfctl.md)
* [A Service Fabric használatának első lépései Linuxon](service-fabric-get-started-linux.md)

