<properties
    pageTitle="Azure Notification Hubs"
    description="Tudja meg, hogyan használhatók a leküldéses értesítések az Azure-ban. A kódminták C# nyelven íródtak, a .NET API használatával."
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="02/11/2016"
    ms.author="wesmc"/>


#Azure Notification Hubs

##Áttekintés

Az Azure Notification Hubs egy egyszerűen használható, többplatformos, kibővített leküldésesértesítés-infrastruktúrát biztosít, amellyel mobil leküldéses értesítéseket küldhet akármilyen háttérrendszerből (a felhőben, vagy a helyszínen) bármilyen mobilplatformra.

A Notification Hubs használatával egyszerűen küldhet paltformfüggetlen, testreszabott leküldéses értesítéseket, absztrakt módon megjelenítve a különböző Platform Notification System (PNS) rendszerek részleteit. Egyetlen API-hívással megcélozhat egyes felhasználókat vagy akár felhasználók millióit tartalmazó teljes célközönség-szegmenseket, az összes eszközön.

A Notification Hubs szolgáltatást vállalati és végfelhasználói célokra is használhatja. Példa:

- Legfrissebb híreket tartalmazó értesítések küldése felhasználók millióinak minimális késéssel (a Notification Hubs működteti a Windows- és Windows Phone-eszközön előre telepített Bing-alkalmazásokat).
- Helyalapú kuponok küldése felhasználói szegmenseknek.
- Eseményértesítések küldése felhasználóknak vagy csoportoknak sport-/pénzügyi/játékalkalmazásokban.
- Felhasználók értesítése olyan vállalati eseményekről, mint az új üzenetek, e-mailek, vagy új értékesítési lehetőségek.
- Egyszer használatos jelszavak küldése többtényezős hitelesítéshez.



##Mik azok a leküldéses értesítések?

Az okostelefonok és táblagépek képesek „értesíteni” a felhasználókat egyes eseményekről. Ezek az értesítések több formában jelenhetnek meg.

A Windows Áruház és a Windows Phone alkalmazásaiban az értesítések _bejelentések_ formájában jelennek meg: megjelenik egy nem kizárólagos ablak, és hangjelzéssel együtt jelzi az új értesítést. Az egyéb értesítési típusok lehetnek a _csempe_, _formázatlan_ és a _jelvény_ típusú értesítések. További információk a Windows-eszközök által támogatott értesítéstípusokról: [Tiles, Badges, and Notifications](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx) (Csempék, jelvények és értesítések).

