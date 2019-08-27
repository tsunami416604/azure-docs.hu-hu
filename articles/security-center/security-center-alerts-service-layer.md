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
ms.date: 8/25/2019
ms.author: v-mohabe
ms.openlocfilehash: bc1b2a07f15ca98da7750c85cd25f2c5766c9bb5
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018167"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Veszélyforrások észlelése az Azure szolgáltatási rétegében Azure Security Center

Ez a témakör a következő Azure-szolgáltatási rétegek monitorozásakor elérhető Azure Security Center riasztásokat mutatja be:

* [Azure hálózati réteg](#network-layer)
* [Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió)](#management-layer)

>[!NOTE]
>A következő elemzések minden erőforrástípus esetében alkalmazhatók. A Security Center által biztosított telemetria használják az Azure belső hírcsatornái számára való koppintással.

## Azure hálózati réteg<a name="network-layer"></a>

Security Center a hálózati rétegbeli elemzések a minta [IPFIX adatokon](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)alapulnak, amelyek az Azure Core-útválasztók által gyűjtött csomagok fejlécei. Ezen adatcsatorna alapján Security Center gépi tanulási modellek a kártékony forgalmi tevékenységeket azonosítják és megjelölik. Az IP-címek dúsításához Security Center a Microsoft Threat Intelligence-adatbázist használja.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Gyanús kimenő RDP-hálózati tevékenység**|A mintavételes hálózati forgalom elemzése rendellenes kimenő RDP protokoll (RDP) kommunikációt észlelt a telepítésben lévő erőforrásból. Ez a tevékenység rendellenesnek minősül ebben a környezetben. Ez arra utalhat, hogy az erőforrást feltörték, és már használatban van egy külső RDP-végpont támadásával. Vegye figyelembe, hogy ez a típusú tevékenység azt eredményezheti, hogy az IP-címe rosszindulatúként van megjelölve külső entitások szerint.|
|**Gyanús kimenő RDP-hálózati tevékenység több célhelyre**|A mintavételen átadott hálózati forgalom elemzése rendellenes kimenő RDP-kommunikációt észlelt, amely egy, az üzemelő példányból több célhelyre származó erőforrásból származik. Ez a tevékenység rendellenesnek minősül ebben a környezetben. Ez arra utalhat, hogy az erőforrást feltörték, és már használatban van a támadási támadást okozó külső RDP-végpontokon. Vegye figyelembe, hogy ez a típusú tevékenység azt eredményezheti, hogy az IP-címe rosszindulatúként van megjelölve külső entitások szerint.|
|**Gyanús kimenő SSH-hálózati tevékenység**|A mintavételen átadott hálózati forgalom elemzése rendellenes kimenő Secure Shell-(SSH-) kommunikációt észlelt, amely a telepítésben lévő erőforrásból származik. Ez a tevékenység rendellenesnek minősül ebben a környezetben. Ez arra utalhat, hogy az erőforrást feltörték, és már használatban van egy külső SSH-végpont támadásával. Vegye figyelembe, hogy ez a típusú tevékenység azt eredményezheti, hogy az IP-címe rosszindulatúként van megjelölve külső entitások szerint.|
|**Gyanús kimenő SSH-hálózati tevékenység több célhelyre**|A mintavételen átvett hálózati forgalom elemzése rendellenes kimenő SSH-kommunikációt észlelt, amely egy, az üzemelő példányból több célhelyre származó erőforrásból származik. Ez a tevékenység rendellenesnek minősül ebben a környezetben. Ez arra utalhat, hogy az erőforrást feltörték, és már használatban van a támadási külső SSH-végpontok ellen. Vegye figyelembe, hogy ez a típusú tevékenység azt eredményezheti, hogy az IP-címe rosszindulatúként van megjelölve külső entitások szerint.|
|**Gyanús bejövő SSH-hálózati tevékenységek több forrásból**|A mintavételes hálózati forgalom elemzése rendellenes bejövő SSH-kommunikációt észlelt több forrásból a telepítésben lévő erőforrásra. Az erőforráshoz csatlakozó különböző egyedi IP-címek rendellenesnek számítanak ebben a környezetben. Ez a tevékenység arra utalhat, hogy az SSH-felületet több gazdagépről (botnet) támadó támadási kísérletet okoz.|
|**Gyanús bejövő SSH-hálózati tevékenység**|A mintavételes hálózati forgalom elemzése rendellenes bejövő SSH-kommunikációt észlelt az üzemelő példányban található erőforráshoz. Az erőforráshoz tartozó bejövő kapcsolatok viszonylag nagy száma ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy találgatásos támadási kísérletet tesz az SSH-felületén.
|**Gyanús bejövő RDP-hálózati tevékenység több forrásból**|A mintavételes hálózati forgalom elemzése rendellenes bejövő RDP-kommunikációt észlelt több forrásból a telepítésben lévő erőforráshoz. Az erőforráshoz csatlakozó különböző egyedi IP-címek rendellenesnek számítanak ebben a környezetben. Ez a tevékenység arra utalhat, hogy az RDP-felületet több gazdagépről (botnet) támadó támadási kísérletet okoz.|
|**Gyanús bejövő RDP-hálózati tevékenység**|A mintavételes hálózati forgalom elemzése rendellenes bejövő RDP-kommunikációt észlelt az üzemelő példány egyik erőforrásához. Az erőforráshoz tartozó bejövő kapcsolatok viszonylag nagy száma ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy találgatásos támadási kísérletet tesz az SSH-felületén.|
|**A rendszer kártékony címekkel rendelkező hálózati kommunikációt észlelt**|A mintavételen átvett hálózati forgalom elemzése egy lehetséges parancs-és vezérlési (C & C) kiszolgálóval való kommunikációt észlelt az üzemelő példányból származó erőforrásból. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|

Annak megismeréséhez, hogy a Security Center hogyan használhatják a hálózattal kapcsolatos jeleket a veszélyforrások elleni védelem alkalmazására, tekintse [meg a Azure Security Center heurisztikus DNS-észleléseit](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)

>[!NOTE]
>Az Azure hálózati veszélyforrások észlelésével kapcsolatos riasztások csak olyan virtuális gépeken jönnek létre Azure Security Center, amelyekhez a rendszer a gyanús kommunikáció megtörténte után a teljes óra esetében ugyanazt az IP-címet rendelte. Ez a virtuális gépekre, valamint az ügyfél előfizetésében a felügyelt szolgáltatás részeként létrehozott virtuális gépekre vonatkozik (pl. AK, Databricks).

## Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió)<a name ="management-layer"></a>

>[!NOTE]
>A Azure Resource Manager alapján Security Center védelmi réteg jelenleg előzetes verzióban érhető el.

A Security Center egy további védelmi réteget biztosít Azure Resource Manager események használatával, amely az Azure-beli vezérlési síkon tekinthető. A Azure Resource Manager rekordok elemzésével Security Center észleli a szokatlan vagy potenciálisan ártalmas műveleteket az Azure-előfizetési környezetben.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**A burst eszközkészlet futtatása**|A rendszer a környezetében egy ismert felhőalapú felderítő eszközkészletet észlelt. Az eszközt [](https://github.com/NetSPI/MicroBurst) a támadók (vagy a behatolási tesztelők) használhatják az előfizetések erőforrásainak leképezhetők, a nem biztonságos konfigurációk azonosítására és a bizalmas információk szivárgására.|
|**Azurite Toolkit futtatása**|A rendszer a környezetében egy ismert felhőalapú felderítő eszközkészletet észlelt. Az eszköz [Azurite](https://github.com/mwrlabs/Azurite) a támadók (vagy a behatolási tesztelők) használhatják az előfizetések erőforrásainak leképezésére és a nem biztonságos konfigurációk azonosítására.|
|**Gyanús felügyeleti munkamenet inaktív fiók használatával**|Az előfizetési tevékenység naplófájljainak elemzése gyanús viselkedést észlelt. A hosszú ideig nem használt rendszerbiztonsági tag mostantól olyan műveleteket hajt végre, amelyek biztonságos adatmegőrzést biztosítanak a támadók számára.|
|**Gyanús felügyeleti munkamenet a PowerShell használatával**|Az előfizetési tevékenység naplófájljainak elemzése gyanús viselkedést észlelt. Egy olyan rendszerbiztonsági tag, amely nem használja rendszeresen a PowerShellt az előfizetési környezet kezeléséhez, most már a PowerShellt használja, és olyan műveleteket hajt végre, amelyek biztonságban lehetnek a támadók számára.|
|**Fejlett Azure-adatmegőrzési technikák használata**|Az előfizetési tevékenység naplófájljainak elemzése gyanús viselkedést észlelt. Testreszabott szerepkörök lettek megadva legitimized. Ez azt eredményezheti, hogy a támadó a perzisztencia egy Azure-beli felhasználói környezetben szerezheti be.|
|**Ritkán használt országból indított tevékenység**|Olyan helyről származó tevékenység, amely a szervezet bármely felhasználója által nemrég vagy soha nem járt le.<br/>Az észlelés múltbeli tevékenységet helyek meghatározásához az új és a ritka figyelembe veszi. Az anomáliadetektálási motor a fenti helyeken, a szervezet felhasználói által használt információkat tárol. 
|**Névtelen IP-címekről indított tevékenység**|A rendszer olyan IP-címről származó felhasználói tevékenységet észlelt, amely névtelen proxy IP-címként lett azonosítva. <br/>Ezeket a proxykat olyan személyek használják, akik el szeretnék rejteni az eszköz IP-címét, és rosszindulatú szándékkal is használhatók. Ez az észlelés egy gépi tanulási algoritmust használ, amely csökkenti a téves pozitív értéket, például a szervezet felhasználói által széles körben használt, helytelenül címkézett IP-címeket.|
|**Lehetetlen utazás észlelhető**|Két felhasználói tevékenység történt (egyetlen vagy több munkamenetben), amely földrajzilag távoli helyekről származik. Ez egy rövidebb időszakon belül történik, mint amikor a felhasználó az első helyről a másodikra utazott volna. Ez azt jelzi, hogy egy másik felhasználó ugyanazokat a hitelesítő adatokat használja. <br/>Ez az észlelés egy gépi tanulási algoritmust használ, amely figyelmen kívül hagyja a nem megfelelő utazási feltételekhez, például a VPN-hez és a szervezet más felhasználói által rendszeresen használt helyekhez hozzájáruló nyilvánvaló téves riasztásokat. Az észlelés kezdeti tanulási időszaka hét nap, amely során az új felhasználó tevékenységi mintáját tanulja meg.|

>[!NOTE]
> Az előző elemzések közül több Microsoft Cloud App Security van. Ezen elemzések kihasználása érdekében aktiválni kell egy Cloud App Security licencet. Ha Cloud App Security licenccel rendelkezik, ezek a riasztások alapértelmezés szerint engedélyezve vannak. A letiltása:
>
> 1. A **Security Center** panelen válassza a **biztonsági házirend**elemet. A módosítani kívánt előfizetés esetében válassza a **beállítások szerkesztése**lehetőséget.
> 2. Válasszaa veszélyforrások észlelése lehetőséget.
> 3. Az **integráció engedélyezése**területen törölje a jelet a **Microsoft Cloud app Security az adatai elérésének engedélyezése**jelölőnégyzetből, majd válassza a **Mentés**lehetőséget.

>[!NOTE]
>Security Center a biztonsággal kapcsolatos ügyféladatokat ugyanabban a földrajzi régióban tárolja, mint az erőforrása. Ha a Microsoft még nem telepített Security Center az erőforrás geo-ban, akkor az a Egyesült Államok tárolja azokat. Ha Cloud App Security engedélyezve van, a rendszer ezeket az adatokat a Cloud App Security földrajzi hely szabályainak megfelelően tárolja. További információkért lásd: [adattároló a nem regionális szolgáltatásokhoz](https://azuredatacentermap.azurewebsites.net/).
