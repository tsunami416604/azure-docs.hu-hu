---
title: Azure Cloud Services def. WorkerRole séma | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: tgore03
ms.author: tagore
ms.openlocfilehash: 518fa0b64277d056796669a3c9f93c9c22325d91
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449023"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Azure Cloud Services Definition WorkerRole séma
Az Azure feldolgozói szerepkör olyan szerepkör, amely általánosított fejlesztéshez hasznos, és egy webes szerepkörhöz tartozó háttérbeli feldolgozást is végezhet.

A szolgáltatás definíciós fájljának alapértelmezett kiterjesztése. csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Alapszintű szolgáltatás-definíciós séma egy feldolgozói szerepkörhöz.
A feldolgozói szerepkört tartalmazó szolgáltatás-definíciós fájl alapszintű formátuma a következő.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
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
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
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
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Séma elemei
A szolgáltatás definíciós fájlja tartalmazza ezeket az elemeket, amelyeket a jelen témakör következő részeiben talál részletesen:

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Beállítás](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Végpontok](#Endpoints)

[InputEndpoint](#InputEndpoint)

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

[BelépésiPont](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Változó](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Startup](#Startup)

[Tevékenység](#Task)

[Tartalmát](#Contents)

[Tartalom](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WorkerRole"></a>WorkerRole
A `WorkerRole` elem olyan szerepkört ír le, amely általánosított fejlesztéshez hasznos, és a webes szerepkörökhöz tartozó háttérbeli feldolgozást is végezhet. Egy szolgáltatás nulla vagy több feldolgozói szerepkört is tartalmazhat.

Az alábbi táblázat a `WorkerRole` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A feldolgozói szerepkör neve. A szerepkör nevének egyedinek kell lennie.|
|enableNativeCodeExecution|logikai|Választható. Az alapértelmezett érték `true`; a natív kód végrehajtása és a teljes megbízhatóság alapértelmezés szerint engedélyezve van. Ezt az attribútumot úgy állítsa be `false`, hogy letiltsa a natív kód végrehajtását a feldolgozói szerepkörben, és használja inkább az Azure részleges megbízhatóságát.|
|vmsize|sztring|Választható. Állítsa be ezt az értéket a szerepkörhöz kiosztott virtuális gép méretének módosításához. Az alapértelmezett érték 0.`Small` A lehetséges virtuálisgép-méretek és azok attribútumainak listáját lásd: [Cloud Services virtuális gépek méretei](cloud-services-sizes-specs.md).|

##  <a name="ConfigurationSettings"></a>ConfigurationSettings
A `ConfigurationSettings` elem a feldolgozói szerepkör konfigurációs beállításainak gyűjteményét ismerteti. Ez az elem a `Setting` elem szülője.

##  <a name="Setting"></a>Beállítás
A `Setting` elem egy olyan név és érték párokat ír le, amely egy szerepkör egy példányának konfigurációs beállítását adja meg.

Az alábbi táblázat a `Setting` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A konfigurációs beállítás egyedi neve.|

A szerepkör konfigurációs beállításai név és érték párok, amelyek deklarálva vannak a szolgáltatás-definíciós fájlban, és a szolgáltatás konfigurációs fájljában vannak megadva.

##  <a name="LocalResources"></a>LocalResources
A `LocalResources` elem a feldolgozói szerepkör helyi tárolási erőforrásainak gyűjteményét ismerteti. Ez az elem a `LocalStorage` elem szülője.

##  <a name="LocalStorage"></a>LocalStorage
A `LocalStorage` elem egy helyi tárolási erőforrást azonosít, amely a szolgáltatáshoz tartozó fájlrendszeri területet biztosít futásidőben. Egy szerepkör nulla vagy több helyi tárolási erőforrást is meghatározhat.

> [!NOTE]
>  A `LocalStorage` elem a `WorkerRole` elem gyermeke is megjelenhet az Azure SDK korábbi verzióival való kompatibilitás érdekében.

Az alábbi táblázat a `LocalStorage` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A helyi tároló egyedi neve.|
|cleanOnRoleRecycle|logikai|Választható. Azt jelzi, hogy a helyi tárolót meg kell-e tisztítani a szerepkör újraindításakor. Az alapértelmezett érték `true`.|
|sizeInMb|int|Választható. A helyi tároló számára lefoglalni kívánt tárterület (MB). Ha nincs megadva, a lefoglalt alapértelmezett tárterület 100 MB. A lefoglalt tárterület minimális mérete 1 MB.<br /><br /> A helyi erőforrások maximális mérete a virtuális gép méretétől függ. További információ: [Cloud Services virtuális gépek méretei](cloud-services-sizes-specs.md).|

A helyi tárolási erőforráshoz lefoglalt könyvtár neve megegyezik a name attribútumhoz megadott értékkel.

##  <a name="Endpoints"></a>Végpontok
A `Endpoints` elem ismerteti egy szerepkör bemeneti (külső), belső és példány típusú bemeneti végpontok gyűjteményét. Ez az elem a `InputEndpoint`, `InternalEndpoint`és `InstanceInputEndpoint` elemek szülője.

A bemeneti és a belső végpontok külön vannak lefoglalva. A szolgáltatás összesen 25 bemeneti, belső és példány típusú bemeneti végponttal rendelkezhet, amelyek lefoglalhatók a szolgáltatásban engedélyezett 25 szerepkör között. Ha például 5 szerepkörrel rendelkezik, legfeljebb 5 bemeneti végpontot foglalhat le, vagy 25 bemeneti végpontot foglalhat le egyetlen szerepkörhöz, vagy 1 bemeneti végpontot is lefoglalhat 25 szerepkörre.

> [!NOTE]
>  Minden telepített szerepkörhöz egy példány szükséges. Az előfizetéshez tartozó alapértelmezett kiépítés legfeljebb 20 magot jelent, így a szerepkör 20 példányára van korlátozva. Ha az alkalmazásnak több példányra van szüksége, mint amennyit az alapértelmezett kiépítés során, tekintse meg a [Számlázási, az előfizetés-kezelés és a kvóta támogatását](https://azure.microsoft.com/support/options/) a kvóta növelésével kapcsolatos további információkért.

##  <a name="InputEndpoint"></a>InputEndpoint
A `InputEndpoint` elem egy feldolgozói szerepkör külső végpontját ismerteti.

Több végpontot is megadhat, amelyek a HTTP-, HTTPS-, UDP-és TCP-végpontok kombinációját jelentik. Megadhatja a bemeneti végponthoz választott portszámot, de a szolgáltatás egyes szerepköreihez megadott portszámoknak egyedinek kell lenniük. Ha például azt adja meg, hogy egy szerepkör az 80-es portot használja a HTTP-hez és a 443-es porthoz a HTTPS-hez, akkor megadhatja, hogy egy második szerepkör a 8080-as portot használja a HTTP protokollhoz, és az 8043

Az alábbi táblázat a `InputEndpoint` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A külső végpont egyedi neve.|
|protokoll|sztring|Kötelező. A külső végpont átviteli protokollja. Feldolgozói szerepkör esetén a lehetséges értékek a következők: `HTTP`, `HTTPS`, `UDP`vagy `TCP`.|
|port|int|Kötelező. A külső végpont portja. Megadhat bármely kiválasztott portszámot, de a szolgáltatás egyes szerepköreihez megadott portszámoknak egyedinek kell lenniük.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|
|tanúsítvány|sztring|HTTPS-végponthoz szükséges. `Certificate` elem által meghatározott tanúsítvány neve.|
|localPort|int|Választható. A végponton belüli belső kapcsolatokhoz használt portot adja meg. A `localPort` attribútum a végpont külső portját a szerepkör belső portjára képezi le. Ez olyan esetekben hasznos, amikor a szerepkörnek olyan belső összetevővel kell kommunikálnia egy olyan porton, amely különbözik a külsőleg elérhetőtől.<br /><br /> Ha nincs megadva, a `localPort` értéke megegyezik a `port` attribútummal. Állítsa a `localPort` értékét "*" értékre, hogy automatikusan rendeljen hozzá egy nem lefoglalt portot, amely a futtatókörnyezeti API használatával felderíthető.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).<br /><br /> A `localPort` attribútum csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.|
|ignoreRoleInstanceStatus|logikai|Választható. Ha az attribútum értéke `true`, akkor a rendszer figyelmen kívül hagyja a szolgáltatás állapotát, és a terheléselosztó nem távolítja el a végpontot. Ez az érték úgy van beállítva, hogy `true` hasznos legyen a szolgáltatás foglalt példányainak hibakereséséhez. Az alapértelmezett érték 0.`false` **Megjegyzés:** A végpontok akkor is fogadhatnak forgalmat, ha a szerepkör nem üzemkész állapotban van.|
|loadBalancerProbe|sztring|Választható. A bemeneti végponthoz rendelt terheléselosztó-mintavétel neve. További információ: [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md).|

##  <a name="InternalEndpoint"></a>InternalEndpoint
A `InternalEndpoint` elem a feldolgozói szerepkör belső végpontját ismerteti. Egy belső végpont csak a szolgáltatáson belül futó egyéb szerepkör-példányok számára érhető el. a szolgáltatáson kívüli ügyfelek számára nem érhető el. Egy feldolgozói szerepkör legfeljebb öt HTTP-, UDP-vagy TCP belső végponttal rendelkezhet.

Az alábbi táblázat a `InternalEndpoint` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A belső végpont egyedi neve.|
|protokoll|sztring|Kötelező. A belső végpont átviteli protokollja. A lehetséges értékek a következők: `HTTP`, `TCP`, `UDP`vagy `ANY`.<br /><br /> `ANY` érték azt jelenti, hogy bármely protokoll, bármely port engedélyezett.|
|port|int|Választható. A végponton belüli belső terheléselosztási kapcsolatokhoz használt port. Egy elosztott terhelésű végpont két portot használ. A nyilvános IP-címhez használt port, valamint a magánhálózati IP-címen használt port. Ezek általában ugyanazok, de különböző portok használatát is választhatja.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).<br /><br /> A `Port` attribútum csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.|

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint
A `InstanceInputEndpoint` elem egy példány bemeneti végpontját írja le egy feldolgozói szerepkörhöz. Egy példány bemeneti végpontja egy adott szerepkör-példánnyal van társítva a terheléselosztó porton keresztüli továbbításával. Az egyes példányok bemeneti végpontja egy adott portra van leképezve a lehetséges portok köréből. Ez az elem a `AllocatePublicPortFrom` elem szülője.

A `InstanceInputEndpoint` elem csak az Azure SDK 1,7-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `InstanceInputEndpoint` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A végpont egyedi neve.|
|localPort|int|Kötelező. Meghatározza azt a belső portot, amelyet az összes szerepkör-példány figyelni fog a terheléselosztó által továbbított bejövő forgalom fogadásához. A lehetséges értékek tartománya 1 és 65535 között van, beleértve a értéket.|
|protokoll|sztring|Kötelező. A belső végpont átviteli protokollja. A lehetséges értékek: `udp` és `tcp`. HTTP-/HTTPS-alapú forgalomhoz használjon `tcp`.|

##  <a name="AllocatePublicPortFrom"></a>AllocatePublicPortFrom
A `AllocatePublicPortFrom` elem azt a nyilvános porttartomány ismerteti, amelyet a külső ügyfelek az egyes példányok bemeneti végpontjának eléréséhez használhatnak. A nyilvános (VIP) portszám le van foglalva ebből a tartományból, és az egyes szerepkör-példányok végpontja számára van hozzárendelve a bérlői telepítés és a frissítés során. Ez az elem a `FixedPortRange` elem szülője.

A `AllocatePublicPortFrom` elem csak az Azure SDK 1,7-es vagy újabb verziójának használatával érhető el.

##  <a name="FixedPort"></a>FixedPort
A `FixedPort` elem a belső végpont portját határozza meg, amely lehetővé teszi a terheléselosztás elosztott terhelésű kapcsolatait a végponton.

A `FixedPort` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `FixedPort` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|port|int|Kötelező. A belső végpont portja. Ennek ugyanaz a hatása, mint a `FixedPortRange` min és a Max értéket ugyanahhoz a porthoz.<br /><br /> A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|

##  <a name="FixedPortRange"></a>FixedPortRange
A `FixedPortRange` elem a belső végponthoz vagy példányhoz tartozó bemeneti végponthoz rendelt portok tartományát adja meg, és beállítja a végponton elosztott terhelésű kapcsolatokhoz használt portot.

> [!NOTE]
>  A `FixedPortRange` elem eltérő módon működik attól függően, hogy melyik elemet tárolja a rendszer. Ha a `FixedPortRange` elem a `InternalEndpoint` elemben van, akkor az összes olyan virtuális gép minimális és maximális attribútumainak tartományán belül megnyílik a terheléselosztó összes portja, amelyen a szerepkör fut. Ha a `FixedPortRange` elem a `InstanceInputEndpoint` elemben található, akkor a rendszer csak egy portot nyit meg a minimális és a maximális attribútum tartományán belül minden olyan virtuális gépen, amelyen a szerepkör fut.

A `FixedPortRange` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `FixedPortRange` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|perc|int|Kötelező. A tartomány minimális portszáma. A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|
|max.|sztring|Kötelező. A tartományon belüli maximális port. A lehetséges értékek tartománya 1 és 65535 között van (az Azure SDK 1,7-es vagy újabb verziója).|

##  <a name="Certificates"></a>Tanúsítványok
A `Certificates` elem ismerteti egy feldolgozói szerepkör tanúsítványainak gyűjteményét. Ez az elem a `Certificate` elem szülője. Egy szerepkörhöz a társított tanúsítványok száma is tartozhat. További információ a tanúsítványok elem használatáról: [a szolgáltatás definíciós fájljának módosítása tanúsítvánnyal](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Tanúsítvány
A `Certificate` elem egy feldolgozói szerepkörhöz társított tanúsítványt ír le.

Az alábbi táblázat a `Certificate` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A tanúsítvány neve, amely akkor használható, ha egy HTTPS `InputEndpoint` elemhez van társítva.|
|storeLocation|sztring|Kötelező. Azon tanúsítványtároló helye, ahol ez a tanúsítvány megtalálható a helyi gépen. A lehetséges értékek a következők: `CurrentUser` és `LocalMachine`.|
|storeName|sztring|Kötelező. Annak a tanúsítványtárolónak a neve, ahol a tanúsítvány a helyi gépen található. A lehetséges értékek közé tartoznak a beépített tárolók neve `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`vagy bármely egyéni tároló neve. Ha meg van adva egy egyéni tároló neve, a rendszer automatikusan létrehozza az áruházat.|
|permissionLevel|sztring|Választható. Megadja a szerepkör-folyamatok számára megadott hozzáférési engedélyeket. Ha azt szeretné, hogy csak emelt szintű folyamatok férhessenek hozzá a titkos kulcshoz, adja meg `elevated` engedélyt. `limitedOrElevated` engedély lehetővé teszi az összes szerepkör-folyamat számára a titkos kulcs elérését. A lehetséges értékek: `limitedOrElevated` és `elevated`. Az alapértelmezett érték 0.`limitedOrElevated`|

##  <a name="Imports"></a>Importálja
A `Imports` elem az importálási modulok gyűjteményét írja le egy feldolgozói szerepkörhöz, amely összetevőket ad hozzá a vendég operációs rendszerhez. Ez az elem a `Import` elem szülője. Ez az elem nem kötelező, és a szerepkörnek csak egy futásidejű blokkja lehet.

A `Imports` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

##  <a name="Import"></a>Importálása
A `Import` elem a vendég operációs rendszerbe felvenni kívánt modult adja meg.

A `Import` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `Import` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|moduleName|sztring|Kötelező. Az importálandó modul neve. Az érvényes importálási modulok a következők:<br /><br /> – RemoteAccess<br />- RemoteForwarder<br />-Diagnosztika<br /><br /> A RemoteAccess és a RemoteForwarder modulok lehetővé teszik a szerepkör-példány konfigurálását a távoli asztali kapcsolatokhoz. További információ: [Távoli asztali kapcsolat engedélyezése](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> A diagnosztikai modul lehetővé teszi a szerepkör-példány diagnosztikai adatainak gyűjtését|

##  <a name="Runtime"></a>Runtime
A `Runtime` elem az Azure-gazdagép folyamatának futásidejű környezetét vezérlő feldolgozói szerepkörhöz tartozó környezeti változók beállításainak gyűjteményét ismerteti. Ez az elem a `Environment` elem szülője. Ez az elem nem kötelező, és a szerepkörnek csak egy futásidejű blokkja lehet.

A `Runtime` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `Runtime` elem attribútumait ismerteti:

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|ExecutionContext|sztring|Választható. Meghatározza azt a környezetet, amelyben a szerepkör-folyamat elindul. Az alapértelmezett környezet `limited`.<br /><br /> -   `limited` – a folyamat rendszergazdai jogosultságok nélkül indul el.<br />-   `elevated` – a folyamat rendszergazdai jogosultságokkal indítható el.|

##  <a name="Environment"></a>Környezet
A `Environment` elem egy feldolgozói szerepkörhöz tartozó környezeti változó beállításainak gyűjteményét ismerteti. Ez az elem a `Variable` elem szülője. Egy szerepkörhöz tetszőleges számú környezeti változó állítható be.

##  <a name="Variable"></a>Változó
A `Variable` elem egy környezeti változót határoz meg, amely a vendég operációs szolgáltatásban állítható be.

A `Variable` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `Variable` elem attribútumait ismerteti:

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A beállítani kívánt környezeti változó neve.|
|érték|sztring|Választható. A környezeti változóhoz beállított érték Tartalmaznia kell egy Value attribútumot vagy egy `RoleInstanceValue` elemet.|

##  <a name="RoleInstanceValue"></a>RoleInstanceValue
A `RoleInstanceValue` elem meghatározza azt az xPath-értéket, amelyből a változó értékét le kell olvasni.

Az alábbi táblázat a `RoleInstanceValue` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|XPath|sztring|Választható. A példány telepítési beállításainak helyének elérési útja. További információ: [konfigurációs változók az XPath](cloud-services-role-config-xpath.md)-ban.<br /><br /> Tartalmaznia kell egy Value attribútumot vagy egy `RoleInstanceValue` elemet.|

##  <a name="EntryPoint"></a>BelépésiPont
A `EntryPoint` elem a szerepkör belépési pontját adja meg. Ez az elem a `NetFxEntryPoint` elemek szülője. Ezek az elemek lehetővé teszik, hogy az alapértelmezett WaWorkerHost. exe fájltól eltérő alkalmazást határozzon meg szerepkör-belépési pontként.

A `EntryPoint` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

##  <a name="NetFxEntryPoint"></a>NetFxEntryPoint
A `NetFxEntryPoint` elem határozza meg a szerepkörhöz futtatandó programot.

> [!NOTE]
>  A `NetFxEntryPoint` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `NetFxEntryPoint` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|AssemblyName|sztring|Kötelező. A belépési pontot tartalmazó szerelvény elérési útja és fájlneve. Az elérési út a **%ROLEROOT%\Approot\\** mappához viszonyítva (ne határozza meg **\\%ROLEROOT%\Approot** `commandLine`, feltételezve). **% ROLEROOT%** az Azure által fenntartott környezeti változó, amely a szerepkör gyökérkönyvtárának helyét jelöli. A **\\%ROLEROOT%\Approot** mappa a szerepkörhöz tartozó alkalmazás mappáját jelöli.|
|targetFrameworkVersion|sztring|Kötelező. A .NET-keretrendszer azon verziója, amelyen a szerelvény létrejött. Például: `targetFrameworkVersion="v4.0"`.|

##  <a name="ProgramEntryPoint"></a>ProgramEntryPoint
A `ProgramEntryPoint` elem határozza meg a szerepkörhöz futtatandó programot. A `ProgramEntryPoint` elem lehetővé teszi olyan program belépési pontjának megadását, amely nem .NET-szerelvényen alapul.

> [!NOTE]
>  A `ProgramEntryPoint` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `ProgramEntryPoint` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|CommandLine|sztring|Kötelező. A végrehajtandó program elérési útja, fájlneve és bármely parancssori argumentuma. Az elérési út a **%ROLEROOT%\Approot** mappához viszonyítva (ne határozza meg a **%ROLEROOT%\Approot** a parancssorban, feltételezve). **% ROLEROOT%** az Azure által fenntartott környezeti változó, amely a szerepkör gyökérkönyvtárának helyét jelöli. A **%ROLEROOT%\Approot** mappa a szerepkörhöz tartozó alkalmazás mappáját jelöli.<br /><br /> Ha a program véget ér, a rendszer újrahasznosítja a szerepkört, így általában úgy állítja be a programot, hogy továbbra is fusson, ahelyett, hogy olyan programot futtasson, amely egy véges feladatot futtat.|
|setReadyOnProcessStart|logikai|Kötelező. Azt adja meg, hogy a szerepkör-példány megvárja-e a parancssori program elindítását. Ezt az értéket jelenleg `true` értékre kell állítani. A `false` értékének beállítása későbbi használatra van fenntartva.|

##  <a name="Startup"></a>Indítási
A `Startup` elem a szerepkör indításakor futtatott feladatok gyűjteményét írja le. Ez az elem lehet a `Variable` elem szülője. További információ a szerepkör-indítási feladatok használatáról: [indítási feladatok konfigurálása](cloud-services-startup-tasks.md). Ez az elem nem kötelező, és a szerepkörnek csak egy indítási blokkja lehet.

Az alábbi táblázat a `Startup` elem attribútumát ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|prioritású|int|Kizárólag belső használatra|

##  <a name="Task"></a>Feladat
A `Task` elem a szerepkör indításakor megjelenő indítási feladatot határozza meg. Az indítási feladatok olyan feladatok elvégzésére használhatók, amelyek előkészítik a szerepkört az ilyen telepítési szoftver-összetevők futtatására vagy más alkalmazások futtatására. A feladatok a `Startup` elem blokkjában megjelenő sorrendben futnak.

A `Task` elem csak az Azure SDK 1,3-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `Task` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|CommandLine|sztring|Kötelező. Egy parancsfájl, például egy CMD-fájl, amely a futtatandó parancsokat tartalmazza. Az indítási parancsot és a Batch-fájlokat ANSI formátumban kell menteni. A byte-Order jelölőt a fájl elején beállított fájlformátumok nem fognak megfelelően feldolgozni.|
|ExecutionContext|sztring|Meghatározza azt a környezetet, amelyben a parancsfájl fut.<br /><br /> -   `limited` [alapértelmezett] – futtassa ugyanazokkal a jogosultságokkal, mint a folyamatot üzemeltető szerepkör.<br />-   `elevated` – Futtatás rendszergazdai jogosultságokkal.|
|taskType|sztring|Meghatározza a parancs végrehajtási viselkedését.<br /><br /> -   `simple` [default] – a rendszer megvárja, amíg a feladat kilép a többi feladat elindítása előtt.<br />-   `background` – a rendszer nem várja meg a feladat kilépését.<br />-   `foreground` – a háttérhez hasonlóan, a szerepkör nem indul el, amíg az összes előtér-feladat ki nem fejeződik.|

##  <a name="Contents"></a>Tartalmát
A `Contents` elem a feldolgozói szerepkör tartalmának gyűjteményét írja le. Ez az elem a `Content` elem szülője.

A `Contents` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

##  <a name="Content"></a>Tartalom
A `Content` elem határozza meg az Azure-beli virtuális gépre másolandó tartalom forrásának helyét, valamint a cél elérési útját, ahová a rendszer átmásolja.

A `Content` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `Content` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|destination|sztring|Kötelező. Annak az Azure-beli virtuális gépnek a helye, ahová a tartalom kerül. Ez a hely a **%ROLEROOT%\Approot**mappához képest relatív.|

Ez az elem a `SourceDirectory` elem szülő eleme.

##  <a name="SourceDirectory"></a>SourceDirectory
A `SourceDirectory` elem határozza meg azt a helyi könyvtárat, amelyből a tartalmat másolni kívánja. Ezzel az elemmel adhatja meg az Azure-beli virtuális gépre másolandó helyi tartalmat.

A `SourceDirectory` elem csak az Azure SDK 1,5-es vagy újabb verziójának használatával érhető el.

Az alábbi táblázat a `SourceDirectory` elem attribútumait ismerteti.

| Attribútum | Type (Típus) | Leírás |
| --------- | ---- | ----------- |
|path|sztring|Kötelező. Egy helyi könyvtár relatív vagy abszolút elérési útja, amelynek tartalmát a rendszer az Azure-beli virtuális gépre másolja. A környezeti változók kiterjesztése a könyvtár elérési útjában támogatott.|

## <a name="see-also"></a>Lásd még:
[Cloud Service (klasszikus) definíciós séma](schema-csdef-file.md)



