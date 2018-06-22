---
title: Team adatok tudományos virtuálisgép-alapú környezetek - Azure bemutatása |} Microsoft Docs
description: Az adatok tudományos VM team vállalati környezetben üzembe helyezéséhez kombinációját.
keywords: a mélyhivatkozással tanulási, AI adatok tudományos eszközök, a adatok tudományos virtuális gép, a földrajzi analytics, a csapat az tudományos folyamata
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 8486b0be1fb5e1385da3c7ad55f6410a1059df93
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309248"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Tudományos virtuálisgép-alapú team adatelemzés és AI környezet 
A [adatok tudományos virtuális gép](overview.md) (DSVM) az Azure platform mesterséges intelligencia (AI) és adatelemzés előre elkészített szoftverrel gazdag környezetet biztosít. 

Hagyományosan a DSVM használatban van, az egyes analytics asztali. Egyes adatszakértőkön az előre elkészített analytics környezetükben megosztott fogalmát termelékenységet kapnak. Nagy analytics csapatok analytics környezetük tervezése az adatelemzők és AI fejlesztők számára, az ismétlődő témák egyik egy megosztott analytics infrastruktúra fejlesztési és kísérletezhet. Ez az infrastruktúra kezelése összhangban a nagyvállalati informatikai házirendek, amelyek is egyszerűbbé teszik az adatok tudományos/analytics csapatok együttműködés és konzisztenciáját. 

A megosztott infrastruktúra is lehetővé teszi, hogy informatikai jobban tudják használni az analytics-környezetben. Egyes szervezetek hívja az adatok team-alapú tudományos/analytics infrastruktúra egy "analytics védőfal." Ez lehetővé teszi a adatszakértőkön gyorsan adatok megismeréséhez, futtassa a kísérletet, feltételezéseket érvényesítése és prediktív modellek létrehozása az éles környezetben anélkül különböző adategységek eléréséhez. 

A DSVM az Azure-infrastruktúra szintjén működik, mert a rendszergazdák könnyen konfigurálható a DSVM meg a vállalati informatikai házirendeknek megfelelően működjön. A DSVM végrehajtási hozzáférést a vállalati adategységek különböző megosztási architektúrák szabályozott módon teljes rugalmasságot biztosít. 

Ez a szakasz ismerteti, amelyek bizonyos minták és útmutatást, amelyek segítségével a DSVM a tudományos adatok team-alapú infrastruktúra telepítése. Ezeket a mintákat a építőelemeit határozza meg az Azure infrastruktúra szolgáltatásként (IaaS), így bármely Azure virtuális gépek vonatkoznak. Az adatsorozat a cikkek a elsősorban ezen Azure-infrastruktúra képességek alkalmazása az adatok tudományos virtuális géphez. 

Vállalati team analytics környezetben kulcsfontosságú építőelemei vannak:

* [Az adatok tudományos virtuális gépek skálázva készlet](dsvm-pools.md)
* [Közös identitás- és hozzáférés-munkaterülethez bármelyik a DSVMs a készletben](dsvm-common-identity.md)
* [Biztonságos hozzáférés a adatforrások](dsvm-secure-access-keys.md)


Cikkek sorozata nyújt útmutatást és mutatók minden az előző elemek. A szempontok és igényeinek DSVM nagyvállalati konfigurációk telepítése nem fedi le. Itt található egyéb Azure dokumentációja, amely során a vállalat DSVM példányok végrehajtási használhatók: 

* [Hálózati biztonság](https://docs.microsoft.com/azure/security/azure-network-security)
* [Figyelési](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) és [kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Naplózás](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [A házirend-beállítást és érvényesítése](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Kártevőirtó](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Titkosítás](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Felderítési adatok és irányítási](https://docs.microsoft.com/azure/data-catalog/)

A [Azure architektúra Center](https://docs.microsoft.com/en-us/azure/architecture/) részletes végpont architektúrát nyújt, és minták kialakításához, és az elemzés felhőalapú infrastruktúra kezelése. 
