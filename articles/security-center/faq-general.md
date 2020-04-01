---
title: Az Azure Security Center – gyakori kérdések – Általános kérdések
description: Gyakori általános kérdések az Azure Security Centerrel kapcsolatban, amely egy olyan termék, amely segít a fenyegetések megelőzésében, észlelésben és az azokra való reagálásban
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
ms.openlocfilehash: 54263a8be900ed381d8450b460e5cf9e6117ac54
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436119"
---
# <a name="faq---general-questions-about-azure-security-center"></a>GYAKRAN FELTETT KÉRDÉSEK – Általános kérdések az Azure Security Centerhasználatával kapcsolatban

## <a name="what-is-azure-security-center"></a>Mi az Azure Security Center?
Az Azure Security Center segítségével megelőzheti, észlelheti és reagálhat a fenyegetésekre, és nagyobb rálátással és az erőforrások biztonságának szabályozására. A biztonsági megoldások átfogó ökoszisztémájának alkalmazásával az összes előfizetésére vonatkozóan integrált biztonságfelügyeletet és szabályzatkezelést biztosít, és segít felderíteni az egyébként nehezen észlelhető fenyegetéseket.

A Security Center a Log Analytics-ügynök segítségével gyűjti és tárolja az adatokat. Részletes információt az Adatgyűjtés az Azure Security Centerben ( [Adatgyűjtés az Azure Security Centerben](security-center-enable-data-collection.md)) területen talál.


