---
title: Adatbázis-nézetek az Azure Blockchain Workbench előzetes verziójában
description: Az Azure Blockchain Workbench előzetes verziójának SQL adatbázis-nézeteinek áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 7548d460d0d99642d11e4eb5755730400b509e94
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845036"
---
# <a name="database-views-in-azure-blockchain-workbench-preview"></a>Adatbázis-nézetek az Azure Blockchain Workbench előzetes verziójában

Az Azure Blockchain Workbench előzetes verziója elosztott főkönyvből származó adatokon *kívüli* SQL db-adatbázisba kézbesíti az adatait. A láncon kívüli adatbázis lehetővé teszi az SQL és a meglévő eszközök, például a [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)használatát a blockchain-alapú adatkezeléshez.

Az Azure Blockchain Workbench olyan adatbázis-nézeteket biztosít, amelyek hozzáférést biztosítanak a lekérdezések végrehajtásakor hasznos adatokhoz. Ezek a nézetek nagy mértékben normalizálva vannak, így könnyen megkezdheti a jelentések létrehozását, elemzését és más módon történő felhasználását a meglévő eszközökkel, és anélkül, hogy blockchain az adatbázis-munkatársakat.

Ez a szakasz áttekintést nyújt az adatbázis-nézetekről és az általuk tartalmazott adatmennyiségekről.

> [!NOTE]
> A következő nézeteken kívül található adatbázis-táblák közvetlen használata nem támogatott.
>

## <a name="vwapplication"></a>vwApplication

Ez a nézet az Azure Blockchain Workbench szolgáltatásba feltöltött **alkalmazások** részleteit tartalmazza.

| Name (Név)                             | Type          | Lehet null | Leírás                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                  | nvarchar (50)  | Nem          | Az alkalmazás neve |
| ApplicationDescription           | nvarchar (255) | Igen         | Az alkalmazás leírása |
| ApplicationDisplayName           | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név |
| ApplicationEnabled               | bit           | Nem          | Annak azonosítása, hogy az alkalmazás jelenleg engedélyezve van-e<br /> **Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltottként jelenik meg az adatbázisban, a társított szerződések megmaradnak a blockchain, és a szerződések adatai továbbra is az adatbázisban maradnak. |
| UploadedDtTm                     | datetime2 (7)  | Nem          | A szerződés feltöltésének dátuma és időpontja |
| UploadedByUserId                 | int           | Nem          | Az alkalmazást feltöltő felhasználó azonosítója |
| UploadedByUserExternalId         | nvarchar (255) | Nem          | Az alkalmazást feltöltő felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a konzorcium Azure Active Directory felhasználója.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Nem          | A felhasználó kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a felhasználót az API hozta létre<br />1 – a felhasználóhoz társított kulcs az adatbázisban<br />2 – a felhasználó teljes mértékben kiépítve                         |
| UploadedByUserFirstName          | nvarchar (50)  | Igen         | A szerződést feltölthető felhasználó vezetékneve |
| UploadedByUserLastName           | nvarchar (50)  | Igen         | A szerződést feltöltő felhasználó vezetékneve |
| UploadedByUserEmailAddress       | nvarchar (255) | Igen         | A szerződést feltöltött felhasználó e-mail-címe |

## <a name="vwapplicationrole"></a>vwApplicationRole

Ez a nézet az Azure Blockchain Workbench alkalmazásokban definiált szerepkörökkel kapcsolatos részleteket tartalmazza.

Egy *Asset-átadási* alkalmazásban például megadhatók szerepkörök, például a *vásárlói* és az *értékesítői* szerepkörök.

| Name (Név)                   | Type             | Lehet null | Leírás                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Nem          | Az alkalmazás egyedi azonosítója           |
| ApplicationName        | nvarchar (50)     | Nem          | Az alkalmazás neve                       |
| ApplicationDescription | nvarchar (255)    | Igen         | Az alkalmazás leírása                  |
| ApplicationDisplayName | nvarchar (255)    | Nem          | A felhasználói felületen megjelenítendő név      |
| RoleId                 | int              | Nem          | Egy szerepkör egyedi azonosítója az alkalmazásban |
| RoleName               | nvarchar50)      | Nem          | A szerepkör neve                              |
| RoleDescription        | Leírás (255) | Igen         | A szerepkör leírása                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Ez a nézet részletesen ismerteti az Azure Blockchain Workbench-alkalmazásokban definiált szerepköröket és a velük társított felhasználókat.

Egy *Asset* -átviteli alkalmazásban például *John Smith* lehet társítva a beszerzői szerepkörhöz.

