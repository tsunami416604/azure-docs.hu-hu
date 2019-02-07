---
title: Mi az Azure API villámnézethez FHIR - FHIR előzetes verzióra az Azure API-t
description: Ez a cikk ismerteti az Azure API FHIR.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 327c28b70c564f7e5099ada14a0d4653b7f0db6c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823506"
---
# <a name="what-is-azure-api-for-fhirreg-preview"></a>Mi az Azure API-FHIR&reg; előzetes?
Az Azure API-val FHIR lehetővé teszi, hogy a gyors keresztül FHIR API-k, egy felügyelt platformon – szolgáltatásként nyújtott (PaaS) kínál a felhőben készít biztonsági adatok cseréje. Egyszerűbbé teszi a bárki betöltését, egészségügyi adatok használata kezelheti, és megőrizheti a védett egészségügyi információk [Phi-k](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) a felhőben: 

- Felügyelt FHIR-szolgáltatás a felhőben percek alatt kiépíthetőek 
- Nagyvállalati szintű, FHIR®-alapú végpont az Azure-ban, az adatok eléréséhez, és a storage FHIR® formátumban
- Nagy teljesítményű, kis késésű
- Megfelelő felhőalapú környezetet védett egészségügyi-adatok (Phi-k), a biztonságos felügyeletéhez
- INTELLIGENS FHIR a mobil és webes megvalósításainak
- A saját nagy mennyiségű adat szabályozhatja a szerepköralapú hozzáférés-vezérlés (RBAC)
- Napló naplózását hozzáférés, létrehozását, módosítását és olvasási belüli összes adattárat

Az Azure API-val FHIR lehetővé teszi, hogy hozhat létre, és kihasználhatja a felhő rugalmas méretezésű percek alatt FHIR szolgáltatás üzembe helyezése.  Csak kell fizetnie az átviteli sebesség és a storage van szüksége. Gyors teljesítmény, függetlenül attól, milyen méretű adatkészletek kezelése az Azure-szolgáltatásokkal, melyek az Azure API FHIR a lettek kialakítva.  A adatmegőrző réteget FHIR API Azure-ban használ az Azure Cosmos DB, ami késéseket, a 99. percentilis garantál, és magas rendelkezésre állást biztosít többkiszolgálós képességekkel. 

