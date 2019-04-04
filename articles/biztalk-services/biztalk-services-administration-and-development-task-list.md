---
title: Felügyeleti és fejlesztési tevékenységlista a BizTalk Services |} A Microsoft Docs
description: Tervezés és a feladat üzembe helyezéséhez az Azure BizTalk Services támogatás.
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 6b8e0fea73dc24f7e680482be1f06fba6d702804
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916240"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Felügyelet és a BizTalk Services fejlesztői feladatlista

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Első lépések
Ha a Microsoft Azure BizTalk Services dolgozik, nincsenek több helyszíni és felhőalapú összetevőit, érdemes figyelembe venni. Első lépésként vegye figyelembe a következő folyamatot:  

| Lépés | Kinek a feladata | Tevékenység | Kapcsolódó hivatkozások |
| --- | --- | --- | --- |
| 1. |Rendszergazda |A Microsoft Azure-előfizetés használatával egy Microsoft-fiókjával vagy szervezeti fiók létrehozása |[Azure Portal](https://portal.azure.com) |
| 2. |Rendszergazda |Hozzon létre, vagy üzembe helyezhetik a BizTalk szolgáltatás. |[BizTalk-szolgáltatás létrehozása](/previous-versions/azure/reference/dn232347(v=azure.100)) |
| 3. |Rendszergazda |Ön vagy vállalata a BizTalk Services üzembe helyezési regisztrálása |[Regisztráció és a BizTalk-szolgáltatások üzembe helyezéséhez a BizTalk Services portálon frissítése](/previous-versions/azure/hh689837(v=azure.100)) |
| 4. |Rendszergazda |Érvényes, ha az alkalmazás egy helyszíni üzletági (LOB) rendszerhez való csatlakozáshoz használja a BizTalk Adapter szolgáltatást, vagy egy üzenetsor vagy témakör cél használja.  Hozzon létre az Azure Service Bus-Namespace. A névtér, a Service Bushoz kapcsolódó kibocsátói név és a Service Bus kiállító kulcsa értékeket adhat a fejlesztő. |[Útmutató: Létrehozása vagy módosítása egy Service Bus szolgáltatás Namespace](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) és [értékek első kiállító neve és kiállító kulcsa](biztalk-issuer-name-issuer-key.md) |
| 5. |Fejlesztői |Az SDK telepítése és a BizTalk-szolgáltatás-projekt létrehozása a Visual Studióban. |[Telepítse az Azure BizTalk Services SDK-val](/previous-versions/azure/hh689760(v=azure.100)) és [gazdag üzenetkezelési végpontok létrehozása az Azure-ban](/previous-versions/azure/hh689766(v=azure.100)) |
| 6. |Fejlesztői |Telepítse a projektet a BizTalk-szolgáltatás az Azure-platformon futó BizTalk-szolgáltatás. |[Üzembe helyezése és a BizTalk Services-projekt frissítése](/previous-versions/azure/hh689881(v=azure.100)) |
| 7. |Rendszergazda |Ha használ EDI vonatkozik.  Hozzáadhat a partnerek és szerződések létrehozása a Microsoft Azure BizTalk Services Portáljára. Amikor egy szerződést hoz létre, adhat hozzá a híd és/vagy a szerződés beállításokat a fejlesztő által létrehozott átalakítások. |[A BizTalk Services portáljának EDI, AS2 és EDIFACT konfigurálása](/previous-versions/azure/hh689853(v=azure.100)) |
| 8. |Rendszergazda |Használatával [REST](/previous-versions/azure/reference/dn232347(v=azure.100)), a BizTalk-szolgáltatás, beleértve a teljesítmény-mérőszámok állapotának monitorozásához. |[BizTalk Services: Irányítópult, figyelés és méret lapok](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Rendszergazda |A Microsoft Azure BizTalk Services portáljának használatával, kezelheti az összetevők, amelyet a BizTalk Services és üzenetek nyomon követése, a híd-fájlok feldolgozása. |[A BizTalk Services portáljának használata](/previous-versions/azure/dn874043(v=azure.100)) |
| 10. |Rendszergazda |Hozzon létre egy biztonsági mentési tervet, készítsen biztonsági másolatot a BizTalk-szolgáltatás. |[Üzletmenet-folytonossági és vészhelyreállítási a BizTalk Services](/previous-versions/azure/dn509557(v=azure.100)) |

## <a name="next-steps"></a>További lépések
[Oktatóanyagok és minták](/previous-versions/azure/hh689895(v=azure.100))

[A projekt létrehozása Visual Studióban](/previous-versions/azure/hh689811(v=azure.100))

[Az Azure BizTalk Services SDK telepítése](/previous-versions/azure/hh689760(v=azure.100))

## <a name="concepts"></a>Alapelvek
[A projekt létrehozása Visual Studióban](/previous-versions/azure/hh689811(v=azure.100))  
[EDI, AS2 és EDIFACT-alapú üzenetkezelés (vállalatközi)](/previous-versions/azure/hh689898(v=azure.100))  

## <a name="other-resources"></a>Egyéb források
[Adja hozzá a forrás, cél és üzenetkezelési végpontok híd](/previous-versions/azure/hh689877(v=azure.100))  
[Ismerje meg, és üzenet térképeket és átalakítások](/previous-versions/azure/hh689905(v=azure.100))  
[A BizTalk Adapter Service (BAS) használatával](/previous-versions/azure/hh689889(v=azure.100))  
[Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)

