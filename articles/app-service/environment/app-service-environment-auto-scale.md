---
title: Automatikus skálázás és az App Service Environment v1 – Azure
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
ms.custom: seodec18
ms.openlocfilehash: 29a639142395c43fea06c1d6d18909b3c9f33b86
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270025"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatikus skálázás és az App Service Environment-környezet v1

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 szól.  Nincs az App Service-környezet, amely egyszerűbb és nagyobb teljesítményű infrastruktúra fut egy újabb verziója. További információ az új verzió elindítása a [az App Service Environment bemutatása](intro.md).
> 

Támogatja az Azure App Service-környezetek *az automatikus skálázás*. Egyéni feldolgozókészletek automatikus méretezését metrika vagy ütemezés szerint is.

![A feldolgozókészletek automatikus skálázási beállítások.][intro]

Az automatikus skálázás automatikusan növekvő és zsugorítását a költségvetéshez igazodó, és vagy profil betöltése az App Service environment optimalizálja az erőforrások kihasználtságát.

## <a name="configure-worker-pool-autoscale"></a>Feldolgozó készlet automatikus skálázás konfigurálása
Automatikus skálázási funkcióit is elérheti a **beállítások** feldolgozókészletként lapján.

![A feldolgozói készlethez tartozó beállítások lapon.][settings-scale]

Itt a felületen lehet viszonylag jól, mert ugyanazt a felhasználói élményt, hogy mikor méretezése App Service-csomag. 

![Manuális skálázás beállításai.][scale-manual]

Automatikus skálázási profil is konfigurálhatja.

![Az automatikus méretezési beállítások.][scale-profile]

Automatikus méretezési profilok hasznosak lehetnek a méretezési korlátok beállításához. Ebben az esetben akkor is egy egységes teljesítményt élményt olyan alsó határérték értéke (1) és a egy előre jelezhető költségek korlát beállításával egy felső határérték (2) beállításával.

![A profil beállításait.][scale-profile2]

A profil határozza meg, miután automatikus skálázási szabály-vagy leskálázhatja a munkavégző készletét. a profil által meghatározott határán belül a példányok száma is hozzáadhat. Az automatikus skálázási szabályok metrikák alapulnak.

![Skálázási szabályhoz.][scale-rule]

 Bármennyi feldolgozókészlet vagy előtérmetrika használható az automatikus skálázási szabályok meghatározásához. Ezek a metrikák olyan metrikák figyelheti az erőforrás panel grafikonok, vagy a riasztásokat állíthat be.

## <a name="autoscale-example"></a>Automatikus skálázási példa
Az automatikus méretezés App Service Environment-környezetek a legjobban, ajánljuk figyelmébe a forgatókönyv által mutatható be.

Ez a cikk leírja a szükséges szempontok, automatikus méretezés beállítása során. A cikk végigvezeti Önt, ha a Ön figyelembe vennie az automatikus skálázás App Service-környezetek, amelyek az App Service-környezetben üzemeltetett interakciókat.

### <a name="scenario-introduction"></a>A forgatókönyv bemutatása
Frank a sysadmin (rendszergazda), aki a számítási feladatok, amelyek kezel egy része lett migrálva az App Service-környezet a vállalati.

Az App Service-környezet konfigurációja manuális skálázás, a következő:

* **Az előtérrendszerek:** 3
* **Feldolgozókészlet 1**: 10
* **Feldolgozókészlet 2**: 5
* **Feldolgozókészlet 3**: 5

Feldolgozókészlet 1 használatos a termelési számítási feladatokhoz, míg feldolgozókészlet 2 és 3 feldolgozókészlet minőségbiztosítás (Tesztelés) és a fejlesztési számítási feladatokhoz.

Az App Service-csomagok QA és fejlesztési manuális skálázás vannak konfigurálva. Az App Service-csomag éles terhelés és forgalom kezelésére van beállítva az automatikus méretezés.