| Name (Név)                       | Type          | Lehet null | Leírás                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Nem          | Az alkalmazás egyedi azonosítója                                                                                                                                                                                               |
| ApplicationName            | nvarchar (50)  | Nem          | Az alkalmazás neve                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar (255) | Igen         | Az alkalmazás leírása                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Nem          | Egy szerepkör egyedi azonosítója az alkalmazásban                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Nem          | A szerepkör neve                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar (255) | Igen         | A szerepkör leírása                                                                                                                                                                                                             |
| UserId                     | int           | Nem          | A szerepkörhöz társított felhasználó azonosítója |
| UserExternalId             | nvarchar (255) | Nem          | A szerepkörhöz társított felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a konzorcium Azure Active Directory felhasználója.                                                                     |
| UserProvisioningStatus     | int           | Nem          | A felhasználó kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a felhasználót az API hozta létre<br />1 – a felhasználóhoz társított kulcs az adatbázisban<br />2 – a felhasználó teljes mértékben kiépítve |
| UserFirstName              | nvarchar (50)  | Igen         | A szerepkörhöz társított felhasználó vezetékneve |
| UserLastName               | nvarchar (255) | Igen         | A szerepkörhöz társított felhasználó vezetékneve |
| UserEmailAddress           | nvarchar (255) | Igen         | A szerepkörhöz társított felhasználó e-mail-címe |

## <a name="vwconnectionuser"></a>vwConnectionUser

Ez a nézet az Azure Blockchain Workbench és a hozzájuk társított felhasználók által meghatározott kapcsolatok részleteit tartalmazza. Az egyes kapcsolatok esetében ez a nézet a következő adatforrásokat tartalmazza:

-   Társított Főkönyv részletei
-   Társított felhasználói adatok

| Name (Név)                     | Type          | Lehet null | Leírás                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Nem          | A kapcsolatok egyedi azonosítója az Azure Blockchain Workbenchben |
| ConnectionEndpointUrl    | nvarchar (50)  | Nem          | A kapcsolatok végponti URL-címe |
| ConnectionFundingAccount | nvarchar (255) | Igen         | A kapcsolatokhoz társított finanszírozási fiók, ha van ilyen |
| LedgerId                 | int           | Nem          | A Főkönyv egyedi azonosítója |
| LedgerName               | nvarchar (50)  | Nem          | A Főkönyv neve |
| LedgerDisplayName        | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő Főkönyv neve |
| UserId                   | int           | Nem          | A kapcsolódó felhasználó azonosítója |
| UserExternalId           | nvarchar (255) | Nem          | A kapcsolatban a szolgáltatáshoz társított felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a konzorcium Azure Active Directory felhasználója. |
| UserProvisioningStatus   | int           | Nem          |A felhasználó kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a felhasználót az API hozta létre<br />1 – a felhasználóhoz társított kulcs az adatbázisban<br />2 – a felhasználó teljes mértékben kiépítve |
| UserFirstName            | nvarchar (50)  | Igen         | Annak a felhasználónak a vezetékneve, aki társítva van a kapcsolatban |
| UserLastName             | nvarchar (255) | Igen         | A szolgáltatáshoz társított felhasználó vezetékneve |
| UserEmailAddress         | nvarchar (255) | Igen         | A szolgáltatáshoz társított felhasználó e-mail-címe |

## <a name="vwcontract"></a>vwContract

Ez a nézet a központilag telepített szerződések részleteit tartalmazza. Az egyes szerződések esetében ez a nézet a következő adatkészleteket tartalmazza:

-   Társított alkalmazás definíciója
-   Társított munkafolyamat-definíció
-   A függvényhez társított Főkönyv implementációja
-   A műveletet kezdeményező felhasználó adatai
-   A blockchain blokkhoz és tranzakcióhoz kapcsolódó részletek

