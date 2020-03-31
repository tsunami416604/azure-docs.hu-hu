---
title: Alkalmazások közzététele külön hálózatokon összekötőcsoportokon keresztül – Azure AD
description: Bemutatja, hogyan hozhat létre és kezelhet összekötők csoportjait az Azure AD alkalmazásproxyban.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fa1de0a0e3bb91480212381e07b17875bf0bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275566"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Alkalmazások közzététele különböző hálózatokon és helyeken összekötőcsoportok használatával

Az ügyfelek az Azure AD alkalmazásproxyját használják egyre több forgatókönyvhöz és alkalmazáshoz. Ezért még rugalmasabbá tettük az App Proxy-t azáltal, hogy több topológiát tettünk lehetővé. Alkalmazásproxy-összekötőcsoportokat hozhat létre, így adott összekötőket rendelhet hozzá adott alkalmazások kiszolgálásához. Ez a funkció több vezérlést és az alkalmazásproxy központi telepítésének optimalizálását teszi lehetővé.

Minden alkalmazásproxy-összekötő egy összekötőcsoporthoz van rendelve. Az azonos összekötők csoportjához tartozó összekötők külön egységként működnek a magas rendelkezésre állás és a terheléselosztás érdekében. Minden összekötő egy összekötőcsoporthoz tartozik. Ha nem hoz létre csoportokat, akkor az összes összekötő egy alapértelmezett csoportban van. A rendszergazda új csoportokat hozhat létre, és összekötőket rendelhet hozzájuk az Azure Portalon.

Minden alkalmazás egy összekötő csoporthoz van rendelve. Ha nem hoz létre csoportokat, akkor az összes alkalmazás egy alapértelmezett csoporthoz lesz rendelve. De ha az összekötők csoportokba rendszerezi, beállíthatja, hogy minden alkalmazás egy adott összekötő csoporttal működjön. Ebben az esetben csak az abban a csoportban lévő összekötők szolgálják ki az alkalmazást kérésre. Ez a funkció akkor hasznos, ha az alkalmazások különböző helyeken vannak tárolva. Összekötőcsoportokat hozhat létre a hely alapján, így az alkalmazásokat mindig olyan összekötők szolgálják ki, amelyek fizikailag közel vannak hozzájuk.

> [!TIP]
> Ha nagy alkalmazásproxy-központi telepítéssel rendelkezik, ne rendeljen alkalmazásokat az alapértelmezett összekötőcsoporthoz. Így az új összekötők nem kapnak élő forgalmat, amíg hozzá nem rendeli őket egy aktív összekötő csoporthoz. Ez a konfiguráció azt is lehetővé teszi, hogy az összekötőket tétlen módban helyezze el, ha visszahelyezi őket az alapértelmezett csoportba, így a karbantartást anélkül végezheti el, hogy ez hatással lenne a felhasználókra.

## <a name="prerequisites"></a>Előfeltételek

Az összekötők csoportosításához meg kell győződnie arról, hogy [több összekötőt telepített.](application-proxy-add-on-premises-application.md) Új összekötő telepítésekor automatikusan csatlakozik az **Alapértelmezett** összekötőcsoporthoz.

## <a name="create-connector-groups"></a>Összekötőcsoportok létrehozása

Ezekkel a lépésekkel annyi összekötőcsoportot hozhat létre, amennyit csak szeretne.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza az **Azure Active Directory** > **Enterprise applications** > **application proxy lehetőséget.**
1. Válassza az **Új összekötőcsoport lehetőséget**. Megjelenik az Új összekötőcsoport panel.

   ![Új összekötőcsoport kijelölésének képernyője](./media/application-proxy-connector-groups/new-group.png)

1. Adjon nevet az új összekötőcsoportnak, majd a legördülő menüsegítségével válassza ki, hogy mely összekötők tartoznak ebbe a csoportba.
1. Kattintson a **Mentés** gombra.

## <a name="assign-applications-to-your-connector-groups"></a>Alkalmazások hozzárendelése az összekötőcsoportokhoz

Az alkalmazásproxyval közzétett minden egyes alkalmazáshoz kövesse az alábbi lépéseket. Az alkalmazást az első közzétételkor hozzárendelheti egy összekötőcsoporthoz, vagy ezekkel a lépésekkel bármikor módosíthatja a hozzárendelést.

