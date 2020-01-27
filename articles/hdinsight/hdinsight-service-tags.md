---
title: Hálózati biztonsági csoport (NSG) szolgáltatás címkéi az Azure HDInsight
description: A HDInsight szolgáltatás-címkék használatával engedélyezheti a fürt bejövő forgalmát a HDInsight állapot-és felügyeleti szolgáltatások csomópontjairól anélkül, hogy explicit módon hozzáadja az IP-címeket a hálózati biztonsági csoportokhoz.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/05/2019
ms.openlocfilehash: 24ecf90c2ffc88415afbf84f54af3efa7d5f4a39
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435427"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Hálózati biztonsági csoport (NSG) szolgáltatás címkéi az Azure HDInsight

A hálózati biztonsági csoportokhoz (NSG) tartozó HDInsight-szolgáltatási címkék az állapot-és felügyeleti szolgáltatások IP-címeinek csoportjai. Ezek a csoportok segítenek a biztonsági szabályok létrehozásának összetettségének minimalizálásában. A [szolgáltatási címkék](../virtual-network/security-overview.md#service-tags) alternatív módszert biztosítanak a bejövő forgalom adott IP-címekről való engedélyezésére anélkül, hogy a hálózati biztonsági csoportokban a [felügyeleti IP-címeket](hdinsight-management-ip-addresses.md) be kellene írni.

Ezeket a szolgáltatási címkéket a HDInsight szolgáltatás hozza létre és kezeli. Nem hozhat létre saját szolgáltatási címkét, vagy nem módosíthat meglévő címkét. A Microsoft kezeli a szolgáltatási címkének megfelelő címet, és automatikusan frissíti a szolgáltatási címkét a címek módosításaként.

## <a name="getting-started-with-service-tags"></a>A szolgáltatáshoz tartozó címkék első lépései

A hálózati biztonsági csoportokban két lehetőség van a szolgáltatási címkék használatára:

1. Egyetlen HDInsight szolgáltatás használata – ezzel a beállítással megnyithatja a virtuális hálózatot minden olyan IP-címre, amelyet a HDInsight szolgáltatás használ a fürtök minden régióban való figyelésére. Ez a legegyszerűbb módszer, de nem lehet megfelelő, ha korlátozó biztonsági követelményekkel rendelkezik.

1. Több regionális szolgáltatási címke használata – ezzel a beállítással megnyithatja a virtuális hálózatot, hogy csak az adott régióban használt IP-címekre HDInsight. Ha azonban több régiót használ, több szolgáltatási címkét is hozzá kell adnia a virtuális hálózathoz.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Egyetlen globális HDInsight szolgáltatási címke használata

A HDInsight-fürthöz tartozó szolgáltatási címkék használatának legegyszerűbb módja, ha hozzáadja a globális címkét `HDInsight` egy hálózati biztonsági csoport szabályához.

1. A [Azure Portal](https://portal.azure.com/)válassza ki a hálózati biztonsági csoportot.

1. A **Beállítások**területen válassza a **bejövő biztonsági szabályok**elemet, majd válassza a **+ Hozzáadás**lehetőséget.

1. A **forrás** legördülő listából válassza ki a **szolgáltatás címkéje**elemet.

1. A **forrás szolgáltatás címkéje** legördülő listában válassza a **HDInsight**lehetőséget.

    ![Azure Portal szolgáltatási címke hozzáadása](./media/hdinisght-service-tags/azure-portal-add-service-tag.png)

Ez a címke tartalmazza az állapot-és felügyeleti szolgáltatások IP-címeit az összes olyan régióban, ahol a HDInsight elérhető, és biztosítja, hogy a fürt a létrehozásuk helyétől függetlenül képes legyen kommunikálni a szükséges egészségügyi és felügyeleti szolgáltatásokkal.

## <a name="use-regional-hdinsight-service-tags"></a>Területi HDInsight szolgáltatásbeli címkék használata

Ha az egyik lehetőség nem fog működni, mert szigorúbb engedélyekre van szüksége, csak az adott régióhoz tartozó szolgáltatási címkéket engedélyezheti. A megfelelő szolgáltatási címkék lehetnek egy, kettő vagy három szolgáltatási címke, attól függően, hogy melyik régiót hozza létre a fürt.

Ha szeretné megtudni, hogy mely szolgáltatási címkéket szeretné hozzáadni a régióhoz, olvassa el a dokumentum következő részeit.

### <a name="use-a-single-regional-service-tag"></a>Egyetlen regionális szolgáltatási címke használata

Ha a Service címke második lehetőségét részesíti előnyben, és a fürt a táblázatban felsorolt egyik régióban található, akkor csak egyetlen regionális szolgáltatási címkét kell hozzáadnia a hálózati biztonsági csoportjához.

| Ország/régió | Region (Régió) | Szolgáltatás címkéje |
| ---- | ---- | ---- |
| Ausztrália | Ausztrália keleti régiója | HDInsight. AustraliaEast |
| &nbsp; | Délkelet-Ausztrália | HDInsight. AustraliaSoutheast |
| &nbsp; | Ausztrália középső régiója | HDInsight. AustraliaCentral |
| Kína | Kína 2. keleti régiója | HDInsight. ChinaEast2 |
| &nbsp; | Kína 2. északi régiója | HDInsight. ChinaNorth2 |
| Egyesült Államok | USA északi középső régiója | HDInsight. NorthCentralUS |
| &nbsp; | USA 2. nyugati régiója | HDInsight. WestUS2 |
| &nbsp; | USA nyugati középső régiója | HDInsight. WestCentralUS |
| Kanada | Kelet-Kanada | HDInsight. CanadaEast |
| Brazília | Dél-Brazília | HDInsight. BrazilSouth |
| Dél-Korea | Korea középső régiója | HDInsight. KoreaCentral |
| &nbsp; | Korea déli régiója | HDInsight. KoreaSouth |
| India | Közép-India | HDInsight. CentralIndia |
| &nbsp; | Dél-India | HDInsight. SouthIndia |
| Japán | Nyugat-Japán | HDInsight. JapanWest |
| Franciaország | Közép-Franciaország| HDInsight. FranceCentral |
| Egyesült Királyság | Egyesült Királyság déli régiója | HDInsight. UKSouth |
| Azure Government | USA középső régiója   | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | USA keleti régiója | HDInsight. USDoDEast |

### <a name="use-multiple-regional-service-tags"></a>Több regionális szolgáltatási címke használata

Ha a 2. lehetőséget választja, és az a régió, amelyben a fürt létrejött, nem szerepel a fentiekben, több regionális szolgáltatás címkét is engedélyeznie kell. Ha egynél többre van szükség, az erőforrás-szolgáltatók különböző régiókban való elrendezése eltérő lehet.

A fennmaradó régiók csoportokba vannak osztva, az általuk használt regionális szolgáltatási címkék alapján.

#### <a name="group-1"></a>1\. csoport

Ha a fürt az alábbi táblázat egyik régiójában jön létre, engedélyezze a szolgáltatás címkéit `HDInsight.WestUS` és `HDInsight.EastUS` a felsorolt regionális szolgáltatás címkén kívül. Az ebben a szakaszban található régiók három szolgáltatási címkét igényelnek.

Ha például a fürt a `East US 2` régióban lett létrehozva, akkor a következő szolgáltatási címkéket kell felvennie a hálózati biztonsági csoportba:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Ország/régió | Region (Régió) | Szolgáltatás címkéje |
| ---- | ---- | ---- |
| Egyesült Államok | USA 2. keleti régiója | HDInsight. EastUS2 |
| &nbsp; | USA középső régiója | HDInsight. CentralUS |
| &nbsp; | NorthCentral minket | HDInsight. NorthCentralUS |
| &nbsp; | USA déli középső régiója | HDInsight. SouthCentralUS |
| &nbsp; | USA keleti régiója | HDInsight. EastUS |
| &nbsp; | USA nyugati régiója | HDInsight. WestUS |
| Japán | Kelet-Japán | HDInsight. JapanEast |
| Európa | Észak-Európa | HDInsight. NorthEurope |
| &nbsp; | Nyugat-Európa| HDInsight. WestEurope |
| Ázsia | Kelet-Ázsia | HDInsight. EastAsia |
| &nbsp; | Délkelet-Ázsia | HDInsight. SoutheastAsia |
| Ausztrália | Ausztrália keleti régiója | HDInsight. AustraliaEast |

#### <a name="group-2"></a>2\. csoport

**Észak-Kína** és **Kelet-Kína**régiójában lévő fürtökön engedélyezni kell két szolgáltatás címkéit: `HDInsight.ChinaNorth` és `HDInsight.ChinaEast`.

#### <a name="group-3"></a>3\. csoport

**US gov Iowa** és **US gov Virginia**régiójában lévő fürtökön engedélyezni kell két szolgáltatás címkéit: `HDInsight.USGovIowa` és `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>4\. csoport

A **németországi Közép** -és **Kelet-Németország**régiójában lévő fürtökön két szolgáltatási címkét kell engedélyezni: `HDInsight.GermanyCentral` és `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Következő lépések

- [Hálózati biztonsági csoportok – szolgáltatás címkéi](../virtual-network/security-overview.md#security-rules)
- [Virtuális hálózatok létrehozása az Azure HDInsight-fürtökhöz](hdinsight-create-virtual-network.md)
