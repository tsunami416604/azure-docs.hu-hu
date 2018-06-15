---
title: A Power BI munkaterület gyűjteményekkel sorszintű biztonság
description: Biztonsági szint a Power BI-munkaterület gyűjteményekkel kapcsolatos részletek
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 7256e2f798fbc32c098f19f60b62e577300868c7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414099"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>A Power BI munkaterület gyűjteményekkel sorszintű biztonság

Sorszintű biztonság (RLS) a felhasználói hozzáférés korlátozása adott adatok egy jelentést vagy adatkészletet, így több másik felhasználó használja ugyanazt a jelentést, minden megnéz különböző adatainak közben használható. A Power BI munkaterület gyűjtemények konfigurálva az RLS adatkészleteket támogatja.

![A sorszintű biztonság van a Power BI munkaterület gyűjtemények folyamata](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> A Power BI munkaterületi gyűjtemények szolgáltatás elavult, és 2018 júniusáig vagy a szerződésében jelzett időpontig érhető el. Javasoljuk, hogy az alkalmazása zavartalan működése érdekében tervezze meg a migrációt a Power BI Embedded szolgáltatásba. Az adatok a Power BI Embedded szolgáltatásba való migrálásának részleteiért lásd a [Power BI munkaterületi gyűjtemények tartalmának Power BI Embedded szolgáltatásba történő migrálásával](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/) foglalkozó cikket.

RLS előnyeit, fontos, hogy tudomásul veszi, hogy három fő alapelv; Felhasználók, szerepkörök és szabályok. Megtudhatja, hogy minden egyes részletes bemutatása:

**Felhasználók** – a rendszer a tényleges végfelhasználók jelentések megtekintése. A Power BI munkaterület gyűjtemények a felhasználók a username tulajdonság egy alkalmazási jogkivonatot a azonosítják.

**Szerepkörök** – szerepkörökhöz tartozó felhasználókat. Egy szerepkör szabályok tárolója, és képes neve például "Értékesítési igazgató" vagy "Értékesítési Rep." A Power BI munkaterület gyűjtemények felhasználók azonosítja a szerepkörök tulajdonságot egy alkalmazási jogkivonatot.

**Szabályok** – szerepkörök rendelkezik szabályokat, és ezeket a szabályokat a tényleges szűrőket, hogy az adatok alkalmazható. Ennek oka lehet egyszerűen "ország = USA" vagy más sokkal több dinamikus.

### <a name="example"></a>Példa

Ez a cikk a többi nyújtunk RLS szerzői, és majd fel, amely beágyazott alkalmazáson belül. A példában a [kiskereskedelmi elemzési minta](http://go.microsoft.com/fwlink/?LinkID=780547) PBIX-fájl.

![Példa értékesítési jelentést](media/row-level-security/scenario-2.png)

A kiskereskedelmi elemzési minta az összes értékesítés egy adott kereskedelmi lánc jeleníti meg. Nélkül RLS, függetlenül attól, milyen körzeti manager jelentkezik be, és megtekinti a jelentést, akkor jelenik meg ugyanazokat az adatokat. Vezető felügyeleti megállapítása szerint a minden körzet manager csak kell megjelennie az értékesítési az általuk felügyelt tárolókat, és ehhez az RLS használhatjuk.

RLS állította össze a Power BI Desktopban. A DataSet adatkészlet és a jelentés megnyitásakor, a lehet diagram nézetet, hogy a séma váltani:

![A Power BI Desktop modell diagramja](media/row-level-security/diagram-view-3.png)

Az alábbiakban néhány dolog, amit a sémát láthatja:

* Az összes mérték, például **összes értékesítés**, tárolja a **értékesítési** ténytábla.
* Nincsenek négy további kapcsolódó dimenziótáblák: **elem**, **idő**, **tároló**, és **körzeti**.
* A nyilak a kapcsolat sorok azt jelzik, hogy mely szűrők haladjanak, az egyik tábla másik módja. Például, ha a szűrőt el van helyezve **idő [dátum]**, az aktuális sémában azt fog csak szűrni le az értékeket a **értékesítési** tábla. Más táblák befolyásolhat a szűrőt, mert a nyilak a kapcsolat sorok összes mutasson az értékesítési táblázathoz és számítógépnél nem.
* A **körzeti** táblázat azt jelzi, aki a kezelő minden körzet:
  
  ![Körzeti táblázat sorait](media/row-level-security/district-table-4.png)

Ebben a sémában alapján, ha a szűrőt érvénybe lépni a **körzeti Manager** az körzeti táblázatot, és ha ez a szűrő megfelel a felhasználó a jelentés megtekintése, amely szűrni is szűrők le a **tároló** és  **Értékesítési** táblázatokat csak jeleníthetők meg, hogy adott körzeti manager.

Itt módját:

1. Kattintson a modellezési lap **szerepkörök kezelése**.  
   ![Szerepkörök gombra a menüszalag modellezése kezelése](media/row-level-security/modeling-tab-5.png)
2. Hozzon létre egy új szerepkör neve **Manager**.  
   ![A Power BI Desktop szerepkörök létrehozása](media/row-level-security/manager-role-6.png)
3. Az a **körzeti** tábla adja meg a következő DAX-kifejezés: **[körzeti Manager] = USERNAME()**  
   ![Szerepkör tábla DAX-kifejezése](media/row-level-security/manager-role-7.png)
4. Győződjön meg arról, a szabályok dolgozik, az a **modellezési** lapra, majd **szerepkörök nézetként**, és írja be a következőt:  
   ![Megtekinteni, mert a szerepkörök](media/row-level-security/view-as-roles-8.png)

   A jelentések most adatait jeleníti meg, mintha csak akkor felhasználóként van bejelentkezve **Andrew Ma**.

A szűrés, azt itt volt módja szűrők le minden rekordot a **körzeti**, **tároló**, és **értékesítési** táblák. A szűrés irányát a közötti kapcsolatok miatt azonban **értékesítési** és **idő**, **értékesítési** és **elem**, és **Elem** és **idő** táblák nem lesznek szűrve le.

![A kijelölt kapcsolatokat a diagram nézet](media/row-level-security/diagram-view-9.png)

Amely ehhez a követelményhez ok lehet, azonban elemeket, amelynek nem rendelkeznek bármely értékesítési kezelők nem szeretnénk, ha azt sikerült kapcsolja be a kétirányú keresztszűrés a kapcsolat és a biztonsági szűrés mindkét irányban folyik. Ezt megteheti a közötti kapcsolat szerkesztésével **értékesítési** és **elem**, ez, például:

![Kereszt-szűrés irányának kapcsolat](media/row-level-security/edit-relationship-10.png)

Most, szűrők is áramolhasson az értékesítési táblából a **elem** tábla:

![Szűrő diagram nézet a kapcsolat irányát ikon](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Az adatok használata DirectQuery módban, kétirányú-határokon szűrés e két beállítás kiválasztásával engedélyezése szeretné:

1. **Fájl** -> **lehetőségek és beállítások** -> **előzetes verziójú funkciók** -> **DirectQuery kétirányú keresztszűrés engedélyezése** .
2. **Fájl** -> **lehetőségek és beállítások** -> **DirectQuery** -> **korlátlan mérték engedélyezése DirectQuery módban**.

Kétirányú keresztszűrés kapcsolatos további információkért töltse le a [kétirányú keresztszűrés a SQL Server Analysis Services 2016 és a Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) tanulmány.

Ez foglalja össze a munka azért van szükség, a Power BI Desktopban, de egy további munkákat, amelyet a érdekében, hogy az RLS-szabályok a Power BI Embedded munkahelyi meghatározott. A felhasználók hitelesítése és az alkalmazás által engedélyezett, és alkalmazási jogkivonatok segítségével egy adott Power BI Embedded jelentést felhasználói hozzáférést. A Power BI Embedded nem rendelkezik a felhasználó ki van információk. Az RLS működéséhez néhány további környezet átadása az alkalmazás jogkivonat részeként kell:

* **felhasználónév** (opcionális) – ami az RLS használt karakterlánc, amely a felhasználó RLS szabályok alkalmazásakor azonosításához használható. Lásd a biztonság a Power BI Embedded sor segítségével:
* **szerepkörök** – a szerepköröket válassza ki a sorszintű biztonság szabályok alkalmazásakor tartalmazó karakterláncot. Ha több szerepkör, át kell őket, egy karakterlánc-tömbben.

A token használatával hoz létre a [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__) metódust. A username tulajdonság esetén is át kell legalább egy értéket a szerepkört.

Például megváltoztathatja az EmbedSample. A DashboardController sor 55 frissítése sikerült

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

erre:

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

A teljes alkalmazás jogkivonat a következőhöz hasonló:

![JSON web token példa](media/row-level-security/app-token-string-12.png)

Most a minden a helyére együtt, ha valaki jelentkezik be, ezt a jelentést az alkalmazás látják kapjanak megtekintéséhez a sorszintű biztonság által meghatározott adatok.

![Az alkalmazás megjelenik a jelentés](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Lásd még

[A sorszintű biztonságot (RLS) rendelkező Power](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Hitelesítés és engedélyezés a Power BI-munkaterületcsoportok használatával](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript beágyazási minta](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

További kérdései vannak? [Tegye próbára a Power BI közösségét](http://community.powerbi.com/)