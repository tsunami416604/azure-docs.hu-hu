---
title: Azure Cloud Services def. WorkerRole séma | Microsoft dokumentumok
description: Az Azure feldolgozói szerepkör általános fejlesztésre szolgál, és egy webes szerepkör háttérfeldolgozását végezhet. Ismerje meg az Azure feldolgozói szerepkör sémája.
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
ms.openlocfilehash: 26225442c72fb209bb1ac4cd2bf4777fb39542fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534371"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Azure Cloud Services definition WorkerRole séma
Az Azure feldolgozói szerepkör egy olyan szerepkör, amely általános fejlesztéshez hasznos, és egy webes szerepkör háttérfeldolgozását végezheti el.

A szolgáltatásdefiníciós fájl alapértelmezett kiterjesztése .csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Alapvető szolgáltatásdefiníciós séma egy feldolgozói szerepkörhöz.
A feldolgozói szerepkört tartalmazó szolgáltatásdefiníciós fájl alapvető formátuma a következő.

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

## <a name="schema-elements"></a>Sémaelemek
A szolgáltatásdefiníciós fájl ezeket az elemeket tartalmazza, amelyeket a témakör későbbi szakaszai részletesen ismertetett:

[WorkerRole (Dolgozói szerep)](#WorkerRole)

[ConfigurationSettings (Konfigurációs beállítások)](#ConfigurationSettings)

[Beállítás](#Setting)

[Helyi források](#LocalResources)

[Localstorage](#LocalStorage)

[Végpontok](#Endpoints)

[Bemenetivégpont](#InputEndpoint)

[Belső végpont](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[PublicPortFrom felosztása](#AllocatePublicPortFrom)

[Rögzített port](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Tanúsítványok](#Certificates)

[Tanúsítvány](#Certificate)

[Behozatal](#Imports)

[Importálás](#Import)

[Futtatókörnyezet](#Runtime)

[Környezet](#Environment)

[EntryPoint](#EntryPoint)

[NetfxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Változó](#Variable)

[RoleInstanceValue érték](#RoleInstanceValue)

[Indítás](#Startup)

[Tevékenység](#Task)

[Tartalmát](#Contents)

[Tartalom](#Content)

[Forráskönyvtár](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a>WorkerRole (Dolgozói szerep)
Az `WorkerRole` elem egy olyan szerepkört ír le, amely általános fejlesztéshez hasznos, és háttérfeldolgozást végezhet egy webes szerepkörhöz. Egy szolgáltatás nulla vagy több feldolgozói szerepkört tartalmazhat.

Az alábbi táblázat az `WorkerRole` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A feldolgozói szerepkör neve. A szerepkör nevének egyedinek kell lennie.|
|enableNativeCodeExecution|logikai|Választható. Az alapértelmezett `true`érték a ; natív kódfuttatásés a teljes megbízhatóság alapértelmezés szerint engedélyezve van. Állítsa be ezt `false` az attribútumot, hogy tiltsa le a natív kódvégrehajtását a feldolgozói szerepkörhöz, és használja az Azure részleges megbízhatósági használatát.|
|vmsize|sztring|Választható. Állítsa be ezt az értéket a szerepkörhöz kiosztott virtuális gép méretének módosításához. Az alapértelmezett érték `Small`. A virtuális gépek lehetséges méreteiről és azok attribútumairól a [Felhőszolgáltatások virtuálisgép-méretei című témakörben található.](cloud-services-sizes-specs.md)|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a>ConfigurationSettings (Konfigurációs beállítások)
Az `ConfigurationSettings` elem egy feldolgozói szerepkör konfigurációs beállításainak gyűjteményét írja le. Ez az elem az `Setting` elem szülője.

##  <a name="setting"></a><a name="Setting"></a>Beállítás
Az `Setting` elem egy nevet és értékpárt ír le, amely egy szerepkör egy példányának konfigurációs beállítását adja meg.

Az alábbi táblázat az `Setting` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A konfigurációs beállítás egyedi neve.|

A szerepkör konfigurációs beállításai a szolgáltatásdefiníciós fájlban deklarált és a szolgáltatáskonfigurációs fájlban beállított név- és értékpárok.

##  <a name="localresources"></a><a name="LocalResources"></a>Helyi források
Az `LocalResources` elem egy feldolgozói szerepkör helyi tárolási erőforrásainak gyűjteményét ismerteti. Ez az elem az `LocalStorage` elem szülője.

##  <a name="localstorage"></a><a name="LocalStorage"></a>Localstorage
Az `LocalStorage` elem azonosítja a helyi tárolási erőforrást, amely futásidőben helyet biztosít a szolgáltatás számára. Egy szerepkör nulla vagy több helyi tárolási erőforrást határozhat meg.

> [!NOTE]
>  Az `LocalStorage` elem megjelenhet az `WorkerRole` elem gyermekeként az Azure SDK korábbi verzióival való kompatibilitás támogatásához.

Az alábbi táblázat az `LocalStorage` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A helyi üzlet egyedi neve.|
|cleanOnRoleRecycle|logikai|Választható. Azt jelzi, hogy a szerepkör újraindításakor meg kell-e tisztítani a helyi tárolót. Az alapértelmezett `true`érték a .|
|méretInMb|int|Választható. A helyi tároló számára lefoglalandó tárhely kívánt mennyisége MB-ban. Ha nincs megadva, az alapértelmezett lefoglalt tárhely 100 MB. A lefoglalható tárterület minimális összege 1 MB.<br /><br /> A helyi erőforrások maximális mérete a virtuális gép méretétől függ. További információ: [Virtual Machine sizes for Cloud Services](cloud-services-sizes-specs.md).|

A helyi tárolási erőforrásszámára lefoglalt könyvtár neve megfelel a névattribútumhoz megadott értéknek.

##  <a name="endpoints"></a><a name="Endpoints"></a>Végpontok
Az `Endpoints` elem egy szerepkör bemeneti (külső), belső és példánybemeneti végpontjainak gyűjteményét írja le. Ez az elem a `InputEndpoint` `InternalEndpoint`, `InstanceInputEndpoint` és az elemek szülője.

A bemeneti és a belső végpontok külön vannak lefoglalva. Egy szolgáltatás összesen 25 bemeneti, belső és példány bemeneti végpontok, amelyek egy szolgáltatásban engedélyezett 25 szerepkör között foglalható el. Ha például 5 szerepkörrel rendelkezik, szerepkörenként 5 bemeneti végpontot rendelhet hozzá, vagy 25 bemeneti végpontot rendelhet egyetlen szerepkörhöz, vagy egyenként 1 bemeneti végpontot rendelhet ki 25 szerepkörhöz.

> [!NOTE]
>  Minden üzembe helyezett szerepkörhöz szerepkörenként egy példány szükséges. Az előfizetés alapértelmezett kiépítése legfeljebb 20 mag, és így egy szerepkör 20 példányai. Ha az alkalmazás több példányt igényel, mint amennyit az alapértelmezett kiépítés biztosít, olvassa el a [Számlázás, az előfizetés-kezelés és a kvótatámogatás](https://azure.microsoft.com/support/options/) című témakört a kvóta növelésével kapcsolatos további információkért.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a>Bemenetivégpont
Az `InputEndpoint` elem egy feldolgozói szerepkör külső végpontját írja le.

Több végpontot is definiálhat, amelyek HTTP, HTTPS, UDP és TCP-végpontok kombinációját képezik. Bármilyen portszámot megadhat egy bemeneti végponthoz, de a szolgáltatás egyes szerepköreihez megadott portszámoknak egyedinek kell lenniük. Ha például azt adja meg, hogy egy szerepkör a HTTP 80-as portját, a HTTPS-hez pedig a 443-as portot használja, megadhatja, hogy egy második szerepkör a 8080-as portot használja a HTTP-hez és a 8043-as portot a HTTPS-hez.

Az alábbi táblázat az `InputEndpoint` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A külső végpont egyedi neve.|
|Protokoll|sztring|Kötelező. A külső végpont átviteli protokollja. Feldolgozói szerepkör esetén a `HTTP` `HTTPS`lehetséges `UDP`értékek `TCP`a , , vagy .|
|port|int|Kötelező. A külső végpont portja. Bármilyen portszámot megadhat, de a szolgáltatás egyes szerepköreihez megadott portszámoknak egyedinek kell lenniük.<br /><br /> A lehetséges értékek 1 és 65535 között mozognak, bezárólag (Az Azure SDK 1.7-es vagy újabb verziója).|
|tanúsítvány|sztring|HTTPS-végponthoz szükséges. Egy `Certificate` elem által definiált tanúsítvány neve.|
|localPort|int|Választható. A végpont belső kapcsolataihoz használt portot adja meg. Az `localPort` attribútum leképezi a külső port a végponton egy belső port egy szerepkör. Ez olyan esetekben hasznos, amikor egy szerepkörnek kommunikálnia kell egy külsőleg elérhető port egy belső összetevőjével.<br /><br /> Ha nincs megadva, `localPort` az értéke megegyezik az `port` attribútummal. Állítsa be `localPort` a "*" értéket, hogy automatikusan hozzárendeljen egy fel nem osztott portot, amely felderíthető a futásidejű API-val.<br /><br /> A lehetséges értékek 1 és 65535 között mozognak, bezárólag (Az Azure SDK 1.7-es vagy újabb verziója).<br /><br /> Az `localPort` attribútum csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.|
|ignoreRoleInstanceStatus|logikai|Választható. Ha ennek az attribútumnak `true`az értéke a beállítás, a szolgáltatás állapotát a rendszer figyelmen kívül hagyja, és a végpontot a terheléselosztó nem távolítja el. Ha ezt `true` az értéket hasznosra állítja egy szolgáltatás foglalt példányainak hibakereséséhez. Az alapértelmezett érték `false`. **Megjegyzés:** Egy végpont továbbra is fogadhat forgalmat akkor is, ha a szerepkör nem kész állapotban van.|
|loadBalancerProbe|sztring|Választható. A bemeneti végponthoz társított terheléselosztó mintavételének neve. További információ: [LoadBalancerProbe Séma](schema-csdef-loadbalancerprobe.md).|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a>Belső végpont
Az `InternalEndpoint` elem egy feldolgozói szerepkör belső végpontját írja le. A belső végpont csak a szolgáltatáson belül futó többi szerepkörpéldány számára érhető el; nem érhető el a szolgáltatáson kívüli ügyfelek számára. Egy feldolgozói szerepkör legfeljebb öt HTTP, UDP vagy TCP belső végponttal rendelkezhet.

Az alábbi táblázat az `InternalEndpoint` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A belső végpont egyedi neve.|
|Protokoll|sztring|Kötelező. A belső végpont átviteli protokollja. A lehetséges `HTTP` `TCP`értékek `UDP`a `ANY`, , vagy .<br /><br /> Az érték `ANY` azt adja meg, hogy bármely protokoll, bármely port engedélyezett.|
|port|int|Választható. A végpont belső terheléselosztásos kapcsolataihoz használt port. A Terheléselosztású végpont két portot használ. A nyilvános IP-címhez használt port és a privát IP-címen használt port. Ezek általában azonosra vannak állítva, de választhat, hogy különböző portokat használ.<br /><br /> A lehetséges értékek 1 és 65535 között mozognak, bezárólag (Az Azure SDK 1.7-es vagy újabb verziója).<br /><br /> Az `Port` attribútum csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a>InstanceInputEndpoint
Az `InstanceInputEndpoint` elem egy feldolgozói szerepkör példánybemeneti végpontját írja le. Egy példány bemeneti végpont jaszmer egy adott szerepkörpéldány használatával porttovábbítás a terheléselosztó. Minden példány bemeneti végpontja egy adott porthoz van leképezve a lehetséges portok tartományából. Ez az elem az `AllocatePublicPortFrom` elem szülője.

Az `InstanceInputEndpoint` elem csak az Azure SDK 1.7-es vagy újabb verziójával érhető el.

Az alábbi táblázat az `InstanceInputEndpoint` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A végpont egyedi neve.|
|localPort|int|Kötelező. Megadja azt a belső portot, amelyet az összes szerepkörpéldány figyelni fog a terheléselosztótól továbbított bejövő forgalom fogadásához. A lehetséges értékek 1 és 65535 között mozognak, beleértve a értéket is.|
|Protokoll|sztring|Kötelező. A belső végpont átviteli protokollja. A lehetséges értékek: `udp` és `tcp`. Http/https alapú forgalomhoz használható. `tcp`|

##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a>PublicPortFrom felosztása
Az `AllocatePublicPortFrom` elem leírja a nyilvános port tartomány, amely a külső ügyfelek által használható az egyes példánybemeneti végpontok eléréséhez. A nyilvános (VIP) portszáma ebből a tartományból van lefoglalva, és a bérlői telepítés és frissítés során minden egyes szerepkörpéldány-végponthoz hozzá van rendelve. Ez az elem az `FixedPortRange` elem szülője.

Az `AllocatePublicPortFrom` elem csak az Azure SDK 1.7-es vagy újabb verziójával érhető el.

##  <a name="fixedport"></a><a name="FixedPort"></a>Rögzített port
Az `FixedPort` elem a belső végpont portját adja meg, amely lehetővé teszi a terheléselosztásos kapcsolatokat a végponton.

Az `FixedPort` elem csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.

Az alábbi táblázat az `FixedPort` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|port|int|Kötelező. A belső végpont portja. Ennek ugyanaz a hatása, `FixedPortRange` mint a min és max beállításának beállítása ugyanarra a portra.<br /><br /> A lehetséges értékek 1 és 65535 között mozognak, bezárólag (Az Azure SDK 1.7-es vagy újabb verziója).|

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a>FixedPortRange
Az `FixedPortRange` elem meghatározza a belső végponthoz vagy példánybemeneti végponthoz rendelt portok tartományát, és beállítja a terheléselosztásos kapcsolatokhoz használt portot a végponton.

> [!NOTE]
>  Az `FixedPortRange` elem másképp működik attól függően, hogy melyik elemben található. Ha `FixedPortRange` az elem `InternalEndpoint` az elem, megnyitja az összes portot a terheléselosztó tartományon belül a min és max attribútumok minden virtuális gép, amelyen a szerepkör fut. Ha `FixedPortRange` az elem `InstanceInputEndpoint` az elemben van, csak egy portot nyit meg a min és a max attribútumok tartományán belül minden olyan virtuális gépen, amely a szerepkört futtatja.

Az `FixedPortRange` elem csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.

Az alábbi táblázat az `FixedPortRange` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|p|int|Kötelező. A tartomány minimális portja. A lehetséges értékek 1 és 65535 között mozognak, bezárólag (Az Azure SDK 1.7-es vagy újabb verziója).|
|Max|sztring|Kötelező. A tartomány maximális portja. A lehetséges értékek 1 és 65535 között mozognak, bezárólag (Az Azure SDK 1.7-es vagy újabb verziója).|

##  <a name="certificates"></a><a name="Certificates"></a>Tanúsítványok
Az `Certificates` elem egy feldolgozói szerepkör tanúsítványainak gyűjteményét írja le. Ez az elem az `Certificate` elem szülője. Egy szerepkörhöz tetszőleges számú társított tanúsítvány tartozhat. A tanúsítványelem használatáról a [Szolgáltatásdefiníció-fájl módosítása tanúsítvánnyal](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files)című témakörben talál további információt.

##  <a name="certificate"></a><a name="Certificate"></a>Tanúsítvány
Az `Certificate` elem egy feldolgozói szerepkörhöz társított tanúsítványt ír le.

Az alábbi táblázat az `Certificate` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A tanúsítvány neve, amely https-elemhez `InputEndpoint` való hozzárendeltként hivatkozik rá.|
|storeLocation (üzlet helye)|sztring|Kötelező. Annak a tanúsítványtárolónak a helye, ahol ez a tanúsítvány megtalálható a helyi számítógépen. A lehetséges `CurrentUser` `LocalMachine`értékek a és a.|
|storeName (üzletneve)|sztring|Kötelező. Annak a tanúsítványtárolónak a neve, ahol ez a tanúsítvány a helyi számítógépen található. A lehetséges értékek közé tartoznak `Root` `CA`a `Trust` `Disallowed`beépített `TrustedPeople` `TrustedPublisher` `AuthRoot`üzletnevek `AddressBook` `My`, , , , , , , , , , vagy bármely egyéni üzletnév. Ha meg van adva egyéni üzletnév, a rendszer automatikusan létrehozza az üzletet.|
|permissionLevel|sztring|Választható. Megadja a szerepkör-folyamatokhozzáférési engedélyeit. Ha azt szeretné, hogy csak emelt szintű folyamatok férhessenek hozzá a személyes kulcshoz, adja meg `elevated` az engedélyt. `limitedOrElevated`engedély lehetővé teszi, hogy minden szerepkör-folyamat hozzáférjen a személyes kulcshoz. A lehetséges értékek: `limitedOrElevated` és `elevated`. Az alapértelmezett érték `limitedOrElevated`.|

##  <a name="imports"></a><a name="Imports"></a>Behozatal
Az `Imports` elem egy feldolgozói szerepkör importálási moduljainak gyűjteményét írja le, amely összetevőket ad hozzá a vendég operációs rendszerhez. Ez az elem az `Import` elem szülője. Ez az elem nem kötelező, és egy szerepkör csak egy futásidejű blokk.

Az `Imports` elem csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.

##  <a name="import"></a><a name="Import"></a>Importálása
Az `Import` elem megad egy modult, amelyet hozzá kell adni a vendég operációs rendszerhez.

Az `Import` elem csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.

Az alábbi táblázat az `Import` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|modulNeve|sztring|Kötelező. Az importálandó modul neve. Az érvényes importálási modulok a következők:<br /><br /> - RemoteAccess<br />- RemoteForwarder<br />- Diagnosztika<br /><br /> A RemoteAccess és a RemoteForwarder modulok lehetővé teszik a szerepkörpéldány távoli asztali kapcsolatokhoz való konfigurálását. További információt a [Távoli asztali kapcsolat engedélyezése](cloud-services-role-enable-remote-desktop-new-portal.md)című témakörben talál.<br /><br /> A Diagnosztika modul lehetővé teszi egy szerepkörpéldány diagnosztikai adatainak gyűjtését|

##  <a name="runtime"></a><a name="Runtime"></a>Runtime
Az `Runtime` elem egy feldolgozói szerepkör környezeti változóbeállításainak gyűjteményét ismerteti, amelyek az Azure gazdafolyamat futásidejű környezetét szabályozzák. Ez az elem az `Environment` elem szülője. Ez az elem nem kötelező, és egy szerepkör csak egy futásidejű blokk.

Az `Runtime` elem csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.

Az alábbi táblázat az `Runtime` elem jellemzőit ismerteti:

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|executionContext|sztring|Választható. Megadja azt a környezetet, amelyben a szerepkör-folyamat elindul. Az alapértelmezett `limited`környezet a .<br /><br /> -   `limited`– A folyamat rendszergazdai jogosultságok nélkül indul el.<br />-   `elevated`– A folyamat rendszergazdai jogosultságokkal indul.|

##  <a name="environment"></a><a name="Environment"></a>Környezet
Az `Environment` elem egy feldolgozói szerepkör környezeti változóbeállításainak gyűjteményét írja le. Ez az elem az `Variable` elem szülője. Egy szerepkörhöz tetszőleges számú környezeti változó van beállítva.

##  <a name="variable"></a><a name="Variable"></a>Változó
Az `Variable` elem egy környezeti változót ad meg, amelyet a vendég működik.

Az `Variable` elem csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.

Az alábbi táblázat az `Variable` elem jellemzőit ismerteti:

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|név|sztring|Kötelező. A beállítandó környezeti változó neve.|
|érték|sztring|Választható. A környezeti változóhoz beállítandó érték. Értékattribútumot vagy `RoleInstanceValue` elemet kell megadnia.|

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a>RoleInstanceValue érték
Az `RoleInstanceValue` elem azt az xPath-ot adja meg, amelyből a változó értékét be szeretné olvasni.

Az alábbi táblázat az `RoleInstanceValue` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|Xpath|sztring|Választható. A példány telepítési beállításainak helyelérési útja. További információt a [Konfigurációs változók az XPath segítségével című témakörben talál.](cloud-services-role-config-xpath.md)<br /><br /> Értékattribútumot vagy `RoleInstanceValue` elemet kell megadnia.|

##  <a name="entrypoint"></a><a name="EntryPoint"></a>Belépési pont
Az `EntryPoint` elem egy szerepkör belépési pontját adja meg. Ez az elem az `NetFxEntryPoint` elemek szülője. Ezek az elemek lehetővé teszik, hogy az alapértelmezett WaWorkerHost.exe alkalmazástól eltérő alkalmazást adjon meg, amely szerepkör-belépési pontként működjön.

Az `EntryPoint` elem csak az Azure SDK 1.5-ös vagy újabb verziójával érhető el.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a>NetfxEntryPoint
Az `NetFxEntryPoint` elem határozza meg a szerepkörhöz futtatandó programot.

> [!NOTE]
>  Az `NetFxEntryPoint` elem csak az Azure SDK 1.5-ös vagy újabb verziójával érhető el.

Az alábbi táblázat az `NetFxEntryPoint` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|szerelvényneve|sztring|Kötelező. A belépési pontot tartalmazó szerelvény elérési útja és fájlneve. Az elérési út a ** \\%ROLEROOT%\Approot** mappához képest van (ne `commandLine`adja meg ** \\a %ROLEROOT%\Approot** értéket a mappában, a(z) feltételezi). **A %ROLEROOT%** az Azure által karbantartott környezeti változó, amely a szerepkör gyökérmappájának helyét jelöli. A ** \\%ROLEROOT%\Approot** mappa a szerepkör alkalmazásmappáját jelöli.|
|targetFrameworkVersion|sztring|Kötelező. A .NET keretrendszer azon verziója, amelyre a szerelvény épült. Például: `targetFrameworkVersion="v4.0"`.|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a>ProgramEntryPoint
Az `ProgramEntryPoint` elem határozza meg a szerepkörhöz futtatandó programot. Az `ProgramEntryPoint` elem lehetővé teszi olyan programbelépési pont megadását, amely nem .NET kódösszeállításon alapul.

> [!NOTE]
>  Az `ProgramEntryPoint` elem csak az Azure SDK 1.5-ös vagy újabb verziójával érhető el.

Az alábbi táblázat az `ProgramEntryPoint` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|Commandline|sztring|Kötelező. A végrehajtandó program elérési útja, fájlneve és parancssori argumentumai. Az elérési út a **%ROLEROOT%\Approot** mappához képest van (ne adja meg a **%ROLEROOT%\Approot** elemet a commandLine-ban, a rendszer feltételezi). **A %ROLEROOT%** az Azure által karbantartott környezeti változó, amely a szerepkör gyökérmappájának helyét jelöli. A **%ROLEROOT%\Approot** mappa a szerepkör alkalmazásmappáját jelöli.<br /><br /> Ha a program befejeződik, a szerepkör újrahasznosítható, így általában állítsa be a programot, hogy továbbra is fut, ahelyett, hogy egy program, amely csak elindul, és fut egy véges feladat.|
|setReadyOnProcessStart|logikai|Kötelező. Itt adható meg, hogy a szerepkörpéldány megvárja-e, hogy a parancssori program jelezze az indítást. Ezt az `true` értéket ebben az időpontban kell beállítani. Az érték `false` beállítása későbbi használatra van fenntartva.|

##  <a name="startup"></a><a name="Startup"></a>Indítási
Az `Startup` elem a szerepkör indításakor futó feladatok gyűjteményét írja le. Ez az elem lehet `Variable` az elem szülője. A szerepkör indítási feladatainak használatáról az [indítási feladatok konfigurálása](cloud-services-startup-tasks.md)című témakörben talál további információt. Ez az elem nem kötelező, és egy szerepkörnek csak egy indítási blokkja lehet.

Az alábbi táblázat az `Startup` elem attribútumát ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|Prioritás|int|Csak belső használatra.|

##  <a name="task"></a><a name="Task"></a>Feladat
Az `Task` elem meghatározza a szerepkör indításakor bekövetkező indítási feladatot. Az indítási feladatok olyan feladatok végrehajtására használhatók, amelyek előkészítik a szerepkört az ilyen telepítési szoftverösszetevők vagy más alkalmazások futtatására. A feladatok abban a sorrendben `Startup` hajthatók végre, ahogyan az elemblokkban megjelennek.

Az `Task` elem csak az Azure SDK 1.3-as vagy újabb verziójával érhető el.

Az alábbi táblázat az `Task` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|Commandline|sztring|Kötelező. A futtatandó parancsokat tartalmazó parancsfájl, például cmd-fájl. Az indítási parancsot és a kötegfájlokat ANSI formátumban kell menteni. A bájtsorrend-jelölőt a fájl elején beállító fájlformátumok nem lesznek megfelelően feldolgozva.|
|executionContext|sztring|Megadja azt a környezetet, amelyben a parancsfájl fut.<br /><br /> -   `limited`[Default] – A folyamatot tároló szerepkörrel azonos jogosultságokkal fut.<br />-   `elevated`– Rendszergazdai jogosultságokkal fut.|
|taskType típusú|sztring|A parancs végrehajtási viselkedését adja meg.<br /><br /> -   `simple`[Alapértelmezett] – A rendszer megvárja, amíg a feladat kilép, mielőtt bármilyen más feladatot indítana.<br />-   `background`– A rendszer nem várja meg, amíg a feladat kilép.<br />-   `foreground`– Hasonló a háttérhez, kivéve, hogy a szerepkör nem indul újra, amíg az összes előtér-feladat ki nem lép.|

##  <a name="contents"></a><a name="Contents"></a>Tartalmát
Az `Contents` elem egy feldolgozói szerepkör tartalmának gyűjteményét írja le. Ez az elem az `Content` elem szülője.

Az `Contents` elem csak az Azure SDK 1.5-ös vagy újabb verziójával érhető el.

##  <a name="content"></a><a name="Content"></a>Tartalom
Az `Content` elem határozza meg az Azure virtuális gépre másolandó tartalom forráshelyét és azt a célelérési utat, amelyre a rendszer másolja.

Az `Content` elem csak az Azure SDK 1.5-ös vagy újabb verziójával érhető el.

Az alábbi táblázat az `Content` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|destination|sztring|Kötelező. Hely az Azure virtuális gépen, amelyre a tartalom van elhelyezve. Ez a hely a **%ROLEROOT%\Approot**mappához képest van.|

Ez az elem az `SourceDirectory` elem szülőeleme.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a>Forráskönyvtár
Az `SourceDirectory` elem határozza meg azt a helyi könyvtárat, amelyből a tartalmat másolja. Ezzel az elemmel megadhatja az Azure virtuális gépre másolni kívánt helyi tartalmat.

Az `SourceDirectory` elem csak az Azure SDK 1.5-ös vagy újabb verziójával érhető el.

Az alábbi táblázat az `SourceDirectory` elem attribútumait ismerteti.

| Attribútum | Típus | Leírás |
| --------- | ---- | ----------- |
|path|sztring|Kötelező. Relatív vagy abszolút elérési útja egy helyi könyvtár, amelynek tartalmát másolja az Azure virtuális gépre. A címtár elérési útján a környezeti változók bővítése támogatott.|

## <a name="see-also"></a>Lásd még:
[Felhőszolgáltatás (klasszikus) definícióséma](schema-csdef-file.md)



