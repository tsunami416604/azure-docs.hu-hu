---
title: "A Service Fabric első lépései az Azure CLI 2.0 használatával"
description: "A Service Fabric parancsmodul használata az Azure CLI 2.0 verziójában többek között fürt csatlakoztatására és alkalmazások kezelésére"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: hu-hu
ms.lasthandoff: 07/01/2017

---
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

# A Service Fabric és az Azure CLI 2.0

Az új Azure CLI 2.0 már Service Fabric fürtök kezelésére szolgáló parancsokat is tartalmaz. Ez a dokumentáció az Azure CLI használatba vételének lépéseit ismerteti.

<a id="install-azure-cli-20" class="xliff"></a>

## Az Azure CLI 2.0 telepítése

Az Azure CLI már Service Fabric-fürtök kezelésére és a velük való interakcióra szolgáló parancsokat is tartalmaz. A legfrissebb Azure CLI beszerzéséhez követheti a [szabványos telepítési eljárást](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

További információt talál az [Azure CLI 2.0 dokumentációjában](https://docs.microsoft.com/en-us/cli/azure/overview)

<a id="cli-syntax" class="xliff"></a>

## A parancssori felület szintaxisa

Az Azure parancssori felületén minden Azure Service Fabric-parancs az `az sf` előtaggal van ellátva. Az használható parancsokról további általános információhoz juthat az `az sf -h` parancs futtatásával. Az `az sf <command> -h` paranccsal részletesebb súgót kap egy adott parancsról.

A parancssori felület Azure Service Fabric-parancsai egy elnevezési mintát követnek

```azurecli
az sf <object> <action>
```

Itt `<object>` a `<action>` művelet tárgya.

<a id="selecting-a-cluster" class="xliff"></a>

## Fürt kiválasztása

Mielőtt bármilyen műveletet végrehajthatna, ki kell választania a fürtöt, amelyhez kapcsolódik. Az alábbi kódrészlet például egy nem biztonságos fürthöz csatlakozik.

> [!WARNING]
> Termelési környezetben ne használjon nem biztonságos Service Fabric-fürtöket

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

A fürt végpont előtagja `http` vagy `https` lehet és meg kell adni hozzá a HTTP-átjáró portszámát. Ez a port és a cím ugyanaz, mint a Service Fabric Explorer-beli URL-cím.

Tanúsítvánnyal biztosított fürtök esetén nem titkosított `pem` vagy `crt` és `key` fájlok is támogatottak.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

További információt talál a [biztonságos fürtökhöz való csatlakozás részletes dokumentációjában](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> A select parancs nem hajt végre lekérdezést a visszatéréséig. Egy fürt helyes megadásának ellenőrzéséhez futtatható parancs például `az sf cluster health`. Győződjön meg róla, hogy a parancs hibaüzenet nélkül fut le.

<a id="performing-basic-operations" class="xliff"></a>

## Alapszintű műveletek végrehajtása

A fürtkapcsolati adatok megőrződnek különböző Azure CLI-munkamenetek között. Egy Service Fabric-fürt kijelölése után már bármilyen Service Fabric parancs futtatható.

A Service Fabric-fürt állapotának kiolvasásához például futtassa a következő parancsot

```azurecli
az sf cluster health
```

A parancs eredménye az alábbi kimenet, feltéve, hogy az Azure CLI konfigurációjában JSON-kimenet van beállítva

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

<a id="tips-and-faq" class="xliff"></a>

## Tippek és gyakran ismételt kérdések

Az itt következő információk hasznosak lehetnek az Azure parancssori felületen használt Service Fabric-parancsokkal kapcsolatos problémák felmerülése esetén

<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

### Tanúsítvány konvertálása PFX-ről PEM formátumra

Az Azure CLI az ügyféloldali tanúsítványokat PEM (`.pem` kiterjesztésű) fájlokként támogatja. Ha Windows rendszerből származó PFX-fájlokat használ, akkor ezeket a tanúsítványokat PEM formátumra kell konvertálnia. PFX-fájl a következő paranccsal konvertálható PEM fájllá:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Részletes információkat az [OpenSSL-dokumentációban](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html) talál.

<a id="connection-issues" class="xliff"></a>

### Kapcsolódási problémák

Műveletek végrehajtása során felmerülhet a következő hiba:

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Ebben az esetben ellenőrizze újra, hogy a megadott fürtvégpont elérhető és figyel. Ellenőrizze azt is, hogy a Service Fabric Explorer felhasználói felülete elérhető az adott állomáson és porton. A végpont frissítéséhez használja az `az sf cluster select` parancsot.

<a id="getting-detailed-logs" class="xliff"></a>

### Részletes naplók lekérése

Hibakeresés vagy probléma bejelentése esetén hasznos mellékelni a részletes naplókat. Az Azure CLI része egy globális `--debug` kapcsoló, amely részletesebb naplókat eredményez.

<a id="command-help-and-syntax" class="xliff"></a>

### Parancsok súgója és szintaxisa

A Service Fabric-parancsok az Azure parancssori felületéével azonos konvenciókat követnek. A `-h` kapcsolóval részletes súgót kaphat egy adott parancsról vagy parancscsoportról. Példa:

```azurecli
az sf application -h
```

vagy

```azurecli
az sf application create -h
```

