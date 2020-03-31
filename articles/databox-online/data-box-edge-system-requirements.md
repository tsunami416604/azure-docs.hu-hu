---
title: Microsoft Azure Data Box Edge rendszerkövetelményei| Microsoft dokumentumok
description: Ismerje meg az Azure Data Box Edge szoftver- és hálózati követelményeit
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260227"
---
# <a name="azure-data-box-edge-system-requirements"></a>Az Azure Data Box Edge rendszerkövetelményei

Ez a cikk ismerteti a fontos rendszerkövetelmények a Microsoft Azure Data Box Edge megoldás és az Azure Data Box Edge-hez csatlakozó ügyfelek számára. Javasoljuk, hogy figyelmesen tekintse át az információkat, mielőtt üzembe helyezná a Data Box Edge-et. Erre az információkra szükség szerint hivatkozhat a központi telepítés és az azt követő művelet során.

A Data Box Edge rendszerkövetelményei a következők:

- **A gazdagépek szoftverkövetelményei** – ismerteti a támogatott platformokat, a helyi konfigurációs felhasználói felület böngészőit, az SMB-ügyfeleket, valamint az eszközt elérő ügyfelekre vonatkozó további követelményeket.
- **Hálózati követelmények az eszköz** - tájékoztatást nyújt a hálózati követelmények a fizikai eszköz működéséhez.

## <a name="supported-os-for-clients-connected-to-device"></a>Támogatott operációs rendszer az eszközhöz csatlakoztatott ügyfelek számára

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Az eszközt elérő ügyfelek támogatott protokolljai

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Támogatott tárolótípusok

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Támogatott böngészők a helyi webes felhasználói felülethez

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Hálózati portkövetelmények

### <a name="port-requirements-for-data-box-edge"></a>A Data Box Edge portkövetelményei

Az alábbi táblázat azokat a portokat sorolja fel, amelyeket meg kell nyitni a tűzfalon az SMB, a felhő vagy a felügyeleti forgalom engedélyezéséhez. Ebben a *táblában a bejövő* vagy *bejövő* arra az irányra utal, amelyből a bejövő ügyfél hozzáférést kér az eszközhöz. *Kimenő* vagy *kimenő* utal, amelyben a Data Box Edge eszköz adatokat küld külsőleg, a központi telepítésen túl, például az internetfelé.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Az IoT Edge portkövetelményei

Az Azure IoT Edge lehetővé teszi a kimenő kommunikációt egy helyszíni Edge-eszközről az Azure-felhőbe a támogatott IoT Hub-protokollok használatával. Bejövő kommunikáció csak akkor szükséges, bizonyos forgatókönyvek, ahol az Azure IoT Hub kell lenyomni az üzeneteket az Azure IoT Edge-eszköz (például felhőről eszközre üzenetküldés).

Használja az alábbi táblázatot az Azure IoT Edge-futásidejű tüzemeltető kiszolgálók portkonfigurációjához:

| Port száma | Be- vagy ki | Port hatóköre | Kötelező | Útmutatás |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Ki       | WAN        | Igen      | Kimenő nyitott Az IoT Edge-kiépítés. Ez a konfiguráció manuális parancsfájlok vagy Az Azure IoT-eszközlétesítési szolgáltatás (DPS) használata esetén szükséges.|

