---
title: Az Azure IoT Edge-tanúsítványok ismertetése |} A Microsoft Docs
description: További tudnivalók az Azure IoT Edge-tanúsítványok és a használatuk.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b1b8c660dfd6ea6890a940a97f5ff1ec1617c28d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963385"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Az Azure IoT Edge-tanúsítvány felhasználási részletek

IoT Edge számára tanúsítványokra van szükség a modulok és az alsóbb rétegbeli IoT-eszközök identitás- és eredetiségének ellenőrzése a [Edge hubot](iot-edge-runtime.md#iot-edge-hub) csatlakozik, futásidőben hívható modulra. Ezeknek az ellenőrzéseknek engedélyezze a TLS (transport layer security) biztonságos kapcsolatot a futtatókörnyezet, a modulok és az IoT-eszközök között. Például magát az IoT Hub IoT Edge szükséges egy biztonságos és aktiválásához IoT-kapcsolat titkosítva (vagy levél) eszközök és az IoT Edge-modulok. A TLS biztonságos kapcsolatot létesíteni, az Edge Hub modul tanúsítványlánc kapcsolódó ügyfelek számára a személyazonosságát mutat be egy kiszolgálót.

IoT Edge használata a tanúsítványok bármilyen zavart kiürítéséhez, ez a cikk bemutatja, hogyan IoT Edge-tanúsítványok is használhatók ipari méretekben forgatókönyvek, valamint fejlesztési és tesztelési forgatókönyvekhez. A parancsfájlok különböző (Powershell és a bash), amelyek fogalmak ugyanazok, Linux és a Windows szolgáltatásai között.

## <a name="iot-edge-certificates"></a>IoT Edge-tanúsítványok

A legtöbb esetben a gyártók a végfelhasználók Edge-eszköz, független entitások. Néha a gyártó és operátor csak kapcsolatának vásárlása egy kereskedelmi forgalomban elérhető Edge-eszköz. Egy másik eset, a gyártó hozhat létre az Edge-eszköz nevében az operátor szerződés alapján működik. Az IoT Edge-tanúsítvány tervezési próbál meg mindkét forgatókönyvet figyelembe véve.

Az alábbi ábra tanúsítványok IoT Edge használatának. Nulla, egy vagy több köztes aláíró tanúsítványok, a legfelső szintű Hitelesítésszolgáltatói tanúsítvány és az eszköz hitelesítésszolgáltató tanúsítványa között lehet. Az érintett entitások számától függően itt megmutatjuk, egyetlen esetet tartalmaz.

![Tipikus tanúsítvány kapcsolatokat bemutató ábra](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Hitelesítésszolgáltató

A hitelesítésszolgáltató vagy a "CA" röviden, nem minden entitás, amely a digitális tanúsítványokat. Egy hitelesítésszolgáltató megbízható harmadik fél tulajdonosa, és a tanúsítvány a fogadó között funkcionál. Egy digitális tanúsítvány igazolja, a tulajdonjogát, a fogadó a tanúsítvány nyilvános kulcsa. A tanúsítvány megbízhatósági láncában egy főtanúsítványhoz, amely a szervezet által kiállított összes tanúsítvány megbízhatóságának alapját kezdetben kiállításával működik. Ezt követően a tulajdonos használatával a legfelső szintű tanúsítvány további köztes tanúsítványokat ("levél" tanúsítványokat).

### <a name="root-ca-certificate"></a>Legfelső szintű hitelesítésszolgáltató tanúsítványa

A legfelső szintű Hitelesítésszolgáltatói tanúsítványt a teljes folyamat bizalomforrásánál. Éles forgatókönyvekben általában hozom Baltimore, a Verisign, a DigiCert és egyéb hasonló megbízható kereskedelmi hitelesítésszolgáltatótól származó Hitelesítésszolgáltatói tanúsítvány lehet. Kell rendelkeznie az Edge (ök) höz kapcsolódó eszközök teljes körű, lehetőség a vállalati szintű hitelesítésszolgáltató használata. Mindkét esetben a teljes tanúsítványlánccal fel az Edge Hub az összesíti, így a levél IoT-eszközök meg kell bíznia a legfelső szintű tanúsítványát. A legfelső szintű Hitelesítésszolgáltatói tanúsítvány tárolására, vagy a megbízható legfelső szintű tanúsítványtárolóban szolgáltató, vagy adja meg a legfelső szintű Hitelesítésszolgáltatói tanúsítvány részletei a kódban, amikor az IoT-ügyfél csatlakozik az IoT Edge.

### <a name="intermediate-certificates"></a>A tanúsítvány(ok) köztes

Egy tipikus gyártási folyamat létrehozásához a biztonságos eszközök legfelső szintű Hitelesítésszolgáltatói tanúsítványok ritkán használt közvetlenül, elsősorban kiszivárgását vagy felfedését kockázata miatt. Általában egy vagy több köztes Hitelesítésszolgáltatói tanúsítványok létrehozása és a legfelső szintű Hitelesítésszolgáltatói tanúsítvány digitális aláírásával. Előfordulhat, hogy csak akkor egyetlen, vagy előfordulhat, hogy ezek a köztes tanúsítványok láncolata. Köztes tanúsítványok láncolatától igénylő forgatókönyvek a következők:

* Egy gyártó belüli szervezeti egységek hierarchiájának.

* Több vállalatot tárolókonfigurációhoz részt az éles környezetben, az eszköz.

* Egy ügyfél vásárlása a legfelső szintű hitelesítésszolgáltató és a egy aláíró tanúsítványt a gyártó származtatás aláírásához az eszközök, győződjön meg arról, az adott ügyfél nevében.

Minden esetben a gyártó egy köztes Hitelesítésszolgáltatói tanúsítványt a lánc végén aláírásához használja az eszköz Hitelesítésszolgáltatói tanúsítvány elhelyezni a záró eszközön. Általában ezek a köztes tanúsítványok szorosan a lapkagyártó műhelyben, védett. Azok a szigorú folyamatai, a fizikai és a használatuk elektronikus mennek keresztül.

### <a name="device-ca-certificate"></a>Eszköz Hitelesítésszolgáltatói tanúsítvány

Az eszköz Hitelesítésszolgáltatói tanúsítvány által létrehozott és a folyamat utolsó köztes Hitelesítésszolgáltatói tanúsítvány aláírásával. Ez a tanúsítvány telepítve van a peremhálózati eszköz, lehetőleg a biztonsági tároló, például a hardveres biztonsági modul (HSM). Ezenkívül egy eszköz Hitelesítésszolgáltatói tanúsítvány egyedileg azonosítja az IoT Edge-eszköz. Az IoT Edge-hez az eszköz Hitelesítésszolgáltatói tanúsítvány egyéb tanúsítványok kiállítása alkalmas állapotban. Például az eszköz hitelesítésszolgáltató tanúsítványát problémák levél eszközök való hitelesítéshez használt tanúsítványok a [Azure IoT Device Provisioning Service](../iot-dps/about-iot-dps.md).

### <a name="iot-edge-workload-ca"></a>Az IoT Edge munkaterhelés hitelesítésszolgáltató

Ezt a tanúsítványt, az első, a "operátor" oldalon, a folyamat által generált a [IoT Edge-biztonságkezelő](iot-edge-security-manager.md) IoT Edge első indításakor. Ez a tanúsítvány által létrehozott és az "eszköz CA tanúsítványát" alá. Ez a tanúsítvány, amely egy másik köztes aláíró tanúsítványt, hozza létre, és bármely más, az IoT Edge-futtatókörnyezete által használt tanúsítványok aláírására szolgál. Napjainkban, amely elsősorban az Edge Hub tanúsítvány a következő szakaszban tárgyalt, de a jövőben tartalmazhatnak egyéb tanúsítványok hitelesítésére az IoT Edge-összetevők.

### <a name="edge-hub-server-certificate"></a>Edge hubot kiszolgálói tanúsítvány

Az Edge Hub tanúsítvány, a tényleges tanúsítványaként levéleszközök és az IoT Edge által megkövetelt TLS-kapcsolat létrehozása során a személyazonosság-modulokat. Ez a tanúsítvány-aláíró tanúsítványok, akár a legfelső szintű Hitelesítésszolgáltatói tanúsítványt, amely meg kell bíznia a levél IoT-eszköz létrehozásához használt a teljes tanúsítványlánccal mutat be. Manager által generált az IoT Edge biztonsági, a köznapi név (CN), amikor az Edge-központ tanúsítványa értéke kisbetűsre átalakítás után a config.yaml fájlban "állomásnév" tulajdonság által megadott név. Ez a keverhetők össze az IoT Edge gyakori forrását.

## <a name="production-implications"></a>Gyártás – következmények

Egy ésszerű kérdés lehet, hogy "Miért IoT Edge kell a"számítási feladat CA"extra tanúsítványt? Nem sikerült, a eszköz Hitelesítésszolgáltatói tanúsítvány használatával közvetlenül az Edge Hub-tanúsítvány előállításához? ". Technikailag sikerült. Azonban a a "számítási feladat" köztes tanúsítvány célja, hogy az eszköz gyártója és az eszköz operátor közötti aggályokat külön. Képzelje el egy forgatókönyvet, ahol IoT Edge-eszköz értékesített vagy egy ügyfél a forráscsomópontról a másikra. Ekkor érdemes a eszköz Hitelesítésszolgáltatói tanúsítvány, nem módosítható a gyártó által biztosított. Azonban a "számítási feladat" tanúsítványok adott működésével, az eszköz törlése kell, és újra létre kell hozni az új központi telepítés kapcsán.

Gyártó és operátor folyamatok el vannak különítve, mert nincsenek néhány figyelembe kell venni az IoT Edge-eszközök éles bevezetést kapcsolatos szempontokat.

* Az ügyféltanúsítvány-alapú folyamatokhoz a legfelső szintű Hitelesítésszolgáltatói tanúsítvány és az összes köztes Hitelesítésszolgáltatói tanúsítványt kell biztonságossá tehetők és az IoT Edge-eszköz bevezetést a teljes folyamat során. Az IoT Edge-eszköz gyártója hatékony folyamatoknak kell rendelkeznie azon megfelelő tároló és a köztes tanúsítványok használatát. Emellett az eszköz Hitelesítésszolgáltatói tanúsítványt kell tárolni, amennyire csak lehetséges, magán az eszközön, lehetőleg egy hardveres biztonsági modul biztonságos tárolóként.

* Mivel az Edge Hub tanúsítvány megjelenik alapján az Edge Hub az csatlakozó ügyféleszközök és az eszköz Hitelesítésszolgáltatói tanúsítvány CN-modulok **nem lehet** ugyanaz, mint az "állomásnév", amely felhasználja az IoT Edge config.yaml az eszköz. A nevét (pl. a kapcsolati karakterlánc vagy a csatlakozás parancs az MQTT GatewayHostName paraméterében) keresztül csatlakozni az IoT Edge-ügyfelek által használt **nem lehet** ugyanaz, mint az eszköz Hitelesítésszolgáltatói tanúsítványban használt köznapi név. Ennek az oka az Edge Hub az ellenőrzéshez az ügyfelek által a teljes tanúsítványlánccal mutat be. Ha az Edge Hub-tanúsítvány és az eszköz Hitelesítésszolgáltatói tanúsítvány is rendelkezik, az azonos CN, kap egy ellenőrző hurokba került, és a tanúsítvány érvénytelenné válik.

* Mert az eszköz Hitelesítésszolgáltatói tanúsítvány használja az IoT Edge biztonsági démon a végső IoT Edge-tanúsítványok előállításához, magának kell egy aláíró tanúsítványt, ami azt jelenti, tanúsítvány-aláírási képességekkel rendelkezik. Ahhoz, hogy ez egy hitelesítésszolgáltató aláírási tanúsítvány, lényegében alkalmazása "V3 alapvető típusmegkötések kiegészítő CA:True" az eszköz CA-tanúsítvány automatikusan beállítja a szükséges kulcshasználat tulajdonságait.

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
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* A **\<átjáró eszköznév\>** ezeket a szkripteket átadott **nem kell** lehet ugyanaz, mint a config.yaml "állomásnév" paramétert. Ahogy már említettük, a parancsfájlok elkerülése érdekében minden problémát ".ca" karakterláncnak, amelyeket a **\<átjáró eszköznév\>** , hogy a névütközést, abban az esetben, ha a felhasználó beállította az Edge mindkét helyen nevének használatával azonban hasznos lehet, kerülje a ugyanazzal a névvel.

>[!Tip]
> Csatlakozhat az eszköz IoT "levél" eszközök és az IoT Edge segítségével a c IoT eszközoldali SDK-t használó alkalmazásokat, hozzá kell adnia a választható GatewayHostName paraméter be az eszköz kapcsolati karakterláncának végén. Az Edge Hub kiszolgálói tanúsítvány jön létre, amikor, a gazdanevet erről config.yaml alacsonyabb kisbetűsek verzióján alapul, ezért a nevek egyezés és a TLS-tanúsítvány ellenőrzése sikeres, kell megadnia a GatewayHostName paraméter kisbetű szerepel.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>A tanúsítvány-hierarchia IoT Edge – példa

A tanúsítvány elérési útjának példáján mutatja be, az alábbi képernyőképen az IoT Edge-eszköz beállítása a transzparens átjáróként működő van. OpenSSL érvényesíti az Edge Hub csatlakozhat, és az írassa ki a tanúsítványok segítségével.

![A tanúsítvány-hierarchia minden szintjére képernyőképe](./media/iot-edge-certs/iotedge-cert-chain.png)

Láthatja, hogy megjelenjen a képernyőképen tanúsítvány mélysége hierarchiáját:

| Legfelső szintű hitelesítésszolgáltató tanúsítványa         | Az Azure IoT Hub Hitelesítésszolgáltatói tanúsítvány teszt csak                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Köztes Hitelesítésszolgáltatói tanúsítvány | Az Azure IoT Hub köztes tanúsítvány csak tesztelési                                                                 |
| Eszköz Hitelesítésszolgáltatói tanúsítvány       | iotgateway.CA ("iotgateway" lett átadva a állomásnévként < átjáró > kényelem és parancsfájlok)      |
| Számítási feladatok Hitelesítésszolgáltatói tanúsítvány     | iotedge munkaterhelés hitelesítésszolgáltató                                                                                       |
| Edge hubot kiszolgálói tanúsítvány | (az állomásnév megegyezik a config.yaml) iotedgegw.local                                                |

## <a name="next-steps"></a>További lépések

[Az Azure IoT Edge-modulok megismerése](iot-edge-modules.md)

[A transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md)