---
title: Mi az Azure-beli hálózati hozzáférés-vezérlési lista?
description: További tudnivalók az Azure-ban a hozzáférés-vezérlési listák
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 83d66c84-8f6b-4388-8767-cd2de3e72d76
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 3a7155380a51273d376226c6be7a004f386181ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035263"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Mit jelent a végponti hozzáférés-vezérlési lista?

> [!IMPORTANT]
> Az Azure két különböző rendelkezik [üzembe helyezési modellek](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) számára az erőforrások létrehozásához és használatához: Resource Manager és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a Resource Manager üzemi modell. 

Végponti hozzáférés-vezérlési lista (ACL) egy biztonsági fejlesztések az Azure-alapú érhető el. Egy ACL lehetővé teszi a szelektív engedélyezéséhez, vagy megtagadják a forgalmat a virtuálisgép-végpont. A csomag szűrési képességek egy további biztonsági réteget nyújt. Végpontok csak hálózati hozzáférés-vezérlési is megadhat. Egy virtuális hálózat vagy virtuális hálózaton található megadott alhálózat ACL nem adható meg. Javasoljuk, hogy a hálózati biztonsági csoportok (NSG-k) használata helyett az ACL-EK, amikor csak lehetséges. Az NSG-k használatakor a végponti hozzáférés-vezérlési lista helyén, és érvényüket vesztik. NSG-kkel kapcsolatos további információkért lásd: [hálózati biztonsági csoportok áttekintése](security-overview.md)

