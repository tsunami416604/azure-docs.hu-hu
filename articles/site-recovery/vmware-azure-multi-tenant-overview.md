---
title: VMware VM több-bérlős vész-helyreállítási azure Site Recovery
description: Áttekintést nyújt az Azure Site Recovery támogatja a VMWare vész-helyreállítási azure-ban egy több-bérlős környezetben (CSP) program.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 840049265d3b6e4d2fddd794646bfd5691aab9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083991"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>A VMware vész-helyreállítási szolgáltatásának több-bérlős támogatásának áttekintése az Azure-ba a CSP segítségével

[Az Azure Site Recovery](site-recovery-overview.md) támogatja a több-bérlős környezetek bérlői előfizetések. Emellett támogatja a több-bérlős bérlői előfizetések, amelyek a Microsoft Cloud Solution Provider (CSP) programon keresztül létrehozott és felügyelt.

Ez a cikk áttekintést nyújt a több-bérlős VMware azure-replikációra történő megvalósításáról és kezeléséről.

## <a name="multi-tenant-environments"></a>Több-bérlős környezetek

Három fő több-bérlős modell létezik:

* **Megosztott tárhelyszolgáltató (HSP)**: A partner birtokolja a fizikai infrastruktúrát, és megosztott erőforrásokat (vCenter, adatközpontok, fizikai tárolás és így tovább) használ több bérlővirtuális gép ugyanazon az infrastruktúrán. A partner vész-helyreállítási felügyeletet biztosíthat felügyelt szolgáltatásként, vagy a bérlő önkiszolgáló megoldásként rendelkezhet vész-helyreállítási szolgáltatással.

* **Dedikált tárhelyszolgáltató:** A partner birtokolja a fizikai infrastruktúrát, de dedikált erőforrásokat (több vCenter, fizikai adattárak és így tovább) használ az egyes bérlők virtuális gépei külön infrastruktúrán való üzemeltetéséhez. A partner vész-helyreállítási felügyeletet biztosíthat felügyelt szolgáltatásként, vagy a bérlő önkiszolgáló megoldásként birtokolhatja.

* **Felügyelt szolgáltatások szolgáltatója (MSP)**: Az ügyfél birtokolja a virtuális gépeket kiszolgáló fizikai infrastruktúrát, és a partner biztosítja a vész-helyreállítási engedélyezést és -kezelést.

## <a name="shared-hosting-services-provider-hsp"></a>Megosztott tárhelyszolgáltató (HSP)

A másik két forgatókönyv a megosztott üzemeltetési forgatókönyv részhalmaza, és ugyanazokat az elveket használják. A különbségeket a megosztott üzemeltetési útmutató végén ismertetik.

A több-bérlős forgatókönyv alapvető követelménye, hogy a bérlők el kell különíteni. Az egyik bérlő nem figyelheti meg, hogy egy másik bérlő mit üzemeltetett. A partnerek által kezelt környezetben ez a követelmény nem olyan fontos, mint egy önkiszolgáló környezetben, ahol kritikus lehet. Ez a cikk feltételezi, hogy a bérlő elkülönítése szükséges.

Az architektúra az alábbi ábrán látható.

