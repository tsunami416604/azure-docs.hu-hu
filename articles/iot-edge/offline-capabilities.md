---
title: Peremhálózati eszközeit kapcsolat nélküli – Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg, hogyan IoT Edge-eszközök és a modulok működhet a hosszabb ideig internetkapcsolat nélkül, és hogyan engedélyezheti a IoT Edge a szokásos IoT-eszközök túl kapcsolat nélküli módban működjön.
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6d82b353f8b485b4441853b7ff8e70e7d69f4d6a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986986"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>A IoT Edge eszközök, modulok és alárendelt eszközök kibővített offline képességeinek megismerése

Azure IoT Edge támogatja a kibővített offline műveleteket a IoT Edge-eszközökön, és lehetővé teszi az offline műveletek használatát a nem IoT Edge alárendelt eszközökön is. Mindaddig, amíg az IoT Edge-eszköz csatlakoztatása az IoT hubhoz, egy lehetőség volt-e, és az alárendelt eszközök továbbra is szakaszos függvény vagy nincs internetkapcsolat. 


## <a name="how-it-works"></a>Működés

Amikor egy IoT Edge eszköz offline módba kerül, a IoT Edge hub három szerepkört vesz igénybe. Első lépésként kerülnek a fölérendelt és menti őket, amíg az eszköz újracsatlakozik üzeneteket tárolja. A második működik nevében IoT hubot, hogy a modulok és a gyermek eszközök hitelesítése, hogy továbbra is megfelelően működjenek. Harmadik lehetővé teszi, hogy normális esetben tenné az IoT Hub gyermek eszközök közötti kommunikációt. 

Az alábbi példa bemutatja, hogyan egy IoT Edge-forgatókönyvet a kapcsolat nélküli üzemmódban működik:

1. **Eszközök konfigurálása**

   IoT Edge-eszközök automatikusan offline képességeiről engedélyezve van. Ezt a képességet más IoT-eszközökkel való kiterjesztéséhez kell deklarálja a szülő-gyermek kapcsolatot, az eszközöket az IoT Hub között. Ezt követően konfigurálja úgy a gyermek eszközöket, hogy megbízzanak a hozzárendelt fölérendelt eszközön, és irányítsa az eszközről a felhőbe irányuló kommunikációt a szülőn keresztül átjáróként. 

2. **Szinkronizálás IoT Hub**

   Legalább egyszer az IoT Edge-futtatókörnyezet a telepítés után az IoT Edge-eszköz online állapotban kell lennie az IoT Hub szinkronizáláshoz. A szinkronizálás az IoT Edge-eszköz lesz rendelve gyermek eszközök adatait. Az IoT Edge-eszköz is biztonságosan frissíti a helyi gyorsítótárat offline műveletek engedélyezése és beállításait a helyi tárhely a telemetriai üzeneteket kérdezi le. 

3. **Kapcsolat nélküli üzemmód**

   Az IoT hubról leválasztott gyermekek IoT-eszközökről, az IoT Edge-eszköz és az üzembe helyezett modulok működhet határozatlan időre. A modulok és a gyermek eszközök elindíthatók és újraindíthatók, ha kapcsolat nélküli üzemmódban végeznek hitelesítést az IoT Edge hub használatával. Az IoT Hub felső kötött telemetriai helyben tárolódnak. Kommunikációs modulok vagy gyermek IoT-eszközök között közvetlen metódusok vagy az üzenetek változatlan marad. 

4. **Újrakapcsolás és Újraszinkronizálás IoT Hub**

   Miután visszaállította a kapcsolatot az IoT Hub, IoT Edge-eszközön újra szinkronizálja. Helyileg tárolt üzenetek tárolt is ugyanabban a sorrendben érkeznek. A modulok kívánt és a jelentett tulajdonságok és az eszközök közötti eltérések egyeztetve. Az IoT Edge-eszköz frissíti a módosításokat a csoporthoz hozzárendelt gyermek IoT-eszközök.

## <a name="restrictions-and-limits"></a>Korlátozások és korlátok

