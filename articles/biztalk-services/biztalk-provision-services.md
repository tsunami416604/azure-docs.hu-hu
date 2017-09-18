---
title: "Azure BizTalk Services létrehozása az Azure Portalon | Microsoft Docs"
description: "Megtudhatja, hogyan építheti ki vagy hozhatja létre az Azure BizTalk Servicest az Azure Portalon: MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: eca77b4a82eb67e1755717bb4429f8d450a64dc5
ms.contentlocale: hu-hu
ms.lasthandoff: 09/18/2017

---
# <a name="create-biztalk-services-using-the-azure-portal"></a>BizTalk Services létrehozása az Azure Portallal

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]


> [!TIP]
> Az Azure Portalra való bejelentkezéshez Azure-fiókra és Azure-előfizetésre van szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. Lásd: [Ingyenes Azure-próbafiók](http://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>BizTalk-szolgáltatás létrehozása
A kiválasztott kiadástól függően lehet, hogy nem mindegyik BizTalk-szolgáltatásbeállítás érhető el.

1. Jelentkezzen be az [Azure portálra](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. A navigációs ablak alján kattintson a **NEW** (ÚJ) elemre:  
   ![Válassza a New (Új) gombot][NEWButton]
3. Válassza az **APP SERVICES** (ALKALMAZÁSSZOLGÁLTATÁSOK) > **BIZTALK SERVICE** > **CUSTOM CREATE** (EGYÉNI LÉTREHOZÁS) elemet:  
   ![Válassza a BizTalk Service elemet, majd a Custom Create (Egyéni létrehozás) elemet][NewBizTalkService]
4. Adja meg a BizTalk-szolgáltatásbeállításokat:
   
    <table border="1">
    <tr>
    <td><strong>BizTalk-szolgáltatás neve</strong></td>
    <td>Bármilyen nevet megadhat, de használjon konkrét nevet. Néhány példa:<br/><br/>
    <em>mycompany</em>.biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>.biztalk.windows.net<br/>
    <em>myapplication</em>.biztalk.windows.net<br/><br/>A „.biztalk.windows.net” automatikusan hozzá lesz adva megadott névhez. Ez olyan URL-t hoz létre, amellyel elérheti a BizTalk-szolgáltatást (például <strong>https://<em>myapplication</em>.biztalk.windows.net</strong>).
    </td>
    </tr>
    <tr>
    <td><strong>Kiadás</strong></td>
    <td>Ha a tesztelési/fejlesztési fázisban van, válassza a <strong>Developer</strong> elemet. Ha a termelési fázisban van, a <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk Services: Kiadások diagramja</a> szakasz alapján határozza meg, hogy a <strong>Premium</strong>, <strong>Standard</strong> vagy <strong>Alapszintű</strong> a legmegfelelőbb választás az üzleti forgatókönyvéhez.
    </td>
    </tr>
    <tr>
    <td><strong>Régió</strong></td>
    <td>Válassza ki a BizTalk-szolgáltatás földrajzi helyét.</td>
    </tr>
    <tr>
    <td><strong>Tartomány URL-je</strong></td>
    <td><strong>Választható</strong>. Alapértelmezés szerint a tartomány URL-je a <em>SajázBizTalkSzolgáltatásNeve</em>.biztalk.windows.net. Egyéni tartomány is beírható. Ha a tartománya például a <em>contoso</em>, beírhatja a következőt: <br/><br/>
    <em>Sajátvállalat</em>.contoso.com<br/>
    <em>SajátVállalatSajátAlkalmazás</em>.contoso.com<br/>
    <em>SajátAlkalmazás</em>.contoso.com<br/>
    <em>BizTalkSzolgáltatásNeve</em>.contoso.com<br/>
    </td>
    </tr>
    </table>
Válassza a TOVÁBB nyilat.
5. Adja meg a tároló és az adatbázis beállításait:  <table border="1">
    <tr>
    <td><strong>Megfigyelő/archiváló tárfiók</strong></td>
    <td>Válasszon meglévő Storage-fiókot, vagy hozzon létre egy új Storage-fiókot. <br/><br/>Ha új Storage-fiókot hoz létre, adja meg a <strong>Storage-fiók nevét</strong>.</td>
    </tr>
    <tr>
    <td><strong>Nyomkövető adatbázis</strong></td>
    <td>Ha meglévő Azure SQL Database-adatbázist használ, azt nem használhatja másik BizTalk-szolgáltatás. Meg kell adnia az Azure SQL Database Server létrehozásakor megadott bejelentkezési nevet és jelszót.<br/><br/><strong>TIPP</strong> A nyomkövető adatbázist és a megfigyelő/archiváló tárfiókot ugyanabban a régióban hozza létre, mint a BizTalk-szolgáltatást.</td>
    </tr>
    </table>
Válassza a TOVÁBB nyilat.
6. Adja meg az adatbázis beállításait:  <table border="1">
    <tr>
    <td><strong>Name (Név)</strong></td>
    <td>Akkor érhető el, ha az előző képernyőn az <strong>Új SQL Database-példány létrehozását</strong> választotta.
    <br/><br/>
Írja be a BizTalk-szolgáltatás által használandó SQL Database-adatbázis nevét.</td>
    </tr>
    <tr>
    <td><strong>Kiszolgáló</strong></td>
    <td>Akkor érhető el, ha az előző képernyőn az <strong>Új SQL Database-példány létrehozását</strong> választotta.
    <br/><br/>
Válasszon egy meglévő SQL Database-kiszolgálót vagy hozzon létre új SQL Database-kiszolgálót.</td>
    </tr>
    <tr>
    <td><strong>Kiszolgáló bejelentkezési neve</strong></td>
    <td>Adja meg a bejelentkezési felhasználónevet.</td>
    </tr>
    <tr>
    <td><strong>Kiszolgáló bejelentkezési jelszava</strong></td>
    <td>Adja meg a bejelentkezési jelszót.</td>
    </tr>
    <tr>
    <td><strong>Régió</strong></td>
    <td>Akkor érhető el, ha az <strong>Új SQL Database-példány létrehozását</strong> választotta. Válassza ki az SQL Database földrajzi helyét.</td>
    </tr>
    </table>

Kattintson a pipára a varázsló befejezéséhez. Megjelenik az állapotjelző ikon:  
![Az állapotjelző ikon megjelenik, ha befejeződött][ProgressComplete]

Amikor elkészült, létrejön az Azure BizTalk-szolgáltatás, és készen áll az alkalmazásokhoz. Az alapértelmezett beállítások elegendőek. Ha módosítani kívánja az alapértelmezett beállításokat, válassza a bal oldali navigációs panelen a **BIZTALK SERVICES** elemet, majd válassza ki a BizTalk-szolgáltatását. A fenti [Irányítópult, Figyelő és Méretezés lapokon](biztalk-dashboard-monitor-scale-tabs.md) további beállítások jelennek meg.

A BizTalk-szolgáltatás állapotától függően néhány művelet nem végezhető el. Ezen műveletek listájáért tekintse meg a [BizTalk-szolgáltatások állapota diagramot](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Kiépítés utáni lépések
* [A tanúsítvány telepítése helyi számítógépre](#InstallCert)
* [Termelésre kész tanúsítvány hozzáadása](#AddCert)
* [A hozzáférés-vezérlési névtér beszerzése](#ACS)

#### <a name="InstallCert"></a>A tanúsítvány telepítése helyi számítógépre
A BizTalk-szolgáltatás kiépítésének részeként létrejön egy önaláírt tanúsítvány, amely társítva van a BizTalk-szolgáltatás előfizetésével. Le kell töltenie és telepítenie kell ezt a tanúsítványt azokra a számítógépekre, amelyekről üzembe helyezi a BizTalk-szolgáltatás alkalmazásait, vagy amelyekről üzeneteket küld a BizTalk-szolgáltatás végpontjaira.

1. Jelentkezzen be az [Azure portálra](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Válassza a bal oldali navigációs panelen a **BIZTALK SERVICES** elemet, majd válassza ki a BizTalk-szolgáltatás előfizetését.
3. Kattintson az **Irányítópult** fülre.
4. Válassza az **SSL-tanúsítvány letöltése** elemet:  
   ![SSL-tanúsítvány módosítása][QuickGlance]
5. Kattintson duplán a tanúsítványra, és haladjon végig a varázslón a tanúsítvány telepítéséhez. Győződjön meg arról, hogy a **Megbízható gyökérhitelesítő hatóságok** tárolóba telepítette a tanúsítványt.

#### <a name="AddCert"></a>Termelésre kész tanúsítvány hozzáadása
A BizTalk-szolgáltatások létrehozásakor automatikusan létrejött önaláírt tanúsítvány csak fejlesztési környezetekben használható. A termelési forgatókönyvekhez cserélje le egy termelésre kész tanúsítványra.

1. Az **Irányítópult** lapon válassza az **SSL-tanúsítvány frissítése** lehetőséget.
2. Keresse meg a BizTalk-szolgáltatás nevét tartalmazó személyes SSL-tanúsítványt (*CertificateName*.pfx), írja be a jelszót, majd kattintson a pipára.

#### <a name="ACS"></a>A hozzáférés-vezérlési névtér beszerzése
1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Válassza a bal oldali navigációs panelen a **BIZTALK SERVICES** elemet, majd válassza ki a BizTalk-szolgáltatást.
3. A tálcán válassza a **Connection Information** (Kapcsolódási adatok) lehetőséget:  
   ![Kattintson a Connection Information (Kapcsolatadatok) elemre.][ACSConnectInfo]
4. Másolja a hozzáférés-vezérlési értékeket.

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
<td>Az előfizetés határozza meg, hogy ki jelentkezhet be az Azure Portalra. A fióktulajdonos hozza létre az előfizetést az <a HREF="https://account.windowsazure.com/Subscriptions"> Azure-előfizetésekben</a>.
<br/><br/>
Az Azure-fiók több előfizetéssel rendelkezhet, és bárki felügyelheti, aki erre jogosult. Az Azure-fióktulajdonos például létrehozhat egy <em>BizTalkServiceSubscription</em> nevű előfizetést, és hozzáférést adhat a vállalaton (például ContosoBTSAdmins@live.com) belüli BizTalk-rendszergazdáknak az előfizetéshez. Ebben a forgatókönyvben a BizTalk-rendszergazdák bejelentkeznek az Azure Portalra, és teljes rendszergazdai jogosultságokkal rendelkeznek az előfizetés összes üzemeltetett szolgáltatásához, beleértve az Azure BizTalk-szolgáltatásokat is. A BizTalk-rendszergazdák nem az Azure-fiók tulajdonosai, és ezért nem érik el a számlázási információkat.
<br/><br/>További információt az 
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">előfizetések és tárfiókok az Azure Portalon való kezelését</a> ismertető szakaszban talál.
</td>
</tr>
<tr>
<td>Azure SQL Database</td>
<td>A BizTalk-szolgáltatás által használt táblázatokat, nézeteket és tárolt eljárásokat tárolja, beleértve a nyomkövető adatokat.
<br/><br/>
BizTalk-szolgáltatások létrehozásakor használhat meglévő Azure SQL Server-kiszolgálót vagy Azure SQL Database-adatbázist, vagy automatikusan létrehozhat egy új kiszolgálót vagy adatbázist.
<br/><br/>
Az SQL Database méretének konfigurálása automatikusan történik. Az alapértelmezett méret általában elég a BizTalk-szolgáltatásokhoz. A méret módosítása hatással van az árra. Lásd: <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Fiókok és számlázás az Azure SQL Database-ben</a>
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
<li>A BizTalk Services-szolgáltatást figyelő naplófájlok. A megfigyelés kimenete az Azure Portal **Figyelés** lapján is megjelenik.</li>
<li>Amikor X12- vagy AS2-egyezményt hoz létre partnerek között, engedélyezheti az Archiválás funkciót az üzenettulajdonságok tárolása érdekében. Ezeket az adatokat a tárfiókba menti a rendszer.</li>
</ul>
<br/>
BizTalk Services-szolgáltatások létrehozásakor használhat meglévő Storage-fiókot, vagy automatikusan létrehozhat egy új Storage-fiókot.
<br/><br/>
Az alapértelmezett tárolóbeállítások elegendőek a BizTalk-szolgáltatásokhoz.
<br/><br/>
Storage-fiók létrehozásakor automatikusan létrejön egy elsődleges kulcs és egy másodlagos kulcs. Ezek a kulcsok vezérlik a tárfiók elérését. A BizTalk Services-szolgáltatás automatikusan az elsődleges kulcsot használja.
<br/><br/>
További információért lásd: <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Storage</a>.
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
<li><strong>Kibővített kulcshasználat</strong>: Az Azure BizTalk Services legalább kiszolgálói hitelesítést igényel.</li>
<li><strong>Köznapi név</strong>: Adja meg az Azure BizTalk Services-szolgáltatás URL-jének teljes tartománynevét (FQDN-jét). Lásd ezen cikk <a HREF="#CreateService">BizTalk Services-szolgáltatás létrehozása</a> szakaszát.</li>
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

## <a name="next-steps"></a>Következő lépések
Most, hogy létrejött a BizTalk Services-szolgáltatás, ismerje meg a különböző [BizTalk Services: Irányítópult, Figyelő és Méret lapokat](biztalk-dashboard-monitor-scale-tabs.md). Az Azure BizTalk-szolgáltatás készen áll az alkalmazásokhoz. Az alkalmazások létrehozásának megkezdéséhez ugorjon az [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197) című témakörre.

## <a name="see-also"></a>Lásd még:
* [BizTalk Services: Kiadások diagramja](biztalk-editions-feature-chart.md)<br/>
* [BizTalk Services: Állapottáblázat](biztalk-service-state-chart.md)<br/>
* [BizTalk Services: Biztonsági mentés és visszaállítás](biztalk-backup-restore.md)<br/>
* [BizTalk Services: Szabályozás](biztalk-throttling-thresholds.md)<br/>
* [BizTalk Services: Kiállító neve és kiállító kulcsa](biztalk-issuer-name-issuer-key.md)<br/>
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Hibrid kapcsolatok](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png

