---
title: Mi az az Azure API for FHIR? – Azure API a FHIR-hez
description: A FHIR készült Azure API lehetővé teszi a gyors adatcserét a FHIR API-kon keresztül. A védett állapotadatok betöltése, kezelése és megőrzése egy felügyelt felhőalapú szolgáltatással.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: aca0d67326a5a0488d0108efa9acd0d01c7788cd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "84820203"
---
# <a name="what-is-azure-api-for-fhirreg"></a>Mi az a FHIR készült Azure API &reg; ?

A FHIR készült Azure API gyors adatcserét tesz lehetővé a gyors egészségügyi együttműködési erőforrások (FHIR®) API-k segítségével, melyeket a felhőben felügyelt, szolgáltatásként nyújtott szolgáltatások ("Pásti") támogatnak. Megkönnyíti az egészségügyi adatokkal dolgozó személyek számára, hogy a felhőben [tárolt védett](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) állapotadatok betöltésére, kezelésére és megőrzésére is alkalmasak legyenek. 

- Felügyelt FHIR szolgáltatás, amely percek alatt kiépítve a felhőben 
- Nagyvállalati szintű, FHIR®-alapú végpont az Azure-ban adathozzáféréshez és tárolás FHIR® formátumban
- Nagy teljesítményű, kis késleltetésű
- A védett egészségügyi adatokat (PHI) biztonságos kezelése a megfelelő felhőalapú környezetben
- INTELLIGENS on FHIR mobil-és webes megvalósításokhoz
- Saját adatai méretezése a szerepköralapú Access Control (RBAC)
- Naplózási naplók nyomon követése az egyes adattárokban való hozzáféréshez, létrehozáshoz, módosításhoz és olvasáshoz

A FHIR készült Azure API lehetővé teszi, hogy percek alatt hozzon létre és helyezzen üzembe egy FHIR szolgáltatást, hogy kihasználja a felhő rugalmas méretét.  Csak a szükséges átviteli sebességért és tárterületért kell fizetnie. A Power Azure API-t a FHIR-hez készült Azure-szolgáltatások gyors teljesítményre tervezték, függetlenül attól, hogy milyen méretű adatkészleteket kezel.

A FHIR API és a megfelelő adattár lehetővé teszi a biztonságos kapcsolódást és a FHIR API-kat használó rendszerekkel való kommunikációt.  A Microsoft igénybe veszi a Pásti-ajánlat üzemeltetési, karbantartási, frissítési és megfelelőségi követelményeit, így a saját működési és Fejlesztési erőforrásai szabadon felhasználhatók. 

Az alábbi videó áttekintést nyújt a FHIR készült Azure API-ról:

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Az FHIR használatával kihasználhatja az adatai hatékonyságát

