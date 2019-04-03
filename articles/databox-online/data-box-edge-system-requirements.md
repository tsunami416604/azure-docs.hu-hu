---
title: A Microsoft Azure Data Box Edge követelményei |} A Microsoft Docs
description: Ismerje meg a szoftver- és az Azure Data Box Edge hálózati követelményei
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: 720f4433c1e7b0d271e5729236f439c92a84687e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884652"
---
# <a name="azure-data-box-edge-system-requirements"></a>Az Azure Data Box Edge rendszerre vonatkozó követelmények

Ez a cikk a Microsoft Azure Data Box peremhálózati megoldás és a kapcsolódó ügyfelek számára az Azure Data Box Edge fontos rendszerkövetelményeit ismerteti. Azt javasoljuk, hogy tekintse át az információkat gondosan a Data Box Edge üzembe helyezése előtt. Ezt az információt szükség szerint a telepítési és az ezt követő művelet során vissza hivatkozhat.

A Data Box Edge rendszerkövetelményei a következők:

- **Gazdagépek szoftverkövetelményei** – a támogatott platformok, a helyi konfigurációs felhasználói Felületet a böngészőben, SMB-kliensek és az ügyfelek, amelyek az eszköz elérésére vonatkozó esetleges további követelményeket ismerteti.
- **Az eszköz hálózatkezelési követelményei** – ismerteti a művelet a fizikai eszköz vonatkozó hálózati követelmények.

## <a name="supported-os-for-clients-connected-to-device"></a>Az eszközhöz csatlakoztatott ügyfelek támogatott operációs rendszer

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Az eszközhöz hozzáférő ügyfelek támogatott protokollok

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Támogatott tárolási típus

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Helyi webes felhasználói felület által támogatott böngészők

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Hálózatkezelési port követelményei

### <a name="port-requirements-for-data-box-edge"></a>Data Box Edge port követelményei

Az alábbi táblázat felsorolja a portot, amelyet meg kell nyitni a tűzfalon az SMB, a felhőben és a felügyeleti forgalom engedélyezése. Ebben a táblázatban *a* vagy *bejövő* irányát hivatkozik, mely a bejövő kérések ügyfélelérési az eszközre. *Ki* vagy *kimenő* , amelyben a Data Box peremhálózati eszköz adatokat küld kívülről, a telepítés túl például kimenő Internet irányát hivatkozik.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge port követelményei

Az Azure IoT Edge lehetővé teszi, hogy az Azure-felhőben az IoT Hub támogatott protokollok használatával a helyszíni peremhálózati eszköz a kimenő kommunikáció. Csak a bejövő kommunikációhoz szükséges bizonyos forgatókönyvek esetén, ahol az Azure IoT Hub kell le üzeneteket leküldése az Azure IoT Edge-eszköz (például eszközök üzenetküldése a felhő).

Használja az alábbi táblázat port konfigurálása az Azure IoT Edge-futtatókörnyezet üzemeltető kiszolgálók:

| Port nem. | És leskálázása | Port hatókör | Szükséges | Útmutatás |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Ki       | WAN        | Igen      | Kimenő nyissa meg az IoT Edge-kiépítés. Ez a konfiguráció manuális parancsprogramokkal vagy az Azure IoT Device Provisioning Service (DPS) szükség.|

Teljes körű információkért látogasson el a [tűzfal és az IoT Edge üzembe helyezési konfigurációs portszabályok](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>URL-mintákra vonatkozó tűzfalszabályok

A hálózati rendszergazdák gyakran konfigurálhatja a speciális tűzfalszabályokat az URL-mintákra szűrése a bejövő és kimenő forgalom alapján. A Data Box Edge-eszköz és a szolgáltatás függenek más Microsoft-alkalmazások, például az Azure Service Bus, az Azure Active Directory hozzáférés-vezérlés, tárfiókok és Microsoft Update-kiszolgálókon. Az ezekhez az alkalmazásokhoz tartozó URL-mintákra tűzfalszabályok konfigurálásához használható. Fontos megérteni, hogy az ezekhez az alkalmazásokhoz tartozó URL-mintákra módosíthatja. Ezeket a módosításokat a hálózati rendszergazda figyelheti és frissítheti a tűzfalszabályok a Data Box Edge, és szükség esetén szükséges.

Azt javasoljuk, hogy beállította-e a tűzfalszabályok liberally fix IP-címeinek, a legtöbb esetben a Data Box Edge alapján, a kimenő forgalom számára. Az alábbi információk segítségével azonban, melyek szükségesek ahhoz, hogy biztonságos környezetek létrehozása speciális tűzfal-szabályokat állíthat be.

> [!NOTE]
> - Az eszköz (forrás) IP-címek mindig a felhő-kompatibilis hálózati adaptereken értékre kell állítani.
> - IP-címeket kell megadni a cél [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Az átjáró szolgáltatás URL-minta

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Számítási szolgáltatás URL-minta

| Az URL-minta                      | Összetevő vagy funkció                     |   |
|----------------------------------|---------------------------------------------|---|
| `https://mcr.microsoft.com`<br></br>https://\*.cdn.mscr.io | A Microsoft a tárolóregisztrációs adatbázis (szükséges)               |   |
| https://\*.azurecr.io                     | Személyes, mind a külső tároló-beállításjegyzékek (nem kötelező) |   |
| https://\*.azure-devices.net              | Az IoT Hub-hozzáférés (kötelező)                             |   |

### <a name="url-patterns-for-azure-government"></a>Az Azure Government URL-minta

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internetes sávszélesség

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Következő lépés

- [Az Azure Data Box Edge üzembe helyezése](data-box-edge-deploy-prep.md)
