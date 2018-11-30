---
title: Az Azure Security Centerben a virtuális gépre vonatkozó javaslatok |} A Microsoft Docs
description: Ez a dokumentum azt ismerteti, hogyan a virtuális gépek és számítógépek és a webalkalmazások és App Service Environment-környezetek védelmét az Azure Security Center javaslatok.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: rkarlin
ms.openlocfilehash: 626b9199f3de6fe9aba03ba537aa6e6b6e28ba5c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310923"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Az Azure Security Center erőforrás kapcsolatos javaslatok megértése


## <a name="recommendations"></a>Javaslatok
Használja az alábbi táblázatokban referenciaként segítenek megérteni a rendelkezésre álló számítási és az App services-ajánlásokat és mindegyikhez funkciója alkalmazásuk esetén azok.

### <a name="computers"></a>Számítógépek
| Ajánlás | Leírás |
| --- | --- |
| [Adatgyűjtés engedélyezése az előfizetések számára](security-center-enable-data-collection.md) |Javasolja, hogy minden egyes előfizetés és virtuális gép (VM) esetében kapcsolja be az adatgyűjtést az előfizetéseinek biztonsági házirendjében. |
| [Az Azure Storage-fiók titkosításának engedélyezése](security-center-enable-encryption-for-storage-account.md) | Inaktív adatok Azure Storage szolgáltatás titkosításának engedélyezését javasolja. Storage Service Encryption (SSE) működik, az adatok titkosítása az Azure storage-bA írt, és visszafejti őket a lekérés előtt. SSE jelenleg csak az Azure Blob service érhető el, és a blokkblobok, lapblobok és használható, és hozzáfűző blobok. További tudnivalókért lásd: [inaktív adatok a Storage Service Encryption](../storage/common/storage-service-encryption.md).</br>Az SSE csak Resource Manager-tárfiókok esetében támogatott. Klasszikus storage-fiókok jelenleg nem támogatott. A klasszikus és Resource Manager üzembe helyezési modellek ismertetése: [Azure üzembehelyezési modell](../azure-classic-rm.md). |
| [Biztonsági konfigurációk javítása](security-center-remediate-os-vulnerabilities.md) |Javasolja, hogy az operációs rendszer konfigurálását az ajánlott biztonsági konfigurációs szabályok, például ne engedélyezze a jelszavak mentését. |
| [Rendszerfrissítések alkalmazása](security-center-apply-system-updates.md) |Javasolja, hogy végezze el a hiányzó rendszerbiztonsági és kritikus frissítések központi telepítését a virtuális gépeken. |
| [A Just-In-Time a alkalmazni a hálózati hozzáférés-vezérlés](security-center-just-in-time.md) | Javasolja, time VM access csak a alkalmazni. Az igény szerinti funkció előzetes verzióban érhető el, és a Security Center Standard szinten elérhető. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md). |
| [Rendszerfrissítések utáni újraindítás](security-center-apply-system-updates.md#reboot-after-system-updates) |Javasolja, hogy a rendszerfrissítések alkalmazási folyamatának befejezéséhez indítson újra egy virtuális gépet. |
| [Endpoint Protection telepítése](security-center-install-endpoint-protection.md) |Javasolja, hogy telepítsen kártevőirtó programokat a virtuális gépekre (csak Windows rendszerű virtuális gépek esetében). |
| [Virtuálisgép-ügynök engedélyezése](security-center-enable-vm-agent.md) |Lehetővé teszi a virtuálisgép-ügynök alkalmazását igénylő virtuális gépek megtekintését. A virtuálisgép-ügynöknek telepítve kell lennie a virtuális gépeken a javítás- és alapkonfiguráció-keresés, valamint a kártevőirtó programok üzembe helyezéséhez. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök telepítéséről a [Virtuális gép-ügynök és -bővítmények – 2. rész](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) cikkben talál információkat. |
| [Lemeztitkosítás alkalmazása](security-center-apply-disk-encryption.md) |Javasolja, hogy végezze el a virtuális gép titkosítását az Azure Disk Encryption használatával (Windows és Linux rendszerű virtuális gépek esetében). A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen. |
| [Operációs rendszer verziójának frissítése](security-center-update-os-version.md) |Javasolja, hogy frissíti az operációs rendszer (OS) verzióját a legújabb elérhető verzió a felhőalapú szolgáltatás az operációsrendszer-család.  Cloud Services kapcsolatos további információkért tekintse meg a [Cloud Services – áttekintés](../cloud-services/cloud-services-choose-me.md). |
| [A sebezhetőségi felmérés nincs telepítve](security-center-vulnerability-assessment-recommendations.md) |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| [Sebezhetőségek javítása](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Lehetővé teszi a virtuális gépre telepített sebezhetőségfelmérő megoldás által észlelt rendszer- és alkalmazássebezhetőségek megtekintését. |

### <a name="app-services"></a>App Services
| Ajánlás | Leírás |
| --- | --- |
| App Service-ben csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül | Javasolja, hogy korlátozza az App Service hozzáférését a HTTPS-kapcsolaton keresztül csak. |
| Webes szoftvercsatornák le kell tiltani a webes alkalmazás| Keresse fel a Web Sockets websocket webalkalmazásokban használatát javasolja.  A Websocket protokoll téve a különböző típusú biztonsági fenyegetéseket. |
| Egyéni tartományok használata az zabalení Webové Aplikace | Egyéni tartományok közös adathalász támadások és más DNS-sel támadások elleni webalkalmazás használatát javasolja. |
| Webes alkalmazás IP-korlátozások konfigurálása | Javasolja, hogy az alkalmazás-hozzáférést IP-címek listájának meghatározását.  Az IP-korlátozások használatát megvédheti webalkalmazását a gyakori támadásoktól. |
| Ne engedélyezze az összes ("*") erőforrások hozzáférhetnek az alkalmazáshoz | Javasolja, hogy nem WEBSITE_LOAD_CERTIFICATES paraméter értéke "*". A paraméter beállítása "*" azt jelenti, hogy minden tanúsítvány betöltődik a webes alkalmazások személyes tanúsítványtárolójába.  A minimális jogosultság elvével való visszaéléshez ez is vezethet, mert nem valószínű, hogy a hely összes tanúsítvány futásidőben hozzá kell férnie. |
| A CORS nem teszi lehetővé az alkalmazás eléréséhez annak minden erőforrás | Javasolja, hogy lehetővé tegye a kommunikál a webes alkalmazások csak a szükséges tartományok. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a webalkalmazáshoz való hozzáférés. |
| A legújabb támogatott .NET-keretrendszer-webalkalmazás | A .NET-keretrendszer legújabb verzióját a legújabb biztonsági osztályok használatát javasolja. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| A Java legújabb támogatott verzióját használja a webalkalmazáshoz | Javasolja, hogy a legújabb Java-verzió esetében a legújabb biztonsági osztályokkal. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| A PHP legújabb támogatott verzióját használja a webalkalmazáshoz | A PHP legújabb verzióját a legújabb biztonsági osztályok használatát javasolja. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| [Webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md) |Javasolja, hogy telepít egy webalkalmazási tűzfal (WAF) webes végpontok. A WAF ajánlás bármely nyilvános IP-Címmel (példány szintű IP vagy Load elosztott terhelésű IP), amely rendelkezik egy társított hálózati biztonsági csoport nyílt bejövő webes porttal (80,443) jelennek meg.</br></br>A Security Center javasolja, hogy a WAF elleni támadásokat a webalkalmazások virtuális gépeken, és az App Service Environment célcsoportkezelés segítségével telepítsen. Van egy App Service Environment (ASE) egy [prémium](https://azure.microsoft.com/pricing/details/app-service/) szolgáltatás, amely az Azure App Service-alkalmazások biztonságos futtatása teljesen elkülönített és dedikált környezetet biztosít az Azure App Service csomag lehetőséget. ASE kapcsolatos további információkért tekintse meg a [App Service Environment dokumentációja](../app-service/environment/intro.md).</br></br>A Security Center több webalkalmazás védheti meg ezeket az alkalmazásokat a meglévő WAF-példányok hozzáadásával. |
| [Alkalmazásvédelem véglegesítése](security-center-add-web-application-firewall.md#finalize-application-protection) |A WAF konfigurációjának befejezéséhez forgalmat a WAF berendezésre át irányítva. Ez a javaslat a következő befejezi a szükséges telepítőfájlokat módosításokat. |
| A legújabb támogatott Node.js verzió használata a webalkalmazáshoz | Javasolja, hogy a legfrissebb Node.js-verzió esetében a legújabb biztonsági osztályokkal. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| A CORS nem teszi lehetővé a Függvényalkalmazás eléréséhez minden erőforrás | Javasolja, hogy lehetővé tegye a kommunikál a webes alkalmazások csak a szükséges tartományok. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a függvény-alkalmazás elérésére. |
| Egyéni tartományok használata az Függvényalkalmazás | Egyéni tartományok közös adathalász támadások és más DNS-sel támadások elleni függvényalkalmazás használatát javasolja. |
| Függvényalkalmazás IP-korlátozások konfigurálása | Javasolja, hogy az alkalmazás-hozzáférést IP-címek listájának meghatározását. Az IP-korlátozások használata gyakori támadások ellen védi a függvényalkalmazást. |
| Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül | Hozzáférés korlátozásáról függvényalkalmazások HTTPS-kapcsolaton keresztül csak javasolni a felhasználóknak. |
| Távoli hibakeresést ki kell kapcsolni a Függvényalkalmazás | Javasolja, kapcsolja ki a hibakeresési függvényalkalmazás, ha már nincs rá szüksége. Távoli hibakeresés használatához meg kell nyitni a Függvényalkalmazás bejövő portokat. |
| Webes szoftvercsatornák le kell tiltani a Függvényalkalmazás | Javasolja, hogy alaposan tekintse át a Web Sockets funkciót alkalmazásokon belüli használata. A Websocket protokoll téve a különböző típusú biztonsági fenyegetéseket. |


## <a name="next-steps"></a>További lépések
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Identitás és hozzáférés az Azure Security Center figyelése](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [A gépek és az alkalmazások az Azure Security Center védelme](security-center-virtual-machine-protection.md)
* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-azure-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
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
