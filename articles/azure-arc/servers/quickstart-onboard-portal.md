---
title: Gyors útmutató – számítógépek összekötése az Azure-ba az Azure arc for Servers használatával – portál
description: Ebből a rövid útmutatóból megtudhatja, hogyan csatlakoztathatók a gépek az Azure-hoz a portálon található kiszolgálókhoz készült Azure arc használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, a kívánt állapot konfigurálása, frissítés kezelése, változások követése, leltár, runbookok, Python, grafikus, hibrid, előkészítés
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: b014f6015b3e13a603cf3893062bd0463eb110ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501987"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Gyors útmutató: számítógépek összekötése az Azure-ban az Azure arc for Servers használatával – portál

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Tekintse át a támogatott ügyfeleket és a szükséges hálózati konfigurációt az [Azure arc for Servers áttekintése című témakörben](overview.md).

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Az ügynök telepítési parancsfájljának létrehozása a Azure Portal használatával

1. Indítás [https://aka.ms/hybridmachineportal] [aka_hybridmachineportal]
1. Kattintson a **+ Hozzáadás** gombra
1. Kövesse a varázslót a befejezéshez
1. Az utolsó oldalon egy parancsfájl lett létrehozva, amelyet másolhat (vagy letöltheti).

A parancsfájlt a csatlakozni kívánt célszámítógépen kell futtatni. Letölti az ügynököt, telepíti, és egyetlen műveletként csatlakoztatja a gépet.

A felügyelni kívánt nem Azure-kiszolgálókon:

1. Bejelentkezés a kiszolgálóra (SSH, RDP vagy PowerShell távelérés használatával)
1. Rendszerhéj elindítása: bash Linuxon, PowerShell rendszergazdaként Windows rendszeren
1. Illessze be a szkriptet a portálról, és hajtsa végre a kiszolgálón az Azure-hoz való csatlakozáshoz.
1. Az egyes kiszolgálók előkészítésének alapértelmezett hitelesítése az Azure Device bejelentkezhessen használatával *interaktív* . A szkript futtatásakor a következőhöz hasonló üzenet jelenik meg:

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   Nyisson meg egy böngészőt, és adja meg a hitelesíteni kívánt kódot. A böngészőnek nem kell futnia az előkészítés alatt lévő kiszolgálón, lehet, hogy egy másik számítógépen, például a laptopon fut.

1. Ha nem interaktív módon szeretné hitelesíteni a hitelesítést, kövesse az [egyszerű szolgáltatás létrehozása](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) és a portálon létrehozott parancsfájl módosítása című témakör lépéseit.

> [!NOTE]
> Ha először használja az Internet Explorert a kiszolgálón a bejelentkezéshez, a rendszer hibát jelez. Egyszerűen nyissa meg újra a böngészőt, és ismételje meg a műveletet.

## <a name="execute-the-script-on-target-nodes"></a>A parancsfájl végrehajtása a cél csomópontokon

Jelentkezzen be az egyes csomópontokra, és hajtsa végre a portálon létrehozott parancsfájlt. A parancsfájl sikeres befejeződése után lépjen a Azure Portal ellenőrizze, hogy a kiszolgáló sikeresen csatlakoztatva van-e.

![Sikeres előkészítés](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A gépek Azure arc-kiszolgálókról történő leválasztásához két lépést kell végrehajtania.

1. Válassza ki a gépet a [portálon](https://aka.ms/hybridmachineportal), kattintson a három pontra (`...`), majd válassza a **Törlés**lehetőséget.
1. Távolítsa el az ügynököt a gépről.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szabályzat társítása csatlakoztatott gépekhez](../../governance/policy/assign-policy-portal.md)