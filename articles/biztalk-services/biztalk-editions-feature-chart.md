---
title: A BizTalk Services-kiadások funkcióinak megismerése | Microsoft Docs
description: 'Hasonlítsa össze a BizTalk Services-kiadások képességeit: Ingyenes, fejlesztői, alapszintű, Standard és prémium. MABS, WABS.'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: c589629f-06b1-44bb-b8ca-1db71826ea59
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: d16bf73c1d9871e4de8940e4ab9bbd7ed0f39384
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765628"
---
# <a name="biztalk-services-editions-chart"></a>BizTalk Services: Kiadások diagramja

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Az Azure BizTalk Services számos kiadást biztosít. A jelen cikk segítségével határozhatja meg, hogy melyik kiadás felel meg a forgatókönyvnek és az üzleti igényeknek.

## <a name="compare-the-editions"></a>A kiadások összehasonlítása
**Ingyenes (előzetes verzió)**

Hibrid kapcsolatokat hozhat létre és felügyelhet. A hibrid kapcsolatokkal könnyedén csatlakoztathat Azure-webhelyet egy helyszíni rendszerhez, például az SQL Serverhez.

**Fejlesztői**

Hibrid kapcsolatokat, a kereskedelmi partnerek felügyeletére szolgáló, könnyen használható portállal feldolgozható EAI- és EDI-üzenetfeldolgozást, az általános EDI-sémák támogatását, valamint az X12-n és AS2-n keresztüli részletes EDI-feldolgozást tartalmaz. A használatával létrehozhat általános EAI-forgatókönyveket, amelyek a felhőbeli szolgáltatásokat csatlakoztatják a HTTP/S, REST, FTP, WCF és SFTP protokollal üzenetek olvasása és írása érdekében.  Kihasználhatja a helyszíni LOB-rendszerekkel létesített kapcsolatok előnyeit a használatra kész SAP-, Oracle eBusiness-, Oracle DB-, Siebel- és SQL Server-adapterekkel. Fejlesztőközpontú környezetet használja a Visual Studio eszközeinek és egyszerű fejlesztését és üzembe helyezéséhez. Fejlesztési és tesztelési célokra van korlátozva szolgáltatói szerződés (SLA) nélkül.

**Basic**

Tartalmazza a Fejlesztői kiadás legtöbb képességét, de több hibrid kapcsolatot, EAI-hidat, EDI-egyezményt és BizTalk Adapter Pack-kapcsolatot tartalmaz. Magas rendelkezésre állást is kínál, valamint a méretezés lehetőségét szolgáltatói szerződéssel (SLA-val).

**Standard**

Tartalmazza az Alapszintű kiadás összes képességét, de több hibrid kapcsolatot, EAI-hidat, EDI-egyezményt és BizTalk Adapter Pack-kapcsolatot tartalmaz. Magas rendelkezésre állást is kínál, valamint a méretezés lehetőségét szolgáltatói szerződéssel (SLA-val).

**Prémium**

Tartalmazza az összes Standard képességet, de több hibrid kapcsolatot, EAI-hidat, EDI-egyezményt és BizTalk Adapter Pack-kapcsolatot tartalmaz. Emellett archiválást, magas rendelkezésre állást is kínál, valamint a méretezés lehetőségét szolgáltatói szerződéssel (SLA-val).

## <a name="editions-chart"></a>Kiadások diagramja
Az alábbi táblázat tartalmazza a különbségeket.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Ingyenes (előzetes verzió)</th>
        <th>Fejlesztői</th>
        <th>Alapszintű</th>
        <th>Standard</th>
        <th>Prémium</th>
</tr>

