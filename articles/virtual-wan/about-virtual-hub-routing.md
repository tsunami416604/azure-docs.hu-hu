---
title: Információ a virtuális központ útválasztásáról
titleSuffix: Azure Virtual WAN
description: Ez a cikk a virtuális központ útválasztását ismerteti
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 659cb9aabe5fcca68aea9a73ea9beadb8e52ec50
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259172"
---
# <a name="about-virtual-hub-routing"></a>Információ a virtuális központ útválasztásáról

A virtuális központ útválasztási funkcióit egy olyan útválasztó biztosítja, amely az átjárók közötti összes útválasztást felügyeli Border Gateway Protocol (BGP) használatával. A virtuális központ több átjárót is tartalmazhat, például a helyek közötti VPN-átjárót, a ExpressRoute átjárót, a pont – hely átjárót Azure Firewall. Ez az útválasztó a virtuális hubhoz csatlakozó virtuális hálózatok közötti átviteli kapcsolatot is biztosít, amely akár 50 GB/s-os összesített átviteli sebességet is képes támogatni. Ezek az útválasztási képességek a szabványos virtuális WAN-ügyfelekre vonatkoznak.

Az Útválasztás konfigurálásával kapcsolatban lásd: [a virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Útválasztási fogalmak

A következő szakaszok ismertetik a virtuális központ útválasztásának legfontosabb fogalmait.

> [!NOTE]
> A központi útválasztási táblázat, a társítás, a terjesztési és a statikus útvonalak egy virtuális hálózati kapcsolaton belüli új fogalmai továbbra is kihasználhatók, és a várhatóan az augusztus 3. hét.
>

### <a name="hub-route-table"></a><a name="hub-route"></a>Hub-útválasztási táblázat

A virtuális központ útválasztási táblázata egy vagy több útvonalat is tartalmazhat. Az útvonal tartalmazza a nevét, a címkét, a célhely típusát, a célhely előtagjainak listáját és a következő ugrási információkat az átirányítani kívánt csomaghoz. A **kapcsolatok** jellemzően olyan útválasztási konfigurációval rendelkeznek, amely hozzá van rendelve vagy propagálva lesz egy útválasztási táblához.

### <a name="connection"></a><a name="connection"></a>Kapcsolat

A kapcsolatok olyan Resource Manager-erőforrások, amelyek útválasztási konfigurációval rendelkeznek. A kapcsolatok négyféle típusa a következők:

* **VPN-kapcsolat**: VPN-hely csatlakoztatása egy virtuális hubhoz VPN-átjáróhoz.
* **ExpressRoute-kapcsolat**: egy ExpressRoute áramkör csatlakoztatása egy virtuális hub ExpressRoute-átjáróhoz.
* **P2S konfigurációs kapcsolat**: a felhasználó VPN-(pont – hely) konfigurációját csatlakoztatja egy virtuális központ felhasználói VPN-(pont – hely) átjáróhoz.
* **Hub virtuális hálózati kapcsolat**: virtuális hálózatok csatlakoztatása egy virtuális hubhoz.

Beállíthatja a virtuális hálózati kapcsolatok útválasztási konfigurációját a telepítés során. Alapértelmezés szerint az összes kapcsolat hozzárendeli és propagálja az alapértelmezett útválasztási táblázatot.

### <a name="association"></a><a name="association"></a>Társulási

Minden egyes kapcsolódás egy útválasztási táblához van társítva. Az útválasztási táblázathoz való kapcsolatok társításával lehetővé válik, hogy a rendszer a forgalmat az útválasztási táblában útvonalként jelölt célhelyre küldje. A kapcsolat útválasztási konfigurációja a társított útválasztási táblázatot fogja megjeleníteni.  Ugyanahhoz az útválasztási táblához több kapcsolat is társítható. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat ugyanahhoz az (alapértelmezett) útválasztási táblázathoz van társítva.

Alapértelmezés szerint minden kapcsolat egy virtuális központ **alapértelmezett útválasztási táblájához** van társítva. Minden egyes virtuális központ saját alapértelmezett útválasztási táblázattal rendelkezik, amely szerkeszthető statikus útvonal (ok) hozzáadásához. A statikusan hozzáadott útvonalak elsőbbséget élveznek az azonos előtagokhoz tartozó dinamikusan megismert útvonalakkal szemben.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Társulási":::

### <a name="propagation"></a><a name="propagation"></a>Propagálás

A kapcsolatok dinamikusan propagálják az útvonalakat egy útválasztási táblába. A VPN-kapcsolattal, a ExpressRoute-kapcsolattal vagy a P2S-konfigurációs kapcsolattal az útvonalakat a rendszer a virtuális központból a helyi útválasztóba továbbítja a BGP használatával. Az útvonalak egy vagy több útválasztási táblára is továbbíthatók.

Minden egyes virtuális központ esetében az **egyik útválasztási tábla sem** érhető el. A none útválasztási táblázatba való propagálás azt jelenti, hogy nem szükséges útvonalakat továbbítani a kapcsolódásból. A VPN-, ExpressRoute-és felhasználói VPN-kapcsolatok az útvonalakat az azonos útválasztási táblákba terjesztik.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Propagálás":::

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Statikus útvonalak konfigurálása virtuális hálózati kapcsolatban

A statikus útvonalak konfigurálása egy olyan mechanizmust biztosít, amely egy következő ugrási IP-címen keresztül irányítja át a forgalmat, ami egy virtuális hubhoz csatolt küllős VNet kiépített hálózati virtuális berendezés (NVA) lehet. A statikus útvonal egy útvonal neve, a célhelyek előtagjainak listája, valamint egy következő ugrási IP-cím.

## <a name="reset-hub"></a><a name="route"></a>Hub alaphelyzetbe állítása
Csak a Azure Portal érhető el, ez a beállítás lehetővé teszi a felhasználó számára, hogy a hibás erőforrásokat, például útválasztási táblákat, hub-útválasztókat vagy a virtuális hub-erőforrást a megfelelő kiépítési állapotba vonja vissza. Ez egy további lehetőség arra, hogy a felhasználó vegye figyelembe a kapcsolatfelvételt a Microsoft támogatási szolgálata előtt. Ez a művelet nem állítja alaphelyzetbe a virtuális központ egyik átjáróját. 

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>Az alapszintű és a standard szintű virtuális hálózatok útválasztási táblái a társítási és propagálási funkciók előtt

Az útválasztási táblák mostantól társítási és propagálási funkciókkal rendelkeznek. A már meglévő útválasztási táblázat olyan útválasztási táblázat, amely nem rendelkezik ezekkel a funkciókkal. Ha már meglévő útvonalak vannak a hub-útválasztásban, és az új képességeket szeretné használni, vegye figyelembe a következőket:

* **Szabványos virtuális WAN-ügyfelek meglévő útvonalakkal a virtuális központban**:

Az útválasztási táblázat új funkcióinak használatához várjon, amíg az Azure-ba való bevezetéshez a 3. augusztusi hét befejeződik. Ha az Útválasztás szakaszban már meglévő útvonalak találhatók a Azure Portal található hubhoz, először törölnie kell őket, majd újra létre kell hoznia az új útválasztási táblákat (az útválasztási táblák szakaszban érhető el Azure Portal)

* **Alapszintű virtuális WAN-ügyfelek meglévő útvonalakkal a Virtual hub-ban**: az útválasztási táblázat új funkcióinak használatához várjon, amíg az Azure-ba való bevezetéshez a 3. augusztusi hét befejeződik. Ha az Útválasztás szakaszban már meglévő útvonalak találhatók a Azure Portal található hubhoz, először törölnie kell őket, majd **frissítenie** kell az alapszintű virtuális WAN-t a standard virtuális WAN-ra. Lásd: [virtuális WAN frissítése alapszintről standard verzióra](upgrade-virtual-wan.md).

## <a name="next-steps"></a>Következő lépések

Az Útválasztás konfigurálásával kapcsolatban lásd: [a virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md).

A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
