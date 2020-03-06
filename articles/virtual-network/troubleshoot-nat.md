---
title: Az Azure Virtual Network NAT kapcsolódási problémáinak elhárítása
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Virtual Network NAT problémáinak elhárítása.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302984"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Az Azure Virtual Network NAT kapcsolódási problémáinak elhárítása

Ez a cikk segítséget nyújt a rendszergazdáknak a kapcsolódási problémák diagnosztizálásában és megoldásában Virtual Network NAT használatakor.

>[!NOTE] 
>Virtual Network NAT jelenleg nyilvános előzetes verzióként érhető el. Jelenleg csak korlátozott számú [régióban](nat-overview.md#region-availability)érhető el. Ez az előzetes verzió szolgáltatási szintű szerződés nélkül érhető el, ezért nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problémák

- [SNAT-kimerültség](#snat-exhaustion).
- [Az ICMP-pingelés sikertelen](#icmp-ping-is-failing).

A problémák megoldásához kövesse az alábbi szakasz lépéseit.

## <a name="resolution"></a>Megoldás:

### <a name="snat-exhaustion"></a>SNAT-kimerülés

Egyetlen [NAT Gateway-erőforrás](nat-gateway-resource.md) 64 000 akár 1 000 000 egyidejű folyamatra is képes.  Minden IP-cím 64 000 SNAT-portot biztosít a rendelkezésre álló leltárhoz. A NAT-átjáró erőforrásain legfeljebb 16 IP-címet használhat.  A SNAT mechanizmus részletes ismertetését [itt](nat-gateway-resource.md#source-network-address-translation) találja.

#### <a name="steps"></a>Lépések:

1. Vizsgálja meg, hogy az alkalmazás hogyan hozza létre a kimenő kapcsolatokat (például a kód felülvizsgálatát vagy a csomagok rögzítését). 
2. Állapítsa meg, hogy a tevékenység várható viselkedés-e, vagy hogy az alkalmazás nem működik-e.  Az eredmények alátámasztására a Azure Monitor [mérőszámait](nat-metrics.md) használhatja.
3. Értékelje ki, hogy a megfelelő mintákat követi-e.
4. Annak kiértékelése, hogy a SNAT-portok kimerülését csökkenteni kell-e a NAT-átjáró erőforrásához rendelt további IP-címekkel.

#### <a name="design-pattern"></a>Tervezési minta:

Mindig használja ki a kapcsolatok újrafelhasználását és a kapcsolatok készletezését, amikor csak lehetséges.  Ez a minta elkerüli az erőforrások kimerülésével kapcsolatos problémákat, és kiszámítható működést eredményezhet. A mintákhoz tartozó primitívek számos fejlesztői könyvtárban és keretrendszerben találhatók.
- A hosszú ideig futó műveletek esetében érdemes [aszinkron lekérdezési mintákat](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) felvenni a kapcsolatok erőforrásainak más műveletekhez való felszabadítására.
- A hosszú élettartamú folyamatokban (például az újrafelhasznált TCP-kapcsolatok esetében) a TCP-Keepalives vagy az Keepalives kell használnia, hogy elkerülje a köztes rendszerek időtúllépését.
- A kecses [újrapróbálkozási mintákat](https://docs.microsoft.com/azure/architecture/patterns/retry) az átmeneti hibák vagy a meghibásodások helyreállítása során el kell kerülni az agresszív újrapróbálkozások/törések elkerülése érdekében.
Egy új TCP-kapcsolat létrehozása minden HTTP-művelethez (más néven "Atomic connections") egy anti-minta.  Az Atomic Connections szolgáltatás megakadályozza, hogy az alkalmazás a jól méretezhető és a hulladék erőforrásokat is kihasználja.  Mindig több műveletet kell ugyanabba a hálózatba csatlakoztatni.  Az alkalmazás a tranzakciós sebességet és az erőforrások költségét is kihasználja.  Ha az alkalmazás Transport Layer encryption (például TLS) protokollt használ, az új kapcsolatok feldolgozásának jelentős díja van.  Tekintse át az [Azure Cloud design-mintákat](https://docs.microsoft.com/azure/architecture/patterns/) az ajánlott eljárások további mintáinak megtekintéséhez.

#### <a name="mitigations"></a>Kezelési lehetőségek

A kimenő kapcsolatok a következőképpen méretezhetők:

| Forgatókönyv | Kezelés |
|---|---|
| A SNAT-portok és a SNAT-portok kimerülése a magas kihasználtságú időszakok során tapasztalható. | Állapítsa meg, hogy adhat-e további nyilvános IP-cím erőforrásokat vagy nyilvános IP-előtag-erőforrásokat. Ez a Hozzáadás legfeljebb 16 IP-címet tesz lehetővé a NAT-átjáró számára. Ez a Hozzáadás további leltárt nyújt a rendelkezésre álló SNAT-portok (64 000/IP-címek) számára, és lehetővé teszi a forgatókönyv további skálázását.|
| Már 16 IP-címet adott meg, és továbbra is SNAT a portok kimerülése. | Terjessze az alkalmazási környezetet több alhálózatra, és adjon meg egy NAT Gateway-erőforrást az egyes alhálózatokhoz. |

>[!NOTE]
>Fontos megérteni, hogy miért történik a SNAT kimerültség. Győződjön meg arról, hogy a megfelelő mintákat használja a méretezhető és megbízható forgatókönyvekhez.  Ha további SNAT-portokat ad hozzá egy forgatókönyvhöz anélkül, hogy meg kellene ismernie a kereslet okát, az utolsó megoldásnak kell lennie. Ha nem érti, hogy a forgatókönyv miért alkalmazza a nyomást a SNAT, további SNAT-portok hozzáadásával a leltárhoz további IP-címek hozzáadásával a rendszer csak az alkalmazás skálázási hibáját fogja késleltetni.  Előfordulhat, hogy más eredménytelenség és anti-mintázatok maszkolására is van lehetőség.

### <a name="icmp-ping-is-failing"></a>Az ICMP-pingelés sikertelen

[Virtual Network NAT](nat-overview.md) támogatja az IPv4 UDP-és TCP-protokollokat. Az ICMP nem támogatott, és a várt sikertelen lesz.  Ehelyett használjon TCP-kapcsolati teszteket (például "TCP ping") és az UDP-specifikus alkalmazás-réteg teszteket a végpontok közötti kapcsolat ellenőrzéséhez.

A következő táblázat kiindulási pontot használhat, amellyel a tesztek elindítására szolgáló eszközök használhatók.

| Operációs rendszer | Általános TCP-kapcsolatok tesztelése | TCP-alkalmazás rétegének tesztelése | UDP |
|---|---|---|---|
| Linux | NC (általános kapcsolatok tesztelése) | Curl (TCP-alkalmazás rétegének tesztelése) | alkalmazás-specifikus |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [-meghívás – Webkérés](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | alkalmazás-specifikus |

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [Virtual Network NAT](nat-overview.md) -ról
- Tudnivalók a [NAT-átjáró erőforrásáról](nat-gateway-resource.md)
- Tudnivalók a [NAT-átjáró erőforrásaira vonatkozó mérőszámokról és riasztásokról](nat-metrics.md).
