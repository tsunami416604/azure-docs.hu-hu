---
title: Eszközök működtetése offline - Azure IoT Edge | Microsoft dokumentumok
description: Ismerje meg, hogy az IoT Edge-eszközök és -modulok hogyan működhetnek internetkapcsolat nélkül hosszabb ideig, és hogy az IoT Edge hogyan engedélyezheti a rendszeres IoT-eszközök offline működését is.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 55512491121aee28404ab5f85b4223c67a2f0e1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80236056"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Az IoT Edge-eszközök, modulok és gyermekeszközök kiterjesztett offline képességeinek megismerése

Az Azure IoT Edge támogatja a kiterjesztett offline műveleteket az IoT Edge-eszközökön, és offline műveleteket tesz lehetővé nem IoT Edge-es gyermekeszközökön is. Mindaddig, amíg egy IoT Edge-eszköz nek egyetlen lehetősége van az IoT Hubhoz való csatlakozásra, az eszköz és a gyermekeszközök továbbra is működhetnek időszakosan, vagy nincs internetkapcsolat.

## <a name="how-it-works"></a>Működés

Amikor egy IoT Edge-eszköz offline módban lép, az IoT Edge hub három szerepkört vesz fel. Először is, tárolja az okat az üzeneteket, amelyek felfelé mennek, és elmenti őket, amíg az eszköz újra csatlakozik. Másodszor, az IoT Hub nevében jár el a modulok és a gyermekeszközök hitelesítéséhez, hogy azok továbbra is működhessenek. Harmadszor, lehetővé teszi a gyermekeszközök közötti kommunikációt, amelyek általában az IoT Hubon keresztül jutnának.

A következő példa bemutatja, hogyan működik az IoT Edge-forgatókönyv kapcsolat nélküli módban:

1. **Eszközök konfigurálása**

   Az IoT Edge-eszközök automatikusan engedélyezték az offline funkciókat. A képesség kiterjesztése más IoT-eszközökre, deklarálnia kell egy szülő-gyermek kapcsolatot az eszközök között az IoT Hubban. Ezután konfigurálja a gyermekeszközöket úgy, hogy megbízzon a hozzájuk rendelt szülőeszközben, és az eszközről a felhőbe irányuló kommunikációt a szülőn keresztül átjáróként irányítsa.

2. **Szinkronizálás az IoT Hubbal**

   Legalább egyszer az IoT Edge-futásidejű telepítése után az IoT Edge-eszköz online állapotban kell lennie az IoT Hub szinkronizálásához. Ebben a szinkronizálásban az IoT Edge-eszköz a hozzá rendelt gyermekeszközök részleteit kapja meg. Az IoT Edge-eszköz is biztonságosan frissíti a helyi gyorsítótárat, hogy engedélyezze az offline műveleteket, és lekéri a telemetriai üzenetek helyi tárolásának beállításait.

3. **Kapcsolat nélküli módba váltás**

   Az IoT Hubról leválasztva az IoT Edge-eszköz, az üzembe helyezett modulok és a gyermekek IoT-eszközei határozatlan ideig működhetnek. A modulok és a gyermekeszközök indítása és újraindítása az IoT Edge hub offline hitelesítésével. Az IoT Hubhoz a streamelt hez kötött telemetriai adatok helyileg tárolódnak. A modulok vagy a gyermek IoT-eszközök közötti kommunikáció közvetlen módszerekkel vagy üzenetekkel marad fenn.

4. **Újracsatlakozás és újraszinkronizálás az IoT Hubbal**

   Az IoT Hubbal való kapcsolat visszaállítása után az IoT Edge-eszköz újra szinkronizál. A helyileg tárolt üzenetek azonnal kézbesítve vannak az IoT Hubra, de a kapcsolat sebességétől, az IoT Hub késése és a kapcsolódó tényezők. A szállítás ugyanabban a sorrendben van, ahogyan azieket tárolták.

   A modulok és eszközök kívánt és jelentett tulajdonságai közötti különbségek egyeztetésre kerülnek. Az IoT Edge-eszköz frissíti a hozzárendelt gyermek IoT-eszközök készletének módosításait.

