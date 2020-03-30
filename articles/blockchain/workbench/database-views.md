---
title: Az Azure Blockchain Workbench adatbázisnézetei
description: Az azure blockchain workbench-i sql-adatbázisnézetek áttekintése.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 585084b4c85c48533bdad96d4f99813ef2e418b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325986"
---
# <a name="azure-blockchain-workbench-database-views"></a>Az Azure Blockchain Workbench adatbázisnézetei

Az Azure Blockchain Workbench Preview az elosztott főkönyvekből származó adatokat egy láncon kívüli SQL *DB-adatbázisba* továbbítja. Az off-chain adatbázis lehetővé teszi az SQL és a meglévő eszközök, például [az SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)használatát a blokklánc-adatokkal való interakcióhoz.

Az Azure Blockchain Workbench adatbázisnézeteket biztosít, amelyek hozzáférést biztosítanak az adatokhoz, amelyek hasznosak lesznek a lekérdezések végrehajtásakor. Ezek a nézetek erősen denormalizált, hogy könnyen gyorsan elkezd épület jelentések, elemzések, és egyébként fogyasztanak blockchain adatok meglévő eszközökkel, és anélkül, hogy áttanítása adatbázis személyzet.

Ez a szakasz áttekintést nyújt az adatbázisnézetekről és a benne lévő adatokról.

> [!NOTE]
> Az adatbázistáblák ezeken a nézeteken kívül található közvetlen használata, bár lehetséges, nem támogatott.
>

## <a name="vwapplication"></a>vwAlkalmazás

Ez a nézet az Azure Blockchain Workbench-be feltöltött **alkalmazások** részleteit tartalmazza.

| Név                             | Típus          | Lehet Null | Leírás                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                  | nvarchar (50)  | Nem          | Az alkalmazás neve |
| Alkalmazásleírása           | nvarchar(255) | Igen         | A kérelem leírása |
| Alkalmazásdisplayneve           | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő név |
| Alkalmazás engedélyezve               | bit           | Nem          | Azonosítja, hogy az alkalmazás jelenleg engedélyezve van-e<br /> **Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltva is megjelenhet az adatbázisban, a kapcsolódó szerződések a blokkláncon maradnak, és az ilyen szerződésekre vonatkozó adatok az adatbázisban maradnak. |
| FeltöltöttDtTm                     | datetime2(7)  | Nem          | A szerződés feltöltésének dátuma és időpontja |
| UploadedByUserId                 | int           | Nem          | Az alkalmazást feltöltő felhasználó azonosítója |
| UploadedByUserExternalId         | nvarchar(255) | Nem          | Az alkalmazást feltöltő felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a felhasználó az Azure Active Directory a konzorcium.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Nem          | A kiépítési folyamat aktuális állapotát határozza meg a felhasználó számára. Lehetséges értékek: <br />0 – A felhasználót az API hozta létre<br />1 – Egy kulcs társítva van a felhasználóval az adatbázisban<br />2 – A felhasználó teljesen ki van építve                         |
| UploadedByUserFirstName          | nvarchar (50)  | Igen         | A szerződést feltöltő felhasználó keresztneve |
| UploadedByUserLastName           | nvarchar (50)  | Igen         | A szerződést feltöltő felhasználó vezetékneve |
| UploadedByUserEmailAddress       | nvarchar(255) | Igen         | A szerződést feltöltő felhasználó e-mail címe |

## <a name="vwapplicationrole"></a>vwApplicationRole

Ez a nézet az Azure Blockchain Workbench-alkalmazásokban definiált szerepkörök részleteit tartalmazza.

Egy *eszközátadási* alkalmazásban például olyan szerepkörök definiálhatók, mint *például a Vevő* és *az Eladó* szerepkör.

| Név                   | Típus             | Lehet Null | Leírás                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Nem          | Az alkalmazás egyedi azonosítója           |
| ApplicationName        | nvarchar (50)     | Nem          | Az alkalmazás neve                       |
| Alkalmazásleírása | nvarchar(255)    | Igen         | A kérelem leírása                  |
| Alkalmazásdisplayneve | nvarchar(255)    | Nem          | A felhasználói felületen megjelenítendő név      |
| Szerepazonosító                 | int              | Nem          | Az alkalmazás szerepkörének egyedi azonosítója |
| RoleName               | nvarchar50)      | Nem          | A szerepkör neve                              |
| Szerepkörleírása        | leírás (255) | Igen         | A szerepkör leírása                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Ez a nézet az Azure Blockchain Workbench-alkalmazásokban definiált szerepkörök és a hozzájuk társított felhasználók részleteit tartalmazza.

Egy *eszközátadási* alkalmazásban például *Kovács János* társítható a *Vevő* szerepkörrel.

