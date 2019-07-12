---
title: Fenyegetések észlelése az Azure-szolgáltatási réteg az Azure Security Centerben |} A Microsoft Docs
description: Ez a témakör bemutatja az Azure-szolgáltatási réteg elérhető riasztások az Azure Security Centerben.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 8c1733877834f82d9ee2524cf8bf54f532e7d9c4
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571724"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>A fenyegetésészlelés, az Azure-szolgáltatási réteg az Azure Security Centerben

Ez a témakör a Security Center riasztásainak elérhető mutat be, a figyelő a következő Azure-szolgáltatási szinteket.

* [Az Azure hálózati réteg](#network-layer)
* [Az Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió)](#management-layer)

>[!NOTE]
>A telemetriát, amely az Azure belső hírcsatornák, koppintson a Security Center használatával, az alább megadott elemzési kell alkalmazni az összes erőforrástípus.

## Az Azure hálózati réteg<a name="network-layer"></a>

A Security Center hálózati rétegből elemzési minta alapuló [IPFIX adatok](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), amelyeket a csomagfejléceket, Azure core útválasztók által gyűjtött. Erre az adatcsatornára alapján a Security Center gépi tanulási modellek azonosíthatja és rosszindulatú forgalom tevékenységet jelző. To enrich IP addresses, Security Center leverages Microsoft’s Threat Intelligence database.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Gyanús kimenő RDP hálózati aktivitás**|A mintavételezett hálózati forgalomelemzés rendellenes kimenő távoli asztal protokoll (RDP)-kommunikációt észlelt származó egy erőforrást a központi telepítésben. Ez a Tevékenység ebben a környezetben rendellenesnek számít, és arra utalhat, hogy az erőforrás biztonsága sérült, és most már használják, hogy külső találgatásos RDP-végpontot. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Gyanús kimenő RDP hálózati aktivitás, több célra irányuló**|A mintavételezett hálózati forgalomelemzés rendellenes kimenő távoli asztal protokoll (RDP)-kommunikációt észlelt egy erőforrást a központi telepítésben származó több célhelyre. Ez a Tevékenység ebben a környezetben rendellenesnek számít, és arra utalhat, hogy az erőforrás biztonsága sérült, és a külső találgatásos RDP-végpontokra irányuló most már használja. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Gyanús kimenő SSH hálózati aktivitás**|A mintavételezett hálózati forgalomelemzés rendellenes kimenő Secure Shell (SSH)-kommunikációt észlelt származó az üzembe helyezés az erőforráshoz. Ez a Tevékenység ebben a környezetben rendellenesnek számít, és utalhat, hogy az erőforrás biztonsága sérült, és most már használják, hogy külső találgatásos SSH-végpont. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Gyanús kimenő SSH hálózati aktivitás, több célra irányuló**|A mintavételezett hálózati forgalomelemzés rendellenes kimenő Secure Shell (SSH)-kommunikációt észlelt egy erőforrást a központi telepítésben származó több célhelyre. Ez a Tevékenység ebben a környezetben rendellenesnek számít, és utalhat, hogy az erőforrás biztonsága sérült, és most találgatásos külső SSH-végpontokra irányuló használja. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Gyanús bejövő SSH hálózati aktivitás a különböző forrásokból származó**|A mintavételezett hálózati forgalomelemzés rendellenes bejövő SSH által kezdeményezett kommunikáció a különböző forrásokból egy erőforrást a központi telepítésben. Ebben a környezetben rendellenesnek számít a különböző egyedi IP-címek az erőforrás csatlakozik. Ez a tevékenység az SSH-kapcsolat több gazdagépről (Botnet) jelezheti irányuló találgatásos kísérlet.|
|**Gyanús bejövő SSH hálózati aktivitás**|A mintavételezett hálózati forgalomelemzés rendellenes bejövő SSH-kommunikációt észlelt egy erőforráshoz a központi telepítésben. Viszonylag nagy számú bejövő kapcsolat az erőforrásra ebben a környezetben rendellenesnek számít. Ez a tevékenység az SSH-kapcsolat jelezheti irányuló találgatásos kísérlet.
|**Gyanús bejövő RDP hálózati aktivitás a különböző forrásokból származó**|A mintavételezett hálózati forgalomelemzés rendellenes bejövő RDP-konfiguráció több forrásból származó erőforráshoz a központi telepítésben. Ebben a környezetben rendellenesnek számít a különböző egyedi IP-címek az erőforrás csatlakozik. Ez a tevékenység az RDP-kapcsolat több gazdagépről (Botnet) jelezheti irányuló találgatásos kísérlet.|
|**Gyanús bejövő RDP hálózati aktivitás**|A mintavételezett hálózati forgalomelemzés rendellenes bejövő RDP-kommunikációt észlelt egy erőforráshoz a központi telepítésben. Viszonylag nagy számú bejövő kapcsolat az erőforrásra ebben a környezetben rendellenesnek számít. Ez a tevékenység az SSH-kapcsolat jelezheti irányuló találgatásos kísérlet.|

Megismerheti, hogyan használhatja a Security Center a hálózati kapcsolódó jelek, veszélyforrások elleni védelem a alkalmazni, lásd: [heurisztikus DNS az Azure Security Center észlelési](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Az Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió)<a name ="management-layer"></a>

>[!NOTE]
>A Security Center védelmi réteget az Azure Resource Manager-alapú jelenleg előzetes verzióban érhető el.

A Security Center nyújtotta előnyöket kihasználva Azure Resource Manager-események, amelyek az Azure-hoz a vezérlősík tekinthető egy további védelmi réteget biztosít. Az Azure Resource Manager-rekordok elemzésével a Security Center szokatlan és vélhetően kárt okozó operations észleli az Azure-előfizetés környezetben.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**MicroBurst toolkit futtatása**|Egy ismert felhőkörnyezet felderítés toolkit futtatása a környezetben észlelt. Az eszköz "MicroBurst" (lásd: https://github.com/NetSPI/MicroBurst) egy támadó (vagy behatolási tesztelő) leképezése az előfizetés(ek) erőforrások, nem biztonságos felismerésében és szivárogtatnak ki bizalmas információkat használhatja.|
|**Azurite toolkit futtatása**|Egy ismert felhőkörnyezet felderítés toolkit futtatása a környezetben észlelt. Az eszköz "Azurite" (lásd: https://github.com/mwrlabs/Azurite) egy támadó (vagy behatolási tesztelő) az előfizetés(ek) erőforrások leképezéséhez, és nem biztonságos konfigurációk azonosítása által használható.|
|**Gyanús felügyeleti munkamenet egy inaktív fiók használata**|Előfizetés tevékenység naplók elemzése egy gyanús viselkedést észlelt. Egy egyszerű, amelynek nem használja egy hosszú ideig, most biztonságossá tételéhez, hogy egy támadó megőrzését műveleteket végez.|
|**Gyanús felügyeleti munkamenet PowerShell-lel**|Előfizetés tevékenység naplók elemzése egy gyanús viselkedést észlelt. Egy egyszerű, amely nem rendszeresen kezelése PowerShell használatával az előfizetési környezetet, most már használhatja a Powershellt vagy műveleteket végrehajtani, amelyek biztonságossá teheti a támadónak adatmegőrzés.|
|**Speciális Azure adatmegőrzés módszer használata**|Előfizetés tevékenység naplók elemzése egy gyanús viselkedést észlelt. Egyéni kialakítású szerepkörök legitimized identitás entitások kapott. Ezzel a módszerrel a támadó perzisztencia egy Azure-ügyfél környezetben.|
|**Ritkán használt országból indított tevékenység**|Tevékenység, amely korábban nem nemrég vagy soha nem látogatta a szervezet bármely felhasználója helyről történt.<br/>Az észlelés múltbeli tevékenységet helyek meghatározásához az új és a ritka figyelembe veszi. Az anomáliadetektálási motor a fenti helyeken, a szervezet felhasználói által használt információkat tárol. 
|**Névtelen IP-címekről indított tevékenység**|Felhasználói tevékenység IP-címről, amely szerint a névtelen proxy IP-címet észlelt lett azonosítva. <br/>Ezek proxyk, akik az eszköz IP-címének elrejtésére és rosszindulatú tevékenységek végrehajtására használhatók. Az észlelés kihasználja a gépi tanulási algoritmus, amely csökkenti a "vakriasztásokat", például a szervezet felhasználói által széles körben használt gépeltünk címkézett IP-címek.|
|**Lehetetlen odautazás észlelt**|Két felhasználói tevékenység (az egy vagy több munkamenet) történt, származó földrajzilag távoli helyekről időnél rövidebb idő alatt, vett volna a felhasználót keresnie az első helyen a második. Ez azt jelzi, hogy egy másik felhasználó használja ugyanazokat a hitelesítő adatokat. <br/>Az észlelés a gépi tanulási algoritmus, amely figyelmen kívül hagyja a nyilvánvaló "vakriasztásokat" a lehetetlen utazás feltételek, például a VPN-EK és a szervezet más felhasználói által rendszeresen használt helyek hozzájáruló használja. Az észlelés egy hét nap, mely során egy új felhasználók tevékenységi mintáit megtanulja betanulási időszakra van.|

>[!NOTE]
> A fenti analytics számos működteti a Microsoft Cloud App Security (MCAS). Ezek az analitikák kihasználhatják, egy aktivált MCAS-licencre szükség. Ha az MCAS-licencre van, majd ezek a riasztások alapértelmezés szerint engedélyezettek. Letiltja őket:
>
> 1. Válassza ki a Security Center panel **biztonsági házirend**. Az előfizetés használatára szeretne átállni, kattintson a **beállításainak szerkesztése**.
> 2. Kattintson a **Fenyegetésészlelés**.
> 3. Alatt **Integrációk engedélyezése**, törölje a jelet **engedélyezése a Microsoft Cloud App Security az adatok eléréséhez**, és kattintson a **mentése**.

>[!NOTE]
>Az Azure Security Center biztonsági vásárlói adatokat tárolja, és annak az erőforrásnak ugyanabban a földrajzi. Ha a Microsoft rendelkezik még nem helyezte üzembe az Azure Security Center az a erőforrás földrajzi, majd tárolja az adatokat az Egyesült Államokban. Ha engedélyezve van a Microsoft Cloud App Security (MCAS), ezeket az információkat tárolja MCAS földrajzi hely szabályainak megfelelően. Lásd: [adatainak tárolására szolgáló további információ a nem régióhoz kötött szolgáltatások](http://azuredatacentermap.azurewebsites.net/).
