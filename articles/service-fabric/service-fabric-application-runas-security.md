---
title: "További tudnivalók az Azure mikroszolgáltatások biztonsági házirendek |} Microsoft Docs"
description: "A rendszer és a helyi biztonsági fiókok, beleértve a SetupEntry pont, amelyen egy alkalmazást kell valamilyen privilegizált művelet végrehajtása előtt a Service Fabric-alkalmazás futtatása áttekintése"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: mfussell
ms.openlocfilehash: b2ff715d8225bd0a9c7f6108f8804cdfa3189cc8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-security-policies-for-your-application"></a>Biztonsági házirendek beállítása az alkalmazáshoz
Azure Service Fabric használatával biztonságossá teheti a különböző felhasználói fiókok a fürtben futó alkalmazás számára. A Service Fabric is lehetővé teszi az alkalmazások által használt felhasználói fiók – központi telepítés alkalmával például erőforrásokat, a fájlokat, a könyvtárak és a tanúsítványok biztonságos. Így futó alkalmazást, még akkor is, a megosztott üzemeltetési környezetben, nagyobb biztonságot nyújt, egymástól.

Alapértelmezés szerint a Service Fabric alkalmazások futnak, a fiók, amely alatt futó a Fabric.exe folyamatban. A Service Fabric is lehetővé teszi a helyi felhasználói fiók vagy helyi rendszerfiók, az alkalmazás jegyzékében belül megadott alkalmazások futtatásához. Támogatott helyi rendszer fiók típusok **LocalUser**, **NetworkService**, **LocalService**, és **LocalSystem**.

 Ha a számítógépén a Service Fabric Windows Server az adatközpontban található az önálló telepítő használatával, az Active Directory tartományi fiókok, beleértve a csoportosan felügyelt szolgáltatásfiókok is használhatja.

Hozzon létre felhasználói csoportokat, hogy egy vagy több felhasználó is adható hozzá mindegyik csoport felügyelete együtt, és megadása. Ez akkor hasznos, ha több felhasználó különböző belépési pontot, és bizonyos közös jogosultságok, amelyek elérhetők a csoportok szintjén van szükségük.

## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>A telepítő belépési pont vonatkozó házirend konfigurálása
A [alkalmazás és szolgáltatás jegyzékfájlokban](service-fabric-application-and-service-manifests.md), a telepítő belépési pont **SetupEntryPoint**, egy kiemelt belépési pontja, amelynek ugyanazokat a hitelesítő adatokat, mint a Service Fabric (általában a *NetworkService* fiók) más belépési pont előtt. A megadott végrehajtható fájl **EntryPoint** általában a hosszan futó szolgáltatás gazdagép legyen. Hogy egy külön telepítő belépési pont elkerülhető, hogy jogosultságokkal történő futtatása az adatszolgáltatás gazdájának végrehajtható magas huzamosabb ideig. A végrehajtható fájl, amely **EntryPoint** megadja futtatása **SetupEntryPoint** sikeresen kilép. Az eredményül kapott folyamat figyeli, és újra kell indítani, és újra kezdődik **SetupEntryPoint** Ha valaha is leáll, vagy összeomlik.

Egy egyszerű service manifest példa bemutatja a SetupEntryPoint és a fő belépési pont a szolgáltatás a következő:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
```

### <a name="configure-the-policy-by-using-a-local-account"></a>Konfigurálja a házirendet egy helyi fiók használatával
Miután konfigurálta a szolgáltatást, hogy a telepítő belépési pont van, módosíthatja a biztonsági engedélyek, amelyek az alkalmazás jegyzékében alatt fusson. A következő példa bemutatja, hogyan konfigurálhatja a szolgáltatás felhasználói rendszergazdai jogokkal futtatásához.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
```

Először hozzon létre egy **rendszerbiztonsági tagok** egy felhasználónevet, pl. SetupAdminUser szakasz ismerteti. Ez azt jelzi, hogy a felhasználó tagja a rendszergazdák rendszer csoport.