A jelen cikkben ismertetett kibővített offline képességek [IoT Edge 1.0.7 vagy újabb verzióban](https://github.com/Azure/azure-iotedge/releases)érhetők el. Korábbi verziók esetében az offline funkciók egy részét. Meglévő IoT Edge kiterjesztett offline képességeiről nem rendelkező eszközök úgy módosítja a futtatókörnyezet verziója nem frissíthető, de egy új IoT Edge eszközidentitással ezek a szolgáltatások eléréséhez a rendszer újra kell konfigurálni. 

A kapcsolat nélküli kiterjesztett technikai támogatás érhető el minden olyan régióban, ahol az IoT Hub érhető el, **kivételével** USA keleti RÉGIÓJA.

Csak a nem IoT Edge eszközök adhatók hozzá alárendelt eszközökként. 

IoT Edge-eszközök és a hozzárendelt gyermek eszközeik működhet határozatlan ideig offline állapotban van, az egyszeri, a kezdeti szinkronizálást követően. Üzenetek tárolása azonban az élettartam (TTL) beállítás, és a rendelkezésre álló lemezterület, az üzenetek tárolására szolgáló idő függ. 

## <a name="set-up-parent-and-child-devices"></a>Szülő és gyermek eszközök beállítása

Ahhoz, hogy egy IoT Edge eszköz kiterjessze kibővített offline képességeit a gyermek IoT-eszközökre, két lépést kell elvégeznie. Először deklarálja a szülő-gyermek kapcsolatokat a Azure Portalban. Másodszor, hozzon létre egy megbízhatósági kapcsolatot a fölérendelt eszköz és az alárendelt eszközök között, majd konfigurálja az eszközről a felhőbe irányuló kommunikációt, hogy az a szülő átjáróként haladjon át. 

### <a name="assign-child-devices"></a>Gyermek-eszközök hozzárendelése

A gyermek eszközök bármely nem IoT Edge eszköz, amely ugyanahhoz a IoT Hub van regisztrálva. A fölérendelt eszközök több alárendelt eszközzel is rendelkezhetnek, de egy alárendelt eszköznek csak egy szülője van. Három lehetőség van a gyermek eszközök peremhálózati eszközre való beállítására: a Azure Portalon keresztül az Azure CLI használatával vagy az IoT Hub Service SDK használatával. 

A következő szakaszokban példákat talál arra, hogyan deklarálhatja a szülő-gyermek kapcsolatot IoT Hub a meglévő IoT-eszközökhöz. Ha új eszköz-identitásokat hoz létre a gyermek eszközökhöz, további információt a következő témakörben talál: [alárendelt eszköz hitelesítése az Azure IoT hub](how-to-authenticate-downstream-device.md) .

#### <a name="option-1-iot-hub-portal"></a>1\. lehetőség: IoT Hub portál

Új eszköz létrehozásakor deklarálhatja a szülő-gyermek kapcsolatot. Vagy a meglévő eszközök esetében deklarálhatja a kapcsolatot a szülő IoT Edge eszköz vagy a gyermek IoT eszköz részletek lapján. 

   ![Gyermek eszközöket kezelheti az IoT Edge-eszköz részleteit tartalmazó oldalra](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>2\. lehetőség: A `az` parancssori eszköz használata

Az [Azure parancssori felület](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) és a IoT- [bővítmény](https://github.com/azure/azure-iot-cli-extension) (v 0.7.0 vagy újabb) használatával az [eszköz-identitás](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) alparancsokkal kezelheti a szülő-gyermek kapcsolatokat. Az alábbi példa egy lekérdezést használ a központban lévő összes nem IoT Edge eszköz hozzárendeléséhez IoT Edge eszköz alárendelt eszközeinek. 

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

A [lekérdezés](../iot-hub/iot-hub-devguide-query-language.md) módosításával kiválaszthatja az eszközök különböző részhalmazát. Ha nagy mennyiségű eszközt ad meg, a parancs több másodpercig is eltarthat.

#### <a name="option-3-use-iot-hub-service-sdk"></a>3\. lehetőség: A IoT Hub Service SDK használata 

Végül a szülő-gyermek kapcsolatokat programozott módon kezelheti a vagy C#a Java vagy a Node. js IOT hub Service SDK használatával. Íme egy példa arra, hogy az C# SDK használatával rendeljen [egy alárendelt eszközt](https://aka.ms/set-child-iot-device-c-sharp) .

### <a name="set-up-the-parent-device-as-a-gateway"></a>A szülő eszköz beállítása átjáróként

Úgy gondolja, hogy egy szülő/gyermek kapcsolat transzparens átjáróként van, ahol a gyermek eszköz saját identitással rendelkezik IoT Hub de a szülőn keresztül kommunikál a felhőben. A biztonságos kommunikációhoz a gyermek eszköznek képesnek kell lennie annak ellenőrzésére, hogy a fölérendelt eszköz megbízható forrásból származik-e. Ellenkező esetben a harmadik felek rosszindulatú eszközöket állíthatnak be a szülők megszemélyesítésére és a kommunikáció elfogására. 

A megbízhatósági kapcsolat létrehozásának egyik módját a következő cikkekben részletesen ismertetjük: 
* [A transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md)
* [Alárendelt (gyermek) eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS-kiszolgálók meghatározása 

A robusztusság javítása érdekében erősen ajánlott megadnia a környezetben használt DNS-kiszolgáló címét. A [DNS-kiszolgáló beállításáról a hibaelhárítási cikkben](troubleshoot.md#resolution-7)két lehetőség látható.

## <a name="optional-offline-settings"></a>Nem kötelező offline beállítások

Ha az eszközök offline állapotba kerülnek, a IoT Edge szülő eszköz az összes eszközről a felhőbe irányuló üzenetet tárolja a kapcsolat újbóli létrehozása előtt. Az IoT Edge hub modul kezeli az offline üzenetek tárolását és továbbítását. Az olyan eszközök esetében, amelyek hosszabb ideig offline állapotba kerülhetnek, optimalizálja a teljesítményt két IoT Edge hub-beállítás konfigurálásával. 

Először is növelje az élő beállítás élettartamát, hogy az IoT Edge hub elég sokáig őrizze meg az üzeneteket, hogy az eszköz újrakapcsolódjon. Ezután adjon hozzá további lemezterületet az üzenetek tárolásához. 

### <a name="time-to-live"></a>Élettartam

Time to live beállítás rendszer mennyi ideig (másodpercben), amely egy üzenetet várhat után járjon le szállítani kell. Az alapértelmezett érték a 7200 másodperc (két óra). A maximális értéket csak egy egész szám változó maximális értéke korlátozza, ami körülbelül 2 000 000 000. 

Ez a beállítás a IoT Edge hub kívánt tulajdonsága, amelyet a különálló modul tárol. A Azure Portal vagy közvetlenül az üzembe helyezési jegyzékben is konfigurálhatja. 

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

### <a name="host-storage-for-system-modules"></a>Host Storage rendszermodulokhoz

Az üzenetek és a modul állapotára vonatkozó információk alapértelmezés szerint az IoT Edge hub helyi tárolójában tárolódnak. A jobb megbízhatóság érdekében, különösen a kapcsolat nélküli üzemmódban, a gazdagép IoT Edge eszközön is kioszthatja a tárolót.

A gazdagéprendszer tárolójának beállításához hozzon létre környezeti változókat az IoT Edge hub számára, és IoT Edge-ügynököt, amely a tároló egyik tárolási mappájára mutat. Ezután használja a létrehozási lehetőségek kötést létrehozni a tároló mappát egy mappát a gazdagépen. 

A környezeti változókat és a IoT Edge hub-modul létrehozási beállításait a **speciális Edge-futtatókörnyezet beállításainak konfigurálása** szakaszban állíthatja be a Azure Portal. 

1. A IoT Edge hub és a IoT Edge Agent esetében is adjon hozzá egy **storageFolder** nevű környezeti változót, amely a modul egyik könyvtárára mutat.
1. A IoT Edge hub és a IoT Edge ügynök esetében adja hozzá a kötéseket, hogy a gazdagépen lévő helyi könyvtárat összekapcsolja a modul egyik könyvtárába. Példa: 

   ![Létrehozási beállítások és környezeti változók hozzáadása a helyi tárolóhoz](./media/offline-capabilities/offline-storage.png)

A helyi tárolót közvetlenül a telepítési jegyzékben is konfigurálhatja. Példa: 

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Cserélje `<HostStoragePath>` le `<ModuleStoragePath>` a és a értéket a gazdagép és a modul tárolási útjára; mindkét értéknek abszolút elérési útnak kell lennie. 

Például azt jelenti `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` , hogy a gazdagép **/etc/iotedge/Storage** lévő címtár a tároló **/iotedge/Storage/** van leképezve. Vagy egy másik példa a Windows rendszerekre `"Binds":["C:\\temp:C:\\contemp"]` : a gazdagépen lévő **c:\\Temp** könyvtárat a rendszer a tárolón lévő **c\\:** . könyvtárba rendeli. 

Linux-eszközök esetén győződjön meg arról, hogy az IoT Edge hub felhasználói profilja, UID 1000, olvasási, írási és végrehajtási engedélyekkel rendelkezik a gazdagép rendszerkönyvtárához. Ezek az engedélyek azért szükségesek, hogy az IoT Edge hub képes legyen üzeneteket tárolni a címtárban, és később beolvasni őket. (A IoT Edge ügynök root-ként működik, ezért nincs szükség további engedélyekre.) A Linux rendszereken több módon is kezelhetők a címtár-engedélyek, például `chown` a használatával módosíthatja a címtár tulajdonosát `chmod` , majd módosíthatja az engedélyeket. Példa:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

További részleteket a [Docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)szolgáltatásbeli létrehozási lehetőségekről talál.

## <a name="next-steps"></a>További lépések

További információ a szülő/gyermek eszköz kapcsolatainak transzparens átjáró beállításáról: 

* [A transzparens átjáróként működő IoT Edge-eszköz konfigurálása](how-to-create-transparent-gateway.md)
* [Alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz](how-to-connect-downstream-device.md)