## <a name="restrictions-and-limits"></a>Korlátozások és korlátozások

A cikkben ismertetett kiterjesztett offline funkciók az [IoT Edge 1.0.7-es vagy újabb verziójában](https://github.com/Azure/azure-iotedge/releases)érhetők el. A korábbi verziók az offline szolgáltatások egy részhalmazával rendelkeznek. A meglévő IoT Edge-eszközök, amelyek nem rendelkeznek kiterjesztett offline képességekkel, nem frissíthetők a futásidejű verzió módosításával, de újra kell konfigurálni egy új IoT Edge-eszköz identitással, hogy ezeket a funkciókat elnyerje.

Gyermekeszközökként csak nem IoT Edge-eszközök adhatók hozzá.

Az IoT Edge-eszközök és a hozzájuk rendelt gyermekeszközök a kezdeti, egyszeri szinkronizálás után határozatlan ideig offline módban is működhetnek. Az üzenetek tárolása azonban az élő idő (TTL) beállításátó tól és az üzenetek tárolásához rendelkezésre álló lemezterülettől függ.

## <a name="set-up-parent-and-child-devices"></a>Szülő- és gyermekeszközök beállítása

Ahhoz, hogy egy IoT Edge-eszköz kibővítse a kiterjesztett offline képességeit a gyermek IoT-eszközökre, két lépést kell végrehajtania. Először deklarálja a szülő-gyermek kapcsolatokat az Azure Portalon. Másodszor hozzon létre egy megbízhatósági kapcsolatot a szülő eszköz és bármely gyermekeszköz között, majd konfigurálja az eszközről a felhőbe irányuló kommunikációt, hogy átjáróként haladjon át a szülőn.

### <a name="assign-child-devices"></a>Gyermekeszközök hozzárendelése

A gyermekeszközök bármely nem IoT Edge-eszköz, amely ugyanahhoz az IoT Hubhoz van regisztrálva. A szülőeszközök több gyermekeszközzel is rendelkezhetnek, de egy gyermekeszköznek csak egy szülője van. Három lehetőség van a gyermekeszközök peremhálózati eszközre való beállítására: az Azure Portalon keresztül, az Azure CLI használatával vagy az IoT Hub-szolgáltatás SDK használatával.

Az alábbi szakaszok példákat mutatnak be arra, hogyan deklarálhatja a szülő-gyermek kapcsolatot az IoT Hubban a meglévő IoT-eszközökhöz. Ha új eszközidentitásokat hoz létre a gyermekeszközökhöz, további információért [olvassa el Az alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubhoz](how-to-authenticate-downstream-device.md) című témakört.

#### <a name="option-1-iot-hub-portal"></a>1. lehetőség: IoT Hub portál

Új eszköz létrehozásakor deklarálhatja a szülő-gyermek kapcsolatot. Vagy a meglévő eszközök, deklarálhatja a kapcsolatot az eszköz részleteit lap vagy a szülő IoT Edge-eszköz vagy a gyermek IoT-eszköz.

   ![Gyermekeszközök kezelése az IoT Edge-eszköz részletei lapjáról](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>2. lehetőség: `az` A parancssori eszköz használata

Az [Azure parancssori felületioT-bővítmény](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) (v0.7.0 vagy újabb) használatával kezelheti a szülő gyermek kapcsolatok at az [eszköz-identitás](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) alparancsok. [IoT extension](https://github.com/azure/azure-iot-cli-extension) Az alábbi példa egy lekérdezést használ az összes nem IoT Edge-eszköz hozzárendeléséhez a hub, hogy egy IoT Edge-eszköz gyermekeszközök.

```azurecli
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

A [lekérdezés](../iot-hub/iot-hub-devguide-query-language.md) módosításával az eszközök egy másik részhalmazát választhatja ki. A parancs több másodpercet is igénybe vehet, ha nagy eszközkészletet ad meg.

#### <a name="option-3-use-iot-hub-service-sdk"></a>3. lehetőség: Az IoT Hub szolgáltatás SDK-jának használata

Végül a szülő gyermekkapcsolatokat programozott módon kezelheti a C#, a Java vagy a Node.js IoT Hub Service SDK használatával. Íme [egy példa egy gyermekeszköz hozzárendelésére](https://aka.ms/set-child-iot-device-c-sharp) a C# SDK használatával.

### <a name="set-up-the-parent-device-as-a-gateway"></a>A szülőeszköz beállítása átjáróként

A szülő-gyermek kapcsolat egy átlátszó átjáró, ahol a gyermek eszköz saját identitással rendelkezik az IoT Hubban, de a felhőn keresztül kommunikál a szülő. A biztonságos kommunikáció érdekében a gyermekeszköznek képesnek kell lennie annak ellenőrzésére, hogy a szülőeszköz megbízható forrásból származik-e. Ellenkező esetben a harmadik felek rosszindulatú eszközöket állíthatnak be a szülők megszemélyesítésére és a kommunikáció lehallgatására.

A bizalmi kapcsolat létrehozásának egyik módja a következő cikkekben található:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Alsóbb rétegbeli (gyermek) eszköz csatlakoztatása egy Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>DNS-kiszolgálók megadása

A robusztusság növelése érdekében erősen ajánlott megadni a környezetben használt DNS-kiszolgálócímeket. A DNS-kiszolgáló IoT Edge beállítása, tekintse meg a felbontás [tedge ügynök modul folyamatosan jelentések "üres konfigurációs fájl", és nem modulok indulnak](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) el az eszközön a hibaelhárítási cikkben.

## <a name="optional-offline-settings"></a>Választható kapcsolat nélküli beállítások

Ha az eszközök offline állapotba kerül, az IoT Edge szülőeszköz tárolja az összes eszköz-felhő üzeneteket, amíg a kapcsolat újbóli létrehozásáig. Az IoT Edge hub modul kezeli az offline üzenetek tárolását és továbbítását. Olyan eszközök esetén, amelyek hosszabb ideig offline állapotba kerülhetnek, optimalizálja a teljesítményt két IoT Edge-hub-beállítás konfigurálásával.

Először növelje az élő beállítás, hogy az IoT Edge hub megtartja az üzeneteket elég hosszú ahhoz, hogy az eszköz újra csatlakozni. Ezután adjon hozzá további lemezterületet az üzenetek tárolásához.

### <a name="time-to-live"></a>Élettartam

Az élő beállítás az az idő (másodpercben), ameddig az üzenet kézbesíthető, mielőtt lejár. Az alapértelmezett érték 7200 másodperc (két óra). A maximális értéket csak egy egész változó maximális értéke korlátozza, amely körülbelül 2 milliárd.

Ez a beállítás az IoT Edge hub kívánt tulajdonsága, amely az ikermodulban van tárolva. Konfigurálhatja az Azure Portalon vagy közvetlenül a központi telepítési jegyzékben.

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

### <a name="host-storage-for-system-modules"></a>Rendszermodulok gazdatárolása

Az üzenetek és a modulállapot-információk alapértelmezés szerint az IoT Edge hub helyi tárolófájlrendszerében tárolódnak. A nagyobb megbízhatóság érdekében, különösen offline módban, a gazdaIoEdge-eszközön is feloszthatja a tárhelyet. További információ: [Modulok hozzáférése az eszköz helyi tárhelyéhez](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>További lépések

További információ aszülő-gyermekeszköz-kapcsolatok transzparens átjárójának beállításáról:

* [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](how-to-create-transparent-gateway.md)
* [Lefelé irányuló eszköz hitelesítése az Azure IoT Hubon](how-to-authenticate-downstream-device.md)
* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)