Ezután bontsa a **ServiceManifestImport** területen házirend alkalmazása a rendszerbiztonsági konfigurálása **SetupEntryPoint**. Ez alapján a Service Fabric, hogy ha a **MySetup.bat** futtatása, kell lennie `RunAs` rendszergazdai jogosultságokkal. Fényében, hogy már rendelkezik *nem* házirend alkalmazása a fő belépési ponthoz, a kód **MyServiceHost.exe** fut a rendszer a **NetworkService** fiók. Ez az az alapértelmezett fiókot, amelyet a szolgáltatás összes belépési pont alatt futnak.

Most adjunk MySetup.bat fájl tesztelése a rendszergazdai jogosultságokat a Visual Studio-projektet. A Visual Studióban kattintson a jobb gombbal a projekt, és adjon hozzá MySetup.bat nevű új fájlt.

Ezt követően győződjön meg arról, hogy a MySetup.bat fájl megtalálható-e a service-csomag. Alapértelmezés szerint nincs. Válassza ki a fájlt, kattintson a jobb gombbal a helyi menü segítségével, és válassza a **tulajdonságok**. A Tulajdonságok párbeszédpanelen győződjön meg arról, hogy **másolása a kimeneti könyvtárba** értéke **másolhatja, ha újabb**. Tekintse meg a következő képernyőképet.

![A Visual Studio CopyToOutput SetupEntryPoint kötegelt fájl][image1]

Most nyissa meg a MySetup.bat fájlt, és adja hozzá a következő parancsokat:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Ezt követően hozza létre, és a megoldás telepítése egy helyi fejlesztési fürtöt. A szolgáltatás elindult, a Service Fabric Explorerben látható, miután láthatja, hogy a két módon sikeres volt-e a MySetup.bat fájl. Nyisson meg egy PowerShell-parancssort, és írja be:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Jegyezze fel a csomópont, ahol a szolgáltatás telepítve lett, és elindítani a Service Fabric Explorerben – például csomópont 2 neve. Ezután keresse meg az alkalmazás példány munkahelyi mappát értékét bemutató out.txt fájlt **TestVariable**. Például, ha a szolgáltatás telepítve van a csomópont 2, majd lépjen az elérési útját a **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

### <a name="configure-the-policy-by-using-local-system-accounts"></a>A házirend konfigurálása a helyi rendszer fiók használatával
Gyakran célszerű a indítási parancsfájl futtatásához rendszergazdai fiók helyett a helyi rendszer fiók használatával. A RunAs házirend fut, a Rendszergazdák csoport tagjaként általában nem működik jól, mert a felhasználói hozzáférés felügyelete (UAC) alapértelmezés szerint engedélyezve vannak. Ilyen esetben **felhasználóként történő futása a SetupEntryPoint LocalSystem, ahelyett, hogy a helyi Rendszergazdák csoportba felvett a javaslat**. A következő példa bemutatja a LocalSystem fiókként való futtatásra SetupEntryPoint beállítása:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

Linux-fürtök esetén a szolgáltatás vagy a telepítő futtatásához belépési pont mint **legfelső szintű**, megadhatja a **AccountType** , **LocalSystem**.

## <a name="start-powershell-commands-from-a-setup-entry-point"></a>A telepítő belépési pontról indítsa el a PowerShell-parancsok
A PowerShell futtatásához a **SetupEntryPoint** pont, futtathatja **PowerShell.exe** parancsfájlban, amely egy PowerShell-fájlra mutat. Először adjon hozzá egy PowerShell fájlt például a projekt-- **MySetup.ps1**. Ne felejtse el, állítsa be a *másolhatja, ha újabb* tulajdonságot, hogy a fájl a szolgáltatáscsomagot is megtalálhatók. A következő példa bemutatja egy minta-kötegfájl MySetup.ps1, amelyek a nevezett rendszer környezeti változó beállítása nevű PowerShell fájl elinduló **TestVariable**.

A PowerShell fájl indítása MySetup.bat:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

