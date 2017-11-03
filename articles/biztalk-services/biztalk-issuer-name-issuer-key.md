---
title: "Kiállító neve és a BizTalk szolgáltatások Issuer Key |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet lekérni a kibocsátó neve és Issuer Key Service Bus vagy Access Control (ACS) a BizTalk szolgáltatások. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: b9fd985c23558596408e78eadae00dd0f95c4214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Kiállító neve és kiállító kulcsa

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk-szolgáltatásokat használja, a Service Bus kibocsátó neve és Issuer Key, és a hozzáférés-vezérlő Kibocsátónév és Issuer Key. Konkrétan:

| Tevékenység | Mely kibocsátó neve és Issuer Key |
| --- | --- |
| A Visual Studio az alkalmazás telepítéséhez |Hozzáférés-vezérlő Kibocsátónév és Issuer Key |
| Az Azure BizTalk szolgáltatások portál konfigurálása |Hozzáférés-vezérlő Kibocsátónév és Issuer Key |
| LOB-továbbítók létrehozása a Visual Studio BizTalk Adapter szolgáltatásokkal |Service Bus kibocsátó neve és Issuer Key |

Ez a témakör a kibocsátó neve és Issuer Key beolvasandó lépéseit sorolja fel. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Hozzáférés-vezérlő Kibocsátónév és Issuer Key
A hozzáférés-vezérlő Kibocsátónév és Issuer Key használnak a következő:

* Az Azure BizTalk szolgáltatás alkalmazás létrehozása a Visual Studio: sikeres telepítéséhez a BizTalk szolgáltatás alkalmazást a Visual Studio Azure Access Control kibocsátó neve és Issuer Key adja meg. 
* Az Azure BizTalk szolgáltatások portálon: BizTalk szolgáltatás létrehozása, és nyissa meg a BizTalk szolgáltatások portált, a hozzáférés-vezérlő Kibocsátónév és Issuer Key automatikusan regisztrálva vannak a hozzáférés-vezérlés értékeitől telepítések esetén.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>A hozzáférés-vezérlő kibocsátó neve és a kiállító kulcs beszerzése

ACS használata a hitelesítéshez, és a kiállító nevével és Issuer Key értékek, az általános lépések az alábbiak:

1. Telepítse a [Azure Powershell-parancsmagok](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Adja hozzá az Azure-fiókjával:`Add-AzureAccount`
3. Az előfizetés nevét adja vissza:`get-azuresubscription`
4. Jelölje ki az előfizetését:`select-azuresubscription <name of your subscription>` 
5. Új névtér létrehozása:`new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Példa:`new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Létrehozásakor az új ACS-névtér (amely több percet is igénybe vehet), a kiállító nevével és Issuer Key értékek listáját a kapcsolati karakterlánc: 

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
Kiállító kulcsát = SharedSecretKey

A több a [New-AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx) parancsmag. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Service Bus kibocsátó neve és Issuer Key
Service Bus kibocsátó neve és Issuer Key BizTalk Adapter szolgáltatások használják. BizTalk szolgáltatások projektre a Visual Studio, a segítségével a BizTalk Adapter szolgáltatások egy helyszíni üzletági (LOB) rendszerhez való csatlakozás. Szeretne csatlakozni, a LOB-továbbítási létrehozása, és adja meg a LOB-rendszer részleteit. Ennek során azt is adja meg a Service Bus kibocsátó neve és Issuer Key.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>A Service Bus kibocsátó neve és Issuer Key beolvasása
1. Jelentkezzen be a [klasszikus Azure portálra](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. A bal oldali navigációs ablakból válassza **Service Bus**.
3. Válassza ki a névteret. A tálcán válassza **kapcsolatadatok**. Ez megjeleníti a **alapértelmezett kibocsátó** (kibocsátó neve) és **alapértelmezett kulcs** (Issuer Key). Az értékekre másolhatók.  

Összefoglalásképpen:  
Kiállító neve alapértelmezett kibocsátó =  
Kiállító kulcsát alapértelmezett kulcs =

## <a name="next"></a>Következő lépés
További Azure BizTalk szolgáltatások témakörök:

* [Az Azure BizTalk szolgáltatások SDK telepítése](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Oktatóanyag: Azure BizTalk szolgáltatások](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Az Azure BizTalk szolgáltatások](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Lásd még:
* [Hogyan: ACS felügyeleti szolgáltatás segítségével a szolgáltatás-identitások konfigurálása](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk szolgáltatások: Fejlesztői, Basic, Standard és prémium kiadás diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk szolgáltatások: Kiépítés használata Azure klasszikus portál](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: Kiépítési állapot diagramja](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Irányítópult, Figyelés és Méret lapok](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Biztonsági mentés és visszaállítás](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Szabályozás](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

