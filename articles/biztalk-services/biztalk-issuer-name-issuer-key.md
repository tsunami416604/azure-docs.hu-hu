---
title: Kiállító neve és kiállító kulcsa a BizTalk Services |} A Microsoft Docs
description: Ismerje meg, hogyan kérheti le a kiállító neve és kiállító kulcsa vagy a Service Bus, vagy az Access Control (ACS) a BizTalk Services számára. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 78796b5dc62cb573f149c24d90205d26fb139cf7
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628647"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Kiállító neve és kiállító kulcsa

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Az Azure BizTalk Services használ, a Service Bushoz kapcsolódó kibocsátói név és kiállító kulcsa, és a hozzáférés-vezérlési kiállító neve és kiállító kulcsa. Konkrétan:

| Tevékenység | Melyik kiállító neve és kiállító kulcsa |
| --- | --- |
| Az alkalmazás a Visual Studióból üzembe helyezése |Hozzáférés-vezérlési kiállító neve és kiállító kulcsa |
| Az Azure BizTalk Services portáljának konfigurálása |Hozzáférés-vezérlési kiállító neve és kiállító kulcsa |
| A BizTalk Adapter szolgáltatás a Visual Studióban való létrehozását ismertető LOB-továbbítók |Service Bus kiállító neve és kiállító kulcsa |

Ez a témakör a kiállító neve és kiállító kulcsa lekérdezésének lépéseit sorolja fel. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Hozzáférés-vezérlési kiállító neve és kiállító kulcsa
A hozzáférés-vezérlési kiállító neve és kiállító kulcsa használják a következőket:

* Az Azure BizTalk-szolgáltatás-alkalmazás létrehozása a Visual Studióban: sikeresen üzembe helyezhetik a BizTalk-szolgáltatás a Visual Studio Azure, a hozzáférés-vezérlési kiállító neve és kiállító kulcsa adja meg. 
* Az Azure BizTalk Services portáljának: Amikor BizTalk-szolgáltatás létrehozása és a BizTalk Services portáljának megnyitásához, a hozzáférés-vezérlési kiállító neve és kiállító kulcsa automatikusan regisztrálva vannak a központi telepítések, hozzáférés-vezérlés ugyanazon értékekkel.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Hozzáférés-vezérlési kiállító neve és kiállító kulcsa

ACS használnak a hitelesítéshez, és a kiállító neve és kiállító kulcsa értékének lekéréséhez, hogy a lépések a következők:

1. Telepítse a [Azure Powershell-parancsmagok](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Adja hozzá az Azure-fiókjával: `Add-AzureAccount`
3. Az előfizetés nevét adja vissza: `get-azuresubscription`
4. Válassza ki az előfizetését: `select-azuresubscription <name of your subscription>` 
5. Hozzon létre egy új névteret: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Példa:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Az új ACS-névtér (Ez több percig is eltarthat) létrehozását követően a kiállító neve és kiállító kulcsa értékek szerepelnek a kapcsolati karakterlánc: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Összefoglalásképpen:  
Kiállító neve = SharedSecretIssuer  
Kiállító kulcsa = SharedSecretKey

A több a [New-AzureSBNamespace](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace) parancsmagot. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Service Bus kiállító neve és kiállító kulcsa
A BizTalk Adapter szolgáltatás által használt Service Bus kiállító neve és kiállító kulcsa. A BizTalk Services projektre a Visual Studióban a BizTalk Adapter szolgáltatás használhatja egy helyszíni üzletági (LOB) rendszerhez való csatlakozáshoz. Szeretne csatlakozni, hozzon létre a LOB-továbbítót, és adja meg a LOB-rendszer adatait. Ha így tesz, akkor is adja meg a Service Bushoz kapcsolódó kibocsátói név és kiállító kulcsa.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>A Service Bushoz kapcsolódó kibocsátói név és kiállító kulcsa lekéréséhez
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg **a Service Bus**, és válassza ki a névteret. 
3. Nyissa meg a **megosztott elérési házirendek** tulajdonságait, válassza ki a szabályzatot, és megtekintheti a **kapcsolati karakterlánc** tartozó név és kulcs értékeit.  

## <a name="next"></a>Következő lépés
További Azure BizTalk Services témakörök:

* [Az Azure BizTalk Services SDK telepítése](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Az oktatóanyagok: Az Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Az Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Lásd még:
* [How to: ACS felügyeleti szolgáltatás segítségével Szolgáltatásidentitások konfigurálása](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [A BizTalk Services: Fejlesztői, alapszintű, Standard és prémium kiadások diagramja](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [A BizTalk Services: kiépítés](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Kiépítési állapot diagramja](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Irányítópult, Figyelés és Méret lapok](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Biztonsági mentés és visszaállítás](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Szabályozás](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