| Név                       | Típus          | Lehet Null | Leírás                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Nem          | Az alkalmazás egyedi azonosítója                                                                                                                                                                                               |
| ApplicationName            | nvarchar (50)  | Nem          | Az alkalmazás neve                                                                                                                                                                                                           |
| Alkalmazásleírása     | nvarchar(255) | Igen         | A kérelem leírása                                                                                                                                                                                                      |
| Alkalmazásdisplayneve     | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő név                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Nem          | Az alkalmazás szerepkörének egyedi azonosítója                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Nem          | A szerepkör neve                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Igen         | A szerepkör leírása                                                                                                                                                                                                             |
| UserId (Felhasználóazonosító)                     | int           | Nem          | A szerepkörhöz társított felhasználó azonosítója |
| UserExternalId azonosító             | nvarchar(255) | Nem          | A szerepkörhöz társított felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a felhasználó az Azure Active Directory a konzorcium.                                                                     |
| UserProvisioningStatus     | int           | Nem          | A kiépítési folyamat aktuális állapotát határozza meg a felhasználó számára. Lehetséges értékek: <br />0 – A felhasználót az API hozta létre<br />1 – Egy kulcs társítva van a felhasználóval az adatbázisban<br />2 – A felhasználó teljesen ki van építve |
| UserFirstName (Felhasználóvezeték neve)              | nvarchar (50)  | Igen         | A szerepkörhöz társított felhasználó utóneve |
| UserLastName (Felhasználóvezeték neve)               | nvarchar(255) | Igen         | A szerepkörhöz társított felhasználó vezetékneve |
| UserEmailAddress cím           | nvarchar(255) | Igen         | A szerepkörhöz társított felhasználó e-mail címe |

## <a name="vwconnectionuser"></a>vwConnectionUser

Ez a nézet az Azure Blockchain Workbenchben definiált kapcsolatok és a hozzájuk társított felhasználók részleteit tartalmazza. Ez a nézet minden kapcsolathoz a következő adatokat tartalmazza:

-   Kapcsolódó főkönyvi részletek
-   Társított felhasználói adatok

| Név                     | Típus          | Lehet Null | Leírás                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId azonosító             | int           | Nem          | Egy kapcsolat egyedi azonosítója az Azure Blockchain Workbench szolgáltatásban |
| ConnectionEndpointUrl    | nvarchar (50)  | Nem          | A kapcsolat végpontjának URL-címe |
| ConnectionFundingAccount (Kapcsolatfinanszírozási fiók) | nvarchar(255) | Igen         | A kapcsolathoz társított finanszírozási számla, ha van ilyen |
| LedgerID                 | int           | Nem          | A főkönyv egyedi azonosítója |
| Főkönyv neve               | nvarchar (50)  | Nem          | A főkönyv neve |
| Főkönyvi bizonylatneve        | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő főkönyv neve |
| UserId (Felhasználóazonosító)                   | int           | Nem          | A kapcsolathoz társított felhasználó azonosítója |
| UserExternalId azonosító           | nvarchar(255) | Nem          | A kapcsolathoz társított felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a felhasználó az Azure Active Directory a konzorcium. |
| UserProvisioningStatus   | int           | Nem          |A kiépítési folyamat aktuális állapotát határozza meg a felhasználó számára. Lehetséges értékek: <br />0 – A felhasználót az API hozta létre<br />1 – Egy kulcs társítva van a felhasználóval az adatbázisban<br />2 – A felhasználó teljesen ki van építve |
| UserFirstName (Felhasználóvezeték neve)            | nvarchar (50)  | Igen         | A kapcsolathoz társított felhasználó utóneve |
| UserLastName (Felhasználóvezeték neve)             | nvarchar(255) | Igen         | A kapcsolathoz társított felhasználó vezetékneve |
| UserEmailAddress cím         | nvarchar(255) | Igen         | A kapcsolathoz társított felhasználó e-mail címe |

## <a name="vwcontract"></a>vwSzerződés

Ez a nézet az üzembe helyezett szerződések részleteit tartalmazza. Ez a nézet minden szerződéshez a következő adatokat tartalmazza:

-   Társított alkalmazásdefiníció
-   Társított munkafolyamat-definíció
-   Társított főkönyvi megvalósítás a függvényhez
-   A műveletet kezdeményező felhasználó adatai
-   A blokklánc blokkjával és tranzakciójával kapcsolatos részletek

