---
title: A Microsoft Azure Data Box Gateway rendszerkövetelményei |} A Microsoft Docs
description: Ismerje meg a szoftver- és az Azure Data Box átjáró hálózati követelményei
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 0d898c8d2273c431967603c36c8ff9d0dd8b4b7b
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417853"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Az Azure Data Box Gateway rendszerkövetelményei

Ez a cikk ismerteti a fontos követelmények a Microsoft Azure Data Box-Gateway-megoldáshoz, és az ügyfelek az Azure Data Box-átjáróhoz való csatlakozáskor. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt a Data Box átjáró üzembe helyezése, majd tekintse át biztonsági, szükség szerint a telepítési és az ezt követő művelet során.

A Data Box-átjáró virtuális eszköz rendszerkövetelményei a következők:

- **Gazdagépek szoftverkövetelményei** – a támogatott platformok, a helyi konfigurációs felhasználói Felületet a böngészőben, SMB-ügyfelek és a gazdagépeket, amelyek csatlakozni az eszközhöz vonatkozó esetleges további követelményeket ismerteti.
- **Az eszköz hálózatkezelési követelményei** – ismerteti a műveletet a virtuális eszköz vonatkozó hálózati követelmények.


## <a name="specifications-for-the-virtual-device"></a>A virtuális eszköz használata estén

A mögöttes gazdagép rendszere a Data Box átjáró viszont rendelnie a virtuális eszköz kiépítése az alábbi forrásanyagokat:

| Specifikációk                                          | Leírás              |
|---------------------------------------------------------|--------------------------|
| Virtuális processzorok (magok)   | Minimum 4 |
| Memory (Memória)  | Minimum 8 GB|
| Rendelkezésre állás|Egyetlen csomópont|
| Lemezek| Operációsrendszer-lemez: 250 GB <br> Adatlemez: 2 TB-os minimális, dinamikusan kiosztott és SSD-k által kell készíteni|
| Hálózati illesztők|1 vagy több virtuális hálózati adapter|


## <a name="supported-os-for-clients-connected-to-device"></a>Az eszközhöz csatlakoztatott ügyfelek támogatott operációs rendszer

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Az eszközhöz hozzáférő ügyfelek támogatott protokollok

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Eszköz támogatott virtualizációs platform

| **Operációs rendszer és platformok**  |**Verziók**   |**Megjegyzések**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |A VMware-eszközök nem támogatottak.         |


## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Támogatott tárolási típus

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Helyi webes felhasználói felület által támogatott böngészők

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Hálózatkezelési port követelményei

Az alábbi táblázat felsorolja a portot, amelyet meg kell nyitni a tűzfalon az SMB, a felhőben és a felügyeleti forgalom engedélyezése. Ebben a táblázatban *a* vagy *bejövő* irányát hivatkozik, mely a bejövő kérések ügyfélelérési az eszközre. *Ki* vagy *kimenő* hivatkozik, amelyben a Data Box átjáróeszköz kívülről, adatokat küld a központi telepítés meghaladja a irány: például kimenő internetkapcsolattal.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>URL-mintákra vonatkozó tűzfalszabályok

A hálózati rendszergazdák gyakran konfigurálhatja a speciális tűzfalszabályokat az URL-mintákra szűrése a bejövő és kimenő forgalom alapján. A Data Box átjáróeszköz és a Data Box-átjáró szolgáltatás függenek más Microsoft-alkalmazások, például az Azure Service Bus, az Azure Active Directory hozzáférés-vezérlés, tárfiókok és Microsoft Update-kiszolgálókon. Az ezekhez az alkalmazásokhoz tartozó URL-mintákra tűzfalszabályok konfigurálásához használható. Fontos megérteni, hogy az ezekhez az alkalmazásokhoz tartozó URL-mintákra módosíthatja. Ezután ezt a beállítást figyelheti és frissítheti a tűzfalszabályok a Data Box átjáró, és szükség esetén a hálózati rendszergazdához.

Azt javasoljuk, hogy beállította-e a tűzfalszabályok alapján a Data Box Gateway liberally fix IP-címeinek, a legtöbb esetben a kimenő forgalom számára. Az alábbi információk segítségével azonban, melyek szükségesek ahhoz, hogy biztonságos környezetek létrehozása speciális tűzfal-szabályokat állíthat be.

> [!NOTE]
> - Az eszköz (forrás) IP-címek mindig a felhő-kompatibilis hálózati adaptereken értékre kell állítani.
> - IP-címeket kell megadni a cél [Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internetes sávszélesség

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Következő lépés

* [Az Azure Data Box átjáró üzembe helyezése](data-box-gateway-deploy-prep.md)

