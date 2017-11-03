---
title: "Azure-tárolót példányok tárolócsoportok"
description: "Tárolócsoportok működése az Azure-tároló példányok ismertetése"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 25eab41c3f0c986bcce33123f86f4c9638b77191
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="container-groups-in-azure-container-instances"></a>Azure-tárolót példányát tárolócsoportok

A legfelső szintű erőforrás Azure tároló példányát tároló olyan. Ez a cikk ismerteti a tárolócsoportok és forgatókönyvek milyen típusú engedélyezése.

## <a name="how-a-container-group-works"></a>Egy tároló csoport működése

A tárolócsoport gyűjteménye, ugyanazon gazdagép ütemezhetők és egy életciklussal, helyi hálózati és tárolási köteteket megosztott tárolókat. Hasonló, amelynek a *pod* a [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) és [DC/OS](https://dcos.io/docs/1.9/deploying-services/pods/).

Az alábbi ábrán látható egy példa a tároló-csoportot, amely több tároló tartalmazza.

![Tároló csoportok – példa][container-groups-example]

Vegye figyelembe:

- A csoport egyetlen gépen van ütemezve.
- A csoport egy egyetlen nyilvános IP-cím, egy kitett port mutatja.
- A csoport két tárolók tevődik össze. Egy tároló 80-as porton, a további figyeli közben 5000-es porton figyel.
- A csoport tartalmazza a kötet csatlakoztatások, két Azure fájlmegosztásokat, és minden egyes tároló csatlakoztatja a megosztások helyileg egyikét.

### <a name="networking"></a>Hálózat

Tárolócsoportok ossza meg IP-cím és port névtér az IP-címet. Ahhoz, hogy a külső ügyfelek számára a csoporton belül tárolója, fel kell fednie a portot, az IP-cím és a tárolóból. Mert a csoport tárolókra port névtér port hozzárendelése nem támogatott. Tárolók egy csoporton belüli képes elérni egymást localhost ki van téve, portokon keresztül akkor is, ha azokat a portokat nem érhetők el kívülről a csoporthoz tartozó IP-címe.

### <a name="storage"></a>Storage

Megadhatja, hogy külső kötetek csatlakoztatni a tároló csoporton belül. A megadott elérési utak belül az egyes tárolók egy csoport leképezheti a köteteket.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Több tároló csoportok olyan hasznos olyan esetekben, ahol kis számú tároló képek, amely különböző csapatok által kézbesítése, és külön erőforrás követelményei egyetlen működési feladat feloszthatja szeretné. Példa használati lehetnek:

- Egy alkalmazás-tárolót és a naplózási tároló. A naplózási tároló gyűjti a naplókat, valamint a metrikák kimeneti a fő alkalmazás, és hosszú távú tárolás írja őket.
- Az alkalmazások és a figyelési tároló. A figyelési tároló rendszeres időközönként egy kérést küld annak érdekében, hogy fut, és helytelenül válaszol, és riasztást küld, ha nem az alkalmazás.
- Egy tároló kiszolgáló webalkalmazás és húzza a legújabb tartalom a verziókövetésből tárolója.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [a több tároló csoport telepítése](container-instances-multi-container-group.md) az Azure Resource Manager sablonnal.

<!-- IMAGES -->

[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png