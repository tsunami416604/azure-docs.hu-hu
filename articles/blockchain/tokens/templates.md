---
title: Azure Blockchain-tokenek sablonjai
description: Az Azure Blockchain-tokenek sablonjai szabványosított és újrafelhasználható sablonok, amelyek leegyszerűsítik a Főkönyv-alapú jogkivonatok létrehozását és üzembe helyezését.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79252206"
---
# <a name="azure-blockchain-tokens-templates"></a>Azure Blockchain-tokenek sablonjai

[!INCLUDE [Preview note](./includes/preview.md)]

Az Azure Blockchain-tokenek sablonja egy szabványosított és újrahasznosítható sablon, amely leegyszerűsíti a Főkönyv-alapú jogkivonatok létrehozását és üzembe helyezését. A sablon egy olyan képletből áll, amely a [jogkivonat-taxonómiai keretrendszer (TTF)](overview.md#token-taxonomy-framework) nyelvtanán alapul. A nyelvtan magában foglalja az alapszintű jogkivonat típusát és a token viselkedési készletét.  

Például a **τϜ {d, m, b, r}** token sablon egy olyan helyettesíthető alaptokent ír le, amely alméretű, felmenthető és írható, és szerepkör-támogatással rendelkezik.
  
## <a name="base-token-types"></a>Alapjogkivonat-típusok

Az adott eszközhöz tartozó Főkönyv-token meghatározásakor és létrehozásakor fontos figyelembe venni, hogy milyen alaptokent kell használni.

### <a name="fungible"></a>Helyettesíthető

A helyettesíthető tokenek (τF-EK) felcserélhető értékkel rendelkeznek egymással, feltéve, hogy ugyanabban az osztályban vagy sorozatban találhatók. Egy jogkivonat ugyanazzal az értékkel rendelkezik, mint egy másik jogkivonat, vagy a tokenek egy adott mennyisége azonos értékkel rendelkezik, mint egy másik azonos mennyiség. A dollár például egy helyettesíthető jogkivonat. Ha két személy van egy dollár számlán, akkor az ilyen Dollar-számlákat a következmények nélkül cserélhetik be. A dollár számláinak értéke egyenlő. 

### <a name="non-fungible"></a>Nem helyettesíthető

A nem helyettesíthető tokenek (τN-EK) nem cserélhetők az azonos típusú más jogkivonatokkal, mivel általában eltérő értékekkel rendelkeznek. Egy tulajdonság címe például egy nem helyettesíthető jogkivonat. Az apartmankomplexum két különböző lakásához tartozó tulajdonságok címei nem szükségszerűen egyenlőek, mert az egység helye, vagy az a szint, amelyen az egység található. A két tulajdonság címe jogkivonatok érzékelt értéke nem egyenlő.

### <a name="hybrid"></a>Hibrid

A hibrid jogkivonatok olyan tokenek, amelyekben a helyettesíthető tokenek és a nem helyettesíthető tokenek összetevői is vannak. A hibrid jogkivonat olyan alapjogkivonat-típus, amely a másik jogkivonat-típus osztályának tulajdonosa.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hibrid, nem helyettesíthető, helyettesíthető szegmensekkel rendelkező talp

A helyettesíthető szegmensek tokenjét tartalmazó hibrid, nem helyettesíthető kiindulási alap nem helyettesíthető jogkivonat-szegmensekkel rendelkezik.
Egy koncert jegy például egy hibrid token, amelyben a koncert dátuma és időpontja a nem helyettesíthető alapjogkivonat. Az adott koncert különböző ülőhelyeit tartalmazó jegyek a szegmensek, amelyek helyettesített tokenekkel rendelkeznek. A jegyek a különálló ülőhelyek között, de nem több különböző szakaszban is cserélhetők.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Nem helyettesíthető szegmensekkel rendelkező hibrid helyettesíthető alap

A nem helyettesíthető szegmensek tokenjét tartalmazó hibrid helyettesíthető alapértékek nem helyettesíthető jogkivonat-szegmensekkel rendelkeznek. A jelzálog által támogatott biztonság például egy hibrid token, amelyben több tulajdonos a több tulajdonos között felosztott helyettesíthető alap. A biztonság felcserélhető. Az egyéni jelzálog a nem helyettesíthető szegmensek, amelyek az adott jelzálog-alapú biztonsági mentést jelölik.

## <a name="token-behaviors"></a>Jogkivonat-viselkedések

A jogkivonat viselkedése a jogkivonat képességeit vagy korlátozásait határozza meg. A viselkedés magában foglalja a jogkivonat-definíció részét képező támogató tulajdonságokat is. A viselkedésmódok az összes jogkivonat-típusra vagy csak egy alkalmazásra alkalmazhatók. A viselkedés a viselkedés hatásaitól függően belső vagy külső lehet. A belső viselkedés lehetővé teszi, hogy maga a jogkivonat tulajdonságait is engedélyezi vagy korlátozza. A külső viselkedés lehetővé teszi vagy korlátozza egy külső szereplő viselkedésének meghívását.

További információ az Azure Blockchain tokenek által támogatott jogkivonat-besorolási keretrendszer (TTF) token-viselkedéséről: [token-megkomponálás](composability.md).

## <a name="pre-built-token-templates"></a>Előre elkészített jogkivonat-sablonok

Az Azure Blockchain-tokenek négy előre elkészített jogkivonat-sablont biztosítanak, amelyek módosítás nélkül használhatók. A legtöbb felhasználási esethez meghívhatja ezeket az előre elkészített sablonokat a tokenek gyors létrehozásának, üzembe helyezésének és kezelésének megkezdéséhez.

### <a name="commodity-tokens"></a>Nyersanyag-tokenek

A nyersanyag-tokenek konzisztens értékkel rendelkeznek, és átruházhatók. Például egy hordó olaj vagy egy energia egység.

**τF {~ d, t, m, b, r}** – helyettesíthető, teljes, átvihető, felmenthető, írható és szerepkör-támogatás

Számos blockchain-forgatókönyv esetében átláthatóságra és láthatóságra van szükség az ellátási láncban vagy több szervezeten belül. A nyersanyag-tokenek ezen gyakori használati esetektől függenek. A tokenek felcserélhetők és konzisztensek. A nyersanyag-jogkivonat sablonja rugalmas és testreszabható metaadatokkal.

### <a name="qualified-tokens"></a>Minősített jogkivonatok

A minősített jogkivonatok egy adott személyt jelképeznek, és általában egy entitáshoz vannak társítva, és nem vihetők át. Például egy oklevél vagy egy parkoló megsértése.

**τN {s, ~ t}** – nem helyettesíthető, egyedi és nem átvihető

A különböző naplózási és igazolási forgatókönyvek megkövetelik, hogy a jogkivonat tulajdonjoga nem módosítható. Vannak olyan használati esetek, amelyeknek szükségük van egy minősített jogkivonat megadására, hogy a társítás jó vagy rossz.

### <a name="asset-tokens"></a>Eszközök jogkivonatai

Az objektum-tokenek egyedi értéktől függenek az elemtől, és nem commoditized. Például egy múzeumi összetevő vagy egy tulajdonság neve.

**τN {s, t}** – nem helyettesíthető, egyszeres és átvihető

Előfordulhat, hogy az eszköz jogkivonatai összetévesztik a jogkivonatokat. A két jogkivonat közötti fő különbség az, hogy az eszközök jogkivonatai eredendően egyediek, és az érték független a jogkivonat típusától. Egy olyan műalkotás például, amely egy kialakult művész által készített olajfestmény, egy eszköz tokenje. A Mona Lisa művészi nyomtatása azonban egy árucikk-tokennek tekintendő. Hasonlóképpen, a tulajdonság címe egy eszköz jogkivonata, mivel az érték a tulajdonság szubjektív tulajdonságaiban szerepel.

### <a name="ticket-tokens"></a>Jegyek jogkivonatai

A jegyek tokenje konzisztens értékkel rendelkezik, de általában lejár. Például egy repülőjegy.

**τN {m, b, r}** – nem helyettesíthető, felmenthető, írható és szerepkör-támogatással rendelkezik.

A Ticket tokenek jellemzően lejárati dátummal rendelkeznek, amely eltér a normál, a szokásostól eltérő jogkivonattól. A repülőjegyek, a koncert jegyek vagy a sportfogadási jegyek például az adott időponthoz rendelt ülőhelyekkel rendelkeznek. Nem lehet egyszerűen átváltani a jegyeket a dátumok vagy az ülőhelyek között.

## <a name="next-steps"></a>További lépések

Ha nagyobb rugalmasságra van szüksége a forgatókönyvhöz, Ismerje meg, hogyan hozhat létre saját jogkivonat-sablonokat a [jogkivonat-megkomponálás](composability.md)használatával.
