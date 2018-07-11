---
title: VMware virtuális gép replikálása az Azure (CSP) az Azure Site Recovery több-bérlős támogatásának áttekintése |} A Microsoft Docs
description: Áttekintést nyújt az Azure Site Recovery támogatási bérlőhöz tartozó előfizetések több-bérlős környezetben, a CSP program keretében.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 2f1a158b6ff4ec603c77f834212cb92fa862b424
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919937"
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>VMware-replikáció az Azure CSP-több-bérlős támogatásának áttekintése

[Az Azure Site Recovery](site-recovery-overview.md) bérlői előfizetések támogatja a több-bérlős környezetben. Több-bérlős létrehozása és felügyelete a Microsoft Cloud Solution Provider (CSP) program keretében bérlői előfizetéseket is támogatja.

Ez a cikk megvalósítása és kezelése az Azure-bA több-bérlős VMware áttekintést nyújt.

## <a name="multi-tenant-environments"></a>Több-bérlős környezetekben

Nincsenek három fő több-bérlős modell:

* **A megosztott üzemeltetési szolgáltatásokat szolgáltató (HSP)**: A partner tulajdonában lévő fizikai infrastruktúráját, és használ megosztott erőforrásokat (vCenter, adatközpontok, fizikai tároló, és így tovább) több bérlő virtuális gépek üzemeltetéséhez ugyanazon az infrastruktúrán. A partner is képes a globálisnév vész-helyreállítási felügyelt szolgáltatás, vagy a bérlő is saját vész-helyreállítási önkiszolgáló megoldást.

* **Dedikált üzemeltetési szolgáltató**: A partner a fizikai infrastruktúra tulajdonosa, de dedikált erőforrásokat (több vCenters, fizikai adattárolók, és így tovább) használja a külön infrastruktúra minden egyes bérlő-alapú virtuális gépek üzemeltetéséhez. A partner is képes a globálisnév vész-helyreállítási felügyelt szolgáltatás, vagy a bérlő is saját, önkiszolgáló megoldás.

* **Felügyelt szolgáltatások szolgáltatói (MSP)**: az ügyfél a tulajdonosa a virtuális gépeket üzemeltető fizikai infrastruktúráját, és a partner által biztosított a vész-helyreállítási engedélyezését és felügyelet.

## <a name="shared-hosting-services-provider-hsp"></a>A megosztott üzemeltetési szolgáltatásokat szolgáltató (HSP)

A két esetben a megosztott üzemeltetési forgatókönyv részhalmaza, és alapelveket használnak. A megosztott üzemeltetési útmutató végén a különbségeket ismerteti.

Egy több-bérlős forgatókönyvben alapvető követelmény az, hogy a bérlők számára elkülönített kell lennie. Egyetlen bérlő nem kell tudni figyelje meg, milyen más bérlők üzemeltetett rendelkezik. Partner által felügyelt környezetben ez a követelmény nem számít, mivel egy önkiszolgáló környezet, amelyben kritikus fontosságú. Ez a cikk feltételezi, hogy a bérlők elkülönítését szükséges.

Az architektúra a következő ábrán látható.

