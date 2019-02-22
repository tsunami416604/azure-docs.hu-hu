---
title: Az Azure Blockchain Workbench alkalmazásban adatbázisnézeteivel
description: Az Azure Blockchain Workbench SQL-adatbázis adatbázisnézeteivel áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/21/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 06b7fb678bc79203589cfa75e8afb457d6ed344f
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594321"
---
# <a name="database-views-in-azure-blockchain-workbench"></a>Az Azure Blockchain Workbench alkalmazásban adatbázisnézeteivel

Az Azure Blockchain Workbench érkező, elosztott főkönyvek továbbítja az adatokat egy *láncon kívüli* SQL DB-adatbázist. A láncon kívüli adatbázis lehetővé teszi a használ SQL és a meglévő eszközökkel, mint [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), blockchain-adatok kezeléséhez.

Az Azure Blockchain Workbench biztosít, amely a lekérdezések végrehajtásakor lesz hasznos adatokhoz hozzáférést biztosító adatbázis-nézetek. Ezek a nézetek fokozottan denormalizált megkönnyíti gyorsan máris jelentések, elemzések, és egyéb felhasználását blockchain meglévő eszközeivel, és szoftveres átképezése adatbázis személyzet nélkül.

Ez a szakasz az adatbázis-nézetek és a rajtuk tárolt adatok áttekintését tartalmazza.

> [!NOTE]
> Kívül, bár lehetséges, ezek a nézetek az adatbázisban található adatbázis-táblák közvetlen használatáért nem támogatott.
>

## <a name="vwapplication"></a>vwApplication

Ez a nézet részletesen **alkalmazások** , amely fel lett töltve az Azure Blockchain Workbench használatával.

| Name (Név)                             | Typo          | NULL értékű lehet | Leírás                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító                    | int           | Nem          | Az alkalmazás egyedi azonosítója |
| Alkalmazásnév                  | nvarchar(50)  | Nem          | Az alkalmazás neve |
| ApplicationDescription           | nvarchar(255) | Igen         | Az alkalmazás leírása |
| ApplicationDisplayName           | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő neve |
| ApplicationEnabled               | bit           | Nem          | Annak jelzése, hogy az alkalmazás jelenleg engedélyezve van<br /> **Megjegyzés:** Annak ellenére, hogy egy alkalmazás is megjelennek, mert az adatbázis le van tiltva, kapcsolódó szerződések blokkláncon maradnak, és a említett szerződések vonatkozó adatokat az adatbázisban maradnak. |
| UploadedDtTm                     | datetime2(7)  | Nem          | A dátum és idő szerződés lett feltöltve. |
| UploadedByUserId                 | int           | Nem          | A felhasználó, aki feltölteni az alkalmazás azonosítója |
| UploadedByUserExternalId         | nvarchar(255) | Nem          | A felhasználó számára az alkalmazás feltöltése külső azonosító. Alapértelmezés szerint ezt az Azonosítót a a felhasználó az Azure consortium az Active Directoryból.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Nem          | Azonosítja a kiépítési folyamat a felhasználó aktuális állapotát. Lehetséges értékek: <br />0 – felhasználó által létrehozott az API-hoz<br />1 – a kulcs társítva a felhasználó az adatbázisban<br />2 – a felhasználó teljes kiépítése                         |
| UploadedByUserFirstName          | nvarchar(50)  | Igen         | A felhasználó, aki a szerződés feltöltött utóneve |
| UploadedByUserLastName           | nvarchar(50)  | Igen         | A felhasználó, aki a szerződés feltöltött vezetékneve |
| UploadedByUserEmailAddress       | nvarchar(255) | Igen         | A szerződés feltöltött felhasználó e-mail-címe |

## <a name="vwapplicationrole"></a>vwApplicationRole

Ez a nézet az Azure Blockchain Workbench alkalmazásban definiált szerepkörök részletesen.

Az egy *eszköz Transfer* alkalmazás, például szerepkörökben, például *vevő* és *értékesítői* szerepkörök is definiálva lehet.

| Name (Név)                   | Typo             | NULL értékű lehet | Leírás                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| Alkalmazásazonosító          | int              | Nem          | Az alkalmazás egyedi azonosítója           |
| Alkalmazásnév        | nvarchar(50)     | Nem          | Az alkalmazás neve                       |
| ApplicationDescription | nvarchar(255)    | Igen         | Az alkalmazás leírása                  |
| ApplicationDisplayName | nvarchar(255)    | Nem          | A felhasználói felületen megjelenítendő neve      |
| RoleId                 | int              | Nem          | Az alkalmazás-szerepkör egyedi azonosítója |
| RoleName               | nvarchar50)      | Nem          | A szerepkör neve                              |
| RoleDescription        | Description(255) | Igen         | A szerepkör leírása                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Ez a nézet a szerepkörök az Azure Blockchain Workbench alkalmazás és a hozzájuk rendelt felhasználók definiált részletesen.

Az egy *eszköz Transfer* alkalmazás, például *Kovács János* lehet társítva a *vásárló* szerepkör.