A PowerShell fájlban adja hozzá a következő, a rendszer környezeti változó beállítása:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Alapértelmezés szerint a parancsfájl futtatása azt ellenőrzi, hogy az alkalmazás mappájában nevű **működik** fájlok. Ebben az esetben, amikor MySetup.bat fut, történjen a MySetup.ps1 fájl található abban a mappában, amely az alkalmazás **kódcsomag** mappa. Ez a mappa módosításához állítsa a munkamappa:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="use-console-redirection-for-local-debugging"></a>A hibakereséshez a helyi konzol átirányítást használ
Bizonyos esetekben célszerű tekintse meg a konzol kimeneti hibakeresési célra parancsfájl futtatását. Ehhez az szükséges, beállíthat egy konzol átirányítási házirendet, amely a kimeneti fájlba írja. A kimeneti fájl beíródik nevű alkalmazás mappát **napló** a csomóponton, ha az alkalmazás üzemel, és futtassa. (Lásd az előző példában megkeresése a.)

> [!WARNING]
> Soha ne használja a konzol átirányítási házirend a kérelmet, mert ez hatással lehet az alkalmazás feladatátvételi éles környezetben telepített. *Csak* használja ezt a helyi fejlesztési és hibakeresési célra.  
> 
> 

A következő példa bemutatja, hogy a konzol átirányítása FileRetentionCount érték beállítása:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Ha most módosítja a MySetup.ps1 fájl, amelybe írni egy **Echo** parancsban, ez fogja írni a kimeneti fájl hibakeresési célra:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

**A parancsfájl hibakeresése azonnal távolítsa el a konzol átirányítási házirend**.

## <a name="configure-a-policy-for-service-code-packages"></a>A szolgáltatás kód csomagok házirend konfigurálása
Az előző lépésben megtudhatta, hogyan RunAs házirend alkalmazása setupentrypoint típust. Nézzük kissé mélyebb ismerteti, hogyan lehet létrehozni a különböző rendszerbiztonsági tagok közül melyek szolgáltatás házirendjei is alkalmazható.

### <a name="create-local-user-groups"></a>Helyi felhasználói csoportok létrehozása
Adja meg, és hozzon létre felhasználói csoportokat, amelyek lehetővé teszik egy vagy több felhasználót a csoporthoz lehet hozzáadni. Ez akkor hasznos, ha több felhasználó különböző belépési pontot, és bizonyos közös jogosultságok, amelyek elérhetők a csoportok szintjén van szükségük. A következő példa bemutatja a csoport **LocalAdminGroup** , amely rendszergazdai jogosultságokkal rendelkezik. Két felhasználók Customer1 és Customer2, a helyi csoport tagjai válnak.

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

### <a name="create-local-users"></a>Helyi felhasználók létrehozása
Létrehozhat egy helyi felhasználót, amelyek segítségével biztonságossá tétele az alkalmazásban egy szolgáltatás. Ha egy **LocalUser** fióktípus szakaszban van megadva a rendszerbiztonsági tagoknak az alkalmazás jegyzékének Service Fabric helyi felhasználói fiókokat hoz gépekre, ahol az alkalmazás központi telepítése. Alapértelmezés szerint ezek a fiókok nem rendelkeznek (például a következő mintában Customer3) az alkalmazás jegyzékében megadottakkal azonos néven. Ehelyett dinamikusan jön létre, és véletlenszerű jelszót.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Ha egy alkalmazás megköveteli, hogy a felhasználói fiókkal és jelszóval (például, hogy engedélyezze az NTLM-hitelesítés) összes gépen azonos, a fürtjegyzékben NTLMAuthenticationEnabled kell értéke igaz. A fürtjegyzékben is meg kell adnia egy NTLMAuthenticationPasswordSecret, amely ugyanazt a jelszót összes számítógépével előállítására szolgál.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

