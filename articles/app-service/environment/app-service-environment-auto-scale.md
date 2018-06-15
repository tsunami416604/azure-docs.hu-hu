---
title: Automatikus skálázás és az App Service Environment-környezet 1-es verzió
description: Automatikus skálázás és az App Service Environment-környezet
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 0feb6e4862f643c35a58c0321181bdda22b032e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837133"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatikus skálázás és az App Service Environment-környezet 1-es verzió

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 tárgya.  Az App Service-környezet, amely könnyebben használható, és nagyobb teljesítményű infrastruktúra fut egy újabb verziója van telepítve. További információt az új verzió útmutató a [az App Service Environment bemutatása](intro.md).
> 

Támogatja az Azure App Service-környezetek *automatikus skálázás*. Használhatja az automatikus skálázás egyedi feldolgozókészletek metrikák vagy ütemezés alapján.

![A feldolgozókészleten automatikus skálázás beállításai.][intro]

Automatikus skálázás optimalizálja az erőforrás-használat automatikusan növekvő és zsugorítását App Service-környezet elférjen a költségvetést, és vagy betöltése.

## <a name="configure-worker-pool-autoscale"></a>Munkavégző készlet automatikus skálázás konfigurálása
Az automatikus skálázás funkciói végezheti el a **beállítások** lapján a munkavégző készletét.

![A feldolgozókészleten beállítások lapján.][settings-scale]

Ezekből a felületet kell viszonylag jól ismert, mert ugyanazt a felhasználói élményt, hogy látni, ha az App Service-csomag méretezni. 

![Manuális skálázási beállításokat.][scale-manual]

Az automatikus skálázási profilok is konfigurálhatja.

![Automatikus skálázási beállításokat.][scale-profile]

Automatikus skálázási profilok hasznosak a skála korlátozható. Ezzel a módszerrel lehet egy egységes élmény úgy, hogy egy felső határa (2) egy alsó határ skálaértéknél (1) és egy előre jelezhető ráfordítás cap beállításával teljesítmény.

![A profil beállítását.][scale-profile2]

Egy profil megadása után az automatikus skálázási szabályok méretezési felfelé vagy lefelé a munkavégző készletét. a profil által meghatározott határain belül található példányok száma is hozzáadhat. Automatikus skálázási szabályok metrikák alapulnak.

![Skálázási szabályhoz.][scale-rule]

 A feldolgozókészleten vagy előtér-metrikák automatikus skálázási szabályok meghatározásához használható. Ezeket a mérési metrikák az erőforrás panel diagramokban figyelése, vagy állítson be riasztásokat a rendszer.

## <a name="autoscale-example"></a>Automatikus skálázás – példa
Egy App Service Environment-környezet automatikus skálázás legjobb érdekében a forgatókönyv segítségével mutatható be.

Ez a cikk ismerteti a szükséges szempontok, automatikus skálázás beállításához. A cikk végigvezeti az olyan műveleteket, amelyek, ha a figyelembe a automatikus skálázás App Service-környezetek, amelyek App Service Environment-környezet.

### <a name="scenario-introduction"></a>A forgatókönyv bemutatása
Frank a sysadmin (rendszergazda), akik az App Service-környezetek át a munkaterheléseket, általa része lett telepítve a vállalati.

Az App Service Environment-környezet van konfigurálva manuális méretezési az alábbiak szerint:

* **Első végződik:** 3
* **A feldolgozókészleten 1**: 10
* **A feldolgozókészleten 2**: 5
* **A feldolgozókészleten 3**: 5

1 feldolgozókészletek használjuk a termelési számítási feladatokhoz, feldolgozókészletek 2 és 3 feldolgozókészletek a minőségi megbízhatósági (QA) és a fejlesztői munkaterhelések szolgálnak.

Az App Service-csomagokról QA és fejlesztői manuális méretezési vannak konfigurálva. Az App Service-csomag üzemi automatikus skálázási értéke változások a terhelési és a forgalom kezelésére.

