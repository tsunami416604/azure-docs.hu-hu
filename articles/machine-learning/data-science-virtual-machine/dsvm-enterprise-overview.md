---
title: Csoportos elemzési és AI-környezet
titleSuffix: Azure Data Science Virtual Machine
description: Minták üzembe helyezéséhez az adatelemző virtuális gép csapat vállalati környezetben.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés, a csoportos adatelemzési folyamat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 06d05d6d410af13bfbe85f3cb66523c1d48cb77c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195666"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Adatok tudományos virtuálisgép-alapú csapat elemzési és AI-környezet 
A [Data Science Virtual Machine](overview.md) (DSVM) az Azure platformon gazdag környezetet biztosít, amely a mesterséges intelligencia (AI) és az adatelemzések előre elkészített szoftvereit tartalmazza.

A DSVM hagyományosan, mint egy egyéni elemzési asztal már használták. Az egyes adatszakértők a közös, előre elkészített elemzési környezettel vehetik igénybe a hatékonyságot. A nagyméretű elemzési csapatok az adatszakértők és a mesterséges intelligencia-fejlesztők számára tervezik meg a környezeteket, és az ismétlődő témák egyike egy közös elemzési infrastruktúra a fejlesztéshez és kísérletezéshez. Ezt az infrastruktúrát a vállalati informatikai szabályzatoknak megfelelően kezelik, amelyek az adatelemzési és az elemzési csapatok együttműködését és következetességét is megkönnyítik.

A megosztott infrastruktúra lehetővé teszi az elemzési környezet jobb kihasználtságát. Egyes szervezetek a Team-alapú adatelemzési/elemzési infrastruktúrát egy *elemzési homokozóban*hívják meg. Lehetővé teszi az adatszakértők számára a különböző adategységek elérését az adatok gyors megismerése érdekében. Ez a homokozó-környezet lehetővé teszi az adatszakértők számára a kísérletek futtatását, a hipotézisek érvényesítését és a prediktív modellek kialakítását anélkül, hogy ez hatással lenne az éles környezetre.

A dsvm-hez az Azure-infrastruktúra szintjén működik, mert a rendszergazdák könnyen konfigurálható a DSVM megfelelnek-e a informatikai házirendek a vállalkozás működtetéséhez. A DSVM teljes rugalmasságot biztosít a különböző megosztási architektúrák megvalósításában, miközben felügyelt módon biztosít hozzáférést a vállalati adategységekhez.

Ez a szakasz ismerteti, bizonyos mintákat és irányelveket, amelyek segítségével a DSVM Csoportalapú data science infrastruktúra üzembe helyezése. Mivel ezek a minták építőelemei az Azure-infrastruktúra szolgáltatásként (IaaS) származnak, minden Azure-beli virtuális gépre érvényesek. Ez a cikksorozat a szabványos Azure-infrastruktúra funkcióinak a DSVM való alkalmazására koncentrál.

A vállalati csapat elemzési környezetének kulcsfontosságú építőelemei a következők:

* [Dsvm-készlet](dsvm-pools.md)
* [Általános identitás- és egy munkaterület elérését bármelyik a Dsvm-készletben](dsvm-common-identity.md)
* [Biztonságos hozzáférés az adatforrások](dsvm-secure-access-keys.md)


Ez a sorozat az előző témakörökhöz nyújt útmutatást és mutatókat. Nem fedi le az Dsvm nagyméretű vállalati konfigurációkban való üzembe helyezésével kapcsolatos szempontokat és követelményeket. Íme néhány további Azure-erőforrás, amelyet a vállalati DSVM-példányok megvalósítása során használhat:

* [Hálózati biztonság](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Figyelés](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) és [kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Naplózás](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [A házirend-beállítással és végrehajtás](../../governance/policy/overview.md)
* [Kártevőirtó](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Titkosítás](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Adatfelderítés és irányítás](https://docs.microsoft.com/azure/data-catalog/)

Végül a [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) részletes, végpontok közötti architektúrát és modelleket biztosít a felhőalapú elemzési infrastruktúra kiépítéséhez és kezeléséhez.