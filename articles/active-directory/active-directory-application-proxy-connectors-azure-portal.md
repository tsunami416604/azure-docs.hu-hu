---
title: "Külön hálózatok és helyek összekötő csoportokat használnak az Azure AD alkalmazás Proxy alkalmazások közzététele |} Microsoft Docs"
description: "Bemutatja, hogyan adhat az Azure AD alkalmazásproxy összekötők csoportok létrehozásához és kezeléséhez."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: afa710e2605bc1416b082f266010c52d13e83973
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Külön hálózatok és helyek összekötő csoportokat használnak az alkalmazások közzététele

Az Azure AD-alkalmazásproxy több és több forgatókönyvek és az alkalmazások használatát. Így hajtottunk alkalmazásnak Proxy rugalmasabb még további topológiák engedélyezésével. Alkalmazásproxy-összekötő csoportokat is létrehozhat, úgy, hogy egyes alkalmazások kiszolgálására adott összekötők rendelhet. Ez a funkció lehetőséget további és a Proxy telepítésének optimalizálása. 

Minden alkalmazásproxy-összekötőhöz egy összekötő csoportjához van hozzárendelve. Az összekötő egy csoportba tartozó összes összekötőt külön egységként a magas rendelkezésre állású működni, és a terheléselosztás. Összekötő csoporthoz tartozó összes összekötőt. Ne hozzon létre csoportokat, ha az összekötők alapértelmezett csoport vannak. A rendszergazda új csoportok létrehozása és hozzárendelése összekötők őket az Azure portálon. 

Minden alkalmazás összekötő csoporthoz rendelt. Ha nem hozza létre a csoportokat, majd az alkalmazások rendelt alapértelmezett csoporthoz. De ha az összekötők csoportokba egyes alkalmazásokat, egy adott összekötőt csoport együttműködve állíthatja be. Ebben az esetben csak az adott csoport összekötők kiszolgálására kérés alapján az alkalmazás. Ez a szolgáltatás akkor hasznos, ha az alkalmazások más-más helyen üzemeltetett. Összekötő csoportok alapján a hely, hozhat létre, így az alkalmazások mindig rendelkezésre, amelyek fizikailag közel őket összekötők által.

>[!TIP] 
>Ha nagy alkalmazásproxy-telepítést, ne rendelje az alkalmazásokat, az alapértelmezett összekötő csoporthoz. Ezzel a módszerrel új összekötőket nem bármely élő forgalom fogadására amíg hozzá nem rendeli azokat egy aktív összekötő csoporthoz. Ez a konfiguráció lehetővé teszi, amelyre az összekötők egy tétlen üzemmódban áthelyezéssel vissza az alapértelmezett csoporthoz, hogy a karbantartási a felhasználók befolyásolása nélkül végezheti el.

## <a name="prerequisites"></a>Előfeltételek
Az összekötők csoportban, győződjön meg arról, hogy rendelkezik [több összekötő telepítve](active-directory-application-proxy-enable.md). Új összekötő telepítésekor automatikusan bekerül a **alapértelmezett** összekötő csoport.

## <a name="create-connector-groups"></a>Összekötő csoportok létrehozása
Ezen lépések segítségével összekötő csoportok létrehozása. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **Azure Active Directory** > **vállalati alkalmazások** > **alkalmazásproxy**.
2. Válassza ki **új összekötő csoport**. Az új összekötő panel jelenik meg.

   ![Új összekötő csoport kiválasztása](./media/active-directory-application-proxy-connectors-azure-portal/new-group.png)

3. Nevezze el az új összekötő csoporthoz, majd válassza ki, melyik összekötők tartozik ennek a csoportnak a legördülő menü segítségével.
4. Kattintson a **Mentés** gombra.

## <a name="assign-applications-to-your-connector-groups"></a>Az összekötő csoportokhoz hozzárendelni
Ezen lépések közzétett minden alkalmazáshoz az alkalmazásproxy. Egy alkalmazás egy összekötő csoporthoz először közzététele, vagy ezeket a lépéseket segítségével módosíthatja a hozzárendelés, bármikor rendelhet hozzá.   

