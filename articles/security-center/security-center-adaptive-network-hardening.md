---
title: Adaptív hálózati megerősítés a Azure Security Centerban | Microsoft Docs
description: " Megtudhatja, hogyan engedélyezheti az adaptív hálózatok megerősítését Azure Security Centerban. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2019
ms.author: v-mohabe
ms.openlocfilehash: 2f82f3fe6f5cb6808ba606125ee0869475a60274
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295892"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptív hálózati megerősítés Azure Security Center
Ismerje meg, hogyan konfigurálhatja az adaptív hálózatok megerősítését Azure Security Centerban.

## <a name="what-is-adaptive-network-hardening"></a>Mi az adaptív hálózat megerősítése?
[Hálózati biztonsági csoportok (NSG-EK)](https://docs.microsoft.com/azure/virtual-network/security-overview) alkalmazása az erőforrások felé irányuló és onnan érkező forgalom szűrésére, javítja a hálózat biztonsági helyzetét. Azonban továbbra is előfordulhatnak olyan esetek, amikor a NSG keresztül áramló tényleges forgalom a definiált NSG-szabályok részhalmaza. Ezekben az esetekben a NSG-szabályoknak a tényleges forgalmi minták alapján történő megerősítése révén a biztonsági helyzet további javítása is megvalósítható.

Az adaptív hálózat megerősítése ajánlásokat biztosít a NSG-szabályok további megerősítéséhez. Egy gépi tanulási algoritmust használ, amely a tényleges forgalom, az ismert megbízható konfiguráció, a veszélyforrások felderítése és más, a biztonsággal kapcsolatos mutatók szempontjából tényezőket tartalmaz, és javaslatokat tesz arra, hogy csak adott IP-/port-rekordok származó forgalmat engedélyezzen.

Tegyük fel például, hogy a meglévő NSG-szabály a 22-es porton engedélyezi a 140.20.30.10/24 forgalmat. Az adaptív hálózat megerősítő javaslata az elemzés alapján szűkíti a tartományt, és engedélyezi a forgalmat a 140.23.30.10/29 – amely egy szűkebb IP-címtartomány, és letiltja az adott portra irányuló összes más forgalmat.

![hálózati megerősítő nézet](./media/security-center-adaptive-network-hardening/traffic-hardening.png)

> [!NOTE]
> Az adaptív hálózati megerősítési javaslatok a következő portokon támogatottak: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Adaptív hálózat megerősítő riasztások és szabályok megtekintése

1. A Security Center területen válassza a **hálózatkezelés** -> **adaptív hálózat megerősítése**lehetőséget. A hálózati virtuális gépek három külön lapon vannak felsorolva:
   * **Sérült erőforrások**: Azok a virtuális gépek, amelyeknek jelenleg vannak olyan javaslatai és riasztásai, amelyeket az adaptív hálózat megerősítő algoritmusának futtatásával váltott ki. 
   * **Kifogástalan erőforrások**: Riasztások és javaslatok nélküli virtuális gépek.
   * Nem **ellenőrzött erőforrások**: Azok a virtuális gépek, amelyekhez az adaptív hálózat megerősítő algoritmusa nem futtatható a következő okok egyike miatt:
      * **A virtuális gépek a klasszikus virtuális gépek**: Csak Azure Resource Manager virtuális gépek támogatottak.
      * **Nem áll rendelkezésre elegendő mennyiségű érték**: Ahhoz, hogy pontos forgalmat megerősítő javaslatokat lehessen előállítani, Security Center legalább 30 napos forgalmi adatokat igényel.
      * **A virtuális gépet az ASC standard nem védi**: Ehhez a szolgáltatáshoz csak a Security Center szabványos díjszabási szintjére beállított virtuális gépek jogosultak.

     ![sérült erőforrások](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. A nem megfelelő **állapotú erőforrások** lapon válasszon ki egy virtuális gépet a riasztások megtekintéséhez, és alkalmazza a javasolt korlátozási szabályokat.

    ![riasztások megerősítése](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Adaptív hálózatra vonatkozó, javasolt szabályok áttekintése és alkalmazása

1. A nem kifogástalan **erőforrások** lapon válasszon ki egy virtuális gépet. A riasztások és az ajánlott megerősítő szabályok szerepelnek a felsorolásban.

     ![szabályok megerősítése](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > A **szabályok** lap felsorolja azokat a szabályokat, amelyekkel az adaptív hálózat megerősítő javasolja a hozzáadását. A **riasztások** lapon láthatók azok a riasztások, amelyek a forgalom miatt keletkeztek, az erőforrás felé áramlanak, amely nem az ajánlott szabályokban engedélyezett IP-tartományon belül van.

2. Ha módosítani szeretné egy szabály egyes paramétereit, módosíthatja azt a [szabály módosítása](#modify-rule)című részben leírtak szerint.
   > [!NOTE]
   > [Törölhet](#delete-rule) vagy [hozzáadhat](#add-rule) egy szabályt is.

3. Válassza ki a NSG alkalmazni kívánt szabályokat, majd kattintson a kikényszerítés gombra.

      > [!NOTE]
      > A kényszerített szabályok hozzáadódnak a virtuális gépet védő NSG (ok) hoz. (A virtuális gépet egy hálózati adapterhez társított NSG, vagy a virtuális gépet tartalmazó alhálózattal, vagy mindkettővel védeni lehet.)

    ![szabályok betartatása](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### Szabály <a name ="modify-rule"></a> módosítása

Érdemes lehet módosítani egy javasolt szabály paramétereit. Előfordulhat például, hogy módosítani szeretné az ajánlott IP-tartományokat.

Néhány fontos útmutató az adaptív hálózat megerősítési szabályának módosításához:

* Az "engedélyezés" szabályok paramétereinek módosítása csak. 
* Az "engedélyezés" szabályok nem módosíthatók "megtagadás" szabályokként. 

  > [!NOTE]
  > A "megtagadás" szabályok létrehozása és módosítása közvetlenül a NSG történik további részletekért lásd: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* A **minden forgalmi** szabály megtagadása az egyetlen "megtagadás" szabály, amely itt is szerepel, és nem módosítható. Azonban törölheti is (lásd: [szabály törlése](#delete-rule)).
  > [!NOTE]
  > A **minden forgalmi** szabály megtagadása ajánlott, ha az algoritmus futtatásának eredményeképpen a Security Center nem azonosítja az engedélyezett forgalmat a meglévő NSG-konfiguráció alapján. Ezért az ajánlott szabály az, hogy megtagadja a megadott portra irányuló összes forgalmat. Az ilyen típusú szabály neve "*rendszer által generált*" néven jelenik meg. A szabály érvénybe léptetése után annak tényleges neve a NSG lesz, amely a protokoll, a forgalom iránya, a "MEGTAGADÁS" és a véletlenszerű szám.

*Adaptív hálózati kötési szabály módosítása:*

1. Egy szabály egyes paramétereinek módosításához a **szabályok** lapon kattintson a három pontra (...) a szabály sorának végén, majd kattintson a **Szerkesztés**gombra.

   ![szabály szerkesztése](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. A **szabály szerkesztése** ablakban frissítse a módosítani kívánt adatokat, majd kattintson a **Mentés**gombra.

   > [!NOTE]
   > A **Mentés**gombra kattintva sikeresen módosította a szabályt. *Azonban nem alkalmazta azt a NSG.* Az alkalmazásához ki kell választania a szabályt a listában, majd a kikényszerítés gombra kell kattintania (a következő lépésben leírtak szerint).

   ![szabály szerkesztése](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. A frissített szabály alkalmazásához a listából válassza ki a frissített szabályt, majd kattintson a kikényszerítés gombra.

    ![szabály betartatása](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### Új szabály <a name ="add-rule"></a> hozzáadása

Hozzáadhat egy "Allow" szabályt, amelyet a Security Center nem ajánlott.

> [!NOTE]
> Itt csak az "engedélyezés" szabályok adhatók hozzá. Ha meg szeretné adni a "megtagadás" szabályokat, ezt közvetlenül a NSG teheti meg. További részletek: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Adaptív hálózati kötési szabály hozzáadása:*

1. Kattintson a **szabály hozzáadása** elemre (a bal felső sarokban található).

   ![szabály hozzáadása](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Az **új szabály** ablakban adja meg a részleteket, és kattintson a **Hozzáadás**gombra.

   > [!NOTE]
   > A **Hozzáadás**gombra kattintva sikeresen felvette a szabályt, és az szerepel a többi javasolt szabályban. Azonban nem alkalmazta azt a NSG. Az aktiváláshoz ki kell választania a szabályt a listában, majd a **kényszerítés** gombra kell kattintania (a következő lépésben leírtak szerint).

3. Az új szabály alkalmazásához a listából válassza ki az új szabályt, majd kattintson a kikényszerítés gombra.

    ![szabály betartatása](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### Szabály <a name ="delete-rule"></a> törlése

Ha szükséges, törölheti a javasolt szabályt. Meghatározhatja például, hogy egy javasolt szabály alkalmazása blokkolhatja a legitim forgalmat.

*Adaptív hálózati kötési szabály törlése:*

1. A **szabályok** lapon kattintson a három pontra (...) a szabály sor végén, majd kattintson a **Törlés**gombra.  

    ![szabályok megerősítése](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

