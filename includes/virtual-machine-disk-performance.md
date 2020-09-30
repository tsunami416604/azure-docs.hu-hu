---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 9f5a1010959658e75dcc809b2ee1d6d9222af056
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540010"
---
Ez a cikk segít a lemezek teljesítményének és működésének tisztázásában az Azure Virtual Machines és az Azure-lemezek kombinálásával. Azt is leírja, hogyan diagnosztizálhatja a lemez i/o-sebességének szűk keresztmetszeteit, és hogy milyen módosításokat végezhet a teljesítmény optimalizálása érdekében.

## <a name="how-does-disk-performance-work"></a>Hogyan működik a lemez teljesítménye?
Az Azure-beli virtuális gépek a virtuális gép típusa és mérete alapján IOPS és teljesítménybeli teljesítménnyel rendelkeznek. A virtuális gépekhez csatlakoztatható operációsrendszer-lemezek és adatlemezek saját IOPs és átviteli korláttal rendelkeznek. Ha a virtuális gépeken futó alkalmazás több IOPS vagy átviteli sebességet igényel, mint a virtuális gép vagy a csatlakoztatott lemezek esetében, az alkalmazás teljesítménye túllépi a korlátot. Ebben az esetben az alkalmazás az optimálisnál rosszabb teljesítményt fog tapasztalni, és bizonyos negatív következményekhez vezethet, például nagyobb késéssel. Néhány példát láthatunk, hogy megszilárdítsuk ezt. Ahhoz, hogy ezeket a példákat könnyen követni lehessen, csak a IOPs tekintjük meg, de ugyanez a logika is érvényes az átviteli sebességre.

## <a name="disk-io-capping"></a>Lemez i/o-korlátja
Beállítás:
- Standard_D8s_v3 
    - Nem gyorsítótárazott IOPS: 12 800
- E30 operációsrendszer-lemez
    - IOPS: 500 
- 2 E30 adatlemez
    - IOPS: 500

![A lemez szintjének maximális korlátja](media/vm-disk-performance/disk-level-throttling.jpg)

A virtuális gépen futó alkalmazás olyan kérést tesz elérhetővé, amelyhez 10 000 IOPs szükséges a virtuális géphez. A virtuális gép mindegyike számára engedélyezett, mert az Standard_D8s_v3 virtuális gép akár 12 800 IOPs-t is végrehajthat. Ezeket a 10 000 IOPs-kérelmeket a rendszer három különböző kérelemre bontja le a különböző lemezekre. 1 000 a IOPs az operációsrendszer-lemezre, a 4 500 IOPs pedig az egyes adatlemezekre kéri a rendszer. Mivel az összes csatlakoztatott lemez E30-lemez, és csak 500 IOPs tud kezelni, a 500 IOPs mindegyikét visszaválaszolják. Az alkalmazás teljesítményét a csatlakoztatott lemezek megszabják, és csak a 1 500-es IOPs tudják feldolgozni. Ha jobb teljesítményű lemezeket használtak, például a prémium SSD P30-lemezeket, a 10 000-es IOPS is dolgozhat.

## <a name="virtual-machine-io-capping"></a>Virtuális gép IO-korlátja
Beállítás:
- Standard_D8s_v3 
    - Nem gyorsítótárazott IOPS: 12 800
- P30 operációsrendszer-lemez
    - IOPS: 5 000 
- 2 P30 adatlemez 
    - IOPS: 5 000

![Virtuális gépek szintjének maximális korlátja](media/vm-disk-performance/vm-level-throttling.jpg)

A virtuális gépen futó alkalmazás 15 000 IOPs igénylő kérelmet tesz elérhetővé. Sajnos a Standard_D8s_v3 virtuális gép csak az 12 800-es IOPs kezelésére van kiépítve. Ettől kezdve az alkalmazást a virtuálisgép-korlátok korlátozzák, és ezután le kell foglalni a kiosztott 12 800 IOPs. A kért 12 800 IOPs a különböző lemezekre három különböző kérelemre bontva. 4 267 a IOPs az operációsrendszer-lemezre, a 4 266 IOPs pedig az egyes adatlemezekre kéri a rendszer. Mivel az összes csatlakoztatott lemez P30-lemez, amely képes kezelni a 5 000 IOPs, a kért összegekkel együtt reagálnak.