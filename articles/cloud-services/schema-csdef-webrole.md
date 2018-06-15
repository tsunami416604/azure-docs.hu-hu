---
title: Azure Cloud Services alapértelmezett Webrole típusról séma |} Microsoft Docs
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 6db3edef937dc0b5cdd805d0045897e02b985e7b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360342"
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Azure Cloud Services – Definition webrole típusról séma
Az Azure webes szerepkör a rendszer egy testreszabott alkalmazásprogramozási web IIS 7, például az ASP.NET, PHP, a Windows Communication Foundation és a FastCGI támogatja.

Az alapértelmezett kiterjesztése a szolgáltatásdefiníciós fájl .csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Alapszintű service definition séma egy webes szerepkör  
A webes szerepkör tartalmazó szolgáltatásdefiníciós fájl alapszintű formátuma a következő.

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
A szolgáltatásdefiníciós fájlban tartalmazza ezeket az elemeket, ez a témakör későbbi részeiben részletesen:  

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

[Importálása](#Imports)

[Importálás](#Import)

[Futásidejű](#Runtime)

[Környezet](#Environment)

[változó](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[webhelyek](#Sites)

[Helykiszolgáló](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Kötések](#Bindings)

[Kötelező](#Binding)

[Indítása](#Startup)

[Tevékenység](#Task)

[Tartalom](#Contents)

[Tartalom](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a> Webrole típusról  
A `WebRole` elem egy webes alkalmazásprogramozási, testreszabott szerepkörét mutatja be, az IIS 7 és az ASP.NET által támogatott. A szolgáltatás tartalmazhat nulla vagy több webes szerepkört.

A következő táblázat ismerteti az attribútumai a `WebRole` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. A webes szerepkör nevét. A szerepkör nevének egyedinek kell lennie.|  
|enableNativeCodeExecution|logikai|Választható. Az alapértelmezett érték `true`; natív alapértelmezés szerint engedélyezve vannak a programkód és a teljesen megbízható. Ez az attribútum beállítása `false` tiltsa le a natív kód végrehajtása a webes szerepkör, és az Azure részlegesen megbízható kapcsolat használata.|  
|vmsize|karakterlánc|Választható. Állítsa be az értéket, amely számára engedélyezett a virtuális gép méretének módosítása a szerepkörhöz. Az alapértelmezett érték `Small`. További információkért lásd: [virtuálisgép-méretek a Felhőszolgáltatások](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a> ConfigurationSettings  
A `ConfigurationSettings` elem írja le a webes szerepkör konfigurációs beállításainak gyűjteményei. Az elem szülőjének a `Setting` elemet.

##  <a name="Setting"></a> Beállítás  
A `Setting` elem egy név-érték pár, amely meghatározza egy szerepkör példánya konfigurációs beállítását ismerteti.

A következő táblázat ismerteti az attribútumai a `Setting` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. A konfigurációs beállítások egyedi nevét.|  

A szerepkör konfigurációs beállításai olyan név-érték párok, amelyek a szolgáltatásdefiníciós fájlban deklarált, és állítsa be a konfigurációs fájlban.

##  <a name="LocalResources"></a> LocalResources  
A `LocalResources` elem írja le a webes szerepkör helyi tároló-erőforrások gyűjteménye. Az elem szülőjének a `LocalStorage` elemet.

##  <a name="LocalStorage"></a> LocalStorage  
A `LocalStorage` elem azonosítja a helyi tároló egyik erőforrásához helyként biztosít a szolgáltatás futási időben. Egy szerepkör határozhatnak meg nulla vagy több helyi tároló-erőforrások.

> [!NOTE]
>  A `LocalStorage` elem gyermekeként szerepelhet a `WebRole` elemben, amely támogatja az Azure SDK korábbi verzióival is kompatibilisek.

A következő táblázat ismerteti az attribútumai a `LocalStorage` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. Egy egyedi nevet a helyi tárolójába.|  
|cleanOnRoleRecycle|logikai|Választható. Azt jelzi, hogy a helyi tárolóban kell törölni, a szerepkör újraindításakor. Alapértelmezett érték `true`.|  
|hogy a sizeInMb|int|Választható. A tárolóhely foglalja le a helyi tárolójába, MB-ban a kívánt memóriamennyiséget. Ha nincs megadva, az alapértelmezett tároló lefoglalt terület 100 MB. A tárolóhely lehet kiosztani minimális mérete 1 MB.<br /><br /> A helyi erőforrások maximális mérete függ, a virtuális gép méretét. További információkért lásd: [virtuálisgép-méretek a Felhőszolgáltatások](cloud-services-sizes-specs.md).|  
  
Annak a könyvtárnak a helyi tároló erőforráshoz van lefoglalva a neve megegyezik a name attribútum megadott érték.

##  <a name="Endpoints"></a> Végpontok  
A `Endpoints` elem írja le a belső (külső) bemeneti gyűjtemény, és a példány bemeneti végpontok szerepkör. Az elem szülőjének a `InputEndpoint`, `InternalEndpoint`, és `InstanceInputEndpoint` elemeket.

Külön bemeneti és a belső végpontok jutnak. Egy szolgáltatás lehet 25 bemeneti, belső, összesen és példány bemeneti végpontok, amelyek szolgáltatás engedélyezett 25 szerepköreiben oszthat ki. Például ha 5 szerepkörökkel rendelkeznek szerepkör 5 bemeneti végpontot foglalhatja vagy foglalhatja 25 bemeneti végpont egy szerepkörre vagy foglalhatja 1 bemeneti végpont minden 25 szerepkörökhöz.

> [!NOTE]
>  Minden telepített szerepkör egy szerepkör egy példányát igényli. Az előfizetéshez tartozó kiépítés alapértelmezett legfeljebb 20 magok és így legfeljebb 20 példánya egy. Ha az alkalmazás által igényelt, mint a kiépítés lásd: alapértelmezés szerint több példány [számlázási, az előfizetés-kezelés és a kvóta támogatás](https://azure.microsoft.com/support/options/) további információt a kvótájának növelését.

##  <a name="InputEndpoint"></a> Bemeneti végponthoz  
A `InputEndpoint` elem írja le a webes szerepkör külső végpontját.

Több kombinációjából HTTP, HTTPS, UDP-végpontokat és TCP-végpontok definiálhat. Megadhatja, hogy bármely portszám úgy dönt, hogy a bemeneti végpontja, de az egyes szerepkörökhöz, a szolgáltatás a megadott számú portok egyedinek kell lennie. Például ha megadja, hogy a webes szerepkör 80-as portot a HTTP és a 443-as portot a HTTPS-hez, majd megadhatja, hogy egy második webes szerepkör használja 8080-as porton HTTP-és port 8043 a HTTPS-hez.

A következő táblázat ismerteti az attribútumai a `InputEndpoint` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. A külső végpont egyedi nevét.|  
|protokoll|karakterlánc|Kötelező. Az átviteli protokoll a külső végpont. A webes szerepkör, a lehetséges értékek: `HTTP`, `HTTPS`, `UDP`, vagy `TCP`.|  
|port|int|Kötelező. A külső végpont portja. Megadhatja, hogy bármely portszám választja, de az egyes szerepkörökhöz, a szolgáltatás a megadott számú portok egyedinek kell lennie.<br /><br /> 1 és 65535 között, beleértve a határértékeket (Azure SDK 1.7 vagy újabb verzió) a lehetséges értékek között.|  
|tanúsítvány|karakterlánc|A HTTPS-végpontnak szükséges. Határozza meg a tanúsítvány neve egy `Certificate` elemet.|  
|Helyi_port|int|Választható. Adja meg a végpont belső kapcsolatokhoz használt port. A `localPort` attribútum egy belső port szerepkör van leképezve a külső portot a végponton. Ez akkor hasznos, ahol egy szerepkör közölnie kell egy belső összetevő porton, hogy az egy másik, amely fel van fedve külsőleg forgatókönyvekben.<br /><br /> Ha nincs megadva, a értékének `localPort` ugyanaz, mint a `port` attribútum. Állítsa be a `localPort` a "*" egy szabad portot, amelyet a felderíthető a futtatókörnyezet API használatával automatikusan hozzárendelni.<br /><br /> 1 és 65535 között, beleértve a határértékeket (Azure SDK 1.7 vagy újabb verzió) a lehetséges értékek között.<br /><br /> A `localPort` attribútum értéke csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.|  
|ignoreRoleInstanceStatus|logikai|Választható. Ha ez az attribútum értékének beállítása `true`, egy szolgáltatás állapotát a rendszer figyelmen kívül hagyja, és a végpont nem távolítja el a terheléselosztó által. Az érték `true` elfoglalt egy szolgáltatás példányának hibakereséshez hasznos. Az alapértelmezett érték `false`. **Megjegyzés:** végpont továbbra is kaphat forgalom akkor is, ha a szerepkör nincs üzemkész állapotban.|  
|loadBalancerProbe|karakterlánc|Választható. A bemeneti végponthoz társított terheléselosztói mintavétel neve. További információkért lásd: [LoadBalancerProbe séma](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a> InternalEndpoint  
A `InternalEndpoint` elem egy webes szerepkör a belső végpont írja le. Belső végpont csak a többi szerepkörpéldányon, a szolgáltatás; belül futó érhető el nem érhető el az ügyfelek számára a szolgáltatás kívül. Webalkalmazás-szerepköröket, amelyek nem tartalmaznak a `Sites` elemnek csak egyetlen HTTP, UDP vagy TCP belső végpont lehet.

A következő táblázat ismerteti az attribútumai a `InternalEndpoint` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. A belső végpont egyedi nevét.|  
|protokoll|karakterlánc|Kötelező. Az átviteli protokoll a belső végpont. A lehetséges értékek: `HTTP`, `TCP`, `UDP`, vagy `ANY`.<br /><br /> Érték `ANY` Megadja, hogy minden protokoll, a port engedélyezve van.|  
|port|int|Választható. A belső elosztott terhelésű végpont kapcsolatokhoz használt port. Egy elosztott terhelésű végpont által használt portot. A nyilvános IP-cím használt portot, és a privát IP-címhez használt port. Ilyen értékük azonos, de eltérő portok használatára választhat.<br /><br /> 1 és 65535 között, beleértve a határértékeket (Azure SDK 1.7 vagy újabb verzió) a lehetséges értékek között.<br /><br /> A `Port` attribútum értéke csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.|  

##  <a name="InstanceInputEndpoint"></a> InstanceInputEndpoint  
A `InstanceInputEndpoint` elem egy webes szerepkör példány bemeneti végpontja írja le. Port a terheléselosztó továbbítással példány bemeneti végpontja társítva van egy adott szerepkör példánya. Minden példány bemeneti végpont le van képezve egy adott portot lehetséges portok tartománya. Az elem szülőjének a `AllocatePublicPortFrom` elemet.

A `InstanceInputEndpoint` eleme csak érhető el az Azure SDK 1.7-es verziójának használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `InstanceInputEndpoint` elemet.
  
| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. A végpont egyedi nevét.|  
|Helyi_port|int|Kötelező. Meghatározza azt a belső portot, amelyek összes szerepkörpéldányt lesz a terheléselosztó bejövő forgalmat továbbítják fogadásához. 1 és 65535 között lehet a lehetséges értékek között.|  
|protokoll|karakterlánc|Kötelező. Az átviteli protokoll a belső végpont. A lehetséges értékek: `udp` és `tcp`. Használjon `tcp` http/https-alapú forgalmat.|  
  
##  <a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom  
A `AllocatePublicPortFrom` elem minden példány bemeneti végpont eléréséhez használható külső ügyfelek által nyilvános porttartomány írja le. A nyilvános (VIP) portszám ebben a tartományban vannak lefoglalva, és bérlős központi telepítés és frissítés során minden egyes szerepkör-példány végpontra hozzárendelni. Az elem szülőjének a `FixedPortRange` elemet.

A `AllocatePublicPortFrom` eleme csak érhető el az Azure SDK 1.7-es verziójának használatával vagy újabb verzióját.

##  <a name="FixedPort"></a> FixedPort  
A `FixedPort` elem meghatározza azt a portot, a belső végpont, mely lehetővé teszi, hogy a végpont kiegyensúlyozott kapcsolatok betölteni.

A `FixedPort` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `FixedPort` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|port|int|Kötelező. A belső végpont portja. Ez tartalmazza a beállítás ugyanaz, mintha a `FixedPortRange` minimális és maximális arra a portra.<br /><br /> 1 és 65535 között, beleértve a határértékeket (Azure SDK 1.7 vagy újabb verzió) a lehetséges értékek között.|  

##  <a name="FixedPortRange"></a> FixedPortRange  
A `FixedPortRange` elem, amely a belső végpont és a példány bemeneti végpont hozzárendelt portok tartományát határozza meg, és a port használt terhelést, elosztott terhelésű végpont kapcsolatok.

> [!NOTE]
>  A `FixedPortRange` elem működik másképp attól függően, hogy azt az elemet. Ha a `FixedPortRange` elem van a `InternalEndpoint` elem, ekkor megnyílik a terheléselosztó a tartományba, amelyre a szerepkört futtató összes virtuális gép minimális és maximális attribútumát minden port. Ha a `FixedPortRange` elem van a `InstanceInputEndpoint` elem, azt a minimális és maximális attribútumait a szerepkört futtató virtuális gépeken belül csak egy portot nyit.

A `FixedPortRange` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `FixedPortRange` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|perc|int|Kötelező. A tartomány minimális port. 1 és 65535 között, beleértve a határértékeket (Azure SDK 1.7 vagy újabb verzió) a lehetséges értékek között.|  
|max.|karakterlánc|Kötelező. A tartomány maximális port. 1 és 65535 között, beleértve a határértékeket (Azure SDK 1.7 vagy újabb verzió) a lehetséges értékek között.|  

##  <a name="Certificates"></a> Tanúsítványok  
A `Certificates` elem írja le a webes szerepkör tanúsítványok gyűjteménye. Az elem szülőjének a `Certificate` elemet. Egy szerepkör előfordulhat, hogy már nem kapcsolódó tanúsítványok. A tanúsítványok elem használatával a további információkért lásd: [módosítsa a szolgáltatás definíciós fájl tanúsítvánnyal](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a> tanúsítvány  
A `Certificate` elem egy tanúsítványt, a webes szerepkör társított ismerteti.

A következő táblázat ismerteti az attribútumai a `Certificate` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. Ez a tanúsítvány, amely hivatkozik rá, amikor társítva van egy HTTPS nevét `InputEndpoint` elemet.|  
|StoreLocation –|karakterlánc|Kötelező. Ha ezt a tanúsítványt a helyi számítógépen találhatók tanúsítványtároló helye. A lehetséges értékek: `CurrentUser` és `LocalMachine`.|  
|storename –|karakterlánc|Kötelező. A tanúsítványtárolóban, ahol ezt a tanúsítványt a helyi számítógépen található neve. Lehetséges értékek: a beépített tárolónevek `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, vagy bármilyen egyéni Tárolónév. Ha egy egyéni tárolóba van megadva, a rendszer automatikusan létrehoz az áruházból.|  
|permissionLevel|karakterlánc|Választható. A megadott szerepkör folyamatokat hozzáférési engedélyeket ad meg. Ha azt szeretné, hogy tudjanak hozzáférni a titkos kulcsot, majd adja meg csak emelt szintű folyamatok `elevated` engedéllyel. `limitedOrElevated` az engedély lehetővé teszi az összes szerepkör-folyamat férhet hozzá a titkos kulcsához. A lehetséges értékek: `limitedOrElevated` és `elevated`. Az alapértelmezett érték `limitedOrElevated`.|  

##  <a name="Imports"></a> Importálása  
A `Imports` elem importálása lehetővé tevő modulokat a webes szerepkör, amely a vendég operációs rendszer-összetevők hozzáadása gyűjteménye írja le. Az elem szülőjének a `Import` elemet. Ez az elem nem kötelező, és a szerepkör importáláshoz csak egy blokk lehet. 

A `Imports` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

##  <a name="Import"></a> Importálás  
A `Import` elem hozzáadása a vendég operációs rendszer modul határozza meg.

A `Import` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `Import` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|Modulnév|karakterlánc|Kötelező. A modul importálása nevét. Érvényes importálási modulokra:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Diagnosztika<br /><br /> A RemoteAccess és RemoteForwarder modulok lehetővé teszik a szerepkörpéldány távoli asztali kapcsolatok konfigurálását. További információ: [engedélyezése a távoli asztali kapcsolat](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> A diagnosztika modul lehetővé teszi egy szerepkörpéldányhoz diagnosztikai adatainak gyűjtéséről.|  

##  <a name="Runtime"></a> Futásidejű  
A `Runtime` elem környezetiváltozó-beállításainak a webes szerepkör, amely szabályozza a futtatási környezetet az Azure-gazdagép folyamat gyűjteménye írja le. Az elem szülőjének a `Environment` elemet. Ez az elem nem kötelező, és a szerepkör csak egy futásidejű blokk lehet.

A `Runtime` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `Runtime` elem:  

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|executionContext|karakterlánc|Választható. Adja meg a környezetben, amelyben a szerepkör-folyamat elindul. Az alapértelmezett környezet `limited`.<br /><br /> -   `limited` – A folyamat rendszergazdai jogosultságok nélkül indul el.<br />-   `elevated` – A folyamat rendszergazdai jogosultságokkal rendelkező nincs elindítva.|  

##  <a name="Environment"></a> Környezet  
A `Environment` elem írja le a webes szerepkör környezetiváltozó-beállításainak gyűjteménye. Az elem szülőjének a `Variable` elemet. Egy szerepkör előfordulhat, hogy már nem környezeti változók készlet.

##  <a name="Variable"></a> változó  
A `Variable` elem a vendég operációs beállítani egy környezeti változó határozza meg.

A `Variable` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `Variable` elem:  

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. A környezeti változó beállítása neve.|  
|érték|karakterlánc|Választható. Az érték a következő környezeti változó beállítása. Meg kell adni a value attribútumként vagy vagy egy `RoleInstanceValue` elemet.|  

##  <a name="RoleInstanceValue"></a> RoleInstanceValue  
A `RoleInstanceValue` elem megadja az xPath, amelyen a változó értékének beolvasása.

A következő táblázat ismerteti az attribútumai a `RoleInstanceValue` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|XPath|karakterlánc|Választható. Központi telepítési beállítások példány helyének elérési útját. További információkért lásd: [XPath-konfigurációs változók](cloud-services-role-config-xpath.md).<br /><br /> Meg kell adni a value attribútumként vagy vagy egy `RoleInstanceValue` elemet.|  

##  <a name="EntryPoint"></a> EntryPoint  
A `EntryPoint` elem azt adja meg a belépési pont szerepkör. Az elem szülőjének a `NetFxEntryPoint` elemek. Ezeket az elemeket engedélyezi, hogy adjon meg egy alkalmazást, nem az alapértelmezett WaWorkerHost.exe szerepkör belépési pontként kezelhető.

A `EntryPoint` eleme csak elérhető az Azure SDK-verzió 1.5-ös vagy újabb verzióját.

##  <a name="NetFxEntryPoint"></a> NetFxEntryPoint  
A `NetFxEntryPoint` elem azt adja meg a program futtatásához egy szerepkörhöz.

> [!NOTE]
>  A `NetFxEntryPoint` eleme csak elérhető az Azure SDK-verzió 1.5-ös vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `NetFxEntryPoint` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|AssemblyName|karakterlánc|Kötelező. A belépési pont tartalmazó szerelvény elérési útja és neve. A mappa alapján értelmezett relatív elérési út  **\\%ROLEROOT%\Approot** (ne adjon meg  **\\%ROLEROOT%\Approot** a `commandLine`, az ajánlott érték). **% ROLEROOT %** egy környezeti változó Azure által kezelt és az azt jelenti, hogy a legfelső szintű mappa helyét az adott szerepkörhöz. A  **\\%ROLEROOT%\Approot** mappát jelöli, a szerepkör az alkalmazás mappájában.<br /><br /> Az üzemeltethető WEBMAG szerepkörök az elérési út mindig relatív a  **\\%ROLEROOT%\Approot\bin** mappa.<br /><br /> A teljes IIS és az IIS Express webes szerepkörök, ha a szerelvény nem található relatív  **\\%ROLEROOT%\Approot** mappa, a  **\\%ROLEROOT%\Approot\bin** keres.<br /><br /> Vissza ősszel teljes IIS viselkedése nem javasoljuk a legjobb, és lehet, hogy eltávolítja a jövőbeli verziói.|  
|targetFrameworkVersion|karakterlánc|Kötelező. A .NET-keretrendszer, amelyen készült el a szerelvény verziója. Például: `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a> webhelyek  
A `Sites` elem webhelyekhez és webes alkalmazásokhoz, amelyek egy webes szerepkörben gyűjteménye írja le. Az elem szülőjének a `Site` elemet. Ha nem ad meg egy `Sites` elem, a webes szerepkör örökölt webes szerepkörben van-e tárolva, és csak egy webhely ne a webes szerepkörben futó tartozhat. Ez az elem nem kötelező, és a szerepkör csak egy hely blokk lehet.

A `Sites` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

##  <a name="Site"></a> Helykiszolgáló  
A `Site` elem egy webhelyre vagy webalkalmazásra alkalmazást, a webes szerepkör részét képező határozza meg.

A `Site` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `Site` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. A webhely vagy alkalmazás neve.|  
|physicalDirectory|karakterlánc|A legfelső szintű hely tartalomkönyvtára helyét. A hely abszolút elérési útjával vagy .csdef helyéhez viszonyított adható meg.|  

##  <a name="VirtualApplication"></a> VirtualApplication  
A `VirtualApplication` elem definiálja az alkalmazás Internet Information Services (IIS) 7 olyan fájlok csoportjaiból állnak, amelyek valamilyen tartalmat vagy szolgáltatást biztosítanak protokollokon, például HTTP. Amikor hoz létre egy alkalmazást az IIS 7, az alkalmazás elérési útja bekerül a webhely URL-címe.

A `VirtualApplication` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `VirtualApplication` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. Adja meg egy nevet a virtuális alkalmazás azonosításához.|  
|physicalDirectory|karakterlánc|Kötelező. A fejlesztési számítógépen, amely tartalmazza a virtuális alkalmazás elérési útja. A compute emulator IIS úgy van konfigurálva, a következő helyről tartalmának lekéréséhez. Az Azure-ba való telepítésekor a fizikai könyvtár tartalma a szolgáltatás a többi együtt vannak csomagolva. A szolgáltatás csomag telepítésekor az Azure-ba, az IIS kicsomagolt tartalmának helye van beállítva.|  

##  <a name="VirtualDirectory"></a> VirtualDirectory  
A `VirtualDirectory` elem (más néven elérési út) könyvtár nevét határozza meg, hogy adja meg az IIS-ben, és egy fizikai könyvtár egy helyi vagy távoli kiszolgálón van leképezve.

A `VirtualDirectory` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `VirtualDirectory` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. Adja meg a virtuális könyvtár azonosító nevet.|  
|érték|physicalDirectory|Kötelező. A fejlesztési számítógépen, a webhely vagy a virtuális könyvtár tartalma tartalmazó elérési út megadása A compute emulator IIS úgy van konfigurálva, a következő helyről tartalmának lekéréséhez. Az Azure-ba való telepítésekor a fizikai könyvtár tartalma a szolgáltatás a többi együtt vannak csomagolva. A szolgáltatás csomag telepítésekor az Azure-ba, az IIS kicsomagolt tartalmának helye van beállítva.|  

##  <a name="Bindings"></a> Kötések  
A `Bindings` elem ismerteti egy webhely kötéseit gyűjteménye. A szülő eleme a `Binding` elemet. Az elemet kell megadni minden `Site` elemet. Végpontok konfigurálásával kapcsolatos további információkért lásd: [kommunikáció engedélyezése a Szerepkörpéldányok](cloud-services-enable-communication-role-instances.md).

A `Bindings` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

##  <a name="Binding"></a> Kötelező  
A `Binding` elem kérelmek egy webhelyre vagy webalkalmazásra alkalmazással történő kommunikációra a szükséges konfigurációs adatait adja meg.

A `Binding` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|név|karakterlánc|Kötelező. Meghatározza a kötés azonosító nevet.|  
|EndpointName|karakterlánc|Kötelező. A végpont neve lehet kötést létrehozni.|  
|Állomásfejléc|karakterlánc|Választható. Adja meg egy állomásnevet, amely lehetővé teszi több hely, másik állomásnevek, egy egyetlen IP-cím/Port kombinációja.|  

##  <a name="Startup"></a> Indítása  
A `Startup` elem, amely a szerepkör indításakor feladatok egy gyűjteményének írja le. Ez az elem szülőjének lehet a `Variable` elemet. A szerepkör indítása feladatok használatával kapcsolatos további információkért lásd: [indítási feladatok konfigurálása](cloud-services-startup-tasks.md). Ez az elem nem kötelező, és a szerepkör csak egy indítási blokk lehet.

A következő táblázat ismerteti az attribútum a `Startup` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|prioritás|int|Csak belső használatra.|  

##  <a name="Task"></a> A feladat  
A `Task` elem határozza meg, amely akkor történik meg a szerepkör indulásakor indítási feladat. Indítási feladatok készíti elő a szerepkör futtatásához ilyen telepítés szoftverösszetevőket, vagy más alkalmazásokat futtat feladatok végrehajtásához használható. Feladatok végrehajtása a megjelenítési belül a `Startup` elem blokkot.

A `Task` eleme csak érhető el az Azure SDK-verzió 1.3 használatával vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `Task` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|Parancssor|karakterlánc|Kötelező. Egy parancsprogram, például a parancsok futtatásához tartalmazó CMD-fájl. Indítási parancs és kötegelt fájlokat ANSI formátumban kell menteni. A fájl elején bájtsorrend jelölő beállított formátumok nem fogja feldolgozni megfelelően.|  
|executionContext|karakterlánc|Megadja a környezetet, amelyben a parancsfájl futtatása.<br /><br /> -   `limited` [Alapértelmezett] – ugyanazon jogokkal rendelkeznek, mint a folyamatot futtató szerepkör futtatásához.<br />-   `elevated` – Rendszergazdai jogosultságokkal futtassa.|  
|taskType|karakterlánc|A parancs végrehajtási viselkedésének megadása<br /><br /> -   `simple` [Alapértelmezett] – a rendszer vár kilép egyéb feladatok végrehajtása előtt a feladathoz.<br />-   `background` – A rendszer nem várja meg a feladat való kilépéshez.<br />-   `foreground` – Hasonló háttér, kivéve a szerepkör nem indítják újra amíg feladataival előtérben történő kilépéshez.|  

##  <a name="Contents"></a> Tartalom  
A `Contents` elem írja le a tartalmat egy webes szerepkör gyűjteménye. Az elem szülőjének a `Content` elemet.

A `Contents` eleme csak elérhető az Azure SDK-verzió 1.5-ös vagy újabb verzióját.

##  <a name="Content"></a> Tartalom  
A `Content` elem másolása az Azure virtuális gép és a cél elérési útja, akkor másolja a tartalom elérési útvonalát határozza meg.

A `Content` eleme csak elérhető az Azure SDK-verzió 1.5-ös vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `Content` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|Cél|karakterlánc|Kötelező. Az Azure virtuális gépen, amelyre a tartalmat el van helyezve helye. Ez a hely van a mappa alapján értelmezett relatív **%ROLEROOT%\Approot**.|  

Az elem azon a `SourceDirectory` elemet.

##  <a name="SourceDirectory"></a> SourceDirectory  
A `SourceDirectory` elem definiálja, amelyből a tartalmat a rendszer átmásolja a helyi címtárszolgáltatásban. Az elem segítségével adja meg a helyi tartalom másolása az Azure virtuális géphez.

A `SourceDirectory` eleme csak elérhető az Azure SDK-verzió 1.5-ös vagy újabb verzióját.

A következő táblázat ismerteti az attribútumai a `SourceDirectory` elemet.

| Attribútum | Típus | Leírás |  
| --------- | ---- | ----------- |  
|elérési út|karakterlánc|Kötelező. A helyi könyvtárat, amelyek tartalma fogja másolni a virtuális gépet az Azure relatív vagy abszolút elérési utat. A könyvtár elérési útja a környezeti változók bővítése esetén támogatott.|  
  
## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) Definition séma](schema-csdef-file.md)
