---
title: SCIM használata az Azure Active Directory-alkalmazások létesítési automatizálásához |} Microsoft Docs
description: Az Azure Active Directory automatikusan telepíthetik a felhasználókat és csoportokat a webszolgáltatás által a felülettel, a SCIM protokoll specifikációja definiálva van fronted alkalmazás vagy identitás tároló
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: 756509648638693689c8fc539a660809728cd5d3
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698747"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>A rendszer a tartományok közötti Identity Management (SCIM) használatával automatikusan a felhasználók és csoportok az Azure Active Directory alkalmazások telepítéséhez

## <a name="overview"></a>Áttekintés
Azure Active Directory (Azure AD) automatikusan is konfigurálta a felhasználókat és csoportokat, a felülettel webszolgáltatás által az fronted alkalmazás vagy identitás tároló definiálva a [a tartományok közötti Identity Management (SCIM) 2.0-s protokoll specifikációja rendszer](https://tools.ietf.org/html/draft-ietf-scim-api-19). Az Azure Active Directory küldhet kérelmek létrehozása, módosítása, illetve törlési rendelt felhasználók és csoportok a webszolgáltatás. A webszolgáltatás majd így felszabadulhatnak ezeket a kérelmeket, olyan műveleteket végez a célként megadott identitás tárolására. 

![][0]
*1. ábra: A webszolgáltatáson keresztül identitás tárolóhoz az Azure Active Directory kiépítés.*

Ez a funkció használható együtt a "állapotba hozása a saját alkalmazás" képességgel rendelkező Azure AD-ben. Ez a funkció lehetővé teszi, hogy egyszeri bejelentkezést és automatikus kiépítést alkalmazásokat, amelyek a rendszer fronted SCIM webszolgáltatás által a felhasználó.

Számos két alkalmazási helyzetei SCIM használata az Azure Active Directoryban.

* **Felhasználók és csoportok SCIM támogató alkalmazások kiépítés** -alkalmazásokat, amelyek támogatják az SCIM 2.0 és OAuth tulajdonosi jogkivonatok használnak, a hitelesítés az Azure AD konfigurálása nélkül működik.
  
* **Az alkalmazások saját kiépítési megoldás létrehozása, amely támogatja a más API-alapú üzembe helyezés** -nem SCIM alkalmazások, létrehozhat egy SCIM végpont lefordítani az Azure AD SCIM végpont és az alkalmazás támogatja-e az API-k között a felhasználók átadása. Segítséget a SCIM végpont, nincsenek közös nyelvi infrastruktúra (CLI) tárak, amelyek bemutatják a adjon meg egy SCIM végpont és SCIM üzenetek fordítása mintakódok együtt.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Felhasználók és csoportok SCIM támogató alkalmazások kiépítése
Az Azure AD beállítható úgy, hogy automatikusan a hozzárendelt rendelkezés felhasználókat és csoportokat, amelyek megvalósítják az alkalmazások egy [tartományok közötti identitáskezeléshez 2 (SCIM) rendszer](https://tools.ietf.org/html/draft-ietf-scim-api-19) webes szolgáltatás, és fogadja el az OAuth tulajdonosi jogkivonatokat a hitelesítéshez. Belül a SCIM 2.0-s specifikációnak alkalmazások követelménynek kell megfelelnie:

* Támogatja az felhasználói és/vagy csoportok szerint szakasz 3.3 SCIM protokoll létrehozását.  
* Támogatja a felhasználók és/vagy csoportok párosítása a patch kéréseknek szakasz 3.5.2 SCIM protokoll szerinti módosítását.  
* Támogatja az adott szakasz 3.4.1 SCIM protokoll ismert erőforrás beolvasása.  
* Támogatja a felhasználók és/vagy csoportok szerint szakasz 3.4.2 SCIM protokoll lekérdezése.  Alapértelmezés szerint externalId rendszer megkérdezi a felhasználókat és csoportokat a rendszer megkérdezi a displayName által.  
* Felhasználói azonosító és a kezelő szakasz 3.4.2 SCIM protokoll szerinti lekérdezése támogatja.  
* Támogatja az ID és a tag szakasz 3.4.2 SCIM protokoll szerinti csoportok lekérdezése.  
* SCIM protokoll szerinti szakasz 2.1 engedélyezési OAuth tulajdonosi jogkivonatokat fogad el.

Kérje meg az alkalmazás-szolgáltatót, és ezeket a követelményeket is kompatibilisek állapotkimutatások az alkalmazás szolgáltató dokumentációját.

### <a name="getting-started"></a>Első lépések
Ez a cikk a leírt SCIM profil támogató alkalmazások Azure Active Directory, az Azure AD application gallery a "nem galéria alkalmazás" funkciójával lehet csatlakoztatni. A csatlakozás után az Azure AD fut a szinkronizálási folyamat akár 40 perces, ahol azt az alkalmazás SCIM végpont hozzárendelt felhasználók és csoportok, lekérdezi és hoz létre vagy módosítja őket a hozzárendelés részletek alapján.

**Alkalmazást, amely támogatja a SCIM:**

1. Jelentkezzen be [az Azure-portálon](https://portal.azure.com). 
2. Keresse meg a **Azure Active Directory > Vállalati alkalmazások**, és válassza ki **új alkalmazás > minden > nem-gyűjtemény alkalmazás**.
3. Adja meg az alkalmazás nevét, és kattintson a **Hozzáadás** ikonra az alkalmazás objektum létrehozásához.
    
  ![][1]
  *2. ábra: Az Azure AD application gallery*
    
4. Az eredményül kapott képernyőn válassza ki a **kiépítési** lapon a bal oldali oszlopban.
5. Az a **kiépítési üzemmódban** menü **automatikus**.
    
  ![][2]
  *3. ábra: Konfigurálása kiosztás az Azure portálon*
    
6. Az a **bérlői URL-cím** mezőbe írja be az alkalmazás SCIM végpont URL-CÍMÉT. Példa: https://api.contoso.com/scim/v2/
7. Ha a SCIM végpont az OAuth tulajdonosi jogkivonat nem az Azure AD egy kibocsátótól igényel, majd másolja a szükséges OAuth tulajdonosi jogkivonatot az opcionális **titkos Token** mező. Ez a mező üresen marad, ha az Azure AD az Azure ad-minden egyes kérelemmel kiadott OAuth tulajdonosi jogkivonat tartalmazza. Alkalmazások, az Azure AD használja az identitásszolgáltató azt is ellenőrzi az Azure AD-jogkivonatot ki.
8. Kattintson a **kapcsolat tesztelése** kell rendelkeznie az Azure Active Directory megpróbál csatlakozni a SCIM végpont gombra. Ha a kísérlet sikertelen, hiba információk jelennek meg.  
9. Ha a kísérel meg csatlakozni az alkalmazás Succeed, majd kattintson a **mentése** mentéséhez rendszergazdai hitelesítő adatokat.
10. Az a **hozzárendelések** szakaszban, a két választható csoport attribútum-leképezésekhez: egy felhasználói objektum, egy, a csoport objektumainak. Válassza ki egyenként tekintse át a az alkalmazás Azure Active Directoryból szinkronizált attribútumok. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználókat és csoportokat a frissítési műveletek az alkalmazásban. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

    >[!NOTE]
    >Igény szerint letilthatja a "csoport" leképezési letiltásával objektumok szinkronizálása. 

11. A **beállítások**, a **hatókör** mező határozza meg, hogy mely felhasználók és az or csoportok szinkronizálva. Válassza a "Sync csak hozzárendelt felhasználók és csoportok" (ajánlott) csak szinkronizálás felhasználók és csoportok hozzárendelve a **felhasználók és csoportok** fülre.
12. A konfiguráció befejezése után módosítsa a **kiépítési állapot** való **a**.
13. Kattintson a **mentése** elindítani az Azure AD szolgáltatás kiépítését. 
14. Ha csak szinkronizálás hozzárendelve felhasználók és csoportok (ajánlott), ügyeljen arra, hogy válassza ki a **felhasználók és csoportok** lapra, és rendeljen a felhasználók és/vagy csoportok, szinkronizálni szeretne.

Ha a kezdeti szinkronizálás elindult, a **naplók** lapon figyelemmel a folyamat állapotát, amely tartalmazza az alkalmazás a létesítési szolgáltatás által végzett összes műveletet. Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

>[!NOTE]
>A kezdeti szinkronizálás végrehajtásához ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut-nál több időt vesz igénybe. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Bármely alkalmazás a saját kiépítési megoldás létrehozása
Hozzon létre egy SCIM webszolgáltatás-bővítmény is eltárolni, amely az Azure Active Directoryval, engedélyezheti a egyetlen bejelentkezést és automatikus felhasználólétesítés szinte bármilyen alkalmazáshoz, amely a kiépítés API REST vagy SOAP felhasználó biztosít.

Itt látható, hogyan működik:

1. Az Azure AD biztosít a közös nyelvi infrastruktúra szalagtár nevű [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Rendszerintegrátorok és a fejlesztők használhatja ezt a szalagtárat hozhat létre és telepíthet egy SCIM-alapú webszolgáltatás végpontja csatlakozni tudnak az Azure AD bármely alkalmazás identitás tárolására.
2. A webszolgáltatás a szabványos felhasználói séma hozzárendelése a felhasználó séma- és az alkalmazás által igényelt protokoll hozzárendelések valósíthatók meg.
3. A végponti URL-cím regisztrálva van az Azure AD-egyéni alkalmazás az alkalmazás-katalógus részeként.
4. Ez az alkalmazás az Azure AD-felhasználók és csoportok vannak hozzárendelve. Hozzárendelés, akkor a célalkalmazásnak történő szinkronizálásának engedélyezése egy várólistára kerülnek. A szinkronizálási folyamat a várólista kezelése 40 percenként fut.

### <a name="code-samples"></a>Kódminták
A folyamat megkönnyítése, [Kódminták](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) feltéve, hogy hozzon létre egy SCIM webszolgáltatási végpontot, és mutassa be, az Automatikus kiépítés. Egy minta megtartja sorait a CSV-felhasználók és csoportok közti szolgáltatóra van.  A másik pedig a szolgáltató, amely az Amazon Web Services identitás és hozzáférés-kezelés szolgáltatás.  

**Előfeltételek**

* A Visual Studio 2013 vagy újabb verzió
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* Windows számítógép, amely támogatja az ASP.NET keretrendszer 4.5-ös, a SCIM végpont használható. Ezen a számítógépen a felhőből elérhetőnek kell lennie.
* [Prémium szintű Azure AD egy próba- vagy licencelt verziójával egy Azure-előfizetés](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Első lépések
Az egy SCIM végpontot, amelyhez is fogadja el a kiépítési kérelmekre, az Azure AD végrehajtásához legkönnyebben létrehozásához és telepítéséhez a kódminta, amely a kiépített felhasználók számára egy vesszővel tagolt (CSV) fájl.

**A minta SCIM-végpont létrehozása:**

1. A kód a minta csomag: [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Bontsa ki a csomagot, és helyezze el a Windows-számítógép C:\AzureAD-BYOA-Provisioning-Samples\ például egy helyen.
3. Ebben a mappában indítsa el a Visual Studio FileProvisioning\Host\FileProvisioningService.csproj.
4. Válassza ki **eszközök > NuGet-Csomagkezelő > Csomagkezelő konzol**, és a következő parancsok a FileProvisioningService projekt oldani a megoldás hivatkozásokat:
  ```` 
   Update-Package -Reinstall
  ````
5. A FileProvisioningService projekt felépítéséhez.
6. Indítsa el a (rendszergazdaként) a Windows parancssori alkalmazás, és használja a **cd** paranccsal lépjen be a **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**mappa.
7. A következő parancsot, a Windows-számítógép IP-cím vagy tartománynév kiszolgálónevét < ip-cím > cseréje:
  ````   
   FileSvc.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. A Windows **Windows-beállítások > hálózat és Internet beállítások**, jelölje be a **Windows tűzfal > Speciális beállítások**, és hozzon létre egy **bejövő forgalomra vonatkozó szabály** , amely lehetővé teszi, hogy a befelé irányuló port 9000.
9. Ha a Windows-számítógép útválasztó mögött, az útválasztó kell megadni a portot, amely kommunikál az internettel 9000, és a port 9000 a Windows-számítógép közötti hálózati hozzáférési fordítási végrehajtásához. Ebben a konfigurációban kell az Azure AD-be lesz hozzáférése ehhez a végponthoz, a felhőben.

**A minta SCIM végpont regisztrálása az Azure ad-ben:**

1. Jelentkezzen be [az Azure-portálon](https://portal.azure.com). 
2. Keresse meg a **Azure Active Directory > Vállalati alkalmazások**, és válassza ki **új alkalmazás > minden > nem-gyűjtemény alkalmazás**.
3. Adja meg az alkalmazás nevét, és kattintson a **Hozzáadás** ikonra az alkalmazás objektum létrehozásához. Az application objektum létrehozása a cél alkalmazás kellene lenniük történő, és egyszeri bejelentkezés, és nem csak a SCIM végpont végrehajtási képviselő készült.
4. Az eredményül kapott képernyőn válassza ki a **kiépítési** lapon a bal oldali oszlopban.
5. Az a **kiépítési üzemmódban** menü **automatikus**.
    
  ![][2]
  *4. ábra: Konfigurálása kiosztás az Azure portálon*
    
6. Az a **bérlői URL-cím** mezőbe írja be az internet elérhetővé tett URL-cím és port a SCIM végpont. A bejegyzés megadása hasonlót http://testmachine.contoso.com:9000 vagy http://<ip-address>:9000/, ahol a < ip-cím > az interneten közzétéve az IP-cím.  
7. Ha a SCIM végpont az OAuth tulajdonosi jogkivonat nem az Azure AD egy kibocsátótól igényel, majd másolja a szükséges OAuth tulajdonosi jogkivonatot az opcionális **titkos Token** mező. Ez a mező üresen marad, ha az Azure AD tartalmazza az Azure ad-minden egyes kérelemmel kiadott OAuth tulajdonosi jogkivonat. Alkalmazások, az Azure AD használja az identitásszolgáltató azt is ellenőrzi az Azure AD-jogkivonatot ki.
8. Kattintson a **kapcsolat tesztelése** kell rendelkeznie az Azure Active Directory megpróbál csatlakozni a SCIM végpont gombra. Ha a kísérlet sikertelen, hiba információk jelennek meg.  
9. Ha a kísérel meg csatlakozni az alkalmazás Succeed, majd kattintson a **mentése** mentéséhez rendszergazdai hitelesítő adatokat.
10. Az a **hozzárendelések** szakaszban, a két választható csoport attribútum-leképezésekhez: egy felhasználói objektum, egy, a csoport objektumainak. Válassza ki egyenként tekintse át a az alkalmazás Azure Active Directoryból szinkronizált attribútumok. A kiválasztott attribútumok **egyező** tulajdonságok használatával felel meg a felhasználókat és csoportokat a frissítési műveletek az alkalmazásban. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.
11. A **beállítások**, a **hatókör** mező határozza meg, hogy mely felhasználók és az or csoportok szinkronizálva. Válassza a "Sync csak hozzárendelt felhasználók és csoportok" (ajánlott) csak szinkronizálás felhasználók és csoportok hozzárendelve a **felhasználók és csoportok** fülre.
12. A konfiguráció befejezése után módosítsa a **kiépítési állapot** való **a**.
13. Kattintson a **mentése** elindítani az Azure AD szolgáltatás kiépítését. 
14. Ha csak szinkronizálás hozzárendelve felhasználók és csoportok (ajánlott), ügyeljen arra, hogy válassza ki a **felhasználók és csoportok** lapra, és rendeljen a felhasználók és/vagy csoportok, szinkronizálni szeretne.

Ha a kezdeti szinkronizálás elindult, a **naplók** lapon figyelemmel a folyamat állapotát, amely tartalmazza az alkalmazás a létesítési szolgáltatás által végzett összes műveletet. Olvassa el az Azure AD-naplók kiépítés módjáról további információkért lásd: [automatikus felhasználói fiók kiépítése jelentések](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

Az utolsó lépés a minta ellenőrzése során, hogy a \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug mappát a Windows-számítógépen nyissa meg a TargetFile.csv fájlt. Az üzembe helyezési folyamat futtatása után ez a fájl társított összes részleteit, és kiosztása a felhasználók és csoportok jeleníti meg.

### <a name="development-libraries"></a>Fejlesztő függvénytárak
A saját webes szolgáltatás, amely megfelel a SCIM specifikációjának elkészítéséhez először ismerkedjen meg az alábbi kódtárak Microsoft egyre gyorsabban jelennek meg a fejlesztési folyamat segítségével biztosítja: 

1. Közös nyelvi infrastruktúra (CLI) szalagtárak alapján, hogy az infrastrukturális, például a C# nyelv felkínált való használatra. A tárak egyik [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), deklarál illesztőfelület, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, az alábbi ábrán látható: a könyvtárak segítségével a fejlesztők egy osztály, amely lehet hivatkozni a, általános, szolgáltatóként felülettel volna megvalósításához. A könyvtárak engedélyezése a fejlesztői központi telepítése egy webszolgáltatás, amely megfelel a SCIM megadását. A webszolgáltatás Internet Information Services, vagy bármilyen végrehajtható közös nyelvi infrastruktúra szerelvény vagy lehet üzemeltetni. A szolgáltató metódusok, amely a fejlesztők által az egyes identitás-tárolására való működésre volna programozott lefordítását kérelem.
  
  ![][3]
  
2. [Express route kezelők](http://expressjs.com/guide/routing.html) érhető el elemzés node.js kérelem objektumokból-hívások (amelyeket a SCIM specification) végrehajtott egy node.js webes szolgáltatás.   

### <a name="building-a-custom-scim-endpoint"></a>Egy egyéni SCIM végpont létrehozása
A CLI-tárakat használ, a tárak használó fejlesztők tárolhatja bármilyen végrehajtható közös nyelvi infrastruktúra szerelvényen belül, vagy az Internet Information Services belül a szolgáltatások. Itt látható mintakód egy végrehajtható szerelvényben, a címen szolgáltatás üzemeltetéséhez http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Ez a szolgáltatás egy HTTP címet és a kiszolgáló hitelesítési tanúsítvánnyal kell rendelkeznie, amelynek a legfelső szintű hitelesítésszolgáltató egyike a következő szerepel: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* A VeriSign szolgáltatótól
* WoSign

Olyan kiszolgálói hitelesítési tanúsítványt is kell kötve egy hálózati rendszerhéj-segédprogrammal történő Windows-gazdagépen: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Itt certhash megadott érték a tanúsítvány ujjlenyomatát a appid argumentum a megadott érték pedig tetszőleges globálisan egyedi azonosító.  

Az Internet Information Services belül a szolgáltatás futtatásához, a fejlesztő volna fejlesztheti a CLA könyvtár kódszerelvényből az egy osztályt indítási alapértelmezett névtér: a szerelvény.  Íme egy példa ilyen osztály: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Kezelési végpont hitelesítés
Az Azure Active Directory kérések tartalmazzák az OAuth 2.0 tulajdonosi jogkivonatot.   Minden szolgáltatás, a kérelem fogadása hitelesítenie kell a kibocsátó, hogy az Azure Active Directory nevében a várt Azure Active Directory-bérlőt az Azure Active Directory Graph webszolgáltatás elérésére.  A jogkivonat a kibocsátó azonosít egy iss jogcímet, például "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Ebben a példában a jogcím értéke alapszintű címéből https://sts.windows.net, azonosítja az Azure Active Directory, a kibocsátó, amíg relatív cím szegmens, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, megtalálható az Azure Active Directory-bérlő egyedi azonosítója a token ki, amelyek nevében.  Ha a jogkivonat az Azure Active Directory Graph webszolgáltatás eléréséhez adta ki, majd szolgáltatáshoz, 00000002-0000-0000-c000-000000000000 azonosítóját kell lennie a token és jogcím értéke.  

A fejlesztők a SCIM szolgáltatás létrehozása a Microsoft által biztosított CLA könyvtárak segítségével hitelesítheti a kérelmeket az Azure Active Directoryból a Microsoft.Owin.Security.ActiveDirectory csomag használata a következő lépések végrehajtásával: 

1. A szolgáltató megvalósíthatja a Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior tulajdonság azt egy metódust kell meghívni, amikor a szolgáltatás el van indítva az vissza: 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. Ez a módszer minden kérelem hitelesítve szem előtt a megadott tenantot, az Azure AD Graph webszolgáltatás elérésére nevében Azure Active Directory által kiadott tokennek, a szolgáltatás végpontok bármelyikére rendelkezik hozzáadása a következő kódot: 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>Felhasználó- és séma
Az Azure Active Directory kétféle típusú erőforrások SCIM webszolgáltatásokhoz építhető ki.  Ilyen típusú erőforrások a felhasználók és csoportok.  

Felhasználói erőforrásokat azonosítja a sémaazonosítót "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User", amely szerepel a protokoll-meghatározása: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Az alapértelmezett leképezését a felhasználók az Azure Active Directoryban "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" erőforrások attribútumait alább tábla 1.  

Erőforrások azonosítják a sémaazonosítót http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Táblázat 2, az alábbi, az alapértelmezett leképezését a csoportok az Azure Active Directoryban attribútumait http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group erőforrásokat.  

### <a name="table-1-default-user-attribute-mapping"></a>1. táblázat: Alapértelmezett felhasználói címtárattribútum-leképezésben
| Az Azure Active Directory-felhasználó | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktív |
| displayName |displayName |
| Telefax-TelephoneNumber |.value phoneNumbers [típus eq "fax"] |
| givenName |name.givenName |
| Beosztás |cím |
| levelezés |e-mailek [típus eq "munkahelyi"] .value |
| mailNickname |externalId |
| kezelő |kezelő |
| Mobileszköz |.value phoneNumbers [típus eq "mobileszköz"] |
| objectId |ID (Azonosító) |
| Irányítószám |[típus eq "munkahelyi"] címek .postalCode |
| proxy-Addresses |[Írja be az "egyéb" eq] e-maileket. Érték |
| fizikai-kézbesítés-OfficeName |[Írja be az "egyéb" eq] címek. Formázott |
| StreetAddress |[típus eq "munkahelyi"] címek .streetAddress |
| Vezetéknév |name.familyName |
| Telefonszám |.value phoneNumbers [típus eq "munkahelyi"] |
| felhasználó-egyszerű név |Felhasználónév |

### <a name="table-2-default-group-attribute-mapping"></a>2. táblázat: Alapértelmezett attribútum leképezése
| Azure Active Directory-csoport | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| levelezés |e-mailek [típus eq "munkahelyi"] .value |
| mailNickname |displayName |
| tag |tag |
| objectId |ID (Azonosító) |
| proxyAddresses |[Írja be az "egyéb" eq] e-maileket. Érték |

## <a name="user-provisioning-and-de-provisioning"></a>Felhasználói üzembe helyezést és megszüntetést
A következő ábra azt mutatja, hogy Azure Active Directory küld SCIM szolgáltatás egy olyan identitás-tárolóban egy másik felhasználó életciklusának kezelését az üzeneteket. Az ábrán is látható, hogyan a CLI könyvtárak készletével megvalósított SCIM szolgáltatás által biztosított Microsoft rendszerbeli kiépítésének e szolgáltatások ezeket a kérelmeket jelenti azt, hogy a szolgáltató metódusok.  

![][4]
*5. ábra: A felhasználók átadása, és megszüntetést feladatütemezési*

1. Az Azure Active Directory lekérdezi egy felhasználó számára a szolgáltatás az Azure AD-ben a felhasználó mailNickname attribútum értékének megfelelő externalId attribútum értéke. A lekérdezés például ebben a példában, amelynek jyoung egy olyan felhasználó, az Azure Active Directoryban egy mailNickname mintát Hypertext Transfer Protocol (HTTP) kérelmet fejezi ki: 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Ha a szolgáltatás a Microsoft által előírt végrehajtási SCIM szolgáltatások közös nyelvi infrastruktúra könyvtárak segítségével lett létrehozva, majd a kérést lefordítását a szolgáltató lekérdezési metódus hívásakor.  Ez a metódus aláírása: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Ez a Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters felület definíciója: 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  A következő példában a lekérdezés egy felhasználó a externalId attribútum egy megadott értékkel a lekérdezés metódusnak átadott argumentumok értékek a következők: 
  * a paraméterek. AlternateFilters.Count: 1
  * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
  * a paraméterek. AlternateFilters.ElementAt(0). ÖsszehasonlítóOperátor: ComparisonOperator.Equals
  * a paraméterek. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Ha egy lekérdezést, amely megfelel a felhasználó a mailNickname attribútum externalId attribútumértékkel rendelkező felhasználó számára a webszolgáltatás válasza nem ad vissza azokat a felhasználókat, Azure Active Directory kéri, hogy a szolgáltatás kiépíteni az Azure Active Directoryban egy megfelelő felhasználó.  Íme egy példa a kérelem: 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  A közös nyelvi infrastruktúra könyvtárak SCIM szolgáltatások végrehajtásához a Microsoft által biztosított lefordítja a kérésre azokat a Create metódussal a szolgáltatás-szolgáltató hívása.  A Create metódussal az aláírása: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  A kérelem egy felhasználó kiépítéséhez az erőforrás argumentum értéke a Microsoft.SystemForCrossDomainIdentityManagement példánya. A Microsoft.SystemForCrossDomainIdentityManagement.Schemas könyvtárban meghatározott Core2EnterpriseUser osztály.  Ha a felhasználó létrehozásához a kérelem sikeres, majd metódus megvalósítása várhatóan térjen vissza a Microsoft.SystemForCrossDomainIdentityManagement példánya. Az újonnan kiépített felhasználó egyedi azonosítóját azonosító tulajdonsága értékének a Core2EnterpriseUser osztályt.  

3. Egy felhasználó által egy SCIM fronted identitás tárolóban található ismert frissítéséhez, Azure Active Directory folytatja úgy, hogy a felhasználó aktuális állapotának kér a szolgáltatás a kéréshez, többek között: 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Használatával a közös nyelvi infrastruktúra könyvtárak SCIM szolgáltatások végrehajtásához a Microsoft által biztosított beépített szolgáltatás a kérelem a lekérése metódus a szolgáltató van lefordítva.  A lekérési metódus aláírása a következő: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  A példa egy kérelem a felhasználó aktuális állapotának beolvasására, a paraméterek argumentum értéke a megadott objektum tulajdonságainak értékei a következők: 
  
  * Azonosító: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Ha a hivatkozási attribútum frissíteni kell, majd az Azure Active Directory-e a hivatkozási attribútum identitás tárolójában aktuális értékének fronted a szolgáltatás már meghatározni a szolgáltatás lekérdezi az Azure Active Directoryban ez az attribútum értéke megegyezik. Felhasználók a, amelyek a jelenlegi érték a ily módon le kell kérdezni attribútum esetén a kezelő attribútum. Íme egy példa egy kérelem annak meghatározásához, hogy a kezelő egy adott felhasználó objektum attribútuma van a megadott érték: 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  Az attribútumok lekérdezési paraméter, a "ID", amely jelzi, ha egy felhasználói objektum, amely eleget tesz a kifejezést a szűrő lekérdezési paraméter értéke, akkor a szolgáltatás várhatóan válaszolni egy "urn: ietf:params:scim:schemas:core:2.0: Felhasználó"vagy"urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"erőforrás, például csak az adott erőforrás"ID"attribútum értéke.  Értékét a **azonosító** a kérelmező ismert attribútum. A szűrő lekérdezési paraméter; érték szerepel. az azt kérő célja ténylegesen kérelmet a minimális erőforrás megjelenítése felel meg a szűrési kifejezés arra utal, hogy az összes ilyen objektum létezik-e.   

  Ha a szolgáltatás a Microsoft által előírt végrehajtási SCIM szolgáltatások közös nyelvi infrastruktúra könyvtárak segítségével lett létrehozva, majd a kérést lefordítását a szolgáltató lekérdezési metódus hívásakor. A paraméterek argumentumnak az értékeként megadott objektum tulajdonságainak értékének a következők: 
  
  * a paraméterek. AlternateFilters.Count: 2. régiója
  * a paraméterek. AlternateFilters.ElementAt(x). AttributePath: "ID"
  * a paraméterek. AlternateFilters.ElementAt(x). ÖsszehasonlítóOperátor: ComparisonOperator.Equals
  * a paraméterek. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * a paraméterek. AlternateFilters.ElementAt(y). AttributePath: "manager"
  * a paraméterek. AlternateFilters.ElementAt(y). ÖsszehasonlítóOperátor: ComparisonOperator.Equals
  * a paraméterek. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * a paraméterek. RequestedAttributePaths.ElementAt(0): "ID"
  * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Itt lehet, hogy az index x értékének 0 és lehet, hogy az index y értéke 1, vagy lehet, hogy az x értéknek 1 és y értékének lehet 0, attól függően, hogy a szűrő lekérdezési paraméter kifejezések sorrendjét.   

5. Itt látható egy példa egy kérelem az Azure Active Directory egy felhasználó frissítéséhez egy SCIM szolgáltatáshoz: 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  A Microsoft közös nyelvi infrastruktúra-könyvtárakban SCIM szolgáltatások végrehajtási lefordítja a kérelem azokat a szolgáltató az Update metódus hívásakor. Az Update metódus aláírása a következő: 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    A példa egy kérelem egy felhasználó frissítéséhez a javítás argumentum értékeként megadott objektumnak a tulajdonságok értékeit: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (Mint PatchRequest2 PatchRequest). Operations.Count: 1
  * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). OperationName: OperationName.Add
  * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Path.AttributePath: "manager"
  * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.Count: 1
  * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.ElementAt(0). Hivatkozás: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.ElementAt(0). Érték: 2819c223-7f76-453a-919d-413861904646

6. Hogy leépíti a felhasználó SCIM szolgáltatása fronted identitás áruházban, az Azure AD egy kérést küld, mint: 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Ha a szolgáltatás a Microsoft által előírt végrehajtási SCIM szolgáltatások közös nyelvi infrastruktúra könyvtárak segítségével lett létrehozva, majd a kérést lefordítását a szolgáltató a Delete metódus hívásakor.   Ez a módszer az aláírása: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  A resourceIdentifier argumentumnak az értékeként megadott objektum leépíti a felhasználó kérést példájában a tulajdonságok értékeit az rendelkezik: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Csoport üzembe helyezést és megszüntetést
A következő ábra azt mutatja, hogy Azure AcD küld a SCIM szolgáltatásnak csoportnak egy másik identitás tárolására az életciklus kezeléséhez az üzeneteket.  Az üzenetek a felhasználók háromféleképpen vonatkozó üzeneteket különböznek: 

* Egy csoport erőforrás sémája ki van jelölve `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* Csoportok beolvasására irányuló kérelmek határozzák meg, hogy a tagok attribútum ki lesznek zárva a bármilyen olyan erőforrás található kérelemre adott válasz.  
* Annak meghatározásához, hogy rendelkezik-e a hivatkozási attribútum egy adott értékre kérelmek azok a tagok attribútum kapcsolatban.  

![][5]
*6. ábra: Az üzembe helyezést és megszüntetést feladatütemezési csoport*

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Felhasználói létesítési vagy megszüntetési SaaS-alkalmazásokhoz való automatizálásához](active-directory-saas-app-provisioning.md)
* [A felhasználók átadása attribútum-leképezésekhez testreszabása](active-directory-saas-customizing-attribute-mappings.md)
* [Attribútum-leképezésekhez kifejezések írása](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Helyezése Hatókörszűrőkkel felhasználói történő üzembe helyezéséhez](active-directory-saas-scoping-filters.md)
* [Alkalmazás-kiépítési értesítések](active-directory-saas-account-provisioning-notifications.md)
* [SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
