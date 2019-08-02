---
title: Helyszíni adatátjáró telepítése – Azure Logic Apps | Microsoft Docs
description: Mielőtt a helyszíni adatokhoz hozzáférhessen Azure Logic Appsről, töltse le és telepítse a helyszíni adatátjárót
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 61a9b319b9ea44f766bc6f014b76bc48d15efc57
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598452"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Helyszíni adatátjáró telepítése Azure Logic Apps

A helyszíni adatforrásokhoz való kapcsolódáshoz Azure Logic Apps a helyi számítógépen töltse le és telepítse a helyszíni adatátjárót. Az átjáró olyan hídként működik, amely gyors adatátvitelt és titkosítást biztosít a helyszínen (nem a felhőben) lévő adatforrások és a logikai alkalmazások között. Ez a cikk bemutatja, hogyan töltheti le, telepítheti és állíthatja be a helyszíni adatátjárót. 

Ugyanezt az átjáró-telepítést más szolgáltatásokkal is használhatja, például a Power BI, a Microsoft Flow, a PowerApps és a Azure Analysis Services. További információ [az adatátjáró működéséről](#gateway-cloud-service).

<a name="supported-connections"></a>

Az átjáró támogatja [](../connectors/apis-list.md#on-premises-connectors) a helyszíni összekötőket Azure Logic Appsban ezekhez az adatforrásokhoz:

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

Az átjáró más szolgáltatásokkal való használatáról a következő cikkekben talál további információt:

* [Microsoft Power BI helyszíni adatátjáró](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Helyszíni adatátjáró Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Helyszíni adatátjáró Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Helyszíni adatátjáró Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) -előfizetéssel rendelkező [munkahelyi vagy iskolai fiók](../active-directory/fundamentals/sign-up-organization.md) 

  Az átjáró telepítése során be kell jelentkeznie ebbe a fiókba, így társíthatja az átjáró telepítését az Azure-előfizetéséhez. 
  Később ugyanezt a fiókot is használhatja, ha Azure-erőforrást hoz létre az átjáró telepítéséhez a Azure Portal. 
  Ha még nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure</a>-fiókra.

* A helyi számítógépekre vonatkozó követelmények:

  **Minimális követelmények**

  * .NET-keretrendszer 4.5.2
  * a Windows 7 vagy a Windows Server 2008 R2 64 bites verziója (vagy újabb)

  **Ajánlott követelmények**

  * 8 magos processzor
  * 8 GB memória
  * a Windows Server 2012 R2 64 bites verziója (vagy újabb)

* **Fontos szempontok**

  * A helyszíni adatátjárót csak helyi számítógépen, nem tartományvezérlőn is telepítheti. Azonban nem kell telepítenie az átjárót ugyanarra a számítógépre, mint az adatforrást. Emellett csak egy átjáróra van szükség az összes adatforráshoz, így nem kell minden egyes adatforráshoz telepítenie az átjárót.

    > [!TIP]
    > A késés csökkentése érdekében az átjárót a lehető legközelebb az adatforráshoz vagy ugyanazon a számítógépen telepítheti, feltéve, hogy rendelkezik a megfelelő engedélyekkel.

  * Telepítse az átjárót olyan számítógépre, amely csatlakozik az internethez, mindig be van kapcsolva, és *nem* alvó állapotba lép. Ellenkező esetben az átjáró nem futtatható. 
  A teljesítmény a vezeték nélküli hálózaton is csökkenhet.

  * A telepítés során csak Azure Active Directory (Azure ad) által felügyelt [munkahelyi vagy iskolai fiókkal](../active-directory/sign-up-organization.md) jelentkezhet be, például @contoso.onmicrosoft.comnem Azure B2B-(vendég-) fiókra vagy személyes @hotmail.com Microsoft-fiókra, például vagy @outlook.com. 
  Győződjön meg arról, hogy ugyanazt a bejelentkezési fiókot használja, ha az átjáró telepítését a Azure Portal regisztrálja egy átjáró-erőforrás létrehozásával. 
  Ezt követően kiválaszthatja ezt az átjáró-erőforrást, amikor létrehozza a kapcsolódást a logikai alkalmazásból a helyszíni adatforráshoz. 
  [Miért kell használni az Azure AD munkahelyi vagy iskolai fiókját?](#why-azure-work-school-account)

  > [!TIP]
  > Ha regisztrált az Office 365-ajánlatra, és nem adta meg a tényleges munkahelyi e-mail-címét, lehet, hogy van egy bejelentkezési címe, amely a következő példához hasonlít:`username@domain.onmicrosoft.com` 
  >
  > Ha [Visual Studio standard](https://visualstudio.microsoft.com/vs/pricing/)előfizetéssel rendelkező Microsoft-fiókt szeretne használni, először [hozzon létre egy könyvtárat (bérlőt) a Azure Active Directoryban](../active-directory/develop/quickstart-create-new-tenant.md), vagy használja az alapértelmezett könyvtárat az Microsoft-fiók. 
  > Adjon hozzá egy jelszót tartalmazó felhasználót a címtárhoz, majd adja meg, hogy a felhasználó hozzáférjen az előfizetéséhez. 
  > Ezt a felhasználónevet és jelszót használva az átjáró telepítése közben is bejelentkezhet.

  * Az átjáró telepítéséhez kiválasztott régió határozza meg azt a helyet, ahol később Azure-erőforrás létrehozásával regisztrálja az átjárót az Azure-ban. 
  Amikor létrehozza ezt az átjáró-erőforrást az Azure-ban, *ugyanazt* a helyet kell kijelölnie, mint az átjáró telepítését. Az alapértelmezett régió az Azure AD-Bérlővel megegyező hely, amely felügyeli az Azure-fiókját, de az átjáró telepítése közben is módosíthatja a helyet.

  * Ha már rendelkezik egy, a 14.16.6317.4 verziónál korábbi telepítővel beállított átjáróval, az átjáró helye nem módosítható a legújabb telepítő futtatásával. A legújabb telepítővel azonban új átjárót állíthat be a kívánt helyre.
  
    Ha olyan 14.16.6317.4 rendelkezik, amely korábbi a verziónál, de még nem telepítette az átjárót, akkor a legújabb telepítőt is letöltheti és használhatja.

## <a name="high-availability-support"></a>Magas rendelkezésre állás támogatása

A helyszíni adatátjáró támogatja a magas rendelkezésre állást, ha egynél több átjárót telepít, és fürtként állítja be őket. Ha meglévő átjáróval rendelkezik, amikor egy másik átjárót hoz létre, igény szerint magas rendelkezésre állású fürtöket is létrehozhat. Ezek a fürtök olyan csoportokba szerveznek átjárókat, amelyek segítségével elkerülhetők az egyes meghibásodási pontok. Emellett az összes helyszíni adatátjáró-összekötő már támogatja a magas rendelkezésre állást.

A helyszíni adatátjáró használatához tekintse át az alábbi követelményeket és szempontokat:

* A telepítéshez már legalább egy Azure-előfizetéshez tartozó átjárót kell telepítenie, mint az elsődleges átjárót és a helyreállítási kulcsot. 

* Az elsődleges átjárónak az átjáró frissítését november 2017 vagy újabb verzióra kell futtatnia.

A követelmények teljesítése után a következő átjáró létrehozásakor válassza a **Hozzáadás meglévő átjáró**-fürthöz lehetőséget, válassza ki a fürt elsődleges átjáróját, és adja meg az elsődleges átjáró helyreállítási kulcsát.
További információ: [magas rendelkezésre állású fürtök](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters)helyszíni adatátjáróhoz.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Adatátjáró telepítése

1. [Töltse le, mentse és futtassa az átjáró telepítőjét egy helyi számítógépen](https://aka.ms/on-premises-data-gateway-installer).

2. Fogadja el az alapértelmezett telepítési útvonalat, vagy adja meg azt a helyet a számítógépen, amelyre telepíteni szeretné az átjárót.

3. Tekintse át és fogadja el a használati feltételeket és az adatvédelmi nyilatkozatot, majd válassza a **telepítés**lehetőséget.

   ![Használati feltételek és adatvédelmi nyilatkozat elfogadása](./media/logic-apps-gateway-install/accept-terms.png)

4. Az átjáró sikeres telepítése után adja meg a munkahelyi vagy iskolai fiókhoz tartozó e-mail-címet, majd válassza a **Bejelentkezés**lehetőséget.

   ![Bejelentkezés munkahelyi vagy iskolai fiókkal](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Válassza az **új átjáró regisztrálása ezen a számítógépen** > **következő**lehetőséget, amely regisztrálja az átjáró telepítését az [átjáró Cloud Service](#gateway-cloud-service)-ben. 

   ![Átjáró regisztrálása](./media/logic-apps-gateway-install/register-new-gateway.png)

6. Adja meg az átjáró telepítéséhez szükséges információkat:

   * A telepítéshez használni kívánt név 

   * A létrehozni kívánt helyreállítási kulcs, amelynek legalább nyolc karakterből kell állnia

     > [!IMPORTANT]
     > Mentse és őrizze meg a helyreállítási kulcsot biztonságos helyen. Erre a kulcsra akkor van szükség, amikor megváltoztatja az átjáró helyét, vagy egy meglévő átjáró áttelepítése, helyreállítása vagy átvétele során.

   * A helyreállítási kulcs megerősítése 

     ![Átjáró beállítása](./media/logic-apps-gateway-install/set-up-gateway.png)

7. Keresse meg az átjáró Cloud Service-hez kiválasztott régiót, és Azure Service Bus, amelyet az átjáró telepítése használ. 

   ![Régió ellenőrzési területe](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > Ha az átjáró telepítésének befejezése után módosítani szeretné ezt a régiót, szüksége lesz az átjáró telepítéséhez szükséges helyreállítási kulcsra. Emellett el kell távolítania és újra kell telepítenie az átjárót. További információkért lásd: a [meglévő átjáró helyének módosítása, áttelepítése, helyreállítása vagy](#update-gateway-installation)átvétele.

   *Miért érdemes módosítani a régiót az átjáró telepítésekor?* 

   Például a késés csökkentése érdekében előfordulhat, hogy az átjáró régióját a logikai alkalmazással megegyező régióra változtatja. 
   Vagy a helyszíni adatforráshoz legközelebb eső régiót is kiválaszthatja. 
   Az *Azure* -beli átjáró-erőforrás és a logikai alkalmazás különböző helyekkel rendelkezhet.

8. Az alapértelmezett régió elfogadásához válassza a **Konfigurálás**lehetőséget. Az alapértelmezett régió módosításához kövesse az alábbi lépéseket:

   1. Az aktuális régió mellett válassza a **régió módosítása**lehetőséget. 

      ![Régió módosítása](./media/logic-apps-gateway-install/change-region.png)

   2. A következő lapon nyissa meg a **régió kiválasztása** listát, válassza ki a kívánt régiót, majd válassza a **kész**lehetőséget.

      ![Válasszon másik régiót](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. A megerősítést kérő oldal megjelenése után válassza a **Bezárás**lehetőséget. 

   A telepítő ellenőrzi, hogy az átjáró online állapotban van-e, és használatra készen áll-e.

   ![Befejezett átjáró](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. Most regisztrálja az átjárót az Azure-ban [egy Azure-erőforrás létrehozásával az átjáró telepítéséhez](../logic-apps/logic-apps-gateway-connection.md). 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Megváltoztathatja a meglévő átjáró helyét, áttelepítését, visszaállítását vagy átvételét

Ha módosítania kell az átjáró helyét, helyezze át az átjáró telepítését egy új számítógépre, állítson helyre egy sérült átjárót, vagy vegyen fel egy meglévő átjáró tulajdonjogát, szüksége lesz az átjáró telepítésekor megadott helyreállítási kulcsra. Ez a művelet leválasztja a régi átjárót.

1. A számítógép **Vezérlőpultján**lépjen a **programok és szolgáltatások**elemre. A programok listában válassza a helyszíni **adatátjáró**lehetőséget, majd válassza az **Eltávolítás**lehetőséget.

2. [Telepítse újra a](https://aka.ms/on-premises-data-gateway-installer)helyszíni adatátjárót.

3. A telepítő megnyitása után jelentkezzen be ugyanazzal a munkahelyi vagy iskolai fiókkal, amelyet korábban az átjáró telepítéséhez használt.

4. Válassza **a meglévő átjáró áttelepíteni, visszaállítása vagy**átvétele lehetőséget, majd válassza a **tovább**lehetőséget.

   ![Válassza a "meglévő átjáró migrálása, visszaállítása vagy átvétele" lehetőséget.](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. A **rendelkezésre álló átjárók** vagy az **elérhető átjáró fürtök**területen válassza ki a módosítani kívánt átjáró telepítését. Adja meg az átjáró telepítésének helyreállítási kulcsát. 

   ![Elsődleges átjáró kiválasztása](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. A régió módosításához válassza a régió és az új régió **módosítása** lehetőséget.

7. Ha elkészült, válassza a **Konfigurálás**lehetőséget.

## <a name="configure-proxy-or-firewall"></a>Proxy vagy tűzfal konfigurálása

A helyszíni adatátjáró kimenő kapcsolatokat hoz létre Azure Service Bushoz. [](https://azure.microsoft.com/services/service-bus/) Ha a munkahelyi környezet megköveteli, hogy a forgalom egy proxyn keresztül hozzáférjen az internethez, akkor ez a korlátozás megakadályozhatja, hogy az adatátjáró csatlakozzon az átjáró Cloud Service-hez. Annak megállapításához, hogy a hálózat proxyt használ-e, tekintse át ezt a cikket a következő címen: superuser.com: 

[Hogyan tudni, hogy milyen proxykiszolgálót használok? (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

Az átjáróhoz tartozó proxy-információk megadásához tekintse meg a [Proxybeállítások konfigurálása](https://docs.microsoft.com/power-bi/service-gateway-proxy)című témakört. Annak ellenőrzéséhez, hogy a proxy vagy a tűzfal blokkolja-e a kapcsolatokat, ellenőrizze, hogy a gép tud-e csatlakozni az internethez és a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Egy PowerShell-parancssorból futtassa a következő parancsot:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Ez a parancs csak a hálózati kapcsolatot és a Azure Service Bushoz való kapcsolódást ellenőrzi. A parancs nem csinál semmit az átjáróval vagy az átjáró felhőalapú szolgáltatásával, amely titkosítja és tárolja a hitelesítő adatait és az átjáró adatait. 
>
> Ez a parancs csak Windows Server 2012 R2 vagy újabb, illetve Windows 8,1 vagy újabb rendszereken érhető el. A korábbi operációsrendszer-verziókon a Telnet használatával tesztelheti a kapcsolatot. További információ a [Azure Service Bus és a hibrid megoldásokról](../service-bus-messaging/service-bus-messaging-overview.md).

Az eredményeknek ehhez a példához hasonlóan kell kinéznie a **TcpTestSucceeded** **true**értékre:

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

Ha a **TcpTestSucceeded** értéke nem **true (igaz**), akkor előfordulhat, hogy az átjárót tűzfal blokkolja. Ha szeretné, hogy az átfogó legyen, cserélje le a **számítógépnév** és a **portok** értékét a jelen cikk [portok konfigurálása](#configure-ports) című részében felsorolt értékekre.

Előfordulhat, hogy a tűzfal blokkolja a Azure Service Bus által az Azure-adatközpontok számára elérhető kapcsolatokat is. Ha ez a helyzet történik, hagyja jóvá (oldja fel a blokkolást) a régiójában lévő adatközpontok összes IP-címét. Ezekhez az IP-címekhez [szerezze be az Azure IP-címek listáját](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Portok konfigurálása

Az átjáró egy kimenő kapcsolatot hoz létre [](https://azure.microsoft.com/services/service-bus/) a Azure Service Bushoz, és kommunikál a kimenő portokon: TCP 443 (alapértelmezett), 5671, 5672, 9350 az 9354-n keresztül. Az átjáró nem igényel bejövő portokat. További információ a [Azure Service Bus és a hibrid megoldásokról](../service-bus-messaging/service-bus-messaging-overview.md).

Az átjáró ezeket a teljes tartományneveket használja:

| Tartománynevek | Kimenő portok | Leírás | 
| ------------ | -------------- | ----------- | 
| *.analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | Hitelesítéshez használatos a konfigurációtól függően. | 
| *.msftncsi.com | 443 | Az internetkapcsolat tesztelésére szolgál, ha az átjáró nem érhető el a Power BI szolgáltatás. | 
| *.servicebus.windows.net | 443, 9350-9354 | Service Bus Relay figyelő a TCP protokollon keresztül (az Access Control token beszerzéséhez 443 szükséges) | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queueing Protocol (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

Bizonyos esetekben Azure Service Bus kapcsolatok IP-címekkel, a teljes tartománynevek helyett. Ezért előfordulhat, hogy az adatterületéhez tartozó IP-címeket szeretné engedélyezni a tűzfalon. Ha tartomány helyett IP-címeket szeretne kijelölni, letöltheti és használhatja az [Microsoft Azure adatközpont IP-címtartományok listáját](https://www.microsoft.com/download/details.aspx?id=41653). A listában szereplő IP-címek [osztály nélküli tartományok közötti útválasztás (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel rendelkeznek.

### <a name="force-https-communication-with-azure-service-bus"></a>HTTPS-kommunikáció kényszerítése Azure Service Bus

Egyes proxyk csak a 80-es és a 443-es porton keresztül teszik lehetővé a forgalmat. Alapértelmezés szerint a Azure Service Bus-vel való kommunikáció a 443-től eltérő portokon történik.
Kényszerítheti az átjárót arra, hogy közvetlen TCP helyett a HTTPS-kapcsolaton keresztül kommunikáljon a Azure Service Bus, de ez nagy mértékben csökkentheti a teljesítményt. A feladat végrehajtásához kövesse az alábbi lépéseket:

1. Keresse meg a helyszíni adatátjáró-ügyfél helyét, amelyet általában itt talál:```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   Ellenkező esetben az ügyfél helyének megkereséséhez nyissa meg a szolgáltatások konzolt ugyanarra a számítógépre, keresse meg a helyszíni **adatátjáró szolgáltatást**, és tekintse meg a végrehajtható tulajdonság **elérési útját** .

2. Nyissa meg ezt a *konfigurációs* fájlt: **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. A **ServiceBusSystemConnectivityModeString** értékének módosítása az **automatikus észlelésről** a **https**-re:

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-szolgáltatásfiók

Azon a számítógépen, amelyen a helyszíni adatátjárót telepíti, az átjáró "helyszíni adatátjáró szolgáltatás" nevű Windows-szolgáltatásfiókként fut. Az átjáró azonban a "Bejelentkezés mint" fiók hitelesítő adatainak "NT SERVICE\PBIEgwService" nevét használja. Alapértelmezés szerint az átjáró a "Bejelentkezés szolgáltatásként" engedélyekkel rendelkezik azon a számítógépen, amelyre az átjárót telepíti. Az átjáróhoz tartozó Windows-szolgáltatásfiók általában eltér a helyszíni adatforrásokhoz való csatlakozáshoz használt fióktól, illetve a Cloud Services szolgáltatásba való bejelentkezéshez használt munkahelyi vagy iskolai fióktól.

Ahhoz, hogy az átjárót létrehozza és fenntartsa a Azure Portalban, ennek a Windows-szolgáltatásfiók legalább **közreműködői** engedélyekkel kell rendelkeznie. Az engedélyek ellenőrzését lásd: [a hozzáférés kezelése a RBAC és a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md). 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Átjáró újraindítása

Az adatátjáró ablak-szolgáltatásként fut, így a többi Windows-szolgáltatáshoz hasonlóan a különböző módokon is elindíthatja és leállíthatja az átjárót. Megnyithatja például a parancssort emelt szintű engedélyekkel azon a számítógépen, amelyen az átjáró fut, és futtathatja a következő parancsot:

* A szolgáltatás leállításához futtassa a következő parancsot:
  
  `net stop PBIEgwService`

* A szolgáltatás elindításához futtassa a következő parancsot:
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>Bérlői szintű felügyelet 

Jelenleg nincs olyan hely, ahol a bérlői rendszergazdák felügyelhetik a többi felhasználó által telepített és konfigurált összes átjárót. Ha Ön bérlői rendszergazda, akkor előfordulhat, hogy a szervezet felhasználóinak hozzá kell adnia a rendszergazdaként minden általuk telepített átjáróhoz. Így a szervezet összes átjáróját az átjáró beállításai lapon vagy [PowerShell](/data-integration/gateway/service-gateway-powershell-support)-parancsokkal kezelheti. 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>Hogyan működik az átjáró?

Az adatátjáró megkönnyíti a logikai alkalmazás, az átjáró Cloud Service és a helyszíni adatforrások közötti gyors és biztonságos kommunikációt. Az átjáró Cloud Service titkosítja és tárolja az adatforrás hitelesítő adatait és az átjáró részleteit. A szolgáltatás emellett a logikai alkalmazás, a helyszíni adatátjáró és a helyszíni adatforrások közötti lekérdezéseket és azok eredményeit is átirányítja. 

Az átjáró tűzfalakkal működik, és csak kimenő kapcsolatokat használ. Minden forgalom biztonságos kimenő adatforgalomból származik az átjáró ügynökének. Az átjáró a helyszíni forrásokból származó adatok továbbítását továbbítja a titkosított csatornákon a Azure Service Buson keresztül. Ez a Service Bus létrehoz egy csatornát az átjáró és a hívó szolgáltatás között, de nem tárol semmilyen adattárolót. Az átjárón keresztül áthaladó összes adatátvitel titkosítva van.

![diagram – helyszíni – adatátjáró – folyamat](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Ezek a lépések azt írják le, hogy mi történik, ha egy Felhőbeli felhasználó egy olyan elemmel kommunikál, amely a helyszíni adatforráshoz csatlakozik:

1. Az átjáró Cloud Service létrehoz egy lekérdezést az adatforrás titkosított hitelesítő adataival együtt, majd elküldi a lekérdezést az átjáró számára a feldolgozáshoz.

2. Az átjáró Cloud Service elemzi a lekérdezést, és leküldi a kérést a Azure Service Busnak.

3. A helyszíni adatátjáró lekérdezi a függőben lévő kérelmek Azure Service Busét.

4. Az átjáró lekéri a lekérdezést, visszafejti a hitelesítő adatokat, és az adatforráshoz kapcsolódik a hitelesítő adatokkal.

5. Az átjáró elküldi a lekérdezést az adatforrásnak a végrehajtáshoz.

6. Az eredményeket a rendszer visszaküldi az adatforrásból az átjárónak, majd az átjáró Cloud Service-nek. Az átjáró Cloud Service ezután az eredményeket használja.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="general"></a>Általános

**K**: Szükségem van-e átjáróra a felhőben található adatforrásokhoz, például Azure SQL Database? <br/>
**A**: Nem, az átjáró csak a helyszíni adatforrásokhoz kapcsolódik.

**K**: Az átjárót ugyanarra a gépre kell telepíteni, mint az adatforrást? <br/>
**A**: Nem, az átjáró a megadott kapcsolati adatok használatával csatlakozik az adatforráshoz. Ebben az értelemben vegye figyelembe az átjárót ügyfélalkalmazásként. Az átjárónak csak a megadott kiszolgálónévhez kell kapcsolódnia.

<a name="why-azure-work-school-account"></a>

**K**: Miért kell munkahelyi vagy iskolai fiókot használni a bejelentkezéshez? <br/>
**A**: A helyszíni adatátjáró telepítésekor csak munkahelyi vagy iskolai fiókot használhat. A bejelentkezési fiókot Azure Active Directory (Azure AD) által felügyelt bérlő tárolja. Az Azure AD-fiók egyszerű felhasználóneve (UPN) megfelel az e-mail-címnek.

**K**: Hol tárolódnak a hitelesítő adataim? <br/>
**A**: Az adatforráshoz megadott hitelesítő adatokat a rendszer titkosítja és tárolja az átjáró Cloud Service-ben. A hitelesítő adatok visszafejtése a helyszíni adatátjárón történik.

**K**: Vannak a hálózati sávszélességre vonatkozó követelmények? <br/>
**A**: Győződjön meg arról, hogy a hálózati kapcsolatok megfelelő átviteli sebességgel rendelkeznek. Minden környezet más, és az elküldhető adatmennyiség hatással lehet az eredményekre. A helyszíni adatforrások és az Azure-adatközpontok közötti átviteli sebesség biztosításához próbálja ki az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)-t. Az átviteli sebesség mérése érdekében Próbáljon ki egy külső eszközt, például az Azure Speed testet.

**K**: Mi a késés a lekérdezések egy adatforráshoz való futtatásához az átjáróról? Mi a legjobb architektúra? <br/>
**A**: A hálózati késés csökkentése érdekében telepítse az átjárót a lehető legközelebb az adatforráshoz. Ha az átjárót a tényleges adatforrásra is telepíti, ez a közelség lekicsinyíti a késést. Vegye fontolóra az Azure-adatközpontok közelségét is. Ha például a szolgáltatás az USA nyugati adatközpontját használja, és egy Azure-beli virtuális gépen van SQL Server üzemeltetve, akkor előfordulhat, hogy az Azure-beli virtuális gépet az USA nyugati régiójában is szeretné használni. Ez a közelség csökkentheti a késést, és elkerüli a kimenő forgalom díját az Azure-beli virtuális gépen.

**K**: Hogyan küldik vissza az eredményeket a felhőbe? <br/>
**A**: Az eredményeket a rendszer a Azure Service Buson keresztül továbbítja.

**K**: Vannak bejövő kapcsolatok az átjáróhoz a felhőből? <br/>
**A**: Nem, az átjáró kimenő kapcsolatokat használ Azure Service Bushoz.

**K**: Mi a teendő, ha blokkolom a kimenő kapcsolatokat? Mit kell megnyitnia? <br/>
**A**: Tekintse meg az átjáró által használt portokat és gazdagépeket.

**K**: Mi a tényleges Windows-szolgáltatás neve? <br/>
**A**: A Feladatkezelő szolgáltatások lapján a szolgáltatás neve "PBIEgwService" vagy Power BI Enterprise Gateway Service. A szolgáltatások konzolján a szolgáltatás neve "helyszíni adatátjáró szolgáltatás". A Windows-szolgáltatás az "NT SERVICE\PBIEgwService" szolgáltatást használja a szolgáltatás SID-ként (SSID).

**K**: Futtatható az átjáró Windows-szolgáltatás Azure Active Directory fiókkal? <br/>
**A**: Nem, a Windows-szolgáltatásnak érvényes Windows-fiókkal kell rendelkeznie.

### <a name="disaster-recovery"></a>Vészhelyreállítás

**K**: Milyen lehetőségek állnak rendelkezésre a katasztrófa utáni helyreállításhoz? <br/>
**A**: Az átjáró visszaállításához vagy áthelyezéséhez használhatja a helyreállítási kulcsot. Az átjáró telepítésekor a helyreállítási kulcsot kell megadnia.

**K**: Milyen előnyökkel jár a helyreállítási kulcs használata? <br/>
**A**: A helyreállítási kulcs vészhelyzet után áttelepítheti vagy helyreállíthatja az átjáró beállításait.

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz a helyszíni adatátjáró beállításakor és használatakor esetlegesen előforduló gyakori problémákat tárgyalja.

**K**: Miért nem sikerült az átjáró telepítése? <br/>
**A**: Ez a probléma akkor fordulhat elő, ha a célszámítógépen lévő vírusirtó szoftver elavult. Frissítheti a víruskereső szoftvert, vagy letilthatja a víruskereső szoftvert, de csak az átjáró telepítésekor, majd ismét engedélyezheti a szoftvert.

**K**: Miért nem látom az átjáróm telepítését az Azure-beli átjáró-erőforrás létrehozásakor? <br/>
**A**: Ez a probléma a következő okok miatt fordulhat elő:

* Az átjáró telepítése már regisztrálva van, és egy másik átjáró-erőforrás igényli az Azure-ban. Az átjáró-telepítések nem jelennek meg a példányok listáján az átjáró erőforrásainak létrehozása után.
Ha ellenőrizni szeretné az átjáró regisztrációját a Azure Portalban, tekintse át az összes Azure **-** erőforrást a helyszíni adatátjárók típusával az *összes* Azure-előfizetéshez. 

* Az átjárót telepítő személy Azure AD-identitása eltér a Azure Portalba bejelentkezett személytől. Győződjön meg arról, hogy ugyanazzal az identitással jelentkezett be, amely az átjárót telepítette.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**K**: Hol találhatók az átjárók? <br/>
**A**: Tekintse meg a cikk későbbi részében található [ **naplók** szakaszt](#logs) .

**K**: Hogyan láthatom, hogy milyen lekérdezéseket küld a rendszer a helyszíni adatforrásnak? <br/>
**A**: Engedélyezheti a lekérdezések nyomkövetését, beleértve az elküldött lekérdezéseket is. Ne felejtse el módosítani a lekérdezés-nyomkövetést az eredeti értékre, ha végzett a hibaelhárítással. A lekérdezés-nyomkövetés bekapcsolásának meghagyása nagyobb naplók létrehozását eredményezi.

Megtekintheti azokat az eszközöket is, amelyeknek az adatforrása nyomkövetési lekérdezéseket végez. Használhat például kiterjesztett eseményeket vagy SQL Profilert SQL Server és Analysis Serviceshoz.

### <a name="outdated-gateway-version"></a>Elavult átjáró verziója

Ha az átjáró verziója elavult, számos probléma merülhet fel. Általános gyakorlatként ellenőrizze, hogy rendelkezik-e a legújabb verzióval. Ha még nem frissítette az átjárót egy hónapig vagy már nem, érdemes lehet telepíteni az átjáró legújabb verzióját, és megtekintheti, hogy reprodukálható-e a probléma.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Hiba: Nem sikerült hozzáadni a felhasználót a csoporthoz. (-2147463168 PBIEgwService Teljesítménynapló felhasználói)

Ez a hiba akkor fordulhat elő, ha az átjárót egy olyan tartományvezérlőre próbálja meg telepíteni, amely nem támogatott. Ügyeljen arra, hogy az átjárót olyan gépre telepítse, amely nem tartományvezérlő.

<a name="logs"></a>

### <a name="logs"></a>Logs

A hibák megoldásához mindig kezdje az átjáró naplófájljainak összegyűjtésével és áttekintésével. A naplók gyűjtésének számos módja van, de az átjáró telepítése után a legegyszerűbb lehetőség az átjáró telepítő felhasználói felületén keresztül. 

1. A számítógépén nyissa meg a helyszíni adatátjáró telepítőjét.
2. A bal oldali menüben válassza a **diagnosztika**lehetőséget.
3. Az **átjáró naplófájljai**területen válassza a **naplók exportálása**lehetőséget.

   ![Naplók exportálása az átjáró-telepítőből](./media/logic-apps-gateway-install/export-logs.png)

A különböző naplók a következő helyekről érhetők el:

| Napló típusa | Location | 
|----------|----------| 
| **Telepítési naplók** | %localappdata%\Temp\On-premises_data_gateway_ <*ééééhhnn*>. <*szám*>. log | 
| **Konfigurációs naplók** | C:\Users\<*Felhasználónév*> \AppData\Local\Microsoft\On-premises gateway\GatewayConfigurator <*ééééhhnn*>. <*szám*>. log | 
| **Vállalati átjáró szolgáltatás naplófájljai** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises-gateway\Gateway <*ééééhhnn*>. <*szám*>. log | 
||| 

**Eseménynaplók**

Az átjáró eseménynaplóinak megkereséséhez kövesse az alábbi lépéseket:

1. Az átjárót telepítő számítógépen nyissa meg a **Eseménynapló**. 
2. Bontsa ki a **Eseménynapló (helyi)**  > **alkalmazások és szolgáltatások naplóit**. 
3. Válassza **a helyszíni adatátjáró szolgáltatást**.

   ![Az átjáró eseménynaplóinak megtekintése](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Lassú lekérdezési teljesítmény áttekintése

Ha úgy találja, hogy a lekérdezések lassan futnak az átjárón keresztül, bekapcsolhatja a további naplózást, amely a lekérdezéseket és azok időtartamait is megjeleníti. Ezek a naplók segíthetnek megkeresni, hogy mely lekérdezések lassúak vagy hosszúak. A lekérdezési teljesítmény finomhangolásához előfordulhat, hogy módosítania kell az adatforrást, például az indexek módosítása SQL Server lekérdezésekhez.

A lekérdezés időtartamának meghatározásához kövesse az alábbi lépéseket:

1. Keresse meg az átjáró-ügyféllel megegyező helyet, amelyet általában itt talál:```C:\Program Files\On-premises data gateway```

   Ellenkező esetben az ügyfél helyének megkereséséhez nyissa meg a szolgáltatások konzolt ugyanarra a számítógépre, keresse meg a helyszíni **adatátjáró szolgáltatást**, és tekintse meg a végrehajtható tulajdonság **elérési útját** .

2. Ezeket a konfigurációs fájlokat a következő módon nyithatja meg és szerkesztheti:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Ebben a fájlban módosítsa a **hamis** értékről az **EmitQueryTraces** értéket **, hogy az** átjáró az átjáróról egy adatforrásba küldött lekérdezéseket naplózza:

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > A EmitQueryTraces beállítás bekapcsolása jelentősen növelheti a napló méretét az átjáró használata alapján. Miután befejezte a naplók áttekintését, győződjön meg róla, hogy a EmitQueryTraces ismét **false** értékre állítja vissza, és ne hagyja hosszú távon ezt a beállítást.

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     Ha szeretné, hogy az átjáró részletes bejegyzései legyenek, beleértve az időtartamot megjelenítő bejegyzéseket is, módosítsa a **TracingVerbosity** értékét **4** – **5** értékre a következő lépés végrehajtásával: 

     * Ebben a konfigurációs fájlban módosítsa a **TracingVerbosity** értékét **4** – **5** értékre. 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * Nyissa meg az átjáró telepítőjét, válassza a **diagnosztika**lehetőséget, kapcsolja be a **további naplózást**, majd kattintson az **alkalmaz**gombra:

       ![További naplózás bekapcsolása](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > A TracingVerbosity beállítás bekapcsolása jelentősen növelheti a napló méretét az átjáró használata alapján. Miután befejezte a naplók áttekintését, győződjön meg róla, hogy kikapcsolja a **további naplózást** az átjáró-telepítőben, vagy állítsa vissza a TracingVerbosity a konfigurációs fájlban **, hogy ne** hagyja meg a beállítást hosszú távon.

3. A lekérdezés időtartamának megkereséséhez kövesse az alábbi lépéseket:

   1. [Exportálja](#logs) és nyissa meg az átjáró naplóját.

   2. Lekérdezés kereséséhez keresse meg a tevékenység típusát, például: 

      | Tevékenységtípus | Leírás | 
      |---------------|-------------| 
      | MGEQ | ADO.NET-en futó lekérdezések. | 
      | MGEO | Az OLEDB-t futtató lekérdezések. | 
      | MGEM | Az adategyesítési motorból futtatott lekérdezések. | 
      ||| 

   3. Jegyezze fel a második GUID azonosítót, amely a kérelem azonosítója.

   4. Folytassa a tevékenység típusának keresését, amíg meg nem talál egy "FireActivityCompletedSuccessfullyEvent" nevű bejegyzést, amelynek időtartama ezredmásodpercben van. 
   Erősítse meg, hogy a bejegyzés ugyanazzal a kérelem-AZONOSÍTÓval rendelkezik, például:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > A "FireActivityCompletedSuccessfullyEvent" bejegyzés egy részletes bejegyzés, amely nem kerül naplózásra, kivéve, ha az "TracingVerbosity" beállítás az 5. szinten van.

### <a name="trace-traffic-with-fiddler"></a>Forgalom nyomkövetése a Hegedűsvel

A [Hegedűs](https://www.telerik.com/fiddler) egy ingyenes eszköz a Telerik, amely FIGYELI a HTTP-forgalmat. A forgalmat áttekintheti az ügyfélszámítógép Power BI szolgáltatás. Ez a szolgáltatás hibákat és egyéb kapcsolódó információkat tartalmazhat.

## <a name="next-steps"></a>További lépések
    
* [Kapcsolódás a helyszíni adatokhoz a Logic appsből](../logic-apps/logic-apps-gateway-connection.md)
* [Vállalati integrációs funkciók](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Az Azure Logic Apps összekötői](../connectors/apis-list.md)
