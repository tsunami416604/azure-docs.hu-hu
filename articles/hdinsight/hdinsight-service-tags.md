---
title: Hálózati biztonsági csoport (NSG) szolgáltatáscímkéi az Azure HDInsighthoz
description: A HDInsight szolgáltatáscímkék használatával engedélyezheti a fürtbe irányuló bejövő forgalmat a HDInsight állapot- és felügyeleti szolgáltatási csomópontjairól anélkül, hogy kifejezetten ip-címeket adna hozzá a hálózati biztonsági csoportokhoz.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117239"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Hálózati biztonsági csoport (NSG) szolgáltatáscímkéi az Azure HDInsighthoz

A hálózati biztonsági csoportok HDInsight szolgáltatáscímkéi az állapot- és felügyeleti szolgáltatások IP-címcsoportjai. Ezek a csoportok segítenek minimalizálni a biztonsági szabályok létrehozásának összetettségét. [A szolgáltatáscímkék](../virtual-network/security-overview.md#service-tags) alternatív módszert biztosítanak az adott IP-címekről érkező bejövő forgalom engedélyezéséhez anélkül, hogy a [felügyeleti IP-címeket](hdinsight-management-ip-addresses.md) a hálózati biztonsági csoportokba írnák be.

Ezeket a szolgáltatáscímkéket a HDInsight szolgáltatás hozta létre és kezeli. Nem hozhat létre saját szolgáltatáscímkét, és nem módosíthatja a meglévő címkét. A Microsoft kezeli a szolgáltatáscímkének megfelelő címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

## <a name="getting-started-with-service-tags"></a>A szolgáltatáscímkék használata

A hálózati biztonsági csoportokban két lehetőség közül választhat:

1. Egyetlen HDInsight-szolgáltatáscímke használata – ez a beállítás megnyitja a virtuális hálózatot az összes OLYAN IP-cím között, amelyet a HDInsight szolgáltatás a fürtök figyelésére használ az összes régióban. Ez a beállítás a legegyszerűbb módszer, de nem megfelelő, ha korlátozó biztonsági követelményekkel rendelkezik.

1. Használjon több regionális szolgáltatáscímkét – ez a beállítás csak az adott régióban a HDInsight által használt IP-címek számára nyitja meg a virtuális hálózatot. Ha azonban több régiót használ, akkor több szolgáltatáscímkét kell hozzáadnia a virtuális hálózathoz.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Egyetlen globális HDInsight-szolgáltatáscímke használata

A szolgáltatáscímkék HDInsight-fürtdel való használatának legegyszerűbb módja `HDInsight` a globális címke hozzáadása egy hálózati biztonsági csoportszabályhoz.

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a hálózati biztonsági csoportot.

1. A **Beállítások csoportban**válassza **a Bejövő biztonsági szabályok**lehetőséget, majd a + **Add**lehetőséget.

1. A **Forrás** legördülő listában válassza a **Szervizcímke**lehetőséget.

1. A **Forrásszolgáltatás címke** legördülő listájában válassza a **HDInsight**lehetőséget.

    ![Az Azure Portal szolgáltatáscímke hozzáadása](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Ez a címke tartalmazza az összes olyan régió ban, ahol a HDInsight elérhető, az állapot- és felügyeleti szolgáltatások IP-címét tartalmazza, és biztosítja, hogy a fürt képes legyen kommunikálni a szükséges állapot- és felügyeleti szolgáltatásokkal, függetlenül attól, hogy hol jön létre.

## <a name="use-regional-hdinsight-service-tags"></a>A regionális HDInsight-szolgáltatáscímkék használata

Ha az első lehetőség nem működik, mert szigorúbb engedélyekre van szüksége, akkor csak a régióra vonatkozó szolgáltatáscímkéket engedélyezhet. A megfelelő szolgáltatáscímkék lehetnek egy, két vagy három szolgáltatáscímkék, attól függően, hogy a régió, ahol a fürt jön létre.

Ha meg szeretné tudni, hogy mely szolgáltatáscímkéket szeretné hozzáadni a régióhoz, olvassa el a dokumentum következő szakaszait.

### <a name="use-a-single-regional-service-tag"></a>Egyetlen regionális szolgáltatáscímke használata

Ha a második szolgáltatáscímkét részesíti előnyben, és a fürt a táblázatban felsorolt régiók egyikében található, akkor csak egyetlen regionális szolgáltatáscímkét kell hozzáadnia a hálózati biztonsági csoporthoz.

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
| Azure Government | USDoD Közép   | HDInsight.USDoDKözép |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | Usdod keleti | HDInsight.USDoDEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Több regionális szolgáltatáscímke használata

Ha a második szolgáltatáscímke-beállítást részesíti előnyben, és a régió, ahol a fürt jön létre, nem szerepel a fent, akkor engedélyeznie kell több regionális szolgáltatáscímkét. Annak szükségességét, hogy egynél több annak köszönhető, hogy a különbségek elrendezése erőforrás-szolgáltatók a különböző régiókban.

A többi régió csoportokra van osztva az általuk használt regionális szolgáltatási címkék alapján.

#### <a name="group-1"></a>1. csoport

Ha a fürt az alábbi táblázat egyik régiójában jön `HDInsight.WestUS` létre, engedélyezze a szolgáltatáscímkéket, és `HDInsight.EastUS` a felsorolt regionális szolgáltatáscímke mellett. Ebben a szakaszban a régiók három szolgáltatáscímkét igényel.

Ha például a fürt a `East US 2` régióban van létrehozva, akkor a következő szolgáltatáscímkéket kell hozzáadnia a hálózati biztonsági csoporthoz:

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

A **Kína északi** és **keleti**régióiban található klasztereknek `HDInsight.ChinaNorth` két `HDInsight.ChinaEast`szolgáltatási címkét kell engedélyezniuk: és .

#### <a name="group-3"></a>3. csoport

Klaszterek régióiban **US Gov Iowa** és us Gov **Virginia** `HDInsight.USGovIowa` , `HDInsight.USGovVirginia`kell, hogy két szolgáltatási címkék: és .

#### <a name="group-4"></a>4. csoport

A **németországi közép-** és **németországi**régiókban található klasztereknek `HDInsight.GermanyCentral` két `HDInsight.GermanyNorthEast`szolgáltatási címkét kell engedélyezniuk: és .

## <a name="next-steps"></a>További lépések

- [Hálózati biztonsági csoportok - szolgáltatáscímkék](../virtual-network/security-overview.md#security-rules)
- [Virtuális hálózatok létrehozása Az Azure HDInsight-fürtökhöz](hdinsight-create-virtual-network.md)
