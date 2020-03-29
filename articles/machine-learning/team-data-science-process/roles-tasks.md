---
title: Csapatadatelemzési folyamat szerepkörei és feladatai
description: A legfontosabb összetevők, személyzeti szerepkörök és az adatelemzési csoport hoz tartozó feladatok vázlata.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720010"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Csapatadatelemzési folyamat szerepkörei és feladatai

A Team Data Science Process (TDSP) a Microsoft által kifejlesztett keretrendszer, amely strukturált módszertant biztosít a prediktív elemzési megoldások és az intelligens alkalmazások hatékony létrehozásához. Ez a cikk ismerteti a legfontosabb személyzeti szerepkörök és a kapcsolódó feladatok egy adatelemzési csapat szabványosítása ebben a folyamatban.

Ez a bevezető cikk a TDSP-környezet beállítására vonatkozó oktatóanyagokra mutat. Az oktatóanyagok részletes útmutatást nyújtanak az Azure DevOps-projektek, az Azure Repos-adattárak és az Azure Boards használatával kapcsolatos részletes útmutatáshoz.  A motiváló cél a koncepciótól a modellezésen át a telepítésig való áttérés.

Az oktatóanyagok az Azure DevOps-t használják, mert így valósítható meg a TDSP a Microsoftnál. Az Azure DevOps megkönnyíti az együttműködést a szerepköralapú biztonság, a munkaelemek kezelése és nyomon követése, valamint a kódüzemeltetési, -megosztás i és forrásellenőrzés integrálásával. Az oktatóanyagok egy Azure [Data Science virtuális gépet](https://aka.ms/dsvm) (DSVM) is használnak elemzési asztalként, amely számos népszerű adatelemzési eszközzel rendelkezik, amelyek előre konfiguráltak és integrálhatók a Microsoft szoftvereivel és az Azure-szolgáltatásokkal. 

Az oktatóanyagok segítségével más kódüzemeltetési, agilis tervezéssel és fejlesztői eszközökkel és környezetekkel valósíthatja meg a TDSP-t, de előfordulhat, hogy egyes funkciók nem érhetők el.

## <a name="structure-of-data-science-groups-and-teams"></a>Adatelemzési csoportok és csoportok felépítése

A vállalatok adatelemzési funkciói gyakran a következő hierarchiában szerveződnek:

- Adatelemzési csoport
  - Adatelemzési csapat/csapat a csoporton belül

Egy ilyen struktúrában vannak csoportérdeklődők és csapatérdeklődők. Általában egy adatelemzési projekt et egy adatelemzési csapat végzi. Az adatelemzési csapatok projekt-menedzsment és irányítási feladatokhoz, valamint egyéni adatszakértők és mérnökök rendelkeznek a projekt adatelemzési és adattechnológiai részeinek elvégzéséhez. A kezdeti projektbeállítást és -irányítást a csoport, a csapat vagy a projektérdeklődők végzik.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>A négy TDSP-szerepkör meghatározása és feladatai
Feltételezve, hogy az adatelemzési egység egy csoporton belüli csapatokból áll, a TDSP-személyzetnek négy különböző szerepköre van:

1. **Csoportvezető:** A teljes adatelemzési egységet kezeli egy vállalatban. Egy adatelemzési egység több csapat, amelyek mindegyike dolgozik több adatelemzési projektek különböző üzleti vertikális. A csoportkezelő átruházhatja a feladatait egy helyettesítőre, de a szerepkörhöz társított feladatok nem változnak.
   
2. **Csapatvezető:** Egy vállalat adatelemzési egységének egy csapatát kezeli. A csapat több adattudósból áll. Egy kis adatelemzési egység esetében a csoportvezető és a csoportvezető lehet ugyanaz a személy.
   
3. **Projektérdeklődő:** Az egyes adatszakértők napi tevékenységeit kezeli egy adott adatelemzési projektben.
   
4. **Egyéni közreműködők projekt:** adatelemzők, üzleti elemzők, adatmérnökök, építészek és mások, akik egy adatelemzési projektet hajtanak végre.

> [!NOTE]
> A vállalat szerkezetétől és méretétől függően egyetlen személy több szerepet is betölthet, vagy egynél több személy tölthet be szerepet.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>A négy szerepkör által teljesítendő feladatok

Az alábbi ábrán az egyes csapatadat-elemzési folyamatszerepkörek legfelső szintű feladatai láthatók. Ez a séma és a következő, részletesebb vázlatot feladatok minden TDSP-szerepkör segíthet kiválasztani a szükséges oktatóanyag a felelősségek alapján.

![Szerepkörök és feladatok – áttekintés](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Csoportkezelői feladatok

A csoportvezető vagy egy kijelölt TDSP-rendszergazda a következő feladatokat hajtja végre a TDSP elfogadásához:

- Létrehoz egy Azure **DevOps-szervezetet** és egy csoportprojektet a szervezeten belül. 
- Létrehoz egy **projektsablon-tárházat** az Azure DevOps csoportprojektben, és a Microsoft TDSP-csapat által kifejlesztett projektsablon-tárházból magjait. A Microsoft TDSP projektsablon-tárház a következőket biztosítja:
  - **Szabványos könyvtárstruktúra,** beleértve az adatok, kódok és dokumentumok könyvtárait.
  - **Szabványosított dokumentumsablonok** készlete a hatékony adatelemzési folyamat irányításához.
- Létrehoz egy **segédprogram-tárházat**, és a Microsoft TDSP csapata által kifejlesztett közüzemi tárházból magjait. A Microsoft TDSP segédprogram-tárháza hasznos segédprogramokat biztosít az adattudós munkájának hatékonyabbá tételéhez. A Microsoft segédprogram-tárháza interaktív adatfeltárási, elemzési, jelentéskészítési, valamint alapmodellezési és -jelentéskészítési segédprogramokat tartalmaz.
- Beállítja a szervezeti fiók **biztonsági vezérlési** házirendjeit.

Részletes útmutatást az [adatelemzési csoport csoportkezelői feladatai című témakörben talál.](group-manager-tasks.md)

## <a name="team-lead-tasks"></a>Csapatvezető-feladatok

A csoportvezető vagy egy kijelölt projektrendszergazda a következő feladatokat hajtja végre a TDSP elfogadásához:

- Létrehoz egy **csapatprojektet** a csoport Azure DevOps-szervezetében.
- Létrehozza a **projektsablon-tárházat** a projektben, és a csoportkezelő vagy a meghatalmazott által létrehozott csoportprojektsablon-tárházból magjazza fel.
- Létrehozza a **csapat közüzemi tárház ,** magok, hogy a csoport közüzemi tárház, és hozzáteszi, csapat-specifikus segédprogramok a tárház.
- Szükség esetén [létrehozza az Azure-fájltárolást,](https://azure.microsoft.com/services/storage/files/) hogy hasznos adateszközöket tároljon a csapat számára. A csapat többi tagja csatlakoztathatja ezt a megosztott felhőfájl-tárolót az analytics-asztalukhoz.
- Szükség esetén csatlakoztatja az Azure-fájltárolót a csapat **DSVM-jéhez,** és hozzáadja a csapatadat-eszközöket.
- A **biztonsági vezérlés** beállítása a csapattagok hozzáadásával és az engedélyeik konfigurálásával.

Részletes utasításokat a [Csapatvezető-feladatok egy adatelemzési csapathoz című témakörben talál.](team-lead-tasks.md)


## <a name="project-lead-tasks"></a>Projekt vezető tevékenységei

A projektérdeklődő a következő tevékenységeket hajtja végre a TDSP elfogadásához:

- Létrehoz egy **projekttárházat** a csapatprojektben, és a projektsablon tárházából magjait.
- Szükség esetén **azure-fájltárolást** is létrehoz a projekt adateszközeinek tárolásához.
- Szükség esetén csatlakoztatja az Azure-fájltárolót a **DSVM-hez,** és projektadat-eszközöket ad hozzá.
- A **biztonsági vezérlés** beállítása a projekttagok hozzáadásával és az engedélyeik konfigurálásával.

Részletes útmutatást a [Projektvezető-tevékenységek egy adatelemzési csapathoz című témakörben talál.](project-lead-tasks.md)

## <a name="project-individual-contributor-tasks"></a>Egyéni közreműködői feladatok projektje

A projekt egyéni közreműködője, általában adatelemző, a Következő feladatokat végzi a TDSP használatával:

- Klónozza a projektvezető által létrehozott **projekttárt.**
- Szükség esetén csatlakoztatja a megosztott csapat és a projekt **Azure-fájltárolás** az **adatelemzési virtuális gép** (DSVM).
- Végrehajtja a projektet.

A projektre való bevezetésrészletes útmutatása a [Project egyéni közreműködői tevékenységeinek egy adatelemzési csapathoz című témakörben található.](project-ic-tasks.md)

## <a name="data-science-project-execution-workflow"></a>Adatelemzési projekt végrehajtási munkafolyamata

A megfelelő oktatóanyagok követésével az adatszakértők, a projektérdeklődők és a csoportérdeklődők munkaelemeket hozhatnak létre a projekt összes tevékenységének és szakaszának nyomon követéséhez az elejétől a végéig. Az Azure Repos használatával elősegíti az adatszakértők közötti együttműködést, és biztosítja, hogy a projekt végrehajtása során létrehozott összetevők verzióvezéreltek és megosztottak legyenek a projekt összes tagja által. Az Azure DevOps segítségével összekapcsolhatja az Azure Boards munkaelemeit az Azure Repos-tárterület-ágakkal, és egyszerűen nyomon követheti, hogy mit tettek egy munkaelemnél.

Az alábbi ábra a Projekt végrehajtásához felelős TDSP-munkafolyamatot ismerteti:

![Tipikus adatelemzési projekt munkafolyamata](./media/roles-tasks/overview-project-execute.png)

A munkafolyamat lépései három tevékenységbe csoportosíthatók:

- A projektvezetők sprinttervezést végeznek
- Adatszakértők fejleszteni leletek `git` ágak kezelésére munkaelemek
- A projektérdeklődők vagy más csapattagok kódellenőrzéseket végeznek, és a munkaágakat egyesítik a főágtal

A projektvégrehajtási munkafolyamattal kapcsolatos részletes útmutatásért olvassa el [az Agilis adatelemzési projektek fejlesztése című témakört.](agile-development.md)

## <a name="tdsp-project-template-repository"></a>TDSP projektsablon-tárház

A hatékony projektvégrehajtás és együttműködés támogatásához használja a Microsoft TDSP-csapat [projektsablon-tárházát.](https://github.com/Azure/Azure-TDSP-ProjectTemplate) A tárház szabványos könyvtárstruktúrát és dokumentumsablonokat biztosít, amelyeket saját TDSP-projektjeihez használhat.

## <a name="next-steps"></a>További lépések

A csapatadat-elemzési folyamat által meghatározott szerepkörök és feladatok részletesebb leírása:

- [Csoportkezelői feladatok adatelemzési csoportszámára](group-manager-tasks.md)
- [Csapatvezető-feladatok egy adatelemzési csapatszámára](team-lead-tasks.md)
- [Projektvezető-tevékenységek adatelemzési csoportszámára](project-lead-tasks.md)
- [Egyéni közreműködői feladatok projektje adatelemzési csoport számára](project-ic-tasks.md)
