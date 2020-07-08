---
title: 'Forgatókönyv: Azure Firewall-Interhub Útválasztás'
titleSuffix: Azure Virtual WAN
description: Forgatókönyvek útválasztáshoz – útválasztás – több, Azure Firewall
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568683"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>Forgatókönyv: Azure Firewall-Interhub (előzetes verzió)

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben az esetben a cél az, hogy átirányítsa a több, Azure Firewallt tartalmazó hubok között. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Forgatókönyv-architektúra

Ebben az esetben feltételezzük a következő konfigurációt:

* Több hub van Azure Firewall az egyes központokban.
* Biztonságos virtuális hubot használ.
* A magánhálózati forgalom (VNet), az Internet és az ág forgalmára vonatkozó szabályzatok beállítása megtörtént.
* VNet (V2I), VNet (V2B), ág-VNet (B2V), mind az összes központ Azure Firewall.

További szempontok:

* Az Azure Firewall-vel való intézményközi forgatókönyv esetén feltételezhető, hogy a küllős virtuális hálózatok nem társítanak külön útválasztási táblákhoz. (például **a "A**" és a "B" **útválasztási táblázathoz**tartozó 2. VNET társított **1** . **VNET** ). Minden virtuális hálózatok ugyanahhoz az útválasztási táblához tartozik, amelyben a Azure Firewall útvonalai találhatók.
* A Azure Firewallon keresztüli biztonságossá tétele jelenleg csak a **fiókirodában <-> VNet** és az **internetes** forgalomra korlátozódik. A Azure Firewall jelenleg nem támogatott ág-ág folyamat.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="architektúra":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Forgatókönyv-munkafolyamat

Ennek a forgatókönyvnek a konfigurálásához a következő lépéseket kell figyelembe vennie:

### <a name="step-1"></a><a name="step-1"></a>1\. lépés

Feltételezve, hogy a Azure Firewall Manageren keresztül már biztosította a kapcsolatokat, az első lépés annak biztosítása, hogy az összes ág- **None**és VNet-kapcsolat ne legyen propagálva. Erre azért van szükség, hogy a forgalom a Azure Firewallon keresztül legyen beállítva.

1. Válassza ki a hubot, és szerkessze a **none** Route táblát.
1. Frissítse a **propagálást** az összes ág és az összes virtuális hálózatok kiválasztásához.

### <a name="step-2"></a><a name="step-2"></a>2. lépés

Egyéni útválasztási táblázat beállítása egy hubhoz.

1. Az **1. hub**esetében hozzon létre útválasztási táblázatot **RT_Hub1**.

    * Ha a Azure Firewall Managert használta, a automatikusan létrehoz egy útvonalat a 0.0.0.0/0 számára a következő ugrási **AZFW1** a központ alapértelmezett útválasztási táblázatában. Ezt a beállítást a 3. lépésben fogjuk módosítani. **RT_Hub1**esetében hozzon létre egy bejegyzést a 0.0.0.0/0 számára explicit módon a következő ugrás **AZFW1**. Ez a beállítás aktiválja a V2V, a B2V és a V2I-t a AZFW1-n keresztül.
    * Mivel azt szeretné, hogy a **hub 2** ág és a virtuális hálózatok elérhető legyen az **1** . **AZFW2** keresztül (a AZFW1-n keresztüli helyett), hozzá kell adnia egy további 2 aggregált útvonalat a **2. HUBHOZ** (10.5.0.0/16->AZFW2) és virtuális hálózatok (10.2.0.0/16->AZFW2).

1. A **2. hub**esetében hozzon létre útválasztási táblázatot **RT_Hub2**.

    * Ha a Azure Firewall Managert használta, a automatikusan létrehoz egy útvonalat a 0.0.0.0/0 számára a következő ugrási **AZFW2** a központ alapértelmezett útválasztási táblázatában. Ezt a beállítást a 3. lépésben fogjuk módosítani. **RT_Hub2**esetében hozzon létre egy bejegyzést a 0.0.0.0/0 számára explicit módon a következő ugrás **AZFW2**. Ez a beállítás aktiválja a V2V, a B2V és a V2I-t a **AZFW2**-n keresztül.
    * Mivel azt szeretné, hogy a csomópontok közötti forgalom a 2. hub **AZFW2**legyen védve, nem kell explicit módon hozzáadnia egy lépést az előző hub 1 lépés második felsorolásjeléhez.

### <a name="step-3"></a><a name="step-3"></a>3. lépés

Módosítsa az egyes hubokon az **alapértelmezett útválasztási táblázatot** , hogy egy statikus útvonalat adjon hozzá az **ág VNet** (B2V) és az **ág – Internet** (B2I) folyamatokhoz a Azure Firewall használatával. Az ág és az ág közötti adatközpont jelenleg nem támogatott Azure Firewallon keresztül.

1. Az **1. hubhoz tartozó alapértelmezett útválasztási táblázathoz**:

    * **Ág – hub 2 ág a AZFW2 keresztül**: 10.5.0.0/16 – >AZFW2. Ez a konfiguráció beállítja a hub 2 tűzfalának útvonalát.
    * **Ág – hub 2 virtuális hálózatok AZFW2 keresztül**: 10.2.0.0/16 – >AZFW2. Ez a konfiguráció beállítja a hub 2 tűzfalának útvonalát.
    * Ág **(helyi)/ág VNet (helyi)/ág az interneten**: 0.0.0.0/0 – >AZFW1.

2. A **2. hubhoz tartozó alapértelmezett útválasztási táblázat**esetében:

    * Ág (helyi és távoli)/ág VNet (helyi és távoli)/ág az internethez: 0.0.0.0/0 – >AZFW2.

Ez azt eredményezi, hogy az útválasztási konfiguráció megváltozik az alábbi ábrán látható módon.

**2. ábra**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="munkafolyamat":::

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
* A virtuális központ útválasztásának konfigurálásával kapcsolatos további információkért lásd: [virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md).
