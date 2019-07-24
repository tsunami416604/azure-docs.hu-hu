---
title: Veszélyforrások észlelése az Azure szolgáltatási rétegéhez Azure Security Centerban | Microsoft Docs
description: Ez a témakör a Azure Security Centerban elérhető Azure szolgáltatási rétegbeli riasztásokat mutatja be.
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
ms.author: v-mohabe
ms.openlocfilehash: f795822d76def4a6695a4746fba7e8566041cb2b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295556"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Veszélyforrások észlelése az Azure szolgáltatási rétegében Azure Security Center

Ez a témakör a következő Azure-szolgáltatási rétegek monitorozásakor elérhető Security Center riasztásokat mutatja be.

* [Azure hálózati réteg](#network-layer)
* [Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió)](#management-layer)

>[!NOTE]
>A Security Center az Azure belső hírcsatornái számára való kihasználása révén telemetria, az alábbi elemzések minden erőforrástípus alkalmazhatók.

## Azure hálózati réteg<a name="network-layer"></a>

Security Center a hálózati rétegbeli elemzések a minta [IPFIX adatokon](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)alapulnak, amelyek az Azure Core-útválasztók által gyűjtött csomagok fejlécei. Ezen adatcsatorna alapján Security Center gépi tanulási modellek a kártékony forgalmi tevékenységeket azonosítják és megjelölik. Az IP-címek gazdagabbá tételéhez Security Center kihasználja a Microsoft fenyegetésekkel kapcsolatos intelligencia-adatbázisát.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Gyanús kimenő RDP-hálózati tevékenység**|A mintavételes hálózati forgalom elemzése rendellenes kimenő RDP protokoll (RDP) kommunikációt észlelt az üzemelő példányból származó erőforrásból. Ez a tevékenység rendellenesnek minősül ebben a környezetben, és arra utalhat, hogy az erőforrást feltörték, és a rendszer már használatban van a külső RDP-végpont találgatásos kényszerítéséhez. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Gyanús kimenő RDP-hálózati tevékenység több célhelyre**|A mintavételes hálózati forgalom elemzése rendellenes kimenő RDP protokoll (RDP) kommunikációt észlelt az üzemelő példányból származó erőforrásból több célhelyre. Ez a tevékenység rendellenesnek minősül ebben a környezetben, és arra utalhat, hogy az erőforrást feltörték, és már használatban van a külső RDP-végpontok találgatásos kényszerítéséhez. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Gyanús kimenő SSH-hálózati tevékenység**|A mintavételes hálózati forgalom elemzése rendellenes kimenő Secure Shell-(SSH-) kommunikációt észlelt az üzemelő példányból származó erőforrásból. Ez a tevékenység rendellenesnek minősül ebben a környezetben, és arra utalhat, hogy az erőforrást feltörték, és a rendszer már használatban van a külső SSH-végpont találgatásos kényszerítéséhez. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Gyanús kimenő SSH-hálózati tevékenység több célhelyre**|A mintavételes hálózati forgalom elemzése rendellenes kimenő Secure Shell (SSH) kommunikációt észlelt az üzemelő példányból származó erőforrásból több célhelyre. Ez a tevékenység rendellenesnek minősül ebben a környezetben, és arra utalhat, hogy az erőforrást feltörték, és már használatban van a külső SSH-végpontok találgatásos kényszerítésére. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Gyanús bejövő SSH-hálózati tevékenységek több forrásból**|A mintavételes hálózati forgalom elemzése rendellenes bejövő SSH-kommunikációt észlelt több forrásból a telepítésben lévő erőforrásra. Az erőforráshoz csatlakozó különböző egyedi IP-címek rendellenesnek számítanak ebben a környezetben. Ez a tevékenység arra utalhat, hogy az SSH-felületet több gazdagépről (botnet) próbálja meg kikényszeríteni.|
|**Gyanús bejövő SSH-hálózati tevékenység**|A mintavételes hálózati forgalom elemzése rendellenes bejövő SSH-kommunikációt észlelt az üzemelő példányban található erőforráshoz. Az erőforráshoz tartozó bejövő kapcsolatok viszonylag nagy száma ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy megpróbálja kikényszeríteni az SSH-felületet.
|**Gyanús bejövő RDP-hálózati tevékenység több forrásból**|A mintavételes hálózati forgalom elemzése rendellenes bejövő RDP-kommunikációt észlelt több forrásból a telepítésben lévő erőforráshoz. Az erőforráshoz csatlakozó különböző egyedi IP-címek rendellenesnek számítanak ebben a környezetben. Ez a tevékenység arra utalhat, hogy az RDP-felületet több gazdagépről (botnet) próbálja meg kikényszeríteni.|
|**Gyanús bejövő RDP-hálózati tevékenység**|A mintavételes hálózati forgalom elemzése rendellenes bejövő RDP-kommunikációt észlelt az üzemelő példány egyik erőforrásához. Az erőforráshoz tartozó bejövő kapcsolatok viszonylag nagy száma ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy megpróbálja kikényszeríteni az SSH-felületet.|

Annak megismeréséhez, hogy a Security Center hogyan használhatják a hálózattal kapcsolatos jeleket a veszélyforrások elleni védelem alkalmazására, tekintse [meg a Azure Security Center heurisztikus DNS-észleléseit](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)
## Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió)<a name ="management-layer"></a>

>[!NOTE]
>A Azure Resource Manager alapján Security Center védelmi réteg jelenleg előzetes verzióban érhető el.

A Security Center egy további védelmi réteget biztosít Azure Resource Manager események kihasználása révén, amely az Azure-beli vezérlési síkon tekinthető. A Azure Resource Manager rekordok elemzésével Security Center észleli a szokatlan vagy potenciálisan ártalmas műveleteket az Azure-előfizetési környezetben.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**A burst eszközkészlet futtatása**|A rendszer a környezetében egy ismert felhőalapú felderítő eszközkészletet észlelt. A "burst" eszköz (lásd https://github.com/NetSPI/MicroBurst) : a támadók (vagy a behatolási tesztelők) az előfizetés (ok) erőforrásainak leképezésére, a nem biztonságos konfigurációk azonosítására és a bizalmas információk szivárgására használhatók.|
|**Azurite Toolkit futtatása**|A rendszer a környezetében egy ismert felhőalapú felderítő eszközkészletet észlelt. A "Azurite" eszközt (lásd https://github.com/mwrlabs/Azurite) : a támadók (vagy a behatolási teszterek) az előfizetés (ok) erőforrásainak leképezésére és a nem biztonságos konfigurációk azonosítására használhatók.|
|**Gyanús felügyeleti munkamenet inaktív fiók használatával**|Az előfizetési tevékenység naplófájljainak elemzése gyanús viselkedést észlelt. Egy olyan rendszerbiztonsági tag, amely hosszú ideje nem volt használatban, mostantól olyan műveleteket hajt végre, amelyek biztonságossá tehetik a támadók számára az adatmegőrzést.|
|**Gyanús felügyeleti munkamenet a PowerShell használatával**|Az előfizetési tevékenység naplófájljainak elemzése gyanús viselkedést észlelt. Egy olyan rendszerbiztonsági tag, amely nem használja rendszeresen a PowerShellt az előfizetési környezet kezeléséhez, mostantól a PowerShellt használja, és olyan műveleteket hajt végre, amelyek biztonságossá tehetik a támadók számára.|
|**Fejlett Azure-adatmegőrzési technikák használata**|Az előfizetési tevékenység naplófájljainak elemzése gyanús viselkedést észlelt. Testreszabott szerepkörök lettek megadva legitimized. Ez azt eredményezheti, hogy a támadó a perzisztencia egy Azure-beli felhasználói környezetben szerezheti be.|
|**Ritkán használt országból indított tevékenység**|Olyan helyről származó tevékenység, amely nem a közelmúltban vagy soha nem látogatott meg a szervezet egyik felhasználója sem.<br/>Az észlelés múltbeli tevékenységet helyek meghatározásához az új és a ritka figyelembe veszi. Az anomáliadetektálási motor a fenti helyeken, a szervezet felhasználói által használt információkat tárol. 
|**Névtelen IP-címekről indított tevékenység**|A rendszer olyan IP-címről származó felhasználói tevékenységet észlelt, amely névtelen proxy IP-címként lett azonosítva. <br/>Ezek proxyk, akik az eszköz IP-címének elrejtésére és rosszindulatú tevékenységek végrehajtására használhatók. Ez az észlelés egy gépi tanulási algoritmust használ, amely csökkenti a "hamis pozitív" értéket, például a szervezet felhasználói által széles körben használt helytelenül címkézett IP-címeket.|
|**Lehetetlen utazás észlelhető**|Két felhasználói tevékenység (egy vagy több munkamenet) történt, amely a földrajzilag távoli helyekről származott, amely rövidebb, mint a felhasználó az első helyről a másodikra való utazáshoz szükséges idő alatt. Ez azt jelzi, hogy egy másik felhasználó ugyanazokat a hitelesítő adatokat használja. <br/>Ez az észlelés egy gépi tanulási algoritmust használ, amely figyelmen kívül hagyja a "hamis pozitív" állapotot, és hozzájárul a lehetetlen utazási feltételekhez, például a VPN-hez és a szervezet más felhasználói által rendszeresen használt helyekhez. Az észlelés egy hét nap, mely során egy új felhasználók tevékenységi mintáit megtanulja betanulási időszakra van.|

>[!NOTE]
> A fenti elemzések közül több Microsoft Cloud App Securityt (MCAS) működtet. Ezen elemzések kihasználása érdekében aktivált MCAS-licencre van szükség. Ha rendelkezik MCAS-licenccel, akkor ezek a riasztások alapértelmezés szerint engedélyezve vannak. A letiltása:
>
> 1. A Security Center panelen válassza a **biztonsági házirend**elemet. A módosítani kívánt előfizetés esetében kattintson a **beállítások szerkesztése**elemre.
> 2. Kattintson a veszélyforrások észlelése elemre.
> 3. Az **integráció engedélyezése**területen törölje a **jelet a Microsoft Cloud app Security az adataihoz való hozzáféréshez**jelölőnégyzetből, majd kattintson a **Mentés**gombra.

>[!NOTE]
>Azure Security Center a biztonsággal kapcsolatos ügyféladatokat ugyanabban a földrajzi régióban tárolja, mint az erőforrása. Ha a Microsoft még nincs központilag telepítve Azure Security Center az erőforrás geo-ban, akkor a Egyesült Államok tárolja azokat. Ha a Microsoft Cloud App Security (MCAS) engedélyezve van, ezeket az információkat a MCAS földrajzi elhelyezkedési szabályainak megfelelően tárolja a rendszer. [További információt a nem regionális szolgáltatások](http://azuredatacentermap.azurewebsites.net/)adattárolási szolgáltatásában talál.
