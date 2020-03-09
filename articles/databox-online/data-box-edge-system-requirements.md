---
title: Microsoft Azure Data Box Edge rendszerkövetelményei | Microsoft Docs
description: Tudnivalók a Azure Data Box Edge szoftveres és hálózati követelményeiről
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384757"
---
# <a name="azure-data-box-edge-system-requirements"></a>Rendszerkövetelmények Azure Data Box Edge

Ez a cikk a Microsoft Azure Data Box Edge megoldás és a Azure Data Box Edgehoz csatlakozó ügyfelek fontos rendszerkövetelményeit ismerteti. Javasoljuk, hogy figyelmesen tekintse át az információkat, mielőtt telepítené a Data Box Edge. Az üzembe helyezés és az azt követő művelet során szükség szerint visszatérhet ezekre az adatokra.

A Data Box Edge rendszerkövetelményei a következők:

- A **gazdagépek szoftveres követelményei** – a támogatott platformokat, a helyi konfiguráció felhasználói felületének böngészőit, az SMB-ügyfeleket, valamint az eszközt elérő ügyfelekre vonatkozó további követelményeket ismerteti.
- **Az eszköz hálózati követelményei** – információt nyújt a fizikai eszköz működésével kapcsolatos hálózati követelményekről.

## <a name="supported-os-for-clients-connected-to-device"></a>Az eszközhöz csatlakoztatott ügyfelek támogatott operációs rendszere

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Az eszközt elérő ügyfelek által támogatott protokollok

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Támogatott tárolási típus

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>A helyi webes felhasználói felület által támogatott böngészők

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Hálózati portokra vonatkozó követelmények

### <a name="port-requirements-for-data-box-edge"></a>A Data Box Edge portjának követelményei

A következő táblázat felsorolja azokat a portokat, amelyeket meg kell nyitni a tűzfalon az SMB-, felhő-vagy felügyeleti forgalom engedélyezéséhez. A (z) vagy a *bejövő* tábla a bejövő ügyfél által az eszközhöz való hozzáférést kérő irányt jelöli. A *kimenő vagy kimenő* *állapot arra utal* , hogy a Data Box Edge-eszköz hogyan küldi el az adatokat külsőleg, az üzemelő példányon kívül, például az internet felé.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>A IoT Edge portjának követelményei

Azure IoT Edge lehetővé teszi a kimenő kommunikációt egy helyszíni peremhálózati eszközről az Azure-felhőbe a támogatott IoT Hub protokollok használatával. A bejövő kommunikációra csak bizonyos esetekben van szükség, amikor az Azure IoT Hub üzeneteket kell leküldenie az Azure IoT Edge eszköznek (például felhőből az eszközre).

Használja a következő táblázatot a port konfigurálásához a Azure IoT Edge futtatókörnyezetet futtató kiszolgálókon:

| Port nem. | Be vagy ki | Port hatóköre | Kötelező | Útmutatás |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Ki       | WAN        | Igen      | A kimenő Megnyitás IoT Edge kiépítés esetén. Ez a konfiguráció manuális parancsprogramokkal vagy az Azure IoT Device Provisioning Service (DPS) szükség.|

