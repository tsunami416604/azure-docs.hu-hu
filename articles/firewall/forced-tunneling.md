---
title: Kényszerített bújtatás Azure Firewall
description: A kényszerített bújtatást beállíthatja úgy, hogy az internetes forgalmat egy további tűzfalra vagy hálózati virtuális készülékre irányítsa további feldolgozás céljából.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597276"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Azure Firewall kényszerített bújtatás (előzetes verzió)

A Azure Firewall konfigurálható úgy, hogy az összes internetes forgalmat a következő ugrásra irányítsa, ahelyett, hogy közvetlenül az internethez kellene jutnia. Előfordulhat például, hogy egy helyszíni peremhálózati tűzfallal vagy más hálózati virtuális berendezéssel (NVA) szeretné feldolgozni a hálózati forgalmat az internetre való továbbítás előtt.

> [!IMPORTANT]
> Azure Firewall kényszerített bújtatás jelenleg nyilvános előzetes verzióban érhető el.
>
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Alapértelmezés szerint a kényszerített bújtatás nem engedélyezett a Azure Firewallon annak biztosítása érdekében, hogy az összes kimenő Azure-függősége teljesül. A felhasználó által megadott route-(UDR-) konfigurációk a *AzureFirewallSubnet* , amelyek alapértelmezett útvonala nem közvetlenül az internetre van letiltva.

## <a name="forced-tunneling-configuration"></a>Kényszerített bújtatás konfigurálása

A kényszerített bújtatás támogatásához a szolgáltatás-felügyeleti forgalom el van különítve az ügyfelek forgalmával. Egy *AzureFirewallManagementSubnet* nevű további dedikált alhálózat (minimális alhálózat mérete/26) szükséges a saját társított nyilvános IP-címéhez. Az ezen az alhálózaton engedélyezett egyetlen útvonal az Internet alapértelmezett útvonala, a BGP-útvonalak propagálását pedig le kell tiltani.

Ha a BGP-n keresztül meghirdetett alapértelmezett útvonalon keresztül kényszeríti a forgalmat a helyszíni rendszerbe, létre kell hoznia a *AzureFirewallSubnet* és a *AzureFirewallManagementSubnet* a tűzfal üzembe helyezése előtt, és rendelkeznie kell egy, az internetre irányuló alapértelmezett útvonallal, és a **virtuális hálózati átjáró útvonal-propagálása** le van tiltva.

Ezen a konfiguráción belül a *AzureFirewallSubnet* mostantól útvonalakat is tartalmazhatnak bármely helyszíni TŰZFAL vagy NVA számára, hogy a forgalmat az internetre való továbbítás előtt dolgozzák fel. Ezeket az útvonalakat a BGP-n keresztül is közzéteheti, ha engedélyezve van a **virtuális hálózati átjáró** *AzureFirewallSubnet* ezen az alhálózaton.

Létrehozhat például egy alapértelmezett útvonalat a *AzureFirewallSubnet* a VPN-átjáróval a következő ugrásként a helyszíni eszköz beszerzéséhez. Vagy engedélyezheti a **virtuális hálózati átjáró útvonal-propagálását** , hogy lekérje a megfelelő útvonalakat a helyszíni hálózatra.

![Virtuális hálózati átjáró útvonalának propagálása](media/forced-tunneling/route-propagation.png)

Miután konfigurálta Azure Firewall a kényszerített bújtatás támogatásához, nem vonhatja vissza a konfigurációt. Ha eltávolítja az összes többi IP-konfigurációt a tűzfalon, a rendszer eltávolítja a felügyeleti IP-konfigurációt is, és a tűzfal fel van foglalva. A felügyeleti IP-konfigurációhoz rendelt nyilvános IP-cím nem távolítható el, de másik nyilvános IP-címet is hozzárendelhet.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása hibrid hálózaton a Azure Portal használatával](tutorial-hybrid-portal.md)