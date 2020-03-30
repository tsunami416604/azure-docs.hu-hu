---
title: Azure Blockchain tokenek sablonjai
description: Az Azure Blockchain tokenek sablonjai szabványosított és újrafelhasználható sablonok, amelyek leegyszerűsítik a főkönyvalapú tokenek létrehozását és üzembe helyezését.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9600a6a251552acd319cc68d2bd281584d65546d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252206"
---
# <a name="azure-blockchain-tokens-templates"></a>Azure Blockchain tokenek sablonjai

[!INCLUDE [Preview note](./includes/preview.md)]

Az Azure Blockchain Tokens sablon egy szabványosított és újrafelhasználható sablon, amely leegyszerűsíti a főkönyvalapú tokenek létrehozását és üzembe helyezését. A sablon egy képletből áll, amely a [Token Taxonómiai Keretrendszer (TTF)](overview.md#token-taxonomy-framework) nyelvtanán alapul. A nyelvtan magában foglalja az alap jogkivonat típusát és a jogkivonat viselkedéskészletét.  

Például **a τ,m,b,r}** tokensablon egy helyettesíthető alaptokent ír le, amely továbbosztható, menthető, égethető és szerepkör-támogatással rendelkezik.
  
## <a name="base-token-types"></a>Alaptokentípusok

Az adott eszköz főkönyvi alapú jogkivonatának meghatározásakor és létrehozásakor fontos figyelembe venni, hogy milyen alapjogkivonatot kell használnia.

### <a name="fungible"></a>Helyettesíthető

A helyettesíthető tokenek (τF) felcserélhető értékkel rendelkeznek egymással, amennyiben ugyanabba az osztályba vagy sorozatba tartoznak. Egy jogkivonat értéke megegyezik egy másik jogkivonat, vagy egy adott mennyiségű jogkivonatok ugyanaz tanusíta, mint egy másik azonos mennyiség. Például, egy dollár egy helyettesíthető token. Ha két ember minden gazdaság egy dollár számlát, akkor lehet cserélni ezeket a dollár számlák következmények nélkül. A dollár számlák egyenlő értékűek. 

### <a name="non-fungible"></a>Nem helyettesíthető

A nem helyettesíthető tokenek (τN) nem cserélhetők fel más azonos típusú tokenekkel, mivel általában eltérő értékekkel rendelkeznek. Például egy tulajdonság cím egy nem helyettesíthető jogkivonat. Ingatlan cím két különböző apartman egy apartman komplexum nem feltétlenül azonos értékű, mivel sem a helyét az egység, vagy melyik emeleten az egység. A két tulajdonságcím-token vélt értéke nem egyenlő.

### <a name="hybrid"></a>Hibrid

A hibrid jogkivonatok olyan tokenek, amelyek egyaránt helyettesíthető tokenek és nem helyettesíthető tokenek összetevői. A hibrid jogkivonat egy alapjogkivonat-típus, amely rendelkezik egy osztály a másik jogkivonat-típus.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Hibrid, nem helyettesíthető alap helyettesíthető szegmensekkel

A hibrid nem helyettesíthető alap helyettesíthető szegmensek token egy nem helyettesíthető alap helyettesíthető token szegmensek.
Például egy koncertjegy egy hibrid jogkivonat, ahol a koncert dátuma és időpontja a nem helyettesíthető alaptoken. A jegyek különböző ülésszakaszokaz adott koncert a szegmensek helyettesíthető tokenek. A jegyek az egyes ülésszakaszokon cserélhetők, de nem a szakaszokközött.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Hibrid helyettesíthető alap nem helyettesíthető szegmensekkel

A nem helyettesíthető szegmensek tokenjével rendelkező hibrid helyettesíthető alap nem helyettesíthető tokenszegmensekkel rendelkezik. Például a jelzálog alapú biztonság egy hibrid jogkivonat, ahol több tulajdonos a sok tulajdonos között felosztott helyettesíthető bázis. A biztonság felcserélhető. Az egyes jelzálog a nem helyettesíthető szegmensek, amelyek képviselik az adott jelzálog-fedezetű értékpapír.

## <a name="token-behaviors"></a>Token viselkedések

