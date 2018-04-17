---
title: Mi az Azure hálózati hozzáférés-vezérlési listaként?
description: További tudnivalók az Azure-ban a hozzáférés-vezérlési listák
services: virtual-network
documentationcenter: na
author: genli
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
ms.openlocfilehash: b2239ae6393e74a518522594d36f7b9c30d2a6f7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="what-is-an-endpoint-access-control-list"></a>Mi az a végpont hozzáférés-vezérlési listaként?

> [!IMPORTANT]
> Azure két különböző rendelkezik [üzembe helyezési modellel](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) az erőforrások létrehozására és kezelésére vonatkozó: Resource Manager és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a Resource Manager üzembe helyezési modellben. 

Végpont hozzáférés-vezérlési listaként (ACL) egy biztonsági fejlesztés az Azure-telepítés érhető el. Hozzáférés-vezérlési Listában lehetővé teszi a szelektív módon engedélyezheti vagy megtagadhatja a forgalom egy virtuális gép végpont számára. Ez a csomag szűrési lehetőség egy további biztonsági réteget biztosít. Csak hálózati ACL-listát is megadhat. Nem adhat meg egy hozzáférés-vezérlési listája egy virtuális hálózathoz vagy egy bizonyos alhálózat virtuális hálózat található. Javasoljuk, hogy hálózati biztonsági csoportokkal (NSG-k) helyett a hozzáférés-vezérlési listákat, amikor csak lehetséges. Az NSG-k kapcsolatos további információkért lásd: [hálózati biztonsági csoport – áttekintés](virtual-networks-nsg.md)

