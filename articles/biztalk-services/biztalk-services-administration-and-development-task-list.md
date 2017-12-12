---
title: "Felügyeleti és fejlesztői tevékenységlista BizTalk szolgáltatásban |} Microsoft Docs"
description: "Tervezési és a feladat támogatási Azure BizTalk szolgáltatások telepítéséhez."
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 9edd7261ca62f505ffb4854e3132fae916768f67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Felügyeleti és fejlesztői feladatlista BizTalk szolgáltatások

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Első lépések
Microsoft Azure BizTalk szolgáltatás használata, ha nincsenek több helyszíni és felhőalapú összetevők kell figyelembe venni. A kezdéshez, vegye figyelembe a következő folyamatot:  

| Lépés | Kinek a feladata | Tevékenység | Kapcsolódó hivatkozások |
| --- | --- | --- | --- |
| 1. |Rendszergazda |A Microsoft Azure-előfizetés a Microsoft-fiókkal vagy szervezeti fiók létrehozása |[Azure Portal](https://portal.azure.com) |
| 2. |Rendszergazda |Hozzon létre, vagy a BizTalk szolgáltatás kiépítése. |[BizTalk szolgáltatás létrehozása](https://msdn.microsoft.com/library/azure/dn232347.aspx) |
| 3. |Rendszergazda |Regisztrálja az Ön vagy vállalata BizTalk szolgáltatások telepítése |[Regisztráció és a BizTalk szolgáltatás központi telepítése a BizTalk szolgáltatások portál frissítése](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Rendszergazda |Érvényes, ha az alkalmazás BizTalk Adapter szolgáltatást használ egy helyszíni üzletági (LOB) rendszerhez való csatlakozás vagy üzenetsor vagy témakör cél használja.  Az Azure Service Bus Namespace létrehozása. A névtér, a Service Bus kibocsátó neve és a Service Bus Issuer Key értékek adnia a fejlesztőnek. |[Hogyan: létrehozhat vagy módosíthat egy Service Bus szolgáltatás Namespace](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) és [kibocsátó nevének beolvasása és Issuer Key értékek](biztalk-issuer-name-issuer-key.md) |
| 5. |Fejlesztői |Az SDK telepítése, és a BizTalk szolgáltatás projekt létrehozása a Visual Studióban. |[Az Azure BizTalk szolgáltatások SDK telepítése](https://msdn.microsoft.com/library/azure/hh689760.aspx) és [gazdag üzenetküldési végpontok létrehozása az Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Fejlesztői |A projekt a BizTalk szolgáltatás Azure-platformon futó BizTalk szolgáltatás üzembe helyezése. |[Központi telepítése és a BizTalk szolgáltatások projekt frissítése](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Rendszergazda |EDI használata vonatkozik.  Adja hozzá a partnerek, és a Microsoft Azure BizTalk szolgáltatások portálon hozza létre az egyezményeket. Amikor egy szerződést hoz létre, a híd és/vagy hozta létre a szerződés beállításokat a fejlesztő átalakítások is hozzáadhat. |[BizTalk szolgáltatások portálja AS2, EDIFACT és a EDI konfigurálása](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Rendszergazda |Használatával [REST](https://msdn.microsoft.com/library/azure/dn232347.aspx), a BizTalk szolgáltatás, így az teljesítménymutatók állapotának figyelésére. |[BizTalk Services: Irányítópult, Figyelés és Méret lapok](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Rendszergazda |A Microsoft Azure BizTalk szolgáltatások portál használatával, és használják a BizTalk szolgáltatások üzenetek nyomon követése, a híd fájlok feldolgozza az összetevők kezelése. |[BizTalk szolgáltatások portál használatával](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Rendszergazda |A biztonsági mentési tervet, készítsen biztonsági másolatot a BizTalk szolgáltatás létrehozása. |[Az üzletmenet folytonossága és vészhelyreállítás a BizTalk szolgáltatások](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>Következő lépések
[Oktatóanyagok és minták](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[A projekt létrehozása a Visual Studióban](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Az Azure BizTalk szolgáltatások SDK telepítése](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Alapelvek
[A projekt létrehozása a Visual Studióban](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI AS2 és EDIFACT Messaging (a vállalatok)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Egyéb források
[Adja hozzá a forrás, a cél és a végpontok üzenetküldési híd](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Ismerje meg, és üzenet leképezések és átalakítások létrehozása](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[A BizTalk Adapter szolgáltatással (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Az Azure BizTalk szolgáltatások](http://go.microsoft.com/fwlink/p/?LinkID=303664)