![Megosztott HSP egy vCenterrel](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Megosztott üzemeltetés egy vCenter-kiszolgálóval**

Az ábrán minden ügyfél nek külön felügyeleti kiszolgálója van. Ez a konfiguráció korlátozza a bérlői hozzáférést a bérlő-specifikus virtuális gépekhez, és engedélyezi a bérlő elkülönítését. A VMware vm replikációja a konfigurációs kiszolgáló nócai segítségével észleli a virtuális gépeket, és ügynököket telepít. Ugyanezek az alapelvek vonatkoznak a több-bérlős környezetek, azzal a kiegészítéssel, hogy korlátozza a virtuális gép felderítése vCenter hozzáférés-vezérlés használatával.

Az adatelkülönítési követelmény azt jelenti, hogy az összes bizalmas infrastruktúra-információ (például a hozzáférési hitelesítő adatok) nem nyilvános a bérlők számára. Ezért azt javasoljuk, hogy a felügyeleti kiszolgáló minden összetevője a partner kizárólagos ellenőrzése alatt maradjon. A felügyeleti kiszolgáló összetevői a következők:

* Konfigurációs kiszolgáló
* Folyamatkiszolgáló
* Fő célkiszolgáló

Egy külön kibővített folyamatkiszolgáló is a partner ellenőrzése alatt áll.

## <a name="configuration-server-accounts"></a>Konfigurációs kiszolgálófiókok

A több-bérlős forgatókönyvben minden konfigurációs kiszolgáló két fiókot használ:

- **vCenter-hozzáférési fiók:** Ez a fiók a bérlői virtuális gépek felderítéséhez használható. VCenter hozzáférési engedélyekkel rendelkezik hozzárendelve. A hozzáférési szivárgások elkerülése érdekében azt javasoljuk, hogy a partnerek maguk adják meg ezeket a hitelesítő adatokat a konfigurációs eszközben.

- **Virtuálisgép-hozzáférési fiók:** Ez a fiók a mobilszolgáltató-ügynök bérlői virtuális gépeken történő telepítéséhez használható, automatikus leküldéses leküldéses. Ez általában egy tartományi fiók, amelyet a bérlő adhat egy partnernek, vagy egy olyan fiók, amelyet a partner közvetlenül kezelhet. Ha egy bérlő nem szeretné megosztani a részleteket a partner közvetlenül, megadhatja a hitelesítő adatokat a konfigurációs kiszolgáló korlátozott idejű elérésén keresztül. Vagy a partner segítségével manuálisan is telepíthetik a Mobilitási szolgáltatót.

## <a name="vcenter-account-requirements"></a>a vCenter-fiók követelményei

Konfigurálja a konfigurációs kiszolgálót egy olyan fiókkal, amelyhez speciális szerepkör van rendelve.

- A szerepkör-hozzárendelés kell alkalmazni a vCenter hozzáférési fiók minden vCenter-objektum, és nem propagálható a gyermek objektumok. Ez a konfiguráció biztosítja a bérlők elkülönítését, mivel a hozzáférés propagálása más objektumokhoz való véletlen hozzáférést eredményezhet.

    ![A Propagálás gyermekobjektumokra beállítás](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Az alternatív megközelítés az adatközpont-objektum felhasználói fiókjának és szerepkörének hozzárendelése és a gyermekobjektumokhoz való propagálása. Ezután adjon a fióknak egy **Nincs hozzáférési** szerepkört minden olyan objektumhoz (például a más bérlőkhöz tartozó virtuális gépekhez), amelyeknek egy adott bérlő számára nem érhető el. Ez a konfiguráció nehézkes. Ez teszi elérhetővé a véletlen hozzáférés-vezérlés, mert minden új gyermek objektum is automatikusan megkapja a hozzáférést, amely örökölt a szülő. Ezért azt javasoljuk, hogy az első megközelítés használatát.

### <a name="create-a-vcenter-account"></a>VCenter-fiók létrehozása

1. Hozzon létre egy új szerepkört az előre definiált *írásvédett* szerepkör klónozásával, majd adjon neki egy kényelmes nevet (például Azure_Site_Recovery, ahogy az ebben a példában látható).
2. Rendelje hozzá a következő engedélyeket ehhez a szerepkörhöz:

   * **Datastore**: Hely lefoglalása, Adattár tallózása, Alacsony szintű fájlműveletek, Fájl eltávolítása, Virtuálisgép-fájlok frissítése
   * **Hálózat**: Hálózati hozzárendelés
   * **Erőforrás:** Virtuális gép hozzárendelése erőforráskészlethez, Kioldott virtuális gép áttelepítése, Virtuális gép áttelepítése
   * **Feladatok**: Feladat létrehozása, Feladat frissítése
   * **VM - Konfiguráció**: Minden
   * **VM - Interakció** > Válasz kérdés, Eszköz kapcsolat, CD-adathordozó konfigurálása, Hajlékonylemez-adathordozó konfigurálása, Kikapcsolás, Bekapcsolás, VMware eszközök telepítése
   * **VM - Készlet** > létrehozás meglévőből, Új létrehozása, Regisztráció, Regisztráció megszüntetése
   * **VM - kiépítése** > Virtuális gép letöltésének engedélyezése, Virtuálisgép-fájlok feltöltésének engedélyezése
   * **Virtuális gép – pillanatkép-kezelés** > pillanatképek eltávolítása

       ![A Szerepkör szerkesztése párbeszédpanel](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Hozzáférési szintek hozzárendelése a (a bérlői konfigurációs kiszolgálón használt) vCenter-fiókhoz a különböző objektumokhoz, az alábbiak szerint:

>| Objektum | Szerepkör | Megjegyzések |
>| --- | --- | --- |
>| vCenter | Írásvédett | Csak a vCenter-hozzáférés engedélyezéséhez szükséges a különböző objektumok kezeléséhez. Eltávolíthatja ezt az engedélyt, ha a fiók soha nem lesz megadva a bérlőnek, vagy a vCenter bármilyen felügyeleti műveletéhez használható. |
>| Adatközpont | Azure_Site_Recovery |  |
>| Állomás- és állomásfürt | Azure_Site_Recovery | Az újrabiztosítva, hogy a hozzáférés az objektum szintjén legyen, így csak az elérhető gazdagépek rendelkeznek bérlői virtuális gépekkel a feladatátvétel előtt és a feladat-visszavétel után. |
>| Adattár- és adattár-fürt | Azure_Site_Recovery | Ugyanaz, mint az előző. |
>| Network (Hálózat) | Azure_Site_Recovery |  |
>| Felügyeleti kiszolgáló | Azure_Site_Recovery | A CS-gépen kívüli összes összetevőhöz (CS, PS és MT) hozzáférést biztosít. |
>| Bérlői virtuális gépek | Azure_Site_Recovery | Biztosítja, hogy egy adott bérlő új bérlői virtuális gépei is megkapják ezt a hozzáférést, vagy nem lesznek felderíthetők az Azure Portalon keresztül. |

A vCenter-fiók hozzáférése befejeződött. Ez a lépés megfelel a feladat-visszavételi műveletek végrehajtásához szükséges minimális engedélyek követelményének. Ezeket a hozzáférési engedélyeket a meglévő házirendekkel is használhatja. Csak módosítsa a meglévő engedélyeket, hogy tartalmazza a szerepkör engedélyeket a 2.

### <a name="failover-only"></a>Csak feladatátvétel
A vész-helyreállítási műveletek feladatátvételig (azaz feladat-visszavételi képességek nélküli) korlátozásához használja az előző eljárást, az alábbi kivételekkel:

- Ahelyett, hogy a *Azure_Site_Recovery* szerepkört a vCenter-hozzáférési fiókhoz rendelne, csak egy *csak olvasható szerepkört* rendeljen a fiókhoz. Ez az engedélykészlet lehetővé teszi a virtuális gép replikációját és feladatátvételét, és nem teszi lehetővé a feladat-visszavételt.
- Minden más az előző folyamatban változatlan marad. A bérlők elkülönítésének biztosítása és a virtuális gép felderítésének korlátozása érdekében minden engedély továbbra is csak az objektum szintjén van hozzárendelve, és nem lesz alárendelt objektumokra propagálva.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Erőforrások üzembe helyezése a bérlői előfizetésben

1. Az Azure Portalon hozzon létre egy erőforráscsoportot, majd telepítse a Recovery Services-tárolót a szokásos folyamat szerint.
2. Töltse le a tároló regisztrációs kulcsát.
3. Regisztrálja a CS a bérlő a tároló regisztrációs kulcs használatával.
4. Adja meg a két hozzáférési fiók hitelesítő adatait, a fiók a vCenter-kiszolgáló eléréséhez, és a fiók a virtuális gép eléréséhez.

    ![Kezelő konfigurációs kiszolgálófiókjai](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Kiszolgálók regisztrálása a tárolóban

1. Az Azure Portalon, a korábban létrehozott tárolóban regisztrálja a vCenter-kiszolgálót a konfigurációs kiszolgálóra a létrehozott vCenter-fiók használatával.
2. Fejezze be az "Infrastruktúra előkészítése" folyamatot a site recovery számára a szokásos folyamat szerint.
3. A virtuális gépek készen állnak a replikálásra. Ellenőrizze, hogy csak a bérlő virtuális gépei jelennek-e meg a Select Virtuális gépek **replikálása** > **közben.**

## <a name="dedicated-hosting-solution"></a>Dedikált üzemeltetési megoldás

Ahogy az alábbi ábrán látható, az architekturális különbség egy dedikált üzemeltetési megoldás, hogy minden bérlő infrastruktúrája van beállítva, hogy a bérlő csak.

![architektúra-megosztott-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedikált üzemeltetési forgatókönyv több vCenterrel**

## <a name="managed-service-solution"></a>Felügyelt szolgáltatásmegoldás

Ahogy az alábbi ábrán látható, a felügyelt szolgáltatás megoldás architekturális különbség, hogy minden bérlő infrastruktúrája fizikailag is elkülönül a többi bérlő infrastruktúráját. Ez a forgatókönyv általában akkor áll fenn, ha a bérlő az infrastruktúra tulajdonosa, és azt akarja, hogy egy megoldásszolgáltató kezelje a vész-helyreállítási.

![architektúra-megosztott-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Felügyelt szolgáltatás forgatókönyv több vCenterek**

## <a name="next-steps"></a>További lépések
- [További információ](site-recovery-role-based-linked-access-control.md) a szerepköralapú hozzáférés-vezérlésről a Site Recovery szolgáltatásban.
- Ismerje meg, hogyan [állíthatja be a VMware virtuális gépek vészutáni helyreállítását az Azure-ba.](vmware-azure-tutorial.md)
- További információ a [virtuális gépekhez való csp-vel kapcsolatos többbérleti szerződésről.](vmware-azure-multi-tenant-csp-disaster-recovery.md)
