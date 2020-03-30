---
title: Adaptív hálózati edzés az Azure Security Centerben | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a tényleges forgalmi mintákat a hálózati biztonsági csoportok (NSG) szabályainak megkeményítésére és a biztonsági állapot további javítására.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385078"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptív hálózati edzés az Azure Security Centerben
Ismerje meg, hogyan konfigurálhatja az Adaptive Network Hardening (Adaptív hálózatedzés) szolgáltatást az Azure Security Centerben.

## <a name="what-is-adaptive-network-hardening"></a>Mi az adaptive network edzés?
A [hálózati biztonsági csoportok (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) alkalmazása az erőforrásokba és erőforrásokból érkező forgalom szűréséhez javítja a hálózat biztonsági állapotát. Azonban még mindig lehetnek olyan esetek, amikor az NSG-n keresztül áramló tényleges forgalom a meghatározott NSG-szabályok egy részhalmaza. Ezekben az esetekben a biztonsági helyzet további javítása az NSG-szabályok nak a tényleges forgalmi minták alapján történő megerősítésével érhető el.

Az Adaptive Network Hardening javaslatokat nyújt az NSG-szabályok további megerősítésére. Egy gépi tanulási algoritmust használ, amely a tényleges forgalom, az ismert megbízható konfiguráció, a fenyegetésfelderítés és más kompromisszumos mutatók tényezőit, majd javaslatokat tesz, hogy csak adott IP/port tuples forgalom engedélyezése.

Tegyük fel például, hogy a meglévő NSG-szabály a 22-es porton a 140.20.30.10/24-es forgalom engedélyezése. Az Adaptive Network Hardening ajánlása az elemzés alapján az lenne, hogy szűkítse a tartományt, és lehetővé tegye a forgalmat 140.23.30.10/29 -től – ami egy szűkebb IP-tartomány, és megtagadja az összes többi forgalmat a portra.

>[!TIP]
> Az adaptive network edzésre vonatkozó javaslatok csak a következő portokon támogatottak (udp és TCP esetén egyaránt): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301 , 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Hálózatedzésnézet](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Adaptív hálózati edzésriasztások és szabályok megtekintése

1. A Security Center ben válassza **a Hálózati** -> **adaptív hálózat edzése**lehetőséget. A hálózati virtuális gépek három külön lapon vannak felsorolva:
   * **Nem megfelelő állapotú erőforrások:** Olyan virtuális gépek, amelyek jelenleg az Adaptive Network Hardening algoritmus futtatásával aktivált javaslatokkal és riasztásokkal rendelkeznek. 
   * **Kifogástalan erőforrások:** virtuális gépek riasztások és javaslatok nélkül.
   * **Be nem vizsgált erőforrások:** Az Adaptív hálózati edzésalgoritmus által nem futtatható virtuális gépek a következő okok valamelyike miatt:
      * **A virtuális gépek klasszikus virtuális gépek:** Csak az Azure Resource Manager virtuális gépek támogatottak.
      * **Nem áll rendelkezésre elegendő adat:** A forgalom megerősítésére vonatkozó javaslatok létrehozásához a Security Center legalább 30 napos forgalmi adatokat igényel.
      * **A virtuális gép nem védett asc szabvány:** Csak a Security Center standard tarifacsomagra beállított virtuális gépek jogosultak erre a funkcióra.

     ![nem megfelelő állapotú erőforrások](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. A **nem megfelelő állapotú erőforrások** lapon válasszon egy virtuális gép a riasztások megtekintéséhez, és az ajánlott edzési szabályok at alkalmazni.

    ![megerősítési riasztások](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Az Adaptív hálózatedzés ajánlott szabályainak áttekintése és alkalmazása

1. A **nem megfelelő állapotú erőforrások** lapon válasszon egy virtuális gép. A riasztások és az ajánlott edzési szabályok fel vannak sorolva.

     ![megerősítési szabályok](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > A **Szabályok** lapon azok a szabályok jelennek meg, amelyeket az Adaptive Network Hardening javasol hozzáadni. A **Riasztások** lap felsorolja azokat a riasztásokat, amelyek a forgalom miatt keletkeztek, és az erőforrásba áramlottak, ami nem tartozik az ajánlott szabályokban engedélyezett IP-tartományon belül.

2. Ha módosítani szeretné egy szabály egyes paramétereit, módosíthatja azt a Szabály módosítása című részben [leírtak](#modify-rule)szerint.
   > [!NOTE]
   > Szabály [is törölhető](#delete-rule) vagy [hozzáadhat.](#add-rule)

3. Válassza ki az NSG-re alkalmazni kívánt szabályokat, és kattintson a **Kényszerítés gombra.**

      > [!NOTE]
      > A kényszerített szabályok hozzáadódnak az NSG(k) a virtuális gép védelme. (A virtuális gép lehet védeni egy NSG, amely a hálózati adapter, vagy az alhálózat, amelyben a virtuális gép található, vagy mindkettő)

    ![szabályok érvényesítése](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Szabály <a name ="modify-rule"> </a> módosítása

Előfordulhat, hogy módosítani szeretné egy ajánlott szabály paramétereit. Előfordulhat például, hogy módosítani szeretné az ajánlott IP-tartományokat.

Néhány fontos irányelv az adaptív hálózati edzési szabályok módosításához:

* Csak az "engedélyezés" szabály paramétereit módosíthatja. 
* Az "engedélyezés" szabály nem változtatható meg úgy, hogy "megtagadási" szabályokká váljon. 

  > [!NOTE]
  > A "megtagadási" szabályok létrehozása és módosítása közvetlenül az NSG-n történik. További információt a [Hálózati biztonsági csoport létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)című témakörben talál.

* A **Minden forgalom megtagadása** szabály az egyetlen olyan típusú "megtagadás" szabály, amely itt szerepel, és nem módosítható. Törölheti azonban (lásd: [Szabály törlése](#delete-rule)).
  > [!NOTE]
  > A **Minden forgalom megtagadása** szabály akkor ajánlott, ha az algoritmus futtatása miatt a Security Center nem azonosítja az engedélyezett forgalmat a meglévő NSG-konfiguráció alapján. Ezért az ajánlott szabály a megadott portra irányuló összes forgalom megtagadása. Az ilyen típusú szabály neve "*Rendszer által létrehozva*" néven jelenik meg. A szabály érvényesítése után az NSG-ben a tényleges neve a protokollból, a forgalom irányából, a "DENY"-ből és egy véletlen számból álló karakterlánc lesz.

*Adaptív hálózati edzésszabály módosítása:*

1. Egy szabály néhány paraméterének módosításához a **Szabályok** lapon kattintson a szabály sorának végén lévő három pontra (...), majd kattintson a **Szerkesztés**gombra.

   ![szabály szerkesztése](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. A **Szabály szerkesztése** ablakban frissítse a módosítani kívánt részleteket, majd kattintson a **Mentés gombra.**

   > [!NOTE]
   > A **Mentés**gombra kattintás után sikeresen módosította a szabályt. *Azonban nem alkalmazta az NSG-nél.* Az alkalmazáshoz ki kell jelölnie a szabályt a listában, és kattintson a **Kényszerítés gombra** (a következő lépésben leírtak szerint).

   ![szabály szerkesztése](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. A frissített szabály alkalmazásához a listából jelölje ki a frissített szabályt, és kattintson a **Kényszerítés gombra.**

    ![szabály érvényesítése](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Új szabály <a name ="add-rule"> </a> hozzáadása

Hozzáadhat egy "engedélyezés" szabályt, amelyet a Security Center nem ajánlott.

> [!NOTE]
> Itt csak "allow" szabályok adhatók hozzá. Ha hozzá szeretné adni a "megtagadás" szabályokat, ezt közvetlenül az NSG-n teheti meg. További információt a [Hálózati biztonsági csoport létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)című témakörben talál.

*Adaptív hálózati edzésszabály hozzáadása:*

1. Kattintson **a Szabály hozzáadása** gombra (a bal felső sarokban található).

   ![hozzáadás, szabály](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Az **Új szabály** ablakban adja meg a részleteket, és kattintson a **Hozzáadás**gombra.

   > [!NOTE]
   > A **Hozzáadás**gombra kattintás után sikeresen felvette a szabályt, és az a többi ajánlott szabállyal együtt szerepel. Azonban nem alkalmazta az NSG-n. Az aktiváláshoz ki kell jelölnie a szabályt a listában, és kattintson a **Kényszerítés gombra** (a következő lépésben leírtak szerint).

3. Az új szabály alkalmazásához a listából jelölje ki az új szabályt, és kattintson a **Kényszerítés gombra.**

    ![szabály érvényesítése](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Szabály <a name ="delete-rule"> </a> törlése

Szükség esetén törölheti az aktuális munkamenethez ajánlott szabályt. Például meghatározhatja, hogy egy javasolt szabály alkalmazása blokkolhatja a jogszerű forgalmat.

*Adaptív hálózati edzésszabály törlése az aktuális munkamenethez:*

1. A **Szabályok** lapon kattintson a szabály sorának végén lévő három pontra (...), majd kattintson a **Törlés gombra.**  

    ![megerősítési szabályok](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)