---
title: "Az Azure Naplóelemzés reguláris kifejezések jelentkezzen keresések |} Microsoft Docs"
description: "A RegEx kulcsszót Naplóelemzési napló kereséseket a szűrő az eredményeket a reguláris kifejezés szerinti használhatja.  Ez a cikk a szintaxis biztosít néhány példa a kifejezést."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: 8915e0e35951871ff10fd84453d55bd5102e97df
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>A Naplóelemzési keres reguláris kifejezésekkel napló szűrése

>[!NOTE]
> Ez a cikk ismerteti az örökölt lekérdezési nyelv használatát Naplóelemzési reguláris kifejezéseket.  Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd tekintse át [reguláris kifejezéseket a nyelvi dokumentáció](https://docs.loganalytics.io/docs/Language-Reference/References/Regular-Expressions-syntax).


[A keresések jelentkezzen](log-analytics-log-searches.md) engedélyezi, hogy az információk kinyerése a Naplóelemzési munkaterület.  [Szűrési kifejezésekben](log-analytics-search-reference.md#filter-expressions) engedélyezi, hogy a megadott feltételek szerint a keresési eredmények szűréséhez.  A **RegEx** kulcsszó lehetővé teszi egy reguláris kifejezést a szűrő számára.  

Ez a cikk részletesen a Naplóelemzési által használt reguláris kifejezés szintaxisa adható meg.

> [!NOTE]
> Csak a kereshető mezők RegEx tudja használni.  További információ a kereshető mezők: **mezőtípusok** a [található adatokat, és napló kereséseket a Naplóelemzési](log-analytics-log-searches.md#use-additional-filters).


## <a name="regex-keyword"></a>RegEx kulcsszó

A következő szintaxissal használja a **RegEx** naplófájl-keresési kulcsszót.  Ez a cikk más részeiben segítségével határozza meg a reguláris kifejezés szintaxisa.

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

Például reguláris kifejezés használata típussal rendelkező riasztási rekordot *figyelmeztetés* vagy *hiba*, a következő naplófájl-keresési szeretné használni.

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>Részleges egyezések
Vegye figyelembe, hogy a reguláris kifejezésnek egyeznie kell a tulajdonság a teljes szöveg.  Részleges egyezéseket nem ad vissza rekordok.  Például, ha vissza rekordok srv01.contoso.com nevű számítógép kívánt, a következő naplófájl-keresési volna **nem** vissza rekordot.

    Computer=RegEx("srv..")

Ennek oka az, csak a név első része a reguláris kifejezésre illeszkedik.  A következő két naplófájl keresi alakítanák vissza rekordok erről a számítógépről, mert azok felel meg a teljes nevet.

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>Karakterek
Adjon meg másik karaktereket.

| Karakter | Leírás | Példa | A minta megfelel |
|:--|:--|:--|:--|
| a | Egy előfordulást karakter. | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| . | Bármely egy karakter. | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| a? | Nulla vagy egy előfordulási karakter. | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | Események nulla vagy több karakter. | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | Egy vagy több események karakter. | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | A szögletes zárójelbe egyetlen karakter | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | A tartomány egyetlen karakternek felel meg.  Több tartomány is tartalmazza. | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | A szögletes zárójelbe karakterek egyike sem | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | A tartomány karakterek egyike sem. | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | Megfelelő numerikus karakterek tartományát. | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | Bármilyen karakterlánc. | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>Több előfordulásának karakter
Adjon meg egy adott karaktereket szoftver többször is előfordul.

| Karakter | Leírás | Példa | A minta megfelel |
|:--|:--|:--|:--|
| a{n} |  *n*a következő karaktert előfordulását. | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  *n*vagy több példányának a következő karaktert. | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| {n, m} |  *n*a *m* előfordulások karakter. | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>Logikai kifejezések
Válasszon ki több érték.

| Karakter | Leírás | Példa | A minta megfelel |
|:--|:--|:--|:--|
| &#124; | Logikai vagy.  Eredményt adja vissza, ha mindkét kifejezés a megfelelő. | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | Figyelmeztetés<br>Hiba |
| & | Logikai és művelet.  Eredményt adja vissza, ha mindkét kifejezés a megfelelő | EventData=regex("(Security.\*&.\*success.\*)") | Biztonsági naplózás sikeres |


## <a name="literals"></a>Literálok
Speciális karakterek átalakítása literális karaktereket.  Ez magában foglalja a karakterek, például a reguláris kifejezések funkciókat biztosító?-\*^\[\]{}\(\)+\|. &.

| Karakter | Leírás | Példa | A minta megfelel |
|:--|:--|:--|:--|
| \\ | Átalakít egy speciális karakter szövegkonstans. | Status_CF =\\[hiba\\] @<br>Status_CF=Error\\-@ | [Hiba] A fájl nem található.<br>Hiba – a fájl nem található. |


## <a name="next-steps"></a>További lépések

* Ismerkedjen meg [keresések jelentkezzen](log-analytics-log-searches.md) megtekintheti és elemezheti a Naplóelemzési munkaterület adatokat.
