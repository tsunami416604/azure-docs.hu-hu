---
title: Az Azure Security Center adaptív hálózati korlátozások |} A Microsoft Docs
description: " Ismerje meg, hogyan engedélyezheti az adaptív hálózati korlátozások az Azure Security Centerben. "
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
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: cede54f69fbeec5e01c17d84436bdc4c9ee91002
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2019
ms.locfileid: "58117055"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Az Azure Security Center adaptív hálózati megerősítése
Ismerje meg, az adaptív hálózati korlátozások konfigurálása az Azure Security Centerben.

## <a name="what-is-adaptive-network-hardening"></a>Mi az adaptív hálózati korlátozások?
Alkalmazása [hálózati biztonsági csoportok (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) az erőforrások, forgalom szűrésére növeli a hálózati biztonságot. Azonban továbbra is lehet bizonyos esetekben, ahol az NSG-t a tényleges forgalmát része a meghatározott NSG-szabályok. Ezekben az esetekben biztonsági irányelvei, tiltják fejlessze biztosíthatja az NSG-szabályokat, a tényleges forgalmi minták alapján korlátozására.

Az adaptív hálózati korlátozások nyújt ajánlásokat tovább erősíti az NSG-szabályokat. A gépi tanulási algoritmus, amely a tényleges forgalom ismert megbízható konfiguráció, a fenyegetésekkel kapcsolatos Tudásbázis és a biztonsági sérülés megállapítását utaló egyéb jeleket számításba használja, és csak az adott IP-port rekordokat tartalmazó érkező adatforgalom engedélyezéséhez majd ajánlásokkal.

Például tegyük fel a meglévő NSG-szabályt a 22-es porton 140.20.30.10/24 érkező adatforgalom engedélyezéséhez. Az adaptív hálózati korlátozások a javaslatot, az elemzés alapján lehet szűkíteni a tartomány és 140.23.30.10/29 – amely egy szűkebb IP-címtartomány, érkező adatforgalom engedélyezéséhez és az összes többi forgalom erre a portra.

![hálózati korlátozások megtekintése](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


> [!NOTE]
> Az adaptív korlátozások a hálózati javaslatok támogatottak a következő portokat: 22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Az adaptív hálózati korlátozások a riasztások megtekintése és szabályok

1. Válassza ki a Security Centerben **hálózatkezelés** -> **adaptív hálózati korlátozások**. A hálózati virtuális gépek három külön lapon vannak felsorolva:
   * **Nem megfelelő állapotú erőforrások**: Virtuális gépek, amelyeken jelenleg a javaslatok és riasztások, amelyek futtatásával az adaptív hálózati korlátozások algoritmus által gyűjtött. 
   * **Kifogástalan állapotú erőforrások**: Riasztások és javaslatok nem rendelkező virtuális gépeket.
   * **Nem vizsgált erőforrások**: Virtuális gépek, amelyek az adaptív hálózati korlátozások algoritmus nem futtatható, az alábbi okok egyike miatt:
      * **A virtuális gépek a klasszikus virtuális gépeket**: – csak a az Azure Resource Manager virtuális gépek támogatottak.
      * **Nincs elég adat nem érhető el**: Annak érdekében, hogy a létrehozás, a pontos forgalom korlátozására vonatkozó javaslatok a Security Center működéséhez legalább 30 nappal a forgalmi adatokat.
      * **A virtuális gép nem védett ASC standard**: Ez a funkció kizárólag a virtuális gépek, amelyek a Security Center Standard tarifacsomagra jogosultak.

     ![nem megfelelő állapotú erőforrások](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Az a **nem megfelelő állapotú erőforrások** lapra, válassza ki a virtuális gép megtekintheti a hozzá tartozó riasztásokat és a javasolt korlátozási szabályok a alkalmazni.

    ![korlátozási értesítések](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Tekintse át és adaptív hálózati korlátozások a javasolt szabályok alkalmazása

1. Az a **nem megfelelő állapotú erőforrások** lapra, válassza ki a virtuális gép. A riasztások és ajánlott korlátozási szabályok listáját.
   ![korlátozási értesítések](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > A **szabályok** lap felsorolja a szabályokat, amelyek adaptív hálózati korlátozások javasolja ad hozzá. A **riasztások** lapon miatt az adatforgalom, az erőforrás, amely nem az ajánlott szabályok engedélyezve az IP-címtartomány belül létrehozott riasztások láthatók.

   ![korlátozási szabályok](./media/security-center-adaptive-network-hardening/hardening-rules.png)

2. Ha egy szabály paraméterek némelyike módosítani szeretné, módosíthatja, leírtak [szabály módosítása](#modify-rule).
   > [!NOTE]
   > Emellett [törlése](#delete-rule) vagy [hozzáadása](#add-rule) szabály.

3. Válassza ki a szabályokat az NSG-t a alkalmazni, és kattintson a kívánt **érvényesítése**. 

### Szabály módosítása  <a name ="modify-rule"> </a>

Érdemes lehet, hogy módosítsa a paramétereket egy szabály, amely rendelkezik ajánlott. Például előfordulhat, hogy módosítani szeretné a javasolt IP-címtartományokat.

Néhány fontos irányelv adaptív hálózati korlátozások a szabály módosítása:

* A paraméterek csak a "engedélyezése" szabályok módosíthatja. 
* "Engedélyezése" szabályok válhat az "Elutasítás" szabály nem módosítható. 

  > [!NOTE]
  > Létrehozása és módosítása az "Elutasítás" szabályok történik, közvetlenül az NSG-t a további részletekért, lásd: [létrehozása, módosítása vagy törlése a hálózati biztonsági csoport](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* A **összes forgalom** szabályt a rendszer kizárólag az "Elutasítás" szabályt, amely akkor is szerepel, és azt nem lehet módosítani. Lehet, azonban törölheti azt (lásd: [szabály törlését](#delete-rule)).
  > [!NOTE]
  > A **összes forgalom** szabály ajánlott, ha, emiatt az algoritmus futni, a Security Center nem forgalom azonosítására szolgáló lehetővé kell tenni a létező NSG-konfiguráció alapján. A javasolt szabály ezért minden forgalom a megadott porton. Az ilyen típusú szabály neve "a rendszer által létrehozott" jelenik meg. Után ez a szabály kényszerítése, a tényleges nevét, az NSG-ben lesz egy karakterlánc-csoportból áll, a protokoll, a forgalom irányát, az "ELUTASÍTÁS" és a egy véletlenszerű számot.

*Az adaptív hálózati korlátozások a szabály módosítása:*

1. Módosítandó egy szabály paraméterek némelyike a **szabályok** lapra, kattintson a szabály a sor végén található három pontra (...), majd kattintson **szabály szerkesztése**.

   ![Szabály szerkesztése](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Az a **szabály szerkesztése** ablakban módosítsa, majd kattintson a kívánt adatainak frissítése **mentése**.

   > [!NOTE]
   > Kattintás után **mentése**, sikeresen módosította a szabályt. *Azonban nem telepítette, az NSG-hez.* Az alkalmazásukhoz, kell a listában válassza ki a szabályt, és kattintson a **érvényesítése** (a következő lépésben leírtak).

3. A frissített szabálynak, a alkalmazni a listából, válassza ki a frissített szabályt, és kattintson a **érvényesítése**.

### Új szabály hozzáadása <a name ="add-rule"> </a>


Hozzáadhat egy "engedélyezése" szabályt, nem a Security Center által ajánlott.

> [!NOTE]
> Itt csak "engedélyezése" szabályokat adhat hozzá. Ha szeretne hozzáadni, "Elutasítás" szabályok, megteheti közvetlenül az NSG-t a. További részletekért lásd: [létrehozása, módosítása vagy törlése a hálózati biztonsági csoport](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Az adaptív hálózati korlátozások szabály felvétele:*

1. Kattintson a **szabály felvétele** (a bal felső sarokban található).

   ![szabály hozzáadása](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Az a **szabály szerkesztése** ablakban adja meg az adatait, majd kattintson **mentése**.

   > [!NOTE]
   > Kattintás után **mentése**, a szabály sikeresen hozzáadta és szerepel a listán az egyéb javasolt szabályoknak. Azonban nem alkalmazza azt a az NSG-t. Az aktiválás kell a listában válassza ki a szabályt, és kattintson a **érvényesítése** (a következő lépésben leírtak).

3. A alkalmazni az új szabályt, a listában, válassza ki az új szabályt, majd kattintson **érvényesítése**.



### Szabály törlése <a name ="delete-rule"> </a>

Ha szükséges, ajánlott szabály törlését is. Például előfordulhat, hogy meghatározzák, hogy javasolt szabály alkalmazása sikerült jogos forgalom blokkolása.

*Az adaptív hálózati korlátozások a szabály törlése:*

1. Az a **szabályok** lapra, kattintson a szabály a sor végén található három pontra (...), majd kattintson **törlési szabály**.

   ![Szabály törlése](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

