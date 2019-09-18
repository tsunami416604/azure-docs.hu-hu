---
title: Névkeresési zónák konfigurálása SMTP szalagcím-bejelentkezéshez az Azure-ban
titlesuffix: Azure Virtual Network
description: Útmutató a névkeresési zónák konfigurálásához az Azure-ban található SMTP szalagcím-ellenőrzések esetében
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
ms.openlocfilehash: 084fdb7f850f3819738a982127fa98efab114197
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059019"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Névkeresési zónák konfigurálása SMTP-szalagcímek vizsgálatához

Ez a cikk azt ismerteti, hogyan használható egy fordított zóna a Azure DNSban, és hogyan hozható létre fordított DNS (PTR) rekord az SMTP-szalagcímek vizsgálatához.

## <a name="symptom"></a>Jelenség

Ha Microsoft Azureban futtat egy SMTP-kiszolgálót, a következő hibaüzenet jelenhet meg a távoli levelezési kiszolgálókról küldött üzenetek küldésekor vagy fogadásakor:

**554: Nincs PTR-rekord**

## <a name="solution"></a>Megoldás

Az Azure-beli virtuális IP-címek esetében a fordított rekordok a Microsoft tulajdonában lévő tartományi zónákban jönnek létre, nem egyéni tartományi zónákban.

A PTR-rekordok Microsoft tulajdonú zónákban való konfigurálásához használja a-ReverseFqdn tulajdonságot a PublicIpAddress-erőforráson. További információkért lásd: [fordított DNS konfigurálása az Azure-ban üzemeltetett szolgáltatásokhoz](../dns/dns-reverse-dns-for-azure-services.md).

A PTR-rekordok konfigurálásakor győződjön meg arról, hogy az előfizetés tulajdonosa az IP-cím és a fordított FQDN. Ha olyan fordított FQDN-t próbál beállítani, amely nem tartozik az előfizetéshez, a következő hibaüzenet jelenik meg:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) A ReverseFqdn az előfizetés alá tartozó nyilvános IP-erőforrások teljes tartománynevére illeszkedik.
    2) A ReverseFqdn az előfizetés alá tartozó nyilvános IP-erőforrások teljes tartománynevét (CName rekordok láncán keresztül) oldja fel.
    3) A szolgáltatás az előfizetés alatt egy statikus nyilvános IP-erőforrás IP-címére (CName és A rekordok láncán keresztül) oldódik fel.

Ha az SMTP-szalagcímet manuálisan módosítja az alapértelmezett fordított FQDN-ként, akkor a távoli levelezési kiszolgáló továbbra is meghiúsulhat, mert várható, hogy az SMTP-szalagcím gazdagépe megfelel a tartomány MX-rekordjának.