![A vCenter egy megosztott HSP](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**A megosztott-üzemeltet egy vCenter-kiszolgáló**

Az ábrán minden ügyfél rendelkezik egy külön felügyeleti kiszolgálóhoz. Ez a konfiguráció korlátozza a bérlői hozzáférési bérlőspecifikus virtuális gépekhez, és lehetővé teszi a bérlők elkülönítésének. VMware virtuális gép replikációja a konfigurációs kiszolgáló virtuális gépek felderítése és ügynökök telepítésére használja. Több-bérlős környezetekben, igény szerinti hozzáadásával vCenter hozzáférés-vezérlés használatával virtuális gépek felderítésének korlátozása ugyanezek az elvek vonatkoznak.

Az adatok elkülönítése követelmény, az azt jelenti, hogy minden bizalmas infrastruktúra információt (például a hozzáférési hitelesítő adatok) továbbra is a bérlők számára nem nyilvános. Ezért azt javasoljuk, hogy a felügyeleti kiszolgáló összes összetevőjét a partner kizárólagos felügyelete alatt maradnak. A felügyeleti kiszolgáló-összetevők a következők:

* Konfigurációs kiszolgáló
* Folyamatkiszolgáló
* Fő célkiszolgáló

A különálló horizontálisan felskálázott folyamatkiszolgálók is a partner ellenőrzés alatt áll.

## <a name="configuration-server-accounts"></a>Konfigurációs kiszolgáló fiókok

Minden konfigurációs kiszolgálót a több-bérlős forgatókönyvben két fiókot használja:

- **vCenter-hozzáférési fiók**: Ez a fiók bérlői virtuális gépek felderítésére szolgál. Hozzárendelt vCenter hozzáférési engedéllyel rendelkezik. Hozzáférés adatszivárgás elkerülése érdekében azt javasoljuk, hogy partnerek adja meg ezeket a hitelesítő adatokat, maguk a konfigurációs eszközt.

- **Virtuálisgép-hozzáférési fiók**: Ez a fiók használható a Mobilitásiszolgáltatás-ügynök telepítése a bérlői virtuális gépeket, az automatikus leküldéses értesítésekkel. Ez általában a egy tartományfiókot, amely egy bérlő biztosíthatnak a partnernek, vagy egy fiókot, amely közvetlenül a partner kezelhet. Egy bérlő adatait megosztja a partner közvetlenül nem szeretné, ha azok adja meg a hitelesítő adatokat, korlátozott idejű hozzáférés és a konfigurációs kiszolgáló. Vagy a partner segítségét, és telepíthetik a Mobilitásiszolgáltatás-ügynök manuális.

## <a name="vcenter-account-requirements"></a>vCenter-fiókra vonatkozó követelmények

A konfigurációs kiszolgáló konfigurálása egy olyan fiókkal, amely egy hozzárendelt különleges szerepkörrel rendelkezik.

- A szerepkör-hozzárendelés legyen a vCenter-hozzáférési fiók minden vCenter objektumon alkalmazza, és az gyermekobjektum nem érvényesülnek. Ez a konfiguráció biztosítja a bérlők elszigetelésére, mivel hozzáférés propagálás véletlen hozzáférést eredményezhet más objektumokra.

    ![Az gyermekobjektum beállítás terjesztése](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Az alternatív megoldás, hogy a felhasználói fiók és az Adatközpont-objektum szerepkörhöz hozzárendelni, és átvezeti őket az gyermekobjektum. Adjon a fióknak egy **nincs hozzáférés** minden objektum (például virtuális gépeket, más bérlők tartozó) szerepkör, amely nem érhető el, hogy egy adott bérlő kell lennie. Ez a konfiguráció nehézkes. Véletlen hozzáférés-vezérlést, mivel minden új propagálás is automatikusan hozzáférést kap, amely örökli a szülő teszi elérhetővé. Ezért azt javasoljuk, hogy az első módszer használata.

### <a name="create-a-vcenter-account"></a>A vCenter-fiók létrehozása

1. Új szerepkör létrehozása a klónozásával az előre meghatározott *csak olvasható* szerepkört, majd adjon meg egy kényelmes nevet (például Azure_Site_Recovery, ahogyan az ebben a példában), és.
2. A következő engedélyek hozzárendelése ehhez a szerepkörhöz:

    * **Adattároló**: foglalható le terület, Tallózás datastore, alacsony szintű fájlműveletek, fájl eltávolítása, frissítés virtuálisgép-fájlok
    * **Hálózati**: hálózati hozzárendelése
    * **Erőforrás**: virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép áttelepítése a virtuális Gépen működő áttelepítése
    * **Feladatok**: a feladat, a frissítési feladat létrehozása
    * **Virtuálisgép - konfiguráció**: összes
    - **Virtuálisgép - kapcsolati** > választ a kérdésre, eszközkapcsolat, CD konfigurálása a media, konfigurálás hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése
    - **Virtuálisgép - készlet** > Létrehozás meglévőből, létrehozhat új, Regisztrálás, regisztráció törlése
    - **VM - kiépítés** > virtuális gép letöltésének, engedélyezése a virtuális gép fájlok feltöltése
    - **VM - pillanatkép kezelése** > pillanatképek eltávolítása

        ![A szerepkör szerkesztése párbeszédpanel](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Rendelje hozzá hozzáférési szintekkel, a vCenter-fióknak (a bérlő konfigurációs kiszolgálón használt) különböző objektumok, a következő:

>| Objektum | Szerepkör | Megjegyzések |
>| --- | --- | --- |
>| vCenter | Csak olvasható | Csak a vCenter hozzáférést a különböző objektumok kezeléséhez szükséges. Is távolítsa el ezt az engedélyt, ha a fiók soha nem fog adni a bérlő, és a vcenter-kiszolgáló felügyeleti műveleteket sem használható. |
>| Adatközpont | Azure_Site_Recovery |  |
>| Gazdagép és a gazdagép fürt | Azure_Site_Recovery | Újra biztosítja, hogy hozzáférést az objektumok szintjén, hogy csak az elérhető gazdagépek bérlői virtuális gépeket a feladatátvétel előtt és után a feladat-visszavétel. |
>| Adattároló és adattároló-fürt | Azure_Site_Recovery | Ugyanaz, mint az előző. |
>| Network (Hálózat) | Azure_Site_Recovery |  |
>| Felügyeleti kiszolgáló | Azure_Site_Recovery | Az összes összetevő (CS, PS és MT) kívül a CS géphez való hozzáférést tartalmaz. |
>| Bérlő virtuális gépek | Azure_Site_Recovery | Biztosítja, hogy minden olyan új bérlőt egy adott bérlő virtuális gépek is hozzáférhet a, vagy nem észlelhető, az Azure Portalon keresztül. |

A vCenter-fiók hozzáférési már befejeződött. Ebben a lépésben teljesíti a minimális engedélyek elvégzéséhez követelmény, hogy feladat-visszavételi műveletet. A hozzáférési engedélyek a meglévő szabályzatokat is használhatja. A meglévő engedélyeket úgy, hogy tartalmazza a szerepkör engedélyeivel 2,. lépés részletes korábban egyszerűen módosíthatja.

### <a name="failover-only"></a>Csak feladatátvételi
Korlátozni vész-helyreállítási művelet csak feladatátvételi másnapi (azt jelenti, feladat-visszavétel képességek nélkül), ezeket a kivételeket az előző eljárás használata:

- Helyett a *Azure_Site_Recovery* a vCenter-hozzáférési fiókként, a szerepkör hozzárendelése csak egy *csak olvasható* -szerepkört ahhoz a fiókhoz. Ezzel a halmazával lehetővé teszi a virtuális gép replikációs és feladatátvételi, és nem teszi lehetővé feladat-visszavétel.
- Minden más, a fenti folyamat marad van. Győződjön meg, hogy a bérlők elszigetelésére, és korlátozzák a virtuális gépek felderítésének, minden engedély továbbra is csak az objektumok szintjén hozzárendelt, és nem vonatkoznak az gyermekobjektum.

### <a name="deploy-resources-to-the-tenant-subscription"></a>A bérlői előfizetéshez erőforrások üzembe helyezése

1. Az Azure Portalon hozzon létre egy erőforráscsoportot, és a Recovery Services-tároló, a szokásos folyamatonként telepíteni.
2. Töltse le a tároló regisztrációs kulcsát.
3. A CS regisztrálja a bérlő által a tároló regisztrációs kulcsát használja.
4. Adja meg a két hozzáférési fiókokat, a fiók a vCenter-kiszolgálóhoz való hozzáféréshez és eléri a virtuális Gépet a fiók hitelesítő adatait.

    ![Manager konfigurációs kiszolgáló-fiókok](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Regisztrálja a kiszolgálót a tárolóban

1. Az Azure Portalon, a tárolóban, amelyet korábban hozott létre a vCenter-kiszolgáló és a konfigurációs kiszolgáló regisztrálása, a létrehozott fiók a vcenter-kiszolgáló használatával.
2. Az "Infrastruktúra előkészítése" folyamat befejezéséhez a Site Recovery a szokásos folyamatonként.
3. A virtuális gépek replikációja készen áll. Győződjön meg arról, hogy csak a bérlői virtuális gépek megjelennek **replikálása** > **válassza ki a virtuális gépek**.

## <a name="dedicated-hosting-solution"></a>Dedikált üzemeltetési megoldás

Az alábbi ábrán látható, az architekturális a dedikált üzemeltetési megoldás különbség, hogy minden bérlő infrastruktúra beállításával csak a bérlő számára.

![architektúra megosztott hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**A több vCenters dedikált üzemeltetés**

## <a name="managed-service-solution"></a>Felügyelt megoldás

Az alábbi ábrán látható, az architekturális felügyelt szolgáltatottszoftver-megoldás a különbség, hogy minden bérlő infrastruktúra is fizikailag elkülönül más bérlők infrastruktúra. Ebben a forgatókönyvben általában létezik, amikor a bérlő az infrastruktúra tulajdonosa és vészhelyreállításhoz megoldásszolgáltató szeretne.

![architektúra megosztott hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Felügyelt szolgáltatás több vCenters forgatókönyv**

## <a name="next-steps"></a>További lépések
- [További](site-recovery-role-based-linked-access-control.md) vonatkozó szerepköralapú hozzáférés-vezérlés a Site Recoveryben.
- Ismerje meg, hogyan [beállítása VMware virtuális gépek vészhelyreállítása az Azure-bA](vmware-azure-tutorial.md).
- Tudjon meg többet [VMWare virtuális gépek több felhőszolgáltatóval](vmware-azure-multi-tenant-csp-disaster-recovery.md).
