---
title: Azure szolgáltatási réteg fenyegetések észlelése – Azure Security Center
description: Ez a témakör a Azure Security Centerban elérhető Azure szolgáltatási rétegbeli riasztásokat mutatja be.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665694"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Veszélyforrások észlelése az Azure szolgáltatási rétegében Azure Security Center

Ez a témakör a következő Azure-szolgáltatási rétegek monitorozásakor elérhető Azure Security Center riasztásokat mutatja be:

* [Azure hálózati réteg](#network-layer)
* [Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

## Azure hálózati réteg<a name="network-layer"></a>

Security Center a hálózati rétegbeli elemzések a minta [IPFIX adatokon](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)alapulnak, amelyek az Azure Core-útválasztók által gyűjtött csomagok fejlécei. Ezen adatcsatorna alapján a Security Center gépi tanulási modelleket használ a kártékony forgalmi tevékenységek azonosítására és megjelölésére. A Security Center a Microsoft Threat Intelligence-adatbázist is használja az IP-címek dúsítására.

Bizonyos hálózati konfigurációk korlátozhatják Security Center a gyanús hálózati tevékenységekre vonatkozó riasztások generálását. A hálózati riasztások létrehozásához Security Center a következőket:

- A virtuális gép nyilvános IP-címmel rendelkezik (vagy egy nyilvános IP-címmel rendelkező terheléselosztó).

- A virtuális gép hálózati kimenő forgalmát nem blokkolja külső azonosító megoldás.

- A virtuális gép ugyanazzal az IP-címmel lett hozzárendelve, mint az a teljes óra, amelyben a gyanús kommunikáció történt. Ez a felügyelt szolgáltatás részeként létrehozott virtuális gépekre is vonatkozik (pl. AK, Databricks).

Az Azure hálózati réteggel kapcsolatos riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-azurenetlayer)tartalmazza.

További információ arról, hogy a Security Center hogyan használhatók a hálózattal kapcsolatos jelek a veszélyforrások elleni védelem alkalmazásához: [a heurisztikus DNS-észlelések a Security Centerban](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).


## Azure felügyeleti réteg (Azure Resource Manager) (előzetes verzió)<a name ="management-layer"></a>

A Azure Resource Manager alapján Security Center védelmi réteg jelenleg előzetes verzióban érhető el.

A Security Center egy további védelmi réteget biztosít Azure Resource Manager események használatával, amely az Azure-beli vezérlési síkon tekinthető. A Azure Resource Manager rekordok elemzésével Security Center észleli a szokatlan vagy potenciálisan ártalmas műveleteket az Azure-előfizetési környezetben.

A Azure Resource Manager (előzetes verzió) riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureresourceman)tekintheti meg.



>[!NOTE]
> Az előző elemzések közül több Microsoft Cloud App Security van. Ezen elemzések kihasználása érdekében aktiválni kell egy Cloud App Security licencet. Ha Cloud App Security licenccel rendelkezik, ezek a riasztások alapértelmezés szerint engedélyezve vannak. A letiltása:
>
> 1. A **Security Center** panelen válassza a **biztonsági házirend**elemet. A módosítani kívánt előfizetés esetében válassza a **beállítások szerkesztése**lehetőséget.
> 2. Válassza a **veszélyforrások észlelése**lehetőséget.
> 3. Az **integráció engedélyezése**területen törölje a jelet a **Microsoft Cloud app Security az adatai elérésének engedélyezése**jelölőnégyzetből, majd válassza a **Mentés**lehetőséget.

>[!NOTE]
>Security Center a biztonsággal kapcsolatos ügyféladatokat ugyanabban a földrajzi régióban tárolja, mint az erőforrása. Ha a Microsoft még nem telepített Security Center az erőforrás geo-ban, akkor az a Egyesült Államok tárolja azokat. Ha Cloud App Security engedélyezve van, a rendszer ezeket az adatokat a Cloud App Security földrajzi hely szabályainak megfelelően tárolja. További információkért lásd: [adattároló a nem regionális szolgáltatásokhoz](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault (előzetes verzió)<a name="azure-keyvault"></a>

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. 

Azure Security Center magában foglalja az Azure-natív, komplex veszélyforrások elleni védelmet Azure Key Vault, amely egy további biztonsági intelligenciát biztosít. Security Center szokatlan és potenciálisan ártalmas kísérleteket észlel Key Vault fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi, hogy biztonsági szakértő nélkül kezeljék a fenyegetéseket, és nincs szükség a harmadik féltől származó biztonsági figyelő rendszerek felügyeletére.  

Ha rendellenes tevékenységek történnek, Security Center riasztásokat jelenít meg, és opcionálisan e-mailben küldi el őket az előfizetés-rendszergazdáknak. Ezek a riasztások tartalmazzák a gyanús tevékenység részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat. 

> [!NOTE]
> Ez a szolgáltatás jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

A Azure Key Vault riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azurekv)tekintheti meg.
