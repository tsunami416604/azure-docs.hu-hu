---
title: Az SMTP szalagcím, ellenőrizze a névkeresési zónák konfigurálása az Azure-ban |} Microsoft Docs
description: Az SMTP szalagcím, ellenőrizze a névkeresési zónák konfigurálása az Azure-ban
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/06/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 1e95b00ea08105238a860265e46275c24ed7bfbd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
ms.locfileid: "29151872"
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Az SMTP szalagcím, ellenőrizze a névkeresési zónák konfigurálása

Ez a cikk ismerteti, hogyan használja az Azure DNS-névkeresési zóna, és hozzon létre egy névkeresési DNS (PTR) rekordot a SMTP szalagcím ellenőrizze. 

## <a name="symptom"></a>Jelenség

Ha a Microsoft Azure-ban SMTP-kiszolgáló működteti, a következő hibaüzenet jelenhet mikor küldjön vagy hibaüzenetet küld távoli levelezési kiszolgálóján:

**554: PTR típusú rekord** 

## <a name="solution"></a>Megoldás

Egy virtuális IP-cím az Azure-ban a fordított rögzíti a Microsoft tulajdonában lévő tartományi zónák, nem az egyéni tartomány zónák jönnek létre.

A Microsoft tulajdonában lévő zónák PTR-rekordok konfigurálásához használja a PublicIpAddress erőforrás - ReverseFqdn tulajdonságát. További információkért lásd: [konfigurálása az Azure-ban tárolt szolgáltatások fordított irányú DNS](../dns/dns-reverse-dns-for-azure-services.md). 

A PTR rekordok beállításakor győződjön meg arról, hogy az IP-cím és a fordított teljes Tartománynevet az előfizetés tulajdonosa. Ha egy fordított teljes tartománynév, amely nem tartozik az előfizetés beállításához, a következő hibaüzenet jelenhet meg:

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) ReverseFqdn megegyezik az előfizetésben; nyilvános IP-cím erőforrás teljes tartománynevét 
    2) ReverseFqdn oldja fel a teljes tartománynevét (a CName-rekordok lánc) bármely nyilvános IP-cím erőforrás a ahhoz az előfizetéshez tartozik; 
    3) Az IP-cím (CName és az A rekordok lánc) az előfizetéshez tartozó statikus nyilvános IP-cím erőforrás legyen hozzárendelve. 

Ha manuálisan módosítja a SMTP szalagcím alapértelmezett egyező fordított teljes tartománynév, a távoli levelezési kiszolgáló továbbra is sikertelen, mert ettől a az SMTP-szalagcím állomás az MX-rekord a tartomány megfelelően.