| Name (Név)                                     | Type           | Lehet null | Leírás                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Nem          | A kapcsolatok egyedi azonosítója az Azure Blockchain Workbenchben.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar (50)   | Nem          | A kapcsolatok végponti URL-címe |
| ConnectionFundingAccount                 | nvarchar (255)  | Igen         | A kapcsolatokhoz társított finanszírozási fiók, ha van ilyen |
| LedgerId                                 | int            | Nem          | A Főkönyv egyedi azonosítója |
| LedgerName                               | nvarchar (50)   | Nem          | A Főkönyv neve |
| LedgerDisplayName                        | nvarchar (255)  | Nem          | A felhasználói felületen megjelenítendő Főkönyv neve |
| ApplicationId                            | int            | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                          | nvarchar (50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName                   | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név |
| ApplicationEnabled                       | bit            | Nem          | Meghatározza, hogy az alkalmazás jelenleg engedélyezve van-e.<br /> **Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltottként jelenik meg az adatbázisban, a társított szerződések megmaradnak a blockchain, és a szerződések adatai továbbra is az adatbázisban maradnak.  |
| WorkflowId                               | int            | Nem          | Egy szerződéshez társított munkafolyamat egyedi azonosítója |
| WorkflowName                             | nvarchar (50)   | Nem          | A szerződéshez társított munkafolyamat neve |
| WorkflowDisplayName                      | nvarchar (255)  | Nem          | A felhasználói felületen megjelenített szerződéshez társított munkafolyamat neve |
| WorkflowDescription                      | nvarchar (255)  | Igen         | A szerződéshez társított munkafolyamat leírása |
| ContractCodeId                           | int            | Nem          | A szerződéshez társított szerződés kódjának egyedi azonosítója |
| ContractFileName                         | int            | Nem          | A munkafolyamat intelligens szerződési kódját tartalmazó fájl neve. |
| ContractUploadedDtTm                     | int            | Nem          | A szerződéssablon feltöltésének dátuma és időpontja |
| ContractId                               | int            | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus               | int            | Nem          | A szerződés kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a szerződést az API hozta létre az adatbázisban<br />1 – a rendszer elküldje a szerződést a főkönyvbe<br />2 – a szerződés sikeresen telepítve lett a főkönyvben<br />3 vagy 4 – a szerződést nem sikerült központilag telepíteni a főkönyvbe<br />5 – a szerződés sikeresen telepítve lett a főkönyvben <br /><br />Az 1,5-es verziótól kezdődően a 0 – 5 értékek támogatottak. A jelenlegi kiadásban a visszamenőleges kompatibilitás érdekében a **vwContractV0** olyan nézet érhető el, amely csak a 0 – 2 értékeket támogatja. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | A szerződést telepítő felhasználó e-mail-címe |
| ContractDeployedByUserId                 | int            | Nem          | A szerződést telepítő felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a felhasználó Azure Active Directory AZONOSÍTÓját jelképező GUID.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar (255)  | Nem          | A szerződést telepítő felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a felhasználó Azure Active Directory AZONOSÍTÓját jelképező GUID.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Nem          | A felhasználó kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a felhasználót az API hozta létre<br />1 – a felhasználóhoz társított kulcs az adatbázisban <br />2 – a felhasználó teljes mértékben kiépítve                     |
| ContractDeployedByUserFirstName          | nvarchar (50)   | Igen         | A szerződést telepítő felhasználó vezetékneve |
| ContractDeployedByUserLastName           | nvarchar (255)  | Igen         | A szerződést telepítő felhasználó vezetékneve |
| ContractDeployedByUserEmailAddress       | nvarchar (255)  | Igen         | A szerződést telepítő felhasználó e-mail-címe |

## <a name="vwcontractaction"></a>vwContractAction

Ez a nézet a szerződésekben végrehajtott műveletekkel kapcsolatos információk többségét jelöli, és úgy van kialakítva, hogy könnyen elősegítse a gyakori jelentési forgatókönyveket. Az összes végrehajtott művelet esetében ez a nézet a következő adatértékeket tartalmazza:

-   Társított alkalmazás definíciója
-   Társított munkafolyamat-definíció
-   Társított intelligens szerződési függvény és paraméter meghatározása
-   A függvényhez társított Főkönyv implementációja
-   Paraméterekhez megadott példányok megadott értékei
-   A műveletet kezdeményező felhasználó adatai
-   A blockchain blokkhoz és tranzakcióhoz kapcsolódó részletek

| Name (Név)                                     | Type          | Lehet null | Leírás                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                          | nvarchar (50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName                   | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név |
| ApplicationEnabled                       | bit           | Nem          | Ez a mező azonosítja, hogy az alkalmazás jelenleg engedélyezve van-e. Megjegyzés – annak ellenére, hogy egy alkalmazás letiltottként jelenik meg az adatbázisban, a társított szerződések megmaradnak a blockchain, és a szerződések adatai az adatbázisban maradnak.                                                  |
| WorkflowId                               | int           | Nem          | Egy munkafolyamat egyedi azonosítója |
| WorkflowName                             | nvarchar (50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName                      | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő munkafolyamat neve |
| WorkflowDescription                      | nvarchar (255) | Igen         | A munkafolyamat leírása |
| ContractId                               | int           | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus               | int           | Nem          | A szerződés kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a szerződést az API hozta létre az adatbázisban<br />1 – a rendszer elküldje a szerződést a főkönyvbe<br />2 – a szerződés sikeresen telepítve lett a főkönyvben<br />3 vagy 4 – a szerződést nem sikerült központilag telepíteni a főkönyvbe<br />5 – a szerződés sikeresen telepítve lett a főkönyvben <br /><br />Az 1,5-es verziótól kezdődően a 0 – 5 értékek támogatottak. A jelenlegi kiadásban a visszamenőleges kompatibilitás érdekében a **vwContractActionV0** olyan nézet érhető el, amely csak a 0 – 2 értékeket támogatja. |
| ContractCodeId                           | int           | Nem          | A szerződés programkódjának megvalósítására szolgáló egyedi azonosító |
| ContractLedgerIdentifier                 | nvarchar (255) | Igen         | Egy adott elosztott Főkönyv esetében az intelligens szerződés telepített verziójához társított egyedi azonosító. Például: Ethereum. |
| ContractDeployedByUserId                 | int           | Nem          | A szerződést telepítő felhasználó egyedi azonosítója |
| ContractDeployedByUserFirstName          | nvarchar (50)  | Igen         | A szerződést telepítő felhasználó vezetékneve |
| ContractDeployedByUserLastName           | nvarchar (255) | Igen         | A szerződést telepítő felhasználó vezetékneve |
| ContractDeployedByUserExternalId         | nvarchar (255) | Nem          | A szerződést telepítő felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító az identitását jelképező GUID a Consortium Azure Active Directoryban.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar (255) | Igen         | A szerződést telepítő felhasználó e-mail-címe |
| WorkflowFunctionId                       | int           | Nem          | Egy munkafolyamat-függvény egyedi azonosítója |
| WorkflowFunctionName                     | nvarchar (50)  | Nem          | A függvény neve |
| WorkflowFunctionDisplayName              | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő függvény neve |
| WorkflowFunctionDescription              | nvarchar (255) | Nem          | A függvény leírása |
| ContractActionId                         | int           | Nem          | Egy szerződéses művelet egyedi azonosítója |
| ContractActionProvisioningStatus         | int           | Nem          | A szerződési művelet kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a szerződési műveletet az API hozta létre az adatbázisban.<br />1 – a szerződés művelet el lett küldve a főkönyvbe<br />2 – a rendszer sikeresen telepítette a szerződés műveletét a főkönyvbe<br />3 vagy 4 – a szerződést nem sikerült központilag telepíteni a főkönyvbe<br />5 – a szerződés sikeresen telepítve lett a főkönyvben <br /><br />Az 1,5-es verziótól kezdődően a 0 – 5 értékek támogatottak. A jelenlegi kiadásban a visszamenőleges kompatibilitás érdekében a **vwContractActionV0** olyan nézet érhető el, amely csak a 0 – 2 értékeket támogatja. |
| ContractActionTimestamp                  | dátum és idő (2, 7) | Nem          | A szerződési művelet időbélyege |
| ContractActionExecutedByUserId           | int           | Nem          | A szerződés műveletét futtató felhasználó egyedi azonosítója |
| ContractActionExecutedByUserFirstName    | int           | Igen         | A szerződés műveletét végrehajtó felhasználó keresztneve |
| ContractActionExecutedByUserLastName     | nvarchar (50)  | Igen         | A szerződéses műveletet végrehajtó felhasználó vezetékneve |
| ContractActionExecutedByUserExternalId   | nvarchar (255) | Igen         | A szerződés műveletét végrehajtó felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító az identitását jelképező GUID a Consortium Azure Active Directoryban. |
| ContractActionExecutedByUserEmailAddress | nvarchar (255) | Igen         | A szerződés műveletét végrehajtó felhasználó e-mail-címe |
| WorkflowFunctionParameterId              | int           | Nem          | A függvény paraméterének egyedi azonosítója |
| WorkflowFunctionParameterName            | nvarchar (50)  | Nem          | A függvény paraméterének neve |
| WorkflowFunctionParameterDisplayName     | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő Function paraméter neve |
| WorkflowFunctionParameterDataTypeId      | int           | Nem          | A munkafolyamat-függvény paraméteréhez társított adattípus egyedi azonosítója |
| WorkflowParameterDataTypeName            | nvarchar (50)  | Nem          | Egy munkafolyamat-függvény paraméteréhez társított adattípus neve |
| ContractActionParameterValue             | nvarchar (255) | Nem          | Az intelligens szerződésben tárolt paraméter értéke |
| blockHash                                | nvarchar (255) | Igen         | A blokk kivonata |
| blockNumber                              | int           | Igen         | A naplóban lévő blokk száma |
| blockTimestamp                           | dátum és idő (2, 7) | Igen         | A blokk időbélyegzője |
| TransactionId                            | int           | Nem          | A tranzakció egyedi azonosítója |
| TransactionFrom                          | nvarchar (255) | Igen         | A tranzakciót kezdeményező fél |
| TransactionTo                            | nvarchar (255) | Igen         | Az a fél, amelyet a |
| transactionHash                          | nvarchar (255) | Igen         | Egy tranzakció kivonata |
| TransactionIsWorkbenchTransaction        | bit           | Igen         | Egy kicsit, amely meghatározza, hogy a tranzakció Azure Blockchain Workbench-tranzakció-e |
| TransactionProvisioningStatus            | int           | Igen         | A tranzakció kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a tranzakciót az API hozta létre az adatbázisban.<br />1 – a tranzakció elküldése a főkönyvbe<br />2 – a tranzakció sikeresen telepítve lett a főkönyvben                 |
| TransactionValue                         | decimális (32, 2) | Igen         | A tranzakció értéke |

## <a name="vwcontractproperty"></a>vwContractProperty

Ez a nézet a szerződéshez kapcsolódó tulajdonságokkal kapcsolatos információk többségét jelöli, és úgy van kialakítva, hogy könnyen elősegítse a gyakori jelentéskészítési forgatókönyveket. Az egyes elkészített tulajdonságok esetében ez a nézet a következő adatértékeket tartalmazza:

-   Társított alkalmazás definíciója
-   Társított munkafolyamat-definíció
-   A munkafolyamatot telepítő felhasználó adatai
-   Társított intelligens szerződés tulajdonságának definíciója
-   A tulajdonságok adott példányának értékei
-   A szerződés State (állapot) tulajdonságának részletei

| Name (Név)                               | Type          | Lehet null | Leírás                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                    | nvarchar (50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName             | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név |
| ApplicationEnabled                 | bit           | Nem          | Meghatározza, hogy az alkalmazás jelenleg engedélyezve van-e.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltottként jelenik meg az adatbázisban, a társított szerződések megmaradnak a blockchain, és a szerződések adatai továbbra is az adatbázisban maradnak.                      |
| WorkflowId                         | int           | Nem          | A munkafolyamat egyedi azonosítója |
| WorkflowName                       | nvarchar (50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName                | nvarchar (255) | Nem          | A felhasználói felületen megjelenő munkafolyamat neve |
| WorkflowDescription                | nvarchar (255) | Igen         | A munkafolyamat leírása |
| ContractId                         | int           | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus         | int           | Nem          | A szerződés kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a szerződést az API hozta létre az adatbázisban<br />1 – a rendszer elküldje a szerződést a főkönyvbe<br />2 – a szerződés sikeresen telepítve lett a főkönyvben<br />3 vagy 4 – a szerződést nem sikerült központilag telepíteni a főkönyvbe<br />5 – a szerződés sikeresen telepítve lett a főkönyvben <br /><br />Az 1,5-es verziótól kezdődően a 0 – 5 értékek támogatottak. A jelenlegi kiadásban a visszamenőleges kompatibilitás érdekében a **vwContractPropertyV0** olyan nézet érhető el, amely csak a 0 – 2 értékeket támogatja. |
| ContractCodeId                     | int           | Nem          | A szerződés programkódjának megvalósítására szolgáló egyedi azonosító |
| ContractLedgerIdentifier           | nvarchar (255) | Igen         | Egy adott elosztott Főkönyv esetében az intelligens szerződés telepített verziójához társított egyedi azonosító. Például: Ethereum. |
| ContractDeployedByUserId           | int           | Nem          | A szerződést telepítő felhasználó egyedi azonosítója |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Igen         | A szerződést telepítő felhasználó vezetékneve |
| ContractDeployedByUserLastName     | nvarchar (255) | Igen         | A szerződést telepítő felhasználó vezetékneve |
| ContractDeployedByUserExternalId   | nvarchar (255) | Nem          | A szerződést telepítő felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító annak a GUID azonosítónak felel meg, amely az identitását jelöli a konzorciumban Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Igen         | A szerződést telepítő felhasználó e-mail-címe |
| workflowPropertyId                 | int           |             | Egy munkafolyamat egy tulajdonságának egyedi azonosítója |
| WorkflowPropertyDataTypeId         | int           | Nem          | A tulajdonság adattípusának azonosítója |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Nem          | A tulajdonság adattípusának neve |
| WorkflowPropertyName               | nvarchar (50)  | Nem          | A munkafolyamat-tulajdonság neve |
| WorkflowPropertyDisplayName        | nvarchar (255) | Nem          | A munkafolyamat-tulajdonság megjelenítendő neve |
| WorkflowPropertyDescription        | nvarchar (255) | Igen         | A tulajdonság leírása |
| ContractPropertyValue              | nvarchar (255) | Nem          | A szerződés egyik tulajdonságának értéke |
| StateName                          | nvarchar (50)  | Igen         | Ha ez a tulajdonság a szerződés állapotát tartalmazza, akkor az állapot megjelenített neve. Ha nincs társítva az állapothoz, az érték null lesz. |
| StateDisplayName                   | nvarchar (255) | Nem          | Ha ez a tulajdonság tartalmazza az állapotot, akkor az állapot megjelenített neve. Ha nincs társítva az állapothoz, az érték null lesz. |
| StateValue                         | nvarchar (255) | Igen         | Ha ez a tulajdonság tartalmazza az állapotot, az állapot értéke. Ha nincs társítva az állapothoz, az érték null lesz. |

## <a name="vwcontractstate"></a>vwContractState

Ez a nézet az adott szerződés állapotával kapcsolatos információk többségét jelöli, és az általános jelentéskészítési forgatókönyvek egyszerű megkönnyítésére szolgál. Az ebben a nézetben szereplő összes rekord a következő adatokból áll:

-   Társított alkalmazás definíciója
-   Társított munkafolyamat-definíció
-   A munkafolyamatot telepítő felhasználó adatai
-   Társított intelligens szerződés tulajdonságának definíciója
-   A szerződés State (állapot) tulajdonságának részletei

| Name (Név)                               | Type          | Lehet null | Leírás                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                    | nvarchar (50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName             | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név |
| ApplicationEnabled                 | bit           | Nem          | Meghatározza, hogy az alkalmazás jelenleg engedélyezve van-e.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltottként jelenik meg az adatbázisban, a társított szerződések megmaradnak a blockchain, és a szerződések adatai továbbra is az adatbázisban maradnak. |
| WorkflowId                         | int           | Nem          | A munkafolyamat egyedi azonosítója |
| WorkflowName                       | nvarchar (50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName                | nvarchar (255) | Nem          | A felhasználói felületen megjelenő név |
| WorkflowDescription                | nvarchar (255) | Igen         | A munkafolyamat leírása |
| ContractLedgerImplementationId     | nvarchar (255) | Igen         | Egy adott elosztott Főkönyv esetében az intelligens szerződés telepített verziójához társított egyedi azonosító. Például: Ethereum. |
| ContractId                         | int           | Nem          | A szerződés egyedi azonosítója |
| ContractProvisioningStatus         | int           | Nem          |A szerződés kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a szerződést az API hozta létre az adatbázisban<br />1 – a rendszer elküldje a szerződést a főkönyvbe<br />2 – a szerződés sikeresen telepítve lett a főkönyvben<br />3 vagy 4 – a szerződést nem sikerült központilag telepíteni a főkönyvbe<br />5 – a szerződés sikeresen telepítve lett a főkönyvben <br /><br />Az 1,5-es verziótól kezdődően a 0 – 5 értékek támogatottak. A jelenlegi kiadásban a visszamenőleges kompatibilitás érdekében a **vwContractStateV0** olyan nézet érhető el, amely csak a 0 – 2 értékeket támogatja. |
| ConnectionId                       | int           | Nem          | Annak a blockchain-példánynak az egyedi azonosítója, amelyre a munkafolyamat telepítve van |
| ContractCodeId                     | int           | Nem          | A szerződés programkódjának megvalósítására szolgáló egyedi azonosító |
| ContractDeployedByUserId           | int           | Nem          | A szerződést telepítő felhasználó egyedi azonosítója |
| ContractDeployedByUserExternalId   | nvarchar (255) | Nem          | A szerződést telepítő felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító az identitását jelképező GUID a Consortium Azure Active Directoryban. |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Igen         | A szerződést telepítő felhasználó vezetékneve |
| ContractDeployedByUserLastName     | nvarchar (255) | Igen         | A szerződést telepítő felhasználó vezetékneve |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Igen         | A szerződést telepítő felhasználó e-mail-címe |
| workflowPropertyId                 | int           | Nem          | Egy munkafolyamat-tulajdonság egyedi azonosítója |
| WorkflowPropertyDataTypeId         | int           | Nem          | A munkafolyamat-tulajdonság adattípusának azonosítója |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Nem          | A munkafolyamat-tulajdonság adattípusának neve |
| WorkflowPropertyName               | nvarchar (50)  | Nem          | A munkafolyamat-tulajdonság neve |
| WorkflowPropertyDisplayName        | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő tulajdonság megjelenített neve |
| WorkflowPropertyDescription        | nvarchar (255) | Igen         | A tulajdonság leírása |
| ContractPropertyValue              | nvarchar (255) | Nem          | A szerződésben tárolt tulajdonság értéke |
| StateName                          | nvarchar (50)  | Igen         | Ha ez a tulajdonság tartalmazza az állapotot, akkor az állapot megjelenített neve. Ha nincs társítva az állapothoz, az érték null lesz. |
| StateDisplayName                   | nvarchar (255) | Nem          | Ha ez a tulajdonság tartalmazza az állapotot, akkor az állapot megjelenített neve. Ha nincs társítva az állapothoz, az érték null lesz. |
| StateValue                         | nvarchar (255) | Igen         | Ha ez a tulajdonság tartalmazza az állapotot, az állapot értéke. Ha nincs társítva az állapothoz, az érték null lesz. |

## <a name="vwuser"></a>vwUser

Ez a nézet az Azure Blockchain Workbench használatára kiépített konzorciumi tagok részleteit tartalmazza. Alapértelmezés szerint a rendszer az adatokat a felhasználó kezdeti kiosztása alapján tölti ki.

| Name (Név)               | Type          | Lehet null | Leírás                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id                 | int           | Nem          | Egy felhasználó egyedi azonosítója |
| externalID         | nvarchar (255) | Nem          | Egy felhasználó külső azonosítója. Alapértelmezés szerint ez az azonosító a felhasználó Azure Active Directory AZONOSÍTÓját jelképező GUID. |
| provisioningStatus | int           | Nem          |A felhasználó kiépítési folyamatának aktuális állapotát azonosítja. Lehetséges értékek a következők: <br />0 – a felhasználót az API hozta létre<br />1 – a felhasználóhoz társított kulcs az adatbázisban<br />2 – a felhasználó teljes mértékben kiépítve |
| FirstName          | nvarchar (50)  | Igen         | A felhasználó vezetékneve |
| LastName           | nvarchar (50)  | Igen         | A felhasználó vezetékneve |
| EmailAddress       | nvarchar (255) | Igen         | A felhasználó e-mail-címe |

## <a name="vwworkflow"></a>vwWorkflow

Ez a nézet a részletek központi munkafolyamatának metaadatait, valamint a munkafolyamat funkcióit és paramétereit mutatja be. A jelentéskészítéshez tervezve a munkafolyamathoz társított alkalmazás metaadatainak is szerepelnek. Ez a nézet több alapul szolgáló táblázatból származó adatokat tartalmaz, amelyek megkönnyítik a munkafolyamatok jelentéskészítését. Az egyes munkafolyamatok esetében ez a nézet a következő fájlokat tartalmazza:

-   Társított alkalmazás definíciója
-   Társított munkafolyamat-definíció
-   Társított munkafolyamat indítási állapotának adatai

| Name (Név)                              | Type          | Lehet null | Leírás                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                   | nvarchar (50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName            | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név |
| ApplicationEnabled                | bit           | Nem          | Annak azonosítása, hogy az alkalmazás engedélyezve van-e |
| WorkflowId                        | int           | Igen         | Egy munkafolyamat egyedi azonosítója |
| WorkflowName                      | nvarchar (50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName               | nvarchar (255) | Nem          | A felhasználói felületen megjelenő név |
| WorkflowDescription               | nvarchar (255) | Igen         | A munkafolyamat leírása. |
| WorkflowConstructorFunctionId     | int           | Nem          | A munkafolyamat konstruktorának kiszolgáló munkafolyamat-függvény azonosítója |
| WorkflowStartStateId              | int           | Nem          | Az állapot egyedi azonosítója |
| WorkflowStartStateName            | nvarchar (50)  | Nem          | Az állapot neve |
| WorkflowStartStateDisplayName     | nvarchar (255) | Nem          | Az állapothoz tartozó felhasználói felületen megjelenítendő név |
| WorkflowStartStateDescription     | nvarchar (255) | Igen         | A munkafolyamat állapotának leírása |
| WorkflowStartStateStyle           | nvarchar (50)  | Igen         | Ez az érték határozza meg a százalékos készültséget, ha a munkafolyamat ebben az állapotban van |
| WorkflowStartStateValue           | int           | Nem          | Az állapot értéke |
| WorkflowStartStatePercentComplete | int           | Nem          | Egy szöveges leírás, amely egy tippet ad az ügyfeleknek az állapot megjelenítéséhez a felhasználói felületen. A támogatott állapotok közé tartozik a sikeres és a *sikertelen* *művelet* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Ez a nézet a részletek központi munkafolyamatának metaadatait, valamint a munkafolyamat funkcióit és paramétereit mutatja be. A jelentéskészítéshez tervezve a munkafolyamathoz társított alkalmazás metaadatainak is szerepelnek. Ez a nézet több alapul szolgáló táblázatból származó adatokat tartalmaz, amelyek megkönnyítik a munkafolyamatok jelentéskészítését. Az egyes munkafolyamat-függvények esetében ez a nézet a következő fájlokat tartalmazza:

-   Társított alkalmazás definíciója
-   Társított munkafolyamat-definíció
-   Munkafolyamat-függvény részletei

| Name (Név)                                 | Type          | Lehet null | Leírás                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName                      | nvarchar (50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName               | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név |
| ApplicationEnabled                   | bit           | Nem          | Annak azonosítása, hogy az alkalmazás engedélyezve van-e |
| WorkflowId                           | int           | Nem          | Egy munkafolyamat egyedi azonosítója |
| WorkflowName                         | nvarchar (50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName                  | nvarchar (255) | Nem          | A felhasználói felületen megjelenő munkafolyamat neve |
| WorkflowDescription                  | nvarchar (255) | Igen         | A munkafolyamat leírása |
| WorkflowFunctionId                   | int           | Nem          | Egy függvény egyedi azonosítója |
| WorkflowFunctionName                 | nvarchar (50)  | Igen         | A függvény neve |
| WorkflowFunctionDisplayName          | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő függvény neve |
| WorkflowFunctionDescription          | nvarchar (255) | Igen         | A munkafolyamat-függvény leírása |
| WorkflowFunctionIsConstructor        | bit           | Nem          | Annak azonosítása, hogy a munkafolyamat-függvény a munkafolyamat konstruktora-e |
| WorkflowFunctionParameterId          | int           | Nem          | Egy függvény paraméterének egyedi azonosítója |
| WorkflowFunctionParameterName        | nvarchar (50)  | Nem          | A függvény paraméterének neve |
| WorkflowFunctionParameterDisplayName | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő Function paraméter neve |
| WorkflowFunctionParameterDataTypeId  | int           | Nem          | A munkafolyamat-függvény paraméteréhez társított adattípus egyedi azonosítója |
| WorkflowParameterDataTypeName        | nvarchar (50)  | Nem          | Egy munkafolyamat-függvény paraméteréhez társított adattípus neve |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Ez a nézet a munkafolyamatokhoz definiált tulajdonságokat jelöli. Minden tulajdonság esetében ez a nézet a következő adatértékeket tartalmazza:

-   Társított alkalmazás definíciója
-   Társított munkafolyamat-definíció
-   Munkafolyamat-tulajdonság részletei

| Name (Név)                         | Type          | Lehet null | Leírás                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName              | nvarchar (50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName       | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név |
| ApplicationEnabled           | bit           | Nem          | Meghatározza, hogy az alkalmazás jelenleg engedélyezve van-e.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltottként jelenik meg az adatbázisban, a társított szerződések megmaradnak a blockchain, és a szerződések adatai továbbra is az adatbázisban maradnak. |
| WorkflowId                   | int           | Nem          | A munkafolyamat egyedi azonosítója |
| WorkflowName                 | nvarchar (50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName          | nvarchar (255) | Nem          | A felhasználói felületen a munkafolyamathoz megjelenítendő név |
| WorkflowDescription          | nvarchar (255) | Igen         | A munkafolyamat leírása |
| WorkflowPropertyID           | int           | Nem          | Egy munkafolyamat egy tulajdonságának egyedi azonosítója |
| WorkflowPropertyName         | nvarchar (50)  | Nem          | A tulajdonság neve |
| WorkflowPropertyDescription  | nvarchar (255) | Igen         | A tulajdonság leírása |
| WorkflowPropertyDisplayName  | nvarchar (255) | Nem          | A felhasználói felületen megjelenítendő név |
| WorkflowPropertyWorkflowId   | int           | Nem          | Annak a munkafolyamatnak az azonosítója, amelyhez ez a tulajdonság társítva van |
| WorkflowPropertyDataTypeId   | int           | Nem          | A tulajdonsághoz megadott adattípus azonosítója |
| WorkflowPropertyDataTypeName | nvarchar (50)  | Nem          | A tulajdonsághoz megadott adattípus neve |
| WorkflowPropertyIsState      | bit           | Nem          | Ez a mező azonosítja, hogy ez a munkafolyamat-tulajdonság tartalmazza-e a munkafolyamat állapotát. |

## <a name="vwworkflowstate"></a>vwWorkflowState

Ez a nézet a munkafolyamathoz társított tulajdonságokat jelöli. Az egyes szerződések esetében ez a nézet a következő adatkészleteket tartalmazza:

-   Társított alkalmazás definíciója
-   Társított munkafolyamat-definíció
-   Munkafolyamat-állapot adatai

| Name (Név)                         | Type          | Lehet null | Leírás                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nem          | Az alkalmazás egyedi azonosítója |
| ApplicationName              | nvarchar (50)  | Nem          | Az alkalmazás neve |
| ApplicationDisplayName       | nvarchar (255) | Nem          | Az alkalmazás leírása |
| ApplicationEnabled           | bit           | Nem          | Meghatározza, hogy az alkalmazás jelenleg engedélyezve van-e.<br />**Megjegyzés:** Annak ellenére, hogy egy alkalmazás letiltottként jelenik meg az adatbázisban, a társított szerződések megmaradnak a blockchain, és a szerződések adatai továbbra is az adatbázisban maradnak. |
| WorkflowId                   | int           | Nem          | A munkafolyamat egyedi azonosítója |
| WorkflowName                 | nvarchar (50)  | Nem          | A munkafolyamat neve |
| WorkflowDisplayName          | nvarchar (255) | Nem          | A munkafolyamat felhasználói felületén megjelenő név |
| WorkflowDescription          | nvarchar (255) | Igen         | A munkafolyamat leírása |
| WorkflowStateID              | int           | Nem          | Az állapot egyedi azonosítója |
| WorkflowStateName            | nvarchar (50)  | Nem          | Az állapot neve |
| WorkflowStateDisplayName     | nvarchar (255) | Nem          | Az állapothoz tartozó felhasználói felületen megjelenítendő név |
| WorkflowStateDescription     | nvarchar (255) | Igen         | A munkafolyamat állapotának leírása |
| WorkflowStatePercentComplete | int           | Nem          | Ez az érték határozza meg a százalékos készültséget, ha a munkafolyamat ebben az állapotban van |
| WorkflowStateValue           | nvarchar (50)  | Nem          | Az állapot értéke |
| WorkflowStateStyle           | nvarchar (50)  | Nem          | Egy szöveges leírás, amely egy tippet ad az ügyfeleknek az állapot megjelenítéséhez a felhasználói felületen. A támogatott állapotok közé tartozik a sikeres és a *sikertelen* *művelet* |
