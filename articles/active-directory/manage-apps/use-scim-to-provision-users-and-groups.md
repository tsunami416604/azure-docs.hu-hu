---
title: Automatikus üzembe helyezés SCIM használata az Azure Active Directory-alkalmazások |} A Microsoft Docs
description: Az Azure Active Directory-felhasználók és csoportok bármilyen alkalmazás vagy az identitás tároló, amely egy webszolgáltatás által meghatározott a SCIM-protokoll specifikációinak felhasználói felülettel fronted van automatikusan létesítsen
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: 87f5153ef71f74a0fa1a6be3c527fba03b65bf83
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53095567"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>A rendszer a tartományok közötti Identity Management (SCIM) használatával automatikus kiépítésére a felhasználók és csoportok alkalmazásokhoz az Azure Active Directoryból

## <a name="overview"></a>Áttekintés
Az Azure Active Directory (Azure AD) automatikusan létesítsen felhasználók és csoportok bármilyen alkalmazás vagy az identitás tároló, amely egy webszolgáltatás felhasználói felülettel fronted van definiálva a [rendszer tartományok közötti Identity Management (SCIM) 2.0-s protokoll specifikáció](https://tools.ietf.org/html/draft-ietf-scim-api-19). Az Azure Active Directory is küldhet kéréseket létrehozásához, módosításához, vagy a delete rendelt felhasználók és csoportok a web service. A web service majd is ezeket a kérelmeket lefordítása a műveletek a célként megadott ügyfélidentitás-tárolóval. 

![][0]
*1. ábra: Kiépítés az Azure Active Directoryból az ügyfélidentitás-tárolóval, webes szolgáltatáson keresztül*

Ez a funkció használható együtt a "saját alkalmazások használata" lehetőséget az Azure AD-ben. Ez a funkció lehetővé teszi az egyszeri bejelentkezést és automatikus felhasználókiépítés az SCIM webszolgáltatás által vannak fronted alkalmazások esetében.

Nincsenek SCIM használata az Azure Active Directoryban két alkalmazási helyzetei:

* **Felhasználók és csoportok alkalmazásokhoz, amelyek támogatják az SCIM-kiépítés** -alkalmazást, amely támogatja az SCIM 2.0 és OAuth tulajdonosi jogkivonatokat hitelesítés működéséről az Azure AD-konfiguráció nélkül.
  
* **A saját kiépítési megoldás az alkalmazások létrehozásához, amely támogatja a más API-alapú üzembe helyezés** -nem SCIM-alkalmazások, létrehozhat egy SCIM-végpont lefordítani az Azure AD SCIM végpont és az alkalmazás támogat az API között felhasználók átadása. Könnyebben fejleszthet egy SCIM-végpontot, nincsenek közös nyelvi infrastruktúra (CLI) kódtárak kódmintákkal, amelyek bemutatják, hogyan egy SCIM-végpontot biztosít, és lefordítja az SCIM-üzenetek együtt.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Felhasználók és csoportok alkalmazásokhoz, amelyek támogatják az SCIM-kiépítés
Az Azure AD beállítható úgy, hogy az automatikusan hozzárendelt felhasználók és csoportok megvalósító alkalmazások egy [a tartományok közötti Identity Management (SCIM) 2 rendszer](https://tools.ietf.org/html/draft-ietf-scim-api-19) webes szolgáltatás, és fogadja el az OAuth tulajdonosi jogkivonatokat a hitelesítéshez. Az SCIM 2.0 ismertetőjének belüli alkalmazások az alábbi követelményeknek kell megfelelnie:

* Támogatja az létrehozását a felhasználók és csoportok szakasz 3.3-as verzióját az SCIM-protokoll megfelelően.  
* Támogatja a felhasználók és csoportok a patch-kérések szakasz 3.5.2-es az SCIM-protokoll alapján módosítja.  
* Támogatja az SCIM-protokoll 3.4.1 szakaszában megfelelően ismert erőforrás beolvasása.  
* Felhasználók és csoportok szakasz 3.4.2 az SCIM-protokoll megfelelően lekérdezése támogatja.  Alapértelmezés szerint externalId rendszer megkérdezi a felhasználókat és csoportokat a rendszer megkérdezi a displayName által.  
* Felhasználói azonosító és a megfelelően az SCIM-protokoll 3.4.2 szakaszában manager lekérdezése támogatja.  
* Lekérdezi csoportok azonosítója és a megfelelően az SCIM-protokoll 3.4.2 szakaszában tag támogatja.  
* Fogadja el az SCIM-protokoll 2.1-es szakaszában megfelelően engedélyezési OAuth tulajdonosi jogkivonatokat.

Ellenőrizze az alkalmazás szolgáltatója vagy utasítások, ezek a követelmények való kompatibilitás érdekében az alkalmazás szolgáltatója dokumentációját.

### <a name="getting-started"></a>Első lépések
Ebben a cikkben leírt az SCIM-profil támogató alkalmazások csatlakoztathatók az Azure Active Directoryhoz a az Azure AD alkalmazáskatalógusában "katalógusban nem szereplő alkalmazás" funkciójával. A csatlakozás után a Azure ad-ben minden 40 perces, ahol azt lekérdezi az alkalmazás SCIM végpont hozzárendelt felhasználók és csoportok, és létrehozza vagy módosítja őket a hozzárendelés részletei alapján futtatja a szinkronizálási folyamat.

**SCIM használatát támogató alkalmazás csatlakoztatása:**

1. Jelentkezzen be a [az Azure Portalon](https://portal.azure.com). 
2. Keresse meg a **Azure Active Directory > Vállalati alkalmazások**, és válassza ki **új alkalmazás > minden > katalógusban nem szereplő alkalmazás**.
3. Adja meg egy nevet az alkalmazásnak, és kattintson a **Hozzáadás** ikonra az alkalmazás-objektum létrehozásához.
    
   ![][1]
   *2. ábra: Azure AD alkalmazáskatalógusában*
    
4. A megjelenő képernyő, válassza ki a **kiépítési** lapra a bal oldali oszlopban.
5. Az a **Kiépítési mód** menüjében válassza **automatikus**.
    
   ![][2]
   *3. ábra: Az Azure Portalon létrehozás konfigurálása*
    
6. Az a **bérlői URL-cím** mezőben adja meg az alkalmazás SCIM-végpont URL-CÍMÉT. Például: https://api.contoso.com/scim/v2/
7. Ha az SCIM-végpont egy OAuth tulajdonosi jogkivonat egy Azure AD-től eltérő kiállítótól van szüksége, majd másolja a szükséges OAuth tulajdonosi jogkivonat a választható **titkos jogkivonat** mező. Ha ezt a mezőt üresen hagyja, az Azure AD az OAuth tulajdonosi jogkivonat kiállított Azure ad-ben minden egyes kérelemmel tartalmazza. Alkalmazások, amelyek használhatja az Azure AD Identitásszolgáltatóként ellenőrizheti ezt az Azure AD-jogkivonatot állít ki.
8. Kattintson a **kapcsolat tesztelése** szeretné, hogy az Azure Active Directory megpróbál csatlakozni az SCIM-végpont gombra. Sikertelen kísérlet, hibaüzenetet jelenik meg.  
9. Ha a kísérel meg csatlakozni az alkalmazás Succeed, majd kattintson az **mentése** a rendszergazdai hitelesítő adatok mentéséhez.
10. Az a **leképezések** szakaszban, a két választható eljáráscsoport attribútumleképezések: egy a felhasználói objektumok és a egy csoport objektumainak. Válassza ki az alkalmazását az Azure Active Directoryból szinkronizált attribútumok áttekintéséhez egyenként. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználókat és csoportokat a frissítési műveleteket az alkalmazásban. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

    >[!NOTE]
    >Igény szerint letilthatja a csoportobjektumokhoz, tiltsa le a "csoport" leképezési szinkronizálása. 

11. A **beállítások**, a **hatókör** mező határozza meg, hogy mely felhasználók és az or csoportok vannak szinkronizálva. Válassza a "Sync csak a hozzárendelt felhasználók és csoportok" (ajánlott) csak szinkronizálás felhasználók és a hozzárendelt csoportok a **felhasználók és csoportok** fülre.
12. A konfiguráció befejezése után módosítsa a **üzembe helyezési állapotra** való **a**.
13. Kattintson a **mentése** az Azure AD létesítési szolgáltatás elindításához. 
14. Ha szinkronizálása csak a hozzárendelt felhasználók és csoportok (ajánlott), mindenképpen jelölje ki a **felhasználók és csoportok** lapra, és rendelje hozzá a felhasználókat, illetve a szinkronizálni kívánt csoportokat.

A kezdeti szinkronizálás megkezdése után is használhatja a **Auditnaplók** fülre, és a figyelő folyamatban, amely megjeleníti a kiépítési szolgáltatást a az alkalmazás által végrehajtott összes műveletet. Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](check-status-user-account-provisioning.md).

> [!NOTE]
> A kezdeti szinkronizálás végrehajtásához, mint az ezt követő szinkronizálások, amely körülbelül 40 percenként történik, amíg a szolgáltatás fut hosszabb időt vesz igénybe. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Bármely alkalmazás a saját kiépítési megoldás létrehozása
Hozzon létre egy SCIM webszolgáltatás is eltárolni, amely az Azure Active Directory, engedélyezheti a egyszeri bejelentkezést és automatikus felhasználókiépítése gyakorlatilag bármilyen alkalmazást, amely egy üzembe helyezési API REST vagy a SOAP felhasználó biztosít.

Itt látható, hogyan működik:

1. Az Azure AD egy közös nyelvi nevű infrastruktúra kódtár biztosít [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Rendszer integrátorok és fejlesztők használatával Ez a kódtár létrehozása és üzembe helyezése az Azure ad-ben csatlakoztatható bármely alkalmazás ügyfélidentitás-tárolóval, SCIM-alapú webes szolgáltatásvégpontot.
2. A szabványos felhasználói sémájából leképezhető a felhasználó séma- és az alkalmazás által megkövetelt protokollt a web service-ben megvalósított leképezések.
3. A végponti URL-cím regisztrálva van egy egyéni alkalmazást az alkalmazás katalógusában részeként az Azure AD-ben.
4. Ez az alkalmazás az Azure AD-felhasználók és csoportok vannak hozzárendelve. Hozzárendelés esetén kerülnek a célalkalmazásnak szinkronizálását egy üzenetsorba. A szinkronizálási folyamat kezelésére a várólista 40 percenként fut.

### <a name="code-samples"></a>Kódminták
A folyamat megkönnyítése, [Kódminták](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) feltéve, hogy hozzon létre egy SCIM webszolgáltatási végpontot, és mutassa be, az Automatikus kiépítés. Egy minta-szolgáltató, amely egy fájl fenntartja a felhasználók és csoportok vesszővel elválasztott értékeket tartalmazó van.  A másik pedig a szolgáltató, amely az Amazon Web Services identitás és hozzáférés-kezelés szolgáltatás működik.  

**Előfeltételek**

* A Visual Studio 2013-as vagy újabb
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* Windows gép, amely támogatja az ASP.NET-keretrendszer 4.5-ös verzióját az SCIM-végpontként kell használni. Ez a gép a felhőben elérhetőknek kell lenniük.
* [Azure AD Premium érvényes próbaverziójával vagy licencével verziójával egy Azure-előfizetés](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Első lépések
A legegyszerűbb módja egy SCIM-végpontot, amely az Azure ad-ből kiépítési kérelmeket fogadhat megvalósításához készíthet és helyezhet üzembe a kódminta, amely a kiépített felhasználók egy vesszővel tagolt (CSV) fájl.

**A minta SCIM-végpont létrehozása:**

1. Töltse le a kódcsomag minta: [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Tömörítse ki a csomagot, és helyezze el a Windows-gépen C:\AzureAD-BYOA-Provisioning-Samples\ például egy helyen.
3. Ebben a mappában nyissa meg a FileProvisioning\Host\FileProvisioningService.csproj projektre a Visual Studióban.
4. Válassza ki **eszközök > NuGet-Csomagkezelő > Csomagkezelő konzol**, és hajtsa végre a következő parancsokat a FileProvisioningService projekt oldja fel a megoldás hivatkozásokat:

   ```
    Update-Package -Reinstall
   ```

5. A FileProvisioningService projekt buildjének elkészítéséhez.
6. Indítsa el a parancssort alkalmazást a Windows (rendszergazdaként), és használja a **cd** paranccsal lépjen be a **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**mappát.
7. Futtassa a következő parancsot, és cserélje le a Windows-gép IP-cím vagy tartomány neve < ip-cím >:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

8. Windows alatt a **Windows-beállítások > hálózat és Internet beállítások**, jelölje be a **Windows tűzfal > Speciális beállítások**, és hozzon létre egy **bejövő szabály** , amely lehetővé teszi, hogy 9000 portot a bejövő hozzáférést.
9. Ha a Windows-gép útválasztó mögött található, az útválasztó kell úgy, hogy hajtsa végre a hálózati hozzáférés fordítási port 9000, amely közvetlenül csatlakozik az internethez, és a Windows-gépen 9000 port között. Ez a konfiguráció megadása kötelező érhetik el ezt a végpontot a felhőben az Azure AD-hez.

**A minta SCIM végpont regisztrálása az Azure ad-ben:**

1. Jelentkezzen be a [az Azure Portalon](https://portal.azure.com). 
2. Keresse meg a **Azure Active Directory > Vállalati alkalmazások**, és válassza ki **új alkalmazás > minden > katalógusban nem szereplő alkalmazás**.
3. Adja meg egy nevet az alkalmazásnak, és kattintson a **Hozzáadás** ikonra az alkalmazás-objektum létrehozásához. A létrehozott alkalmazás objektum célja a célalkalmazással kívánja kell való üzembe helyezést, majd egyszeri bejelentkezést a, és nem csak az SCIM-végpont jelölésére.
4. A megjelenő képernyő, válassza ki a **kiépítési** lapra a bal oldali oszlopban.
5. Az a **Kiépítési mód** menüjében válassza **automatikus**.
    
   ![][2]
   *4. ábra: Az Azure Portalon létrehozás konfigurálása*
    
6. Az a **bérlői URL-cím** mezőben adja meg az interneten közzétett URL-cím és port a SCIM-végpont. A bejegyzés a következőképpen fog kinézni http://testmachine.contoso.com:9000 vagy http://<ip-address>:9000/, ahol az < ip-cím > az internetről elérhető IP-címet.  
7. Ha az SCIM-végpont egy OAuth tulajdonosi jogkivonat egy Azure AD-től eltérő kiállítótól van szüksége, majd másolja a szükséges OAuth tulajdonosi jogkivonat a választható **titkos jogkivonat** mező. Ha ezt a mezőt üresen hagyja, az Azure AD tartalmazza az OAuth tulajdonosi jogkivonat ki minden egyes kérelemmel Azure AD-ből. Alkalmazások, amelyek használhatja az Azure AD Identitásszolgáltatóként ellenőrizheti ezt az Azure AD-jogkivonatot állít ki.
8. Kattintson a **kapcsolat tesztelése** szeretné, hogy az Azure Active Directory megpróbál csatlakozni az SCIM-végpont gombra. Sikertelen kísérlet, hibaüzenetet jelenik meg.  
9. Ha a kísérel meg csatlakozni az alkalmazás Succeed, majd kattintson az **mentése** a rendszergazdai hitelesítő adatok mentéséhez.
10. Az a **leképezések** szakaszban, a két választható eljáráscsoport attribútumleképezések: egy a felhasználói objektumok és a egy csoport objektumainak. Válassza ki az alkalmazását az Azure Active Directoryból szinkronizált attribútumok áttekintéséhez egyenként. A kiválasztott attribútumok **megfelelést kiváltó** tulajdonságok segítségével felel meg a felhasználókat és csoportokat a frissítési műveleteket az alkalmazásban. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.
11. A **beállítások**, a **hatókör** mező határozza meg, hogy mely felhasználók és az or csoportok vannak szinkronizálva. Válassza a "Sync csak a hozzárendelt felhasználók és csoportok" (ajánlott) csak szinkronizálás felhasználók és a hozzárendelt csoportok a **felhasználók és csoportok** fülre.
12. A konfiguráció befejezése után módosítsa a **üzembe helyezési állapotra** való **a**.
13. Kattintson a **mentése** az Azure AD létesítési szolgáltatás elindításához. 
14. Ha szinkronizálása csak a hozzárendelt felhasználók és csoportok (ajánlott), mindenképpen jelölje ki a **felhasználók és csoportok** lapra, és rendelje hozzá a felhasználókat, illetve a szinkronizálni kívánt csoportokat.

A kezdeti szinkronizálás megkezdése után is használhatja a **Auditnaplók** fülre, és a figyelő folyamatban, amely megjeleníti a kiépítési szolgáltatást a az alkalmazás által végrehajtott összes műveletet. Az Azure AD létesítési naplók olvasása további információkért lásd: [-jelentések automatikus felhasználói fiók kiépítése](check-status-user-account-provisioning.md).

Az utolsó lépés a minta ellenőrzése, hogy nyissa meg a TargetFile.csv fájlt a Windows-gépen \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug mappában. A kiépítési folyamat futtatása után a fájl részleteit az összes hozzárendelt, és üzembe helyezett felhasználók és csoportok jeleníti meg.

### <a name="development-libraries"></a>Fejlesztési kódtárak
Segítségével hozhatja létre saját webes szolgáltatás, amely megfelel az SCIM-specifikációnak, először ismerkedjen meg az alábbi kódtárak segítségével felgyorsíthatja a fejlesztési folyamatot a Microsoft által biztosított: 

1. Kódtárak nyelvi infrastruktúra (CLI) alapján az infrastruktúrát, például a C# nyelv használatra érhetők el. Ezek a kódtárak egyik [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), deklarálja a megfelelő interfészre, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, az alábbi ábrán látható: A fejlesztői a kódtárak használatával, amelyeket egy olyan osztállyal, hogy lássa, mint szolgáltatónak említett, előfordulhat, hogy kapcsolat lenne megvalósításához. A szalagtárak köszönhetően a fejlesztő webszolgáltatás üzembe helyezése, amely megfelel az SCIM-specifikációnak. A webszolgáltatás Internet Information Services vagy bármilyen végrehajtható közös nyelvi infrastruktúra szerelvény vagy lehet üzemeltetni. Kérelem alakítja át a rendszer a szolgáltató metódusokkal, amelyeket a fejlesztő néhány ügyfélidentitás-tárolóval, a művelethez használandó lenne programozott hívásokat.
  
   ![][3]
  
2. [Express route-kezelők](https://expressjs.com/guide/routing.html) elemzéséhez node.js kérelem objektumok-hívás (megfelelően az SCIM-specifikáció szerint), amely elérhető végrehajtott egy node.js webes szolgáltatás.   

### <a name="building-a-custom-scim-endpoint"></a>Egy egyéni SCIM-végpont létrehozása
A CLI-kódtárak használata esetén ezek a kódtárak használatával a fejlesztők is szolgáltatásaikat bármilyen végrehajtható közös nyelvi infrastruktúra szerelvény, vagy az Internet Information Services. Itt látható mintakód a címen egy végrehajtható v sestavení szolgáltatás üzemeltetéséhez http://localhost:9000: 

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

Ez a szolgáltatás egy HTTP címet és a kiszolgáló hitelesítési tanúsítvánnyal kell rendelkeznie, amelynek a legfelső szintű hitelesítésszolgáltatót az alábbiak egyike a következő nevekkel: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* A VeriSign szolgáltatótól
* WoSign

Olyan kiszolgálói hitelesítési tanúsítványt a hálózati rendszerhéj segédprogrammal Windows gazdagépen köthető, port: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Itt a certhash argumentumhoz megadott érték a tanúsítvány ujjlenyomatát a megadott alkalmazásazonosító értéke egy tetszőleges globálisan egyedi azonosítóját.  

Az Internet Information Services szolgáltatás üzemeltetéséhez, a fejlesztők egy CLA kód könyvtár szerelvény lenne készíthet indítási sestavení alapértelmezett névtér osztály.  Íme egy példa az ilyen osztály: 

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
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Kezelési végpont hitelesítés
Az Azure Active Directoryból kérések az OAuth 2.0 tulajdonosi jogkivonat tartalmazzák.   Minden olyan szolgáltatás, a kérelem fogadása hitelesítenie kell a kibocsátó, hogy az Azure Active Directory a várt Azure Active Directory-bérlő számára az Azure Active Directory Graph web Service access nevében.  A jogkivonat a kibocsátó iss jogcím, például "iss" által azonosított: "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Ebben a példában a jogcímérték základní adresa https://sts.windows.net, azonosítja az Azure Active Directory, a kibocsátó relatív cím szegmens, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, van egy Azure Active Directory-bérlő egyedi azonosítója a jogkivonat ki, amelyek nevében.  A jogkivonatot az Azure Active Directory Graph webes szolgáltatás van kiadva, akkor a szolgáltatáshoz, 00000002-0000-0000-c000-000000000000 azonosítóját kell legyen a token aud jogcím értékét.  

A CLA-kódtárakat SCIM szolgáltatás létrehozásához a Microsoft által biztosított használó fejlesztők hitelesítheti kéréseket az Azure Active Directoryból a Microsoft.Owin.Security.ActiveDirectory csomag segítségével az alábbi lépéseket: 

1. Egy szolgáltató meg Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior tulajdonság visszaadása egy metódust meghív, ha a szolgáltatás elindult, hogy: 

   ```
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
   ```

2. Ez a módszer bármely kérelem hitelesítve nevében egy megadott bérlőt, az Azure AD Graph web Service access Azure Active Directory által kiállított jogkivonatok szem előtt, a szolgáltatás-végpontok bármelyikét, hogy a következő kód hozzáadása: 

   ```
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
   ```


## <a name="user-and-group-schema"></a>Felhasználók és csoportok séma
Az Azure Active Directoryban két típusú erőforrásokat az SCIM-webszolgáltatások helyezhet üzembe.  Ilyen típusú erőforrások a felhasználók és csoportok.  

Felhasználói erőforrásokat azonosítja a séma-azonosító, "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User", amely tartalmazza a protokoll-meghatározása: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Az alapértelmezett leképezést az "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" erőforrás attribútumait az Azure Active Directory felhasználói attribútumok 1,. táblázat lejjebb tekinthetők meg.  

Felhasználóicsoport-erőforrások azonosítja a séma azonosító http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  2 mutat be az alábbi táblázat az alapértelmezett leképezést az attribútumok a csoportok az Azure Active Directoryban attribútumait http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group erőforrásokat.  

### <a name="table-1-default-user-attribute-mapping"></a>1. táblázat: Alapértelmezett felhasználói attribútumleképezés

| Az Azure Active Directory-felhasználó | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktív |
| displayName |displayName |
| Telefax-TelephoneNumber |phoneNumbers [típus eq "fax"] .value |
| givenName |name.givenName |
| Beosztás |cím |
| levelezés |e-mailek [típus eq "work"] .value |
| mailNickname |externalId |
| kezelő |kezelő |
| mobil |phoneNumbers [típus eq "mobileszköz"] .value |
| objectId |ID (Azonosító) |
| Irányítószám |.postalCode címek [típus eq "work"] |
| proxy-Addresses |e-mailek [Írja be a eq "egyéb"]. Érték |
| fizikai-kézbesítési-OfficeName |a címek [Írja be a eq "egyéb"]. Formázott |
| streetAddress |.streetAddress címek [típus eq "work"] |
| Vezetéknév |name.familyName |
| Telefonszám – |phoneNumbers [típus eq "work"] .value |
| felhasználó-PrincipalName |Felhasználónév |

### <a name="table-2-default-group-attribute-mapping"></a>2. táblázat: Alapértelmezett csoport attribútumleképezés

| Azure Active Directory-csoport | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| levelezés |e-mailek [típus eq "work"] .value |
| mailNickname |displayName |
| tag |tag |
| objectId |ID (Azonosító) |
| proxyAddresses |e-mailek [Írja be a eq "egyéb"]. Érték |

## <a name="user-provisioning-and-de-provisioning"></a>Felhasználói üzembe helyezést és megszüntetést
A következő ábra azt mutatja, hogy az Azure Active Directory által SCIM szolgáltatásba küldött üzeneteket egy felhasználó egy másik ügyfélidentitás-tárolóval, az életciklusnak a kezelésében. Az ábrán is látható, hogyan a CLI-kódtárak használatával megvalósított SCIM szolgáltatás által biztosított Microsoft épület ilyen szolgáltatások lefordítása a metódusok a szolgáltató ezeket a kérelmeket.  

![][4]
*5. ábra: Felhasználók átadásának és megszüntetést feladatütemezési*

1. Az Azure Active Directory az Azure ad-ben a felhasználó mailNickname attribútum értéke egyező externalId attribútumértékkel rendelkező felhasználó számára a szolgáltatás lekérdezi. Hypertext Transfer Protocol (HTTP) kérelem például viselkedésmintáit jyoung-e egy felhasználó Azure Active Directory-mailNickname mintát ebben a példában a lekérdezés kifejezése: 

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának közös nyelvi infrastruktúra kódtárak használatával lett létrehozva, a kérelem fordítja van a szolgáltató lekérdezési metódus hívása.  Itt van ez a módszer aláírása: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ````

   Íme a Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters felület definíciója: 

   ```
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
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
   ```

   A lekérdezés egy felhasználó a externalId attribútum egy megadott értékkel a következő mintát a lekérdezés metódusnak átadott argumentumok értékei a következők: 
   * a paraméterek. AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * a paraméterek. AlternateFilters.ElementAt(0). ÖsszehasonlítóOperátor: ComparisonOperator.Equals
   * a paraméterek. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Ha a válaszban a webszolgáltatást, amely megfelel a felhasználó mailNickname attribútum értéke externalId attribútumértékkel rendelkező felhasználó nem ad vissza minden olyan felhasználók, majd az Azure Active Directory kéri, hogy a szolgáltatás üzembe helyezése a megfelelő felhasználó az Azure Active Directoryban.  Íme egy példa a kérés: 

   ```
     POST https://.../scim/Users HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
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
   ```

   A közös nyelvi infrastruktúra kódtárak SCIM szolgáltatások megvalósításához a Microsoft által biztosított kérelmet lefordítása tenné a szolgáltató a Create metódus hívása.  A Create metódus az aláírás rendelkezik: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create (
       Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
       string correlationIdentifier);
   ```

   Az egy felhasználó kiépítésére irányuló kérést az erőforrás argumentum értéke egy példányát a Microsoft.SystemForCrossDomainIdentityManagement. A Microsoft.SystemForCrossDomainIdentityManagement.Schemas könyvtárban meghatározott Core2EnterpriseUser osztály.  Ha a felhasználó kiépítésére irányuló kérést sikeres, a metódus végrehajtásának várhatóan a Microsoft.SystemForCrossDomainIdentityManagement egy példányát adja vissza. Az újonnan létrehozott felhasználó egyedi azonosítója az azonosító tulajdonsága értékét Core2EnterpriseUser osztály.  

3. Frissíteni egy felhasználó által az SCIM fronted identitás tárolóban található ismert, Azure Active Directory által a szolgáltatás egy kérelmet, mint például az, hogy a felhasználó aktuális állapotának kér folytatódik: 

   ```
     GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   A beépített, az a közös nyelvi infrastruktúra kódtáraival SCIM szolgáltatások megvalósításához a Microsoft által biztosított szolgáltatás a kérelem lefordítani a szolgáltató a lekérése metódus hívása.  Íme a podpis metody olvashatók be: 

    ```
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
   ```

   A példában a felhasználó aktuális állapotának beolvasására irányuló kérelem, az értékeket a paraméterek argumentum értékeként megadott objektum tulajdonságai a következők: 
  
   * Azonosító: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Ha a hivatkozási attribútum frissíteni kell, akkor az Azure Active Directory-e a hivatkozási attribútum az ügyfélidentitás-tárolóval, az aktuális értékének fronted a szolgáltatás már meghatározni a szolgáltatás lekérdezi ezt az attribútumot az Azure Active értéke megegyezik. A könyvtár. A felhasználóknak, amelyek a jelenlegi érték lekérdezése követi, így egyetlen attribútuma a vezetői attribútumához. Íme egy példa kérést megállapítására, hogy a kezelő attribútum egy adott felhasználói objektum jelenleg jogosult-e a megadott érték: 

   ```
     GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq  2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
     Authorization: Bearer ...
   ```

   Az attribútumok lekérdezési paraméter "ID", érték, amely jelzi, ha felhasználói objektum létezik, amely eleget tesz a kifejezést a szűrő lekérdezési paraméter értékeként megadott, akkor is, a szolgáltatás várhatóan az "urn: ietf:params:scim:schemas:core:2.0: Felhasználó"vagy"urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"erőforrás, beleértve a csak az adott erőforrás"ID"attribútum értéke.  Értékét a **azonosító** attribútum ismert, hogy a kérelmezőnek. A szűrő lekérdezési paraméter; az érték szerepel. a kérdezi azt célja ténylegesen kérelem minimális erőforrás reprezentációját, amely azt jelzi-e az összes ilyen objektum létezik, a szűrőkifejezés teljesíthetők.   

   Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának közös nyelvi infrastruktúra kódtárak használatával lett létrehozva, a kérelem fordítja van a szolgáltató lekérdezési metódus hívása. A paraméterek argumentum értékeként megadott objektum tulajdonságainak értéke a következők: 
  
   * a paraméterek. AlternateFilters.Count: 2
   * a paraméterek. AlternateFilters.ElementAt(x). AttributePath: "ID"
   * a paraméterek. AlternateFilters.ElementAt(x). ÖsszehasonlítóOperátor: ComparisonOperator.Equals
   * a paraméterek. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * a paraméterek. AlternateFilters.ElementAt(y). AttributePath: "manager"
   * a paraméterek. AlternateFilters.ElementAt(y). ÖsszehasonlítóOperátor: ComparisonOperator.Equals
   * a paraméterek. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * a paraméterek. RequestedAttributePaths.ElementAt(0): "ID"
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Itt az index x értéke lehet 0 és az index y értéke 1, előfordulhat, hogy lehet vagy x értéke lehet 1 és y értéke 0, attól függően, a szűrő lekérdezési paraméter a kifejezések sorrendjét.   

5. Íme egy példa egy kérést az Azure Active Directoryból frissíteni egy felhasználó egy SCIM-szolgáltatáshoz: 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
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
   ```

   A Microsoft Common Language infrastruktúra kódtárak SCIM-szolgáltatások végrehajtásának lenne a kérelem lefordítása a szolgáltató frissítési módszer hívása. Itt látható a frissítés metódus aláírása: 

   ```
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
   ```

    A példában egy kérelem egy felhasználó frissítéséhez a javítás argumentum értékeként megadott objektum rendelkezik a tulajdonságok értékeit: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (Mint PatchRequest2 PatchRequest). Operations.Count: 1
   * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). OperationName: OperationName.Add
   * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Path.AttributePath: "manager"
   * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.Count: 1
   * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.ElementAt(0). Referencia: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (Mint PatchRequest2 PatchRequest). Operations.ElementAt(0). Value.ElementAt(0). Érték: 2819c223-7f76-453a-919d-413861904646

6. Megszüntetése a felhasználói identitás adattárba fronted SCIM szolgáltatás által, az Azure AD egy kérést küld például: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Ha a szolgáltatás a Microsoft által biztosított SCIM-szolgáltatások végrehajtásának közös nyelvi infrastruktúra kódtárak használatával lett létrehozva, a kérelem fordítja van a Delete metódust a szolgáltató hívása.   Ez a módszer a aláírással rendelkezik: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Az objektum az resourceIdentifier argumentum értékeként megadott a tulajdonságok értékeit a példában egy kérelem megszüntetése a felhasználó rendelkezik: 

   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Csoport üzembe helyezést és megszüntetést
A következő ábra azt mutatja, hogy Azure AcD által SCIM szolgáltatásba küldött üzeneteket a csoportnak egy másik ügyfélidentitás-tárolóval, az életciklusnak a kezelésében.  Ezeket az üzeneteket a felhasználók három módon vonatkozó üzeneteket különböznek: 

* Az egy adott csoport erőforrás-séma ki van jelölve `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* Csoportok beolvasására irányuló kérelmek kötni, hogy a tag attribútum ki lesznek zárva a válasz a kérésre megadott bármely erőforrás.  
* Határozza meg, hogy rendelkezik-e a hivatkozási attribútum egy adott értéket a kérelmek azok a tag attribútum kapcsolatban.  

![][5]
*6. ábra: Az üzembe helyezést és megszüntetést feladatütemezési csoport*

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Felhasználói kiépítés és megszüntetés SaaS-alkalmazások automatizálása](user-provisioning.md)
* [A felhasználók átadásának attribútumleképezések testreszabása](customize-application-attributes.md)
* [Attribútum-leképezéshez kifejezések írása](functions-for-customizing-application-data.md)
* [A felhasználók átadásának Hatókörszűrő](define-conditional-rules-for-provisioning-user-accounts.md)
* [Alkalmazáskiépítési értesítések](user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