Frank tisztában van az alkalmazás. Tudja, hogy a terhelést csúcsidőszakon 9:00-kor és 18:00:00 közé esnek, mivel ez egy alkalmazottak által használt során az office-üzletági (LOB) alkalmazás. Ezt követően használati elutasítja azokat a felhasználókat adott napon szabásakor. Csúcsidőszakon kívüli nincs még néhány betöltése mert felhasználók férhetnek hozzá az alkalmazás távolról a mobileszközök és az otthoni számítógépek használatával. Az üzemi App Service-csomag már be van állítva a következő szabályokat a CPU-használat alapján automatikus skálázásra:

![Az ÜZLETÁGI alkalmazás beállításait.][asp-scale]

| **Automatikus skálázási profil – létrehozását – az App Service-csomag** | **Automatikus skálázási profil – hétvégéket – az App Service-csomag** |
| --- | --- |
| **Name:** hétköznap profil |**Name:** hétvégi profil |
| **A skála:** ütemezés és a teljesítmény szabályok |**A skála:** ütemezés és a teljesítmény szabályok |
| **Profil:** hétköznapokon |**Profil:** hétvégi |
| **Típus:** ismétlődési |**Típus:** ismétlődési |
| **Tartomány cél:** 5-20 példányok |**Tartomány cél:** 3 – 10 példányok |
| **Nap:** hétfő, kedd, szerda, csütörtök, péntek |**Nap:** , szombat és vasárnap |
| **Kezdési időpont:** 9:00-kor |**Kezdési időpont:** 9:00-kor |
| **Időzóna:** UTC-08 |**Időzóna:** UTC-08 |
|  | |
| **Automatikus skálázási szabály (vertikális Felskálázás)** |**Automatikus skálázási szabály (vertikális Felskálázás)** |
| **Erőforrás:** éles (App Service Environment-környezet) |**Erőforrás:** éles (App Service Environment-környezet) |
| **A metrika:** CPU % |**A metrika:** CPU % |
| **Művelet:** nagyobb, mint 60 % |**Művelet:** 80 %-nál nagyobb |
| **Időtartam:** 5 perc |**Időtartam:** 10 perc |
| **Összesítési idő:** átlagos |**Összesítési idő:** átlagos |
| **Művelet:** számának növeléséhez a 2. régiója |**Művelet:** számának növeléséhez 1 |
| **Ritkán használt adatok le (perc):** 15 |**Ritkán használt adatok le (perc):** 20 |
|  | |
| **Automatikus skálázási szabály (méretezési le)** |**Automatikus skálázási szabály (méretezési le)** |
| **Erőforrás:** éles (App Service Environment-környezet) |**Erőforrás:** éles (App Service Environment-környezet) |
| **A metrika:** CPU % |**A metrika:** CPU % |
| **Művelet:** 30 %-nál kisebb |**Művelet:** 20 %-nál kisebb |
| **Időtartam:** 10 perc |**Időtartam:** 15 perc |
| **Összesítési idő:** átlagos |**Összesítési idő:** átlagos |
| **Művelet:** számának csökkentése 1 |**Művelet:** számának csökkentése 1 |
| **Ritkán használt adatok le (perc):** 20 |**Ritkán használt adatok le (perc):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Az alkalmazásszolgáltatási csomag inflációja
App Service-csomagokról konfigurált automatikus skálázásra ehhez óránként sebességet. Ezt az értéket a megadott automatikus skálázási szabály megadott értékek alapján lehet kiszámolni.

Megismerés és kiszámítása a *App Service-csomag inflációja* fontos az App Service-környezet automatikus skálázás, mert a feldolgozókészleten méretezési módosításai nincsenek azonnali.

Az App Service-csomag inflációja kiszámítása a következőképpen történik:

![Az alkalmazásszolgáltatási csomag inflációja számítási.][ASP-Inflation]