<tr>
<td><strong>Kezdő ár</strong></td>
<td colspan="5"><a HREF="https://go.microsoft.com/fwlink/p/?LinkID=304011"> Az Azure BizTalk Services árképzése</a> <br/><br/> <a HREF="https://azure.microsoft.com/pricing/calculator/?scenario=full">Azure díjkalkulátor</a></td>
</tr>
<tr>
<td><strong>Alapértelmezett minimális konfiguráció</strong></td>
<td>1 ingyenes egység</td>
<td>1 fejlesztői egység</td>
<td>1 alapszintű egység</td>
<td>1 standard egység</td>
<td>1 prémium egység</td>
</tr>
<tr>
<td><strong>Méretezés</strong></td>
<td>Nincs méretezés</td>
<td>Nincs méretezés</td>
<td>Igen, 1 alapszintű egységnyi növekményekben</td>
<td>Igen, 1 standard egységnyi növekményekben</td>
<td>Igen, 1 prémium egységnyi növekményekben</td>
</tr>
<tr>
<td><strong>Maximális engedélyezett horizontális felskálázás</strong></td>
<td>Nincs méretezés</td>
<td>Nincs méretezés</td>
<td>Legfeljebb 8 egység</td>
<td>Legfeljebb 8 egység</td>
<td>Legfeljebb 8 egység</td>
</tr>
<tr>
<td><strong>EAI-hidak egységenként</strong></td>
<td>Nem tartalmazza</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
TPM-egyezményeket tartalmaz</td>
<td>Nem tartalmazza</td>
<td>Tartalmazza 10 egyezmény egységenként.</td>
<td>Tartalmazza 50 egyezmény egységenként.</td>
<td>Tartalmazza 250 egyezmény egységenként.</td>
<td>Tartalmazza 1000 egyezmény egységenként.</td>
</tr>
<tr>
<td><strong>Hibrid kapcsolatok egységenként</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Hibrid kapcsolat adatátvitele (GB) egységenként</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>BizTalk Adapter Service kapcsolatai helyszíni LOB-rendszereken</strong></td>
<td>Nem tartalmazza</td>
<td>1 kapcsolat</td>
<td>2 kapcsolat</td>
<td>5 kapcsolat</td>
<td>25 kapcsolat</td>
</tr>
<tr>
<td align="left"><strong>Támogatott protokollok/rendszerek:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Azure-blob</li>
<li>REST API-k</li>
</ul>
</td>
<td>Nem tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
</tr>
<tr>
<td><strong>Magas rendelkezésre állás</strong>
<br/><br/>
A szolgáltatói szerződést (SLA) lásd: <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=304011">Az Azure BizTalk Services árképzése</a>.
</td>
<td>Nem tartalmazza</td>
<td>Nem tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
</tr>
<tr>
<td><strong>Biztonsági mentés és visszaállítás</strong></td>
<td>Nem tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
</tr>
<tr>
<td><strong>Nyomon követés</strong></td>
<td>Nem tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
</tr>
<tr>
<td><strong>Archiválás</strong><br/><br/>
Tartalmazza a számla letagadhatatlanságát (NRR) és a követett üzenetek letöltését</td>
<td>Nem tartalmazza</td>
<td>Tartalmazza</td>
<td>Nem tartalmazza</td>
<td>Nem tartalmazza</td>
<td>Tartalmazza</td>
</tr>
<tr>
<td><strong>Egyéni kód használata</strong></td>
<td>Nem tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
</tr>
<tr>
<td><strong>Átalakítások használata, beleértve az egyéni XSLT-t</strong></td>
<td>Nem tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
<td>Tartalmazza</td>
</tr>
</table>

> [!NOTE]
> A hardverhibák elleni rugalmasság érdekében a magas rendelkezésre állás azt jelenti, hogy több virtuális gép van egyetlen BizTalk-egységben.
> 
> 

## <a name="faqs"></a>Gyakori kérdések
#### <a name="what-is-a-biztalk-unit"></a>Mi a BizTalk-egység?
Az „egység” az Azure BizTalk Services telepítésének atomi szintje. Mindegyik kiadás egységének számítási kapacitása és memóriája különböző. Az alapszintű egység például több számításra képes, mint a fejlesztői, a standard több számításra képes, mint az alapszintű stb. A BizTalk Service méretezése egységekben történik.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Mi a különbség a BizTalk Services és az Azure BizTalk virtuális gépek között?
A BizTalk Services valódi PaaS-architektúrát biztosít a felhőben kiépített integrációs megoldásokhoz. A PaaS modellel teljes mértékben az alkalmazás logikájára összpontosíthat, és a Microsoftra hagyhatja az infrastruktúra felügyeletét, beleértve a következőket:

* Nem kell felügyelnie vagy javítania a virtuális gépeket.
* A Microsoft biztosítja a rendelkezésre állást.
* A méretet igény szerint szabályozhatja, és egyszerűen több vagy kevesebb kapacitást kérhet az Azure Portalon keresztül.

Az Azure Virtual Machinesben futó BizTalk Server IaaS-architektúrát biztosít. A helyszíni környezethez hasonlóan hozhat létre és konfigurálhat virtuális gépeket, így megkönnyítve a meglévő alkalmazások futtatását a felhőben anélkül, hogy a kódot módosítania kellene. Az IaaS használata esetén továbbra is felelős Ön a virtuális gépek konfigurálásáért, a virtuális gépek felügyeletéért (például a szoftverek és az operációs rendszer javításainak telepítéséért) és az alkalmazás magas rendelkezésre állásához való kialakításáért.

