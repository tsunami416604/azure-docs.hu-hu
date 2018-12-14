---
title: Az Azure Security Center és Linux rendszerű Azure-beli virtuális gépek | Microsoft Azure
description: Ebből a dokumentumból megismerheti, hogyan védi az Azure Security Center az Azure-beli virtuális gépeket.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 5fe5a12c-5d25-430c-9d47-df9438b1d7c5
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: yurid
ms.openlocfilehash: 5ec77d7bcc3715946c4574bca17f280a9f483b45
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340948"
---
# <a name="azure-security-center-and-azure-virtual-machines-with-linux"></a>Az Azure Security Center és Linux rendszerű Azure-beli virtuális gépek
[Az Azure Security Center](https://azure.microsoft.com/services/security-center/) lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Ez a cikk azt ismerteti, hogyan segíthet a Security Center a Linuxot futtató Azure-beli virtuális gépek (VM) védelmében.

## <a name="why-use-security-center"></a>Miért használja a Security Centert?
A Security Center segít megóvni az Azure-beli virtuális gépeken tárolt adatokat azáltal, hogy betekintést nyújt a virtuális gép biztonsági beállításaiba, és nyomon követi az esetleges fenyegetéseket. A Security Center a következőket tudja megfigyelés alatt tartani a virtuális gépeken: 

* Az operációs rendszer (OS) biztonsági beállításai ajánlott konfigurációs szabályokkal
* Rendszerbiztonság és a hiányzó kritikus frissítések
* Az Endpoint Protection javaslatai
* Lemeztitkosítás ellenőrzése
* Hálózatalapú támadások (csak a [standard verzióban](https://azure.microsoft.com/pricing/details/security-center/) érhető el)

A Security Center az Azure-beli virtuális gépek védelme mellett a Cloud Services, az App Services, a Virtual Networks és mások biztonsági felügyeletében és kezelésében is segítséget nyújt. 

> [!NOTE]
> Az Azure Security Centerrel kapcsolatos további információkért olvassa el [Az Azure Security Center bemutatása](security-center-intro.md)című témakört.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az Azure Security Centert használatának elkezdése előtt tudnia kell és meg kell fontolnia a következőket:

* Szüksége lesz egy Microsoft Azure-előfizetésre. A Security Center ingyenes és standard csomagjairól a [ Security Center díjszabását](https://azure.microsoft.com/pricing/details/security-center/) ismertető oldalon talál további információt.
* Tervezze meg a Security Center bevezetését az[Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) című dokumentum segítségével, amely további információkat nyújt a tervezés és működtetés szempontjairól.
* További információ a támogatott operációs rendszerekről: [Azure Security Centerhez kapcsolódó gyakori kérdések (GYIK)](security-center-faq.md). 

## <a name="set-security-policy"></a>Biztonsági házirend beállítása
Engedélyezze az adatgyűjtést, hogy az Azure Security Center összegyűjthesse a szükséges információkat a beállított biztonsági házirend alapján létrehozott javaslatok és riasztások biztosításához. Az alábbi ábrán látható, hogy az **Adatgyűjtés** állapota **Be** van kapcsolva.

A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az egy adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol. A biztonsági házirend engedélyezése előtt engedélyeznie kell az adatgyűjtést. A Security Center összegyűjti az adatokat a virtuális gépekről a biztonsági állapotuk értékeléséhez, a biztonsági javaslatok létrehozásához és a fenyegetésekkel kapcsolatos riasztásokhoz. A Security Centerben a vállalata biztonsági igényeinek és az egyes előfizetésekben szereplő alkalmazások típusának vagy az adatok bizalmasságának megfelelően határozhatja meg az Azure-előfizetésekre vagy -erőforráscsoportokra vonatkozó házirendeket. 

![Biztonsági házirend](./media/security-center-linux-virtual-machine/security-center-linux-virtual-machine-fig1.png)

> [!NOTE]
> További információ az egyes elérhető **Megelőzési házirendekről**: [Biztonsági házirendek beállítása](tutorial-security-policy.md).
> 

## <a name="manage-security-recommendations"></a>Biztonsági javaslatok kezelése
A Security Center elemzi az Azure-erőforrások biztonsági állapotát. A Security Center javaslatokat hoz létre, amikor lehetséges biztonsági réseket észlel. A javaslatok végigvezetik Önt a szükséges vezérlők konfigurálásának folyamatán.

Miután beállított egy biztonsági házirendet, a Security Center elemzi az Ön erőforrásainak állapotát, hogy észlelhesse a potenciális biztonsági réseket. A javaslatok egy tábla formájában jelennek meg, amelyben minden egyes sor egy adott javaslatot tartalmaz. Az alábbi táblázat néhány, a Linuxot futtató Azure virtuális gépekre vonatkozó javaslatot, valamint alkalmazásuk esetén a működésük leírását tartalmazza. Egy javaslat kiválasztásakor megjelennek annak a Security Centerben történő megvalósításához kapcsolódó információk.

| Ajánlás | Leírás |
| --- | --- |
| [Adatgyűjtés engedélyezése az előfizetések számára](security-center-enable-data-collection.md) |Javasolja, hogy minden egyes előfizetés és virtuális gép (VM) esetében kapcsolja be az adatgyűjtést az előfizetéseinek biztonsági házirendjében. |
| [Operációs rendszerek sebezhetőségeinek javítása](security-center-remediate-os-vulnerabilities.md) |Javasolja, hogy az operációs rendszer konfigurálását az ajánlott konfigurációs szabályok szerint végezze, például ne engedélyezze a jelszavak mentését. |
| [Rendszerfrissítések alkalmazása](security-center-apply-system-updates.md) |Javasolja, hogy végezze el a hiányzó rendszerbiztonsági és kritikus frissítések központi telepítését a virtuális gépeken. |
| [Rendszerfrissítések utáni újraindítás](security-center-apply-system-updates.md#reboot-after-system-updates) |Javasolja, hogy a rendszerfrissítések alkalmazási folyamatának befejezéséhez indítson újra egy virtuális gépet. |
| [Virtuálisgép-ügynök engedélyezése](security-center-enable-vm-agent.md) |Lehetővé teszi a virtuálisgép-ügynök alkalmazását igénylő virtuális gépek megtekintését. A virtuálisgép-ügynöknek telepítve kell lennie a virtuális gépeken a javítás- és alapkonfiguráció-keresés, valamint a kártevőirtó programok üzembe helyezéséhez. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök telepítéséről a [Virtuális gép-ügynök és -bővítmények – 2. rész](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) cikkben talál információkat. |
| [Lemeztitkosítás alkalmazása](security-center-apply-disk-encryption.md) |Javasolja, hogy végezze el a virtuális gép titkosítását az Azure Disk Encryption használatával (Windows és Linux rendszerű virtuális gépek esetében). A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen. |


> [!NOTE]
> A javaslatokról további információkat a [Biztonsági javaslatok kezelése](security-center-recommendations.md) cikkben talál.
> 

## <a name="monitor-security-health"></a>A biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](tutorial-security-policy.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat.  A **Resource Security Health** (Erőforrás biztonsági állapota) panelen megtekintheti az erőforrások biztonsági állapotát, valamint az esetleg felmerülő problémákat. A **Resources security health** (Erőforrás biztonsági állapota) csempe **Virtual machines** (Virtuális gépek) elemére kattintva megnyithatja a **Virtual machines** (Virtuális gépek) panelt, amelyen megtekintheti a virtuális gépekkel kapcsolatos javaslatokat. 

![Biztonsági állapot](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>A biztonsági riasztások kezelése és a riasztásokra való válaszadás
A valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében a Security Center automatikusan elvégzi a naplóadatok összegyűjtését, elemzését és integrálását az Azure-erőforrásokról, a hálózatról és a csatlakoztatott partnermegoldásokból (például tűzfalról és Endpoint Protection-megoldásokból). A Security Center számos [észlelési képesség](security-center-detection-capabilities.md) együttesének használatával képes a rangsorolt biztonsági riasztások létrehozására, amelyek segítségével gyorsan megtörténhet a problémák kivizsgálása, valamint a lehetséges támadások következményeinek javítására vonatkozó javaslattétel.

![Biztonsági riasztások](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Válasszon ki egy biztonsági riasztást, ha többet szeretne megtudni a riasztást kiváltó esemény(ek)ről, és arról, hogy milyen lépéseket kell tennie a támadás elhárítása érdekében. A biztonsági riasztások [típus](security-center-alerts-type.md) és dátum szerint vannak csoportosítva.

## <a name="monitor-security-health"></a>A biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](tutorial-security-policy.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat.  A **Resource Security Health** (Erőforrás biztonsági állapota) panelen megtekintheti az erőforrások biztonsági állapotát, valamint az esetleg felmerülő problémákat. A **Resources security health** (Erőforrás biztonsági állapota) csempe **Virtual machines** (Virtuális gépek) elemére kattintva megnyithatja a **Virtual machines** (Virtuális gépek) panelt, amelyen megtekintheti a virtuális gépekkel kapcsolatos javaslatokat. 

![Biztonsági állapot](./media/security-center-linux-virtual-machine/security-center-linux-virtual-machine-fig4.png)

Ha a javaslatra kattint, további részleteket tudhat meg az adott, a probléma megoldására szolgáló műveletekről. A részletek a panel alsó részén, a **Recommendations** (Javaslatok) alatt jelennek meg. 

![Biztonsági állapot, 2](./media/security-center-linux-virtual-machine/security-center-linux-virtual-machine-fig5.png)


## <a name="see-also"></a>Lásd még
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.

