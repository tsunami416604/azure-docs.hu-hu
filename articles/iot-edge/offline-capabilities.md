---
title: Peremhálózati eszközeit kapcsolat nélküli – Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg, hogyan IoT Edge-eszközök és a modulok működhet a hosszabb ideig internetkapcsolat nélkül, és hogyan engedélyezheti a IoT Edge a szokásos IoT-eszközök túl kapcsolat nélküli módban működjön.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4f3e5c1566271573b43e24a1749b42daa7530555
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051948"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Az IoT Edge-eszközök, a modulok és a gyermek eszközök bővített offline képességekről

Az Azure IoT Edge az IoT Edge-eszközök bővített offline műveleteket támogatja, és lehetővé teszi az IoT Edge gyermek eszközökön offline műveletek túl. Mindaddig, amíg az IoT Edge-eszköz csatlakoztatása az IoT hubhoz, egy lehetőség volt-e, és az alárendelt eszközök továbbra is szakaszos függvény vagy nincs internetkapcsolat. 


## <a name="how-it-works"></a>Működés

IoT Edge-eszköz kapcsolat nélküli módba kerül, amikor az IoT Edge hubot a három szerepkört vesz igénybe. Első lépésként kerülnek a fölérendelt és menti őket, amíg az eszköz újracsatlakozik üzeneteket tárolja. A második működik nevében IoT hubot, hogy a modulok és a gyermek eszközök hitelesítése, hogy továbbra is megfelelően működjenek. Harmadik lehetővé teszi, hogy normális esetben tenné az IoT Hub gyermek eszközök közötti kommunikációt. 

Az alábbi példa bemutatja, hogyan egy IoT Edge-forgatókönyvet a kapcsolat nélküli üzemmódban működik:

1. **Eszközök konfigurálása**

   IoT Edge-eszközök automatikusan offline képességeiről engedélyezve van. Ezt a képességet más IoT-eszközökkel való kiterjesztéséhez kell deklarálja a szülő-gyermek kapcsolatot, az eszközöket az IoT Hub között. Ezután konfigurálnia a gyermek eszközök megbízható hozzárendelt szülő eszközükön, és irányíthatja az eszközről a felhőbe való kommunikáció keretében a szülő átjáróként. 

2. **Az IoT Hub szinkronizálása**

   Legalább egyszer az IoT Edge-futtatókörnyezet a telepítés után az IoT Edge-eszköz online állapotban kell lennie az IoT Hub szinkronizáláshoz. A szinkronizálás az IoT Edge-eszköz lesz rendelve gyermek eszközök adatait. Az IoT Edge-eszköz is biztonságosan frissíti a helyi gyorsítótárat offline műveletek engedélyezése és beállításait a helyi tárhely a telemetriai üzeneteket kérdezi le. 

3. **Kapcsolat nélküli módba**

   Az IoT hubról leválasztott gyermekek IoT-eszközökről, az IoT Edge-eszköz és az üzembe helyezett modulok működhet határozatlan időre. Modulok és a gyermek eszköz indítása, és indítsa újra az IoT Edge hubot közben történő offline állapotban van. Az IoT Hub felső kötött telemetriai helyben tárolódnak. Kommunikációs modulok vagy gyermek IoT-eszközök között közvetlen metódusok vagy az üzenetek változatlan marad. 

4. **Újra és szinkronizálja újra az IoT hubbal**

   Miután visszaállította a kapcsolatot az IoT Hub, IoT Edge-eszközön újra szinkronizálja. Helyileg tárolt üzenetek tárolt is ugyanabban a sorrendben érkeznek. A modulok kívánt és a jelentett tulajdonságok és az eszközök közötti eltérések egyeztetve. Az IoT Edge-eszköz frissíti a módosításokat a csoporthoz hozzárendelt gyermek IoT-eszközök.

## <a name="restrictions-and-limits"></a>Korlátozások és korlátok