1. A címtár felügyeleti irányítópultján válassza a **Vállalati alkalmazások** > **minden alkalmazás** > az alkalmazáshoz rendelni kívánt alkalmazás > **alkalmazásproxy**.
1. Az **Összekötőcsoport** legördülő menüben válassza ki azt a csoportot, amelyet az alkalmazáshasználni szeretne.
1. A módosítás alkalmazásához válassza a **Mentés** gombot.

## <a name="use-cases-for-connector-groups"></a>Esetek használata összekötőcsoportokhoz

Az összekötőcsoportok különböző forgatókönyvek esetén hasznosak, például:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Több összekapcsolt adatközponttal rendelkező helyek

Számos szervezet rendelkezik számos összekapcsolt adatközponttal. Ebben az esetben szeretné megtartani a lehető legnagyobb forgalmat az adatközponton belül, mert az adatközpontok közötti kapcsolatok drágák és lassúak. Összekötők üzembe helyezése minden adatközpontban csak az adatközponton belül található alkalmazások kiszolgálására. Ez a megközelítés minimalizálja az adatközpontok közötti kapcsolatokat, és teljesen átlátható élményt nyújt a felhasználóknak.

### <a name="applications-installed-on-isolated-networks"></a>Elszigetelt hálózatokra telepített alkalmazások

Az alkalmazások olyan hálózatokban üzemeltethetők, amelyek nem részei a fő vállalati hálózatnak. Összekötőcsoportok használatával dedikált összekötők elszigetelt hálózatokon is elkülönítheti az alkalmazásokat a hálózaton. Ez általában akkor fordul elő, ha egy külső szállító egy adott alkalmazást tart fenn a szervezet számára.

Az összekötőcsoportok lehetővé teszik, hogy dedikált összekötőket telepítsen azokra a hálózatokra, amelyek csak bizonyos alkalmazásokat tesznek közzé, így könnyebbés biztonságosabb az alkalmazáskezelés külső gyártókhoz való kiszervezése.

### <a name="applications-installed-on-iaas"></a>Az IaaS-re telepített alkalmazások

Az IaaS-re telepített felhőalapú hozzáféréshez telepített alkalmazások esetében az összekötőcsoportok közös szolgáltatást nyújtanak az összes alkalmazás hoz való hozzáférés biztosításához. Az összekötőcsoportok nem hoznak létre további függőséget a vállalati hálózattól, és nem töredelítik az alkalmazásélményt. Az összekötők minden felhőalapú adatközpontra telepíthetők, és csak az adott hálózatban található alkalmazásokat szolgálják ki. A magas rendelkezésre állás elérése érdekében több csatlakozót is telepíthet.

Vegyünk példát egy olyan szervezet, amely több virtuális gép csatlakozik a saját IaaS üzemeltetett virtuális hálózat. Annak érdekében, hogy az alkalmazottak használhassák ezeket az alkalmazásokat, ezek a magánhálózatok a helyek közötti VPN használatával csatlakoznak a vállalati hálózathoz. Ez jó élményt nyújt a helyszíni alkalmazottak számára. De nem feltétlenül ideális a távoli alkalmazottak számára, mivel további helyszíni infrastruktúrára van szükség a hozzáférés irányításához, amint az az alábbi ábrán látható:

