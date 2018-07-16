---
title: Adjon meg ellenőrzés – a Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
description: megoldások a fenyegetések között szerepelnek a Threat Modeling Tool
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
ms.openlocfilehash: d97388732f0b5cf4570026d5b23a64b2d689e5d4
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056851"
---
# <a name="security-frame-input-validation--mitigations"></a>Biztonsági keret: Bemeneti ellenőrzés |} Megoldások 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[XSLT-szkriptek használata a nem megbízható stíluslapok összes átalakítások letiltása](#disable-xslt)</li><li>[Győződjön meg arról, hogy minden felhasználó ellenőrizhető tartalmat sikerült tartalmazó lapon hibajelentéseket a Microsoft automatikus MIME-elemzése](#out-sniffing)</li><li>[Felvértezni vagy XML-entitás tiltása](#xml-resolution)</li><li>[Http.sys használó alkalmazások URL-cím kanonikussá tétele ellenőrzés végrehajtása](#app-verification)</li><li>[Győződjön meg, hogy a megfelelő vezérlők vannak érvényben, amikor a felhasználók fájlokat elfogadása](#controls-users)</li><li>[Győződjön meg arról, hogy típus környezetben is biztonságos paraméterek használ a webes alkalmazás adatelérési](#typesafe)</li><li>[Használjon külön modell kötése osztályok vagy kötési szűrő listázza az MVC tömeges hozzárendelés biztonsági rések elkerülése érdekében](#binding-mvc)</li><li>[Nem megbízható webes kimeneti előtti renderelési kódolása](#rendering)</li><li>[Bemenet-ellenőrzést és a szűrést az összes karakterlánc típusú modell tulajdonságai](#typemodel)</li><li>[Tisztító űrlap mezőit, amely fogadja el az összes karaktert, például:, rich text szövegszerkesztőben a alkalmazni kell](#richtext)</li><li>[Ne rendeljen DOM-elemek, amelyek nem rendelkeznek a beépített kódolás fogadóként](#inbuilt-encode)</li><li>[Ellenőrizze az alkalmazáson belül átirányítások lezárt vagy biztonságosan kész](#redirect-safe)</li><li>[Az összes karakterlánc típusú paramétert fogadja el a vezérlő metódusokhoz bemenet-ellenőrzés végrehajtása](#string-method)</li><li>[Állítsa be, hogy a DoS miatt rossz reguláris kifejezések feldolgozása reguláris kifejezés felső korlátja időkorlátja](#dos-expression)</li><li>[A Razor-nézetekben Html.Raw használatának elkerülése](#html-razor)</li></ul> | 
| **Adatbázis** | <ul><li>[Ne használjon dinamikus lekérdezéseiben a tárolt eljárások](#stored-proc)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a modell érvényesítése történik-e a webes API-metódusai](#validation-api)</li><li>[Bemenet-ellenőrzés végrehajtása az összes karakterlánc típusú paramétert fogadja el a webes API-metódusai](#string-api)</li><li>[Győződjön meg arról, hogy típus környezetben is biztonságos használt paraméterek webes API-t az adatok eléréséhez](#typesafe-api)</li></ul> | 
| **Az Azure Document DB-ről** | <ul><li>[Paraméteres SQL-lekérdezések használata az Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF-bemenet érvényesítési séma kötését keresztül](#schema-binding)</li><li>[WCF - bemenet-ellenőrzési paraméter vizsgálók keresztül](#parameters)</li></ul> |

## <a id="disable-xslt"></a>XSLT-szkriptek használata a nem megbízható stíluslapok összes átalakítások letiltása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [XSLT Security](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript Property](http://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Lépések** | XSLT támogatja a parancsfájlok használatával stíluslapok belül a `<msxml:script>` elemet. Ez lehetővé teszi az XSLT-átalakítás használandó egyéni függvényekhez. A parancsfájl végrehajtása a környezetben a végrehajtása az átalakítási folyamat. XSLT-parancsfájl a nem megbízható környezet nem megbízható kód végrehajtásának elkerülése érdekében le kell tiltani. *Ha a .NET használatával:* XSLT parancsfájlok alapértelmezés szerint le van tiltva; azonban győződjön meg arról, hogy azt nem kifejezetten engedélyezett keresztül a `XsltSettings.EnableScript` tulajdonság.|

### <a name="example"></a>Példa 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Példa
Ha használja az MSXML 6.0 használatával, XSLT-scripting alapértelmezésben le van tiltva; azonban biztosítania kell, hogy azt nem explicit módon engedélyezve van az XML-DOM-objektum tulajdonságának AllowXsltScript keresztül. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Példa
Ha használja az MSXML 5 vagy az alábbi XSLT parancsfájlok alapértelmezés szerint engedélyezve van, és explicit módon kell letiltja azt. Az XML-DOM-objektum tulajdonságának AllowXsltScript állítsa hamis értékre. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Győződjön meg arról, hogy minden felhasználó ellenőrizhető tartalmat sikerült tartalmazó lapon hibajelentéseket a Microsoft automatikus MIME-elemzése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [IE8 Biztonsági rész - alkalmazásvédelem](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Lépések** | <p>Az egyes lapok tartalmazhatnak a felhasználó ellenőrizhető tartalmat, a HTTP-fejléc kell használnia `X-Content-Type-Options:nosniff`. Felel meg ennek a követelménynek, vagy beállíthatja a kötelező fejléc oldalról oldalra csak felhasználói ellenőrizhető tartalmat tartalmazó előfordulhat, hogy lapjainak, vagy beállíthatja globálisan minden lap az alkalmazásban.</p><p>Minden típusú fájlt egy webkiszolgálóról-i tartozik [MIME-típus](http://en.wikipedia.org/wiki/Mime_type) (más néven egy *a content-type*), amely ismerteti a jellegét a tartalmat (azaz, kép, szöveg, alkalmazás, stb.)</p><p>Az X-tartalom-típus-Options fejlécben egy HTTP-fejlécet, amely lehetővé teszi a fejlesztők számára, hogy adja meg, hogy a tartalom nem lehet MIME-felszippantásra. Ez a fejléc célja MIME-elemző támadások számának csökkentése érdekében. Ez a fejléc támogatása hozzáadva az Internet Explorer 8 (IE8)</p><p>Csak azok a felhasználók, az Internet Explorer 8 (IE8), X-tartalom – Választékáról élvezheti. Az Internet Explorer korábbi verzióiban jelenleg nem veszik figyelembe az X-tartalom-típus-Options fejlécben</p><p>Az Internet Explorer 8 (és újabb verziók) az egyetlen jelentős böngészők megvalósításához a MIME-elemzés az elutasítás szolgáltatást is. Más főbb böngésző (a Firefox, a Safari, Chrome) hasonló funkciók megvalósításához, ha ezt az ajánlást frissülni fog tartalmazza, valamint ezek a böngészők szintaxisa</p>|

### <a name="example"></a>Példa
Ahhoz, hogy a szükséges fejlécének globálisan az alkalmazás összes oldalán, a következők egyikét teheti: 

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

* A globális alkalmazáson keresztül a fejléc hozzáadása\_BeginRequest 

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

* Csak egyes oldalakat szükséges fejléce egyes válaszok való hozzáadással engedélyezheti: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Felvértezni vagy XML-entitás tiltása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [XML-entitás bővítése](http://capec.mitre.org/data/definitions/197.html), [XML szolgáltatásmegtagadási támadások és a védelem](http://msdn.microsoft.com/magazine/ee335713.aspx), [MSXML biztonsági áttekintése](http://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [ajánlott eljárások az MSXML kód](http://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ NSXMLParserDelegate protokoll referenciája](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [külső hivatkozások feloldása](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Lépések**| <p>Bár ez nem széles körben használják, nincs egyik funkciója, amely lehetővé teszi, hogy az XML-elemző, bontsa ki a makró entitások meghatározása a dokumentum törzsében, vagy a külső forrásból származó értékekkel XML. Például a dokumentum megadhat egy entitás a következő értékkel: "Microsoft", "companyname" úgy, hogy minden alkalommal a szöveges "&companyname;" jelenik meg a dokumentum automatikusan cserélni a szöveget a Microsoft. Vagy a dokumentum egy entitás "MSFTStock" beolvasni a Microsoft készlet aktuális értéke külső webszolgáltatás hivatkozó határozhat meg.</p><p>Ezután bármikor "&MSFTStock;" jelenik meg a dokumentum, a program automatikusan felülírja az aktuális tőzsdei árfolyam. Ez a funkció azonban jogosultság szolgáltatásmegtagadásos (DoS) szolgáltatási feltételek létrehozásához. A támadó ágyazhatja több entitás létrehozása egy exponenciális bővítése XML bomba, hogy a rendszer az összes rendelkezésre álló memóriát használ fel. </p><p>Azt is megteheti ő hozhat létre egy külső referencia streamelő vissza egy végtelen adatmennyiség, vagy egyszerűen lefagy, amely a szál. Ennek eredményeképpen az összes csoport le kell tiltania belső és/vagy külső XML entitások megoldási teljesen, ha az alkalmazás nem használhatja, vagy manuálisan korlátozza a memória és az idő, amelyet az alkalmazás felhasználhat az entitás feloldása, ha ez a funkció feltétlenül szükséges. Ha az entitások megoldási nem szükséges az alkalmazás számára, majd tiltsa le azt. </p>|

### <a name="example"></a>Példa
A .NET-keretrendszer code a következő módszerek használhatók:

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
Vegye figyelembe, hogy az alapértelmezett érték `ProhibitDtd` a `XmlReaderSettings` értéke igaz, de a `XmlTextReader` false (hamis). Ha XmlReaderSettings használ, nem kell beállítani az igaz értékre, explicit módon ProhibitDtd, de ajánlott a biztonsági szakét arra, hogy ne. Azt is vegye figyelembe, hogy engedélyezi-e a XmlDocument attribútummal hívja osztály entitások megoldási alapértelmezés szerint. 

### <a name="example"></a>Példa
Entitások megoldási XmlDocuments letiltásához használja a `XmlDocument.Load(XmlReader)` túlterhelni a Load metódus, és állítsa be a megfelelő tulajdonságokat tiltása, XmlReader argumentumában, ahogyan az alábbi kódot: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Példa
Ha a letiltás entitás feloldása nem lehetséges, hogy az alkalmazás, a XmlReaderSettings.MaxCharactersFromEntities tulajdonsága egy ésszerű értéket, az alkalmazás igényeinek megfelelően. Ez korlátozza a potenciális exponenciális bővítése szolgáltatásmegtagadási támadások hatásainak. Az alábbi kód erre a megközelítésre szemlélteti: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Példa
Ha szeretne oldja meg a beágyazott entitásokat, de ehhez nem szükséges külső entitások, oldja meg a XmlReaderSettings.XmlResolver tulajdonság értéke null. Példa: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Vegye figyelembe, hogy az MSXML6, ProhibitDTD true értékre van állítva (letiltását DTD feldolgozása) alapértelmezés szerint. Az Apple osx-ről/iOS-code-ban van két XML elemzők is használhatja: NSXMLParser és libXML2. 

## <a id="app-verification"></a>Http.sys használó alkalmazások URL-cím kanonikussá tétele ellenőrzés végrehajtása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>A http.sys olyan alkalmazásokhoz kell tartaniuk az alábbi irányelveket:</p><ul><li>Korlátozza az URL-cím hossza legfeljebb 16 384 karakter (ASCII vagy Unicode). Ez az az abszolút URL-cím hosszabb az alapértelmezett az Internet Information Services (IIS) 6 beállítás alapján. Webhelyek törekedni kell egy rövidebb, mint ez a lehetőség</li><li>Szabványos .NET-keretrendszer fájl i/o-osztály (például a FileStream) használhatja a .NET FX végzi el ezeket a kanonikussá tétele szabályok előnyeit</li><li>Explicit módon hozhat létre egy ismert fájlnevek engedélyezése – lista</li><li>Kifejezetten elutasítás nem szolgálja, hogy UrlScan elutasítások ismert fájl típusai: exe, bat cmd, com, IDA, ida, idq, htr, idc, shtm [l], stm, nyomtató, ini, pol, dat-fájlok</li><li>Az alábbi kivételeket:<ul><li>System.ArgumentException (az eszköz nevét)</li><li>(A data-adatfolyamok) System.NotSupportedException</li><li>(A érvénytelen escape-karakterrel megjelölt fájlnevek) System.IO.FileNotFoundException</li><li>System.IO.DirectoryNotFoundException (a érvénytelen escape-karakterrel megjelölt könyvtárak)</li></ul></li><li>*Ne* Win32 fájl adatátviteli API-jain feliratozni. Az egy URL-címe érvénytelen szabályosan adja vissza egy 400-as hiba a felhasználó, és a valós hiba jelentkezik.</li></ul>|

## <a id="controls-users"></a>Győződjön meg, hogy a megfelelő vezérlők vannak érvényben, amikor a felhasználók fájlokat elfogadása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Fájl feltöltése unrestricted](https://www.owasp.org/index.php/Unrestricted_File_Upload), [aláírás-tábla](http://www.garykessler.net/library/file_sigs.html) |
| **Lépések** | <p>Feltöltött fájlok alkalmazások egy kockázatot jelentenek.</p><p>Sok első lépése, hogy a rendszer a megtámadott kell némi kódot kap. Ezután a támadás csak meg kell keresnie a kód végrehajtott vásárolhat. Egy fájlfeltöltési segít a támadó az első lépés elvégzését. Korlátlan fájlfeltöltés következményeinek változhat, beleértve a teljes rendszer átvétel, túlterhelt fájlrendszer vagy adatbázis-továbbítás háttérrendszerekhez, és egyszerű megrongálása támadások.</p><p>Attól függ, az alkalmazás működésével a feltöltött fájl- és különösen hol tárolják. Hiányzik a kiszolgáló oldalán érvényesítése fájlfeltöltéseket. A következő biztonsági vezérlők fájlfeltöltési funkciókhoz kell végrehajtani:</p><ul><li>Fájl kiterjesztése ellenőrzése (csak egy engedélyezett fájltípus érvényes készletét fogadhatók el)</li><li>Maximális méretkorlátot</li><li>Fájl nem fel kell tölteni a webroot; a hely legyen egy nem rendszer-meghajtó könyvtár</li><li>Az elnevezési konvenciót kell követni, a feltöltött fájl neve lehet néhány véletlenszerűségre azért fájl megelőzése érdekében, hogy felülírja</li><li>Fájlokat kell futtatni a víruskereső a lemezre írás előtt</li><li>Győződjön meg arról, hogy a fájl nevét és minden más metaadatokat (például a fájl elérési útja) ellenőrzi a rosszindulatú karakterek</li><li>Fájl formátuma aláírás-ellenőrzésének, megakadályozza, hogy egy felhasználó masqueraded fájlt feltölteni (pl. egy exe-fájl feltöltése a txt kiterjesztés módosításával)</li></ul>| 

### <a name="example"></a>Példa
A legutóbbi pont aláírás fájlformátumának érvényesítése kapcsolatban tekintse meg a részleteket alább osztály: 

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

## <a id="typesafe"></a>Győződjön meg arról, hogy típus környezetben is biztonságos paraméterek használ a webes alkalmazás adatelérési

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Használatakor a paraméterek gyűjtemény, az SQL kezeli a bemeneti van, mint végrehajtható kódok, majd inkább konstans érték-e. A bemeneti adatok típusát és hosszát vynutit a paraméterek gyűjtemény használható. A tartományon kívül eső értékeket indítása kivétel. Típus környezetben is biztonságos SQL-paraméterek nem használhatók, ha a támadók lehet injektálási támadásokkal kihasználható a szűretlen bemeneti beágyazott hajtható végre.</p><p>Biztonságos Típusparaméterek használata SQL-lekérdezések kapcsolatot létesítő lehetséges SQL-injektálásos támadásokról, amely akkor fordulhat elő, szűretlen bemenettel elkerülése érdekében. Biztonságos Típusparaméterek is használhatja, tárolt eljárásokkal és a dinamikus SQL-utasításokkal. Paraméterek az adatbázis konstansértékekkel, nem pedig végrehajtható kód kell kezelni. Paramétereket is típusát és hosszát ellenőrzi.</p>|

### <a name="example"></a>Példa 
A következő kód bemutatja, hogyan típus biztonságos paraméterek használata a SqlParameterCollection, amikor egy tárolt eljárás meghívásakor. 

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
Az előző példakódban a bemeneti érték nem lehet hosszabb 11 karakternél. Ha az adatok nem felel meg a típus vagy paraméter által meghatározott hossza, a SqlParameter osztály kivételt jelez. 

## <a id="binding-mvc"></a>Használjon külön modell kötése osztályok vagy kötési szűrő listázza az MVC tömeges hozzárendelés biztonsági rések elkerülése érdekében

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Attribútumok metaadatai](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [nyilvános kulcs biztonsági biztonsági rések és kockázatcsökkentési](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [teljes körű útmutatót az ASP.NET mvc-ben háttértár-hozzárendelés](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [EF MVC használatával – első lépések](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Lépések** | <ul><li>**Mikor kell kinéznie a túlküldéses a biztonsági rések? -** Túlküldéses a biztonsági rések fordulhat elő, bármely modell osztályok kötést létrehozni a felhasználói bevitel érvényben. Például MVC keretrendszerek hozhat létre egyéni .NET-osztályok, beleértve az egyszerű régi CLR-beli objektumok (POCOs) a felhasználói adatokat. MVC automatikusan feltölti a ezeket az adatokat a kérésből a modell osztályokat biztosít egy kényelmes ábrázolását felhasználói adatbevitelt tartalmazó többé vesződnie a sérült. Ha ezeket az osztályokat, amely nem állítható a felhasználó tulajdonságai közé tartozik, az alkalmazás is érinti a túlküldéses támadások, amelyek lehetővé teszik a felhasználói vezérlő, amelyek az alkalmazás soha nem javasolt. MVC-modell kötése, például adatbázis technológiák, például/objektumrelációs leképező entitás-keretrendszer például gyakran is támogatás eléréséhez POCO objektumok segítségével, amely adatbázis adatokat jelöli. Ezen adatok modell osztályok adja meg az adatbázis adatainak foglalkoznak, mint MVC foglalkozó felhasználói adatbevitelt tartalmazó azonos kényelmi. MVC és az adatbázis is támogatja a hasonló minták POCO objektumok, például mert úgy tűnik, a függvénytárnak az osztályai újrahasznosíthatja mindkét célra. Ez az eljárás segítségével megőrizheti a kockázatok elkülönítésének meghiúsul, és egy közös területre, ahol modell kötése, túlzott könyvelési támadások engedélyezése nem kívánt tulajdonságok vannak kitéve.</li><li>**Miért nem szabad használni saját szűretlen adatbázis modell osztályok paraméterekként saját MVC műveleteket hajthat végre? -** Mert MVC modell kötése fog kötődni semmit az adott osztály. Akkor is, ha az adatok nem jelenik meg a nézetben, egy rosszindulatú felhasználó HTTP-kérést küldhet az ezeket az adatokat tartalmazza, és az MVC legszívesebben köti, mert a művelet szerint a adatbázisosztály az alakzat, hogy fogadja el a felhasználói adatok.</li><li>**Miért érdemes az alakzat használt modell kötése érdeklik? -** ASP.NET MVC használatával modell kötése a túlságosan széleskörű modellek közzéteszi az alkalmazást túlküldéses támadások. Túlzott könyvelési sikerült engedélyezni a támadók milyen a fejlesztői készült, például az ár egy elem, vagy a fiók biztonsági jogosultságokat felülbírálása túl az alkalmazások adatainak módosítása. Alkalmazások művelet-specifikus modelleket (vagy a meghatározott engedélyezett szűrő tulajdonságlistákról) adjon meg egy explicit kötés szerződés milyen nem megbízható bemenet modell kötése keresztül kell használnia.</li><li>**Külön kötést modellek csak a kód másolása tapasztalja? -** Nem, célszerű a kockázatok elkülönítésének pár. Adatbázis modellek műveletmetódusokhoz használ fel, ha Ön visszajelzései a bármely vlastnost (vagy alárendelt tulajdonság) abban, hogy az osztály egy HTTP-kérelem a felhasználó által beállítható. Ha ez nem ajánlott MVC tennie, kell szűrőlista vagy egy külön osztály alakzat milyen adatok származhatnak felhasználói bevitel inkább MVC megjelenítéséhez.</li><li>**Ha külön kötést modellek a felhasználói bevitelhez, rendelkezik saját adatok jegyzet összes attribútum ismétlődő? -** Nem feltétlenül. Az adatbázis-modellosztály a MetadataTypeAttribute használatával a metaadatokat egy modell kötése osztály mutató hivatkozás. Csak vegye figyelembe, hogy a típus a MetadataTypeAttribute által hivatkozott kell lennie a hivatkozó típusa (is kell kevesebb tulajdonság, de nem lehet több) egy részét.</li><li>**Adatok áthelyezése oda-vissza felhasználói bemeneti modelleket és adatbázis-modellek között fárasztó feladat. Is egyszerűen másolható tükröződés használatával az összes tulajdonság keresztül? -** Igen. A csak a kötés modellekben megjelenő tulajdonságok meghatározta a rendszer a felhasználói bevitelhez biztonsága lépnek. Nem indokolt biztonsági, amely megakadályozza a tükröződés segítségével másolja át az alábbi két modell közti közös létező az összes tulajdonság.</li><li>**Mi a helyzet [kötési (kizárása = "â" ¦")]. Használható, amely külön kötést modelleket kellene helyett? -** Ez a módszer nem ajánlott. Használatával [kötési (kizárása = "â" ¦")] azt jelenti, hogy minden új tulajdonság köthető alapértelmezés szerint. Új tulajdonság hozzáadásakor, hogy biztonságos tarthatja a dolgokat egy plusz lépésre van, nem pedig a Tervező kellene lennie alapértelmezés szerint biztonságos. Attól függően, a fejlesztői ellenőrzése ebben a listában minden alkalommal, amikor egy tulajdonság hozzáadása a kockázatos.</li><li>**Van [kötési (Belefoglalás = "â" ¦")] hasznos, ha szerkesztési műveletek? -** Nem. [Kötési (Belefoglalás = "â" ¦")] csak ideális INSERT stílusú operations (hozzáadása új adatok). A frissítés stílusú műveletek (indítják a meglévő adatok) egy másik módszert használja, például külön kötést modelleket kellene vagy UpdateModel vagy TryUpdateModel tulajdonságok engedélyezett egy explicit listájához való átadásához. Hozzáadás egy [kötése (Belefoglalás = ":" ¦")] attribútum szerkesztési művelet azt jelenti, hogy a MVC-objektum-példány létrehozása, és csak a felsorolt tulajdonságai között állítsa be minden más alapértelmezett értékükön hagyja. Amikor az adatokat a rendszer megőrzi, teljesen lecseréli a meglévő entitásra, bármely nincs megadva tulajdonságok értékeit visszaállítja az alapértelmezett értéken. Például, ha IsAdmin kimaradt egy [kötési (Belefoglalás = ":" ¦")] attribútum szerkesztési művelet esetén minden felhasználó, amelynek a neve ezzel a művelettel keresztül szerkesztettek kellene állítani IsAdmin = false (bármely szerkesztett felhasználó elveszítik a rendszergazdai állapota). Ha meg szeretné akadályozni a frissítések bizonyos tulajdonságok, használja a fenti egyéb megközelítések egyikét. Vegye figyelembe, hogy az MVC eszközök egyes verziói készítése a vezérlő osztályok [kötési (Belefoglalás = ":" ¦")] a műveletek szerkesztéséhez, és jelenti azt, hogy egy tulajdonság eltávolítása a listából megakadályozza a túlzott könyvelési támadások. Azonban a fentiekben ismertetettek szerint ezt a megközelítést nem működik helyesen és helyette alaphelyzetbe állítja a nincs megadva tulajdonságok az adatokat az alapértelmezett értékekre.</li><li>**A létrehozás műveletek vannak-e bármilyen figyelmeztetések használatával [kötési (Belefoglalás = "â" ¦")] ahelyett, hogy külön kötést modellek? -** Igen. Ez a megközelítés először nem működik a Szerkesztés alkalmazási igénylő problémák enyhítését célzó összes előny könyvelési biztonsági rések két külön megközelítés karbantartása. Második, különálló kötés modellek kényszerítése a felhasználói bevitel alakzatot és az alakzatot, megőrzéshez használt közötti kockázatok elkülönítésének valami [kötési (Belefoglalás = "â" ¦")] nem teszi meg. Harmadik, vegye figyelembe, hogy [kötési (Belefoglalás = "â" ¦")] csak a legfelső szintű tulajdonságok; tud kezelni az attribútum nem engedélyezheti a alárendelt tulajdonságait (például "Details.Name") csak egyes részeit. Végül, és talán ennél is fontosabb, használatával [kötési (Belefoglalás = "â" ¦")] hozzáad egy plusz lépésre, amely az osztály bármikor lehet megjegyzett használt modell kötése. Ha egy új tartozó műveleti módszer közvetlenül összekapcsolja az adatok osztály és elfelejti tartalmazza egy [kötési (Belefoglalás = ":" ¦")] attribútum, azt is érinti a túlküldéses támadások, ezért a [kötési (Belefoglalás =": "¦")] megközelítés kevésbé biztonságos, alapértelmezés szerint. Ha használ [kötési (Belefoglalás = "â" ¦")], mindig ügyeljen arra, hogy ne felejtse el megadni, hogy minden alkalommal, amikor az adatok osztályok művelet metódus paramétereinek jelennek meg.</li><li>**A létrehozás műveletek esetében mi a helyzet üzembe a [kötési (Belefoglalás = "â" ¦")] attribútum a modellosztály magát a? Így nem kell ne feledje, minden tartozó műveleti módszer helyezi az attribútum nem ez a megközelítés? -** Ez a módszer bizonyos esetekben működik. Használatával [kötési (Belefoglalás = "â" ¦")] a modell típusa magát (és nem a műveletek paramétereinek Ez az osztály használatával), így nem kell ne felejtse el bevenni a [kötési (Belefoglalás ="â "¦")] attribútumot minden tartozó műveleti módszer. Az attribútum az osztály a közvetlenül elkülöníti a Ez az osztály modell kötése célokra elkülönített terület. Azonban ez a módszer csak egy modell kötése alakzat kiszolgálónként modellosztály teszi lehetővé. Ha egy tartozó műveleti módszer van szüksége, hogy a modell kötése egy mező (például csak a rendszergazda által a műveletet, amely frissíti a felhasználói szerepkörök), és egyéb műveleteket kell akadályozni, hogy ez a mező modell kötése, ez a módszer nem fog működni. Minden osztály legfeljebb egy modell kötése alakzat; különböző műveleteket kell különböző modell kötése alakzatokat, szükség esetén vagy különálló modell kötése osztályokkal külön alakzatok jelölik, vagy külön [kötési (Belefoglalás = "â" ¦")] attribútumok a műveleti metódusoknál.</li><li>**Milyen köti modellek? Ugyanaz, mint a modellek megtekintése használnak? -** Ezek a két kapcsolódó fogalom. A kifejezés kötés modell egy műveletet paraméterlistában (az alakzat az MVC-modell kötése a művelet metódusnak átadott) használt modell osztályra hivatkozik. A kifejezés nézetmodellben nézet egy tartozó műveleti módszer az átadott modell osztályra hivatkozik. Nézet-specifikus modellek használata az adatok átadására egy művelet metódus egy olyan nézetre általánosan használt megközelítés. Gyakran előfordul ez az alakzat is ideális modell kötése, és mindkét helyen használt ugyanannak a modellnek tekintse meg a kifejezés nézetmodellben használható. Ahhoz, hogy pontos, ez az eljárás ismerteti kifejezetten kötés modelleket, a műveletet, ami igazán tömeges hozzárendelés céljából átadva az alakzat összpontosítva.</li></ul>| 

## <a id="rendering"></a>Nem megbízható webes kimeneti előtti renderelési kódolása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, Web Forms-MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az ASP.NET scripting többhelyes megakadályozása](http://msdn.microsoft.com/library/ms998274.aspx), [parancsfájlok](http://cwe.mitre.org/data/definitions/79.html), [(Adatbázisközi hely Scripting) XSS megelőzési Adatlap lap](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Lépések** | Az online services vagy bármely alkalmazás vagy összetevő a bemenetet a webes feldolgozó egy támadási vektor többhelyes parancsfájl-kezelési (gyakori rövidítése XSS). XSS biztonsági rések előfordulhat, hogy a támadó egy sebezhető webkiszolgáló alkalmazáson keresztül egy másik felhasználó gépen hajtsa végre a parancsfájlt. Rosszindulatú parancsfájlok ellopni a cookie-k és egyéb illetéktelenül módosítani a JavaScript használatával egy áldozat gép használható. XSS van ebben az esetben a felhasználói bevitel ellenőrzése, biztosítva van formázva és kódolást, mielőtt egy weblap jelenik meg. Bemenet-ellenőrzéshez és a kódolási kimeneti webes védelmi kódtár használatával elvégezhető. A felügyelt kód (C\#, VB.net, stb.), használjon egy vagy több megfelelő kódolási módszereket a webes védelem (kártevőirtó-XSS) könyvtárból, attól függően, a környezetet, ahol a felhasználói bevitel eszközében beolvasása:| 

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

## <a id="typemodel"></a>Bemenet-ellenőrzést és a szűrést az összes karakterlánc típusú modell tulajdonságai

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Érvényesítési hozzáadása](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [modellt az adatok az MVC alkalmazások](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [irányadó elvek, az ASP.NET MVC-alkalmazások](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Lépések** | <p>A bemeneti paraméterek ellenőrizni kell, mielőtt győződjön meg arról, hogy az alkalmazás elleni rosszindulatú felhasználói bevitelek üzlettársaik használhatók az alkalmazásban. Ellenőrizze a bemeneti értékek reguláris kifejezés ellenőrzések használata engedélyezett érvényesítési stratégiát a kiszolgálói oldalon. Felhasználói bevitelek unsanitized / módszerek átadott paraméterek okozhat kód injektálási biztonsági rések felderítéséhez.</p><p>Webalkalmazások esetén belépési pontok is tartalmazhatnak, űrlap mezőit, sorolva, a cookie-k, HTTP-fejlécek és webszolgáltatás-paraméterek.</p><p>A következő bemenet-ellenőrzés ellenőrzése után modell kötése kell végrehajtani:</p><ul><li>A modell tulajdonságait kell feliratozva, a válaszban jegyzet, a megengedett karakterek és a maximális megengedett hossz fogadásához</li><li>A vezérlő metódusokhoz ModelState érvényességi hajtson végre.</li></ul>|

## <a id="richtext"></a>Tisztító űrlap mezőit, amely fogadja el az összes karaktert, például:, rich text szövegszerkesztőben a alkalmazni kell

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Nem biztonságos bemeneti kódolása](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML Sanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Lépések** | <p>Határozza meg a használni kívánt összes statikus jelölőnyelvi címkéket. Általános gyakorlat, hogy korlátozza a biztonságos HTML-elemek, például a formázás `<b>` (félkövér), és `<i>` (dőlt).</p><p>Az adatokat, HTML-kódolása írása előtt azt. Ezáltal a rosszindulatú parancsfájlok biztonságos okozza, hogy a szöveg, nem pedig a végrehajtható kód hibaként szerint.</p><ol><li>Adja hozzá a ValidateRequest ASP.NET kérelem ellenőrzésének letiltása = "false" attribútumot a \@ lap irányelv</li><li>Kódolás a karakterláncot tartalmazó bemeneti HtmlEncode módszerrel</li><li>Egy StringBuilder használja, és a csere metódushívás eltávolításának a kódolást, hogy engedélyezni szeretné a HTML-elemek</li></ol><p>Oldal a hivatkozások letiltja az ASP.NET kérés érvényesítése beállításával `ValidateRequest="false"`. HTML-kódolja a bemeneti és a szelektív lehetővé teszi, hogy a `<b>` és `<i>` azt is megteheti, a .NET-kódtár a HTML-tisztító is használható.</p><p>HtmlSanitizer egy .NET-kódtár szerkezetek vezethet, XSS-támadások, a dokumentumok és a HTML-töredékek tisztításhoz. AngleSharp elemzi, módosítására és HTML és CSS megjelenítéséhez használ. HtmlSanitizer NuGet-csomagként telepíthető, és a felhasználói bevitel HTML és CSS tisztító módszerekről, társvállalatait, a kiszolgálói oldalon kell átadni. Vegye figyelembe, hogy egy biztonsági vezérlőként tisztító csak utolsó lehetőségként kell figyelembe venni.</p><p>Bemenet-ellenőrzéshez és a kódolási kimeneti minősülnek jobb biztonsági vezérlők.</p> |

## <a id="inbuilt-encode"></a>Ne rendeljen DOM-elemek, amelyek nem rendelkeznek a beépített kódolás fogadóként

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Javascript-függvények ne végezze el a kódolást alapértelmezés szerint. A DOM-elemek az ilyen függvények használatával nem megbízható bemeneti hozzárendelésekor hely (XSS) parancsprogram-végrehajtások közötti eredményezhet.| 

### <a name="example"></a>Példa
Az alábbiakban nem biztonságos, példákat: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Ne használjon `innerHtml`; helyette használjon `innerText`. Hasonlóképpen, nem pedig `$("#elm").html()`, használata `$("#elm").text()` 

## <a id="redirect-safe"></a>Ellenőrizze az alkalmazáson belül átirányítások lezárt vagy biztonságosan kész

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az OAuth 2.0 engedélyezési keretrendszer - nyílt átirányító](http://tools.ietf.org/html/rfc6749#section-10.15) |
| **Lépések** | <p>Felhasználó által megadott helyre való átirányítás igénylő alkalmazás-tervezés kell korlátozhatja a lehetséges átirányítás célok helyeket vagy tartományokat előre meghatározott "biztonságos" listájára. Az alkalmazás összes átirányítások lezárt/biztonságos kell lennie.</p><p>Ehhez tegye a következőket:</p><ul><li>Az összes átirányítások azonosítása</li><li>Minden átirányítási egy megfelelő megoldás megvalósításához. Megfelelő megoldások közé tartozik az átirányítási engedélyezési listájához, vagy felhasználói megerősítést. Ha egy webhely vagy egy megnyitott átirányítási biztonságirés-szolgáltatás használ a Facebook/OAuth és OpenID Identitásszolgáltatók, a támadó eltulajdonít egy felhasználói bejelentkezési jogkivonat és, hogy a felhasználó megszemélyesítése. Ez egy rejlő kockázatot OAuth, amelyek leírása itt található RFC 6749 "Az OAuth 2.0 engedélyezési keretrendszer" használatakor, 10.15 "nyissa meg a átirányítja a" szakasz hasonlóan, által célzott adathalász támadások használatával nyílt átirányítást sérülhet felhasználók hitelesítő adatai</li></ul>|

## <a id="string-method"></a>Az összes karakterlánc típusú paramétert fogadja el a vezérlő metódusokhoz bemenet-ellenőrzés végrehajtása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Adatok modellezése az MVC alkalmazások ellenőrzése](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [irányadó elvek, az ASP.NET MVC-alkalmazások](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Lépések** | Elfogadó csak primitív adattípust, és nem a modellek argumentumként módszerek reguláris kifejezés használata bemenet-ellenőrzést kell végezni. Itt Regex.IsMatch kell használni egy érvényes Reguláriskifejezés-minta. Ha a bemenet nem felel meg a meghatározott reguláris kifejezéssel, vezérlőelem kell nem folytatható, és egy megfelelő figyelmeztetés kapcsolatos érvényesítési hiba üzenetnek kell megjelennie.| 

## <a id="dos-expression"></a>Állítsa be, hogy a DoS miatt rossz reguláris kifejezések feldolgozása reguláris kifejezés felső korlátja időkorlátja

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, Web Forms-MVC5, MVC6  |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [DefaultRegexMatchTimeout Property ](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Lépések** | Annak biztosítása érdekében a program rosszul szolgáltatásmegtagadási támadások ellen létrehozott reguláris kifejezések, sok meglátogassa okozó beállítása az alapértelmezett globális időtúllépés. A feldolgozási idő, mint a megadott felső határ hosszabb időt vesz igénybe, ha azt egy időtúllépési kivételt jelez. Semmi nem történik meg, ha az időkorlát végtelen lesz.| 

### <a name="example"></a>Példa
Például a következő konfigurációt kivételt fogja kijelezni egy RegexMatchTimeoutException, ha a feldolgozási időt vesz igénybe, több mint 5 másodperc: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>A Razor-nézetekben Html.Raw használatának elkerülése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| Lépés | ASP.Net-weblapok (Razor) kódolásra automatikus HTML. Az összes beágyazott kódot nuggets (@ blokkolja) által nyomtatott karakterláncokat automatikusan HTML-kódolású. Azonban, hogy amikor `HtmlHelper.Raw` metódus meghívásakor kerül, akkor adja vissza, amely nem kódolású HTML kódtípus. Ha `Html.Raw()` segédmetódus használja, az automatikus Razor biztosító kódolási védelem figyelmen kívül hagyja azt.|

### <a name="example"></a>Példa
Következő biztonságos példája: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Ne használjon `Html.Raw()` , kivéve, ha meg kell jelenítenie a jelölés. Ez a módszer nem hajt végre implicit módon kódolás kimenete. Használja például a más ASP.NET-segítők `@Html.DisplayFor()` 

## <a id="stored-proc"></a>Ne használjon dinamikus lekérdezéseiben a tárolt eljárások

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>SQL-injektálásos támadásokkal szemben bemenet-ellenőrzés tetszőleges parancsok futtatásához az adatbázis biztonsági réseit kihasználva. Ez akkor fordulhat elő, amikor az alkalmazás-adatbázisának eléréséhez dinamikus SQL-utasítások létrehozásához használja a bemeneti. Ez akkor is előfordulhat, ha a kódot használja a tárolt eljárások, amelyek a rendszer átad a nyers felhasználói adatbevitelt tartalmazó karakterláncok. Használja az SQL-injektálásos támadásokkal szemben, a támadó hajthat végre tetszőleges parancsokat az adatbázisban. Az összes SQL-utasítások (beleértve a tárolt eljárások az SQL-utasítások) kell lehet paraméterezni. Paraméteres SQL-utasítások karakter, amely az SQL-be (például szimpla idézőjel) problémák nélkül különleges jelentéssel bírnak, mert azok erősen írta be fogja fogadni. |

### <a name="example"></a>Példa
Következő példaként szolgál a biztonságos dinamikus tárolt eljárást: 

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
Következő megvalósítva biztonságosan azonos tárolt eljárás: 
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

## <a id="validation-api"></a>Győződjön meg arról, hogy a modell érvényesítése történik-e a webes API-metódusai

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC5, MVC6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Modell érvényesítése az ASP.NET webes API-k ](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Lépések** | Egy ügyfél adatokat küld a webes API-KHOZ, esetén kötelező feldolgozási végrehajtása előtt az adatok érvényesítéséhez. Az ASP.NET Web API-kat elfogadó bemenetként, modellek használatával adatok jegyzetek modelleken ellenőrzési szabályokat állíthat be a modell tulajdonságait.|

### <a name="example"></a>Példa
A következő kód bemutatja az ugyanaz: 

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
A művelet metódus az API-vezérlők a modell érvényességi explicit módon kell ellenőrizni, ahogy az alábbi rendelkezik: 

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

## <a id="string-api"></a>Bemenet-ellenőrzés végrehajtása az összes karakterlánc típusú paramétert fogadja el a webes API-metódusai

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, MVC 5, 6 MVC |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Adatok modellezése az MVC alkalmazások ellenőrzése](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [irányadó elvek, az ASP.NET MVC-alkalmazások](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Lépések** | Elfogadó csak primitív adattípust, és nem a modellek argumentumként módszerek reguláris kifejezés használata bemenet-ellenőrzést kell végezni. Itt Regex.IsMatch kell használni egy érvényes Reguláriskifejezés-minta. Ha a bemenet nem felel meg a meghatározott reguláris kifejezéssel, vezérlőelem kell nem folytatható, és egy megfelelő figyelmeztetés kapcsolatos érvényesítési hiba üzenetnek kell megjelennie.|

## <a id="typesafe-api"></a>Győződjön meg arról, hogy típus környezetben is biztonságos használt paraméterek webes API-t az adatok eléréséhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Használatakor a paraméterek gyűjtemény, az SQL kezeli a bemeneti van, mint végrehajtható kódok, majd inkább konstans érték-e. A bemeneti adatok típusát és hosszát vynutit a paraméterek gyűjtemény használható. A tartományon kívül eső értékeket indítása kivétel. Típus környezetben is biztonságos SQL-paraméterek nem használhatók, ha a támadók lehet injektálási támadásokkal kihasználható a szűretlen bemeneti beágyazott hajtható végre.</p><p>Biztonságos Típusparaméterek használata SQL-lekérdezések kapcsolatot létesítő lehetséges SQL-injektálásos támadásokról, amely akkor fordulhat elő, szűretlen bemenettel elkerülése érdekében. Biztonságos Típusparaméterek is használhatja, tárolt eljárásokkal és a dinamikus SQL-utasításokkal. Paraméterek az adatbázis konstansértékekkel, nem pedig végrehajtható kód kell kezelni. Paramétereket is típusát és hosszát ellenőrzi.</p>|

### <a name="example"></a>Példa
A következő kód bemutatja, hogyan típus biztonságos paraméterek használata a SqlParameterCollection, amikor egy tárolt eljárás meghívásakor. 

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
Az előző példakódban a bemeneti érték nem lehet hosszabb 11 karakternél. Ha az adatok nem felel meg a típus vagy paraméter által meghatározott hossza, a SqlParameter osztály kivételt jelez. 

## <a id="sql-docdb"></a>Paraméteres SQL-lekérdezések használata a Cosmos DB

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az Azure Cosmos DB SQL-paraméterezés bejelentése](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Lépések** | Bár az Azure Cosmos DB támogatja a csak olvasási lekérdezések csak, SQL-injektálás számára továbbra is lehetséges, ha a változó összefűzésével előállítjuk felhasználói adatbevitelt tartalmazó lekérdezések tevődnek. Nem lehet érnek a gyűjteményen belül rosszindulatú SQL-lekérdezések létrehozásával adatokhoz való hozzáférést egy felhasználó lehet. Az SQL-lekérdezések paraméteres lekérdezések tevődnek. Ha a felhasználói bemenet alapján. |

## <a id="schema-binding"></a>WCF-bemenet érvényesítési séma kötését keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, 3. NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Lépések** | <p>Más típusú injektálási támadásokkal kihasználható érvényesítési hiánya vezet.</p><p>Üzenet-ellenőrzés a WCF-alkalmazás védelmét a védelmi egy vonal jelöli. Ezzel a módszerrel sémák WCF szolgáltatási műveletek elleni támadás egy rosszindulatú ügyfél üzenetek ellenőrzése. Ellenőrizze az ügyfél elleni támadás rosszindulatú szolgáltatás által az ügyfél által fogadott összes üzenet. Üzenet-ellenőrzés lehetővé teszi a műveletek felhasználása üzenet szerződések vagy adatok szerződések, amely nem hajtható végre, amikor üzenetek érvényesítéséhez használja a paraméter ellenőrzése. Üzenet érvényesítési ellenőrzési logika belül sémák, így nagyobb rugalmasságot biztosít, és a fejlesztési idő csökkentése létrehozását teszi lehetővé. Sémák adatreprezentációt szabványainak létrehozása, a szervezeten belüli különböző alkalmazásokhoz felhasználhatók. Emellett üzenet-ellenőrzés lehetővé teszi, így megvédheti a műveleteket, ha összetettebb adattípusok használata esetén az üzleti logika jelölő szerződések használnak.</p><p>Üzenet-ellenőrzés végrehajtásához először létrehozhat egy sémát, amely a szolgáltatás és az adattípusokat, ezek a műveletek által igénybe vett az operations. Ezután hozzon létre egy .NET-osztály, amely egy egyéni üzenetet vizsgáló és egyéni dispatcher üzenet vizsgáló ellenőrzése és-tárolókról a szolgáltatás küldött/fogadott üzeneteket. Ezután egy egyéni végpont működés ahhoz, hogy az ügyfél és a szolgáltatás érvényesítése üzenet valósíthatja meg. Végül megvalósítása a egy egyéni konfigurációs elem az osztály, amely lehetővé teszi, hogy a konfigurációs fájlban, a szolgáltatás vagy az ügyfél kiterjesztett egyéni végpont viselkedésének az"</p>|

## <a id="parameters"></a>WCF - bemenet-ellenőrzési paraméter vizsgálók keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános, 3. NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Lépések** | <p>Bemenet és az adatok hitelesítése a WCF-alkalmazás védelmét a védelmet egy fontos üzletági jelöli. Meg kell erősítenie a WCF szolgáltatási műveletek a szolgáltatás elleni támadás rosszindulatú ügyfél által elérhetővé tett összes paramétert. Ezzel szemben akkor is ellenőrizni kell, az ügyfél elleni támadás rosszindulatú szolgáltatás által az ügyfél által fogadott összes visszatérési értékek</p><p>A WCF, amelyek segítségével testre szabhatja a WCF működését egyéni bővítmények létrehozásával különböző bővítési pontokat biztosít. Vlastnosti Messageinspectors paraméter vizsgálók jsou nagyobb irányítást az ügyfél és a egy szolgáltatás közötti áthaladó adatok felett két bővítési mechanizmust. Bemenet-ellenőrzés paraméter vizsgálók használni kell, és vlastnosti messageinspectors használata csak akkor, amikor szüksége van a teljes üzenetet egy szolgáltatás adataikkal áramló vizsgálata.</p><p>Bemenet-ellenőrzés végrehajtásához fog hozhat létre egy .NET-osztály, és annak érdekében, hogy érvényesítse a paramétereket a szolgáltatási műveletek egy egyéni paraméter-vizsgáló megvalósításához. Majd egy egyéni végpont működés ahhoz, hogy az ügyfél és a szolgáltatás érvényesítése kívánja végrehajtani. Végül kívánja végrehajtani egy egyéni konfigurációs elem az osztály, amely lehetővé teszi, hogy a konfigurációs fájlban, a szolgáltatás vagy az ügyfél a kiterjesztett egyéni végpont viselkedés</p>|