| Név                                     | Típus           | Lehet Null | Leírás                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId azonosító                             | int            | Nem          | Egy kapcsolat egyedi azonosítója az Azure Blockchain Workbench szolgáltatásban.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar (50)   | Nem          | A kapcsolat végpontjának URL-címe |
| ConnectionFundingAccount (Kapcsolatfinanszírozási fiók)                 | nvarchar(255)  | Igen         | A kapcsolathoz társított finanszírozási számla, ha van ilyen |
| LedgerID                                 | int            | Nem          | A főkönyv egyedi azonosítója |
| Főkönyv neve                               | nvarchar (50)   | Nem          | A főkönyv neve |
| Főkönyvi bizonylatneve                        | nvarchar(255)  | Nem          | A felhasználói felületen megjelenítendő főkönyv neve |
| ApplicationId                            | int            | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                          | nvarchar (50) Az  | Nem          | Az alkalmazás neve |
| Alkalmazásdisplayneve                   | nvarchar (255) Az | Nem          | A felhasználói felületen megjelenítendő név |
| Alkalmazás engedélyezve                       | bit            | Nem          | Azt határozza meg, hogy az alkalmazás jelenleg engedélyezve van-e.<br /> **Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltva is megjelenhet az adatbázisban, a kapcsolódó szerződések a blokkláncon maradnak, és az ilyen szerződésekre vonatkozó adatok az adatbázisban maradnak.  |
| Munkafolyamat-azonosító                               | int            | Nem          | A szerződéshez társított munkafolyamat egyedi azonosítója |
| Munkafolyamatneve                             | nvarchar (50)   | Nem          | A szerződéshez társított munkafolyamat neve |
| Munkafolyamat-megjelenítésneve                      | nvarchar(255)  | Nem          | A felhasználói felületen megjelenített szerződéshez társított munkafolyamat neve |
| Munkafolyamatleírása                      | nvarchar(255)  | Igen         | A szerződéshez társított munkafolyamat leírása |
| ContractCodeId                           | int            | Nem          | A szerződéshez társított szerződéskód egyedi azonosítója |
| Szerződésfájlneve                         | int            | Nem          | A munkafolyamat intelligens szerződéskódját tartalmazó fájl neve. |
| ContractUploadedDtTm                     | int            | Nem          | A szerződéskód feltöltésének dátuma és időpontja |
| ContractId                               | int            | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus               | int            | Nem          | A szerződés létesítési folyamatának aktuális állapotát határozza meg. Lehetséges értékek: <br />0 – A szerződést az API hozta létre az adatbázisban<br />1 – A szerződést elküldték a főkönyvnek<br />2 – A szerződés sikeresen fellett helyezve a főkönyvbe<br />3 vagy 4 - A szerződés nem volt telepítve a főkönyvbe<br />5 - A szerződés sikeresen fellett helyezve a főkönyvbe <br /><br />Az 1.5-ös verziótól kezdve a 0-tól 5-ig támogatott értékek támogatottak. A visszafelé kompatibilitás az aktuális kiadásban, tekintse meg **a vwContractV0** érhető el, amely csak támogatja a 0-2 értékeket. |
| ContractLedgerIdentifier                 | nvarchar (255) Az |             | A szerződést üzembe helyező felhasználó e-mail címe |
| ContractDeployedByUserId                 | int            | Nem          | A szerződést üzembe helyező felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a felhasználó Azure Active Directory-azonosítóját képviselő guid.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Nem          | A szerződést üzembe helyező felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a felhasználó Azure Active Directory-azonosítóját képviselő guid.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Nem          | A kiépítési folyamat aktuális állapotát azonosítja a felhasználó számára. Lehetséges értékek: <br />0 – a felhasználót az API hozta létre<br />1 – Egy kulcs társítva van a felhasználóval az adatbázisban <br />2 – A felhasználó teljesen ki van építve                     |
| ContractDeployedByUserFirstName          | nvarchar (50)   | Igen         | A szerződést üzembe helyező felhasználó keresztneve |
| ContractDeployedByUserLastName           | nvarchar(255)  | Igen         | A szerződést üzembe helyező felhasználó vezetékneve |
| ContractDeployedByUserEmailcím       | nvarchar(255)  | Igen         | A szerződést üzembe helyező felhasználó e-mail címe |

## <a name="vwcontractaction"></a>vwContractAction

Ez a nézet a szerződésekkel kapcsolatos intézkedésekkel kapcsolatos információk többségét képviseli, és célja a közös jelentéstételi forgatókönyvek megkönnyítése. Ez a nézet minden egyes végrehajtott művelethez a következő adatokat tartalmazza:

-   Társított alkalmazásdefiníció
-   Társított munkafolyamat-definíció
-   Társított intelligens szerződés függvény és paraméterdefiníció
-   Társított főkönyvi megvalósítás a függvényhez
-   Paraméterekhez megadott egyedi példányértékek
-   A műveletet kezdeményező felhasználó adatai
-   A blokklánc blokkjával és tranzakciójával kapcsolatos részletek