Az automatikus skálázás – a hét napja profil a termelési App Service-csomag vertikális Felskálázás szabály alapján:

![App Service csomag infláció arány alapján automatikus skálázás – vertikális Felskálázás szabály létrehozását.][Equation1]

Automatikus skálázási – a hétvégi profil a termelési App Service-csomag vertikális Felskálázás szabály esetében a képlet kellene tartoznia:

![App Service csomag infláció arány hétvégéket automatikus skálázás – vertikális Felskálázás szabály alapján.][Equation2]

Ez az érték is kerülhet sor, lefelé méretezéshez műveletekhez.

Az automatikus skálázás – a hét napja profil az App Service-csomag, az üzemi méretezési le szabály alapján ez lenne az alábbiak szerint:

![App Service csomag infláció arány alapján automatikus skálázás – méretezési le szabály létrehozását.][Equation3]

Automatikus skálázási – az App Service-csomag üzemi hétvégi profiljának méretezési le szabály esetében a képlet kellene tartoznia:  

![App Service csomag infláció arány hétvégéket automatikus skálázás – méretezési le szabály alapján.][Equation4]

Az App Service-csomag üzemi milyen mértékben növelhető a maximális sebesség nyolc példányok/óra a héten és négy példányok/óra a hétvégén. Azt is megjelenhetnek példányok négy példányok/óra során a hét sebességet és hat példányok/óra során tartozik.

Ha több App Service-csomagokról a feldolgozókészleten alatt tárolt, kell számítani a *teljes inflációja* az összes az App Service-csomagok, amelyeket a inflációja összege az adott munkavégző készletét tartalmazó.

