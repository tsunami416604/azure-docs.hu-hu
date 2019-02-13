---
title: 'Azure AD Connect: Támogatott topológiák |} A Microsoft Docs'
description: Ez a témakör részletesen támogatott és nem támogatott topológiák az Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b1c0d33a7d920f76bcbea6d8d6babc7390003bc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196586"
---
# <a name="topologies-for-azure-ad-connect"></a>Azure AD Connect-topológiák
Ez a cikk ismerteti a különböző helyszíni és Azure Active Directory (Azure AD) topológiákat, amelyek az Azure AD Connect-szinkronizálást használ, a főbb integrációs megoldása. Ez a cikk is támogatott, és nem támogatott konfigurációkat tartalmaz.


Íme a képeket a cikkben a jelmagyarázatban:

| Leírás | Szimbólum |
| --- | --- |
| A helyszíni Active Directory-erdő |![A helyszíni Active Directory-erdő](./media/plan-connect-topologies/LegendAD1.png) |
| A helyszíni Active Directory szűrt importálás |![Szűrt importálás az Active Directory](./media/plan-connect-topologies/LegendAD2.png) |
| Az Azure AD Connect szinkronizálási kiszolgáló |![Az Azure AD Connect szinkronizálási kiszolgáló](./media/plan-connect-topologies/LegendSync1.png) |
| "Átmeneti módban" az Azure AD Connect szinkronizálási kiszolgáló |!["Átmeneti módban" az Azure AD Connect szinkronizálási kiszolgáló](./media/plan-connect-topologies/LegendSync2.png) |
| A Forefront Identity Manager (FIM) 2010 vagy a Microsoft Identity Manager (MIM) 2016 GALSync |![A FIM 2010 vagy a MIM 2016 GALSync](./media/plan-connect-topologies/LegendSync3.png) |
| Az Azure AD Connect szinkronizálási kiszolgáló, részletes |![Az Azure AD Connect szinkronizálási kiszolgáló, részletes](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Nem támogatott forgatókönyv |![Nem támogatott forgatókönyv](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> A Microsoft nem támogatja a konfigurációkat és a műveleteket, amelyeket műveleteken az Azure AD Connect szinkronizálásának módosítását vagy a. Ezek a konfigurációk vagy műveletek bármelyike okozhat az Azure AD Connect szinkronizálása inkonzisztens vagy nem támogatott állapotban. A Microsoft ezért nem tud műszaki támogatást biztosítani az ilyen környezetekhez.


## <a name="single-forest-single-azure-ad-tenant"></a>Egyetlen erdő, egyetlen Azure AD-bérlő
![Egyetlen erdő és a egy egybérlős topológia](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

A leggyakrabban használt topológia akkor egyetlen helyszíni erdő, egy vagy több tartomány és a egy egyetlen Azure AD-bérlőben. Az Azure AD-hitelesítés a Jelszókivonat-szinkronizálás szolgál. Az Azure AD Connect gyorstelepítés csak ez a topológia támogatja.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Egyetlen erdő, több szinkronizálási kiszolgálót egy Azure AD-bérlővel
![Egyetlen erdő nem támogatott, szűrt topológia](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Kellene több Azure AD Connect szinkronizálási kiszolgálót az Azure AD-bérlőhöz kapcsolódó nem támogatott, kivéve a egy [átmeneti kiszolgáló](#staging-server). Ez akkor is, ha ezek a kiszolgálók úgy vannak konfigurálva, kísérletezzen kölcsönösen kizárják egymást az objektumok szinkronizálásához nem támogatott. Előfordulhat, hogy kell figyelembe venni ehhez a topológiához Ha ugyanazon a kiszolgálón az erdő összes tartománya nem éri el, vagy ha kívánja terjeszteni a terhelés több kiszolgáló között.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Több erdő, egyetlen Azure AD-bérlő
![Több erdő és a egy egybérlős topológia](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Számos vállalat rendelkezik olyan több helyszíni Active Directory-erdővel rendelkező környezetben. Különböző oka, hogy több mint egy helyszíni Active Directory-erdőben. Tipikus példák a fiók erőforráserdővel és a egy összeolvadást vagy felvásárlást eredményét tervek.

Ha több erdő, minden olyan erdőben van egyetlen elérhetőnek kell lennie az Azure AD Connect szinkronizálási kiszolgáló. Nem kell a kiszolgáló csatlakoztatása egy tartományhoz. Ahhoz, hogy minden olyan erdőben, ha a kiszolgáló a szegélyhálózaton (más néven DMZ-t, vagy demilitarizált zónában és demilitarizált) helyezheti.

Az Azure AD Connect telepítővarázsló egyesíthetők a felhasználók, akik több erdő megjelenjen többféle lehetőséget kínál. A célja, hogy egy felhasználó Azure AD-ben egyszer szerepelnek. Van néhány gyakori topológiák az egyéni telepítési elérési út a telepítési varázslóban konfigurálható. Az a **felhasználók egyedi azonosítása** lapon, válassza ki a megfelelő beállítást, amely a topológia jelöli. Az összevonás csak a felhasználók számára konfigurált. Ismétlődő csoportok nem kell összevonni alapértelmezés szerint.

Közös topológiákat tárgyalja a szakaszok kapcsolatos külön topológiák [teljes rácsot alkotó](#multiple-forests-full-mesh-with-optional-galsync), és [a fiók-erőforrás topológia](#multiple-forests-account-resource-forest).

Feltételezi, hogy az alapértelmezett konfiguráció az Azure AD Connect szinkronizálása:

* Minden felhasználó csak egy engedélyezett fiókkal rendelkezik, és az erdőben, ahol ennek a fióknak-e a felhasználó hitelesítésére szolgál. Ez feltételezi, a jelszókivonatok szinkronizálása, átmenő hitelesítést és összevonási. UserPrincipalName and sourceAnchor/immutableID come from this forest.
* Minden felhasználó csak egy postaláda rendelkezik.
* Az erdő, amely a postaládát egy felhasználó futtatja a legjobb adatminőség attribútumok látható a az Exchange globális cím lista (GAL) rendelkezik. Ha a felhasználó nem postaláda, bármely erdőben való közreműködésre ezeket az attribútumértékeket használható.
* Rendelkezhetnek hivatkozott postafiókkal, ha nincs egy fiók a bejelentkezéshez használt eltérő erdőben.

Ha a környezet nem egyezik meg az ilyen Előfeltevések, a következő dolog történik:

* Ha egynél több aktív fiók vagy postaláda egynél több, a szinkronizálási motor választja ki egyet, és figyelmen kívül hagyja a többi.
* Nincs más aktív fiókkal hivatkozott postafiókkal nem exportálja az Azure ad-hez. A felhasználói fiók nem szerepel minden olyan csoport tagjaként. A DirSync hivatkozott postafiókkal mindig a normál postaláda jelzi. Ez a változás szándékosan egy másik működés, amelyek hatékonyabban támogatják a több erdővel rendelkező forgatókönyvek.

További információk a [az alapértelmezett konfiguráció ismertetése](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Több erdő, több szinkronizálási kiszolgálót egy Azure AD-bérlővel
![Több erdő, és több szinkronizálási kiszolgálót nem támogatott topológiát](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Egynél több Azure AD Connect szinkronizálási kiszolgáló csatlakozik egy Azure AD-bérlő nem támogatott. Kivételt jelentenek ez alól használatát egy [átmeneti kiszolgáló](#staging-server).

Ebben a topológiában az alábbihoz, amely eltér **több szinkronizálási kiszolgálót** kapcsolódik, egyetlen Azure AD bérlő nem támogatott.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Több erdő, egyetlen szinkronizálási kiszolgálóval, a felhasználók csak egy címtárban szerepelnek
![A felhasználók csak egyszer jelölő címtárak összességében lehetőség](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Több erdő, és külön topológiák ábrázolása](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

Ebben a környezetben helyszíni erdőket külön entitásokként kell kezelni. Nincs felhasználó nem található bármely másik erdőben. Minden olyan erdőben, a saját Exchange-szervezet rendelkezik, és nincs GALSync az erdők között van. Ez a topológia fúzió vagy felvásárlás után, vagy egy szervezet a helyzet lehet, ahol mindegyik üzleti egység egymástól függetlenül működik. Erdők ugyanazon a szervezeten belül, az Azure ad-ben és az egyesített GAL jelennek meg. Az előző képen látható minden erdőben lévő minden egyes objektum tartozik a metaverzumba egyszer jelölt és összevonva jelenik meg a céloldali Azure AD-bérlő.

### <a name="multiple-forests-match-users"></a>Több erdő: felhasználó
Ezek a forgatókönyvek a gyakori, hogy terjesztési és biztonsági csoportok felhasználókat, névjegyeket és idegen rendszerbiztonsági tagok (FSP) vegyesen tartalmazhatnak. FSP használják az Active Directory Domain Servicesben (AD DS), amelyek tagjai a más erdőkben egyetlen biztonsági csoportba. Az Azure ad-ben minden FSP hozzárendelve a tényleges objektum.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Több erdő: nem kötelező GALSync a háló teljes
![Az egyező, ha a felhasználói identitások több címtárat is léteznek a levél attribútum használatára vonatkozó beállítást](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Teljes rácsot alkotó topológia több erdőhöz](./media/plan-connect-topologies/MultiForestFullMesh.png)

Egy teljes rácsot alkotó topológia lehetővé teszi, hogy bármely erdőben található felhasználók és erőforrások. Általában nincsenek kétirányú megbízhatósági kapcsolattal az erdők között.

Ha Exchange egynél több erdőben található, előfordulhat, (opcionálisan) egy helyszíni GALSync megoldás. Minden felhasználó van majd kapcsolattartóként az összes többi erdője. FIM 2010 vagy a MIM 2016 GALSync gyakran biztosítják. Az Azure AD Connect a helyszíni GALSync nem használható.

Ebben a forgatókönyvben a levél attribútum keresztül csatlakoztatott identitásobjektumok. Egy postaládát egy erdő rendelkező felhasználóként csatlakozik, a többi erdőben lévő ügyfelekkel.

### <a name="multiple-forests-account-resource-forest"></a>Több erdő: fiók-Erőforráserdő
![Ha több címtárat is léteznek identitások egyeztetéséhez az ObjectSID és msExchMasterAccountSID attribútumok használata beállítást](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Fiók-erőforrás szolgának több erdőhöz](./media/plan-connect-topologies/MultiForestAccountResource.png)

Egy fiók-erőforrás szolgának, fel kell egy vagy több *fiók* erdőkbe tartozó aktív felhasználói fiókok. Azt is, hogy egy vagy több *erőforrás* , letiltott fiókokat tartalmazó erdők.

Ebben a forgatókönyvben egy (vagy több) Erőforráserdő összes fiókerdővel megbízik. Az erőforráserdőben általában rendelkezik egy kiterjesztett Active Directory-sémát az Exchange és a Lync. Az Exchange és a Lync-szolgáltatások más közös szolgáltatásokat, valamint az erdőben találhatók. Felhasználók letiltott felhasználói fiók rendelkezik az erdőben, és a postaládát a fiókerdő van csatolva.

## <a name="office-365-and-topology-considerations"></a>Az Office 365 és a topológiai szempontok
Egyes Office 365 számítási feladatokhoz van bizonyos korlátozások a támogatott topológiák:

| Számítási feladat | Korlátozások |
| --------- | --------- |
| Exchange Online | Támogatja az Exchange Online hibrid topológiák kapcsolatos további információkért lásd: [hibrid telepítések esetén több Active Directory-erdők](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype Vállalati verzió | Ha több helyszíni erdővel használ, csak a fiók-erőforrás szolgának használata támogatott. További információkért lásd: [környezeti követelmények a Skype for Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Ha Ön által felügyelt nagyobb szervezettel, akkor érdemes használni a [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) funkció. Lehetővé teszi, hogy meghatározza, mely adatközpont-régiót, a felhasználói erőforrások találhatók.

## <a name="staging-server"></a>Átmeneti kiszolgáló
![Átmeneti kiszolgáló topológiában](./media/plan-connect-topologies/MultiForestStaging.png)

A második kiszolgáló telepítése az Azure AD Connect támogatja *átmeneti módban*. Ebben a módban egy kiszolgáló adatokat olvas be minden csatlakoztatott címtár, de nem írja minden csatlakoztatott címtárhoz. A normál szinkronizálási ciklust használja, és ezért a frissített másolatát a azonosító adatok is.

A katasztrófa, ahol az elsődleges kiszolgáló meghibásodik, a is átadja a feladatokat az átmeneti kiszolgálót. Ehhez az Azure AD Connect varázslójában. Ez a második kiszolgáló egy másik adatközpontban lévő helyezhetők, mert nincs infrastruktúrára van megosztva az elsődleges kiszolgáló. Manuálisan másolja a második kiszolgáló az elsődleges kiszolgálón végrehajtott konfigurációs változásokat.

Egy átmeneti kiszolgálón segítségével tesztelheti egy új egyéni konfigurációs és arról, hogy rendelkezik az adatokon. A módosítások megtekintheti és módosíthatja a konfigurációt. Ha elégedett az új konfigurációt, győződjön meg az átmeneti kiszolgálón az aktív kiszolgáló, és állítsa be a régi aktív kiszolgáló átmeneti módra.

Ez a módszer használatával az aktív szinkronizálási kiszolgálót cserélje le. Készítse elő az új kiszolgálón, és állítsa be az átmeneti környezetű üzemmód. Győződjön meg arról, hogy jó állapotban átmeneti módban (aktiválás), tiltsa le, és állítsa le a jelenleg aktív kiszolgálót.

Lehetőség van egynél több átmeneti kiszolgálón, ha rendelkezik több biztonsági mentése különböző adatközpontokban szeretne.

## <a name="multiple-azure-ad-tenants"></a>Több Azure AD-bérlő
Azt javasoljuk, hogy egyetlen új bérlő kellene a szervezet Azure AD-ben.
Mielőtt több Azure AD-bérlőt használni kívánja, tekintse meg a cikket [adminisztrációs egységek felügyelete az Azure ad-ben](../users-groups-roles/directory-administrative-units.md). Ez fedezi a gyakori forgatókönyvek, ahol egyetlen bérlő használhat.

![Több erdő, és több bérlő topológiája](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

1:1 kapcsolatot egy Azure AD Connect szinkronizálási kiszolgáló és az Azure AD-bérlő között van. Minden egyes Azure AD-bérlővel kell egy Azure AD Connect szinkronizálási kiszolgáló telepítés. Az Azure AD bérlőhöz példányok elvárt legyenek különítve. Azt jelenti egy bérlő felhasználói nem látja a többi bérlő felhasználói. Ha azt szeretné, hogy ez a fajta elkülönítés, ez a konfiguráció támogatott. Ellenkező esetben használjon az egyetlen Azure AD-bérlő modell.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Az egyes objektumok csak egyszer az Azure AD-bérlő
![Egyetlen erdő szűrt topológia](./media/plan-connect-topologies/SingleForestFiltered.png)

Ebben a topológiában egy Azure AD Connect szinkronizálási kiszolgáló minden egyes Azure AD-bérlőhöz van kapcsolva. Az Azure AD Connect szinkronizálási kiszolgálót kell konfigurálni a szűréshez, ezért a művelethez használandó objektumok egymást kölcsönösen kizáró csoportja. Az egyes tartományokhoz vagy szervezeti egység minden kiszolgáló például gazdagépcsoportjaira.

A DNS-tartomány regisztrálni lehet egyetlen Azure AD-bérlővel. Az UPN-EK, a felhasználók a helyszíni Active Directory-példányban is használnia kell a különálló névtereket. Ha például az előző képen látható, három különálló UPN-utótagot regisztrált a helyszíni Active Directory-példányban: contoso.com, fabrikam.com és wingtiptoys.com. Minden egyes a helyszíni Active Directory-tartományban lévő felhasználók egy másik névtér használatára.

>[!NOTE]
>Globális cím lista szinkronizálás (GalSync) nem automatikusan történik, ebben a topológiában, és megköveteli egy további egyéni MIM megvalósításának egyes bérlők ne legyen a teljes globális cím lista (GAL) az Exchange Online és Skype vállalati online.


Ez a topológia rendelkezik a következő egyéb korlátozások támogatott forgatókönyvek:

* Az Azure AD-bérlőt csak az egyik engedélyezheti az Exchange hibrid a helyszíni Active Directory-példánnyal.
* Windows 10-eszközök csak egy Azure AD-bérlővel társítható.
* Egyszeri bejelentkezés (SSO) esetén alkalmazott beállítás jelszó Jelszókivonat-szinkronizálás és átmenő hitelesítés csak egy Azure AD-bérlővel is használható.

A jelszóvisszaíró objektumok egymást kölcsönösen kizáró csoportja vonatkozó követelmény is vonatkozik. Mivel egyetlen helyszíni konfigurációt azt feltételezik néhány visszaíró szolgáltatását nem támogatottak ebben a topológiában. Ezek a funkciók a következők:

* A jelszóvisszaíró csoport alapértelmezett konfigurációval.
* Eszközvisszaírás.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Minden objektum többször is feldolgozza az Azure AD-bérlő
![Egyetlen erdő, és több bérlő nem támogatott topológiát](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Egyetlen erdő, és több összekötő nem támogatott topológiát](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Ezek a feladatok nem támogatottak:

* Több Azure AD-bérlő számára ugyanaz a felhasználó szinkronizálása.
* Győződjön meg arról, konfigurációjának módosításával, hogy egy Azure AD-bérlő felhasználóinak névjegyeit, állítsa be egy másik Azure AD-bérlő jelennek meg.
* Az Azure AD Connect szinkronizálási szeretne csatlakozni a több Azure AD-bérlő módosítása

### <a name="galsync-by-using-writeback"></a>A jelszóvisszaíró használatával GALSync
![Több erdő, és több címtár, az Azure ad-ben összpontosító GALSync nem támogatott topológiát](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Több erdő, és több címtár, a GALSync koncepciójának nem támogatott topológiát a helyszíni Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Az Azure AD-bérlők elvárt legyenek különítve. Ezek a feladatok nem támogatottak:

* Az Azure AD Connect szinkronizálási adatokat olvasni az egy másik Azure AD-bérlő konfigurációjának módosítása
* Felhasználók exportálása névjegyként egy másik helyszíni Active Directory-példánynak az Azure AD Connect-szinkronizálás használatával.

### <a name="galsync-with-on-premises-sync-server"></a>A helyszíni szinkronizálási kiszolgálóval GALSync
![Több erdő, és több címtár topológiában GALSync](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

FIM 2010 vagy a MIM 2016 helyszíni segítségével (keresztül GALSync) felhasználók szinkronizálása az Exchange két szervezet között. A felhasználók az egyik szervezet külső felhasználók/ügyfelek a másik szervezet jelennek meg. A különféle helyszíni Active Directory-példányok majd szinkronizálhatók a saját Azure AD-bérlőt.

## <a name="next-steps"></a>További lépések
Ebben az esetben az Azure AD Connect telepítésével kapcsolatban lásd: [az Azure AD Connect egyéni telepítési](how-to-connect-install-custom.md).

Tudjon meg többet a [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációja.

Tudjon meg többet [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).
