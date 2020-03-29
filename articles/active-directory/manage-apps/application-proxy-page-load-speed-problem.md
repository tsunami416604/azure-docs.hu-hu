---
title: Az alkalmazásproxy-alkalmazások betöltése túl sokáig tart | Microsoft dokumentumok
description: Az Azure AD alkalmazásproxyval kapcsolatos lapbetöltési teljesítményproblémák elhárítása
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65782652"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Az alkalmazásproxy-alkalmazások betöltése túl sokáig tart

Ez a cikk segít megérteni, hogy egy Azure AD alkalmazásproxy-alkalmazás betöltése miért hosszú időt vehet igénybe. Azt is elmagyarázza, hogy mit tehet a probléma megoldásáért.

## <a name="overview"></a>Áttekintés
Bár az alkalmazások működnek, hosszú késleltetést tapasztalhatnak. Előfordulhat, hogy a hálózati topológia csíp, hogy tudod, hogy javítsa a sebességet. A különböző topológiák kiértékelését a [hálózati szempontokat tartalmazó dokumentumban](application-proxy-network-topology.md)lehet tudni.

A hálózati topológia mellett jelenleg nincs további javaslat a teljesítményhangolására. Az alkalmazásproxy-szolgáltatás kibővítésével előfordulhat, hogy egy fizikailag közelebbi adatközpontba kerül. A közelebbi közelség segíthet a késésben. Az Azure-adatközpontok listáját a [késéstesztlapján](http://www.azurespeed.com/Azure/Latency)találja. 

Az alkalmazásproxy-szolgáltatással rendelkező adatközpontok az [Összekötőportok teszteszközzel](https://aadap-portcheck.connectorporttest.msappproxy.net/)találhatók. 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Visszajelzés az alkalmazásproxy-adatközpontok helyeiről 
Előfordulhat, hogy az Azure-adatközpontok, amelyek még nem tartalmazzák az alkalmazásproxy, de nagy késés javulást eredményezne az Ön számára. Az adatközpont helyének aadapfeedback@microsoft.comküldése a rendszernek. A Microsoft a visszajelzéseket a bővítési tervekhez használja fel.

A Microsoft további képességeken dolgozik a késés javítása érdekében. Amint ezek a fejlesztések rendelkezésre állnak, a dokumentáció frissül.

## <a name="next-steps"></a>További lépések
[Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