A teljes körű információkért látogasson el [a tűzfal és a port konfigurációs szabályok IoT Edge telepítéséhez.](https://docs.microsoft.com/azure/iot-edge/troubleshoot)

## <a name="url-patterns-for-firewall-rules"></a>A tűzfalszabályok URL-mintái

A hálózati rendszergazdák gyakran konfigurálhatnak speciális tűzfalszabályokat az URL-minták alapján a bejövő és a kimenő forgalom szűréséhez. A Data Box Edge-eszköz és a szolgáltatás más Microsoft-alkalmazásoktól, például az Azure Service Bustól, az Azure Active Directory hozzáférés-vezérléstől, a tárfiókoktól és a Microsoft Update-kiszolgálóktól függ. Az ezekhez az alkalmazásokhoz társított URL-minták a tűzfalszabályok konfigurálására használhatók. Fontos megérteni, hogy az ezekhez az alkalmazásokhoz társított URL-minták változhatnak. Ezek a változások megkövetelik, hogy a hálózati rendszergazda szükség szerint figyelje és frissítse a Data Box Edge tűzfalszabályait.

Azt javasoljuk, hogy a tűzfal szabályok at kimenő forgalom alapján Data Box Edge rögzített IP-címek, a legtöbb esetben bőségesen. Az alábbi információk segítségével azonban speciális tűzfalszabályokat állíthat be, amelyek a biztonságos környezetek létrehozásához szükségesek.

> [!NOTE]
> - Az eszköz (forrás) IP-k mindig be kell állítani, hogy az összes felhő-kompatibilis hálózati adapterek.
> - A cél IP-címeket [az Azure adatközpont IP-tartományaira](https://www.microsoft.com/download/confirmation.aspx?id=41653)kell állítani.

### <a name="url-patterns-for-gateway-feature"></a>AZ átjárószolgáltatás URL-mintái

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>A számítási funkció URL-mintái

| URL-minta                      | Összetevő vagy funkció                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft tárolóbeállítás-nyilvántartás (kötelező)               |
| https://\*.azurecr.io                     | Személyes és külső tárolónyilvántartások (nem kötelező) | 
| https://\*.azure-devices.net              | IoT Hub-hozzáférés (kötelező)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Az Azure Government átjárójának URL-mintái

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Az Azure Government számítási URL-mintái

| URL-minta                      | Összetevő vagy funkció                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft tárolóbeállítás-nyilvántartás (kötelező)               |
| https://\*.azure-devices.us              | IoT Hub-hozzáférés (kötelező)           |
| https://\*.azurecr.us                    | Személyes és külső tárolónyilvántartások (nem kötelező) | 

## <a name="internet-bandwidth"></a>Internetes sávszélesség

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Számítási méretezési szempontok

Használja a tapasztalatfejlesztése és tesztelése során a megoldást annak érdekében, hogy elegendő kapacitás legyen a Data Box Edge eszközön, és az eszköz optimális teljesítményt nyújtson.

A következő tényezőket kell figyelembe vennie:

- **Konténer-specifikus -** Gondoljon a következőkre.

    - Hány tároló van a számítási feladatokban? Lehet, hogy egy csomó könnyű tárolók szemben néhány erőforrás-igényes is.
    - Melyek a tárolókszámára lefoglalt erőforrások, szemben az általuk felhasznált erőforrásokkal?
    - Hány réteget osztanak meg a tárolók?
    - Vannak használaton kívüli konténerek? A leállított tároló továbbra is lemezterületet foglal.
    - Milyen nyelven vannak megírva a tárolók?
- **A feldolgozott adatok mérete** – Mennyi adatot fognak feldolgozni a tárolók? Ezek az adatok lemezterületet foglalnak el, vagy az adatok fellesznek dolgozva a memóriában?
- **Várható teljesítmény** – Melyek a megoldás kívánt teljesítményjellemzői? 

A megoldás teljesítményének megértéséhez és finomításához a következőket használhatja:

- Az Azure Portalon elérhető számítási metrikák. Nyissa meg a Data Box Edge erőforrást, majd nyissa meg **a Figyelés > metrikák .** Tekintse meg az **Edge-számítás – Memóriahasználat** és **Edge-számítás – Százalékos CPU** a rendelkezésre álló erőforrások megértéséhez, és hogyan kerülnek az erőforrások at.
- Az eszköz PowerShell-felületén keresztül elérhető figyelési parancsok, például:

    - `dkrdbe stats`a tároló(k) erőforrás-használati statisztikáinak élő közvetítésének leéséhez. A parancs támogatja a PROCESSZOR, a memóriahasználat, a memóriakorlát és a hálózati IO-metrikákat.
    - `dkrdbe system df`a felhasznált lemezterülettel kapcsolatos információk beszerezéséhez. 
    - `dkrdbe image prune`a nem használt képek megtisztításához és a szabad hely felszabadításához.
    - `dkrdbe ps --size`a futó tároló hozzávetőleges méretének megtekintéséhez. 

    A rendelkezésre álló parancsokkal kapcsolatos további információkért látogasson el a [Számítási modulok figyelése és hibaelhárítása című témakörbe.](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)

Végül győződjön meg arról, hogy érvényesíti a megoldást az adatkészleten, és számszerűsíti a teljesítményt a Data Box Edge éles környezetben üzembe helyezése előtt.


## <a name="next-step"></a>Következő lépés

- [Az Azure Data Box Edge üzembe helyezése](data-box-edge-deploy-prep.md)
