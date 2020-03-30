---
title: Automatikus skálázás v1
description: Automatikus skálázás és Az App Service-környezet v1. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687281"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatikus skálázás és App Service-környezet v1

> [!NOTE]
> Ez a cikk az App Service-környezet 1-es v1-es programjáról szól.  Az App Service-környezet egy újabb verziója könnyebben használható, és hatékonyabb infrastruktúrán fut. Ha többet szeretne megtudni az új verzió kezdődik az [App Service-környezet bemutatása](intro.md).
> 

Az Azure App Service-környezetek támogatják *az automatikus skálázást.* Az egyes munkavégző készletek et metrikák vagy ütemezés alapján automatikusan skálázhatja.

![Munkavégző készlet automatikus skálázási beállításai.][intro]

Az automatikus skálázás optimalizálja az erőforrás-kihasználtságot azáltal, hogy automatikusan növekszik és zsugorítja az App Service-környezetet a költségvetésnek és a terhelési profilnak megfelelően.

## <a name="configure-worker-pool-autoscale"></a>A munkavégzőkészlet automatikus skálázásának konfigurálása
Az automatikus skálázási funkciót a munkavégző készlet **Beállítások** lapján érheti el.

![A munkavégző készlet Beállítások lapja.][settings-scale]

Onnan a felület kell elég ismerős, mivel ugyanaz a tapasztalat, amit az App Service-csomag méretezésekénél látni. 

![Kézi méretezési beállítások.][scale-manual]

Automatikus skálázási profilt is konfigurálhat.

![Automatikus méretezési beállítások.][scale-profile]

Az automatikus skálázási profilok hasznosak a méretezési korlátozások beállításához. Így egy felső határ (2) beállításával egységes teljesítményélményben lehet része, ha egy alsó határskála-értéket (1) és egy kiszámítható költési korlátot állít be.

![Beállítások méretezése a profilban.][scale-profile2]

Miután definiált egy profilt, hozzáadhat automatikus skálázási szabályokat a feldolgozókészlet példányainak felfelé vagy lefelé méretezéséhez a profil által meghatározott határokon belül. Az automatikus skálázási szabályok metrikákon alapulnak.

![Méretezési szabály.][scale-rule]

 Bármely munkavégző készlet vagy előtér-metrikák automatikus skálázási szabályok definiálására használható. Ezek a metrikák ugyanazok a metrikák, amelyeket figyelhet az erőforrás-panel grafikonok, vagy riasztások beállítása.

## <a name="autoscale-example"></a>Példa automatikus méretezésre
Az App Service-környezet automatikus skálázása a legjobban egy forgatókönyv ben való átjárással szemléltethető.

Ez a cikk ismerteti az automatikus skálázás beállításakor a szükséges szempontokat. A cikk bemutatja az okat, amelyek az App Service-környezetben üzemeltetett automatikus skálázási alkalmazásszolgáltatás-környezetekben lépnek életbe.

### <a name="scenario-introduction"></a>Forgatókönyv bevezetése
Frank egy olyan vállalat rendszergazdai feladata, amely az általuk kezelt munkaterhelések egy részét egy App Service-környezetbe telepítette át.

Az App Service-környezet az alábbiak szerint van kézi méretezésre:

* **Elülső végek:** 3
* **Munkáskészlet 1:** 10
* **Munkáskészlet 2:** 5
* **Munkáskészlet 3:** 5

Az 1-es dolgozói készlet éles számítási feladatokhoz használatos, míg a 2-es és a 3-as dolgozói készlet a minőségbiztosításhoz (QA) és a fejlesztési munkaterhelésekhez használatos.

Az App Service-csomagok a minőségbiztosítási és fejlesztési manuális méretezési. Az éles App Service-csomag automatikus skálázásra van beállítva a terhelés és a forgalom változásainak kezeléséhez.

