---
title: Az Azure IoT Hub Device Provisioning Service áttekintése | Microsoft Docs
description: A Device Provisioning Service és az IoT Hub használatával az Azure-ban történő eszközregisztrációt írja le.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 89e0b6b5bedf36c89bf3a54f4baf42f7019625c0
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051467"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Eszközregisztráció az Azure IoT Hub Device Provisioning Service használatával
A Microsoft Azure integrált nyilvános felhőszolgáltatások széles skáláját biztosítja, az IoT-megoldásokkal kapcsolatos igények teljes körű kielégítése céljából. Az IoT Hub Device Provisioning Service az IoT Hub segítő szolgáltatása, amely emberi beavatkozás nélkül teszi lehetővé a megfelelő IoT Hubon való érintésmentes, igény szerinti üzembe helyezést, így az ügyfelek eszközök millióit építhetik ki biztonságos és méretezhető módon.

## <a name="when-to-use-device-provisioning-service"></a>Mikor érdemes használni a Device Provisioning Service-t?
A Device Provisioning Service sokféle kiépítési forgatókönyv esetében kiváló választás az eszközök IoT Hubhoz való csatlakoztatása és konfigurálása céljából, mint például:

* Érintésmentes kiépítés egyetlen IoT-megoldásra, az IoT Hub-kapcsolati adatok gyári kódolása nélkül (kezdeti telepítés)
* Eszközök terheléselosztása több hubon
* Eszközök csatlakoztatása a tulajdonosuk IoT-megoldásához az értékesítési tranzakciós adatok alapján (több-bérlős mód)
* Eszközök csatlakoztatása egy adott IoT-megoldáshoz a használati esettől függően (megoldáselkülönítés)
* Az eszközök legkisebb mértékű késleltetéssel rendelkező IoT Hubhoz való csatlakoztatása (földrajzi horizontális skálázás)
* Ismételt regisztráció az eszközt érintő változás alapján
* Az eszköz által az IoT Hubhoz való csatlakozáshoz használt kulcsok váltása (nem X.509-tanúsítványokkal történő csatlakozás esetében)

## <a name="behind-the-scenes"></a>A színfalak mögött
Az előző szakaszban felsorolt összes forgatókönyv megvalósítható az érintésmentes üzembe helyezés üzembehelyezési szolgáltatásával, ugyanazzal a folyamattal. A Device Provisioning Service az üzembe helyezés hagyományosan manuális lépései közül sokat automatizál, csökkentve az IoT-eszközök üzembehelyezési idejét és a manuális jellegű hibák bekövetkeztének kockázatát. A következő szakasz azt ismerteti, hogy mi történik a színfalak mögött az eszközök regisztrálása során. Az első lépés manuális, az összes további lépés pedig automatizált.

![Alapszintű üzembehelyezési folyamat](./media/about-iot-dps/dps-provisioning-flow.png)

