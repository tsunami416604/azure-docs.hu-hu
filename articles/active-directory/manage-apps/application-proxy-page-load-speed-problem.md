---
title: Egy alkalmazásproxy-alkalmazás betöltése túl sokáig tart |} A Microsoft Docs
description: Oldal betöltési teljesítmény az Azure AD-alkalmazásproxy problémáinak hibaelhárítása
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4110ceddb55de1990d85597f8c1a9618743f946
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170431"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Egy alkalmazásproxy-alkalmazás betöltése túl sokáig tart

Ez a cikk segít megismerni, miért az Azure AD-alkalmazásproxy alkalmazás betöltése hosszú időt vehet igénybe. Azt is bemutatja, mi mindent a probléma megoldásához.

## <a name="overview"></a>Áttekintés
Bár az alkalmazások dolgozik, akkor a hosszú várakozási ideje tapasztalhatnak. Előfordulhat, hogy hálózati topológia honlap, amely növeli a sebességet teheti. Egy másik topológiák próbaidőszakára, tekintse meg a [hálózati megfontolások dokumentum](application-proxy-network-topology.md).

Hálózati topológia mellett jelenleg nincs további javaslatok a teljesítmény finomhangolásának. Az alkalmazásproxy szolgáltatás bővül, akkor előfordulhat, hogy jár egy adatközpont, amely fizikailag közelebb van. A közelebb közelében segíthetnek, a késés. Azure-beli adatközpontok listájáért tekintse meg a [késés tesztlap](http://www.azurespeed.com/Azure/Latency). 

Az alkalmazásproxy-szolgáltatás és az adatközpontok között található a [összekötő portok vizsgálati eszköz](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Visszajelzés az alkalmazásproxy-adatok központ helye 
Előfordulhat, hogy Azure-beli adatközpontok, nem még alkalmazásproxy tartalmazzák, de az Ön számára egy nagyszerű késés fokozása vezetne. Az Adatközpont-helyre való küldés aadapfeedback@microsoft.com. A Microsoft visszajelzést használja a nyelvi csomagok.

A Microsoft dolgozik további képességeket késés javítása érdekében. Ezek a fejlesztések elérhetők, amint az a dokumentáció frissülni fog.

## <a name="next-steps"></a>További lépések
[Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
