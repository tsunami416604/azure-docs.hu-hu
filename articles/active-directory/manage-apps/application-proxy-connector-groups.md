---
title: Közzététel külön hálózatokon és helyeket összekötő csoportokat használnak az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: A cikk ismerteti, hogyan csoportok az Azure AD-alkalmazásproxy-összekötők létrehozásához és kezeléséhez.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 8905ea028668f00d92f18bf7d8412e29ffc8283f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153589"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Külön hálózatok és helyek összekötőcsoportok használatával az alkalmazások közzététele

Ügyfeleink további és további forgatókönyvek az Azure AD-alkalmazásproxy és az alkalmazások használatára. Így végeztünk alkalmazásproxyval még több rugalmas további topológiák engedélyezésével. Application Proxy connector csoportokat hozhat létre, így hozzárendelhet adott összekötők adott alkalmazások kiszolgálására. Ez a funkció lehetővé teszi további ellenőrzési és a Proxy telepítésének optimalizálása. 

Minden Application Proxy connector hozzá van rendelve egy összekötőcsoporthoz. Összekötő ugyanabba a csoportba tartozó összes összekötő jár el a magas rendelkezésre állású külön egységként, és a terheléselosztás. Összekötő csoporthoz tartozó összes összekötőt. Ha nem hoz létre csoportokat, minden összekötőt egy alapértelmezett csoport vannak. A rendszergazda létrehozhat új csoportokat és összekötők hozzájuk rendelhet az Azure Portalon. 

Összes alkalmazás hozzá van rendelve egy összekötőcsoporthoz. Ha nem hoz létre csoportokat, majd az alkalmazások vannak hozzárendelve egy alapértelmezett csoport. De ha csoportokba rendezheti a az összekötőkhöz, állíthatja be minden egyes a szolgáltatásalkalmazás alkalmassá tétele egy adott összekötőcsoporthoz. Ebben az esetben csak az adott csoport összekötők szolgálja ki az alkalmazás kérésre. Ez a funkció akkor hasznos, ha az alkalmazások különböző helyeken üzemelnek. Összekötőcsoportok helye, hozhat létre, így az alkalmazások mindig szolgálja ki, amelyek fizikailag közel őket az összekötők által.

>[!TIP] 
>Ha egy nagy Proxy telepítésének, minden alkalmazást az alapértelmezett összekötő csoporthoz sem rendelhet. Ezzel a módszerrel új összekötő nem kapja meg az élő adatforgalom mindaddig, amíg egy aktív összekötőcsoport hozzárendelhetők. Ez a konfiguráció lehetővé teszi put összekötők egy tétlen üzemmódban vissza az alapértelmezett csoport helyezi őket, hogy a felhasználók anélkül is elvégezheti a karbantartás.

## <a name="prerequisites"></a>Előfeltételek
Az összekötők csoport, győződjön meg arról, hogy rendelkezik [több összekötő telepítve](application-proxy-add-on-premises-application.md). Egy új összekötő a telepítéskor automatikusan bekerül a **alapértelmezett** összekötőcsoport.

## <a name="create-connector-groups"></a>Összekötő-csoportok létrehozása
Ezen lépések összekötő csoportok létrehozásához. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **Azure Active Directory** > **vállalati alkalmazások** > **alkalmazásproxy**.
2. Válassza ki **új összekötőcsoport**. Az új Összekötőcsoport panel jelenik meg.

   ![Válassza ki az új összekötőcsoport](./media/application-proxy-connector-groups/new-group.png)

3. Adja meg az új összekötő csoport nevét, majd használja a legördülő menüből válassza ki, mely összekötők az ebbe a csoportba tartoznak.
4. Kattintson a **Mentés** gombra.

## <a name="assign-applications-to-your-connector-groups"></a>Az összekötőcsoportok alkalmazások hozzárendelése
Használja ezeket a lépéseket, már közzétett minden alkalmazáshoz az alkalmazásproxy használatával. Először tegye közzé azt, vagy használhatja ezeket a lépéseket, ha azt szeretné, a-hozzárendelés módosítása egy összekötőcsoporthoz alkalmazás rendelhet hozzá.   

