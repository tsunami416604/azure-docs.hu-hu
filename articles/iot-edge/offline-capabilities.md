---
title: Peremhálózati eszközeit kapcsolat nélküli – Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg, hogyan IoT Edge-eszközök és a modulok működhet a hosszabb ideig internetkapcsolat nélkül, és hogyan engedélyezheti a IoT Edge a szokásos IoT-eszközök túl kapcsolat nélküli módban működjön.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7bf672715b45233807ab848c78aeb1bed2d352e9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699346"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>Megismerheti a kiterjesztett offline funkcióit az IoT Edge-eszközök, a modulok és a gyermek eszközökön (előzetes verzió)

Az Azure IoT Edge az IoT Edge-eszközök bővített offline műveleteket támogatja, és lehetővé teszi, hogy a gyermek nem peremhálózati eszközökön offline műveletek túl. Mindaddig, amíg az IoT Edge-eszköz csatlakoztatása az IoT hubhoz, egy lehetőség volt-e, és az alárendelt eszközök továbbra is szakaszos függvény vagy nincs internetkapcsolat. 

>[!NOTE]
>Az IoT Edge offline támogatást [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Működés

IoT Edge-eszköz kapcsolat nélküli módba kerül, amikor az IoT Edge hubot a három szerepkört vesz igénybe. Első lépésként kerülnek a fölérendelt és menti őket, amíg az eszköz újracsatlakozik üzeneteket tárolja. A második működik nevében IoT hubot, hogy a modulok és a gyermek eszközök hitelesítése, hogy továbbra is megfelelően működjenek. Harmadik lehetővé teszi, hogy normális esetben tenné az IoT Hub gyermek eszközök közötti kommunikációt. 

Az alábbi példa bemutatja, hogyan egy IoT Edge-forgatókönyvet a kapcsolat nélküli üzemmódban működik:

1. **IoT Edge-eszköz konfigurálása.**

   IoT Edge-eszközök automatikusan offline képességeiről engedélyezve van. Ezt a képességet más IoT-eszközökkel való kiterjesztéséhez kell deklarálja a szülő-gyermek kapcsolatot, az eszközöket az IoT Hub között. 

2. **Szinkronizálás az IoT hubbal.**

   Legalább egyszer az IoT Edge-futtatókörnyezet a telepítés után az IoT Edge-eszköz online állapotban kell lennie az IoT Hub szinkronizáláshoz. A szinkronizálás az IoT Edge-eszköz lesz rendelve gyermek eszközök adatait. Az IoT Edge-eszköz is biztonságosan frissíti a helyi gyorsítótárat offline műveletek engedélyezése és beállításait a helyi tárhely a telemetriai üzeneteket kérdezi le. 

3. **Kapcsolat nélküli módba.**

   Az IoT hubról leválasztott gyermekek IoT-eszközökről, az IoT Edge-eszköz és az üzembe helyezett modulok működhet határozatlan időre. Modulok és a gyermek eszköz indítása, és indítsa újra az IoT Edge hubot közben történő offline állapotban van. Az IoT Hub felső kötött telemetriai helyben tárolódnak. Kommunikációs modulok vagy gyermek IoT-eszközök között közvetlen metódusok vagy az üzenetek változatlan marad. 

4. **Újra és szinkronizálja újra az IoT hubbal.**

   Miután visszaállította a kapcsolatot az IoT Hub, IoT Edge-eszközön újra szinkronizálja. Helyileg tárolt üzenetek tárolt is ugyanabban a sorrendben érkeznek. A modulok kívánt és a jelentett tulajdonságok és az eszközök közötti eltérések egyeztetve. Az IoT Edge-eszköz frissíti a módosításokat a csoporthoz hozzárendelt gyermek IoT-eszközök.

## <a name="restrictions-and-limits"></a>Korlátozások és korlátok

A jelen cikkben ismertetett kiterjesztett offline lehetőségek állnak rendelkezésére [IoT Edge 1.0.4-es verzió vagy újabb](https://github.com/Azure/azure-iotedge/releases). Korábbi verziók esetében az offline funkciók egy részét. Meglévő IoT Edge kiterjesztett offline képességeiről nem rendelkező eszközök úgy módosítja a futtatókörnyezet verziója nem frissíthető, de egy új IoT Edge eszközidentitással ezek a szolgáltatások eléréséhez a rendszer újra kell konfigurálni. 

A kapcsolat nélküli kiterjesztett technikai támogatás érhető el minden olyan régióban, ahol az IoT Hub érhető el, **kivételével** USA keleti RÉGIÓJA.

Csak az Edge IoT-eszközök gyermek eszközöket adhat hozzá. 

IoT Edge-eszközök és a hozzárendelt gyermek eszközeik működhet határozatlan ideig offline állapotban van, az egyszeri, a kezdeti szinkronizálást követően. Üzenetek tárolása azonban az élettartam (TTL) beállítás, és a rendelkezésre álló lemezterület, az üzenetek tárolására szolgáló idő függ. 

## <a name="set-up-an-iot-edge-device"></a>IoT Edge-eszköz beállítása

Az IoT Edge-eszköz gyermek IoT-eszközökön a kiterjesztett offline lehetőségek bővítése céljából kell deklarálni, a szülő-gyermek kapcsolatba az Azure Portalon.

### <a name="assign-child-devices"></a>Gyermek-eszközök hozzárendelése

Gyermek eszközök bármely nem peremhálózati eszköz, a egy IoT-központban regisztrált lehet. A szülő-gyermek kapcsolat létrehozásával új eszköz vagy az eszköz részleteit tartalmazó oldalra, vagy a szülő IoT Edge-eszköz vagy az alárendelt IoT-eszköz is kezelheti. 

   ![Gyermek eszközöket kezelheti az IoT Edge-eszköz részleteit tartalmazó oldalra](./media/offline-capabilities/manage-child-devices.png)

Szülő eszközök több gyermek eszköz rendelkezhet, de egy gyermek eszköz legfeljebb egy szülő.

### <a name="specifying-dns-servers"></a>DNS-kiszolgálók megadása 

Robusztusság javítása érdekében javasoljuk, adja meg a környezetében használt DNS-kiszolgáló címei. Például a Linux, a frissítés **/etc/docker/daemon.json** (szüksége lehet létrehozni a fájlt) a következők:

```json
{
    "dns": [“1.1.1.1”]
}
```

Ha a helyi DNS-kiszolgálót használ, cserélje le a 1.1.1.1 a helyi DNS-kiszolgáló IP-címét. Indítsa újra a docker-szolgáltatást, a módosítások érvénybe léptetéséhez.


## <a name="optional-offline-settings"></a>Nem kötelező offline beállítások

Ha várhatóan gyűjteni az eszközök mennyi ideig offline időszakokban létrehozott összes üzenetet, konfigurálja az IoT Edge hubot, hogy az összes üzenetet tárolhat. Nincsenek a két változik, hogy végezhet IoT Edge hubot hosszú távú tárolási engedélyezéséhez. Először is növelheti time to live beállítás. Adja hozzá a további lemezterületet üzenetek tárolására. 

### <a name="time-to-live"></a>Élettartam

Time to live beállítás rendszer mennyi ideig (másodpercben), amely egy üzenetet várhat után járjon le szállítani kell. Az alapértelmezett érték a 7200 másodperc (két óra). 

Ez a beállítás az IoT Edge hub, amely tárolja az ikermodul kívánt tulajdonságot. Segítségével konfigurálhatja az Azure Portalon, az a **speciális Edge-futtatókörnyezet-beállítások konfigurálása** szakaszt, vagy közvetlenül a központi telepítésben lévő jegyzékfájl. 

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

Például `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` azt jelenti, hogy a könyvtár **/etc/iotedge/storage** a gazdagépen futó rendszer le van képezve a címtár **/iotedge/storage/** a tárolón. Vagy egy másik példa a Windows rendszerek esetében `"Binds":["C:\\temp:C:\\contemp]"` azt jelenti, hogy a könyvtár **C:\\temp** a gazdagépen futó rendszer le van képezve a címtár **C:\\contemp** a tárolón. 

Ugyanitt találhat további információt a beállítások létrehozása [docker docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>További lépések

A kiterjesztett offline műveletek engedélyezése a [transzparens átjáró](how-to-create-transparent-gateway.md) forgatókönyveket.
