---
title: Csapatelemzés és AI-környezet
titleSuffix: Azure Data Science Virtual Machine
description: Az adatelemzési virtuális gép vállalati csapatkörnyezetben történő üzembe helyezésének mintái.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 1f3a24afb2238c2448cb19b3889467a1b1819724
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460545"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Adattudomány virtuális gép alapú csapatelemzés és AI-környezet 
Az [adatelemzési virtuális gép](overview.md) (DSVM) gazdag környezetet biztosít az Azure platformon, előre elkészített szoftverrel a mesterséges intelligencia (AI) és az adatelemzés számára.

Hagyományosan a DSVM-et egyéni analitikai asztalként használják. Az egyes adatszakértők ezzel a megosztott, előre elkészített elemzési környezettel termelékenységet nyernek. Mivel a nagy elemzőcsapatok környezeteket terveznek adattudósaik és a ai-fejlesztők számára, az ismétlődő témák egyike a fejlesztéshez és kísérletezéshez szükséges megosztott elemzési infrastruktúra. Ezt az infrastruktúrát a vállalati informatikai házirendekkel összhangban kezelik, amelyek megkönnyítik az együttműködést és a konzisztenciát az adatelemzési és elemzési csapatok között.

A megosztott infrastruktúra lehetővé teszi az elemzési környezet jobb informatikai kihasználtságát. Egyes szervezetek a csapatalapú adatelemzési/elemzési infrastruktúrát *elemzési sandboxnak nevezik.* Lehetővé teszi az adatszakértők számára, hogy különböző adateszközökhöz férjenek hozzá az adatok gyors megértéséhez. Ez a sandbox környezet is segít az adatszakértők kísérletek futtatásában, hipotézisek érvényesítése, és hozzon létre prediktív modellek anélkül, hogy befolyásolná az éles környezetben.

Mivel a DSVM az Azure infrastruktúra szintjén működik, a rendszergazdák könnyen konfigurálhatják a DSVM-et, hogy a vállalati informatikai szabályzatokkal összhangban működjön. A DSVM teljes rugalmasságot biztosít a különböző megosztási architektúrák megvalósításában, miközben ellenőrzött módon hozzáférést biztosít a vállalati adateszközökhöz.

Ez a szakasz ismerteti a minták és irányelvek, amelyek segítségével telepítheti a DSVM, mint egy csapat-alapú adatelemzési infrastruktúra. Mivel ezek a minták építőkövei az Azure-infrastruktúra szolgáltatásként (IaaS) származnak, minden Azure-beli virtuális gépre vonatkoznak. Ez a cikksorozat ezeknek a szabványos Azure-infrastruktúra-képességeknek a DSVM-re való alkalmazására összpontosít.

A vállalati csapatelemzési környezet fő építőkövei a következők:

* [DSVM-ek automatikusskálázott készlete](dsvm-pools.md)
* [Közös identitás és a munkaterülethez való hozzáférés a készletben lévő DSVM-ek bármelyikéből](dsvm-common-identity.md)
* [Biztonságos hozzáférés az adatforrásokhoz](dsvm-secure-access-keys.md)


Ez a sorozat útmutatást és útmutatást nyújt az előző témakörökhöz. Nem terjed ki a DSVM-ek nagyvállalati konfigurációkban történő üzembe helyezésével kapcsolatos összes szempontra és követelményre. Íme néhány egyéb Azure-erőforrás, amelyet a dsvm-példányok vállalati megvalósítása során használhat:

* [Hálózati biztonság](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Felügyelet](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) és [irányítás](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Naplózás](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [A politika meghatározása és érvényesítése](../../governance/policy/overview.md)
* [Kártevőirtó](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Titkosítás](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview.md)
* [Adatfelderítés és -irányítás](https://docs.microsoft.com/azure/data-catalog/)

Végül az [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) részletes, végpontok között architektúrát és modelleket biztosít a felhőalapú elemzési infrastruktúra létrehozásához és kezeléséhez.