| Név                                     | Típus          | Lehet Null | Leírás                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                          | nvarchar (50)  | Nem          | Az alkalmazás neve |
| Alkalmazásdisplayneve                   | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő név |
| Alkalmazás engedélyezve                       | bit           | Nem          | Ez a mező azonosítja, hogy az alkalmazás jelenleg engedélyezve van-e. Megjegyzés – Annak ellenére, hogy egy alkalmazás letiltva is megjelenhet az adatbázisban, a kapcsolódó szerződések a blokkláncon maradnak, és az ilyen szerződésekre vonatkozó adatok az adatbázisban maradnak.                                                  |
| Munkafolyamat-azonosító                               | int           | Nem          | Munkafolyamat egyedi azonosítója |
| Munkafolyamatneve                             | nvarchar (50)  | Nem          | A munkafolyamat neve |
| Munkafolyamat-megjelenítésneve                      | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő munkafolyamat neve |
| Munkafolyamatleírása                      | nvarchar(255) | Igen         | A munkafolyamat leírása |
| ContractId                               | int           | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus               | int           | Nem          | A szerződés létesítési folyamatának aktuális állapotát határozza meg. Lehetséges értékek: <br />0 – A szerződést az API hozta létre az adatbázisban<br />1 – A szerződést elküldték a főkönyvnek<br />2 – A szerződés sikeresen fellett helyezve a főkönyvbe<br />3 vagy 4 - A szerződés nem volt telepítve a főkönyvbe<br />5 - A szerződés sikeresen fellett helyezve a főkönyvbe <br /><br />Az 1.5-ös verziótól kezdve a 0-tól 5-ig támogatott értékek támogatottak. A visszafelé kompatibilitás az aktuális kiadásban, tekintse meg **a vwContractActionV0** érhető el, amely csak támogatja a 0-2 értékeket. |
| ContractCodeId                           | int           | Nem          | A szerződés kódimplementációjának egyedi azonosítója |
| ContractLedgerIdentifier                 | nvarchar(255) | Igen         | Adott elosztott főkönyvintelligens szerződés üzembe helyezett verziójához társított egyedi azonosító. Például Ethereum. |
| ContractDeployedByUserId                 | int           | Nem          | A szerződést üzembe helyező felhasználó egyedi azonosítója |
| ContractDeployedByUserFirstName          | nvarchar (50)  | Igen         | A szerződést üzembe helyező felhasználó utóneve |
| ContractDeployedByUserLastName           | nvarchar(255) | Igen         | A szerződést üzembe helyező felhasználó vezetékneve |
| ContractDeployedByUserExternalId         | nvarchar(255) | Nem          | A szerződést üzembe helyező felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító az a guid, amely az identitásukat képviseli a konzorciumi Azure Active Directoryban.                                                                                                                                                |
| ContractDeployedByUserEmailcím       | nvarchar(255) | Igen         | A szerződést üzembe helyező felhasználó e-mail címe |
| WorkflowFunctionId                       | int           | Nem          | Munkafolyamat-függvény egyedi azonosítója |
| WorkflowFunctionName                     | nvarchar (50)  | Nem          | A függvény neve |
| WorkflowFunctionDisplayName              | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő függvény neve |
| WorkflowFunctionDescription              | nvarchar(255) | Nem          | A függvény leírása |
| ContractActionId                         | int           | Nem          | A szerződésművelet egyedi azonosítója |
| ContractActionProvisioningStatus         | int           | Nem          | A szerződésművelet létesítési folyamatának aktuális állapotát határozza meg. Lehetséges értékek: <br />0 – A szerződésműveletet az API hozta létre az adatbázisban<br />1 – A szerződésművelet elküldve a főkönyvbe<br />2 – A szerződésművelet sikeresen telepítve van a főkönyvben<br />3 vagy 4 - A szerződés nem volt telepítve a főkönyvbe<br />5 - A szerződés sikeresen fellett helyezve a főkönyvbe <br /><br />Az 1.5-ös verziótól kezdve a 0-tól 5-ig támogatott értékek támogatottak. A visszafelé kompatibilitás az aktuális kiadásban, tekintse meg **a vwContractActionV0** érhető el, amely csak támogatja a 0-2 értékeket. |
| ContractActionTimestamp                  | időpont [2;7] | Nem          | A szerződésművelet időbélyege |
| ContractActionExecuteByUserId           | int           | Nem          | A szerződésműveletet végrehajtó felhasználó egyedi azonosítója |
| ContractActionExecutebyuserFirstName    | int           | Igen         | A szerződésműveletet végrehajtó felhasználó utóneve |
| ContractActionExecuteByUserLastName     | nvarchar (50)  | Igen         | A szerződésműveletet végrehajtó felhasználó vezetékneve |
| ContractActionExecuteByUserExternalId   | nvarchar(255) | Igen         | A szerződésműveletet végző felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító az a guid, amely az identitásukat képviseli a konzorciumi Azure Active Directoryban. |
| ContractActionexecutebyuseremailaddress | nvarchar(255) | Igen         | A szerződésműveletet végrehajtó felhasználó e-mail címe |
| WorkflowFunctionParameterId azonosító              | int           | Nem          | A függvény egy paraméterének egyedi azonosítója |
| WorkflowFunctionParameterName            | nvarchar (50)  | Nem          | A függvény paraméterének neve |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő függvényparaméter neve |
| WorkflowFunctionParameterDataTypeId      | int           | Nem          | A munkafolyamat-függvényparaméterhez társított adattípus egyedi azonosítója |
| WorkflowParameterDataTypeName            | nvarchar (50)  | Nem          | Munkafolyamat-függvényparaméterhez társított adattípus neve |
| ContractActionParameterValue érték             | nvarchar(255) | Nem          | Az intelligens szerződésben tárolt paraméter értéke |
| BlokkHash                                | nvarchar(255) | Igen         | A blokk kivonata |
| Blokkszám                              | int           | Igen         | A főkönyvblokk száma |
| BlockTimestamp (Blokkidőbélyeg)                           | időpont [2;7] | Igen         | A blokk időbélyegzője |
| Tranzakcióazonosító                            | int           | Nem          | A tranzakció egyedi azonosítója |
| Tranzakciófrom                          | nvarchar(255) | Igen         | Az a fél, aki az ügyletet kezdeményezte |
| Tranzakcióto                            | nvarchar(255) | Igen         | Az a párt, amelyik |
| TranzakcióHash                          | nvarchar(255) | Igen         | A tranzakció kivonata |
| TransactionIsWorkbenchTransaction        | bit           | Igen         | Egy bit, amely azonosítja, ha a tranzakció egy Azure Blockchain Workbench tranzakció |
| TransactionProvisioningStatus            | int           | Igen         | A tranzakció létesítési folyamatának aktuális állapotát határozza meg. Lehetséges értékek: <br />0 – A tranzakciót az API hozta létre az adatbázisban<br />1 – A tranzakció elküldve a főkönyvnek<br />2 – A tranzakció sikeresen telepítve van a főkönyvben                 |
| TransactionValue érték                         | decimális(32;2) | Igen         | A tranzakció értéke |

