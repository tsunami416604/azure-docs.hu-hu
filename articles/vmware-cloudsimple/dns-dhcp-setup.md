---
title: Azure VMware-megoldás a CloudSimple-től – DNS és DHCP számítási feladatok beállítása a magánfelhőhöz
description: A DNS és DHCP beállítása a CloudSimple private cloud környezetben futó alkalmazásokhoz és számítási feladatokhoz
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024687"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>DNS- és DHCP-alkalmazások és számítási feladatok beállítása a CloudSimple magánfelhőben

A magánfelhő-környezetben futó alkalmazások és munkaterhelések névfeloldást és DHCP-szolgáltatásokat igényelnek a keresgéléshez és az IP-címhozzárendeléshez.  A szolgáltatások biztosításához megfelelő DHCP- és DNS-infrastruktúrára van szükség.  Konfigurálhatja a virtuális gépet, hogy ezeket a szolgáltatásokat a privát felhőkörnyezetben.  

## <a name="prerequisites"></a>Előfeltételek

* Elosztott portcsoport VLAN-nal konfigurálva
* A telepítés irányítása a helyszíni vagy az internetalapú DNS-kiszolgálókra
* Virtuálisgép-sablon vagy ISO virtuális gép létrehozásához

## <a name="linux-based-dns-server-setup"></a>Linux-alapú DNS-kiszolgáló beállítása

A Linux különböző csomagokat kínál a DNS-kiszolgálók beállításához.  Íme egy [példa a DigitalOcean beállítására,](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) amely egy nyílt forráskódú BIND DNS-kiszolgáló beállítására vonatkozó utasításokat tartalmaz.

## <a name="windows-based-setup"></a>Windows-alapú telepítés

Ezek a Microsoft-témakörök azt ismertetik, hogyan állítható be a Windows-kiszolgáló DNS-kiszolgálóként és DHCP-kiszolgálóként.

* [Windows Server DNS-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server DHCP-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
