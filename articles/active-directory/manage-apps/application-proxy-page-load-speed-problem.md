---
title: Egy alkalmazásproxy-alkalmazás túl sokáig tart a betöltéshez | Microsoft Docs
description: Az Azure AD Application Proxyával kapcsolatos problémák elhárítása az oldalon
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1dc1b14a917786e124775a4505c53f63238a383
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86203854"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Egy alkalmazásproxy-alkalmazás túl sokáig tart a betöltéshez

Ebből a cikkből megtudhatja, hogy az Azure AD Application Proxy-alkalmazások miért tarthatnak hosszú időt a betöltéshez. Azt is ismerteti, hogy mit tehet a probléma megoldásához.

## <a name="overview"></a>Áttekintés
Habár az alkalmazásai működnek, hosszú késést tapasztalhatnak. Előfordulhat, hogy a sebesség növeléséhez hálózati topológiát kell megadnia. A különböző topológiák kiértékeléséhez tekintse meg a [hálózati megfontolásokat ismertető dokumentumot](application-proxy-network-topology.md).

A hálózati topológia mellett jelenleg nincsenek további javaslatok a teljesítmény finomhangolásához. Az alkalmazásproxy szolgáltatás kibontásakor előfordulhat, hogy az adatközpont fizikailag közelebb van. A szorosabb közelség segíthet a késésben. Az Azure-adatközpontok listáját a [késési teszt oldalon](http://www.azurespeed.com/Azure/Latency)tekintheti meg. 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Visszajelzés az Application proxy adatközpontjának helyeiről 
Előfordulhat, hogy olyan Azure-adatközpontok vannak, amelyek még nem tartalmazzák az alkalmazásproxy-t, de az Ön számára jelentős késéssel jár. Küldje el az adatközpont helyét a következőnek: aadapfeedback@microsoft.com . A Microsoft a bővítési csomagok visszajelzéseit használja.

A Microsoft a késés javítása érdekében további képességekkel dolgozik. Amint ezek a frissítések elérhetők, a dokumentáció frissülni fog.

## <a name="next-steps"></a>További lépések
[Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