![Az Azure AD IaaS-hálózatot szemléltető diagram](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Az Azure AD alkalmazásproxy-összekötő csoportokkal engedélyezheti, hogy egy közös szolgáltatás biztosítsa a hozzáférést az összes alkalmazáshoz anélkül, hogy további függőséget hozna létre a vállalati hálózatban:

![Az Azure AD IaaS több felhőbeli szállítója](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Többerdős – különböző összekötőcsoportok az egyes erdőkhöz

Az alkalmazásproxyt üzembe helyező legtöbb ügyfél a Kerberos korlátozott delegálás (KCD) végrehajtásával használja az egyszeri bejelentkezési (SSO) képességeit. Ennek elérése érdekében az összekötő gépeit csatlakoztatni kell egy olyan tartományhoz, amely delegálhatja a felhasználókat az alkalmazás felé. A KCD támogatja az erdők közötti képességeket. De a vállalatok, akik különböző többerdős környezetben nincs bizalom közöttük, egyetlen összekötő nem használható minden erdőben. 

Ebben az esetben adott összekötők erdőnként telepíthetők, és úgy állíthatók be, hogy olyan alkalmazásokat szolgálnak ki, amelyek csak az adott erdő felhasználóinak kiszolgálására kerültek közzétételre. Minden összekötőcsoport egy másik erdőt jelöl. Míg a bérlő és a legtöbb a tapasztalat összes erdő, a felhasználók hozzárendelhetők az erdőalkalmazások az Azure AD-csoportok használatával.

### <a name="disaster-recovery-sites"></a>Katasztrófa utáni helyreállítási helyek

A katasztrófa-elhárítási (DR) hely két különböző megközelítést alkalmazhat, attól függően, hogy a webhelyek hogyan valósulnak meg:

* Ha a VÉSZ-hely aktív-aktív módban van, ahol pontosan olyan, mint a főhely, és ugyanazokkal a hálózati és AD-beállításokkal rendelkezik, létrehozhatja az összekötőket a VÉSZ-helyen ugyanabban az összekötőcsoportban, mint a főhelyen. Ez lehetővé teszi, hogy az Azure AD észleli a feladatátvételt az Ön számára.
* Ha a VÉSZ-hely elkülönül a fő helytől, létrehozhat egy másik összekötő csoportot a VÉSZ-helyen, és vagy 1) biztonsági mentési alkalmazásokkal rendelkezik, vagy 2) manuálisan átirányítja a meglévő alkalmazást a VÉSZ-összekötő csoportba, ha szükséges.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Több vállalat kiszolgálása egyetlen bérlőből

Számos különböző módon valósítható meg egy olyan modell, amelyben egyetlen szolgáltató telepíti és karbantartja az Azure AD-vel kapcsolatos szolgáltatásokat több vállalat számára. Összekötő csoportok segítségével a rendszergazda elkülöníti az összekötők és alkalmazások különböző csoportokba. Az egyik módja, amely alkalmas a kis vállalatok, az, hogy egyetlen Azure AD-bérlő, míg a különböző vállalatok saját domain nevet és hálózatokat. Ez az M&A forgatókönyvekre és helyzetekre is igaz, amikor egyetlen informatikai részleg több vállalatot szolgál ki szabályozási vagy üzleti okokból.

## <a name="sample-configurations"></a>Mintakonfigurációk

Néhány példa, hogy a valósítható meg, a következő összekötő csoportok közé tartozik.

### <a name="default-configuration--no-use-for-connector-groups"></a>Alapértelmezett konfiguráció – nincs használat összekötőcsoportokhoz

Ha nem használ összekötőcsoportokat, a konfiguráció a következőkre fog kinézni:

![Példa Az Azure AD Nincs összekötőcsoport](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Ez a konfiguráció elegendő a kis telepítések és tesztek. Akkor is jól fog működni, ha a szervezet egy sima hálózati topológiával rendelkezik.

### <a name="default-configuration-and-an-isolated-network"></a>Alapértelmezett konfiguráció és elszigetelt hálózat

Ez a konfiguráció az alapértelmezett konfiguráció, amelyben van egy adott alkalmazás, amely egy elszigetelt hálózaton, például az IaaS virtuális hálózaton fut:

![Példa Az Azure AD nincs összekötőcsoportok és egy elszigetelt hálózat](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Ajánlott konfiguráció – több konkrét csoport és egy alapértelmezett csoport az alapjárathoz

A nagy és összetett szervezetek ajánlott konfigurációja az alapértelmezett összekötőcsoport olyan csoportként való használata, amely nem szolgál ki alkalmazásokat, és tétlen vagy újonnan telepített összekötőkhöz használatos. Minden alkalmazás személyre szabott összekötőcsoportokkal szolgálki. Ez lehetővé teszi a fent leírt forgatókönyvek összes összetettségét.

Az alábbi példában a vállalat két adatközponttal rendelkezik, az A és a B, két összekötővel, amelyek az egyes helyeket szolgálják ki. Minden webhely különböző alkalmazásokkal rendelkezik, amelyek futnak rajta.

![Példa 2 adatközponttal és 2 összekötővel rendelkező vállalatra](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>További lépések

* [Az Azure AD alkalmazásproxy-összekötők megismerése](application-proxy-connectors.md)
* [Egyszeri bejelentkezés engedélyezése](what-is-single-sign-on.md)