Frank tisztában van az alkalmazás. Tudja, hogy a terhelés csúcsidőszakon 9:00 és 18:00:00 között van, mivel ez egy – üzletági (LOB) alkalmazás, amely az alkalmazottak használata során az office. Ezt követően használati csökken, amikor a felhasználók az adott napra történik. Csúcsidőn kívül van még néhány terhelést, mert a felhasználók férhetnek hozzá az alkalmazás távolról a mobil eszközök és az otthoni számítógépeket használ. Az üzemi App Service-csomag már konfigurálva van a következő szabályokat a processzorhasználat alapján automatikus skálázást:

![LOB-alkalmazás vonatkozó beállításokat.][asp-scale]

| **Automatikus skálázási profil – Weekdays – App Service-csomag** | **Automatikus skálázási profil – szabadnapok – App Service-csomag** |
| --- | --- |
| **név:** Hét napja profil |**név:** Hétvégi profil |
| **A skálázás:** Ütemezési és teljesítményszabályok |**A skálázás:** Ütemezési és teljesítményszabályok |
| **Profil:** Hétköznap |**Profil:** Hétvége |
| **Írja be:** Ismétlődés |**Írja be:** Ismétlődés |
| **Cél porttartomány:** 5 – 20 példány |**Cél porttartomány:** 3 – 10-példányok |
| **Nap:** Hétfő, kedd, szerda, csütörtök, péntek |**Nap:** Szombat, vasárnap |
| **Kezdő időpont:** 9:00-KOR |**Kezdő időpont:** 9:00-KOR |
| **Időzóna:** UTC-08 |**Időzóna:** UTC-08 |
|  | |
| **Automatikus skálázási szabály (vertikális Felskálázás)** |**Automatikus skálázási szabály (vertikális Felskálázás)** |
| **erőforrás:** Éles (App Service Environment-környezet) |**erőforrás:** Éles (App Service Environment-környezet) |
| **Metrika:** CPU-használat (%) |**Metrika:** CPU-használat (%) |
| **Művelet:** Nagyobb, mint 60 % |**Művelet:** 80 %-nál nagyobb |
| **Időtartam:** 5 perc |**Időtartam:** 10 perc |
| **Idő összesítése:** Átlag |**Idő összesítése:** Átlag |
| **Művelet:** Mennyiség növelése a következővel 2 |**Művelet:** Mennyiség növelése a következővel 1 |
| **Lehűlés (percben):** 15 |**Lehűlés (percben):** 20 |
|  | |
| **Automatikus skálázási szabály (Scale le)** |**Automatikus skálázási szabály (Scale le)** |
| **erőforrás:** Éles (App Service Environment-környezet) |**erőforrás:** Éles (App Service Environment-környezet) |
| **Metrika:** CPU-használat (%) |**Metrika:** CPU-használat (%) |
| **Művelet:** Kevesebb mint 30 % |**Művelet:** Kevesebb mint 20 % |
| **Időtartam:** 10 perc |**Időtartam:** 15 perc |
| **Idő összesítése:** Átlag |**Idő összesítése:** Átlag |
| **Művelet:** Mennyiség csökkentése a következővel 1 |**Művelet:** Mennyiség csökkentése a következővel 1 |
| **Lehűlés (percben):** 20 |**Lehűlés (percben):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Az App Service-csomag infláció arány
App Service-csomagok konfigurált automatikus ehhez óránként sebességet. Ez a díjszabás az automatikus skálázási szabály a megadott értékek alapján számítható.

Megismerés és kiszámítása a *App Service-csomag infláció arány* fontos az App Service-környezet automatikus, mivel a méretezési csoport módosításai a feldolgozókészletek nem azonnali.

Az App Service csomag infláció sebesség számítása a következőképpen:

![Az App Service csomag infláció sebesség kiszámítása.][ASP-Inflation]

