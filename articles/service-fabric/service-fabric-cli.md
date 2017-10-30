---
title: "Az Azure Service Fabric parancssori felület használatának első lépései"
description: "Ez a dokumentum ismerteti az Azure Service Fabric parancssori felület használatát. Megtudhatja, hogyan csatlakozhat fürtökhöz, és hogyan kezelheti alkalmazásait."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 10/20/2017
ms.author: edwardsa
ms.openlocfilehash: d24c7618c5d53cfe2871d596bfc0fe2cadd5940a
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2017
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric parancssori felület

Az Azure Service Fabric parancssori felület (CLI) egy parancssori eszköz a Service Fabric-entitások használatához és kezeléséhez. A Service Fabric parancssori felület Windows- vagy Linux-fürtökön is használható. A Service Fabric parancssori felület bármilyen platformon fut, amely támogatja a Pythont.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Előfeltételek

A telepítés előtt győződjön meg arról, hogy a környezetben a Python és a pip is telepítve van. További információkért olvassa el a [pip rövid útmutató dokumentációját](https://pip.pypa.io/en/latest/quickstart/) és a hivatalos [Python-telepítési dokumentációt](https://wiki.python.org/moin/BeginnersGuide/Download).

A parancssori felület a Python 2.7-es, 3.5-ös és 3.6-os verzióit támogatja. A Python 3.6-os az ajánlott verzió, mivel a Python 2.7 támogatottsága hamarosan véget ér.

### <a name="service-fabric-target-runtime"></a>A Service Fabric cél futtatókörnyezete

A Service Fabric parancssori felület a Service Fabric SDK legfrissebb futtatókörnyezet-verzióját hivatott támogatni. A következő táblázat segítségével határozhatja meg, hogy melyik parancssori felületet telepítse:

| Parancssori felület verziója   | támogatott futtatókörnyezet-verzió |
|---------------|---------------------------|
| Legfrissebb (~=3)  | Legfrissebb (~=6.0)            |
| 1.1.0         | 5.6, 5.7                  |

A parancssori felület telepítendő célverzióját úgy is megadhatja, ha a `pip install` parancsot kiegészíti a `==<version>` kifejezéssel. Például az 1.1.0-s verzió esetén a szintaxis a következő:

```
pip install -I sfctl==1.1.0
```

Szükség esetén cserélje a `pip install` parancsot a korábban említett parancsra.

A Service Fabric parancssori felület kiadásaival kapcsolatos további információért tekintse meg a [GitHub-dokumentációt](https://github.com/Azure/service-fabric-cli/releases).

## <a name="install-pip-python-and-the-service-fabric-cli"></a>A pip, a Python és a Service Fabric parancssori felület telepítése

Sokféleképpen telepítheti a pipet és a Pythont a platformra. Itt találja a Python 3 és a pip gyors telepítésének néhány lépését a főbb operációs rendszerekhez.

### <a name="windows"></a>Windows

Windows 10, Windows Server 2016 és Windows Server 2012 R2 esetén használja a normál telepítési utasításokat. A Python telepítője alapértelmezés szerint a pipet is telepíti.

1. Látogasson el a hivatalos [Python letöltési oldalra](https://www.python.org/downloads/), és töltse le a Python 3.6 legújabb kiadását

2. Indítsa el a telepítőt.

3. A telepítő ablakának alján jelölje be az **Add Python 3.6 to PATH** (Python 3.6 hozzáadása az ELÉRÉSI ÚTON) lehetőséget.

4. Válassza az **Install Now** (Telepítés) lehetőséget, és fejezze be a telepítést.

Most megnyithat egy új parancsablakot, és lekérheti a Python és a pip verzióját.

```bat
python --version
pip --version
```

Ezután futtassa a következő parancsot a Service Fabric parancssori felületének telepítéséhez:

```bat
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Ubuntu és a Linux Windows alrendszere

A Service Fabric parancssori felületének telepítéséhez futtassa a következő parancsokat:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

Ezután a telepítést a következő paranccsal tesztelheti:

```bash
sfctl -h
```

Ha egy „parancs nem található” hiba jelenik meg, például a következő:

`sfctl: command not found`

Ellenőrizze, hogy a `~/.local/bin` elérhető-e a `$PATH` útvonalon:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```

Ha a Linux Windows alrendszerén végzett telepítés nem megfelelő mappaengedélyek miatt meghiúsul, szükség lehet a telepítés újbóli megkísérlésére emelt szintű engedélyekkel:

```bash
sudo pip3 install sfctl
```

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

MacOS rendszeren javasoljuk, hogy a [HomeBrew csomagkezelőt](https://brew.sh) használja. Ha a HomeBrew még nincs telepítve, a következő parancs futtatásával telepítse:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Ezután a terminálról telepítse a Python 3.6-ot, a pipet és a Service Fabric parancssori felületet a következő parancsok futtatásával:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>A parancssori felület szintaxisa

A parancsok előtagja mindig a következő: `sfctl`. Az összes használható paranccsal kapcsolatos általános információkért használja az `sfctl -h` parancsot. Ha egyetlen paranccsal kapcsolatban van szüksége segítségre, használja az `sfctl <command> -h` parancsot.

A parancsok ismételhető szerkezetűek, és a parancs célja mindig megelőzi a műveletet.

```azurecli
sfctl <object> <action>
```

Ebben a példában az `<object>` az `<action>` művelet célpontja.

## <a name="select-a-cluster"></a>Fürt kiválasztása

A műveletek végrehajtása előtt ki kell választania egy fürtöt, amelyhez csatlakozni kíván. A következő parancs futtatásával például kiválaszthatja a `testcluster.com` nevű fürtöt, és csatlakozhat hozzá:

> [!WARNING]
> Éles környezetben ne használjon nem védett Service Fabric-fürtöket.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

A fürt végpontját `http` vagy `https` előtaggal kell ellátni. Tartalmaznia kell a HTTP-átjáróhoz tartozó portot. A port és a cím megegyezik a Service Fabric Explorer URL-címével.

A tanúsítvánnyal védett fürtök esetében megadhat egy PEM-kódolású tanúsítványt. Ez a tanúsítvány megadható egyetlen fájlként vagy egy tanúsítványból és egy kulcsból álló párként. Ha ez az önaláírt tanúsítvány nem rendelkezik hitelesítésszolgáltatói aláírással, a `--no-verify` beállítás átadásával kerülheti meg a hitelesítésszolgáltatói ellenőrzést.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

További információ: [Csatlakozás védett Azure Service Fabric-fürthöz](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Alapszintű műveletek

A fürt kapcsolatadatai több Service Fabric CLI-munkamenetben is megmaradnak. Egy Service Fabric-fürt kiválasztása után bármilyen Service Fabric-parancsot futtathat a fürtön.

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

Itt talál néhány javaslatot és tippet a gyakori problémák megoldásához.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Tanúsítvány konvertálása PFX formátumról PEM formátumra

A Service Fabric parancssori felület PEM- (.pem kiterjesztésű) fájlok formájában támogatja az ügyféloldali tanúsítványokat. Ha Windows rendszerből származó PFX-fájlokat lát, át kell alakítania a tanúsítványokat PEM formátumba. A PFX-fájlok PEM-fájlokká történő konvertálásához használja a következő parancsot:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Ehhez hasonlóan a PEM-fájlok PFX-fájlokká történő konvertálásához a következő parancsot használhatja (itt nem kell jelszót megadnia):

```bash
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

További információt az [OpenSSL-dokumentációban](https://www.openssl.org/docs/) találhat.

### <a name="connection-problems"></a>Csatlakozási problémák

Egyes műveletek az alábbi üzenetet hozhatják létre:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Ellenőrizze, hogy a megadott fürtvégpont elérhető-e és figyel-e. Továbbá ellenőrizze, hogy a Service Fabric Explorer felhasználói felülete elérhető-e a gazdagépen és a porton. A végpont frissítéséhez használja az `sfctl cluster select` parancsot.

### <a name="detailed-logs"></a>Részletes naplók

A részletes naplók gyakran hasznosak a hibák javításához vagy a problémák jelentéséhez. Egy globális `--debug` jelző növeli a naplófájlok részletességét.

### <a name="command-help-and-syntax"></a>Parancsok súgója és szintaxisa

Ha segítségre van szüksége egy bizonyos paranccsal vagy parancscsoporttal kapcsolatban, használja a `-h` jelzőt.

```azurecli
sfctl application -h
```

Egy másik példa:

```azurecli
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>A Service Fabric parancssori felület frissítése 

A Service Fabric parancssori felületének frissítéséhez futtassa a következő parancsokat (az eredeti telepítéskor használt paramétertől függően a `pip` helyett írjon be `pip3`-at):

```bash
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Következő lépések

* [Alkalmazás üzembe helyezése az Azure Service Fabric parancssori felülettel](service-fabric-application-lifecycle-sfctl.md)
* [A Service Fabric használatának első lépései Linuxon](service-fabric-get-started-linux.md)