| Name (Név)                       | Typo          | NULL értékű lehet | Leírás                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító              | int           | Nem          | Az alkalmazás egyedi azonosítója                                                                                                                                                                                               |
| Alkalmazásnév            | nvarchar(50)  | Nem          | Az alkalmazás neve                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Igen         | Az alkalmazás leírása                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő neve                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Nem          | Az alkalmazás-szerepkör egyedi azonosítója                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Nem          | A szerepkör neve                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Igen         | A szerepkör leírása                                                                                                                                                                                                             |
| Felhasználói azonosító                     | int           | Nem          | A felhasználó a szerepkörhöz társított azonosítója. |
| UserExternalId             | nvarchar(255) | Nem          | A felhasználó, aki a szerepkörhöz társított külső azonosítója. Alapértelmezés szerint ezt az Azonosítót a a felhasználó az Azure consortium az Active Directoryból.                                                                     |
| UserProvisioningStatus     | int           | Nem          | Azonosítja a kiépítési folyamat a felhasználó aktuális állapotát. Lehetséges értékek: <br />0 – felhasználó által létrehozott az API-hoz<br />1 – a kulcs társítva a felhasználó az adatbázisban<br />2 – a felhasználó teljes kiépítése |
| UserFirstName              | nvarchar(50)  | Igen         | A szerepkörhöz társított felhasználó utóneve |
| UserLastName               | nvarchar(255) | Igen         | A szerepkörhöz társított felhasználó vezetékneve |
| UserEmailAddress           | nvarchar(255) | Igen         | A felhasználó, aki a szerepkörhöz társított e-mail-címe |

## <a name="vwconnectionuser"></a>vwConnectionUser

Ez a nézet részletesen a kapcsolatokat, az Azure Blockchain Workbench alkalmazásban definiált és a hozzájuk rendelt felhasználók. Minden kapcsolat esetén ez a nézet tartalmazza a következő adatokat:

-   Társított főkönyvi részletei
-   Kapcsolódó felhasználói adatok

| Name (Név)                     | Typo          | NULL értékű lehet | Leírás                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Nem          | Az Azure Blockchain Workbench alkalmazásban egy kapcsolat egyedi azonosítója |
| ConnectionEndpointUrl    | nvarchar(50)  | Nem          | A kapcsolati végpont URL-címe |
| ConnectionFundingAccount | nvarchar(255) | Igen         | A kapcsolat létrehozásakor, ha van ilyen tartozó finanszírozási fiók |
| LedgerId                 | int           | Nem          | A Főkönyv egyedi azonosítója |
| LedgerName               | nvarchar(50)  | Nem          | A Főkönyv neve |
| LedgerDisplayName        | nvarchar(255) | Nem          | A Főkönyv a felhasználói felületen megjelenítendő neve |
| Felhasználói azonosító                   | int           | Nem          | A kapcsolat társított felhasználó azonosítója |
| UserExternalId           | nvarchar(255) | Nem          | A felhasználó van társítva a kapcsolat külső azonosítója. Alapértelmezés szerint ezt az Azonosítót a a felhasználó az Azure consortium az Active Directoryból. |
| UserProvisioningStatus   | int           | Nem          |Azonosítja a kiépítési folyamat a felhasználó aktuális állapotát. Lehetséges értékek: <br />0 – felhasználó által létrehozott az API-hoz<br />1 – a kulcs társítva a felhasználó az adatbázisban<br />2 – a felhasználó teljes kiépítése |
| UserFirstName            | nvarchar(50)  | Igen         | A kapcsolat társított felhasználó utóneve |
| UserLastName             | nvarchar(255) | Igen         | A kapcsolat társított felhasználó vezetékneve |
| UserEmailAddress         | nvarchar(255) | Igen         | A kapcsolat társított felhasználó e-mail-címe |

## <a name="vwcontract"></a>vwContract

Ez a nézet az üzembe helyezett szerződések részletesen ismerteti. Ebben a nézetben minden szerződés, a következő adatokat tartalmazza:

-   Társított felügyeltalkalmazás-definíció
-   Társított munkafolyamat-definíció
-   A függvény társított főkönyvi megvalósítása
-   A felhasználó által kezdeményezett művelet részletei
-   A blockchain letiltása és a tranzakció kapcsolatos részleteket