1. Válassza ki a kezelési irányítópult a címtáron, **vállalati alkalmazások** > **minden alkalmazás** > az alkalmazás egy összekötőcsoporthoz hozzárendelni kívánt > **Alkalmazásproxy**.
2. Használja a **Összekötőcsoport** legördülő menüre, és válassza ki a csoportot, a használni kívánt alkalmazást.
3. Válassza ki **mentése** a módosítás alkalmazására.

## <a name="use-cases-for-connector-groups"></a>Összekötőcsoportok alkalmazási helyzetei 

Összekötőcsoportok hasznosak lehetnek a különböző forgatókönyvekben, például:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Összekapcsolt több adatközpont-webhelyekre

Számos szervezet rendelkezik összekapcsolt adatközpontok számos. Ebben az esetben szeretné megőrizni a lehető akár az adatközponton belül forgalmat, mivel több adatközpontot hivatkozások költséges és a lassú. Telepítheti az összekötők a valamennyi adatközpont csak az adatközponton belüli alkalmazások kiszolgálására. Ez a megközelítés minimálisra csökkenti a kereszt-datacenter hivatkozások, és a egy teljes mértékben átlátható felhasználói élményt biztosít a felhasználók számára.

### <a name="applications-installed-on-isolated-networks"></a>Elkülönített hálózatok a telepített alkalmazások

Alkalmazások hálózatokban, amelyek nem részei a fő vállalati hálózat lehet üzemeltetni. Összekötőcsoportok segítségével dedikált összekötők telepítése elkülönített hálózatok is elkülönítse az alkalmazásokat a hálózathoz. Ez általában akkor fordul elő, ha egy harmadik fél fenntart egy adott alkalmazást a szervezete számára. 

Összekötőcsoportok lehetővé teszi, hogy telepítse dedikált összekötőket az olyan meglévő hálózatait, amely csak bizonyos alkalmazások közzététele így egyszerűbb és biztonságosabb kiszervezik a külső gyártók alkalmazáskezelésével.

### <a name="applications-installed-on-iaas"></a>Az IaaS telepített alkalmazások 

Összekötőcsoportok felhőhozzáférést az IaaS-on telepített alkalmazásokat, egy közös szolgáltatás az összes alkalmazáshoz való hozzáférés biztonsága érdekében adja meg. Összekötőcsoportok ne hozzon létre további függőség a vállalati hálózaton, vagy töredékben a felhasználói élményt. Összekötők minden felhőbeli adatközpontot is telepíthető, és csak a hálózaton található alkalmazások kiszolgálására. Magas rendelkezésre állás eléréséhez több összekötő is telepítheti.

Igénybe vehet egy olyan szervezet, amely több, saját IaaS csatlakozó virtuális gépek virtuális hálózati üzemeltetett példaként. Ahhoz, hogy az alkalmazottak ezek az alkalmazások használatát, e magánhálózatok site-to-site VPN használatát a vállalati hálózathoz csatlakozik. Jó élményt biztosít az alkalmazottak, amelyek a helyszínen található. Azonban nem lehet ideális megoldás a távoli alkalmazottak számára, mert a szükséges további helyszíni infrastruktúra irányíthatja a hozzáférést, ahogy az az alábbi ábrán látható:

