---
title: Kulcstartó védelme | Microsoft Docs
description: Kulcstartó-hozzáférési engedélyek kezelése tárolók, kulcsok és titkos kulcsok kezeléséhez. Kulcstartó hitelesítési és engedélyezési modellje, illetve a kulcstartó biztosításának módja
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: ambapat
ms.openlocfilehash: a3493c9e9ef6a5bafd832510f42f33cc3f07f088
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="secure-your-key-vault"></a>Kulcstartó védelme
Az Azure Key Vault egy felhőszolgáltatás, mely a titkosítási kulcsokat és titkos kulcsokat (pl. tanúsítványok, kapcsolati karakterláncok, jelszavak) védi az Ön felhőalkalmazásainál. Mivel ezek érzékeny és üzleti szempontból kritikus jellegű adatok, fontos, hogy Ön csak jogosult alkalmazások és felhasználók számára engedélyezzen kulcstartó-hozzáférést. Ez a cikk áttekintést ad a kulcstartó-hozzáférési modellről, elmagyarázza, mi a hitelesítés és az engedélyezés, és egy példával szemlélteti, hogyan biztosíthat kulcstartó-hozzáférést felhőalkalmazásai számára.

## <a name="overview"></a>Áttekintés
A kulcstartó-hozzáférés vezérlése két külön felületen, a felügyeleti síkon és az adatsíkon keresztül történik. Mindkét sík esetében megfelelő hitelesítés és engedélyezés szükséges ahhoz, hogy a hívó (felhasználó vagy alkalmazás) kulcstartó-hozzáférést kaphasson. A hitelesítés a hívó azonosítását szolgálja, az engedélyezés pedig meghatározza, milyen műveletek elvégzésére jogosult a hívó.

Hitelesítésre mind a felügyeleti sík, mind az adatsík az Azure Active Directoryt használja. Engedélyezésre a felügyeleti sík szerepköralapú hozzáférés-vezérlést (RBAC) használ, míg az adatsík kulcstartó-hozzáférési házirendet.

A tárgyalt témakörök rövid áttekintése:

[Hitelesítés az Azure Active Directory használatával](#authentication-using-azure-active-directory) – Ebből a fejezetből megtudhatja, hogyan végezhet hitelesítést a hívó az Azure Active Directoryval a felügyeleti síkon és az adatsíkon keresztüli kulcstartó-hozzáférés céljából. 

[Felügyeleti sík és adatsík](#management-plane-and-data-plane) – A felügyeleti sík és az adatsík a kulcstartó-hozzáféréséhez használt két hozzáférési síkot jelenti. Minden hozzáférési sík meghatározott műveleteket támogat. Ez a fejezet a hozzáférés végpontjait, a támogatott műveleteket és az egyes síkokon használt hozzáférés-vezérlés módját írja le. 

[Felügyeleti sík hozzáférés-vezérlése](#management-plane-access-control) – Ebben a fejezetben megtekintjük, hogyan engedélyezhetünk hozzáférést a felügyeleti sík műveletei számára szerepköralapú hozzáférés-vezérlés útján.

[Adatsík hozzáférés-vezérlése](#data-plane-access-control) – Ez a fejezet leírja, hogyan használható a kulcstartó-hozzáférési házirend az adatsík-hozzáférés vezérléséhez.

[Példa](#example) – Ez a példa megmutatja, hogyan állíthat be kulcstartójához hozzáférés-vezérlést, három különböző csapat (biztonsági csapat, fejlesztők/operátorok és ellenőrök) számára engedélyezve meghatározott alkalmazásfejlesztési, -kezelési és -figyelési feladatok végrehajtását az Azure rendszerben.

## <a name="authentication-using-azure-active-directory"></a>Hitelesítés az Azure Active Directory használatával
Amikor Azure-előfizetésében kulcstartót hoz létre, az automatikusan az előfizetés Azure Active Directory-bérlőjéhez társul. Valamennyi hívót (felhasználót és alkalmazást) regisztrálni kell ebben a bérlőben, hogy az adott kulcstartóhoz hozzáférhessen. Az alkalmazásnak vagy felhasználónak hitelesítést kell végeznie az Azure Active Directory felé, hogy a kulcstartóhoz hozzáférhessen. Ez mind a felügyeleti síkon, mind pedig az adatsíkon érvényes. Mindkét esetben kétféle módon férhet hozzá az alkalmazás a kulcstartóhoz:

* **felhasználó + alkalmazás-hozzáférés** – ez általában olyan alkalmazások esetén történik, amelyek bejelentkezett felhasználó nevében férnek hozzá a kulcstartóhoz. Ilyen típusú hozzáférés például az Azure PowerShell vagy az Azure Portal. A felhasználóknak kétféle módon adható hozzáférés: az egyik szerint bármilyen alkalmazásból hozzáférhetnek a kulcstartóhoz, a másik mód esetén csak akkor, amikor egy meghatározott alkalmazást használnak (ezt összetett identitásnak nevezik). 
* **csak alkalmazás hozzáférése** – démonszolgáltatásokat, háttérfeladatokat stb. futtató alkalmazások számára. A kulcstartó-hozzáférés az alkalmazásidentitás számára engedélyezett.

Az Azure Active Directory-hitelesítés és -tokenbeolvasás az alkalmazás mindkét típusa esetén elvégezhető a [támogatott hitelesítési módszerek](../active-directory/active-directory-authentication-scenarios.md) bármelyikével. A hitelesítés módszere az alkalmazás típusától függ. Az alkalmazás a későbbiekben ezt a tokent használja, amikor REST API-kérelmet küld a kulcstartónak. Felügyeletisík-hozzáférés esetén a kérelmek az Azure Resource Manager végpontjához kerülnek. Adatsík-hozzáférés esetén az alkalmazások közvetlenül a kulcstartó végpontjával kommunikálnak. További részletek a [teljes hitelesítési folyamatról](../active-directory/active-directory-protocols-oauth-code.md). 

Annak az erőforrásnak a neve, melyhez az alkalmazás tokent kér, attól függően változik, hogy az alkalmazás felügyeleti vagy adatsíkhoz készül hozzáférni. Így az erőforrásnév vagy felügyeleti sík, vagy adatsík végpontja, amint az egy később szakasz táblázatában szerepel, az Azure-környezettől függően.

Ha mind a felügyeleti síkra, mind az adatsíkra egyetlen hitelesítési mechanizmust alkalmaz, annak megvannak a maga előnyei:

* A szervezetek központilag vezérelhetik valamennyi kulcstartójukat
* Ha egy felhasználó távozik, azonnal elveszíti a szervezeten belüli összes kulcstartó-hozzáférését
* A szervezetek az Azure Active Directory beállításain keresztül testre szabhatják a hitelesítést (például Multi-Factor Authentication engedélyezésével fokozhatják a biztonságot)

## <a name="management-plane-and-data-plane"></a>Felügyeleti sík és adatsík
Az Azure Key Vault az Azure Resource Manager-alapú üzemi modellen keresztül elérhető Azure-szolgáltatás. Kulcstartó létrehozásakor Ön virtuális tárolóhoz jut, és benne egyéb objektumokat – például kulcsokat, titkos kulcsokat és tanúsítványokat – hozhat létre. Ezután felügyeleti és adatsíkokon keresztül férhet hozzá kulcstartójához, és végezhet meghatározott műveleteket. A felügyeleti sík felületén magát a kulcstartót kezelheti, például kulcstartó-attribútumokat hozhat létre, törölhet és frissíthet, illetve hozzáférési házirendeket állíthat be az adatsíkhoz. Az adatsík felületén a kulcstartójában tárolt kulcsok, titkos kulcsok és tanúsítványok felvételére, törlésére, módosítására és használatára van lehetősége.

A felügyeleti sík és az adatsík felületei eltérő végpontokon keresztül érhetők el (lásd a táblázatot). A táblázat második oszlopa ismerteti ezen végpontok DNS-neveit különböző Azure-környezetekben. A harmadik oszlop az egyes hozzáférési síkokról elvégezhető műveleteket ismerteti. Minden hozzáférési síknak megvan a saját hozzáférés-vezérlési mechanizmusa is: a felügyeleti sík hozzáférés-vezérlése az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) használatával van beállítva, míg az adatsík hozzáférés-vezérlésének beállítása a kulcstartó hozzáférési házirendjének használatával történik.

| Hozzáférési sík | Hozzáférés végpontjai | Műveletek | Hozzáférés-vezérlési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík |**Globálisan:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |Kulcstartó létrehozása/olvasása/frissítése/törlése <br> Kulcstartó hozzáférési házirendjeinek beállítása<br>Címkék beállítása kulcstartóhoz |Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsík |**Globálisan:**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 |Kulcsok: Visszafejtés, Titkosítás, UnwrapKey, WrapKey, Ellenőrzés, Aláírás, Beolvasás, Listázás, Frissítés, Létrehozás, Importálás, Törlés, Biztonsági mentés, Visszaállítás<br><br> Titkos kulcsok: Beolvasás, Listázás, Beállítás, Törlés |Kulcstartó hozzáférési házirendje |

A felügyeleti sík és az adatsík hozzáférés-vezérlése egymástól függetlenül működik. Ha például hozzáférést engedélyez egy alkalmazásnak egy kulcstartóban lévő kulcs használatához, csak adatsík-hozzáférési engedélyt kell adnia a kulcstartó-hozzáférési házirenden keresztül, és nincs szükség felügyeleti síkú hozzáférésre ennél az alkalmazásnál. Ha viszont azt szeretné, hogy a felhasználó olvasni tudja a tároló tulajdonságait és címkéit, de ne férjen hozzá a kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz, engedélyezhet ennek a felhasználónak „olvasási” hozzáférést RBAC használatával, de nem szükséges hozzáférést biztosítani az adatsíkhoz.

## <a name="management-plane-access-control"></a>Felügyeleti sík hozzáférés-vezérlése
A felügyelet sík olyan műveleteket tartalmaz, amelyek magára a kulcstartóra vannak hatással. Például létrehozhat vagy törölhet egy kulcstartót. Előfizetésben megkaphatja a tárolók listáját. Lehívhatja a kulcstartó tulajdonságait (például cikkszám, címkék), és beállíthatja a kulcstartóban lévő kulcsokhoz és titkos kulcsokhoz hozzáférő felhasználókat és alkalmazásokat vezérlő kulcstartó-hozzáférési házirendeket. A felügyeleti sík hozzáférés-vezérlése RBAC használatával történik. A felügyeleti síkon keresztül elvégezhető kulcstartó-műveletek teljes listáját az előző fejezet táblázatában találja. 

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
Minden Azure-előfizetés Azure Active Directoryval rendelkezik. Ezen könyvtár valamennyi felhasználója, csoportja és alkalmazása hozzáférést kaphat Azure-előfizetése Azure Resource Manager-alapú üzemi modellt használó erőforrásainak kezeléséhez. Az ilyen típusú hozzáférés-vezérlést szerepköralapú hozzáférés-vezérlésnek (RBAC-nek) nevezzük. A hozzáférés az [Azure Portal](https://portal.azure.com/), az [Azure Parancssorifelület-eszközök](../cli-install-nodejs.md), a [PowerShell](/powershell/azureps-cmdlets-docs) vagy az [Azure Resource Manager REST API-k](https://msdn.microsoft.com/library/azure/dn906885.aspx) használatával kezelhető.

Az Azure Resource Manager modellel kulcstartót hozhat létre erőforráscsoporton belül, és az Azure Active Directory használatával vezérelheti e kulcstartó felügyeleti síkját. Lehetőséget adhat például felhasználóknak vagy egy csoportnak, hogy egy adott erőforráscsoport kulcstartóit kezeljék.

Megfelelő RBAC-szerepek kiosztásával meghatározott hatáskörrel rendelkező felhasználók, csoportok és alkalmazások számára biztosíthat hozzáférést. Például, ha kulcstartókezelési hozzáférést kíván biztosítani egy felhasználónak, előre meghatározott „kulcstartó-közreműködői” szerepkört rendelhet ehhez a felhasználóhoz egy adott hatókörnél. A hatókör ebben az esetben lehet egy előfizetés, egy erőforráscsoport vagy egyszerűen egy adott kulcstartó. Az előfizetés-szinten hozzárendelt szerepkör az adott előfizetésen belül működő összes erőforráscsoportra és erőforrásra vonatkozik. Az erőforráscsoport-szinten hozzárendelt szerepkör az adott erőforráscsoport valamennyi erőforrására vonatkozik. A meghatározott erőforráshoz rendelt szerepkör csak az adott erőforrásra vonatkozik. Számos előre definiált szerepkör létezik (lásd: [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md)), ha pedig az előre definiált szerepkörök nem felelnek meg igényeinek, saját szerepköröket is meghatározhat.

> [!IMPORTANT]
> Vegye figyelembe: ha a kulcstartó felügyeleti síkjához a felhasználónak közreműködői engedélye (RBAC) van, hozzáférést engedélyezhet magának az adatsíkhoz is az adatsík-hozzáférést vezérlő kulcstartó-hozzáférési házirend beállításával. Ezért javasoljuk, hogy tartsa szigorú ellenőrzés alatt, ki rendelkezhet „közreműködői” hozzáféréssel kulcstartóihoz, így csak az arra jogosult személyek érhetik el, illetve kezelhetik kulcstartóit, kulcsait, titkos kulcsait és tanúsítványait.
> 
> 

## <a name="data-plane-access-control"></a>Adatsík-hozzáférés vezérlése
A kulcstartó adatsíkja olyan műveletekből épül fel, melyek a kulcstartó objektumaira (például kulcsok, titkos kulcsok és tanúsítványok) vannak hatással.  Olyan, kulcsokkal kapcsolatos műveleteket foglal magába, mint a kulcsok létrehozása, importálása, frissítése, listázása, biztonsági mentése és visszaállítása, valamint kriptográfiai műveleteket, mint például a kulcsok aláírása, ellenőrzése, titkosítása, visszafejtése, be- és kicsomagolása, a kulcsok címkéinek és egyéb attribútumainak beállítása. Hasonlóképpen, titkos kulcsok esetén magába foglalja a beolvasást, beállítást, listázást és törlést.

Az adatsík-hozzáférés a kulcstartó hozzáférési házirendjeinek beállításán keresztül biztosítható. Ahhoz, hogy egy felhasználó, csoport vagy alkalmazás hozzáférési házirendet tudjon beállítani egy adott kulcstartóhoz, rendelkeznie kell a kulcstartó felügyeleti síkjának közreműködői engedélyeivel (RBAC). A felhasználók, csoportok és alkalmazások számára hozzáférés engedélyezhető egy adott kulcstartó kulcsaihoz vagy titkos kulcsaihoz kapcsolódó, meghatározott műveletek elvégzéséhez. Akár 16 hozzáférésiházirend-bejegyzés támogatása kulcstartók esetében. Azure Active Directory biztonsági csoport létrehozásával és felhasználók felvételével a csoportba több felhasználó számára biztosíthat adatsíkszintű kulcstartó-hozzáférést.

### <a name="key-vault-access-policies"></a>Kulcstartó-hozzáférési házirendek
A kulcstartó-hozzáférési házirendekkel külön-külön engedélyezheti a kulcsok, titkos kulcsok és tanúsítványok elérését. Egy adott felhasználó számára hozzáférést engedélyezhet például kizárólag a kulcsokhoz, de a titkos kulcsokhoz nem. A kulcsok, titkos kulcsok és tanúsítványok hozzáférése ugyanakkor tárolószinten engedélyezett. Másként megfogalmazva, a kulcstartó-hozzáférési házirend nem támogatja az objektumszintű engedélyeket. Kulcstartó-hozzáférési házirendeket az [Azure portal](https://portal.azure.com/), az [Azure parancssorifelület-eszközök](../cli-install-nodejs.md), a [PowerShell](/powershell/azureps-cmdlets-docs) vagy a [Kulcstartókezelési REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx) használatával állíthat be.

> [!IMPORTANT]
> Vegye figyelembe, hogy a kulcstartó-hozzáférési házirendek tárolószinten érvényesek. Például, ha egy felhasználó engedélyt kap kulcsok létrehozására és törlésére, az adott kulcstartó összes kulcsa esetén végrehajthatja ezeket a műveleteket.
> 
> 

## <a name="example"></a>Példa
Tegyük fel, hogy Ön egy olyan alkalmazást fejleszt, amely az SSL-hez tanúsítványt használ, adattárolásra Azure-tárolást, az aláírási műveletekhez pedig RSA 2048 bites kulcsot. Tegyük fel azt is, hogy ez az alkalmazás virtuális gépen fut (vagy virtuálisgép-méretezési csoportban). Kulcstartót használhat az összes titkos alkalmazáskulcs tárolására, valamint azon rendszerindítási tanúsítvány tárolására, amelyet az alkalmazás az Azure Active Directoryval történő hitelesítéshez használ.

Íme egy összegzés arról, mely kulcsok és titkos kulcsok tárolhatók a kulcstartóban.

* **SSL-tanúsítvány** – az SSL-hez használatos
* **Tárkulcs** – a tárfiók eléréséhez használatos
* **RSA 2048 bites kulcs** – az aláírási műveletekhez használatos
* **Rendszerindítási tanúsítvány** – az Azure Active Directory-hitelesítéshez, a tárkulcslehíváshoz szükséges kulcstartó-hozzáférés megszerzéséhez, valamint az aláírási RSA-kulcs használatához szükséges.

Most nézzük meg, kik kezelik, helyezik üzembe és naplózzák ezt az alkalmazást. Ebben a példában három szerepkört használunk.

* **Biztonsági csapat** – ezek rendszerint a CSO (biztonsági vezető) irodájának informatikai munkatársai vagy nekik megfelelő személyek, akik a titkos kulcsok – például SSL-tanúsítványok, aláíráshoz használt RSA-kulcsok, adatbázisok kapcsolati karakterláncai és tárfiókok kulcsai – megfelelő biztonságban tartásáért felelősek.
* **Fejlesztők/operátorok** – Ezek azok munkatársak, akik az alkalmazást fejlesztik és üzembe helyezik az Azure-ban. Általában nem tagjai a biztonsági csapatnak, így nem javasolt, hogy bármilyen bizalmas adathoz, például SSL-tanúsítványokhoz vagy RSA-kulcsokhoz hozzáférhessenek, de az általuk üzembe helyezett alkalmazásnak hozzá kell férnie azokhoz.
* **Ellenőrök** – Általában külön csoport, akik a fejlesztőktől és az általános informatikai személyzettől elkülönítve dolgoznak. Felelősségük a tanúsítványok, kulcsok stb. helyes használatának és karbantartásának felügyeletére, illetve az adatbiztonsági szabványoknak való megfelelés biztosítására terjed ki. 

Van még egy, ezen alkalmazás hatáskörén kívül eső szerepkör, amelyet itt fontos megemlíteni, ez pedig az előfizetés- (vagy erőforráscsoport-) rendszergazdai szerepkör. Az előfizetés-rendszergazda beállítja a kezdeti hozzáférési engedélyeket a biztonsági csapat számára. Itt abból indulunk ki, hogy az előfizetés-rendszergazda a biztonsági csapatnak hozzáférést engedélyezett ahhoz az erőforráscsoporthoz, amelyben a jelen alkalmazáshoz szükséges összes erőforrás megtalálható.

Most nézzük meg, milyen műveletet hajtanak végre az egyes szerepkörök a jelen alkalmazás összefüggésében.

* **Biztonsági csapat**
  * Kulcstartók létrehozása
  * Kulcstartónaplózás bekapcsolása
  * Kulcsok/titkos kulcsok hozzáadása
  * Kulcsok biztonsági másolatának létrehozása vészhelyreállításhoz
  * Kulcstartó-hozzáférési házirend beállítása adott műveletek felhasználók és alkalmazások általi végrehajtásának engedélyezéséhez
  * Kulcsok/titkos kulcsok időnkénti összegzése
* **Fejlesztők/operátorok**
  * Rendszerindítási referenciák és SSL-tanúsítványok (ujjlenyomatok), tárkulcs (titkos URI) és aláírási kulcs (kulcs URI) lekérése a biztonsági csapattól
  * A kulcsokhoz és titkos kulcsokhoz programozott módon hozzáférő alkalmazás fejlesztése és üzembe helyezése
* **Ellenőrök**
  * Ellenőrzik a használati naplókat, és megerősítik, a helyes kulcsokat/titkos kulcsokat használták-e az adatbiztonsági szabványoknak megfelelően

Most nézzük meg, milyen kulcstartó-hozzáférési engedélyek szükségesek az egyes szerepkörökhöz (és az alkalmazáshoz) hozzárendelt feladataik elvégzéséhez. 

| Felhasználói szerepkör | Felügyeleti sík engedélyei | Adatsík engedélyei |
| --- | --- | --- |
| Biztonsági csapat |Kulcstartó-közreműködő |Kulcsok: biztonsági mentése, létrehozása, törlése, beolvasása, importálása, listázása, visszaállítása <br> Titkos kulcsok: összes |
| Fejlesztők/operátor |kulcstartó-üzembehelyezési engedély, hogy az általuk üzembe helyezett virtuális gépek lehívhassák a kulcstartóból a titkos kulcsokat |None |
| Ellenőrök |None |Kulcsok: listája<br>Titkos kulcsok: listája |
| Alkalmazás |None |Kulcsok: aláírása<br>Titkos kulcsok: beolvasása |

> [!NOTE]
> Az ellenőröknek listázniuk kell a kulcsok és titkos kulcsok engedélyeit, hogy kivizsgálhassák azon kulcsok és titkos kulcsok attribútumait, amelyek nem kerülnek naplózásra, például címkék, aktiválási és lejárati dátumok.
> 
> 

A kulcstartó-engedélyezéseken túl mindhárom szerepkörnek hozzá kell férnie egyéb erőforrásokhoz is. Például azért, hogy üzembe tudják helyezni a virtuális gépeket (vagy webes alkalmazásokat stb.) A fejlesztőknek/operátoroknak is ilyen „közreműködői” hozzáférésre van szükségük az adott erőforrástípusokhoz. Az ellenőröknek ahhoz a tárfiókhoz kell olvasási hozzáféréssel rendelkezniük, ahol a kulcstartónaplók tárolódnak.

Mivel ebben a cikkben a legfőbb hangsúly az Ön kulcstartófiókjához való biztonságos hozzáférésre esik, csak a témához tartozó fontos részeket szemléltetjük, és nem térünk ki a tanúsítványok üzembe helyezésével, illetve a kulcsokhoz és titkos kulcsokhoz való programozott hozzáféréssel kapcsolatos részletekre. Ezeket a részleteket máshol már tárgyaltuk. A kulcstartóban tárolt tanúsítványok virtuális gépekhez történő üzembe helyezésével egy [blogbejegyzés](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) foglalkozik, és rendelkezésre áll egy [mintakód](https://www.microsoft.com/download/details.aspx?id=45343) annak szemléltetésére, hogyan kell a rendszerindítási tanúsítványt Azure Active Directoryval történő hitelesítésre használni kulcstartó-hozzáférés céljából.

A hozzáférési engedélyek nagy része az Azure Portalon is megadható, de előfordulhat, hogy a részletes engedélyezés során Azure PowerShell (vagy Azure parancssori felület) használata szükséges a kívánt eredmények eléréséhez. 

A következő PowerShell-kódtöredékek esetén a következőket vesszük alapul:

* Az Azure Active Directory-rendszergazda biztonsági csoportokat hozott létre, amelyek a három szerepkört képviselik, nevezetesen: Contoso biztonsági csapat, Contoso alkalmazás fejlesztői és operátorai, illetve a Contoso alkalmazás ellenőrei. A rendszergazda felhasználókat is hozzáadott azokhoz a csoportokhoz, amelyekbe tartoznak.
* **ContosoAppRG**: az az erőforráscsoport, amelyben minden erőforrás megtalálható. **contosologstorage**: a naplók tárolási helye. 
* A **ContosoKeyVault** kulcstartónak és a kulcstartónaplókhoz használt **contosologstorage** tárfióknak az Azure rendszer egyazon helyén kell lennie

Az előfizetés-rendszergazda először „kulcstartó-közreműködő” és „felhasználóihozzáférés-rendszergazda” szerepkört rendel hozzá a biztonsági csapathoz. Így a biztonsági csapat más erőforrások hozzáférését, valamint a ContosoAppRG erőforráscsoportjában található kulcstartókat is kezelni tudja.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Az alábbi parancsprogram azt szemlélteti, hogyan hozhat létre a biztonsági csapat kulcstartót, hogyan állíthatja be a naplózást, és állíthat be hozzáférési engedélyeket más szerepkörök és az alkalmazás számára. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Az egyéni szerepkör, meghatározása után, csak ahhoz az előfizetéshez rendelhető hozzá, amelyben a ContosoAppRG erőforráscsoportot létrehozták. Ha ugyanazon egyéni szerepköröket más előfizetések más projektekhez használnak, előfordulhat, hogy annak hatóköréhez több előfizetést is hozzáadtak.

A fejlesztők/operátorok egyéniszerep-hozzárendelése „üzembe helyezés/művelet” engedélyezéséhez az erőforráscsoport hatókörén belül van. Így csak a „ContosoAppRG” erőforráscsoportban létrehozott virtuális gépek kapják meg a titkos kulcsokat (SSL-tanúsítványt és a rendszerindítási tanúsítványt). A fejlesztők/operátorok csapatának tagjai által másik erőforráscsoportban létrehozott egyetlen virtuális gép sem olvashatja be ezeket a titkos kulcsokat, még abban az esetben sem, ha ismerték a titkos URI-ket.

Ez a példa egy egyszerű forgatókönyvet mutat be. Előfordulhat, hogy a valós életbeli forgatókönyvek bonyolultabbak, és szükség lehet a kulcstartóengedélyek igényei szerinti módosítására. Példánkban abból indulunk ki, hogy a biztonsági csapat adja azokat a kulcs- és titkoskulcs-referenciákat (URI-k és ujjlenyomatok), amelyekre a fejlesztők/operátorok csapatának hivatkoznia kell alkalmazásaiban. Ezért nem kell semmilyen adatsík-hozzáférést biztosítania a fejlesztők/operátorok számára. Fontos megjegyezni, hogy ebben a példában legfőképpen a kulcstartó biztonságossá tételére összpontosítunk. Hasonló figyelmet kell fordítani [virtuális gépei](https://azure.microsoft.com/services/virtual-machines/security/), [tárfiókjai](../storage/common/storage-security-guide.md) és egyéb Azure-erőforrásai biztosítására is.

> [!NOTE]
> Megjegyzés: Ez a példa bemutatja, hogyan lesz zárolva a kulcstartó-hozzáférés üzemi környezetben. A fejlesztőknek saját előfizetéssel vagy erőforráscsoporttal kell rendelkezniük, ahol teljes körű engedélyekkel rendelkeznek kulcstartóik, virtuális gépeik és a tárfiókjuk kezelésére az alkalmazás fejlesztésének helyén.
> 
> 

## <a name="resources"></a>További források
* [Azure Active Directory szerepköralapú hozzáférés-vezérlése](../role-based-access-control/role-assignments-portal.md)
  
  Ez a cikk az Azure Active Directory szerepkörön alapuló hozzáférés-vezérlését és annak működési módját ismerteti.
* [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md)
  
  Ez a cikk az RBAC-ben elérhető összes beépített szerepkört ismerteti.
* [A Resource Manager-alapú és a klasszikus üzembe helyezés ismertetése](../azure-resource-manager/resource-manager-deployment-model.md)
  
  Ez a cikk a Resource Manager-alapú és a klasszikus üzembe helyezési modelleket ismerteti, és bemutatja az erőforrás-kezelő és az erőforráscsoportok használatának előnyeit
* [Szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell-lel](../role-based-access-control/role-assignments-powershell.md)
  
  Ez a cikk azt ismerteti, hogyan kezelheti a szerepköralapú hozzáférés-vezérlést Azure PowerShell-lel
* [Szerepköralapú hozzáférés-vezérlés kezelése REST API-val](../role-based-access-control/role-assignments-rest.md)
  
  Ez a cikk leírja, hogyan használható a REST API az RBAC kezeléséhez.
* [Szerepköralapú hozzáférés-vezérlés az Ignite-tól a Microsoft Azure számára](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Ez a hivatkozás a Channel 9 2015-ös MS Ignite-konferencia videójára mutat. Ebben a részben arról beszélnek, milyen hozzáférés-kezelési és jelentési képességeket nyújt az Azure, és bemutatják az Azure-előfizetés hozzáférés-biztosításának legjobb gyakorlatait az Azure Active Directory használatával.
* [Hozzáférés engedélyezése webalkalmazásoknak OAuth 2.0 és az Azure Active Directory használatával](../active-directory/active-directory-protocols-oauth-code.md)
  
  A cikk az Azure Active Directoryval történő hitelesítés teljes OAuth 2.0-s folyamatát ismerteti.
* [Kulcstartókezelési REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Ez a dokumentum referenciaként szolgál a REST API-k számára a kulcstartó programozott kezeléséhez, a kulcstartó-hozzáférési házirend beállításával együtt.
* [kulcstartó REST API-k](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  A kulcstartó REST API referenciadokumentációjára mutató hivatkozás.
* [Kulcshozzáférés-vezérlés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  A titkoskulcshozzáférés-vezérlés referenciadokumentációjára mutató hivatkozás.
* [Titkoskulcs-hozzáférés vezérlése](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  A kulcshozzáférés-vezérlés referenciadokumentációjára mutató hivatkozás.
* Kulcstartó-hozzáférési házirend [beállítása](https://msdn.microsoft.com/library/mt603625.aspx) és [eltávolítása](https://msdn.microsoft.com/library/mt619427.aspx) PowerShell használatával
  
  PowerShell-parancsmagok kulcstartó-hozzáférési házirend kezeléséhez szükséges referenciadokumentációjára mutató hivatkozások.

## <a name="next-steps"></a>További lépések
A rendszergazdáknak szóló bevezető oktatóanyag a [Bevezetés az Azure Key Vault használatába](key-vault-get-started.md) című cikkben érhető el.

A kulcstartó használatának naplózásával kapcsolatos további információkért tekintse meg [Az Azure Key Vault naplózása](key-vault-logging.md) című cikket.

A kulcsok és a titkos kulcsok Azure Key Vaulttal történő használatával kapcsolatos további információkért tekintse meg az [About Keys and Secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx) (Kulcsok és titkos kulcsok) című cikket.

Amennyiben a kulcstartóval kapcsolatban kérdése merülne fel, tekintse meg az [Azure Key Vault fórumait](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

