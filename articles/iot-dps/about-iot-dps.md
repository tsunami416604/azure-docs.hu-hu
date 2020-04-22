---
title: Az Azure IoT Hub Device Provisioning Service áttekintése | Microsoft Docs
description: Az azure-beli eszközkiépítés az eszközkiépítési szolgáltatással (DPS) és az IoT Hubbal
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 1b12886ee55741f62a1156269423ffadd34cd433
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683295"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Eszközregisztráció az Azure IoT Hub Device Provisioning Service használatával
A Microsoft Azure integrált nyilvános felhőszolgáltatások széles skáláját biztosítja, az IoT-megoldásokkal kapcsolatos igények teljes körű kielégítése céljából. Az IoT Hub-eszközlétesítési szolgáltatás (DPS) egy segítő szolgáltatás az IoT Hub, amely lehetővé teszi a nulla érintéses, just-in-time kiépítése a megfelelő IoT hub emberi beavatkozás nélkül. A DPS lehetővé teszi több millió eszköz biztonságos és méretezhető módon történő kiépítését.

## <a name="when-to-use-device-provisioning-service"></a>Mikor érdemes használni a Device Provisioning Service-t?
Számos kiépítési forgatókönyv, amelyben a DPS kiváló választás az eszközök csatlakoztatásához és az IoT Hubhoz konfigurálásához, például:

* Érintésmentes kiépítés egyetlen IoT-megoldásra, az IoT Hub-kapcsolati adatok gyári kódolása nélkül (kezdeti telepítés)
* Terheléselosztási eszközök több elosztóközött
* Eszközök csatlakoztatása a tulajdonos IoT-megoldásához értékesítési tranzakciós adatok alapján (többcsatornás)
* Eszközök csatlakoztatása egy adott IoT-megoldáshoz a használati esettől függően (megoldáselkülönítés)
* Az eszközök legkisebb mértékű késleltetéssel rendelkező IoT Hubhoz való csatlakoztatása (földrajzi horizontális skálázás)
* Ismételt regisztráció az eszközt érintő változás alapján
* Az eszköz által az IoT Hubhoz való csatlakozáshoz használt kulcsok váltása (nem X.509-tanúsítványokkal történő csatlakozás esetében)

## <a name="behind-the-scenes"></a>A színfalak mögött
Az előző szakaszban felsorolt összes forgatókönyv a DPS használatával végezhető el a nulla érintéses kiépítéshez ugyanazzal a folyamattal. A kiépítésben hagyományosan részt vevő manuális lépések közül sok automatizált a DPS-szel, hogy csökkentse az IoT-eszközök üzembe helyezéséhez szükséges időt, és csökkentse a manuális hiba kockázatát. A következő szakasz azt ismerteti, hogy mi történik a színfalak mögött az eszközök regisztrálása során. Az első lépés manuális, az összes további lépés pedig automatizált.

![Alapszintű üzembehelyezési folyamat](./media/about-iot-dps/dps-provisioning-flow.png)

