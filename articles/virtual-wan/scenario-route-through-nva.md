---
title: 'Forgatókönyv: forgalom irányítása hálózati virtuális készüléken (NVA) keresztül'
titleSuffix: Azure Virtual WAN
description: Forgalom irányítása NVA-n keresztül
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 46ffb5bfe52fe4f398594a1dfed76a6ea6c0fd81
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530794"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Forgatókönyv: forgalom irányítása NVA keresztül

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a NVA-forgatókönyvben a cél az, hogy átirányítsa a forgalmat egy NVA (hálózati virtuális készüléken) a VNet és a VNet ágat. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

> [!NOTE]
> Ha már rendelkezik olyan útvonalakkal, amelyek a [virtuális központ útválasztásának konfigurálásához](how-to-virtual-hub-routing.md) szükséges új funkciókkal rendelkeznek, használja a cikkek ezen verzióiban ismertetett lépéseket:
>* [Azure Portal cikk](virtual-wan-route-table-nva-portal.md)
>* [PowerShell-cikk](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Tervezés

Ebben az esetben az elnevezési konvenciót fogjuk használni:

* "NVA virtuális hálózatok" olyan virtuális hálózatok esetében, amelyekben a felhasználók egy NVA telepítettek, és más virtuális hálózatokat is csatlakoztatottak a küllők (VNet 2 és VNet 4 a **kapcsolati mátrixban**).
* "NVA küllők" a NVA-VNet (VNet 5, VNet 6, VNet 7 és VNet 8) csatlakoztatott virtuális hálózatok számára a következő **kapcsolati mátrixban**:.
* "Nem NVA virtuális hálózatok" a virtuális WAN-hoz csatlakoztatott virtuális hálózatok esetében, amelyekhez nincs NVA vagy más virtuális hálózatok társítva (VNet 1 és VNet 3 a **kapcsolati mátrixban**).
* "Hubok" a Microsoft által felügyelt virtuális WAN-hubokhoz, ahol a NVA virtuális hálózatok csatlakoznak. A NVA küllős virtuális hálózatok nem kell csatlakoznia a virtuális WAN-hubokhoz, csak NVA-virtuális hálózatok.

A következő kapcsolati mátrix összegzi az ebben a forgatókönyvben támogatott folyamatokat:

**Kapcsolati mátrix**

| Forrás             | Címzett:|   *NVA küllők*|*NVA virtuális hálózatok*|*Nem NVA virtuális hálózatok*|*Ágak*|
|---|---|---|---|---|---|
| **NVA küllők**   | &#8594; | 0/0 UDR  |  Társviszony-létesítés |   0/0 UDR    |  0/0 UDR  |
| **NVA virtuális hálózatok**    | &#8594; |   Statikus |      X   |        X     |      X    |
| **Nem NVA virtuális hálózatok**| &#8594; |   Statikus |      X   |        X     |      X    |
| **Ágak**     | &#8594; |   Statikus |      X   |        X     |      X    |

A kapcsolati mátrix minden cellája azt írja le, hogy egy virtuális WAN-kapcsolat (a folyamat "feladó" oldala, a tábla sorainak fejléce) megtanulja-e a cél előtagját (a folyamat "to" oldalát, a táblázat oszlopainak fejlécét) egy adott forgalmi folyamat esetében. Az "X" azt jelenti, hogy a kapcsolat a virtuális WAN által natív módon van megadva, a "statikus" pedig azt jelenti, hogy a virtuális WAN a statikus útvonalak használatával biztosít kapcsolatot. A következőket ajánljuk figyelmébe:

* A NVA Küllőit nem a virtuális WAN kezeli. Ennek eredményeképpen azokat a mechanizmusokat, amelyekkel más virtuális hálózatok vagy ágakkal kommunikálni fognak, a felhasználó tartja karban. A NVA-VNet való kapcsolódást egy VNet-társítás biztosítja, és a következő ugrás során a 0.0.0.0/0-ra mutató alapértelmezett útvonal a NVA való kapcsolódásra, az internetre, más küllőre és ágakra mutat
* A NVA virtuális hálózatok a saját NVA beszél, de nem arról, hogy a NVA más NVA-virtuális hálózatok csatlakozik. Az 1. táblázatban például a VNet 2 az 5. és a VNet 6 VNet ismeri, de nem más küllők, például a VNet 7 és a VNet 8 között. A más küllők előtagjainak NVA-virtuális hálózatok való beadásához statikus útvonal szükséges.
* Hasonlóképpen, az ágak és a nem NVA virtuális hálózatok nem ismernek semmilyen NVA, mert a NVA küllők nincsenek csatlakoztatva a VWAN hubokhoz. Ennek eredményeképpen a statikus útvonalakra is szükség lesz.

Vegye figyelembe, hogy a NVA küllőit nem a virtuális WAN felügyeli, minden más sor ugyanazt a csatlakozási mintát jeleníti meg. Ennek eredményeképpen egyetlen útválasztási tábla (az alapértelmezett érték) a következő lesz:

* Virtuális hálózatok (nem központi virtuális hálózatok és felhasználói hub virtuális hálózatok):
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **alapértelmezett**
* Ágak
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **alapértelmezett**

Ebben a forgatókönyvben azonban meg kell gondolni, hogy mely statikus útvonalakat kell konfigurálni. Minden statikus útvonal két összetevővel fog rendelkezni, amelyek közül a virtuális WAN-hub egyik része a virtuális WAN-összetevőket közli, hogy az egyes küllők milyen kapcsolaton keresztül csatlakoznak egymáshoz, és egy másik az adott kapcsolathoz, amely a NVA hozzárendelt konkrét IP-címet (vagy több NVA előtt egy terheléselosztó) mutat, az **1. ábrán** látható módon:

**1. ábra**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="1. ábra":::

Ezzel az alapértelmezett táblában a NVA a NVA VNet mögötti forgalom küldéséhez szükséges statikus útvonalak a következők:

| Description | Útválasztási táblázat | Statikus útvonal              |
| ----------- | ----------- | ------------------------- |
| 2. VNet       | Alapértelmezett     | 10.2.0.0/16 – > eastusconn |
| 4. VNet       | Alapértelmezett     | 10.4.0.0/16 – > weconn     |

A Virtual WAN már tudja, hogy melyik kapcsolattal küldi el a csomagokat, de a kapcsolatnak tudnia kell, mi a teendő a csomagok fogadásakor: ez az a hely, ahol a kapcsolati útválasztási táblák vannak használatban. Itt fogjuk használni a rövidebb előtagokat (/24 a hosszabb/16 helyett), hogy meggyőződjünk arról, hogy ezek az útvonalak előnyben részesítettek a NVA virtuális hálózatok importált útvonalakon (VNet 2 és VNet 4):

| Description | Kapcsolat | Statikus útvonal            |
| ----------- | ---------- | ----------------------- |
| 5. VNet       | eastusconn | 10.2.1.0/24 – > 10.2.0.5 |
| 6. VNet       | eastusconn | 10.2.2.0/24 – > 10.2.0.5 |
| 7. VNet       | weconn     | 10.4.1.0/24 – > 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24 – > 10.4.0.5 |

Most NVA virtuális hálózatok, nem NVA virtuális hálózatok, és az ágak tudják, hogyan érheti el az összes NVA küllőt. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektúra

A **2. ábrán**két hub van; **Hub1** és **Hub2**.

* A **Hub1** és a **Hub2** közvetlenül csatlakozik a NVA virtuális hálózatok **VNet 2** és a **VNet 4**rendszerhez.

* A **VNet 5** és a **VNet 6** a **VNet 2**.

* A **VNet 7** és a **VNet 8** a **VNet 4**-vel van összetársítva.

* Az **5, 6, 7, 8 virtuális hálózatok** közvetett küllők, amelyek nem kapcsolódnak közvetlenül a virtuális hubhoz.

**2. ábra**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="2. ábra" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Forgatókönyv-munkafolyamat

Az Útválasztás NVA-n keresztüli beállításához a következő lépéseket kell figyelembe venni:

1. Azonosítsa a NVA küllős VNet-kapcsolatokat. A **2. ábrán**a **VNet 2 (eastusconn)** és a **VNet 4 (weconn) kapcsolatok**szerepelnek.

   Győződjön meg arról, hogy a UDR beállítása megtörtént:
   * VNet 5 és VNet 6 – VNet 2 NVA IP
   * VNet 7 és VNet 8 és VNet 4 NVA IP között 
   
   Az 5, 6, 7, 8 virtuális hálózatok nem kell közvetlenül a virtuális hubhoz csatlakoznia. Győződjön meg arról, hogy a virtuális hálózatok 5, 6, 7, 8 NSG engedélyezi a forgalmat az ág (VPN/ER/P2S) vagy a távoli virtuális hálózatok csatlakoztatott virtuális hálózatok számára. Például az virtuális hálózatok 5, 6 esetében biztosítania kell, hogy a NSG engedélyezze a helyszíni címek előtagjainak és a 2., 8. virtuális hálózatok, amelyek a távoli hubhoz csatlakoznak.

A virtuális WAN nem támogatja azt a forgatókönyvet, amelyben a virtuális hálózatok 5, 6 csatlakozik a virtuális hubhoz, és a VNet 2 NVA IP-címen keresztül kommunikál. Ezért a virtuális hálózatok 5, 6 és VNet2 közötti kapcsolatra van szükség, és hasonlóképpen VNet 7, 8 és VNet 4 között kell lennie.

2. Adjon hozzá egy összesített statikus útvonal-bejegyzést az virtuális hálózatok 2, 5, 6 és az 1. hub alapértelmezett útválasztási táblázatához.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Példa":::

3. Konfiguráljon egy statikus útvonalat a virtuális hálózatok 5, 6 a VNet 2 virtuális hálózati kapcsolatban. A virtuális hálózati kapcsolatok útválasztási konfigurációjának beállításához lásd: [virtuális központ útválasztása](how-to-virtual-hub-routing.md#routing-configuration).

4. Adjon hozzá egy összesített statikus útválasztási bejegyzést a 4., 7. és 8. virtuális hálózatok az 1. hub alapértelmezett útválasztási táblájához.

5. Ismételje meg a 2., 3. és 4. lépést a hub alapértelmezett útválasztási táblázatához.

Ez az útválasztási konfiguráció változását eredményezi, ahogy az a **3. ábrán**is látható.

**3. ábra**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="3. ábra" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
