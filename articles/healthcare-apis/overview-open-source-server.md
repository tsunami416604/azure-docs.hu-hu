---
title: Mi az Azure-ban – Azure FHIR-kiszolgáló FHIR Server
description: Ez a cikk ismerteti a FHIR Server az Azure-hoz.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 20b29ba4c23da90b4941b37d02a1d3f42310dd40
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823513"
---
# <a name="what-is-fhir-server-for-azure"></a>Mi az Azure-ban FHIR Server

FHIR&reg; Server az Azure egy nyílt forráskódú megvalósítása a feltörekvő [HL7 gyors Healthcare együttműködési erőforrások (FHIR) specifikáció](https://www.hl7.org/fhir/) a Microsoft cloud tervezve. A FHIR specifikáció határozza meg, hogyan clinical health adatokat lehet tenni együttműködésre rendszerekből. FHIR Server az Azure segítségével a felhőben, hogy együttműködés megkönnyítése. A Microsoft Healthcare projekt az a célja, hogy a fejlesztők gyorsan üzembe helyezheti a FHIR szolgáltatás.
 
Az adatok a FHIR formátumban, a gyors feldolgozására, és a felhőben, FHIR adatkészletek kezelése az Azure segítségével a fejlesztők FHIR-kiszolgálójának nyomon követéséhez és felügyelt adathozzáférés és normalizálása az adatokat a machine learning számítási feladatokhoz. Az Azure-ökoszisztéma FHIR Server az Azure-hoz optimalizált: 
* Parancsprogramok és ARM-sablonok érhetők el a Microsoft cloud azonnali üzembe helyezés 
* Parancsfájlok érhetők el Azure AAD rendeljen és engedélyezése szerepköralapú hozzáférés-vezérlés (RBAC) 

FHIR Server az Azure-ban van beépített logikai elkülönítése, így a fejlesztők hogyan van megvalósítva, és a lehetőségek bővítése, igény szerint módosíthatja a rugalmas. A logikai rétegek a FHIR-kiszolgáló a következők: 

* Üzemeltetési réteg – támogatja a különböző környezetekben, egyéni konfigurációs vezérlő invertálásának (IoC) a tárolók üzemeltetéséhez. 
* RESTful API-réteg – a HL7 FHIR specifikációja által definiált API-k végrehajtására. 
* Alapvető logikai réteg az alapvető FHIR logika megvalósítását. 
* Adatmegőrző réteget – egy moduláris adatmegőrzés szolgáltató engedélyezése a FHIR kiszolgáló gyakorlatilag bármilyen adatok megőrzését segédprogram csatlakozni. FHIR Server az Azure-hoz egy használatra kész adatmegőrzési-adatszolgáltatója az Azure Cosmos DB (egy globálisan replikált adatbázis-szolgáltatás, amely az adatok széleskörű lekérdezését) tartalmaz. 

FHIR Server az Azure lehetővé teszi a fejlesztők számára – így ha gyorsan FHIR kiszolgáló saját alkalmazásokba integrálásához szükséges időt takarít meg, vagy a cégével őket, amelyen azok szabhatja testre a saját FHIR szolgáltatás épül. Egy nyílt forráskódú projektként hozzájárulások és FHIR fejlesztői visszajelzést továbbra is a projekt javítása érdekében. 

Adatvédelmi és biztonsági felismertük, és a FHIR-kiszolgáló Azure identitáskezelési követelmények védett egészségügyi információ (PHI) támogatásához. Az Azure-szolgáltatásokhoz a FHIR Server az Azure-hoz használt [védett egészségügyi információ a megfelelőségi követelmények teljesítése érdekében](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings).

A nyílt forráskódú projekt teljesen alapját a Microsoft Healthcare csapat, de tudjuk, hogy ez a projekt csak lekérése a visszajelzéseikkel és hozzájárulásaikkal a jobb. Azt a kódbázis fejlesztésének vezetnek, és a buildelési és üzembe helyezési teszt naponta. 