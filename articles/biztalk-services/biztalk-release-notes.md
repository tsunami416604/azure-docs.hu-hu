---
title: Az Azure BizTalk Services kibocsátási megjegyzései |} A Microsoft Docs
description: Az ismert hibákat tartalmazza az Azure BizTalk Services
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 10e790c2edb22b3c7926216535d76c50261589f3
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260333"
---
# <a name="release-notes-for-azure-biztalk-services"></a>Az Azure BizTalk Services – kibocsátási megjegyzések

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

A Microsoft Azure BizTalk Services – kibocsátási megjegyzések az ebben a kiadásban az ismert hibákat tartalmaznak.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>A BizTalk Services novemberi frissítés újdonságai
* Titkosítás inaktív állapotban is engedélyezhető a BizTalk Services Portáljára. Lásd: [titkosítás inaktív állapotban a BizTalk Services portáljának engedélyezése](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Frissítési előzmények
### <a name="october-update"></a>. Októberi frissítés
* A szervezeti fiókokat támogatja:  
  * **A forgatókönyv**: Egy BizTalk-szolgáltatás telepítését, Microsoft-fiókkal regisztrált (például user@live.com). Ebben a forgatókönyvben a Microsoft Account felhasználók csak a BizTalk Service a BizTalk Services portáljának használata kezelheti. Szervezeti fiók nem használható.  
  * **A forgatókönyv**: BizTalk-szolgáltatások üzembe helyezéséhez egy Azure Active Directory szervezeti fiókkal regisztrált (például user@fabrikam.com vagy user@contoso.com). Ebben a forgatókönyvben a csak az Azure Active Directory-felhasználók a szervezeten belül a BizTalk Service a BizTalk Services portáljának használata kezelheti. Microsoft-fiók nem használható.  
* BizTalk-szolgáltatás létrehozása, amikor Ön automatikusan regisztrálva van a BizTalk Services portálon.
  * **A forgatókönyv**: Az Azure-ba, BizTalk-szolgáltatás létrehozása, és válassza ki **kezelés** Ez az első alkalommal. Amikor megnyílik a BizTalk Services portáljára, a BizTalk Service automatikusan regisztrálja, és készen áll a központi telepítések a.  
    Lásd: [Regisztrálásán, illetve frissítésén BizTalk-szolgáltatások üzembe helyezéséhez a BizTalk Services portál](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Augusztus 14-frissítés
* Szerződés és a híd elválasztás – kereskedelmi partneri szerződéseket és hidak most leválasztott a BizTalk Services Portáljára. Most már szerződések és a hidak külön létrehozott, és futásidőben hidak feloldani egy szerződést az EDI-üzenetben értékei alapján. Lásd: [szerződések létrehozása az Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689908.aspx), [hozzon létre egy BizTalk Services portáljának használata EDI híd](https://msdn.microsoft.com/library/azure/dn793986.aspx), [hozzon létre egy AS2-hidat, BizTalk Services portáljának használata](https://msdn.microsoft.com/library/azure/dn793993.aspx), és [ Hogyan megoldja hidak futásidőben szerződéseket?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* A szerződések sablonok létrehozásának lehetőségét megszakad.  
* A küldési-oldalon szerződés mostantól megadhatja az egyes séma csoportjainak különböző elválasztó karakter. Ez a konfiguráció a küldési oldalon szerződés protokoll beállításaiban van megadva. További információkért lásd: [egy X12 létrehozása az Azure BizTalk Services szerződés](https://msdn.microsoft.com/library/azure/hh689847.aspx) és [az EDIFACT-egyezmény létrehozása az Azure BizTalk Services](https://msdn.microsoft.com/library/azure/dn606267.aspx). Két új entitásokat is bekerülnek a TPM OM API ugyanazt a célt. Lásd: [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) és [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Standard XSD szerkezeteket, beleértve a származtatott típusok, mostantól támogatottak. Lásd: [használjon standard XSD hoz létre, a maps-](https://msdn.microsoft.com/library/azure/dn793987.aspx) és [használata származtatott típusok Leképezési forgatókönyvek és példák a](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 új MIC üzenet az aláíráshoz és új titkosítási algoritmusok támogatja. Lásd: [egy AS2-egyezmény létrehozása az Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Ismert problémák

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Kapcsolódási problémák, miután a BizTalk Services portál frissítése
  Ha a BizTalk Services portáljának megnyitásához, amíg a BizTalk Services szeretné visszaállítani a módosításokat a szolgáltatás frissítése, között előfordulhat, hogy kapcsolati problémák a BizTalk Services Portáljára.  
  Áthidaló megoldásként, előfordulhat, hogy indítsa újra a böngészőt, törölje a böngésző gyorsítótárát, vagy privát módban indítsa el a portálon.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE nem található az összetevő, kattintson egy hiba vagy figyelmeztetés a BizTalk Services-projektben
Telepítse a Visual Studio 2012 Update 3 RC 1 a probléma megoldásához.  

### <a name="custom-binding-project-reference"></a>Egyéni kötés projekt leírása
Vegye figyelembe a BizTalk Services a projekt egy Visual Studio-megoldás a következő helyzetekben:  

* Ugyanabban a Visual Studio-megoldásban nincs a BizTalk Services-projekt és a egy egyéni kötés projekt. A BizTalk Service-projektet az egyéni kötés soubor projektu vonatkozó hivatkozás van.
* A BizTalk Service-projektet egy egyéni kötés/működés DLL vonatkozó hivatkozás van.

"Fejleszt" a megoldást a Visual Studióban sikeresen megtörtént. Ezután "Újraépítése" vagy "Tiszta" a megoldás. Ezt követően építse újra, vagy újra, tisztítsa meg a következő hiba jelenik meg:  
  Nem sikerült a Másolás <Path to DLL> a "bin\Debug\FileName.dll". A folyamat nem tud hozzáférni a "bin\Debug\FileName.dll" fájlt, mert egy másik folyamat használja.  

#### <a name="workaround"></a>Áthidaló megoldás
* Ha [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) van telepítve, a következő két lehetősége van:
  
  * Indítsa újra a Visual Studióban, vagy
  * Indítsa újra a megoldást. Csak egy Build, majd hajtsa végre a megoldást.  
* Ha [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) nem telepített, nyissa meg a Feladatkezelőben kattintson a folyamatok lapon, kattintson a MSBuild.exe folyamat, és kattintson a folyamat leállítása gombra.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>BasicHttpRelay végpontok az Útválasztás nem támogatott a hidak és a BizTalk Services portáljának nem nyomtatható karakterek is támogatni a HTTP-fejléceket, ha
Használatakor nem nyomtatható karakterek Előléptetett tulajdonságok részeként üzenetek ezeket az üzeneteket nem irányítható a BasicHttpRelay kötést használó relay célhelyre. Előléptetett tulajdonságait, amely is elérhető részeként nyomkövetési URL-kódolású blobok és a célhelyek nem kódolt vannak.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN aszinkron módon van elküldve, még akkor is, ha a Küldés aszinkron MDN-beállítás nincs bejelölve
Ebben a forgatókönyvben – vegye figyelembe, ha a **aszinkron MDN küldésének cél** jelölőnégyzetet, és adjon meg egy URL-címet az aszinkron MDN küldése, és ezután törölje a jelet a **aszinkron MDN küldésének cél** jelölőnégyzet újra, az MDN továbbra is küld a annak ellenére, hogy az aszinkron visszaigazolással küldött beállítás nincs bejelölve a megadott URL-CÍMÉT.  
Áthidaló megoldásként törölnie kell a megadott URL-cím jelölésének törlése előtt az **aszinkron MDN küldésének cél** jelölőnégyzetet, illetve telepítheti az AS2-megállapodás.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Az elválasztó karakterek nem egy érvényes adatcsere okozhat, üres üzenetet kell küldeni a suspend-végpont
Ha nem állhat kizárólag szóközökből túl egy IEA szegmenst, a kicsomagoló ezt tekinti az aktuális adatcsere végén, és megvizsgálja a következő set nem állhat kizárólag szóközökből következő üzenetnek számít. Mivel ez a lehetőség nem érvényes adatcsere, előfordulhat, hogy figyelje meg, hogy egy sikeres üzenetet küld az útvonal cél és a egy üres üzenetet küld a suspend-végpont.  

### <a name="tracking-in-biztalk-services-portal"></a>A BizTalk Services portáljának nyomon követése
Akár az EDI-üzenetfeldolgozást, és bármely korrelációs rögzítve lesznek a követési események. Ha egy üzenetet a protokoll szakaszban kívül nem sikerül, nyomkövetési sikeres jelennek meg. Ebben a helyzetben, tekintse meg a szakasz alatt a **részletek** oszlopa **követési** a hiba részletei.
A X12 beállítások küldhet és fogadhat ([hozzon létre egy X12 szerződés az Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx)) a protokoll szakasz információkat tartalmaz.  

### <a name="update-agreement"></a>Szerződés frissítése
A BizTalk Services portáljának lehetővé teszi a identitás minősítője módosíthatja, amikor egy van konfigurálva. Ez az inkonzisztens tulajdonságok eredményezheti. Például nincs ZZ:1234567 és ZZ:7654321 a minősítő egy szerződést. A BizTalk Services portáljának-profiljának beállításainál ZZ:1234567 01:ChangedValue kell módosítania. Megnyitja a szerződést, és 01:ChangedValue ZZ:1234567 helyett jelenik meg.
A minősítő identitás módosítása, a szerződés törlése, frissíteni **identitások** a partner profilba, majd hozza létre újra a szerződést.  

> AZURE-T. Ez a viselkedés figyelmeztetés hatással van a X12 és az AS2.  
> 
> 

### <a name="as2-attachments"></a>AS2-mellékletek
Az AS2-üzenetek nem támogatottak a mellékletek küldeni vagy fogadni. Pontosabban a mellékletek csendes figyelmen kívül hagyja, és mint egy normál AS2-üzenet feldolgozása az üzenet törzse.  

### <a name="resources-remembering-path"></a>Erőforrások: Elérési útját megjegyezné
Hozzáadásakor **erőforrások**, a párbeszédablak előfordulhat, hogy ne felejtse el az erőforrás hozzáadása a korábban használt elérési út. Ne feledje az előzőleg használt elérési utat, próbáljon meg a BizTalk Services portáljának webhely **megbízható helyek** az Internet Explorerben.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Nevezze át az entitás egy híd, és a projekt bezárása a módosítások mentése nélkül, ha az entitás újra megnyitni a hibát eredményez
Példaként vegyünk egy forgatókönyvet, a következő sorrendben:  

* BizTalk Service-projektet ad hozzá a híd (például egy XML-One-Way-híd)  
* Nevezze át a híd az entitás neve tulajdonsághoz megadott érték megadásával. Ez a a megadott nevű társított .bridgeconfig fájlt nevez át.  
* Zárja be a .bcs fájlt (a Visual Studióban a lapon bezárásával) a módosítások mentése nélkül.  
* Nyissa meg a .bcs fájlt újra a Megoldáskezelőben.  
  Láthatja majd, míg a társított .bridgeconfig fájl a megadott új néven, az entitás neve a tervezőfelületre, továbbra is a régi nevére. Ha megpróbálja megnyitni a híd konfigurációjához ehhez kattintson duplán a híd összetevő, a következő hibaüzenetet kap:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` Ebben a forgatókönyvben való futtatásának elkerüléséhez ellenőrizze a BizTalk Service-projektet az entitások átnevezése után a módosítások mentéséhez.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>BizTalk-szolgáltatás projekt sikeresen létrejön, még akkor is, ha egy összetevő ki van zárva a Visual Studio-projekt
Példaként vegyünk egy forgatókönyvet, ahol hozzáadhat egy összetevő (például XSD-fájl) BizTalk-szolgáltatás-projektbe, adott összetevő bevonni a híd konfigurációjához (például megadásával, a kérelem üzenet típusa) és kizárása a Visual Studio-projekt. Ebben az esetben elkészítése nem biztosít semmilyen hiba mindaddig, amíg a törölt összetevő érhető el a lemezen, ahol a Visual Studio-projekt méretéből ugyanazon a helyen.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>A BizTalk-szolgáltatás projekt nem ellenőrzi a séma rendelkezésre állás érdekében a hidak konfigurálása során
A BizTalk Service-projektet Ha egy sémát, amely adnak hozzá a projekthez egy másik sémát, importálja a BizTalk-szolgáltatás projekt nem ellenőrzi-e az importált séma bekerül a projekt. Próbál az ilyen a projekt buildjének elkészítéséhez, ha bármely fordítási hibákat nem kap.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>A válaszüzenet egy XML-kérés-válasz híd mindig az UTF-8 karakterkészlet
Ebben a kiadásban a válaszüzenet egy XML-kérés-válasz hídról karakterkódolás mindig értéke UTF-8.
  
### <a name="user-defined-datatypes"></a>Felhasználó által definiált adattípusok
A BizTalk Adapter Pack-adapterek belül a BizTalk Adapter Service szolgáltatás képes használni a felhasználó által definiált adattípusok adapter műveletekhez.
Felhasználó által definiált adattípusok használatakor a meghajtó: \Program Files\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\ vagy, a globális szerelvény gyorsítótárban (GAC) a BizTalk Adapter Service szolgáltatást futtató kiszolgálón másolja a fájlok (.dll). Ellenkező esetben a következő hiba fordulhat elő, az ügyfélen:  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> Javasoljuk, hogy GACUtil.exe használatával telepíti, a globális szerelvény-gyorsítótárba. Ez az eszköz és a Visual Studio parancssori kapcsolók használata GACUtil.exe-dokumentumok.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>A BizTalk Adapter Service webhely újraindítása
Telepíti a **BizTalk Adapter Service futtatókörnyezeti*** hoz létre a **BizTalk Adapter Service** webhelyet az IIS-ben, amely tartalmazza a **BAService** alkalmazás. **BAService** alkalmazás belső használ továbbítási kötés kielégítése, a helyszíni service-végpont a felhőbe. Egy üzemeltetett szolgáltatás a helyszíni a megfelelő relay-végpontot fog regisztrálható a Service Bus csak akkor, amikor a helyszíni szolgáltatás elindul.  

Állítsa le és indítsa el az alkalmazást, ha egy alkalmazás automatikus indítása a konfiguráció nem figyelembe véve. Így amikor **BAService** van leállítva, hogy mindig újra kell indítani a **BizTalk Adapter Service** webhely helyette. Nem indítása és leállítása a **BAService** alkalmazás.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Különleges karakterek nem használható a LOB-összetevők címet és az entitás neve
Ne használjon speciális karakterek a LOB-összetevők címet és az entitás nevét. Ha így tesz, a BizTalk-szolgáltatás projekt üzembe helyezése során megjelenik egy hibaüzenet. Az egyes karaktereket, például: "%", a BizTalk Adapter Service webhely előfordulhat, hogy lépjen egy leállított állapotba kerül, és indítsa el manuálisan kell.

### <a name="test-map-with-get-context-property"></a>Vizsgálati térkép a Get-környezeti tulajdonsága
Ha egy-egy átalakítási tartalmaz egy **környezeti tulajdonság lekérése** térkép művelet, **vizsgálati térkép** sikertelen lesz. Ideiglenes Áthidaló megoldásként cserélje le a **környezeti tulajdonság lekérése** térkép műveletet egy karakterlánc összefűzésére térkép művelettel tartalmazó adattartalom. Ezzel a célséma feltöltése és engedélyezése az egyéb átalakító funkciók tesztelése.

### <a name="test-map-property-does-not-display"></a>Vizsgálati térkép tulajdonság nem jelenik meg
A **vizsgálati térkép** tulajdonságai nem jelennek meg a Visual Studióban. Ez akkor fordulhat elő, ha a **tulajdonságok** ablakot, és a **Megoldáskezelőben** ablak nem egyszerre dokkolt vezérlőelem. Ennek megoldásához dock a **tulajdonságok** és a **Megoldáskezelőben** windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Dátum és idő újraformázása legördülő szürkén jelenik meg
Amikor egy dátum és idő újraformázása térkép művelet hozzáadva a Tervező felületére és konfigurálva van, a formátuma legördülő lista – előfordulhat, hogy szürkén jelennek meg. Ez akkor fordulhat elő, ha a számítógép megjelenítési **Közepes – 125 %** vagy **nagyobb – 150 %**. Oldja meg, állítsa a megjelenített **Smaller – 100 %-os (alapértelmezett)** az alábbi lépéseket követve:  

1. Nyissa meg a **Vezérlőpult** kattintson **Megjelenés és a személyre szabott**.
2. Kattintson a **megjelenített**.
3. Kattintson a **Smaller – 100 %-os (alapértelmezett)** kattintson **alkalmaz**.

A **formátum** legördülő lista – most már a várt módon működik a kell.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>A BizTalk Services Portáljára a duplikált szerződések
Vegye figyelembe az alábbi forgatókönyvet:

1. Hozzon létre egy szerződést a Trading Partner Management OM API segítségével.
2. Nyissa meg a megállapodás a két különböző lapokon a BizTalk Services Portáljára.
3. Helyezze üzembe a megállapodás a két lap.
4. Ennek eredményeképpen a szerződéseket is üzembe lesznek helyezve a BizTalk Services portáljának ismétlődő bejegyzést eredményez

**Megkerülő megoldás**. Nyissa meg az ismétlődő szerződések közül bármelyik a BizTalk Services Portáljára, és visszavonhatja annak üzembe helyezését.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Hidak ne használja a frissített tanúsítvány tanúsítvány frissült az összetevő tároló után is
Vegye figyelembe a következő esetekben:  

**1. forgatókönyv: Ujjlenyomat-alapú tanúsítványok használatával biztonságossá tételéhez egy végpontot egy hídról üzenetküldés**  
Példaként vegyünk egy forgatókönyvet, ahol ujjlenyomat-alapú tanúsítványokat használ a BizTalk-szolgáltatás-projektben. A BizTalk Services Portáljára a tanúsítvány frissítése ugyanazzal a névvel, de egy másik ujjlenyomatot, de ennek megfelelően nem frissítik a BizTalk Service-projektet. Ilyen esetben a híd előfordulhat, hogy továbbra is az üzeneteket feldolgozni, mert a régebbi adatok továbbra is lehet a csatorna gyorsítótárban. Ezt követően a következő üzenet feldolgozása sikertelen lesz.  

**Megkerülő megoldás**: A BizTalk Service-projektet a tanúsítvány frissítése és ismételt üzembe helyezése a projekt.  

**2. forgatókönyv: -Névalapú viselkedések használatával azonosíthatja a tanúsítványok védelmét biztosító üzenet átvitelét egy híd egy végpontot**

Példaként vegyünk egy forgatókönyvet, ahol-névalapú viselkedések azonosítására használt tanúsítványok a BizTalk-szolgáltatás-projektben. A BizTalk Services Portáljára a tanúsítvány frissítése, de ennek megfelelően nem frissítik a BizTalk Service-projektet. Ilyen esetben a híd előfordulhat, hogy továbbra is az üzeneteket feldolgozni, mert a régebbi adatok továbbra is lehet a csatorna gyorsítótárban. Ezt követően a következő üzenet feldolgozása sikertelen lesz.  

**Megkerülő megoldás**: A BizTalk Service-projektet a tanúsítvány frissítése és ismételt üzembe helyezése a projekt.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Hidak folytatja az üzenetek feldolgozásához, még akkor is, ha az SQL-adatbázis offline állapotban
A BizTalk Services hidak továbbra is üzenetek feldolgozása egy ideig, még akkor is, ha a Microsoft Azure SQL Database (amely tárolja a futó további információkat, például a telepített összetevőket és folyamatokat), offline állapotban van. Ennek az az oka a BizTalk Services használja a gyorsítótárazott összetevők és a híd konfigurációjához.
Ha nem szeretné, hogy a hidak olyan üzeneteket feldolgoznia, ha az SQL-adatbázis offline állapotban, a BizTalk Services PowerShell-parancsmagok segítségével vagy felfüggesztheti a BizTalk-szolgáltatás. Lásd: [Azure BizTalk Service Management példa](https://go.microsoft.com/fwlink/p/?LinkID=329019) műveleteinek kezelésére szolgáló Windows PowerShell-parancsmagok számára.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Egy extra Anyagjegyzék-karaktert tartalmaz belüli egy híd egyéni kód XML-üzenet olvasása
Példaként vegyünk egy forgatókönyvet, ahol szeretné olvasni a híd egyéni kód egy XML-üzenet. Ha a .NET API System.Text.Encoding.UTF8.GetString(bytes) egy extra AJ karakter a kimenet az üzenet elején szerepel. Ezért, ha nem szeretné, hogy a kimenet tartalmazza az extra AJ karaktert, kell használnia ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Üzenetek küldése egy a WCF-fel híd nem méretezhető
A WCF-fel hidat küldött üzenetek nem méretezhető. Ha azt szeretné, hogy egy méretezhető ügyfél HttpWebRequest Ehelyett használjon.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>FRISSÍTÉS: Jogkivonat-szolgáltató hibát az általánosan elérhető (GA) a BizTalk Services előzetes verzióról
Nincs az EDI- vagy AS2-egyezményt aktív kötegek. Ha a BizTalk-szolgáltatás előzetes verzióról a végleges verzió frissítve van, a következő fordulhat elő:

* Hiba: A jogkivonat-szolgáltató nem tudta meg egy biztonsági jogkivonatot. Jogkivonat-szolgáltató következő üzenetet adta vissza: A távoli név nem oldható fel.
* Batch-feladatok törölve lesznek.

**Megkerülő megoldás**: A BizTalk-szolgáltatás frissítése az általános elérhető (GA) után telepítse újra a szerződést.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>FRISSÍTÉS: Eszközkészlet megjeleníti a régi híd ikonok a BizTalk Services SDK frissítése után
Amikor frissít egy korábbi verzióját a BizTalk Services SDK, amelynek régi ikonjai a hidak volt, az eszközkészlet továbbra is a régi ikonokat találhat a hidak. Azonban BizTalk-szolgáltatás projekt tervezőfelületére hidat hozzá, ha a surface bemutatja az új ikonra.  

**Megkerülő megoldás**. A probléma megoldásához használhatja az .tbd fájlokat alatt <system drive>: \Users\<felhasználó > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>FRISSÍTÉS: BizTalk-portál frissítése előzetes verzióról a végleges verzió előfordulhat, hogy megjelenítése egy hibaüzenet, hogy a EDI funkció nem érhető el
Ha van bejelentkezve a BizTalk Services Portáljára, amíg a BizTalk Services általánosan frissítése az előzetes verzió, kaphat a következő hibát a portálon:  

Ez a funkció nem érhető el a Microsoft Azure BizTalk Services jelen kiadásában részeként. Használja ezeket a képességeket váltson megfelelő kiadás.  

**Megoldás**: Jelentkezzen ki a portálról, zárja be, és nyissa meg a böngészőt, majd jelentkezzen be a portálra.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>FRISSÍTÉS: Új címkövetési adatokat nem jelenik meg a BizTalk Services általánosan frissítése után
Tegyük fel, a forgatókönyv, amelyben egy XML-hidat, a BizTalk Services előzetes előfizetésben üzembe helyezett. Üzeneteket küld a híd, és a kapcsolódó követési adatokat érhető el a BizTalk Services Portáljára. Mostantól a BizTalk Services Portáljára, és a BizTalk Services modul a bits általánosan elérhető verzióra frissíti, és a egy üzenet küldéséhez az azonos híd végpont korábban telepített, a nyomon követési adatok nem jelenik a frissítés után küldött üzeneteket.  

### <a name="pipelines-versus-bridges"></a>Folyamatok és a hidak
Ebben a dokumentumban a kifejezés "folyamatok" és "hidak" kifejezés ugyanazt jelenti. Mindkét lényegében amely, a BizTalk Services telepített üzenet feldolgozóegység ugyanazt jelenti.  

### <a name="concepts"></a>Alapelvek
[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

