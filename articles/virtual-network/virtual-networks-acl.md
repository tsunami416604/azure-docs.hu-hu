---
title: Mi az Azure hálózati hozzáférés-vezérlési lista?
description: Tudnivalók az Azure-beli hozzáférés-vezérlési listáról
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 5e410dc4e7f47223430497825dc2a26e41c6af67
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979557"
---
# <a name="what-is-an-endpoint-access-control-list"></a>Mi az a végpontok hozzáférés-vezérlési listája?

> [!IMPORTANT]
> Az Azure két különböző [üzembe helyezési modellel](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendelkezik az erőforrások létrehozásához és használatához: Resource Manager és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy a legtöbb új központi telepítés a Resource Manager-alapú üzemi modellt használja. 

A végpontok hozzáférés-vezérlési listája (ACL) az Azure-beli üzembe helyezés számára elérhető biztonsági fejlesztés. Az ACL lehetővé teszi, hogy szelektíven engedélyezze vagy megtagadja a virtuális gép végpontjának forgalmát. Ez a csomagszűrő funkció további biztonsági réteget biztosít. Csak a végpontok hálózati hozzáférés-vezérlési listáit lehet megadni. Nem adhat meg ACL-t egy virtuális hálózathoz vagy egy virtuális hálózatban található adott alhálózathoz. Az ACL-ek helyett hálózati biztonsági csoportokat (NSG) ajánlott használni, amikor csak lehetséges. A NSG használatakor a rendszer lecseréli a végpontok hozzáférés-vezérlési listáját, és a továbbiakban nem kényszeríti ki. További információ a NSG: [hálózati biztonsági csoport – áttekintés](security-overview.md)

Az ACL-eket a PowerShell vagy a Azure Portal használatával lehet konfigurálni. Ha hálózati ACL-t szeretne konfigurálni a PowerShell használatával, tekintse meg a [végpontok hozzáférés-vezérlési listáinak kezelése a PowerShell használatával](virtual-networks-acl-powershell.md)című témakört. Ha a hálózati ACL-t a Azure Portal használatával szeretné konfigurálni, tekintse [meg a végpontok virtuális géphez való beállítását](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)ismertető témakört.

A hálózati ACL-ek használatával a következőket teheti:

* A bejövő forgalom szelektív engedélyezése vagy megtagadása a távoli alhálózat IPv4-címtartomány alapján egy virtuálisgép-bemeneti végponton.
* Feketelista IP-címei
* Több szabály létrehozása virtuálisgép-végponton
* A szabályok sorrendjét használva biztosíthatja, hogy a megfelelő szabálykészlet legyen alkalmazva egy adott virtuálisgép-végponton (a legalacsonyabb – a legmagasabb).
* Egy adott távoli alhálózati IPv4-címnek megfelelő ACL-t kell megadnia.

Tekintse meg az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) szóló cikket az ACL korlátaival kapcsolatban.

## <a name="how-acls-work"></a>Az ACL-ek működése
Az ACL egy olyan objektum, amely tartalmazza a szabályok listáját. Amikor létrehoz egy ACL-t, és alkalmazza azt egy virtuálisgép-végpontra, a csomagszűrés a virtuális gép gazdagép csomópontján történik. Ez azt jelenti, hogy a távoli IP-címekről érkező forgalmat a gazdagép csomópontja szűri az ACL-szabályoknak a virtuális gépen való megfeleltetése helyett. Ez megakadályozza, hogy a virtuális gép értékes CPU-ciklusokat fordítson a csomagszűrés számára.

Virtuális gép létrehozásakor a rendszer egy alapértelmezett ACL-t helyez el az összes bejövő forgalom blokkolásához. Ha azonban létrehoz egy végpontot a (3389-es porthoz), akkor az alapértelmezett ACL-t úgy módosítja, hogy az adott végpont összes bejövő forgalmát engedélyezze. A rendszer a távoli alhálózatokról érkező bejövő forgalmat ezután engedélyezi a végpont számára, és nincs szükség tűzfal üzembe helyezésére. Az összes többi port blokkolva van a bejövő forgalom számára, kivéve, ha végpontok jönnek létre a portok számára. A kimenő forgalom alapértelmezés szerint engedélyezett.

**Példa alapértelmezett ACL-táblázatra**

| **Szabály #** | **Távoli alhálózat** | **Végpont** | **Engedélyezés/megtagadás** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Engedélyezése |

## <a name="permit-and-deny"></a>Engedélyezés és megtagadás
A virtuális gép bemeneti végpontjának hálózati forgalmát szelektív módon engedélyezheti vagy tilthatja le olyan szabályok létrehozásával, amelyek az "engedélyezés" vagy a "megtagadás" beállítást adják meg. Fontos megjegyezni, hogy a végpontok létrehozásakor alapértelmezés szerint minden forgalom engedélyezett a végpont számára. Ezért fontos tisztában lenni az engedélyezési/megtagadási szabályok létrehozásával és a megfelelő sorrendben történő elhelyezésével, ha azt szeretné, hogy a hálózati forgalom részletesen szabályozható legyen a virtuális gép végpontjának eléréséhez.