Automatikus skálázási – a hét napja profil a termelési App Service-csomag vertikális Felskálázás szabály alapján:

![Az App Service csomag infláció aránya hétköznap, az automatikus méretezés – vertikális Felskálázás szabály alapján.][Equation1]

Az automatikus méretezés a hétvégi profil a termelési App Service-csomag vertikális Felskálázás szabály esetén a képlet szeretné feloldani:

![Az App Service csomag infláció aránya hétvégeken, az automatikus méretezés – vertikális Felskálázás szabály alapján.][Equation2]

Ez az érték is kiszámítható skálázási műveletek.

Az automatikus méretezés méretezési le a termelési App Service-csomag, a hét napja profil szabály alapján ez lenne hasonlítania:

![Az App Service csomag infláció aránya hétköznap, automatikus méretezés méretezési le szabály alapján.][Equation3]

Az automatikus méretezés méretezési le a szabály a hétvégi profil a termelési App Service-csomag esetén a képlet szeretné feloldani:  

![Az App Service csomag infláció aránya hétvégeken, automatikus méretezés méretezési le szabály alapján.][Equation4]

Az App Service-csomag éles növelhető a héten nyolc példányok/óra maximális száma és négy példány/óra a hétvégén. Azt is megjelenhetnek a példányok négy példány/óra hétre esik-e a maximális gyakorisággal és hétvégi hat példány/óra.

Ha több App Service-csomagok folyamatban van az feldolgozókészletek, kell számítani a *teljes infláció arány* folyamatban van az összes App Service-csomagok infláció díjának összege üzemeltetése az adott feldolgozókészlet.

