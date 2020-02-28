---
title: Azure Security Center GYIK – általános kérdések
description: Gyakori kérdések a Azure Security Centerről, a fenyegetések megelőzésére, észlelésére és reagálására szolgáló termékről
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: f9043ae1414b63f25583d52100774f3e87754a74
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661839"
---
# <a name="faq---general-questions-about-azure-security-center"></a>GYIK – általános kérdések Azure Security Center

## <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?
Azure Security Center segít megakadályozni, észlelni és reagálni a fenyegetésekre az erőforrások biztonságának jobb láthatóságával és szabályozásával. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Security Center a Microsoft monitoring Agent használatával gyűjti és tárolja az adatokat. Részletes információk: [adatgyűjtés Azure Security Centerban](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Hogyan szerezhetem be az Azure Security Center?
A Azure Security Center engedélyezve van a Microsoft Azure-előfizetéssel, és a [Azure Portal](https://azure.microsoft.com/features/azure-portal/)érhető el. A hozzáféréshez [Jelentkezzen be a portálra](https://portal.azure.com), válassza a **Tallózás**lehetőséget, majd görgessen a **Security Center**elemre.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Melyik Azure-erőforrásokat az Azure Security Center által figyelt?
Az Azure Security Center figyeli a következő Azure-erőforrások:

* Virtuális gépek (VM-EK) (beleértve a [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtuálisgép-méretezési csoportok
* Azure virtuális hálózatok
* Tárolók
* Azure SQL service
* Azure Storage-fiók
* Azure Web Apps ( [app Service Environment](../app-service/environment/intro.md))
* Például a webalkalmazási tűzfal a virtuális gépek és az App Service Environment az Azure-előfizetésében integrált partnermegoldások

Emellett a nem Azure-t (beleértve a helyszíni gépeket is) Azure Security Center is figyelheti. A [Windows rendszerű gépek](./quick-onboard-windows-computer.md) és a Linux rendszerű [gépek](./quick-onboard-linux-computer.md) egyaránt támogatottak.


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hogyan tekinthetem meg a saját Azure-erőforrások aktuális biztonsági állapotát?
A **Security Center áttekintő** oldal a környezet általános biztonsági állapotát mutatja számítás, hálózatkezelés, tárterület & adatok és alkalmazások szerint lebontva. Minden erőforrástípus tartalmaz egy mutatót, amely az azonosított biztonsági réseket mutatja be. Minden egyes csempére kattintva azonosítja az erőforrást az előfizetésében leltára együtt a Security Center, biztonsági problémák listáját jeleníti meg.



## <a name="what-is-a-security-policy"></a>Mi az a biztonsági szabályzatot?
Biztonsági szabályzat határozza meg, az adott előfizetésen belüli erőforrások ajánlott szabályozza. Az Azure Security Centerben szabályzatokat az Azure-előfizetést a vállalat biztonsági elvárásainak és az alkalmazások típusához vagy az egyes előfizetések adatainak érzékenysége tartozó meghatározása.

Az Azure Security Center meghajtó biztonsági javaslatok és a figyelés az engedélyezett biztonsági szabályzatok. A biztonsági házirendekkel kapcsolatos további információkért lásd: [biztonsági állapot figyelése Azure Security Center](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Ki módosíthatja a biztonsági szabályzatot?
Biztonsági szabályzat módosításához a rendszergazda vagy a tulajdonosának vagy Közreműködőjének előfizetésben kell lennie.

A biztonsági szabályzatok konfigurálásával kapcsolatos további információkért lásd: [biztonsági házirendek beállítása Azure Security Centerban](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Mi az a biztonsági javaslatok?
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Ha az azonosított potenciális biztonsági réseket, javaslatok jönnek létre. A javaslatok végigvezetik a szükséges vezérlőt konfigurálásának folyamatán. Példák:

* Kiépítés kártevőirtó szoftverek azonosításához és kártevő szoftverek eltávolítása
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md) és szabályok a virtuális gépek forgalmának vezérléséhez
* A webalkalmazások célzó támadások elleni segítségével webalkalmazási tűzfal kiépítése
* Hiányzó rendszerfrissítések telepítése
* Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

Csak azok a javaslatok, amelyek engedélyezve vannak a biztonsági szabályzatok Itt jelennek meg.



## <a name="what-triggers-a-security-alert"></a>Mi a biztonsági riasztást aktivál?
Az Azure Security Center automatikusan gyűjti, elemzi és biztosítók az Azure-erőforrások, a hálózati és a partneri megoldások, például kártevőirtó- és tűzfalak naplóadatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

* Feltört virtuális gépek, amelyek kártékonyként azonosított IP-címekkel kommunikálnak
* Használatával a Windows hibajelentés észlelt speciális kártevő
* Virtuális gépek elleni, a teljes kipróbálás módszerén alapuló támadások
* Integrált partneri biztonsági megoldások, például a kártevő- vagy webalkalmazás-tűzfalak biztonsági riasztásai


## Miért változnak a biztonsági pontszám értékei? <a name="secure-score-faq"></a>
Február 2019-én a súlyosságuk jobb illeszkedése érdekében Security Center néhány javaslat pontszámát. Ennek a beállításnak az eredményeképpen előfordulhat, hogy a biztonsági pontszámok teljes értékei módosulnak.  A biztonságos pontszámról további információt a [biztonságos pontszám kiszámítása](security-center-secure-score.md)című témakörben talál.


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Mi a különbség a fenyegetéseket észlelt, és kapni a Microsoft Security Response Center és az Azure Security Center által?
A Microsoft Security Response Center (MSRC) hajt végre, válassza ki a biztonsági figyelése az Azure-hálózat és az infrastruktúra, és harmadik felektől származó threat intelligence és visszaélések panaszok kap. MSRC tudomására jut, hogy vásárlói adatokat egy jogosulatlan vagy illetéktelen fél hozzáfért-e, illetve, hogy az Azure használatát az ügyfél nem felel meg a feltételeket az elfogadható használja, amikor egy biztonsági incidens manager értesíti a. Értesítés általában akkor fordul elő, a biztonsági felelősök kapcsolati adatait meg az Azure Security Center vagy az Azure-előfizetés tulajdonosa, ha nincs megadva a biztonsági kapcsolattartó e-mail elküldésével.

A Security Center egy Azure-szolgáltatás, amely folyamatosan figyeli az ügyfél Azure-környezetben, és automatikusan észlelni a potenciálisan kártékony tevékenységek számos elemzési vonatkozik. Ezek az észlelések illesztett biztonsági riasztásai a Security Center irányítópultján.