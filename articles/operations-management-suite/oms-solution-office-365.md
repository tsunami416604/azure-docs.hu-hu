---
title: Az Office 365 felügyeleti megoldás az Azure-ban |} Microsoft Docs
description: Ez a cikk részletesen konfigurációs és használja az Office 365-megoldás az Azure-ban.  A Naplóelemzési létrehozott Office 365-rekordok részletes leírását tartalmazza.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: bwren
ms.openlocfilehash: 8797e08ad942687b7d2defd765f4fe3f9765812f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="office-365-management-solution-in-azure-preview"></a>Az Office 365 felügyeleti megoldás az Azure (előzetes verzió)

![Az Office 365-embléma](media/oms-solution-office-365/icon.png)

Az Office 365 felügyeleti megoldás az Office 365-környezethez az Naplóelemzési figyelését teszi lehetővé.

- Az Office 365-fiókok elemezheti a használati szokásokat, valamint a viselkedési trendek azonosításához felhasználói tevékenység figyelésének. Például is kibonthat egy konkrét használati forgatókönyvek, például a szervezet vagy a legnépszerűbb SharePoint-webhelyek kívül megosztott fájlokat.
- Rendszergazdai tevékenységek nyomon követéséhez a konfigurációs módosítások vagy magas jogosultsági műveletek figyelését.
- Észleli, és vizsgálja meg a nem kívánt felhasználói viselkedés, amely a szervezet igényeinek testre is szabható.
- Naplózási és megfelelőségi bemutatása. Például fájl hozzáférési műveleteket bizalmas fájlokat, amely segíthet a naplózási és megfelelőségi eljárásait a figyelheti.
- A működési hibaelhárítás [keresések jelentkezzen](../log-analytics/log-analytics-log-search.md) Office 365-tevékenységek adatait a szervezet felett.

## <a name="prerequisites"></a>Előfeltételek
A következő szükség, mielőtt a megoldás telepítve és konfigurálva.

