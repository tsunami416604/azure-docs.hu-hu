<properties
   pageTitle="Alkalmazások közzététele egyéni felhasználók számára egy Azure RemoteApp-gyűjteményben (előzetes) | Microsoft Azure"
   description="Ismerje meg, hogyan tehet közzé alkalmazásokat egyéni felhasználók számára a csoportok használata helyett az Azure RemoteAppban."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="piotrci"/>

# Alkalmazások közzététele egyéni felhasználók számára egy Azure RemoteApp-gyűjteményben (előzetes)

> [AZURE.IMPORTANT]
> Azure RemoteApp hamarosan megszűnik. A részletekért olvassa el a [bejelentést](https://go.microsoft.com/fwlink/?linkid=821148).

Ez a cikk ismerteti, hogyan tehet közzé alkalmazásokat egyéni felhasználók számára egy Azure RemoteApp-gyűjteményben. Ez az Azure RemoteApp új funkciója, amely jelenleg „privát előzetes verzióként”, csak egyes korai belépők számára érhető el értékelési célokra.

Az Azure RemoteAppban eredetileg csak egy módja volt az alkalmazások „közzétételének”: a rendszergazda alkalmazásokat tehetett közzé a rendszerképről, amelyek a gyűjteményben lévő összes felhasználó számára láthatók voltak.

Egy általános forgatókönyv számos alkalmazás egyetlen rendszerképbe való belefoglalása, és egy gyűjtemény üzembe helyezése a felügyeleti költségek csökkentése érdekében. Gyakran nem minden felhasználónak van szüksége minden alkalmazásra, így a rendszergazdák inkább az egyéni felhasználók számára szeretnének alkalmazásokat közzétenni, hogy ne lássák a szükségtelen alkalmazásokat az alkalmazásfolyamukban.

Ez mostantól lehetséges az Azure RemoteAppban – jelenleg korlátozott előzetes verzióként. Az új funkció rövid összefoglalása:

1. Egy gyűjtemény két módba állítható be:
 
  - az eredeti „gyűjtemény módba”, amelyben egy gyűjtemény minden felhasználója láthatja az összes közzétett alkalmazást. Ez az alapértelmezett mód.
  - az új „alkalmazás módba”, amelyben a felhasználók csak a kifejezetten a hozzájuk rendelt alkalmazásokat láthatják

2. Az alkalmazás mód jelenleg csak Azure RemoteApp PowerShell-parancsmagok használatával engedélyezhető.

  - Amikor alkalmazás módba állítja be a gyűjteményt, a gyűjtemény felhasználó-hozzárendelése nem kezelhető az Azure Portalon keresztül. A felhasználó-hozzárendelés kezelését PowerShell-parancsmagokon keresztül kell elvégezni.

3. A felhasználók ekkor csak a közvetlenül a számukra közzétett alkalmazásokat láthatják. Lehetséges azonban, hogy a felhasználók továbbra is elindíthatják a rendszerképen lévő többi alkalmazást, ha közvetlenül az operációs rendszerben férnek hozzájuk.
  - Ez a szolgáltatás nem biztosítja az alkalmazások biztonságos zárolását; csak a láthatóságot korlátozza az alkalmazásfolyamban.
  - Ha el kell különítenie felhasználókat egyes alkalmazásoktól, különálló gyűjteményeket kell használnia.

## Az Azure RemoteApp PowerShell-parancsmagok beszerzése

Az előzetes verzió új funkcióinak kipróbálásához Azure PowerShell-parancsmagokat kell használnia. Az Azure felügyeleti portáljával jelenleg nincs lehetőség az új alkalmazás-közzétételi mód engedélyezésére.

Először győződjön meg róla, hogy telepítve van az [Azure PowerShell modul](../powershell-install-configure.md).

Ezután indítsa el a PowerShell-konzolt rendszergazdai módban, és futtassa a következő parancsmagot:

        Add-AzureAccount

A rendszer felkéri az Azure felhasználói nevének jelszavának megadására. Miután bejelentkezett, futtathatja az Azure RemoteApp-parancsmagokat az Azure-előfizetésén.

## Egy gyűjtemény üzemmódjának ellenőrzése

Futtassa a következő parancsmagot:

        Get-AzureRemoteAppCollection <collectionName>

![Ellenőrizze a gyűjtemény használati módját](./media/remoteapp-perapp/araacllelvel.png)

Az AclLevel tulajdonság a következő értékeket veheti fel:

- Collection: az eredeti közzétételi mód. Az összes felhasználó láthat minden közzétett alkalmazást.
- Alkalmazás: az új közzétételi mód. A felhasználók csak a közvetlenül a számukra közzétett alkalmazásokat látják.

## Váltás az alkalmazások közzétételi módja között

Futtassa a következő parancsmagot:

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

A rendszer megőrzi az alkalmazások közzétételének állapotát: kezdetben az összes felhasználó látni fog minden eredetileg közzétett alkalmazást.

## Azon felhasználók listázása, akik láthatnak egy adott alkalmazást

Futtassa a következő parancsmagot:

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

A parancs megjeleníti az összes felhasználót, aki láthatja az alkalmazást.

Megjegyzés: Az alkalmazások aliasait (a fenti szintaxisban „app alias”) a Get-AzureRemoteAppProgram -CollectionName <collectionName> parancsmag futtatásával jelenítheti meg.

## Alkalmazás hozzárendelése egy felhasználóhoz

Futtassa a következő parancsmagot:

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

A felhasználó ekkor látni fogja az alkalmazást az Azure RemoteApp-ügyfélen, és csatlakozni tud hozzá.

## Alkalmazások eltávolítása egy felhasználótól

Futtassa a következő parancsmagot:

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## Visszajelzés küldése
Nagyra értékeljük visszajelzését és javaslatait az előzetes verzióként elérhető szolgáltatással kapcsolatban. Kérjük, töltse ki a [felmérést](http://www.instant.ly/s/FDdrb), és ossza meg velünk a véleményét.

## Nem volt lehetősége kipróbálni az előzetes verzióként elérhető szolgáltatást?
Ha még nem használta az előzetes verziót, töltse ki ezt a [felmérést](http://www.instant.ly/s/AY83p) a hozzáférés kéréséhez.



<!--HONumber=sep16_HO1-->


