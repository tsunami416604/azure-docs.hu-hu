---
title: Azure Cloud Services def. Webrole-séma | Microsoft Docs
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
ms.openlocfilehash: 0bb0946ea48a4c206d6bfe683da0835aca9b198b
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "60613244"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Az Azure Cloud Services Definition webszerepkör-sémája
Az Azure webes szerepkör olyan szerepkör, amely az IIS 7 által támogatott webalkalmazás-programozáshoz van testreszabva, például ASP.NET, PHP, Windows Communication Foundation és FastCGI.

A szolgáltatás definíciós fájljának alapértelmezett kiterjesztése. csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Alapszintű szolgáltatás-definíciós séma webes szerepkörhöz  
A webes szerepkört tartalmazó szolgáltatás-definíciós fájl alapszintű formátuma a következő.

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
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>  
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
A szolgáltatás definíciós fájlja tartalmazza ezeket az elemeket, amelyeket a jelen témakör következő részeiben talál részletesen:  

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

[Importálja](#Imports)

[Importálás](#Import)

[Runtime](#Runtime)

[Környezet](#Environment)

[Változó](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Helyek](#Sites)

[Hely](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Kötések](#Bindings)

[Kötés](#Binding)

[Indítási](#Startup)

[Tevékenység](#Task)

[Tartalmát](#Contents)

[Tartalom](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> WebRole  
Az `WebRole` elem egy webalkalmazás-programozáshoz testre szabott szerepkört ismertet, amelyet az IIS 7 és a ASP.net támogat. Egy szolgáltatás nulla vagy több webes szerepkört is tartalmazhat.

Az alábbi táblázat az `WebRole` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|Karakterlánc|Kötelező. A webes szerepkör neve. A szerepkör nevének egyedinek kell lennie.|  
|enableNativeCodeExecution|boolean|Választható. Az alapértelmezett érték `true`:; a natív kód végrehajtása és a teljes megbízhatóság alapértelmezés szerint engedélyezve van. Ezt az attribútumot `false` úgy állítsa be, hogy letiltsa a natív kód végrehajtását a webes szerepkör esetében, és használja helyette az Azure részleges megbízhatóságát.|  
|vmsize|Karakterlánc|Választható. Állítsa be ezt az értéket a szerepkörre kiosztott virtuális gép méretének módosításához. Az alapértelmezett érték `Small`. További információ: [Cloud Services virtuális gépek méretei](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a>ConfigurationSettings  
Az `ConfigurationSettings` elem a webes szerepkör konfigurációs beállításainak gyűjteményét írja le. Ez az elem az `Setting` elem szülője.

##  <a name="Setting"></a>Beállítás  
Az `Setting` elem egy név és egy érték párokat ír le, amely meghatározza a szerepkör egy példányának konfigurációs beállításait.

Az alábbi táblázat az `Setting` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|Karakterlánc|Kötelező. A konfigurációs beállítás egyedi neve.|  

A szerepkör konfigurációs beállításai név és érték párok, amelyek deklarálva vannak a szolgáltatás-definíciós fájlban, és a szolgáltatás konfigurációs fájljában vannak megadva.

##  <a name="LocalResources"></a>LocalResources  
Az `LocalResources` elem a webes szerepkör helyi tárolási erőforrásainak gyűjteményét írja le. Ez az elem az `LocalStorage` elem szülője.

##  <a name="LocalStorage"></a>LocalStorage  
Az `LocalStorage` elem egy helyi tárolási erőforrást azonosít, amely a szolgáltatás futtatásához rendelkezésre álló fájlrendszert biztosít. Egy szerepkör nulla vagy több helyi tárolási erőforrást is meghatározhat.

> [!NOTE]
>  Az `LocalStorage` elem gyermekként `WebRole` is megjelenhet az Azure SDK korábbi verzióival való kompatibilitás támogatásához.

Az alábbi táblázat az `LocalStorage` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A helyi tároló egyedi neve.|  
|cleanOnRoleRecycle|boolean|Választható. Azt jelzi, hogy a helyi tárolót meg kell-e tisztítani a szerepkör újraindításakor. Az `true`alapértelmezett érték:.|  
|sizeInMb|int|Választható. A helyi tároló számára lefoglalni kívánt tárterület (MB). Ha nincs megadva, a lefoglalt alapértelmezett tárterület 100 MB. A lefoglalt tárterület minimális mérete 1 MB.<br /><br /> A helyi erőforrások maximális mérete a virtuális gép méretétől függ. További információ: [Cloud Services virtuális gépek méretei](cloud-services-sizes-specs.md).|  
  
A helyi tárolási erőforráshoz lefoglalt könyvtár neve megegyezik a name attribútumhoz megadott értékkel.

##  <a name="Endpoints"></a>Végpontok  
Az `Endpoints` elem ismerteti egy szerepkör bemeneti (külső), belső és példány típusú bemeneti végpontok gyűjteményét. Ez az elem a `InputEndpoint`, `InternalEndpoint`és `InstanceInputEndpoint` elemek szülője.

A bemeneti és a belső végpontok külön vannak lefoglalva. A szolgáltatás összesen 25 bemeneti, belső és példány típusú bemeneti végponttal rendelkezhet, amelyek lefoglalhatók a szolgáltatásban engedélyezett 25 szerepkör között. Ha például 5 szerepkörrel rendelkezik, legfeljebb 5 bemeneti végpontot foglalhat le, vagy 25 bemeneti végpontot foglalhat le egyetlen szerepkörhöz, vagy 1 bemeneti végpontot is lefoglalhat 25 szerepkörre.

> [!NOTE]
>  Minden telepített szerepkörhöz egy példány szükséges. Az előfizetéshez tartozó alapértelmezett kiépítés legfeljebb 20 magot jelent, így a szerepkör 20 példányára van korlátozva. Ha az alkalmazásnak több példányra van szüksége, mint amennyit az alapértelmezett kiépítés során, tekintse meg a [Számlázási, az előfizetés-kezelés és a kvóta támogatását](https://azure.microsoft.com/support/options/) a kvóta növelésével kapcsolatos további információkért.

##  <a name="InputEndpoint"></a>InputEndpoint  
Az `InputEndpoint` elem egy webes szerepkörhöz tartozó külső végpontot ír le.

Több végpontot is megadhat, amelyek a HTTP-, HTTPS-, UDP-és TCP-végpontok kombinációját jelentik. Megadhatja a bemeneti végponthoz választott portszámot, de a szolgáltatás egyes szerepköreihez megadott portszámoknak egyedinek kell lenniük. Ha például azt adja meg, hogy egy webes szerepkör a HTTP-t és a 443-es portot használja a HTTPS-hez, akkor előfordulhat, hogy egy második webes szerepkör a 80-as portot 8080 használja a HTTP protokollhoz, és az 8043 portot HTTPS-kapcsolatra.

Az alábbi táblázat az `InputEndpoint` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A külső végpont egyedi neve.|  
|protocol|Karakterlánc|Kötelező. A külső végpont átviteli protokollja. Webes szerepkör esetén a lehetséges értékek a következők `HTTP` `HTTPS` `UDP`:,, vagy `TCP`.|  
|port|int|Kötelező. A külső végpont portja. Megadhat bármely kiválasztott portszámot, de a szolgáltatás egyes szerepköreihez megadott portszámoknak egyedinek kell lenniük.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|  
|tanúsítvány|sztring|HTTPS-végponthoz szükséges. Egy `Certificate` elem által meghatározott tanúsítvány neve.|  
|localPort|int|Választható. A végponton belüli belső kapcsolatokhoz használt portot adja meg. Az `localPort` attribútum a végpont külső portját a szerepkör belső portjára képezi le. Ez olyan esetekben hasznos, amikor a szerepkörnek olyan belső összetevővel kell kommunikálnia egy olyan porton, amely különbözik a külsőleg elérhetőtől.<br /><br /> Ha nincs megadva, az értéke `localPort` megegyezik `port` az attribútummal. A "*" `localPort` értékének beállításával automatikusan hozzárendelhet egy nem lefoglalt portot, amely a futásidejű API használatával felderíthető.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).<br /><br /> Az `localPort` attribútum csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.|  
|ignoreRoleInstanceStatus|boolean|Választható. Ha ennek az attribútumnak `true`az értéke, akkor a rendszer figyelmen kívül hagyja a szolgáltatás állapotát, és a terheléselosztó nem távolítja el a végpontot. Ennek az értéknek `true` a beállításával hasznos lehet a szolgáltatás foglalt példányainak hibakereséséhez. Az alapértelmezett érték `false`. **Megjegyzés:**  A végpontok akkor is fogadhatnak forgalmat, ha a szerepkör nem üzemkész állapotban van.|  
|loadBalancerProbe|Karakterlánc|Választható. A bemeneti végponthoz rendelt terheléselosztó-mintavétel neve. További információ: [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a>InternalEndpoint  
Az `InternalEndpoint` elem egy webes szerepkör belső végpontját ismerteti. Egy belső végpont csak a szolgáltatáson belül futó egyéb szerepkör-példányok számára érhető el. a szolgáltatáson kívüli ügyfelek számára nem érhető el. Az `Sites` elemet nem tartalmazó webes szerepkörök csak egyetlen http-, UDP-vagy TCP belső végponttal rendelkezhetnek.

Az alábbi táblázat az `InternalEndpoint` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A belső végpont egyedi neve.|  
|protocol|Karakterlánc|Kötelező. A belső végpont átviteli protokollja. A `HTTP` `TCP` `ANY`lehetséges értékek:,, vagy. `UDP`<br /><br /> A érték `ANY` azt határozza meg, hogy bármely protokoll, bármely port engedélyezett-e.|  
|port|int|Választható. A végponton belüli belső terheléselosztási kapcsolatokhoz használt port. Egy elosztott terhelésű végpont két portot használ. A nyilvános IP-címhez használt port, valamint a magánhálózati IP-címen használt port. Ezek általában ugyanazok, de különböző portok használatát is választhatja.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).<br /><br /> Az `Port` attribútum csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.|  

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint  
Az `InstanceInputEndpoint` elem egy példány bemeneti végpontját írja le egy webes szerepkörhöz. Egy példány bemeneti végpontja egy adott szerepkör-példánnyal van társítva a terheléselosztó porton keresztüli továbbításával. Az egyes példányok bemeneti végpontja egy adott portra van leképezve a lehetséges portok köréből. Ez az elem az `AllocatePublicPortFrom` elem szülője.

Az `InstanceInputEndpoint` elem csak az Azure SDK 1,7-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `InstanceInputEndpoint` elem attribútumait ismerteti.
  
| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A végpont egyedi neve.|  
|localPort|int|Kötelező. Meghatározza azt a belső portot, amelyet az összes szerepkör-példány figyelni fog a terheléselosztó által továbbított bejövő forgalom fogadásához. A lehetséges értékek tartománya 1 és 65535 között van, beleértve a értéket.|  
|protocol|Karakterlánc|Kötelező. A belső végpont átviteli protokollja. A lehetséges értékek: `udp` és `tcp`. Http `tcp` /HTTPS-alapú forgalomhoz használható.|  
  
##  <a name="AllocatePublicPortFrom"></a>AllocatePublicPortFrom  
Az `AllocatePublicPortFrom` elem azt a nyilvános porttartomány ismerteti, amelyet a külső ügyfelek az egyes példányok bemeneti végpontjának eléréséhez használhatnak. A nyilvános (VIP) portszám le van foglalva ebből a tartományból, és az egyes szerepkör-példányok végpontja számára van hozzárendelve a bérlői telepítés és a frissítés során. Ez az elem az `FixedPortRange` elem szülője.

Az `AllocatePublicPortFrom` elem csak az Azure SDK 1,7-es vagy újabb verziójának használatával érhető el.

##  <a name="FixedPort"></a> FixedPort  
Az `FixedPort` elem a belső végponthoz tartozó portot adja meg, amely lehetővé teszi a terheléselosztás elosztott terhelésű kapcsolatait a végponton.

Az `FixedPort` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `FixedPort` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|port|int|Kötelező. A belső végpont portja. Ennek ugyanaz a hatása, mint a `FixedPortRange` min és a Max beállítása ugyanahhoz a porthoz.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|  

##  <a name="FixedPortRange"></a>FixedPortRange  
Az `FixedPortRange` elem megadja a belső végponthoz vagy példányhoz tartozó bemeneti végponthoz rendelt portok tartományát, és beállítja a végponton elosztott terhelésű kapcsolatokhoz használt portot.

> [!NOTE]
>  Az `FixedPortRange` elem eltérő módon működik attól függően, hogy melyik elemet tárolja. Ha az `FixedPortRange` elem szerepel a `InternalEndpoint` elemben, a a terheléselosztó összes portját a minimális és a maximális attribútumok tartományán belül nyitja meg az összes olyan virtuális géphez, amelyen a szerepkör fut. Ha a `FixedPortRange` elem szerepel a `InstanceInputEndpoint` elemben, az csak egy portot nyit meg a minimális és a maximális attribútum tartományán belül minden olyan virtuális gépen, amelyen a szerepkör fut.

Az `FixedPortRange` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `FixedPortRange` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|perc|int|Kötelező. A tartomány minimális portszáma. A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|  
|max.|Karakterlánc|Kötelező. A tartományon belüli maximális port. A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|  

##  <a name="Certificates"></a>Tanúsítványok  
Az `Certificates` elem a webes szerepkör tanúsítványainak gyűjteményét írja le. Ez az elem az `Certificate` elem szülője. Egy szerepkörhöz a társított tanúsítványok száma is tartozhat. További információ a tanúsítványok elem használatáról: [a szolgáltatás definíciós fájljának módosítása tanúsítvánnyal](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Tanúsítvány  
Az `Certificate` elem egy webes szerepkörhöz társított tanúsítványt ír le.

Az alábbi táblázat az `Certificate` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|Karakterlánc|Kötelező. A tanúsítvány neve, amely akkor használható, ha egy https `InputEndpoint` -elemmel társítva van.|  
|storeLocation|Karakterlánc|Kötelező. Azon tanúsítványtároló helye, ahol ez a tanúsítvány megtalálható a helyi gépen. A lehetséges értékek `CurrentUser` a `LocalMachine`következők: és.|  
|storeName|Karakterlánc|Kötelező. Annak a tanúsítványtárolónak a neve, ahol a tanúsítvány a helyi gépen található. A lehetséges értékek közé tartoznak a beépített tárolók `My`neve `Root` `CA`, `TrustedPeople` `Disallowed` `Trust` ,,`TrustedPublisher`,,,,, vagy bármely egyéni tároló neve. `AuthRoot` `AddressBook` Ha meg van adva egy egyéni tároló neve, a rendszer automatikusan létrehozza az áruházat.|  
|permissionLevel|Karakterlánc|Választható. Megadja a szerepkör-folyamatok számára megadott hozzáférési engedélyeket. Ha azt szeretné, hogy csak emelt szintű folyamatok férhessenek hozzá a titkos kulcshoz, `elevated` adja meg az engedélyt. `limitedOrElevated`az engedély lehetővé teszi az összes szerepkör-folyamat számára a titkos kulcs elérését. A lehetséges értékek: `limitedOrElevated` és `elevated`. Az alapértelmezett érték `limitedOrElevated`.|  

##  <a name="Imports"></a>Importálja  
Az `Imports` elem az importálási modulok gyűjteményét írja le egy webes szerepkörhöz, amely összetevőket ad hozzá a vendég operációs rendszerhez. Ez az elem az `Import` elem szülője. Ez az elem nem kötelező, és egy szerepkör csak egyetlen importálási blokkot tartalmazhat. 

Az `Imports` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

##  <a name="Import"></a>Importálása  
Az `Import` elem a vendég operációs rendszerhez hozzáadandó modult adja meg.

Az `Import` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `Import` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|moduleName|Karakterlánc|Kötelező. Az importálandó modul neve. Az érvényes importálási modulok a következők:<br /><br /> – RemoteAccess<br />- RemoteForwarder<br />-Diagnosztika<br /><br /> A RemoteAccess és a RemoteForwarder modulok lehetővé teszik a szerepkör-példány konfigurálását a távoli asztali kapcsolatokhoz. További információ: [Távoli asztali kapcsolat engedélyezése](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> A diagnosztikai modul lehetővé teszi a szerepkör-példányok diagnosztikai adatainak gyűjtését.|  

##  <a name="Runtime"></a>Runtime  
Az `Runtime` elem a környezeti változók egy gyűjteményét írja le egy webes szerepkörhöz, amely az Azure-gazdagép folyamatának futásidejű környezetét vezérli. Ez az elem az `Environment` elem szülője. Ez az elem nem kötelező, és a szerepkörnek csak egy futásidejű blokkja lehet.

Az `Runtime` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

A következő táblázat a `Runtime` elem attribútumait ismerteti:  

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|executionContext|Karakterlánc|Választható. Meghatározza azt a környezetet, amelyben a szerepkör-folyamat elindul. Az alapértelmezett környezet `limited`:.<br /><br /> -   `limited`– A folyamat rendszergazdai jogosultságok nélkül indul el.<br />-   `elevated`– A folyamat rendszergazdai jogosultságokkal van elindítva.|  

##  <a name="Environment"></a>Környezet  
Az `Environment` elem egy webes szerepkörhöz tartozó környezeti változó beállításainak gyűjteményét ismerteti. Ez az elem az `Variable` elem szülője. Egy szerepkörhöz tetszőleges számú környezeti változó állítható be.

##  <a name="Variable"></a>Változó  
Az `Variable` elem egy környezeti változót határoz meg, amely a vendég operációs szolgáltatásban állítható be.

Az `Variable` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

A következő táblázat a `Variable` elem attribútumait ismerteti:  

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|Karakterlánc|Kötelező. A beállítani kívánt környezeti változó neve.|  
|value|Karakterlánc|Választható. A környezeti változóhoz beállított érték Tartalmaznia kell egy Value attribútumot vagy egy `RoleInstanceValue` elemet.|  

##  <a name="RoleInstanceValue"></a>RoleInstanceValue  
Az `RoleInstanceValue` elem azt az xPath-értéket adja meg, amelyből be kell olvasni a változó értékét.

Az alábbi táblázat az `RoleInstanceValue` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|XPath|Karakterlánc|Választható. A példány telepítési beállításainak helyének elérési útja. További információ: [konfigurációs változók az XPath](cloud-services-role-config-xpath.md)-ban.<br /><br /> Tartalmaznia kell egy Value attribútumot vagy egy `RoleInstanceValue` elemet.|  

##  <a name="EntryPoint"></a> EntryPoint  
Az `EntryPoint` elem a szerepkör belépési pontját adja meg. Ez az elem az `NetFxEntryPoint` elemek szülője. Ezek az elemek lehetővé teszik, hogy az alapértelmezett WaWorkerHost. exe fájltól eltérő alkalmazást határozzon meg szerepkör-belépési pontként.

Az `EntryPoint` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
Az `NetFxEntryPoint` elem meghatározza a szerepkörhöz futtatandó programot.

> [!NOTE]
>  Az `NetFxEntryPoint` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `NetFxEntryPoint` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|assemblyName|sztring|Kötelező. A belépési pontot tartalmazó szerelvény elérési útja és fájlneve. Az elérési út a  **\\%ROLEROOT%\Approot** mappához képest relatív (ne  **\\** határozza meg `commandLine`a%ROLEROOT%\Approot a alkalmazásban, feltételezve). **% ROLEROOT%** az Azure által fenntartott környezeti változó, amely a szerepkör gyökérkönyvtárának helyét jelöli. A%ROLEROOT%\Approot mappa a szerepkörhöz tartozó alkalmazás mappáját jelöli.  **\\**<br /><br /> Üzemeltethető webmag-szerepkörök esetén az elérési út mindig a  **\\%ROLEROOT%\Approot\bin** mappához viszonyított.<br /><br /> Teljes IIS-és IIS Express webes szerepkörök esetén, ha a szerelvény nem található a  **\\%ROLEROOT%\Approot** mappához képest, a rendszer a  **\\%ROLEROOT%\Approot\bin** keresi.<br /><br /> Ez a teljes IIS-re vonatkozó biztonsági mentés nem ajánlott eljárás, és a későbbi verziókban valószínűleg el lesz távolítva.|  
|targetFrameworkVersion|sztring|Kötelező. A .NET-keretrendszer azon verziója, amelyen a szerelvény létrejött. Például: `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a>Helyek  
Az `Sites` elem a webes szerepkörben üzemeltetett webhelyek és webalkalmazások gyűjteményét ismerteti. Ez az elem az `Site` elem szülője. Ha nem ad meg egy `Sites` elemet, a webes szerepkör örökölt webes szerepkörként fut, és csak egy webhely fut a webes szerepkörben. Ez az elem nem kötelező, és egy szerepkör csak egy hellyel rendelkezhet.

Az `Sites` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

##  <a name="Site"></a>Hely  
Az `Site` elem a webes szerepkör részét képező webhelyet vagy webalkalmazást adja meg.

Az `Site` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `Site` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. A webhely vagy alkalmazás neve.|  
|physicalDirectory|Karakterlánc|A hely gyökeréhez tartozó tartalom könyvtárának helye. A hely abszolút elérési úttal vagy a. csdef helyhez viszonyítva is megadható.|  

##  <a name="VirtualApplication"></a>VirtualApplication  
Az `VirtualApplication` elem a Internet Information Services (IIS) 7 alkalmazásban definiál egy alkalmazást, amely tartalmat biztosít vagy szolgáltatásokat biztosít protokollokon, például HTTP-n keresztül. Amikor létrehoz egy alkalmazást az IIS 7-es verziójában, az alkalmazás elérési útja a hely URL-címévé válik.

Az `VirtualApplication` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `VirtualApplication` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|Karakterlánc|Kötelező. A virtuális alkalmazás azonosítására szolgáló nevet adja meg.|  
|physicalDirectory|Karakterlánc|Kötelező. Megadja a virtuális alkalmazást tartalmazó fejlesztői gép elérési útját. A Compute Emulator az IIS úgy van konfigurálva, hogy tartalmat kérjen le erről a helyről. Az Azure-ban való üzembe helyezéskor a fizikai könyvtár tartalma a szolgáltatás többi részével együtt van csomagolva. A szervizcsomag Azure-beli üzembe helyezése után az IIS a kicsomagolt tartalom helyével van konfigurálva.|  

##  <a name="VirtualDirectory"></a>Virtuáliskönyvtár  
Az `VirtualDirectory` elem megadja az IIS-ben megadott könyvtárnév (más néven elérési út) nevét, amely a helyi vagy távoli kiszolgálón található fizikai könyvtárra van leképezve.

Az `VirtualDirectory` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `VirtualDirectory` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|Karakterlánc|Kötelező. A virtuális könyvtár azonosítására szolgáló nevet adja meg.|  
|value|physicalDirectory|Kötelező. Megadja a webhelyet vagy a virtuális könyvtár tartalmát tartalmazó fejlesztői számítógép elérési útját. A Compute Emulator az IIS úgy van konfigurálva, hogy tartalmat kérjen le erről a helyről. Az Azure-ban való üzembe helyezéskor a fizikai könyvtár tartalma a szolgáltatás többi részével együtt van csomagolva. A szervizcsomag Azure-beli üzembe helyezése után az IIS a kicsomagolt tartalom helyével van konfigurálva.|  

##  <a name="Bindings"></a>Kötések  
Az `Bindings` elem egy webhely kötéseinek gyűjteményét írja le. Ez a `Binding` elem szülő eleme. Az elemnek minden `Site` elemhez kötelezőnek kell lennie. A végpontok konfigurálásával kapcsolatos további információkért lásd: [a szerepkör-példányok kommunikációjának engedélyezése](cloud-services-enable-communication-role-instances.md).

Az `Bindings` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

##  <a name="Binding"></a>Kötés  
Az `Binding` elem határozza meg a webhelyekkel vagy webalkalmazásokkal való kommunikációra irányuló kérésekhez szükséges konfigurációs adatokat.

Az `Binding` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|name|sztring|Kötelező. Megadja a kötés azonosítására szolgáló nevet.|  
|Végpontneve|sztring|Kötelező. A végpont nevét adja meg a kötéshez.|  
|hostHeader|Karakterlánc|Választható. Megadja azt az állomásnevet, amely lehetővé teszi több, különböző állomásnévvel rendelkező hely üzemeltetését egyetlen IP-cím/portszám kombinációban.|  

##  <a name="Startup"></a>Indítási  
Az `Startup` elem a szerepkör indításakor futtatott feladatok gyűjteményét írja le. Ez az elem lehet az `Variable` elem szülője. További információ a szerepkör-indítási feladatok használatáról: [indítási feladatok konfigurálása](cloud-services-startup-tasks.md). Ez az elem nem kötelező, és a szerepkörnek csak egy indítási blokkja lehet.

Az alábbi táblázat az `Startup` elem attribútumát ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|priority|int|Csak belső használatra.|  

##  <a name="Task"></a>Feladat  
Az `Task` elem a szerepkör indításakor megjelenő indítási feladatot határozza meg. Az indítási feladatok olyan feladatok elvégzésére használhatók, amelyek előkészítik a szerepkört az ilyen telepítési szoftver-összetevők futtatására vagy más alkalmazások futtatására. A feladatok az `Startup` Element blokkban megjelenő sorrendben futnak.

Az `Task` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `Task` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|commandLine|Karakterlánc|Kötelező. Egy parancsfájl, például egy CMD-fájl, amely a futtatandó parancsokat tartalmazza. Az indítási parancsot és a Batch-fájlokat ANSI formátumban kell menteni. A byte-Order jelölőt a fájl elején beállított fájlformátumok nem fognak megfelelően feldolgozni.|  
|executionContext|sztring|Meghatározza azt a környezetet, amelyben a parancsfájl fut.<br /><br /> -   `limited`[Alapértelmezett] – futtassa ugyanazokkal a jogosultságokkal, mint a folyamatot üzemeltető szerepkör.<br />-   `elevated`– Futtatás rendszergazdai jogosultságokkal.|  
|taskType|Karakterlánc|Meghatározza a parancs végrehajtási viselkedését.<br /><br /> -   `simple`[Alapértelmezett] – a rendszer megvárja, amíg a feladat kilép, mielőtt más feladatok elindulnak.<br />-   `background`– A rendszer nem várja meg a feladat kilépését.<br />-   `foreground`– A háttérhez hasonlóan, a szerepkör nem indul újra, amíg az összes előtér-feladat ki nem fejeződik.|  

##  <a name="Contents"></a>Tartalmát  
Az `Contents` elem a webes szerepkörök tartalmának gyűjteményét írja le. Ez az elem az `Content` elem szülője.

Az `Contents` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

##  <a name="Content"></a>Tartalom  
Az `Content` elem határozza meg az Azure-beli virtuális gépre másolandó tartalom forrásának helyét, valamint a cél elérési útját, ahová a rendszer átmásolja.

Az `Content` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `Content` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|destination|Karakterlánc|Kötelező. Annak az Azure-beli virtuális gépnek a helye, ahová a tartalom kerül. Ez a hely a **%ROLEROOT%\Approot**mappához képest relatív.|  

Ez az elem a `SourceDirectory` elem szülő eleme.

##  <a name="SourceDirectory"></a>SourceDirectory  
Az `SourceDirectory` elem határozza meg azt a helyi könyvtárat, amelyből a tartalmat másolni kívánja. Ezzel az elemmel adhatja meg az Azure-beli virtuális gépre másolandó helyi tartalmat.

Az `SourceDirectory` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat az `SourceDirectory` elem attribútumait ismerteti.

| Attribútum | Type | Leírás |  
| --------- | ---- | ----------- |  
|path|sztring|Kötelező. Egy helyi könyvtár relatív vagy abszolút elérési útja, amelynek tartalmát a rendszer az Azure-beli virtuális gépre másolja. A környezeti változók kiterjesztése a könyvtár elérési útjában támogatott.|  
  
## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)