A jogkivonat viselkedése határozza meg a jogkivonat képességeit vagy korlátozásait. A viselkedés magában foglalja a jogkivonat-definíció részét tartalmazó támogató tulajdonságokat. Viselkedések lehet alkalmazni az összes jogkivonat-típusok, vagy csak egy. Viselkedések lehetnek belső vagy külső attól függően, hogy milyen a viselkedés hatások. A belső viselkedés engedélyezi vagy korlátozza a token tulajdonságait. A külső viselkedés lehetővé teszi vagy korlátozza a viselkedés külső aktorból történő meghívását.

Az Azure Blockchain tokenek által támogatott token taxonómiai keretrendszer (TTF) tokenviselkedésekről a [tokenek komponálhatósága](composability.md)című témakörben talál további információt.

## <a name="pre-built-token-templates"></a>Előre elkészített tokensablonok

Az Azure Blockchain tokenek négy előre elkészített jogkivonat-sablont biztosít, amelyek módosítás nélkül használhatók. Ezeket az előre elkészített sablonokat a legtöbb használati esetekben a tokenek létrehozásának megkezdéséhez, üzembe helyezéséhez és kezeléséhez.

### <a name="commodity-tokens"></a>Árucikk tokenek

Az árualapú tokenek konzisztens értékkel rendelkeznek, és átruházhatók. Például egy hordó olaj vagy egy energiaegység.

**τF {~d, t,m, b, r}** - helyettesíthető, egész, átruházható, menthető, égethető, és szereptámogatással rendelkezik

Számos blokklánc-forgatókönyv átláthatóságot és láthatóságot igényel az ellátási láncban vagy több szervezetben. Az árualapú tokenek ezeken a gyakori használati eseteken alapulnak. A tokenek felcserélhetők és konzisztensek. Az árujogkivonat-sablon rugalmas és metaadatokkal testreszabható.

### <a name="qualified-tokens"></a>Minősített tokenek

A minősített tokenek jelentenek valamit szerzett, és általában társított egy entitás, és nem lehet átvinni. Például egy oklevél vagy egy parkolási szabálysértés.

**τN{s,~t}** - nem helyettesíthető, szingli és nem átruházható

Különböző naplózási és tanúsítvány-forgatókönyvek megkövetelik, hogy a jogkivonat tulajdonjoga nem módosítható. Van egy sor használati esetek, amelyek szükség van, hogy egy minősített jogkivonatot, hogy a társítás jó vagy rossz.

### <a name="asset-tokens"></a>Eszköztokenek

Az eszköztokenek egyedi értéke a cikktől függ, és nem árucikké váltak. Például egy múzeumi műtárgy vagy egy tulajdon címe.

**τN{s,t}** - nem helyettesíthető, szingli és átruházható

Az eszköztokenek összetéveszthetők az árualapú tokenekkel. A fő különbség a két jogkivonat között az, hogy az eszköztokenek eredendően egyediek, és az érték független a jogkivonat típusától. Például egy műalkotás, mint egy olajfestmény egy elismert művész egy eszköz token. Azonban a Mona Lisa művészeti nyomata árukincsnek számít. Hasonlóképpen a tulajdonság jogkivonat egy eszköz token, mivel az érték létezik a tulajdonság szubjektív tulajdonságai.

### <a name="ticket-tokens"></a>Jegytokenek

A jegytokenek konzisztens értékkel rendelkeznek, de általában lejárnak. Például egy repülőjegy.

**τN{m,b,r}** - nem helyettesíthető, menthető, égethető és szerepkör-támogatással rendelkezik.

A jegytokenek általában lejárati dátummal rendelkeznek, amely megkülönbözteti őket a normál árualapú tokentől. Például egy repülőjegy, koncertjegy vagy sportjegy mindegyikének van lehetősége a kijelölt ülőhelyekre, meghatározott felhasználási dátumokkal. Nem lehet könnyen átváltani a jegyeket a dátumok vagy az ülőhelyek között.

## <a name="next-steps"></a>További lépések

Ha nagyobb rugalmasságra van szüksége a forgatókönyvhöz, ismerje meg, hogyan hozhat létre saját tokensablonokat a [tokenek kompolhatóságának](composability.md)használatával.
