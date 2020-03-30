---
title: 'Azure AD Connect: Támogatott topológiák | Microsoft dokumentumok'
description: Ez a témakör az Azure AD Connect támogatott és nem támogatott topológiáit részletezi
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
ms.openlocfilehash: 9618e02f54fbb2a3b92771761c5fcf700d126b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253831"
---
# <a name="topologies-for-azure-ad-connect"></a>Azure AD Connect-topológiák
Ez a cikk ismerteti a különböző helyszíni és az Azure Active Directory (Azure AD) topológiák, amelyek az Azure AD Connect szinkronizálása, mint a kulcs integrációs megoldás. Ez a cikk a támogatott és a nem támogatott konfigurációkat is tartalmazza.


Itt a legenda a képek a cikkben:

| Leírás | Szimbólum |
| --- | --- |
| Helyszíni Active Directory-erdő |![Helyszíni Active Directory-erdő](./media/plan-connect-topologies/LegendAD1.png) |
| Helyszíni Active Directory szűrt importálással |![Active Directory szűrt importálással](./media/plan-connect-topologies/LegendAD2.png) |
| Azure AD Connect szinkronizálási kiszolgáló |![Azure AD Connect szinkronizálási kiszolgáló](./media/plan-connect-topologies/LegendSync1.png) |
| Az Azure AD Connect szinkronizálási kiszolgálójának "átmeneti módja" |![Az Azure AD Connect szinkronizálási kiszolgálójának "átmeneti módja"](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync forefront Identity Manager (FIM) 2010 vagy A Microsoft Identity Manager (MIM) 2016 |![GALSync a FIM 2010 vagy MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Azure AD Connect szinkronizálási kiszolgáló, részletes |![Azure AD Connect szinkronizálási kiszolgáló, részletes](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Nem támogatott forgatókönyv |![Nem támogatott forgatókönyv](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> A Microsoft nem támogatja az Azure AD Connect szinkronizálásának módosítását vagy üzemeltetését a hivatalosan dokumentált konfigurációkon vagy műveleteken kívül. Ezen konfigurációk vagy műveletek bármelyike az Azure AD Connect szinkronizálásának inkonzisztens vagy nem támogatott állapotát eredményezheti. Ennek eredményeképpen a Microsoft nem tud technikai támogatást nyújtani az ilyen telepítésekhez.


## <a name="single-forest-single-azure-ad-tenant"></a>Egyetlen erdő, egyetlen Azure AD-bérlő
![Egyetlen erdő és egyetlen bérlő topológiája](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

A leggyakoribb topológia egy helyszíni erdő, egy vagy több tartománnyal, és egy Azure AD-bérlővel. Az Azure AD-hitelesítéshez jelszókivonat-szinkronizálást használ. Az Azure AD Connect gyors telepítése csak ezt a topológiát támogatja.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Egyetlen erdő, több szinkronizálási kiszolgáló egy Azure AD-bérlővel
![Nem támogatott, szűrt topológia egyetlen erdőhöz](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Az, hogy több Azure AD Connect-szinkronizálási kiszolgáló csatlakozik ugyanahhoz az Azure AD-bérlőhöz, nem támogatott, kivéve egy [átmeneti kiszolgálót.](#staging-server) Ez nem támogatott, még akkor is, ha ezek a kiszolgálók úgy vannak beállítva, hogy szinkronizáljanak egy kölcsönösen kizáró objektumkészlettel. Ezt a topológiát akkor vehette figyelembe, ha nem érhető el az erdő összes tartománya egyetlen kiszolgálóról, vagy ha a terhelést több kiszolgáló között szeretné elosztani.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Több erdő, egyetlen Azure AD-bérlő
![Topológia több erdőés egyetlen bérlő számára](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Számos szervezet több helyszíni Active Directory-erdővel rendelkezik. Több oka lehet annak, hogy egynél több helyszíni Active Directory-erdő vel rendelkezik. Tipikus példák a fiókerőforrás-erdőkkel rendelkező tervek, valamint az egyesülés vagy felvásárlás eredménye.

Ha több erdővel rendelkezik, az összes erdőnek elérhetőnek kell lennie egyetlen Azure AD Connect szinkronizálási kiszolgálón keresztül. A kiszolgálót tartományhoz kell csatlakoztatni. Ha az összes erdő eléréséhez szükséges, a kiszolgálót szegélyhálózatba helyezheti (más néven DMZ, demilitarizált zóna és szűrt alhálózat).

Az Azure AD Connect telepítővarázsló számos lehetőséget kínál a több erdőben képviselt felhasználók konszolidálására. A cél az, hogy a felhasználó csak egyszer jelenik meg az Azure AD-ben. Vannak olyan gyakori topológiák, amelyeket a telepítővarázsló egyéni telepítési útvonalán konfigurálhat. A **Felhasználók egyedi azonosítása** lapon válassza ki a topológiát jelölő megfelelő beállítást. A konszolidáció csak a felhasználók számára van konfigurálva. Az ismétlődő csoportok nem lesznek konszolidálva az alapértelmezett konfigurációval.

A közös topológiákat a különálló topológiákról, [a teljes hálóról](#multiple-forests-full-mesh-with-optional-galsync)és [a fiók-erőforrás topológiáról](#multiple-forests-account-resource-forest)szóló szakaszok ismertetik.

Az Azure AD Connect szinkronizálásalapértelmezett konfigurációja a következőket feltételezi:

* Minden felhasználónak csak egy engedélyezett fiókja van, és a fiók helye szerinti erdő a felhasználó hitelesítésére szolgál. Ez a feltételezés a jelszókivonat-szinkronizálásra, az átmenő hitelesítésre és az összevonásra szolgál. A UserPrincipalName és a sourceAnchor/immutableID ebből az erdőből származik.
* Minden felhasználónak csak egy postaládája van.
* Az az erdő, amely a felhasználó postaládáját üzemelteti, a legjobb adatminőséggel rendelkezik az Exchange globális címlistájában (GAL) látható attribútumok hoz. Ha nincs postaláda a felhasználó számára, bármelyik erdő használható az attribútumértékek hozzájárulásához.
* Ha összekapcsolt postaládával rendelkezik, akkor egy másik erdőben is van fiók, amelyet a bejelentkezéshez használnak.

Ha a környezet nem felel meg ezeknek a feltételezéseknek, a következő dolgok történnek:

* Ha egynél több aktív fiókkal vagy egynél több postaládával rendelkezik, a szinkronizálási motor kiválasztja az egyiket, és figyelmen kívül hagyja a másikat.
* Más aktív fiókkal nem rendelkező csatolt postaláda nem lesz exportálva az Azure AD-be. A felhasználói fiók egyik csoportban sem szerepel tagként. A DirSync csatolt postaládája mindig normál postaládaként jelenik meg. Ez a módosítás szándékosan egy másik viselkedés, hogy jobban támogassa a több erdős forgatókönyvek.

További részleteket [az Alapértelmezett konfiguráció ismertetése](concept-azure-ad-connect-sync-default-configuration.md)című témakörben talál.

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Több erdő, több szinkronizálási kiszolgáló egy Azure AD-bérlővel
![Nem támogatott topológia több erdőhez és több szinkronizálási kiszolgálóhoz](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Egynél több Azure AD Connect szinkronizálási kiszolgáló egyetlen Azure AD-bérlőhöz csatlakoztatott nem támogatott. Kivételt képez az [átmeneti kiszolgáló](#staging-server)használata.

Ez a topológia eltér az alábbi, hogy **több szinkronizálási kiszolgálók** egyetlen Azure AD-bérlőhöz csatlakoztatott nem támogatott.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Több erdő, egyszinkronizálási kiszolgáló, a felhasználók csak egy könyvtárban jelennek meg
![Lehetőség arra, hogy a felhasználókat csak egyszer képviselje az összes könyvtárban](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Több erdő ábrázolása és különálló topológiák ábrázolása](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

Ebben a környezetben az összes helyszíni erdő külön entitásként lesz kezelve. Más erdőben nincs felhasználó. Minden erdő rendelkezik saját Exchange-szervezettel, és nincs GALSync az erdők között. Ez a topológia lehet a helyzet az egyesülés/felvásárlás után, vagy egy olyan szervezetnél, ahol minden részleg egymástól függetlenül működik. Ezek az erdők ugyanabban a szervezetben az Azure AD és megjelennek egy egységes GAL. Az előző képen minden egyes erdő minden objektum egyszer jelenik meg a metaverzumban, és a cél Azure AD-bérlőben összesítve jelenik meg.

### <a name="multiple-forests-match-users"></a>Több erdő: egyezés a felhasználókkal
Az összes ilyen forgatókönyv közös, hogy a terjesztési és biztonsági csoportok felhasználók, kapcsolattartók és külföldi rendszerbiztonsági tagok (FSP-k) vegyesen tartalmazhatnak. Az Active Directory tartományi szolgáltatások (AD DS) az Active Directory tartományi szolgáltatásokban használatosak a biztonsági csoport más erdőinek tagjainak ábrázolására. Az összes FSP az Azure AD-ben a valódi objektumra van feloldva.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Több erdő: teljes háló opcionális GALSync-el
![A mail attribútum egyeztetésre való használatának lehetősége, ha több könyvtárban léteznek felhasználói identitások](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Teljes hálótopológia több erdőhez](./media/plan-connect-topologies/MultiForestFullMesh.png)

A teljes hálótopológiája lehetővé teszi, hogy a felhasználók és az erőforrások bármely erdőben elhelyezkedjenek. Általában kétirányú bizalmi kapcsolatok vannak az erdők között.

Ha az Exchange egynél több erdőben van jelen, előfordulhat, hogy (tetszés szerint) van egy helyszíni GALSync-megoldás. Ezután minden felhasználó kapcsolattartóként jelenik meg az összes többi erdőben. A GALSync-et általában a FIM 2010 vagy a MIM 2016 hajtja végre. Az Azure AD Connect nem használható a helyszíni GALSync.

Ebben az esetben az identitásobjektumok a levelezési attribútumon keresztül vannak összekötve. Az egyik erdőben postaládával rendelkező felhasználó csatlakozik a többi erdőben lévő kapcsolattartókhoz.

### <a name="multiple-forests-account-resource-forest"></a>Több erdő: fiókerőforrás-erdő
![Az ObjectSID és az msExchMasterAccountSID attribútumok egyeztetésére szolgáló beállítás, ha több könyvtárban léteznek identitások](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Fiókerőforrás-erdőtopológiája több erdőhöz](./media/plan-connect-topologies/MultiForestAccountResource.png)

Egy fiók-erőforrás erdőtopológiában egy vagy több *fiókerdő* aktív felhasználói fiókkal rendelkezik. Egy vagy több *letiltott* fiókkal rendelkező erőforráserdővel is rendelkezik.

Ebben az esetben egy (vagy több) erőforráserdő bízik az összes fiókerdőben. Az erőforráserdő általában kiterjesztett Active Directory-sémával rendelkezik az Exchange és a Lync alkalmazással. Az összes Exchange- és Lync-szolgáltatás, valamint más megosztott szolgáltatások ezen az erdőben találhatók. A felhasználók letiltott felhasználói fiókkal rendelkeznek ebben az erdőben, és a postaláda a fiókerdőhöz van csatolva.

## <a name="office-365-and-topology-considerations"></a>Az Office 365-tel és a topológiával kapcsolatos szempontok
Egyes Office 365-ös számítási feladatok bizonyos korlátozásokkal rendelkeznek a támogatott topológiákra vonatkozóan:

| Számítási feladat | Korlátozások |
| --------- | --------- |
| Exchange Online | Az Exchange Online által támogatott hibrid topológiákról a [több Active Directory-erdővel rendelkező hibrid telepítések](https://technet.microsoft.com/library/jj873754.aspx)című témakörben olvashat bővebben. |
| Skype Vállalati verzió | Ha több helyszíni erdőt használ, csak a fiók-erőforrás erdő topológiája támogatott. További információt a [Skype Vállalati kiszolgáló 2015 környezetvédelmi követelményei című](https://technet.microsoft.com/library/dn933910.aspx)témakörben talál. |

Ha Ön nagyobb szervezet, akkor érdemes megfontolnia az [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) szolgáltatás használatát. Lehetővé teszi annak meghatározását, hogy a felhasználó erőforrásai melyik adatközpont-régióban találhatók.

## <a name="staging-server"></a>Átmeneti kiszolgáló
![Átmeneti kiszolgáló egy topológiában](./media/plan-connect-topologies/MultiForestStaging.png)

Az Azure AD Connect támogatja egy második kiszolgáló *átmeneti módban*történő telepítését. Ebben a módban a kiszolgáló beolvassa az adatokat az összes csatlakoztatott könyvtárból, de nem ír semmit a csatlakoztatott könyvtárakba. A normál szinkronizálási ciklust használja, és ezért rendelkezik az identitásadatok frissített másolatával.

Olyan katasztrófa esetén, amikor az elsődleges kiszolgáló meghibásodik, átveheti a feladatát az átmeneti kiszolgálónak. Ezt az Azure AD Connect varázslóban teszi meg. Ez a második kiszolgáló egy másik adatközpontban is elhelyezhető, mert nincs infrastruktúra megosztva az elsődleges kiszolgálóval. Az elsődleges kiszolgálón végrehajtott konfigurációs módosításokat manuálisan kell átmásolnia a második kiszolgálóra.

Az átmeneti kiszolgáló segítségével tesztelheti az új egyéni konfigurációt, valamint azt az adatokra gyakorolt hatásával. Megtekintheti a módosításokelőnézetét, és módosíthatja a konfigurációt. Ha elégedett az új konfigurációval, beállíthatja az átmeneti kiszolgálót aktív kiszolgálóvá, és a régi aktív kiszolgálót átmeneti módba állíthatja.

Ezzel a módszerrel az aktív szinkronizálási kiszolgálót is lecserélheti. Készítse elő az új kiszolgálót, és állítsa átmeneti módba. Győződjön meg arról, hogy jó állapotban van, tiltsa le az átmeneti módot (aktívvá téve), és állítsa le az éppen aktív kiszolgálót.

Lehetséges, hogy egynél több átmeneti kiszolgáló, ha azt szeretné, hogy több biztonsági mentések különböző adatközpontokban.

## <a name="multiple-azure-ad-tenants"></a>Több Azure AD-bérlő
Azt javasoljuk, hogy egy egyetlen bérlő az Azure AD egy szervezet számára.
Mielőtt több Azure AD-bérlő használatát tervezne használni, tekintse meg a Felügyeleti egységek kezelése az [Azure AD-ben](../users-groups-roles/directory-administrative-units.md)című cikket. Ez magában foglalja a gyakori forgatókönyvek, ahol egyetlen bérlő használható.

![Topológia több erdő höz és több bérlőhöz](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Az Azure AD Connect szinkronizálási kiszolgálója és az Azure AD-bérlő között 1:1-es kapcsolat van. Minden Azure AD-bérlőhöz egy Azure AD Connect-szinkronizálási kiszolgáló telepítésére van szükség. Az Azure AD-bérlői példányok kialakítása szerint vannak elkülönítve. Ez azt, hogy az egyik bérlő felhasználói nem láthatják a másik bérlő felhasználóit. Ha ezt a szétválasztást szeretné, ez egy támogatott konfiguráció. Ellenkező esetben az egyetlen Azure AD-bérlői modellt kell használnia.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Minden objektum csak egyszer egy Azure AD-bérlőben
![Szűrt topológia egyetlen erdőhöz](./media/plan-connect-topologies/SingleForestFiltered.png)

Ebben a topológiában egy Azure AD Connect szinkronizálási kiszolgáló csatlakozik az egyes Azure AD-bérlők. Az Azure AD Connect szinkronizálási kiszolgálókat úgy kell konfigurálni a szűréshez, hogy mindegyiknek van egy kölcsönösen kizáró objektumkészlete, amelyen üzemeltetni kell. Az egyes kiszolgálókat például egy adott tartományra vagy szervezeti egységre is kihasználhatja.

A DNS-tartomány csak egyetlen Azure AD-bérlőben regisztrálható. A helyszíni Active Directory-példány felhasználóinak upns-nak is külön névtereket kell használniuk. Az előző képen például három különálló UPN-utótag van regisztrálva a helyszíni Active Directory-példányban: contoso.com, fabrikam.com és wingtiptoys.com. Az egyes helyszíni Active Directory-tartományok felhasználói más névteret használnak.

>[!NOTE]
>A globális címlista-szinkronizálás (GalSync) nem történik meg automatikusan ebben a topológiában, és további egyéni MIM-implementációra van szükség annak biztosításához, hogy minden bérlő rendelkezik teljes globális címlistával (GAL) az Exchange Online-ban és a Skype Vállalati online verzióban.


Ez a topológia a következő korlátozásokkal rendelkezik az egyébként támogatott forgatókönyvekre vonatkozóan:

* Az Azure AD-bérlők közül csak egy engedélyezheti az Exchange hibridet a helyszíni Active Directory-példánysal.
* A Windows 10-es eszközök csak egy Azure AD-bérlőhöz társíthatók.
* Az egyszeri bejelentkezési (SSO) beállítás jelszókivonat-szinkronizáláshoz és átadó hitelesítéshez csak egy Azure AD-bérlővel használható.

A kölcsönösen kizáró objektumkészlet követelménye a visszaírásra is vonatkozik. Egyes visszaírási funkciók nem támogatottak ezzel a topológiával, mert egyetlen helyszíni konfigurációt feltételeznek. Ezek a szolgáltatások a következők:

* Csoportvisszaírás alapértelmezett konfigurációval.
* Eszköz visszaírása.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Minden objektum többször egy Azure AD-bérlőben
![Nem támogatott topológia egyetlen erdőhöz és több bérlőhöz](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Nem támogatott topológia egyetlen erdőhöz és több összekötőhöz](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Ezek a feladatok nem támogatottak:

* Szinkronizálja ugyanazt a felhasználót több Azure AD-bérlővel.
* Konfigurálási módosítást kell lehetővé tenni, hogy az egyik Azure AD-bérlő felhasználói kapcsolattartóként jelenjenek meg egy másik Azure AD-bérlőben.
* Módosítsa az Azure AD Connect-szinkronizálást, hogy több Azure AD-bérlőhöz csatlakozzon.

### <a name="galsync-by-using-writeback"></a>GALSync a visszaírás használatával
![Nem támogatott topológia több erdőhez és több könyvtárhoz, a GALSync az Azure AD-re összpontosítva](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Nem támogatott topológia több erdőhez és több könyvtárhoz, a GALSync a helyszíni Active Directoryra összpontosítva](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Az Azure AD-bérlők kialakítás szerint vannak elkülönítve. Ezek a feladatok nem támogatottak:

* Módosítsa az Azure AD Connect-szinkronizálás konfigurációját, hogy adatokat olvasson egy másik Azure AD-bérlőről.
* A felhasználókat kapcsolattartóként exportálhatja egy másik helyszíni Active Directory-példányba az Azure AD Connect szinkronizáláshasználatával.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync helyszíni szinkronizálási kiszolgálóval
![GALSync több erdő és több könyvtár topológiájában](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

A FIM 2010 vagy a MIM 2016 helyszíni használatával szinkronizálhatja a felhasználókat (a GALSyncen keresztül) két Exchange-szervezet között. Az egyik szervezet felhasználói a másik szervezetben külföldi felhasználóként/kapcsolattartóként jelennek meg. Ezek a különböző helyszíni Active Directory-példányok ezután szinkronizálhatók a saját Azure AD-bérlőkkel.

## <a name="next-steps"></a>További lépések
Az Azure AD Connect ilyen forgatókönyvekhez való telepítéséről az [Azure AD Connect egyéni telepítése](how-to-connect-install-custom.md)című témakörben olvashat.

További információ az [Azure AD Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációjáról.

További információ [a helyszíni identitások azure-active Directoryval való integrálásáról.](whatis-hybrid-identity.md)
