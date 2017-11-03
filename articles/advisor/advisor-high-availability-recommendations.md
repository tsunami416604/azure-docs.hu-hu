---
title: "Az Azure Advisor magas rendelkezésre állású javaslatok |} Microsoft Docs"
description: "Azure Advisor használata az Azure-környezetekhez magas rendelkezésre állásának javítása érdekében."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b63de2453180e562596c211d40cebe85b95bd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-high-availability-recommendations"></a>Magas rendelkezésre állású javaslatokat biztosít

Az Azure Advisor segítségével győződjön meg arról, és az üzleti szempontból kritikus fontosságú alkalmazások folytonosságának javításához. Advisor a magas rendelkezésre állású javaslatok beszerezheti a **magas rendelkezésre állású** az Advisor irányítópult.

![Az Advisor irányítópult magas rendelkezésre állás gomb](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>Virtuális gép a hibatűrés érdekében

Az Advisor azonosítja a virtuális gépek, amelyek nem részei egy rendelkezésre állási csoportot és helyezi át a rendelkezésre állási csoportok javasolja. Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel-e a virtuális gép az Azure SLA-t. Kiválaszthatja a rendelkezésre állási készlet a virtuális gép létrehozásához vagy a virtuális gép hozzáadása egy meglévő rendelkezésre állási csoportot.

> [!NOTE]
> Ha egy rendelkezésre állási csoport létrehozása mellett dönt, hozzá kell adnia legalább egy további virtuális gép bele. Azt javasoljuk, hogy Ön csoporthoz két vagy több virtuális gép rendelkezésre állási beállítása annak érdekében, hogy legalább egy gép érhető kimaradás során.

![Az Advisor javaslat: A virtuális gép redundancia, használja a rendelkezésre állási csoportok](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>Ellenőrizze a rendelkezésre állási csoport hibatűrés 

Az Advisor tartalmazhat egyetlen virtuális gép rendelkezésre állási készleteket azonosítja, és azt javasolja, hogy egy vagy több virtuális gépek hozzáadása. Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel-e a virtuális gép az Azure SLA-t. Választhat, vagy hozzon létre egy virtuális gépet vagy egy már meglévő virtuális gép, és adja hozzá a rendelkezésre állási csoportot.  

![Az Advisor javaslat: egy vagy több virtuális gépek felvétele a rendelkezésre állási csoport](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>Ellenőrizze az alkalmazás átjáró hibatűrés
Kritikus fontosságú alkalmazások alkalmazás-átjárók vannak kapcsolva, az üzletmenet folytonosságának biztosításához, Advisor azonosítja alkalmazás példányai, amelyek nincsenek beállítva a hibatűrés érdekében, és javaslatot tesz, a szervizelési műveletek is. Az Advisor azonosítja közepes vagy nagy Egypéldányos alkalmazás átjárók, és hozzáadja legalább egy további példányt javasol. Egy vagy több instance kis alkalmazásátjárót azonosítja és a közepes vagy nagy termékváltozatok történő javasolja. Ezek a műveletek ellenőrizze, hogy az átjáró alkalmazáspéldányok megfelelnek a jelenlegi SLA ezekhez az erőforrásokhoz tartozó javasol.

![Az Advisor javaslat: két vagy több közepes vagy nagy méretű kérelem átjáró példányok telepítése](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>A teljesítmény és a virtuális gépek lemezeit megbízhatóságának javítása

Az Advisor azonosítja a virtuális gépek a standard lemezek és frissítése a premium lemezek javasolja.
 
Prémium szintű Storage nagy teljesítményű, alacsony késésű lemez I/O-igényes munkaterhelések futó virtuális gépek támogatása nyújt. Prémium szintű storage-fiókok használó virtuálisgép-lemezek SSD-meghajtót (SSD) adatait tárolják. A legjobb teljesítmény érdekében az alkalmazáshoz azt javasoljuk, hogy az áttelepített a prémium szintű Storage magas IOPS igénylő virtuális gép lemezei. 

A lemezek nem igényelnek magas iops értéket, ha szabványos Storage fenntartásuk korlátozhatja költségeket. Standard szintű tárolást virtuálisgép-lemez adatokat tárolja a (merevlemezes HDD) meghajtók SSD-k helyett. Ha szeretné, telepítse át a virtuális gépek lemezeit a prémium szintű lemezekhez. A legtöbb virtuális gép termékváltozatok Premium lemezek támogatottak. Azonban néhány esetben, ha szeretné használni a premium lemezek esetén szükség lehet a virtuális gép verziófrissítése termékváltozatok is.

![Az Advisor javaslat: a prémium szintű lemezekhez frissítse a virtuális gépek lemezeit megbízhatóságának javítása](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>A virtuális gép adatainak véletlen törlés elleni védelme
Az Advisor azonosítja a virtuális gépek, ahol a biztonsági mentés nem engedélyezett, és azt a biztonsági mentés engedélyezését javasolja. Virtuális gép biztonsági mentése beállításával biztosítja az üzleti szempontból kritikus fontosságú adatok rendelkezésre állását, és véletlen törlés vagy -sérülés elleni védelmet nyújt.

![Az Advisor javaslat: konfigurálja a virtuális gép biztonsági mentése a kritikus fontosságú adatok védelme](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>Hozzáférés magas rendelkezésre állású javaslatok Advisor

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Kattintson a bal oldali ablaktáblában **további szolgáltatások**.

3. A szolgáltatás menü ablaktáblán alatt **figyelés és felügyelet**, kattintson a **Azure Advisor**.  
 Az Advisor irányítópult jelenik meg.

4. Az Advisor irányítópultján kattintson a **magas rendelkezésre állású** lapot, és válassza ki az ajánlások fogadni kívánt előfizetést.

> [!NOTE]
> Advisor-javaslatokra szeretne használni, először *az előfizetés regisztrálása* az Advisor szolgáltatásban. Egy előfizetés regisztrálva amikor egy *előfizetés tulajdonosának* elindítja az Advisor irányítópulton, és rákattint a **javaslatok beszerzése** gombra. Ez egy *egyszeri művelet*. Az előfizetés regisztrálása után érheti el, az Advisor-javaslatokra *tulajdonos*, *közreműködő*, vagy *olvasó* előfizetés, egy erőforráscsoport vagy egy adott erőforrás.

## <a name="next-steps"></a>Következő lépések

Advisor-javaslatokra kapcsolatos további információkért lásd:
* [Bevezetés az Azure Advisor](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Költség javaslatokat biztosít](advisor-performance-recommendations.md)
* [Teljesítmény javaslatokat biztosít](advisor-performance-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-security-recommendations.md)

