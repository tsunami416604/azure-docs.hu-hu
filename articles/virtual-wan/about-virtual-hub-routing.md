---
title: Információ a virtuális központ útválasztásáról
titleSuffix: Azure Virtual WAN
description: Ez a cikk a virtuális központ útválasztását ismerteti
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 51480a49aab2c1277eeb846c593fcb2bc858d1f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983720"
---
# <a name="about-virtual-hub-routing"></a>Információ a virtuális központ útválasztásáról

A virtuális központ útválasztási funkcióit egy olyan útválasztó biztosítja, amely az átjárók közötti összes útválasztást felügyeli Border Gateway Protocol (BGP) használatával. A virtuális központ több átjárót is tartalmazhat, például a helyek közötti VPN-átjárót, a ExpressRoute átjárót, a pont – hely átjárót Azure Firewall. Ez az útválasztó a virtuális hubhoz csatlakozó virtuális hálózatok közötti átviteli kapcsolatot is biztosít, amely akár 50 GB/s-os összesített átviteli sebességet is képes támogatni. Ezek az útválasztási képességek a szabványos virtuális WAN-ügyfelekre vonatkoznak. 

Az Útválasztás konfigurálásával kapcsolatban lásd: [a virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Útválasztási fogalmak

A következő szakaszok ismertetik a virtuális központ útválasztásának legfontosabb fogalmait.

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

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Társulási":::

### <a name="labels"></a><a name="static"></a>Címkék
A címkék olyan mechanizmust biztosítanak, amely logikailag csoportosítja az útválasztási táblákat. Ez különösen hasznos lehet az útvonalaknak több útválasztási táblába való csatlakozása során. Az alapértelmezett útválasztási táblázat például egy "default" nevű beépített címkével rendelkezik. Amikor a felhasználók propagálják a kapcsolati útvonalakat az "alapértelmezett" címkére, a rendszer automatikusan az összes alapértelmezett útválasztási táblára alkalmazza a virtuális WAN összes csomópontján. 

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Statikus útvonalak konfigurálása virtuális hálózati kapcsolatban

A statikus útvonalak konfigurálása egy olyan mechanizmust biztosít, amely egy következő ugrási IP-címen keresztül irányítja át a forgalmat, ami egy virtuális hubhoz csatolt küllős VNet kiépített hálózati virtuális berendezés (NVA) lehet. A statikus útvonal egy útvonal neve, a célhelyek előtagjainak listája, valamint egy következő ugrási IP-cím.

## <a name="reset-hub"></a><a name="route"></a>Hub alaphelyzetbe állítása
Csak a Azure Portal érhető el, ez a beállítás lehetővé teszi a felhasználó számára, hogy a hibás erőforrásokat, például útválasztási táblákat, hub-útválasztókat vagy a virtuális hub-erőforrást a megfelelő kiépítési állapotba vonja vissza. Ez egy további lehetőség arra, hogy a felhasználó vegye figyelembe a kapcsolatfelvételt a Microsoft támogatási szolgálata előtt. Ez a művelet nem állítja alaphelyzetbe a virtuális központ egyik átjáróját. 

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>Az alapszintű és a standard szintű virtuális hálózatok útválasztási táblái a társítási és propagálási funkciók előtt

Az útválasztási táblák mostantól társítási és propagálási funkciókkal rendelkeznek. A már meglévő útválasztási táblázat olyan útválasztási táblázat, amely nem rendelkezik ezekkel a funkciókkal. Ha már meglévő útvonalak vannak a hub-útválasztásban, és az új képességeket szeretné használni, vegye figyelembe a következőket:

* **Szabványos virtuális WAN-ügyfelek meglévő útvonalakkal a virtuális központban**:

Ha az Útválasztás szakaszban már meglévő útvonalak találhatók a Azure Portal található hubhoz, először törölnie kell őket, majd újra létre kell hoznia az új útválasztási táblákat (az útválasztási táblák szakaszban érhető el Azure Portal)

* **Alapszintű virtuális WAN-ügyfelek meglévő útvonalakkal a virtuális központban**: ha a Azure Portalben lévő központ útválasztási szakasza már meglévő útvonalakat tartalmaz, először törölnie kell őket, majd **frissítenie** kell az alapszintű virtuális WAN-t a standard Virtual WAN-ra. Lásd: [virtuális WAN frissítése alapszintről standard verzióra](upgrade-virtual-wan.md).

## <a name="virtual-wan-routing-considerations"></a><a name="considerations"></a>A virtuális WAN útválasztási szempontjai

Virtuális WAN-útválasztás konfigurálásakor vegye figyelembe a következőket:

* Az összes ág-kapcsolatot (pont – hely, hely – hely és ExpressRoute) társítani kell az alapértelmezett útválasztási táblázathoz. Így az összes ág ugyanazokat az előtagokat fogja megtanulni.
* Az összes ág-kapcsolatnak az útvonalakat ugyanabba az útválasztási táblákba kell terjesztenie. Ha például úgy dönt, hogy az ágaknak az alapértelmezett útválasztási táblázatba kell terjeszteniük, akkor ennek a konfigurációnak konzisztensnek kell lennie az összes ág között. Ennek eredményeképpen az alapértelmezett útválasztási táblázathoz társított összes kapcsolat elérheti az összes ágát.
* Azure Firewall jelenleg nem támogatott ág – ág.
* Ha több régióban Azure Firewall használ, az összes küllős virtuális hálózatot ugyanahhoz az útválasztási táblához kell társítani. Ha például a virtuális hálózatok egy részhalmaza lesz a Azure Firewallon, míg más virtuális hálózatok megkerüli a Azure Firewall ugyanabban a virtuális központban nem lehetséges.
* VNet-kapcsolaton keresztül egyetlen következő ugrási IP-cím is konfigurálható.
* A virtuális központ nem támogatja a statikus útvonalat a 0.0.0.0/0 és a következő ugrás Virtual Network kapcsolathoz (vagy egy berendezés IP-címéhez a VNet-kapcsolaton)
* A 0.0.0.0/0 útvonalra vonatkozó összes információ a helyi központ útválasztási táblájára korlátozódik. Ez az útvonal nem terjed ki a hubok között.

## <a name="next-steps"></a>Következő lépések

Az Útválasztás konfigurálásával kapcsolatban lásd: [a virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md).

A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