Frank nagyon jól ismeri a jelentkezési lapot. Tudják, hogy a terhelés csúcsideje 9:00 és 18:00 óra között van, mivel ez egy üzletági (LOB) alkalmazás, amelyet az alkalmazottak az irodában használnak. Használat csökken után, amikor a felhasználók kész az adott napon. A csúcsidőn kívül még mindig van némi terhelés, mivel a felhasználók távolról is elérhetik az alkalmazást mobileszközeik vagy otthoni számítógépeik használatával. Az éles App Service-csomag már konfigurálva van a processzorhasználat alapján automatikus skálázásra a következő szabályokszerint:

![A LOB alkalmazás speciális beállításai.][asp-scale]

| **Automatikus skálázási profil – Hétköznapok – App Service-csomag** | **Automatikus skálázási profil – Hétvégék – App Service-csomag** |
| --- | --- |
| **Név:** Hétköznap profil |**Név:** Hétvégi profil |
| **Skálázható:** Ütemezési és teljesítményszabályok |**Skálázható:** Ütemezési és teljesítményszabályok |
| **Profil:** Hétköznap |**Profil:** Hétvégén |
| **Típus:** Ismétlődés |**Típus:** Ismétlődés |
| **Céltartomány:** 5–20 példány |**Céltartomány:** 3–10 példány |
| **Napok:** Hétfő, Kedd, Szerda, Csütörtök, Péntek |**Napok:** Szombat, Vasárnap |
| **Kezdés időpontja:** 9:00 |**Kezdés időpontja:** 9:00 |
| **Időzóna:** UTC-08 |**Időzóna:** UTC-08 |
|  | |
| **Automatikus skálázási szabály (Felskálázás)** |**Automatikus skálázási szabály (Felskálázás)** |
| **Erőforrás:** Éles környezet (App Service-környezet) |**Erőforrás:** Éles környezet (App Service-környezet) |
| **Mérőszám:** CPU % |**Mérőszám:** CPU % |
| **Művelet:** 60%-nál nagyobb |**Művelet:** 80%-nál nagyobb |
| **Időtartam:** 5 perc |**Időtartam:** 10 perc |
| **Időösszesítés:** Átlagos |**Időösszesítés:** Átlagos |
| **Művelet:** A számlálás növelése 2-vel |**Művelet:** A számlálás növelése 1-vel |
| **Lehűlés (perc):** 15 |**Lehűlés (perc):** 20 |
|  | |
| **Automatikus skálázási szabály (Leskálázás)** |**Automatikus skálázási szabály (Leskálázás)** |
| **Erőforrás:** Éles környezet (App Service-környezet) |**Erőforrás:** Éles környezet (App Service-környezet) |
| **Mérőszám:** CPU % |**Mérőszám:** CPU % |
| **Művelet:** Kevesebb mint 30% |**Művelet:** Kevesebb mint 20% |
| **Időtartam:** 10 perc |**Időtartam:** 15 perc |
| **Időösszesítés:** Átlagos |**Időösszesítés:** Átlagos |
| **Művelet:** A számlálás csökkentése 1-vel |**Művelet:** A számlálás csökkentése 1-vel |
| **Lehűlés (perc):** 20 |**Lehűlés (perc):** 10 |

### <a name="app-service-plan-inflation-rate"></a>App Service-csomag inflációs rátája
Az automatikus skálázásra konfigurált App Service-csomagok óránkénti maximális díjcsomagban teszik meg. Ez a sebesség az automatikus skálázási szabályban megadott értékek alapján számítható ki.

Az App *Service-csomag inflációs rátájának* megértése és kiszámítása fontos az App Service-környezet automatikus skálázása, mert a munkavégző készlet méretezési módosításai nem azonnaliak.

Az App Service-csomag inflációs rátája a következőképpen történik:

![App Service-csomag inflációs ráta számítása.][ASP-Inflation]

Az automatikus skálázás – felskálázás szabály az éles App Service-csomag Hétköznap profilja alapján:

![App Service-csomag inflációs ráta hétköznapokon alapuló automatikus skálázás – felskálázási szabály.][Equation1]

Az automatikus skálázás – felskálázás szabály esetén az éles App Service-csomag hétvégi profilja esetén a képlet a következő:

![App Service-csomag inflációs ráta a hétvégék alapján automatikus skálázás – felskálázási szabály.][Equation2]

Ez az érték a leskálázási műveletekhez is kiszámítható.

Az automatikus skálázás – leskálázás i szabály alapján az éles App Service-csomag Hétköznap profilja alapján ez a következőképpen fog kinézni:

![App Service-csomag inflációs ráta hétköznapokon alapuló automatikus skálázás – leskálázási szabály.][Equation3]

Az automatikus skálázás – leskálázás i szabály esetén az éles App Service-csomag hétvégi profilja esetén a képlet a következő:  

![App Service-csomag inflációs ráta a hétvégék alapján automatikus skálázás – leskálázási szabály.][Equation4]

Az éles App Service-csomag a hét folyamán legfeljebb nyolc példány/óra, hétvégén pedig négy példány/óra sebességgel növekedhet. A hét folyamán legfeljebb négy példány/óra, hétvégén pedig hat példány/óra sebességgel szabadíthat fel példányokat.

Ha több App Service-csomag van tárolva egy feldolgozókészletben, ki kell számítania a *teljes inflációs rátát* az adott munkavégző készletben üzemeltetett összes App Service-csomag inflációs rátájának összegeként.

