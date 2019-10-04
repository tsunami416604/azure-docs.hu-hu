---
title: Eszközbiztonság – Azure IoT Edge-tanúsítványok |} A Microsoft Docs
description: Az Azure IoT Edge tanúsítványt használ, eszközök, a modulok és a levéleszközök érvényesítéséhez, és a kettő közötti biztonságos kapcsolatokat hozhat létre.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0aa70e591c7aac977fe13ed638f8ee56b88e4bd1
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982912"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Az Azure IoT Edge-tanúsítvány felhasználási részletek

IoT Edge tanúsítványokat használ a modulokhoz és az alsóbb rétegbeli IoT eszközökhöz, hogy ellenőrizze az [IoT Edge hub](iot-edge-runtime.md#iot-edge-hub) Runtime-modul identitását és törvényességét, amelyhez csatlakoznak. Ezeknek az ellenőrzéseknek engedélyezze a TLS (transport layer security) biztonságos kapcsolatot a futtatókörnyezet, a modulok és az IoT-eszközök között. Például magát az IoT Hub IoT Edge szükséges egy biztonságos és aktiválásához IoT-kapcsolat titkosítva (vagy levél) eszközök és az IoT Edge-modulok. A biztonságos TLS-kapcsolat létrehozásához az IoT Edge hub modul egy kiszolgálói tanúsítványláncot jeleníti meg az ügyfelek csatlakoztatásához, hogy azok személyazonosságát ellenőrizzék.

Ez a cikk azt ismerteti, hogy IoT Edge tanúsítványok hogyan működhetnek üzemi, fejlesztési és tesztelési helyzetekben. A parancsfájlok különböző (Powershell és a bash), amelyek fogalmak ugyanazok, Linux és a Windows szolgáltatásai között.

## <a name="iot-edge-certificates"></a>IoT Edge-tanúsítványok

A gyártók általában nem a IoT Edge eszköz végfelhasználói. Előfordulhat, hogy az egyetlen kapcsolat a kettő között, amikor a végfelhasználó vagy a kezelő a gyártó által készített általános eszközt vásárol. Máskor a gyártó szerződéses úton működik, hogy az operátor nevében hozzon létre egy egyéni eszközt. Az IoT Edge-tanúsítvány tervezési próbál meg mindkét forgatókönyvet figyelembe véve.

Az alábbi ábra tanúsítványok IoT Edge használatának. A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány és az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa között nulla, egy vagy több köztes aláíró tanúsítvány lehet az érintett entitások számától függően. Itt egy esetet mutatunk be.

![Tipikus tanúsítvány kapcsolatokat bemutató ábra](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Hitelesítésszolgáltató

A hitelesítésszolgáltató vagy a "CA" röviden, nem minden entitás, amely a digitális tanúsítványokat. Egy hitelesítésszolgáltató megbízható harmadik fél tulajdonosa, és a tanúsítvány a fogadó között funkcionál. Egy digitális tanúsítvány igazolja, a tulajdonjogát, a fogadó a tanúsítvány nyilvános kulcsa. A tanúsítvány megbízhatósági láncában egy főtanúsítványhoz, amely a szervezet által kiállított összes tanúsítvány megbízhatóságának alapját kezdetben kiállításával működik. Ezt követően a tulajdonos használatával a legfelső szintű tanúsítvány további köztes tanúsítványokat ("levél" tanúsítványokat).

### <a name="root-ca-certificate"></a>Legfelső szintű hitelesítésszolgáltató tanúsítványa

A legfelső szintű Hitelesítésszolgáltatói tanúsítványt a teljes folyamat bizalomforrásánál. Éles környezetben Ez a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány általában megbízható kereskedelmi hitelesítésszolgáltatótól vásárolható, például Baltimore, VeriSign vagy DigiCert. Ha teljes körű vezérléssel rendelkezik a IoT Edge eszközökhöz csatlakozó eszközök felett, vállalati szintű hitelesítésszolgáltató is használható. Mindkét esetben a IoT Edge hub teljes tanúsítványlánc felfelé görget, így a Leaf IoT-eszközöknek meg kell bízniuk a főtanúsítványban. A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a megbízható legfelső szintű hitelesítésszolgáltató tárolóban tárolhatja, vagy megadhatja a tanúsítvány részleteit az alkalmazás kódjában.

### <a name="intermediate-certificates"></a>Köztes tanúsítványok

Egy tipikus gyártási folyamat létrehozásához a biztonságos eszközök legfelső szintű Hitelesítésszolgáltatói tanúsítványok ritkán használt közvetlenül, elsősorban kiszivárgását vagy felfedését kockázata miatt. A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehoz és digitálisan aláír egy vagy több köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt. Előfordulhat, hogy csak akkor egyetlen, vagy előfordulhat, hogy ezek a köztes tanúsítványok láncolata. Köztes tanúsítványok láncolatától igénylő forgatókönyvek a következők:

* Egy gyártó belüli szervezeti egységek hierarchiájának.

* Több vállalatot tárolókonfigurációhoz részt az éles környezetben, az eszköz.

* Egy ügyfél vásárlása a legfelső szintű hitelesítésszolgáltató és a egy aláíró tanúsítványt a gyártó származtatás aláírásához az eszközök, győződjön meg arról, az adott ügyfél nevében.

Minden esetben a gyártó egy köztes Hitelesítésszolgáltatói tanúsítványt a lánc végén aláírásához használja az eszköz Hitelesítésszolgáltatói tanúsítvány elhelyezni a záró eszközön. Általában ezek a köztes tanúsítványok szorosan a lapkagyártó műhelyben, védett. Azok a szigorú folyamatai, a fizikai és a használatuk elektronikus mennek keresztül.

### <a name="device-ca-certificate"></a>Eszköz Hitelesítésszolgáltatói tanúsítvány

Az eszköz Hitelesítésszolgáltatói tanúsítvány által létrehozott és a folyamat utolsó köztes Hitelesítésszolgáltatói tanúsítvány aláírásával. Ez a tanúsítvány a IoT Edge eszközre van telepítve, lehetőleg biztonságos tárolóban, például hardveres biztonsági modulban (HSM). Ezenkívül egy eszköz Hitelesítésszolgáltatói tanúsítvány egyedileg azonosítja az IoT Edge-eszköz. IoT Edge esetén az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa más tanúsítványokat is kiadhat. Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa például az eszközök az [Azure IoT Device kiépítési szolgáltatásba](../iot-dps/about-iot-dps.md)való hitelesítéséhez használt levél-eszköz tanúsítványokat bocsát ki.

### <a name="iot-edge-workload-ca"></a>Az IoT Edge munkaterhelés hitelesítésszolgáltató

A [IoT Edge Security Manager](iot-edge-security-manager.md) létrehozza a munkaterhelés hitelesítésszolgáltatói tanúsítványát, amely a folyamat "operátor" oldalának első indításakor IoT Edge először elindul. Ez a tanúsítvány által létrehozott és az "eszköz CA tanúsítványát" alá. Ez a tanúsítvány, amely egy másik köztes aláíró tanúsítványt, hozza létre, és bármely más, az IoT Edge-futtatókörnyezete által használt tanúsítványok aláírására szolgál. Napjainkban ez elsősorban az IoT Edge hub-kiszolgáló tanúsítványa, amelyet a következő szakaszban tárgyaltak, de a jövőben más tanúsítványokat is tartalmazhatnak IoT Edge összetevők hitelesítéséhez.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge hub-kiszolgáló tanúsítványa

Az IoT Edge hub-kiszolgáló tanúsítványa az a tényleges tanúsítvány, amelyet a rendszer a leküldési eszközök és modulok számára biztosít a hitelesítéshez a IoT Edge által igényelt TLS-kapcsolatok létrehozása során. Ez a tanúsítvány-aláíró tanúsítványok, akár a legfelső szintű Hitelesítésszolgáltatói tanúsítványt, amely meg kell bíznia a levél IoT-eszköz létrehozásához használt a teljes tanúsítványlánccal mutat be. Ha a IoT Edge Security Manager létrehozta, akkor ennek az IoT Edge hub-tanúsítványnak a köznapi neve (CN) a config. YAML fájlban a "hostname" tulajdonságra van állítva, a kisbetűvé alakítás után. Ez a keverhetők össze az IoT Edge gyakori forrását.

## <a name="production-implications"></a>Gyártás – következmények

Egy ésszerű kérdés lehet, hogy "Miért IoT Edge kell a"számítási feladat CA"extra tanúsítványt? Nem tudta használni az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát az IoT Edge hub-kiszolgáló tanúsítványának közvetlen létrehozásához? ". Technikailag sikerült. Azonban a a "számítási feladat" köztes tanúsítvány célja, hogy az eszköz gyártója és az eszköz operátor közötti aggályokat külön. Képzelje el egy forgatókönyvet, ahol IoT Edge-eszköz értékesített vagy egy ügyfél a forráscsomópontról a másikra. Ekkor érdemes a eszköz Hitelesítésszolgáltatói tanúsítvány, nem módosítható a gyártó által biztosított. Azonban a "számítási feladat" tanúsítványok adott működésével, az eszköz törlése kell, és újra létre kell hozni az új központi telepítés kapcsán.

Mivel a gyártási és üzemeltetési folyamatok el vannak különítve, a következő szempontokat kell figyelembe venni a termelési eszközök előkészítésekor:

* Az ügyféltanúsítvány-alapú folyamatokhoz a legfelső szintű Hitelesítésszolgáltatói tanúsítvány és az összes köztes Hitelesítésszolgáltatói tanúsítványt kell biztonságossá tehetők és az IoT Edge-eszköz bevezetést a teljes folyamat során. Az IoT Edge-eszköz gyártója hatékony folyamatoknak kell rendelkeznie azon megfelelő tároló és a köztes tanúsítványok használatát. Emellett az eszköz Hitelesítésszolgáltatói tanúsítványt kell tárolni, amennyire csak lehetséges, magán az eszközön, lehetőleg egy hardveres biztonsági modul biztonságos tárolóként.

* Az IoT Edge hub-kiszolgáló tanúsítványát IoT Edge hub mutatja be a csatlakoztatott ügyféleszközök és modulok számára. Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának köznapi neve (CN) **nem** egyezhet meg a "hostname" értékkel, amelyet a IoT Edge-eszköz config. YAML használ. Az ügyfelek által a IoT Edgehoz való kapcsolódáshoz használt név (például a kapcsolati karakterlánc GatewayHostName paraméterén vagy a MQTT CSATLAKOZTATÁSi parancsán keresztül) **nem egyezhet** meg az eszköz hitelesítésszolgáltatói tanúsítványában használt köznapi névvel. Ez a korlátozás azért van, mert az IoT Edge hub a teljes tanúsítványláncot mutatja be az ügyfelek általi ellenőrzéshez. Ha a IoT Edge hub-kiszolgáló tanúsítványa és az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa azonos KN-val rendelkezik, a rendszer egy ellenőrzési hurkot kap, és a tanúsítvány érvénytelenné válik.

* Mivel az IoT Edge biztonsági démon az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát használja a végső IoT Edge tanúsítványok előállításához, akkor magát aláíró tanúsítványnak kell lennie, ami azt jelenti, hogy tanúsítvány-aláírási képességekkel rendelkezik. A "v3 alapkorlátozások CA: true" alkalmazása az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa automatikusan beállítja a szükséges kulcshasználat tulajdonságokat.

>[!Tip]
> Ha Ön már mintajelentéshez IoT Edge beállítása "kényelmi szkriptjeit" használatával fejlesztési-Tesztelési forgatókönyvek esetében transzparens átjáróként (lásd a következő szakaszban), és ugyanazt az állomásnevet használja, az eszköz Hitelesítésszolgáltatói tanúsítvány létrehozásakor, az állomásnév a config.yaml, mint , miért működött értéksorokkal. Annak érdekében, hogy a fejlesztői élmény egyszerűsítése érdekében a felhasználók kényelme érdekében parancsfájlok hozzáfűzi a ".ca" a parancsprogramhoz adja át a név végén. Így például, "mygateway" mindkét az eszköz nevét a szkriptek és config.yaml az állomásnevet használja, ha a korábbi lesz kapcsolva mygateway.ca be előtt használt a CN, az eszköz Hitelesítésszolgáltatói tanúsítvány.

## <a name="devtest-implications"></a>Fejlesztés/tesztelés – következmények

A fejlesztés megkönnyítése érdekében, és tesztelési forgatókönyvek, a Microsoft biztosít [kényelmi parancsfájlok](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) : az IoT Edge-hez nem éles tanúsítványok létrehozása a transzparens átjáró használata esetén a. A parancsfájlok működésének példákért lásd: [a transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md).

Ezek a szkriptek a tanúsítvány láncot szerkezetűek ebben a cikkben ismertetett tanúsítványok előállításához. Az alábbi parancsokat a "legfelső szintű Hitelesítésszolgáltatói tanúsítvány" és a egy egyetlen "köztes Hitelesítésszolgáltatói tanúsítványt" elő.

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

Hasonlóképpen ezek a parancsok készítése az "eszköz CA tanúsítványát".

```bash
./certGen.sh create_edge_device_ca_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDeviceCA "<gateway device name>"
```

* Az adott parancsfájlba átadott **\> átjáró-eszköznév nem egyezhet meg a config. YAML "hostname" paraméterével. \<** A parancsfájlok segítségével elkerülhetők a hibák, ha a ". ca" karakterláncot az **\<átjáró-eszköz nevére\>** fűzi, hogy a név ne legyen ütközés abban az esetben, ha egy felhasználó a két helyen azonos névvel állítja be IoT Edge. Azonban érdemes elkerülni ugyanazt a nevet.

>[!Tip]
> Csatlakozhat az eszköz IoT "levél" eszközök és az IoT Edge segítségével a c IoT eszközoldali SDK-t használó alkalmazásokat, hozzá kell adnia a választható GatewayHostName paraméter be az eszköz kapcsolati karakterláncának végén. Az Edge Hub kiszolgálói tanúsítvány jön létre, amikor, a gazdanevet erről config.yaml alacsonyabb kisbetűsek verzióján alapul, ezért a nevek egyezés és a TLS-tanúsítvány ellenőrzése sikeres, kell megadnia a GatewayHostName paraméter kisbetű szerepel.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>A tanúsítvány-hierarchia IoT Edge – példa

A tanúsítvány elérési útjának példáján mutatja be, az alábbi képernyőképen az IoT Edge-eszköz beállítása a transzparens átjáróként működő van. Az OpenSSL az IoT Edge hubhoz való kapcsolódásra, a tanúsítványok érvényesítésére és kiírására szolgál.

![A tanúsítvány-hierarchia minden szintjére képernyőképe](./media/iot-edge-certs/iotedge-cert-chain.png)

Láthatja, hogy megjelenjen a képernyőképen tanúsítvány mélysége hierarchiáját:

| Legfelső szintű hitelesítésszolgáltató tanúsítványa         | Az Azure IoT Hub Hitelesítésszolgáltatói tanúsítvány teszt csak                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Köztes Hitelesítésszolgáltatói tanúsítvány | Az Azure IoT Hub köztes tanúsítvány csak tesztelési                                                                 |
| Eszköz Hitelesítésszolgáltatói tanúsítvány       | iotgateway.CA ("iotgateway" lett átadva a állomásnévként < átjáró > kényelem és parancsfájlok)      |
| Számítási feladatok Hitelesítésszolgáltatói tanúsítvány     | iotedge munkaterhelés hitelesítésszolgáltató                                                                                       |
| IoT Edge hub-kiszolgáló tanúsítványa | (az állomásnév megegyezik a config.yaml) iotedgegw.local                                                |

## <a name="next-steps"></a>További lépések

[Az Azure IoT Edge-modulok megismerése](iot-edge-modules.md)

[A transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md)