1. Az eszközgyártó hozzáadja az eszköz regisztrációs adatait az Azure portál regisztrációs listájához.
2. Az eszköz kapcsolatba lép a gyárilag beállított üzembehelyezési szolgáltatásvégponttal. Az eszköz továbbítja az azonosító adatait az üzembehelyezési szolgáltatásnak, identitásának bizonyítása céljából.
3. Az üzembehelyezési szolgáltatás érvényesíti az eszköz identitását azáltal, hogy összehasonlítja a regisztrációs azonosítót és kulcsot a regisztrációs lista bejegyzésével, egyszeri kulcsos ellenőrzéssel ([platformmegbízhatósági modul](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) vagy szabványos X.509-ellenőrzéssel (X.509).
4. Az üzembehelyezési szolgáltatás regisztrálja az eszközt egy IoT Hubban, és feltölti az eszköz [kívánt ikerállapotát](../iot-hub/iot-hub-devguide-device-twins.md).
5. Az IoT Hub visszaküldi az eszköz azonosító adatait az üzembehelyezési szolgáltatásnak.
6. Az üzembehelyezési szolgáltatás visszaküldi az IoT Hub kapcsolatadatait az eszköznek. Ezután az eszköz már képes adatokat küldeni közvetlenül az IoT Hubnak.
7. Az eszköz csatlakozik az IoT Hubhoz.
8. Az eszköz fogadja az IoT Hubban található ikereszköz kívánt állapotát.

## <a name="provisioning-process"></a>A regisztráció folyamata
Az eszközök telepítési folyamata, amelyben a Device Provisioning Service részt vesz, két különálló lépésből áll, amelyek egymástól függetlenül végezhetők el:

* A **gyártási lépés**, amely során az eszközt előállítják és előkészítik a gyárban, valamint
* A **felhőalapú telepítési lépés**, amelynek keretében a Device Provisioning Service-t automatikus üzembe helyezésre konfigurálják.

Mindkét lépés zökkenőmentesen illeszkedik a létező gyártási és üzembehelyezési folyamatokba. A Device Provisioning Service le is egyszerűsít néhány olyan üzembehelyezési folyamatot, amelyek keretében nagy mennyiségű manuális munkára van szükség a kapcsolatadatoknak az eszközre való eljuttatásához.

### <a name="manufacturing-step"></a>Gyártási lépés
Ez a lépés a gyártósoron történő műveletekről szól. Az ebben a lépésben részt vevő szerepkörök közé tartozik a lapkatervező, a lapkagyártó, az integrátor és/vagy az eszköz véggyártója. Ez a lépés magának a hardvernek a gyártásáról szól.

A Device Provisioning Service nem vezet be új lépést a gyártási folyamatba; ehelyett ahhoz a létező lépéshez kapcsolódik, amelynek során a kezdeti szoftvereket és (ideális esetben) a HSM-et telepítik az eszközre. Az eszközazonosító ebben a lépésben történő létrehozása helyett az eszközbe az üzembehelyezési szolgáltatás adatait programozzák bele, lehetővé téve a számára az üzembehelyezési szolgáltatás meghívását bekapcsoláskor, a kapcsolatadatok/IoT-megoldáshozzárendelés lekérése céljából.

Szintén ebben a lépésben látja el a gyártó az eszköz telepítőjét/kezelőjét azonosítókulcs-adatokkal. Ezen adatok átadása lehet egyszerű, mint például annak megerősítése, hogy mindegyik eszköz a telepítője/kezelője által biztosított aláíró tanúsítványból létrehozott X.509-tanúsítvánnyal rendelkezik, illetve bonyolult, mint például a TPM-ellenőrzőkulcs nyilvános részének kinyerése mindegyik TPM-eszközből. Ezek a szolgáltatások ma már sok lapkagyártó kínálatában szerepelnek.

### <a name="cloud-setup-step"></a>Felhőalapú telepítési lépés
Ennek a lépésnek a lényege a felhő megfelelő automatikus üzembe helyezésre való konfigurálása. A felhőalapú telepítési lépésben általában kétféle felhasználó vesz részt: az, aki tudja, hogyan kell elvégezni az eszközök kezdeti beállítását (eszközkezelő), és az, aki tudja, hogyan kell elosztani az eszközöket az IoT Hubok között (megoldáskezelő).

Az üzembe helyezés kezdeti beállítását egyszer kell elvégezni, ami általában a megoldásüzemeltető feladata. Ha az üzembehelyezési szolgáltatás konfigurálása megtörtént, nem szükséges többé módosítani, kivéve, ha megváltozik a használati eset.

A szolgáltatást az automatikus üzembe helyezésre való konfigurálás után fel kell készíteni az eszközök regisztrálására. Ezt a lépést az eszközkezelő végzi el, aki ismeri az eszköz(ök) kívánt konfigurációját. Az ő feladata biztosítani, hogy az üzembehelyezési szolgáltatás képes legyen megfelelően igazolni az eszköz identitását, amikor az az IoT Hubot keresi. Az eszközkezelő hozzáadja a gyártótól kapott azonosítókulcs-adatokat a regisztrációs listához. A regisztrációs lista későbbi frissítése lehetséges, ha új bejegyzéseket adnak hozzá, vagy a létező bejegyzéseket frissítik az eszközökre vonatkozó legfrissebb adatokkal.

## <a name="registration-and-provisioning"></a>Regisztráció és üzembe helyezés
Az *üzembe helyezés* különféle jelentéseket takarhat attól függően, mely iparágban használják. Az IoT-eszközök felhőalapú megoldáson való üzembe helyezésének kontextusában az üzembe helyezés kétrészes folyamat:

1. Az első lépés a kezdeti kapcsolat létesítése az eszköz és az IoT-megoldás között az eszköz regisztrálása révén.
2. A második lépés a megfelelő konfiguráció alkalmazása az eszközre annak a megoldásnak az egyéni követelményei alapján, amelyre az eszközt regisztrálták.

Ha mindkét lépés befejeződött, az eszköz teljesen üzembe helyezettnek tekinthető. Egyes felhőszolgáltatások csak az üzembehelyezési folyamat első lépését, az eszközök IoT-megoldásvégponton való regisztrációját biztosítják, a kezdeti konfigurációt viszont nem. A Device Provisioning Service a két lépés automatizálásával biztosítja az eszközök zökkenőmentes kiépítését.

## <a name="features-of-the-device-provisioning-service"></a>A Device Provisioning Service funkciói
A Device Provisioning Service számos funkciójának köszönhetően ideális az eszközök üzembe helyezéséhez.

* **Biztonságos állapotigazolás** X.509- és a TPM-alapú identitásokhoz is.
* Az esetleg regisztráló eszközök/eszközcsoportok teljes rekordját tartalmazó **regisztrációs lista**. A regisztrációs lista tartalmazza az eszköz kívánt konfigurációjára vonatkozó adatokat, amint az regisztrál, és bármikor frissíthető.
* A **Többszörös kiosztási szabályzatokkal** szabályozható, hogy hogyan rendeljen a Device Provisioning Service eszközöket az IoT Hubokhoz a forgatókönyvek támogatására.
* A **Naplózás monitorozása és hibakeresése** segít biztosítani, hogy minden jól működjön.
* A **Több központ támogatása** lehetővé teszi, hogy a Device Provisioning Service egynél több IoT Hubhoz rendeljen hozzá eszközöket. A Device Provisioning Service tud kommunikálni a több Azure-előfizetést felölelő központokkal.
* A **Régiófüggetlen támogatás** lehetővé teszi a Device Provisioning Service számára, hogy más régiókban lévő IoT Hubokhoz is hozzárendeljen eszközöket.

Az eszközkiépítéssel kapcsolatban felmerülő alapelvekről és szolgáltatásokról az [eszközzel kapcsolatos alapelvek](concepts-device.md), [szolgáltatással kapcsolatos alapelvek](concepts-service.md) és [biztonsági alapelvek](concepts-security.md) szakaszokban szerezhet további információkat.

## <a name="cross-platform-support"></a>Platformfüggetlen támogatás
A Device Provisioning Service, akárcsak az összes Azure IoT-szolgáltatás, számos különböző operációs rendszeren működik. Az Azure számos [nyelven](https://github.com/Azure/azure-iot-sdks) kínál nyílt forráskódú SDK-kat az eszközök csatlakoztatásához és a szolgáltatás felügyeletéhez. A Device Provisioning Service az alábbi protokollokat támogatja az eszközök csatlakoztatásához:

* HTTPS
* AMQP
* AMQP WebSocketen keresztül
* MQTT
* MQTT WebSocketen keresztül

A Device Provisioning Service csak HTTPS-kapcsolatokat támogat szolgáltatási műveletekhez.

## <a name="regions"></a>Régiók
Device Provisioning Service számos régióban elérhető. A meglévő és újonnan bejelentett régiók naprakész listája minden szolgáltatáshoz megtalálható az [Azure-régióknál](https://azure.microsoft.com/regions/). A Device Provisioning Service elérhetőségét az [Azure állapota](https://azure.microsoft.com/status/) lapon tekintheti meg.

> [!NOTE]
> A Device Provisioning Service globális, és nem helyhez kötött. Meg kell adnia azonban egy régiót, ahol a Device Provisioning Service-profiljához társuló metaadatok lesznek találhatók.

## <a name="availability"></a>Rendelkezésre állás
A Device Provisioning Service-hez 99,9%-os szolgáltatói szerződés érhető el, amely [el is olvasható](https://azure.microsoft.com/support/legal/sla/iot-hub/). Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/)-ban található.

## <a name="quotas"></a>Kvóták
Minden Azure-előfizetésre alapértelmezett kvótakorlátozások vonatkoznak, és ezek a korlátozások hatással lehetnek az IoT-megoldás hatókörére. A jelenlegi határérték előfizetésenként 10 Device Provisioning Service.

További információ a kvótakorlátozásokról:

* [Az Azure-előfizetési szolgáltatási korlátok](../azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Kapcsolódó Azure-összetevők
A Device Provisioning Service az eszközkiépítést az Azure IoT Hub segítségével automatizálja. További információk az [IoT Hubról](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>További lépések
Mostanra nagyjából átlátja az IoT-eszközök Azure-ban való kiépítését. A következő lépés egy teljes körű IoT-forgatókönyv kipróbálása.
> [!div class="nextstepaction"]
> [IoT Hub Device Provisioning Service beállítása az Azure Portallal](quick-setup-auto-provision.md)
> [Szimulált eszköz létrehozása és kiépítése](quick-create-simulated-device.md)
> [Eszköz beállítása a kiépítéshez](tutorial-set-up-device.md)
