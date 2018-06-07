---
title: 'Az Azure AD Connect: Támogatott topológiák |} Microsoft Docs'
description: Ez a témakör a támogatott és nem támogatott topológiák részletezi az Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 02/27/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4ea45bef5e50c41d07ddfeb5fcd6dba4d7247c61
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593761"
---
# <a name="topologies-for-azure-ad-connect"></a>Azure AD Connect-topológiák
Ez a cikk ismerteti a különböző helyszíni és az Azure AD Connect szinkronizálási szolgáltatás, a kulcs integrációs megoldást használó Azure Active Directory (Azure AD) topológiákat. Ez a cikk egyaránt támogatott, és nem támogatott konfigurációkat tartalmazza.

A jelmagyarázatban képeket a cikkben a következő:

| Leírás | Szimbólum |
| --- | --- |
| A helyszíni Active Directory-erdő |![A helyszíni Active Directory-erdő](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| A helyszíni Active Directory szűrt importálás |![Szűrt importálás Active Directory](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Az Azure AD Connect sync-kiszolgáló |![Az Azure AD Connect sync-kiszolgáló](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| Az Azure AD Connect szinkronizálási kiszolgálót "átmeneti módban" |![Az Azure AD Connect szinkronizálási kiszolgálót "átmeneti módban"](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| A Forefront Identity Manager (FIM) 2010 vagy a Microsoft Identity Manager (MIM) 2016 GALSync |![A FIM 2010 vagy a MIM 2016 GALSync](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Az Azure AD Connect szinkronizálási kiszolgálót, részletes |![Az Azure AD Connect szinkronizálási kiszolgálót, részletes](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| A forgatókönyv nem támogatott |![A forgatókönyv nem támogatott](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> A Microsoft nem támogatja a módosítása, vagy a konfiguráció vagy hivatalosan ismertetett műveletek kívül az Azure AD Connect szinkronizálási működő. E konfiguráció vagy a műveletek bármelyike okozhatja az Azure AD Connect szinkronizálási szolgáltatás inkonzisztens vagy nem támogatott állapotban. A Microsoft ezért nem tud műszaki támogatást biztosítani az ilyen környezetekhez.


## <a name="single-forest-single-azure-ad-tenant"></a>Egyetlen erdő, egyetlen Azure AD-bérlő
![Egyetlen erdő esetén, és egyetlen bérlő topológia](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

A leggyakrabban használt topológia egyetlen helyszíni erdő, egy vagy több tartományt, és az egy egyetlen Azure AD bérlői. Az Azure AD-alapú hitelesítés Jelszókivonat-szinkronizálást használatos. Az Azure AD Connect a gyorstelepítés csak ez a topológia támogatja.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Egyetlen erdő, több szinkronizálási kiszolgálót egy Azure AD-bérlő számára
![Egyetlen erdő esetén nem támogatott, a szűrt topológia](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Ha több az Azure AD Connect szinkronizálási kiszolgálót is csatlakozik ugyanahhoz az Azure AD-bérlő nem támogatott, kivéve a [kiszolgáló átmeneti](#staging-server). Ez akkor is, ha ezek a kiszolgálók úgy vannak konfigurálva, egymást kölcsönösen kizáró objektumkészleteket szinkronizálása nem támogatott. Akkor lehet, hogy tekintette ebben a topológiában Ha egy kiszolgálóról az erdőben lévő összes tartományban nem éri el, vagy ha osszák szét a több kiszolgálót szeretne.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Több erdő, egyetlen Azure AD-bérlő
![Több erdő és a egybérlős topológia](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Számos szervezet környezetekben a helyszíni Active Directory több erdővel rendelkezik. Nincsenek egynél több a helyszíni Active Directory-erdő rendelkező különböző okait. Jellemző példák tervek fiók erőforráserdővel és egy összeolvadást vagy felvásárlást eredményét.

Ha rendelkezik több erdő, az összes erdőben egyetlen elérhetőnek kell lennie az Azure AD Connect szinkronizálási kiszolgálót. Nem rendelkezik a kiszolgáló csatlakoztatása egy tartományhoz. Ahhoz, hogy az összes erdőben, ha a kiszolgáló elhelyezheti a szegélyhálózaton (más néven DMZ, demilitarizált zóna és demilitarizált).

Az Azure AD Connect telepítővarázsló vonják össze a felhasználók számára jelennek meg több erdő több lehetőséget kínál. A célja, hogy a felhasználó Azure AD-ben csak egyszer jelenik meg. Nincsenek néhány gyakori topológiák konfigurálható a telepítési varázsló egyéni telepítési útvonalán. Az a **felhasználók egyedi azonosítása** lapon, válassza ki a megfelelő beállítást, amely a topológia jelöli. Az összevonás konfigurálta csak a felhasználók számára. Duplikált csoportok nem kell összevonni az alapértelmezett konfigurációval.

Közös topológiák ismerteti a szakaszok kapcsolatos [topológiák külön](#multiple-forests-separate-topologies), [háló teljes](#multiple-forests-full-mesh-with-optional-galsync), és [a fiók-erőforrás topológia](#multiple-forests-account-resource-forest).

Az alapértelmezett konfiguráció a Azure AD Connect szinkronizálási szolgáltatás azt feltételezi, hogy:

* Minden felhasználó csak egy engedélyezett fiókkal rendelkezik, és az erdőben, ahol ennek a fióknak-e a felhasználó hitelesítésére szolgál. Ez feltételezi, a Jelszókivonat-szinkronizálás, az átmenő hitelesítés és az összevonási. UserPrincipalName és sourceAnchor/immutableID határozza meg az erdő.
* Minden felhasználónak csak egy postaláda van.
* Az erdő, amelyen a postaládát, a felhasználó rendelkezik a legjobb adatok minőségének látható a az Exchange globális cím lista (GAL) attribútumok. Ha a felhasználó nem postaláda, bármely erdőben ezeket az attribútumértékeket közre használható.
* Ha rendelkezhetnek hivatkozott postafiókkal, akkor is fiók a bejelentkezéshez használt egy másik erdőben.

Ha a környezet nem egyezik meg az ilyen Előfeltevések, a következő dolgokat okozhat:

* Ha egynél több aktív fiók vagy egynél több postaládát, a szinkronizálási motor választja ki az egyik, és figyelmen kívül hagyja, a másik.
* Nincs más aktív fiókkal hivatkozott postafiókkal ne exportálja az Azure ad Szolgáltatásba. A felhasználói fiók nem szerepel minden csoport tagjaként. A DirSync hivatkozott postafiókkal mindig a normál postaláda jelzi. Ez a változás a szándékosan egy másik működés jobban a több erdőből forgatókönyvek támogatása céljából.

További részletek találhatók [az alapértelmezett konfiguráció ismertetése](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Több erdő, több szinkronizálási kiszolgálót egy Azure AD-bérlő számára
![Több erdő és a több szinkronizálási kiszolgálót nem támogatott topológia](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Egynél több, az Azure AD Connect szinkronizálási kiszolgálóhoz csatlakoztatott egyetlen Azure AD-bérlő nem támogatott. A kivétel: használatát egy [kiszolgáló átmeneti](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Több erdő, külön topológiák
![A felhasználók csak egyszer képviselő címtárak beállítás](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Több erdővel és külön topológiák ábrázolása](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Ebben a környezetben az összes helyi erdőben külön entitásokként kezelik. Felhasználó nem szerepel a más erdőkben. Minden olyan erdőben, a saját Exchange-szervezet rendelkezik, és nem GALSync az erdők között van. Ez a topológia lehet a helyzet egy összeolvadást vagy felvásárlást követően, vagy olyan szervezetekben, ahol minden részleg egymástól függetlenül működik. Erdők ugyanazon a szervezeten belül, az Azure ad-ben, és egy egységes globális Címlista együtt jelennek meg. A fenti kép minden erdőben az egyes objektumok képviselt egyszer a metaverzumban és a cél az Azure AD bérlő összesíteni.

### <a name="multiple-forests-match-users"></a>Több erdő: felhasználók kereséséhez
Ezek a forgatókönyvek közös, amely terjesztési és biztonsági csoportok felhasználók, partnerek és idegen rendszerbiztonsági (FSP) vegyesen tartalmazhatnak. A más erdőkben biztonsági csoportban lévő tagok képviselő FSP Active Directory tartományi szolgáltatások (AD DS) használnak. Minden FSP, megtörténik a valódi objektum az az Azure ad-ben.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Több erdő: opcionális GALSync ellátott teljes
![Az egyező, amikor a felhasználók identitásai több címtárban is léteznek a levél attribútum használatára vonatkozó beállítás](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Teljes rácsot alkotó topológia több erdő](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

A teljes rácsot alkotó topológia lehetővé teszi, hogy erdőkben található felhasználókat és erőforrásokat. Gyakran nincsenek kétirányú bizalmi kapcsolattal az erdők között.

Ha Exchange megtalálható-e egynél több erdő, valószínűleg (opcionális) egy helyszíni GALSync megoldás. Minden felhasználó van majd kapcsolattartóként az összes többi erdője. FIM 2010 vagy a MIM 2016 segítségével gyakran használják a GALSync. Az Azure AD Connect helyszíni GALSync nem használható.

Ebben a forgatókönyvben identitásobjektumok kapcsolódnak-e a levél attribútum használatával. Egy postaládát egy erdő rendelkező felhasználó csatlakozik, a többi erdőben lévő ügyfelekkel.

### <a name="multiple-forests-account-resource-forest"></a>Több erdő: fiók erőforráserdőben
![Amikor identitásai több címtárban is léteznek egyeztetéséhez a ObjectSID és msExchMasterAccountSID attribútum használatára vonatkozó beállítás](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Fiók-erőforrás szolgának több erdő](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Egy fiók-erőforrás szolgának, fel kell egy vagy több *fiók* erdőkbe tartozó aktív felhasználói fiókok. Akkor is rendelkezik, egy vagy több *erőforrás* erdők letiltott fiókok.

Ebben a forgatókönyvben egy (vagy több) erőforrás-erdő megbízhatónak tekinti minden fiókerdőben. Erőforráserdő jellemzően az Exchange és a Lync kiterjesztett Active Directory-sémát rendelkezik. Exchange és a Lync-szolgáltatások más megosztott szolgáltatások, valamint az erdőben találhatók. Felhasználók letiltott felhasználói fiók rendelkezik az erdőben, és a postaládát a fiókerdő kapcsolódik.

## <a name="office-365-and-topology-considerations"></a>Az Office 365 és topológiai szempontok
Néhány Office 365 számítási feladattal bizonyos korlátozások a támogatott topológiák rendelkezik:

| Számítási feladat | Korlátozások |
| --------- | --------- |
| Exchange Online | További információ a támogatott Exchange online hibrid topológiák: [hibrid telepítések esetén több Active Directory-erdő](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype Vállalati verzió | Több helyszíni erdővel használata esetén csak a fiók-erőforrás szolgának használata támogatott. További információkért lásd: [környezeti követelményei a Skype vállalati Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Ha nagyobb cég áll, akkor érdemes használni a [Office 365 PreferredDataLocation](active-directory-aadconnectsync-feature-preferreddatalocation.md) szolgáltatás. Ez lehetővé teszi, hogy adja meg, melyik adatközpontban régióban, a felhasználó erőforrások találhatók.

## <a name="staging-server"></a>Átmeneti kiszolgáló
![Átmeneti kiszolgálói topológia](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

A második kiszolgáló telepítése az Azure AD Connect támogatja *átmeneti módban*. Ebben a módban egy kiszolgáló olvassa be az adatokat az összes csatlakoztatott könyvtárak, de nem írható minden csatlakoztatott könyvtárak. A normál szinkronizálási ciklust használ, és ezért rendelkezik a identitásadatok frissített másolatát.

Egy olyan vészhelyzet esetén, ahol az elsődleges kiszolgáló meghibásodik, a feladatokat átveheti a fejlesztői kiszolgálóhoz. Ehhez az Azure AD Connect varázsló. A második kiszolgáló is található egy ugyanabban az adatközpontban, mert infrastruktúrával nem az elsődleges kiszolgáló van osztva. A konfigurációs változásokat, a második kiszolgáló az elsődleges kiszolgálón végzett manuálisan át kell másolnia.

Egy átmeneti kiszolgálón segítségével tesztelheti egy új egyéni konfigurációt, és milyen hatása, hogy az adatokról. Módosításokat, és módosítsa a konfigurációt. Ha már elégedett az új konfigurációt, az átmeneti kiszolgáló legyen az aktív kiszolgáló, és állítsa be a régi aktív kiszolgáló átmeneti módra.

Ez a módszer segítségével is az aktív szinkronizálási kiszolgálót le kell cserélni. Készítse elő az új kiszolgálón, és állítsa be az átmeneti környezetű üzemmód. Ellenőrizze, hogy jó állapotban, tiltsa le a átmeneti módban (aktiválás), és állítsa le a jelenleg aktív kiszolgálóhoz.

Lehetséges, hogy csak egy átmeneti kiszolgálón helyzet rendelkezik több biztonsági mentése különböző adatközpontokban.

## <a name="multiple-azure-ad-tenants"></a>Több Azure AD-bérlő
Javasoljuk, egyetlen bérlő szervezet Azure AD-ben.
Előtt is több Azure AD-bérlőt használni kívánja, tekintse meg a cikk [az adminisztrációs egységek felügyelete az Azure AD](../active-directory-administrative-units-management.md). Gyakori forgatókönyvek, ahol ugyanúgy használhatók egy egybérlős magában foglalja.

![Több erdő és a több bérlő topológia](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Az Azure AD Connect szinkronizálási kiszolgálót és az Azure AD-bérlő közötti 1:1 kapcsolat van. Minden Azure AD-bérlő van szüksége egy Azure AD Connect szinkronizálási kiszolgáló telepítés. Az Azure AD-bérlő példányban vannak elszigetelt úgy lett kialakítva. Ez azt jelenti, hogy az egy bérlő felhasználók nem látják a többi bérlő felhasználók számára. Ha azt szeretné, hogy ez az elkülönítés, ez a beállítás támogatott. Ellenkező esetben az egyetlen kell használni az Azure AD bérlő modell.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Az egyes objektumok csak egyszer az Azure AD-bérlő
![Egyetlen erdő esetén szűrt topológia](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Ebben a topológiában egy Azure AD Connect szinkronizálási kiszolgáló csatlakozik-e minden Azure AD-bérlő. Az Azure AD Connect szinkronizálási kiszolgálók be kell állítani a szűréshez, hogy mindegyik rendelkezik-e az való működésre objektumok egymást kölcsönösen kizáró csoportja. Például hatókörét megadhatja az egyes tartományokhoz vagy szervezeti egység minden kiszolgálón.

A DNS-tartomány regisztrálni lehet egyetlen Azure AD-bérlő. Az UPN-EK a felhasználók a helyszíni Active Directory-példányban is használnia kell a különálló névtereket. Például a fenti kép három különálló UPN-utótagot regisztrált a helyszíni Active Directory-példányban: contoso.com, fabrikam.com és wingtiptoys.com. Az egyes a helyszíni Active Directory tartományi felhasználók használja egy másik névtérből.

>[!NOTE]
>Globális cím lista szinkronizálási (GalSync) nem automatikusan történik, ebben a topológiában, és további egyéni MIM végrehajtása minden egyes bérlő legyen egy teljes globális cím lista (GAL) az Exchange Online és Skype vállalati online teszi szükségessé.


Ez a topológia rendelkezik-e a következő egyéb korlátozások a támogatott forgatókönyveket:

* Az Azure AD-bérlőt csak az egyik engedélyezheti az Exchange hibrid a helyszíni Active Directory-példánnyal.
* Windows 10-eszközöket csak egy Azure AD-bérlővel társítható.
* Egyszeri bejelentkezés (SSO) beállítás megadása jelszó kivonatát szinkronizálás és az áteresztő hitelesítés csak egy Azure AD-bérlő használható.

Objektumok egymást kölcsönösen kizáró számú követelmény is vonatkozik visszaírásához. Néhány visszaíró szolgáltatását nem támogatottak a topológia, mert azok feltételezik, hogy egyetlen helyszíni konfigurációt. Ezek a funkciók a következők:

* Csoport visszaírási alapértelmezett konfigurációval.
* Eszközök visszaírásához.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Az egyes objektumok többször az Azure AD-bérlő
![Egyetlen erdő esetén, és több bérlő nem támogatott topológia](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Egyetlen erdő esetén, és több összekötő nem támogatott topológia](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Ezek a feladatok nem támogatottak:

* A ugyanazon felhasználó számára több Azure AD-bérlő szinkronizálni.
* Olyan konfigurációs módosítást, hogy a felhasználók számára egy Azure AD-bérlő frissítésként jelenik meg egy másik Azure AD-bérlő partnerek ellenőrizze.
* Azure AD Connect szinkronizálási való csatlakozáshoz a több Azure AD-bérlő módosítása.

### <a name="galsync-by-using-writeback"></a>A visszaírási GALSync
![Több erdő és a több-címtárak esetén az Azure ad-val előtérbe GALSync nem támogatott topológia](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Több erdő, és több könyvtárak, GALSync előtérbe nem támogatott topológiát a helyszíni Active Directory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Az Azure AD-bérlő munkakönyvtárral úgy lett kialakítva. Ezek a feladatok nem támogatottak:

* Az Azure AD Connect szinkronizálási adatokat olvasni egy másik Azure AD-bérlő konfigurációjának módosítása
* Felhasználók exportálása partnereket, hogy egy másik a helyszíni Active Directory-példányban, az Azure AD Connect szinkronizálási szolgáltatás használatával.

### <a name="galsync-with-on-premises-sync-server"></a>A helyszíni szinkronizálási kiszolgálóval GALSync
![Több erdő és a több könyvtárak topológiában GALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

FIM 2010 vagy a MIM 2016 helyszíni segítségével (keresztül GALSync) felhasználók szinkronizálása az Exchange két szervezet között. A felhasználók az egyik szervezet külső felhasználók/contacts a másik szervezet jelennek meg. Ezek különböző a helyszíni Active Directory példányok majd szinkronizálhatja a saját Azure AD-bérlő.

## <a name="next-steps"></a>További lépések
Forgatókönyvek esetén az Azure AD Connect telepítésével kapcsolatban lásd: [az Azure AD Connect egyéni telepítési](active-directory-aadconnect-get-started-custom.md).

További információ a [az Azure AD Connect szinkronizálási szolgáltatás](active-directory-aadconnectsync-whatis.md) konfigurációs.

További információ [a helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
