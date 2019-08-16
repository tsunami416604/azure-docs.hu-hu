---
title: Virtuális gépekre vonatkozó javaslatok a Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum ismerteti a Azure Security Center javaslatokat a virtuális gépek és számítógépek, valamint a webalkalmazások és a App Service környezetek védelmének biztosításához.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: 798e027ca611905766b1fb8bcdb89cba4aeaf9b2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69531338"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>A Azure Security Center erőforrással kapcsolatos javaslatok ismertetése


## <a name="recommendations"></a>Javaslatok
Az alábbi táblázatokat hivatkozásként használva megismerheti az elérhető számítási és app Services-ajánlásokat, és azt, hogy mit tesznek, ha alkalmazza azt.

### <a name="computers"></a>Számítógépek
| Ajánlás | Leírás |
| --- | --- |
| Adatgyűjtés engedélyezése az előfizetések számára|Javasolja, hogy minden egyes előfizetés és virtuális gép (VM) esetében kapcsolja be az adatgyűjtést az előfizetéseinek biztonsági házirendjében. |
| Az Azure Storage-fiók titkosításának engedélyezése| Javasolja, hogy engedélyezze az Azure Storage Service Encryptiont a nyugalmi állapotban lévő adatokhoz. Storage Service Encryption (SSE) az adatok titkosításával működik, amikor az Azure Storage-ba íródik, és a lekérés előtt visszafejti őket. Az SSE jelenleg csak az Azure Blob service számára érhető el, és a Blobok, a blobok és a hozzáfűző Blobok számára is használható. További információ: [Storage Service encryption for](../storage/common/storage-service-encryption.md)inaktív adatok.</br>Az SSE csak Resource Manager-beli Storage-fiókok esetén támogatott. A klasszikus Storage-fiókok jelenleg nem támogatottak. A klasszikus és a Resource Manager-alapú üzemi modellek megismeréséhez tekintse meg az Azure Deployment models című témakört. |
| Biztonsági konfigurációk szervizelése|Javasolja, hogy az operációs rendszer konfigurációit az ajánlott biztonsági konfigurációs szabályokkal igazítsa, például ne engedélyezze a jelszavak mentését. |
| Rendszerfrissítések alkalmazása |Javasolja, hogy végezze el a hiányzó rendszerbiztonsági és kritikus frissítések központi telepítését a virtuális gépeken. |
| Igény szerinti hálózati hozzáférés-vezérlés alkalmazása| Javasolja, hogy csak időben alkalmazza a virtuális gépekhez való hozzáférést. Az igény szerinti funkció előzetes verzióban érhető el, és a standard szintű Security Center elérhető. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md). |
| Újraindítás a rendszerfrissítések után|Javasolja, hogy a rendszerfrissítések alkalmazási folyamatának befejezéséhez indítson újra egy virtuális gépet. |
| Az Endpoint Protection telepítése|Javasolja, hogy telepítsen kártevőirtó programokat a virtuális gépekre (csak Windows rendszerű virtuális gépek esetében). |
| VM-ügynök engedélyezése |Lehetővé teszi a virtuálisgép-ügynök alkalmazását igénylő virtuális gépek megtekintését. A virtuálisgép-ügynöknek telepítve kell lennie a virtuális gépeken a javítás- és alapkonfiguráció-keresés, valamint a kártevőirtó programok üzembe helyezéséhez. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök telepítéséről a [Virtuális gép-ügynök és -bővítmények – 2. rész](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) cikkben talál információkat. |
| Lemeztitkosítás alkalmazása|Javasolja, hogy végezze el a virtuális gép titkosítását az Azure Disk Encryption használatával (Windows és Linux rendszerű virtuális gépek esetében). A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen. |
| Operációs rendszer verziójának frissítése|Javasolja, hogy frissítse a Cloud Service operációs rendszer (OS) verzióját a legújabb, az operációsrendszer-család számára elérhető verzióra.  Ha többet szeretne megtudni a Cloud Servicesről, tekintse meg a Cloud Services áttekintést. |
| A sebezhetőségi felmérés nincs telepítve|Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| Biztonsági rések szervizelése) |Lehetővé teszi a virtuális gépre telepített sebezhetőségfelmérő megoldás által észlelt rendszer- és alkalmazássebezhetőségek megtekintését. |

