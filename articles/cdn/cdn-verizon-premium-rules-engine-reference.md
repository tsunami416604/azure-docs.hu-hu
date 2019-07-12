---
title: Az Azure CDN szabálymotor-referencia |} A Microsoft Docs
description: Az Azure CDN dokumentációja szabályok szabálymotor egyezési feltételei és funkciókat.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593146"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Az Azure CDN a Verizon prémium szintű szabályok szabálymotor referencia

Ez a cikk felsorolja a részletes leírását, a rendelkezésre álló egyezési feltételei és a szolgáltatások esetében az Azure Content Delivery Network (CDN) [szabálymotorral](cdn-verizon-premium-rules-engine.md).

A rules engine tervezték hogyan adott típusú kérelmet a végső hatóság dolgozza fel a CDN-t.

**Gyakori használati**:

- Bírálja felül, vagy egy egyéni gyorsítótár-szabályzat meghatározása.
- Biztonságos, vagy megtagadja a tartalomhoz bizalmas.
- Kérelmek átirányítása.
- Az egyéni napló data Store.

## <a name="terminology"></a>Terminológia

Egy szabály van meghatározva használatával [ **feltételes kifejezéseket**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [ **feltételeknek megfelelő**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), és [ **funkciók**](cdn-verizon-premium-rules-engine-reference-features.md). Ezeket az elemeket az alábbi ábrán vannak kiemelve:

 ![CDN egyezési feltételei](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Szintaxis

A speciális karakterek kezelik, amelyben módon hogyan egy egyezési feltétellel vagy a szolgáltatás kezeli a szöveges értékek függően változik. Egy az egyezési feltétellel vagy a szolgáltatás értelmezhetik szöveg a következő módszerek valamelyikével:

1. [**Konstans érték**](#literal-values)
2. [**Helyettesítő karakteres értékek**](#wildcard-values)
3. [**Reguláris kifejezések**](#regular-expressions)

### <a name="literal-values"></a>Konstans érték

Szöveg, amely szöveges értékként értelmezi a % szimbólum kivételével minden speciális karakterek az érték, amely egyeztetni részeként kezeli. Azaz szövegkonstansnak beállítása feltételnek megfelelő-e `\'*'\` csak meggyőződött róla, hogy pontos értékek amikor (azaz `\'*'\`) található.

Egy százalék szimbólummal használva URL-Címének kódolása azt (például `%20`).

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek

Szöveg, amely egy helyettesítő karaktert tartalmazó értéket kerül értelmezésre további jelentése rendel a különleges karaktereket. A következő táblázat ismerteti, hogyan értelmezi a következő karakterkészlet:

Karakter | Leírás
----------|------------
\ | Fordított perjel karaktert a jelen táblázatban lévő megadott karakterek egyikét sem. Egy fordított perjel elé a speciális karaktert kell lennie escape-karakterrel kell megadni.<br/>Ha például a következő szintaxist egy csillag lehet kilépni: `\*`
% | Egy százalék szimbólummal használva URL-Címének kódolása azt (például `%20`).
\* | Egy csillag, amely egy vagy több karaktert helyettesítő karakterként.
Űr | Szóköz karakter jelzi, hogy egyeztetési feltételt lehet teljesíteni a megadott értékek vagy minták egyikét.
"érték" | Jednoduchá uvozovka nincs kulcsszó különleges jelentéssel. Azonban egy aposztrófok használatos jelzi, hogy egy érték Szövegkonstansérték kell kezelni. Az alábbi módon használható:<br><br/>-Lehetővé teszi, ha a megadott értéke megegyezik az összehasonlítási érték bármely részének teljesítendő egyeztetési feltételt.  Ha például `'ma'` megfelel a következő karakterláncok bármelyikét: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/ business/sablon. **ma**p<br /><br />-Lehetővé teszi egy speciális karaktert a literális karakter adható meg. Például megadhat egy literális karakter belül szimpla idézőjelek között egy készletét a szóköz karakter kötegfájlokban (azaz `' '` vagy `'sample value'`).<br/>-Lehetővé teszi egy üres értéket kell megadni. Adjon meg egy üres értéket szimpla idézőjelek között megadásával (vagyis ").<br /><br/>**Fontos:**<br/>– Ha a megadott érték nem tartalmaz helyettesítő karaktert, majd automatikusan tekintendő konstans érték, ami azt jelenti, hogy nem szükségesek, hogy adjon meg egy szimpla idézőjelek között.<br/>– Ha egy fordított perjel nem ez a táblázat egy másik karakterek elkerülésére, hogy rendszer figyelmen kívül hagyja a szimpla idézőjelek között belül van megadva.<br/>– Egy másik módja egy speciális karaktert adja meg, ahogyan a használatával egy fordított perjel karaktert a literális karakter (azaz `\`).

### <a name="regular-expressions"></a>Reguláris kifejezések

Reguláris kifejezések meghatározása egy mintát, amely egy szöveges érték belül kell keresni. Reguláris kifejezés jelöléssel meghatározott jelentéssel számos különböző szimbólumok határozza meg. Az alábbi táblázat azt jelzi, hogy milyen speciális karakterek egyezési feltételei és reguláris kifejezésekkel támogató szolgáltatások kezeli.

Speciális karakter | Leírás
------------------|------------
\ | Egy fordított perjel lehet kilépni a karakter, az alábbiak szerint informatikai, aminek a karakter konstans érték helyett a reguláris kifejezés jelentése szerinti kell kezelni. Ha például a következő szintaxist egy csillag lehet kilépni: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.mdEgy százalék szimbólummal szerinti attól függ, hogy a használatát. Docs
descr`%{HTTPVariable}`: Ez a szintaxis egy HTTP-változó azonosítja.match`%{HTTPVariable%Pattern}`: Ez a szintaxis egy százalék szimbólummal változó és elválasztóként HTTP azonosítására használ.1/2019`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`20`).f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | Csillag lehetővé teszi, hogy az előző karaktert egyezést kell keresni nulla vagy több alkalommal.
Űr | Szóköz karakter általában számít literális karakter.
"érték" | Literális karakter szimpla idézőjelek között kell kezelni. Egy készletét szimpla idézőjelek között nincs kulcsszó különleges jelentéssel.

## <a name="next-steps"></a>További lépések

- [Szabálymotor egyezési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [A rules engine használatával HTTP a működés felülbírálása](cdn-verizon-premium-rules-engine.md)
- [Az Azure CDN áttekintése](cdn-overview.md)