![Teljes inflációs ráta számítás a feldolgozókészletben tárolt több App Service-csomag hoz létre.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Az App Service-csomag inflációs rátájának használata a munkavégzőkészlet automatikus skálázási szabályainak meghatározásához
Az automatikus skálázásra konfigurált, az App Service-csomagokat üzemeltető munkavégző készleteket kapacitáspufferként kell lefoglalni. A puffer lehetővé teszi, hogy az automatikus skálázási műveletek nő, és szükség szerint zsugorítja az App Service-csomag. A minimális puffer a számított Teljes app service-csomag inflációs ráta.

Mivel az App Service-környezet méretezési műveleteinek alkalmazása némi időt vesz igénybe, minden változásnak figyelembe kell vennie a további igényváltozásokat, amelyek egy méretezési művelet folyamatban lévő állapotában fordulhatnak elő. A késés hez azt javasoljuk, hogy a számított Teljes alkalmazásszintű szolgáltatáscsomag inflációs rátáját használja az egyes automatikus skálázási műveletekhez hozzáadott példányok minimális számaként.

Ezzel az információval Frank a következő automatikus skálázási profilt és szabályokat határozhatja meg:

![Automatikus skálázási profilszabályok a LOB-hoz példa.][Worker-Pool-Scale]

| **Automatikus skálázási profil – Hétköznapok** | **Autoscale profil – Hétvégék** |
| --- | --- |
| **Név:** Hétköznap profil |**Név:** Hétvégi profil |
| **Skálázható:** Ütemezési és teljesítményszabályok |**Skálázható:** Ütemezési és teljesítményszabályok |
| **Profil:** Hétköznap |**Profil:** Hétvégén |
| **Típus:** Ismétlődés |**Típus:** Ismétlődés |
| **Céltartomány:** 13–25 példány |**Céltartomány:** 6–15 példány |
| **Napok:** Hétfő, Kedd, Szerda, Csütörtök, Péntek |**Napok:** Szombat, Vasárnap |
| **Kezdés időpontja:** 7:00 |**Kezdés időpontja:** 9:00 |
| **Időzóna:** UTC-08 |**Időzóna:** UTC-08 |
|  | |
| **Automatikus skálázási szabály (Felskálázás)** |**Automatikus skálázási szabály (Felskálázás)** |
| **Erőforrás:** Dolgozói készlet 1 |**Erőforrás:** Dolgozói készlet 1 |
| **Mérőszám:** Elérhető dolgozók |**Mérőszám:** Elérhető dolgozók |
| **Művelet:** Kevesebb, mint 8 |**Művelet:** Kevesebb mint 3 |
| **Időtartam:** 20 perc |**Időtartam:** 30 perc |
| **Időösszesítés:** Átlagos |**Időösszesítés:** Átlagos |
| **Művelet:** A szám növelése 8-mal |**Művelet:** A számlálás növelése 3-mal |
| **Lehűlés (perc):** 180 |**Lehűlés (perc):** 180 |
|  | |
| **Automatikus skálázási szabály (Leskálázás)** |**Automatikus skálázási szabály (Leskálázás)** |
| **Erőforrás:** Dolgozói készlet 1 |**Erőforrás:** Dolgozói készlet 1 |
| **Mérőszám:** Elérhető dolgozók |**Mérőszám:** Elérhető dolgozók |
| **Művelet:** 8-nál nagyobb |**Művelet:** 3-nál nagyobb |
| **Időtartam:** 20 perc |**Időtartam:** 15 perc |
| **Időösszesítés:** Átlagos |**Időösszesítés:** Átlagos |
| **Művelet:** A szám csökkentése 2-vel |**Művelet:** A szám csökkentése 3-mal |
| **Lehűlés (perc):** 120 |**Lehűlés (perc):** 120 |

A profilban definiált céltartományt az App Service-terv + puffer profiljában meghatározott minimális példányok számítják ki.

A Maximális tartomány a munkavégző készletben tárolt összes App Service-csomag összes maximális tartományának összege lenne.

A skálázási szabályok növelése díja az App Service-csomag inflációs rátájának legalább tízszeresére kell beállítani a felskálázáshoz.

A csökkenések száma az App Service-csomag inflációs rátájának 1/2-szerese vagy 1-szerese közötti értékre állítható.

### <a name="autoscale-for-front-end-pool"></a>Automatikus skálázás előtér-készlethez
Az előtér-automatikus skálázás szabályai egyszerűbbek, mint a feldolgozókészletek esetében. Elsősorban meg kell  
győződjön meg arról, hogy a mérés időtartama és a letöltőidő időzítők úgy vélik, hogy az App Service-csomag méretezési műveletei nem azonnaliak.

Ebben a forgatókönyvben Frank tudja, hogy a hibaarány növekszik, miután az előtér-végek elérik a 80%-os CPU-kihasználtságot, és az automatikus skálázási szabályt úgy állítja be, hogy növelje a példányokat az alábbiak szerint:

![Az előtér-készlet automatikus skálázási beállításai.][Front-End-Scale]

| **Automatikus skálázási profil – Előtér-végződések** |
| --- |
| **Név:** Autoscale - Front ends |
| **Skálázható:** Ütemezési és teljesítményszabályok |
| **Profil:** Mindennapi |
| **Típus:** Ismétlődés |
| **Céltartomány:** 3–10 példány |
| **Napok:** Mindennapi |
| **Kezdés időpontja:** 9:00 |
| **Időzóna:** UTC-08 |
|  |
| **Automatikus skálázási szabály (Felskálázás)** |
| **Erőforrás:** Előtér-készlet |
| **Mérőszám:** CPU % |
| **Művelet:** 60%-nál nagyobb |
| **Időtartam:** 20 perc |
| **Időösszesítés:** Átlagos |
| **Művelet:** A számlálás növelése 3-mal |
| **Lehűlés (perc):** 120 |
|  |
| **Automatikus skálázási szabály (Leskálázás)** |
| **Erőforrás:** Dolgozói készlet 1 |
| **Mérőszám:** CPU % |
| **Művelet:** Kevesebb mint 30% |
| **Időtartam:** 20 perc |
| **Időösszesítés:** Átlagos |
| **Művelet:** A szám csökkentése 3-mal |
| **Lehűlés (perc):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
