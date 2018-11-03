---
title: Az Azure Virtual Machines elérése a Server Explorer |} A Microsoft Docs
description: Get létrehozása és kezelése az Azure virtuális gépeken (VM) a Visual Studio Server Explorer megtekintése áttekintését.
services: visual-studio-online
author: ghogen
manager: douge
assetId: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/31/2017
ms.author: ghogen
ms.openlocfilehash: d25e79efa496d27d16c4c10c587ba25f25507455
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969411"
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Az Azure Virtual Machines elérése a Server Explorerből

Ha az Azure által üzemeltetett virtuális gépek, a Server Explorerben elérheti őket. Először jelentkezzen be az Azure-előfizetéshez a mobilszolgáltatások megtekintéséhez. Jelentkezzen be, a Server Explorerben nyissa meg a helyi menü, az Azure-csomópont számára, és válassza **csatlakozás a Microsoft Azure**.

1. A Cloud Explorerben válassza ki a virtuális gépet, és válassza a Tulajdonságok ablak megjelenítése a F4 billentyű.

    Az alábbi táblázat bemutatja, milyen tulajdonságok érhetőek el, de azok az összes csak olvasható. Őket módosításához használja a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

   | Tulajdonság | Leírás |
   | --- | --- |
   | DNS-név |Az a virtuális gép azon URL-CÍMÉT. |
   | Környezet |Virtuális gép esetén ez a tulajdonság értéke mindig éles környezetben. |
   | Name (Név) |A virtuális gép neve. |
   | Méret |A méret a virtuális gép, amely a rendelkezésre álló szabad memória és lemezterület mennyiségét tükrözi. További információkért lásd: [virtuálisgép-méretek](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | status |Értékek: indítás, elindítva, leállítása, Stopped és állapotának beolvasása. Állapot beolvasása jelenik meg, ha a jelenlegi állapot nem ismert. Ez a tulajdonság az értékek különböznek az értékeket, amelyeket a rendszer a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | SubscriptionID |Az Azure-fiók előfizetés-azonosítója. Ezt az információt a megjelenítheti a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) előfizetés tulajdonságainak megtekintésével. |
2. Válasszon egy végpont csomópont, és nézze meg a **tulajdonságok** ablak.
3. A következő táblázat ismerteti az elérhető tulajdonságok végpontok, de csak olvasható. Hozzáadása vagy szerkesztése a végpontok egy virtuális gép használja a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). 

   | Tulajdonság | Leírás |
   | --- | --- |
   | Name (Név) |A végpont-azonosítója. |
   | Magánhálózati Port |A port, az alkalmazás belső hálózati hozzáférést. |
   | Protokoll |A protokoll, amely a szállítási réteg esetében ezt a végpontot használja, TCP vagy UDP. |
   | Nyilvános port |A nyilvánosan elérhető az alkalmazáshoz használt port. |
