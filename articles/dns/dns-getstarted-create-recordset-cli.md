---
title: "Rekordhalmaz és rekordok létrehozása egy DNS-zóna számára a parancssori felület használatával| Microsoft Docs"
description: "Állomásrekordok létrehozása az Azure DNS számára. Rekordhalmazok és rekordok beállítása a parancssori felület használatával"
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 40accee35eca32eefd4afc2315c70d51e0edcdcd


---
# <a name="create-dns-record-sets-and-records-by-using-cli"></a>DNS-rekordhalmazok és -rekordok létrehozása a parancssori felület használatával
> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)
> 
> 

Ez a cikk végigvezeti a rekordok és a rekordhalmazok parancssori felület használatával történő létrehozásának folyamatán. Miután létrehozta a DNS-zónát, fel kell vennie a tartomány DNS-rekordjait. Ehhez tisztában kell lennie a DNS-rekordok és a rekordhalmazok jelentésével.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>Rekordhalmaz és rekord létrehozása
Ebben a szakaszban egy rekordhalmaz és néhány rekord létrehozását mutatjuk be. A példában egy olyan rekordhalmazt hozunk létre, amely a „www” relatív névvel rendelkezik a „contoso.com” DNS-zónában. A rekordok teljesen minősített neve „www.contoso.com”. A rekordtípus „A”, az élettartam (TTL) pedig 60 másodperc. A lépés befejezése után egy üres rekordhalmaz fog a rendelkezésére állni.

Ha a zóna (ebben az esetben a „contoso.com”) legfelső pontján szeretne létrehozni egy rekordot, használja az "@", rekordnevet (az idézőjelekkel együtt). Ez a DNS-rendszerben általános egyezmény.

### <a name="1-create-a-record-set"></a>1. Rekordhalmaz létrehozása
Rekordhalmaz létrehozásához használja az `azure network dns record-set create` parancsot. Adja meg az erőforráscsoportot, a zóna nevét, a rekordhalmaz relatív nevét, a rekordtípust, valamint az élettartamot (TTL). Ha a `--ttl` paraméter nincs megadva, az alapértelmezett érték négy (másodpercben). A lépés befejezése után egy üres „www” rekordhalmaz fog a rendelkezésére állni.

*Használat: network dns record-set create <erőforráscsoport> <dns-zóna-neve><name> <type> <ttl>*

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

### <a name="2-add-records"></a>2. Rekordok hozzáadása
Az újonnan létrehozott „www” rekordhalmaz használatához rekordokat kell hozzáadni. A rekordokat az `azure network dns record-set add-record` parancs használatával adhatja hozzá a rekordhalmazokhoz.

A rekordok rekordhalmazhoz adásának paraméterei a rekordhalmaz típusától függően eltérnek. Például az „A” típusú rekordhalmazok használata esetén csak az `-a <IPv4 address>` paraméterrel lehet megadni a paramétereket.

A „www” rekordhalmazhoz a következő paranccsal adhat hozzá *A* típusú IPv4-rekordokat:

*Használat: network dns record-set add-record <erőforráscsoport> <dns-zóna-neve> <rekordhalmaz-neve> <type>*

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## <a name="additional-record-type-examples"></a>További példák a rekordtípusokra
Az alábbi példák azt mutatják be, hogy hogyan hozhatók létre a különböző rekordtípusú rekordhalmazok. Minden rekordhalmaz egyetlen rekordot tartalmaz.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="next-steps"></a>Következő lépések
A rekordhalmaz és a rekordok kezelésével kapcsolatban tekintse meg [A DNS-rekordok és -rekordhalmazok kezelése a parancssori felület használatával](dns-operations-recordsets-portal.md) című cikket.

Az Azure DNS szolgáltatással kapcsolatos további információért tekintse meg [Mi az Azure DNS?](dns-overview.md) című cikket.




<!--HONumber=Nov16_HO2-->


