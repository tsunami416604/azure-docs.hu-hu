---
title: Névkeresési zónák egy SMTP szalagcím-ellenőrzésre konfigurálása az Azure-ban |} A Microsoft Docs
description: Névkeresési zónák egy SMTP szalagcím-ellenőrzésre konfigurálása az Azure-ban
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
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 815e3c711850eab11aef63e04a1c512c4510a910
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684285"
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Névkeresési zónák egy SMTP szalagcím-ellenőrzésre konfigurálása

Ez a cikk bemutatja, hogyan használhatja az Azure DNS-névkeresési zóna, és hozzon létre egy rekordot a fordított DNS (PTR) SMTP szalagcím ellenőrzése. 

## <a name="symptom"></a>Jelenség

Ha egy SMTP-kiszolgálót a Microsoft Azure-ban, a következő hibaüzenet jelenhet mikor küldjön vagy üzenetet fogadó távoli levelezési kiszolgálóján:

**554: PTR típusú rekord** 

## <a name="solution"></a>Megoldás

Virtuális IP-cím az Azure-ban a Microsoft tulajdonában lévő tartományi zónák, nem az egyéni tartomány zónák a fordított rekordok jönnek létre.

A Microsoft tulajdonában lévő zónák PTR-rekordok konfigurálásához használja a fordított teljes tartománynév - tulajdonság a PublicIpAddress erőforráson. További információkért lásd: [– címfeloldási DNS konfigurálása az Azure-ban üzemeltetett szolgáltatások](../dns/dns-reverse-dns-for-azure-services.md). 

A PTR-rekordok konfigurálásakor győződjön meg arról, hogy az IP-cím és a fordított teljes Tartománynevet az előfizetés tulajdonosa. Ha fordított teljes tartománynév, amely nem tartozik az előfizetés beállításához, a következő hibaüzenet jelenhet meg:

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) Fordított teljes tartománynév egyezik a az előfizetéshez tartozó nyilvános IP-cím erőforrás teljes tartományneve 
    2) Fordított teljes tartománynév feloldása egy olyan teljes tartománynevét (a CName rekordok lánc) bármely nyilvános IP-cím erőforráshoz az előfizetés; 
    3) Az ip-címet (CName és A rekordok lánc) az előfizetéshez tartozó statikus nyilvános IP-cím erőforrás legyen hozzárendelve. 

Ha manuálisan módosítja az alapértelmezett megfelelően az SMTP szalagcím fordított teljes tartománynév, a távoli levelezési kiszolgáló továbbra is sikertelen, mert a, előfordulhat, hogy várhatóan az MX-rekord a tartomány megfelelően hostitel SMTP szalagcím.