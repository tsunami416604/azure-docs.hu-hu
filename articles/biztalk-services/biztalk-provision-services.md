---
title: Azure BizTalk Services létrehozása az Azure Portalon | Microsoft Docs
description: 'Megtudhatja, hogyan építheti ki vagy hozhatja létre az Azure BizTalk Servicest az Azure Portalon: MABS, WABS'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: d47aab9e3d083cd7f51b1c3ba613f421fc1777a6
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764098"
---
# <a name="create-biztalk-services-using-the-azure-portal"></a>BizTalk Services létrehozása az Azure Portallal

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!TIP]
> Az Azure Portalra való bejelentkezéshez Azure-fiókra és Azure-előfizetésre van szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. Lásd: [Ingyenes Azure-próbafiók](https://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>BizTalk-szolgáltatás létrehozása

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

A BizTalk-szolgáltatás állapotától függően néhány művelet nem végezhető el. Ezen műveletek listájáért tekintse meg a [BizTalk-szolgáltatások állapota diagramot](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Kiépítés utáni lépések
* [A tanúsítvány telepítése helyi számítógépre](#InstallCert)
* [Termelésre kész tanúsítvány hozzáadása](#AddCert)
* [A hozzáférés-vezérlési névtér beszerzése](#ACS)

#### <a name="InstallCert"></a>A tanúsítvány telepítése helyi számítógépre

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="AddCert"></a>Termelésre kész tanúsítvány hozzáadása

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="ACS"></a>A hozzáférés-vezérlési névtér beszerzése

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Amikor BizTalk Service-projektet helyez üzembe a Visual Studióból, ezt a hozzáférés-vezérlési névteret írja be. A hozzáférés-vezérlési névtér automatikusan létrejön a BizTalk Services-szolgáltatáshoz.

A hozzáférés-vezérlési értékeket bármilyen alkalmazással használhatja. Az Azure BizTalk-szolgáltatások létrehozásakor ez a hozzáférés-vezérlési névtér vezérli a BizTalk Services-szolgáltatás üzembe helyezésekor végzett hitelesítést. Az előfizetés módosításához vagy a névtér felügyeletéhez válassza a bal oldali navigációs panelen az **ACTIVE DIRECTORY** elemet, majd válassza ki a névteret. A tálca felsorolja a lehetőségeket.

A **Manage** (Felügyelet) gombra kattintva megnyitja a hozzáférés-vezérlési felügyeleti portált. A hozzáférés-vezérlési felügyeleti portálon a BizTalk Services-szolgáltatás **szolgáltatásidentitásokat** használ:  
![ACS-szolgáltatásidentitások a hozzáférés-vezérlési felügyeleti portálon][ACSServiceIdentities]

A hozzáférés-vezérlési szolgáltatásidentitás olyan hitelesítő adatok készlete, amelyekkel az alkalmazások vagy ügyfelek közvetlenül hitelesíthetnek a hozzáférés-vezérléssel, és tokent kaphatnak.

> [!IMPORTANT]
> A BizTalk Services-szolgáltatás a **Tulajdonost** használja az alapértelmezett szolgáltatásidentitáshoz és a **Jelszó** értékéhez. Ha a Szimmetrikus kulcs értéket használja a Jelszó érték helyett, a következő hiba fordulhat elő.<br/><br/>*Nem lehet kapcsolódni a hozzáférés-vezérlési felügyeleti szolgáltatásfiókhoz a megadott hitelesítő adatokkal*
> 
> 

[Az ACS-névtér felügyeletét](https://msdn.microsoft.com/library/azure/hh674478.aspx) ismertető dokumentum tartalmaz néhány útmutatást és javaslatot.

## <a name="requirements-explained"></a>A követelmények részletei
Ezek a követelmények nem érvényesek az ingyenes kiadásra.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Mi szükséges</strong></td>
        <td><strong>Miért szükséges</strong></td>
</tr>
<tr>
<td>Azure-előfizetés</td>
<td>Az előfizetés határozza meg, hogy ki jelentkezhet be az Azure-ba. A fióktulajdonos hozza létre az előfizetést az <a HREF="https://account.windowsazure.com/Subscriptions"> Azure-előfizetésekben</a>.
<br/><br/>
Az Azure-fiók több előfizetéssel rendelkezhet, és bárki felügyelheti, aki erre jogosult. Az Azure-fióktulajdonos például létrehozhat egy <em>BizTalkServiceSubscription</em> nevű előfizetést, és hozzáférést adhat a vállalaton (például ContosoBTSAdmins@live.com) belüli BizTalk-rendszergazdáknak az előfizetéshez. Ebben a forgatókönyvben a BizTalk-rendszergazdák bejelentkeznek az Azure-ba, és teljes rendszergazdai jogosultságokkal rendelkeznek az előfizetés összes üzemeltetett szolgáltatásához, beleértve az Azure BizTalk-szolgáltatásokat is. A BizTalk-rendszergazdák nem az Azure-fiók tulajdonosai, és ezért nem érik el a számlázási információkat.
<br/><br/>További információt az 
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=267577">előfizetések és tárfiókok az Azure-ban való kezelését</a> ismertető szakaszban talál.
</td>
</tr>
<tr>
<td>Azure SQL Database</td>
<td>A BizTalk-szolgáltatás által használt táblázatokat, nézeteket és tárolt eljárásokat tárolja, beleértve a nyomkövető adatokat.
<br/><br/>
BizTalk-szolgáltatások létrehozásakor használhat meglévő Azure SQL Server-kiszolgálót vagy Azure SQL Database-adatbázist, vagy automatikusan létrehozhat egy új kiszolgálót vagy adatbázist.
<br/><br/>
Az SQL Database méretének konfigurálása automatikusan történik. Az alapértelmezett méret általában elég a BizTalk-szolgáltatásokhoz. A méret módosítása hatással van az árra. Lásd: <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=234930">Fiókok és számlázás az Azure SQL Database-ben</a>
<br/><br/>
<strong>Megjegyzések</strong>
<br/>
<ul>
<li> Új Azure SQL Server és adatbázis létrehozásakor az Azure-szolgáltatások automatikusan engedélyezettek. A BizTalk-szolgáltatáshoz az Azure-szolgáltatásoknak engedélyezve kell lenniük.</li>
<li>Ha új Azure SQL Database-adatbázist hoz létre meglévő Azure SQL Server -kiszolgálón, a kiszolgáló tűzfalszabályai nem változnak. Ennek eredményeképpen lehet, hogy más Azure-szolgáltatások nem tudják elérni a kiszolgáló adatbázisait.</li>
</ul>
</td>
</tr>
<tr>
<td>Azure hozzáférés-vezérlési névtér</td>
<td>Az Azure BizTalk Services modullal végez hitelesítést. Amikor BizTalk Service-projektet helyez üzembe a Visual Studióból, ezt a hozzáférés-vezérlési névteret írja be. BizTalk Services-szolgáltatás létrehozásakor a hozzáférés-vezérlési névtér automatikusan létrejön.</td>
</tr>

<tr>
<td>Azure Storage-fiók</td>
<td>Hozzáférést nyújt a BizTalk Services-szolgáltatás által használt táblázatokhoz, blobokhoz és sorokhoz a következők mentése érdekében:

<ul>
<li>A BizTalk Services-szolgáltatást figyelő naplófájlok. </li>
<li>Amikor X12- vagy AS2-egyezményt hoz létre partnerek között, engedélyezheti az Archiválás funkciót az üzenettulajdonságok tárolása érdekében. Ezeket az adatokat a tárfiókba menti a rendszer.</li>
</ul>
<br/>
BizTalk Services-szolgáltatások létrehozásakor használhat meglévő Storage-fiókot, vagy automatikusan létrehozhat egy új Storage-fiókot.
<br/><br/>
Az alapértelmezett tárolóbeállítások elegendőek a BizTalk-szolgáltatásokhoz.
<br/><br/>
Storage-fiók létrehozásakor automatikusan létrejön egy elsődleges kulcs és egy másodlagos kulcs. Ezek a kulcsok vezérlik a tárfiók elérését. A BizTalk Services-szolgáltatás automatikusan az elsődleges kulcsot használja.
<br/><br/>
További információért lásd: <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285671">Storage</a>.
</td>
</tr>

<tr>
<td>SSL személyes tanúsítvány</td>
<td>
Azure BizTalk Services-szolgáltatások létrehozásakor létrejön a BizTalk Services-szolgáltatás nevét tartalmazó HTTPS URL. Ez az URL automatikusan úgy van konfigurálva, hogy önaláírt, csak fejlesztési tanúsítványt használjon. A termeléshez személyes SSL-tanúsítvány szükséges.
<br/><br/>
<strong>SSL-tanúsítvánnyal kapcsolatos fontos információk</strong>

<ul>
<li>A tanúsítvány lejárati dátumának 5 éven belül kell lennie.</li>
<li>Minden személyes tanúsítványhoz jelszó szükséges. Jegyezze meg ezt a jelszót, és ajánlott eljárásként ossza meg a rendszergazdáival.</li>
<li>A tesztelési/fejlesztési környezetekben önaláírt tanúsítványokat használnak. Önaláírt tanúsítvány használatakor importálja a tanúsítványt a személyes tanúsítványtárolójába és a megbízható legfelső szintű hitelesítésszolgáltatók tanúsítványtárolóba.</li>
</ul>
<br/>Amikor a termelési tanúsítvánnyal kapcsolatos kérést elküldi a hitelesítésszolgáltatójának, adja meg a tanúsítvány következő tulajdonságait:
<br/>

<ul>
<li><strong>Kibővített kulcshasználat</strong>: Az Azure BizTalk Services legalább a kiszolgálói hitelesítést igényel.</li>
<li><strong>Köznapi név</strong>: Adja meg az Azure BizTalk Service URL-JÉNEK teljes tartománynevét (FQDN). Lásd ezen cikk <a HREF="#CreateService">BizTalk Services-szolgáltatás létrehozása</a> szakaszát.</li>
</ul>
<br/>
A BizTalk szolgáltatás létrehozása után hozzáadható egy új vagy másik tanúsítvány.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>Hibrid kapcsolatok
Azure BizTalk Servies-szolgáltatások létrehozásakor elérhető a **Hibrid kapcsolatok** lap:

![Hibrid kapcsolatok lap][HybridConnectionTab]

A hibrid kapcsolatok az Azure-webhelyeket vagy Azure-mobilszolgáltatásokat bármely, statikus TCP-portot használó helyszíni erőforráshoz, például SQL Serverhez, MySQL-hez, HTTP Web API-khoz, mobilszolgáltatásokhoz és a legtöbb egyéni webszolgáltatáshoz csatlakoztatják.  A hibrid kapcsolatok és a BizTalk Adapter Service nem ugyanaz. A BizTalk Adapter Service Azure BizTalk Services-szolgáltatásokat csatlakoztat helyszíni üzletági (LOB) rendszerekhez.

 További információért lásd a [Hibrid kapcsolatok](integration-hybrid-connection-overview.md) című szakaszt, ahol a hibrid kapcsolatok létrehozásáról és felügyeletéről is olvashat.

## <a name="next-steps"></a>További lépések
Most, hogy létrejött a BizTalk-szolgáltatás, ismerkedjen meg a különböző [BizTalk Services: Irányítópult, figyelés és méret lapok](biztalk-dashboard-monitor-scale-tabs.md). Az Azure BizTalk-szolgáltatás készen áll az alkalmazásokhoz. Az alkalmazások létrehozásának megkezdéséhez ugorjon az [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=235197) című témakörre.

## <a name="see-also"></a>Lásd még
* [A BizTalk Services: Kiadások diagramja](biztalk-editions-feature-chart.md)<br/>
* [A BizTalk Services: Állapottáblázat](biztalk-service-state-chart.md)<br/>
* [A BizTalk Services: Biztonsági mentés és visszaállítás](biztalk-backup-restore.md)<br/>
* [A BizTalk Services: Szabályozás](biztalk-throttling-thresholds.md)<br/>
* [A BizTalk Services: Kiállító neve és kiállító kulcsa](biztalk-issuer-name-issuer-key.md)<br/>
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Hibrid kapcsolatok](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