Az Apple iOS-eszközökön a leküldéses értesítés hasonlóan működik: megjelenik egy párbeszédablak, amely arra kéri a felhasználót, hogy tekintse meg az értesítést, vagy zárja be azt. A **Megtekintés** lehetőségre való kattintással megnyílik az az alkalmazás, amely az üzenetet kapta. További információk az iOS-értesítésekről: [iOS Notifications](http://go.microsoft.com/fwlink/?LinkId=615245) (iOS-értesítések).

A leküldéses értesítések lehetővé teszik a mobileszközökön a naprakész információk energiatakarékos megjelenítését. A háttérrendszerek akkor is elküldhetik az értesítéseket egy mobileszközre, ha a hozzájuk kapcsolódó alkalmazások nem aktívak a készüléken. A leküldéses értesítések a végfelhasználói alkalmazások fontos részei, és elősegítik az alkalmazással kapcsolatos marketingtevékenységeket és az alkalmazáshasználatot. Az értesítések a vállalatok számára is hasznosak lehetnek – a naprakész információknak köszönhetően javulhat az alkalmazottak válaszkészsége a vállalati eseményekkel kapcsolatban.

Néhány konkrét példa a mobilmarketing-forgatókönyvekre:

1.  Egy csempe frissítése Windows 8 vagy Windows Phone rendszeren az aktuális pénzügyi információkkal.
2.  Egy felhasználó bejelentéssel történő riasztása, hogy egy munkaelem lett hozzárendelve a felhasználóhoz egy munkafolyamat-alapú vállalati alkalmazásban.
3.  Egy jelvény megjelenítése az aktuális értékesítési érdeklődők számával egy CRM alkalmazásban (például Microsoft Dynamics CRM).

##A leküldéses értesítések működése

A leküldéses értesítéseket a rendszerek platformspecifikus, _Platform Notification System_ (PNS) nevű infrastruktúrákon keresztül küldik el. A PNS-ek lecsupaszított funkciókkal rendelkeznek (tehát nem támogatják a szórásos küldést és a testreszabást), és nincs közös felületük. Például egy értesítés küldéséhez egy Windows Áruházbeli alkalmazásnak egy fejlesztőnek kapcsolatba kell lépnie a WNS-sel (Windows értesítési szolgáltatás), egy értesítés küldéséhez egy iOS-eszközre pedig ugyanazon fejlesztőnek kapcsolatba kell lépnie az APNS-sel (Apple Push Notification szolgáltatás), és másodszor is el kell küldenie az üzenetet. Az Azure Notification Hubs egy közös felület biztosításával, és más, a platformokon átívelő leküldéses értesítések támogatásához használható funkciókkal segít.

Ugyanakkor magas szinten minden platformos értesítési rendszere ugyanazt a sablont követi:

1.  Az ügyfélalkalmazás kapcsolatba lép a PNS-sel, hogy lekérje a _leíróját_. A leíró típusa a rendszertől függ. A WNS esetén egy URI vagy „értesítési csatorna.” Az APNS esetén egy token.
2.  Az ügyfélalkalmazás ezt a leírót az alkalmazás _hátterében_ tárolja a későbbi használatra. A WNS esetén a háttér tipikusan egy felhőszolgáltatás. Az Apple esetén a rendszer neve _szolgáltató_.
3.  Egy leküldéses értesítés elküldéséhez az alkalmazás háttere a leíró használatával kapcsolatba lép a PNS-sel, hogy egy adott ügyfélalkalmazási példányt célozzon meg.
4.  A PNS továbbküldi az értesítést a leíróban meghatározott eszközre.

![][0]

##A leküldéses értesítések kihívásai

Habár ezek a rendszerek nagyon hatékonyak, számos teendőt hagynak az alkalmazásfejlesztőre még a gyakori leküldéses értesítési forgatókönyvek, például a szórásos küldés vagy a leküldéses értesítések szegmentált felhasználóknak való küldése megvalósítása esetén is.

A leküldéses értesítések küldése a mobilalkalmazások számára elérhető felhőszolgáltatások egyik leginkább kért funkciója. Ennek az az oka, hogy a működésükhöz szükséges infrastruktúra elég bonyolult, és általában nem kapcsolódik az alkalmazás fő üzleti logikájához. Néhány példa az igény szerinti leküldéses infrastruktúrák felépítésének kihívásaira:

- **Platformfüggőség.** Ahhoz, hogy különböző platformokat használó eszközökre értesítéseket lehessen küldeni, többféle felületet kell bekódolni a háttérbe. Nem csak a kevésbé fontos részletek különböznek, de az értesítés megjelenése (csempe, bejelentés vagy jelvény) is platformfüggő. Ezek a különbségek bonyolult és nehezen fenntartható háttérkódhoz vezethetnek.

- **Méretezés.** Az infrastruktúra méretezésének két szempontja van:
    + A PNS-irányelvek szerint az eszközök tokenjeit minden alkalommal frissíteni kell, amikor az alkalmazás elindul. Ez már csak a tokenek naprakészen tartásához is nagy forgalomhoz (és az ezekhez tartozó adatbázis-elérésekhez) vezet. Amikor az eszközök száma nő (akár milliókra), az infrastruktúra létrehozásának és fenntartásának költségei nem elhanyagolhatók.

    + A legtöbb PNS nem támogatja a több eszközre küldött szórásos küldést. Ez azt jelenti, hogy egy eszközök millióira való szórásos küldés a PNS-ekre küldött hívások millióit eredményezi. A kérelmek méretezésének képessége nem magától értetődő, mert az alkalmazásfejlesztők általában szeretnék alacsonyan tartani a teljes késést (például az üzenetet megkapó utolsó eszköznek nem szabad az értesítést 30 perccel az értesítés elküldése után megkapnia, mert ez sok esetben értelmetlenné tenné a leküldéses értesítéseket).
- **Útválasztás.** A PNS-ek módot biztosítanak egy üzenet elküldéséhez egy eszközre. A legtöbb alkalmazásban azonban az értesítések célpontjai a felhasználók és/vagy az érdekelt csoportok (például minden alkalmazott, ki egy adott ügyfélfiókhoz van hozzárendelve). Így aztán ahhoz, hogy az értesítéseket a megfelelő eszközökhöz küldjék, az alkalmazások hátterének tartalmaznia kell egy beállításjegyzéket, amely hozzácsatolja az érdekelt csoportokat az eszközök tokenjeihez. Ez a terhelés hozzáadódik az alkalmazások piacra kerülési idejéhez és fenntartási költségeihez.

##Miért érdemes a Notification Hubsot használni?

A Notification Hubs megszünteti a bonyolultságot: nem kell megküzdeni a leküldéses értesítések kihívásaival. Ehelyett használhat egy értesítési központot. A Notification Hubs egy multiplatformos, kibővített leküldéses értesítési infrastruktúrát használ, és jelentősen lecsökkenti a leküldéshez tartozó kódot, amely az alkalmazás hátterében fut. A Notification Hubs a leküldéses infrastruktúrák minden funkcióját megvalósítja . Az eszközök csak a PNS-leírók regisztrálásáért, a háttér pedig a platformfüggetlen üzenetek felhasználóknak vagy érdekcsoportoknak való elküldéséért felelős, ahogy a következő ábrán is látszik:

![][1]


A Notification Hubs egy használatra kész leküldéses értesítési infrastruktúrát biztosít a következő előnyökkel:

- **Több platform.**
    +  Minden nagyobb mobilplatform támogatása. A Notification Hubs képes leküldéses értesítéseket küldeni Windows Áruházbeli, iOS, Android és Windows Phone alkalmazásokra is.

    +  A Notification Hubs közös felületet biztosít az értesítések küldéséhez az összes támogatott platformra. Nincs szükség platformspecifikus protollokra. Az alkalmazás háttér-infrastruktúrája platformspecifikus vagy platformfüggetlen formátumú értesítéseket is tud küldeni. Az alkalmazás kizárólag a Notification Hubs szolgáltatással kommunikál.

    +  Eszközleírók kezelése. A Notification Hubs tartja fenn a leírók jegyzékét és PNS-visszajelzéseket.

- **Bármely háttér-infrastuktúrával használható**: felhőalapú vagy helyszíni, .NET, PHP, Java, Node stb.

- **Méretezés.** Az értesítési központok eszközök millióira méretezhetők újratervezés és horizontális skálázás nélkül.


- **Sokféle kézbesítési minta**:

    - *Szórás*: közel egyidejű továbbítást tesz lehetővé eszközök millióira egyetlen API-hívással.

    - *Egyedi küldés/csoportos küldés*: leküldés egyes felhasználókat jelölő címkékre, például az összes eszközükre; vagy szélesebb csoportokat, például különböző kiviteleket (táblagépeket és telefonokat) jelölő címkékre.

    - *Szegmentálás*: leküldés címkekifejezésekkel meghatározott összetett szegmensekhez (például a Yankees csapatát követő eszközök New Yorkban).

    Amikor leírót egy értesítési központba küldi, minden eszköz megadhat egy vagy több _címkét_. További információk a [címkét]. A címkék előzetes kiosztása és eldobása nem szükséges. A címkékkel egyszerűen küldhet értesítéseket felhasználóknak vagy érdeklődési köröknek. Mivel a címkék bármilyen alkalmazásspecifikus azonosítót tartalmazhatnak (például felhasználói vagy csoportazonosítót), a használatukkal a háttér-infrastruktúra felszabadítható az eszközleírók tárolásával és kezelésével járó terhelés alól.

- **Személyre szabás**: Minden eszköznek lehet egy vagy több sablonja, így a háttérkód módosítása nélkül érhető el az eszközönkénti honosítás és személyre szabás.

- **Biztonság**: közös hozzáférésű jogosultságkód (SAS) vagy összevont hitelesítés.

- **Részletes telemetria**: A portálon és szoftveresen is elérhető.


##App Service Mobile Apps-integráció

A különböző Azure-szolgáltatásokon elérhető zökkenőmentes és egységes élmény érdekében az [App Service Mobile Apps] a leküldéses értesítések beépített támogatását nyújtja a Notification Hubs használatával. Az [App Service Mobile Apps] jól skálázható, világszerte elérhető, vállalati fejlesztők és rendszerintegrátorok számára készült mobilalkalmazás-fejlesztő platform, amely számos lehetőséget kínál a mobilos fejlesztésekhez.

A Mobile Apps-fejlesztők a következő munkafolyamatoknál használhatják a Notification Hubs szolgáltatást:

1. Eszköz PNS-leírójának lekérése
2. Eszközök és [sablonok] regisztrálása a Notification Hubsban a Mobile Apps-ügyfél kényelmesen használható SDK-regisztrációs API-ján keresztül
    + Vegye figyelembe, hogy a Mobile Apps biztonsági okokból eltávolítja az összes regisztrációs címkét. Közvetlenül a háttérrendszerről használhatja a Notification Hubst a címkék és eszközök társításához.
3. Értesítések küldése az alkalmazás háttérrendszeréből a Notification Hubs használatával

Íme néhány hasznos szolgáltatás a fejlesztők számára, amelyeket ez az integráció tett elérhetővé:
- **Mobile Apps-ügyfél SDK-k.** Ezek a többplatformos SDK-k egyszerű API-kat biztosítanak a regisztrációhoz, illetve a mobilalkalmazáshoz automatikusan kapcsolódó értesítési központtal való kommunikációhoz. A fejlesztőknek nem kell átrágniuk magukat a Notification Hubs hitelesítő adatain, és egy másik szolgáltatást használniuk.
    + Az SDK-k automatikusan felcímkézik az adott eszközt a Mobile Apps által hitelesített felhasználói azonosítóval, így lehetővé teszik a felhasználóknak küldött leküldéses értesítéseket.
    + Az SDK-k a Mobile Apps telepítési azonosítót automatikusan GUID-ként használják a Notification Hubsban való regisztrációhoz, így a fejlesztőknek nem kell több szolgáltatásbeli GUID azonosítót fenntartaniuk.
- **Telepítési modell.** A Mobile Apps a Notification Hubs legújabb leküldési modelljével mutatja be az eszközök leküldéses tulajdonságait egy JSON-telepítésben, amely egyszerűen használható, és igazodik a leküldéses értesítési szolgáltatásokhoz.
- **Rugalmasság.** A fejlesztők mindig választhatják a Notification Hubs közvetlen használatát, még az integráció megvalósítása után is.
- **Integrált felhasználói felület az [Azure portálon].** A leküldési funkciót vizuálisan megjelenik a Mobile Appsban, és a fejlesztők könnyedén használhatják a kapcsolódó értesítési központtal a Mobile Appson keresztül.



##Következő lépések

A Notification Hubsról a következő témakörökben talál további információt:

+ **[Hogyan használják az ügyfelek a Notification Hubs szolgáltatást]**

+ **[A Notification Hubs szolgáltatással kapcsolatos oktatóanyagok és útmutatók]**

+ **A Notification Hubs használatának első lépéseit ismertető kotatóanyagok ** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

A megfelelő .NET által felügyelt API-hivatkozások a leküldéses értesítésekhez itt találhatók:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [Hogyan használják az ügyfelek a Notification Hubs szolgáltatást]: http://azure.microsoft.com/services/notification-hubs
  [A Notification Hubs szolgáltatással kapcsolatos oktatóanyagok és útmutatók]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [App Service Mobile Apps]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [sablonok]: notification-hubs-templates.md
  [Azure portálon]: https://portal.azure.com
  [címkét]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)



<!--HONumber=Jun16_HO2-->


