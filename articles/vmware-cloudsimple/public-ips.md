---
title: Azure VMware-megoldások (AVS) – nyilvános IP-címek lefoglalása
description: Ismerteti, hogyan lehet nyilvános IP-címeket lefoglalni a virtuális gépek számára az AVS Private Cloud Environment-ban
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87133f5efb9f096d3fdb0956aab1caac58b4bd94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024296"
---
# <a name="allocate-public-ip-addresses-for-avs-private-cloud-environment"></a>Nyilvános IP-címek lefoglalása az AVS Private Cloud Environment számára

Nyissa meg a nyilvános IP-címek fület a hálózat lapon, hogy nyilvános IP-címeket foglaljon le a virtuális gépek számára az AVS Private Cloud-környezetben.

1. [Nyissa meg az AVS-portált](access-cloudsimple-portal.md) , és válassza a **hálózat** lehetőséget az oldalsó menüben.
2. Válassza a **nyilvános IP**-címek lehetőséget.
3. Kattintson az **új nyilvános IP-cím**elemre.

    ![Nyilvános IP-címek lap](media/public-ips-page.png)

4. Adja meg az IP-cím bejegyzését azonosító nevet.
5. Tartsa meg az alapértelmezett helyet.
6. Ha szükséges, használja a csúszkát az Üresjárati időkorlát módosításához.
7. Adja meg azt a helyi IP-címet, amelyhez nyilvános IP-címet szeretne hozzárendelni.
8. Adjon meg egy társított DNS-nevet.
9. Kattintson a **Submit (Küldés**) gombra.

![Nyilvános IP-címek lefoglalása](media/network-public-ip-allocate.png)

A nyilvános IP-cím lefoglalásának feladata megkezdődik. A feladat állapotát a **tevékenység > feladatok** oldalon tekintheti meg. A foglalás befejezésekor az új bejegyzés megjelenik a nyilvános IP-címek lapon.