### <a name="assign-policies-to-the-service-code-packages"></a>A szolgáltatás kód csomagok házirendek hozzárendelése
A **RunAsPolicy** szakasz egy **ServiceManifestImport** a rendszerbiztonsági tagok szakaszában a kódcsomag futtatásához használt fiókot adja meg. A felhasználói fiókok a rendszerbiztonsági tagok szakaszban kód csomagok a szolgáltatás-jegyzékfájlból is társítja. Ez a beállítás vagy a fő belépési pontok adhat meg, vagy megadhat `All` egyaránt vonatkozik. A következő példa bemutatja a különböző szabályzatok:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Ha **EntryPointType** nincs megadva, az alapértelmezett érték az EntryPointType = "Main". Adja meg **SetupEntryPoint** akkor különösen akkor hasznos, ha bizonyos magas szintű jogosultságokat igénylő telepítési műveletekhez a rendszerfiók futtatja. A tényleges kódját egy alacsonyabb szintű szolgáltatásfiók alatt futhatnak.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Minden szolgáltatás kód csomagok egy alapértelmezett házirend alkalmazása
Használja a **DefaultRunAsPolicy** szakaszban adja meg az összes kód alapértelmezett felhasználói fiók csomagok, amelyeket nem kell egy adott **RunAsPolicy** definiálva. Ha a szolgáltatás egy alkalmazás által használt jegyzékfájlban megadott kód csomagokat a legtöbb kell futni ugyanahhoz a felhasználóhoz, az alkalmazás csak definiálhat egy alapértelmezett RunAs házirendet, hogy felhasználói fiókkal. Az alábbi példa azt jelenti, hogy ha a kódcsomag nem rendelkezik egy **RunAsPolicy** megadva, a kódcsomag kell futnia a **MyDefaultAccount** rendszerbiztonsági tagok szakaszában megadott.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```
### <a name="use-an-active-directory-domain-group-or-user"></a>Az Active Directory tartományi csoport vagy felhasználó használata
A Service Fabric az önálló telepítő segítségével a Windows Server telepített példánya futtathatja a hitelesítő adatok a szolgáltatás az Active Directory-felhasználó vagy csoport fiókját. Ez az Active Directory helyszíni saját tartományában, és nincs az Azure Active Directoryval (Azure AD). Tartományi felhasználó vagy csoport segítségével érheti el más erőforrások (például fájlmegosztások) a tartományban, amely engedéllyel rendelkezik.

A következő példa bemutatja az Active Directory-felhasználó nevű *tesztfelhasználó néven* a tartomány-tanúsítvány használatával titkosítja jelszó az úgynevezett *MyCert*. Használhatja a `Invoke-ServiceFabricEncryptText` PowerShell-parancsot a titkos titkosított szöveg létrehozásához. Lásd: [Service Fabric-alkalmazások a titkos kulcsok kezelése](service-fabric-application-secret-management.md) részleteiről.

Ha már telepítette a titkosított jelszót a helyi számítógép egy sávon kívüli módszer használatával a tanúsítvány titkos kulcsának (Azure-ban ez az Azure Resource Manager használatával). Ezt követően a Service Fabric telepíti a szolgáltatáscsomagnak a gép, esetén képes visszafejteni a titkos kulcsot, és (valamint a felhasználónév) hitelesítést és az Active Directory futtatásához használandó ezeket a hitelesítő adatokat.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```
### <a name="use-a-group-managed-service-account"></a>Adjon meg egy csoportot felügyelt szolgáltatásfiók.
A Service Fabric az önálló telepítő segítségével a Windows Server telepített példánya a service is futhat, a csoportosan felügyelt szolgáltatásfiókok (gMSA). Vegye figyelembe, hogy ez az Active Directory a helyszíni tartományán belüli és nem az Azure Active Directoryval (Azure AD). Csoportosan felügyelt szolgáltatásfiók használatával nem kell jelszót vagy a titkosított jelszót a `Application Manifest`.

A következő példa bemutatja, hogyan nevű csoportosan felügyelt szolgáltatásfiókok létrehozása *svc-teszt$*; központi telepítése a fürt csomópontjai; a felügyelt szolgáltatásfiók és a konfigurálása egyszerű felhasználónév.

##### <a name="prerequisites"></a>Előfeltételek.
- A tartomány kell KDS-gyökérkulcsot.
- A tartományban kell lennie egy Windows Server 2012 vagy újabb működési szintjét.