## <a name="vwcontractproperty"></a>vwContractProperty

Ez a nézet a szerződéshez kapcsolódó tulajdonságokkal kapcsolatos információk többségét képviseli, és célja a gyakori jelentési forgatókönyvek megkönnyítése. Ez a nézet minden egyes tulajdonsághoz a következő adatokat tartalmazza:

-   Társított alkalmazásdefiníció
-   Társított munkafolyamat-definíció
-   A munkafolyamatot üzembe helyező felhasználó adatai
-   Társított intelligens szerződés tulajdonságdefiníciója
-   A tulajdonságok adott példányértékei
-   A szerződés állami tulajdonának részletei

| Név                               | Típus          | Lehet Null | Leírás                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                    | nvarchar (50)  | Nem          | Az alkalmazás neve |
| Alkalmazásdisplayneve             | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő név |
| Alkalmazás engedélyezve                 | bit           | Nem          | Azt határozza meg, hogy az alkalmazás jelenleg engedélyezve van-e.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltva is megjelenhet az adatbázisban, a kapcsolódó szerződések a blokkláncon maradnak, és az ilyen szerződésekre vonatkozó adatok az adatbázisban maradnak.                      |
| Munkafolyamat-azonosító                         | int           | Nem          | A munkafolyamat egyedi azonosítója |
| Munkafolyamatneve                       | nvarchar (50)  | Nem          | A munkafolyamat neve |
| Munkafolyamat-megjelenítésneve                | nvarchar(255) | Nem          | A felhasználói felületen megjelenő munkafolyamat neve |
| Munkafolyamatleírása                | nvarchar(255) | Igen         | A munkafolyamat leírása |
| ContractId                         | int           | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus         | int           | Nem          | A szerződés létesítési folyamatának aktuális állapotát határozza meg. Lehetséges értékek: <br />0 – A szerződést az API hozta létre az adatbázisban<br />1 – A szerződést elküldték a főkönyvnek<br />2 – A szerződés sikeresen fellett helyezve a főkönyvbe<br />3 vagy 4 - A szerződés nem volt telepítve a főkönyvbe<br />5 - A szerződés sikeresen fellett helyezve a főkönyvbe <br /><br />Az 1.5-ös verziótól kezdve a 0-tól 5-ig támogatott értékek támogatottak. A visszafelé kompatibilitás az aktuális kiadásban, tekintse meg **a vwContractPropertyV0** érhető el, amely csak támogatja a 0-2 értékeket. |
| ContractCodeId                     | int           | Nem          | A szerződés kódimplementációjának egyedi azonosítója |
| ContractLedgerIdentifier           | nvarchar(255) | Igen         | Adott elosztott főkönyvintelligens szerződés üzembe helyezett verziójához társított egyedi azonosító. Például Ethereum. |
| ContractDeployedByUserId           | int           | Nem          | A szerződést üzembe helyező felhasználó egyedi azonosítója |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Igen         | A szerződést üzembe helyező felhasználó utóneve |
| ContractDeployedByUserLastName     | nvarchar(255) | Igen         | A szerződést üzembe helyező felhasználó vezetékneve |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nem          | A szerződést üzembe helyező felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító az a guid, amely az identitásukat képviseli a konzorciumban az Azure Active Directoryban |
| ContractDeployedByUserEmailcím | nvarchar(255) | Igen         | A szerződést üzembe helyező felhasználó e-mail címe |
| WorkflowPropertyId azonosító                 | int           |             | Munkafolyamat tulajdonságának egyedi azonosítója |
| WorkflowPropertyDataTypeId azonosító         | int           | Nem          | A tulajdonság adattípusának azonosítója |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Nem          | A tulajdonság adattípusának neve |
| WorkflowPropertyName               | nvarchar (50)  | Nem          | A munkafolyamat-tulajdonság neve |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nem          | A munkafolyamat-tulajdonság megjelenítendő neve |
| WorkflowPropertyDescription        | nvarchar(255) | Igen         | Az ingatlan leírása |
| ContractPropertyValue érték              | nvarchar(255) | Nem          | A szerződésben lévő ingatlan értéke |
| Államnév                          | nvarchar (50)  | Igen         | Ha ez a tulajdonság a szerződés állapotát tartalmazza, akkor az az állam megjelenítendő neve. Ha nincs társítva az állapothoz, az érték null lesz. |
| StateDisplayName (Állapotmegjelenítő neve)                   | nvarchar(255) | Nem          | Ha ez a tulajdonság az állapotot tartalmazza, akkor az állam megjelenítendő neve. Ha nincs társítva az állapothoz, az érték null lesz. |
| StateValue értéke                         | nvarchar(255) | Igen         | Ha ez a tulajdonság tartalmazza az államot, akkor az az állapot értéke. Ha nincs társítva az állapothoz, az érték null lesz. |

## <a name="vwcontractstate"></a>vwContractState