A jelen cikkben ismertetett kiterjesztett offline lehetőségek állnak rendelkezésére [IoT Edge 1.0.4-es verzió vagy újabb](https://github.com/Azure/azure-iotedge/releases). Korábbi verziók esetében az offline funkciók egy részét. Meglévő IoT Edge kiterjesztett offline képességeiről nem rendelkező eszközök úgy módosítja a futtatókörnyezet verziója nem frissíthető, de egy új IoT Edge eszközidentitással ezek a szolgáltatások eléréséhez a rendszer újra kell konfigurálni. 

A kapcsolat nélküli kiterjesztett technikai támogatás érhető el minden olyan régióban, ahol az IoT Hub érhető el, **kivételével** USA keleti RÉGIÓJA.

Gyermek-eszközök csak az IoT Edge-eszközöket adhat hozzá. 

IoT Edge-eszközök és a hozzárendelt gyermek eszközeik működhet határozatlan ideig offline állapotban van, az egyszeri, a kezdeti szinkronizálást követően. Üzenetek tárolása azonban az élettartam (TTL) beállítás, és a rendelkezésre álló lemezterület, az üzenetek tárolására szolgáló idő függ. 

## <a name="set-up-parent-and-child-devices"></a>Szülő és gyermek eszközök beállítása

Az IoT Edge-eszköz gyermek IoT-eszközökön a kiterjesztett offline lehetőségek bővítése céljából végezze el a két lépést kell. Először is deklarálni a szülő-gyermek kapcsolatok az Azure Portalon. Második a szülő eszköz és bármely gyermek eszközök közötti megbízhatósági kapcsolat létrehozásához, majd haladjon végig a szülő átjáróként eszközről a felhőbe való kommunikáció konfigurálása a. 

### <a name="assign-child-devices"></a>Gyermek-eszközök hozzárendelése

Gyermek az eszközök lehetnek bármely IoT Edge eszköz regisztrált azonos IoT hubon. Szülő eszközök rendelkezhet több gyermek eszközt, de csak akkor van egy szülő gyermek eszköz. Gyermek eszközök beállítása egy edge-eszközön három lehetőség áll rendelkezésre: az Azure CLI használatával az Azure Portalon keresztül, vagy használja az IoT Hub szolgáltatási SDK-val. 

A következő szakaszok példái milyen eszközhöz adhat meg a szülő-gyermek kapcsolat a meglévő IoT-eszközök az IoT Hub. Ha próbál létrehozni új eszközidentitások gyermeke eszközök, tekintse meg [hitelesítése egy alsóbb rétegbeli eszközök Azure IoT hubra](how-to-authenticate-downstream-device.md) további információt.

#### <a name="option-1-iot-hub-portal"></a>Option 1: Az IoT Hub portálon

A szülő-gyermek kapcsolat eszközhöz adhat meg egy új eszköz létrehozása során. Vagy a meglévő eszközök esetében a kapcsolat az eszköz részleteit megjelenítő oldalon vagy a szülő IoT Edge-eszköz vagy az alárendelt IoT-eszköz is deklarálja. 

   ![Gyermek eszközöket kezelheti az IoT Edge-eszköz részleteit tartalmazó oldalra](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Option 2: Használja a `az` parancssori eszköz

Használatával a [Azure parancssori felület](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) a [IoT-bővítmény](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 vagy újabb), a szülő-gyermek típusú kapcsolatokat kezelheti a [device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) almenüpontok. Az alábbi példa egy lekérdezést használ az agyban az alárendelt az eszközöket az IoT Edge-eszköz lehet minden IoT Edge-eszközök hozzárendelése. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

Módosíthatja a [lekérdezés](../iot-hub/iot-hub-devguide-query-language.md) válassza ki az eszközöket egy másik részét. A parancs eltarthat néhány másodpercig, ha rengeteg eszközök adja meg.

#### <a name="option-3-use-iot-hub-service-sdk"></a>3\. lehetőség: Use IoT Hub Service SDK 

Végül szülő-gyermek típusú kapcsolatokat használatával programozott módon kezelheti C#, Java vagy Node.js IoT Hub szolgáltatási SDK-t. Íme egy [példa a gyermeke eszköz hozzárendelése](https://aka.ms/set-child-iot-device-c-sharp) használatával a C# SDK-t.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Állítsa be a szülő eszközt átjáróként

Transzparens átjáróként, ahol a gyermek eszköz saját azonosítóval rendelkezik az IoT Hub, de a szülő-n keresztül a felhőn keresztül kommunikál egy szülő-gyermek kapcsolat is felfoghatók. A biztonságos kommunikáció érdekében a gyermek eszköz képesnek kell lennie ellenőrizni, hogy a szülő eszköz megbízható forrásból származik-e. Ellenkező esetben a harmadik féltől megszemélyesíteni a szülők és kommunikációs intercept rosszindulatú eszközök beállítása sikerült. 

A bizalmi kapcsolat létrehozásának egyik módja a következő cikkek részletesen ismertetjük: 
* [A transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md)
* [Egy alárendelt (gyermek) eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Adja meg a DNS-kiszolgálók 

Robusztusság javítása érdekében azt javasoljuk, adja meg, hogy a környezetében használt DNS-kiszolgáló címei. A két lehetőség jelenik meg [állítsa be a DNS-kiszolgáló a hibaelhárítási cikk](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Nem kötelező offline beállítások

Az eszközök kapcsolat nélküli módba, ha az szülő IoT Edge-eszközön tárolja, amíg a kapcsolat helyreállt az összes eszköz – felhő üzeneteket. The IoT Edge hub module manages the storage and forwarding of offline messages. Olyan eszközöknél, előfordulhat, hogy kapcsolat nélküli módba a hosszabb ideig a teljesítmény optimalizálása két IoT Edge hub beállításainak konfigurálásával. 

Először növelje élő beállítása úgy, hogy az IoT Edge hub az eszköz újracsatlakoztatása dlouhé üzeneteket fogja megőrizni a időt. Adja hozzá a további lemezterületet üzenetek tárolására. 

### <a name="time-to-live"></a>Élettartam

Time to live beállítás rendszer mennyi ideig (másodpercben), amely egy üzenetet várhat után járjon le szállítani kell. Az alapértelmezett érték a 7200 másodperc (két óra). Egy egész szám típusú változó, amely körülbelül 2 milliárd maximális értéke csak korlátozza a maximális értéket. 

Ez a beállítás az IoT Edge hub, amely tárolja az ikermodul kívánt tulajdonságot. Az Azure Portalon, vagy közvetlenül a manifest nasazení konfigurálhatja. 

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

Üzenetek az IoT Edge hubot tároló fájlrendszer alapértelmezés szerint tárolják. A tárterület mérete nem elegendő az offline igényeinek, ha az IoT Edge-eszközön a helyi tároló használhat. Hozzon létre egy környezeti változót az IoT Edge hub egy tároló mappát a tárolóban. Ezután használja a létrehozási lehetőségek kötést létrehozni a tároló mappát egy mappát a gazdagépen. 

Az Azure Portalon konfigurálhatja a környezeti változók és az IoT Edge hubot modul a létrehozási lehetőségek a **speciális Edge-futtatókörnyezet-beállítások konfigurálása** szakaszban. Vagy beállíthatja, közvetlenül a manifest nasazení. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
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

Cserélje le `<HostStoragePath>` és `<ModuleStoragePath>` a gazdagép és a modul tároló elérési útja; gazdagép és a modul. tárolási elérési útja abszolút elérési útnak kell lennie. A létrehozás beállítások kötése a gazdagép és a modul tárolási útvonalat együtt. Ezután hozzon létre egy környezeti változó, amely a modul elérési útja.  

Például `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` azt jelenti, hogy a könyvtár **/etc/iotedge/storage** a gazdagépen futó rendszer le van képezve a címtár **/iotedge/storage/** a tárolón. Vagy egy másik példa a Windows rendszerek esetében `"Binds":["C:\\temp:C:\\contemp"]` azt jelenti, hogy a könyvtár **C:\\temp** a gazdagépen futó rendszer le van képezve a címtár **C:\\contemp** a tárolón. 

Ugyanitt találhat további információt a beállítások létrehozása [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>További lépések

További információ a szülő-gyermek-eszköz kapcsolatok esetében transzparens átjáró beállítása: 

* [A transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md)
* [Az Azure IoT hubra egy alsóbb rétegbeli eszköz hitelesítéséhez](how-to-authenticate-downstream-device.md)
* [Egy alárendelt eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md)