## <a name="how-do-i-get-azure-security-center"></a>Hogyan juthatok be az Azure Security Center?
Az Azure Security Center engedélyezve van a Microsoft Azure-előfizetéssel, és az [Azure Portalról](https://azure.microsoft.com/features/azure-portal/)érhető el. Eléréséhez [jelentkezzen be a portálra,](https://portal.azure.com)válassza a **Tallózás**gombot, és görgessen a **Biztonsági központhoz.**


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Mely Azure-erőforrásokat figyeli az Azure Security Center?
Az Azure Security Center a következő Azure-erőforrásokat figyeli:

* Virtuális gépek (VM-ek) (beleértve [a felhőszolgáltatásokat is)](../cloud-services/cloud-services-choose-me.md)
* Virtuálisgép-méretezési csoportok
* Azure virtuális hálózatok
* Containers
* Azure SQL szolgáltatás
* Azure Storage-fiók
* Azure Web Apps [(App Service-környezetben)](../app-service/environment/intro.md)
* Az Azure-előfizetéssel integrált partnermegoldások, például webalkalmazás-tűzfal a virtuális gépeken és az App Service-környezetben

Emellett a nem Azure-beli (beleértve a helyszíni) gépeket is figyelheti az Azure Security Center. Mind [a Windows-gépek,](./quick-onboard-windows-computer.md) mind a [Linux-gépek](./quick-onboard-linux-computer.md) támogatottak.


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hogyan láthatom az Azure-erőforrások aktuális biztonsági állapotát?
A **Biztonsági központ áttekintése** lap a környezet általános biztonsági állapotát jeleníti meg számítási, hálózati, tárolási & adatok és alkalmazások szerinti bontásban. Minden erőforrástípusnak van egy jelzője, amely az azonosított biztonsági réseket mutatja. Az egyes csempékre kattintva megjelenik a Biztonsági központ által azonosított biztonsági problémák listája, valamint az előfizetéserőforrásainak leltára.



## <a name="what-is-a-security-policy"></a>Mi az a biztonsági házirend?
A biztonsági házirend határozza meg a megadott előfizetésen belüli erőforrásokhoz ajánlott vezérlők készletét. Az Azure Security Centerben szabályzatokat határozmeg az Azure-előfizetésekhez a vállalat biztonsági követelményeinek és az egyes előfizetésekben lévő alkalmazások típusának vagy érzékenységének megfelelően.

Az Azure Security Centerben engedélyezett biztonsági szabályzatok biztonsági javaslatokat és figyelést eredményeznek. A biztonsági házirendekről a [Biztonsági állapot figyelése az Azure Security Centerben.](security-center-monitoring.md)


## <a name="who-can-modify-a-security-policy"></a>Ki módosíthatja a biztonsági házirendet?
A biztonsági házirend módosításához biztonsági rendszergazdának vagy az adott előfizetés tulajdonosának vagy közreműködőjének kell lennie.

A biztonsági házirendek konfigurálásáról a [Biztonsági házirendek beállítása az Azure Security Centerben című témakörben](tutorial-security-policy.md)olvashat.


## <a name="what-is-a-security-recommendation"></a>Mi az a biztonsági javaslat?
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. A lehetséges biztonsági rések azonosításakor javaslatok jönnek létre. A javaslatok végigvezetik a szükséges vezérlő konfigurálásának folyamatán. Példák:

* Kártevőirtó kiépítés a kártékony szoftverek azonosításához és eltávolításához
* [Hálózati biztonsági csoportok](../virtual-network/security-overview.md) és szabályok a virtuális gépek forgalmának szabályozásához
* Webalkalmazás-tűzfal kiépítése a webalkalmazásokat célzó támadások elleni védekezés érdekében
* Hiányzó rendszerfrissítések telepítése
* Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

Itt csak a biztonsági házirendekben engedélyezett javaslatok jelennek meg.



## <a name="what-triggers-a-security-alert"></a>Mi indítja el a biztonsági riasztást?
Az Azure Security Center automatikusan gyűjti, elemzi és egyesíti a naplóadatokat az Azure-erőforrásokból, a hálózatból és a partnermegoldásokból, például a kártevőirtó és a tűzfalakból. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

* Feltört virtuális gépek, amelyek kártékonyként azonosított IP-címekkel kommunikálnak
* A Windows hibajelentésével észlelt speciális kártevők
* Virtuális gépek elleni, a teljes kipróbálás módszerén alapuló támadások
* Biztonsági riasztások integrált partnerbiztonsági megoldásokból, például kártevőirtó vagy webalkalmazás-tűzfalakból


## <a name="why-did-secure-score-values-change"></a>Miért változtak a Biztonságos pontszám értékek? <a name="secure-score-faq"></a>
2019 februárjától a Security Center módosította néhány ajánlás pontszámát, hogy jobban illeszkedjen a súlyosságukhoz. Ennek a korrekciónak az eredményeként változások következhetnek be az általános biztonságos pontszámértékekben.  A biztonságos pontszámról a [Biztonságos pontszám számítása című](security-center-secure-score.md)témakörben talál további információt.


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Mi a különbség a Microsoft Security Response Center és az Azure Security Center által észlelt és az Azure Security Center által észlelt és riasztást küldő fenyegetések között?
A Microsoft Security Response Center (MSRC) az Azure-hálózat és -infrastruktúra kiválasztott biztonsági figyelését végzi, és harmadik felektől fenyegetésfelderítési és visszaélési panaszokat kap. Ha az MSRC tudomására jut, hogy az ügyféladatokhoz egy jogellenes vagy jogosulatlan fél hozzáfért, vagy hogy az Ügyfél által használt Azure nem felel meg az elfogadható használatra vonatkozó feltételeknek, a biztonsági incidenskezelő értesíti az ügyfelet. Értesítés általában akkor történik, ha e-mailt küld az Azure Security Centerben megadott biztonsági kapcsolattartóknak vagy az Azure-előfizetés tulajdonosának, ha nincs megadva biztonsági kapcsolattartó.

A Security Center egy Azure-szolgáltatás, amely folyamatosan figyeli az ügyfél Azure-környezetét, és elemzéseket alkalmaz a potenciálisan rosszindulatú tevékenységek széles körének automatikus észleléséhez. Ezek az észlelések biztonsági riasztásokként jelennek meg a Security Center irányítópultján.