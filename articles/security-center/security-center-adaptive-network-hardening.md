---
title: Adaptív hálózati megerősítés a Azure Security Centerban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a tényleges forgalmi mintákat a hálózati biztonsági csoportok (NSG) szabályainak megerősítéséhez és a biztonsági helyzet javításához.
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
ms.openlocfilehash: bc610fa1d7a5fa1a10db3298164404b92d5d9f85
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139589"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptív hálózati megerősítés Azure Security Center
Ismerje meg, hogyan konfigurálhatja az adaptív hálózatok megerősítését Azure Security Centerban.

## <a name="what-is-adaptive-network-hardening"></a>Mi az adaptív hálózat megerősítése?
[Hálózati biztonsági csoportok (NSG-EK)](https://docs.microsoft.com/azure/virtual-network/security-overview) alkalmazása az erőforrások felé irányuló és onnan érkező forgalom szűrésére, javítja a hálózat biztonsági helyzetét. Azonban továbbra is előfordulhatnak olyan esetek, amikor a NSG keresztül áramló tényleges forgalom a definiált NSG-szabályok részhalmaza. Ezekben az esetekben a NSG-szabályoknak a tényleges forgalmi minták alapján történő megerősítése révén a biztonsági helyzet további javítása is megvalósítható.

Az adaptív hálózat megerősítése ajánlásokat biztosít a NSG-szabályok további megerősítéséhez. Egy gépi tanulási algoritmust használ, amely a tényleges forgalom, az ismert megbízható konfiguráció, a veszélyforrások felderítése és más, a biztonsággal kapcsolatos mutatók szempontjából tényezőket tartalmaz, és javaslatokat tesz arra, hogy csak adott IP-/port-rekordok származó forgalmat engedélyezzen.

Tegyük fel például, hogy a meglévő NSG-szabály a 22-es porton engedélyezi a 140.20.30.10/24 forgalmat. Az adaptív hálózat megerősítő javaslata az elemzés alapján szűkíti a tartományt, és engedélyezi a forgalmat a 140.23.30.10/29 – amely egy szűkebb IP-címtartomány, és letiltja az adott portra irányuló összes más forgalmat.

>[!TIP]
> Az adaptív hálózati korlátozási javaslatok csak bizonyos portokon támogatottak. A teljes listát lásd: [mely portok támogatottak?](#which-ports-are-supported) alább. 


![Hálózati megerősítő nézet](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Adaptív hálózat megerősítő riasztások és szabályok megtekintése

1. Security Center válassza a **hálózatkezelés** -> **adaptív hálózat megerősítése**lehetőséget. A hálózati virtuális gépek három külön lapon vannak felsorolva:
   * Nem megfelelő **erőforrások**: azok a virtuális gépek, amelyeken jelenleg az adaptív hálózat megerősítő algoritmusának futtatásával aktiválva vannak javaslatok és riasztások. 
   * **Kifogástalan erőforrások**: riasztások nélküli virtuális gépek és javaslatok.
   * Nem **vizsgált erőforrások**: a következő okok egyike miatt nem futtathatók az adaptív hálózati kötési algoritmust használó virtuális gépek:
      * **A virtuális gépek klasszikus virtuális gépek**: csak Azure Resource Manager virtuális gépek támogatottak.
      * **Nem áll rendelkezésre elegendő mennyiségű adatok**: a pontos forgalom-megerősítési javaslatok létrehozásához Security Center legalább 30 napos forgalmi adatokat igényel.
      * A **virtuális gépet az ASC standard nem védi**: csak az Security Center Standard díjszabási szintjére beállított virtuális gépek jogosultak erre a szolgáltatásra.

     ![sérült erőforrások](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. A nem megfelelő **állapotú erőforrások** lapon válasszon ki egy virtuális gépet a riasztások megtekintéséhez, és a javasolt korlátozási szabályokat alkalmazza.

    ![riasztások megerősítése](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Adaptív hálózatra vonatkozó, javasolt szabályok áttekintése és alkalmazása

1. A nem kifogástalan **erőforrások** lapon válasszon ki egy virtuális gépet. A riasztások és az ajánlott megerősítő szabályok szerepelnek a felsorolásban.

     ![szabályok megerősítése](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > A **szabályok** lap felsorolja azokat a szabályokat, amelyekkel az adaptív hálózat megerősítő javasolja a hozzáadását. A **riasztások** lapon láthatók azok a riasztások, amelyek a forgalom miatt keletkeztek, az erőforrás felé áramlanak, amely nem az ajánlott szabályokban engedélyezett IP-tartományon belül van.

2. Ha módosítani szeretné egy szabály egyes paramétereit, módosíthatja azt a [szabály módosítása](#modify-rule)című részben leírtak szerint.
   > [!NOTE]
   > [Törölhet](#delete-rule) vagy [hozzáadhat](#add-rule) egy szabályt is.

3. Válassza ki a NSG alkalmazni kívánt szabályokat, majd kattintson a **kikényszerítés**gombra.

      > [!NOTE]
      > A kényszerített szabályok hozzáadódnak a virtuális gépet védő NSG (ok) hoz. (A virtuális gépet egy hálózati adapterhez társított NSG, vagy a virtuális gépet tartalmazó alhálózattal, vagy mindkettővel védeni lehet.)

    ![szabályok betartatása](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### Szabály <a name ="modify-rule"> </a> módosítása

Érdemes lehet módosítani egy javasolt szabály paramétereit. Előfordulhat például, hogy módosítani szeretné az ajánlott IP-tartományokat.

Néhány fontos útmutató az adaptív hálózat megerősítési szabályának módosításához:

* Az "engedélyezés" szabályok paramétereinek módosítása csak. 
* Az "engedélyezés" szabályok nem módosíthatók "megtagadás" szabályokként. 

  > [!NOTE]
  > A "megtagadás" szabályok létrehozása és módosítása közvetlenül a NSG történik. További információ: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* A **minden forgalmi szabály megtagadása** az egyetlen "megtagadás" szabály, amely itt is szerepel, és nem módosítható. Azonban törölheti is (lásd: [szabály törlése](#delete-rule)).
  > [!NOTE]
  > A **minden forgalmi szabály megtagadása** ajánlott, ha az algoritmus futtatásának eredményeképpen a Security Center nem azonosítja az engedélyezett forgalmat a meglévő NSG-konfiguráció alapján. Ezért az ajánlott szabály az, hogy megtagadja a megadott portra irányuló összes forgalmat. Az ilyen típusú szabály neve "*rendszer által generált*" néven jelenik meg. A szabály érvénybe léptetése után annak tényleges neve a NSG lesz, amely a protokoll, a forgalom iránya, a "MEGTAGADÁS" és a véletlenszerű szám.

*Adaptív hálózati kötési szabály módosítása:*

1. Egy szabály egyes paramétereinek módosításához a **szabályok** lapon kattintson a három pontra (...) a szabály sorának végén, majd kattintson a **Szerkesztés**gombra.

   ![szabály szerkesztése](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. A **szabály szerkesztése** ablakban frissítse a módosítani kívánt adatokat, majd kattintson a **Mentés**gombra.

   > [!NOTE]
   > A **Mentés**gombra kattintva sikeresen módosította a szabályt. *Azonban nem alkalmazta azt a NSG.* Az alkalmazásához ki kell választania a szabályt a listában, majd a **kikényszerítés** gombra kell kattintania (a következő lépésben leírtak szerint).

   ![szabály szerkesztése](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. A frissített szabály alkalmazásához a listából válassza ki a frissített szabályt, majd kattintson a **kikényszerítés**gombra.

    ![szabály betartatása](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### Új szabály <a name ="add-rule"> </a> hozzáadása

Hozzáadhat egy "Allow" szabályt, amelyet a Security Center nem ajánlott.

> [!NOTE]
> Itt csak az "engedélyezés" szabályok adhatók hozzá. Ha meg szeretné adni a "megtagadás" szabályokat, ezt közvetlenül a NSG teheti meg. További információ: [hálózati biztonsági csoport létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Adaptív hálózati kötési szabály hozzáadása:*

1. Kattintson a **szabály hozzáadása** elemre (a bal felső sarokban található).

   ![szabály hozzáadása](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Az **új szabály** ablakban adja meg a részleteket, és kattintson a **Hozzáadás**gombra.

   > [!NOTE]
   > A **Hozzáadás**gombra kattintva sikeresen felvette a szabályt, és az szerepel a többi javasolt szabályban. Azonban nem alkalmazta azt a NSG. Az aktiváláshoz ki kell választania a szabályt a listában, majd a **kényszerítés** gombra kell kattintania (a következő lépésben leírtak szerint).

3. Az új szabály alkalmazásához a listából válassza ki az új szabályt, majd kattintson a **kikényszerítés**gombra.

    ![szabály betartatása](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### Szabály <a name ="delete-rule"> </a> törlése

Ha szükséges, törölheti az aktuális munkamenet javasolt szabályát. Meghatározhatja például, hogy egy javasolt szabály alkalmazása blokkolhatja a legitim forgalmat.

*Adaptív hálózati kötési szabály törlése az aktuális munkamenethez:*

1. A **szabályok** lapon kattintson a három pontra (...) a szabály sor végén, majd kattintson a **Törlés**gombra.  

    ![szabályok megerősítése](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)

 

## <a name="which-ports-are-supported"></a>Mely portok támogatottak?

Az adaptív hálózati korlátozási javaslatok csak bizonyos portokon támogatottak. Ez a táblázat a teljes listát tartalmazza:

|Port|Protokoll|Társított szolgáltatás|
|:---:|:----:|:----|
|13|UDP|Nappali szolgáltatás|
|17|UDP|QOTD protokoll|
|19|UDP|CHARGEN protokoll|
|22|TCP|SSH|
|23|TCP|Telnet|
|53|UDP|DNS|
|69|UDP|TFTP|
|81|TCP|Potenciálisan rosszindulatú (TOR-kilépési csomópont)|
|111|TCP/UDP|RPC|
|119|TCP|NNTP|
|123|UDP|NTP|
|135|TCP/UDP|Endpoint Mapper; RPC DCE|
|137|TCP/UDP|NetBIOS-név szolgáltatás|
|138|TCP/UDP|NetBIOS datagram szolgáltatás|
|139|TCP|NetBIOS munkamenet-szolgáltatás|
|161|TCP/UDP|SNMP|
|162|TCP/UDP|SNMP|
|389|TCP|LDAP|
|445|TCP|SMB|
|512|TCP|Rexec|
|514|TCP|Távoli rendszerhéj|
|593|TCP/UDP|HTTP RPC|
|636|TCP|LDAP|
|873|TCP|Rsync|
|1433|TCP|MS SQL|
|1434|UDP|MS SQL|
|1900|UDP|SSDP|
|1900|UDP|SSDP|
|2049|TCP/UDP|NFS|
|2301|TCP|Compaq felügyeleti szolgáltatás|
|2323|TCP|3D – nfsd|
|2381|TCP|Compaq felügyeleti szolgáltatás|
|3268|TCP|LDAP|
|3306|TCP|MySQL|
|3389|TCP|RDP|
|4333|TCP|mSQL|
|5353|UDP|mDNS|
|5432|TCP|PostgreSQL|
|5555|TCP|Személyes ügynök; HP OmniBack|
|5800|TCP|VNC|
|5900|TCP|Távoli framebuffer; VNC|
|5900|TCP|VNC|
|5985|TCP|Windows PowerShell|
|5986|TCP|Windows PowerShell|
|6379|TCP|Redis|
|6379|TCP|Redis|
|7000|TCP|Cassandra|
|7001|TCP|Cassandra|
|7199|TCP|Cassandra|
|8081|TCP|CosmosDB Sun proxy rendszergazdája|
|8089|TCP|Splunk|
|8545|TCP|Potenciálisan rosszindulatú (Cryptominer)|
|9042|TCP|Cassandra|
|9160|TCP|Cassandra|
|9300|TCP|Elasticsearch|
|11211|UDP|Memcached|
|16379|TCP|Redis|
|26379|TCP|Redis|
|27017|TCP|MongoDB|
|37215|TCP|Potenciálisan rosszindulatú|
||||