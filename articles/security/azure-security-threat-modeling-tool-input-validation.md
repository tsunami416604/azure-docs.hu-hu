---
title: "-Microsoft fenyegetés modellezési eszköz - érvényesítési Azure bemeneti |} Microsoft Docs"
description: "a fenyegetések modellezése eszköz felfedett fenyegetések megoldást"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: c0d90f7c6ad136cd1a558f6158cf734de51b9538
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="security-frame-input-validation--mitigations"></a>Biztonsági keret: Bemeneti ellenőrzés |} Megoldást 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[A nem megbízható stíluslapok használó összes átalakítások scripting XSLT letiltása](#disable-xslt)</li><li>[Győződjön meg arról, hogy minden felhasználó vezérelhető tartalmat sikerült tartalmazó oldalon jelentésküldési kívül automatikus MIME-elemzése](#out-sniffing)</li><li>[Alakuljanak vagy XML entitás tiltása](#xml-resolution)</li><li>[Alkalmazások használata a http.sys hajtsa végre az URL-cím kanonizálási ellenőrzése](#app-verification)</li><li>[Győződjön meg arról, megfelelő vezérlők érvényben vannak, amikor a felhasználók fájlokat elfogadása](#controls-users)</li><li>[Győződjön meg arról, hogy biztonságos típusú paraméterek használ a webalkalmazás adatelérési](#typesafe)</li><li>[Használjon külön modell kötés osztályok vagy kötés szűrő listázza az MVC tömeges hozzárendelés biztonsági rés megelőzése érdekében](#binding-mvc)</li><li>[Nem megbízható webes kimeneti megjelenítés előtt kódolása](#rendering)</li><li>[Hajtsa végre a bemenet-ellenőrzéshez és a szűrést az összes karakterlánc típusú modell tulajdonságai](#typemodel)</li><li>[Tisztítási mezőknek, amelyekben az összes karakterek, például azt, rich text editor alkalmazandó](#richtext)</li><li>[Ne rendeljen DOM elemek mosdók, amely nem rendelkezik beépített kódolás](#inbuilt-encode)</li><li>[Ellenőrizheti az alkalmazáson belül átirányítások lezárt vagy biztonságosan történik](#redirect-safe)</li><li>[Megvalósítása bemenet-ellenőrzést az összes karakterlánc típusú paramétert fogadja el a tartományvezérlő módszerek](#string-method)</li><li>[Beállítani felső korlátja időkorlátját reguláris kifejezés feldolgozása DoS megelőzése miatt hibás reguláris kifejezések](#dos-expression)</li><li>[Kerülje a Html.Raw Razor nézetekben](#html-razor)</li></ul> | 
| **Adatbázis** | <ul><li>[Ne használjon dinamikus lekérdezések a tárolt eljárások](#stored-proc)</li></ul> |
| **Webes API** | <ul><li>[Arról, hogy Modellellenőrzés a webes API-módszer](#validation-api)</li><li>[Megvalósítása bemenet-ellenőrzést az összes karakterlánc típusú paraméterek fogadja el a webes API-módszer](#string-api)</li><li>[Ellenőrizze, hogy biztonságos típusú paraméterek szolgáltatás webes API-ban az adatelérési](#typesafe-api)</li></ul> | 
| **Az Azure Document DB rendszerbe** | <ul><li>[Azure Cosmos DB paraméteres SQL-lekérdezések használata](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF bemeneti érvényesítési séma kötését keresztül](#schema-binding)</li><li>[WCF - bemenet érvényesítési paraméter ellenőrök keresztül](#parameters)</li></ul> |

## <a id="disable-xslt"></a>A nem megbízható stíluslapok használó összes átalakítások scripting XSLT letiltása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [XSLT biztonsági](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript tulajdonsággal](http://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Lépések** | XSLT támogatja a parancsfájlok használatával stíluslapok belül a `<msxml:script>` elemet. Ez lehetővé teszi az XSLT-átalakítás használandó egyéni függvényekhez. A parancsfájl végrehajtása a végrehajtása az átalakítási folyamat alatt. XSLT-parancsfájl egy nem megbízható környezetbe, hogy a nem megbízható kód végrehajtását, amely le kell tiltani. *Ha a .NET használatával:* XSLT-parancsfájlok alapértelmezés szerint le van tiltva; azonban győződjön meg arról, hogy nincs explicit módon Engedélyezés keresztül a `XsltSettings.EnableScript` tulajdonság.|

### <a name="example"></a>Példa 

```C#
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Példa
Ha használja az MSXML 6.0 használatával, XSLT-scripting alapértelmezésben le van tiltva; azonban úgy kell beállítania, hogy azt nem explicit módon engedélyezve van az XML DOM objektum AllowXsltScript tulajdonságon keresztül. 

```C#
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Példa
Ha az MSXML 5 használ, vagy az alábbi XSLT-parancsfájlok alapértelmezés szerint engedélyezve van, és explicit módon kell tiltsa le azt. Az XML DOM-objektum tulajdonság AllowXsltScript hamis értékre állítható be. 

```C#
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Győződjön meg arról, hogy minden felhasználó vezérelhető tartalmat sikerült tartalmazó oldalon jelentésküldési kívül automatikus MIME-elemzése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [IE8 Biztonsági rész - átfogó védelmének](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Lépések** | <p>Az egyes lapok tartalmazhatnak felhasználói ellenőrizhető tartalmat, használnia kell a HTTP-fejléc `X-Content-Type-Options:nosniff`. Felel meg ennek a követelménynek, vagy beállítható a kötelező fejlécet oldalanként csak a felhasználó vezérelhető tartalmat tartalmazó lapok, vagy beállíthatja azt globálisan az alkalmazás összes lapja esetében.</p><p>Egy webkiszolgáló-i fájltípusok tartozik egy [MIME-típus](http://en.wikipedia.org/wiki/Mime_type) (más néven a *tartalomtípus*), amely leírja, hogy a tartalom (Ez azt jelenti, kép, szöveg, alkalmazás, stb.) jellege</p><p>Az X-tartalom-típust-beállítások fejlécet, amely lehetővé teszi a fejlesztők számára adja meg, hogy a tartalmat nem kell MIME-felszippantásra HTTP-fejléc. Ezt a fejlécet védelme az MIME-elemzés célja. Ezt a fejlécet támogatása az Internet Explorer 8 (IE8) lett hozzáadva.</p><p>Csak az Internet Explorer 8 (IE8) felhasználók élvezi, az X-tartalom-típust-beállítások. Az Internet Explorer korábbi verzióiban jelenleg nem veszik figyelembe az X-tartalom-típust-beállítások fejléc</p><p>Internet Explorer 8 (és újabb) rendszer megvalósításához a MIME-elemzés lemondáshoz szolgáltatást csak ismertebb böngésző. Ha más ismertebb böngésző (Firefox, Safari, Chrome) valósít meg hasonló funkciókat, ez a javaslat frissíti tartalmazza azokat a böngészőket szintaxisa</p>|

### <a name="example"></a>Példa
A kötelező fejlécet globálisan az alkalmazás összes lapja esetében engedélyezni, tegye a következők egyikét: 

* A fejléc hozzáadása a web.config fájlban, ha az alkalmazás által az Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* A globális alkalmazáson keresztül fejléc hozzáadása\_BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Egyéni HTTP-modulja megvalósítása 

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

* A szükséges fejléc csak az adott lapok egyes válaszok való hozzáadással engedélyezheti: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Alakuljanak vagy XML entitás tiltása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [XML entitás bővítése](http://capec.mitre.org/data/definitions/197.html), [XML szolgáltatásmegtagadási támadások és Védelmekkel](http://msdn.microsoft.com/magazine/ee335713.aspx), [MSXML biztonsági áttekintése](http://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [gyakorlati tanácsok biztonságossá tétele az MSXML kód](http://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ NSXMLParserDelegate protokoll referenciája](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [külső hivatkozások feloldása](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Lépések**| <p>Bár nem elterjedt, nem az XML-kód, amely lehetővé teszi az XML-elemző makró entitások kibontásához a dokumentum törzsében vagy külső forrásból definiált értékekkel. Meghatározhatja például, a dokumentum előfordulhat, hogy egy entitás "#companyname" a "Microsoft" értékű, amely minden alkalommal a szöveges "&companyname;" jelenik meg a dokumentum automatikusan helyébe a következő szöveggel: Microsoft. Vagy a dokumentum "MSFTStock" beolvasása a Microsoft-készlet aktuális értéke külső webszolgáltatásokból hivatkozó entitás határozhat meg.</p><p>Bármikor majd "&MSFTStock;" jelenik meg a dokumentum, a program automatikusan felülírja az aktuális árfolyam. Azonban ez a funkció keresztül szolgáltatásmegtagadási (DoS) szolgáltatási feltételek. Egy támadó ágyazhatja több entitás egy exponenciális bővítése XML bomba, amely az összes rendelkezésre álló memória, a rendszer akkor létrehozásához. </p><p>Azt is megteheti hogy hozhat létre egy külső hivatkozást, amely az adatfolyamokat, vissza végtelen adatmennyiséget, vagy egyszerűen lefagy a szál. Ennek eredményeképpen az összes csoport le kell tiltania a belső vagy külső XML entitás feloldási teljesen, ha az alkalmazás nem használja, vagy manuálisan korlátozásához a memória és az alkalmazás a entitás feloldásához vehet igénybe, ha ez a funkció időt feltétlenül szükséges. Ha az alkalmazás nem követeli meg entitás megoldás, majd tiltsa le azt. </p>|

### <a name="example"></a>Példa
.NET-keretrendszer kódot az alábbi megközelítések is használhatja:

```C#
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
Vegye figyelembe, hogy az alapértelmezett érték `ProhibitDtd` a `XmlReaderSettings` értéke igaz, de a `XmlTextReader` azt értéke false. Ha XmlReaderSettings használ, nem ProhibitDtd igaz explicit módon be kell, de ajánlott biztonsági szakét, hogy végezzen. Ne feledje, hogy engedélyezi-e a XmlDocument osztály entitás feloldási alapértelmezés szerint. 

### <a name="example"></a>Példa
XmlDocuments entitás feloldási letiltásához használja a `XmlDocument.Load(XmlReader)` túlterhelés a Load metódus, és állítsa be a megfelelő tulajdonságokat XmlReader argumentumában tiltása, a következőkben ismertetett módon: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Példa
Ha a letiltása entitás feloldása nem lehetséges, hogy az alkalmazás, a XmlReaderSettings.MaxCharactersFromEntities tulajdonsága egy ésszerű értéket az alkalmazás igényeinek megfelelően. Ez korlátozza az exponenciális bővítése DoS potenciális támadások káros hatása. Az alábbi kód ezt a módszert mutatja: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Példa
Ha szeretné feloldani beágyazott entitásokat, de tegye nem szükséges külső entitások, oldja meg a XmlReaderSettings.XmlResolver tulajdonság állítható be null értékre. Példa: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Vegye figyelembe, hogy az MSXML6, ProhibitDTD igaz értékre van beállítva (a DTD feldolgozásának letiltása) alapértelmezés szerint. Apple OSX/iOS kód nincsenek használható két XML elemzők: NSXMLParser és libXML2. 

## <a id="app-verification"></a>Alkalmazások használata a http.sys hajtsa végre az URL-cím kanonizálási ellenőrzése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>A http.sys használó összes alkalmazásra kell kövesse az alábbi irányelveket:</p><ul><li>Korlátozza az URL-cím hossza legfeljebb 16 384 karakter (ASCII vagy Unicode). Ez egy abszolút URL-cím legfeljebb alapértelmezett Internet Information Services (IIS) 6 beállítás alapján. Webhelyek törekedni kell egy rövidebb, mint ez a lehetőség szerint</li><li>Ezek fogják kihasználni a kanonizálási szabály a .NET FX a használhatja a szabványos .NET-keretrendszer fájl i/o-osztály (például a FileStream)</li><li>Explicit módon az egy ismert fájlnevek engedélyezési-lista létrehozása</li><li>Kifejezetten elutasítja az ismert fájl típusai nem teljesíti a UrlScan elutasítások: exe, bat, cmd, com, IDA, ida, idq, htr, idc, shtm [l], stm, nyomtató, ini, pol, dat fájlok</li><li>A következő kivételeket:<ul><li>System.ArgumentException (az eszköz neve)</li><li>(Az adatfolyamokat) System.NotSupportedException</li><li>System.IO.FileNotFoundException (a érvénytelen escape-karakterrel megjelölt fájlnév)</li><li>(A érvénytelen escape-karakterrel megjelölt könyvtárak) System.IO.DirectoryNotFoundException</li></ul></li><li>*Ne* Win32 fájl i/o-API hívásához. Az egy URL-cím érvénytelen szabályosan adja vissza egy 400 hiba a felhasználó, és a valós hiba jelentkezik.</li></ul>|

## <a id="controls-users"></a>Győződjön meg arról, megfelelő vezérlők érvényben vannak, amikor a felhasználók fájlokat elfogadása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Fájl feltöltése korlátozás](https://www.owasp.org/index.php/Unrestricted_File_Upload), [aláírás tábla](http://www.garykessler.net/library/file_sigs.html) |
| **Lépések** | <p>Feltöltött fájlok alkalmazások jelentős kockázatot jelentenek.</p><p>Számos támadás első lépése, hogy a rendszer megtámadott kell néhány kód letöltése. Ezután a támadás csak meg kell keresnie a kód végrehajtott beolvasni. A fájlfeltöltés segítségével a támadó az első lépés elvégzését. A következmények korlátlan fájlfeltöltés változhat, beleértve a teljes rendszer felvásárlási, túlterhelt fájlrendszer vagy adatbázis, a háttér-rendszereket, és egyszerű felülírása támadások továbbítás.</p><p>Ez függ az alkalmazás funkciója a feltöltött fájl, és különösen helyén. Kiszolgáló-oldali ellenőrzés fájlfeltöltéseket hiányzik. A következő biztonsági vezérlők fájlfeltöltés funkciójának kell végrehajtani:</p><ul><li>Fájl kiterjesztése ellenőrzése (csak engedélyezett fájltípus érvényes meghatározott fogadhatók el)</li><li>Maximális méretkorlátot.</li><li>Fájl nem fel kell tölteni webroot; a hely egy könyvtár a rendszert nem tartalmazó meghajtón kell lennie.</li><li>Elnevezési konvenciót kell követni, úgy, hogy a fájl feltöltése névnek kell néhány annak megelőzésére fájl véletlenszerű felülírja</li><li>Fájlok kell futtatni víruskereső írása a lemezre előtt</li><li>Győződjön meg arról, hogy a fájl nevét és minden egyéb metaadatot (például a fájl elérési útja) rosszindulatú karakterek érvényesítése</li><li>Fájlaláírás formátum ellenőrizni kell, ezáltal megakadályozhatja, hogy a felhasználó egy masqueraded feltöltés (pl. egy exe fájl feltöltése a txt kiterjesztés módosításával)</li></ul>| 

### <a name="example"></a>Példa
A legutóbbi pont fájl formátumának aláírás érvényesítése kapcsolatban tekintse meg az osztály alábbi részleteket: 

```C#
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

## <a id="typesafe"></a>Győződjön meg arról, hogy biztonságos típusú paraméterek használ a webalkalmazás adatelérési

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Ha használja a paramétergyűjtemény, SQL kezeli a bemeneti van, mint végrehajtható kód, nem konstans érték. A paraméterek gyűjteményében segítségével érvényesíthetők bemeneti adatok típusát és a hossz korlátozások. A tartományon kívül eső értékeket kivételt vált. Típus környezetben is biztonságos SQL-paraméterek nem használják, ha a támadók hajtható végre a szűretlen bemeneti beágyazott injektálási támadások lehet.</p><p>Biztonságos Típusparaméterek használja az SQL-lekérdezések térített lehetséges SQL injektálási támadásokkal szemben, amelyek alakulhat ki nem szűrt bemeneti elkerülése érdekében. Biztonságos Típusparaméterek is használhatja, tárolt eljárások és a dinamikus SQL-utasításokat. Paraméterek az adatbázis által literálértékek, nem pedig végrehajtható kód kell kezelni. Paraméterek típusa és hossza is ellenőrzi.</p>|

### <a name="example"></a>Példa 
A következő kód bemutatja, hogyan használható a SqlParameterCollection biztonságos Típusparaméterek tárolt eljárás meghívásakor. 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Az előző példakódban a bemeneti érték nem lehet hosszabb, mint 11 karakter. Ha az adatok nem felelnek meg a típus vagy paraméter által meghatározott, a SqlParameter osztály kivételt jelez. 

## <a id="binding-mvc"></a>Használjon külön modell kötés osztályok vagy kötés szűrő listázza az MVC tömeges hozzárendelés biztonsági rés megelőzése érdekében

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Metaadat-attribútumok](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [nyilvános kulcs biztonsági biztonsági rés és a megoldás](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [háttértár-hozzárendelést az ASP.NET MVC teljes körű útmutatót](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [MVC használatával EF első lépések](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Lépések** | <ul><li>**Mikor kell keresni túlküldéses biztonsági rések? -** Túlküldéses biztonsági rések modell osztályok kötést létrehozni a felhasználói bevitel bárhol fordulhat. Például MVC keretrendszerek jelenthet a felhasználói adatok .NET egyéni osztályokat, beleértve az egyszerű régi CLR objektumok (POCOs). MVC automatikusan tölti fel ezeket az adatokat a kérésből modell osztályokat biztosít a felhasználói bevitel kapcsolatos kényelmes megjelenítése. Ezeket az osztályokat tartalmazzák a felhasználó nem állítható tulajdonságokat, ha az alkalmazás ki van téve a túlküldéses támadások, amelyek lehetővé teszik az adatok, az alkalmazás soha nem célja a felhasználói vezérlő lehet. MVC modellkötést, például adatbázis technológiák, például az Entity Framework objektum relációs mappers például gyakran is támogatás igénybe vételét POCO objektumok adatbázis adatok használatával. Ezen adatok modell osztályok adja meg az adatbázis adatok MVC nem felhasználói bevitel kezelésével foglalkozó azonos kényelmi. Mert MVC és az adatbázis is támogatja a hasonló modellek POCO objektumok, például tűnik könnyen újra felhasználhatja a azonos osztályok mindkét célokra. Ez az eljárás nem tudja aggályokat elkülönítése megőrzése, és egy közös területen, ahol nem kívánt tulajdonságai vannak kitéve modellkötést túlzott könyvelési támadások engedélyezése.</li><li>**Miért nem használható a modell nem szűrt adatbázisosztályokkal paraméterként a saját MVC műveletek? -** Mert MVC modellkötést köti, semmit a szóban forgó osztályban. Akkor is, ha az adatok nem jelenik meg a nézet, egy rosszindulatú felhasználó képes HTTP-kérelem elküldeni ezeket az adatokat tartalmazza, és az MVC legszívesebben köti, mert a művelet szerint a adatbázis osztály az alakzat, hogy fogadja el a felhasználói adatok.</li><li>**Miért érdemes az alakzat modell kötések létrehozására használt érdeklik? -** Modellkötést használ az ASP.NET MVC túlságosan széleskörű modellekkel való közzétesz egy alkalmazást túlküldéses támadások. Túlzott könyvelési lehetővé teszi a támadók milyen a fejlesztői szánt, például az ár egy elem vagy egy olyan fiók biztonsági jogosultságokkal felülbírálása túl adatainak módosítása. Alkalmazások művelet-specifikus modellek (vagy adott engedélyezett szűrő tulajdonságlisták) explicit megadását kötés szerződés milyen nem megbízható bemenet modellkötést keresztül kell használnia.</li><li>**Csak a kód duplikálását külön kötést modellek nehézségekkel? -** Nem, akkor aggályokat elkülönítését pár. Ha adatbázis modellek műveletmetódusokhoz szeretné újrafelhasználni, akkor véleményét bármely (vagy az alárendelt tulajdonság) abban, hogy az osztály egy HTTP-kérelem a felhasználó által állítható be. Ez nem ajánlott MVC a teendő, ha szüksége szűrőlista vagy egy külön osztály alakzat MVC milyen adatok származhatnak felhasználói bevitel helyette.</li><li>**A felhasználói bevitelhez külön kötést modellek van, ha rendelkezik saját adatok jegyzet attribútumok ismétlődő? -** Nem feltétlenül. Az adatbázis-modell osztály MetadataTypeAttribute segítségével a metaadatokat egy modellosztállyal kötés kapcsolódik. Csak vegye figyelembe, hogy a MetadataTypeAttribute által hivatkozott típus (rendelkezhet kevesebb tulajdonság, de nem lehet több) hivatkozó típusú részkészletének kell lennie.</li><li>**Adatok áthelyezése oda-vissza felhasználói bemeneti modellek és adatbázis-modellek között fárasztó. Is szeretnék csak másolja át az összes tulajdonság a következőt reflexió használatával? -** Igen. A kötés modellek megjelenő csak tulajdonságok biztonsága a felhasználói bevitelhez megadta, hogy azok. Nincs, amely megakadályozza a reflexió használatával másolja át az összes tulajdonságai között két modell közös létező biztonsági OK.</li><li>**Mi a helyzet [kötése (kizárása = "â" ¦")]. Használható, amelyek ahelyett, hogy külön kötést modellek? -** Ez a módszer nem ajánlott. Használatával [kötése (kizárása = "â" ¦")] azt jelenti, hogy minden új tulajdonság köthető alapértelmezés szerint. Amikor új tulajdonság ad hozzá, egy további lépést megjegyezhető dolgot biztonságban van, nem pedig a Tervező képezniük alapértelmezés szerint biztonságos. Attól függően, hogy a fejlesztő a lista ellenőrzése minden alkalommal, amikor egy tulajdonság hozzáadása kockázatos.</li><li>**Van [kötése (Belefoglalás = "â" ¦")] Szerkesztés műveletekhez hasznos? -** Nem. [Kötése (Belefoglalás = "â" ¦")] csak alkalmas INSERT stílusú operations (az új adatok hozzáadása). A frissítés stílusú műveleteket (a meglévő adatok módosítása), egy másik módszert használja, például hogy külön kötést modellek vagy tulajdonságok engedélyezett explicit listáját átadni UpdateModel vagy TryUpdateModel. Hozzáadása egy [kötése (Belefoglalás = ":" ¦")] attribútum szerkesztési művelet azt jelenti, hogy MVC fog egy objektumpéldányra beállítása csak a felsorolt tulajdonságokat, a többi alapértelmezett értékükön hagyja. Amikor az adatok, azzal lecseréli a meglévő entitás-, a gyári beállítások visszaállításával minden elhagyott tulajdonságok értékeit az alapértelmezett teljes egészében. Például, ha IsAdmin kimaradt egy [kötése (Belefoglalás = ":" ¦")] attribútum szerkesztési művelet esetén minden olyan felhasználó, amelynek a neve szerkesztették keresztül ez a művelet vissza kellene állítani a IsAdmin = false (bármely módosított felhasználói elveszítik rendszergazdai állapota). Ha meg szeretné akadályozni, hogy a frissítések bizonyos tulajdonságokra, használja a fenti más megoldások egyikét. Vegye figyelembe, hogy egyes verziói MVC tooling készítése tartalmazó vezérlő osztályok [kötése (Belefoglalás = ":" ¦")] műveletek szerkesztése és utalnak, hogy a lista alapján tulajdonság eltávolítása megakadályozza a túlzott könyvelési támadásokat. Azonban fentiekben ismertetettek szerint ezt a megközelítést nem működik megfelelően, és helyette alaphelyzetbe egyetlen megadott adattal sem a nincs megadva tulajdonságok az alapértelmezett értékekre.</li><li>**A Create műveleteket, vannak-e bármilyen figyelmeztetések segítségével [kötése (Belefoglalás = "â" ¦")] ahelyett, hogy külön kötést modellek? -** Igen. Ez a megközelítés először nem működik Szerkesztés forgatókönyvek esetén igénylő összes túlzott könyvelési biztonsági rés kiküszöböléséhez két külön megközelítés karbantartása. Második, különálló kötés modellek válassza szét az alakzat használt felhasználói bevitel és az adatmegőrzési, alakzatot közötti kérdések valami [kötése (Belefoglalás = "â" ¦")] nem. Harmadik, vegye figyelembe, hogy [kötése (Belefoglalás = "â" ¦")] kezelni tud a csak legfelső szintű tulajdonságok; az attribútum nem engedélyezheti a alárendelt tulajdonságait (például "Details.Name") csak egy részeit. Végül, és lehet, hogy a legfontosabb, használja [kötése (Belefoglalás = "â" ¦")] hozzáadja egy további lépést, amely a osztály bármikor veszni modellkötést szolgál. Ha egy új műveletmetódus közvetlenül az adatok osztály kötődik, és felvenni elfelejti a [kötési (Include = ":" ¦")] attribútum, azok ki van téve a túlküldéses támadások, ezért a [kötése (Include =": "¦")] Alapértelmezés szerint kevésbé biztonságos megoldás. Használatakor [kötése (Belefoglalás = "â" ¦")], ügyeljen arra mindig, ne felejtse el megadni, akkor minden alkalommal, amikor a adatosztályokat művelet a metódusok paramétereihez jelennek meg.</li><li>**A létrehozás műveleteket, mi a helyzet üzembe a [kötése (Belefoglalás = "â" ¦")] a modell osztály attribútuma? Nem ez a módszer nem kelljen jegyezze meg a attribútum elhelyezése minden tartozó műveleti módszer? -** Ezt a módszert használja bizonyos esetekben működik. Használatával [kötése (Include = "â" ¦")] a modell típus saját magát (és nem a művelet paramétereinek Ez az osztály használatával), kelljen ne felejtse el bevenni a [kötése (Include ="â "¦")] attribútumot minden tartozó műveleti módszer. Az attribútum az osztály a közvetlenül elkülöníti a egy külön felületének Ez az osztály a modell adatkötéshez. Azonban ez a megközelítés csak egy modell kötés alakzat / modellosztállyal lehetővé teszi. Ha egy tartozó műveleti módszer kell (például egy csak rendszergazdai műveletet, amely frissíti a felhasználói szerepkörök) mező modellkötést engedélyezése és egyéb műveleteket kell ennek a mezőnek modellkötést akadályozni, ez a módszer nem fog működni. Minden osztály csak rendelkezhet egy modell kötés alakzat; Ha különböző műveleteket kell másik modellhez kötés alakzatok, szükségük vagy külön modell kötés osztályokat külön alakzatok képviseli, vagy külön [kötése (Belefoglalás = "â" ¦")] attribútumok használatát a műveletmetódusokhoz.</li><li>**Mi köti modellek? Ugyanaz, mint a modellek vannak? -** Ezek a két kapcsolódó fogalmak. A kötés modell hivatkozik egy modell művelet használt osztály kifejezés paraméterlista (az alakzat származó MVC a művelet metódusnak átadott). A kifejezés nézet modell egy nézet egy művelet metódusból átadott modell osztályra hivatkozik. Egy nézet-specifikus modell használata egy általánosan használt megközelítés beállítását adatok átadására egy művelet metódus nézetre. Gyakran ez az alakzat is modellkötést alkalmas, és a kifejezés nézet modell segítségével tekintse meg a ugyanannak a modellnek mindkét helyen használja. Ahhoz, hogy pontos, ezzel az eljárással beszél kifejezetten modellek az alakzat, a művelet, amely fontos információk tömeges hozzárendelés céljából átadott összpontosító kötés.</li></ul>| 

## <a id="rendering"></a>Nem megbízható webes kimeneti megjelenítés előtt kódolása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, Web Forms, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [ASP.NET-parancsainak többhelyes megakadályozása](http://msdn.microsoft.com/library/ms998274.aspx), [parancsfájlok](http://cwe.mitre.org/data/definitions/79.html), [lehetővé (helyközi telephelyek közötti) megelőzési Cheat lap](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Lépések** | Az online szolgáltatások vagy a bármely alkalmazás vagy összetevő feldolgozó bemeneti az internetről felhasználók támadási vektorainak többhelyes parancsfájl-kezelési (általában lehetővé rövidítése). Lehetővé biztonsági rések előfordulhat, hogy egy támadó végrehajtani a parancsprogramot a másik felhasználó sebezhető webes alkalmazás segítségével. Rosszindulatú parancsfájlok segítségével ellopni a cookie-k és egyéb illetéktelenül módosítani a áldozat géppel JavaScript keresztül. Felhasználói bevitel ellenőrzése, győződjön meg arról, hogy helyes formátumú-e, és egy weblapon megjelenítése előtt kódolás lehetővé miatt. Bemenet-ellenőrzéshez és a kimeneti kódolás webes védelmi könyvtár használatával elvégezhető. A felügyelt kód (C\#, VB.net stb.), használjon egy vagy több lehetséges, hogy a webalkalmazás-védelmi (kártevőirtó-lehetővé) könyvtárból, attól függően, hogy a környezetben, ahol lekérdezi lemezegység jelenik meg a felhasználó által megadott kódolási módszert:| 

### <a name="example"></a>Példa

```C#
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

## <a id="typemodel"></a>Hajtsa végre a bemenet-ellenőrzéshez és a szűrést az összes karakterlánc típusú modell tulajdonságai

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Érvényesítési hozzáadása](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [modell az adatok egy MVC alkalmazás](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [irányadó elvek az ASP.NET MVC-alkalmazások](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Lépések** | <p>A bemeneti paraméterek ellenőrizni kell, mielőtt győződjön meg arról, hogy az alkalmazás elleni rosszindulatú felhasználói bevitelek megőrizzék használhatók az alkalmazásban. Ellenőrizze a bemeneti értékek reguláris kifejezés érvényesítést egy engedélyezési lista érvényesítési biztosításával kapcsolatos stratégia kiszolgáló oldalán. Felhasználói bevitel unsanitized / átadott, a metódusok okozhat kód injektálási biztonsági réseket.</p><p>A webes alkalmazásokhoz, a belépési pontok is tartalmazhatnak, űrlapmezők, a QueryStrings, a cookie-k, a HTTP-fejlécek és a webszolgáltatás-paramétereket.</p><p>A következő bemeneti érvényességi ellenőrzéseket modellkötést esetén kell végrehajtani:</p><ul><li>A modell tulajdonságai kell feliratozni a reguláris kifejezés megjegyzés, a megengedett karakterek és a maximális megengedett hossz</li><li>A vezérlő metódusokhoz végre kell hajtania ModelState érvényességi</li></ul>|

## <a id="richtext"></a>Tisztítási mezőknek, amelyekben az összes karakterek, például azt, rich text editor alkalmazandó

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Nem biztonságos bemenet kódolása](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML Sanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Lépések** | <p>Határozza meg az összes statikus markup címkéket szeretné használni. Egy általános gyakorlat az, hogy korlátozása a biztonságos HTML-elem, például a formázást `<b>` (félkövér) és `<i>` (dőlt).</p><p>Az adatok, HTML-kódolja írása előtt azt. Ez megnehezíti a rosszindulatú parancsfájlok biztonságos által szövegként, nem pedig a végrehajtható kód kezelni azt.</p><ol><li>Tiltsa le a kérelem ellenőrzése ASP.NET adja hozzá a ValidateRequest = "false" attribútumot a @ lap direktívájában</li><li>A karakterlánc-bemenet kódolása HtmlEncode módszer</li><li>A StringBuilder használja, és hívja meg az a név felülírandó metódust szelektív eltávolítása a HTML-elem van, hogy engedélyezni szeretné a kódolás</li></ol><p>A lap a hivatkozások letiltja az ASP.NET kérelem érvényesítési úgy, hogy `ValidateRequest="false"`. Azt a bemeneti HTML-kódolja és szelektív módon lehetővé teszi, hogy a `<b>` és `<i>` azt is megteheti, a .NET könyvtár az HTML tisztítási is használható.</p><p>HtmlSanitizer a .NET-szalagtár egyik HTML-töredék és dokumentumok szerkezetek lehetővé támadások vezethet. AngleSharp elemezni, módosíthatók és HTML és CSS megjelenítéséhez használ. HtmlSanitizer NuGet-csomagként telepíthető, és a felhasználó által megadott is átadható a HTML- vagy CSS tisztítási módszerekről, amennyiben alkalmazható, a kiszolgáló oldalán. Vegye figyelembe a biztonsági vezérlőként tisztítási csak utolsó lehetőségként kell figyelembe venni.</p><p>Bemenet-ellenőrzéshez és a kimeneti kódolás minősülnek jobb biztonsági vezérlők.</p> |

## <a id="inbuilt-encode"></a>Ne rendeljen DOM elemek mosdók, amely nem rendelkezik beépített kódolás

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Sok javascript-funkcióként nem a kódolást alapértelmezés szerint. Nem megbízható bemenet ilyen funkciókon keresztül DOM elemek hozzárendelésekor hely parancsfájl (lehetővé) végrehajtások közötti eredményezhet.| 

### <a name="example"></a>Példa
Az alábbiakban nem biztonságos példák: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Ne használjon `innerHtml`; Ehelyett használjon `innerText`. Hasonlóképpen, ahelyett, hogy `$("#elm").html()`, használata`$("#elm").text()` 

## <a id="redirect-safe"></a>Ellenőrizheti az alkalmazáson belül átirányítások lezárt vagy biztonságosan történik

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [OAuth 2.0 hitelesítési keretrendszer - nyitott átirányító](http://tools.ietf.org/html/rfc6749#section-10.15) |
| **Lépések** | <p>Egy felhasználó által megadott helyre való átirányítást igénylő alkalmazás-Tervező kell korlátozni a lehetséges átirányítási célok helyeket vagy tartományokat előre definiált "biztonságos" listája. Az alkalmazás összes átirányítások zárva vagy biztonságos kell lennie.</p><p>Ehhez tegye a következőket:</p><ul><li>Minden átirányítások azonosítása</li><li>Minden átirányítási egy megfelelő megoldás megvalósításához. Megfelelő megoldást átirányítási engedélyezési lista vagy a felhasználó a jóváhagyás tartalmazza. Ha egy webhely vagy a szolgáltatást, amely egy nyitott átirányítási biztonsági rés használja a Facebook/OAuth/OpenID Identitásszolgáltatók, egy támadó ellopni a felhasználó bejelentkezési jogkivonatot, és, hogy a felhasználó megszemélyesítése. Ez egy járó kockázat használata esetén OAuth, amelyek az RFC 6749 "Az OAuth 2.0 hitelesítési keretrendszer" ismertetett, 10.15 "nyissa meg a átirányítja a felhasználókat" szakasz hasonlóan, a felhasználói hitelesítő adatok sérülhet a rendszer által célzott adathalász támadások nyitott átirányítások használatával</li></ul>|

## <a id="string-method"></a>Megvalósítása bemenet-ellenőrzést az összes karakterlánc típusú paramétert fogadja el a tartományvezérlő módszerek

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [A modell adatai egy MVC alkalmazás ellenőrzése](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [irányadó elvek az ASP.NET MVC-alkalmazások](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Lépések** | Elfogadó csak primitív adattípus és nem a modell argumentumként módszerek reguláris kifejezés használatával bemenet-ellenőrzést kell végezni. Itt Regex.IsMatch használandó érvényes reguláris kifejezéssel mintával. Ha a bemeneti adatok nem egyezik a megadott reguláris kifejezés, vezérlő nem kell végrehajtásának folytatásához, és egy érvényesítési hiba megfelelő figyelmeztetés üzenetnek kell megjelennie.| 

## <a id="dos-expression"></a>Beállítani felső korlátja időkorlátját reguláris kifejezés feldolgozása DoS megelőzése miatt hibás reguláris kifejezések

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, Web Forms, MVC5, MVC6  |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [DefaultRegexMatchTimeout tulajdonság](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Lépések** | Annak biztosítása érdekében a szolgáltatásmegtagadási támadások elleni rosszul létrehozott reguláris kifejezések, nagy mennyiségű meglátogassa okozó beállítani globális alapértelmezett időkorlátját. A feldolgozási időt tovább tart, mint a megadott felső korlátja, ha azt szeretné throw egy időtúllépési kivétel. Ha semmi van konfigurálva, az időtúllépés végtelen lesz.| 

### <a name="example"></a>Példa
Például a következő konfigurációs kivételhibát egy RegexMatchTimeoutException, ha a feldolgozási 5 másodpercnél: 

```C#
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Kerülje a Html.Raw Razor nézetekben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| Lépés | ASP.Net-weblapok (Razor) kódolásra automatikus HTML. Beágyazott kód nuggets (@ blokkolja) által nyomtatott összes értékek a következők automatikusan HTML-kódolású. Azonban ha `HtmlHelper.Raw` metódus meghívták, visszatérési kód, amely nincs HTML-kódolásban. Ha `Html.Raw()` segítő módszert használ, az automatikus kódolási védelem Razor biztosító kihagyva.|

### <a name="example"></a>Példa
Az alábbiakban egy nem biztonságos példa látható: 

```C#
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Ne használjon `Html.Raw()` kivéve, ha szeretné megjeleníteni a jelölés során. Ez a módszer nem hajt végre implicit módon kódolás kimenete. Más ASP.NET segítő például használata`@Html.DisplayFor()` 

## <a id="stored-proc"></a>Ne használjon dinamikus lekérdezések a tárolt eljárások

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Egy SQL injektálási támadás bemenet-ellenőrzéshez tetszőleges parancsok futtatásához az adatbázis biztonsági réseit kihasználva. Ez akkor fordulhat elő, amikor az alkalmazás-adatbázisának eléréséhez dinamikus SQL-utasítások létrehozásához használja a bemeneti. Ez akkor is előfordulhat, ha a kódot használja átadott nyers felhasználói bevitel karakterláncokat, tárolt eljárások. Az SQL-injektálás támadás használ, a támadó hajthat végre tetszőleges parancsok az adatbázisban. Az összes SQL-utasítások (beleértve az SQL-utasítások tárolt eljárások) kell paraméteres. Paraméteres SQL-utasítások fogad el (például az aposztróf) SQL hiba nélkül speciális jelentéssel bírnak, mert azok erős típusmegadású karaktereket. |

### <a name="example"></a>Példa
Az alábbiakban nem biztonságos dinamikus tárolt eljárás egy példa látható: 

```C#
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
Az alábbiakban olvashat a megvalósított biztonságosan azonos tárolt eljárást: 
```C#
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

## <a id="validation-api"></a>Arról, hogy Modellellenőrzés a webes API-módszer

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC5, MVC6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az ASP.NET Web API Modellellenőrzés](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Lépések** | Egy ügyfél adatokat küld egy webes API-t, esetén kötelező minden elvégzése előtt az adatok érvényesítéséhez. Az ASP.NET Web API-kat elfogadó bemenetként, modellek segítségével adatokat jegyzetek modell ellenőrzési szabályok a modell tulajdonságai.|

### <a name="example"></a>Példa
A következő kód bemutatja, az ugyanaz: 

```C#
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
A műveleti módszer az API-vezérlők a modell érvényességi ki kell explicit módon ellenőrizhetők alább látható módon: 

```C#
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

## <a id="string-api"></a>Megvalósítása bemenet-ellenőrzést az összes karakterlánc típusú paraméterek fogadja el a webes API-módszer

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, MVC 5, 6 MVC |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [A modell adatai egy MVC alkalmazás ellenőrzése](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [irányadó elvek az ASP.NET MVC-alkalmazások](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Lépések** | Elfogadó csak primitív adattípus és nem a modell argumentumként módszerek reguláris kifejezés használatával bemenet-ellenőrzést kell végezni. Itt Regex.IsMatch használandó érvényes reguláris kifejezéssel mintával. Ha a bemeneti adatok nem egyezik a megadott reguláris kifejezés, vezérlő nem kell végrehajtásának folytatásához, és egy érvényesítési hiba megfelelő figyelmeztetés üzenetnek kell megjelennie.|

## <a id="typesafe-api"></a>Ellenőrizze, hogy biztonságos típusú paraméterek szolgáltatás webes API-ban az adatelérési

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Ha használja a paramétergyűjtemény, SQL kezeli a bemeneti van, mint végrehajtható kód, nem konstans érték. A paraméterek gyűjteményében segítségével érvényesíthetők bemeneti adatok típusát és a hossz korlátozások. A tartományon kívül eső értékeket kivételt vált. Típus környezetben is biztonságos SQL-paraméterek nem használják, ha a támadók hajtható végre a szűretlen bemeneti beágyazott injektálási támadások lehet.</p><p>Biztonságos Típusparaméterek használja az SQL-lekérdezések térített lehetséges SQL injektálási támadásokkal szemben, amelyek alakulhat ki nem szűrt bemeneti elkerülése érdekében. Biztonságos Típusparaméterek is használhatja, tárolt eljárások és a dinamikus SQL-utasításokat. Paraméterek az adatbázis által literálértékek, nem pedig végrehajtható kód kell kezelni. Paraméterek típusa és hossza is ellenőrzi.</p>|

### <a name="example"></a>Példa
A következő kód bemutatja, hogyan használható a SqlParameterCollection biztonságos Típusparaméterek tárolt eljárás meghívásakor. 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
Az előző példakódban a bemeneti érték nem lehet hosszabb, mint 11 karakter. Ha az adatok nem felelnek meg a típus vagy paraméter által meghatározott, a SqlParameter osztály kivételt jelez. 

## <a id="sql-docdb"></a>Cosmos DB paraméteres SQL-lekérdezések használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az Azure Document DB rendszerbe | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az Azure Cosmos DB SQL Parameterization bejelentése](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Lépések** | Bár az Azure Cosmos DB csak olvasási lekérdezéseket támogat, SQL-injektálás továbbra is lehetséges, ha a lekérdezések össze a felhasználói bevitel szereplő. Ahhoz, hogy nem kell érik belül ugyanaz a gyűjtemény rosszindulatú SQL-lekérdezések létrehozásával adatokhoz való hozzáférés egy felhasználó lehet. Használja a paraméteres SQL-lekérdezések Ha lekérdezések össze a felhasználói bevitel alapján. |

## <a id="schema-binding"></a>WCF bemeneti érvényesítési séma kötését keresztül

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Lépések** | <p>Érvényesítési hiánya különböző típusú injektálási támadások vezet.</p><p>Üzenet érvényesség-ellenőrzése a WCF-alkalmazás védelméhez védelmi egy vonal jelöli. Ezt a módszert ellenőrizni üzenetek sémák WCF szolgáltatási műveleteket egy rosszindulatú ügyfél támadások elleni védelméhez. Ellenőrizze az ügyfél támadások elleni védelméhez a rosszindulatú szolgáltatás által az ügyfél által fogadott összes üzenet. Üzenet érvényesítési lehetővé teszi a műveletek üzenet szerződések és nem lehet végrehajtani, adategyezményeinek használni, amikor üzenetek érvényesítéséhez paraméter-érvényesítés használatával. Üzenet érvényesítési ellenőrzési logika sémákat, ezáltal nagyobb rugalmasságot biztosít, és fejlesztési időn belül létrehozását teszi lehetővé. Sémák ismételten a szervezeti adatok ábrázolása szabványai létrehozása belüli különböző alkalmazásokhoz használhatók. Emellett üzenet érvényesítési lehetővé teszi műveletek védelmét, amikor összetettebb adattípusok használata esetén az üzleti logika képviselő szerződéseket.</p><p>Üzenet érvényességi végrehajtásához először elkészítette a séma, amely a műveletek a szolgáltatás és a műveletek által használt adatok típusát jelöli. Ezután hozzon létre egy .NET-osztályt, amely megvalósítja az egy egyéni ügyfél-üzenet inspector és egyéni kézbesítő üzenetet inspector belőle a szolgáltatás küldött/fogadott üzenetek ellenőrzése. A következő üzenet érvényesítéséhez, az ügyfél és a szolgáltatás egyéni végpontviselkedéshez valósíthatja meg. Végezetül megvalósítása a egy egyéni konfigurációs elem az osztályhoz, amely lehetővé teszi a kiterjesztett egyéni végpontviselkedéshez a konfigurációs fájlban, a szolgáltatás vagy az ügyfél közzétételét az"</p>|

## <a id="parameters"></a>WCF - bemenet érvényesítési paraméter ellenőrök keresztül

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Lépések** | <p>Bemeneti és adatérvényesítés a WCF-alkalmazás védelméhez védelmi egy fontos vonal jelöli. Ellenőriznie kell a WCF szolgáltatási műveletek a szolgáltatás támadások elleni védelméhez a rosszindulatú ügyfél által elérhetővé tett összes paramétert. Ezzel szemben akkor is ellenőrizni kell, az ügyfél támadások elleni védelméhez a rosszindulatú szolgáltatás által az ügyfél által fogadott összes visszatérési érték</p><p>WCF, amelyek lehetővé teszik a WCF futtatási viselkedés testreszabásához hozzon létre egyéni kiterjesztések különböző bővítési pontokat biztosít. Üzenet ellenőrök és paraméter ellenőrök ahhoz, hogy nagyobb mértékben vezérelheti az adatok egy ügyfél és a szolgáltatás között használt két bővítési mechanizmus. Kell használni ellenőrök paraméter bemenet-ellenőrzéshez és üzenet ellenőrök használja, csak a teljes üzenet mindkét szolgáltatás áramló vizsgálata szükség esetén.</p><p>Bemenet-ellenőrzést végrehajtani, akkor a .NET-osztályt elkészíti és valósítja meg az egyéni paraméter-inspector paraméterek műveletek a szolgáltatás ellenőrizni fogja. Ahhoz, hogy az ügyfél és a szolgáltatás érvényesítése egyéni végpontviselkedéshez majd megvalósítandó. Végezetül megvalósítandó egy egyéni konfigurációs elem a osztály, amely lehetővé teszi a kiterjesztett egyéni végpontviselkedéshez a konfigurációs fájlban, a szolgáltatás vagy az ügyfél közzétételét</p>|