ACL-ek konfigurálhatók a PowerShell vagy az Azure portal használatával. A hálózati hozzáférés-szabályozási beállítása PowerShell-lel: [kezelése hozzáférés-vezérlési listák a PowerShell-lel végpontok](virtual-networks-acl-powershell.md). A hálózati hozzáférés-szabályozási beállítása az Azure portal használatával: [beállítása a virtuális gép végpontjainak](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Hálózati hozzáférés-vezérlési listák használatával, akkor a következőket teheti:

* Szelektív vagy megtagadhatja a bejövő forgalmat a távoli alhálózat IPv4-címtartomány egy virtuális gép bemeneti végpontjára alapján.
* Blacklist IP-címek
* Virtuális gép végpontonként több szabály létrehozása
* Egy adott virtuális gép végpontja (legalacsonyabb és legmagasabb) használata a szabály rendezése annak biztosítása érdekében a megfelelő szabálykészletet alkalmazandók
* Adjon meg egy ACL-t egy adott távoli alhálózat IPv4-címet.

Tekintse meg a [Azure korlátairól](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) hozzáférés-szabályozási korlátok ismertető cikket.

## <a name="how-acls-work"></a>Hogyan működik a hozzáférés-vezérlési listák
ACL-szabályok listáját tartalmazó objektum. Hozzon létre egy ACL-t, és alkalmazza azt a virtuális gép végpontjának, hálózaticsomag-szűrés történik a virtuális gép gazdagép-csomóponton. Ez azt jelenti, hogy a távoli IP-címekről érkező forgalom a virtuális gép megfelelő ACL-szabályok helyett a gazdacsomópont szerint van szűrve. Ez megakadályozza, hogy a virtuális gép fordítania az értékes CPU-ciklusok hálózaticsomag-szűrés.

Virtuális gép létrehozásakor a hely, ahol minden bejövő forgalom blokkolása egy alapértelmezett ACL-JÉBEN helyezze el. Azonban ha egy végpont jön létre (3389-es port), majd az alapértelmezett ACL-t, hogy minden bejövő forgalom számára, hogy a végpont módosul. Az adott végpontra majd engedélyezett a bejövő forgalom az összes távoli alhálózatot, és nincs tűzfal kiépítése nem szükséges. Minden más port le lesznek tiltva a bejövő forgalmat, ha ezeket a portokat végpontok jön létre. Kimenő forgalom alapértelmezés szerint engedélyezett.

**Példa alapértelmezett ACL-tábla**

| **# Szabály** | **Távoli alhálózat** | **Végpont** | **Engedélyezési vagy megtagadási** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Engedély |

## <a name="permit-and-deny"></a>Engedélyezése és tiltása
Engedélyezheti szelektív vagy megtagadják a hálózati forgalmat egy virtuális gép bemeneti végponton szabályokat, amelyek adja meg az "engedélyezve" vagy "Elutasítás" létrehozásával. Fontos megjegyezni, hogy alapértelmezés szerint egy végpont létrehozását követően az összes forgalom számára engedélyezett a végpontot. Éppen ezért fontos megérteni engedélyezési és megtagadási szabályokat hozhat létre és elhelyezése a megfelelő, fontossági sorrendben, ha azt szeretné, hogy a virtuálisgép-végpont elérését engedélyezi, hogy a hálózati adatforgalom pontos szabályozása.

Megfontolandó szempontok:

1. **Nincs hozzáférés-szabályozási –** alapértelmezés szerint a végpont létrehozásakor azt engedélyezzen minden az a végpont.
2. **Lehetővé teszik -** hozzáadásakor egy vagy több "engedélyezve" tartományt, megtagadása minden más tartományok alapértelmezés szerint. Csak az engedélyezett IP-címtartományból csomagok fog tudni kommunikálni a virtuális gép végpontjának.
3. **Elutasítás -** egy vagy több "Elutasítás" címtartományok hozzáadásakor engedélyező forgalom összes tartományát alapértelmezés szerint.
4. **Engedélyezési és megtagadási - kombinációja** "engedélyezve" és "Elutasítás" Ha azt szeretné, hogy különítsen el egy adott IP-címtartomány engedélyezett vagy letiltott kombinációját használhatja.

## <a name="rules-and-rule-precedence"></a>Szabályok és a szabály fontossági sorrend
Hálózati hozzáférés-vezérlési listák az adott virtuális gépek végpontjaihoz állíthat be. Ha például egy RDP-végpontot, létrehozott egy virtuális gépen, hogy melyik zárolások le a hozzáférést az egyes IP-címek a hálózati hozzáférés-szabályozási is megadhat. Az alábbi táblázat olyan módon, hozzáférés biztosítása a nyilvános virtuális IP-címek (VIP) bizonyos tartománya, hogy engedélyezze a hozzáférést az RDP. Minden más távoli IP-címet a rendszer megtagadta. Követjük a *legalacsonyabb elsőbbséget* rendelés szabály.

### <a name="multiple-rules"></a>Több szabály
Az alábbi példában engedélyezi a hozzáférést az RDP-végpontot, csak a két nyilvános IPv4-címtartományokat (65.0.0.0/8, és 159.0.0.0/8), ha a elérése megadása két *lehetővé* szabályokat. Ebben az esetben RDP egy virtuális gép alapértelmezés szerint létrejön, mert előfordulhat, hogy szeretné zárolását, így az RDP-portra egy távoli alhálózat alapján való hozzáférést. Az alábbi példa szemlélteti a nyilvános virtuális IP-címek (VIP) bizonyos tartományának elérésének RDP-hez való hozzáférést biztosít egy módját. Minden más távoli IP-címet a rendszer megtagadta. Ez működik, mert a hálózati hozzáférés-vezérlési listák beállíthatja egy adott virtuális gép végponton, és alapértelmezés szerint nem férhet hozzá.

**Példa – több szabály**

| **# Szabály** | **Távoli alhálózat** | **Végpont** | **Engedélyezési vagy megtagadási** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Engedély |
| 200 |159.0.0.0/8 |3389 |Engedély |

### <a name="rule-order"></a>A szabály sorrend
A végpont több szabály adható meg, mert rendszerezését szabályokat annak meghatározásához, hogy melyik szabály lép érvénybe kell. A szabály rendelés a sorrend határozza meg. Hálózati hozzáférés-vezérlési listák kövesse a *legalacsonyabb elsőbbséget* rendelés szabály. Az alábbi példában a 80-as portot a végponton szelektív hozzáférést csak bizonyos IP-címtartományok. Ennek konfigurálásához rendelkezünk egy megtagadási szabályt (szabály \# 100) a címek a 175.1.0.1/24 területen. A második szabály majd prioritással 200-as, amellyel hozzáférést adhat a 175.0.0.0/8 más címek van megadva.

**Példa – szabály sorrend**

| **# Szabály** | **Távoli alhálózat** | **Végpont** | **Engedélyezési vagy megtagadási** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Megtagadás |
| 200 |175.0.0.0/8 |80 |Engedély |

## <a name="network-acls-and-load-balanced-sets"></a>Hálózati hozzáférés-vezérlési listák és az elosztott terhelésű készletek
Hálózati hozzáférés-vezérlési listák egy elosztott terhelésű készlet végponton adható meg. Ha egy ACL elosztott terhelésű készlet van megadva, a hálózati hozzáférés-szabályozási alkalmazza a elosztott terhelésű készletben lévő összes virtuális gépen. Például ha egy elosztott terhelésű csoportot hozott létre a "80-as Port" és az elosztott terhelésű készlet 3 virtuális gépeket tartalmaz, a hálózati hozzáférés-szabályozási létrehozott végponton "80-as Port" egy virtuális gép automatikusan érvénybe lépnek a többi virtuális géphez.

![Hálózati hozzáférés-vezérlési listák és az elosztott terhelésű készletek](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>További lépések
[PowerShell-lel végpontok hozzáférés-vezérlési listák felügyelete](virtual-networks-acl-powershell.md)