A teljes információ: a [tűzfal és a port konfigurációs szabályai IoT Edge központi telepítéshez](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Tűzfalszabályok URL-mintái

A hálózati rendszergazdák gyakran konfigurálhatják a speciális tűzfalszabályok alapján a bejövő és a kimenő forgalom szűrésére szolgáló URL-mintákat. A Data Box Edge eszköz és a szolgáltatás más Microsoft-alkalmazásokkal (például Azure Service Bus, Azure Active Directory Access Control, Storage-fiókokkal és Microsoft Update-kiszolgálókkal függ. Az ezekhez az alkalmazásokhoz társított URL-mintákat a tűzfalszabályok konfigurálására lehet használni. Fontos megérteni, hogy az alkalmazásokhoz társított URL-minták megváltoztathatók. Ezekhez a változásokhoz a hálózati rendszergazdának kell megfigyelnie és frissítenie a tűzfal szabályait a Data Box Edge, ahogy és amikor szükséges.

Javasoljuk, hogy a legtöbb esetben a Data Box Edge rögzített IP-címek alapján állítsa be a tűzfal szabályait a kimenő forgalomra vonatkozóan. Az alábbi információk segítségével azonban megadhatja a biztonságos környezetek létrehozásához szükséges speciális tűzfalszabályok beállításait is.

> [!NOTE]
> - Az eszköz (forrás) IP-címeit mindig az összes felhőalapú hálózati adapterre kell beállítani.
> - A cél IP-címeket az [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653)értékre kell beállítani.

### <a name="url-patterns-for-gateway-feature"></a>Az átjáró szolgáltatás URL-mintái

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>A számítási funkció URL-mintái

| URL-minta                      | Összetevő vagy funkció                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*. cdn.mscr.io | Microsoft Container Registry (kötelező)               |
| https://\*. azurecr.io                     | Személyes és harmadik féltől származó tároló-nyilvántartások (opcionális) | 
| https://\*. azure-devices.net              | IoT Hub hozzáférés (kötelező)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>A Azure Government átjárójának URL-mintái

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>A számítási feladatokhoz tartozó URL-minták Azure Government

| URL-minta                      | Összetevő vagy funkció                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*. cdn.mscr.com | Microsoft Container Registry (kötelező)               |
| https://\*. azure-devices.us              | IoT Hub hozzáférés (kötelező)           |
| https://\*. azurecr.us                    | Személyes és harmadik féltől származó tároló-nyilvántartások (opcionális) | 

## <a name="internet-bandwidth"></a>Internetes sávszélesség

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Számítási méretezési szempontok

A megoldás fejlesztésével és tesztelésével gondoskodhat arról, hogy elegendő kapacitás legyen a Data Box Edge-eszközön, és hogy az eszköz optimális teljesítményű legyen.

Az alábbi tényezőket érdemes figyelembe venni:

- **Tárolók sajátosságai** – gondolja át az alábbiakat.

    - Hány tároló van a munkaterhelésben? Számos könnyű tárolót használhat, és néhány erőforrás-igényes is.
    - Milyen erőforrásokra vannak kiosztva ezek a tárolók, illetve milyen erőforrásokat használnak?
    - Hány réteget osztanak meg a tárolók?
    - Vannak fel nem használt tárolók? Egy leállított tároló továbbra is elegendő lemezterületet igényel.
    - Milyen nyelven íródott a tárolók?
- **A feldolgozott** adatmennyiség mérete – mennyi az adattárolók feldolgozása? Az adatmennyiség lemezterületet vagy az adatmennyiséget fogja feldolgozni a memóriában?
- **Várt teljesítmény** – Mik a megoldás kívánt teljesítmény-jellemzői? 

A megoldás teljesítményének megismeréséhez és pontosításához használhatja a következőt:

- A Azure Portalben elérhető számítási mérőszámok. Lépjen a Data Box Edge-erőforráshoz, és válassza a **figyelés > metrikák**lehetőséget. Tekintse meg az **Edge számítási memória használatának** és a **peremhálózati számítási kapacitásának százalékos arányát** , hogy megismerje a rendelkezésre álló erőforrásokat és a felhasznált erőforrásokat.
- Az eszköz PowerShell-felületén keresztül elérhető figyelési parancsok, például:

    - `dkrdbe stats`, hogy élő streamet kapjon a tároló (k) erőforrás-használati statisztikához. A parancs támogatja a PROCESSZORt, a memóriahasználat, a memória korlátját és a hálózati IO-metrikákat.
    - `dkrdbe system df` a felhasznált lemezterület mennyiségére vonatkozó információk lekéréséhez. 
    - `dkrdbe image prune` a fel nem használt rendszerképek tisztítására és lemezterület felszabadítására.
    - `dkrdbe ps --size` egy futó tároló hozzávetőleges méretének megtekintéséhez. 

    Az elérhető parancsokról további információt a következő témakörben olvashat: a [számítási modulok figyelése és hibáinak megoldása](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Végezetül ellenőrizze, hogy az adatkészleten érvényesíti-e a megoldást, és az éles környezetben való üzembe helyezés előtt számszerűsítse Data Box Edge teljesítményét.


## <a name="next-step"></a>Következő lépés

- [A Azure Data Box Edge üzembe helyezése](data-box-edge-deploy-prep.md)
