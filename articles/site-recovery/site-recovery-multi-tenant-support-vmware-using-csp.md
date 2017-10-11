---
title: "VMware virtuális gép replikációs az Azure-ba (CSP program) több-bérlős támogatás |} Microsoft Docs"
description: "Ismerteti, hogyan lehet az Azure Site Recovery üzembe egy több-bérlős környezetben replikáció, feladatátvétel és helyreállítás a helyszíni VMware virtuális gépeket (VM) az Azure-bA a CSP programon keresztül vezénylését az Azure portál használatával"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: manayar
ms.openlocfilehash: 97edbe67c25036dc1156f0f0ca5431a617d7a004
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Több-bérlős támogatás az Azure Site Recovery VMware virtuális gépek replikálása Azure-bA CSP keresztül

Az Azure Site Recovery támogatja a több-bérlős környezetekben bérlői előfizetések. Több vállalat kiszolgálása a bérlői előfizetések, amelyek létrehozása és kezelése a Microsoft Cloud Solution Provider (CSP) programon keresztül is támogatja. Ez a cikk részletezi az útmutató a telepítését és felügyeletét a több-bérlős VMware-Azure forgatókönyvek. Azt is magában foglalja, létrehozását és kezelését a bérlői előfizetések CSP keresztül.

Ez az útmutató fokozottan megrajzolja a VMware virtuális gépek replikálása Azure-bA meglévő dokumentációját. További információkért lásd: [replikálása VMware virtuális gépeket az Azure Site Recovery szolgáltatással](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Több-bérlős környezetekben
Három fő több-bérlős modell van:

* **A megosztott üzemeltetési szolgáltatásokat szolgáltató (HSP)**: A partner birtokolja a fizikai infrastruktúra, és több bérlő virtuális gépeket ugyanazon az infrastruktúrán használ megosztott erőforrások (vCenter, adatközpontok, fizikai tárhelyet, és így tovább). A partner biztosíthat a vész-helyreállítási felügyeleti felügyelt szolgáltatásként, vagy a bérlői önkiszolgáló megoldás vész-helyreállítási rendelkezhet.

* **Üzemeltetési szolgáltató dedikált**: A partner birtokolja a fizikai infrastruktúra, de a dedikált erőforrások (több Vcenter, fizikai datastores, és így tovább) használ a külön infrastruktúra minden egyes bérlői virtuális gépekre. A partner biztosíthat a vész-helyreállítási felügyeleti felügyelt szolgáltatásként, vagy a bérlő rendelkezhet önkiszolgáló megoldás.

* **Felügyelt szolgáltatások szolgáltató (MSP)**: az ügyfél birtokolja a fizikai infrastruktúra, amely a virtuális gépet futtat, és a partner biztosít a vész-helyreállítási engedélyezése és kezelése.

## <a name="shared-hosting-multi-tenant-guidance"></a>Több-bérlős megosztott üzemeltetési útmutató
Ez a szakasz a megosztott üzemeltetési forgatókönyv részletesen ismerteti. A többi két említett helyzet a megosztott üzemeltetési forgatókönyv részhalmaza, és ugyanez az elv használnak. A megosztott üzemeltetési útmutató végén a különbségeket ismerteti.

A több-bérlős forgatókönyvek alapvető követelmény a különböző bérlők elkülönítésére. Egy bérlő nem kell tudni figyelje meg, milyen más bérlőket birtokolt. Egy partner által felügyelt környezetben ez a követelmény nem ugyanolyan fontos, mert az egy önkiszolgáló környezetben, ahol azok kritikus. Ez az útmutató feltételezi, hogy a bérlők elszigetelésére szükséges.

Az architektúra az alábbi ábra jelenik meg:

![Egy vcenter megosztott HSP](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**A megosztott üzemeltetési a forgatókönyvet, és egy vCenter**

A fenti ábrán látható, minden egyes ügyfélnek van, külön felügyeleti kiszolgálóhoz. Ez a konfiguráció korlátozza a bérlő elérésére vonatkozó bérlői virtuális gépeket, és lehetővé teszi a bérlők elkülönítésének. A VMware virtuális gépek replikálásának forgatókönyv használ a konfigurációs kiszolgáló virtuális gépek felderítése, és az ügynökök telepítése fiókokat szeretne kezelni. Ugyanez az elv több-bérlős környezetben körének korlátozása a hozzáférés-vezérléssel vCenter VM felderítési kövesse azt.

Az adatforgalom-elkülönítés követelmény, hogy az összes bizalmas infrastrukturális információk (például a hozzáférési hitelesítő adatok) nyilvánosságra a bérlők számára teszi szükségessé. Ezért azt javasoljuk, hogy a felügyeleti kiszolgáló összetevők a partner kizárólagos ellenőrzése alatt maradnak. A felügyeleti kiszolgáló-összetevők a következők:
* Konfigurációs kiszolgáló (CS)
* Folyamatkiszolgáló (PS)
* Fő célkiszolgáló (MT) 

A kibővített PS is a partner ellenőrzés alatt áll.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>A több-bérlős forgatókönyvben minden CS két fiókot használja

- **vCenter hozzáférési fiók**: ezt a fiókot használják a bérlői virtuális gépek felderítése. (A következő szakaszban leírt) hozzárendelt vCenter hozzáférési engedéllyel rendelkezik. Hozzáférés véletlen kiszivárgásának elkerülése érdekében azt javasoljuk, hogy partnerek adja meg ezeket a hitelesítő adatokat, magukat a konfigurációs eszközt.

- **Virtuális gép hozzáférési fiók**: ezt a fiókot használják a mobilitási ügynök telepítése az automatikus ügyfélleküldéses keresztül a bérlői virtuális gépek. Akkor általában egy olyan tartományi fiók, hogy egy partnert biztosít a bérlők vagy, hogy azt is megteheti, a partner kezelhet közvetlenül. Ha a bérlő nem szeretné, hogy közvetlenül a részletek megosztása a partner, általa is engedélyezni kell a adja meg a korlátozott idejű hozzáférés hitelesítő adatait a CS, vagy a partner segítséget mobilitási ügynökök manuális telepítése.

### <a name="requirements-for-a-vcenter-access-account"></a>A vCenter-hozzáférési fiók követelményei

Ahogy azt korábban említettük, az előző szakaszban leírt, konfigurálnia kell a CS egy olyan fiókkal, amely azt különleges szerepkörrel rendelkezik. A szerepkör-hozzárendelés legyen a vCenter-hozzáférési fiók minden egyes vCenter objektumra vonatkozik, és nem jutott el a gyermekobjektumok. Ez a konfiguráció biztosítja a bérlők elszigetelésére, mert a hozzáférés propagálás más objektumok véletlen elérésében is megnyilvánulhat.

![A gyermekobjektum beállítást terjesztése](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

Ez esetben a felhasználói fiók és a datacenter objektumazonosítóra szerepkör hozzárendelése és továbbíthatja őket a gyermekobjektumok. Adja meg a fiók egy *nem lehet hozzáférni* szerepkör minden objektum (például a többi bérlő VM-EK), amely nem érhető el, hogy egy adott bérlő kell lennie. Ez a konfiguráció nehézkes, és véletlen hozzáférés-vezérléssel érheti el, mert minden új gyermekobjektum is hozzáférést kap, amely örökli a szülő. Ezért azt javasoljuk, hogy használja-e az első módszer.

A vCenter-fiókelérést eljárás a következőképpen történik:

1. Új szerepkör létrehozása a klónozásával az előre definiált *írásvédett* szerepkört, majd adjon egy kényelmes nevét (például Azure_Site_Recovery, ebben a példában látható módon).

2. Az alábbi engedélyek hozzárendelése a szerepkörhöz:

    * **Datastore**: szóköz, Tallózás datastore, alacsony szintű fájlműveletek, távolítsa el a fájlt, frissítés virtuálisgép-fájlok foglalása

    * **Hálózati**: hálózati hozzárendelése

    * **Erőforrás**: hozzárendelése VM erőforráskészlethez, ki van kapcsolva a virtuális gépek áttelepítése a virtuális gép kapcsolva áttelepítése

    * **Feladatok**: a feladat, a frissítési feladat létrehozása

    * **Virtuális gép**: 
        * Konfigurációs > minden
        * Interakció > válaszoljon a kérdést, eszköz kapcsolat, CD konfigurálása media, konfigurálás hajlékonylemez media, kapcsolja ki a bekapcsolás, VMware-eszközök telepítése
        * Készlet > létrehozása meglévő, létrehozhat új, regisztrálása, Unregister
        * Kiépítés > engedélyezése virtuális gép letölthető, a virtuális gép fájlok feltöltése
        * Pillanatkép-kezelő > pillanatképek eltávolítása

    ![A szerep szerkesztése párbeszédpanel](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. Hozzáférési szintek hozzárendelése vCenter (használt fiók a bérlő CS) a különböző objektumok, az alábbiak szerint:

>| Objektum | Szerepkör | Megjegyzések |
>| --- | --- | --- |
>| vCenter | Csak olvasható | Csak a különböző objektumok kezelésére szolgáló vCenter hozzáférés engedélyezése szükséges. Eltávolíthatja ezt az engedélyt, ha soha nem lesz a fiók a bérlők számára biztosított, vagy a vcenter felügyeleti műveleteket sem használja. |
>| Adatközpont | Azure_Site_Recovery |  |
>| Gazdagép és a gazdagépfürtön | Azure_Site_Recovery | Újra biztosítja, hogy hozzáférést a objektum szinten, hogy elérhető-állomások csak a bérlői virtuális gépek a feladatátvétel előtt és után feladat-visszavétel. |
>| Datastore és adattároló-fürt | Azure_Site_Recovery | Ugyanaz, mint megelőző. |
>| Network (Hálózat) | Azure_Site_Recovery |  |
>| Felügyeleti kiszolgáló | Azure_Site_Recovery | Magában foglalja a minden összetevő (CS PS és fő Célkiszolgáló) elérését is, ha a rendszer a CS gép kívül. |
>| Bérlői virtuális gépek | Azure_Site_Recovery | Biztosítja, hogy bármely új bérlői virtuális gépek egy adott bérlő is a hozzáférés, vagy nem is felderíthetők az Azure portálon keresztül. |

Befejeződött a vCenter felhasználóifiók-hozzáférés. Ebben a lépésben teljesíti a minimális engedélyek elvégzéséhez követelmény, hogy feladat-visszavétel műveletek. A hozzáférési engedélyeket is használható a meglévő szabályzatokat. Csak módosítsa a meglévő engedélyeket úgy, hogy a 2. lépést szerepkörengedélyek korábban részletes tartalmazza.

Vész-helyreállítási műveletek korlátozása csak a feladatátadást végez (Ez azt jelenti, hogy feladat-visszavétel képességek nélkül), kövesse az előző eljárást, egy kivétellel: ne a *Azure_Site_Recovery* a vCenter-hozzáférési fiókként, a szerepkör hozzárendelése csak egy *csak olvasható* szerepkör ezekhez a fiókokhoz. Ez az engedélycsoport lehetővé teszi a virtuális gép replikációs és feladatátvételi, és nem teszi lehetővé feladat-visszavételre. Minden más, az előző folyamatban marad van. Győződjön meg arról a bérlők elszigetelésére, és korlátozzák a virtuális gép felderítése, minden engedélyt objektumszinten csak rendelve, és nem jutott el a gyermek-objektumok.

## <a name="other-multi-tenant-environments"></a>Más több-bérlős környezetekben

Az előző szakaszban leírt egy több-bérlős környezet megosztott üzemeltetési megoldás beállítása. A két fő megoldásait futtató és a felügyelt vannak fenntartva. Ezek a megoldások architektúrája az alábbi szakaszokban ismertetett.

### <a name="dedicated-hosting-solution"></a>Dedikált üzemeltetési megoldás

Ahogy a következő ábrán is látható, a architekturális egy dedikált üzemeltetési megoldás különbség, hogy a bérlő infrastruktúra csak a bérlő van beállítva. Bérlők el választva egymástól külön Vcenter keresztül, mert a szolgáltató még mindig kövesse CSP megosztott üzemeltetéséhez biztosított, de nem kell aggódnia a bérlők elkülönítését. Kriptográfiai Szolgáltató beállítása változatlan marad.

![architektúra megosztott hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**Dedikált üzemeltetés több Vcenter**

### <a name="managed-service-solution"></a>Felügyelt megoldás

Ahogy a következő ábrán is látható, a architekturális felügyelt megoldás különbség, hogy minden bérlő infrastruktúrát is fizikailag nem más bérlők infrastruktúra. Ebben a forgatókönyvben általában akkor beszélünk, amikor a bérlő az infrastruktúra tulajdonosa, és kezelheti a vész-helyreállítási megoldás szolgáltató szeretne. Ebben az esetben bérlőkre fizikailag elkülönített különböző infrastruktúrák keresztül, mert a CSP lépések partner igényeinek megosztott üzemeltetéséhez biztosított tartalmaz, de nem kell aggódnia a bérlők elkülönítését. Kriptográfiai Szolgáltató kiépítés változatlan marad.

![architektúra megosztott hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**Felügyelt szolgáltatást a forgatókönyvet, és több Vcenter**

## <a name="csp-program-overview"></a>Kriptográfiai Szolgáltató program áttekintése
A [CSP program](https://partner.microsoft.com/en-US/cloud-solution-provider) elősegíti a megfelelőbb együtt szövegek által biztosított partnerek minden Microsoft felhőszolgáltatásai, beleértve az Office 365, a nagyvállalati mobilitási csomag és a Microsoft Azure. Kriptográfiai Szolgáltató partnereink az ügyfelek a végpontok közötti kapcsolatot saját és lesz az elsődleges kapcsolathoz kapcsolódási pontot. Partnerek telepítheti az Azure-előfizetések az ügyfelek és kombinálhatja a saját hozzáadott, testreszabott ajánlatokat az előfizetések.

Az Azure Site Recovery partnerek a teljes vész-helyreállítási megoldás felügyelheti az ügyfelek közvetlenül a CSP-hez. Vagy állítsa be a Site Recovery környezetekben, és lehetővé teszik az ügyfelek kezelése a saját vész-helyreállítási igényekre önkiszolgáló módon CSP használhatnak. Mindkét esetben a partnerek olyan a Site Recovery és az ügyfelek között. Partnerek az Ügyfélkapcsolat szolgáltatást, és a Site Recovery használata az ügyfelek kiszámlázni.

## <a name="create-and-manage-tenant-accounts"></a>Bérlői fiókok létrehozása és kezelése

### <a name="step-0-prerequisite-check"></a>0. lépés: Az előfeltétel-ellenőrzés

A virtuális gép is leírtak szerint a [Azure Site Recovery dokumentáció](site-recovery-vmware-to-azure.md). E előfeltételein kell a korábban említett hozzáférés-vezérlést helyen CSP bérlői tartalomablakban folytatása előtt. Az egyes bérlők számára hozzon létre egy külön felügyeleti kiszolgáló, amely képes kommunikálni a bérlői virtuális gépek és a partner által létrehozott vCenter. Csak a partner rendelkezik hozzáférési jogosultsággal ehhez a kiszolgálóhoz.

### <a name="step-1-create-a-tenant-account"></a>1. lépés: Bérlői fiók létrehozása

1. Keresztül [Microsoft Partner Center](https://partnercenter.microsoft.com/), jelentkezzen be a CSP-fiókjába. 
 
2. Az a **irányítópult** menü **ügyfelek**.

    ![A Microsoft Partner Center felhasználóinak hivatkozás](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

3. A megnyíló lapon kattintson a **Hozzáadás ügyfél** gombra.

    ![Az ügyfél hozzáadása gomb](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

4. Az a **új ügyfél** lapon adja meg a fiók adatai a bérlő számára, és kattintson a **tovább: előfizetések**.

    ![A fiók adatai lap](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

5. Az előfizetések kiválasztása lapon válassza ki a **Microsoft Azure** jelölőnégyzetet. Most, vagy más bármikor másik előfizetés is hozzáadhat.

    ![A Microsoft Azure-előfizetés jelölőnégyzetet](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

6. Az a **felülvizsgálati** lapon erősítse meg a bérlői adatokat, és kattintson a **Submit**.

    ![A nyomtatási kép](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)  

    Miután létrehozta a bérlői fiókot, a jóváhagyó lapon jelenik meg, az alapértelmezett fiókot és egy jelszót, hogy az előfizetés részleteinek megjelenítése. 

7. Mentse az adatokat, és módosítsa a jelszót később keresztül az Azure portál bejelentkezési lapon szükség szerint.  
 
    Ezek az információk megosztása a bérlő, mert a, vagy hozzon létre, és egy külön fiókot megosztani, ha szükséges.

### <a name="step-2-access-the-tenant-account"></a>2. lépés: A bérlői fiók eléréséhez.

Érheti el a bérlő előfizetés a Microsoft Partner Center irányítópultján, lásd: "1. lépés: bérlői fiók létrehozása." 

1. Lépjen a **ügyfelek** lapon, majd a bérlői fiók nevét.

2. Az a **előfizetések** lap a bérlői fiók figyelheti a meglévő fiók-előfizetések, és adja hozzá legalább egy előfizetésre, szükség szerint. Válassza ki a bérlő vész-helyreállítási műveleteinek a felügyeletét, **összes erőforrást (Azure-portál)**.

    ![Az összes erőforrás-hivatkozás](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)  
    
    Kattintson a **összes erőforrás** hozzáférést biztosít a bérlő Azure-előfizetések. Hozzáférés az Azure Active Directory tetején kattintva ellenőrizheti az Azure-portálon sarkában.

    ![Az Azure Active Directory-hivatkozás](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

Ezután hajtsa végre az összes webhely-helyreállítási művelet az Azure portálon keresztül a bérlői és vész-helyreállítási műveleteinek a felügyeletét. A bérlői előfizetéshez eléréséhez CSP keresztül felügyelt vész-helyreállítási kövesse a korábban ismertetett folyamatot.

### <a name="step-3-deploy-resources-to-the-tenant-subscription"></a>3. lépés: Telepítse a bérlői előfizetéshez erőforrásokat
1. Az Azure portálon hozzon létre egy erőforráscsoportot, és a Recovery Services-tárolónak a szokásos folyamatonként telepíteni. 
 
2. Töltse le a tárolóregisztrációs kulcsot.

3. A Tanúsítványszolgáltatások regisztrálja a bérlő a tárolóbeli regisztrációs kulcs használatával.

4. Adja meg a hitelesítő adatokat a két hozzáférési fiókjaiként: vCenter hálózatelérési fiók és a virtuális gép fér hozzá a fiókjához.

    ![Kezelői konfigurációs kiszolgáló fiókok](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>4. lépés: Regisztráció a Site Recovery infrastruktúrát a Recovery Services-tároló
1. Az Azure portálon, a tároló, amelyet korábban hozott létre a regisztrálja a vCenter-kiszolgáló a CS regisztrált a "3. lépés: telepítse a bérlői előfizetéshez erőforrásokat." A vCenter hálózatelérési fiókot használja erre a célra.
2. Az "Infrastruktúra előkészítése" folyamat befejezéséhez a Site Recovery a szokásos folyamatonként.
3. A virtuális gépek replikálása most már készen áll. Győződjön meg arról, hogy csak a bérlői virtuális gépek jelennek meg a **válassza ki a virtuális gépek** részen a **replikálása** lehetőséget.

    ![A Select virtuális gépek panelje bérlői virtuális gépek listája](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-the-subscription"></a>5. lépés: A bérlő hozzáférés hozzárendelése az előfizetéshez

Az adatok önkiszolgáló helyreállítását, adja meg a bérlő fiók adatait, a 6. lépésében az "1. lépés: bérlői fiók létrehozása" szakasz. A művelet végrehajtása után a fiókpartner állít be a vész-helyreállítási infrastruktúra. Hogy a vész-helyreállítási típus kezelt vagy az önkiszolgáló, partnerek bérlői előfizetések kell férnie a CSP-portálon keresztül. Azok a partner által birtokolt tároló beállítása, és regisztrálja az infrastruktúrát, hogy a bérlői előfizetések.

Partnerek is hozzáadhat egy új felhasználót a CSP-portálon keresztül a bérlői előfizetéshez a következő módon:

1. Nyissa meg a bérlői CSP előfizetés oldalát, és válassza a **felhasználók és licencek** lehetőséget.

    ![A bérlő CSP-előfizetés lapján](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    Most létrehozhat egy új felhasználót, írja be a vonatkozó adatokat, majd válassza az engedélyeket, vagy fel kell tölteni egy CSV-fájlban szereplő felhasználók listáját.

2. Miután létrehozott egy új, lépjen vissza az Azure-portálon, majd a felhasználó a **előfizetés** panelen válassza ki a megfelelő előfizetést.

3. A megjelenő panelen válassza ki a **hozzáférés-vezérlés (IAM)**, és kattintson a **Hozzáadás** hozzáadni egy felhasználót a megfelelő hozzáférési szinttel.      
    A felhasználók a CSP-portálon létrehozott automatikusan megjelennek a olyan hozzáférési szint kattintás után megjelenő panelen.

    ![Felhasználó hozzáadása](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    A legtöbb felügyeleti műveleteket a *közreműködő* szerepkör is elegendő. Ez a hozzáférési szint rendelkező felhasználók is mindent meg azzal a különbséggel hozzáférési szint módosítása előfizetés (amelynek *tulajdonos*-szintű hozzáféréssel kell). A hozzáférési szintek szükség szerint is finomhangolására.
