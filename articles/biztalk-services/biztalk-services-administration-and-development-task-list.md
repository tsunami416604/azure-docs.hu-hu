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
ms.openlocfilehash: e762141e089b11dd0fb129f3bf758874d4ad4da8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227637"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Felügyelet és a BizTalk Services fejlesztői feladatlista

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Első lépések
Ha a Microsoft Azure BizTalk Services dolgozik, nincsenek több helyszíni és felhőalapú összetevőit, érdemes figyelembe venni. Első lépésként vegye figyelembe a következő folyamatot:  

| Lépés | Kinek a feladata | Tevékenység | Kapcsolódó hivatkozások |
| --- | --- | --- | --- |
| 1. |Rendszergazda |A Microsoft Azure-előfizetés használatával egy Microsoft-fiókjával vagy szervezeti fiók létrehozása |[Azure Portal](https://portal.azure.com) |
| 2. |Rendszergazda |Hozzon létre, vagy üzembe helyezhetik a BizTalk szolgáltatás. |[BizTalk-szolgáltatás létrehozása](https://msdn.microsoft.com/library/azure/dn232347.aspx) |
| 3. |Rendszergazda |Ön vagy vállalata a BizTalk Services üzembe helyezési regisztrálása |[Regisztráció és a BizTalk-szolgáltatások üzembe helyezéséhez a BizTalk Services portálon frissítése](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Rendszergazda |Érvényes, ha az alkalmazás egy helyszíni üzletági (LOB) rendszerhez való csatlakozáshoz használja a BizTalk Adapter szolgáltatást, vagy egy üzenetsor vagy témakör cél használja.  Hozzon létre az Azure Service Bus-Namespace. A névtér, a Service Bushoz kapcsolódó kibocsátói név és a Service Bus kiállító kulcsa értékeket adhat a fejlesztő. |[Hogyan: létrehozása vagy módosítása egy Service Bus szolgáltatás Namespace](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) és [értékek első kiállító neve és kiállító kulcsa](biztalk-issuer-name-issuer-key.md) |
| 5. |Fejlesztői |Az SDK telepítése és a BizTalk-szolgáltatás-projekt létrehozása a Visual Studióban. |[Telepítse az Azure BizTalk Services SDK-val](https://msdn.microsoft.com/library/azure/hh689760.aspx) és [gazdag üzenetkezelési végpontok létrehozása az Azure-ban](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Fejlesztői |Telepítse a projektet a BizTalk-szolgáltatás az Azure-platformon futó BizTalk-szolgáltatás. |[Üzembe helyezése és a BizTalk Services-projekt frissítése](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Rendszergazda |Ha használ EDI vonatkozik.  Hozzáadhat a partnerek és szerződések létrehozása a Microsoft Azure BizTalk Services Portáljára. Amikor egy szerződést hoz létre, adhat hozzá a híd és/vagy a szerződés beállításokat a fejlesztő által létrehozott átalakítások. |[A BizTalk Services portáljának EDI, AS2 és EDIFACT konfigurálása](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Rendszergazda |Használatával [REST](https://msdn.microsoft.com/library/azure/dn232347.aspx), a BizTalk-szolgáltatás, beleértve a teljesítmény-mérőszámok állapotának monitorozásához. |[BizTalk Services: Irányítópult, Figyelés és Méret lapok](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Rendszergazda |A Microsoft Azure BizTalk Services portáljának használatával, kezelheti az összetevők, amelyet a BizTalk Services és üzenetek nyomon követése, a híd-fájlok feldolgozása. |[A BizTalk Services portáljának használata](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Rendszergazda |Hozzon létre egy biztonsági mentési tervet, készítsen biztonsági másolatot a BizTalk-szolgáltatás. |[Üzletmenet-folytonossági és vészhelyreállítási a BizTalk Services](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>További lépések
[Oktatóanyagok és minták](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[A projekt létrehozása a Visual Studióban](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Az Azure BizTalk Services SDK telepítése](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Alapelvek
[A projekt létrehozása a Visual Studióban](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI, AS2 és EDIFACT-alapú üzenetkezelés (vállalatközi)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Egyéb források
[Adja hozzá a forrás, cél és üzenetkezelési végpontok híd](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Ismerje meg, és üzenet térképeket és átalakítások](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[A BizTalk Adapter Service (BAS) használatával](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Az Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)

