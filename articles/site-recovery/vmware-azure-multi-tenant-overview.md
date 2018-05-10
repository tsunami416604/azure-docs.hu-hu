---
title: VMware virtuális gép replikációs az Azure (CSP) Azure Site Recovery segítségével több-bérlős támogatás áttekintése |} Microsoft Docs
description: Áttekintést nyújt az Azure Site Recovery támogatási bérlői előfizetések több-bérlős környezetben, a CSP programon keresztül.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: manayar
ms.openlocfilehash: 285086964365339291e9027a7fe8e5ee0083e13b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>Több-bérlős támogatás az Azure-bA CSP VMware-replikáció áttekintése

[Az Azure Site Recovery](site-recovery-overview.md) bérlői előfizetések támogatja a több-bérlős környezetekben. Több vállalat kiszolgálása a bérlői előfizetések, amelyek létrehozása és kezelése a Microsoft Cloud Solution Provider (CSP) programon keresztül is támogatja.

Ez a cikk telepítését és felügyeletét a több-bérlős VMware-ből az Azure replikációs áttekintést nyújt.

## <a name="multi-tenant-environments"></a>Több-bérlős környezetekben

Három fő több-bérlős modell van:

* **A megosztott üzemeltetési szolgáltatásokat szolgáltató (HSP)**: A partner birtokolja a fizikai infrastruktúra, és a által használt megosztott erőforrások (vCenter, adatközpontok, fizikai tárhelyet, és így tovább) több bérlői virtuális gépek üzemeltetéséhez ugyanazon az infrastruktúrán. A partner biztosíthat a vész-helyreállítási felügyeleti felügyelt szolgáltatásként, vagy a bérlői önkiszolgáló megoldás vész-helyreállítási rendelkezhet.

* **Üzemeltetési szolgáltató dedikált**: A partner birtokolja a fizikai infrastruktúra, de a dedikált erőforrások (több Vcenter, fizikai datastores, és így tovább) használ a külön infrastruktúra minden egyes bérlői virtuális gépekre. A partner biztosíthat a vész-helyreállítási felügyeleti felügyelt szolgáltatásként, vagy a bérlő rendelkezhet önkiszolgáló megoldás.

* **Felügyelt szolgáltatások szolgáltató (MSP)**: az ügyfél birtokolja a fizikai infrastruktúra, amely a virtuális gépet futtat, és a partner biztosít a vész-helyreállítási engedélyezése és kezelése.

## <a name="shared-hosting-services-provider-hsp"></a>A megosztott üzemeltetési szolgáltatásokat szolgáltató (HSP)

A többi két említett helyzet a megosztott üzemeltetési forgatókönyv részhalmaza, és ugyanez az elv használnak. A megosztott üzemeltetési útmutató végén a különbségeket ismerteti.

A több-bérlős forgatókönyvek alapvető követelmény, hogy a bérlők elkülönített kell lennie. Egy bérlő nem kell tudni figyelje meg, milyen más bérlőket birtokolt. Egy partner által felügyelt környezetben ez a követelmény nem ugyanolyan fontos, mert az egy önkiszolgáló környezetben, ahol azok kritikus. Ez a cikk feltételezi, hogy a bérlők elszigetelésére szükséges.

Az architektúra a következő ábrán látható.