### App Services<a name="app-services"></a>
| Ajánlás | Leírás |
| --- | --- |
| App Service csak HTTPS-kapcsolaton keresztül érhető el | Javasolja, hogy csak HTTPS-kapcsolaton korlátozza a App Service elérését. |
| Webes szoftvercsatornák le kell tiltani a webes alkalmazás| Javasolja, hogy alaposan tekintse át a webes szoftvercsatornák használatát a webalkalmazásokon belül.  A Websocket protokoll téve a különböző típusú biztonsági fenyegetéseket. |
| Egyéni tartományok használata az zabalení Webové Aplikace | Javasolja, hogy egyéni tartományokat használjon a webalkalmazások a gyakori támadásokkal, például az adathalászattal és más DNS-mel kapcsolatos támadásokkal szembeni védelemhez. |
| Webes alkalmazás IP-korlátozások konfigurálása | Javasolja az alkalmazás elérésére jogosult IP-címek listájának megadását.  Az IP-korlátozások használatát megvédheti webalkalmazását a gyakori támadásoktól. |
| Ne engedélyezze az összes ("*") erőforrások hozzáférhetnek az alkalmazáshoz | Azt javasolja, hogy ne állítsa be a WEBSITE_LOAD_CERTIFICATES paramétert a következőre: " *". Ha a paramétert "* " értékre állítja, akkor az összes tanúsítvány betöltődik a webalkalmazások személyes tanúsítványtárolóba.  A minimális jogosultság elvével való visszaéléshez ez is vezethet, mert nem valószínű, hogy a hely összes tanúsítvány futásidőben hozzá kell férnie. |
| A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen az alkalmazáshoz | Javasolja, hogy csak a szükséges tartományokat engedélyezze a webalkalmazással való kommunikációhoz. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a webalkalmazáshoz való hozzáférés. |
| A legújabb támogatott .NET-keretrendszer-webalkalmazás | Javasolja, hogy a legújabb biztonsági osztályokhoz használja a .NET-keretrendszer legújabb verzióját. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| A Java legújabb támogatott verzióját használja a webalkalmazáshoz | Javasolja, hogy a legújabb biztonsági osztályokhoz a Java legújabb verzióját használja. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| A PHP legújabb támogatott verzióját használja a webalkalmazáshoz | Javasolja, hogy a legújabb biztonsági osztályokhoz a PHP legújabb verzióját használja. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| [Webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md) |Javasolja, hogy webes végpontok számára telepítsen webalkalmazási tűzfalat (WAF). Egy WAF javaslat jelenik meg a nyilvános IP-címek (a példányok szintjének IP-címe vagy a terheléselosztási IP-cím) számára, amely egy nyitott bejövő webes porttal (80 443) rendelkező hálózati biztonsági csoporttal rendelkezik.</br></br>Security Center azt javasolja, hogy egy WAF hozzon létre, amely segít megvédeni a webalkalmazásait a virtuális gépeken és a App Service Environmenteken. Az App Service Environment (bevezetési) egy olyan [prémium](https://azure.microsoft.com/pricing/details/app-service/) szintű szolgáltatási Azure app Service csomag, amely teljesen elkülönített és dedikált környezetet biztosít Azure app Service alkalmazások biztonságos futtatásához. A betanító szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [app Service Environment dokumentációját](../app-service/environment/intro.md).</br></br>Az alkalmazások a meglévő WAF-környezetekhez való hozzáadásával Security Center több webalkalmazást is védetté tenni. |
| [Alkalmazásvédelem véglegesítése](security-center-add-web-application-firewall.md#finalize-application-protection) |Egy WAF konfigurációjának befejezéséhez át kell irányítani a forgalmat a WAF készülékre. Ezt a javaslatot követve a telepítés szükséges módosításokat hajtja végre. |
| A legújabb támogatott Node.js verzió használata a webalkalmazáshoz | Javasolja, hogy a legújabb biztonsági osztályokhoz a Node. js legújabb verzióját használja. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| A CORS nem teszi lehetővé a Függvényalkalmazás eléréséhez minden erőforrás | Javasolja, hogy csak a szükséges tartományokat engedélyezze a webalkalmazással való kommunikációhoz. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a függvény-alkalmazás elérésére. |
| Egyéni tartományok használata az Függvényalkalmazás | Azt javasolja, hogy egyéni tartományokat használjon az olyan gyakori támadások elleni védelemhez, mint például az adathalászat és a DNS-sel kapcsolatos egyéb támadások. |
| Függvényalkalmazás IP-korlátozások konfigurálása | Javasolja az alkalmazás elérésére jogosult IP-címek listájának megadását. Az IP-korlátozások használata gyakori támadások ellen védi a függvényalkalmazást. |
| Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül | Javasolja, hogy csak HTTPS-kapcsolaton keresztül korlátozza a Function apps alkalmazásait. |
| A távoli hibakeresést ki kell kapcsolni a függvényalkalmazásban | Javasolja, hogy kapcsolja ki a függvényalkalmazás hibakeresését, ha már nincs szüksége a használatára. Távoli hibakeresés használatához meg kell nyitni a Függvényalkalmazás bejövő portokat. |
| Webes szoftvercsatornák le kell tiltani a Függvényalkalmazás | Javasolja, hogy körültekintően tekintse át a web Sockets használatát a Function apps szolgáltatásban. A Websocket protokoll téve a különböző típusú biztonsági fenyegetéseket. |


## <a name="next-steps"></a>További lépések
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Identitás és hozzáférés az Azure Security Center figyelése](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