![Azure ad IaaS hálózati](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Az Azure AD Application Proxy connector csoportok egy közös szolgáltatást, hogy az alkalmazások biztonságos elérését minden további függőség létrehozása a vállalati hálózaton nélkül engedélyezheti:

![Azure ad IaaS több Felhőszolgáltatóknak](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Többerdős – különböző összekötőcsoportok az egyes erdőkhöz

A legtöbb ügyfél, aki telepítette szolgáltatásalkalmazás-Proxy az egyszeri bejelentkezéses (SSO) képességeket használ Kerberos által korlátozott delegálás (KCD) végrehajtásával. Ennek érdekében a csatlakozó gépek is engedélyezheti – delegálja a felhasználók felé az alkalmazás egy tartományhoz csatlakoztatni kell. KCD támogatja az erdők közötti képességeit. De különböző több erdőt tartalmazó környezetek között nincs megbízhatóság közöttük rendelkező vállalatok esetében egy összekötőt nem használható minden olyan erdőben. 

Ebben az esetben egyedi összekötők telepíthető erdőnként, és állítsa be, amely csak az adott erdőre felhasználók kiszolgálása érdekében közzétett alkalmazások kiszolgálására. Minden egyes összekötőcsoport jelöli egy másik erdőben találhatók. Amíg a bérlő és a felhasználói élményt a legtöbb egységesített van minden olyan erdőben, a felhasználókhoz rendelhető erdő alkalmazásokhoz az Azure AD-csoportok használatával.
 
### <a name="disaster-recovery-sites"></a>Vész-helyreállítási hely

Attól függően, hogyan vannak megvalósítva a helyek egy vész-helyreállítási hely elvégezhető két többféle módon is:

* A DR-hely aktív – aktív üzemmód, ahol a fő helye pontosan ugyanúgy történik, és azonos hálózati és AD-beállításokat a épül fel, ha az összekötők is létrehozhat a DR hely ugyanazt a összekötő tartozik, mint a fő helye. Ez lehetővé teszi az Azure AD észleli a feladatátvételeket az Ön számára.
* Ha a DR hely elkülönül a fő helye, egy másik összekötőcsoport a DR-helyet hozhat létre, és vagy (1.) kell biztonsági mentési alkalmazások vagy (2) manuálisan átirányít a meglévő alkalmazást a Vészhelyreállítás összekötőcsoportot, igény szerint.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Több vállalatot továbbítása az egyetlen új bérlő

Nincsenek a számos különböző módon, amelyben egyetlen szolgáltató üzembe helyezi és kezeli az Azure AD egy modell megvalósításához kapcsolódó szolgáltatások több vállalatok számára. Összekötőcsoportok segítségként a rendszergazdának a elkülönítse az összekötők és az alkalmazások különböző csoportokba. Egyik lehetőség, amelyhez a kisvállalatok számára ideális, hogy egyetlen Azure AD bérlői, míg a különböző vállalatok a saját tartománynév és a hálózatok. Ez a is M & A-forgatókönyveinek és olyan helyzetekben, egy egyetlen IT-részleg szolgál a szabályozási és üzleti okokból több vállalat. 

## <a name="sample-configurations"></a>Minta-konfigurációk

Amely valósítható meg, például a következő összekötő-csoportokat.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Alapértelmezett konfiguráció – összekötőcsoportok nem használható

Összekötőcsoportok nem használja, ha a konfiguráció következő lenne:

![Azure ad nem Összekötőcsoportok](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)
 
Ebben a konfigurációban is használhatók a kisebb telepítésekhez és a teszteket. Jól, ha a szervezet rendelkezik egy egybesimított hálózati topológia fog működni.
 
### <a name="default-configuration-and-an-isolated-network"></a>Alapértelmezett konfiguráció és a egy elkülönített hálózat

Ez a konfiguráció az alapértelmezett értéktől, amelyben van egy adott alkalmazás, például az IaaS virtuális hálózat egy elkülönített hálózaton futó továbbfejlesztett változata: 

![Azure ad nem Összekötőcsoportok](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Ajánlott konfiguráció – több adott csoportok és a egy alapértelmezett csoport üresjárati

Az ajánlott konfiguráció nagy és összetett szervezetek számára, hogy az alapértelmezett összekötőcsoport rendelkezik, mint egy csoportot, amely nem szolgál az alkalmazásokat, és a tétlen vagy újonnan telepített összekötők szolgál. Minden alkalmazás fájlnévkiterjesztései, egyéni összekötő csoportok használatával. Ez lehetővé teszi a fent leírt forgatókönyveket összetettségét.

Az alábbi példában a vállalat rendelkezik két adatközpontban, A és B való két összekötőt, amely minden hely szolgálnak. Minden hely rendelkezik rajta futó különböző alkalmazásokhoz. 

![Azure ad nem Összekötőcsoportok](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>További lépések

* [Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)
* [Egyszeri bejelentkezés engedélyezése](what-is-single-sign-on.md)