Megfontolandó pontok:

1. **Nincs ACL –** A végpontok létrehozásakor alapértelmezés szerint az összes végpontot engedélyezzük.
2. **Engedélyezés –** Ha hozzáad egy vagy több "engedélyezési" tartományt, a rendszer alapértelmezés szerint letiltja az összes többi tartományt. Csak az engedélyezett IP-címtartomány csomagjai lesznek képesek kommunikálni a virtuális gép végpontjának használatával.
3. **Megtagadás –** Egy vagy több "megtagadás" tartomány hozzáadásakor a rendszer alapértelmezés szerint engedélyezi az összes többi tartomány forgalmát.
4. **Az engedélyezés és a Megtagadás kombinációja –** Az "engedélyezés" és a "megtagadás" kombinációját használhatja, ha egy adott IP-címtartományt szeretne kifaragni, amelyet engedélyezni vagy megtagadni kíván.

## <a name="rules-and-rule-precedence"></a>Szabályok és szabályok prioritása
Hálózati ACL-ek állíthatók be adott virtuálisgép-végpontokon. Megadhat például egy hálózati ACL-t egy virtuális gépen létrehozott RDP-végponthoz, amely lezárja bizonyos IP-címek elérését. Az alábbi táblázat bemutatja, hogyan biztosíthat hozzáférést egy bizonyos tartományhoz tartozó nyilvános virtuális IP-címekhez (VIP), hogy engedélyezze a hozzáférést az RDP-hez. Minden más távoli IP-cím megtagadva. A *legalacsonyabb sorrendű* szabályt követjük.

### <a name="multiple-rules"></a>Több szabály
Ha az alábbi példában az RDP-végpont elérését csak két nyilvános IPv4-címtartomány (65.0.0.0/8 és 159.0.0.0/8) alapján szeretné engedélyezni, ezt két *engedélyezési* szabály megadásával érheti el. Ebben az esetben, mivel a virtuális gép alapértelmezés szerint az RDP-t hozza létre, a távoli alhálózat alapján érdemes lehet zárolni az RDP-porthoz való hozzáférést. Az alábbi példa azt mutatja be, hogyan lehet hozzáférést biztosítani egy bizonyos tartomány nyilvános virtuális IP-címeihez (VIP), hogy engedélyezze a hozzáférést az RDP-hez. Minden más távoli IP-cím megtagadva. Ez azért működik, mert hálózati ACL-ek állíthatók be egy adott virtuálisgép-végponthoz, és a hozzáférés alapértelmezés szerint meg van tagadva.

**Példa – több szabály**

| **Szabály #** | **Távoli alhálózat** | **Végpont** | **Engedélyezés/megtagadás** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Engedélyezése |
| 200 |159.0.0.0/8 |3389 |Engedélyezése |

### <a name="rule-order"></a>Szabály sorrendje
Mivel egy végponthoz több szabály is megadható, a szabályok rendszerezésének meg kell felelnie, hogy melyik szabály elsőbbséget élvez. A szabály sorrendje a sorrendet adja meg. A hálózati ACL-ek a *legalacsonyabb elsőbbségi* szabályok sorrendjét követik. Az alábbi példában az 80-as porton lévő végpont szelektív módon kap hozzáférést csak bizonyos IP-címtartományok számára. Ennek konfigurálásához egy megtagadási szabály (\# 100-es szabály) tartozik a 175.1.0.1/24 terület címeihez. Ezután megadhat egy második szabályt a 200-as prioritással, amely engedélyezi a hozzáférést az összes többi címhez a 175.0.0.0/8 alatt.

**Példa – szabályok prioritása**

| **Szabály #** | **Távoli alhálózat** | **Végpont** | **Engedélyezés/megtagadás** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Megtagadás |
| 200 |175.0.0.0/8 |80 |Engedélyezése |

## <a name="network-acls-and-load-balanced-sets"></a>Hálózati ACL-ek és elosztott terhelésű készletek
A hálózati ACL-ek megadhatók egy elosztott terhelésű készlet végpontján. Ha egy elosztott terhelésű készlethez hozzáférés-vezérlési lista van megadva, a rendszer a hálózati ACL-t alkalmazza a terheléselosztásos készletben lévő összes virtuális gépre. Ha például egy elosztott terhelésű készlet jön létre a "port 80" értékkel, és az elosztott terhelésű készlet 3 virtuális gépet tartalmaz, akkor az egyik virtuális gép "port 80" végpontján létrehozott hálózati ACL automatikusan a többi virtuális gépre lesz érvényes.

![Hálózati ACL-ek és elosztott terhelésű készletek](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Következő lépések
[A végpontok hozzáférés-vezérlési listáját a PowerShell használatával kezelheti](virtual-networks-acl-powershell.md)