![Több App Service-csomagokban üzemeltetett feldolgozókészletek teljes infláció sebesség számítása.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Használja az App Service csomag infláció arány feldolgozó készlet automatikus skálázási szabályok meghatározásához
Feldolgozó készletek App Service-csomagok konfigurált automatikus skálázást, amelyet ki kell álló kapacitást kell a gazdagépen. A puffer lehetővé teszi, hogy az automatikus skálázási műveletek növelhető vagy csökkenthető az App Service-csomag szükséges. A minimális puffer lenne számított összes App Service csomag infláció átviteli sebessége.

App Service-környezet skálázási műveletek némi időt a alkalmazni, mert további fordulhat elő, amíg folyamatban van egy skálázási művelet igények változásainak bármilyen változás kell figyelni. Befogadásához a késés, javasoljuk, hogy használjon számított összes App Service csomag infláció átviteli sebessége, a hozzáadott példányok minimális száma minden automatikus skálázási művelet.

Ezekkel az információkkal Frank határozhatja meg a következő automatikus skálázási profil és szabályok:

![Automatikus skálázási profil szabályok LOB például.][Worker-Pool-Scale]

| **Automatikus skálázási profil – hétköznap** | **Automatikus skálázási profil – szabadnapok** |
| --- | --- |
| **név:** Hét napja profil |**név:** Hétvégi profil |
| **A skálázás:** Ütemezési és teljesítményszabályok |**A skálázás:** Ütemezési és teljesítményszabályok |
| **Profil:** Hétköznap |**Profil:** Hétvége |
| **Írja be:** Ismétlődés |**Írja be:** Ismétlődés |
| **Cél porttartomány:** 13 – 25-példányok |**Cél porttartomány:** 6. és 15-példányok |
| **Nap:** Hétfő, kedd, szerda, csütörtök, péntek |**Nap:** Szombat, vasárnap |
| **Kezdő időpont:** 7:00-KOR |**Kezdő időpont:** 9:00-KOR |
| **Időzóna:** UTC-08 |**Időzóna:** UTC-08 |
|  | |
| **Automatikus skálázási szabály (vertikális Felskálázás)** |**Automatikus skálázási szabály (vertikális Felskálázás)** |
| **erőforrás:** Feldolgozókészlet 1 |**erőforrás:** Feldolgozókészlet 1 |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Művelet:** Kevesebb mint 8 |**Művelet:** Kevesebb mint 3 |
| **Időtartam:** 20 perc |**Időtartam:** 30 perc |
| **Idő összesítése:** Átlag |**Idő összesítése:** Átlag |
| **Művelet:** Mennyiség növelése a következővel 8 |**Művelet:** Mennyiség növelése a következővel 3 |
| **Lehűlés (percben):** 180 |**Lehűlés (percben):** 180 |
|  | |
| **Automatikus skálázási szabály (Scale le)** |**Automatikus skálázási szabály (Scale le)** |
| **erőforrás:** Feldolgozókészlet 1 |**erőforrás:** Feldolgozókészlet 1 |
| **Metrika:** WorkersAvailable |**Metrika:** WorkersAvailable |
| **Művelet:** 8-nál nagyobb |**Művelet:** Nagyobb, mint 3 |
| **Időtartam:** 20 perc |**Időtartam:** 15 perc |
| **Idő összesítése:** Átlag |**Idő összesítése:** Átlag |
| **Művelet:** Mennyiség csökkentése a következővel 2 |**Művelet:** Mennyiség csökkentése a következővel 3 |
| **Lehűlés (percben):** 120 |**Lehűlés (percben):** 120 |

A minimális példányok, az App Service-csomag + puffer-profilban megadott számolható ki a céltartomány-profilban megadott.

A maximális tartomány lenne az összes App Service-csomagok, a feldolgozó készletben lévő üzemeltetett összes maximális tartományok összege.

A szabályokat a méretezési csoport mennyiség növelése a következővel kell beállítani, legalább 1 X méretezés App Service csomag infláció díjaival.

Mennyiség csökkentése a módosítható egy 1/2 X vagy 1 X méretezés App Service csomag infláció díjaival között le.

### <a name="autoscale-for-front-end-pool"></a>Automatikus előtér-készlet
Az előtér-automatikus skálázási szabályok egyszerűbbek, mint a feldolgozókészletek. Elsősorban érdemes  
Győződjön meg arról, hogy a mérés és az utáni időzítők időtartama vegye figyelembe, hogy az App Service-csomag skálázási műveletek nem azonnali állnak.

Ebben a forgatókönyvben a Frank tudja, hogy a hibák aránya előtérrendszerek eléri a 80 %-os CPU-kihasználtság megnő, és beállítja az automatikus skálázási szabály növelje a példányok a következő:

![Automatikus skálázási beállítások előtér-készlet.][Front-End-Scale]

| **Automatikus skálázási profil – előtérrendszer** |
| --- |
| **név:** Automatikus méretezés előtérrendszer |
| **A skálázás:** Ütemezési és teljesítményszabályok |
| **Profil:** mindennap |
| **Írja be:** Ismétlődés |
| **Cél porttartomány:** 3 – 10-példányok |
| **Nap:** mindennap |
| **Kezdő időpont:** 9:00-KOR |
| **Időzóna:** UTC-08 |
|  |
| **Automatikus skálázási szabály (vertikális Felskálázás)** |
| **erőforrás:** Előtér-készlet |
| **Metrika:** CPU-használat (%) |
| **Művelet:** Nagyobb, mint 60 % |
| **Időtartam:** 20 perc |
| **Idő összesítése:** Átlag |
| **Művelet:** Mennyiség növelése a következővel 3 |
| **Lehűlés (percben):** 120 |
|  |
| **Automatikus skálázási szabály (Scale le)** |
| **erőforrás:** Feldolgozókészlet 1 |
| **Metrika:** CPU-használat (%) |
| **Művelet:** Kevesebb mint 30 % |
| **Időtartam:** 20 perc alatt |
| **Idő összesítése:** Átlag |
| **Művelet:** Mennyiség csökkentése a következővel 3 |
| **Lehűlés (percben):** 120 |

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