Ez a nézet egy adott szerződés állapotával kapcsolatos információk többségét képviseli, és célja, hogy megkönnyítse a közös jelentéstételi forgatókönyveket. Ebben a nézetben minden rekord a következő adatokat tartalmazza:

-   Társított alkalmazásdefiníció
-   Társított munkafolyamat-definíció
-   A munkafolyamatot üzembe helyező felhasználó adatai
-   Társított intelligens szerződés tulajdonságdefiníciója
-   A szerződés állami tulajdonának részletei

| Név                               | Típus          | Lehet Null | Leírás                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                    | nvarchar (50)  | Nem          | Az alkalmazás neve |
| Alkalmazásdisplayneve             | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő név |
| Alkalmazás engedélyezve                 | bit           | Nem          | Azt határozza meg, hogy az alkalmazás jelenleg engedélyezve van-e.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltva is megjelenhet az adatbázisban, a kapcsolódó szerződések a blokkláncon maradnak, és az ilyen szerződésekre vonatkozó adatok az adatbázisban maradnak. |
| Munkafolyamat-azonosító                         | int           | Nem          | A munkafolyamat egyedi azonosítója |
| Munkafolyamatneve                       | nvarchar (50)  | Nem          | A munkafolyamat neve |
| Munkafolyamat-megjelenítésneve                | nvarchar(255) | Nem          | A felhasználói felületen megjelenő név |
| Munkafolyamatleírása                | nvarchar(255) | Igen         | A munkafolyamat leírása |
| ContractLedgerVégrehajtási azonosító     | nvarchar(255) | Igen         | Adott elosztott főkönyvintelligens szerződés üzembe helyezett verziójához társított egyedi azonosító. Például Ethereum. |
| ContractId                         | int           | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus         | int           | Nem          |A szerződés létesítési folyamatának aktuális állapotát határozza meg. Lehetséges értékek: <br />0 – A szerződést az API hozta létre az adatbázisban<br />1 – A szerződést elküldték a főkönyvnek<br />2 – A szerződés sikeresen fellett helyezve a főkönyvbe<br />3 vagy 4 - A szerződés nem volt telepítve a főkönyvbe<br />5 - A szerződés sikeresen fellett helyezve a főkönyvbe <br /><br />Az 1.5-ös verziótól kezdve a 0-tól 5-ig támogatott értékek támogatottak. A visszafelé kompatibilitás a jelenlegi kiadás, tekintse **meg vwContractStateV0** érhető el, amely csak támogatja a 0-2 értékeket. |
| ConnectionId azonosító                       | int           | Nem          | A munkafolyamat által üzembe helyezett blockchainpéldány egyedi azonosítója |
| ContractCodeId                     | int           | Nem          | A szerződés kódimplementációjának egyedi azonosítója |
| ContractDeployedByUserId           | int           | Nem          | A szerződést üzembe helyező felhasználó egyedi azonosítója |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nem          | A szerződést üzembe helyező felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító az a guid, amely az identitásukat képviseli a konzorciumi Azure Active Directoryban. |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Igen         | A szerződést üzembe helyező felhasználó utóneve |
| ContractDeployedByUserLastName     | nvarchar(255) | Igen         | A szerződést üzembe helyező felhasználó vezetékneve |
| ContractDeployedByUserEmailcím | nvarchar(255) | Igen         | A szerződést üzembe helyező felhasználó e-mail címe |
| WorkflowPropertyId azonosító                 | int           | Nem          | Munkafolyamat-tulajdonság egyedi azonosítója |
| WorkflowPropertyDataTypeId azonosító         | int           | Nem          | A munkafolyamat-tulajdonság adattípusának azonosítója |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Nem          | A munkafolyamat-tulajdonság adattípusának neve |
| WorkflowPropertyName               | nvarchar (50)  | Nem          | A munkafolyamat-tulajdonság neve |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő tulajdonság megjelenítendő neve |
| WorkflowPropertyDescription        | nvarchar(255) | Igen         | Az ingatlan leírása |
| ContractPropertyValue érték              | nvarchar(255) | Nem          | A szerződésben tárolt ingatlan értéke |
| Államnév                          | nvarchar (50)  | Igen         | Ha ez a tulajdonság tartalmazza az államot, akkor az állam megjelenítendő neve. Ha nincs társítva az állapothoz, az érték null lesz. |
| StateDisplayName (Állapotmegjelenítő neve)                   | nvarchar(255) | Nem          | Ha ez a tulajdonság az állapotot tartalmazza, akkor az állam megjelenítendő neve. Ha nincs társítva az állapothoz, az érték null lesz. |
| StateValue értéke                         | nvarchar(255) | Igen         | Ha ez a tulajdonság tartalmazza az államot, akkor az az állapot értéke. Ha nincs társítva az állapothoz, az érték null lesz. |

## <a name="vwuser"></a>vwFelhasználó

Ez a nézet az Azure Blockchain Workbench használatára kiépített konzorciumi tagok adatait tartalmazza. Alapértelmezés szerint az adatok a felhasználó kezdeti kiépítésével vannak feltöltve.

