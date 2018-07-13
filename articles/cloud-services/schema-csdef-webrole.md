---
title: Az Azure Cloud Services def WebRole séma |} A Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: 60
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: e548841f334705aa71ada92c43ccde207a1f6318
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002313"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Azure Cloud Services – definíciós WebRole séma
Az Azure webes szerepkör egy szerepkör, amely az IIS 7, például az ASP.NET, a PHP, a Windows Communication Foundation és a FastCGI támogatja a webes alkalmazások programozását testreszabott.

Az alapértelmezett kiterjesztése a szolgáltatásdefiníciós fájl .csdef esetében.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Alapszintű szolgáltatásdefiníciós sémában egy webes szerepkör  
A webes szerepkör tartalmazó szolgáltatásdefiníciós fájl alapvető formátuma a következő.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Séma elemei  
A szolgáltatásdefiníciós fájlt tartalmazza ezeket az elemeket, ez a témakör későbbi részeiben részletesen ismertetjük:  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Beállítás](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Végpontok](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Tanúsítványok](#Certificates)

[Tanúsítvány](#Certificate)

[Import](#Imports)

[Importálás](#Import)

[Modul](#Runtime)

[Környezet](#Environment)

[A változó](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Helyek](#Sites)

[Hely](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Kötések](#Bindings)

[Kötés](#Binding)

[Indítás](#Startup)

[Tevékenység](#Task)

[Tartalom](#Contents)

[Tartalom](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> WebRole  
A `WebRole` elem ismerteti egy szerepkör, amely a webes konfigurálóalkalmazás-programozási igényeire szabva van, és az IIS 7 ASP.NET támogatja. Egy szolgáltatás nulla vagy több webes szerepkörök is tartalmazhat.

A következő táblázat ismerteti az attribútumai a `WebRole` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. A webes szerepkör nevét. A szerepkör nevének egyedinek kell lennie.|  
|enableNativeCodeExecution|logikai|Választható. Az alapértelmezett érték `true`; natív alapértelmezés szerint engedélyezve vannak a programkód és a teljesen megbízható. Ez az attribútum beállítása `false` tiltsa le a webes szerepkör nativní kód végrehajtását, és használja helyettük a Azure részleges megbízhatóságot.|  
|vmsize|sztring|Választható. Állítsa be az értéket, hogy engedélyezett a virtuálisgép-méretének módosítása a szerepkörhöz. Az alapértelmezett érték `Small`. További információkért lásd: [a Cloud Services virtuálisgép-méretek](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a> ConfigurationSettings  
A `ConfigurationSettings` elem írja le a webes szerepkör konfigurációs beállításainak gyűjteményei. Az elem szülője a `Setting` elemet.

##  <a name="Setting"></a> Beállítás  
A `Setting` elem egy név-érték párt, amely meghatározza egy szerepkör példányának konfigurációs beállítását ismerteti.

A következő táblázat ismerteti az attribútumai a `Setting` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. Egy egyedi nevet a konfigurációs beállítás.|  

A szerepkör konfigurációs beállításai olyan név-érték párok, melyek a szolgáltatásdefiníciós fájlban deklarálva, és állítsa be a konfigurációs fájlban.

##  <a name="LocalResources"></a> LocalResources  
A `LocalResources` elem a helyi tároló-erőforrások gyűjteménye, egy webes szerepkör írja le. Az elem szülője a `LocalStorage` elemet.

##  <a name="LocalStorage"></a> LocalStorage  
A `LocalStorage` elem azonosítja egy helyi tároló-erőforrás, amely a fájl rendszer tárterületet biztosít a szolgáltatás futásidőben. Helyi tároló-erőforrások nulla vagy több szerepkör adhat meg.

> [!NOTE]
>  A `LocalStorage` elem gyermekeként is megjelenhetnek a `WebRole` elemet az Azure SDK korábbi verzióival való kompatibilitás támogatására.

A következő táblázat ismerteti az attribútumai a `LocalStorage` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. Egy egyedi nevet a helyi tárolóban.|  
|cleanOnRoleRecycle|logikai|Választható. Azt jelzi, hogy a helyi tárolóban meg kell tisztítani a szerepkör újraindítását követően. Alapértelmezett érték `true`.|  
|hogy a sizeinmb értéke|int|Választható. A kívánt mennyiségű tárterület lefoglalása a helyi tárolóhoz, MB-ban. Ha nincs megadva, a lefoglalt lemezterület alapértelmezett, 100 MB. A tárolóhely lehet kiosztani minimális mérete 1 MB.<br /><br /> A helyi erőforrások maximális mérete a virtuális gép mérete függ. További információkért lásd: [a Cloud Services virtuálisgép-méretek](cloud-services-sizes-specs.md).|  
  
A neve annak a könyvtárnak a helyi tároló erőforráshoz van lefoglalva a name attribútum a megadott érték tartozik.

##  <a name="Endpoints"></a> Végpontok  
A `Endpoints` elem belső bemenet (külső), a gyűjtemény írja le, és a példány bemeneti végpontja egy. Az elem szülője a `InputEndpoint`, `InternalEndpoint`, és `InstanceInputEndpoint` elemeket.

Bemeneti és a belső végpont külön van lefoglalva. Szolgáltatásként lehet 25 bemeneti, belső, összesen és példány bemeneti végpontokat, amely kiosztható egy szolgáltatásban megengedett a 25 szerepkörök között. Például ha 5 szerepkörökkel rendelkeznek szerepkörönként 5 bemeneti végpontok foglalhat le vagy foglalhat egyetlen szerepkörhöz 25 bemeneti végpontok, illetve 1 bemeneti végpont minden 25 szerepkörökhöz foglalhat le.

> [!NOTE]
>  Minden szerepkör telepítve van egy példányra szerepkörönként. Az előfizetéshez tartozó kiépítés alapértelmezett legfeljebb 20 magot és így legfeljebb 20 példányt egy szerepkör. Ha az alkalmazás igényel, mint az alapértelmezett, lásd: provisioning által biztosított további példányok [számlázási, az előfizetés-kezelési és a kvóta támogatási](https://azure.microsoft.com/support/options/) bővebben a kvóta növelését.

##  <a name="InputEndpoint"></a> Bemeneti végponthoz  
A `InputEndpoint` elem írja le a webes szerepkör külső végpontját.

Megadhatja, hogy több végpontokat kombinációja HTTP, HTTPS, UDP és TCP-végpontokhoz. Bármely úgy dönt, hogy a bemeneti végpontok portszámot is megadhat, de a portszámokat, az egyes szerepkörökhöz a szolgáltatásban megadott egyedinek kell lennie. Például ha megadja, hogy egy webes szerepkörben 80-as portot a HTTP és a 443-as HTTPS-hez, előfordulhat, hogy majd meg, hogy egy második webes szerepkör használja 8080-as port HTTP-és port 8043 HTTPS-hez.

A következő táblázat ismerteti az attribútumai a `InputEndpoint` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. A külső végpont egyedi nevét.|  
|protokoll|sztring|Kötelező. Az átviteli protokoll a külső végpont számára. A webes szerepkör, a lehetséges értékek a következők `HTTP`, `HTTPS`, `UDP`, vagy `TCP`.|  
|port|int|Kötelező. A külső végpont portja. Bármely választja portszámot is megadhat, de a portszámokat, az egyes szerepkörökhöz a szolgáltatásban megadott egyedinek kell lennie.<br /><br /> A lehetséges értékek a tartomány 1 és 65535 közé, tartományba (az Azure SDK 1.7 vagy újabb verzió).|  
|tanúsítvány|sztring|HTTPS-végpont szükséges. Egy által meghatározott tanúsítvány nevére a `Certificate` elemet.|  
|localPort|int|Választható. Adja meg a végpont belső kapcsolatokhoz használt port. A `localPort` attribútum a külső portot a végponton képez le egy belső portját szerepet. Ez akkor hasznos, ahol egy szerepkör közölnie kell egy belső összetevő egy porton, hogy eltérő, amely ki van téve külsőleg forgatókönyvekben.<br /><br /> Ha nincs megadva, az értékét `localPort` ugyanaz, mint a `port` attribútum. Állítsa az értékét `localPort` a "*" automatikusan hozzárendelni egy szabad portot, amely felderíthető a futtatókörnyezeti API használata.<br /><br /> A lehetséges értékek a tartomány 1 és 65535 közé, tartományba (az Azure SDK 1.7 vagy újabb verzió).<br /><br /> A `localPort` attribútum értéke csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.|  
|ignoreRoleInstanceStatus|logikai|Választható. Ha ez az attribútum értéke `true`, egy szolgáltatás állapotát a rendszer figyelmen kívül hagyja, és a végpont nem távolítja el a terheléselosztó által. Az érték `true` hasznos foglalt szolgáltatás példányai a hibakereséshez. Az alapértelmezett érték `false`. **Megjegyzés:** végpont is fogadhatja a forgalom még ha a szerepkör nem kész állapotú.|  
|loadBalancerProbe|sztring|Választható. A bemeneti végponthoz társított tartozó terheléselosztói szonda neve. További információkért lásd: [LoadBalancerProbe séma](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a> InternalEndpoint  
A `InternalEndpoint` elem egy belső végpont egy webes szerepkörben való írja le. Egy belső végpont csak érhető el, a szolgáltatásban; egyéb szerepkör példányai nem érhető el a szolgáltatás kívüli ügyfelek számára. Webes szerepkörök, amelyek nem tartalmaznak a `Sites` elem legfeljebb egyetlen HTTP, az UDP vagy TCP belső végpont.

A következő táblázat ismerteti az attribútumai a `InternalEndpoint` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. Egy egyedi nevet a belső végpont számára.|  
|protokoll|sztring|Kötelező. Az átviteli protokoll a belső végpont számára. Lehetséges értékek a következők `HTTP`, `TCP`, `UDP`, vagy `ANY`.<br /><br /> Érték `ANY` Megadja, hogy minden protokoll, bármely porton engedélyezve van.|  
|port|int|Választható. Belső elosztott terhelésű kapcsolatok a végpont által használt port. Egy elosztott terhelésű végpontot használ két port. A nyilvános IP-cím használt port, és a magánhálózati IP-címet a használt port. Ezek általában azonos az értékük, de Ön a eltérő portok használatára.<br /><br /> A lehetséges értékek a tartomány 1 és 65535 közé, tartományba (az Azure SDK 1.7 vagy újabb verzió).<br /><br /> A `Port` attribútum értéke csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.|  

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
A `InstanceInputEndpoint` elem ismerteti az egy példány egy webes szerepkör bemeneti végpontot. Egy példány bemeneti végpont porttovábbítást a load balancer használatával egy adott szerepkörpéldány társítva. Minden példány bemeneti végpontja egy adott portot a lehetséges portok tartománya van leképezve. Az elem szülője a `AllocatePublicPortFrom` elemet.

A `InstanceInputEndpoint` elem csak akkor érhető el a használatával az Azure SDK 1.7-es verzió vagy újabb.

A következő táblázat ismerteti az attribútumai a `InstanceInputEndpoint` elemet.
  
| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. Egy egyedi nevet a végpont.|  
|localPort|int|Kötelező. Megadja a belső port, amely az összes szerepkörpéldány fogadni fog annak érdekében, hogy a továbbított bejövő forgalom fogadására a terheléselosztótól. A lehetséges értékek közötti tartományba esik 1 és 65535 között lehet.|  
|protokoll|sztring|Kötelező. Az átviteli protokoll a belső végpont számára. A lehetséges értékek: `udp` és `tcp`. Használat `tcp` http/https-alapú forgalmat.|  
  
##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
A `AllocatePublicPortFrom` elem ismerteti, hogy minden példány bemeneti végpont elérésére használható külső ügyfelek által nyilvános porttartományát. Ebből a tartományból lefoglalt és bérlős központi telepítés és a frissítés során minden egyes szerepkör-példány végpont rendelt (VIP) nyilvános port számát. Az elem szülője a `FixedPortRange` elemet.

A `AllocatePublicPortFrom` elem csak akkor érhető el a használatával az Azure SDK 1.7-es verzió vagy újabb.

##  <a name="FixedPort"></a> FixedPort  
A `FixedPort` elem azt határozza meg a portot, a belső végpont, mely lehetővé teszi, hogy a végpont terheléselosztásos kapcsolatok betöltése.

A `FixedPort` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

A következő táblázat ismerteti az attribútumai a `FixedPort` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|port|int|Kötelező. A belső végpont portja. Ez ugyanaz a hatása beállításként az `FixedPortRange` minimális és maximális ugyanahhoz a porthoz.<br /><br /> A lehetséges értékek a tartomány 1 és 65535 közé, tartományba (az Azure SDK 1.7 vagy újabb verzió).|  

##  <a name="FixedPortRange"></a> FixedPortRange  
A `FixedPortRange` elem, amely a belső végpont és a példány bemeneti végpontja hozzárendelt portok tartományát határozza meg, és a portot használja a terheléselosztást csoportok, elosztott terhelésű végpont kapcsolatok.

> [!NOTE]
>  A `FixedPortRange` elem, amelyben található elem függően eltérően működik. Ha a `FixedPortRange` elem van a `InternalEndpoint` elem, megnyílik a terheléselosztóhoz, amelyre a szerepkört futtató összes virtuális gép minimális és maximális attribútumai tartományán belül minden port. Ha a `FixedPortRange` elem van a `InstanceInputEndpoint` elem, megnyílik a szerepkört futtató minden egyes virtuális gépen a minimális és maximális attribútumok tartományán belül csak egy portot.

A `FixedPortRange` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

A következő táblázat ismerteti az attribútumai a `FixedPortRange` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|perc|int|Kötelező. A tartományban minimum port. A lehetséges értékek a tartomány 1 és 65535 közé, tartományba (az Azure SDK 1.7 vagy újabb verzió).|  
|max.|sztring|Kötelező. A tartomány maximális port. A lehetséges értékek a tartomány 1 és 65535 közé, tartományba (az Azure SDK 1.7 vagy újabb verzió).|  

##  <a name="Certificates"></a> Tanúsítványok  
A `Certificates` elem írja le a webes szerepkör-tanúsítványok gyűjteménye. Az elem szülője a `Certificate` elemet. Előfordulhat, hogy egy szerepkörhöz társított tanúsítványok bármilyen számát. A tanúsítványok elemmel további információkért lásd: [módosítsa a szolgáltatásdefiníciós fájlból tanúsítvánnyal](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> Tanúsítvány  
A `Certificate` elem egy tanúsítványt, egy webes szerepkörben társított ismerteti.

A következő táblázat ismerteti az attribútumai a `Certificate` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. Ez a tanúsítvány, hivatkoznia kell rá, amikor egy HTTPS társítva használt nevét `InputEndpoint` elemet.|  
|storeLocation|sztring|Kötelező. Ahol ezt a tanúsítványt a helyi számítógépen is található a tanúsítványtárolóban helye. Lehetséges értékek a következők `CurrentUser` és `LocalMachine`.|  
|storeName|sztring|Kötelező. Ahol ezt a tanúsítványt a helyi gépen található a tanúsítványtárolóban neve. Lehetséges értékek: a beépített tárolónevek `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, vagy bármilyen egyéni tároló nevét. Ha egy egyéni tároló neve van megadva, automatikusan létrejön a tárolóban.|  
|permissionLevel|sztring|Választható. Adja meg a szerepkör folyamatok hozzáférési engedélyeket. Ha azt szeretné, hogy tudják elérni a titkos kulcsot, majd adja meg csak emelt szintű folyamatok `elevated` engedéllyel. `limitedOrElevated` az engedély lehetővé teszi a titkos kulcs elérésére szolgál, az összes szerepkör folyamatokat. A lehetséges értékek: `limitedOrElevated` és `elevated`. Az alapértelmezett érték `limitedOrElevated`.|  

##  <a name="Imports"></a> Import  
A `Imports` elem importálása a modulok egy webes szerepkör, amely a vendég operációs rendszer összetevőket adhat gyűjteménye írja le. Az elem szülője a `Import` elemet. Ez az elem nem kötelező, és a szerepkör importálás csak egy blokk rendelkezhet. 

A `Imports` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

##  <a name="Import"></a> Importálás  
A `Import` elem azt határozza meg a vendég operációs rendszer hozzáadása egy modult.

A `Import` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

A következő táblázat ismerteti az attribútumai a `Import` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|modulename:|sztring|Kötelező. A modul importálása neve. Érvényes importálás modulokra:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Diagnosztika<br /><br /> A RemoteAccess és RemoteForwarder modult a szerepkörpéldány távoli asztali kapcsolatok konfigurálását teszik lehetővé. További információ: [távoli asztali kapcsolat engedélyezése](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> A diagnosztikai modul lehetővé teszi egy szerepkörpéldány a diagnosztikai adatainak összegyűjtése.|  

##  <a name="Runtime"></a> Modul  
A `Runtime` elem környezetiváltozó-beállításainak egy webes szerepkörben, amelyek vezérlik a futtatási környezetet az Azure-beli gazdagéppel folyamat gyűjteménye írja le. Az elem szülője a `Environment` elemet. Ez az elem nem kötelező, és a szerepkör csak egy modul blokk rendelkezhet.

A `Runtime` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

A következő táblázat ismerteti az attribútumai a `Runtime` elem:  

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|executionContext|sztring|Választható. Adja meg a környezetet, amelyben a szerepkör folyamat indul el. Az alapértelmezett környezet `limited`.<br /><br /> -   `limited` – Rendszergazdai jogosultságok nélkül a folyamat elindítása.<br />-   `elevated` – A folyamat elindítása rendszergazdai jogosultságokkal.|  

##  <a name="Environment"></a> Környezet  
A `Environment` elem írja le a webes szerepkör környezetiváltozó-beállításainak gyűjteménye. Az elem szülője a `Variable` elemet. Előfordulhat, hogy a szerepkör tetszőleges számú környezeti változók beállítása.

##  <a name="Variable"></a> A változó  
A `Variable` elem azt határozza meg, egy környezeti változót, a vendég operációs beállítása.

A `Variable` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

A következő táblázat ismerteti az attribútumai a `Variable` elem:  

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. Állítsa be a környezeti változó neve.|  
|érték|sztring|Választható. A környezeti változó beállított értéke. Meg kell adni vagy a value attribútumként vagy egy `RoleInstanceValue` elemet.|  

##  <a name="RoleInstanceValue"></a> RoleInstanceValue  
A `RoleInstanceValue` elem azt határozza meg, ahonnan a változó értékét a vizualizációhoz XPath értékét.

A következő táblázat ismerteti az attribútumai a `RoleInstanceValue` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|XPath|sztring|Választható. Központi telepítési beállítások példány helyének elérési útját. További információkért lásd: [XPath konfigurációs változókat](cloud-services-role-config-xpath.md).<br /><br /> Meg kell adni vagy a value attribútumként vagy egy `RoleInstanceValue` elemet.|  

##  <a name="EntryPoint"></a> EntryPoint  
A `EntryPoint` elem azt határozza meg, a belépési pont szerepkör. Az elem szülője a `NetFxEntryPoint` elemeket. Ezeket az elemeket adja meg az alkalmazás nem az alapértelmezett WaWorkerHost.exe működjön, a belépési pont helyrendszerszerepkör teszi lehetővé.

A `EntryPoint` elem csak akkor érhető el az Azure SDK verzióval 1.5-ös vagy újabb.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
A `NetFxEntryPoint` elem azt határozza meg a program futtatásához egy adott szerepkör esetében.

> [!NOTE]
>  A `NetFxEntryPoint` elem csak akkor érhető el az Azure SDK verzióval 1.5-ös vagy újabb.

A következő táblázat ismerteti az attribútumai a `NetFxEntryPoint` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|AssemblyName|sztring|Kötelező. A belépési pont tartalmazó szerelvény elérési útja és fájlneve. Az elérési út a mappán  **\\%ROLEROOT%\Approot** (nem ad meg  **\\%ROLEROOT%\Approot** a `commandLine`, feltételezhető). **A(z) % ROLEROOT %** egy környezeti változó az Azure által fenntartott és azt jelenti, hogy a legfelső szintű mappa helye a szerepkörhöz. A  **\\%ROLEROOT%\Approot** mappát jelöli, a szerepkör az alkalmazás mappájában.<br /><br /> Az üzemeltethető WEBMAG szerepkörök az elérési út minden esetben viszonyítva a  **\\%ROLEROOT%\Approot\bin** mappát.<br /><br /> A teljes IIS és az IIS Express webes szerepkörök, ha a szerelvény nem található viszonyítva  **\\%ROLEROOT%\Approot** mappában a  **\\%ROLEROOT%\Approot\bin** keres.<br /><br /> Ez csökkenése vissza teljes IIS viselkedése nem javasoljuk az ajánlott eljárás, talán eltávolítva a jövőbeli verzióiban.|  
|targetFrameworkVersion|sztring|Kötelező. A szerelvény készítették, amelyen a .NET-keretrendszer verzióját. Például: `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a> Helyek  
A `Sites` elem ismerteti egy webes szerepkörben futó webhelyek és webalkalmazások egy gyűjteményét. Az elem szülője a `Site` elemet. Ha nem ad meg egy `Sites` elem, a webes szerepkör örökölt webes szerepkörként üzemel, és akkor legfeljebb egy, a webes szerepkör-on üzemeltetett webhelyén. Ez az elem nem kötelező, és a szerepkör csak egy hely blokk rendelkezhet.

A `Sites` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

##  <a name="Site"></a> Hely  
A `Site` elem azt határozza meg, egy webhelyre vagy webalkalmazásra alkalmazást, amely a webes szerepkör része.

A `Site` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

A következő táblázat ismerteti az attribútumai a `Site` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. A webhely vagy alkalmazás neve.|  
|physicalDirectory|sztring|A legfelső szintű hely tartalomkönyvtára helye. A hely abszolút elérési utat vagy .csdef helyéhez viszonyított adható meg.|  

##  <a name="VirtualApplication"></a> VirtualApplication  
A `VirtualApplication` elem definiálja az alkalmazás Internet Information Services (IIS) 7 csoportosítása fájljait kézbesíti a tartalmat, vagy szolgáltatást biztosítanak protokollokon, például HTTP. Az IIS 7 alkalmazást hoz létre, amikor az az alkalmazás elérési útja a webhely URL-cím részévé válik.

A `VirtualApplication` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

A következő táblázat ismerteti az attribútumai a `VirtualApplication` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. Adja meg egy nevet a virtuális alkalmazás azonosításához.|  
|physicalDirectory|sztring|Kötelező. A fejlesztői gépen, amely tartalmazza a virtuális alkalmazás elérési útja. A compute emulatorban az IIS ezen a helyen tartalmat lekérjen van konfigurálva. Amikor üzembe helyezése az Azure-ban, a fizikai könyvtár tartalma a szolgáltatás további mellett vannak csomagolva. A service-csomag telepítése az Azure-ba, amikor az IIS a kicsomagolt tartalmának helye van beállítva.|  

##  <a name="VirtualDirectory"></a> VirtualDirectory  
A `VirtualDirectory` elemben (más néven elérési útja) könyvtár nevét adja meg, hogy adja meg az IIS-ben, és leképezése a fizikai könyvtárat egy helyi vagy távoli kiszolgálón.

A `VirtualDirectory` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

A következő táblázat ismerteti az attribútumai a `VirtualDirectory` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. Adja meg a virtuális könyvtár nevét.|  
|érték|physicalDirectory|Kötelező. Az elérési utat adja meg a fejlesztői gépen, amely tartalmazza a webhely vagy virtuális könyvtár tartalmát. A compute emulatorban az IIS ezen a helyen tartalmat lekérjen van konfigurálva. Amikor üzembe helyezése az Azure-ban, a fizikai könyvtár tartalma a szolgáltatás további mellett vannak csomagolva. A service-csomag telepítése az Azure-ba, amikor az IIS a kicsomagolt tartalmának helye van beállítva.|  

##  <a name="Bindings"></a> Kötések  
A `Bindings` elem ismerteti egy webhely kötéseit gyűjteménye. A fölérendelt eleme legyen a `Binding` elemet. Az elem szükség minden `Site` elemet. Végpontok konfigurálásával kapcsolatos további információkért lásd: [kommunikáció engedélyezése a szerepkörpéldányok](cloud-services-enable-communication-role-instances.md).

A `Bindings` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

##  <a name="Binding"></a> Kötés  
A `Binding` elem azt határozza meg a kérelmek egy webhelyre vagy webalkalmazásra alkalmazás folytatott kommunikációhoz szükséges konfigurációs adatokat.

A `Binding` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|sztring|Kötelező. Meghatározza a kötés azonosítására szolgáló nevet.|  
|Végpontneve|sztring|Kötelező. A végpont neve való kötéshez.|  
|Állomásfejléc|sztring|Választható. Adja meg egy állomásnevet, amely lehetővé teszi, hogy több, különböző állomásnevek egy egyetlen IP-cím és Port kombinációja a webhely üzemeltetése.|  

##  <a name="Startup"></a> Indítás  
A `Startup` elem azt ismerteti, amikor elindul a szerepkört futtató tevékenységek gyűjteményei. Ez az elem szülője lehet a `Variable` elemet. A szerepkör indítási feladatok használatával kapcsolatos további információkért lásd: [indítási feladatok konfigurálása](cloud-services-startup-tasks.md). Ez az elem nem kötelező, és a egy szerepkör csak egy indítási blokk rendelkezhet.

A következő táblázat ismerteti a attribútuma a `Startup` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|prioritás|int|Csak belső használatra.|  

##  <a name="Task"></a> A feladat  
A `Task` elem azt határozza meg, amely a szerepkör indulásakor indítási feladat. Indítási feladatok segítségével készíti elő a szerepkör az ilyen telepítés futtatása szoftverösszetevőket vagy más alkalmazásokat futtathat feladatokat. Feladatok megjelenési belül hajtsa végre a `Startup` elem letiltása.

A `Task` elem csak elérhető az Azure SDK verzióval 1.3-as vagy újabb verziója.

A következő táblázat ismerteti az attribútumai a `Task` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|Parancssor|sztring|Kötelező. Egy szkript, például olyan CMD-fájl, amely tartalmazza a parancsokat. Indítási parancs és a batch-fájlokat ANSI formátumban kell menteni. Fájlformátum, amely a fájl elején a bájtsorrendjelző jelölő beállítása nem fogja megfelelően feldolgozni.|  
|executionContext|sztring|Adja meg a környezetet, amelyben a szkript futása.<br /><br /> -   `limited` [Alapértelmezés szerint] – azonos jogokkal rendelkeznek, mint a folyamatot futtató szerepkör futtatásához.<br />-   `elevated` – Rendszergazdai jogosultságokkal futtassa.|  
|taskType|sztring|A parancs végrehajtási viselkedésének megadása<br /><br /> -   `simple` [Alapértelmezett] – a rendszer vár, mielőtt egyéb tevékenységeket indult való kilépéshez pedig a feladathoz.<br />-   `background` – A rendszer nem várja meg a feladat való kilépéshez.<br />-   `foreground` – Hasonló háttér, azzal a különbséggel mindaddig, amíg az összes előtérben futó feladatok kilépéshez szerepkör nem indul újra.|  

##  <a name="Contents"></a> Tartalom  
A `Contents` elem írja le a tartalmat egy webes szerepkör gyűjteménye. Az elem szülője a `Content` elemet.

A `Contents` elem csak akkor érhető el az Azure SDK verzióval 1.5-ös vagy újabb.

##  <a name="Content"></a> Tartalom  
A `Content` elem másolhatók az Azure virtuális gépen, és a célhely elérési útja, amelybe másolja a tartalom elérési útvonalát határozza meg.

A `Content` elem csak akkor érhető el az Azure SDK verzióval 1.5-ös vagy újabb.

A következő táblázat ismerteti az attribútumai a `Content` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|cél|sztring|Kötelező. Helye az az Azure virtuális gépen, amelyre a tartalmat el van helyezve. Ez a hely van a mappa viszonyított **%ROLEROOT%\Approot**.|  

Az elem azon a `SourceDirectory` elemet.

##  <a name="SourceDirectory"></a> SourceDirectory  
A `SourceDirectory` elem definiálja a helyi könyvtárban, amelyből tartalmat másolja. Ez az elem használatával adja meg a helyi tartalom másolása az Azure virtuális gépen.

A `SourceDirectory` elem csak akkor érhető el az Azure SDK verzióval 1.5-ös vagy újabb.

A következő táblázat ismerteti az attribútumai a `SourceDirectory` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|elérési út|sztring|Kötelező. Relatív vagy abszolút elérési útját egy helyi könyvtárba, amelynek a tartalmát az Azure virtuális gépen lesznek másolva. A könyvtár elérési útja a környezeti változók bővítése használata támogatott.|  
  
## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)