Hozzáférés-vezérlési listákat a PowerShell vagy az Azure portál segítségével konfigurálhatók. A hálózati hozzáférés-vezérlési lista beállítása a PowerShell használatával: [kezelése hozzáférés-vezérlési lista PowerShell-lel végpontok](virtual-networks-acl-powershell.md). A hálózati hozzáférés-vezérlési lista beállítása az Azure portál használatával: [beállítása végpontok egy virtuális géphez](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Használ a hálózati hozzáférés-vezérlési listákat, a következőket teheti:

* Választhatóan engedélyezheti vagy letilthatja a távoli alhálózati IPv4-címtartomány a virtuális gép bemeneti végponthoz alapján bejövő forgalmat.
* Blacklist IP-címek
* Virtuális gép végpontonként több szabály létrehozása
* Egy adott virtuális gép végpontjának (legalacsonyabb, legmagasabb és) használata a szabály kívánt szabályok biztosításához rendezés alkalmazzák
* Adjon meg egy hozzáférés-vezérlési listája egy adott távoli alhálózat IPv4-címet.

Tekintse meg a [Azure korlátozza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) ACL korlátok a cikkben találhat.

## <a name="how-acls-work"></a>Hozzáférés-vezérlési listák működése
Az ACL szabályok listáját tartalmazó objektum. Hozzáférés-vezérlési Listában létrehozásakor, és alkalmazza azt a virtuális gép végpontjának, hálózaticsomag-szűrés akkor történik meg a virtuális gép gazdagép-csomóponton. Ez azt jelenti, hogy a távoli IP-címekről érkező forgalmat a virtuális Gépet a megfelelő ACL-szabályok ahelyett, hogy a gazdagép csomópont szűrve van. Ez megakadályozza, hogy a virtuális gép hálózaticsomag-szűrés az értékes CPU-ciklusok beszállítói költségeit.

Amikor egy virtuális gépet hoz létre, egy alapértelmezett hozzáférés-vezérlési lista kerül, hogy minden bejövő forgalom blokkolása. Azonban ha egy végpontot hoz létre (3389-es port), majd az alapértelmezett hozzáférés-vezérlési lista úgy módosul, hogy az adott végpontra minden bejövő forgalom engedélyezése. Bejövő forgalom bármely távoli alhálózatból majd engedélyezve van az adott végpontra, és nincs tűzfal kiépítése szükség. Más portok blokkolják a bejövő forgalom, kivéve, ha azokat a portokat végpontokat hoz létre. Kimenő forgalom alapértelmezés szerint engedélyezett.

**Példa alapértelmezett hozzáférés-vezérlési lista tábla**

| **# Szabály** | **Távoli alhálózati** | **végpont** | **Engedélyezési vagy megtagadási** |
| --- | --- | --- | --- |
| 100 |0.0.0.0/0 |3389 |Engedély |

## <a name="permit-and-deny"></a>Engedélyezi és elutasítása
Akkor is szelektív módon engedélyezheti vagy megtagadhatja a hálózati forgalom egy virtuális gép bemeneti végpont szabályokat, amelyek adja meg az "Engedélyezés" vagy "Elutasítás" létrehozásával. Fontos megjegyezni, hogy alapértelmezés szerint a végpont jön létre, amikor minden forgalom engedélyezett a végpontnak. Ezért fontos annak megértése, hogyan engedélyezési vagy megtagadási szabályokat hozhat létre és helyezze el őket a megfelelő, fontossági sorrendben Ha azt szeretné, hogy a hálózati forgalmat, amely engedélyezi a virtuális gép végpontjának eléréséhez részletes szabályozását.

Megfontolandó szempontok:

1. **Nincs hozzáférés-vezérlési lista –** alapértelmezés szerint a végpont jön létre, ha azt teszi lehetővé a végpont az összes.
2. **Lehetővé teszik -** egy vagy több "Engedélyezés" tartományok hozzáadásakor megtagadása az összes tartomány alapértelmezés szerint. Csak az engedélyezett IP-címtartományt a csomagokat tudnak a virtuális gép végponttal való kommunikációhoz.
3. **Megtagadási -** ad hozzá egy vagy több "Elutasítás" tartományokhoz, ha engedélyezi az összes többi forgalom tartomány alapértelmezés szerint.
4. **Engedélyezési és megtagadási -** "Engedélyezés" és "Elutasítás" helyzet ki egy adott IP-címtartomány engedélyezett vagy letiltott carve is használhatja.

## <a name="rules-and-rule-precedence"></a>Szabályok és a szabály prioritását
Hálózati hozzáférés-vezérlési listákat az adott virtuális gép végpontokon is beállítható. Megadhatja például, hogy egy RDP-végpontot létrehozni egy virtuális gépen, hogy mely zárolások hozzáférés az egyes IP-címek a hálózati hozzáférés-vezérlési lista. Az alábbi táblázat oly módon, hogy hozzáférést biztosítson a nyilvános virtuális IP-címek (VIP) egy bizonyos tartomány való hozzáférés engedélyezése az RDP. Minden más távoli IP-címeket a rendszer megtagadja. Hajtsa végre az azt egy *legalacsonyabb elsőbbséget* szabály sorrendje.

### <a name="multiple-rules"></a>Több szabály
Az alábbi példában az RDP-végpontot való hozzáférés engedélyezése csak a két nyilvános IPv4-címtartományokat (65.0.0.0/8, és 159.0.0.0/8), ha a elérése megadó két *engedélyezése* szabályok. Ebben az esetben mivel RDP alapértelmezés szerint a virtuális gép jön létre, érdemes lehet a távoli alhálózati alapján RDP-porthoz való hozzáférés zárolását. Az alábbi példában látható módon való hozzáférés biztosítása a nyilvános virtuális IP-címek (VIP) egy bizonyos tartomány való hozzáférés engedélyezése az RDP. Minden más távoli IP-címeket a rendszer megtagadja. Ez működik, mert a hálózati hozzáférés-vezérlési listák egy adott virtuális gép végpont állítható be, és alapértelmezés szerint a hozzáférés megtagadva.

**Példa – több szabály**

| **# Szabály** | **Távoli alhálózati** | **végpont** | **Engedélyezési vagy megtagadási** |
| --- | --- | --- | --- |
| 100 |65.0.0.0/8 |3389 |Engedély |
| 200 |159.0.0.0/8 |3389 |Engedély |

### <a name="rule-order"></a>A szabály sorrendje
A végpont több szabály adható meg, mert olyan módon nem rendszerezhetik a szabály annak megállapítására, hogy melyik szabály élvez kell. A szabály rendelés sorrend határozza meg. Hálózati hozzáférés-vezérlési listák hajtsa végre a *legalacsonyabb elsőbbséget* szabály sorrendje. Az alábbi példában a 80-as portot a végponton szelektív hozzáférést csak bizonyos IP-címtartományokra. Ennek konfigurálásához olyan megtagadási szabályt kell (szabály \# 100) a címek a 175.1.0.1/24 területen. Egy második szabály a kötelező prioritással 200 175.0.0.0/8 alatt más címek hozzáférést tesz lehetővé.

**Példa – szabály sorrendje**

| **# Szabály** | **Távoli alhálózati** | **végpont** | **Engedélyezési vagy megtagadási** |
| --- | --- | --- | --- |
| 100 |175.1.0.1/24 |80 |Megtagadás |
| 200 |175.0.0.0/8 |80 |Engedély |

## <a name="network-acls-and-load-balanced-sets"></a>Hálózati hozzáférés-vezérlési listákat, és betölti az elosztott terhelésű készletek
Hálózati hozzáférés-vezérlési listák egy elosztott terhelésű készlet végpont lehet megadni. Ha hozzáférés-vezérlési Listában elosztott terhelésű készlet van megadva, a hálózati hozzáférés-vezérlési lista vonatkozik, hogy elosztott terhelésű készlet összes virtuális gépnek. Például ha egy elosztott terhelésű készlet jön létre a "Port a 80-as" és az elosztott terhelésű készlet 3 virtuális gép, a hálózati hozzáférés-vezérlési lista létrehozott végponton "80-as Port" egy virtuális gép automatikusan települ a többi virtuális gépe.

![Hálózati hozzáférés-vezérlési listákat, és betölti az elosztott terhelésű készletek](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>További lépések
[Hozzáférés-vezérlési listák PowerShell-lel végpontok kezelése](virtual-networks-acl-powershell.md)