A BizTalk Services segítségével az infrastruktúra kezelését megkönnyítő integrációs megoldásokat készíthet. Ha gyorsan szeretné áttelepíteni a meglévő BizTalk-megoldásokat, vagy igény szerinti környezetet keres a BizTalk Server-alkalmazások fejlesztéséhez és teszteléséhez, Azure virtuális gépen használja a BizTalk Servert.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Mi a különbség a BizTalk Adapter Service és a hibrid kapcsolatok között?
A BizTalk Adapter Service-t Azure BizTalk Service használja. A BizTalk Adapter Service a BizTalk Adapter Pack használatával csatlakozik egy helyszíni üzletági (LOB) rendszerhez. A hibrid kapcsolattal könnyen és kényelmesen csatlakozhat Azure-alkalmazásokat, például az Azure App Service Web Apps szolgáltatását vagy az Azure Mobile Servicest egy helyszíni erőforráshoz.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>Mit jelent a „hibrid kapcsolat adatátvitele (GB) egységenként”? Ez percenként/óránként/naponta/hetente/havonta értendő? Mi történik a korlát elérésekor?
A hibrid kapcsolat egységenkénti költsége a BizTalk Services kiadásától függ. A költségek egyszerűen attól függnek, hogy mennyi adatot visz át. Napi 10 GB adat átvitele például kevesebbe kerül, mint napi 100 GB adat átvitele. A BizTalk Services [díjkalkulátorával](https://azure.microsoft.com/pricing/calculator/?scenario=full) határozhatja meg a pontos költségeket. A korlátok érvényesítése általában naponta történik. A korlát elérése után GB-onként 1 dolláros díjat kell fizetnie a túllépésért.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Amikor egyezményt hozok létre a BizTalk Servicesben, miért kettővel nő a hidak száma egy helyett?
Minden egyezmény két különböző hídból áll: egy küldőoldali kommunikációs hídból és egy fogadóoldali kommunikációs hídból.

#### <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>Mi történik, amikor elérem a hidak számának vagy az egyezményeknek a kvótakorlátját?
Nem tud új hidakat üzembe helyezni és új egyezményeket létrehozni. Továbbiak üzembe helyezéséhez a BizTalk szolgáltatás vertikális felskálázásával több egységet kell beszereznie, vagy magasabb kiadásra kell frissítenie.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Hogyan hajthatok végre áttelepítést át a BizTalk Services egyik szintjéről egy másikra?
Az ingyenes kiadás nem telepíthető át vagy skálázható fel másik szintre, és nem készíthető róla biztonsági másolat egy másik szinten való visszaállításhoz. Ha másik rétegre van szüksége, hozzon létre egy új BizTalk szolgáltatást az új szinten. Az ingyenes kiadásban létrehozott összes összetevőt, beleértve a hibrid kapcsolatokat is, újra létre kell hozni az új BizTalk szolgáltatásban. 

A többi kiadás esetén biztonsági mentéssel és visszaállítással telepítheti át az összetevőket az egyik rétegről a másikra. Készítsen biztonsági másolatot például az összetevőkről a Standard rétegen, majd állítsa vissza azokat a Prémium rétegen. [A BizTalk Services: Biztonsági mentés és visszaállítás](biztalk-backup-restore.md) ismerteti a támogatott áttelepítési útvonalakat, és felsorolja, mely összetevőkről készül biztonsági másolat. Vegye figyelembe, hogy a hibrid kapcsolatokról nem készül biztonsági másolat. Az új szintre készített biztonsági mentés és visszaállítás után újra létre kell hoznia a hibrid kapcsolatokat.  

#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>A BizTalk Adapter Service szerepel a szolgáltatásban? Hogyan kapom meg a szoftvert?
Igen, a BizTalk Adapter Service és a BizTalk Adapter Pack szerepel a Azure BizTalk Services SDK [letöltésben](https://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>További lépések
Azure BizTalk Services létrehozása az Azure Portalon, lépjen a [BizTalk Services: Kiépítés az Azure portal használatával](biztalk-provision-services.md). Az alkalmazások létrehozásának megkezdéséhez ugorjon az [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=235197) című témakörre.

## <a name="additional-resources"></a>További források
* [A BizTalk Services: Kiépítés az Azure portal használatával](biztalk-provision-services.md)<br/>
* [A BizTalk Services: Kiépítési állapot diagramja](biztalk-service-state-chart.md)<br/>
* [A BizTalk Services: Irányítópult, figyelés és méret lapok](biztalk-dashboard-monitor-scale-tabs.md)<br/>
* [A BizTalk Services: Biztonsági mentés és visszaállítás](biztalk-backup-restore.md)<br/>
* [A BizTalk Services: Szabályozás](biztalk-throttling-thresholds.md)<br/>
* [A BizTalk Services: Kiállító neve és kiállító kulcsa](biztalk-issuer-name-issuer-key.md)<br/>
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

