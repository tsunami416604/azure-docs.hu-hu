---
title: 'Oktatóanyag: Azure BizTalk szolgáltatásokkal EDIFACT számlák feldolgozásához |} Microsoft Docs'
description: Létrehozása és konfigurálja az mezőben összekötő vagy az API-alkalmazást, és használhatja a logikai alkalmazást az Azure App Service
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
ms.openlocfilehash: 2ebd6a8cb70f218c3b56bc78c9b853dbf51ab468
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26633866"
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Oktatóanyag: Folyamat EDIFACT számlák Azure BizTalk szolgáltatás használata

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

A BizTalk szolgáltatások portál segítségével konfigurálhatja és telepítheti a X12 és EDIFACT-egyezmények. Ebben az oktatóanyagban úgy tekintünk létrehozása az üzleti partnerek között számlák cseréjét EDIFACT szerződést is. Ez az oktatóanyag egy végpont megoldás használata esetén két kereskedelmi partnereknek, EDIFACT üzeneteket Northwind és Contoso körül írása.  

## <a name="sample-based-on-this-tutorial"></a>Ez az oktatóanyag alapján minta
Ez az oktatóanyag egy minta körül írása **küldése EDIFACT számlák BizTalk szolgáltatások használatával**, elérhető töltheti le a [MSDN Kódgalériából](http://go.microsoft.com/fwlink/?LinkId=401005). Nem sikerült a mintát használja, és az oktatóanyag lépéseinek megértése, hogyan készült el a mintát. Vagy használhat ez az oktatóanyag a saját megoldás ground up létrehozásához. Ez az oktatóanyag a második megközelítés cél, hogy megismerte a hogyan készült el ebben a megoldásban. Is amennyire csak lehet, az oktatóanyag konzisztensek legyenek a minta és az összetevők (például sémákat, átalakítások) azonos nevét használja, mint a mintában használt.  

> [!NOTE]
> Mivel ezen megoldás keretein egy EAI-Összekötők híd üzenetet küld egy EDI híd, akkor a rendszer újból felhasználja a [BizTalk szolgáltatások híd minta láncolás](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) minta.  
> 
> 

## <a name="what-does-the-solution-do"></a>Mit jelent a megoldást?
Ebben a megoldásban a Northwind EDIFACT számlák megkapja a Contoso. A következő számlák nincsenek szabványos EDI-formátumú. Ezért mielőtt elküldi a számla Northwind, azt kell alakul EDIFACT (más néven INVOIC) számla dokumentumhoz. Fogadását, a Northwind kell feldolgozni a EDIFACT számla, és térjen vissza a vezérlő üzenet (más néven CONTRL) Contoso.

![][1]  

Az üzleti terület eléréséhez a Contoso használ a Microsoft Azure BizTalk szolgáltatások által nyújtott szolgáltatásokat.

* A Contoso EAI-Összekötők hidak átalakítására használja az eredeti számlát EDIFACT INVOIC használ.
* A EAI-Összekötők híd majd a üzenetet küld egy EDI küldési híd konfigurálva a BizTalk szolgáltatások portálon szerződés részeként.
* A EDI-küldési híd dolgozza fel a EDIFACT INVOIC, és továbbítja a Northwind.
* A számla beérkezése után Northwind visszaadja egy CONTRL üzenet a EDI kapják meg a szerződés részeként híd.  

> [!NOTE]
> Szükség esetén ez a megoldás is bemutatja, hogyan használja a kötegelés kötegekben, minden egyes számla külön küldése helyett a számlák küldhet.  
> 
> 

A forgatókönyv végrehajtásához a Service Bus-üzenetsorok a Contoso számla Northwind küldeni / nyugtázási fogadását Northwind használjuk. Ezek a várólisták hozhatók létre egy ügyfélalkalmazást, amely letölthető, és a rendelkezésre álló minta csomagban található ebben az oktatóanyagban részeként.  

## <a name="prerequisites"></a>Előfeltételek
* A Service Bus-névtér kell rendelkeznie. Egy névtér létrehozása vonatkozó utasításokért lásd: [Útmutató: létrehozhat vagy módosíthat egy Service Bus szolgáltatás Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx). Tételezzük fel, hogy már rendelkezik kiépített, Service Bus-névtér neve **edifactbts**.
* A BizTalk szolgáltatások előfizetéssel kell rendelkeznie. Ebben az oktatóanyagban Tételezzük nevű BizTalk szolgáltatások előfizetéssel rendelkezik **contosowabs**.
* BizTalk szolgáltatások előfizetését a BizTalk szolgáltatások portál regisztrálni. Útmutatásért lásd: [egy BizTalk szolgáltatás telepítését a BizTalk szolgáltatások portál regisztrálása](https://msdn.microsoft.com/library/hh689837.aspx)
* Visual Studio telepítve kell rendelkeznie.
* BizTalk szolgáltatások SDK telepítve kell rendelkeznie. Letöltheti az SDK [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>1. lépés: A Service Bus-üzenetsorok létrehozása
A megoldás az Service Bus-üzenetsorok üzenetek üzleti partnerek között. A Contoso és a Northwind üzenetek küldése a várólistákat a, ahol a EAI-Összekötők és/vagy EDI hidak felhasználni azokat. Ez a megoldás három Service Bus-üzenetsorok szükséges:

* **northwindreceive** – Northwind fogad a számla Contoso keresztül ebből a várólistából.
* **contosoreceive** – Contoso fogad a nyugtázási Northwind keresztül ebből a várólistából.
* **Felfüggesztve** – az összes felfüggesztett üzenet a várólistában legyenek átirányítva. Üzenetek fel vannak függesztve, ha azt elmulasztják feldolgozása során.

A Service Bus-üzenetsorok hozhat létre egy ügyfélalkalmazást, a minta csomag használatával.  

1. Nyissa meg a helyről, amelybe letöltötte a minta **oktatóanyag küldése számlák használatával BizTalk szolgáltatások EDI Bridges.sln**.
2. Nyomja le az **F5** építsenek, és indítsa el a **oktatóanyag ügyfél** alkalmazás.
3. A képernyőn adja meg a Service Bus ACS névteret, a kibocsátó neve és a kibocsátó kulcsa.
   
   ![][2]  
4. Egy üzenetablak megadását kéri, hogy a Service Bus-névtér három várólisták létrejön. Kattintson az **OK** gombra.
5. Hagyja meg az oktatóanyag futtató ügyfélen. Nyissa meg a lehetőségre **Service Bus** > ***a Service Bus-névtér*** > **várólisták**, és győződjön meg arról, hogy a három várólisták jöttek létre.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>2. lépés: Hozzon létre, és kereskedelmipartner-egyezmény telepítése
A Contoso és a Northwind közötti kereskedelmipartner-egyezmény létrehozása. Kereskedelmipartner-egyezmény definiál egy kereskedelmi szerződés között a két üzleti partnerek, például milyen üzenet használandó sémát, melyik üzenetküldési protokoll stb. Kereskedelmipartner-egyezmény tartalmaz egy kereskedelmi partnerek üzeneteket küldhet két EDI hidak (hívása a **EDI küldése híd**) és egy kereskedelmi partnerek üzenetek fogadása (hívása a **EDI-fogadási híd**).

Ebben a megoldásban a környezetében a EDI-küldési híd felel meg a szerződés küldési-oldalon, és a Contoso küldendő a EDIFACT számla Northwind használható. Ehhez hasonlóan a EDI fogadó hídon felel meg a szerződés fogadóoldali, és fogadási nyugtázása a Northwind szolgál.  

### <a name="create-the-trading-partners"></a>A kereskedelmi partnereknek létrehozása
Kereskedelmi partnereknek a Contoso és a Northwind kezdődnie, hozzon létre.  

1. A BizTalk szolgáltatások portálon a a **partnerek** lapra, majd **Hozzáadás**.
2. Adja meg az új partner lap **Contoso** lehetőséget egy partner neve, majd kattintson **mentése**.
3. Ismételje meg a második partner létrehozásához **Northwind**.  

### <a name="create-the-agreement"></a>A szerződés létrehozása
Kereskedelmipartner-egyezmények üzleti profilok kereskedelmi partnerek között jönnek létre. Ez a megoldás jönnek létre automatikusan a partnerek létrehozott alapértelmezett partner portprofilokat használ.  

1. BizTalk szolgáltatások portálon kattintson **megállapodások** > **Hozzáadás**.
2. Az új szerződés a **általános beállítások** lapon adja meg az értékeket, az alábbi ábrán látható módon, és kattintson a **Folytatás**.
   
   ![][3]  
   
   Miután rákattintott **Folytatás**, két lap található, **fogadási beállítások** és **küldési beállítások** elérhetővé válnak.
3. Hozzon létre a Contoso és a Northwind küldési megállapodást. Ez a szerződés szabályozza, hogyan Contoso küld a EDIFACT számla Northwind.
   
   1. Kattintson a **küldési beállításainak**.
   2. Tartsa meg az alapértelmezett értékeket a a **bejövő URL**, **átalakítási**, és **Batching** lapokon.
   3. Az a **protokoll** lap a **sémák** részen töltse fel a **EFACT_D93A_INVOIC.xsd** séma. Ebben a sémában a minta csomaggal érhető el.
      
      ![][4]  
   4. Az a **átviteli** lapra, adja meg a Service Bus-üzenetsorok adatait. A küldő-oldalon szerződés használjuk a **northwindreceive** várólista a EDIFACT számla küldendő Northwind, és a **felfüggesztve** várólista irányítja az üzenetek, sikertelen feldolgozása során, és fel vannak függesztve. Ezek a várólisták a létrehozott **1. lépés: a Service Bus-üzenetsorok létrehozása** (szakaszát).
      
      ![][5]  
      
      A **átviteli beállításai > átviteli típus** és **felfüggesztéséről az Üzenetbeállítások > átviteli típus**, válassza ki az Azure Service Bus, és adja meg az értékeket, az ábrán látható módon.
4. Hozzon létre a Contoso és a Northwind receive megállapodást. Ez a szerződés szabályozza, hogyan Contoso fogadja a nyugtázási Northwind.
   
   1. Kattintson a **beállítások**.
   2. Tartsa meg az alapértelmezett értékeket a a **átviteli** és **átalakítási** lapokon.
   3. Az a **protokoll** lap a **sémák** részen töltse fel a **EFACT_4.1_CONTRL.xsd** séma. Ebben a sémában a minta csomaggal érhető el.
   4. Az a **útvonal** lapra, létrehozhat egy szűrőt, győződjön meg arról, hogy csak a Northwind nyugták Contoso legyenek átirányítva. A **útvonal beállítások**, kattintson a **Hozzáadás** a útválasztási szűrő létrehozásához.
      
      ![][6]  
      
      1. Adjon meg értékeket a **szabálynév**, **útvonal szabály**, és **útvonal cél** az ábrán látható módon.
      2. Kattintson a **Save** (Mentés) gombra.
   5. Az a **útvonal** újra lapra, adja meg, ahol felfüggesztett nyugták (a nyugtázás a feldolgozás során eleget nem tevő) legyenek átirányítva. Állítsa be az átvitelt típusát Azure Service Bus, a céltípus irányításához **várólista**, hitelesítési típus **közös hozzáférésű Jogosultságkód** (SAS), adja meg a Service Bus-névtér az SAS-kapcsolati karakterlánc, és adja meg a várólista neve, ahogyan **felfüggesztve**.
5. Végezetül kattintson **telepítés** központi telepítése a szerződést. Vegye figyelembe a végpontok ahol a küldési és fogadási megállapodások telepítve.
   
   * Az a **küldési beállítások** lap **bejövő URL**, vegye figyelembe a végpont. Üzenet küldése a Contoso a EDI-küldési bridge segítségével Northwind, el kell küldenie egy üzenet ehhez a végponthoz.
   * Az a **fogadási beállítások** lap **átviteli**, vegye figyelembe a végpont. Üzenet küldése a Northwind Contoso a EDI használatával kap híd, el kell küldenie egy üzenet ehhez a végponthoz.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>3. lépés: Hozzon létre, és a BizTalk szolgáltatások projekt telepítése
Az előző lépésben a EDI küldési telepített, és feldolgozni EDIFACT számlákat és a nyugtázás a megállapodások fogadására. Ezek a szerződések csak megfeleljen a szabványos EDIFACT üzenet sémának folyamat üzeneteket is. Azonban az ebben a megoldásban a forgatókönyvben egy Contoso küld számla Northwind belső fejlesztésű jogvédett sémában. Igen az üzenet elküldése a EDI-küldési híd, azt kell alakul a belső fejlesztésű séma a szabványos EDIFACT számla sémának. A BizTalk EAI-szolgáltatások Projekt funkciója, amely.

BizTalk szolgáltatások projekt **InvoiceProcessingBridge**, hogy átalakítások az üzenet is része a letöltött minta részeként. A projekt a következő összetevőket tartalmazza:

* **INHOUSEINVOICE. XSD** – a belső fejlesztésű számla Northwind küldött séma.
* **EFACT_D93A_INVOIC. XSD** – szabványos EDIFACT számla séma.
* **EFACT_4.1_CONTRL. XSD** – a Northwind által a Contoso EDIFACT nyugtázási sémája.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – az átalakító, amely a belső fejlesztésű számla séma van leképezve a szabványos EDIFACT számla séma.  

### <a name="create-the-biztalk-services-project"></a>A BizTalk szolgáltatások projekt létrehozása
1. A Visual Studio-megoldásban, bontsa ki a InvoiceProcessingBridge projektet, és nyissa meg a **MessageFlowItinerary.bcs** fájlt.
2. Kattintson bárhová a vásznon, és állítsa be a **BizTalk szolgáltatás URL-címe** tulajdonság mezőben írja be a BizTalk szolgáltatások előfizetés nevét. Például: `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Az Eszközkészlet palettáról, húzzon egy **Xml One-Way híd** a vászonra. Állítsa be a **egyednév** és **relatív címet** a híd tulajdonságainak **ProcessInvoiceBridge**. Kattintson duplán a **ProcessInvoiceBridge** a híd konfigurációs felület megnyitásához.
4. Belül a **üzenettípusok** párbeszédpanelen kattintson a plusz (**+**) gombra kattintva adja meg a séma, a bejövő üzenet. Mivel a EAI-Összekötők híd a bejövő üzenet mindig a belső fejlesztésű számla, állítsa ezt a beállítást **INHOUSEINVOICE**.
   
   ![][8]  
5. Kattintson a **XML-átalakító** alakzat, és a tulajdonság mezőben az a **Maps** tulajdonság, kattintson a három pont (**...** ) gombra. Az a **Maps kijelölés** párbeszédpanelen jelölje ki a **INHOUSEINVOICE_to_D93AINVOIC** fájl átalakítása, és kattintson a **OK**.
   
   ![][9]  
6. Lépjen vissza a **MessageFlowItinerary.bcs**, és az Eszközkészlet palettáról, húzza a **kétirányú külső végpont** jobb oldalán a **ProcessInvoiceBridge**. Állítsa be a **egyednév** tulajdonságot **EDIBridge**.
7. A Megoldáskezelőben bontsa ki a **MessageFlowItinerary.bcs** , és kattintson duplán a **EDIBridge.config** fájlt. Tartalom felülírja a **EDIBridge.config** a következőre.
   
   > [!NOTE]
   > Miért szükséges a .config fájl szerkesztése? A külső végpontot, amelyhez a híd tervezői vászonra hozzáadott a korábbi helyezett EDI hidak jelöli. EDI hidak kétirányú hidak, a Küldés és a fogadási oldalon. A EAI-Összekötők bridge, amelyek a híd Designer hozzáadott azonban egy egyirányú híd. Igen a két hidak másik üzenet exchange mintáinak kezelésére, használjuk egy egyéni híd viselkedés konfigurációjában belefoglalja a .config fájl. Emellett egyéni működését is a EDI küldési híd végpont a hitelesítési kezeli. Ez a viselkedés egyéni áll rendelkezésre, mert egy külön mintát [BizTalk szolgáltatások híd minta - EAI-Összekötők a EDI-láncolás](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Ez a megoldás a rendszer újból felhasználja a minta.  
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
8. Frissítés a EDIBridge.config fájlt a konfiguráció részletei
   
   * A  *<behaviors>* , adja meg az ACS-névteret és a BizTalk szolgáltatás-előfizetéshez társított kulcs.
   * A  *<client>* , adja meg a végpont a EDI-küldési szerződés telepítési helyét.
   
   Mentse a módosításokat, és zárja be a konfigurációs fájlt.
9. Az Eszközkészlet palettáról, kattintson a **összekötő** , és csatlakozzon a **ProcessInvoiceBridge** és **EDIBridge** összetevőket. Válassza ki az összekötőt, és a Tulajdonságok párbeszédpanelen **szűrési feltételt** való **egyezés minden**. Ez biztosítja, hogy a EAI-Összekötők híd által feldolgozott összes üzenetet a EDI híd legyenek átirányítva.
   
   ![][10]  
10. Módosítások mentése a megoldáshoz.  

### <a name="deploy-the-project"></a>A projekt telepítése
1. A számítógép, amelyen létrehozta a BizTalk szolgáltatások projekt töltse le és telepítse az SSL-tanúsítványt BizTalk szolgáltatás előfizetését. A, a BizTalk szolgáltatások területen kattintson **irányítópult**, és kattintson a **SSL-tanúsítvány letöltése**. Kattintson duplán a tanúsítványra, és a telepítés befejezéséhez a felszólítást követve. Győződjön meg arról, hogy a tanúsítvány telepítése **megbízható legfelső szintű hitelesítésszolgáltatók** tanúsítványtárolójába.
2. A Visual Studio Solution Explorerben kattintson a jobb gombbal a **InvoiceProcessingBridge** projektre, és kattintson a **telepítés**.
3. Az ábrán látható módon adja meg az értékeket, és kattintson a **telepítés**. Kaphat az ACS-hitelesítő adatok BizTalk szolgáltatások kattintva **kapcsolatadatok** a BizTalk szolgáltatások irányítópulton.
   
   ![][11]  
   
   Másolja a végpont, ahol a EAI-Összekötők híd telepíti, például a kimeneti ablaktábla `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Később szüksége lesz a végponti URL-cím.  

## <a name="step-4-test-the-solution"></a>4. lépés: A megoldás tesztelése
Ebben a témakörben úgy tekintünk, a megoldás használatával tesztelése a **oktatóanyag ügyfél** alkalmazás a minta részeként.  

1. A Visual Studio, nyomja le az F5 elindítani a **oktatóanyag ügyfél**.
2. A képernyőn a különböző értéknek kell lennie az lépésben előre feltöltve ahol a Service Bus-üzenetsorok létrehozott. Kattintson a **Tovább** gombra.
3. A következő ablakban adja meg az ACS BizTalk szolgáltatások előfizetés hitelesítő adatait, és a végpontok ahol EAI- és EDI (kap) hidak vannak telepítve.
   
   Az előző lépésben másolt volna a EAI-Összekötők híd végpont. EDI küld híd a végponthoz, a BizTalk szolgáltatások portálon, írja be a szerződés > fogadási beállítások > átviteli > végpont.
   
   ![][12]  
4. A Contoso, a következő ablakban kattintson a **belső fejlesztésű számla küldése** gombra. A fájl párbeszédpanel megnyitásához, nyissa meg a INHOUSEINVOICE.txt fájlt. Vizsgálja meg a fájl tartalmát, és kattintson a **OK** a számla küldeni.
   
   ![][13]  
5. Néhány másodpercen belül a számla Northwind fogadja. Kattintson a **nézet üzenet** hivatkozásra kattintva megtekintheti a Northwind által kapott számlán. Figyelje meg, hogyan a Northwind által kapott számlán van szabványos EDIFACT séma közben Contoso által küldött olyan belső fejlesztésű séma.
   
   ![][14]  
6. Válassza ki a számla, és kattintson a **küldése nyugtázási**. Előugró párbeszédpanelen láthatja, hogy a csomópont-Azonosítót a kapott számlán és a küldött nyugtázási azonos. Kattintson az OK gombra a **küldése nyugtázási** párbeszédpanel megnyitásához.
   
   ![][15]  
7. Néhány másodpercen belül a nyugtázási sikeresen Contoso fogadja.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>(Választható) 5. lépés: küldése EDIFACT számla kötegek
BizTalk szolgáltatások EDI hidak is támogatja a kimenő üzenetek kötegelés. Ez a szolgáltatás akkor hasznos, inkább az üzenetkötegek (bizonyos feltételeknek megfelelő) helyett az egyes üzeneteket fogadhat-partnerek fogadására.

A legfontosabb szempont az kötegek használatakor nem a tényleges a köteg, más néven a kiadási feltételek. A kiadási feltételek is alapulhat, hogyan szeretne rendelni a fogadó partner üzeneteket fogadni. Ha kötegelés engedélyezve van, a EDI híd nem küld a kimenő üzenet fogadó partnernek mindaddig, amíg a kiadási feltétel teljesül. Például egy kötegelési feltételek alapján üzenet mérete kivételkezelési egy kötegben csak akkor, ha "n" üzenetek kötegelni vannak. A kötegelt feltételeket is időalapú, úgy, hogy az adott időpontban naponta egy kötegelt zajlik. Ebben a megoldásban a üzenetméret alapú feltételek próbálja azt.

1. BizTalk szolgáltatások portálon kattintson a korábban létrehozott. Kattintson a Küldés beállítások > kötegelés > kötegelt hozzáadása.
2. Kötegelt neveként, írja be a **InvoiceBatch**, adjon meg egy leírást, és kattintson a **következő**.
3. Adjon meg egy kötegelt feltételeket, amely meghatározza, hogy mely üzenetek kötegelni kell lennie. Ebben a megoldásban kötegelt azt minden üzenetet. Igen, jelölje be a speciális definíciók beállítás használata, és írja be **1 = 1**. Ez egy feltételt, amely mindig lesz igaz értékű, és ezért minden üzenetet fog lehet kötegelni. Kattintson a **Tovább** gombra.
   
   ![][17]  
4. Adjon meg egy kötegelt kiadási feltételeket. A legördülő listából válassza ki a **MessageCountBased**, és a **száma**, adja meg **3**. Ez azt jelenti, hogy az három üzenetkötegek Northwind kapnak. Kattintson a **Tovább** gombra.
   
   ![][18]  
5. Tekintse át az összefoglalást, és kattintson a **mentése**. Kattintson a **telepítés** újratelepíteni a szerződést.
6. Lépjen vissza a **oktatóanyag ügyfél**, kattintson a **belső fejlesztésű számla küldése**, és kövesse az utasításokat a számla küldése. Megfigyelheti, hogy számla nem érkezik a Northwind, mert a Köteg mérete nem teljesül. Ismételje meg ezt a lépést még kétszer, így kell három számla Northwind küldött üzeneteket. Ez megfelel, 3 üzenet kötegelt kiadási feltételeinek, és meg kell jelennie a Northwind számla.

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

