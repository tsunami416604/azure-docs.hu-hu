---
title: Beviteli ellenőrzés - Microsoft Threat Modeling Tool - Azure | Microsoft dokumentumok
description: a fenyegetésmodellezési eszközben elérhető fenyegetések enyhítése
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: f443bf3111d2ab97874bdc62ec1370d17e2fc406
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728057"
---
# <a name="security-frame-input-validation--mitigations"></a>Biztonsági keret: Bevitelérvényesítés | Enyhítése 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[XSLT-parancsfájlok letiltása az összes átalakításhoz nem megbízható stíluslapok használatával](#disable-xslt)</li><li>[Győződjön meg arról, hogy minden olyan oldal, amely a felhasználó által szabályozható tartalmat tartalmazhat, leiratkozik az automatikus MIME-szippantásról](#out-sniffing)</li><li>[XML-entitásfeloldás ának bekeményítése vagy letiltása](#xml-resolution)</li><li>[A http.sys-t használó alkalmazások URL-kanonizációs ellenőrzést végeznek](#app-verification)</li><li>[Győződjön meg arról, hogy megfelelő vezérlők vannak érvényben a felhasználóktól származó fájlok elfogadásakor](#controls-users)</li><li>[Annak ellenőrzése, hogy a webalkalmazásban a típusbiztos paraméterek et használja-e az adateléréshez](#typesafe)</li><li>[Külön modellkötési osztályok vagy kötésszűrő-listák használata az MVC tömeghozzárendelési biztonsági résének megelőzése érdekében](#binding-mvc)</li><li>[Nem megbízható webes kimenet kódolása renderelés előtt](#rendering)</li><li>[Beviteli ellenőrzés és szűrés végrehajtása az összes karakterlánctípus modelltulajdonságán](#typemodel)</li><li>[A fertőtlenítést olyan űrlapmezőkön kell alkalmazni, amelyek minden karaktert elfogadnak, pl. rich text szerkesztő](#richtext)</li><li>[Ne rendeljen DOM-elemeket olyan mosogatókhoz, amelyek nem rendelkeznek beépített kódolással](#inbuilt-encode)</li><li>[Az alkalmazáson belüli összes átirányítás ellenőrzése lezárva vagy biztonságosan elvégezhető](#redirect-safe)</li><li>[A Vezérlő metódusai által elfogadott összes karakterlánctípus-paraméter bemeneti érvényesítésének megvalósítása](#string-method)</li><li>[Állítsa be a reguláris kifejezések feldolgozásának felső határidejét, hogy megakadályozza a DoS-t a hibás regisztrakifejezések miatt](#dos-expression)</li><li>[Kerülje a Html.Raw használatát a Razor nézetekben](#html-razor)</li></ul> | 
| **Adatbázis** | <ul><li>[Ne használjon dinamikus lekérdezéseket a tárolt eljárásokban](#stored-proc)</li></ul> |
| **Webes API** | <ul><li>[Annak ellenőrzése, hogy a modell érvényesítése webes API-metódusokon történik-e](#validation-api)</li><li>[A webes API-metódusok által elfogadott összes karakterlánctípus-paraméter bemeneti érvényesítésének megvalósítása](#string-api)</li><li>[Annak ellenőrzése, hogy a webAPI-ban a típusbiztos paraméterek et használja-e az adatok eléréséhez](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Paraméterezett SQL-lekérdezések használata az Azure Cosmos DB-hez](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF bemeneti érvényesítéssé sémakötéssel](#schema-binding)</li><li>[WCF - Bemeneti ellenőrzés paraméterellenőrökön keresztül](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>XSLT-parancsfájlok letiltása az összes átalakításhoz nem megbízható stíluslapok használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [XSLT Security](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript tulajdonság](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Lépéseket** | Az XSLT támogatja a stíluslapokon belüli parancsfájlok futtatását az `<msxml:script>` elem használatával. Ez lehetővé teszi, hogy az egyéni függvények xslt transzformációban is használhatók legyenek. A parancsfájl végrehajtása az átalakítást végző folyamat környezetében történik. Az XSLT-parancsfájlt le kell tiltani, ha nem megbízható környezetben van, hogy megakadályozza a nem megbízható kód végrehajtását. *A .NET használata esetén:* Az XSLT-parancsfájlok alapértelmezés szerint le vannak tiltva; azonban meg kell győződnie arról, hogy `XsltSettings.EnableScript` nem volt kifejezetten engedélyezve a tulajdonságon keresztül.|

### <a name="example"></a>Példa 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Példa
MsXML 6.0 használata esetén az XSLT parancsfájlok alapértelmezés szerint le vannak tiltva; azonban meg kell győződnie arról, hogy nem volt kifejezetten engedélyezve az XML DOM objektum tulajdonság A AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Példa
Ha az MSXML 5 vagy az azt nem régi, az XSLT parancsfájlok alapértelmezés szerint engedélyezve vannak, és azt kifejezetten le kell tiltania. Állítsa az AllowXsltScript XML DOM objektumtulajdonságot false értékűre. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Győződjön meg arról, hogy minden olyan oldal, amely a felhasználó által szabályozható tartalmat tartalmazhat, leiratkozik az automatikus MIME-szippantásról

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [IE8 Biztonsági V. rész – Átfogó védelem](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Lépéseket** | <p>Minden olyan lapon, amely a felhasználó által szabályozható `X-Content-Type-Options:nosniff`tartalmat tartalmazhat, a HTTP-fejlécet kell használnia. Ennek a követelménynek való megfelelés érdekében beállíthatja a szükséges fejlécoldalt csak azokra az oldalakra, amelyek felhasználó által szabályozható tartalmat tartalmazhatnak, vagy beállíthatja globálisan az alkalmazás összes lapjához.</p><p>A webkiszolgálóról szállított minden egyes fájltípushoz tartozik egy [MIME-típus](https://en.wikipedia.org/wiki/Mime_type) (más néven *tartalomtípus),* amely leírja a tartalom jellegét (azaz képet, szöveget, alkalmazást stb.)</p><p>Az X-Content-Type-Options fejléc egy HTTP-fejléc, amely lehetővé teszi a fejlesztők számára, hogy megadják, hogy a tartalmukat ne lehessen mime-szippantottnak. Ez a fejléc a MIME-szimatoló támadások csökkentésére szolgál. A fejléc támogatása az Internet Explorer 8 (IE8) böngészőben lett hozzáadva</p><p>Az X-Content-Type-Options csak az Internet Explorer 8 (IE8) felhasználói élvezhetik a kedvezmény előnyeit. Az Internet Explorer korábbi verziói jelenleg nem tartják tiszteletben az X-Content-Type-Options fejlécet</p><p>Az Internet Explorer 8 (és újabb verziói) az egyetlen főbb böngészők, amelyek megvalósítják a MIME-szippantás letiltási funkciót. Ha és amikor más nagy böngészők (Firefox, Safari, Chrome) hasonló funkciókat valósítanak meg, ez az ajánlás frissül, hogy tartalmazza a szintaxist az említett böngészőkszámára is</p>|

### <a name="example"></a>Példa
Ha az alkalmazás összes lapjához globálisan engedélyezni szeretné a szükséges fejlécet, tegye az alábbiak egyikét: 

* Adja hozzá a fejlécet a web.config fájlhoz, ha az alkalmazást az Internet Information Services (IIS) 7 üzemelteti 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* A fejléc hozzáadása a\_globális Alkalmazás BeginRequest alkalmazáson keresztül 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Egyéni HTTP-modul megvalósítása 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* A szükséges fejlécet csak bizonyos oldalakhoz engedélyezheti, ha hozzáadja az egyes válaszokhoz: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>XML-entitásfeloldás ának bekeményítése vagy letiltása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [XML entitásbővítés,](https://capec.mitre.org/data/definitions/197.html) [XML szolgáltatásmegtagadási támadások és védelem](https://msdn.microsoft.com/magazine/ee335713.aspx), [MSXML-biztonság – gyakorlati](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx)tanácsok az [MSXML-kód biztonságossá tétele ,](https://msdn.microsoft.com/library/ms759188(VS.85).aspx) [NSXMLParserDelegate protokollhivatkozás](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [külső hivatkozások feloldása](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Lépéseket**| <p>Bár nem széles körben használják, az XML-nek van egy olyan szolgáltatása, amely lehetővé teszi az XML-elemző számára, hogy kibontsa a makróentitásokat olyan értékekkel, amelyekértéke a dokumentumon belül vagy külső forrásokból van meghatározva. Előfordulhat például, hogy a dokumentum "cégnév" entitást definiál a "Microsoft" értékkel, így minden alkalommal, amikor a "&companyname;szöveg megjelenik a dokumentumban, automatikusan lecseréli a Microsoft szövegre. Vagy a dokumentum definiálhat egy "MSFTStock" entitást, amely egy külső webszolgáltatásra hivatkozik a Microsoft-készlet aktuális értékének lekéréséhez.</p><p>Ezután bármikor&MSFTStock;" " jelenik meg a dokumentumban, akkor automatikusan lecseréli az aktuális tőzsdei ár. Ezzel a funkcióval azonban vissza lehet élni a szolgáltatásmegtagadási (DoS) feltételek létrehozásához. A támadó több entitást is beágyazhat, hogy olyan exponenciális bővítő XML-bombát hozzon létre, amely a rendszer összes rendelkezésre álló memóriáját felhasználja. </p><p>Másik lehetőségként létrehozhat egy külső hivatkozást, amely végtelen mennyiségű adatot továbbít vissza, vagy egyszerűen lefagy a szál. Ennek eredményeképpen minden csapatnak teljesen le kell tiltania a belső és/vagy külső XML-entitás felbontását, ha az alkalmazás nem használja, vagy manuálisan korlátoznia kell az alkalmazás által entitásfeloldáshoz felhasználható memória és idő mennyiségét, ha ez a funkció feltétlenül szükséges. Ha az entitás feloldása nem szükséges az alkalmazás, majd tiltsa le. </p>|

### <a name="example"></a>Példa
A .

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Ne feledje, `ProhibitDtd` hogy `XmlReaderSettings` az alapértelmezett `XmlTextReader` értéke igaz, de ez hamis. Ha xmlreadersettings- ot használ, nem kell explicit módon true-ra állítania a ProhibitDtd értéket, de biztonsági okokból ajánlott. Azt is vegye figyelembe, hogy az XmlDocument osztály alapértelmezés szerint lehetővé teszi az entitás felbontását. 

### <a name="example"></a>Példa
Az XmlDocuments entitásfelbontásának letiltásához használja a `XmlDocument.Load(XmlReader)` Betöltés metódus túlterhelését, és állítsa be a megfelelő tulajdonságokat az XmlReader argumentumban a megoldás letiltásához, amint azt a következő kód szemlélteti: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Példa
Ha az entitásfeloldás letiltása nem lehetséges az alkalmazásban, állítsa az XmlReaderSettings.MaxCharactersFromentityes tulajdonságot az alkalmazás igényeinek megfelelően ésszerű értékre. Ez korlátozza a lehetséges exponenciális terjeszkedési DoS-támadások hatását. A következő kód egy példát mutat be erre a megközelítésre: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Példa
Ha fel kell oldania a szövegközi entitásokat, de nem kell feloldania a külső entitásokat, állítsa az XmlReaderSettings.XmlResolver tulajdonságot null értékre. Példa: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Ne feledje, hogy az MSXML6 alkalmazásban a ProhibitDTD értéke alapértelmezés szerint igaz (a DTD-feldolgozás letiltása). Az Apple OSX/iOS-kódokhoz két XML-elemző használható: Az NSXMLParser és a libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>A http.sys-t használó alkalmazások URL-kanonizációs ellenőrzést végeznek

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>A http.sys-t használó alkalmazásoknak az alábbi irányelveket kell követniük:</p><ul><li>Az URL-cím hosszát legfeljebb 16 384 karakterre (ASCII vagy Unicode) korlátozhatja. Ez az alapértelmezett Internet Information Services (IIS) 6 beállítás alapján megadott abszolút maximális URL-hossz. A weboldalaknak törekedniük kell arra, hogy ez nél rövidebb legyen, ha lehetséges</li><li>Használja a szabványos .NET Framework fájl I/O osztályait (például a FileStream-et), mivel ezek kihasználják a .NET FX kanonizációs szabályait.</li><li>Az ismert fájlnevek engedélyezési listájának explicit módon létrehozása</li><li>Explicit elutasítja az ismert fájltípusoknem fogja kiszolgálni UrlScan elutasítja: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, nyomtató, ini, pol, dat fájlok</li><li>Fogja meg a következő kivételeket:<ul><li>System.ArgumentException (eszköznevekesetén)</li><li>System.NotSupportedException (adatfolyamok esetén)</li><li>System.IO.FileNotFoundException (érvénytelen escaped fájlnevek esetén)</li><li>System.IO.DirectoryNotFoundException (érvénytelen escaped dirs esetén)</li></ul></li><li>*Ne* hívja ki a Win32 fájl I/O API-kat. Egy érvénytelen URL-cím esetén szabályosan 400-as hibát ad vissza a felhasználónak, és naplózza a valós hibát.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Győződjön meg arról, hogy megfelelő vezérlők vannak érvényben a felhasználóktól származó fájlok elfogadásakor

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Korlátlan fájlfeltöltés](https://www.owasp.org/index.php/Unrestricted_File_Upload), [Fájlaláírási táblázat](https://www.garykessler.net/library/file_sigs.html) |
| **Lépéseket** | <p>A feltöltött fájlok jelentős kockázatot jelentenek az alkalmazásokra nézve.</p><p>Az első lépés a sok támadás, hogy néhány kódot a rendszer megtámadják. Akkor a támadásnak csak meg kell találnia a módját, hogy végrehajtsa a kódot. A fájlfeltöltés használata segít a támadónak az első lépés elvégzésében. A korlátlan fájlfeltöltés következményei eltérőek lehetnek, beleértve a teljes rendszerátvételt, a túlterhelt fájlrendszert vagy adatbázist, a támadások továbbítását a háttérrendszerekre, és az egyszerű defacement.</p><p>Ez attól függ, hogy az alkalmazás mit csinál a feltöltött fájlt, és különösen, ha tárolják. Hiányzik a fájlfeltöltések kiszolgálóoldali érvényesítése. A Fájlfeltöltés funkcióhoz a következő biztonsági ellenőrzéseket kell végrehajtani:</p><ul><li>Fájlkiterjesztés-ellenőrzés (csak érvényes, engedélyezett fájltípus-készletet kell elfogadni)</li><li>A fájlméret maximális korlátja</li><li>A fájlt nem szabad feltölteni a webroot programba; a helynek könyvtárnak kell lennie a nem rendszermeghajtón</li><li>Az elnevezési konvenciót úgy kell követni, hogy a feltöltött fájlnév véletlenszerű legyen, hogy megakadályozza a fájlfelülírásokat</li><li>A lemezre írás előtt meg kell vizsgálni a fájlok at anti-vírus</li><li>Győződjön meg arról, hogy a fájlnév és az egyéb metaadatok (pl. fájlelérési út) rosszindulatú karakterekre vannak érvényesítve</li><li>A fájlformátum-aláírást ellenőrizni kell, hogy a felhasználó ne tölthetne fel álarcosfájlt (pl. exe fájl feltöltése a kiterjesztés txt-re történő módosításával)</li></ul>| 

### <a name="example"></a>Példa
A fájlformátum-aláírás-ellenőrzéssel kapcsolatos utolsó pontot az alábbi osztályban találja a részletekért: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Annak ellenőrzése, hogy a webalkalmazásban a típusbiztos paraméterek et használja-e az adateléréshez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Ha a Paraméterek gyűjteményt használja, az SQL a bemenetet konstans értékként, nem pedig végrehajtható kódként kezeli. A Paraméterek gyűjtemény a bemeneti adatok típus- és hosszkorlátainak kényszerítésére használható. A tartományon kívüli értékek kivételt váltanak ki. Ha nem használ típusbiztos SQL-paramétereket, előfordulhat, hogy a támadók képesek végrehajtani a szűretlen bemenetbe ágyazott injektálási támadásokat.</p><p>Az SQL-lekérdezések létrehozásakor használjon típusbiztonsági paramétereket a szűretlen bevitellel esetlegesen előforduló SQL-injektálási támadások elkerülése érdekében. Típusbiztonsági paramétereket használhat a tárolt eljárásokkal és a dinamikus SQL utasításokkal. A paramétereket az adatbázis konstans értékként kezeli, nem pedig végrehajtható kódként. A paraméterek típusát és hosszát is ellenőrzik.</p>|

### <a name="example"></a>Példa 
A következő kód bemutatja, hogyan használhatja a típusbiztonsági paramétereket az SqlParameterCollection-vel egy tárolt eljárás hívásakor. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Az előző kódpéldában a bemeneti érték nem lehet hosszabb 11 karakternél. Ha az adatok nem felelnek meg a paraméter által meghatározott típusnak vagy hossznak, az SqlParameter osztály kivételt okoz. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Külön modellkötési osztályok vagy kötésszűrő-listák használata az MVC tömeghozzárendelési biztonsági résének megelőzése érdekében

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Metaadat-attribútumok](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [nyilvános kulcsbiztonsági biztonsági rés és kockázatcsökkentés](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [teljes útmutató a tömeges hozzárendelés ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Első lépések az EF segítségével MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Lépéseket** | <ul><li>**Mikor kell keresni a túlzott kiküldetés biztonsági rések?** A túlkönyvelési biztonsági rések minden olyan helyen előfordulhatnak, ahol a modellosztályokat a felhasználói bevitelből köti. Az MVC-hez hasonló keretrendszerek egyéni .NET osztályokban, például egyszerű régi CLR-objektumokban (POCOs) is képviselhetnek felhasználói adatokat. Az MVC automatikusan feltölti ezeket a modellosztályokat a kérelemből származó adatokkal, így kényelmes encikleket biztosít a felhasználói bevitel kezeléséhez. Ha ezek az osztályok olyan tulajdonságokat tartalmaznak, amelyeket a felhasználónak nem szabad beállítania, az alkalmazás ki lehet téve a túlküldési támadásoknak, amelyek lehetővé teszik az alkalmazás által soha nem kívánt adatok felhasználói vezérlését. Az MVC-modellkötéshez hasonlóan az adatbázis-hozzáférési technológiák, például az objektum/relációs leképezők, például az Entity Framework gyakran támogatják a POCO-objektumok használatát az adatbázisadatok ábrázolására. Ezek az adatmodell-osztályok ugyanolyan kényelmet biztosítanak az adatbázis-adatok kezelésében, mint az MVC a felhasználói bevitel kezelésében. Mivel mind az MVC, mind az adatbázis támogatja a hasonló modelleket, például a POCO objektumokat, úgy tűnik, hogy mindkét célra könnyen újra fellehet használni ugyanazokat az osztályokat. Ez a gyakorlat nem őrzi meg az aggodalmak elkülönítését, és ez egy olyan gyakori terület, ahol a nem kívánt tulajdonságok modellkötésnek vannak kitéve, ami lehetővé teszi a túlküldési támadásokat.</li><li>**Miért ne használjam a szűretlen adatbázismodell-osztályaimat az MVC-műveletem paramétereiként?** Mert az MVC modellkötés az adott osztályban bármit megköt. Még akkor is, ha az adatok nem jelennek meg a nézetben, a rosszindulatú felhasználó küldhet HTTP-kérelmet ezekkel az adatokkal, és az MVC örömmel köti meg, mert a művelet azt mondja, hogy az adatbázisosztály az az adatforma, amelyet el kell fogadnia a felhasználói bevitelhez.</li><li>**Miért érdekel ne érdekelne a modellkötéshez használt forma?** A ASP.NET MVC-modell kötés túlságosan széles modellek teszi ki az alkalmazás túlzott kiküldetés támadások. A túlküldés lehetővé teheti a támadók számára, hogy a fejlesztő által kívántnál is megváltoztassák az alkalmazásadatokat, például felülbírálják egy elem árát vagy egy fiók biztonsági jogosultságait. Az alkalmazásoknak műveletspecifikus kötési modelleket (vagy meghatározott engedélyezett tulajdonságszűrőlistákat) kell használniuk, hogy explicit szerződést biztosítsanak arra vonatkozóan, hogy milyen nem megbízható bemenetet engedélyezze a modellkötésen keresztül.</li><li>**A külön kötési modellek csak a kód másolása?** Nem, ez az aggodalmak szétválasztásának kérdése. Ha újra felhasználja az adatbázismodelleket a műveletmódszerekben, azt mondja, hogy az adott osztály bármely tulajdonságát (vagy altulajdonságát) a felhasználó beállíthat egy HTTP-kérelemben. Ha nem ezt szeretné, hogy az MVC megtegye, akkor szűrőlistára vagy külön osztályalakzatra van szüksége ahhoz, hogy megmutassa az MVC-nek, hogy milyen adatok származhatnak a felhasználói bevitelből.</li><li>**Ha külön kötési modellem van a felhasználói bevitelhez, meg kell kettőznem az összes adatjegyzet-attribútumomat?** Nem feltétlenül. Az adatbázismodell-osztály MetadataTypeAtattribútuma segítségével egy modellkötési osztály metaadataihoz csatolhat hivatkozást. Csak vegye figyelembe, hogy a MetadataTypeAttribute által hivatkozott típusnak a hivatkozástípus egy részhalmazának kell lennie (kevesebb tulajdonsággal rendelkezhet, de nem többel).</li><li>**A felhasználói beviteli modellek és az adatbázismodellek közötti adatmozgatás a felhasználó bemeneti modelljei és az adatbázis-modellek között fárasztó. Átmásolhatok az összes tulajdonságot a tükröződés segítségével? - Igen, az.** A kötési modellekben csak azok a tulajdonságok jelennek meg, amelyek ről úgy döntött, hogy biztonságosak a felhasználói bevitel szempontjából. Nincs olyan biztonsági ok, amely megakadályozza, hogy a tükröződés használatával másolja a két modell között közös összes tulajdonságot.</li><li>**Mi a helyzet [Bind(Exclude ="â € |")]. Használhatom ezt külön kötési modellek helyett? -** Ez a megközelítés nem ajánlott. A [Bind(Exclude ="â€¦")] használata azt jelenti, hogy minden új tulajdonság alapértelmezés szerint kötelező. Új tulajdonság hozzáadásakor egy további lépéssel nem szabad a dolgokat biztonságban tartani, ahelyett, hogy a terv alapértelmezés szerint biztonságos lenne. Attól függően, hogy a fejlesztő ellenőrzi ezt a listát minden alkalommal, amikor egy tulajdonság hozzáadása kockázatos.</li><li>**A [Bind(Include ="â€¦")] hasznos a szerkesztési műveletekhez? -** nem. [Bind(Include ="â €¦")] csak beszúrási stílusú műveletekhez alkalmas (új adatok hozzáadása). UPDATE-stílusú műveletek (meglévő adatok felülvizsgálata), használjon egy másik megközelítést, például külön kötési modellek vagy átadása explicit listát az engedélyezett tulajdonságok UpdateModel vagy TryUpdateModel. A [Bind(Include ="â€¦")] attribútum hozzáadása egy szerkesztési művelethez azt jelenti, hogy az MVC objektumpéldányt hoz létre, és csak a felsorolt tulajdonságokat állítja be, így az összes többi az alapértelmezett értéken marad. Ha az adatok megmaradnak, akkor teljes mértékben felülírja a meglévő entitást, és alaphelyzetbe állítja a kihagyott tulajdonságok értékeit az alapértelmezett értékekre. Ha például az IsAdmin ot kihagyta egy [Bind(Include ="â€¦")] attribútum egy Szerkesztési műveleten, akkor minden olyan felhasználó, akinek a nevét ezzel a művelettel szerkesztették, visszaáll IsAdmin = false értékre (bármely szerkesztett felhasználó elveszíti a rendszergazdai státuszt). Ha meg szeretné akadályozni bizonyos tulajdonságok frissítését, használja a fenti módszerek egyikét. Vegye figyelembe, hogy az MVC eszközküldés egyes verziói a [Bind(Include ="â€¦")] vezérlőosztályokat hoznak létre a Műveletek szerkesztése kor, és azt jelentik, hogy egy tulajdonság eltávolítása a listáról megakadályozza a túlküldési támadásokat. A fentiekben leírtak szerint azonban ez a megközelítés nem a megfelelően működik, és ehelyett visszaállítja a kihagyott tulajdonságokban lévő adatokat az alapértelmezett értékekre.</li><li>**A műveletek létrehozása, vannak-e olyan kikötések segítségével [Bind(Include ="â € |")] helyett külön kötési modellek? -** igen. Először is ez a megközelítés nem működik a Szerkesztési forgatókönyvek, megkövetelése két külön megközelítések enyhítésére az összes túl-kiküldetés biztonsági réseket. Másodszor, külön kötési modellek érvényesíteni közötti aggodalmak közötti alakzat a felhasználói bevitel és az alakzat használt perzisztencia, valami [Bind(Include ="â €¦")] nem csinál. Harmadszor, vegye figyelembe, hogy [Bind(Include ="â € |")] csak kezelni felső szintű tulajdonságok; nem engedélyezheti az attribútumban az altulajdonságoknak csak egy részét (például "Details.Name"). Végül, és talán a legfontosabb, a [Bind(Include ="â€¦")] használatával hozzáad egy további lépést, amelyet minden alkalommal meg kell jegyezni, amikor az osztály modellkötéshez használatos. Ha egy új műveletmetódus közvetlenül kötődik az adatosztályhoz, és elfelejti a [Bind(Include ="â€¦")] attribútumot, akkor ki van téve a túlzott közzétételi támadásoknak, így a [Bind(Include ="â€¦")] megközelítés alapértelmezés szerint valamivel kevésbé biztonságos. Ha a [Bind(Include ="â€¦")]-t használja, mindig ügyeljen arra, hogy mindig adja meg azt minden alkalommal, amikor az adatosztályok műveletmetódus-paraméterekként jelennek meg.</li><li>**A create műveletek, mi a helyzet üzembe a [Bind(Include ="â € |")] attribútum a modell osztály maga? Ez a megközelítés nem kerüli el annak szükségességét, hogy emlékezzen az attribútum minden műveletmódszerre? -** Ez a megközelítés bizonyos esetekben működik. A [Bind(Include ="â€¦")] használata a modelltípuson (nem pedig az osztályt használó műveletparamétereken) megakadályozza, hogy minden műveletmetóduson ne felejtse el bevonni a [Bind(Include ="â€¦")] attribútumot. Az attribútum közvetlen használata közvetlenül az osztályon hatékonyan létrehoz egy külön felületet ennek az osztálynak a modellkötési célokra. Ez a megközelítés azonban modellosztályonként csak egy modellkötési alakzatot tesz lehetővé. Ha egy műveletmódszernek engedélyeznie kell egy mező (például egy csak rendszergazdai művelet, amely frissíti a felhasználói szerepköröket) és más műveleteknek lehetővé kell tenniük a mező modellkötését, ez a megközelítés nem fog működni. Minden osztálynak csak egy modellkötési alakzata lehet; ha a különböző műveletek különböző modellkötési alakzatokat igényelnek, akkor ezeket a különálló alakzatokat külön modellkötési osztályok vagy külön [Bind(Include ="â€¦")] attribútumok használatával kell ábrázolniuk a műveletmetódusokon.</li><li>**Mik azok a kötési modellek? Ugyanazok, mint a modellek megtekintése? -** Ez két kapcsolódó fogalom. A kötési modell kifejezés egy művelet paraméterlistájában használt modellosztályra utal (az MVC modellkötésből a műveletmetódushoz átadott alakzat). A nézetmodell kifejezés egy műveletmetódusból nézetbe átadott modellosztályra utal. A nézetspecifikus modell használata gyakori módszer az adatok műveletmetódusból nézetbe történő továbbítására. Gyakran előfordul, hogy ez az alakzat modellkötésre is alkalmas, és a nézetmodell kifejezés használható a mindkét helyen használt modell rereferenzésére. Hogy pontos legyek, ez az eljárás kifejezetten a kötelező modellekről beszél, a cselekvésnek átadott alakra összpontosítva, ami a tömeges kijelölés szempontjából fontos.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Nem megbízható webes kimenet kódolása renderelés előtt

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, Webes űrlapok, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Hogyan lehet megakadályozni cross-site scripting ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [Cross-site Scripting](https://cwe.mitre.org/data/definitions/79.html), [XSS (Cross Site Scripting) Megelőzés Cheat Sheet](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Lépéseket** | A helyek közötti parancsfájlok (rövidítve XSS-ként) az online szolgáltatások vagy bármely olyan alkalmazás/összetevő támadási vektora, amely az internetről származó adatokat használja fel. Az XSS biztonsági rései lehetővé tvehetik, hogy a támadó parancsfájlt hajtson végre egy másik felhasználó számítógépén egy sebezhető webalkalmazáson keresztül. A rosszindulatú parancsfájlok cookie-k ellopására és az áldozat gépének JavaScript-en keresztültörténő más módon történő manipulálására használhatók. Az XSS-t megakadályozza a felhasználói bevitel ellenőrzése, biztosítva, hogy jól formázott és kódolású legyen, mielőtt egy weboldalon megjelenne. A bemeneti érvényesítés és a kimeneti kódolás a Web Protection Library segítségével végezhető el. A Felügyelt kód\#(C, VB.NET stb.) esetében használjon egy vagy több megfelelő kódolási módszert a Web Protection (Anti-XSS) könyvtárból, attól függően, hogy a felhasználói bevitel melyik környezetben jelenik meg:| 

### <a name="example"></a>Példa

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Beviteli ellenőrzés és szűrés végrehajtása az összes karakterlánctípus modelltulajdonságán

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Érvényesítés hozzáadása](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [modelladatok érvényesítése egy MVC alkalmazásban,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [az Ön ASP.NET MVC alkalmazások ra vonatkozó irányelvei](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Lépéseket** | <p>Az alkalmazásban való használat előtt minden bemeneti paramétert ellenőrizni kell annak érdekében, hogy az alkalmazás védve legyen a rosszindulatú felhasználói bevitelekkel szemben. Ellenőrizze a bemeneti értékeket reguláris kifejezés-ellenőrzéssel a kiszolgálóoldalon egy engedélyezési lista érvényesítési stratégiával. A metódusoknak átadott nem fertőtlenített felhasználói bemenetek / paraméterek kódinjektálási biztonsági réseket okozhatnak.</p><p>Webes alkalmazások esetén a belépési pontok űrlapmezőket, QueryStrings-eket, cookie-kat, HTTP-fejléceket és webszolgáltatás-paramétereket is tartalmazhatnak.</p><p>A modellkötéskor a következő bemeneti érvényesítési ellenőrzéseket kell elvégezni:</p><ul><li>A modell tulajdonságokat regularexpression jegyzeteléssel kell eljegyzetálni az engedélyezett karakterek és a maximálisan megengedett hossz elfogadásához.</li><li>A vezérlőmetódusok nak modelstate érvényességet kell végrehajtaniuk</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>A fertőtlenítést olyan űrlapmezőkön kell alkalmazni, amelyek minden karaktert elfogadnak, pl. rich text szerkesztő

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Nem biztonságos bevitel ,](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3)HTML [sanitizer kódolása](https://github.com/mganss/HtmlSanitizer) |
| **Lépéseket** | <p>Azonosítsa az összes használni kívánt statikus jelölőcímkét. Általános gyakorlat, hogy a formázást biztonságos `<b>` HTML-elemekre `<i>` korlátozzák, például (félkövér) és (dőlt) elemekre.</p><p>Az adatok írása előtt html-kódolja azt. Ez minden rosszindulatú parancsfájlt biztonságossá tesz azáltal, hogy azt szövegként kezeli, nem pedig végrehajtható kódként.</p><ol><li>Tiltsa le ASP.NET a kérés érvényesítését a ValidateRequest="false" attribútum nak az \@ oldaldirektívához való hozzáadásával</li><li>A karakterlánc-bevitel kódolása a HtmlEncode metódussal</li><li>StringBuilder használatával hívja meg a Csere metódust, hogy szelektíven távolítsa el az engedélyezni kívánt HTML-elemek kódolását</li></ol><p>A hivatkozások oldalbe - letiltja ASP.NET `ValidateRequest="false"`a kérelmek érvényesítését a beállításával. Ez a HTML-kódolja a bemeneti és szelektíven lehetővé teszi a `<b>` és `<i>` alternatívaként, a .NET könyvtár HTML fertőtlenítés is használható.</p><p>A HtmlSanitizer egy .NET könyvtár, amely a HTML-töredékek és dokumentumok olyan konstrukciókból való tisztítására használható, amelyek XSS-támadásokhoz vezethetnek. Az AngleSharp segítségével elemzi, manipulálja és rendereli a HTML-t és a CSS-t. A HtmlSanitizer NuGet csomagként telepíthető, és a felhasználói bevitel átadható a megfelelő HTML vagy CSS fertőtlenítési módszereken, adott esetben a szerveroldalon. Kérjük, vegye figyelembe, hogy a fertőtlenítés, mint biztonsági ellenőrzés csak az utolsó lehetőségnek tekintendő.</p><p>A bemeneti ellenőrzés és a kimeneti kódolás jobb biztonsági ellenőrzésnek tekinthető.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Ne rendeljen DOM-elemeket olyan mosogatókhoz, amelyek nem rendelkeznek beépített kódolással

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Sok javascript függvény alapértelmezés szerint nem végez kódolást. Ha nem megbízható bemenetet rendel a DOM-elemekhez ilyen függvényeken keresztül, az a helyek közötti parancsfájlok (XSS) végrehajtását eredményezheti.| 

### <a name="example"></a>Példa
A következő példák nem biztonságosak: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Ne használja `innerHtml`a; helyette `innerText`használja . Hasonlóképpen, a `$("#elm").html()`helyett használja a`$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Az alkalmazáson belüli összes átirányítás ellenőrzése lezárva vagy biztonságosan elvégezhető

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az OAuth 2.0 engedélyezési keretrendszer – Open Redirectors](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Lépéseket** | <p>A felhasználó által megadott helyre történő átirányítást igénylő alkalmazástervnek a lehetséges átirányítási célokat egy előre meghatározott "biztonságos" helyek vagy tartományok listájára kell korlátoznia. Az alkalmazás ban lévő összes átirányítást le kell zárni/biztonságosnak kell lennie.</p><p>Ehhez tegye a következőket:</p><ul><li>Az összes átirányítás azonosítása</li><li>Valósítsa meg a megfelelő mérséklésminden átirányítás. A megfelelő megoldások közé tartozik az átirányítási engedélyezési lista vagy a felhasználó megerősítése. Ha egy nyitott átirányítási rést tartalmazó webhely vagy szolgáltatás Facebook/OAuth/OpenID identitásszolgáltatókat használ, a támadó ellophatja a felhasználó bejelentkezési jogkivonatát, és megszemélyesítheti a felhasználót. Ez az OAuth használatakor ez a kockázat, amelyet a 6749-es RFC "Az OAuth 2.0 engedélyezési keretrendszer" 10.15 szakasza " Open Redirects" Hasonlóképpen, a felhasználók hitelesítő adatait a nyílt átirányításokat használó adathalász támadások veszélyeztethetik.</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>A Vezérlő metódusai által elfogadott összes karakterlánctípus-paraméter bemeneti érvényesítésének megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [A modelladatok ellenőrzése egy MVC alkalmazásban,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [az Ön ASP.NET MVC alkalmazások ra vonatkozó irányelvei](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Lépéseket** | A primitív adattípust csak a primitív adattípust, a modelleket argumentumként nem tartalmazó metódusok esetében a reguláris kifejezés használatával végzett bemeneti érvényesítést kell elvégezni. Itt Regex.IsMatch kell használni egy érvényes regex mintát. Ha a bemenet nem felel meg a megadott reguláris kifejezésnek, a vezérlés nem haladhat tovább, és megfelelő figyelmeztetést kell megjeleníteni az érvényesítési hibával kapcsolatban.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Állítsa be a reguláris kifejezések feldolgozásának felső határidejét, hogy megakadályozza a DoS-t a hibás regisztrakifejezések miatt

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, Webes űrlapok, MVC5, MVC6  |
| **Attribútumok**              | N/A  |
| **Referencia**              | [DefaultRegexMatchTimeout tulajdonság](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Lépéseket** | A szolgáltatásmegtagadási támadások biztosításához a rosszul létrehozott reguláris kifejezések ellen, amelyek sok visszakövetést okoznak, állítsa be a globális alapértelmezett időtúllépést. Ha a feldolgozási idő hosszabb időt vesz igénybe, mint a megadott felső korlát, akkor időtúllépési kivételt eredményez. Ha semmi sincs konfigurálva, az időkorlát végtelen lesz.| 

### <a name="example"></a>Példa
A következő konfiguráció például egy RegexMatchTimeoutException-et hajt végre, ha a feldolgozás 5 másodpercnél tovább tart: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Kerülje a Html.Raw használatát a Razor nézetekben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| Lépés | ASP.NET a weblapok (Razor) automatikus HTML-kódolást hajtanak végre. A beágyazott kódrögök (@ blokkok) által kinyomtatott összes karakterlánc automatikusan HTML-kódolású lesz. A metódus `HtmlHelper.Raw` meghívásakor azonban olyan jelölést ad vissza, amely nem HTML-kódolású. Ha `Html.Raw()` segítő módszert alkalmaznak, megkerüli a Razor által biztosított automatikus kódolási védelmet.|

### <a name="example"></a>Példa
A következő egy nem biztonságos példa: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Csak akkor `Html.Raw()` használja, ha korrektúrát kell megjelenítenie. Ez a módszer implicit módon nem hajt végre kimeneti kódolást. Használjon más ASP.NET segítőt, pl.`@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Ne használjon dinamikus lekérdezéseket a tárolt eljárásokban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Az SQL-injektálási támadás kihasználja a bemeneti ellenőrzés biztonsági réseit, és tetszőleges parancsokat futtat az adatbázisban. Ez akkor fordulhat elő, ha az alkalmazás bemeneti adatokat használ a dinamikus SQL-utasítások létrehozásához az adatbázis eléréséhez. Ez akkor is előfordulhat, ha a kód olyan tárolt eljárásokat használ, amelyek nyers felhasználói bevitelt tartalmazó karakterláncokat tartalmaznak. Az SQL-injektálási támadás használatával a támadó tetszőleges parancsokat hajthat végre az adatbázisban. Minden SQL utasítást (beleértve a tárolt eljárásokban lévő SQL-utasításokat is) paraméteresíteni kell. A paraméterezett SQL-utasítások gond nélkül elfogadják azokat a karaktereket, amelyek nek különleges jelentése van az SQL számára (például az egyszeres idézőjelnek). |

### <a name="example"></a>Példa
Az alábbiakban egy példa a nem biztonságos dinamikus tárolt eljárás: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Példa
Az alábbiakban ugyanazokat a tárolt eljárást hajtják végre biztonságosan: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Annak ellenőrzése, hogy a modell érvényesítése webes API-metódusokon történik-e

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Modellérvényesítés ASP.NET webes API-ban](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Lépéseket** | Amikor egy ügyfél adatokat küld egy webes API-nak, a feldolgozás előtt kötelező ellenőrizni az adatokat. A ASP.NET webes API-k, amelyek elfogadják modellek bemeneti, használja az adat-annotációk modellek a modell tulajdonságaira vonatkozó érvényesítési szabályok beállításához.|

### <a name="example"></a>Példa
A következő kód ugyanazt mutatja: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Példa
Az API-vezérlők műveletmódszerében a modell érvényességét az alábbiak szerint explicit módon ellenőrizni kell: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>A webes API-metódusok által elfogadott összes karakterlánctípus-paraméter bemeneti érvényesítésének megvalósítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, MVC 5, MVC 6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [A modelladatok ellenőrzése egy MVC alkalmazásban,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [az Ön ASP.NET MVC alkalmazások ra vonatkozó irányelvei](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Lépéseket** | A primitív adattípust csak a primitív adattípust, a modelleket argumentumként nem tartalmazó metódusok esetében a reguláris kifejezés használatával végzett bemeneti érvényesítést kell elvégezni. Itt Regex.IsMatch kell használni egy érvényes regex mintát. Ha a bemenet nem felel meg a megadott reguláris kifejezésnek, a vezérlés nem haladhat tovább, és megfelelő figyelmeztetést kell megjeleníteni az érvényesítési hibával kapcsolatban.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Annak ellenőrzése, hogy a webAPI-ban a típusbiztos paraméterek et használja-e az adatok eléréséhez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>Ha a Paraméterek gyűjteményt használja, az SQL a bemenetet konstans értékként, nem pedig végrehajtható kódként kezeli. A Paraméterek gyűjtemény a bemeneti adatok típus- és hosszkorlátainak kényszerítésére használható. A tartományon kívüli értékek kivételt váltanak ki. Ha nem használ típusbiztos SQL-paramétereket, előfordulhat, hogy a támadók képesek végrehajtani a szűretlen bemenetbe ágyazott injektálási támadásokat.</p><p>Az SQL-lekérdezések létrehozásakor használjon típusbiztonsági paramétereket a szűretlen bevitellel esetlegesen előforduló SQL-injektálási támadások elkerülése érdekében. Típusbiztonsági paramétereket használhat a tárolt eljárásokkal és a dinamikus SQL utasításokkal. A paramétereket az adatbázis konstans értékként kezeli, nem pedig végrehajtható kódként. A paraméterek típusát és hosszát is ellenőrzik.</p>|

### <a name="example"></a>Példa
A következő kód bemutatja, hogyan használhatja a típusbiztonsági paramétereket az SqlParameterCollection-vel egy tárolt eljárás hívásakor. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Az előző kódpéldában a bemeneti érték nem lehet hosszabb 11 karakternél. Ha az adatok nem felelnek meg a paraméter által meghatározott típusnak vagy hossznak, az SqlParameter osztály kivételt okoz. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Paraméterezett SQL-lekérdezések használata a Cosmos DB-hez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az SQL paraméterezés bejelentése az Azure Cosmos DB-ben](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Lépéseket** | Bár az Azure Cosmos DB csak csak az írásvédett lekérdezéseket támogatja, az SQL-injektálás továbbra is lehetséges, ha a lekérdezések felhasználói bevitellel való összefűzéssel készülnek. Előfordulhat, hogy a felhasználó hozzáférhet azokhoz az adatokhoz, amelyekhez nem férhetnek hozzá ugyanazon a gyűjteményen belül rosszindulatú SQL-lekérdezések létrehozásával. Paraméterezett SQL-lekérdezéseket használjon, ha a lekérdezések felhasználói bevitel alapján épülnek fel. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>WCF bemeneti érvényesítéssé sémakötéssel

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, NET keret rendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Lépéseket** | <p>Az érvényesítés hiánya különböző típusú injekciós támadásokhoz vezet.</p><p>Az üzenetérvényesítés egy védelmi vonalat jelent a WCF-alkalmazás védelmében. Ezzel a módszerrel sémák használatával érvényesítheti az üzeneteket a WCF-szolgáltatás műveletek rosszindulatú ügyfél általi támadásokkal szembeni védelme érdekében. Ellenőrizze az ügyfél által fogadott összes üzenetet, hogy megvédje az ügyfelet a rosszindulatú szolgáltatás általi támadástól. Az üzenetek érvényesítése lehetővé teszi az üzenetek érvényesítését, amikor a műveletek üzenetegyezményeket vagy adategyezményeket használnak fel, amelyek paraméter-érvényesítéssel nem végezhetők el. Az üzenetérvényesítés lehetővé teszi érvényesítési logika létrehozását a sémákon belül, ezáltal nagyobb rugalmasságot biztosítva és csökkentve a fejlesztési időt. A sémák újra felhasználhatók a szervezeten belüli különböző alkalmazásokközött, és szabványokat hozhatnak létre az adatábrázoláshoz. Emellett az üzenetek érvényesítése lehetővé teszi a műveletek védelmét, ha összetettebb adattípusokat használnak fel, amelyek üzleti logikát képviselő szerződéseket foglalnak magukban.</p><p>Az üzenetek érvényesítéséhez először hozzon létre egy sémát, amely a szolgáltatás műveleteit és a műveletek által felhasznált adattípusokat jelöli. Ezután hozzon létre egy .NET osztályt, amely egy egyéni ügyfélüzenet-felügyelőt és egyéni diszpécser üzenetellenőrt valósít meg a szolgáltatásba küldött/fogadott üzenetek érvényesítéséhez. Ezután egy egyéni végponti viselkedést valósít meg, amely lehetővé teszi az üzenetek érvényesítését mind az ügyfélen, mind a szolgáltatáson. Végül egy egyéni konfigurációs elemet valósít meg az osztályon, amely lehetővé teszi a kiterjesztett egyéni végpontviselkedés takarását a szolgáltatás vagy az ügyfél konfigurációs fájljában."</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF - Bemeneti ellenőrzés paraméterellenőrökön keresztül

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, NET keret rendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Lépéseket** | <p>A bemeneti és adatérvényesítés a WCF-alkalmazás védelmének egyik fontos védelmi vonalát jelenti. A szolgáltatás rosszindulatú ügyfél általi támadásokkal szembeni védelme érdekében ellenőrizze a WCF-szolgáltatás műveleteiben elérhetővé tett összes paramétert. Ezzel szemben az ügyfél által kapott összes visszatérési értéket is ellenőriznie kell, hogy megvédje az ügyfelet a rosszindulatú szolgáltatás általi támadástól.</p><p>A WCF különböző bővíthetőségi pontokat biztosít, amelyek lehetővé teszik a WCF futásidejű viselkedésének testreszabását egyéni bővítmények létrehozásával. Az üzenetellenőrök és a paraméterellenőrök két bővíthetőségi mechanizmus, amelyek az ügyfél és a szolgáltatás közötti adatok nagyobb ellenőrzésének megszerzésére szolgálnak. Csak akkor használjon paraméterellenőröket a bemeneti érvényesítéshez, és csak akkor használja az üzenetellenőröket, ha a teljes, a szolgáltatásból be- és kiáramló üzenetet meg kell vizsgálnia.</p><p>A bemeneti ellenőrzés végrehajtásához hozzon létre egy .NET osztályt, és valósítson meg egy egyéni paraméterellenőrt a szolgáltatásban lévő műveletek paramétereinek érvényesítéséhez. Ezután egy egyéni végpontviselkedést valósít meg, amely lehetővé teszi az érvényesítést mind az ügyfélen, mind a szolgáltatáson. Végül egy egyéni konfigurációs elemet valósít meg az osztályon, amely lehetővé teszi a kiterjesztett egyéni végpontviselkedés takarását a szolgáltatás vagy az ügyfél konfigurációs fájljában.</p>|
