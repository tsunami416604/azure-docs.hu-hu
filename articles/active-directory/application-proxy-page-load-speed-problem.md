---
title: Az alkalmazásproxy alkalmazás betöltése túl sokáig tart |} Microsoft Docs
description: Elhárítása lap betöltési teljesítmény az Azure AD-alkalmazásproxy
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 26acc620184b51719a2ee55b75bd01966d225b8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330927"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Az alkalmazásproxy alkalmazás betöltése túl sokáig tart

Ez a cikk segítségével megismerheti, hogy miért érdemes az Azure AD alkalmazásproxy alkalmazás betöltése sok időre lehet szükség. Azt is bemutatja, mi mindent a probléma megoldásához.

## <a name="overview"></a>Áttekintés
Bár az alkalmazások dolgozik, akkor tapasztalhat, a hosszú várakozási ideje. Előfordulhat, hogy sebesség növelése érdekében, a hálózati topológia beállításokból állnak. Különböző topológiák értékelése, tekintse meg a [hálózati szempontok dokumentum](manage-apps/application-proxy-network-topology.md).

Hálózati topológia mellett jelenleg nincs további javaslatok teljesítményhangolás. Az alkalmazásproxy-szolgáltatás bővíti, akkor előfordulhat, hogy térjen egy adatközpont, amely fizikailag közelebb van. A szorosabb közelségi kapcsolat késési segíthet. Azure-adatközpont listájáért lásd: a [késés tesztoldalt](http://www.azurespeed.com/Azure/Latency). 

Az alkalmazásproxy-szolgáltatás a adatközpontjaiban található az a [összekötő portok vizsgálati eszköz](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Alkalmazásproxy data center helyeken visszajelzés 
Előfordulhat, hogy Azure-adatközpont, amely nem még alkalmazásproxy tartalmaz, de meg a nagy késleltetésű fokozása vezetne. Az Adatközpont-helyre való küldés aadapfeedback@microsoft.com. A Microsoft a visszajelzést bővítése tervek használ.

Microsoft dolgozik a további lehetőségeket a késés javítására. Ezek a fejlesztések elérhetők, amint a dokumentáció fog frissülni.

## <a name="next-steps"></a>További lépések
[A meglévő helyszíni proxykiszolgálókkal működik](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
