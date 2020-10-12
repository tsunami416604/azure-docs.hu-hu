---
title: Azure VMware-megoldás CloudSimple – a számítási feladatok DNS-és DHCP-beállítása a privát felhőhöz
description: Ismerteti, hogyan lehet DNS-t és DHCP-t beállítani a CloudSimple saját felhőalapú környezetében futó alkalmazások és munkaterhelések számára
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7531a22d5b0f44fdaae837a75d17ea18852acf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077295"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>DNS-és DHCP-alkalmazások és-munkaterhelések beállítása a saját CloudSimple-felhőben

A privát felhőalapú környezetekben futó alkalmazások és munkaterhelések névfeloldást és DHCP-szolgáltatásokat igényelnek a keresési és IP-címek hozzárendeléséhez.  Ezeknek a szolgáltatásoknak a biztosításához megfelelő DHCP-és DNS-infrastruktúra szükséges.  A virtuális gépeket konfigurálhatja úgy, hogy biztosítsa ezeket a szolgáltatásokat a saját felhőalapú környezetében.  

## <a name="prerequisites"></a>Előfeltételek

* Elosztott porttartomány konfigurált VLAN-val
* A beállítás átirányítása helyszíni vagy internetalapú DNS-kiszolgálókra
* Virtuálisgép-sablon vagy ISO virtuális gép létrehozásához

## <a name="linux-based-dns-server-setup"></a>Linux-alapú DNS-kiszolgáló beállítása

A Linux különböző csomagokat kínál a DNS-kiszolgálók beállításához.  Íme egy [példa a DigitalOcean-ről](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) , amely egy nyílt forráskódú kötési DNS-kiszolgáló beállítására vonatkozó utasításokat tartalmaz.

## <a name="windows-based-setup"></a>Windows-alapú telepítés

Ezek a Microsoft-témakörök leírják, hogyan állíthat be egy Windows-kiszolgálót DNS-kiszolgálóként és DHCP-kiszolgálóként.

* [Windows Server DNS-kiszolgálóként](/windows-server/networking/dns/dns-top)
* [Windows Server DHCP-kiszolgálóként](/windows-server/networking/technologies/dhcp/dhcp-top)
