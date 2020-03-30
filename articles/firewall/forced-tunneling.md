---
title: Az Azure Firewall kényszerített bújtatása
description: Beállíthatja a kényszerített bújtatást, hogy az internethez kötött forgalmat egy további tűzfalra vagy hálózati virtuális készülékre irányítsa további feldolgozáscéljából.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/24/2020
ms.author: victorh
ms.openlocfilehash: e51f6de370a5340082f64a0ca15c61583f75962b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597276"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Az Azure Firewall kényszerített bújtatása (előzetes verzió)

Beállíthatja, hogy az Azure Firewall az összes internethez kötött forgalmat egy kijelölt következő ugráshoz irányítsa, ahelyett, hogy közvetlenül az internetre menne. Előfordulhat például, hogy egy helyszíni tűzfal vagy más hálózati virtuális berendezés (NVA) rendelkezik a hálózati forgalom feldolgozásához, mielőtt azt átadnák az internetnek.

> [!IMPORTANT]
> Az Azure Firewall kényszerített bújtatása jelenleg nyilvános előzetes verzióban.
>
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Alapértelmezés szerint kényszerített bújtatás nem engedélyezett az Azure Firewall annak biztosítása érdekében, az összes kimenő Azure-függőség teljesülnek. Az *AzureFirewallSubnet* felhasználói által definiált útvonalkonfigurációi, amelyek alapértelmezett útvonala nem megy közvetlenül az internetre, le vannak tiltva.

## <a name="forced-tunneling-configuration"></a>Kényszerített bújtatási konfiguráció

A kényszerített bújtatás támogatása érdekében a szolgáltatáskezelési forgalom elkülönül az ügyfélforgalomtól. Egy további dedikált alhálózat nevű *AzureFirewallManagementSubnet* (minimális alhálózati méret /26) van szükség a saját társított nyilvános IP-címet. Az alhálózaton csak az internetre szóló alapértelmezett útvonal engedélyezett, és a BGP-útvonal propagálását le kell tiltani.

Ha a BGP-n keresztül meghirdetett alapértelmezett útvonallal rendelkezik a forgalom helyszíni kikényszerítéséhez, létre kell hoznia az *AzureFirewallSubnet* és az *AzureFirewallManagementSubnet* alkalmazást a tűzfal üzembe helyezése előtt, és rendelkeznie kell egy UDR-rel az internetre, és le kell tiltania a **virtuális hálózati átjáró propagálását.**

Ebben a konfigurációban az *AzureFirewallSubnet* mostantól útvonalakat bármely helyszíni tűzfalvagy NVA a forgalom feldolgozásához, mielőtt az átaz internetre. Ezeket az útvonalakat a BGP-n keresztül az *AzureFirewallSubnet* szolgáltatásnak is közzéteheti, ha **a virtuális hálózati átjáróútvonal-propagálás** engedélyezve van ezen az alhálózaton.

Létrehozhat például egy alapértelmezett útvonalat az *AzureFirewallSubnet-en* a VPN-átjáróval a következő ugrásként a helyszíni eszközhöz való eljuttatáshoz. Vagy engedélyezheti **a virtuális hálózati átjáró útvonalpropagálását** a megfelelő útvonalak leválasztásához a helyszíni hálózathoz.

![Virtuális hálózati átjáró útvonalának propagálása](media/forced-tunneling/route-propagation.png)

Miután konfigurálta az Azure Firewall-t a kényszerített bújtatás támogatására, nem vonhatja vissza a konfigurációt. Ha eltávolítja az összes többi IP-konfigurációt a tűzfalon, a felügyeleti IP-konfiguráció is törlődik, és a tűzfal felvan osztva. A felügyeleti IP-konfigurációhoz rendelt nyilvános IP-cím nem távolítható el, de másik nyilvános IP-címet rendelhet hozzá.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az Azure Tűzfal telepítése és konfigurálása hibrid hálózatban az Azure Portal használatával](tutorial-hybrid-portal.md)