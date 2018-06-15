---
title: VMware virtuális gép replikációs az Azure-ba (CSP program) több-bérlős támogatás |} Microsoft Docs
description: Ismerteti, hogyan lehet az Azure Site Recovery üzembe egy több-bérlős környezetben replikáció, feladatátvétel és helyreállítás a helyszíni VMware virtuális gépeket (VM) az Azure-bA a CSP programon keresztül vezénylését az Azure portál használatával
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 532dd399d2d5fcbab616744dd02f4a95078cbb1b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768465"
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Több-bérlős támogatás az Azure Site Recovery VMware virtuális gépek replikálása Azure-bA CSP keresztül

Az Azure Site Recovery támogatja a több-bérlős környezetekben bérlői előfizetések. Több vállalat kiszolgálása a bérlői előfizetések, amelyek létrehozása és kezelése a Microsoft Cloud Solution Provider (CSP) programon keresztül is támogatja. Ez a cikk részletezi az útmutató a telepítését és felügyeletét a több-bérlős VMware-Azure forgatókönyvek. Részleteket létrehozására, illetve bérlői előfizetések kezelése [több-bérlős CSP kezelése](site-recovery-manage-multi-tenancy-with-csp.md) .

Ez az útmutató fokozottan megrajzolja a VMware virtuális gépek replikálása Azure-bA meglévő dokumentációját. További információkért lásd: [replikálása VMware virtuális gépeket az Azure Site Recovery szolgáltatással](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Több-bérlős környezetekben
Három fő több-bérlős modell van:

* **A megosztott üzemeltetési szolgáltatásokat szolgáltató (HSP)**: A partner birtokolja a fizikai infrastruktúra, és a által használt megosztott erőforrások (vCenter, adatközpontok, fizikai tárhelyet, és így tovább) több bérlő virtuális gépek üzemeltetéséhez ugyanazon az infrastruktúrán. A partner biztosíthat a vész-helyreállítási felügyeleti felügyelt szolgáltatásként, vagy a bérlői önkiszolgáló megoldás vész-helyreállítási rendelkezhet.

* **Üzemeltetési szolgáltató dedikált**: A partner birtokolja a fizikai infrastruktúra, de a dedikált erőforrások (több Vcenter, fizikai datastores, és így tovább) használ a külön infrastruktúra minden egyes bérlői virtuális gépekre. A partner biztosíthat a vész-helyreállítási felügyeleti felügyelt szolgáltatásként, vagy a bérlő rendelkezhet önkiszolgáló megoldás.

* **Felügyelt szolgáltatások szolgáltató (MSP)**: az ügyfél birtokolja a fizikai infrastruktúra, amely a virtuális gépet futtat, és a partner biztosít a vész-helyreállítási engedélyezése és kezelése.

## <a name="shared-hosting-multi-tenant-guidance"></a>Több-bérlős megosztott üzemeltetési útmutató
Ez a szakasz a megosztott üzemeltetési forgatókönyv részletesen ismerteti. A többi két említett helyzet a megosztott üzemeltetési forgatókönyv részhalmaza, és ugyanez az elv használnak. A megosztott üzemeltetési útmutató végén a különbségeket ismerteti.

A több-bérlős forgatókönyvek alapvető követelmény a különböző bérlők elkülönítésére. Egy bérlő nem kell tudni figyelje meg, milyen más bérlőket birtokolt. Egy partner által felügyelt környezetben ez a követelmény nem ugyanolyan fontos, mert az egy önkiszolgáló környezetben, ahol azok kritikus. Ez az útmutató feltételezi, hogy a bérlők elszigetelésére szükséges.

Az architektúra az alábbi ábra jelenik meg:

![Egy vcenter megosztott HSP](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**A megosztott üzemeltetési a forgatókönyvet, és egy vCenter**

A fenti ábrán látható, minden egyes ügyfélnek van, külön felügyeleti kiszolgálóhoz. Ez a konfiguráció korlátozza a bérlő elérésére vonatkozó bérlői virtuális gépeket, és lehetővé teszi a bérlők elkülönítésének. A VMware virtuális gépek replikálásának forgatókönyv használ a konfigurációs kiszolgáló virtuális gépek felderítése, és az ügynökök telepítése fiókokat szeretne kezelni. Ugyanez az elv több-bérlős környezetekben körének korlátozása a hozzáférés-vezérléssel vCenter VM felderítési vonatkozik.

Az adatforgalom-elkülönítés követelmény szükségessé teszi, hogy az összes bizalmas infrastrukturális információk (például a hozzáférési hitelesítő adatok) továbbra is a bérlők számára nem nyilvános. Ezért azt javasoljuk, hogy a felügyeleti kiszolgáló összetevők a partner kizárólagos ellenőrzése alatt maradnak. A felügyeleti kiszolgáló-összetevők a következők:
* Konfigurációs kiszolgáló (CS)
* Folyamatkiszolgáló (PS)
* Fő célkiszolgáló (MT)

A kibővített PS is a partner ellenőrzés alatt áll.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>A több-bérlős forgatókönyvben minden CS két fiókot használja

- **vCenter hozzáférési fiók**: ezt a fiókot használják a bérlői virtuális gépek felderítése. (A következő szakaszban leírt) hozzárendelt vCenter hozzáférési engedéllyel rendelkezik. Hozzáférés véletlen kiszivárgásának elkerülése érdekében azt javasoljuk, hogy partnerek adja meg ezeket a hitelesítő adatokat, magukat a konfigurációs eszközt.

- **Virtuális gép hozzáférési fiók**: ezt a fiókot használják a mobilitási ügynök telepítése az automatikus ügyfélleküldéses keresztül a bérlői virtuális gépek. Akkor általában olyan tartományfiókot, amely a bérlő biztosít egy partnert, vagy egy, a partner közvetlenül kezelhet. Ha a bérlő nem szeretné, hogy közvetlenül a részletek megosztása a partner, akkor adja meg a hitelesítő adatokat korlátozott idejű hozzáférés keresztül a CS, vagy a partner segítséget mobilitási ügynökök manuális telepítése engedélyezhető.

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
>| Felügyeleti kiszolgáló | Azure_Site_Recovery | Minden összetevő (CS PS és fő Célkiszolgáló) kívül a CS gép tartoznak. |
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

## <a name="next-steps"></a>További lépések
[További](site-recovery-role-based-linked-access-control.md) vonatkozó szerepköralapú hozzáférés-vezérlést az Azure Site Recovery központi telepítések felügyeletéhez szükséges.

[Több vállalat kiszolgálása a kriptográfiai Szolgáltató kezelése](site-recovery-manage-multi-tenancy-with-csp.md)
