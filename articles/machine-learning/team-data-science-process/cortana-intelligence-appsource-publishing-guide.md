---
title: A Cortana Intelligence AppSource közzétételi útmutató |} Microsoft Docs
description: A Microsoft Partner, lépései a következők minden közzétenni a Cortana Intelligence megoldás AppSource kövesse.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams
ms.openlocfilehash: 9f4e88be7b9b8e3ed7f6a2bbd299fd1e92f9c7af
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836619"
---
# <a name="cortana-intelligence-appsource-publishing-guide"></a>A Cortana Intelligence AppSource közzétételi útmutató

## <a name="overview"></a>Áttekintés
AppSource felderítése és zökkenőmentesen próbálja az üzleti megoldások/alkalmazások által a partnerek és értékelik ki, a Microsoft üzleti döntéshozók (BDMs) egyetlen célját. A Watch [Ez a videó](https://youtu.be/hpq_Y9LuIB8) megtudhatja, hogyan AppSource működik. 

A Microsoft Partner, mint révén valóban kihasználhatja a közzététel AppSource, ha:
- Egy intelligens megoldás/alkalmazás használatával készített [Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/?cdn=disable).
- A megoldás vagy alkalmazás-címek egy adott üzleti probléma.
- Ön a beépített modulok vagy szellemi tulajdonra vonatkozó, az ügyfelek felhasználhat viszonylag gyorsan kiszámítható módon.

Vessen egy pillantást [Cortana Intelligence megoldások](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1) AppSource, amely már közzé. 

Ez a cikk részletesen ismerteti a lépéseket a AppSource közzétett egy partner által létrehozott Cortana Intelligence megoldás keresztül

## <a name="getting-started"></a>Első lépések
1. Az a [Partner Közösség előnyei útmutató](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF), lásd az beszerzése felsorolt Advanced Analytics partnerként 9.
1. Fejezze be a [küldje el az alkalmazás](https://appsource.microsoft.com/en-us/partners/list-an-app) űrlap.

    A kérdés *"Válassza ki a termékeket, az alkalmazás a beépített*", ellenőrizze a **más** jelölőnégyzetet és a lista a Szerkesztés "Cortana Intelligence" szabályozzák.
1. A Cortana Intelligence alkalmazások beszerezheti a AppSource előkészítve, mielőtt a Cortana Intelligence partneri megoldás informatikai csoport által beolvasása minősített. Hogy folyamat elindult, az alkalmazás, felmérés űrlap kitöltésével megkérjük megosztás adatainak eléréséhez [Cortana Intelligence megoldás értékelési AppSource közzétételi](https://aka.ms/cisappsrceval). Ezen a helyen jelszóval védett, és a hely rendelkezik utasításokat a jelszó beszerzésére.

## <a name="solution-evaluation-criteria"></a>Megoldás értékelési feltételek
Ez az alkalmazás meg kell felelnie feltételek listája
1. Cím adott üzleti alkalmazást kell használni eset probléma egy adott funkcionális területen minimális konfigurációkat a repeatable módon előre definiált értéknövelő implementable rövid időn belül.
1. Megoldás használatakor a következő összetevők legalább egyikét:

    - HDInsight
    - Machine Learning
    - Data Lake Analytics
    - Stream Analytics
    - Cognitive Services
    - Robotkeretrendszer
    - Analysis Services
    - Microsoft R Server önállóan.
    - R-szolgáltatásokat az SQL 2016 vagy HDInsight prémium
1. Megoldás a legalább $1000 havi / DPOR/kriptográfiai Szolgáltató használatával kell létrehozásakor.
1. Megoldás használata (AAD összevont egyszeri bejelentkezés) Azure Active Directory összevont egyszeri bejelentkezés hozzájárulási engedélyezve van a felhasználói hitelesítés és az erőforrás hozzáférés-vezérlést. Szüksége lesz az értékelés csapatának megjelenítése, hogy a megoldás-e az aad-ben összevont egyszeri Bejelentkezéses engedélyezve van, mielőtt az alkalmazás is lehet a AppSource előkészítve.

     Mit jelent, ha az AAD összevont egyszeri Bejelentkezéses engedélyezve kell megtekintéséhez pozícionálni 02:35 a pozíció [AppSource próbaverziója bízná](https://aka.ms/trialexperienceforwebapps) videó. Ha az alkalmazás nem engedélyezték a AAD összevont egyszeri Bejelentkezést, de itt van néhány dokumentációjában
   1. [Egy kattintással bejelentkezés](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/).
   1. [Alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application).
     
1. Használja a Power bi-ban a megoldásban: kötelező, de erősen ajánlott, mivel érdeklődők nagyobb számú létrehozásához bizonyítása.

## <a name="devcenter-account-setup"></a>DevCenter fiók beállítása
Ez az a vállalat számára a Microsoft publisher válik regisztrációján. Ha már létező DevCenter fiókkal érvényes kiadó, ossza meg az-DevCenter fiókjához tartozó e-mail azonosítója. Az alkalmazás közzététele számára jóvá van hagyva, miután elérhetővé válik teljes dokumentáció eléréséhez [publisher profil felhő Portal kezelése](https://cloudpartner.azure.com/#documentation/manage-publisher-profile)

Ha még nincs fiókja, az alábbiakban a fontos lépések DevCenter fiók beállítása.
1. Microsoft-fiók létrehozása [Itt](https://signup.live.com/signup.aspx).
1. Keresse fel a Microsoft DevCenter [regisztrációs lapjához](http://go.microsoft.com/fwlink/?LinkId=615100) , és kattintson az "előfizetés".
1. A fizetési megadását kéri, használja a kódot, amely azt az Ön számára biztosított. Ha még nem rendelkezik egy kódot, forduljon a [ appsourcecissupport@microsoft.com ](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup).
1. Válassza ki a [ország vagy régió](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) , ahol él, illetve ahol az üzleti. **Ön nem fog tudni később módosítható.**
1. Válassza ki a [developer-fiók típusa](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) a AppSource, válassza ki **vállalati**. A vállalati fiók, ne feledje el áttekinteni a [irányelvek](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account).
1. Adja meg a kapcsolattartási adatokat a fejlesztői fiókhoz használni kívánt.
További részletes részletes információk az DevCenter fiókot a telepítő hogyan, tekintse meg a útmutatást [Itt](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration).

## <a name="provide-info-for-microsoft-sellers"></a>A Microsoft eladók adatainak megadása
Azokra a partnerekre vonatkozó AppSource kulcs értéknövelő egyik dolgozhatnak együtt a Microsoft Sellers a lehetséges ügyfeleket elé partneralkalmazások elhelyezésében.

Felfelé kitölti [partneri megoldás információ a következő Microsoft Sellers](https://aka.ms/aapartnerappinfo) küldje el a [ appsourcecissupport@microsoft.com ](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs). Ez az beszerzése jóvá kell hagyni a közzétételt a Cortana Intelligence alkalmazások szükséges lépés.

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>Egy kötelező felhasználói forgatókönyv AppSource létrehozása
Vessen egy pillantást [Neal Analytics készlet optimalizálási](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) AppSource a. Minden alkalmazás bejegyzést AppSource számára cím, összefoglalás (legfeljebb 100 karakter), leírás (maximális 1300 karakter), képek, videók (nem kötelező), PDF-dokumentumokat leszámítva belépési pontot egy próbaverziója. Partnerek ki kell használniuk az összes ezek egy kötelező felhasználói élmény létrehozásához.

Partnerek kell gondolja, hogy azok magánjellegét, mint egy teljes körű értékesítési vezénylési AppSource a tartalmat. Az ügyfelek címe és értékajánlatához megértéséhez, valamint majd halad át a képeket és videók megérteni, hogy mi az a megoldás leírása olvasható. Esettanulmányok érdekében tekintse meg az ügyfelek hogyan meglévő potenciális vevők értéket kap. 

A kell teszi az ügyfél az összes érzi, hogy az érdekelt és további szeretne. Gondolja, hogy ezek betűközű alapú fedélzetek bemutató jó műszaki értékesítő lenne az új ügyfelek végezze el. A javasolt Leírás formátuma alárendelt szakaszokra tagolhatja értéket a szöveges osztására propositions, az és egy alárendelt címsor kiemelve. 

Látogatók általában a "ajánlat-összefoglaló" mezője és alárendelt fejlécére kattintva rendezhető, milyen az alkalmazás címének gist beolvasandó áttekintése, és miért akkor érdemes csak egy gyors áttekintő az alkalmazásra. Igen, fontos, hogy a felhasználó első figyelmet olvassa be a a mintaadatokról miért számukra.

Tekintse meg a partnerek tette.
- [Neal Analytics készlet optimalizálása](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure kereskedelmi személyre szabása](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint állampolgár szolgáltatások](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

Értékesítési közül, hogy az alkalmazás/megoldás jelenít meg, hogyan értékajánlatához kerül egy bemutató megjelenítése. Ez pontosan mit jelent a AppSource egy ügyfél próbaverziója. Egy jó bemutató fog tegye a következőket:
- A az alkalmazás a rövid és a kimenő belül az ügyfél vállalkozás ki kellene interakciót folytatni a megoldás személyeknek lista értékajánlatához újra összefoglalója
- Szövegegység mondja el, és beállítja a környezet konkrét problémák foglalkozó minta ügyfelekkel kapcsolatos
- Azt ismertetik, hogyan a helyzet általában kezdeményezzenek és hatással lehet az ügyfél (előtt) VS, mi történne a megoldás használatban van. Ez is megjeleníthetők, használja a Power bi-irányítópultok stb.
- Összesítésének módját a megoldás lehetővé teszi bármely adott stb Machine Learning algoritmusokkal fordulhat elő.

A hozzáadni kívánt AppSource a tartalom kiváló minőségű legyen, és stitched másolatot ahhoz, hogy a következő:
- Egy partner által létrehozott műszaki értékesítési segítsen kell használnia, az értékesítési vezénylési. Az értékesítési csapat használja azt a helyes bejelentkezési MS értékesítési segítsen lehetőség van ugyanerre a várható. Ezzel a lépéssel engedélyezi a következetesen továbbítják a csapat kocsikísérők és magasabb ups és a jóváhagyások beolvasandó előtt a beszerzési üzlet végezhető ugyanazon szövegegység tennie kapcsolódási pontot ügyfél.
- A rendszerfrissítésről biotermeléssel ügyfél is halad át a tartalom összes maguk és érzetéhez érdeklődőbbek megválaszolásához partner kommunikációt azokat, amelyek a következő lépések együtt mozognak.

Ezért partnerek gondolja kell, hogy azok magánjellegét, mint egy teljes körű értékesítési vezénylési AppSource a tartalmat. Szövegegység sor és funkciók jelennek meg próbaverziója alapján ajánlat típusú léphet.

## <a name="publish-your-app-on-the-publishing-portal"></a>A közzétételi portálon az alkalmazás közzététele
Ha az alkalmazás azt kiértékelni a fenti lépéseket a közzétételi portál eléréséhez, és látható [keresztül Cloud Partner portálra a Cortana Intelligence ajánlat közzététele](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app) lépések részletes ismertetése.

Információ mezőinek van szüksége, ha e-mail- <appsourcecissupport@microsoft.com>.
## <a name="my-app-is-published-on-appsource---now-what"></a>Saját alkalmazás közzétett AppSource - most Mi a teendő?
Először is az alkalmazás első Gratulálunk közzé.
A szint nem tesz közzé az alkalmazást a AppSource fokozottan kapott értéket ad vissza attól függ, hogyan befolyásolja-e a célközönség. Lásd: [a Cortana Intelligence alkalmazás AppSource növekedési támadásoknak](http://aka.ms/aagrowthhackguide) olvashat, hogyan maximalizálhatja a értéket ad vissza.

Ha bármilyen kérdése vagy javaslatok, hogy kapcsolatba velünk, <appsourcecissupport@microsoft.com>.