| Name (Név)                                     | Typo           | NULL értékű lehet | Leírás                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Nem          | Az Azure Blockchain Workbench alkalmazásban egy kapcsolat egyedi azonosítója.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Nem          | A kapcsolati végpont URL-címe |
| ConnectionFundingAccount                 | nvarchar(255)  | Igen         | A kapcsolat létrehozásakor, ha van ilyen tartozó finanszírozási fiók |
| LedgerId                                 | int            | Nem          | A Főkönyv egyedi azonosítója |
| LedgerName                               | nvarchar(50)   | Nem          | A Főkönyv neve |
| LedgerDisplayName                        | nvarchar(255)  | Nem          | A Főkönyv a felhasználói felületen megjelenítendő neve |
| Alkalmazásazonosító                            | int            | Nem          | Az alkalmazás egyedi azonosítója |
| Alkalmazásnév                          | nvarchar (legfeljebb 50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName                   | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő neve |
| ApplicationEnabled                       | bit            | Nem          | Annak jelzése, hogy az alkalmazás jelenleg engedélyezve van.<br /> **Megjegyzés:** Annak ellenére, hogy egy alkalmazás is megjelennek, mert az adatbázis le van tiltva, kapcsolódó szerződések blokkláncon maradnak, és a említett szerződések vonatkozó adatokat az adatbázisban maradnak.  |
| WorkflowId                               | int            | Nem          | A szerződés társított munkafolyamat egyedi azonosítója |
| WorkflowName                             | nvarchar(50)   | Nem          | A szerződés társított munkafolyamat neve |
| WorkflowDisplayName                      | nvarchar(255)  | Nem          | A munkafolyamat társított a szerződésben, a felhasználói felületen megjelenített neve |
| WorkflowDescription                      | nvarchar(255)  | Igen         | A szerződés társított munkafolyamat leírása |
| ContractCodeId                           | int            | Nem          | A szerződés kódot, a szerződés társított egyedi azonosítója |
| ContractFileName                         | int            | Nem          | Ez a munkafolyamat intelligens szerződés kódját tartalmazó fájl neve. |
| ContractUploadedDtTm                     | int            | Nem          | A dátum és idő, a szerződés kódot töltött fel |
| ContractId                               | int            | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus               | int            | Nem          | Azonosítja a kiépítési folyamat a szerződés aktuális állapotát. Lehetséges értékek: <br />0 – a szerződés által az API-t az adatbázis létrejött<br />1 – a szerződés el lett küldve a Főkönyv<br />2 – a szerződés rendelkezik sikerült üzembe helyezni a Főkönyv<br />3-as vagy 4 – a szerződés nem sikerült üzembe helyezni a Főkönyv<br />5 – a szerződés sikeresen települt a Főkönyv <br /><br />1.5-ös verziójának kezdve az értékek 0 – 5 támogatottak. A visszamenőleges kompatibilitás a jelenlegi kiadásban, a nézet **vwContractV0** érhető el, hogy csak értékek 0 és 2. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | A felhasználó, aki a szerződés telepített e-mail-címe |
| ContractDeployedByUserId                 | int            | Nem          | A felhasználó, aki a szerződés telepített külső azonosítója. Alapértelmezés szerint ezt az Azonosítót a jelölő, az Azure Active Directory azonosító a felhasználó GUID azonosítója.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Nem          | A szerződés központilag telepített a felhasználó külső azonosítója. Alapértelmezés szerint ezt az Azonosítót a jelölő, az Azure Active Directory azonosító a felhasználó GUID azonosítója.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Nem          | Azonosítja a kiépítési folyamat a felhasználó aktuális állapotát. Lehetséges értékek: <br />0 – felhasználó által létrehozott az API-hoz<br />1 – a kulcs társítva a felhasználó az adatbázisban <br />2 – a felhasználó teljes kiépítése                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Igen         | A szerződés telepített a felhasználó utóneve |
| ContractDeployedByUserLastName           | nvarchar(255)  | Igen         | A szerződés telepített a felhasználó vezetékneve |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Igen         | A felhasználó, aki a szerződés telepített e-mail-címe |

## <a name="vwcontractaction"></a>vwContractAction

Ez a nézet a legtöbb szerződések végzett műveleteket kapcsolatos adatokat jelöli, és általános jelentéskészítési forgatókönyvekhez könnyen megkönnyítésére szolgál. Végrehajtott műveletek Ez a nézet tartalmazza a következő adatokat:

-   Társított felügyeltalkalmazás-definíció
-   Társított munkafolyamat-definíció
-   Társított intelligens szerződés függvény és a paraméter meghatározása
-   A függvény társított főkönyvi megvalósítása
-   Példány értékek paraméterekhez
-   A felhasználó által kezdeményezett művelet részletei
-   A blockchain letiltása és a tranzakció kapcsolatos részleteket

| Name (Név)                                     | Typo          | NULL értékű lehet | Leírás                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító                            | int           | Nem          | Az alkalmazás egyedi azonosítója |
| Alkalmazásnév                          | nvarchar(50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName                   | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő neve |
| ApplicationEnabled                       | bit           | Nem          | Ez a mező annak jelzése, hogy az alkalmazás jelenleg engedélyezve van. Megjegyzés: – annak ellenére, egy alkalmazás is megjelennek, mert az adatbázis le van tiltva, kapcsolódó szerződések továbbra is a blockchain és említett szerződések vonatkozó adatokat az adatbázisban maradnak.                                                  |
| WorkflowId                               | int           | Nem          | Egy munkafolyamat egyedi azonosítója |
| WorkflowName                             | nvarchar(50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName                      | nvarchar(255) | Nem          | A munkafolyamat a felhasználói felületen megjelenítendő neve |
| WorkflowDescription                      | nvarchar(255) | Igen         | A munkafolyamat leírása |
| ContractId                               | int           | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus               | int           | Nem          | Azonosítja a kiépítési folyamat a szerződés aktuális állapotát. Lehetséges értékek: <br />0 – a szerződés által az API-t az adatbázis létrejött<br />1 – a szerződés el lett küldve a Főkönyv<br />2 – a szerződés rendelkezik sikerült üzembe helyezni a Főkönyv<br />3-as vagy 4 – a szerződés nem sikerült üzembe helyezni a Főkönyv<br />5 – a szerződés sikeresen települt a Főkönyv <br /><br />1.5-ös verziójának kezdve az értékek 0 – 5 támogatottak. A visszamenőleges kompatibilitás a jelenlegi kiadásban, a nézet **vwContractActionV0** érhető el, hogy csak értékek 0 és 2. |
| ContractCodeId                           | int           | Nem          | A kód végrehajtását a szerződés egyedi azonosítója |
| ContractLedgerIdentifier                 | nvarchar(255) | Igen         | Egy adott elosztott Főkönyv intelligens szerződések telepített verziójának egyedi azonosítója. Ha például Ethereum. |
| ContractDeployedByUserId                 | int           | Nem          | A szerződés központilag telepített a felhasználó egyedi azonosítója |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Igen         | A szerződés telepített a felhasználó utóneve |
| ContractDeployedByUserLastName           | nvarchar(255) | Igen         | A szerződés telepített a felhasználó vezetékneve |
| ContractDeployedByUserExternalId         | nvarchar(255) | Nem          | A szerződés telepített a felhasználó külső azonosítója. Alapértelmezés szerint ezt az Azonosítót a guid-azonosítót fog kérni a az Azure Active Directory konzorcium képviselő.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Igen         | A felhasználó, aki a szerződés telepített e-mail-címe |
| WorkflowFunctionId                       | int           | Nem          | Egy munkafolyamat-funkció egyedi azonosítója |
| WorkflowFunctionName                     | nvarchar(50)  | Nem          | A függvény neve |
| WorkflowFunctionDisplayName              | nvarchar(255) | Nem          | Egy függvény a felhasználói felületen megjelenítendő neve |
| WorkflowFunctionDescription              | nvarchar(255) | Nem          | A funkció leírása |
| ContractActionId                         | int           | Nem          | A szerződés művelet egyedi azonosítója |
| ContractActionProvisioningStatus         | int           | Nem          | Azonosítja a kiépítési folyamat a szerződés művelet aktuális állapotát. Lehetséges értékek: <br />0 – a szerződés művelet által létrehozott az adatbázis az API<br />1 – a szerződés műveletet el lett küldve a Főkönyv<br />2 – a szerződés művelet már sikeresen alkalmazva van a Főkönyv<br />3-as vagy 4 – a szerződés nem sikerült üzembe helyezni a Főkönyv<br />5 – a szerződés sikeresen települt a Főkönyv <br /><br />1.5-ös verziójának kezdve az értékek 0 – 5 támogatottak. A visszamenőleges kompatibilitás a jelenlegi kiadásban, a nézet **vwContractActionV0** érhető el, hogy csak értékek 0 és 2. |
| ContractActionTimestamp                  | datetime(2,7) | Nem          | A szerződés művelet történő küldés időbélyegzője |
| ContractActionExecutedByUserId           | int           | Nem          | A felhasználót, hogy a szerződés művelet végrehajtása egyedi azonosítója |
| ContractActionExecutedByUserFirstName    | int           | Igen         | A szerződés műveletet végrehajtó felhasználó utóneve |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Igen         | A szerződés műveletet végrehajtó felhasználó vezetékneve |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Igen         | A szerződés műveletet végrehajtó felhasználó külső azonosítója. Alapértelmezés szerint ezt az Azonosítót a guid-azonosítót fog kérni a az Azure Active Directory konzorcium képviselő. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Igen         | A szerződés műveletet végrehajtó felhasználó e-mail-címe |
| WorkflowFunctionParameterId              | int           | Nem          | A függvény egy paramétert egyedi azonosítója |
| WorkflowFunctionParameterName            | nvarchar(50)  | Nem          | A függvénynek egy paraméter neve |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Nem          | Egy függvény paramétere a felhasználói felületen megjelenítendő neve |
| WorkflowFunctionParameterDataTypeId      | int           | Nem          | Egy munkafolyamat függvényparaméter társított adatok típusának egyedi azonosítója |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Nem          | Egy munkafolyamat függvényparaméter társított adattípus neve |
| ContractActionParameterValue             | nvarchar(255) | Nem          | Az intelligens szerződés tárolja a paraméter értéke |
| BlockHash                                | nvarchar(255) | Igen         | A blokk kivonata |
| BlockNumber                              | int           | Igen         | A Főkönyv a blokkok száma |
| BlockTimestamp                           | datetime(2,7) | Igen         | A blokk időbélyege |
| TransactionId                            | int           | Nem          | A tranzakció egyedi azonosítója |
| TransactionFrom                          | nvarchar(255) | Igen         | A felet, adja meg a tranzakció |
| TransactionTo                            | nvarchar(255) | Igen         | Az entitás, amely lett a tranzakció |
| TransactionHash                          | nvarchar(255) | Igen         | Egy tranzakció kivonata |
| TransactionIsWorkbenchTransaction        | bit           | Igen         | Egy kis, amely azonosítja, ha a tranzakció az Azure Blockchain Workbench-tranzakció |
| TransactionProvisioningStatus            | int           | Igen         | Azonosítja a tranzakció a kiépítési folyamat az aktuális állapotát. Lehetséges értékek: <br />0 – a tranzakció az adatbázis az API által létrehozva<br />1 – a tranzakció el lett küldve a Főkönyv<br />2 – a tranzakció már sikeresen alkalmazva van a Főkönyv                 |
| TransactionValue                         | Decimal(32,2) | Igen         | Az érték a tranzakció |

## <a name="vwcontractproperty"></a>vwContractProperty

Ez a nézet a legtöbb olyan szerződés társított tulajdonságok kapcsolatos adatokat jelöli, és általános jelentéskészítési forgatókönyvekhez könnyen megkönnyítésére szolgál. Ebben a nézetben minden egyes végrehajtott tulajdonsághoz a következő adatokat tartalmazza:

-   Társított felügyeltalkalmazás-definíció
-   Társított munkafolyamat-definíció
-   A felhasználó, aki a munkafolyamat telepített részletei
-   Társított intelligens szerződés tulajdonság meghatározása
-   Tulajdonságok értékeit példány
-   Az Eszközállapot-tulajdonságot a szerződés részletei

| Name (Név)                               | Typo          | NULL értékű lehet | Leírás                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító                      | int           | Nem          | Az alkalmazás egyedi azonosítója |
| Alkalmazásnév                    | nvarchar(50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName             | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő neve |
| ApplicationEnabled                 | bit           | Nem          | Annak jelzése, hogy az alkalmazás jelenleg engedélyezve van.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás is megjelennek, mert az adatbázis le van tiltva, kapcsolódó szerződések blokkláncon maradnak, és a említett szerződések vonatkozó adatokat az adatbázisban maradnak.                      |
| WorkflowId                         | int           | Nem          | A munkafolyamat egyedi azonosítója |
| WorkflowName                       | nvarchar(50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName                | nvarchar(255) | Nem          | A munkafolyamat a felhasználói felületen megjelenített neve |
| WorkflowDescription                | nvarchar(255) | Igen         | A munkafolyamat leírása |
| ContractId                         | int           | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus         | int           | Nem          | Azonosítja a kiépítési folyamat a szerződés aktuális állapotát. Lehetséges értékek: <br />0 – a szerződés által az API-t az adatbázis létrejött<br />1 – a szerződés el lett küldve a Főkönyv<br />2 – a szerződés rendelkezik sikerült üzembe helyezni a Főkönyv<br />3-as vagy 4 – a szerződés nem sikerült üzembe helyezni a Főkönyv<br />5 – a szerződés sikeresen települt a Főkönyv <br /><br />1.5-ös verziójának kezdve az értékek 0 – 5 támogatottak. A visszamenőleges kompatibilitás a jelenlegi kiadásban, a nézet **vwContractPropertyV0** érhető el, hogy csak értékek 0 és 2. |
| ContractCodeId                     | int           | Nem          | A kód végrehajtását a szerződés egyedi azonosítója |
| ContractLedgerIdentifier           | nvarchar(255) | Igen         | Egy adott elosztott Főkönyv intelligens szerződések telepített verziójának egyedi azonosítója. Ha például Ethereum. |
| ContractDeployedByUserId           | int           | Nem          | A szerződés központilag telepített a felhasználó egyedi azonosítója |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Igen         | A szerződés telepített a felhasználó utóneve |
| ContractDeployedByUserLastName     | nvarchar(255) | Igen         | A szerződés telepített a felhasználó vezetékneve |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nem          | A szerződés telepített a felhasználó külső azonosítója. Alapértelmezés szerint ezt az Azonosítót a guid-azonosítót fog kérni a az Azure Active Directory konzorcium jelölő |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Igen         | A felhasználó, aki a szerződés telepített e-mail-címe |
| WorkflowPropertyId                 | int           |             | A munkafolyamatok tulajdonság egyedi azonosítója |
| WorkflowPropertyDataTypeId         | int           | Nem          | A tulajdonság adattípusát azonosítója |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Nem          | A tulajdonság adattípusát neve |
| WorkflowPropertyName               | nvarchar(50)  | Nem          | A munkafolyamat-tulajdonság neve |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nem          | A munkafolyamat tulajdonság megjelenített neve |
| WorkflowPropertyDescription        | nvarchar(255) | Igen         | A tulajdonság leírása |
| ContractPropertyValue              | nvarchar(255) | Nem          | A szerződés egy tulajdonság értéke |
| StateName                          | nvarchar(50)  | Igen         | Ez a tulajdonság tartalmazza a szerződés állapotát, hogy az állapot nevét. Ha nem az állapot társítva, értéke null értékű lesz. |
| StateDisplayName                   | nvarchar(255) | Nem          | Ez a tulajdonság tartalmazza az állapot, hogy az állapot nevét. Ha nem az állapot társítva, értéke null értékű lesz. |
| StateValue                         | nvarchar(255) | Igen         | Ez a tulajdonság tartalmazza az állapot, ha az értéke állapota. Ha nem az állapot társítva, értéke null értékű lesz. |

## <a name="vwcontractstate"></a>vwContractState

Ez a nézet egy adott szerződés állapotát kapcsolatos adatokat a legtöbb jelöli, és célja, hogy könnyen megkönnyítik az általános jelentéskészítési forgatókönyvekhez. Ebben a nézetben minden egyes rekord a következő adatokat tartalmazza:

-   Társított felügyeltalkalmazás-definíció
-   Társított munkafolyamat-definíció
-   A felhasználó, aki a munkafolyamat telepített részletei
-   Társított intelligens szerződés tulajdonság meghatározása
-   Az Eszközállapot-tulajdonságot a szerződés részletei

| Name (Név)                               | Typo          | NULL értékű lehet | Leírás                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító                      | int           | Nem          | Az alkalmazás egyedi azonosítója |
| Alkalmazásnév                    | nvarchar(50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName             | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő neve |
| ApplicationEnabled                 | bit           | Nem          | Annak jelzése, hogy az alkalmazás jelenleg engedélyezve van.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás is megjelennek, mert az adatbázis le van tiltva, kapcsolódó szerződések blokkláncon maradnak, és a említett szerződések vonatkozó adatokat az adatbázisban maradnak. |
| WorkflowId                         | int           | Nem          | A munkafolyamat egyedi azonosítója |
| WorkflowName                       | nvarchar(50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName                | nvarchar(255) | Nem          | A felhasználói felületen megjelenített neve |
| WorkflowDescription                | nvarchar(255) | Igen         | A munkafolyamat leírása |
| ContractLedgerImplementationId     | nvarchar(255) | Igen         | Egy adott elosztott Főkönyv intelligens szerződések telepített verziójának egyedi azonosítója. Ha például Ethereum. |
| ContractId                         | int           | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus         | int           | Nem          |Azonosítja a kiépítési folyamat a szerződés aktuális állapotát. Lehetséges értékek: <br />0 – a szerződés által az API-t az adatbázis létrejött<br />1 – a szerződés el lett küldve a Főkönyv<br />2 – a szerződés rendelkezik sikerült üzembe helyezni a Főkönyv<br />3-as vagy 4 – a szerződés nem sikerült üzembe helyezni a Főkönyv<br />5 – a szerződés sikeresen települt a Főkönyv <br /><br />1.5-ös verziójának kezdve az értékek 0 – 5 támogatottak. A visszamenőleges kompatibilitás a jelenlegi kiadásban, a nézet **vwContractStateV0** érhető el, hogy csak értékek 0 és 2. |
| ConnectionId                       | int           | Nem          | A munkafolyamat van telepítve a blockchain-példány egyedi azonosítója |
| ContractCodeId                     | int           | Nem          | A kód végrehajtását a szerződés egyedi azonosítója |
| ContractDeployedByUserId           | int           | Nem          | A szerződés központilag telepített a felhasználó egyedi azonosítója |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nem          | A szerződés telepített a felhasználó külső azonosítója. Alapértelmezés szerint ezt az Azonosítót a guid-azonosítót fog kérni a az Azure Active Directory konzorcium képviselő. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Igen         | A szerződés telepített a felhasználó utóneve |
| ContractDeployedByUserLastName     | nvarchar(255) | Igen         | A szerződés telepített a felhasználó vezetékneve |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Igen         | A felhasználó, aki a szerződés telepített e-mail-címe |
| WorkflowPropertyId                 | int           | Nem          | Egy munkafolyamat tulajdonság egyedi azonosítója |
| WorkflowPropertyDataTypeId         | int           | Nem          | A munkafolyamat tulajdonság adattípusát azonosítója |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Nem          | A munkafolyamat tulajdonság adattípusát neve |
| WorkflowPropertyName               | nvarchar(50)  | Nem          | A munkafolyamat-tulajdonság neve |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nem          | A tulajdonságát a felhasználói felületen megjelenített neve |
| WorkflowPropertyDescription        | nvarchar(255) | Igen         | A tulajdonság leírása |
| ContractPropertyValue              | nvarchar(255) | Nem          | A szerződés tárolja egy tulajdonság értéke |
| StateName                          | nvarchar(50)  | Igen         | Ez a tulajdonság tartalmazza az állapot, ha azt a megjelenítési nevet az állapot. Ha nem az állapot társítva, értéke null értékű lesz. |
| StateDisplayName                   | nvarchar(255) | Nem          | Ez a tulajdonság tartalmazza az állapot, hogy az állapot nevét. Ha nem az állapot társítva, értéke null értékű lesz. |
| StateValue                         | nvarchar(255) | Igen         | Ez a tulajdonság tartalmazza az állapot, ha az értéke állapota. Ha nem az állapot társítva, értéke null értékű lesz. |

## <a name="vwuser"></a>vwUser

Ez a nézet az Azure Blockchain Workbench használatával üzembe helyezett consortium tagok részletesen. Alapértelmezés szerint a adatainak kitöltése a felhasználó a kezdeti kiépítés keresztül.

| Name (Név)               | Typo          | NULL értékű lehet | Leírás                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID (Azonosító)                 | int           | Nem          | A felhasználó egyedi azonosítója |
| ExternalID         | nvarchar(255) | Nem          | A felhasználó külső azonosítója. Alapértelmezés szerint ezt az Azonosítót a jelölő, az Azure Active Directory azonosító a felhasználó GUID azonosítója. |
| ProvisioningStatus | int           | Nem          |Azonosítja a kiépítési folyamat a felhasználó aktuális állapotát. Lehetséges értékek: <br />0 – felhasználó által létrehozott az API-hoz<br />1 – a kulcs társítva a felhasználó az adatbázisban<br />2 – a felhasználó teljes kiépítése |
| FirstName          | nvarchar(50)  | Igen         | A felhasználó utóneve |
| LastName           | nvarchar(50)  | Igen         | A felhasználó vezetékneve |
| E-mail cím       | nvarchar(255) | Igen         | A felhasználó e-mail-címe |

## <a name="vwworkflow"></a>vwWorkflow

Ez a nézet a részletek alapvető munkafolyamat metaadatait, valamint a munkafolyamat funkciók és paraméterek jelöli. Reporting számára kifejlesztett is tartalmaz, az alkalmazás a munkafolyamathoz társított metaadatokat. Ez a nézet a munkafolyamatokat a jelentéskészítés megkönnyítése érdekében több alapul szolgáló tábla adatait tartalmazza. Minden egyes munkafolyamat esetében ez a nézet tartalmazza a következő adatokat:

-   Társított felügyeltalkalmazás-definíció
-   Társított munkafolyamat-definíció
-   Kapcsolódó adatok munkafolyamat állapota

| Name (Név)                              | Typo          | NULL értékű lehet | Leírás                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító                     | int           | Nem          | Az alkalmazás egyedi azonosítója |
| Alkalmazásnév                   | nvarchar(50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName            | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő neve |
| ApplicationEnabled                | bit           | Nem          | Annak jelzése, hogy az alkalmazás engedélyezve van |
| WorkflowId                        | int           | Igen         | Egy munkafolyamat egyedi azonosítója |
| WorkflowName                      | nvarchar(50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName               | nvarchar(255) | Nem          | A felhasználói felületen megjelenített neve |
| WorkflowDescription               | nvarchar(255) | Igen         | A munkafolyamat leírása. |
| WorkflowConstructorFunctionId     | int           | Nem          | A munkafolyamat-funkció, amely a konstruktor a munkafolyamat azonosítóját |
| WorkflowStartStateId              | int           | Nem          | Az állapot egyedi azonosítója |
| WorkflowStartStateName            | nvarchar(50)  | Nem          | Az állapot neve |
| WorkflowStartStateDisplayName     | nvarchar(255) | Nem          | Az állapot a felhasználói felületen megjelenítendő neve |
| WorkflowStartStateDescription     | nvarchar(255) | Igen         | A munkafolyamat-állapot leírása |
| WorkflowStartStateStyle           | nvarchar(50)  | Igen         | Ez az érték azonosítja, hogy a munkafolyamat van ebben az állapotban lévő teljes százalékos |
| WorkflowStartStateValue           | int           | Nem          | Az állapot értékét |
| WorkflowStartStatePercentComplete | int           | Nem          | Egy szöveges leírása, hogyan jelennek meg a felhasználói felületén ebben az állapotban lévő ügyfelek mutatót. Támogatott állapotok a következők: *sikeres* és *hiba* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Ez a nézet a részletek alapvető munkafolyamat metaadatait, valamint a munkafolyamat funkciók és paraméterek jelöli. Reporting számára kifejlesztett is tartalmaz, az alkalmazás a munkafolyamathoz társított metaadatokat. Ez a nézet a munkafolyamatokat a jelentéskészítés megkönnyítése érdekében több alapul szolgáló tábla adatait tartalmazza. Ez a nézet az egyes munkafolyamat-funkció, a következő adatokat tartalmazza:

-   Társított felügyeltalkalmazás-definíció
-   Társított munkafolyamat-definíció
-   A munkafolyamat függvény részletei

| Name (Név)                                 | Typo          | NULL értékű lehet | Leírás                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| Alkalmazásazonosító                        | int           | Nem          | Az alkalmazás egyedi azonosítója |
| Alkalmazásnév                      | nvarchar(50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName               | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő neve |
| ApplicationEnabled                   | bit           | Nem          | Annak jelzése, hogy az alkalmazás engedélyezve van |
| WorkflowId                           | int           | Nem          | Egy munkafolyamat egyedi azonosítója |
| WorkflowName                         | nvarchar(50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName                  | nvarchar(255) | Nem          | A munkafolyamat a felhasználói felületen megjelenített neve |
| WorkflowDescription                  | nvarchar(255) | Igen         | A munkafolyamat leírása |
| WorkflowFunctionId                   | int           | Nem          | Egy függvény egyedi azonosítója |
| WorkflowFunctionName                 | nvarchar(50)  | Igen         | A függvény neve |
| WorkflowFunctionDisplayName          | nvarchar(255) | Nem          | Egy függvény a felhasználói felületen megjelenítendő neve |
| WorkflowFunctionDescription          | nvarchar(255) | Igen         | A munkafolyamat-funkció leírása |
| WorkflowFunctionIsConstructor        | bit           | Nem          | Ha a munkafolyamat-funkció a konstruktor a munkafolyamat azonosítja |
| WorkflowFunctionParameterId          | int           | Nem          | A függvény egy paramétert egyedi azonosítója |
| WorkflowFunctionParameterName        | nvarchar(50)  | Nem          | A függvénynek egy paraméter neve |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Nem          | Egy függvény paramétere a felhasználói felületen megjelenítendő neve |
| WorkflowFunctionParameterDataTypeId  | int           | Nem          | Az adattípus egy munkafolyamat függvényparaméter társított egyedi azonosítója |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Nem          | Egy munkafolyamat függvényparaméter társított adattípus neve |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Ez a nézet a munkafolyamat megadott tulajdonságok jelöli. Ebben a nézetben minden tulajdonsághoz a következő adatokat tartalmazza:

-   Társított felügyeltalkalmazás-definíció
-   Társított munkafolyamat-definíció
-   A munkafolyamat tulajdonságainak részleteit

| Name (Név)                         | Typo          | NULL értékű lehet | Leírás                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító                | int           | Nem          | Az alkalmazás egyedi azonosítója |
| Alkalmazásnév              | nvarchar(50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName       | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő neve |
| ApplicationEnabled           | bit           | Nem          | Annak jelzése, hogy az alkalmazás jelenleg engedélyezve van.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás is megjelennek, mert az adatbázis le van tiltva, kapcsolódó szerződések blokkláncon maradnak, és a említett szerződések vonatkozó adatokat az adatbázisban maradnak. |
| WorkflowId                   | int           | Nem          | A munkafolyamat egyedi azonosítója |
| WorkflowName                 | nvarchar(50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName          | nvarchar(255) | Nem          | A munkafolyamat a felhasználói felületen megjelenítendő neve |
| WorkflowDescription          | nvarchar(255) | Igen         | A munkafolyamat leírása |
| WorkflowPropertyID           | int           | Nem          | A munkafolyamatok tulajdonság egyedi azonosítója |
| WorkflowPropertyName         | nvarchar(50)  | Nem          | A tulajdonság neve |
| WorkflowPropertyDescription  | nvarchar(255) | Igen         | A tulajdonság leírása |
| WorkflowPropertyDisplayName  | nvarchar(255) | Nem          | A felhasználói felületen megjelenítendő neve |
| WorkflowPropertyWorkflowId   | int           | Nem          | A munkafolyamat, amelyhez ez a tulajdonság nem azonosítója |
| WorkflowPropertyDataTypeId   | int           | Nem          | A tulajdonság definiált azonosítója |
| WorkflowPropertyDataTypeName | nvarchar(50)  | Nem          | A tulajdonság definiált neve |
| WorkflowPropertyIsState      | bit           | Nem          | Ez a mező annak jelzése, hogy ez a munkafolyamat tulajdonság tartalmazza a munkafolyamat állapotát |

## <a name="vwworkflowstate"></a>vwWorkflowState

Ez a nézet egy munkafolyamathoz társított tulajdonságokat jelöli. Ebben a nézetben minden szerződés, a következő adatokat tartalmazza:

-   Társított felügyeltalkalmazás-definíció
-   Társított munkafolyamat-definíció
-   Munkafolyamat-állapot adatainak

| Name (Név)                         | Typo          | NULL értékű lehet | Leírás                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alkalmazásazonosító                | int           | Nem          | Az alkalmazás egyedi azonosítója |
| Alkalmazásnév              | nvarchar(50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName       | nvarchar(255) | Nem          | Az alkalmazás leírása |
| ApplicationEnabled           | bit           | Nem          | Annak jelzése, hogy az alkalmazás jelenleg engedélyezve van.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás is megjelennek, mert az adatbázis le van tiltva, kapcsolódó szerződések blokkláncon maradnak, és a említett szerződések vonatkozó adatokat az adatbázisban maradnak. |
| WorkflowId                   | int           | Nem          | A munkafolyamat egyedi azonosítója |
| WorkflowName                 | nvarchar(50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName          | nvarchar(255) | Nem          | A felhasználói felület, a munkafolyamat megjelenített neve |
| WorkflowDescription          | nvarchar(255) | Igen         | A munkafolyamat leírása |
| WorkflowStateID              | int           | Nem          | Az állapot egyedi azonosítója |
| WorkflowStateName            | nvarchar(50)  | Nem          | Az állapot neve |
| WorkflowStateDisplayName     | nvarchar(255) | Nem          | Az állapot a felhasználói felületen megjelenítendő neve |
| WorkflowStateDescription     | nvarchar(255) | Igen         | A munkafolyamat-állapot leírása |
| WorkflowStatePercentComplete | int           | Nem          | Ez az érték azonosítja, hogy a munkafolyamat van ebben az állapotban lévő teljes százalékos |
| WorkflowStateValue           | nvarchar(50)  | Nem          | Az állapot értékét |
| WorkflowStateStyle           | nvarchar(50)  | Nem          | Egy szöveges leírása, hogyan jelennek meg a felhasználói felületén ebben az állapotban lévő ügyfelek mutatót. Támogatott állapotok a következők: *sikeres* és *hiba* |
