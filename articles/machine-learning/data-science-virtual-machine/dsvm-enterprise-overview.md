---
title: Bevezetés az adatok tudományos virtuálisgép-alapú Team környezetek - Azure |} Microsoft Docs
description: Minták adatok tudományos virtuális Gépet állítsanak vállalati csapatok környezet üzembe helyezéséhez.
keywords: a mélyhivatkozással tanulási, AI adatok tudományos eszközök, a adatok tudományos virtuális gép, a földrajzi analytics, a csapat az tudományos folyamata
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Adatok tudományos virtuálisgép-alapú Team elemzés és AI környezet 
A [adatok tudományos virtuális gép](overview.md) (DSVM) gazdag környezetet biztosít az Azure-felhő előre elkészített szoftverrel AI és az adatok elemzéséhez. Hagyományosan a DSVM vettük egyedi analytics asztali, és egyes adatszakértőkön szerezhet az előre elkészített analytics környezetükben megosztott fogalmát termelékenységet. Nagy analytics csapatok analytics környezetük tervezése az adatelemzők és AI fejlesztők számára, az ismétlődő témák egyik megjelenik a vállalati informatikai felügyelt megosztott analytics fejlesztési és kísérletezés infrastruktúra házirendek amely is elősegíti együttműködés és konzisztencia között a teljes adattudomány / analytics csapatának. A megosztott infrastruktúra is lehetővé teszi, hogy informatikai jobban tudják használni az analytics-környezetben. A team-alapú adattudomány / analytics infrastruktúra más néven egyes szervezetek "Analytics védőfal", amely lehetővé teszi az adatszakértőkön át a gyorsan adatok megismeréséhez, futtassa a kísérletet, feltevése ellenőrizze, biztonságos módon prediktív modellek létrehozása adatok különböző eszközökhöz való hozzáférés során az éles környezetben befolyásolása nélkül. 

A DSVM az Azure-infrastruktúra szintjén működik, mivel a rendszergazdák könnyen konfigurálható a DSVM összhangban az IT-házirendeknek a vállalati és ajánlatok teljes rugalmasságot biztosít a végrehajtási hozzáférést különböző megosztási architektúrák működik Vállalati adategységek szabályozott módon. 

Ez a szakasz ismerteti, amelyek bizonyos minták és útmutatást a DSVM a tudományos adatok team-alapú infrastruktúra központi telepítéséhez használható.  Ezeket a mintákat a építőelemeit közvetlenül használja az Azure IaaS (szolgáltatott infrastruktúra), és mint ilyen alkalmazandó bármely Azure virtuális gépeken. A megadott cikkek sorozata elsősorban ezen Azure-infrastruktúra képességek alkalmazása az adatok tudományos virtuális géphez. 

Vállalati team analytics környezetben kulcsfontosságú építőelemei vannak:

* [Az adatok tudományos virtuális gépek készlet automatikus méretezése](dsvm-pools.md)
* [Közös identitás- és hozzáférés a munkaterülethez bármelyik a DSVMs a készletben](dsvm-common-identity.md)
* [Biztonságos hozzáférés a adatforrások](dsvm-secure-access-keys.md)


A cikkek sorozat útmutatás és mutatók szerepelnek ezen fent szempontokat. Természetesen nincsenek számos további szempontok és a vonatkozó igényeket DSVM nagyvállalati konfigurációk nem még közvetlenül jelez a cikkek a sorozat telepítésekor. Az alábbiakban néhány egyéb szempontok és általános Azure dokumentációja, miközben a vállalati DSVM-példányokon bevezetné, könnyen használható mutató hivatkozások. 

* [Hálózati biztonság](https://docs.microsoft.com/azure/security/azure-network-security)
* [Figyelési](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) és [kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Naplózás és naplózás](https://docs.microsoft.com/azure/security/azure-log-audit)
* [A szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [A házirend-beállítást és érvényesítése](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Kártevőirtó](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Titkosítás](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Felderítési adatok és irányítási](https://docs.microsoft.com/azure/data-catalog/)

A [Azure architektúra center](https://docs.microsoft.com/en-us/azure/architecture/) is van, amely részletes végpont architektúra és minták biztosít felépítését és kezelését a felhő alapú analytics infrastruktúra kiváló forrást. 
