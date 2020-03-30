---
title: Microsoft Azure Data Box Gateway rendszerkövetelményei| Microsoft dokumentumok
description: Ismerje meg az Azure Data Box Gateway szoftver- és hálózati követelményeit
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 05/08/2019
ms.author: alkohli
ms.openlocfilehash: e8932097bcdef782b1a551d386c2872e02d8abfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260188"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Az Azure Data Box Gateway rendszerkövetelményei

Ez a cikk ismerteti a fontos rendszerkövetelmények a Microsoft Azure Data Box Gateway megoldás és az Azure Data Box Gateway csatlakozó ügyfelek számára. Azt javasoljuk, hogy alaposan tekintse át az információkat, mielőtt üzembe helyezné a Data Box Gateway-t, majd szükség szerint hivatkozzon rá a központi telepítés és az azt követő művelet során.

A Data Box Gateway virtuális eszköz rendszerkövetelményei a következők:

- **A gazdagépek szoftverkövetelményei** – ismertetik a támogatott platformokat, a helyi konfigurációs felhasználói felület böngészőit, az SMB-ügyfeleket, valamint az eszközhöz csatlakozó állomásokra vonatkozó további követelményeket.
- **Hálózati követelmények az eszköz** - tájékoztatást nyújt a hálózati követelmények a virtuális eszköz működéséhez.


## <a name="specifications-for-the-virtual-device"></a>A virtuális eszköz műszaki adatai

A Data Box Gateway alapjául szolgáló gazdarendszer a következő erőforrásokat tudja a virtuális eszköz kiépítésére fordítani:

| Specifikációk                                          | Leírás              |
|---------------------------------------------------------|--------------------------|
| Virtuális processzorok (magok)   | Minimum 4 |
| Memory (Memória)  | Minimum 8 GB|
| Rendelkezésre állás|Egyetlen csomópont|
| Lemezek| Operációsrendszer-lemez: 250 GB <br> Adatlemez: legalább 2 TB, dinamikusan kiosztott és SSD-másolatokkal kiegészített|
| Hálózati illesztők|1 vagy több virtuális hálózati adapter|


## <a name="supported-os-for-clients-connected-to-device"></a>Támogatott operációs rendszer az eszközhöz csatlakoztatott ügyfelek számára

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Az eszközt elérő ügyfelek támogatott protokolljai

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Támogatott virtualizációs platformok eszközhöz

| **Operációs rendszer/platform**  |**Verziók**   |**Megjegyzések**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |A VMware-eszközök nem támogatottak.         |


## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Támogatott tárolótípusok

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Támogatott böngészők a helyi webes felhasználói felülethez

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Hálózati portkövetelmények

Az alábbi táblázat azokat a portokat sorolja fel, amelyeket meg kell nyitni a tűzfalon az SMB, a felhő vagy a felügyeleti forgalom engedélyezéséhez. Ebben a *táblában a bejövő* vagy *bejövő* arra az irányra utal, amelyből a bejövő ügyfél hozzáférést kér az eszközhöz. *A* *kimenő* vagy kimenő eszköz arra az irányra utal, amelyben a Data Box Gateway eszköz a központi telepítésen kívül, például az internetre való kimenő adatokat küldi.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>A tűzfalszabályok URL-mintái

A hálózati rendszergazdák gyakran konfigurálhatnak speciális tűzfalszabályokat az URL-minták alapján a bejövő és a kimenő forgalom szűréséhez. A Data Box Gateway eszköz és a Data Box Gateway szolgáltatás más Microsoft-alkalmazásoktól, például az Azure Service Bustól, az Azure Active Directory hozzáférés-vezérléstől, a tárfiókoktól és a Microsoft Update-kiszolgálóktól függ. Az ezekhez az alkalmazásokhoz társított URL-minták a tűzfalszabályok konfigurálására használhatók. Fontos megérteni, hogy az ezekhez az alkalmazásokhoz társított URL-minták változhatnak. Ehhez pedig a hálózati rendszergazdának szükség szerint figyelnie és frissítenie kell a Data Box Gateway tűzfalszabályait.

Azt javasoljuk, hogy a tűzfal szabályok at kimenő forgalom alapján Data Box Gateway rögzített IP-címek, a legtöbb esetben. Az alábbi információk segítségével azonban speciális tűzfalszabályokat állíthat be, amelyek a biztonságos környezetek létrehozásához szükségesek.

> [!NOTE]
> - Az eszköz (forrás) IP-k mindig be kell állítani, hogy az összes felhő-kompatibilis hálózati adapterek.
> - A cél IP-címeket [az Azure adatközpont IP-tartományaira](https://www.microsoft.com/download/confirmation.aspx?id=41653)kell állítani.

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>URL-minták az Azure Government számára

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internetes sávszélesség

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Következő lépés

* [Az Azure Data Box-átjáró üzembe helyezése](data-box-gateway-deploy-prep.md)

