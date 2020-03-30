---
title: Tanúsítványok az eszközök biztonsága érdekében - Azure IoT Edge | Microsoft dokumentumok
description: Az Azure IoT Edge tanúsítványt használ az eszközök, modulok és levéleszközök érvényesítéséhez, és biztonságos kapcsolatokat létesítenek közöttük.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58294c7afdf31ddd29611351d6442db1c4966157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269036"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Ismerje meg, hogyan használja az Azure IoT Edge a tanúsítványokat

Az IoT Edge-tanúsítványokat a modulok és az alsóbb rétegbeli IoT-eszközök használják az [IoT Edge hub](iot-edge-runtime.md#iot-edge-hub) futásidejű modul identitásának és legitimitásának ellenőrzésére. Ezek az ellenőrzések lehetővé teszik a TLS (átviteli réteg biztonsága) biztonságos kapcsolatot a futásidejű, a modulok és az IoT-eszközök között. Az IoT Hubhoz hasonlóan az IoT Edge-nek is biztonságos és titkosított kapcsolatot igényel az IoT alsóbb rétegbeli (vagy leveles) eszközeiről és az IoT Edge-modulokról. Biztonságos TLS-kapcsolat létrehozásához az IoT Edge hub modul egy kiszolgálói tanúsítványláncot jelenít meg a csatlakozó ügyfelekszámára, hogy ellenőrizhessék az identitását.

Ez a cikk bemutatja, hogyan működhetnek az IoT Edge-tanúsítványok az éles, fejlesztési és tesztelési forgatókönyvekben. Míg a szkriptek különbözőek (Powershell vs bash), a fogalmak azonosak a Linux és a Windows között.

## <a name="iot-edge-certificates"></a>IoT Edge-tanúsítványok

Általában a gyártók nem az IoT Edge-eszközök végfelhasználói. Néha az egyetlen kapcsolat a kettő között az, amikor a végfelhasználó, vagy üzemeltető, vásárol egy általános eszköz által a gyártó. Máskor a gyártó szerződés alapján dolgozik, hogy egyéni eszközt építsen az üzemeltető számára. Az IoT Edge-tanúsítvány tervezése megkísérli mindkét forgatókönyv figyelembevételét.

Az alábbi ábra az IoT Edge tanúsítványok használatát mutatja be. A legfelső szintű hitelesítésszolgáltatói tanúsítvány és az eszköz hitelesítésszolgáltatói tanúsítványa között az érintett entitások számától függően nulla, egy vagy több köztes aláíró tanúsítvány lehet. Itt mutatunk egy esetet.

![Tipikus tanúsítványkapcsolatok diagramja](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Hitelesítésszolgáltató

A hitelesítésszolgáltató ( röviden "Hitelesítésszolgáltató" – digitális tanúsítványokat kiállító entitás. A hitelesítésszolgáltató megbízható harmadik félként működik a tulajdonos és a tanúsítvány címzettje között. A digitális tanúsítvány igazolja a nyilvános kulcs tulajdonjogát a tanúsítvány fogadója által. A megbízhatósági tanúsítványlánc úgy működik, hogy először főtanúsítványt állít ki, amely a hatóság által kibocsátott összes tanúsítvány megbízhatóságának alapja. Ezt követően a tulajdonos a főtanúsítvány segítségével további köztes tanúsítványokat ("levél" tanúsítványokat) állíthat ki.

### <a name="root-ca-certificate"></a>Legfelső szintű hitelesítésszolgáltatói tanúsítvány

A legfelső szintű hitelesítésszolgáltatói tanúsítvány a teljes folyamat megbízhatóságának gyökere. Éles környezetben ezt a hitelesítésszolgáltatói tanúsítványt általában egy megbízható kereskedelmi hitelesítésszolgáltatótól, például Baltimore-tól, Verisign-tól vagy DigiCerttől vásárolják. Ha teljes mértékben szabályozhatja az IoT Edge-eszközökhöz csatlakozó eszközöket, lehetőség van egy vállalati szintű hitelesítésszolgáltató használatára. Mindkét esetben a teljes tanúsítványlánc az IoT Edge hub felgöngyüldel, így a levél IoT-eszközök meg kell bíznia a főtanúsítvány. A legfelső szintű hitelesítésszolgáltatói tanúsítvány tárolható a megbízható legfelső szintű hitelesítésszolgáltató itontárolójában, vagy az alkalmazáskódban is megadhatja a tanúsítvány adatait.

### <a name="intermediate-certificates"></a>Köztes tanúsítványok

A biztonságos eszközök létrehozásának tipikus gyártási folyamatában a legfelső szintű hitelesítésszolgáltató-tanúsítványokat ritkán használják közvetlenül, elsősorban a szivárgás vagy az expozíció kockázata miatt. A legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehoz és digitálisan aláír egy vagy több köztes hitelesítésszolgáltatói tanúsítványt. Lehet, hogy csak egy, vagy lehet, hogy egy lánc ilyen köztes tanúsítványok. Olyan forgatókönyvek, amelyek köztes tanúsítványok láncát igénylik, a következők:

* A gyártón belüli részlegek hierarchiája.

* Több vállalat is részt vesz sorozatban egy eszköz gyártásában.

* A legfelső szintű hitelesítésszolgáltatót megvásárló ügyfél, aki a gyártó számára aláírási tanúsítványt készít az adott ügyfél nevében elkészíteni kívánt eszközök aláírásához.

A gyártó minden esetben egy köztes hitelesítésszolgáltatói tanúsítványt használ a lánc végén, hogy aláírja a végfelhasználói eszközre helyezett hitelesítésszolgáltatói tanúsítványt. Ezeket a köztes tanúsítványokat általában szigorúan őrzik a gyártóüzemben. Szigorú folyamatokon mennek keresztül, mind fizikai, mind elektronikus használatuk miatt.

### <a name="device-ca-certificate"></a>Eszközhitelesítéstanúsítvány

Az eszköz hitelesítésszolgáltatói tanúsítványát a folyamat utolsó köztes hitelesítésszolgáltatói tanúsítványa hozza létre és írja alá. Ez a tanúsítvány maga az IoT Edge-eszközre van telepítve, lehetőleg biztonságos tárolóban, például hardveres biztonsági modulban (HSM). Emellett egy eszköz hitelesítésszolgáltatói tanúsítvány egyedileg azonosítja az IoT Edge-eszközt. Az eszköz hitelesítésszolgáltatói tanúsítványa más tanúsítványokat is aláírhat.

### <a name="iot-edge-workload-ca"></a>IoT peremhálózati számítási feladat ca

Az [IoT Edge Security Manager](iot-edge-security-manager.md) a folyamat "operátor" oldalán az első, az IoT Edge első indításakor létrehozza a számítási feladatok hitelesítésszolgáltatói tanúsítványát. Ezt a tanúsítványt az "eszközhitelesítési tanúsítvány" hozza létre és írja alá. Ez a tanúsítvány, amely csak egy másik köztes aláíró tanúsítvány, az IoT Edge futásideje által használt egyéb tanúsítványok létrehozására és aláírására szolgál. Ma, ez elsősorban az IoT Edge hub-kiszolgáló tanúsítvány a következő szakaszban tárgyalt, de a jövőben tartalmazhat más tanúsítványokat az IoT Edge-összetevők hitelesítéséhez.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge-elosztó kiszolgálótanúsítvány

Az IoT Edge hub-kiszolgáló tanúsítványa a tényleges tanúsítvány bemutatott levél eszközök és modulok identitás-ellenőrzés létrehozása során a TLS-kapcsolat által igényelt IoT Edge. Ez a tanúsítvány a létrehozáshoz használt aláíró tanúsítványok teljes láncolatát mutatja be a legfelső szintű hitelesítésszolgáltatói tanúsítványig, amelyben a levél IoT-eszköznek bíznia kell. Az IoT Edge Security Manager által generált IoT Edge-i hubtanúsítvány köznapi neve (CN) a config.yaml fájl "hostname" tulajdonságára van állítva a kisbetűs átalakítás után. Ez a konfiguráció az IoT Edge-szel való félreértés ek gyakori forrása.

## <a name="production-implications"></a>Termelési vonatkozások

Ésszerű kérdés lehet" miért van szüksége az IoT Edge-nek a "számítási feladat hitelesítésszolgáltató" extra tanúsítványára? Nem tudta használni az eszköz hitelesítésszolgáltatói tanúsítványát az IoT Edge hub-kiszolgálói tanúsítvány közvetlen létrehozásához?". Technikailag, lehet. Ennek a "munkaterhelés" köztes tanúsítványnak azonban az a célja, hogy elkülönítse az aggályokat az eszköz gyártója és az eszköz üzemeltetője között. Képzeljen el egy olyan forgatókönyvet, amelyben egy IoT Edge-eszközt eladnak vagy átruháznak az egyik ügyfélről a másikra. Valószínűleg azt szeretné, hogy a gyártó által biztosított eszközhitelesítési tanúsítvány a nem módosítható legyen. Az eszköz működésére vonatkozó "munkaterhelési" tanúsítványokat azonban törölni kell, és újra létre kell hozni az új központi telepítéshez.

Mivel a gyártási és üzemeltetési folyamatok elkülönülnek egymástól, a termelési eszközök előkészítésekor vegye figyelembe a következő következményeket:

* Bármely tanúsítványalapú folyamat esetén a legfelső szintű hitelesítésszolgáltatói tanúsítványt és az összes köztes hitelesítésszolgáltatói tanúsítványt az IoT Edge-eszközök üzembe sodrásának teljes folyamata során biztosítani és figyelni kell. Az IoT Edge-eszköz gyártójának erős folyamatokkal kell rendelkeznie a köztes tanúsítványok megfelelő tárolásához és használatához. Ezenkívül az eszköz hitelesítésszolgáltatói tanúsítványát a lehető legbiztonságosabb helyen kell tartani magán az eszközön, lehetőleg egy hardveres biztonsági modulon.

* Az IoT Edge hub-kiszolgáló tanúsítványát az IoT Edge hub mutatja be a csatlakozó ügyféleszközökhöz és modulokhoz. Az eszköz hitelesítésszolgáltatói tanúsítványának köznapi neve (CN) **nem egyezhet meg** az IoT Edge-eszközön a config.yaml fájlban használt "állomásnévvel". Az ügyfelek által az IoT Edge-hez való csatlakozáshoz használt név (például a kapcsolati karakterlánc GatewayHostName paraméterén keresztül vagy az MQTT CONNECT parancsán keresztül) **nem lehet** ugyanaz, mint az eszköz hitelesítésszolgáltatói tanúsítványában használt közös név. Ez a korlátozás azért van, mert az IoT Edge hub bemutatja a teljes tanúsítványlánc ot az ügyfelek általi ellenőrzéshez. Ha az IoT Edge hub-kiszolgáló tanúsítványa és az eszköz hitelesítésszolgáltatói tanúsítványa is ugyanazt a CN-t rendelkezik, akkor egy ellenőrzési hurkot kap, és a tanúsítvány érvényteleníti.

* Mivel az eszköz hitelesítésszolgáltatói tanúsítványát az IoT Edge biztonsági démona használja a végső IoT Edge-tanúsítványok létrehozásához, magának is aláíró tanúsítványnak kell lennie, ami azt jelenti, hogy tanúsítványaláírási képességekkel rendelkezik. A "V3 alapvető megkötések CA:True" alkalmazásával az eszköz hitelesítésszolgáltatói tanúsítványa automatikusan beállítja a szükséges kulcshasználati tulajdonságokat.

>[!Tip]
> Ha már átment az IoT Edge beállításán, mint egy átlátható átjáró egy fejlesztési/tesztelési forgatókönyvben a "kényelmi parancsfájlok" használatával (lásd a következő szakaszt), és ugyanazt az állomásnevet használta az eszköz hitelesítésszolgáltatói tanúsítványának létrehozásakor, mint a config.yaml állomásnévhez, előfordulhat, hogy csodálkozik, hogy miért működött. Annak érdekében, hogy egyszerűsítse a fejlesztői élményt, a kényelmi szkriptek hozzáfűzi a ".ca" a végén a név adja át a parancsfájlt. Így például, ha a "mygateway" használt mind a készülék nevét a szkriptek és a hostname a config.yaml, az előbbi lesz mygateway.ca, mielőtt használják a CN az eszköz CA cert.

## <a name="devtest-implications"></a>Fejlesztési/tesztelési vonatkozások

A fejlesztési és tesztelési forgatókönyvek megkönnyítése érdekében a Microsoft [kényelmi parancsfájlokat](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) biztosít az IoT Edge számára megfelelő nem éles tanúsítványok létrehozásához az átlátszó átjáróforgatókönyvben. A parancsfájlok működésére vonatkozó példákról a [Demótanúsítványok létrehozása az IoT Edge-eszközszolgáltatások teszteléséhez](how-to-create-test-certificates.md)című témakörben talál.

>[!Tip]
> Az eszköz IoT "leaf" eszközök és alkalmazások, amelyek az IoT-eszköz SDK-t az IoT Edge-en keresztül, hozzá kell adnia a választható GatewayHostName paraméter az eszköz kapcsolati karakterláncának végén. Az Edge Hub Server tanúsítvány létrehozásakor a config.yaml állomásnév kisméretű verzióján alapul, ezért ahhoz, hogy a nevek egyeztetése és a TLS-tanúsítvány ellenőrzése sikeres legyen, kisbetűsen adja meg a GatewayHostName paramétert.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Példa az IoT Edge tanúsítványhierarchiára

A tanúsítvány elérési útjának szemléltetéséhez a következő képernyőkép egy átlátszó átjáróként beállított működő IoT Edge-eszközről származik. Az OpenSSL az IoT Edge hubhoz való csatlakozáshoz, a tanúsítványok érvényesítéséhez és kiírásához használható.

![Képernyőkép a tanúsítványhierarchiáról minden szinten](./media/iot-edge-certs/iotedge-cert-chain.png)

A tanúsítványmélység hierarchiáját a képernyőképen láthatja:

| Legfelső szintű hitelesítésszolgáltatói tanúsítvány         | Csak az Azure IoT Hub hitelesítési tanúsítványának tesztje                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Köztes hitelesítésszolgáltatói tanúsítvány | Csak az Azure IoT Hub köztes tanúsítványtesztje                                                                 |
| Eszközhitelesítési tanúsítvány       | iotgateway.ca ("iotgateway" lett átírva, mivel a < átjáró állomás neve > a kényelmi parancsfájlok)   |
| Számítási feladat hitelesítésszolgáltatói tanúsítványa     | iotedge munkaterhelés ca                                                                                       |
| IoT edge hub-kiszolgáló tanúsítványa | iotedgegw.local (megegyezik a config.yaml "hostname" nevével)                                            |

## <a name="next-steps"></a>További lépések

[Az Azure IoT Edge-modulok ismertetése](iot-edge-modules.md)

[IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