Az egészségügyi ágazat gyorsan átalakítja az egészségügyi adatmennyiséget a feltörekvő [FHIR &reg; ](https://hl7.org/fhir) (gyors egészségügyi együttműködési erőforrások). A FHIR egy robusztus, bővíthető adatmodellt biztosít szabványosított szemantikai és adatcsere-szolgáltatással, amely lehetővé teszi, hogy az összes rendszer a FHIR használatával működjenek együtt.  Az adatok FHIR történő átalakításával gyorsan összekapcsolhatók a meglévő adatforrások, például az elektronikus egészségügyi nyilvántartási rendszerek vagy a kutatási adatbázisok. A FHIR Emellett lehetővé teszi a gyors adatcserét a mobil-és webes fejlesztés modern implementációjában. A FHIR a legfontosabb, hogy egyszerűsítse az adatfeldolgozást, és gyorsítsa fel a fejlesztést az elemzési és gépi tanulási eszközökkel.  

### <a name="securely-manage-health-data-in-the-cloud"></a>A felhőben tárolt állapotadatok biztonságos kezelése

Az Azure API for FHIR lehetővé teszi az adatok egységes, REST-alapú, FHIR API-kkal való cseréjét a HL7 FHIR specifikációja alapján. Az Azure-ban felügyelt, Pásti által nyújtott támogatással is méretezhető és biztonságos környezetet biztosít a védett egészségügyi információ (PHI) adatainak a natív FHIR-formátumban történő kezeléséhez és tárolásához.  

### <a name="free-up-your-resources-to-innovate"></a>Erőforrások felszabadítása innovációra

A saját FHIR-szolgáltatás létrehozásához és futtatásához, de a FHIR készült Azure API-hoz a Microsoft igénybe veszi a műveletek, a karbantartás, a frissítések és a megfelelőségi követelmények számítási feladatait, lehetővé téve a saját működési és fejlesztési erőforrásainak felszabadítását.

### <a name="enable-interoperability-with-fhir"></a>Együttműködés engedélyezése a FHIR

A FHIR készült Azure API használata lehetővé teszi a kapcsolódást bármely olyan rendszerhez, amely a FHIR API-kat használja az olvasási, írási, keresési és egyéb funkciókhoz.  Hatékony eszközként használható az elektronikus egészségügyi adatokból, az orvosokból és a beteg irányítópultokból, a távoli figyelési programokból, illetve a FHIR API-kat használó rendszereken kívüli adatbázisokkal végzett gépi tanulás összevonásához, normalizálása és alkalmazása során.

### <a name="control-data-access-at-scale"></a>Az adathozzáférés szabályozása méretezéskor

Ön szabályozhatja az adatait. A szerepköralapú Access Control (RBAC) lehetővé teszi az adatai tárolásának és elérésének kezelését.  A fokozott biztonság és a felügyeleti munkaterhelés csökkentése révén meghatározhatja, hogy ki férhet hozzá a létrehozott adatkészletekhez a környezetéhez létrehozott szerepkör-definíciók alapján.  

### <a name="audit-logs-and-tracking"></a>Naplók és nyomkövetés naplózása 

Gyorsan nyomon követheti, hogy az adatai hogyan lesznek a beépített naplók. Nyomon követheti a hozzáférést, a létrehozást, a módosítást és az olvasást az egyes adattárokon belül.

### <a name="secure-your-data"></a>Az adatok védelme

A PHI védelme páratlan biztonsági intelligenciával.  Az adatai el vannak különítve egy egyedi adatbázisra API-példányon, és a többrégiós feladatátvételsel védettek. A FHIR készült Azure API egy rétegzett, részletes védelmi és komplex veszélyforrások elleni védelmet valósít meg az adataihoz.  

## <a name="applications-for-a-fhir-service"></a>FHIR-szolgáltatás alkalmazásai

A FHIR-kiszolgálók az állapotadatok együttműködési képességének legfontosabb eszközei.  A FHIR készült Azure API API-ként és szolgáltatásként lett kialakítva, amellyel gyorsan létrehozhatja, üzembe helyezheti és megkezdheti a használatát.  Mivel a FHIR standard kibővül az egészségügyi ellátásban, a használati esetek továbbra is növekedni fognak, de néhány olyan kezdeti ügyfél-alkalmazás esetében, ahol az Azure API for FHIR hasznosak, a következők: 

- **Indítás/IOT és alkalmazások fejlesztése:**  A beteg vagy szolgáltató központú alkalmazást (mobil vagy webes) fejlesztő ügyfelek teljes körűen felügyelt FHIR használhatják az Azure API-t. Az Azure API for FHIR egy értékes erőforrást biztosít az ügyfelek számára az adatkezeléshez és az adatcseréhez az állapot-ellenőrzésre tervezett biztonságos felhőalapú környezetben, a FHIR-megvalósítási irányelvek intelligens használatát, és lehetővé teszi, hogy a technológiájuk az összes szolgáltatói rendszer számára elérhető legyen (például a legtöbb EHRs engedélyezve van a FHIR olvasási API-k).   
- **Egészségügyi ökoszisztémák:**  Habár a EHRs a legtöbb klinikai beállításban az igazság forrásaként szerepel, nem ritka, hogy a szolgáltatók több olyan adatbázissal rendelkeznek, amelyek nem csatlakoznak egymáshoz, vagy különböző formátumokban tárolják az adattárolást.  Ha az Azure API-t használja a FHIR-hez, az ezen rendszerek egyikén alapuló szolgáltatásként FHIR formátumban szabványosíthatja az adatmennyiséget.  Ez lehetővé teszi, hogy egységes adatformátummal engedélyezze az adatcserét több rendszer között. 

- **Kutatás:** Az egészségügyi kutatók az általános FHIR standard és az Azure API for FHIR hasznosak, mivel az adatok normalizálják a közös FHIR adatmodelleket, és csökkentik a gépi tanulás és az adatmegosztás munkaterhelését.
A FHIR készült Azure API-n keresztüli adatcsere naplózási naplókat és hozzáférés-vezérléseket biztosít, amelyek segítségével szabályozható az adatáramlás, és ki férhet hozzá az adattípusokhoz. 

## <a name="fhir-from-microsoft"></a>FHIR a Microsofttól

A Microsoft FHIR képességei két konfigurációban érhetők el:

* Azure API for FHIR – az Azure-beli, a Azure Portal által könnyen kiépített és a Microsoft által felügyelt.
* FHIR Server for Azure – egy nyílt forráskódú projekt, amely üzembe helyezhető az Azure-előfizetésében, amely elérhető a GitHubon https://github.com/Microsoft/fhir-server .

Olyan használati esetekhez, amelyek a FHIR-kiszolgáló kiterjesztését vagy testreszabását igénylik, vagy ha a mögöttes szolgáltatásokat (például az adatbázist) szeretnék elérni a FHIR API-k használata nélkül, a fejlesztőknek ki kell választaniuk az Azure nyílt forráskódú FHIR-kiszolgálóját.   Kulcsrakész, éles használatra kész FHIR API-és háttér-szolgáltatás megvalósításához, ahol a megőrzött adatmennyiséget csak a FHIR API-n keresztül szabad elérni, a fejlesztőknek ki kell választaniuk az Azure API-t a FHIR

## <a name="next-steps"></a>Következő lépések

A FHIR készült Azure API használatának megkezdéséhez kövesse az 5 perces rövid útmutatót a FHIR készült Azure API üzembe helyezéséhez.

>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-portal-quickstart.md)

Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.
