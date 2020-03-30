---
title: Azure VMware-megoldás a CloudSimple által – nyilvános IP-címek lefoglalása
description: Ez a témakör azt ismerteti, hogy miként foglalhatja le a nyilvános IP-címeket a virtuális gépekhez a magánfelhő-környezetben
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024296"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Nyilvános IP-címek lefoglalása privát felhőalapú környezethez

Nyissa meg a Nyilvános IP-címek lapot a Hálózat lapon, és rendeljen nyilvános IP-címeket a virtuális gépekhez a privát felhőbeli környezetben.

1. Az oldalsó menüben [elérheti a CloudSimple portált,](access-cloudsimple-portal.md) és válassza a **Hálózat** lehetőséget.
2. Válassza a **Nyilvános IP-eket**.
3. Kattintson **az Új nyilvános IP gombra.**

    ![Nyilvános IP-oldalak](media/public-ips-page.png)

4. Adjon meg egy nevet az IP-címbejegyzés azonosításához.
5. Tartsa meg az alapértelmezett helyet.
6. A csúszkával szükség esetén módosíthatja az tétlen időkimenőt.
7. Adja meg azt a helyi IP-címet, amelyhez nyilvános IP-címet kíván rendelni.
8. Adjon meg egy társított DNS-nevet.
9. Kattintson a **Küldés gombra.**

![Nyilvános IP-k kiosztása](media/network-public-ip-allocate.png)

Megkezdődik a nyilvános IP-cím felosztása. A tevékenység állapotát a Tevékenység **> tevékenységek** lapon ellenőrizheti. Ha a foglalás befejeződött, az új bejegyzés megjelenik a Nyilvános IP-oldalakon.
