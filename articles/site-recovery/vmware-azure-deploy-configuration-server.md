---
title: A konfigurációs kiszolgáló telepítése az Azure Site Recovery szolgáltatásban
description: Ez a cikk azt ismerteti, hogy miként telepíthet konfigurációs kiszolgálót a VMware vész-helyreállítási szolgáltatásához az Azure Site Recovery szolgáltatással
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 5209dab5e0934cc98bb1334a1565cc13998a7d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257380"
---
# <a name="deploy-a-configuration-server"></a>Konfigurációs kiszolgáló üzembe helyezése

A helyszíni konfigurációs kiszolgálót akkor telepíti, amikor [az Azure Site Recovery](site-recovery-overview.md) szolgáltatást használja a VMware virtuális gépek és a fizikai kiszolgálók azure-beli vészhelyreállítása érdekében. A konfigurációs kiszolgáló koordinálja a helyszíni VMware és az Azure közötti kommunikációt. Az adatreplikációt is kezeli. Ez a cikk végigvezeti a konfigurációs kiszolgáló üzembe helyezéséhez szükséges lépéseket, amikor vmware virtuális gépeket replikál az Azure-ba. Ha konfigurációs kiszolgálót kell beállítania a fizikai kiszolgáló replikációjához, olvassa [el a Konfigurációs kiszolgáló beállítása az Azure-ba irányuló fizikai kiszolgálók vész-helyreállítási szolgáltatásához című témakört.](physical-azure-set-up-source.md)

> [!TIP]
> Ha többet szeretne megtudni egy konfigurációs kiszolgáló szerepéről az Azure Site Recovery architektúra részeként, olvassa el a [VMware az Azure vész-helyreállítási architektúráját.](vmware-azure-architecture.md)

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>Konfigurációs kiszolgáló telepítése OVA-sablonon keresztül

A konfigurációs kiszolgálót magas rendelkezésre állású VMware vm-ként kell beállítani bizonyos minimális hardver- és méretezési követelményekkel. A kényelmes és egyszerű telepítés érdekében a Site Recovery egy letölthető Open Virtualization Application (OVA) sablont biztosít az itt felsorolt összes követelménynek megfelelő konfigurációs kiszolgáló beállításához.

## <a name="prerequisites"></a>Előfeltételek

A konfigurációs kiszolgáló minimális hardverkövetelményeit a következő szakaszok foglalják össze.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Az Azure Active Directory engedélykövetelményei

Az Azure Active Directoryban (Azure AD) beállított alábbi engedélyek egyikével rendelkező felhasználónak kell rendelkeznie a konfigurációs kiszolgáló Azure Site Recovery-szolgáltatásokkal való regisztrálásához.

1. Az alkalmazás létrehozásához a felhasználónak alkalmazásfejlesztői szerepkört kell létrehoznia.
    - Az ellenőrzéshez jelentkezzen be az Azure Portalra.</br>
    - Nyissa meg az **Azure Active Directory szerepkörök** > **és rendszergazdák**című.</br>
    - Ellenőrizze, hogy az alkalmazásfejlesztői szerepkör hozzá van-e rendelve a felhasználóhoz. Ha nem, használjon ezzel az engedéllyel rendelkező felhasználót, vagy lépjen kapcsolatba [a rendszergazdával az engedély engedélyezéséhez.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)
    
2. Ha az alkalmazás fejlesztői szerepkör nem rendelhető hozzá, győződjön meg arról, hogy a **felhasználók regisztrálhatnak alkalmazások jelzője** **igazként** van beállítva a felhasználó számára egy identitás létrehozásához. Az alábbi engedélyek engedélyezéséhez tegye a következőket:
    - Jelentkezzen be az Azure portálra.
    - Nyissa meg az **Azure Active Directory** > **felhasználói beállításait.**
    - Az **Alkalmazásregisztrációk csoportban**a **Felhasználók regisztrálhatnak alkalmazásokat,** válassza az **Igen**lehetőséget.

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Az Active Directory *összevonási szolgáltatások nem támogatottak.* Az [Azure Active Directoryn](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)keresztül kezelt fiók használata.