![Egy vcenter megosztott HSP](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Egy vCenter-kiszolgáló megosztott-tároló**

A diagramon minden ügyfélnek van, külön felügyeleti kiszolgálóhoz. Ez a konfiguráció korlátozza a bérlő elérésére vonatkozó bérlői virtuális gépeket, és lehetővé teszi a bérlők elkülönítésének. VMware virtuális gép replikációs virtuális gépeket, valamint az ügynökök telepítéséhez használja a konfigurációs kiszolgáló. Az azonos alapelvek több-bérlős környezetekben, virtuális gép felderítési vCenter hozzáférés-vezérlés használatával történő hozzáadásával.

Az elkülönítési követelménynek azt jelenti, hogy az összes bizalmas infrastrukturális információk (például a hozzáférési hitelesítő adatok) továbbra is a bérlők számára nem nyilvános. Ezért azt javasoljuk, hogy a felügyeleti kiszolgáló összetevők a partner kizárólagos ellenőrzése alatt maradnak. A felügyeleti kiszolgáló-összetevők a következők:

* Konfigurációs kiszolgáló
* Folyamatkiszolgáló
* Fő célkiszolgáló

Egy különálló kibővített folyamatkiszolgálót is a partner ellenőrzés alatt áll.

## <a name="configuration-server-accounts"></a>Konfigurációs kiszolgáló fiókok

Minden konfigurációs kiszolgáló, a több-bérlős forgatókönyvben két fiókot használja:

- **vCenter hozzáférési fiók**: Ez a fiók bérlői virtuális gépek felderítésére szolgál. VCenter hozzáférési engedélyek rendelve van. Hozzáférés kiszivárgásának elkerülése érdekében, azt javasoljuk, hogy partnerek adja meg ezeket a hitelesítő adatokat, magukat a kiszolgálókonfigurációs eszköz.

- **Virtuális gép hozzáférési fiók**: ezt a fiókot használja a mobilitási szolgáltatás ügynöke a bérlői virtuális gépek, az automatikus leküldéses telepítése. Akkor általában olyan tartományfiókot, amely egy partner biztosít a bérlők vagy egy fiókot, amelyet a partner közvetlenül kezelhet. A bérlő nem szeretné megosztani a részletek a partner közvetlenül, ha azok adja meg a hitelesítő adatok korlátozott idejű hozzáférés és a konfigurációs kiszolgáló. Vagy a partner segítséget, telepíthetik a mobilitási szolgáltatás ügynöke manuálisan.

## <a name="vcenter-account-requirements"></a>vCenter fiókra vonatkozó követelmények

A konfigurációs kiszolgáló konfigurálása egy olyan fiókkal, amely azt különleges szerepkörrel rendelkezik.

- A szerepkör-hozzárendelés legyen a vCenter-hozzáférési fiók minden egyes vCenter objektumra vonatkozik, és nem jutott el a gyermekobjektumok. Ez a konfiguráció biztosítja a bérlők elszigetelésére, mert a hozzáférés propagálás más objektumok véletlen elérésében is megnyilvánulhat.

    ![A gyermekobjektum beállítást terjesztése](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Az alternatív megoldás részeként, hogy a felhasználói fiók és a datacenter objektumazonosítóra szerepkör hozzárendelése, és továbbíthatja őket a gyermekobjektumok. Adja meg a fiók egy **nem lehet hozzáférni** minden objektum (például virtuális gépek más bérlők tartozó) szerepkör, amely nem érhető el, hogy egy adott bérlő kell lennie. Ez a konfiguráció nincs nehézkes. Véletlenszerű hozzáférés-vezérlést, mert minden új gyermekobjektum is hozzáférést kap, amely örökli a szülő teszi elérhetővé. Ezért azt javasoljuk, hogy használja-e az első módszer.

### <a name="create-a-vcenter-account"></a>VCenter-fiók létrehozása

1. Új szerepkör létrehozása a klónozásával az előre meghatározott *csak olvasható* szerepkört, majd adjon egy kényelmes nevét (például Azure_Site_Recovery, ebben a példában látható módon).
2. Az alábbi engedélyek hozzárendelése a szerepkörhöz:

    * **Datastore**: szóköz, Tallózás datastore, alacsony szintű fájlműveletek, távolítsa el a fájlt, frissítés virtuálisgép-fájlok foglalása
    * **Hálózati**: hálózati hozzárendelése
    * **Erőforrás**: hozzárendelése VM erőforráskészlethez, ki van kapcsolva a virtuális gépek áttelepítése a virtuális gép kapcsolva áttelepítése
    * **Feladatok**: a feladat, a frissítési feladat létrehozása
    * **Virtuálisgép - konfiguráció**: összes
    - **Virtuálisgép - kapcsolati tevékenység** > válaszoljon a kérdést, eszköz kapcsolat, CD konfigurálása media, konfigurálás hajlékonylemez media, kapcsolja ki a bekapcsolás, VMware-eszközök telepítése
    - **Virtuálisgép - készlet** > létrehozása meglévő, létrehozhat új, regisztrálása, Unregister
    - **VM - kiépítés** > engedélyezése virtuális gép letölthető, a virtuális gép fájlok feltöltése
    - **Virtuálisgép - pillanatképét felügyeleti** > pillanatképek eltávolítása

        ![A szerep szerkesztése párbeszédpanel](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Rendelje hozzá hozzáférési szintek a vCenter-fiókhoz (a bérlő kiszolgáló szerepel) a különböző objektumok, a következőképpen:

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

### <a name="failover-only"></a>Csak feladatátvételi
Vészhelyzeti helyreállítási művelet csak feladatátvételi beállítást korlátozása (Ez azt jelenti, hogy feladat-visszavétel kezelésére), a fenti eljárással, kivéve a következőket:

- Ne a *Azure_Site_Recovery* a vCenter-hozzáférési fiókként, a szerepkör hozzárendelése csak egy *írásvédett* szerepkör ezekhez a fiókokhoz. Ez az engedélycsoport lehetővé teszi a virtuális gép replikációs és feladatátvételi, és nem teszi lehetővé feladat-visszavételre.
- Minden más, az előző folyamatban marad van. Győződjön meg arról a bérlők elszigetelésére, és korlátozzák a virtuális gép felderítése, minden engedélyt objektumszinten csak rendelve, és nem jutott el a gyermek-objektumok.

### <a name="deploy-resources-to-the-tenant-subscription"></a>A bérlői előfizetéshez erőforrások telepítése

1. Az Azure portálon hozzon létre egy erőforráscsoportot, és a Recovery Services-tárolónak a szokásos folyamatonként telepíteni.
2. Töltse le a tároló regisztrációs kulcsát.
3. A Tanúsítványszolgáltatások regisztrálja a bérlő a tárolóbeli regisztrációs kulcs használatával.
4. Adja meg a hitelesítő adatokat a két hozzáférési fiókokat, a vCenter-kiszolgáló eléréséhez a fióknak és a virtuális gép elérésére szolgáló fiók.

    ![Kezelői konfigurációs kiszolgáló fiókok](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Regisztrálja a kiszolgálót a tárolóban

1. Az Azure portálon, a tárolóban lévő, korábban létrehozott regisztrálja a vCenter-kiszolgáló és a konfigurációs kiszolgáló, a létrehozott vCenter fiók használatával.
2. Az "Infrastruktúra előkészítése" folyamat befejezéséhez a Site Recovery a szokásos folyamatonként.
3. A virtuális gépek replikálása most már készen áll. Ellenőrizze, hogy csak a bérlői virtuális gépek jelennek-e a **replikálása** > **válassza ki a virtuális gépek**.

## <a name="dedicated-hosting-solution"></a>Dedikált üzemeltetési megoldás

Ahogy a következő ábrán is látható, a architekturális egy dedikált üzemeltetési megoldás különbség, hogy a bérlő infrastruktúra csak a bérlő van beállítva.

![architektúra megosztott hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedikált üzemeltetés több Vcenter**

## <a name="managed-service-solution"></a>Felügyelt megoldás

Ahogy a következő ábrán is látható, a architekturális felügyelt megoldás különbség, hogy minden bérlő infrastruktúrát is fizikailag nem más bérlők infrastruktúra. Ebben a forgatókönyvben általában akkor beszélünk, amikor a bérlő az infrastruktúra tulajdonosa, és kezelheti a vész-helyreállítási megoldás szolgáltató szeretne.

![architektúra megosztott hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Felügyelt szolgáltatást a forgatókönyvet, és több Vcenter**

## <a name="next-steps"></a>További lépések
- [További](site-recovery-role-based-linked-access-control.md) vonatkozó szerepköralapú hozzáférés-vezérlés a Site Recovery szolgáltatásban.
- Megtudhatja, hogyan [vész helyreállítási VMware virtuális gépek Azure-bA](vmware-azure-tutorial.md).
- További információ [CSP VMWare virtuális gépek esetén a több-bérlős](vmware-azure-multi-tenant-csp-disaster-recovery.md).
