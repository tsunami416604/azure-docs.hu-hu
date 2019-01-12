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
ms.openlocfilehash: 91cde6965f3635d6d2acfaf581f570779020f8ff
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232226"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Az Azure IoT Edge-tanúsítvány felhasználási részletek

IoT Edge számára tanúsítványokra van szükség a modulok és az alsóbb rétegbeli IoT-eszközök az identitás- és eredetiségének ellenőrzése a [IoT Edge hubot](iot-edge-runtime.md#iot-edge-hub) futásidőben hívható modulra, amelyekhez csatlakoznak. Ezeknek az ellenőrzéseknek engedélyezze a TLS (transport layer security) biztonságos kapcsolatot a futtatókörnyezet, a modulok és az IoT-eszközök között. Például magát az IoT Hub IoT Edge szükséges egy biztonságos és aktiválásához IoT-kapcsolat titkosítva (vagy levél) eszközök és az IoT Edge-modulok. TLS biztonságos kapcsolatot létesíteni, az IoT Edge hubot modul kapcsolódó ügyfelek számára arra a tanúsítványlánc mutat be egy kiszolgálót.

Ez a cikk bemutatja, hogyan IoT Edge-tanúsítványok fejlesztői, éles környezetben működnek és tesztelési forgatókönyvek. A parancsfájlok különböző (Powershell és a bash), amelyek fogalmak ugyanazok, Linux és a Windows szolgáltatásai között.

## <a name="iot-edge-certificates"></a>IoT Edge-tanúsítványok

A gyártók általában nem az adott IoT Edge-eszköz végfelhasználói. Néha az egyetlen kapcsolat, a kettő között akkor, ha a végfelhasználó, vagy a kezelő, a gyártó által készített általános eszköz vásárol. Más alkalmakkor a gyártó hozhat létre egy egyéni eszköz nevében az operátor szerződés alapján működik. Az IoT Edge-tanúsítvány tervezési próbál meg mindkét forgatókönyvet figyelembe véve.

Az alábbi ábra tanúsítványok IoT Edge használatának. Nulla, egy vagy több köztes aláíró tanúsítványok között a legfelső szintű Hitelesítésszolgáltatói tanúsítvány és az érintett entitások számától függően az eszköz Hitelesítésszolgáltatói tanúsítvány lehet. Itt bemutatjuk egyetlen esetet tartalmaz.

![Tipikus tanúsítvány kapcsolatokat bemutató ábra](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Hitelesítésszolgáltató

A hitelesítésszolgáltató vagy a "CA" röviden, nem minden entitás, amely a digitális tanúsítványokat. Egy hitelesítésszolgáltató megbízható harmadik fél tulajdonosa, és a tanúsítvány a fogadó között funkcionál. Egy digitális tanúsítvány igazolja, a tulajdonjogát, a fogadó a tanúsítvány nyilvános kulcsa. A tanúsítvány megbízhatósági láncában egy főtanúsítványhoz, amely a szervezet által kiállított összes tanúsítvány megbízhatóságának alapját kezdetben kiállításával működik. Ezt követően a tulajdonos használatával a legfelső szintű tanúsítvány további köztes tanúsítványokat ("levél" tanúsítványokat).

### <a name="root-ca-certificate"></a>Legfelső szintű hitelesítésszolgáltató tanúsítványa

A legfelső szintű Hitelesítésszolgáltatói tanúsítványt a teljes folyamat bizalomforrásánál. Éles forgatókönyvekben a CA-tanúsítvány általában Baltimore, a Verisign vagy a DigiCert megbízható kereskedelmi hitelesítésszolgáltatótól vásárolt. Kell rendelkeznie az eszközök csatlakoztatása az IoT Edge-eszközök teljes körű, lehetőség a vállalati szintű hitelesítésszolgáltató használata. Mindkét esetben a teljes tanúsítványlánccal fel IoT Edge-központból összesíti, így a levél IoT-eszközök meg kell bíznia a legfelső szintű tanúsítványát. A legfelső szintű Hitelesítésszolgáltatói tanúsítvány tárolására, vagy a megbízható legfelső szintű tanúsítványtárolóban szolgáltató, vagy adja meg a tanúsítvány adatait az alkalmazás kódjában.

### <a name="intermediate-certificates"></a>Köztes tanúsítványok

Egy tipikus gyártási folyamat létrehozásához a biztonságos eszközök legfelső szintű Hitelesítésszolgáltatói tanúsítványok ritkán használt közvetlenül, elsősorban kiszivárgását vagy felfedését kockázata miatt. A legfelső szintű Hitelesítésszolgáltatói tanúsítványt hoz létre, és a egy vagy több köztes Hitelesítésszolgáltatói tanúsítványok digitális aláírással. Előfordulhat, hogy csak akkor egyetlen, vagy előfordulhat, hogy ezek a köztes tanúsítványok láncolata. Köztes tanúsítványok láncolatától igénylő forgatókönyvek a következők:

* Egy gyártó belüli szervezeti egységek hierarchiájának.

* Több vállalatot tárolókonfigurációhoz részt az éles környezetben, az eszköz.

* Egy ügyfél vásárlása a legfelső szintű hitelesítésszolgáltató és a egy aláíró tanúsítványt a gyártó származtatás aláírásához az eszközök, győződjön meg arról, az adott ügyfél nevében.

Minden esetben a gyártó egy köztes Hitelesítésszolgáltatói tanúsítványt a lánc végén aláírásához használja az eszköz Hitelesítésszolgáltatói tanúsítvány elhelyezni a záró eszközön. Általában ezek a köztes tanúsítványok szorosan a lapkagyártó műhelyben, védett. Azok a szigorú folyamatai, a fizikai és a használatuk elektronikus mennek keresztül.

### <a name="device-ca-certificate"></a>Eszköz Hitelesítésszolgáltatói tanúsítvány

Az eszköz Hitelesítésszolgáltatói tanúsítvány által létrehozott és a folyamat utolsó köztes Hitelesítésszolgáltatói tanúsítvány aláírásával. Ez a tanúsítvány telepítve van az IoT Edge-eszköz, lehetőleg a biztonsági tároló, például a hardveres biztonsági modul (HSM). Ezenkívül egy eszköz Hitelesítésszolgáltatói tanúsítvány egyedileg azonosítja az IoT Edge-eszköz. Az IoT Edge-hez az eszköz Hitelesítésszolgáltatói tanúsítványát is tanúsítványokat más. Például az eszköz hitelesítésszolgáltató tanúsítványát tanúsítványokat levél eszköz, amely segítségével hitelesíti az eszközöket, a [Azure IoT Device Provisioning Service](../iot-dps/about-iot-dps.md).

### <a name="iot-edge-workload-ca"></a>Az IoT Edge munkaterhelés hitelesítésszolgáltató

A [IoT Edge-biztonságkezelő](iot-edge-security-manager.md) hoz létre a számítási feladatok Hitelesítésszolgáltatói tanúsítvány, a "operátor" oldalon, a folyamat az első IoT Edge első indításakor. Ez a tanúsítvány által létrehozott és az "eszköz CA tanúsítványát" alá. Ez a tanúsítvány, amely egy másik köztes aláíró tanúsítványt, hozza létre, és bármely más, az IoT Edge-futtatókörnyezete által használt tanúsítványok aláírására szolgál. Most, amely elsősorban az IoT Edge hubot kiszolgálói tanúsítvány a következő szakaszban tárgyalt, de a jövőben tartalmazhatnak egyéb tanúsítványok hitelesítésére az IoT Edge-összetevők.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge hubot kiszolgálói tanúsítvány

A tanúsítvány az IoT Edge hub az a tényleges tanúsítvány levéleszközök és az IoT Edge által megkövetelt TLS-kapcsolat létrehozása során a személyazonosság-modulokat jelenik meg. Ez a tanúsítvány-aláíró tanúsítványok, akár a legfelső szintű Hitelesítésszolgáltatói tanúsítványt, amely meg kell bíznia a levél IoT-eszköz létrehozásához használt a teljes tanúsítványlánccal mutat be. Manager által generált az IoT Edge biztonsági, a köznapi név (CN), amikor az IoT Edge-központ tanúsítvány értéke a "állomásnév" tulajdonság a config.yaml fájlban kisbetűsre átalakítás után. Ez a keverhetők össze az IoT Edge gyakori forrását.

## <a name="production-implications"></a>Gyártás – következmények

Egy ésszerű kérdés lehet, hogy "Miért IoT Edge kell a"számítási feladat CA"extra tanúsítványt? Nem sikerült, használja az eszköz Hitelesítésszolgáltatói tanúsítványt közvetlenül a az IoT Edge hub-tanúsítvány előállításához? ". Technikailag sikerült. Azonban a a "számítási feladat" köztes tanúsítvány célja, hogy az eszköz gyártója és az eszköz operátor közötti aggályokat külön. Képzelje el egy forgatókönyvet, ahol IoT Edge-eszköz értékesített vagy egy ügyfél a forráscsomópontról a másikra. Ekkor érdemes a eszköz Hitelesítésszolgáltatói tanúsítvány, nem módosítható a gyártó által biztosított. Azonban a "számítási feladat" tanúsítványok adott működésével, az eszköz törlése kell, és újra létre kell hozni az új központi telepítés kapcsán.

Gyártási és művelet folyamatok el vannak különítve, mert ha éles eszközök előkészítése vegye figyelembe a következő szempontokat:

* Az ügyféltanúsítvány-alapú folyamatokhoz a legfelső szintű Hitelesítésszolgáltatói tanúsítvány és az összes köztes Hitelesítésszolgáltatói tanúsítványt kell biztonságossá tehetők és az IoT Edge-eszköz bevezetést a teljes folyamat során. Az IoT Edge-eszköz gyártója hatékony folyamatoknak kell rendelkeznie azon megfelelő tároló és a köztes tanúsítványok használatát. Emellett az eszköz Hitelesítésszolgáltatói tanúsítványt kell tárolni, amennyire csak lehetséges, magán az eszközön, lehetőleg egy hardveres biztonsági modul biztonságos tárolóként.

* A tanúsítvány az IoT Edge hub az IoT Edge hub az csatlakozó ügyféleszközök és a modulok által jelennek meg. Az eszköz Hitelesítésszolgáltatói tanúsítvány köznapi név (CN) **nem lehet** ugyanaz, mint az "állomásnév", amely az IoT Edge-eszközön config.yaml használja. A nevét (például a kapcsolati karakterlánc vagy a csatlakozás parancs az MQTT GatewayHostName paraméterében) keresztül csatlakozni az IoT Edge-ügyfelek által használt **nem lehet** ugyanaz, mint az eszköz Hitelesítésszolgáltatói tanúsítványban használt köznapi név. Ez a korlátozás azért, hogy az IoT Edge hub az ellenőrzéshez az ügyfelek által a teljes tanúsítványlánccal mutat be. Ha az IoT Edge hub-tanúsítvány és az eszköz Hitelesítésszolgáltatói tanúsítvány is rendelkezik, az azonos CN, kap egy ellenőrző hurokba került, és a tanúsítvány érvényteleníti.

* Mert az eszköz Hitelesítésszolgáltatói tanúsítvány használja az IoT Edge biztonsági démon a végső IoT Edge-tanúsítványok előállításához, magának kell egy aláíró tanúsítványt, ami azt jelenti, tanúsítvány-aláírási képességekkel rendelkezik. Alkalmazása "V3 alapvető típusmegkötések kiegészítő CA:True" az eszköz hitelesítésszolgáltató tanúsítványa automatikusan úgy állítja be a szükséges kulcshasználat tulajdonságai.

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

* A **\<átjáró eszköznév\>** ezeket a szkripteket átadott **nem kell** lehet ugyanaz, mint a config.yaml "állomásnév" paramétert. A parancsfájlok segítségével bármely problémák elkerülése érdekében a ".ca" karakterláncnak, amelyeket a **\<átjáró eszköznév\>** , hogy a névütközést, abban az esetben, ha a felhasználó beállította az IoT Edge mindkét helyen azonos névvel. Azonban hasznos lehet, kerülje a ugyanazzal a névvel.

>[!Tip]
> Csatlakozhat az eszköz IoT "levél" eszközök és az IoT Edge segítségével a c IoT eszközoldali SDK-t használó alkalmazásokat, hozzá kell adnia a választható GatewayHostName paraméter be az eszköz kapcsolati karakterláncának végén. Az Edge Hub kiszolgálói tanúsítvány jön létre, amikor, a gazdanevet erről config.yaml alacsonyabb kisbetűsek verzióján alapul, ezért a nevek egyezés és a TLS-tanúsítvány ellenőrzése sikeres, kell megadnia a GatewayHostName paraméter kisbetű szerepel.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>A tanúsítvány-hierarchia IoT Edge – példa

A tanúsítvány elérési útjának példáján mutatja be, az alábbi képernyőképen az IoT Edge-eszköz beállítása a transzparens átjáróként működő van. OpenSSL csatlakoztatása az IoT Edge-központhoz, ellenőrzésének és írassa ki a tanúsítványok segítségével.

![A tanúsítvány-hierarchia minden szintjére képernyőképe](./media/iot-edge-certs/iotedge-cert-chain.png)

Láthatja, hogy megjelenjen a képernyőképen tanúsítvány mélysége hierarchiáját:

| Legfelső szintű hitelesítésszolgáltató tanúsítványa         | Az Azure IoT Hub Hitelesítésszolgáltatói tanúsítvány teszt csak                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Köztes Hitelesítésszolgáltatói tanúsítvány | Az Azure IoT Hub köztes tanúsítvány csak tesztelési                                                                 |
| Eszköz Hitelesítésszolgáltatói tanúsítvány       | iotgateway.CA ("iotgateway" lett átadva a állomásnévként < átjáró > kényelem és parancsfájlok)      |
| Számítási feladatok Hitelesítésszolgáltatói tanúsítvány     | iotedge munkaterhelés hitelesítésszolgáltató                                                                                       |
| Az IoT Edge hubot kiszolgálói tanúsítvány | (az állomásnév megegyezik a config.yaml) iotedgegw.local                                                |

## <a name="next-steps"></a>További lépések

[Az Azure IoT Edge-modulok megismerése](iot-edge-modules.md)

[A transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md)