| Név               | Típus          | Lehet Null | Leírás                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID (Azonosító)                 | int           | Nem          | A felhasználó egyedi azonosítója |
| Külső azonosító         | nvarchar(255) | Nem          | A felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a felhasználó Azure Active Directory-azonosítóját képviselő guid. |
| ProvisioningStatus (Kiépítésállapota) | int           | Nem          |A kiépítési folyamat aktuális állapotát határozza meg a felhasználó számára. Lehetséges értékek: <br />0 – A felhasználót az API hozta létre<br />1 – Egy kulcs társítva van a felhasználóval az adatbázisban<br />2 – A felhasználó teljesen ki van építve |
| FirstName          | nvarchar (50)  | Igen         | A felhasználó utóneve |
| LastName           | nvarchar (50)  | Igen         | A felhasználó vezetékneve |
| E-mail cím       | nvarchar(255) | Igen         | A felhasználó e-mail címe |

## <a name="vwworkflow"></a>vwWorkflow

Ez a nézet a munkafolyamat alapvető metaadatait, valamint a munkafolyamat funkcióit és paramétereit mutatja. Jelentéskészítésre tervezték, és a munkafolyamathoz társított alkalmazás metaadatait is tartalmazza. Ez a nézet több alapul szolgáló tábla adatait tartalmazza a munkafolyamatok jelentésének megkönnyítése érdekében. Ez a nézet minden munkafolyamathoz a következő adatokat tartalmazza:

-   Társított alkalmazásdefiníció
-   Társított munkafolyamat-definíció
-   Társított munkafolyamat indítási állapotadatai

| Név                              | Típus          | Lehet Null | Leírás                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                   | nvarchar (50)  | Nem          | Az alkalmazás neve |
| Alkalmazásdisplayneve            | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő név |
| Alkalmazás engedélyezve                | bit           | Nem          | Azonosítja, ha az alkalmazás engedélyezve van |
| Munkafolyamat-azonosító                        | int           | Igen         | Munkafolyamat egyedi azonosítója |
| Munkafolyamatneve                      | nvarchar (50)  | Nem          | A munkafolyamat neve |
| Munkafolyamat-megjelenítésneve               | nvarchar(255) | Nem          | A felhasználói felületen megjelenő név |
| Munkafolyamatleírása               | nvarchar(255) | Igen         | A munkafolyamat leírása. |
| WorkflowConstructorFunctionId     | int           | Nem          | A munkafolyamat konstruktoraként szolgáló munkafolyamat-függvény azonosítója |
| WorkflowStartStateId              | int           | Nem          | Az állapot egyedi azonosítója |
| WorkflowStartStateName            | nvarchar (50)  | Nem          | Az állam neve |
| WorkflowStartStateDisplayName     | nvarchar(255) | Nem          | Az állapot felhasználói felületén megjelenítendő név |
| WorkflowStartStateDescription     | nvarchar(255) | Igen         | A munkafolyamat-állapot leírása |
| WorkflowStartStateStyle           | nvarchar (50)  | Igen         | Ez az érték azonosítja, hogy a munkafolyamat hány százalékban teljes ebben az állapotban |
| WorkflowStartStateValue érték           | int           | Nem          | Az állam értéke |
| WorkflowStartStatePercentComplete | int           | Nem          | Szöveges leírás, amely útmutatást ad az ügyfeleknek arról, hogyan jeleníthetik meg ezt az állapotot a felhasználói felületen. A támogatott állapotok közé tartozik *a siker* és *a sikertelenség* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Ez a nézet a munkafolyamat alapvető metaadatait, valamint a munkafolyamat funkcióit és paramétereit mutatja. Jelentéskészítésre tervezték, és a munkafolyamathoz társított alkalmazás metaadatait is tartalmazza. Ez a nézet több alapul szolgáló tábla adatait tartalmazza a munkafolyamatok jelentésének megkönnyítése érdekében. Ez a nézet minden munkafolyamat-függvényhez a következő adatokat tartalmazza:

-   Társított alkalmazásdefiníció
-   Társított munkafolyamat-definíció
-   Munkafolyamat-függvény részletei

| Név                                 | Típus          | Lehet Null | Leírás                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                      | nvarchar (50)  | Nem          | Az alkalmazás neve |
| Alkalmazásdisplayneve               | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő név |
| Alkalmazás engedélyezve                   | bit           | Nem          | Azonosítja, ha az alkalmazás engedélyezve van |
| Munkafolyamat-azonosító                           | int           | Nem          | Munkafolyamat egyedi azonosítója |
| Munkafolyamatneve                         | nvarchar (50)  | Nem          | A munkafolyamat neve |
| Munkafolyamat-megjelenítésneve                  | nvarchar(255) | Nem          | A felhasználói felületen megjelenő munkafolyamat neve |
| Munkafolyamatleírása                  | nvarchar(255) | Igen         | A munkafolyamat leírása |
| WorkflowFunctionId                   | int           | Nem          | Egy függvény egyedi azonosítója |
| WorkflowFunctionName                 | nvarchar (50)  | Igen         | A függvény neve |
| WorkflowFunctionDisplayName          | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő függvény neve |
| WorkflowFunctionDescription          | nvarchar(255) | Igen         | A munkafolyamat-függvény leírása |
| WorkflowFunctionIsConstructor        | bit           | Nem          | Azt határozza meg, hogy a munkafolyamat-függvény a munkafolyamat konstruktora-e |
| WorkflowFunctionParameterId azonosító          | int           | Nem          | Egy függvény paraméterének egyedi azonosítója |
| WorkflowFunctionParameterName        | nvarchar (50)  | Nem          | A függvény paraméterének neve |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő függvényparaméter neve |
| WorkflowFunctionParameterDataTypeId  | int           | Nem          | A munkafolyamat-függvényparaméterhez társított adattípus egyedi azonosítója |
| WorkflowParameterDataTypeName        | nvarchar (50)  | Nem          | Munkafolyamat-függvényparaméterhez társított adattípus neve |

