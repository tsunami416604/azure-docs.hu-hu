---
title: Kényszerített bújtatás Azure Firewall
description: A kényszerített bújtatást beállíthatja úgy, hogy az internetes forgalmat egy további tűzfalra vagy hálózati virtuális készülékre irányítsa további feldolgozás céljából.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/01/2020
ms.author: victorh
ms.openlocfilehash: a467aa60b131e47e9251366369b3fae8dd95c004
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84267698"
---
# <a name="azure-firewall-forced-tunneling"></a>Kényszerített bújtatás Azure Firewall

Amikor új Azure Firewall konfigurál, az összes internetes forgalmat átirányíthatja egy kijelölt következő ugrásra ahelyett, hogy közvetlenül az internethez kellene csatlakoznia. Előfordulhat például, hogy egy helyszíni peremhálózati tűzfallal vagy más hálózati virtuális berendezéssel (NVA) szeretné feldolgozni a hálózati forgalmat az internetre való továbbítás előtt. A kényszerített bújtatáshoz azonban nem konfigurálhat meglévő tűzfalat.

Alapértelmezés szerint a kényszerített bújtatás nem engedélyezett a Azure Firewallon annak biztosítása érdekében, hogy az összes kimenő Azure-függősége teljesül. A felhasználó által megadott route-(UDR-) konfigurációk a *AzureFirewallSubnet* , amelyek alapértelmezett útvonala nem közvetlenül az internetre van letiltva.

## <a name="forced-tunneling-configuration"></a>Kényszerített bújtatás konfigurálása

A kényszerített bújtatás támogatásához a szolgáltatás-felügyeleti forgalom el van különítve az ügyfelek forgalmával. Egy *AzureFirewallManagementSubnet* nevű további dedikált alhálózat (minimális alhálózat mérete/26) szükséges a saját társított nyilvános IP-címéhez. Az ezen az alhálózaton engedélyezett egyetlen útvonal az Internet alapértelmezett útvonala, a BGP-útvonalak propagálását pedig le kell tiltani.

Ha a BGP-n keresztül meghirdetett alapértelmezett útvonalon keresztül kényszeríti a forgalmat a helyszíni rendszerbe, létre kell hoznia a *AzureFirewallSubnet* és a *AzureFirewallManagementSubnet* a tűzfal üzembe helyezése előtt, és rendelkeznie kell egy, az internetre irányuló alapértelmezett útvonallal, és a **virtuális hálózati átjáró útvonal-propagálása** le van tiltva.

Ebben a konfigurációban a *AzureFirewallSubnet* mostantól a helyszíni tűzfalakhoz vagy NVA útvonalakat is tartalmazhatnak a forgalom feldolgozásához, mielőtt a rendszer átadja az internetre. Ezeket az útvonalakat a BGP-n keresztül is közzéteheti, ha engedélyezve van a **virtuális hálózati átjáró** *AzureFirewallSubnet* ezen az alhálózaton.

Létrehozhat például egy alapértelmezett útvonalat a *AzureFirewallSubnet* a VPN-átjáróval a következő ugrásként a helyszíni eszköz beszerzéséhez. Vagy engedélyezheti a **virtuális hálózati átjáró útvonal-propagálását** , hogy lekérje a megfelelő útvonalakat a helyszíni hálózatra.

![Virtuális hálózati átjáró útvonalának propagálása](media/forced-tunneling/route-propagation.png)

Ha engedélyezi a kényszerített bújtatást, az internethez kötött forgalom a AzureFirewallSubnet egyik címfordítást, a forrást pedig a helyszíni tűzfalból elrejti.

Ha a szervezete nyilvános IP-címtartományt használ a magánhálózatok számára, Azure Firewall SNATs a forgalmat a AzureFirewallSubnet egyik tűzfal magánhálózati IP-címére. Azonban úgy is konfigurálhatja a Azure Firewallt, hogy **ne** SNAT a nyilvános IP-címtartományt. További információ: [Azure Firewall SNAT magánhálózati IP-címtartományok](snat-private-range.md).

Miután konfigurálta Azure Firewall a kényszerített bújtatás támogatásához, nem vonhatja vissza a konfigurációt. Ha eltávolítja az összes többi IP-konfigurációt a tűzfalon, a rendszer eltávolítja a felügyeleti IP-konfigurációt is, és a tűzfal fel van foglalva. A felügyeleti IP-konfigurációhoz rendelt nyilvános IP-cím nem távolítható el, de másik nyilvános IP-címet is hozzárendelhet.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása hibrid hálózaton a Azure Portal használatával](tutorial-hybrid-portal.md)