- Szervezeti Office 365-előfizetéssel.
- Egy felhasználói fiókot, amely globális rendszergazda hitelesítő adatait.
- Naplózási adatok fogadására kell [naplózásának konfigurálása](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) az Office 365-előfizetésben.  Vegye figyelembe, hogy [postaláda-naplózás](https://technet.microsoft.com/library/dn879651.aspx) külön kell beállítani.  Továbbra is telepítheti a megoldás, és be más adatokat, ha a naplózás nincs konfigurálva.
 


## <a name="management-packs"></a>Felügyeleti csomagok
Ez a megoldás a csatlakoztatott felügyeleti csoportok nem telepíti a felügyeleti csomagok.
  

## <a name="configuration"></a>Konfiguráció
Egyszer, [az Office 365-megoldás az előfizetéshez hozzáadott](../log-analytics/log-analytics-add-solutions.md), csatlakozni az Office 365-előfizetéssel rendelkezik.

1. A Riasztáskezelési megoldás hozzáadni a Naplóelemzési munkaterület ismertetett eljárással [megoldások hozzáadása](../log-analytics/log-analytics-add-solutions.md).
2. Ugrás a **beállítások** az OMS-portálon.
3. A **csatlakoztatott források**, jelölje be **Office 365**.
4. Kattintson a **csatlakozni az Office 365**.<br>![Kapcsolat az Office 365](media/oms-solution-office-365/configure.png)
5. Jelentkezzen be Office 365 az előfizetéshez tartozó globális rendszergazdai fiókkal. 
6. Az előfizetés, amely figyeli a megoldás munkaterheléseivel jelennek meg.<br>![Kapcsolat az Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>Adatgyűjtés
### <a name="supported-agents"></a>Támogatott ügynökök
Az Office 365-megoldás nem adatainak lekérése bármelyikét a [OMS-ügynököt](../log-analytics/log-analytics-data-sources.md).  Office 365-ről, lekéri az adatokat.

### <a name="collection-frequency"></a>A gyűjtés gyakorisága
Az Office 365 küld egy [webhook értesítési](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) szükséges részletes adatok Naplóelemzési minden alkalommal létrejön egy bejegyzés.

## <a name="using-the-solution"></a>A megoldás használata
Ha az Office 365-megoldás hozzáadni a Naplóelemzési munkaterület a **Office 365** csempe nem kerülnek be az irányítópulton. Ez a csempe a környezetben jelenleg elérhető számítógépek számát és grafikus ábrázolását jeleníti meg, valamint a frissítési megfelelőségi állapotukat.<br><br>
![Az Office 365 összefoglalás Csempéje](media/oms-solution-office-365/tile.png)  

Kattintson a **Office 365** csempére kattintva nyissa meg a **Office 365** irányítópult.

![Az Office 365 irányítópult](media/oms-solution-office-365/dashboard.png)  

Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Minden oszlop felső tíz riasztások száma a megadott hatókör és időtartomány adott oszlop feltételeknek megfelelő sorolja fel. A napló-keresés, amely teljes listáját jeleníti meg, tekintse meg az összes oszlop alján kattintva vagy az oszlop fejlécére kattintva futtathatja.

| Oszlop | Leírás |
|:--|:--|
| Műveletek | Az összes figyelt Office 365-előfizetés az aktív felhasználók információkat biztosít. Fordulhat elő, időbeli tevékenységek számát is lesz meg.
| Exchange | Az Exchange Server tevékenységek, például az Add-postaláda engedély, vagy a Set-postaláda részletes információkat tartalmazza. |
| SharePoint | A felső tevékenységek jeleníti meg, hogy a felhasználók végrehajtani a SharePoint-dokumentumok. Ha ez a csempe a Lehatolás a lapon ezeket a tevékenységeket, például a céldokumentumban és helyét, ezt a tevékenységet részleteit jeleníti meg. Például a fájl elérhető esemény, akkor fog tudni tekintse meg a dokumentumot, is hozzáférnek, hozzárendelt fiók nevét és IP-címet. |
| Azure Active Directory | Felső felhasználói tevékenységek, például a felhasználói jelszó alaphelyzetbe állítása és a bejelentkezési kísérletek tartalmazza. Ha le, lesz, ezek a tevékenységek, például az eredmény állapot részleteinek megtekintéséhez. Főleg bizonyulhat hasznosnak, ha azt szeretné, hogy az Azure Active Directory gyanús tevékenységek figyelésére. |




## <a name="log-analytics-records"></a>Log Analytics-rekordok

A Naplóelemzési munkaterület az Office 365-megoldás által létrehozott összes rekord rendelkezik egy **típus** a **OfficeActivity**.  A **OfficeWorkload** tulajdonság határozza meg, melyik Office 365 szolgáltatás a rekord hivatkozik - Exchange, az AzureActiveDirectory, a SharePoint vagy a onedrive-on.  A **RecordType** tulajdonság határozza meg a műveletet.  A tulajdonságok az egyes művelet függ, és az alábbi táblázatban láthatók.

### <a name="common-properties"></a>Általános tulajdonságai
A következő tulajdonságok megegyeznek az összes Office 365 bejegyzésre.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus | *OfficeActivity* |
| ClientIP | Az eszköz, amikor a tevékenység naplózott használt IP-címét. Az IP-cím IPv4 vagy IPv6 cím formátumban jelenik meg. |
| OfficeWorkload | Az Office 365 szolgáltatás, amely a rekord hivatkozik.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Művelet | A felhasználói vagy rendszergazdai tevékenység nevét.  |
| A szervezeti | A szervezet Office 365-bérlő GUID azonosítója. Ez az érték mindig lesz ugyanaz a szervezet az Office 365 szolgáltatást, amely függetlenül. |
| recordType | A végrehajtott művelet típusa. |
| ResultStatus | Azt jelzi, hogy (a művelet tulajdonságban megadott) a művelet sikeres volt-e vagy sem. Lehetséges értékek: sikeres, PartiallySucceded vagy sikertelen. Az Exchange felügyeleti tevékenység, értéke pedig IGAZ vagy hamis. |
| UserId | Az UPN (egyszerű felhasználónév) a felhasználó hajtotta végre a műveletet, amelyek a rekord, a naplózott; például my_name@my_domain_name. Vegye figyelembe, hogy rendszer fiókok (például SHAREPOINT\system vagy NTAUTHORITY\SYSTEM) által végzett tevékenység rekordok is szerepelnek. | 
| UserKey | A UserId tulajdonság a megadott felhasználó alternatív azonosító.  Például ezt a tulajdonságot a passport egyedi azonosítója (PUID) és az Exchange a SharePoint, a onedrive vállalati verzió a felhasználók által végrehajtott események telepítéskor. Ez a tulajdonság is meghatározza, ugyanazt az értéket a UserID tulajdonság a szolgáltatások és rendszerfiókok által végzett események előforduló események|
| UserType | A felhasználó által végrehajtott a művelet típusát.<br><br>Adminisztratív körzet<br>Alkalmazás<br>DcAdmin<br>Rendszeres<br>Foglalt<br>Szolgáltatásnév<br>Rendszer |


### <a name="azure-active-directory-base"></a>Az Azure Active Directory-alap
A következő tulajdonságok megegyeznek az összes Azure Active Directory bejegyzésre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| recordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Az Azure AD-esemény típusa. |
| Az ExtendedProperties | A kiterjesztett tulajdonságok az Azure AD-esemény. |


### <a name="azure-active-directory-account-logon"></a>Az Azure Active Directory-fiók bejelentkezési
Ezeket a rekordokat hoz létre egy Active Directory-felhasználó megpróbál bejelentkezni.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| recordType     | AzureActiveDirectoryAccountLogon |
| Alkalmazás | Az alkalmazás, amely elindítja a fiók bejelentkezési esemény, például az Office 15. |
| Ügyfél | Az ügyfél adatait eszközt, az eszköz operációs rendszere és a használt eszköz böngészőjében a fiók bejelentkezési esemény. |
| LoginStatus | Ez a tulajdonság közvetlenül OrgIdLogon.LoginStatus származik. Különböző érdekes bejelentkezési hibák leképezése által algoritmusok riasztási megteheti. |
| UserDomain | A bérlői azonosító adatok (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory-objektumok végrehajtott módosítás vagy kiegészítés érkezett ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| recordType     | AzureActiveDirectory |
| AADTarget | A felhasználó, aki a (a művelet tulajdonság által azonosított) műveletet hajtott végre. |
| Aktor | A felhasználó vagy szolgáltatás egyszerű, amely hajtotta végre a műveletet. |
| ActorContextId | A GUID Azonosítót a szervezet, amely a szereplő tartozik. |
| ActorIpAddress | A szereplő IP-cím IPV4 vagy IPv6-cím formátuma. |
| InterSystemsId | A GUID, amelyek nyomon követik a műveletek az Office 365 szolgáltatáson belül lévő összetevői között. |
| IntraSystemId |   Az Azure Active Directory nyomon követéséhez a művelet által létrehozott GUID. |
| SupportTicketId | Az ügyfélszolgálati Jegyazonosító "művelet" helyzetekben a művelethez. |
| TargetContextId | A GUID azonosítója, amely a célként megadott felhasználó tagja a szervezet. |


### <a name="data-center-security"></a>Adatközpont-biztonsági
Ezeket a rekordokat Data Center biztonsági naplózási adatokból jönnek létre.  

| Tulajdonság | Leírás |
|:--- |:--- |
| EffectiveOrganization | A bérlő a jogosultságszint-emelés parancsmagot célzott a neve. |
| ElevationApprovedTime | Ha a jogosultsági szint emeléséhez jóváhagyva időbélyegzőjét. |
| ElevationApprover | A Microsoft-kezelő neve. |
| ElevationDuration | Az időtartam, amely a jogosultsági szint emeléséhez aktív volt. |
| ElevationRequestId |  A jogosultságszint-emelési kérelem egyedi azonosítója. |
| ElevationRole | A szerepkör a jogosultságszint-emelés szükséges. |
| ElevationTime | A jogosultsági szint emeléséhez kezdési idejét. |
| Start_Time | A kezdési időt a parancsmagok végrehajtása. |


### <a name="exchange-admin"></a>Exchange-rendszergazda
Ezeket a rekordokat hoz létre módosításai az Exchange konfigurálása.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeAdmin |
| ExternalAccess |  Meghatározza, hogy a parancsmag futtatása egy felhasználó a szervezete a Microsoft datacenter személyzet vagy datacenter szolgáltatásfiók, vagy a meghatalmazott rendszergazda. A hamis értéket jelzi, hogy a parancsmag futott-e valaki a szervezetében. Az IGAZ értéket jelzi, hogy a parancsmag datacenter csoporthoz, datacenter szolgáltatásfiók vagy a meghatalmazott rendszergazda futtatták. |
| ModifiedObjectResolvedName |  Ez az az objektum, amelyet a parancsmag által felhasználóbarát nevét. Ez a rendszer naplózza csak akkor, ha a parancsmag módosítja az objektum. |
| Szervezetnév | A bérlő neve. |
| OriginatingServer | A kiszolgáló, amelyből végre lett hajtva a parancsmag neve. |
| Paraméterek | A név- és az összes paramétert, a parancsmag Operations tulajdonságában azonosított használt. |


### <a name="exchange-mailbox"></a>Exchange postaláda
Exchange-postaládák végrehajtott módosításait és kiegészítéseit ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeItem |
| ClientInfoString | Az e-mail ügyfélprogramokban, például egy böngésző verziója, az Outlook verzióját és a mobileszköz-információk a művelet végrehajtásához használt információk. |
| Client_IPAddress | Az eszközt, hogy a művelet naplózásakor használt IP-címét. Az IP-cím IPv4 vagy IPv6 cím formátumban jelenik meg. |
| ClientMachineName | A számítógép neve, amelyen az Outlook ügyfélnek. |
| ClientProcessName | Az e-mail ügyfélprogramban a postaládájuk eléréséhez használt. |
| ClientVersion | Az e-mailek ügyfél verzióját. |
| InternalLogonType | Belső használatra fenntartva. |
| Logon_Type | A postaláda érhető el, és a naplózott műveletet végre felhasználó típusát jelöli. |
| LogonUserDisplayName |    A műveletet végző felhasználó felhasználóbarát neve. |
| LogonUserSid | A művelet elvégző felhasználó SID. |
| MailboxGuid | Az Exchange GUID azonosítója, amelynek a postaládát. |
| MailboxOwnerMasterAccountSid | Postaláda tulajdonos fiók fő fiók SID azonosítója. |
| MailboxOwnerSid | A postaláda-tulajdonos biztonsági azonosítója. |
| MailboxOwnerUPN | A postaláda, amelynek tulajdonosának e-mail címe. |


### <a name="exchange-mailbox-audit"></a>Exchange postaláda naplózása
Ezeket a rekordokat hoz létre egy postaláda naplóbejegyzés jön létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| recordType     | ExchangeItem |
| Elem | Az elem, amelyre a művelet végrehajtása jelenti. | 
| SendAsUserMailboxGuid | Az Exchange GUID azonosítója a postaládát, amelynek elküldeni e-mailekhez. |
| SendAsUserSmtp | SMTP-cím megszemélyesített felhasználó. |
| SendonBehalfOfUserMailboxGuid | Az Exchange GUID nevében üzenetküldésre használt postafiók. |
| SendOnBehalfOfUserSmtp | A felhasználó, akinek a nevében az e-mailt küld az SMTP-cím. |


### <a name="exchange-mailbox-audit-group"></a>Exchange postaláda naplózási csoport
Exchange-csoportok végrehajtott módosításait és kiegészítéseit ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | A csoport összes elemére vonatkozó adatokat. |
| CrossMailboxOperations | Azt jelzi, ha a művelet csak egy postaláda vesz-e. |
| DestMailboxId | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Megadja a cél postaláda GUID. |
| DestMailboxOwnerMasterAccountSid | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Meghatározza a fő fiók SID azonosítója a target postaláda tulajdonosának a SID-AZONOSÍTÓT. |
| DestMailboxOwnerSid | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Megadja a cél postaláda biztonsági azonosítója. |
| DestMailboxOwnerUPN | Állítsa be, csak akkor, ha a CrossMailboxOperations paraméter értéke True. Megadja a cél postaláda tulajdonosának az egyszerű Felhasználónevet. |
| DestFolder | A célmappa, például az áthelyezési műveletek. |
| Mappa | A tároló mappát cikkek csoportja. |
| Mappák |     A forrás; egy műveletben érintett mappák kapcsolatos információk Ha például mappák van kiválasztva, és a törölt. |


### <a name="sharepoint-base"></a>SharePoint-alap
Ezek a Tulajdonságok megegyeznek a minden SharePoint-rekordot.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Meghatározza, hogy az esemény történt-e a SharePoint. Lehetséges értékek a következők: a SharePoint vagy ObjectModel. |
| ItemType | A típusú objektum, amely nem érhető el, vagy módosították. Részletekért tekintse meg az ItemType táblázatot objektumok típusával. |
| MachineDomainInfo | Eszköz szinkronizálási műveleteire vonatkozó adatokat. Ezek az információk csak akkor, ha a kérelem szerepel az elvártnak. |
| MachineId |   Eszköz szinkronizálási műveleteire vonatkozó adatokat. Ezek az információk csak akkor, ha a kérelem szerepel az elvártnak. |
| Site_ | A GUID azonosítója a hely, ahol a fájl vagy mappa a felhasználók férjenek hozzá. |
| Source_Name | A rendszervizsgálati módban levő műveletet kiváltó entitás. Lehetséges értékek a következők: a SharePoint vagy ObjectModel. |
| Felhasználói ügynök | A felhasználó ügyfél vagy a böngésző információt. Ez az információ az ügyfél vagy a böngésző által biztosított. |


### <a name="sharepoint-schema"></a>SharePoint-séma
Konfiguráció módosításai SharePoint ezeket a rekordokat hoz létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Egyéni események választható karakterláncot. |
| Event_Data |  Egyéni események választható hasznos. |
| ModifiedProperties | A tulajdonság része a rendszergazda eseményeket, mint például a felhasználó hozzáadása egy helyhez vagy egy hely gyűjtési felügyeleti csoport tagjaként. A tulajdonság módosításának (például a webhely-felügyeleti csoport), az új érték a módosított tulajdonság (például a felhasználó hozzá lett adva, a webhely rendszergazdájától), és a módosított objektum az előző érték a tulajdonság nevét tartalmazza. |


### <a name="sharepoint-file-operations"></a>SharePoint fájlműveletek
Ezeket a rekordokat a SharePoint fájlműveletek válaszul jönnek létre.

| Tulajdonság | Leírás |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A fájl kiterjesztése egy fájlt másol vagy áthelyezték. Ez a tulajdonság csak FileCopied és FileMoved esemény jelenik meg. |
| DestinationFileName | A fájl másolása vagy áthelyezett neve. Ez a tulajdonság csak FileCopied és FileMoved esemény jelenik meg. |
| DestinationRelativeUrl | A célmappa, ahol egy fájlt másolja vagy áthelyezték URL-CÍMÉT. A SiteURL DestinationRelativeURL és DestinationFileName paraméter értékének kombinációja megegyezik a ObjectID tulajdonság, amely a másolt fájl teljes elérési útja neve értékét. Ez a tulajdonság csak FileCopied és FileMoved esemény jelenik meg. |
| SharingType | A megosztási engedélyek a felhasználót, hogy az erőforrás lett megosztva rendelt típusa. Ez a felhasználó a UserSharedWith paraméter azonosítja. |
| Site_Url | A hely, ahol a fájl vagy mappa a felhasználók férjenek hozzá URL-CÍMÉT. |
| SourceFileExtension | A fájl kiterjesztése a fájlt, amely a felhasználó hozzáfért. A tulajdonság üres, ha az objektum, amelynek egy mappát. |
| SourceFileName |  A fájl vagy mappa a felhasználók férjenek hozzá a neve. |
| SourceRelativeUrl | A mappa, amelyben a felhasználók férjenek hozzá a fájl URL-CÍMÉT. A SiteURL SourceRelativeURL és SourceFileName paraméterek érték megegyezik a ObjectID tulajdonság, amely a fájl, a felhasználók férjenek hozzá a teljes elérési út értéke. |
| UserSharedWith |  A felhasználó a megosztott erőforrás. |




## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által összegyűjtött frissítési rekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

| Lekérdezés | Leírás |
| --- | --- |
|Az Office 365-előfizetés az összes műveletek száma |Típus = OfficeActivity &#124; művelet count() mérésére |
|SharePoint-webhelyek használata|Típus = OfficeActivity OfficeWorkload = sharepoint &#124; count() mértékcsoport által SiteUrl darabszámként &#124; száma asc rendezése|
|Hozzáférés fájlműveletek felhasználói típus szerint|Típus = OfficeActivity OfficeWorkload sharepoint művelet = = FileAccessed &#124; által UserType count() mérésére|
|Egy adott kulcsszóval keresése|Típus OfficeActivity OfficeWorkload = "MyTest" azureactivedirectory =|
|A figyelő külső műveletek Exchange|Típus = OfficeActivity OfficeWorkload = exchange ExternalAccess = true|



## <a name="troubleshooting"></a>Hibaelhárítás

Ha az Office 365-megoldás nem gyűjt adatokat várt módon, ellenőrizze az állapotát, az OMS-portállal **beállítások** -> **csatlakoztatott források** -> **Office 365**. A következő táblázat ismerteti az egyes állapotokhoz.

| status | Leírás |
|:--|:--|
| Aktív | Az Office 365-előfizetés nem aktív, és a munkaterhelés sikeresen csatlakoztatva van a Naplóelemzési munkaterület. |
| Függőben | Az Office 365-előfizetés nem aktív, de a munkaterhelés van még nincs hozzákapcsolva a Naplóelemzési munkaterület sikeresen megtörtént. Csatlakozás az Office 365-előfizetéssel, először a munkaterhelések lesz ez az állapot addig, amíg sikeresen csatlakoznak. Várjon 24 órában aktív Váltás a munkaterhelések. |
| Inaktív | Az Office 365-előfizetéssel inaktív állapotban van. Ellenőrizze az Office 365 felügyeleti a lapot. Az Office 365-előfizetés aktiválása után szüntesse meg a Naplóelemzési munkaterületet, és ismét indítsa el az adatok fogadása a hivatkozás. |



## <a name="next-steps"></a>További lépések
* A részletes frissítési adatokat a [Log Analytics](../log-analytics/log-analytics-log-searches.md) Naplókeresés funkciójával is megtekintheti.
* [Hozzon létre egy saját irányítópultok](../log-analytics/log-analytics-dashboards.md) kedvenc Office 365 keresési lekérdezések megjelenítéséhez.
* [Hozzon létre a riasztások](../log-analytics/log-analytics-alerts.md) proaktív értesíti a fontos Office 365-tevékenységek.  
