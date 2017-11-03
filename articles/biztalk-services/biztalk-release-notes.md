---
title: "Az Azure BizTalk szolgáltatások kibocsátási megjegyzései |} Microsoft Docs"
description: "Az ismert problémák listája Azure BizTalk szolgáltatások"
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 18ed891a9bba2b4011d3492722a2366d96fb3c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-azure-biztalk-services"></a>Kibocsátási megjegyzések a Azure BizTalk szolgáltatások

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

A kibocsátási megjegyzések a Microsoft Azure BizTalk szolgáltatások ebben a kiadásban az ismert problémákról tartalmaznak.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>BizTalk szolgáltatások November frissítés újdonságai
* A BizTalk szolgáltatások portálja titkosítását is engedélyezhető. Lásd: [engedélyezheti a titkosítást a BizTalk szolgáltatások portál aktívan](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Frissítési előzmények
### <a name="october-update"></a>. Októberi frissítés
* Munkahelyi és iskolai fiókok támogatottak:  
  * **A forgatókönyv**: regisztrálta a BizTalk szolgáltatás központi telepítése a Microsoft-fiókkal (például user@live.com). Ebben a forgatókönyvben csak Microsoft Account kezelése a BizTalk szolgáltatás a BizTalk szolgáltatások portál használatával. Egy szervezeti fiók nem használható.  
  * **A forgatókönyv**: regisztrálta a BizTalk szolgáltatás központi telepítése egy Azure Active Directory szervezeti fiókkal (például user@fabrikam.com vagy user@contoso.com). Ebben a forgatókönyvben csak Azure Active Directory-felhasználók a szervezeten belül kezelheti a BizTalk szolgáltatás a BizTalk szolgáltatások portál használatával. A Microsoft-fiók nem használható.  
* BizTalk szolgáltatás létrehozása a klasszikus Azure portálon, amikor Ön automatikusan regisztrálva van a BizTalk szolgáltatások portálon.
  * **A forgatókönyv**: BizTalk szolgáltatás létrehozása a klasszikus Azure-portál ba, és válassza **kezelése** a legelső alkalommal. Amikor megnyílik a BizTalk Services portálra, a BizTalk szolgáltatás automatikusan regisztrálja, és készen áll a központi telepítések a.  
    Lásd: [regisztrálása és a BizTalk a BizTalk szolgáltatás központi telepítés frissítése Services portál](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Augusztus 14 frissítés
* A szerződés és híd leválasztásával – Kereskedelmipartner-egyezmények és hidak most le a BizTalk Services portálra. Mostantól létrehozhat szerződések és a hidak külön-külön, és futásidőben hidak feloldani egy szerződést a EDI-üzenetben levő értékek alapján. Lásd: [megállapodások létrehozása az Azure BizTalk szolgáltatások](https://msdn.microsoft.com/library/azure/hh689908.aspx), [hozzon létre egy BizTalk szolgáltatások portálon EDI híd](https://msdn.microsoft.com/library/azure/dn793986.aspx), [hozzon létre egy BizTalk szolgáltatások portálon AS2 híd](https://msdn.microsoft.com/library/azure/dn793993.aspx), és [ Hogyan tegye megoldásához hidak futásidőben szerződéseket?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* Az egyszeri jelszóhoz tartozó szerződéseket létrehozhat megszakad.  
* A küldő-oldalon szerződés megadhat különböző elválasztó mindegyik séma-készletet. Ez a konfiguráció a küldési oldal megállapodás protokoll beállításai szakaszban megadott. További információkért lásd: [egy X12 létrehozása az Azure BizTalk szolgáltatások megállapodás](https://msdn.microsoft.com/library/azure/hh689847.aspx) és [egy EDIFACT-egyezmény létrehozása az Azure BizTalk szolgáltatások](https://msdn.microsoft.com/library/azure/dn606267.aspx). Két új entitásokat is bekerülnek a TPM OM API ugyanerre a célra. Lásd: [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) és [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Standard XSD szerkezetek, beleértve a származtatott típusok támogatása. Lásd: [a a maps szerkezeteket használjon szokásos XSD](https://msdn.microsoft.com/library/azure/dn793987.aspx) és [használati származtatott típusok Leképezési forgatókönyvek és példák](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 új MIC üzenet az aláíráshoz és új titkosítási algoritmusok támogatja. Lásd: [az AS2-egyezmény létrehozása az Azure BizTalk szolgáltatások](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Ismert problémák

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>BizTalk szolgáltatások portál frissítése után csatlakozási problémák
  Ha a BizTalk szolgáltatások portálja nyissa meg a BizTalk szolgáltatások szeretné visszaállítani a módosításokat a szolgáltatás frissítése közben, akkor előfordulhat, hogy szembesülhetnek csatlakozási hibák léptek fel a BizTalk Services portálra.  
  A probléma megoldásához előfordulhat, hogy indítsa újra a böngészőt, törli a gyorsítótárban, illetve a portálon a saját módjában indul el.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE környezetben nem található az összetevő, ha egy hiba vagy figyelmeztetés BizTalk szolgáltatások projektben kattintson
Telepítse a Visual Studio 2012 3 RC 1 kijavítani a problémát.  

### <a name="custom-binding-project-reference"></a>Egyéni kötésben projektbe egy hivatkozást a
Vegye figyelembe a BizTalk szolgáltatások projekt egy Visual Studio megoldás a következő esetekben:  

* Visual Studio megoldáskezelőben nincs BizTalk szolgáltatások projekt és az egyéni kötésben projekt. A BizTalk szolgáltatás projektet az egyéni kötésben projektfájlt hivatkozás van.
* A BizTalk szolgáltatás projekt egy egyéni kötési/viselkedését DLL hivatkozás van.

Ön "" a megoldás felépítéséhez a Visual Studio sikeresen megtörtént. Ezt követően "Rebuild" vagy "Tiszta" a megoldás. Ezt követően tiszta újra, vagy építse újra a következő hiba történt:  
  Nem sikerült a másolás, <Path to DLL> "bin\Debug\FileName.dll" számára. A folyamat nem éri a "bin\Debug\FileName.dll" fájlt, mert egy másik folyamat használja.  

#### <a name="workaround"></a>Megkerülő megoldás
* Ha [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) van telepítve, a következő lehetőségei vannak:
  
  * Indítsa újra a Visual Studio vagy
  * Indítsa újra a megoldás. Ezután hajtson végre Build csak a megoldásban.  
* Ha [Visual Studio 2012 Update 3](https://www.microsoft.com/download/details.aspx?id=39305) nincs telepítve, nyissa meg a Feladatkezelő, kattintson a folyamatok lapon, kattintson a MSBuild.exe folyamat, majd a folyamat leállítása gombra.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Útválasztás BasicHttpRelay végpontokhoz nem támogatott hidak és a BizTalk szolgáltatások portálja nem nyomtatható karakterek van előléptetve, mint a HTTP-fejlécek Ha
Használatakor nem nyomtatható karakterek előléptetett tulajdonságainak üzenetek azokat az üzeneteket nem lehet továbbküldeni BasicHttpRelay kötésének használó továbbítási célhelyre. Az előléptetett tulajdonságok, amely is elérhető részét követési URL-kódolású a blobok és megbízhatatlan kódolt célhoz vannak.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN aszinkron módon legyen elküldve, még akkor is, ha a küldési aszinkron MDN beállítás nincs bejelölve
Ebben a forgatókönyvben – vegye figyelembe, ha a **aszinkron MDN küldése** jelölőnégyzetet, és adjon meg egy URL-címet a MDN aszinkron küldés, és törölje a **aszinkron MDN küldése** jelölőnégyzet újra, a MDN továbbra is küld a annak ellenére, hogy a küldött aszinkron MDNs beállítás nincs bejelölve a megadott URL-CÍMÉT.  
A probléma megoldásához törölje a jelölést a megadott URL-cím jelölésének törlése előtt a **aszinkron MDN küldése** jelölőnégyzetet, majd telepítse az AS2-szerződés.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Érvényes interchange túl szóköz karakterből okozhat a suspend-végpont küldendő egy üres üzenet
Ha egy IEA szegmens túl szóközöket, a kicsomagoló kezeli a jelenlegi interchange végét, és ellenőrzi, hogy az a következő készlete szóközöket a következő üzenetben. Mivel ez nem érvényes interchange, azt láthatja, hogy az útvonal cél egy sikeres üzenetet kap, és egy üres üzenetet küld a suspend-végpont.  

### <a name="tracking-in-biztalk-services-portal"></a>BizTalk szolgáltatások portálon nyomon követése
Nyomkövetési események kerülnek rögzítésre a EDI-üzenet feldolgozása és bármely korrelációs. Ha egy üzenetet a protokoll szakasz kívül nem sikerül, nyomkövetési sikeres jeleníti meg. Ebben a helyzetben tekintse meg a napló szakaszában a **részletek** oszlopa **követési** a hiba részletei.
A X12 beállítások küldhet és fogadhat ([hozzon létre egy X12 Azure BizTalk Services szerződés](https://msdn.microsoft.com/library/azure/hh689847.aspx)) kapcsolatban nyújtanak információkat a protokoll szakasszal.  

### <a name="update-agreement"></a>Frissítés megállapodás
A BizTalk szolgáltatások portál lehetővé teszi, hogy a minősítő identitás módosíthatja, ha szerződés úgy van konfigurálva. Ez a eredményezhet inconsistence tulajdonságait. Például ZZ:1234567 és ZZ:7654321 a minősítő megállapodás van. BizTalk szolgáltatások portál beállításai ZZ:1234567 01:ChangedValue kell módosítani. Megnyitja a szerződést, és 01:ChangedValue helyett ZZ:1234567 jelenik meg.
A minősítő identitás, a szerződés törlése módosításához frissíteni **identitások** a partner profilba, majd hozza létre újra a szerződést.  

> AZURE. Ez a viselkedés figyelmeztetés X12 és AS2 hatással van.  
> 
> 

### <a name="as2-attachments"></a>AS2-mellékletek
Üzenetek nem támogatottak az AS2-mellékletek elküldeni vagy fogadni. Pontosabban mellékletek csendes figyelmen kívül hagyja, és az üzenettörzs rendszeres AS2-üzenetet dolgoz fel.  

### <a name="resources-remembering-path"></a>Erőforrások: Jelszóelőzmények elérési útja
Hozzáadásakor **erőforrások**, a párbeszédpanelen nem lehetséges, hogy tárolja az erőforrás hozzáadása korábban használt elérési utat. Jegyezze meg a korábban használt elérési utat, próbálja meg a hozzáadni a BizTalk szolgáltatások Portal webhelyet **megbízható helyek** az Internet Explorerben.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Nevezze át az entitás egy híd, és a módosítások mentése nélkül zárja be, ha az entitás újra megnyitni a hibát eredményez
Vegye figyelembe a forgatókönyv a következő sorrendben:  

* Adjon hozzá egy híd (például egy XML One-Way híd) egy BizTalk szolgáltatás projekthez  
* Nevezze át a híd az entitás neve tulajdonság értékének megadásával. Ez átnevezi, a társított .bridgeconfig fájlt a megadott név.  
* A módosítások mentése nélkül zárja be a a .bcs fájlt (a Visual Studio lapján bezárásával).  
* .Bcs újból megnyitni a megoldáskezelőjében.  
  Megfigyelheti, hogy míg a társított .bridgeconfig fájl a megadott új nevet, a entitás neve a Tervező felületére, továbbra is a régi név. Ha megpróbálja megnyitni az kattintson duplán a híd összetevő, akkor a következő hibaüzenet:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist`Ebben a forgatókönyvben elkerülje, győződjön meg arról, hogy az entitások BizTalk szolgáltatás projekt átnevezése után a módosítások mentése.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>BizTalk szolgáltatás projekt sikeresen hoz létre, akkor is, ha egy közbülső ki lett zárva a Visual Studio-projekt
Fontolja meg egy olyan forgatókönyvet, ahol hozzáadhat egy összetevő (például egy XSD-fájlt) BizTalk szolgáltatás projektbe, (például megadásával, egy kérelem üzenet típusa) az adott összetevő bevonni és kizárni a Visual Studio-projekt. Ebben az esetben a projekt nem küld olyan hiba, amíg a törölt összetevő ugyanazon a helyen, ahol ezt a Visual Studio-projekt tartalmazzák-e a rendszer a lemezen.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>A BizTalk szolgáltatás projekt nem ellenőrzi a séma rendelkezésre állás érdekében a hidak konfigurálása közben
BizTalk szolgáltatás projektben a projekt hozzáadott séma importálhatja egy másik séma, ha a BizTalk szolgáltatás projekt nem ellenőrzi, hogy az importált séma hozzáadódik a projektet. A projekt felépítéséhez kísérli meg, ha nem jelenik meg összeállítási hiba.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>A válasz egy XML-kérelem-válasz híd üzenet mindig a charset UTF-8
Ebben a kiadásban a válaszüzenet egy XML-kérelem-válasz híd charset értéke mindig UTF-8.
  
### <a name="user-defined-datatypes"></a>Felhasználó által definiált adattípusok
A BizTalk Adapter Pack adapterek belül a BizTalk Adapter szolgáltatás nyújthatnak a felhasználó által definiált adattípusok adapter műveletekhez.
Felhasználó által definiált adattípusok használata esetén a meghajtó: \Program Files\Microsoft BizTalk Adapter Service\BAServiceRuntime\bin\ vagy számára a globális szerelvény gyorsítótárban (GAC) a BizTalk Adapter Service szolgáltatást futtató kiszolgálón másolja a fájlok (.dll). Ellenkező esetben a következő hiba fordulhat elő az ügyfélen:  
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
> Javasoljuk, hogy GACUtil.exe segítségével telepítse a fájlt a globális szerelvény-gyorsítótárban. GACUtil.exe dokumentumokat az eszköz és a Visual Studio parancssori kapcsolók használata.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>A BizTalk Adapter szolgáltatás webhely újraindítása
Telepíti a **BizTalk Adapter szolgáltatás futásideje*** hoz létre a **BizTalk szolgáltatás** webhely az IIS-ben, amely tartalmazza a **BAService** alkalmazás. **BAService** alkalmazás belső módon használja a továbbítási kötés kiterjesztése a felhőbe a helyszíni végpont nem hozzáférhető. Egy üzemeltetett szolgáltatást helyi a megfelelő továbbítási végpont regisztrálva lesz a Service buson csak akkor, ha a helyszíni szolgáltatás elindul.  

Állítsa le és indítsa el a kérelmet, ha egy alkalmazás automatikus indítása a konfiguráció nem vehető figyelembe. Így ha **BAService** van leállítja, hogy mindig újra kell indítani a **BizTalk szolgáltatás** webhely helyett. Nem elindulni, vagy állítsa le a **BAService** alkalmazás.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Különleges karakterek nem használható a cím és egyéb entitások nevekkel LOB-összetevők
Ne használjon speciális karaktereket a cím és egyéb entitások nevekkel LOB-összetevők. Ha így tesz, a BizTalk szolgáltatás projekt telepítése során hiba lép fel. Bizonyos karaktereket, mint például a "%", a BizTalk szolgáltatás webhely mehet leállított állapotba, és később kell manuálisan indítsa el.

### <a name="test-map-with-get-context-property"></a>A Context tulajdonság Get térkép tesztelése
Ha egy átalakítási tartalmaz egy **Context tulajdonság beolvasása** térkép művelet **teszt térkép** sikertelen lesz. Ideiglenes megoldásként, cserélje le a **Context tulajdonság Get** egy karakterlánc összefűzésére térkép művelet típusú adatokat tartalmazó térkép műveletet. Ez a cél séma feltöltéséhez, és tesztelése más átalakítási-funkciók engedélyezése.

### <a name="test-map-property-does-not-display"></a>Teszt térkép tulajdonság nem jelenik meg
A **teszt térkép** tulajdonságok nem jelennek meg a Visual Studióban. Ez akkor fordulhat elő, ha a **tulajdonságok** ablak és a **Megoldáskezelőben** ablak egyidejűleg nincs rögzítve. A probléma megoldásához, rögzítése a **tulajdonságok** és a **Megoldáskezelőben** windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Dátum és idő formázza újra legördülő szürkén jelenik meg
Amikor egy dátum/idő formázza újra térkép művelet fel a tervezési felülethez és konfigurálva van, a Formátum legördülő lista lehet, hogy jelenítette meg. Ez akkor fordulhat elő, ha a számítógép megjelenítési **Közepes – 125 %** vagy **nagyobb – 150 %**. Megoldása érdekében adja meg a megjelenített **Smaller – 100 %-os (alapértelmezett)** az alábbi lépéseket követve:  

1. Nyissa meg a **Vezérlőpult** kattintson **Megjelenés és személyre szabása**.
2. Kattintson a **megjelenített**.
3. Kattintson a **Smaller – 100 %-os (alapértelmezett)** kattintson **alkalmaz**.

A **formátum** legördülő lista most már a várt módon működik a kell.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>A BizTalk szolgáltatások portálon ismétlődő megállapodások
Vegye figyelembe a következő forgatókönyvet:

1. Hozzon létre egy szerződést a kereskedelmi Partner OM API használatával.
2. Nyissa meg a szerződés a BizTalk szolgáltatások portálon két különböző lapokon.
3. Telepítse a megállapodás mindkét a lapról.
4. Ennek eredményeképpen mindkét szerződések telepítve, a BizTalk szolgáltatások portálja ismétlődő bejegyzést eredményez

**Megkerülő megoldás**. Nyissa meg az ismétlődő szerződések egyikét sem a BizTalk Services portálra, és eltávolítása.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Hidak ne használja a frissített tanúsítvány még a tanúsítvány összetevő tárolójában frissítése után
Vegye figyelembe a következő esetekben:  

**1. forgatókönyv: Ujjlenyomat-alapú tanúsítványok használatával biztonságossá tételéhez üzenet átvitelét hidat szolgáltatásvégpont**  
Fontolja meg egy olyan forgatókönyvet, ahol a BizTalk szolgáltatás projekt ujjlenyomat-alapú tanúsítványokat használ. Frissítse a tanúsítványt a BizTalk szolgáltatások portálon azonos nevű, de egy másik ujjlenyomatot, de nem a BizTalk szolgáltatás projekt ennek megfelelően frissülnek. Ilyen esetben a híd továbbra is az üzenetek feldolgozásához, mert a csatorna gyorsítótár továbbra is lehetséges, hogy a régebbi adatok nem. Ezt követően üzenet feldolgozása sikertelen.  

**Megkerülő megoldás**: a tanúsítvány a BizTalk szolgáltatás projekt frissítése, és telepítse újra a projektet.  

**2. forgatókönyv: Parancsfájlokkal-névalapú tanúsítványok védelmét biztosító üzenet átvitelét hidat szolgáltatásvégpont azonosítása**

Fontolja meg egy olyan forgatókönyvet, ahol-névalapú viselkedések azonosítására használt tanúsítványok a BizTalk szolgáltatás projektben. Frissítse a tanúsítványt a BizTalk szolgáltatások portálon, de nem frissíti a BizTalk szolgáltatás projektet ennek megfelelően. Ilyen esetben a híd továbbra is az üzenetek feldolgozásához, mert a csatorna gyorsítótár továbbra is lehetséges, hogy a régebbi adatok nem. Ezt követően üzenet feldolgozása sikertelen.  

**Megkerülő megoldás**: a tanúsítvány a BizTalk szolgáltatás projekt frissítése, és telepítse újra a projektet.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Hidak folytatja az üzenetek feldolgozásához, akkor is, ha az SQL-adatbázis offline állapotban.
A BizTalk szolgáltatások hidak folytatja a üzenetek egy ideig, még akkor is, ha a Microsoft Azure SQL Database (amely tárolja a futó adatok, például a telepített összetevők és folyamatok), offline állapotban. Ennek az az oka BizTalk szolgáltatások gyorsítótárazott összetevők és konfigurációs híd használja.
Ha nem szeretné, hogy a hidak bármely üzenetek feldolgozásához, amikor az SQL-adatbázis offline állapotban, a BizTalk szolgáltatások PowerShell-parancsmagok segítségével vagy felfüggesztheti a BizTalk szolgáltatás. Lásd: [Azure BizTalk szolgáltatás felügyeleti minta](http://go.microsoft.com/fwlink/p/?LinkID=329019) műveleteinek a felügyeletét a Windows PowerShell parancsmagok.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Az XML-üzenet egy híd egyéni kód összetevőn belüli olvasásakor tartalmaz egy extra AJ karakter
Fontolja meg egy olyan forgatókönyvet, ahol szeretné olvasni az XML-üzeneteket egy híd egyéni kód. Ha a .NET API System.Text.Encoding.UTF8.GetString(bytes) egy extra AJ karakter a kimenet az üzenet elején szerepel. Ezért, ha nem szeretné, hogy a kimenet tartalmazza a felesleges AJ karaktert, használnia kell ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Üzenetek küldése egy WCF használatával híd nem méretezhető
WCF használatával hálózati hidat küldött üzenetek nem méretezhető. Ha azt szeretné, hogy méretezhető ügyfél HttpWebRequest helyette használjon.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>FRISSÍTÉS: Jogkivonat-szolgáltató által jelzett hibát BizTalk szolgáltatások Preview verzióról az általános rendelkezésre állási (GA)
A program egy EDI vagy AS2 megállapodás aktív kötegek. Ha a BizTalk szolgáltatás GA Preview legyen frissítve, a következő fordulhat elő:

* Hiba: A jogkivonat-szolgáltató nem tudta adjon meg egy biztonsági jogkivonatot. Jogkivonat-szolgáltató küldött: A távoli név nem sikerült feloldani.
* Kötegelt feladatok törölve lesznek.

**Megkerülő megoldás**: után a BizTalk szolgáltatás frissítve lett az általános rendelkezésre állási (GA), telepítse újra a szerződést.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>FRISSÍTÉS: Eszközkészlet jeleníti meg a régi híd ikonok a BizTalk SDK-t a frissítés után
A BizTalk szolgáltatások SDK, melyből régi ikonjai a hidak, egy korábbi frissítés után az eszközkészlet továbbra is a régi a hidak ikonok megjelenítése. Azonban hidat BizTalk szolgáltatás projekt Tervező felületére való hozzáadásakor a felület mutatja az új ikonra.  

**Megkerülő megoldás**. A probléma megoldásához használhat az .tbd fájlokat a <system drive>: \Users\<felhasználó > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>FRISSÍTÉS: A BizTalk portál frissítése Preview GA arról, hogy a EDI funkció nem érhető el hiba előfordulhat, hogy megjelenítése
Ha van bejelentkezve a BizTalk szolgáltatások portált, a BizTalk szolgáltatások Preview GA frissítése közben, a következő hiba kaphat a portál:  

Ez a funkció nem érhető el a Microsoft Azure BizTalk Services jelen kiadása részeként. Használni ezen képességek megfelelő kiadás váltani.  

**Megoldási**: a portálon, zárja be és nyissa meg a böngészőt, majd jelentkezzen be a portálra a kimenő napló.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>FRISSÍTÉS: Új követési adatok nem jelenik meg a BizTalk szolgáltatások GA történő frissítése után
Tegyük fel, a helyzetet, amelyben egy XML híd BizTalk szolgáltatás előzetes előfizetés telepítve vannak-e. Üzenetek küldése a híd, és a hozzá tartozó nyomkövetési adatok a BizTalk szolgáltatások portálja elérhető-e. Mostantól Ha a BizTalk Services portál és a BizTalk szolgáltatások futásidejű bits GA verzióra frissíti, és egy üzenetet küld a korábban telepített azonos híd végpont, a nyomon követési adatok nem jelenik meg a frissítés után küldött üzenetek.  

### <a name="pipelines-versus-bridges"></a>És a hidak csővezeték
Ez a dokumentum a kifejezés "folyamatok" és "hidak" van megegyezik. Mindkét lényegében ugyanazt, amely, egy üzenet feldolgozása egysége BizTalk szolgáltatások telepíthetők jelenti.  

### <a name="concepts"></a>Alapelvek
[BizTalk szolgáltatások](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

