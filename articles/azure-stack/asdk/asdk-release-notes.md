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
ms.date: 02/27/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 8268408137653e99863f7803300e6d843c1d9a47
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889307"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a módosításokat, javításokat és ismert problémák az Azure Stack Development Kit (ASDK) kapcsolatos információkat. Ha nem biztos abban, hogy melyik verziót futtatja, akkor az [ellenőrizhető a portál használatával](../azure-stack-updates.md#determine-the-current-version).

What's new in a ASDK való feliratkozással vétele a [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [hírcsatorna](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

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

## <a name="build-11809090"></a>1.1809.0.90 összeállítása

### <a name="new-features"></a>Új funkciók

Ebben a kiadásban új szolgáltatások listáját lásd: [ebben a szakaszban](../azure-stack-update-1809.md#new-features) kiadási megjegyzések az Azure Stack.

### <a name="fixed-issues"></a>Hibák kijavítva:

Ebben a kiadásban megoldott problémák listáját lásd: [ebben a szakaszban](../azure-stack-update-1809.md#fixed-issues).

### <a name="known-issues"></a>Ismert problémák

Ebben a kiadásban az ismert problémák listáját lásd: [ebben a szakaszban](../azure-stack-update-1809.md#known-issues-post-installation).