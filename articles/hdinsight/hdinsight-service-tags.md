---
title: Hálózati biztonsági csoport (NSG) szolgáltatáscímkéi az Azure HDInsighthoz
description: A HDInsight szolgáltatáscímkék használatával engedélyezheti a fürtbe irányuló bejövő forgalmat az állapot- és felügyeleti szolgáltatások csomópontjaiból anélkül, hogy IP-címeket adna hozzá az NSG-khez.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 5608d0cd83e506bc6b30337db5148f344f59f80e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410851"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>NSG szolgáltatáscímkék az Azure HDInsighthoz

Az Azure HDInsight szolgáltatáscímkék hálózati biztonsági csoportok (NSG-k) az állapot- és felügyeleti szolgáltatások IP-címcsoportjai. Ezek a csoportok segítenek minimalizálni a biztonsági szabályok létrehozásának összetettségét. [A szolgáltatáscímkék](../virtual-network/security-overview.md#service-tags) lehetővé teszik az adott IP-címekről érkező bejövő forgalmat anélkül, hogy az nsg-kben megírnák az egyes [felügyeleti IP-címeket.](hdinsight-management-ip-addresses.md)

A HDInsight szolgáltatás kezeli ezeket a szolgáltatáscímkéket. Nem hozhat létre saját szolgáltatáscímkét, és nem módosíthatja a meglévő címkét. A Microsoft kezeli a szolgáltatáscímkének megfelelő címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

## <a name="get-started-with-service-tags"></a>A szolgáltatáscímkék használata

A hálózati biztonsági csoportokban két lehetőség közül választhat:

- **Egyetlen globális HDInsight-szolgáltatáscímke használata:** Ez a beállítás megnyitja a virtuális hálózatot az összes OLYAN IP-címre, amelyet a HDInsight szolgáltatás a fürtök figyelésére használ az összes régióban. Ez a beállítás a legegyszerűbb módszer, de nem biztos, hogy megfelelő, ha korlátozó biztonsági követelményekkel rendelkezik.

- **Több regionális szolgáltatáscímke használata:** Ez a beállítás csak az adott régióban a HDInsight által használt IP-címek számára nyitja meg a virtuális hálózatot. Ha azonban több régiót használ, több szolgáltatáscímkét kell hozzáadnia a virtuális hálózathoz.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Egyetlen globális HDInsight-szolgáltatáscímke használata

A legegyszerűbb en keresztül kezdheti használni a szolgáltatáscímkéket a `HDInsight` HDInsight-fürttel, ha hozzáadja a globális címkét egy NSG-szabályhoz.

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a hálózati biztonsági csoportot.

1. A **Beállítások csoportban**válassza **a Bejövő biztonsági szabályok**lehetőséget, majd a + **Add**lehetőséget.

1. A **Forrás** legördülő listában válassza a **Szervizcímke**lehetőséget.

1. A **Forrásszolgáltatás címke** legördülő listájában válassza a **HDInsight**lehetőséget.

    ![Szolgáltatáscímke hozzáadása az Azure Portalról](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Ez a címke tartalmazza az összes olyan régió ban az állapot- és felügyeleti szolgáltatások IP-címét, ahol a HDInsight elérhető. A címke biztosítja, hogy a fürt képes kommunikálni a szükséges állapot-és felügyeleti szolgáltatások nem számít, hol jön létre.

## <a name="use-regional-hdinsight-service-tags"></a>A regionális HDInsight-szolgáltatáscímkék használata

Ha a globális címke beállítás nem működik, mert szigorúbb engedélyekre van szüksége, csak a régióra vonatkozó szolgáltatáscímkéket engedélyezhet. Előfordulhat, hogy több szolgáltatáscímkék, attól függően, hogy a régió, ahol a fürt jön létre.

Ha meg szeretné tudni, hogy mely szolgáltatáscímkéket kell hozzáadnia a régióhoz, olvassa el a cikk következő szakaszait.

### <a name="use-a-single-regional-service-tag"></a>Egyetlen regionális szolgáltatáscímke használata

Ha a fürt található, a táblázatban felsorolt régióban, csak meg kell adnia egy regionális szolgáltatás címkét az NSG.If your cluster is located in a region listed in this table, you only need to add a single regional service tag to your NSG.

| Ország | Régió | Szolgáltatáscímke |
| ---- | ---- | ---- |
| Ausztrália | Kelet-Ausztrália | HDInsight.AustraliaKelet |
| &nbsp; | Délkelet-Ausztrália | HDInsight.AustraliaDélkelet |
| &nbsp; | Ausztrália középső régiója | HDInsight.AustraliaKözponti |
| Kína | Kína Keleti 2 | HDInsight.ChinaEast2 |
| &nbsp; | Kína Észak 2 | HDInsight.ChinaNorth2 |
| Egyesült Államok | USA északi középső régiója | HDInsight.NorthCentralUS |
| &nbsp; | USA nyugati régiója, 2. | HDInsight.WestUS2 |
| &nbsp; | USA nyugati középső régiója | HDInsight.WestCentralUS |
| Kanada | Kelet-Kanada | HDInsight.CanadaEast |
| Brazília | Dél-Brazília | HDInsight.BrazíliaDél |
| Dél-Korea | Dél-Korea középső régiója | HDInsight.KoreaKözponti |
| &nbsp; | Dél-Korea déli régiója | HDInsight.KoreaDél |
| India | Közép-India | HDInsight.CentralIndia |
| &nbsp; | Dél-India | HDInsight.DélIndia |
| Japán | Nyugat-Japán | HDInsight.JapanWest |
| Franciaország | Közép-Franciaország| HDInsight.FranceCentral |
| Egyesült Királyság | Az Egyesült Királyság déli régiója | HDInsight.UKDél |
| Azure Government | USDoD Közép | HDInsight.USDoDKözép |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | Usdod keleti | HDInsight.USDoDEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Több regionális szolgáltatáscímke használata

Ha a régió, ahol a fürt jött létre, nem szerepel az előző táblázatban, engedélyeznie kell több regionális szolgáltatás címkéket. Az egynél több erőforrás-szolgáltató eltérő elrendezése miatt kell egynél többet használni a különböző régiókban.

A többi régió csoportokra van osztva az általuk használt regionális szolgáltatási címkék alapján.

#### <a name="group-1"></a>1. csoport

Ha a fürt az alábbi táblázat ban található régiók egyikében van létrehozva, engedélyezze a szolgáltatáscímkéket `HDInsight.WestUS` és `HDInsight.EastUS`a . Is, a regionális szolgáltatási címke szerepel. Ebben a szakaszban a régiók három szolgáltatáscímkét igényel.

Ha például a fürt a `East US 2` régióban van létrehozva, a következő szolgáltatáscímkéket kell hozzáadnia a hálózati biztonsági csoporthoz:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Ország | Régió | Szolgáltatáscímke |
| ---- | ---- | ---- |
| Egyesült Államok | USA 2. keleti régiója | HDInsight.EastUS2 |
| &nbsp; | USA középső régiója | HDInsight.CentralUS |
| &nbsp; | USA északnyugati régiója | HDInsight. Észak-Közép-Amerikai Egyesült Államok |
| &nbsp; | USA déli középső régiója | HDInsight.SouthCentralUS |
| &nbsp; | USA keleti régiója | HDInsight.EastUS |
| &nbsp; | USA nyugati régiója | HDInsight.WestUS |
| Japán | Kelet-Japán | HDInsight.JapanKelet |
| Európa | Észak-Európa | HDInsight.NorthEurope |
| &nbsp; | Nyugat-Európa| HDInsight.WestEurope |
| Ázsia | Kelet-Ázsia | HDInsight.Kelet-Ázsia |
| &nbsp; | Délkelet-Ázsia | HDInsight.Délkelet-Ázsia |
| Ausztrália | Kelet-Ausztrália | HDInsight.AustraliaKelet |

#### <a name="group-2"></a>2. csoport

A *Kína északi* és *keleti* régióiban található fürtöknek `HDInsight.ChinaNorth` `HDInsight.ChinaEast`két szolgáltatási címkét kell engedélyezniuk: és .

#### <a name="group-3"></a>3. csoport

Klaszterek régióiban *US Gov Iowa* és az USA Gov `HDInsight.USGovVirginia` *Virginia* kell, hogy két szolgáltatási címkék: `HDInsight.USGovIowa` és .

#### <a name="group-4"></a>4. csoport

A *Németországi Közép-* és *Németország északkeleti* régióiban `HDInsight.GermanyCentral` található `HDInsight.GermanyNortheast`fürtöknek két szolgáltatási címkét kell engedélyezniuk: és .

## <a name="next-steps"></a>További lépések

- [Hálózati biztonsági csoportok: szolgáltatáscímkék](../virtual-network/security-overview.md#security-rules)
- [Virtuális hálózatok létrehozása Az Azure HDInsight-fürtökhöz](hdinsight-create-virtual-network.md)
