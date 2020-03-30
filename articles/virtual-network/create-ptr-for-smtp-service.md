---
title: Névkeresési zónák konfigurálása SMTP szalagcím-ellenőrzéshez
titlesuffix: Azure Virtual Network
description: A névkeresési zónák konfigurálása Az SMTP szalagcímellenőrzéséhez az Azure-ban
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6c37ba61005c9936e6421d06369d1f52b93ac264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201696"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Névkeresési zónák konfigurálása SMTP szalagcím-ellenőrzéshez

Ez a cikk ismerteti, hogyan kell használni a fordított zóna az Azure DNS-ben, és hozzon létre egy fordított DNS (PTR) rekordot SMTP banner check.

## <a name="symptom"></a>Hibajelenség

Ha smtp-kiszolgálót üzemeltet a Microsoft Azure-ban, a következő hibaüzenet jelenhet meg, amikor távoli levelezési kiszolgálókról küld vagy fogad üzenetet:

**554: Nincs PTR rekord**

## <a name="solution"></a>Megoldás

Az Azure-beli virtuális IP-cím esetén a fordított rekordok a Microsoft tulajdonában lévő tartományzónákban jönnek létre, nem pedig egyéni tartományi zónákban.

A PTR-rekordok Microsoft tulajdonában lévő zónákban történő konfigurálásához használja a PublicIpAddress erőforrás -ReverseFqdn tulajdonságát. További információt a [Fordított DNS konfigurálása az Azure-ban üzemeltetett szolgáltatásokhoz című témakörben talál.](../dns/dns-reverse-dns-for-azure-services.md)

A PTR-rekordok konfigurálásakor győződjön meg arról, hogy az IP-cím és a fordított teljes tartománynév az előfizetés tulajdonában van. Ha olyan fordított teljes tartománynát próbál beállítani, amely nem tartozik az előfizetéshez, a következő hibaüzenet jelenik meg:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) A ReverseFqdn az előfizetés alatt lévő nyilvános IP-erőforrások fqdn-jének felel meg;
    2) A ReverseFqdn az előfizetés alatt lévő bármely nyilvános ip-erőforrás fqdn-jével (a CName records chain-en keresztül) oldja fel;
    3) Egy statikus nyilvános IP-erőforrás IP-címére (CName és A records chain) az előfizetés alatt oldódik fel.

Ha manuálisan módosítja az SMTP-szalagcímet úgy, hogy megfeleljen az alapértelmezett fordított teljes tartománynévnek, a távoli levelezési kiszolgáló továbbra is sikertelen maradhat, mert előfordulhat, hogy az SMTP szalagcímgazda várhatóan megegyezik a tartomány MX rekordjával.
