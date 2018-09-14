---
title: Bevezetés a csapat adatelemző virtuális gép-alapú környezetek – Azure |} A Microsoft Docs
description: Minták üzembe helyezéséhez az adatelemző virtuális gép csapat vállalati környezetben.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés, a csoportos adatelemzési folyamat
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
ms.openlocfilehash: ea8d53ee71e9272167a045e2ea1780828f974d30
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573591"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Adatok tudományos virtuálisgép-alapú csapat elemzési és AI-környezet 
A [adatelemző virtuális gép](overview.md) (DSVM) az Azure platformon, a mesterséges intelligencia (AI) és a data analytics előre összeállított szoftver gazdag környezetet biztosít. 

A DSVM hagyományosan, mint egy egyéni elemzési asztal már használták. Egyes adatszakértők nyereség ebben az előre összeállított elemzési környezet megosztott fogalma a termelékenység. Nagy méretű elemzési csapatok a elemzési környezetek tervezése az adatszakértők és a Mesterségesintelligencia-fejlesztőknek, mert az ismétlődő témák egyikét egy megosztott analytics infrastruktúra fejlesztési és kísérletezés. Ez az infrastruktúra felügyelt vállalati informatikai megfelelően házirendeket, amelyek is lehetővé teszik együttműködés és a konzisztencia a data science/analytics csapatok között. 

Megosztott infrastruktúra is lehetővé teszi, hogy jobban kihasználhassák az elemzési környezet. Egyes szervezetek hívja az team-alapú adatelemzési és analitikai infrastruktúrát egy "analytics védőfal." Gyorsan megismerheti az adatok, futtasson kísérleteket, feltételezéseket ellenőrzése és az éles környezet befolyásolása nélkül építhet ki prediktív modelleket különböző adategységek eléréséhez adatszakértők számára lehetővé teszi. 

A dsvm-hez az Azure-infrastruktúra szintjén működik, mert a rendszergazdák könnyen konfigurálható a DSVM megfelelnek-e a informatikai házirendek a vállalkozás működtetéséhez. A dsvm-hez a hozzáférést a vállalati adategységek különböző megosztási architektúrák megvalósítása szabályozott módon teljes rugalmasságot biztosítja. 

Ez a szakasz ismerteti, bizonyos mintákat és irányelveket, amelyek segítségével a DSVM Csoportalapú data science infrastruktúra üzembe helyezése. Ezek a minták a építőelemeit származnak az Azure infrastruktúra szolgáltatás (IaaS), így minden olyan Azure virtuális gépekre vonatkoznak. Az a cikksorozat célja az ezeket a standard szintű Azure-infrastruktúra képességeket alkalmazására az adatelemző virtuális gép. 

A fő építőelemeit csapat analytics vállalati környezetben a következők:

* [Az adatelemző virtuális gépek maximumára készlet](dsvm-pools.md)
* [Általános identitás- és egy munkaterület elérését bármelyik a Dsvm-készletben](dsvm-common-identity.md)
* [Biztonságos hozzáférés az adatforrások](dsvm-secure-access-keys.md)


A cikksorozat nyújt útmutatást és mutatók minden az előző elemek. A szempontok és igényeinek megfelelően a DSVM üzembe nagyvállalati konfigurációk nem fedi le. A következő egyéb Azure dokumentációja közben DSVM példányok megvalósítása a vállalati használható: 

* [Hálózati biztonság](https://docs.microsoft.com/azure/security/azure-network-security)
* [Figyelés](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) és [kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Naplózás](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [A házirend-beállítással és végrehajtás](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Kártevőirtó](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Titkosítás](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Adatfelderítés és irányítás](https://docs.microsoft.com/azure/data-catalog/)

A [Azure Architecture Centert](https://docs.microsoft.com/azure/architecture/) egy részletes teljes körű architektúrát biztosít, és az analytics felhőalapú infrastruktúra kiépítése és kezelése a trendeket. 