## <a name="vwworkflowproperty"></a>vwWorkflowTulajdonság

Ez a nézet a munkafolyamathoz definiált tulajdonságokat jelöli. Ez a nézet minden tulajdonsághoz a következő adatokat tartalmazza:

-   Társított alkalmazásdefiníció
-   Társított munkafolyamat-definíció
-   Munkafolyamat-tulajdonság részletei

| Név                         | Típus          | Lehet Null | Leírás                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName              | nvarchar (50)  | Nem          | Az alkalmazás neve |
| Alkalmazásdisplayneve       | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő név |
| Alkalmazás engedélyezve           | bit           | Nem          | Azt határozza meg, hogy az alkalmazás jelenleg engedélyezve van-e.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltva is megjelenhet az adatbázisban, a kapcsolódó szerződések a blokkláncon maradnak, és az ilyen szerződésekre vonatkozó adatok az adatbázisban maradnak. |
| Munkafolyamat-azonosító                   | int           | Nem          | A munkafolyamat egyedi azonosítója |
| Munkafolyamatneve                 | nvarchar (50)  | Nem          | A munkafolyamat neve |
| Munkafolyamat-megjelenítésneve          | nvarchar(255) | Nem          | A munkafolyamat neve a felhasználói felületen |
| Munkafolyamatleírása          | nvarchar(255) | Igen         | A munkafolyamat leírása |
| Munkafolyamat-tulajdonságazonosító           | int           | Nem          | Munkafolyamat tulajdonságának egyedi azonosítója |
| WorkflowPropertyName         | nvarchar (50)  | Nem          | Az ingatlan neve |
| WorkflowPropertyDescription  | nvarchar(255) | Igen         | Az ingatlan leírása |
| WorkflowPropertyDisplayName  | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő név |
| WorkflowPropertyWorkflowId azonosító   | int           | Nem          | Annak a munkafolyamatnak az azonosítója, amelyhez ez a tulajdonság társítva van |
| WorkflowPropertyDataTypeId azonosító   | int           | Nem          | A tulajdonsághoz definiált adattípus azonosítója |
| WorkflowPropertyDataTypeName | nvarchar (50)  | Nem          | A tulajdonsághoz definiált adattípus neve |
| WorkflowPropertyIsState      | bit           | Nem          | Ez a mező azonosítja, hogy ez a munkafolyamat-tulajdonság tartalmazza-e a munkafolyamat állapotát. |

## <a name="vwworkflowstate"></a>vwWorkflowState

Ez a nézet a munkafolyamathoz társított tulajdonságokat jelöli. Ez a nézet minden szerződéshez a következő adatokat tartalmazza:

-   Társított alkalmazásdefiníció
-   Társított munkafolyamat-definíció
-   Munkafolyamat állapotadatai

| Név                         | Típus          | Lehet Null | Leírás                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName              | nvarchar (50)  | Nem          | Az alkalmazás neve |
| Alkalmazásdisplayneve       | nvarchar(255) | Nem          | A kérelem leírása |
| Alkalmazás engedélyezve           | bit           | Nem          | Azt határozza meg, hogy az alkalmazás jelenleg engedélyezve van-e.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltva is megjelenhet az adatbázisban, a kapcsolódó szerződések a blokkláncon maradnak, és az ilyen szerződésekre vonatkozó adatok az adatbázisban maradnak. |
| Munkafolyamat-azonosító                   | int           | Nem          | A munkafolyamat egyedi azonosítója |
| Munkafolyamatneve                 | nvarchar (50)  | Nem          | A munkafolyamat neve |
| Munkafolyamat-megjelenítésneve          | nvarchar(255) | Nem          | A munkafolyamat felhasználói felületén megjelenő név |
| Munkafolyamatleírása          | nvarchar(255) | Igen         | A munkafolyamat leírása |
| Munkafolyamat-stateid              | int           | Nem          | Az állapot egyedi azonosítója |
| WorkflowStateName            | nvarchar (50)  | Nem          | Az állam neve |
| WorkflowStateDisplayName     | nvarchar(255) | Nem          | Az állapot felhasználói felületén megjelenítendő név |
| WorkflowStateDescription     | nvarchar(255) | Igen         | A munkafolyamat-állapot leírása |
| WorkflowStatePercentComplete | int           | Nem          | Ez az érték azonosítja, hogy a munkafolyamat hány százalékban teljes ebben az állapotban |
| WorkflowStateValue érték           | nvarchar (50)  | Nem          | Az állam értéke |
| WorkflowStateStyle           | nvarchar (50)  | Nem          | Szöveges leírás, amely útmutatást ad az ügyfeleknek arról, hogyan jeleníthetik meg ezt az állapotot a felhasználói felületen. A támogatott állapotok közé tartozik *a siker* és *a sikertelenség* |