1. Az eszközgyártó hozzáadja az eszköz regisztrációs adatait az Azure portál regisztrációs listájához.
2. Az eszköz felveszi a kapcsolatot a gyáriDPS-végpontkal. Az eszköz továbbítja az azonosító adatokat a DPS-nek, hogy bizonyítsa személyazonosságát.
3. A DPS úgy ellenőrzi az eszköz identitását, hogy a regisztrációs azonosítót és a kulcsot a regisztrációs lista bejegyzéshez igazgatja egy nonce kihívás ([platformmegbízhatósági modul](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) vagy szabványos X.509-es ellenőrzés (X.509) használatával.
4. A DPS regisztrálja az eszközt egy IoT hubbal, és feltölti az eszköz [kívánt ikerállapotát.](../iot-hub/iot-hub-devguide-device-twins.md)
5. Az IoT hub eszközazonosító adatokat ad vissza a DPS-nek.
6. A DPS visszaadja az IoT hub kapcsolati adatait az eszköznek. Ezután az eszköz már képes adatokat küldeni közvetlenül az IoT Hubnak.
7. Az eszköz csatlakozik az IoT Hubhoz.
8. Az eszköz fogadja az IoT Hubban található ikereszköz kívánt állapotát.

## <a name="provisioning-process"></a>A regisztráció folyamata
Egy eszköz telepítési folyamatának két különböző lépése van, amelyben a DPS önállóan elvégezhető szerepet vesz igénybe:

* A **gyártási lépés**, amely során az eszközt előállítják és előkészítik a gyárban, valamint
* A **felhőalapú telepítési lépés**, amelynek keretében a Device Provisioning Service-t automatikus üzembe helyezésre konfigurálják.

Mindkét lépés zökkenőmentesen illeszkedik a létező gyártási és üzembehelyezési folyamatokba. A DPS még bizonyos telepítési folyamatokat is leegyszerűsít, amelyek kézi munkát foglalnak magukban, hogy kapcsolati információkat kapjanak az eszközre.

### <a name="manufacturing-step"></a>Gyártási lépés
Ez a lépés a gyártósoron történő műveletekről szól. Az ebben a lépésben részt vevő szerepkörök közé tartozik a lapkatervező, a lapkagyártó, az integrátor és/vagy az eszköz véggyártója. Ez a lépés magának a hardvernek a gyártásáról szól.

A DPS nem vezet be új lépést a gyártási folyamatban; inkább a meglévő lépéshez kapcsolódik, amely telepíti a kezdeti szoftvert és (ideális esetben) a HSM-et az eszközön. Az eszközazonosító ebben a lépésben történő létrehozása helyett az eszközbe az üzembehelyezési szolgáltatás adatait programozzák bele, lehetővé téve a számára az üzembehelyezési szolgáltatás meghívását bekapcsoláskor, a kapcsolatadatok/IoT-megoldáshozzárendelés lekérése céljából.

Szintén ebben a lépésben látja el a gyártó az eszköz telepítőjét/kezelőjét azonosítókulcs-adatokkal. Ezen adatok átadása lehet egyszerű, mint például annak megerősítése, hogy mindegyik eszköz a telepítője/kezelője által biztosított aláíró tanúsítványból létrehozott X.509-tanúsítvánnyal rendelkezik, illetve bonyolult, mint például a TPM-ellenőrzőkulcs nyilvános részének kinyerése mindegyik TPM-eszközből. Ezek a szolgáltatások ma már sok lapkagyártó kínálatában szerepelnek.

### <a name="cloud-setup-step"></a>Felhőalapú telepítési lépés
Ennek a lépésnek a lényege a felhő megfelelő automatikus üzembe helyezésre való konfigurálása. A felhőalapú telepítési lépésben általában kétféle felhasználó vesz részt: az, aki tudja, hogyan kell elvégezni az eszközök kezdeti beállítását (eszközkezelő), és az, aki tudja, hogyan kell elosztani az eszközöket az IoT Hubok között (megoldáskezelő).

Az üzembe helyezés kezdeti beállítását egyszer kell elvégezni, ami általában a megoldásüzemeltető feladata. Ha az üzembehelyezési szolgáltatás konfigurálása megtörtént, nem szükséges többé módosítani, kivéve, ha megváltozik a használati eset.

A szolgáltatást az automatikus üzembe helyezésre való konfigurálás után fel kell készíteni az eszközök regisztrálására. Ezt a lépést az eszközkezelő végzi el, aki ismeri az eszköz(ök) kívánt konfigurációját. Az ő feladata biztosítani, hogy az üzembehelyezési szolgáltatás képes legyen megfelelően igazolni az eszköz identitását, amikor az az IoT Hubot keresi. Az eszközkezelő hozzáadja a gyártótól kapott azonosítókulcs-adatokat a regisztrációs listához. A regisztrációs lista későbbi frissítése lehetséges, ha új bejegyzéseket adnak hozzá, vagy a létező bejegyzéseket frissítik az eszközökre vonatkozó legfrissebb adatokkal.

## <a name="registration-and-provisioning"></a>Regisztráció és üzembe helyezés
Az *üzembe helyezés* különféle jelentéseket takarhat attól függően, mely iparágban használják. Az IoT-eszközök felhőalapú megoldáson való üzembe helyezésének kontextusában az üzembe helyezés kétrészes folyamat:

1. Az első lépés a kezdeti kapcsolat létesítése az eszköz és az IoT-megoldás között az eszköz regisztrálása révén.
2. A második lépés a megfelelő konfiguráció alkalmazása az eszközre annak a megoldásnak az egyéni követelményei alapján, amelyre az eszközt regisztrálták.

Ha mindkét lépés befejeződött, az eszköz teljesen üzembe helyezettnek tekinthető. Egyes felhőszolgáltatások csak az üzembehelyezési folyamat első lépését, az eszközök IoT-megoldásvégponton való regisztrációját biztosítják, a kezdeti konfigurációt viszont nem. A DPS mindkét lépést automatizálja, hogy zökkenőmentes kiépítési élményt nyújtson az eszközszámára.

## <a name="features-of-the-device-provisioning-service"></a>A Device Provisioning Service funkciói
A DPS számos funkcióval rendelkezik, így ideális az eszközök kiépítéséhez.

* **Biztonságos állapotigazolás** X.509- és a TPM-alapú identitásokhoz is.
* Az esetleg regisztráló eszközök/eszközcsoportok teljes rekordját tartalmazó **regisztrációs lista**. A regisztrációs lista tartalmazza az eszköz kívánt konfigurációjára vonatkozó adatokat, amint az regisztrál, és bármikor frissíthető.
* **Több foglalási szabályzat oka annak** szabályozása, hogy a DPS hogyan rendeli hozzá az eszközöket az IoT-központokhoz a forgatókönyvek támogatása érdekében: A legkisebb késés, az egyenletesen súlyozott terjesztés (alapértelmezett) és a statikus konfiguráció a regisztrációs listán keresztül. A késés tmegegyezik a Traffic Manager metódussal azonos [módszerrel.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#performance)
* A **Naplózás monitorozása és hibakeresése** segít biztosítani, hogy minden jól működjön.
* **A többhubos támogatás** lehetővé teszi a DPS számára, hogy több IoT-központhoz rendeljen eszközöket. A DPS több Azure-előfizetésben is tud hubokkal beszélni.
* **A régiók közötti támogatás** lehetővé teszi a DPS számára, hogy eszközöket rendeljen más régiók IoT-központjaihoz.
* **Az inaktív adatok titkosítása** lehetővé teszi a DPS-ben lévő adatok titkosítását és visszafejtését transzparens módon 256 bites AES titkosítással, amely az egyik legerősebb blokktitkosítás, és fips 140-2 kompatibilis.


Az eszközkiépítéssel kapcsolatban felmerülő alapelvekről és szolgáltatásokról az [eszközzel kapcsolatos alapelvek](concepts-device.md), [szolgáltatással kapcsolatos alapelvek](concepts-service.md) és [biztonsági alapelvek](concepts-security.md) szakaszokban szerezhet további információkat.

## <a name="cross-platform-support"></a>Platformfüggetlen támogatás
Az Összes Azure IoT-szolgáltatáshoz hasonlóan a DPS is platformfüggetlen, különböző operációs rendszerekkel működik. Az Azure számos [nyelven](https://github.com/Azure/azure-iot-sdks) kínál nyílt forráskódú SDK-kat az eszközök csatlakoztatásához és a szolgáltatás felügyeletéhez. A DPS a következő protokollokat támogatja az eszközök csatlakoztatásához:

* HTTPS
* AMQP
* AMQP WebSocketen keresztül
* MQTT
* MQTT WebSocketen keresztül

A DPS csak a szervizműveletekhez támogatja a HTTPS-kapcsolatokat.

## <a name="regions"></a>Régiók
A DPS számos régióban elérhető. A meglévő és újonnan bejelentett régiók naprakész listája minden szolgáltatáshoz megtalálható az [Azure-régióknál](https://azure.microsoft.com/regions/). A Device Provisioning Service elérhetőségét az [Azure állapota](https://azure.microsoft.com/status/) lapon tekintheti meg.

> [!NOTE]
> A DPS globális, és nem kötődik egy helyhez. Meg kell azonban adnia azt a területet, ahol a DPS-profilhoz társított metaadatok a dps-profilhoz kapcsolódnak.

## <a name="availability"></a>Rendelkezésre állás
A DPS-hez 99,9%-os szolgáltatásiszint-szerződés van, és [elolvashatja az SLA-t.](https://azure.microsoft.com/support/legal/sla/iot-hub/) Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/)-ban található.

## <a name="quotas"></a>Kvóták
Minden Azure-előfizetésre alapértelmezett kvótakorlátozások vonatkoznak, és ezek a korlátozások hatással lehetnek az IoT-megoldás hatókörére. A jelenlegi határérték előfizetésenként 10 Device Provisioning Service.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

További információ a kvótakorlátozásokról:
* [Az Azure-előfizetés szolgáltatásokra vonatkozó korlátozásai](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Kapcsolódó Azure-összetevők
A DPS automatizálja az eszközök kiépítést az Azure IoT Hubbal. További információk az [IoT Hubról](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>További lépések
Mostanra nagyjából átlátja az IoT-eszközök Azure-ban való kiépítését. A következő lépés egy teljes körű IoT-forgatókönyv kipróbálása.
> [!div class="nextstepaction"]
> [Az IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon](quick-setup-auto-provision.md)
> [Hozzon létre és hozzon létre egy szimulált eszközt,](quick-create-simulated-device.md)
> amely eszköz beállítása a[kiépítéshez](tutorial-set-up-device.md)