1. Válassza ki a kezelési irányítópult a címtáron, **vállalati alkalmazások** > **összes alkalmazás** > összekötő csoporthoz hozzárendelni kívánt alkalmazás > **Alkalmazásproxy**.
2. Használja a **összekötő csoport** legördülő menüre kattintva válassza ki a használni kívánt alkalmazást a csoportot.
3. Válassza ki **mentése** a módosítás alkalmazására.

## <a name="use-cases-for-connector-groups"></a>Összekötő csoportok alkalmazási helyzetei 

Összekötő csoportok hasznosak több, különböző esetekre, beleértve:

### <a name="sites-with-multiple-interconnected-datacenters"></a>A több összekapcsolt adatközpontokkal helyek

Számos szervezet rendelkezik több összekapcsolt adatközpontokban. Ebben az esetben meg szeretné tartani az adatközponton belül mértékű forgalom lehető mert kereszt-datacenter hivatkozások drága, és a lassú. Csak az adatközponton belül a alkalmazásokhoz kiszolgálására mindkét adatközpont összekötők telepítése. Ezt a módszert minimálisra csökkenti a kereszt-datacenter hivatkozásokat, és teljes mértékben transzparens élményt nyújt a felhasználók számára.

### <a name="applications-installed-on-isolated-networks"></a>Elkülönített hálózatok a telepített alkalmazások

Alkalmazások hálózatokban, amelyek nem részei a fő vállalati hálózat lehet üzemeltetni. Összekötő csoportok segítségével dedikált összekötők telepítése is különítheti el a hálózati alkalmazások elkülönített hálózatokon. Ez általában akkor fordul elő, ha egy külső gyártó kezeli a szervezet számára az adott alkalmazást. 

Összekötő csoportok lehetővé teszik, hogy csak bizonyos alkalmazások közzétételéhez hálózatok a dedikált összekötők telepítése egyszerűbbé és biztonságosabb, harmadik féltől alkalmazáskezelésével kihelyező tétele.

### <a name="applications-installed-on-iaas"></a>Infrastruktúra-szolgáltatási a telepített alkalmazások 

Összekötő csoportok felhőalapú férhetnek hozzá az infrastruktúra-szolgáltatási a telepített alkalmazások, adja meg egy közös szolgáltatás a hozzáférés az alkalmazásokhoz. Összekötő csoportok nem további függőség létrehozása a vállalati hálózaton, vagy a felhasználói élményt darabolható. Összekötők minden felhőbeli adatközpontot is telepíthető, és csak erre a hálózatra telepített alkalmazások kiszolgálására. Magas rendelkezésre állás biztosítása érdekében több összekötő is telepítheti.

Olyan szervezet, amely több virtuális gépet a saját IaaS csatlakozó virtuális hálózati üzemeltetett példaként érvénybe. Ahhoz, hogy az alkalmazottak ezek az alkalmazások használatát, e magánhálózatok csatlakoznak a vállalati hálózati telephelyek közötti VPN használatával. A megfelelő környezet biztosít az alkalmazottak, amelyek a helyszínen. Azonban nem lehet ideális, ha távoli alkalmazottak, mert további helyszíni infrastruktúra irányíthatja az access, az alábbi ábrán látható módon:

![Infrastruktúra-szolgáltatási hálózati AzureAD](./media/application-proxy-publish-apps-separate-networks/application-proxy-iaas-network.png)
  
Az Azure AD-alkalmazásproxy-összekötő csoportokkal engedélyezheti a közös szolgáltatás az összes alkalmazás-hozzáférés biztonságossá a vállalati hálózaton további függőség létrehozása nélkül:

![AzureAD infrastruktúra több felhőalapú szállítók](./media/application-proxy-publish-apps-separate-networks/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Többerdős – különböző összekötő csoportok az egyes erdőkhöz

A legtöbb ügyfelek, akik alkalmazásproxy telepítették az egyszeri bejelentkezéses (SSO) képességeket használ Kerberos által korlátozott delegálás (KCD) elvégzésével. Ennek érdekében az összekötő gépeknek el kell csatlakoztatni kell egy tartományhoz, amely delegálhatja a felhasználók az alkalmazás felé. Kerberos által korlátozott Delegálás támogatja az erdők közötti képességeit. De különböző Többerdős környezetben egymás között nincs megbízhatóság rendelkező vállalatok esetében egyetlen összekötőt nem használható az összes erdőben. 

Ebben az esetben adott összekötők is telepítése erdőnként, és állítsa be, csak az adott erdőben felhasználók kiszolgálására közzétett alkalmazások kiszolgálására. Minden egyes összekötőt csoport egy másik erdőben jelöli. Amíg a bérlő és a felhasználói élmény a legtöbb van egységesített összes erdőben, az erdő alkalmazásaikat, az Azure AD-csoportok használata felhasználók is hozzárendelhető.
 
### <a name="disaster-recovery-sites"></a>Vész-helyreállítási helyeken

Kétféleképpen különböző egy vész-helyreállítási helyen, attól függően, hogy a helyek kialakításával hogyan hajthatók végre:

* A vész-Helyreállítási hely épül, ahol pontosan például a fő helye és az azonos hálózati és az AD-beállítások aktív-aktív módban, ha az összekötők is létrehozhat ugyanahhoz a összekötő tartozik, mint a fő helye a vész-Helyreállítási helyen. Ez lehetővé teszi az Azure AD-észleli a feladatátvételeket meg.
* Ha a vész-Helyreállítási hely nem azonos az elsődleges helyről, létrehozhat egy másik összekötő csoportot a vész-Helyreállítási helyen, és akár 1.) kell biztonsági mentést végző alkalmazások vagy a meglévő alkalmazáshoz, a vész-Helyreállítási összekötő csoporthoz 2) manuálisan átirányít, igény szerint.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Több vállalat kiszolgálására egyetlen bérlőtől

Nincsenek a számos különböző módja a modelltől, amelyben egyetlen szolgáltatót telepíti és kezeli az Azure AD megvalósításához kapcsolódó szolgáltatások több vállalatok esetén engedélyeket. Összekötő csoportok segítségként a rendszergazdának a elkülönítse az összekötők és az alkalmazások különböző csoportokba. Egyik módja, amely megfelelő kisvállalkozások, egy egyetlen Azure AD bérlői, míg a különböző vállalatok a saját tartomány neve és a hálózatok. Ez igaz is M & A forgatókönyvek és helyzetekben ahol egyetlen IT-részleg szolgál több vállalat szabályozási és üzleti okokból. 

## <a name="sample-configurations"></a>A minta-konfigurációk

Amely is alkalmazható, például a következő összekötő-csoportokat.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Alapértelmezett konfigurációja – összekötő csoportok nem használható

Ha nem adja meg összekötő csoportok, a konfiguráció néznek ki:

![AzureAD összekötő csoport](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-1.png)
 
Ez a konfiguráció is elegendő a kisebb telepítésekhez és teszteket. Is, ha a szervezete egy egyszerű hálózati topológia fog működni.
 
### <a name="default-configuration-and-an-isolated-network"></a>Alapértelmezett konfiguráció és az elkülönített hálózat

Ez a konfiguráció még az alapértelmezett, ahol van egy adott alkalmazást, amely egy elkülönített hálózaton, például a virtuális hálózati infrastruktúra fut: 

![AzureAD összekötő csoport](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Ajánlott konfiguráció – több meghatározott csoportok és egy alapértelmezett csoport üresjárati

Az ajánlott konfiguráció bonyolult és nagy szervezetek számára, hogy az alapértelmezett összekötő csoport, amely lehessen az alkalmazásokat, és inaktív vagy újonnan telepített összekötők használható csoportként. Minden alkalmazás szolgáltatott egyéni összekötő csoportok használatával. Ez lehetővé teszi, hogy a fent leírt forgatókönyvek összetettsége.

Az alábbi példában a vállalati van két adatközpont, A és B, az átadott minden hely két összekötőt. Minden hely rendelkezik a különböző alkalmazások, amelyek akkor futnak. 

![AzureAD összekötő csoport](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Következő lépések

* [Az Azure AD-alkalmazásproxy összekötők ismertetése](application-proxy-understand-connectors.md)
* [Egyszeri bejelentkezés engedélyezése](application-proxy-sso-overview.md)


