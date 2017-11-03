---
title: "Az Azure virtuális gépek használata a Server Explorer |} Microsoft Docs"
description: "Get megtekintése létrehozása és kezelése az Azure virtuális gépek (VM) a Visual Studio Server Explorer."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/31/2017
ms.author: kraigb
ms.openlocfilehash: 75dcc603327b50718b279f3ce055663ec0bc2596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>A Server Explorer Azure virtuális gépek elérése
A Visual Studio Server Explorer használatával jelenítheti meg információkat az Azure által futtatott virtuális gépek kapcsolatban.

## <a name="accessing-virtual-machines-in-server-explorer"></a>A Server Explorer virtuális gépek elérése
Ha az Azure-ban üzemeltetett virtuális gépeket, a Server Explorer is elérhet. Először be kell jelentkeznie Azure-előfizetése a mobile services megtekintéséhez. Jelentkezzen be, a Server Explorer nyissa meg a helyi menü az Azure csomóponthoz, és válassza **kapcsolódás a Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>A virtuális gépek kapcsolatos adatok
1. Cloud Explorerben válassza ki a virtuális gépet, és válassza a Tulajdonságok ablak megjelenítése F4 billentyűt.
   
    Az alábbi táblázat bemutatja, milyen tulajdonságok érhetőek el, de összes csak olvasható. Ha módosítani szeretné azokat, használja a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   
   | Tulajdonság | Leírás |
   | --- | --- |
   | DNS-név |A virtuális gép internetcímét URL-CÍMÉT. |
   | Környezet |A virtuális gép esetén ez a tulajdonság értéke mindig éles. |
   | Név |A virtuális gép neve. |
   | Méret |A virtuális gép, amely tükrözi a rendelkezésre álló szabad memória és lemezterület mennyisége mérete. További információkért lásd: [virtuálisgép-méretek](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | status |Értékek: indítása, elindítva, leállítása, leállítva vagy Állapot lekérése során. Ha beolvasása állapot jelenik meg, a jelenlegi állapot: ismeretlen. Ez a tulajdonság értékek eltérnek az értékeket, amelyeket a rendszer a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | Előfizetés-azonosító |Az Azure-fiók előfizetés-azonosító. Ezt az információt is megjeleníthetők a a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040) egy előfizetés tulajdonságai között megtekintésével. |
2. Válasszon egy végpont csomópont, és nézze meg a **tulajdonságok** ablak.
3. A következő táblázat ismerteti a rendelkezésre álló tulajdonságok végpontok, de csak olvasható. Adja hozzá vagy szerkesztheti a végpontokat a virtuális gép használja a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
   
   | Tulajdonság | Leírás |
   | --- | --- |
   | Név |A végpont azonosítója. |
   | Magánhálózati Port |A port, az alkalmazás belső hálózati hozzáféréshez. |
   | Protokoll |A protokoll által használt a szállítási réteg ezen a végponton, TCP vagy UDP. |
   | Nyilvános port |Az alkalmazás nyilvánosan elérhető használt port. |

## <a name="next-steps"></a>Következő lépések
A Visual Studio Azure szerepkörök használatával kapcsolatos további tudnivalókért lásd: [a távoli asztal Azure szerepkörök](vs-azure-tools-remote-desktop-roles.md).

