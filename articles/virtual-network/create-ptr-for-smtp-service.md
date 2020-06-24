---
title: Névkeresési zónák konfigurálása SMTP-szalagcímek vizsgálatához
titlesuffix: Azure Virtual Network
description: Útmutató a névkeresési zónák konfigurálásához az Azure-ban található SMTP szalagcím-ellenőrzések esetében
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 0264ad93eb53e27d1dc76f2b20ad175a6ee2f8de
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84688687"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Névkeresési zónák konfigurálása SMTP-szalagcímek vizsgálatához

Ez a cikk azt ismerteti, hogyan használható egy fordított zóna a Azure DNSban, és hogyan hozható létre fordított DNS (PTR) rekord az SMTP-szalagcímek vizsgálatához.

## <a name="symptom"></a>Hibajelenség

Ha Microsoft Azureban futtat egy SMTP-kiszolgálót, a következő hibaüzenet jelenhet meg a távoli levelezési kiszolgálókról küldött üzenetek küldésekor vagy fogadásakor:

**554: nincs PTR-rekord**

## <a name="solution"></a>Megoldás

Az Azure-beli virtuális IP-címek esetében a fordított rekordok a Microsoft tulajdonában lévő tartományi zónákban jönnek létre, nem egyéni tartományi zónákban.

A PTR-rekordok Microsoft tulajdonú zónákban való konfigurálásához használja a-ReverseFqdn tulajdonságot a PublicIpAddress-erőforráson. További információkért lásd: [fordított DNS konfigurálása az Azure-ban üzemeltetett szolgáltatásokhoz](../dns/dns-reverse-dns-for-azure-services.md).

A PTR-rekordok konfigurálásakor győződjön meg arról, hogy az előfizetés tulajdonosa az IP-cím és a fordított FQDN. Ha olyan fordított FQDN-t próbál beállítani, amely nem tartozik az előfizetéshez, a következő hibaüzenet jelenik meg:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) A ReverseFqdn az előfizetés alá tartozó nyilvános IP-erőforrások teljes tartománynevére illeszkedik.
    2) A ReverseFqdn az előfizetés alá tartozó nyilvános IP-erőforrások teljes tartománynevét (CName rekordok láncán keresztül) oldja fel.
    3) A szolgáltatás az előfizetés alatt egy statikus nyilvános IP-erőforrás IP-címére (CName és A rekordok láncán keresztül) oldódik fel.

Ha az SMTP-szalagcímet manuálisan módosítja az alapértelmezett fordított FQDN-ként, akkor a távoli levelezési kiszolgáló továbbra is meghiúsulhat, mert várható, hogy az SMTP-szalagcím gazdagépe megfelel a tartomány MX-rekordjának.
