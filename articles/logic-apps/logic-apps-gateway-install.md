---
title: "Telepítse a helyszíni adatok gateway - Azure Logic Apps |} Microsoft Docs"
description: "Hozzáférés a helyszíni adatforrások, előtt telepítse az helyszíni data gateway gyors adatátvitel és a titkosításhoz a helyszíni adatforrások és a logic Apps alkalmazások között"
keywords: "adatok, a helyszíni, az adatok átvitele, a titkosítás és a adatforrások eléréséhez"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: b3c1e2afadea91f010c3e4b43206b6d30a75ec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Az a helyszíni átjáró telepítése az Azure Logic Apps

A logic apps a helyszíni adatforrások eléréséhez, telepítse és állítsa be az a helyszíni átjáró. Az átjáró működik, amely gyors adatátvitel és a titkosítás a helyszínen és a logic Apps alkalmazások közötti hídként. Az átjáró továbbítja a titkosított csatornákon keresztül az Azure Service Bus helyszíni forrásból származó adatokat. Az összes forgalom származik, az átjáró ügynök biztonságos kimenő forgalmát. További információ [az átjáró működése](#gateway-cloud-service).

Az átjáró a helyszíni e adatforrásokkal létesített kapcsolatokat is támogatja:

*   BizTalk Server 2016
*   DB2  
*   Fájlrendszer
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP-alkalmazáskiszolgáló 
*   SAP üzenet kiszolgáló
*   SharePoint
*   SQL Server
*   Teradata

A lépések bemutatják, hogyan először telepítse a helyszíni adatátjáró előtt [az átjáró és a logic Apps alkalmazások közötti kapcsolat beállítása](./logic-apps-gateway-connection.md). További információ a támogatott összekötők: [az Azure Logic Apps összekötők](https://docs.microsoft.com/azure/connectors/apis-list). 

Az átjáró más szolgáltatásokkal való használatával kapcsolatos információkért lásd: ezek a cikkek:

*   [Microsoft Power BI helyszíni adatátjáró](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Az Azure Analysis Services helyi adatátjáró](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow helyszíni adatátjáró](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps helyszíni adatátjáró](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>Követelmények

**Minimális**:

* .NET 4.5 keretrendszer
* 64 bites Windows 7 vagy Windows Server 2008 R2 (vagy újabb)

**Ajánlott**:

* 8 mag Processzor
* 8 GB memória
* 64 bites Windows 2012 R2 (vagy újabb)

**Fontos tudnivalók találhatók**:

* Az a helyszíni átjáró telepítése csak a helyi számítógépen.
Az átjáró nem telepíthet tartományvezérlőre.

   > [!TIP]
   > Nem kell telepíteni az átjárót a adatforrásként ugyanazon a számítógépen. Késés csökkentése érdekében érdemes a lehető az adatforráshoz, vagy ugyanazon a számítógépen, feltéve, hogy Ön jogosult az átjáró is telepítheti.

* Az átjáró nem telepíthető olyan számítógépre, amelyen kikapcsolja, alvó állapotba vagy nem csatlakozik az internethez, mivel az átjáró nem fut ilyen körülmények. Emellett átjáró teljesítménye csökkenhet, vezeték nélküli hálózaton keresztül.

* A telepítés során be kell jelentkeznie a egy [munkahelyi vagy iskolai fiók](https://docs.microsoft.com/azure/active-directory/sign-up-organization) , amely az Azure Active Directory (Azure AD), nem Microsoft-fiók felügyeli.

  > [!TIP]
  > Ha szeretné használni a Microsoft-fiókkal, amely a Visual Studio MSDN-előfizetés, először rendelkezik [hozzon létre egy könyvtárat (bérlői) az Azure Active Directoryban](../active-directory/develop/active-directory-howto-tenant.md) a Microsoft-fiókot, vagy használja az alapértelmezett mappát. Jelszóval rendelkező felhasználó hozzáadása a könyvtárat, majd a felhasználói hozzáférést az előfizetéséhez. Ezután bejelentkezhet átjáró telepítéskor a felhasználónévvel és jelszóval.

  Kell használnia ugyanazzal a munkahelyi vagy iskolai fiók később az Azure-portálon létrehozásakor, és rendelje hozzá az átjáró telepítése egy átjáró-erőforráshoz. Az átjáró-erőforráshoz, majd válassza ki, ha a kapcsolatot a Logic Apps alkalmazást és a helyszíni adatforrás között. [Miért kell használni az Azure AD munkahelyi vagy iskolai fiókkal?](#why-azure-work-school-account)

  > [!TIP]
  > Ha regisztrált az Office 365 ajánlat, és nem adja meg a tényleges munkahelyi e-mail címét, a bejelentkezési címe nézhet jeff@contoso.onmicrosoft.com. 

* Ha egy meglévő átjárót, a korábbi verziónál 14.16.6317.4 telepítő beállított, az átjáró helye nem módosítható a legújabb telepítő futtatásával. A legújabb telepítő használatával azonban a helyet, amelyet helyette az új átjárót a beállítása.
  
  Ha egy átjáró installer korábbi verziója 14.16.6317.4, de még nem telepítette az átjárót, letöltheti és a legújabb telepítővel.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>Az adatátjáró telepítése

1.  [Töltse le és futtassa az átjáró telepítőt a helyi számítógép](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Tekintse át és fogadja el a használati feltételei és adatvédelmi nyilatkozata.

3. Adja meg az elérési út a helyi számítógépen, amelyen az átjáró telepíteni szeretné.

4. Amikor a rendszer kéri, jelentkezzen be a a Azure munkahelyi vagy iskolai fiókkal, nem Microsoft-fiókkal.

   ![Jelentkezzen be Azure munkahelyi vagy iskolai fiók](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. A telepített átjárót most regisztrálni a [átjáró felhőszolgáltatáshoz](#gateway-cloud-service). Válasszon **ezen a számítógépen egy új átjáró regisztrálása**.

   Az átjáró felhőszolgáltatáshoz titkosítja, és tárolja az adatforrás hitelesítő adatai és az átjáró beállításai között. 
   A szolgáltatás irányítja a helyszíni lekérdezések és az eredmények a Logic Apps alkalmazást, az a helyszíni átjáró és az adatforrás között is.

6. Adjon meg egy nevet az átjáró telepítése. Hozzon létre helyreállítási kulcsot, majd erősítse meg a helyreállítási kulcsot. 

   > [!IMPORTANT] 
   > A helyreállítási kulcs legalább nyolc karakterből kell állnia. Győződjön meg arról, hogy mentse el, és a kulcs tartsa biztonságos helyen. Ha meg szeretné áttelepíteni, állítsa vissza, vagy vegye át egy meglévő átjárót is kell ezt a kulcsot.

   1. Az átjáró felhőszolgáltatás és az Azure Service Bus átjáró által használt alapértelmezett régióját módosításához válassza **módosítása régió**.

      ![Régió módosítása](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      Az alapértelmezett terület az Azure AD bérlőhöz társított régióban.

   2. A következő ablaktáblára, nyissa meg a **régiót válassza** kiválasztása egy másik régióban.

      ![Válasszon ki egy másik régiót](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      Például akkor lehet, hogy ugyanabban a régióban, a Logic Apps alkalmazást, vagy válasszon a helyszíni adatforrás legközelebb eső régiót, a késés csökkentése érdekében. Az átjáró-erőforráshoz és logikai alkalmazás különböző helyen is rendelkezhetnek.

      > [!IMPORTANT]
      > Ebben a régióban a telepítés után nem módosítható. Ebben a régióban is határozza meg, és korlátozza a hely, ahol az átjáró létrehozhat az Azure-erőforrás. Ezért az átjáró erőforrás létrehozásakor az Azure-ban győződjön meg arról, hogy az erőforrás helye megegyezik-e az átjáró telepítésekor kiválasztott régióban.
      > 
      > Ha azt szeretné, egy másik régióban később az átjáró használatára, be kell állítania egy új átjárót.

   3. Ha elkészült, válassza ki a **végzett**.

7. Most kövesse az alábbi lépéseket az Azure portálon, így [hozzon létre egy Azure-erőforrás az átjáró](../logic-apps/logic-apps-gateway-connection.md). 

További információ [az átjáró működése](#gateway-cloud-service).

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>Telepítse át, állítsa vissza, vagy vegye át egy meglévő átjáróhoz

A feladatok végrehajtásához rendelkeznie kell a helyreállítási kulcs van megadva, az átjáró telepítésekor.

1. A számítógép Start menüből **helyszíni adatátjáró**.

2. Miután a telepítő nyit meg, jelentkezzen be az Azure ugyanazzal a munkahelyi vagy iskolai fiók, amely korábban az átjáró telepítéséhez használt.

3. Válasszon **áttelepítése, visszaállítása vagy átvétele egy meglévő átjáróhoz**.

4. Adja meg az átjáró áttelepítése, visszaállítása vagy vegye át kívánt nevét és a helyreállítási kulcsot.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-szolgáltatásfiók

Az a helyszíni átjáró egy Windows fut, és be van állítva a használandó `NT SERVICE\PBIEgwService` a Windows szolgáltatás bejelentkezési hitelesítő adatokat. Alapértelmezés szerint az átjáró a "Bejelentkezés szolgáltatásként" jogosultsággal rendelkezik a gép, amelyre telepíti az átjárót. Hozhatja létre, és kezelheti az Azure-portálon az átjáró, a a Windows szolgáltatás fióknak rendelkeznie kell legalább **közreműködő** engedélyek. 

> [!NOTE]
> A Windows-fiók eltér a helyi adatforrások, való kapcsolódáshoz használt fiók és az Azure-ból munkahelyi vagy iskolai fiók használatával jelentkezzen be a felhőalapú szolgáltatások.

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>Indítsa újra az átjárót

Mint bármely más Windows-szolgáltatás indítsa el, és állítsa le a szolgáltatást, többféle módon. Például nyisson meg egy parancssort emelt szintű engedélyekkel a számítógépen, amelyen az átjáró fut-e, és futtassa az alábbi parancsokat vagy:

* Állítsa le a szolgáltatást, futtassa a parancsot:
  
    `net stop PBIEgwService`

* A szolgáltatás elindításához futtassa ezt a parancsot:
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>Egy tűzfal vagy a proxy konfigurálása

Az átjáró egy kimenő kapcsolatot hoz [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Adja meg az átjáró a proxy adatait, tekintse meg [konfigurálja a proxybeállításokat](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Ellenőrizze, hogy a tűzfal, vagy a proxy, előfordulhat, hogy kapcsolat tiltása, győződjön meg arról, hogy a gép ténylegesen kapcsolódhat az internethez és a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Futtassa ezt a parancsot egy PowerShell-parancssorba:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> A parancs csak teszteli a hálózati kapcsolatot és az Azure Service Bus-kapcsolatokat. Ezért a parancs nincs bármilyen formában az átjáró vagy az átjáró felhőalapú szolgáltatás, amely titkosítja, és tárolja a hitelesítő adatait, és az átjáró beállításai között. 
>
> Ez a parancs is csak a Windows Server 2012 R2 vagy későbbi, és a Windows 8.1 vagy újabb. A korábbi operációsrendszer-verziók a Telnet segítségével kapcsolat tesztelése. További információ [Azure Service Bus vagy hibrid megoldások](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Az eredmények ebben a példában hasonlóan kell kinéznie:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Ha **TcpTestSucceeded** értéke nem **igaz**, előfordulhat, hogy blokkolja tűzfal. Ha azt szeretné, hogy átfogó, helyettesítse a **számítógépnév** és **Port** értékek alatt szereplő értékekkel [portok konfigurálása](#configure-ports) ebben a témakörben.

A tűzfal is megakadályozhatja, hogy az Azure Service Bus által az Azure adatközpontjaiban kapcsolatok. Ez a forgatókönyv akkor fordul elő, ha jóváhagyja (feloldása) azokat az adott régióban adatközpontok összes IP-címet. Azon IP-címek [beolvasása az Azure IP-címek listája itt](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Portok konfigurálása

Az átjáró egy kimenő kapcsolatot hoz [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) és kimenő portok folytat: TCP 443-as (alapértelmezett), 5671, 5672, 9350 – 9354-es. Az átjáró nincs szükség a bejövő portra. További információ [Azure Service Bus vagy hibrid megoldások](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| TARTOMÁNYNEVEK | KIMENŐ PORTOK | LEÍRÁS |
| --- | --- | --- |
| *. analysis.windows.net | 443 | HTTPS | 
| *. login.windows.net | 443 | HTTPS | 
| *. servicebus.windows.net | 5671-5672 | Speciális üzenetsor-kezelési protokoll (AMQP) | 
| *. servicebus.windows.net | 443, 9350-9354 | A Service Bus Relay (a 443-as kér a hozzáférés-vezérlés jogkivonat beszerzése) TCP-n keresztül figyelői | 
| *. frontend.clouddatahub.net | 443 | HTTPS | 
| *. core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *. msftncsi.com | 443 | Internet kapcsolat tesztelése, ha az átjáró nem érhető el a Power BI szolgáltatás segítségével. | 

Ha a tartományok helyett IP-címek jóváhagyása, töltse le és használja a [Microsoft Azure Datacenter IP-címtartományok lista](https://www.microsoft.com/download/details.aspx?id=41653). Bizonyos esetekben az Azure Service Bus-kapcsolatokat válnak, teljes tartománynevek helyett IP-címet.

<a name="gateway-cloud-service"></a>
## <a name="how-does-the-data-gateway-work"></a>Hogyan működik az átjáró?

Az átjáró elősegíti a Logic Apps alkalmazást, az átjáró felhőalapú szolgáltatás és a helyszíni adatforrás között gyors és biztonságos kommunikációt. 

![diagram-for-on-premises-Data-Gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Ezért amikor a felhasználó, a felhőben kommunikál a helyszíni adatforráshoz kapcsolódó elemet:

1. Az átjáró felhőszolgáltatáshoz lekérdezést hoz létre, és a titkosított hitelesítő adatokat az adatforráshoz, és a lekérdezést küld a várólista feldolgozása az átjáró.

2. Az átjáró felhőszolgáltatáshoz elemzi a lekérdezést, és leküldéses értesítések a kérelmet az Azure Service Bus.

3. Az a helyszíni átjáró kérdezze le az Azure Service Bus, a függőben lévő kérelmek.

4. Az átjáró a lekérdezés lekérdezi, visszafejti a hitelesítő adatokat, és kapcsolódik az adatforráshoz, ezeket a hitelesítő adatokkal.

5. Az átjáró a lekérdezést küld az adatforrás-végrehajtásra.

6. Az eredményeket az adatforrásból kerülnek vissza az átjáró, majd az átjáró felhőszolgáltatáshoz. Az átjáró felhőszolgáltatáshoz majd értékeket használja.

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="general"></a>Általános kérdések

**A Q**: van egy átjáró adatforrások, például az SQL Azure a felhőben? <br/>
**A**: nem. Átjáró csatlakozik a helyi adatforrások csak.

**A Q**: az adatforrással azonos gépen kell telepíteni az átjárót rendelkezik? <br/>
**A**: nem. Az átjáró kapcsolódik az adatforráshoz, a megadott kapcsolati információk használatával. Egy ügyfélalkalmazás abban az értelemben, fontolja meg az átjáró. Az átjáró éppen kell képes csatlakozni a megadott kiszolgáló neve.

<a name="why-azure-work-school-account"></a>

**A Q**: Miért kell I használja egy Azure munkahelyi vagy iskolai fiókkal bejelentkezni? <br/>
**A**: csak használja az Azure munkahelyi vagy iskolai fiókkal, az a helyszíni átjáró telepítésekor. A bejelentkezési fiók egy Azure Active Directory (Azure AD) által felügyelt bérlői tárolja. Általában az Azure AD-fiókot egyszerű felhasználónév (UPN) felel meg az e-mail cím.

**A Q**: a hitelesítő adataimat tároló? <br/>
**A**: adatforráshoz megadott hitelesítő adatok titkosítva, és az átjáró felhőszolgáltatásban tárolt. A hitelesítő adatokat a helyszíni adatok átjáróra lesznek visszafejtve.

**A Q**: vannak-e hálózati sávszélesség vonatkozó követelmények? <br/>
**A**: javasoljuk, hogy rendelkezik-e a hálózati kapcsolat megfelelő teljesítményt. Minden környezet más, és a küldött adatok mennyisége hatással van az eredményeket. A helyszíni és az Azure adatközpontjaiban közötti átviteli szintű segíthet az ExpressRoute segítségével.
A külső eszköz Azure sebesség teszt alkalmazás segítségével fel tudja mérni a teljesítményt.

**A Q**: Mi az a várakozási lekérdezések futtatását adatforráshoz az átjáró? Mi az a legjobb architektúra? <br/>
**A**: hálózati késés csökkentésére, telepítse az átjáró lehető az adatforrás a lehető legjobban hasonlítson. Az átjáró telepíthető a konkrét adatforrásokhoz, ha a közelségi kapcsolat minimalizálja a várakozási bevezetett. Túl vegye figyelembe az adatközpontokban. Például ha a szolgáltatás használja, az USA nyugati régiója adatközpontban, és egy Azure virtuális Gépen található SQL-kiszolgálóval rendelkezik, az Azure virtuális gép kell lennie az USA nyugati régiója túl. A közelségi kapcsolat minimálisra csökkenti a késést, és ezzel elkerülheti a kimenő forgalom díjak, az Azure virtuális gépen.

**A Q**: hogyan eredmények küldött vissza a felhőben? <br/>
**A**: eredmények az Azure Service Bus keresztül zajlik.

**A Q**: vannak-e a bejövő kapcsolatokat az átjáróra a felhőből? <br/>
**A**: nem. Az átjáró Azure Service Bus kimenő kapcsolatokat használja.

**A Q**: Mi történik, ha az általam letiltottak kimenő kapcsolatokat? Mit kell megnyitni? <br/>
**A**: a portok és a gazdagépekhez, amelyek az átjárót használja.

**A Q**: a tényleges Windows-szolgáltatás úgynevezett?<br/>
**A**: A szolgáltatások, az átjáró Power BI Enterprise Gateway szolgáltatás neve.

**A Q**: Azure Active Directory-fiókkal az átjáró Windows-szolgáltatás futtatható? <br/>
**A**: nem. A Windows-szolgáltatás egy érvényes Windows-fiókkal kell rendelkeznie. Alapértelmezés szerint a szolgáltatás fut a szolgáltatás SID NT SERVICE\PBIEgwService.

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

**A Q**: milyen beállítások érhetők el a vész-helyreállítási? <br/>
**A**: a helyreállítási kulcs használatával állítsa vissza, vagy helyezze át az átjárót. Az átjáró telepítésekor adja meg a helyreállítási kulcsot.

**A Q**: Mi az az előnye, hogy a helyreállítási kulcs? <br/>
**A**: A helyreállítási kulcs biztosítja az áttelepítéshez, vagy az átjáró beállításainak katasztrófa utáni helyreállításhoz.

**A Q**: vannak-e bármilyen tervek engedélyezése az átjáró magas rendelkezésre állás elérésére? <br/>
**A**: ezek a forgatókönyvek a programba, de még nincs ütemterv.

## <a name="troubleshooting"></a>Hibaelhárítás

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**A Q**: Hogyan tekinthető meg mi lekérdezések folyamatban van a helyszíni adatforrás küldött? <br/>
**A**: engedélyezheti a lekérdezés nyomon követése, beleértve a küldött lekérdezések. Ne felejtse el módosítani a lekérdezés vissza az eredeti értéket, miután befejezte a hibakeresési nyomkövetés. A lekérdezés nyomon követése engedélyezve van a Kilépés hoz létre a nagyobb naplókat.

Is megtekintheti, hogy az adatforrás rendelkezik a nyomkövetési lekérdezések eszközök. Például használhatja bővített eseményektől vagy SQL Profiler az SQL Server és az Analysis Services.

**A Q**: hol találhatók az átjáró naplói? <br/>
**A**: eszközök lásd a témakör későbbi részében.

### <a name="update-to-the-latest-version"></a>Frissítés a legújabb verzióra

Számos problémájának is surface, ha az átjáró verziója elavult. Jó általános gyakorlat győződjön meg arról, hogy a legújabb verzióját használja-e. Ha az átjáró nem frissültek, havonta vagy hosszabb ideig, akkor előfordulhat, hogy javasoljuk, hogy az átjáró legújabb verzióját telepítse, és ha Reprodukálja a hibát.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Hiba: Nem sikerült hozzáadni a felhasználót a csoporthoz. (Felhasználói-2147463168 PBIEgwService teljesítmény)

Ez a hiba kaphat, ha az átjáró telepítésekor a tartományvezérlőn, amely nem támogatott. Győződjön meg arról, hogy telepít-e az átjáró gépen, amely nem tartományvezérlő.

## <a name="tools"></a>Eszközök

### <a name="collect-logs-from-the-gateway-configurer"></a>Az átjáró configurer naplóinak gyűjtése

Az átjáró több naplók hozhatja létre. A naplók mindig kezdődnie!

#### <a name="installer-logs"></a>Telepítési naplókat

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Konfigurációs naplók

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Vállalati átjáró service naplóit

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Eseménynaplók

Az adatkezelési átjáró, a PowerBIGateway naplókat alatt található **alkalmazás- és szolgáltatásnaplók**.

### <a name="fiddler-trace"></a>Fiddler nyomkövetési

[Fiddler](http://www.telerik.com/fiddler) van egy ingyenes eszközt, amely figyeli a HTTP-forgalom Telerik. Ilyen típusú adatforgalom a Power BI szolgáltatással, az ügyfélszámítógépen való tekintheti meg. Ez a szolgáltatás előfordulhat, hogy a hibák és egyéb kapcsolódó információk megjelenítése

## <a name="next-steps"></a>Következő lépések
    
* [A helyszíni adatokhoz csatlakozva a logic Apps alkalmazásokból](../logic-apps/logic-apps-gateway-connection.md)
* [Vállalati integrációs szolgáltatások](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Az Azure Logic Apps összekötők](../connectors/apis-list.md)
