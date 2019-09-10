---
title: Helyszíni adatátjáró telepítése – Azure Logic Apps
description: Mielőtt a helyszíni adatokhoz hozzáférhessen Azure Logic Appsről, töltse le és telepítse a helyszíni adatátjárót
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 657bc704e33e89b1646dffa6123a27169e6c317a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860803"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Helyszíni adatátjáró telepítése Azure Logic Apps

A helyszíni adatforrásokhoz való kapcsolódáshoz Azure Logic Apps a helyi számítógépen töltse le és telepítse a helyszíni adatátjárót. Az átjáró olyan hídként működik, amely gyors adatátvitelt és titkosítást biztosít a helyszínen (nem a felhőben) lévő adatforrások és a logikai alkalmazások között. Ugyanezt az átjáró-telepítést más felhőalapú szolgáltatásokkal is használhatja, például Power BI, Microsoft Flow, PowerApps és Azure Analysis Services. Az átjáró ezen szolgáltatásokkal való használatáról a következő cikkekben talál további információt:

* [Microsoft Power BI helyszíni adatátjáró](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Helyszíni adatátjáró Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Helyszíni adatátjáró Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Helyszíni adatátjáró Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Ez a cikk bemutatja, hogyan töltheti le, telepítheti és állíthatja be a helyszíni adatátjárót, hogy a helyszíni adatforrások hozzáférhessenek Azure Logic Apps. További információ arról, [hogy az adatátjáró hogyan működik a](#gateway-cloud-service) témakör későbbi részében.

<a name="supported-connections"></a>

Az átjáró támogatja a helyszíni [összekötőket](../connectors/apis-list.md#on-premises-connectors) Azure Logic Appsban ezekhez az adatforrásokhoz:

* BizTalk Server 2016
* Fájlrendszer
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Annak ellenére, hogy az átjáró önmagában nem jár további költségekkel, a [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md) ezen összekötők és a Azure Logic apps egyéb műveleteire is vonatkozik.

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

  * Az átjáró telepítéséhez és felügyeletéhez ugyanazt az Azure-fiókot kell használnia. A telepítés során ezzel az Azure-fiókkal társíthatja az átjárót a számítógépén egy Azure-előfizetéssel. Később ugyanazt az Azure-fiókot használja, amikor Azure-erőforrást hoz létre a Azure Portal az átjáró telepítéséhez. 

  * Be kell jelentkeznie munkahelyi fiókkal vagy iskolai fiókkal, más néven *szervezeti* fiókkal, amely a következőképpen néz ki `username@contoso.com`:. Nem használhat Azure B2B-(vendég-) fiókokat vagy személyes Microsoft-fiókokat @outlook.com, például @hotmail.com vagy.

    > [!TIP]
    > Ha regisztrált az Office 365-ajánlatra, és nem adta meg a munkahelyi e-mail-címét, előfordulhat `username@domain.onmicrosoft.com`, hogy a címe hasonlít. A fiókját egy Azure Active Directory (Azure AD) bérlőn belül tárolja a rendszer. A legtöbb esetben az Azure AD-fiókhoz tartozó egyszerű felhasználónév (UPN) megegyezik az e-mail-címével.
    >
    > Ha egy Microsoft-fiókhoz társított [Visual Studio standard-előfizetést](https://visualstudio.microsoft.com/vs/pricing/) szeretne használni, először [hozzon létre egy bérlőt az Azure ad-ben](../active-directory/develop/quickstart-create-new-tenant.md), vagy használja az alapértelmezett könyvtárat. Adjon hozzá egy jelszót tartalmazó felhasználót a címtárhoz, majd adja meg, hogy a felhasználó hozzáférjen az előfizetéséhez. 
    > Ezt a felhasználónevet és jelszót használva az átjáró telepítése közben is bejelentkezhet.

* A helyi számítógépekre vonatkozó követelmények:

  **Minimális követelmények**

  * .NET-keretrendszer 4.6
  * a Windows 7 vagy a Windows Server 2008 R2 64 bites verziója (vagy újabb)

  **Ajánlott követelmények**

  * 8 magos processzor
  * 8 GB memória
  * a Windows Server 2012 R2 vagy újabb 64 bites verziója
  * SSD-tároló a várólista-tároláshoz

  > [!NOTE]
  > Az átjáró nem támogatja a Windows Server 2016 Core rendszert.

* **Kapcsolódó megfontolások**

  * A helyszíni adatátjárót csak helyi számítógépen, nem tartományvezérlőn is telepítheti. Azonban nem kell telepítenie az átjárót ugyanarra a számítógépre, mint az adatforrást. Az összes adatforráshoz csak egy átjáró szükséges, így nem kell telepítenie az átjárót az egyes adatforrásokhoz.

    > [!TIP]
    > A késés csökkentése érdekében az átjárót a lehető legközelebb az adatforráshoz vagy ugyanazon a számítógépen telepítheti, feltéve, hogy rendelkezik a megfelelő engedélyekkel.

  * Telepítse az átjárót olyan számítógépre, amely vezetékes hálózaton található, az internethez csatlakozik, mindig be van kapcsolva, és nem alvó állapotba kerül. Ellenkező esetben az átjáró nem futtatható, és a teljesítmény a vezeték nélküli hálózaton keresztül csökkenhet.

  * Windows-hitelesítés használata esetén győződjön meg arról, hogy az átjárót olyan számítógépre telepíti, amely az adatforrásokkal megegyező Active Directory-környezet tagja.

  * Az átjáró telepítéséhez kiválasztott régió ugyanaz a hely, amelyet később a logikai alkalmazáshoz tartozó Azure Gateway-erőforrás létrehozásakor ki kell választania. Alapértelmezés szerint ez a régió megegyezik az Azure-fiókját kezelő Azure AD-Bérlővel. A helyet azonban megváltoztathatja az átjáró telepítése közben is.

  * Az átjárónak két módja van: a standard mód és a személyes mód, amely csak a Power BIre vonatkozik. Ugyanazon a számítógépen nem lehet egynél több olyan átjáró, amely ugyanazon a módban fut.

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Adatátjáró telepítése

1. [Töltse le és futtassa az átjáró telepítőjét egy helyi számítógépen](https://aka.ms/on-premises-data-gateway-installer).

1. A telepítő megnyitása után kattintson a **Tovább gombra**.

   ![A telepítő bemutatása](./media/logic-apps-gateway-install/gateway-intro-screen.png)

1. Válassza **a helyszíni adatátjáró (ajánlott)** lehetőséget, amely standard mód, majd válassza a **tovább**lehetőséget.

   ![Átjáró mód kiválasztása](./media/logic-apps-gateway-install/select-gateway-mode.png)

1. Tekintse át a minimális követelményeket, tartsa meg az alapértelmezett telepítési útvonalat, fogadja el a használati feltételeket, majd válassza a **telepítés**lehetőséget.

   ![A követelmények áttekintése és a használati feltételek elfogadása](./media/logic-apps-gateway-install/accept-terms.png)

1. Az átjáró sikeres telepítése után adja meg az e-mail-címet a szervezeti fiókhoz, majd válassza a **Bejelentkezés**lehetőséget, például:

   ![Bejelentkezés munkahelyi vagy iskolai fiókkal](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Most bejelentkezett a fiókjába.

1. Jelölje be az **új átjáró regisztrálása ezen a számítógépen** > jelölőnégyzetet **.** Ez a lépés regisztrálja az átjáró telepítését az [átjáró Cloud Service](#gateway-cloud-service)-ben.

   ![Átjáró regisztrálása](./media/logic-apps-gateway-install/register-gateway.png)

1. Adja meg az átjáró telepítéséhez szükséges információkat:

   * Egy átjáró neve, amely egyedi az Azure AD-bérlőn belül
   * A használni kívánt helyreállítási kulcsnak legalább nyolc karakterből kell állnia.
   * A helyreállítási kulcs megerősítése

   ![Átjáró beállítása](./media/logic-apps-gateway-install/set-up-gateway.png)

   > [!IMPORTANT]
   > Mentse és őrizze meg a helyreállítási kulcsot biztonságos helyen. Erre a kulcsra akkor van szükség, ha módosítani szeretné az átjárók helyét, áthelyezését, helyreállítását vagy átvételét.

   Vegye figyelembe, hogy **egy meglévő átjáró fürthöz való hozzáadás**lehetősége, amelyet akkor kell kiválasztani, amikor további átjárókat telepít a [magas rendelkezésre állású forgatókönyvekhez](#high-availability).

1. Keresse meg az átjáró felhőalapú szolgáltatásának régióját, és [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) , amelyet az átjáró telepítése használ. Alapértelmezés szerint ez a régió ugyanaz a hely, mint az Azure AD-bérlő az Azure-fiókjához.

   ![Régió ellenőrzési területe](./media/logic-apps-gateway-install/check-region.png)

1. Az alapértelmezett régió elfogadásához válassza a **Konfigurálás**lehetőséget. Ha azonban az alapértelmezett régió nem az Önhöz legközelebb eső, akkor módosíthatja a régiót.

   *Miért érdemes módosítani a régiót az átjáró telepítésekor?*

   Például a késés csökkentése érdekében előfordulhat, hogy az átjáró régióját a logikai alkalmazással megegyező régióra változtatja. Vagy a helyszíni adatforráshoz legközelebb eső régiót is kiválaszthatja. Az *Azure* -beli átjáró-erőforrás és a logikai alkalmazás különböző helyekkel rendelkezhet.

   1. Az aktuális régió mellett válassza a **régió módosítása**lehetőséget.

      ![Régió módosítása](./media/logic-apps-gateway-install/change-region.png)

   1. A következő lapon nyissa meg a **régió kiválasztása** listát, válassza ki a kívánt régiót, majd válassza a **kész**lehetőséget.

      ![Válasszon másik régiót](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Tekintse át a végső megerősítési ablakban található információkat. Ez a példa ugyanazt a fiókot használja Logic Apps, Power BI, PowerApps és Microsoft Flow esetében, így az átjáró elérhető az összes szolgáltatáshoz. Ha elkészült, válassza a **Bezárás**lehetőséget.

   ![Befejezett átjáró](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Most [hozza létre az Azure-erőforrást az átjáró telepítéséhez](../logic-apps/logic-apps-gateway-connection.md).

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Magas rendelkezésre állás támogatása

Ha el szeretné kerülni a helyszíni adathozzáféréshez szükséges egyetlen meghibásodási pontot, a különböző számítógépeken egyszerre több átjáró telepíthető (csak standard módban), és beállíthatja őket fürtként vagy csoportként. Így ha az elsődleges átjáró nem érhető el, az adatkérések átirányítva a második átjáróra, és így tovább. Mivel a számítógépen csak egy standard átjáró telepíthető, a fürtben lévő minden további átjárót telepíteni kell egy másik számítógépen. A helyszíni adatátjáróval működő összes összekötő támogatja a magas rendelkezésre állást. 

* A telepítéshez már legalább egy Azure-előfizetéshez tartozó átjárót kell telepítenie, mint az elsődleges átjárót és a helyreállítási kulcsot.

* Az elsődleges átjárónak az átjáró frissítését november 2017 vagy újabb verzióra kell futtatnia.

Miután beállította az elsődleges átjárót, amikor egy másik átjárót telepít, válassza a **Hozzáadás meglévő átjáró-fürthöz**lehetőséget, válassza ki az elsődleges átjárót, amely az első telepített átjáró, és adja meg az adott átjáró helyreállítási kulcsát. További információ: [magas rendelkezésre állású fürtök helyszíni adatátjáróhoz](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Megváltoztathatja a meglévő átjáró helyét, áttelepítését, visszaállítását vagy átvételét

Ha módosítania kell az átjáró helyét, helyezze át az átjáró telepítését egy új számítógépre, állítson helyre egy sérült átjárót, vagy vegyen fel egy meglévő átjáró tulajdonjogát, szüksége lesz az átjáró telepítésekor megadott helyreállítási kulcsra.

1. Futtassa az átjáró telepítőjét azon a számítógépen, amelyen a meglévő átjáró található. Ha nem rendelkezik a legújabb átjáró-telepítővel, [töltse le az átjáró legújabb verzióját](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Mielőtt visszaállítja az átjárót azon a számítógépen, amelyen az eredeti átjáró telepítve van, először el kell távolítania az átjárót az adott számítógépen. Ez a művelet leválasztja az eredeti átjárót.
   > Ha bármely felhőalapú szolgáltatáshoz eltávolít vagy töröl egy átjáró-fürtöt, akkor a fürt nem állítható vissza.

1. A telepítő megnyitása után jelentkezzen be ugyanazzal az Azure-fiókkal, amelyet az átjáró telepítéséhez használt.

1. Válassza a >  **meglévő átjáró áttelepíteni, visszaállítása vagy átvétele** **lehetőséget, például**:

   ![Válassza a "meglévő átjáró migrálása, visszaállítása vagy átvétele" lehetőséget.](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Válasszon a rendelkezésre álló fürtök és átjárók közül, és adja meg a kiválasztott átjáró helyreállítási kulcsát, például:

   ![Átjáró kiválasztása](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. A régió módosításához válassza a **régió módosítása**lehetőséget, és válassza ki az új régiót.

1. Ha elkészült, válassza a **Konfigurálás** lehetőséget a feladat befejezéséhez.

## <a name="configure-proxy-or-firewall"></a>Proxy vagy tűzfal konfigurálása

Ha a munkahelyi környezet megköveteli, hogy a forgalom egy proxyn keresztül hozzáférjen az internethez, akkor ez a korlátozás megakadályozhatja, hogy a helyszíni adatátjáró csatlakozzon az átjáró Cloud Service-hez, és [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). További információ: [configure proxy Settings for the](https://docs.microsoft.com/power-bi/service-gateway-proxy)helyszíni adatátjáró.

Annak ellenőrzéséhez, hogy a proxy vagy a tűzfal blokkolja-e a kapcsolatokat, ellenőrizze, hogy a számítógép tud-e csatlakozni az internethez, és Azure Service Bus. Egy PowerShell-parancssorból futtassa a következő parancsot:

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

<a name="configure-ports"></a>

## <a name="configure-ports"></a>Portok konfigurálása

Az átjáró egy kimenő kapcsolatot hoz létre a Azure Service Bushoz, és kommunikál a kimenő portokon: TCP 443 (alapértelmezett), 5671, 5672, 9350 az 9354-n keresztül. Az átjáró nem igényel bejövő portokat. További információ a [Azure Service Bus és a hibrid megoldásokról](../service-bus-messaging/service-bus-messaging-overview.md).

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

Bizonyos esetekben Azure Service Bus kapcsolatok IP-címekkel, a teljes tartománynevek helyett. Ezért előfordulhat, hogy fel kívánja oldani az adatterülete IP-címeinek feloldását a tűzfalon. A tartományok helyett IP-címek elérésének engedélyezéséhez letöltheti és használhatja az [Microsoft Azure adatközpont IP-címtartományok listáját](https://www.microsoft.com/download/details.aspx?id=41653). A listában szereplő IP-címek [osztály nélküli tartományok közötti útválasztás (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel rendelkeznek.

### <a name="force-https-communication-with-azure-service-bus"></a>HTTPS-kommunikáció kényszerítése Azure Service Bus

Egyes proxyk csak a 80-es és a 443-es porton keresztül teszik lehetővé a forgalmat. Alapértelmezés szerint a Azure Service Bus-vel való kommunikáció a 443-től eltérő portokon történik. Kényszerítheti az átjárót arra, hogy közvetlen TCP helyett a HTTPS-kapcsolaton keresztül kommunikáljon a Azure Service Bus, de ez nagy mértékben csökkentheti a teljesítményt. További információkért lásd: [HTTPS-kommunikáció kényszerítése Azure Service Busokkal](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication#force-https-communication-with-azure-service-bus).

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows-szolgáltatásfiók

Alapértelmezés szerint az átjáró helyi számítógépen történő telepítése a "helyszíni adatátjáró szolgáltatás" nevű Windows-szolgáltatásfiókként fut. Az átjáró telepítése azonban a `NT SERVICE\PBIEgwService` "Bejelentkezés mint" fiók hitelesítő adatait használja, és "Bejelentkezés szolgáltatásként" engedélyekkel rendelkezik.

> [!NOTE]
> A Windows-szolgáltatásfiók eltér a helyszíni adatforrásokhoz való csatlakozáshoz használt fióktól és az Azure-fióktól, amelyet a Cloud Services szolgáltatásba való bejelentkezéskor használ.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Átjáró újraindítása

Az adatátjáró ablak-szolgáltatásként fut, így a többi Windows-szolgáltatáshoz hasonlóan a különböző módokon is elindíthatja és leállíthatja az átjárót. További információ: [helyszíni adatátjáró újraindítása](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

## <a name="tenant-level-administration"></a>Bérlői szintű felügyelet

Az Azure AD-bérlőben található összes helyszíni adatátjáró megismeréséhez a bérlő globális rendszergazdái bejelentkezhetnek a [Power platform felügyeleti központjába](https://powerplatform.microsoft.com) bérlői rendszergazdaként, és az **adatátjárók** lehetőségre is kiválaszthatók. További információ: [bérlői szintű felügyelet a helyszíni adatátjáróhoz](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Az átjáró működése

Az adatátjáró megkönnyíti a logikai alkalmazás, az átjáró Cloud Service és a helyszíni adatforrások közötti gyors és biztonságos kommunikációt. Az átjáró Cloud Service titkosítja és tárolja az adatforrás hitelesítő adatait és az átjáró részleteit. A szolgáltatás emellett a logikai alkalmazás, a helyszíni adatátjáró és a helyszíni adatforrások közötti lekérdezéseket és azok eredményeit is átirányítja.

Az átjáró tűzfalakkal működik, és csak kimenő kapcsolatokat használ. Minden forgalom biztonságos kimenő adatforgalomból származik az átjáró ügynökének. Az átjáró a helyszíni forrásokból származó adatok továbbítása a Azure Service Buson keresztül titkosított csatornákon keresztül történik. Ez a Service Bus létrehoz egy csatornát az átjáró és a hívó szolgáltatás között, de nem tárol semmilyen adattárolót. Az átjárón keresztül áthaladó összes adatátvitel titkosítva van.

![Helyszíni adatátjáró architektúrája](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Ezek a lépések azt írják le, hogy mi történik, ha egy Felhőbeli felhasználó egy olyan elemmel kommunikál, amely a helyszíni adatforráshoz csatlakozik:

1. Az átjáró Cloud Service létrehoz egy lekérdezést az adatforrás titkosított hitelesítő adataival együtt, majd elküldi a lekérdezést az átjáró számára a feldolgozáshoz.

1. Az átjáró Cloud Service elemzi a lekérdezést, és leküldi a kérést a Azure Service Busnak.

1. A helyszíni adatátjáró lekérdezi a függőben lévő kérelmek Azure Service Busét.

1. Az átjáró lekéri a lekérdezést, visszafejti a hitelesítő adatokat, és az adatforráshoz kapcsolódik a hitelesítő adatokkal.

1. Az átjáró elküldi a lekérdezést az adatforrásnak a végrehajtáshoz.

1. Az eredményeket a rendszer visszaküldi az adatforrásból az átjárónak, majd az átjáró Cloud Service-nek. Az átjáró Cloud Service ezután az eredményeket használja.

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

* Az átjáró telepítése már regisztrálva van, és egy másik átjáró-erőforrás igényli az Azure-ban. Az átjáró-telepítések nem jelennek meg a példányok listáján az átjáró erőforrásainak létrehozása után. Ha ellenőrizni szeretné az átjáró regisztrációját a Azure Portalban, tekintse át az összes Azure-erőforrást a helyszíni **adatátjárók** típusával az *összes* Azure-előfizetéshez.

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

1. A bal oldali menüben válassza a **diagnosztika**lehetőséget.

1. Az **átjáró naplófájljai**területen válassza a **naplók exportálása**lehetőséget.

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

1. Bontsa ki a **Eseménynapló (helyi)**  > **alkalmazások és szolgáltatások naplóit**.

1. Válassza **a helyszíni adatátjáró szolgáltatást**.

   ![Az átjáró eseménynaplóinak megtekintése](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>Lassú lekérdezési teljesítmény áttekintése

Ha úgy találja, hogy a lekérdezések lassan futnak az átjárón keresztül, bekapcsolhatja a további naplózást, amely a lekérdezéseket és azok időtartamait is megjeleníti. Ezek a naplók segíthetnek megkeresni, hogy mely lekérdezések lassúak vagy hosszúak. A lekérdezési teljesítmény finomhangolásához előfordulhat, hogy módosítania kell az adatforrást, például az indexek módosítása SQL Server lekérdezésekhez.

A lekérdezés időtartamának meghatározásához kövesse az alábbi lépéseket:

1. Keresse meg az átjáró-ügyféllel megegyező helyet, amelyet általában itt talál:```C:\Program Files\On-premises data gateway```

   Ellenkező esetben az ügyfél helyének megkereséséhez nyissa meg a szolgáltatások konzolt ugyanarra a számítógépre, keresse meg a helyszíni **adatátjáró szolgáltatást**, és tekintse meg a **végrehajtható tulajdonság elérési útját** .

1. Ezeket a konfigurációs fájlokat a következő módon nyithatja meg és szerkesztheti:

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     Ebben a fájlban módosítsa a **hamis** értékről a **EmitQueryTraces** értéket **úgy, hogy az** átjáró az átjáróról egy adatforrásba küldött lekérdezéseket naplózza:

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
     > A TracingVerbosity beállítás bekapcsolása jelentősen növelheti a napló méretét az átjáró használata alapján. Miután befejezte a naplók áttekintését, győződjön meg róla, hogy kikapcsolja a **további naplózást** az átjáró-telepítőben, vagy állítsa vissza a **TracingVerbosity a konfigurációs fájlban, hogy ne** hagyja meg a beállítást hosszú távon.

1. A lekérdezés időtartamának megkereséséhez kövesse az alábbi lépéseket:

   1. [Exportálja](#logs) és nyissa meg az átjáró naplóját.

   1. Lekérdezés kereséséhez keresse meg a tevékenység típusát, például:

      | Tevékenységtípus | Leírás |
      |---------------|-------------|
      | MGEQ | ADO.NET-en futó lekérdezések |
      | MGEO | OLEDB-en futó lekérdezések |
      | MGEM | Az adategyesítési motorból futtatott lekérdezések |
      |||

   1. Jegyezze fel a második GUID azonosítót, amely a kérelem azonosítója.

   1. Folytassa a tevékenység típusának keresését, amíg meg nem talál egy "FireActivityCompletedSuccessfullyEvent" nevű bejegyzést, amelynek időtartama ezredmásodpercben van. Erősítse meg, hogy a bejegyzés ugyanazzal a kérelem-AZONOSÍTÓval rendelkezik, például:

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
