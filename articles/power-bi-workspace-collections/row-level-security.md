---
title: Sorszintű biztonság a Power BI munkaterületi gyűjteményekkel
description: Sorszintű biztonság a Power BI-Munkaterületcsoportok részleteit
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: ce1e3818edea6e0fdaedd33b6ec0c3214f475340
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048556"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Sorszintű biztonság a Power BI munkaterületi gyűjteményekkel

Sorszintű biztonság (RLS) a felhasználói hozzáférés korlátozása egy adott jelentés vagy adatkészlet, amely lehetővé teszi a több különböző felhasználó használhassa ugyanazt a jelentést, de különböző adatokat láthasson adott adatok használható. Power BI munkaterületi gyűjtemények konfigurálva az RLS adatkészleteket támogatja.

![Sorszintű biztonság a Power BI-Munkaterületcsoportok munkafolyamata](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

Az RLS kihasználása érdekében fontos tisztában három fő alapelvet: Felhasználók, szerepkörök és szabályok. Tekintsünk meg minden egyes közelebbről:

**Felhasználók** – ezek vannak a jelentéseket megtekintő tényleges végfelhasználók. A Power BI-Munkaterületcsoportok, a felhasználók egy alkalmazási jogkivonatot lévő felhasználónév tulajdonság azonosítja.

**Szerepkörök** – felhasználók szerepkörökhöz tartoznak. Egy szerepkör szabályok tárolói és olyan nevük lehet például "Értékesítési vezető" vagy "Értékesítő". A Power BI-Munkaterületcsoportok, a felhasználók egy alkalmazási jogkivonatot a szerepkörök tulajdonság azonosítja.

**Szabályok** – szerepkörök szabályokkal rendelkeznek, és ezeket a szabályokat a alkalmazni az adatok tényleges szűrők. Ez lehet egyszerűen "ország = USA", vagy sokkal dinamikusabb.

### <a name="example"></a>Példa

Ez a cikk további részének biztosítunk egy példát az rls elkészítésére, és ezután megadunk egy beágyazott alkalmazásban. A példában a [kiskereskedelmi elemzési minta](http://go.microsoft.com/fwlink/?LinkID=780547) PBIX-fájlt.

![Példa értékesítési jelentés](media/row-level-security/scenario-2.png)

A kiskereskedelmi elemzési minta egy adott kiskereskedelmi láncban lévő összes áruház értékesítéseit jeleníti meg. Rls-t, függetlenül attól, hogy melyik district manager jelentkezik be, és megtekinti a jelentést, nélkül ugyanazokat az adatokat láthatják. Vezetőség meghatározta, mindegyik kerületi menedzser csak az általuk kezelt áruházak értékesítéseit mellett, és ehhez használhatjuk az rls-t.

Az RLS a Power BI Desktopban készül. Ha az adatkészlet és jelentés megnyitásakor, a is diagramnézet a séma váltani:

![A Power BI Desktopban modellek diagramja](media/row-level-security/diagram-view-3.png)

Az alábbiakban néhány szempont, figyelje meg, hogy ebben a sémában:

* Az összes mértékre, például **Total Sales**, vannak tárolva a **értékesítési** ténytáblában.
* Nincsenek négy további kapcsolódó dimenziótábla: **elem**, **idő**, **Store**, és **kerület**.
* A kapcsolatvonalakon lévő azt jelzik, hogy milyen módon szűrőket is flow egyik táblából egy másikba. Ha például egy szűrő el van helyezve **Time [Date]**, az aktuális sémában, akkor csak szűrése lefelé értékeket a **értékesítési** tábla. Más táblák a szűrőt, mert a kapcsolatvonalakon lévő összes mutasson a sales táblába, és azonnal nem befolyásolhat.
* A **kerület** mérőszámának táblájában, aki a kezelő az egyes kerületek:
  
  ![Táblázatsorok körzet](media/row-level-security/district-table-4.png)

A séma alapján, ha szűrőt alkalmazunk a **körzeti vezető** a körzeti táblában, és ez a szűrő megfelel a jelentést megtekintő felhasználónak, ha, amely lehet szűkíteni is szűrők a **Store** és  **Értékesítési** csak táblázatokban adott adott menedzser adatait manager.

Íme, miként:

1. Kattintson a modellezés lap **szerepkörök kezelése**.  
   ![Szerepkörök gombra a modellezés menüszalag kezelése](media/row-level-security/modeling-tab-5.png)
2. Hozzon létre egy új szerepkör nevű **Manager**.  
   ![Szerepkörök létrehozása a Power BI Desktopban](media/row-level-security/manager-role-6.png)
3. Az a **kerület** táblában írja be a következő DAX-kifejezést: **[District Manager] = USERNAME()**  
   ![Tábla a szerepkör DAX-kifejezés](media/row-level-security/manager-role-7.png)
4. Annak biztosításához, hogy a szabályok dolgozik, az a **modellezés** lapra, majd **megtekintés szerepkörökként**, és írja be a következőt:  
   ![Megtekintés szerepkörökként](media/row-level-security/view-as-roles-8.png)

   A jelentéseket most már adatai jelennek meg, mintha néven jelentkezett **Andrew Ma**.

A szűrő alkalmazásakor, az itt tettük, így az összes rekordhoz szűri a **kerület**, **Store**, és **értékesítési** táblák. Közötti kapcsolat szűrőiránya miatt azonban **értékesítési** és **idő**, **értékesítési** és **elem**, és **Elem** és **idő** táblák nem szűr le.

![A kiemelt kapcsolatok diagram nézet](media/row-level-security/diagram-view-9.png)

Ehhez a követelményhez ok lehet, azonban elemek nincs semmilyen rendelkeznek kezelők nem szeretnénk, ha azt sikerült kapcsolja be a kétirányú keresztszűrés a kapcsolat és a flow a biztonsági szűrőt mindkét irányban. Ezt megteheti közötti kapcsolat szerkesztésével **értékesítési** és **elem**, ehhez hasonló:

![Tartományok közötti kapcsolat szűrőiránya](media/row-level-security/edit-relationship-10.png)

Most szűrőket is áramolhasson a Sales tábláról, hogy a **elem** tábla:

![Szűrő iránya ikon kapcsolat a diagram nézet](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> A DirectQuery mód használata az adatok, ha szüksége a két lehetőség kiválasztásával szűrés kétirányú-közötti engedélyezése:

1. **Fájl** -> **lehetőségek és beállítások** -> **előzetes verziójú funkciók** -> **DirectQuery kétirányú keresztszűrés engedélyezése** .
2. **Fájl** -> **lehetőségek és beállítások** -> **DirectQuery** -> **korlátlan mérték engedélyezése DirectQuery módban**.

Kétirányú keresztszűrés kapcsolatos további információkért töltse le a [kétirányú keresztszűrés a Power BI Desktop és az SQL Server Analysis Services 2016](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx) tanulmány.

Ez kell tenni a Power BI Desktopban minden munkát értünk, de egy további munkákat, amely kell végrehajtani, hogy az RLS-szabályok meghatározott munkahelyi Power BI Embedded. Felhasználókat hitelesíti és engedélyezi az alkalmazás számára, és alkalmazási jogkivonatok segítségével egy adott Power BI Embedded-jelentés felhasználói hozzáférést. Power BI Embedded nem rendelkezik konkrét információkkal arról, hogy ki a felhasználó van. Az RLS működéséhez további kontextust az alkalmazás-jogkivonatára részeként kell:

* **felhasználónév** (nem kötelező) – használja az RLS Ez egy karakterláncérték, amely segítségével azonosítható a felhasználó az RLS-szabályok alkalmazásakor. Lásd a sorszintű biztonság a Power BI Embedded használatával:
* **szerepkörök** – sorszintű biztonsági szabályok alkalmazásakor kiválasztható szerepköröket tartalmazó karakterlánc. Több szerepkör átadásakor kell azokat átadni karakterlánctömbként.

A jogkivonat használatával hoz létre a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__) metódust. Ha a felhasználónév tulajdonság jelen, akkor szerepkörök is meg kell adnia legalább egy értéket.

Ha például sikerült módosítani a EmbedSample. Sikerült frissíteni a DashboardController sor 55

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

erre:

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

A teljes alkalmazás-jogkivonatára alábbihoz hasonlóan jelenik meg:

![Példa JSON webes jogkivonat](media/row-level-security/app-token-string-12.png)

Most rendelkező minden a helyére együtt, amikor valaki bejelentkezik az alkalmazás a jelentés megtekintéséhez láthatják az adatokat, amelyek a számukra engedélyezett jelenik meg, ahogyan azt a sorszintű biztonság.

![Az alkalmazás megjelenik a jelentés](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Lásd még

[Sorszintű biztonság (RLS) Power](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)