---
title: Telepítse a helyszíni adatátjáró – Azure Logic Apps |} A Microsoft Docs
description: A helyszíni adatok eléréséhez az Azure Logic Apps, töltse le és a helyszíni adatátjáró telepítése
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 67a918b227ad3b33a2f63b17f86b94f36fbc9fa3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679125"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>A helyszíni adatátjárót telepíthet az Azure Logic Apps

Mielőtt a helyszíni adatforrásokhoz csatlakozhat az Azure Logic Apps, töltse le, és a helyszíni adatátjáró telepítése a helyi számítógépen. Az átjáró hídként, amelyen keresztül biztosítja a gyors adatátvitel és a titkosítás (nem a felhőben) helyszíni adatforrások és a logikai alkalmazások között működik. Ez a cikk bemutatja, hogyan töltik le, telepítse és állítsa be a helyszíni adatátjáró. 

Használhatja ugyanazt a kapcsolódásiátjáró-telepítés más szolgáltatásokkal, például a Power bi-ban, a Microsoft Flow, a PowerApps és az Azure Analysis Services. Tudjon meg többet [az átjáró működése](#gateway-cloud-service).

<a name="supported-connections"></a>

Az átjáró támogatja a [helyszíni összekötők](../connectors/apis-list.md#on-premises-connectors) az Azure Logic Appsben ezen adatforrások számára:

*   BizTalk Server 2016
*   Fájlrendszer
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP alkalmazáskiszolgáló 
*   SAP üzenetkiszolgáló
*   SharePoint Server
*   SQL Server
*   Teradata

Az átjáró más szolgáltatásokkal való használatával kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [A Microsoft Power BI helyszíni adatátjáró](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [A helyszíni adatátjáró a Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow helyszíni adatátjáró](https://flow.microsoft.com/documentation/gateway-manage/)
* [Az Azure Analysis Services a helyszíni adatátjáró](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* A [munkahelyi vagy iskolai fiók](../active-directory/fundamentals/sign-up-organization.md) , amely rendelkezik egy [Azure-előfizetés](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) 

  Átjáró telepítése során, jelentkezzen be ezt a fiókot, hozzárendelheti a kapcsolódásiátjáró-telepítés az Azure-előfizetésében. 
  Később is használhatja ugyanazt a fiókot az Azure Portalon az átjáró telepítésekor egy Azure-erőforrás létrehozásakor. 
  Ha nem rendelkezik Azure-előfizetésem, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókkal</a>.

* Az alábbiakban a helyi számítógépre vonatkozó követelményeket:

  **Minimális követelmények**

  * .NET-keretrendszer 4.5.2-es verziója
  * 64 bites Windows 7 vagy Windows Server 2008 R2 (vagy újabb)

  **Javasolt követelményeknek**

  * 8 magos CPU
  * 8 GB memória
  * a Windows Server 2012 R2 (vagy újabb) 64 bites verziója

* **Fontos szempontok**

  * A helyszíni adatátjáró csak a helyi számítógépen, a tartományvezérlő nem telepítheti. Azonban nem kell telepítenie az átjárót az adatforrással ugyanazon a számítógépen. Emellett szüksége csak egy átjáró az adatforrásokat, így nem kell telepítenie az átjárót, az egyes adatforrások esetében.

    > [!TIP]
    > A késés minimalizálása érdekében az átjáró igyekezzen minél, az adatforráshoz, vagy ugyanazon a számítógépen, feltéve, hogy Ön jogosult is telepítheti.

  * Telepítse az átjárót olyan számítógépre, amely csatlakozik az internethez, a mindig bekapcsolva, és *nem* alvó állapot. Ellenkező esetben az átjáró nem tud futni. 
  Ezenkívül teljesítménye csökkenhet, vezeték nélküli hálózaton keresztül.

  * A telepítés során csak jelentkezhet be egy [munkahelyi vagy iskolai fiók](../active-directory/sign-up-organization.md) által felügyelt Azure Active Directory (Azure AD), például @contoso.onmicrosoft.com, és nem az Azure B2B (vendég) fiók vagy személyes Microsoft-fiók, mint például @hotmail.com vagy @outlook.com. 
  Ellenőrizze, hogy az ugyanazon bejelentkezési fiók használata is regisztrálhatja, az átjáró telepítése az Azure Portalon hoz létre egy átjáró-erőforrást. 
  Ezután kiválaszthatja az átjáró-erőforrás, a kapcsolat létrehozásakor a logikai alkalmazás a helyszíni adatforráshoz. 
  [Miért kell egy Azure ad-ben munkahelyi vagy iskolai fiókkal?](#why-azure-work-school-account)

  > [!TIP]
  > Ha feliratkozott egy Office 365 ajánlatra, és nem adott meg a tényleges munkahelyi e-mail-címét, lehetséges, hogy egy bejelentkezési cím, amely az alábbi példára hasonlít: `username@domain.onmicrosoft.com` 
  >
  > Használandó Microsoft-fiókkal, amely rendelkezik egy [Visual Studio Standard előfizetés](https://visualstudio.microsoft.com/vs/pricing/), először [hozzon létre egy könyvtárat (bérlő) Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md), vagy használja az alapértelmezett címtár, Microsoft-fiókjával. 
  > A jelszóval rendelkező felhasználó hozzáadása a címtárhoz, majd a felhasználói hozzáférést az előfizetéshez. 
  > Ezután bejelentkezhet a felhasználónévvel és jelszóval kapcsolódásiátjáró-telepítés során.

  * Az átjáró telepítésekor választott régió határozza meg a helyet, ahol később regisztrálja az átjárót az Azure-ban hozzon létre egy Azure-erőforrás. 
  Ha az átjáró erőforrás hoz létre az Azure-ban, ki kell választania a *ugyanazon* az átjáró telepítése a helyen. Az alapértelmezett régió az Azure AD-bérlővel, amely kezeli az Azure-fiókjával, ugyanazon a helyen, de a kapcsolódásiátjáró-telepítés során módosíthatják a helyet.

  * Ha már rendelkezik egy átjárót, hogy beállította a telepítő 14.16.6317.4 verziónál korábbi, a legújabb telepítőjének futtatásával az átjáró helye nem módosítható. Azonban a legújabb telepítő használatával állítsa be a helyre, hanem az új átjárót.
  
    Ha egy átjáró telepítőjének korábbi verziójú 14.16.6317.4 rendelkezik, de még nem telepítette az átjárót, letöltheti és a legújabb telepítő használja helyette.

## <a name="high-availability-support"></a>Magas rendelkezésre állás támogatása

A helyszíni adatátjáró támogatja a magas rendelkezésre állású, ha több mint egy átjáró telepített, és állítsa be őket fürtként. Ha egy meglévő átjáró amikor létrehoz egy másik átjárót, létrehozhat magas rendelkezésre állású fürtöket igény szerint. Ezek a fürtök átjárók, amely segíthet a kritikus hibapontok elkerülése érdekében csoportokba rendezheti. Emellett a minden helyszíni átjáró adatösszekötők mostantól támogatja a magas rendelkezésre állás.

A helyszíni adatátjáró használatához, tekintse át ezeket a követelményeket és szempontokat:

* Már rendelkeznie kell legalább egy átjáró telepítése az elsődleges átjárót és a helyreállítási kulcsot a telepítést, az azonos Azure-előfizetésen belül. 

* Az elsődleges átjárót az átjáró frissítése 2017 novemberi vagy újabb kell futnia.

E követelmények teljesítése után a következő átjáró létrehozásakor válassza **hozzáadása egy meglévő átjárófürthöz**, válassza ki a fürt elsődleges átjárójának, és adja meg az adott elsődleges átjáró helyreállítási kulcsával.
További információkért lásd: [magas rendelkezésre állású fürtök helyszíni adatátjáróhoz tartozó](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Adatátjáró telepítése

1. [Töltse le, mentése és az átjáró telepítőjének futtatásához a helyi számítógép](https://aka.ms/on-premises-data-gateway-installer).

2. Fogadja el az alapértelmezett elérési útvonal, vagy adja meg a helyet a számítógépre, amelyre telepíteni az átjárót.

3. Tekintse át és fogadja el a használati feltételei és adatvédelmi nyilatkozata, és válassza a **telepítése**.

   ![Fogadja el a használati feltételei és adatvédelmi nyilatkozata](./media/logic-apps-gateway-install/accept-terms.png)

4. Az átjáró sikeres telepítése után adja meg e-mail-címét a munkahelyi vagy iskolai fiókjával, és válassza a **jelentkezzen be a**.

   ![Jelentkezzen be munkahelyi vagy iskolai fiókjával](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Válasszon **ezen a számítógépen új átjáró regisztrálása** > **tovább**, ami regisztrálja az átjáró telepítése és a [gateway felhőszolgáltatás](#gateway-cloud-service). 

   ![Átjáró regisztrálása](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Adja meg ezt az információt az átjáró telepítésekor:

   * A telepítéshez választott név 

   * A helyreállítási kulcs szeretne létrehozni, legalább nyolc karakter hosszúságúnak kell lennie

     > [!IMPORTANT]
     > Mentse, és a helyreállítási kulcs tartsa biztonságos helyen. Ez a kulcs szüksége, amikor módosítja az átjáró helyét, vagy ha áttelepítéséhez, helyreállításához vagy egy meglévő átjáró átvétele.

   * A helyreállítási kulcs megerősítése 

     ![Átjáró beállítása](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Ellenőrizze a kiválasztott az átjáró felhőszolgáltatása és az Azure Service Bus az átjáró telepítése által használt régió. 

   ![Ellenőrizze a régió](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Az átjáró telepítése után módosíthatja az ebben a régióban, szükség van a helyreállítási kulcsot a kapcsolódásiátjáró-telepítés. Ezenkívül kell távolítania, és telepítse újra az átjárót. További információkért lásd: [helyének módosításához, áttelepítéséhez, helyreállításához vagy meglévő átjáró átvétele](#update-gateway-installation).

   *Miért módosult a régióban, az átjáró telepítésekor?* 

   Például a késés csökkentése érdekében érdemes lehet módosítani az átjáró régióban, és a logikai alkalmazás ugyanabban a régióban. 
   Vagy a helyszíni adatforrás legközelebb eső régiót választhat. 
   A *átjáró-erőforrást az Azure-ban* és a logikai alkalmazás különböző helyeken is rendelkezhet.

8. Fogadja el az alapértelmezett régió, válassza a **konfigurálása**. Vagy, ha módosítani szeretné az alapértelmezett régió, kövesse az alábbi lépéseket:

   1. Az aktuális terület mellett válassza ki a **régió módosítása**. 

      ![Régió módosítása](./media/logic-apps-gateway-install/change-region.png)

   2. Nyissa meg a következő oldalon a **régió kiválasztása** listájához, válassza ki a régiót, és válassza a **kész**.

      ![Válasszon egy másik régióba](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. Miután a Megerősítés lapon megjelenik, válassza ki a **Bezárás**. 

   A telepítő ellenőrzi, hogy az átjáró már nem online és használatra kész.

   ![Befejezett átjáró](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Most regisztrálja az átjárót az Azure- [az átjáró telepítésekor egy Azure-erőforrás létrehozása](../logic-apps/logic-apps-gateway-connection.md). 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Módosítsa a helyet, áttelepítése, visszaállítása vagy meglévő átjáró átvétele

Ha kell módosítani az átjáró helyét, helyezze át az átjáró telepítése egy új számítógépre, sérült átjáró helyreállítása, vagy egy meglévő átjáró saját tulajdonba, akkor a helyreállítási kulcsot a megadott kapcsolódásiátjáró-telepítés során. Ezzel a művelettel megszünteti a régi átjáró.

1. A számítógép **Vezérlőpult**, lépjen a **programok és szolgáltatások**. A programok listájában válassza ki a **a helyszíni adatátjáró**, és válassza a **Eltávolítás**.

2. [Telepítse újra a helyszíni adatátjáró](https://aka.ms/on-premises-data-gateway-installer).

3. Miután a telepítő nyílik meg, jelentkezzen be ugyanazzal a munkahelyi vagy iskolai fiókkal, amelyet korábban az átjáró telepítéséhez használt.

4. Válassza ki **áttelepítése, visszaállítása vagy egy meglévő átjáró átvétele**, és válassza a **tovább**.

   ![Válassza ki a "Áttelepítése, visszaállítása vagy egy meglévő átjáró átvétele"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. A **rendelkezésre álló átjárók** vagy **rendelkezésre álló átjárófürtök**, válassza ki a módosítani kívánt kapcsolódásiátjáró-telepítés. Adja meg a helyreállítási kulcsot az átjáró telepítéséhez. 

   ![Elsődleges átjáró kiválasztása](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. A régió módosításához válasszon **régió módosítása** és az új régióban.

7. Ha elkészült, válassza ki a **konfigurálása**.

## <a name="configure-proxy-or-firewall"></a>Proxy vagy tűzfal konfigurálása

A helyi átjáró egy kimenő kapcsolatot hoz létre [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Ha a munkahelyi környezetre van szüksége, hogy a forgalom halad keresztül csatlakozik az internetre proxyn keresztül, ez a korlátozás megakadályozhatja, hogy az átjárót az átjáró felhőszolgáltatása csatlakozzanak. Annak megállapításához, hogy a hálózat használja-e a proxy, tekintse át a superuser.com, ez a cikk: 

[Honnan tudhatom, hogy milyen Proxykiszolgálót használok? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

Adja meg a proxyadatokat az átjáró számára, lásd: [konfigurálja a proxybeállításokat](https://docs.microsoft.com/power-bi/service-gateway-proxy). Ellenőrizze, hogy a proxy vagy tűzfal előfordulhat, hogy blokkolja a kapcsolatokat, győződjön meg arról, hogy a gép ténylegesen csatlakozhatnak az internethez, és a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Egy PowerShell-parancssort, a következő parancs futtatásával:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Ez a parancs csak a hálózati kapcsolatot és a kapcsolatot az Azure Service Bus ellenőrzi. A parancs nem csinál semmi az átjáró vagy az átjáró felhőszolgáltatása, amely titkosítja, és tárolja a hitelesítő adatok és az átjáró részleteit. 
>
> Ez a parancs a, csak a Windows Server 2012 R2 vagy újabb, és a Windows 8.1 vagy újabb verziója. A korábbi operációsrendszer-verziók a Telnet használatával kapcsolat tesztelése. Tudjon meg többet [Azure Service Bus és a hibrid megoldások](../service-bus-messaging/service-bus-messaging-overview.md).

Az eredmények az alábbihoz hasonlóan kell kinéznie **TcpTestSucceeded** beállítása **igaz**:

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

Ha **TcpTestSucceeded** értéke **igaz**, előfordulhat, hogy az átjáró blokkolja tűzfal. Ha szeretne átfogó, cserélje le a **ComputerName** és **Port** alatt felsorolt értékekkel értékek [portok konfigurálása](#configure-ports) ebben a cikkben.

A tűzfal is előfordulhat, hogy blokkolja a kapcsolatokat, az Azure Service Bus hajt végre az Azure-adatközpontokból. Ez a forgatókönyv akkor fordul elő, ha jóváhagyása (feloldania) azokat az Ön régiójában adatközpontok összes IP-címet. Az adott IP-címek [lekérése az Azure IP-címlistát Itt](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Portok konfigurálása

Az átjáró egy kimenő kapcsolatot hoz létre [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) és a kimenő portokon kommunikál: A TCP 443 (alapméretezett), 5671, 5672, 9350 – 9354. Az átjáró használatához nincs szükség bejövő portokra. Tudjon meg többet [Azure Service Bus és a hibrid megoldások](../service-bus-messaging/service-bus-messaging-overview.md).

Az átjáró a teljesen minősített tartománynevet használja:

| Tartománynevek | Kimenő portok | Leírás | 
| ------------ | -------------- | ----------- | 
| *.analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | Konfigurációjától függően a hitelesítéshez használt. | 
| *.msftncsi.com | 443 | Az internetkapcsolat tesztelésére, amikor az átjáró nem érhető el a Power BI szolgáltatás által használt. | 
| *.servicebus.windows.net | 443, 9350-9354 | A Service Bus Relay (a 443-as igényel a hozzáférés-vezérlési token beszerzéséhez) TCP-n keresztül figyelők | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queueing Protocol (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

Bizonyos esetekben az Azure Service Bus-kapcsolatok teljes tartománynevek helyett IP-címek menjenek végbe. Ezért érdemes IP-címeket az adatrégió a a tűzfalon. Engedélyezett IP-címek helyett tartományok, letöltheti és használhatja a [Microsoft Azure adatközpont IP-címtartományok listáját](https://www.microsoft.com/download/details.aspx?id=41653). Az IP-címeket a listában szerepelnek [Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel.

### <a name="force-https-communication-with-azure-service-bus"></a>Az Azure Service busszal HTTPS-kommunikáció kényszerítése

Vannak proxyk, amelyek lehetővé teszik, csak a 80-as és 443-as forgalom. Alapértelmezés szerint az Azure Service Bus a kommunikációt a 443-astól különböző portokon történik.
Kényszerítheti az átjárót keresztül közvetlen TCP helyett HTTPS az Azure Service Bus kommunikálni, de ez így is nagymértékben csökkenti a teljesítményt. Ez a feladat végrehajtásához kövesse az alábbi lépéseket:

1. Keresse meg a helyet, a helyszíni adatok átjáró ügyfél, amely általában itt talál: ```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   Ellenkező esetben az ügyfél helyének megkereséséhez nyissa meg az ugyanazon a számítógépen a szolgáltatások konzolt, keresse meg **a helyszíni adatátjáró szolgáltatás**, és megtekintheti a **végrehajtható fájl elérési útja** tulajdonság.

2. Nyissa meg a *konfigurációs* fájlt: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. Módosítsa a **ServiceBusSystemConnectivityModeString** értéket **automatikus észlelés** való **Https**:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-szolgáltatásfiók

Azon a számítógépen, amelyre telepíti a helyszíni adatátjáróval az átjáró fut, mint egy "Helyszíni adatátjáró szolgáltatás" nevű Windows-szolgáltatásfiókot. Az átjáró azonban az "NT SERVICE\PBIEgwService" neve a "Bejelentkezés mint" fiók hitelesítő adatait használja. Alapértelmezés szerint az átjáró "Bejelentkezés szolgáltatásként" engedéllyel rendelkezik a számítógépen, amelyre telepíti az átjárót. A Windows-szolgáltatásfiókot az átjárón általában különbözik a fiók, amellyel a helyszíni adatforrásokhoz, illetve a munkahelyi vagy iskolai fiókkal a felhőalapú szolgáltatásokhoz való bejelentkezéshez használja.

Hozhat létre és karbantartása az átjáró az Azure Portalon, a Windows-szolgáltatásfiókot kell rendelkeznie legalább **közreműködői** engedélyeket. Ezek az engedélyek megtekintéséhez [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md). 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Átjáró újraindítása

Az átjáró fut egy ablak, így bármely más Windows szolgáltatás, például, elindíthatja és leállíthatja a különféle módon az átjárót. Például nyisson meg egy parancssort emelt szintű engedélyekkel a számítógépen, amelyen az átjáró fut, és mindkét parancs futtatásával:

* Állítsa le a szolgáltatást, a következő parancs futtatásával:
  
  `net stop PBIEgwService`

* Indítsa el a szolgáltatást, a következő parancs futtatásával:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Bérlőszintű felügyelet 

Jelenleg nincs olyan hely, ahol a bérlői rendszergazdák kezelhetik az átjárók, amelyek más felhasználók telepítve és konfigurálva. Ha Ön Bérlői rendszergazda, érdemes minden átjáró telepítési rendszergazdaként vegye fel Önt a szervezetben a felhasználók rendelkeznek. Ezzel a módszerrel a kezelheti minden átjáró a szervezetben az átjáró beállításai panelen vagy keresztül [PowerShell-parancsok](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Hogyan működik az átjáró?

Az átjáró a logikai alkalmazás, az átjáró felhőszolgáltatása, és a helyszíni adatforrás közötti gyors és biztonságos kommunikációt létesít. Az átjáró felhőszolgáltatása titkosítja, és tárolja az adatforrás hitelesítő adatainak és az átjáró részleteit. A szolgáltatási útvonalak a helyszínen lekérdezések és a logikai alkalmazást, a helyszíni adatátjáró és az adatforrás közötti eredményeket is. 

Az átjáró működik, tűzfalakkal, és csak a kimenő kapcsolatokat használ. Az összes forgalom származik, az átjáró ügynök biztonságos kimenő forgalmát. Az átjáró a helyszíni forrásokból, titkosított csatornákon keresztül az Azure Service Bus továbbítókat. Ez a service bus az átjáró és a hívó közötti csatornát hoz létre, de nem tárol adatokat. Az átjárón keresztül haladó összes adat titkosítva van.

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Ezeket a lépéseket írja le, mi történik, ha egy felhasználó a felhőben egy helyszíni adatforráshoz csatlakozó elemet használja:

1. Az átjáró felhőszolgáltatása létrehoz egy lekérdezést az adatforrás titkosított hitelesítő adataival, és elküldi a lekérdezést az átjáróhoz tartozó üzenetsorba.

2. Az átjáró felhőszolgáltatása elemzi a lekérdezést, majd leküldi a kérelmet az Azure Service bushoz.

3. A helyszíni adatátjáró lekérdezi az Azure Service Bus, a függőben lévő kérelmek.

4. Az átjáró megkapja a lekérdezést, visszafejti a hitelesítő adatokat, és csatlakozik az adatforráshoz, ezekkel az adatokkal.

5. Az átjáró elküldi a lekérdezést az adatforráshoz a végrehajtáshoz.

6. Az eredmények az adatforrásból kerülnek vissza a az átjárót, majd az átjáró felhőszolgáltatása. Az átjáró felhőszolgáltatása majd ezekkel az eredményekkel dolgozik.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="general"></a>Általános kérdések

**Q**: Kell egy átjárót az adatforrásokat a felhőben, például az Azure SQL Database? <br/>
**A**: Az átjáró nem, csak a helyszíni adatforrásokhoz kapcsolódik.

**Q**: Az átjáró telepíthető ugyanarra a gépre, az adatforrással rendelkezik? <br/>
**A**: Nem, az átjáró csatlakozik az adatforráshoz megadott kapcsolati információk használatával. Fontolja meg az átjáró egyfajta ügyfélalkalmazásnak abban az értelemben. Az átjáró csak teszi, hogy a kiszolgáló nevét, a megadott csatlakozni kell.

<a name="why-azure-work-school-account"></a>

**Q**: Miért kell használnia, jelentkezzen be munkahelyi vagy iskolai fiókkal? <br/>
**A**: Csak használhatja a munkahelyi vagy iskolai fiókkal, a helyszíni adatátjáró telepítése során. A bejelentkezési fiók tárolva van egy Azure Active Directory (Azure AD) által felügyelt bérlői. Általában az Azure AD-fiókot egyszerű felhasználónév (UPN) megegyezik az e-mail-cím.

**Q**: Hol tárolódnak a hitelesítő adataim? <br/>
**A**: A megadott hitelesítő adatainak az adatforrások vannak titkosítva, és az átjáró felhőszolgáltatása tárolja. A hitelesítő adatokat, a helyszíni adatátjáró lesznek visszafejtve.

**Q**: Vannak a hálózati sávszélességgel kapcsolatos követelmények? <br/>
**A**: Ellenőrizze, hogy a hálózati kapcsolat van-e a jó teljesítmény. Minden környezet más, és küldött adatok mennyisége hatással lehetnek. Garantálja a helyszíni adatforrás és az Azure-adatközpontok közötti átviteli sebesség szintet, próbálja meg [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Annak érdekében, hogy az átviteli sebességet a mérőműszer, próbálja meg egy külső eszköz, például az Azure Speed Test.

**Q**: Mi az a lekérdezések futtatásának késése az adatforrás és az átjáró? Mi az a legjobb architektúra? <br/>
**A**: Hálózati késés csökkentéséhez telepítse a közel az adatforráshoz az átjárót, amennyire csak lehetséges. Ha az átjárót a tényleges adatforrásra is telepíthető, a közelségi minimalizálja a késést. Is fontolja meg az Azure-adatközpontok közelében. Például ha a szolgáltatás az USA nyugati adatközpontjában használ, és egy Azure virtuális Gépen üzemeltetett SQL Serverrel rendelkezik, majd érdemes az Azure virtuális gép az USA nyugati régiójában túl. A közelségi minimalizálja a hálózati késést, és ezzel elkerülheti a kimenő forgalom költségeit, az Azure virtuális gépen.

**Q**: Hogyan lesznek visszaküldve az eredmények a felhőbe? <br/>
**A**: Az eredmények küldése az Azure Service Bus keresztül történik.

**Q**: Vannak bemenő kapcsolatok az átjáróba a felhőben? <br/>
**A**: Nem, az átjáró kimenő kapcsolatokat használ az Azure Service Bus.

**Q**: Mi történik, ha letiltom a kimenő kapcsolatokat? Hogyan készüljek megnyitásához? <br/>
**A**: Tekintse meg a portot és az átjáró által használt gazdagépek.

**Q**: Mi a tényleges Windows-szolgáltatás neve? <br/>
**A**: A szolgáltatások lapon a Feladatkezelőben a szolgáltatás neve "PBIEgwService" vagy a Power BI Enterprise Gateway szolgáltatás. A szolgáltatások konzolban a szolgáltatás nevét a "helyszíni adatátjáró szolgáltatás". A Windows-szolgáltatás használ "NT SERVICE\PBIEgwService", a szolgáltatás biztonsági azonosító (SSID).

**Q**: Futtatható az átjáró Windows-szolgáltatás Azure Active Directory-fiókkal? <br/>
**A**: A Windows-szolgáltatás nem egy érvényes Windows-fiókkal kell rendelkeznie.

### <a name="disaster-recovery"></a>Vészhelyreállítás

**Q**: Milyen lehetőségek állnak rendelkezésre a vész-helyreállítási? <br/>
**A**: A helyreállítási kulcs használatával egy átjáró visszaállítását vagy áthelyezését. Az átjáró telepítésekor adja meg a helyreállítási kulcsot.

**Q**: Mi az az előnye, hogy a helyreállítási kulcs? <br/>
**A**: A helyreállítási kulcs áttelepítéséhez vagy helyreállíthatja átjárója beállításait egy vészhelyzetet követően lehetővé teszi.

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz azon néhány gyakori probléma, lehetséges, hogy közben beállításával és a helyszíni adatátjáró használatával.

**Q**: Miért volt sikertelen a kapcsolódásiátjáró-telepítés? <br/>
**A**: A probléma akkor fordulhat elő, ha a víruskereső szoftver a cél számítógépen elavult. Frissítse a víruskereső szoftvert, vagy az tiltsa le a víruskereső szoftverek azonban csak az átjáró telepítése során, és majd engedélyezze újra a szoftvert.

**Q**: Miért nem látom a kapcsolódásiátjáró-telepítés az átjáró-erőforrás létrehozásakor az Azure-ban? <br/>
**A**: A probléma az alábbi okok miatt fordulhat elő:

* Az átjáró telepítése már regisztrálva és engedte, hogy egy másik átjáró erőforrás az Azure-ban. Átjáró telepítése nem jelennek meg a példányok listájában, miután az átjáró-erőforrások jönnek létre számukra.
Ellenőrizze az átjáró regisztrációkat az Azure Portalon, tekintse át az összes Azure-erőforrásai a **a helyszíni Adatátjárók** írja be a *összes* Azure-előfizetést. 

* A személy, aki az átjáró telepítve van az Azure AD-identitásnak eltér a személy, aki bejelentkezett az Azure Portalon. Ellenőrizze, hogy van-e bejelentkezve ugyanazzal az identitással, amelyet telepíteni az átjárót.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: Hol találhatók az átjárónaplók? <br/>
**A**: Tekintse meg a [ **naplók** szakasz](#logs) a cikk későbbi részében.

**Q**: Hogyan tekinthetem meg mit lekérdezés folyamatban van a helyszíni adatforrás küldött? <br/>
**A**: Engedélyezheti a lekérdezések nyomon követését, amely tartalmazza a lekérdezéseket küldött el. Ne felejtse el módosítani a lekérdezés nyomkövetés vissza az eredeti értékre, a hibaelhárítás végrehajtása után. Hagyja a lekérdezések nyomon követését bekapcsolva hoz létre a nagyobb naplókat.

Tekintse meg az adatforrást a lekérdezések nyomon követésére rendelkező eszközöket is. Például használhatja Extended Eventset vagy az SQL Profiler az SQL Server és az Analysis Services.

### <a name="outdated-gateway-version"></a>Elavult átjáró verziója

Számos probléma akkor merülhet fel, ha az átjáró verziója elavult válik. Általános gyakorlat győződjön meg arról, hogy a legújabb verzióval rendelkezik. Ha az átjáró egy hónapig vagy hosszabb ideig nem frissítette, előfordulhat, hogy fontolja meg az átjáró legújabb verziójának telepítését, és tekintse meg, ha Reprodukálja a problémát.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Hiba: Nem sikerült hozzáadni a felhasználói csoporthoz. (-2147463168 PBIEgwService Teljesítménynapló felhasználói)

Előfordulhat, hogy megjelenik a hibaüzenet, ha az átjáró telepítésekor a tartományvezérlőn, amely nem támogatott. Ellenőrizze, hogy az átjárót olyan gépre, amely nem tartományvezérlő központi telepítése.

<a name="logs"></a>

### <a name="logs"></a>Naplók

Könnyebben háríthatja el a, mindig először összegyűjtése és az átjáró naplók áttekintése. A naplók gyűjtésére szolgáló több módszert is rendelkezik, de a legegyszerűbb lehetőség az átjáró telepítése után az átjáró telepítőjének felhasználói felületen keresztül. 

1. Nyissa meg a számítógépen, a helyszíni adatátjáró telepítője.
2. A bal oldali menüben válassza ki a **diagnosztikai**.
3. A **átjárónaplók**válassza **naplók exportálása**.

   ![Az átjáró telepítőjének naplók exportálása](./media/logic-apps-gateway-install/export-logs.png)

Más helyre, ahol megtalálhatja a különféle naplókból a következők:

| Napló típusa | Hely | 
|----------|----------| 
| **Telepítőnaplók** | %localappdata%\Temp\On-premises_data_gateway_ <*ÉÉÉÉHHNN*>. <*szám*> .log | 
| **Konfigurációs naplók** | C:\Users\<*felhasználónév*> \AppData\Local\Microsoft\On-premises adatok gateway\GatewayConfigurator <*ÉÉÉÉHHNN*>. <*szám*>. napló | 
| **Vállalati átjárószolgáltatás naplói** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises adatok gateway\Gateway <*ÉÉÉÉHHNN*>. <*szám*> .log | 
||| 

**Eseménynaplók**

Az átjáró eseménynaplóit megkereséséhez kövesse az alábbi lépéseket:

1. A számítógépen, amelyen a kapcsolódásiátjáró-telepítés, nyissa meg a **Eseménynapló**. 
2. Bontsa ki a **Eseménynapló (helyi)** > **alkalmazások és szolgáltatásnaplók**. 
3. Válassza ki **a helyszíni adatátjáró szolgáltatás**.

   ![Átjáró Eseménynapló megtekintése](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Lassú lekérdezések teljesítményének áttekintése

Ha azt tapasztalja, hogy a lekérdezések lassan futnak az átjárón keresztül, bekapcsolhatja a további naplózás, amely lekérdezéseket és azok időtartamát. Ezek a naplók segíthetnek úgy találja, hogy melyik lekérdezések lassú vagy hosszú ideig futó. A lekérdezési teljesítmény hangolása, előfordulhat, hogy kell például módosítani az adatforrást, módosítsa az indexek az SQL Server-lekérdezéseket.

Egy lekérdezés időtartamának meghatározásához, kövesse az alábbi lépéseket:

1. Keresse meg az átjáró-ügyfelet, amely általában találja itt ugyanazon a helyen: ```C:\Program Files\On-premises data gateway```

   Ellenkező esetben az ügyfél helyének megkereséséhez nyissa meg az ugyanazon a számítógépen a szolgáltatások konzolt, keresse meg **a helyszíni adatátjáró szolgáltatás**, és megtekintheti a **végrehajtható fájl elérési útja** tulajdonság.

2. Nyissa meg, és módosítsa ezeket a konfigurációs fájlokat:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Ezt a fájlt, módosítsa a **EmitQueryTraces** értéket **false (hamis)** való **igaz** , az átjáró bejelentkezhet egy adatforrást az átjáróból küldött lekérdezések:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > Az emitquerytraces értékét módosítsa a beállítás bekapcsolásával jelentősen megnövelheti a napló méretét, az átjáró használatától függően. A naplók áttekintése után ellenőrizze, hogy alaphelyzetbe állítja az EmitQueryTraces **hamis** ismét helyett ezzel a beállítással meghagyása a hosszú távú.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     A részletes bejegyzéseket naplóz, beleértve a, időtartamot megjelenítő bejegyzéseket átjárója módosítsa a **TracingVerbosity** értéket **4** való **5** mindkét lépés végrehajtásával: 

     * A konfigurációs fájlban módosítsa a **TracingVerbosity** értéket **4** való **5** 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Válassza ki az átjáró telepítőjének megnyitása **diagnosztikai**, kapcsolja be a **további naplózás**, és válassza a **alkalmaz**:

       ![További naplózás bekapcsolása](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > A TracingVerbosity beállítás bekapcsolásával jelentősen megnövelheti a napló méretét, az átjáró használatától függően. A naplók áttekintése után ellenőrizze, hogy kikapcsolja **további naplózás** az átjáró telepítő vagy alaphelyzetbe állítása a TracingVerbosity **4** újra a konfigurációs fájlban helyett ezzel a beállítással meghagyása a hosszú távú.

3. Az időtartamot a lekérdezés megkereséséhez kövesse az alábbi lépéseket:

   1. [Exportálás](#logs) , és nyissa meg az átjáró naplóját.

   2. Lekérdezés megkereséséhez keressen egy tevékenység típusa, például: 

      | Tevékenység típusa | Leírás | 
      |---------------|-------------| 
      | MGEQ KERESÉSÉT | ADO.NET-kapcsolaton futó lekérdezések. | 
      | MGEO | OLEDB-kapcsolaton futó lekérdezések. | 
      | MGEM | Az adategyesítési motor-ről futó lekérdezéseket. | 
      ||| 

   3. Megjegyzés: a második GUID azonosítója, amely a kérés azonosítóját.

   4. Továbbra is a tevékenység típusát keresése, amíg meg nem látja ezt a nevet a "FireActivityCompletedSuccessfullyEvent", amely időtartama (MS). 
   Győződjön meg arról, a bejegyzés rendelkezik azonos kérés azonosítója, például:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > A "FireActivityCompletedSuccessfullyEvent" bejegyzés egy részletes bejegyzés és a program nem naplózza, ha a "TracingVerbosity" beállítás értéke legalább 5.

### <a name="trace-traffic-with-fiddler"></a>Fiddler-nyomkövetés forgalom

[A fiddler](https://www.telerik.com/fiddler) a HTTP-adatforgalom figyelésére telerik ingyenes eszköze. Ez a forgalom áramlását az ügyfélszámítógépen a Power BI szolgáltatással tekintheti meg. Ez a szolgáltatás megjelenítése előfordulhat, hogy a hibák és egyéb kapcsolódó információkat.

## <a name="next-steps"></a>További lépések
    
* [Csatlakozás helyszíni adatokhoz logikai alkalmazásokból](../logic-apps/logic-apps-gateway-connection.md)
* [Vállalati integrációs szolgáltatások](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Az Azure Logic Apps összekötői](../connectors/apis-list.md)
