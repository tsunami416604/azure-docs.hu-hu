---
title: Azure VMware-megoldások (AVS) – a számítási feladatok DNS-és DHCP-beállítása az AVS Private Cloud szolgáltatáshoz
description: Ismerteti, hogyan lehet DNS-t és DHCP-t beállítani az AVS saját felhőalapú környezetében futó alkalmazások és munkaterhelések számára
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024687"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>DNS-és DHCP-alkalmazások és-munkaterhelések beállítása az AVS Private Cloud-ban

Az AVS Private Cloud Environment-környezetekben futó alkalmazások és munkaterhelések névfeloldást és DHCP-szolgáltatásokat igényelnek a kereséshez és az IP-címek kiosztásához. Ezeknek a szolgáltatásoknak a biztosításához megfelelő DHCP-és DNS-infrastruktúra szükséges. A virtuális gépeket konfigurálhatja úgy, hogy ezeket a szolgáltatásokat az AVS saját felhőalapú környezetében adja meg. 

## <a name="prerequisites"></a>Előfeltételek

* Elosztott porttartomány konfigurált VLAN-val
* A beállítás átirányítása helyszíni vagy internetalapú DNS-kiszolgálókra
* Virtuálisgép-sablon vagy ISO virtuális gép létrehozásához

## <a name="linux-based-dns-server-setup"></a>Linux-alapú DNS-kiszolgáló beállítása

A Linux különböző csomagokat kínál a DNS-kiszolgálók beállításához. Íme egy [példa a DigitalOcean-ről](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) , amely egy nyílt forráskódú kötési DNS-kiszolgáló beállítására vonatkozó utasításokat tartalmaz.

## <a name="windows-based-setup"></a>Windows-alapú telepítés

Ezek a Microsoft-témakörök leírják, hogyan állíthat be egy Windows-kiszolgálót DNS-kiszolgálóként és DHCP-kiszolgálóként.

* [Windows Server DNS-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server DHCP-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
