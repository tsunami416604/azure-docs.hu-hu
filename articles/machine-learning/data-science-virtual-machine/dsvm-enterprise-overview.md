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
ms.openlocfilehash: 911d6484421cc9fddad0530bf8d9ab4f01d48bf8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Adatok tudományos virtuálisgép-alapú Team elemzés és AI környezet 
A [adatok tudományos virtuális gép](overview.md) (DSVM) gazdag környezetet biztosít az Azure-felhő előre elkészített szoftverrel AI és az adatok elemzéséhez. Hagyományosan a DSVM vettük egyedi analytics asztali, és egyes adatszakértőkön szerezhet az előre elkészített analytics környezetükben megosztott fogalmát termelékenységet. Nagy analytics csapatok analytics környezetük tervezése az adatelemzők és AI fejlesztők számára, az ismétlődő témák egyik megjelenik a vállalati informatikai felügyelt megosztott analytics fejlesztési és kísérletezés infrastruktúra házirendek amely is elősegíti együttműködés és konzisztencia között a teljes adattudomány / analytics csapatának. A megosztott infrastruktúra is lehetővé teszi, hogy informatikai jobban tudják használni az analytics-környezetben. A team-alapú adattudomány / analytics infrastruktúra más néven egyes szervezetek "Analytics védőfal", amely lehetővé teszi az adatszakértőkön át a gyorsan adatok megismeréséhez, futtassa a kísérletet, feltevése ellenőrizze, biztonságos módon prediktív modellek létrehozása adatok különböző eszközökhöz való hozzáférés során az éles környezetben befolyásolása nélkül. 

A DSVM az Azure-infrastruktúra szintjén működik, mivel a rendszergazdák könnyen konfigurálható a DSVM összhangban az IT-házirendeknek a vállalati és ajánlatok teljes rugalmasságot biztosít a végrehajtási hozzáférést különböző megosztási architektúrák működik Vállalati adategységek szabályozott módon. 

Ez a szakasz ismerteti, amelyek bizonyos minták és útmutatást a DSVM a tudományos adatok team-alapú infrastruktúra központi telepítéséhez használható.  Ezeket a mintákat a építőelemeit közvetlenül használja az Azure IaaS (szolgáltatott infrastruktúra), és mint ilyen alkalmazandó bármely Azure virtuális gépeken. A megadott cikkek sorozata elsősorban ezen Azure-infrastruktúra képességek alkalmazása az adatok tudományos virtuális géphez. 

Vállalati team analytics környezetben kulcsfontosságú építőelemei vannak:

* [Az adatok tudományos virtuális gépek készlet automatikus méretezése](dsvm-pools.md)
* [Közös identitás- és hozzáférés a munkaterülethez bármelyik a DSVMs a készletben](dsvm-common-identity.md)
* [Biztonságos hozzáférés a adatforrások](dsvm-secure-access-keys.md)
* Irányítási és a vállalati és a nyitott adatkészletek felderítése

A cikkek sorozat útmutatás és mutatók megadott minden ezeket az jellemzőket. A [Azure architektúra center](https://docs.microsoft.com/en-us/azure/architecture/) a analytics infrastruktúra végpont architektúra részletes.  
