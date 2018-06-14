---
title: Az Azure virtuális gépek használata a Server Explorer |} Microsoft Docs
description: Get megtekintése létrehozása és kezelése az Azure virtuális gépek (VM) a Visual Studio Server Explorer.
services: visual-studio-online
author: ghogen
manager: douge
assetId: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/31/2017
ms.author: ghogen
ms.openlocfilehash: a19f33c4dd2654538c5718d2cd7dbe5d018e4de1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792885"
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>A Server Explorer Azure virtuális gépek elérése

Ha az Azure-ban üzemeltetett virtuális gépeket, a Server Explorer is elérhet. Először be kell jelentkeznie Azure-előfizetése a mobile services megtekintéséhez. Jelentkezzen be, a Server Explorer nyissa meg a helyi menü az Azure csomóponthoz, és válassza **kapcsolódás a Microsoft Azure**.

1. Cloud Explorerben válassza ki a virtuális gépet, és válassza a Tulajdonságok ablak megjelenítése F4 billentyűt.

    Az alábbi táblázat bemutatja, milyen tulajdonságok érhetőek el, de összes csak olvasható. Ha módosítani szeretné azokat, használja a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040).

   | Tulajdonság | Leírás |
   | --- | --- |
   | DNS-név |A virtuális gép internetcímét URL-CÍMÉT. |
   | Környezet |A virtuális gép esetén ez a tulajdonság értéke mindig éles. |
   | Name (Név) |A virtuális gép neve. |
   | Méret |A virtuális gép, amely tükrözi a rendelkezésre álló szabad memória és lemezterület mennyisége mérete. További információkért lásd: [virtuálisgép-méretek](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | status |Értékek: indítása, elindítva, leállítása, leállítva vagy Állapot lekérése során. Ha beolvasása állapot jelenik meg, a jelenlegi állapot: ismeretlen. Ez a tulajdonság értékek eltérnek az értékeket, amelyeket a rendszer a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | SubscriptionID |Az Azure-fiók előfizetés-azonosító. Ezt az információt is megjeleníthetők a a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040) egy előfizetés tulajdonságai között megtekintésével. |
2. Válasszon egy végpont csomópont, és nézze meg a **tulajdonságok** ablak.
3. A következő táblázat ismerteti a rendelkezésre álló tulajdonságok végpontok, de csak olvasható. Adja hozzá vagy szerkesztheti a végpontokat a virtuális gép használja a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040). 

   | Tulajdonság | Leírás |
   | --- | --- |
   | Name (Név) |A végpont azonosítója. |
   | Magánhálózati Port |A port, az alkalmazás belső hálózati hozzáféréshez. |
   | Protokoll |A protokoll által használt a szállítási réteg ezen a végponton, TCP vagy UDP. |
   | Nyilvános port |Az alkalmazás nyilvánosan elérhető használt port. |
