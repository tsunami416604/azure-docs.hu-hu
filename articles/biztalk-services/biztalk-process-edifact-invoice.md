---
title: 'Oktatóanyag: Az Azure BizTalk Services segítségével EDIFACT számlák feldolgozása |} A Microsoft Docs'
description: Hogyan hozhat létre, és a Box-összekötő vagy az API-alkalmazás konfigurálása és használhatja az Azure App Service logikai alkalmazás
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: bb07e3ab8043aab24d6d8c3e3db3f3674b28c6f3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244491"
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Oktatóanyag: Folyamat EDIFACT számlák Azure BizTalk Services használatával

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

A BizTalk Services portáljának használatával konfigurálhatja és telepítheti a X12 és EDIFACT-egyezmények. Ebben az oktatóanyagban áttekintjük, hogyan hozhat létre egy EDIFACT-egyezmény a kereskedelmi partnerek közötti számlák cseréjét. Ebben az oktatóanyagban egy teljes körű üzleti megoldás használata esetén, amelyek exchange-EDIFACT-üzenetek Northwind és Contoso két kereskedelmi partnerek körül van megírva.  

## <a name="sample-based-on-this-tutorial"></a>Ez az oktatóanyag alapján minta
Ebben az oktatóanyagban egy mintául szolgáló körül írt **EDIFACT számlák használatával a BizTalk Services küldése**, letölthető vagyis a [MSDN Kódgalériából](https://go.microsoft.com/fwlink/?LinkId=401005). Sikerült a mintát használja, és megérteni, hogyan lett létrehozva a minta az oktatóanyag. Másik lehetőségként ebben az oktatóanyagban használhatja saját megoldás földön másolatot létrehozni. Ebben az oktatóanyagban a második megközelítéssel cél, hogy megismerte, hogyan lett létrehozva az ebben a megoldásban. Ezenkívül, amennyire csak lehetséges, az oktatóanyag konzisztensek legyenek a minta és -összetevők (például a sémák, a átalakítások) ugyanazokat a neveket használja, mint a mintában használt.  

> [!NOTE]
> Mivel ezen megoldás keretein belül üzenet küldése egy EAI-hidat, EDI-híd, használja a [BizTalk Services híd minta láncolási](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) minta.  
> 
> 

## <a name="what-does-the-solution-do"></a>Mit jelent a megoldás?
Ebben a megoldásban a Northwind kap a Contoso EDIFACT számlák. A következő számlák nem szabványos EDI formátumban vannak. Így a számlát küld a Northwind, mielőtt azt kell átalakítva EDIFACT számlák (más néven INVOIC) dokumentumhoz. Kézhezvétele után Northwind kell az EDIFACT számlák feldolgozása, és térjen vissza egy ellenőrző üzenet (más néven CONTRL) Contoso.

![][1]  

Az üzleti forgatókönyv eléréséhez a Contoso Microsoft Azure BizTalk Services által nyújtott szolgáltatásokat használ.

* Contoso EAI-hidak használja az eredeti számlát EDIFACT INVOIC átalakítására.
* A EAI-hidat ezután elküldi az üzenetet egy EDI küldési híd a BizTalk Services portálon konfigurált szerződés részeként.
* Az EDI-küldési híd az EDIFACT INVOIC feldolgozza, és továbbítja azt a Northwind.
* A számla megjelenését követően a Northwind visszaadja egy CONTRL üzenetet az EDI a szerződés részeként üzembe helyezett híd kapnak.  

> [!NOTE]
> Szükség esetén ez a megoldás is bemutatja, hogyan kötegelés használata helyett minden számlán külön-külön kötegekben a számlák küldése.  
> 
> 

A forgatókönyv végrehajtásához a Service Bus-üzenetsorok a számla a Contoso Northwind küldeni vagy fogadni a nyugtázás a Northwind használjuk. Ezek a várólisták hozható létre egy ügyfélalkalmazást, amely letölthető, és tartalmazza a minta csomagban elérhető ez az oktatóanyag részeként.  

## <a name="prerequisites"></a>Előfeltételek
* Service Bus-névtér kell rendelkeznie. Egy névtér létrehozásával kapcsolatos útmutatóért lásd: [Útmutató: létrehozása vagy módosítása egy Service Bus szolgáltatás Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx). Tegyük fel, hogy már rendelkezik kiépített, a Service Bus-névtér nevű **edifactbts**.
* A BizTalk Services-előfizetéssel kell rendelkeznie. Ebben az oktatóanyagban Tételezzük nevű BizTalk Services-előfizetéssel rendelkezik **contosowabs**.
* A BizTalk Services portálon a BizTalk Services-előfizetés regisztrálása. Útmutatásért lásd: [BizTalk szolgáltatások üzembe helyezéséhez a BizTalk Services portálon regisztrálása](https://msdn.microsoft.com/library/hh689837.aspx)
* A Visual Studio telepítve kell rendelkeznie.
* A BizTalk Services SDK-t kell rendelkeznie. Letöltheti az SDK-t a [http://go.microsoft.com/fwlink/?LinkId=235057](https://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>1. lépés: Hozzon létre a Service Bus-üzenetsorok
Ez a megoldás Service Bus-üzenetsorok használatával kereskedelmi partnerek közötti üzeneteket. A Contoso és a Northwind küldhet üzeneteket az üzenetsorok, ahol az EAI-t és/vagy EDI hidak felhasználni azokat. Ez a megoldás három Service Bus-üzenetsorok szüksége lesz:

* **northwindreceive** – Northwind fogad a számla a Contoso ennek az üzenetsornak.
* **contosoreceive** – Contoso fogad a nyugtázás a Northwind ennek az üzenetsornak.
* **felfüggesztett** – az összes felfüggesztett üzeneteket ennek az üzenetsornak legyenek irányítva. Üzenetek fel vannak függesztve, ha feldolgozása során.

A Service Bus-üzenetsorok a minta-csomagban található ügyfélalkalmazás használatával hozhat létre.  

1. A hely, ahová letöltötte a minta, nyissa meg a **oktatóanyag küldése számlákat használatával a BizTalk Services EDI Bridges.sln**.
2. Nyomja meg **F5** hozhat létre, és indítsa el a **oktatóanyag ügyfél** alkalmazás.
3. A képernyőn adja meg a Service Bus-ACS-névtér, kiállító neve és kiállító kulcsa.
   
   ![][2]  
4. Egy üzenetpanel kérni fogja, hogy három üzenetsorok jön létre a Service Bus-névtérben. Kattintson az **OK** gombra.
5. Hagyja meg az oktatóanyag futtató ügyfélen. Nyissa meg kattintson **a Service Bus** > ***a Service Bus-névtér*** > **üzenetsorok**, és győződjön meg arról, hogy a három várólisták jöttek létre.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>2. lépés: Hozzon létre, és üzembe helyezése a kereskedelmi partneri szerződés
Hozzon létre kereskedelmipartner-egyezmény a Contoso és a Northwind között. Kereskedelmipartner-egyezmény határozza meg, hogy a kereskedelmi szerződés, a két üzleti partnerek között, például a melyik üzenet-sémát használja, mely üzenetkezelő protokoll, stb. Kereskedelmipartner-egyezmény két EDI hidak, egy kereskedelmi partnerekkel való üzenetküldéshez tartalmaz (nevű a **EDI küldése híd**), a másik az üzeneteket fogadhat a kereskedelmi partnerekkel (nevű a **EDI kap híd**).

Ez a megoldás kontextusában a EDI küldési híd felel meg a szerződés küldési-oldalon, és a Contoso küldendő az EDIFACT számlák Northwind használja. Ehhez hasonlóan a EDI receive híd felel meg annak a szerződésnek a fogadóoldali, és a Northwind receive nyugtázás szolgál.  

### <a name="create-the-trading-partners"></a>A kereskedelmi partnerek létrehozása
Első lépésként hozzon létre a Contoso és a Northwind kereskedelmi partnerek.  

1. A BizTalk Services Portáljára a a **partnerek** lapra, majd **Hozzáadás**.
2. Adja meg az új partner lapon **Contoso** partner neve, és kattintson **mentése**.
3. Ismételje meg a második partner létrehozása **Northwind**.  

### <a name="create-the-agreement"></a>A megállapodás létrehozása
Kereskedelmipartner-egyezmények profilok kereskedelmi partnerek között jönnek létre. Ez a megoldás a partnerek létrehozásakor automatikusan létrehozott alapértelmezett partner profilok használ.  

1. A BizTalk Services portáljának kattintson **szerződések** > **Hozzáadás**.
2. Az új szerződés **általános beállítások** lapon adja meg az értékeket az alábbi képen látható módon, és kattintson a **Folytatás**.
   
   ![][3]  
   
   Miután rákattintott **Folytatás**, két lap található, **fogadási beállítások** és **küldési beállítások** elérhetővé válnak.
3. Hozzon létre a Contoso és a Northwind küldési megállapodást. A jelen szerződés szabályozza, hogy hogyan Contoso Northwind EDIFACT számlát küld.
   
   1. Kattintson a **küldési beállítások**.
   2. Megtartja az alapértelmezett értékeket a a **URL-cím bejövő**, **átalakítása**, és **kötegelés** lapokon.
   3. Az a **protokoll** lap a **sémák** szakaszt, és töltse fel a **EFACT_D93A_INVOIC.xsd** séma. Ebben a sémában a minta csomag érhető el.
      
      ![][4]  
   4. Az a **átviteli** lapra, adja meg a Service Bus-üzenetsorok adatait. A küldési-oldalon szerződés használjuk a **northwindreceive** az EDIFACT számlák küldendő Northwind, üzenetsor és a **felfüggesztve** várólista irányíthatja a feldolgozás során nem sikerül, és fel vannak függesztve üzeneteket. Ezek a várólisták a létrehozott **1. lépés: hozzon létre a Service Bus-üzenetsorok** (az ebben a témakörben).
      
      ![][5]  
      
      A **átviteli beállításai > típus átviteli** és **üzenetek felfüggesztése beállításai > típus átviteli**, válassza ki az Azure Service Bus, és adja meg az értékeket, az ábrán látható módon.
4. Hozzon létre a Contoso és a Northwind receive megállapodást. A jelen szerződés szabályozza, hogy hogyan Contoso fogadja a nyugtázás Northwind.
   
   1. Kattintson a **kapják meg a beállításokat**.
   2. Megtartja az alapértelmezett értékeket a a **átviteli** és **átalakítása** lapokon.
   3. Az a **protokoll** lap a **sémák** szakaszt, és töltse fel a **EFACT_4.1_CONTRL.xsd** séma. Ebben a sémában a minta csomag érhető el.
   4. Az a **útvonal** fülre, hozzon létre egy szűrőt annak biztosítására, hogy csak a nyugtázás a Northwind Contoso legyenek irányítva. A **útvonal beállítások**, kattintson a **Hozzáadás** az útválasztási szűrő létrehozásához.
      
      ![][6]  
      
      1. Adja meg az értékeket **szabálynév**, **útválasztási szabály**, és **útvonal cél** a képen látható módon.
      2. Kattintson a **Save** (Mentés) gombra.
   5. Az a **útvonal** újra lap, adja meg, ahol felfüggesztett nyugtázás (a nyugtázás a feldolgozás során sikertelen) legyenek irányítva. Állítsa be az átviteli típust, az Azure Service Bus, irányíthatja a cél típusát a **várólista**, hitelesítés típusa **közös hozzáférésű Jogosultságkód** (SAS), adja meg a SAS-kapcsolati karakterláncot a Service Bus-névtér majd adja meg az üzenetsor neve, mint **felfüggesztve**.
5. Végül kattintson **telepítés** üzembe helyezéséhez a szerződést. Vegye figyelembe a végpontok, küldése és fogadása szerződések üzembe lesznek helyezve.
   
   * Az a **küldési beállítások** lap **URL-cím bejövő**, vegye figyelembe a végpontot. Üzenet küldése a Contoso az EDI-küldési híd használata Northwind, ezt a végpontot kell küldenie egy üzenetet.
   * Az a **fogadási beállítások** lap **átviteli**, vegye figyelembe a végpontot. Üzenet küldése a Northwind contoso használatával az EDI kap híd, egy üzenet küldéséhez kell ezt a végpontot.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>3. lépés: Hozzon létre, és a BizTalk Services-projekt üzembe helyezése
Az előző lépésben üzembe helyezett EDI küldése és feldolgozni EDIFACT számlák és a nyugtázás a szerződések kap. Ezek a szerződések csak megfeleljen a standard szintű EDIFACT-üzenet sémának folyamat üzeneteket is. Azonban a forgatókönyvben a megoldás egy Contoso küld számla Northwind házon védett sémában. Tehát az üzenetet küld az EDI-küldési híd, mielőtt azt kell lesz átalakítva a belső fejlesztésű séma a standard EDIFACT számlák sémához. A BizTalk Services EAI-projekt végzi, amely.

A BizTalk Services projekt **InvoiceProcessingBridge**, hogy átalakítások az üzenet is tartalmaz a letöltött minta részeként. A projektet a következő összetevőket tartalmazza:

* **INHOUSEINVOICE. XSD** – a belső fejlesztésű számla Northwind küldött sémájával.
* **EFACT_D93A_INVOIC. XSD** – a standard szintű EDIFACT számlák sémájával.
* **EFACT_4.1_CONTRL. XSD** – az EDIFACT-átvételi, amely Northwind küld a Contoso séma.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – az átalakítás, amely a belső fejlesztésű számla séma a standard szintű EDIFACT számlák séma van leképezve.  

### <a name="create-the-biztalk-services-project"></a>A BizTalk Services-projekt létrehozása
1. A Visual Studio-megoldásban, bontsa ki a InvoiceProcessingBridge projektet, és nyissa meg a **MessageFlowItinerary.bcs** fájlt.
2. Kattintson bárhová a vásznon, és állítsa be a **BizTalk-szolgáltatás URL-címe** a tulajdonság mezőbe, írja be a BizTalk Services-előfizetés nevét. Például: `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. A eszközkészletből, húzza át egy **Xml One-Way híd** a vászonra. Állítsa be a **entitásnév** és **Relativní Adresa** a híd tulajdonságainak **ProcessInvoiceBridge**. Kattintson duplán a **ProcessInvoiceBridge** a híd konfigurációs felület megnyitásához.
4. Belül a **üzenettípusok** párbeszédpanelen kattintson a plusz (**+**) gombra kattintva adja meg a bejövő üzenet sémája. Mivel a bejövő üzenetben az EAI-hidat az mindig a belső fejlesztésű számlát, állítsa ezt a beállítást **INHOUSEINVOICE**.
   
   ![][8]  
5. Kattintson a **Xml-átalakításhoz** alakzat, és a tulajdonság mezőben az a **Maps** tulajdonság, kattintson a három pont (**...** ) gombra. Az a **Maps kijelölés** párbeszédpanelen válassza ki a **INHOUSEINVOICE_to_D93AINVOIC** átalakíthatja fájlt, és kattintson a **OK**.
   
   ![][9]  
6. Lépjen vissza **MessageFlowItinerary.bcs**, és húzza a eszközkészletből a **kétirányú külső szolgáltatásvégpontot** jobb oldalán a **ProcessInvoiceBridge**. Állítsa be a **entitásnév** tulajdonságot **EDIBridge**.
7. A megoldáskezelőben bontsa ki a **MessageFlowItinerary.bcs** , és kattintson duplán a **EDIBridge.config** fájlt. Cserélje le a tartalmát a **EDIBridge.config** az alábbira.
   
   > [!NOTE]
   > Miért kell a .config fájl szerkesztésével? A külső szolgáltatásvégpontot, amely a híd tervezői vászonra felvettük a korábban üzembe helyezett EDI hidak jelöli. EDI-hidak kétirányú hidak, a Küldés és a fogadó oldali. Azonban az EAI-hidat, amely felvettük a híd-Tervező, egy egyirányú híd. Így különböző üzenet exchange mintázatait, a két hidak kezelése érdekében használjuk egy egyéni híd működés fel annak konfigurációját a .config fájlban. Emellett egyéni működését is az való bejelentkezések hitelesítését kezelik az EDI küldési híd végpont. Ez a viselkedés egyéni érhető el, külön mintaként [BizTalk Services híd minta – EDI, EAI-láncolás](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Ez a megoldás a mintát használja.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. A konfigurációs részleteivel EDIBridge.config fájl frissítése
   
   * A *<behaviors>*, adja meg az ACS-névtér és a BizTalk Services-előfizetéséhez társított kulcs.
   * A *<client>*, adja meg a végpontot, ahol a küldési EDI-szerződéshez üzemel.
   
   Mentse a módosításokat, és zárja be a konfigurációs fájlt.
9. Az eszköztáron kattintson a **összekötő** , és csatlakozzon a **ProcessInvoiceBridge** és **EDIBridge** összetevőket. Válassza ki az összekötőt, és a Tulajdonságok párbeszédpanelen állítsa be **szűrési feltételt** való **egyezés minden**. Ez biztosítja, hogy a EAI-hidat által feldolgozott összes üzenetet a EDI híd legyenek irányítva.
   
   ![][10]  
10. A megoldás módosításainak mentése.  

### <a name="deploy-the-project"></a>A projekt telepítése
1. A számítógépen, ahol létrehozta a BizTalk Services-projekt letöltése és a BizTalk Services-előfizetéséhez tartozó SSL-tanúsítvány telepítése. FROM, a BizTalk Services-csoportban **irányítópult**, és kattintson a **SSL-tanúsítvány letöltése**. Kattintson duplán a tanúsítványra, és kövesse a telepítés befejezéséhez a rendszer kéri. Ellenőrizze, hogy telepítette a tanúsítványt a **megbízható legfelső szintű hitelesítésszolgáltatók** tanúsítványtárolójába.
2. A Visual Studio Megoldáskezelőben kattintson a jobb gombbal a **InvoiceProcessingBridge** projektre, és kattintson a **telepítés**.
3. Az ábrán látható módon adja meg az értékeket, és kattintson a **telepítés**. Megtekintheti az ACS-hitelesítő adatok a BizTalk Services kattintva **kapcsolatadatok** a BizTalk Services irányítópultról.
   
   ![][11]  
   
   A kimeneti ablakban másolja a végpont, ahol a EAI-hidat üzemel, például `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Később szüksége lesz a végpont URL-címe.  

## <a name="step-4-test-the-solution"></a>4. lépés: A megoldás teszteléséhez.
Ebben a témakörben áttekintjük a megoldás tesztelése használatával a **oktatóanyag ügyfél** a minta részeként megadott alkalmazást.  

1. A Visual Studióban lenyomja az F5 billentyűt, indítsa el a **oktatóanyag ügyfél**.
2. A képernyő rendelkeznie kell a a lépéseket az előre megadott értékeket, a Service Bus-üzenetsorok létrehoztunk. Kattintson a **Tovább** gombra.
3. A következő ablakban adja meg az ACS a BizTalk Services-előfizetés hitelesítő adatait, és a végpontok ahol EAI és EDI-t (fogadás) hidak vannak telepítve.
   
   Az előző lépésben kimásolt kellett az EAI-hidat végpont. EDI kapnak híd végpont, a BizTalk Services portálon, nyissa meg a szerződést > fogadási beállítások > átviteli > végpont.
   
   ![][12]  
4. Kattintson a következő ablakban, a Contoso, a **házon belüli számla küldése** gombra. A fájl párbeszédpanel megnyitásához, nyissa meg a INHOUSEINVOICE.txt fájlt. Vizsgálja meg a fájl tartalmát, és kattintson a **OK** a számla küldése.
   
   ![][13]  
5. Néhány másodpercen belül Northwind megkapta a számlán. Kattintson a **Zobrazit Zprávu** hivatkozásra kattintva megtekintheti a számla Northwind által fogadott. Figyelje meg, hogyan a Northwind által kapott számlán szerepel standard EDIFACT-séma egy belső fejlesztésű séma közben a Contoso által küldött.
   
   ![][14]  
6. Válassza ki a számlát, és kattintson a **küldése nyugtázási**. A felugró párbeszédpanel láthatja, hogy az adatcsere-azonosító ugyanazt a kapott számlán és elismervényt küld a rendszer. Kattintson az OK gombra a **küldése nyugtázási** párbeszédpanel bezárásához.
   
   ![][15]  
7. A nyugtázási pár másodpercen belül sikeresen kézbesítve Contoso.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>(Nem kötelező) 5. lépés: kötegekben küldése EDIFACT számlák
A BizTalk Services EDI hidak is támogatja a kimenő üzenetek kötegelése. Ez a funkció akkor hasznos, partnerek, amelyek jobban szeretik az üzenetkötegek (bizonyos feltételeknek megfelelő) helyett az egyes üzenetek fogadására.

A legfontosabb szempont az kötegek használatakor a tényleges kiadása a batch, a kiadási feltételekben néven is ismert. A kiadási feltételekben alapulhat hogyan a fogadó partner üzeneteket kapni szeretne. Kötegelés engedélyezett, ha az EDI-híd nem küld a kimenő üzenetet, a fogadó partner a kiadási feltételekben teljesítéséig. Például a kötegelés feltételek alapján üzenet mérete kiszállítására kötegelt csak akkor, ha n üzenetek kötegelt vannak. A batch feltételeket is időalapú, úgy, hogy a batch minden nap rögzített egyszerre zajlik. Ebben a megoldásban megpróbáljuk a üzenetméret alapú feltételeknek.

1. A BizTalk Services Portáljára kattintson a korábban létrehozott szerződést. Kattintson a Küldés beállítások > kötegelés > kötegelt hozzáadása.
2. A batch neveként adja meg **InvoiceBatch**, és adjon meg egy leírást, majd kattintson a **tovább**.
3. Adjon meg egy kötegelt feltételeket, amely meghatározza, mely üzeneteket mely kell kötegelni. Ebben a megoldásban azt a köteg összes üzenet. Tehát, válassza ki a speciális definíciók beállítás használatát, és adja meg **1 = 1**. Ez egy feltételt, amely mindig igaz lesz, és ezért minden üzenetet fog kötegelni. Kattintson a **Tovább** gombra.
   
   ![][17]  
4. Adjon meg egy kötegelt kiadási feltételek. A legördülő listából válassza ki a **MessageCountBased**, és a **száma**, adja meg **3**. Ez azt jelenti, hogy három üzenetkötegek Northwind küld. Kattintson a **Tovább** gombra.
   
   ![][18]  
5. Tekintse át az összefoglalást, és kattintson a **mentése**. Kattintson a **telepítés** újbóli üzembe helyezéséhez a szerződést.
6. Lépjen vissza a **oktatóanyag ügyfél**, kattintson a **házon belüli számla küldése**, és kövesse az utasításokat a számla küldése. Megfigyelheti, hogy számla nem kapott a Northwind, mert a Köteg mérete nem teljesül. Ismételje meg ezt még két alkalommal, hogy három számla üzeneteket küldeni a Northwind rendelkezik. Ez megfelel a kötegelt kiadási feltételek, 3 üzenet, és meg kell jelennie a Northwind számlát.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

