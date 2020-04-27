---
title: Egy alkalmazásproxy-alkalmazás túl sokáig tart a betöltéshez | Microsoft Docs
description: Az Azure AD Application Proxyával kapcsolatos problémák elhárítása az oldalon
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac1182d719d7c90129115e1fadf94f4f86a28e8
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "65782652"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Egy alkalmazásproxy-alkalmazás túl sokáig tart a betöltéshez

Ebből a cikkből megtudhatja, hogy az Azure AD Application Proxy-alkalmazások miért tarthatnak hosszú időt a betöltéshez. Azt is ismerteti, hogy mit tehet a probléma megoldásához.

## <a name="overview"></a>Áttekintés
Habár az alkalmazásai működnek, hosszú késést tapasztalhatnak. Előfordulhat, hogy a sebesség növeléséhez hálózati topológiát kell megadnia. A különböző topológiák kiértékeléséhez tekintse meg a [hálózati megfontolásokat ismertető dokumentumot](application-proxy-network-topology.md).

A hálózati topológia mellett jelenleg nincsenek további javaslatok a teljesítmény finomhangolásához. Az alkalmazásproxy szolgáltatás kibontásakor előfordulhat, hogy az adatközpont fizikailag közelebb van. A szorosabb közelség segíthet a késésben. Az Azure-adatközpontok listáját a [késési teszt oldalon](http://www.azurespeed.com/Azure/Latency)tekintheti meg. 

Az alkalmazásproxy szolgáltatással rendelkező adatközpontok a [Connector ports test eszközzel](https://aadap-portcheck.connectorporttest.msappproxy.net/)találhatók. 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Visszajelzés az Application proxy adatközpontjának helyeiről 
Előfordulhat, hogy olyan Azure-adatközpontok vannak, amelyek még nem tartalmazzák az alkalmazásproxy-t, de az Ön számára jelentős késéssel jár. Küldje el az adatközpont helyét aadapfeedback@microsoft.coma következőnek:. A Microsoft a bővítési csomagok visszajelzéseit használja.

A Microsoft a késés javítása érdekében további képességekkel dolgozik. Amint ezek a frissítések elérhetők, a dokumentáció frissülni fog.

## <a name="next-steps"></a>További lépések
[Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
