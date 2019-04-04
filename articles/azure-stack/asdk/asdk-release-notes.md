---
title: A Microsoft Azure Stack Development Kit kibocsátási megjegyzései |} A Microsoft Docs
description: Fejlesztések, javításokat és az Azure Stack Development Kit kapcsolatos ismert problémák.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: ee98aee0873796c2a06db73d3365e3ff9ef87ccf
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915883"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a módosításokat, javításokat és ismert problémák az Azure Stack Development Kit (ASDK) kapcsolatos információkat. Ha nem biztos abban, hogy melyik verziót futtatja, akkor az [ellenőrizhető a portál használatával](../azure-stack-updates.md#determine-the-current-version).

What's new in a ASDK való feliratkozással vétele a [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [hírcsatorna](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-1903"></a>1903 összeállítása

A 1903 hasznos adatai nem tartalmaz egy ASDK kiadást.

## <a name="build-11902069"></a>1.1902.0.69 összeállítása

### <a name="new-features"></a>Új funkciók

- A 1902 build vezet be az Azure Stack rendszergazdai portál csomagok, ajánlatok, kvóták és kiegészítő csomagok létrehozására szolgáló új felhasználói felületet. További információk, többek között a képernyőképek: [csomagok, ajánlatok és kvóták létrehozása](../azure-stack-create-plan.md).

- Az egyéb módosítások és fejlesztések azzal kapcsolatban, ebben a kiadásban, lásd: [ebben a szakaszban](../azure-stack-update-1902.md#improvements) az Azure Stack a kibocsátási megjegyzéseket.

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Ismert problémák

- Hibát talált, amelyben a csomagok több mint 1450 bájt egy belső Load Balancer (ILB), a rendszer elveti. A probléma okozza a gazdagépen, hogy túl alacsony, hogy megfeleljen a szerepkört, amely 1901 től került, a gazdagép áthaladó VXLAN beágyazású csomagok MTU-beállítását. Legalább két forgatókönyv közül választhat, amelyek akkor fordulhatnak, amelyben úgy találtuk, manifest magát a probléma:

  - SQL-lekérdezéseket az SQL Always-On, amely egy belső Load Balancer (ILB) mögött, és több mint 660 bájt.
  - Kubernetes üzembe helyezések meghiúsulnak, ha több főkiszolgálót engedélyezését.  

  A probléma akkor fordul elő, ha egy virtuális Gépet és a egy ILB közötti kommunikáció az azonos virtuális hálózatba, de különböző alhálózatokon. A probléma megkerüléséhez az alábbi parancsokat egy rendszergazda jogú parancssorban a ASDK gazdagépen futó:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>1.1901.0.95 összeállítása

Tekintse meg a [fontos build adatai az Azure Stack kibocsátási megjegyzésekben](../azure-stack-update-1901.md#build-reference).

### <a name="changes"></a>Módosítások

A build az Azure Stack a következő fejlesztéseket tartalmazza:

- A BGP és a NAT-összetevők mostantól a fizikai gazdagépre vannak telepítve. Így nem kell a két nyilvános vagy a vállalati IP-címet a ASDK üzembe helyezéséhez, és emellett leegyszerűsíti az üzembe helyezés.
- Az Azure Stackkel integrált rendszerek biztonsági mentések mostantól [érvényesíthető](asdk-validate-backup.md) használatával a **asdk-installer.ps1** PowerShell-parancsfájlt.

### <a name="new-features"></a>Új funkciók

- Ebben a kiadásban új szolgáltatások listáját lásd: [ebben a szakaszban](../azure-stack-update-1901.md#new-features) kiadási megjegyzések az Azure Stack.

### <a name="fixed-and-known-issues"></a>Rögzített méretű és ismert problémák

- Ebben a kiadásban megoldott problémák listáját lásd: [ebben a szakaszban](../azure-stack-update-1901.md#fixed-issues) kiadási megjegyzések az Azure Stack. Ismert problémák listáját lásd: [ebben a szakaszban](../azure-stack-update-1901.md#known-issues-post-installation).
- Vegye figyelembe, hogy [elérhető az Azure Stack-gyorsjavítások](../azure-stack-update-1901.md#azure-stack-hotfixes) nem használhatók az Azure Stack ASDK.

## <a name="build-118110101"></a>1.1811.0.101 összeállítása

### <a name="changes"></a>Módosítások

A build az Azure Stack a következő fejlesztéseket tartalmazza:  

- Nincs új minimális és ajánlott hardver- és szoftverkövetelményei a ASDK egy készletét. Ezek új specifikáció ajánlott vannak dokumentálva [Azure Stack üzembe helyezési, tervezési megfontolások a](asdk-deploy-considerations.md). Az Azure Stack platform alakult ki, további szolgáltatások már elérhetők és további erőforrások lehet szükség. A nagyobb specifikáció átdolgozott javaslatokról tükrözik.

### <a name="new-features"></a>Új funkciók

Ebben a kiadásban új szolgáltatások listáját lásd: [ebben a szakaszban](../azure-stack-update-1811.md#new-features) kiadási megjegyzések az Azure Stack.

### <a name="fixed-and-known-issues"></a>Rögzített méretű és ismert problémák

Ebben a kiadásban megoldott problémák listáját lásd: [ebben a szakaszban](../azure-stack-update-1811.md#fixed-issues) kiadási megjegyzések az Azure Stack. Ismert problémák listáját lásd: [ebben a szakaszban](../azure-stack-update-1811.md#known-issues-post-installation).