##### <a name="example"></a>Példa
1. Active directory tartományi rendszergazdának létre csoportosan felügyelt szolgáltatás fiókja rendelkezik a `New-ADServiceAccount` parancsmagot, és győződjön meg arról, hogy a `PrincipalsAllowedToRetrieveManagedPassword` tartalmazza az összes, a service fabric-csomópont. Vegye figyelembe, hogy `AccountName`, `DnsHostName`, és `ServicePrincipalName` egyedinek kell lennie.
```
New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
```
2. A service fabric-csomópont minden egyes (például `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), telepítése és tesztelése a csoportosan felügyelt szolgáltatásfiókot.
```
Add-WindowsFeature RSAT-AD-PowerShell
Install-AdServiceAccount svc-Test$
Test-AdServiceAccount svc-Test$
```
3. Az egyszerű, valamint a felhasználói hivatkozni RunAsPolicy konfigurálni.
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
      </Policies>
   </ServiceManifestImport>
  <Principals>
    <Users>
      <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Rendelje hozzá a biztonsági házirend HTTP és HTTPS-végpontok
Ha RunAs házirend vonatkozik egy szolgáltatás, és a szolgáltatás jegyzékfájl deklarálja a HTTP protokoll végpont erőforrások, meg kell adnia egy **SecurityAccessPolicy** arról, hogy ezeket a végpontokat rendelt portok megfelelően a futtató felhasználói fiók, amely a szolgáltatás fut a felsorolt hozzáférés-vezérlés. Ellenkező esetben **http.sys** nem rendelkezik hozzáféréssel a szolgáltatáshoz, és sikertelen hívások kapott az ügyféltől. A következő példa a Customer1 fiók vonatkozik nevű végpont **EndpointName**, amely azt teszi teljes körű hozzáférési jogosultságokat.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

A HTTPS-végpont esetében is a tanúsítvány vissza az ügyfélnek jelzésére. Ehhez a **EndpointBindingPolicy**, a tanúsítványok szakaszban található az alkalmazás jegyzékében meghatározott tanúsítvánnyal.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```
## <a name="upgrading-multiple-applications-with-https-endpoints"></a>A https-végpontnak több alkalmazás frissítése
Ügyeljen arra, hogy használni szeretné a **ugyanazt a portot** http használata esetén egy alkalmazás különböző példányai**s**. A hiba oka, hogy a Service Fabric nem lehet frissíteni a tanúsítvány az alkalmazás példányai közül. Ha például 1 vagy alkalmazás 2 mindkét alkalmazás szeretné, hogy az 1. tanúsítvány frissítése a cert 2. Ha a frissítés történik, a Service Fabric előfordulhat, hogy rendelkezik kiüríti az 1. tanúsítvány regisztrálása a következőben: http.sys annak ellenére, hogy a többi alkalmazás továbbra is használja. Ennek megelőzése érdekében a Service Fabric észleli, hogy már egy másik alkalmazáspéldány regisztrálva a porton (mert a http.sys) a tanúsítványhoz, és a művelet sikertelen lesz.

Ezért a Service Fabric nem támogatja a frissítést két különböző szolgáltatásokat **ugyanazt a portot** másik alkalmazási esetekben. Ez azt jelenti ugyanazt a tanúsítványt nem használhat a különböző szolgáltatások ugyanazt a portot. Ha egy megosztott tanúsítvány rendelkezik ugyanazon a porton van szüksége, győződjön meg arról, hogy a szolgáltatások egy elhelyezési korlátozás rendelkező különböző gépeken kerülnek szeretné. Vagy fontolja meg a Service Fabric dinamikus portok lehetőség szerint minden egyes szolgáltatás minden egyes alkalmazás-példányban. 

Ha megjelenik egy frissítés sikertelen lesz, a https, hiba, figyelmeztetés, amely szerint "A Windows HTTP-kiszolgáló API nem támogatja több tanúsítvány egy portot használó alkalmazásokhoz."

## <a name="a-complete-application-manifest-example"></a>A teljes alkalmazás jegyzékének példa
A következő alkalmazás jegyzékfájlja jeleníti meg a különböző beállításainak jelentős része:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
* [Az alkalmazásmodell ismertetése](service-fabric-application-model.md)
* [Adja meg a szolgáltatás jegyzék erőforrások](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