## <a name="download-the-template"></a>A sablon letöltése

1. A tárolóban nyissa **meg az Infrastruktúra** > forrás előkészítése**című**részt.
2. A **Forrás előkészítése** ablakban válassza a **+Konfigurációs kiszolgáló** elemet.
3. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **Kiszolgálótípus** mezőben a **Konfigurációs kiszolgáló VMware-hez** érték jelenik meg.
4. Töltse le a konfigurációs kiszolgáló OVA sablonját.

   > [!TIP]
   >A konfigurációs kiszolgáló sablonjának legújabb verzióját közvetlenül a [Microsoft letöltőközpontból](https://aka.ms/asrconfigurationserver)is letöltheti.

> [!NOTE]
> Az OVA sablonnal ellátott licenc egy 180 napig érvényes értékelési licenc. Ezen időszak után licencet kell szereznie.

## <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMWare vSphere Client használatával.
2. A **Fájl** **menüOVF-sablon telepítése parancsára** kattintson az **OVF-sablon telepítése** varázsló elindításához.

     ![OVF-sablon telepítése](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. A **Select source** (Forrás kiválasztása) területen adja meg a letöltött OVF helyét.
4. A **Review details** (Részletek áttekintése) területen válassza a **Next** (Tovább) gombot.
5. A **Select name and folder** (Név és mappa kiválasztása) és a **Select configuration** (Konfiguráció kiválasztása) területen fogadja el az alapértelmezett beállításokat.
6. A **Select storage** (Tároló kiválasztása) területen a legjobb teljesítmény érdekében válassza a **Thick Provision Eager Zeroed** formátumot a **Select virtual disk format** (Virtuális lemez formátumának kiválasztása) mezőben. A vékony kiépítési beállítás használata hatással lehet a konfigurációs kiszolgáló teljesítményére.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **Ready to complete** (Befejezésre kész) területen:

    * Ha a virtuális gépet az alapértelmezett beállításokkal szeretné beállítani, válassza **a Bekapcsolás a telepítés** > **befejezése**után lehetőséget.
    * További hálózati adapter hozzáadásához törölje a jelet **a Bekapcsolás a telepítés után jelölőnégyzetből,** majd kattintson a **Befejezés gombra.** Alapértelmezés szerint a konfigurációs kiszolgáló sablonját a rendszer egyetlen hálózati adapterre telepíti. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

> [!IMPORTANT]
> Ne módosítsa az erőforrás-konfigurációkat, például a memóriát, a magokat és a processzorkorlátozást, és ne módosítsa vagy törölje a telepített szolgáltatásokat vagy fájlokat a konfigurációs kiszolgálón a telepítés után. Az ilyen típusú módosítások hatással vannak a konfigurációs kiszolgáló Regisztrációjára az Azure-szolgáltatásokkal és a konfigurációs kiszolgáló teljesítményére.

## <a name="add-an-additional-adapter"></a>További adapter hozzáadása

> [!NOTE]
> Két hálózati adapterre van szükség, ha meg szeretné őrizni a feladatátvételi forrásgépek IP-címét, és később vissza szeretne adni a helyszíni adatoknak. Az egyik hálózati adapter forrásgépekhez csatlakozik, a másik hálózati adapter pedig az Azure-kapcsolathoz használatos.

Ha további hálózati adaptert szeretne hozzáadni a konfigurációs kiszolgálóhoz, adja hozzá, mielőtt regisztrálna a kiszolgálót a tárolóban. A regisztrálást követően további adapterek hozzáadása nem támogatott.

1. A vSphere Client-leltárban kattintson a jobb gombbal a virtuális gépre, és válassza az **Edit Settings** (Beállítások szerkesztése) elemet.
2. A **Hardware** (Hardver) területen válassza az **Add** > **Ethernet Adapter** (Hozzáadás > Ethernet-adapter) elemet. Ezután válassza a **Tovább**gombot.
3. Válassza ki a hálózati adapter típusát és a hálózatot.
4. Ha a virtuális hálózati adaptert a virtuális gép bekapcsolásakor szeretné csatlakoztatni, válassza a **Csatlakozás bekapcsoláskor**lehetőséget. Ezután válassza a **Következő** > **befejezés** > **OK**lehetőséget.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>A konfigurációs kiszolgáló regisztrálása az Azure Site Recovery szolgáltatással

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre.
4. Az első bejelentkezéskor néhány másodpercen belül elindul az Azure Site Recovery Configuration eszköz.
5. Adjon meg egy nevet, amelyen a konfigurációs kiszolgálót regisztrálja a Site Recovery szolgáltatásban. Ezután válassza a **Tovább**gombot.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe.</br>
    a. Olyan hitelesítő adatokra van szükség, amelyekkel hozzá lehet férni a tárolóhoz, amelyben regisztrálni kívánja a konfigurációs kiszolgálót.</br>
    b. Győződjön meg arról, hogy a kiválasztott felhasználói fiók rendelkezik engedéllyel egy alkalmazás létrehozásához az Azure-ban. A szükséges engedélyek engedélyezéséhez kövesse az [Azure Active Directory engedélykövetelményeinek szakaszirányelveit.](#azure-active-directory-permission-requirements)
7. Az eszköz végrehajt néhány konfigurációs feladatot, majd újraindul.
8. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló kezelővarázslója néhány másodpercen belül automatikusan elindul.

### <a name="configure-settings"></a>Beállítások konfigurálása

1. A konfigurációs kiszolgáló kezelése varázslóban válassza a **Telepítési kapcsolat**lehetőséget. A legördülő mezőkből először válassza ki azt a hálózati adaptert, amelyet a beépített folyamatkiszolgáló a mobilitási szolgáltatás felderítéséhez és leküldéses telepítéséhez használ a forrásgépeken. Ezután válassza ki a hálózati adaptert, amelyet a konfigurációs kiszolgáló az Azure-ral való kapcsolathoz használ. Kattintson a **Mentés** gombra. Ez a beállítás a konfigurálás után nem módosítható. Ne módosítsa a konfigurációs kiszolgáló IP-címét. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt IP-cím statikus IP-cím, nem pedig DHCP IP.
2. A **Recovery Services tárolójának kiválasztása**koron jelentkezzen be a Microsoft Azure-ba a Configuration Server [regisztrálása az Azure Site Recovery szolgáltatásokkal való regisztrálása](#register-the-configuration-server-with-azure-site-recovery-services)6.
3. A bejelentkezés után válassza ki az Azure-előfizetést és a megfelelő erőforráscsoportot és tárolót.

    > [!NOTE]
    > A regisztráció után nincs rugalmasság a helyreállítási szolgáltatások tárolójának módosítása.
    > A helyreállítási szolgáltatások tárolójának módosítása a konfigurációs kiszolgáló nak az aktuális tárolóból való szétválását igényli, és a konfigurációs kiszolgáló nívós összes védett virtuális gépének replikációja le van állítva. További információ: [A VMware VM vész-helyreállítási konfigurációs kiszolgálójának kezelése.](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)

4. Külső **gyártótól származó szoftver telepítésekor:**

    |Forgatókönyv   |Követendő lépések  |
    |---------|---------|
    |Letölthetem és telepíthetem manuálisan a MySQL-t?     |  Igen. Töltse le a MySQL alkalmazást, helyezze a **C:\Temp\ASRSetup**mappába, majd telepítse manuálisan. Miután elfogadta a feltételeket, és válassza a **Letöltés és telepítés**lehetőséget, a portál azt *mondja, hogy már telepítve van.* Folytassa a következő lépéssel.       |
    |Elkerülhetem a MySQL online letöltését?     |   Igen. Helyezze a MySQL telepítőalkalmazást a **C:\Temp\ASRSetup mappába.** Fogadja el a feltételeket, válassza a **Letöltés és telepítés**lehetőséget, és a portál az alkalmazás telepítéséhez hozzáadott telepítőt használja. A telepítés befejezése után folytassa a következő lépéssel.    |
    |Szeretném letölteni és telepíteni a MySQL-t az Azure Site Recovery szolgáltatáson keresztül.    |  Fogadja el a licencszerződést, és válassza a **Letöltés és telepítés**lehetőséget. A telepítés befejezése után folytassa a következő lépéssel.       |

5. A **készülék konfigurációjának ellenőrzése**esetén az előfeltételek a folytatás előtt ellenőrizhetők.
6. A **VCenter Server/vSphere ESXi-kiszolgáló konfigurálása**kor adja meg a vCenter-kiszolgáló vagy a vSphere-állomás Teljes tartományn-azonosító- vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
7. Adja meg a konfigurációs kiszolgáló által a VMware-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatokat. A Site Recovery ezeknek a hitelesítő adatoknak a használatával automatikusan deríti fel a replikáláshoz elérhető VMware virtuális gépeket. Válassza **a Hozzáadás** > **folytatás a lehetőséget.** Az itt megadott hitelesítő adatok at helyben menti a rendszer.
8. A **Virtuálisgép hitelesítő adatainak konfigurálása**párbeszédpanelen adja meg a mobilszolgáltatás replikáció során automatikus annektálásához szükséges virtuális gépek felhasználónevét és jelszavát. **Windows-gépek** esetén a fióknak helyi rendszergazdai jogosultságokra van szüksége a replikálni kívánt gépeken. **Linux esetén**adja meg a gyökérfiók részleteit.
9. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.
10. A regisztráció befejezése után nyissa meg az Azure portalt, és ellenőrizze, hogy a **konfigurációs**kiszolgáló és a VMware-kiszolgáló szerepel-e a Helyreállítási szolgáltatások > **tárolójában** > **a hely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálóinak kezelése szolgáltatásban.**

## <a name="upgrade-the-configuration-server"></a>A konfigurációs kiszolgáló frissítése

A konfigurációs kiszolgáló legújabb verzióra való frissítéséhez olvassa [el a VMware VM vész-helyreállítási konfigurációs kiszolgálójának kezelése című témakört.](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) Az összes hely-helyreállítási összetevő frissítéséről a [Webhely-helyreállítás szolgáltatásfrissítései (Site Recovery) témakörben](service-updates-how-to.md)talál.

## <a name="manage-the-configuration-server"></a>A konfigurációs kiszolgáló kezelése

A folyamatos replikáció megszakításainak elkerülése érdekében győződjön meg arról, hogy a konfigurációs kiszolgáló IP-címe nem változik, miután a konfigurációs kiszolgáló regisztrálva van egy tárolóban. A konfigurációs kiszolgáló gyakori felügyeleti feladatairól a [VMware VM vész-helyreállítási konfigurációs kiszolgálójának kezelése](vmware-azure-manage-configuration-server.md)című témakörben olvashat bővebben.

## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

A telepítési & kapcsolódási problémák megoldásához tekintse meg [hibaelhárítási](vmware-azure-troubleshoot-configuration-server.md) cikkünket.

## <a name="faqs"></a>Gyakori kérdések

* Mennyi ideig érvényes az OVF-en keresztül telepített konfigurációs kiszolgálón biztosított licenc? Mi történik, ha nem aktiválom újra a licencet?

    Az OVA sablonnal ellátott licenc 180 napig érvényes értékelési licenc. A lejárat előtt aktiválnia kell a licencet. Ellenkező esetben a konfigurációs kiszolgáló gyakori leállítását eredményezheti, és akadályozza a replikációs tevékenységeket. További információ: [A VMware VM vész-helyreállítási konfigurációs kiszolgálójának kezelése.](vmware-azure-manage-configuration-server.md#update-windows-license)

* Használhatom azt a virtuális gép, ahol a konfigurációs kiszolgáló különböző célokra telepítve van?

    Nem. Használja a virtuális gép a konfigurációs kiszolgáló kizárólagos célja. Győződjön meg arról, hogy betartja a vész-helyreállítási hatékony kezeléséhez szükséges [előfeltételekben](#prerequisites) említett összes előírást.
* Válthatok a konfigurációs kiszolgálón már regisztrált tároló között egy újonnan létrehozott tárolóval?

    Nem. Miután egy tároló regisztrálva van a konfigurációs kiszolgálón, nem módosítható.
* Használhatom ugyanazt a konfigurációs kiszolgálót a fizikai és a virtuális gépek védelmére?

    Igen. Ugyanaz a konfigurációs kiszolgáló használható fizikai és virtuális gépek replikálásához. A fizikai gép azonban csak egy VMware virtuális gépre hibásulthat vissza.
* Mi a célja a konfigurációs szerver, és hol használják?

    Ha többet szeretne megtudni a konfigurációs kiszolgálóról és annak funkcióiról, olvassa el a [VMware az Azure replikációs architektúrájának című témakört.](vmware-azure-architecture.md)
* Hol találom a konfigurációs kiszolgáló legújabb verzióját?

    A konfigurációs kiszolgáló portálon keresztüli frissítésének [lépéseit a Konfigurációs kiszolgáló frissítése című](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)témakörben található. Az összes hely-helyreállítási összetevő frissítéséről a [Webhely-helyreállítás szolgáltatásfrissítései (Site Recovery) témakörben](https://aka.ms/asr_how_to_upgrade)talál.
* Hol tölthetem le a konfigurációs kiszolgáló jelmondatát?

    A jelszó letöltéséhez olvassa [el a VMware VM vész-helyreállítási konfigurációs kiszolgálójának kezelése című témakört.](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)
* Megváltoztathatom a jelszót?

    Nem. Ne módosítsa a konfigurációs kiszolgáló jelmondatát. A jelszó módosítása megszakítja a védett gépek replikációját, és kritikus állapothoz vezet.
* Hol tölthetem le a trezor regisztrációs kulcsait?

    A **Recovery Services Vault alkalmazásban**válassza a**Hely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálóinak** **kezelése** > lehetőséget. A **Kiszolgálók alkalmazásban**válassza **a Regisztrációs kulcs letöltése** lehetőséget a tároló hitelesítő fájljának letöltéséhez.
* Klónozhatok egy meglévő konfigurációs kiszolgálót, és használhatom replikációs vezénylésre?

    Nem. A klónozott konfigurációs kiszolgálóösszetevő használata nem támogatott. A kibővített folyamatkiszolgáló klónozása szintén nem támogatott forgatókönyv. A Site Recovery összetevőinek klónozása hatással van a folyamatban lévő replikációkra.

* Módosíthatom a konfigurációs kiszolgáló IP-címét?

    Nem. Ne módosítsa a konfigurációs kiszolgáló IP-címét. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt összes IP-cím statikus IP-cím, nem pedig DHCP IP-cím.
* Beállíthatok egy konfigurációs kiszolgálót az Azure-ban?

    Hozzon létre egy konfigurációs kiszolgálót helyszíni környezetben, közvetlen látótávolsággal a v-centerrel, és minimalizálja az adatátviteli késéseket. A konfigurációs kiszolgáló ütemezett biztonsági másolatait [feladat-visszavétel céljából készítheti](vmware-azure-manage-configuration-server.md#failback-requirements)el.

* Módosítható a gyorsítótár-illesztőprogram konfigurációs kiszolgálón vagy kibővített folyamatkiszolgálón?

    Nem, a gyorsítótár-illesztőprogram nem módosítható a beállítás befejezése után.

A konfigurációs kiszolgálókkal kapcsolatos további gyakori kérdésekért olvassa el a [Konfigurációs kiszolgáló gyakori kérdései](vmware-azure-common-questions.md#configuration-server).

## <a name="next-steps"></a>További lépések

Állítsa be a [VMware virtuális gépek](vmware-azure-tutorial.md) vészhelyreállítását az Azure-ba.