A FHIR API és a megfelelő adattár lehetővé teszi biztonságos kapcsolódást és interakcióba FHIR API-kat a rendszer.  A Microsoft operations, karbantartási, frissítések és a PaaS kínál, és a megfelelőségi követelmények vesz igénybe, így a saját üzemeltetési és fejlesztési erőforrásokat szabadíthat. 

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>**Az adatok FHIR teljesítményét kihasználva**
Az egészségügyi ágazatból áll gyorsan átformálják egészségügyi adatok, melyek az új normál [FHIR&reg; ](https://hl7.org/fhir) (gyors Healthcare együttműködési erőforrások). FHIR lehetővé teszi, hogy egy szabványos szemantika és az exchange, amely lehetővé teszi az összes rendszer FHIR segítségével működik együtt a nagy teljesítményű és bővíthető adatmodellt.  Típusúvá alakítja őket a FHIR lehetővé teszi a gyors csatlakozás meglévő adatforrás, például az elektronikus egészségügyi rekord rendszerek vagy a szintaxisukat adatbázisok. FHIR is lehetővé teszi a gyors adatcsere modern megvalósításokhoz, mobil és webes fejlesztés. Ennél is fontosabb FHIR adatbetöltés leegyszerűsítheti és felgyorsíthatja a fejlesztést az elemzési és gépi tanulási eszközökkel.  

### <a name="securely-manage-health-data-in-the-cloud"></a>**Biztonságosan kezelheti az egészségügyi adatok a felhőben**
A FHIR az Azure API lehetővé teszi, hogy az Exchange-hez, az adatok keresztül konzisztens, RESTful, FHIR API-k alapján a HL7 FHIR-specifikációnak. Ajánlat az Azure-ban felügyelt paas-megoldás által támogatott, azt is méretezhető és biztonságos környezetet biztosít felügyeleti és a natív formátumban FHIR védett egészségügyi információ (PHI) adatok tárolására.  

### <a name="free-up-your-resources-to-innovate"></a>**Szabadítsa fel erőforrásait az innováció**
Sikerült fektet erőforrások létrehozásához és a saját FHIR-szolgáltatást futtató, de a FHIR Azure API-val a Microsoft fogja elvégezni a műveleteket, karbantartási, frissítések és megfelelőségi követelmények, a munkaterhelés lehetővé teszi, hogy ingyenes akár a saját üzemeltetési és fejlesztési az erőforrásokat.

### <a name="enable-interoperability-with-fhir"></a>**Együttműködés a FHIR engedélyezése** 
Lehetővé teszi, hogy minden olyan rendszernél, amely kihasználja a FHIR API-jait az olvasási, írási, keresés és más függvényekkel FHIR az Azure API használatával csatlakozzon.  Használat a hatékony eszközzel, összesítése, normalizálása és gépi tanulási klinikai adatokon elektronikus egészségügyi nyilvántartásokban, klinikust és a betegek irányítópultok, a távoli figyelési programok, vagy FHIR API-kkal rendelkező adatbázisok kívül a rendszer a alkalmazni.

### <a name="control-data-access-at-scale"></a>**Nagy mennyiségű adat elérés**
Szabályozhatja az adatok. Szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi az adatok hogyan tárolásukról és elérésükről kezelése.  Így növelhető a biztonsági, és csökkenti az adminisztratív terhelést, meghatározhatja, hogy ki férhet hozzá az adatkészlethez, a környezetben létrehozott szerepkör-definíciók alapján.  

### <a name="audit-logs-and-tracking"></a>**Vizsgálati naplók és nyomon követése**
Gyorsan nyomon, ahol az adatok a beépített naplók dolgozhat. Nyomon követheti a hozzáférést, létrehozását, módosítását és olvasási belüli összes adattárat.

### <a name="secure-your-data"></a>**Az adatok védelme** 
A Phi-k védelme páratlan biztonsági intelligencia.  Az adatok logikailag egy API-példány egyedi adatbázishoz, és a többrégiós feladatátvétel védett. Az Azure API for FHIR egy rétegzett, részletes védelem és a komplex veszélyforrások elleni védelem az adatok valósítja meg.  

## <a name="applications-for-a-fhir-service"></a>**Alkalmazások FHIR szolgáltatáshoz**
FHIR kiszolgálók egészségügyi adatok együttműködés fő eszközök.  FHIR számára az Azure API célja egy API-t és a szolgáltatás, amely is létrehozhat, üzembe helyezése, és gyorsan használatának megkezdéséhez.  A FHIR standard kibővíti az egészségügyben, használati esetek egyre nő, de az alábbiakban néhány kezdeti ügyfélalkalmazások, ahol az Azure API-FHIR hasznos: 

- **Indítási/IOT- és alkalmazásfejlesztés:**  Az ügyfelek egy páciens vagy a szolgáltató központú alkalmazás fejlesztésével (mobil vagy web) FHIR háttérrendszer teljes körűen felügyelt szolgáltatásként a kihasználhatják az Azure API. FHIR számára az Azure API olyan valuable erőforrás abban, hogy az ügyfelek kezelése az adatok végezheti és adatcsere környezetben biztonságos felhőalapú egészségügyi adatok tervezve, használja ki az INTELLIGENS FHIR implementációs segédlet a, és azok technológiával is használható az összes engedélyezése szolgáltató rendszerek (például a legtöbb EHRs engedélyezte FHIR, olvassa el az API-k).   
- **Egészségügyi rendszerekben teszi elérhetővé:**  Miközben EHRs létezik, és az elsődleges "hiteles forrásaként" számos klinikai beállításaiban, már nem ritka, hogy a szolgáltatók számára, amelyek nem csatlakoznak egy másik, vagy tárolja az adatokat különböző formátumokban az adatbázis.  Az Azure API használatával a FHIR, amely az ezekhez a rendszerekhez felett szolgáltatás lehetővé teszi a FHIR formátumú adatokat szabványosíthatja.  Így lehetővé teszik az alkalmazáskonzisztens adatok formátumú több rendszer között. 

- **Research:** Egészségügyi kutatói lesz található általános FHIR normál és az Azure API FHIR hasznos adatokat tartalmaznak egy olyan közös FHIR-modell normalizálása és csökkenti a számítási feladatok, a machine learning és az adatok megosztása.
Exchange-adatok az Azure API-n keresztül a FHIR biztosít a vizsgálati naplók és hozzáférés-vezérlés a segítségével vezérelheti az adatok, és ki férhet hozzá, hogy milyen típusú adatokat a folyamat. 

## <a name="fhir-from-microsoft"></a>A Microsoft FHIR

A Microsoft FHIR képességek két konfigurációban érhetők el:

* FHIR – A PaaS-ajánlat az Azure-ban, az Azure API-val könnyedén kiépítése az Azure Portalon, és a Microsoft által felügyelt.
* FHIR Server az Azure-ban – egy nyílt forráskódú projekt, amely a is telepíthető az Azure-előfizetése, elérhető a Githubon található https://github.com/Microsoft/fhir-server.

Használatra esetekről, bővítése vagy testreszabása a FHIR-kiszolgáló szükséges, vagy van szüksége az alapul szolgáló szolgáltatások eléréséhez – például az adatbázis – nélkül a FHIR API-kon keresztül, a fejlesztők az Azure a nyílt forráskódú FHIR kiszolgáló kell kiválasztani.   Végrehajtása egy kulcsrakész, éles használatra kész FHIR API és a háttérszolgáltatás, ahol megőrzött adatok csak hozzáférhetők a FHIR API-n keresztül, a fejlesztők az Azure API válasszák a FHIR

## <a name="get-started"></a>Bevezetés

A FHIR szolgáltatás használatának megkezdéséhez, hajtsa végre az 5 perces gyors útmutatót:

* Üzembe helyezés nyílt forráskiszolgáló FHIR [PowerShell](fhir-oss-powershell-quickstart.md)

## <a name="next-steps"></a>További lépések

Miután beállította a FHIR-szolgáltatás, hajtsa végre a fontos lépések végrehajtásával konfigurálhatja, és tesztelheti a szolgáltatást:

* [A postmannel FHIR szolgáltatás](access-fhir-postman-tutorial.md)

FHIR HL7 bejegyzett védjegye az pedig HL7 engedélyével.