![A feldolgozókészleten üzemeltetett több App Service-csomagokról teljes inflációja számítását.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Az App Service-csomag inflációja segítségével munkavégző készlet automatikus skálázási szabályok definiálása
Munkavégző készletbe üzemeltető App Service-csomagokról konfigurált automatikus skálázásra kell kapacitás puffert lefoglalni. A puffer lehetővé teszi, hogy az automatikus skálázási művelet növelhető vagy csökkenthető az App Service-csomag, igény szerint. A minimális puffer lenne a számított teljes App Service megtervezése inflációja.

App Service-környezet skálázási műveleteinek néhány történő alkalmazása időbe telhet, mert további fordulhat elő, amíg folyamatban van egy skálázási műveletet igény szerinti módosítások változásait kell fiókot. Ez a késés teljesítése érdekében azt javasoljuk, hogy használ a számított teljes App Service megtervezése inflációja a hozzáadott példányok minimális számának minden automatikus skálázási művelet.

Az információ Frank határozhatja meg a következő automatikus skálázási profil és a szabályok:

![Automatikus skálázási profil szabályok LOB például.][Worker-Pool-Scale]

| **Automatikus skálázási profil – hétköznapokon** | **Automatikus skálázási profil – hétvégéket** |
| --- | --- |
| **Name:** hétköznap profil |**Name:** hétvégi profil |
| **A skála:** ütemezés és a teljesítmény szabályok |**A skála:** ütemezés és a teljesítmény szabályok |
| **Profil:** hétköznapokon |**Profil:** hétvégi |
| **Típus:** ismétlődési |**Típus:** ismétlődési |
| **Tartomány cél:** 13-25 példányok |**Tartomány cél:** 6 – 15 példányok |
| **Nap:** hétfő, kedd, szerda, csütörtök, péntek |**Nap:** , szombat és vasárnap |
| **Kezdési időpont:** 7:00-kor |**Kezdési időpont:** 9:00-kor |
| **Időzóna:** UTC-08 |**Időzóna:** UTC-08 |
|  | |
| **Automatikus skálázási szabály (vertikális Felskálázás)** |**Automatikus skálázási szabály (vertikális Felskálázás)** |
| **Erőforrás:** feldolgozókészletek 1 |**Erőforrás:** feldolgozókészletek 1 |
| **A metrika:** WorkersAvailable |**A metrika:** WorkersAvailable |
| **Művelet:** legalább 8 |**Művelet:** legalább 3 |
| **Időtartam:** 20 perc |**Időtartam:** 30 perc |
| **Összesítési idő:** átlagos |**Összesítési idő:** átlagos |
| **Művelet:** számának növeléséhez 8 |**Művelet:** növeléséhez száma 3 |
| **Ritkán használt adatok le (perc):** 180 |**Ritkán használt adatok le (perc):** 180 |
|  | |
| **Automatikus skálázási szabály (méretezési le)** |**Automatikus skálázási szabály (méretezési le)** |
| **Erőforrás:** feldolgozókészletek 1 |**Erőforrás:** feldolgozókészletek 1 |
| **A metrika:** WorkersAvailable |**A metrika:** WorkersAvailable |
| **Művelet:** 8-nál nagyobb |**Művelet:** 3-nál nagyobb |
| **Időtartam:** 20 perc |**Időtartam:** 15 perc |
| **Összesítési idő:** átlagos |**Összesítési idő:** átlagos |
| **Művelet:** számának csökkentése 2 |**Művelet:** csökkentése száma 3 |
| **Ritkán használt adatok le (perc):** 120 |**Ritkán használt adatok le (perc):** 120 |

A célként megadott tartomány-profilban megadott az App Service-csomag + puffer-profilban megadott minimális példányainak számítja ki.

A maximális tartomány lenne a munkavégző készletben tárolt összes App Service-csomagokról az összes maximális tartomány összege.

A skálázási szabályok növekedése számát létre kell hozni legalább az App Service csomag inflációja bővített X 1.

Csökkentse száma módosítható egy 1/2 X vagy az App Service csomag inflációja bővített X 1 közötti le.

### <a name="autoscale-for-front-end-pool"></a>Automatikus skálázás előtér-készlet
Az előtér-automatikus skálázási szabályok egyszerűbbek, mint a feldolgozókészletek. Elsősorban akkor  
Győződjön meg arról, hogy a mérés és a cooldown időzítők időtartama fontolja meg, hogy az App Service-csomagot a skálázási műveletek nem azonnali.

Ebben a forgatókönyvben a Frank tudja, hogy a Hibaarány előtér-webkiszolgálóinak eléri a 80 %-os processzorhasználatot megnő, és beállítja az automatikus skálázási szabály, amely növeli következőképpen példányok:

![Automatikus skálázási beállításokat előtér-készlet.][Front-End-Scale]

| **Automatikus skálázási profil – első karakterlánccal végződik-e** |
| --- |
| **Name:** automatikus skálázás – első karakterlánccal végződik-e |
| **A skála:** ütemezés és a teljesítmény szabályok |
| **Profil:** mindennapi |
| **Típus:** ismétlődési |
| **Tartomány cél:** 3 – 10 példányok |
| **Nap:** mindennapi |
| **Kezdési időpont:** 9:00-kor |
| **Időzóna:** UTC-08 |
|  |
| **Automatikus skálázási szabály (vertikális Felskálázás)** |
| **Erőforrás:** előtér-készlet |
| **A metrika:** CPU % |
| **Művelet:** nagyobb, mint 60 % |
| **Időtartam:** 20 perc |
| **Összesítési idő:** átlagos |
| **Művelet:** növeléséhez száma 3 |
| **Ritkán használt adatok le (perc):** 120 |
|  |
| **Automatikus skálázási szabály (méretezési le)** |
| **Erőforrás:** feldolgozókészletek 1 |
| **A metrika:** CPU % |
| **Művelet:** 30 %-nál kisebb |
| **Időtartam:** 20 perc |
| **Összesítési idő:** átlagos |
| **Művelet:** csökkentése száma 3 |
| **Ritkán használt adatok le (perc):** 120 |

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
