---
title: Az Azure IoT Edge offline képességei |} A Microsoft Docs
description: Ismerje meg, hogyan IoT Edge-eszközök és a modulok működhet offline huzamosabb ideig, és hogyan engedélyezheti a IoT Edge a szokásos IoT-eszközök túl kapcsolat nélküli módban működjön.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d84df3e5e0b961b8a53044102f99205ee0fe9896
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914107"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Megismerheti a kiterjesztett offline funkcióit az IoT Edge-eszközök, a modulok és a gyermek eszközökön (előzetes verzió)

Az Azure IoT Edge az IoT Edge-eszközök bővített offline műveleteket támogatja, és lehetővé teszi, hogy a gyermek nem peremhálózati eszközökön offline műveletek túl. Mindaddig, amíg az IoT Edge-eszköz csatlakoztatása az IoT hubhoz, egy lehetőség volt-e, és az alárendelt eszközök továbbra is szakaszos függvény vagy nincs internetkapcsolat. 

>[!NOTE]
>Az IoT Edge offline támogatást [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Működés

IoT Edge-eszköz kapcsolat nélküli módba kerül, ha a három szerepkört az Edge hub vesz igénybe. Első lépésként kerülnek a fölérendelt és menti őket, amíg az eszköz újracsatlakozik üzeneteket tárolja. A második működik nevében IoT hubot, hogy a modulok és a gyermek eszközök hitelesítése, hogy továbbra is megfelelően működjenek. Harmadik lehetővé teszi, hogy normális esetben tenné az IoT Hub gyermek eszközök közötti kommunikációt. 

Az alábbi példa bemutatja, hogyan egy IoT Edge-forgatókönyvet a kapcsolat nélküli üzemmódban működik:

1. **IoT Edge-eszköz konfigurálása.**

   IoT Edge-eszközök automatikusan offline képességeiről engedélyezve van. Ezt a képességet más IoT-eszközökkel való kiterjesztéséhez kell deklarálja a szülő-gyermek kapcsolatot, az eszközöket az IoT Hub között. 

2. **Szinkronizálás az IoT hubbal.**

   Legalább egyszer az IoT Edge-futtatókörnyezet a telepítés után az IoT Edge-eszköz online állapotban kell lennie az IoT Hub szinkronizáláshoz. A szinkronizálás az IoT Edge-eszköz lesz rendelve gyermek eszközök adatait. Az IoT Edge-eszköz is biztonságosan frissíti a helyi gyorsítótárat offline műveletek engedélyezése és beállításait a helyi tárhely a telemetriai üzeneteket kérdezi le. 

3. **Kapcsolat nélküli módba.**

   Az IoT hubról leválasztott gyermekek IoT-eszközökről, az IoT Edge-eszköz és az üzembe helyezett modulok működhet határozatlan időre. Modulok és a gyermek eszköz indítása, és indítsa újra az Edge hub közben történő offline állapotban van. Az IoT Hub felső kötött telemetriai helyben tárolódnak. Kommunikációs modulok vagy gyermek IoT-eszközök között közvetlen metódusok vagy az üzenetek változatlan marad. 

4. **Újra és szinkronizálja újra az IoT hubbal.**

   Miután visszaállította a kapcsolatot az IoT Hub, IoT Edge-eszközön újra szinkronizálja. Helyileg tárolt üzenetek tárolt is ugyanabban a sorrendben érkeznek. A modulok kívánt és a jelentett tulajdonságok és az eszközök közötti eltérések egyeztetve. Az IoT Edge-eszköz frissíti a módosításokat a csoporthoz hozzárendelt gyermek IoT-eszközök.

## <a name="restrictions-and-limits"></a>Korlátozások és korlátok

A jelen cikkben ismertetett kiterjesztett offline lehetőségek állnak rendelkezésére [IoT Edge verzió 1.0.2-es vagy újabb](https://github.com/Azure/azure-iotedge/releases). Korábbi verziók esetében az offline funkciók egy részét. Meglévő IoT Edge kiterjesztett offline képességeiről nem rendelkező eszközök úgy módosítja a futtatókörnyezet verziója nem frissíthető, de egy új IoT Edge eszközidentitással ezek a szolgáltatások eléréséhez a rendszer újra kell konfigurálni. 

Offline kiterjesztett technikai támogatás érhető el minden olyan régióban, ahol az IoT Hub elérhető, kivéve az USA keleti Régiójában és Nyugat-Európa. 

Csak az Edge IoT-eszközök gyermek eszközöket adhat hozzá. 

IoT Edge-eszközök és a hozzárendelt gyermek eszközeik működhet határozatlan ideig offline állapotban van, az egyszeri, a kezdeti szinkronizálást követően. Üzenetek tárolása azonban az élettartam (TTL) beállítás, és a rendelkezésre álló lemezterület, az üzenetek tárolására szolgáló idő függ. 

## <a name="set-up-an-edge-device"></a>Edge-eszköz beállítása

Minden IoT Edge-eszköz, amely kiterjesztett offline időszakokban végrehajtására vonatkozó szándékát állítsa be az IoT Edge-futtatókörnyezet MQTT protokollt használó kommunikációra. 

Az IoT Edge-eszköz gyermek IoT-eszközökön a kiterjesztett offline lehetőségek bővítése céljából kell deklarálni, a szülő-gyermek kapcsolatba az Azure Portalon.

### <a name="set-the-upstream-protocol-to-mqtt"></a>Mqtt-ről a felsőbb rétegbeli protokoll beállítása

Az Edge hub és az Edge agent való kommunikációhoz MQTT felsőbb rétegbeli protokoll konfigurálni. Ez a protokoll deklarálva van, a környezeti változók használatával manifest nasazení. 

Az Azure Portalon érhető el az Edge hub és az Edge agent modulmeghatározásokat kiválasztása a **speciális Edge-futtatókörnyezet-beállítások konfigurálása** gombot, ha a központi modulok beállítása. Mindkét modult, hozzon létre egy környezeti változó nevű **UpstreamProtocol** és az értékét állítsa **MQTT**. 

A központi telepítési sablon JSON-JÁT, a környezeti változók deklarált az alábbi példában látható módon: 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "UpstreamProtocol": {
            "value": "MQTT"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

### <a name="assign-child-devices"></a>Gyermek-eszközök hozzárendelése

Gyermek eszközök bármely nem peremhálózati eszköz, a egy IoT-központban regisztrált lehet. A szülő-gyermek kapcsolat létrehozásával új eszköz vagy az eszköz részleteit tartalmazó oldalra, vagy a szülő IoT Edge-eszköz vagy az alárendelt IoT-eszköz is kezelheti. 

   ![Gyermek eszközöket kezelheti az IoT Edge-eszköz részleteit tartalmazó oldalra](./media/offline-capabilities/manage-child-devices.png)

Szülő eszközök több gyermek eszköz rendelkezhet, de egy gyermek eszköz legfeljebb egy szülő.

## <a name="optional-offline-settings"></a>Nem kötelező offline beállítások

Ha az eszközök mennyi ideig offline időszakok, amely után az összes üzenetet generált, gyűjteni kívánt felfedeznie a várható konfigurálja az Edge hub úgy, hogy az összes üzenetet képes tárolni. Nincsenek a két változik, hogy akkor is győződjön meg arról, az Edge hub hosszú távú tárolási engedélyezéséhez. Előbb növelnie time to live beállítás, és adja hozzá a további lemezterületet üzenetek tárolására. 

### <a name="time-to-live"></a>Élettartam

Time to live beállítás rendszer mennyi ideig (másodpercben), amely egy üzenetet várhat után járjon le szállítani kell. Az alapértelmezett érték a 7200 másodperc (két óra). 

Ez a beállítás az Edge hub, amely tárolja az ikermodul kívánt tulajdonsága. Segítségével konfigurálhatja az Azure Portalon, az a **speciális Edge-futtatókörnyezet-beállítások konfigurálása** szakaszt, vagy közvetlenül a központi telepítésben lévő jegyzékfájl. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>További offline tárolás

Alapértelmezés szerint üzeneteket az Edge hub tároló fájlrendszer vannak tárolva. A tárterület mérete nem elegendő az offline igényeinek, ha az IoT Edge-eszközön a helyi tároló használhat. Az Edge hub egy tároló mappát a tárolóban egy környezeti változó létrehozásához szükséges. Ezután használja a létrehozási lehetőségek kötést létrehozni a tároló mappát egy mappát a gazdagépen. 

Az Azure Portalon konfigurálhatja a környezeti változók és az Edge hub modul a létrehozási lehetőségek a **speciális Edge-futtatókörnyezet-beállítások konfigurálása** szakaszban. Vagy beállíthatja, közvetlenül a manifest nasazení. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"Binds\":[\"<HostStoragePath>:<ModuleStoragePath>\"],\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Cserélje le `<HostStoragePath>` és `<ModuleStoragePath>` a gazdagép és a modul tároló elérési útja; gazdagép és a modul. tárolási elérési útja abszolút elérési útnak kell lennie.  Ha például `\"Binds\":[\"/etc/iotedge/storage/:/iotedge/storage/"` azt jelenti, hogy a gazdagép elérési útja `/etc/iotedge/storage` tároló elérési úthoz csatlakoztatott `/iotedge/storage/`.  További információt a createOptions is annak [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>További lépések

A kiterjesztett offline műveletek engedélyezése a [transzparens átjáró](how-to-create-transparent-gateway.md) forgatókönyveket.
