---
title: Az Azure VM-replikáció – problémamegoldás az Azure Site Recovery szolgáltatásban
description: Hibák elhárítása, amikor az Azure virtuális gépek replikálása a vész-helyreállítási hibák.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: c131609a5622061e2ea49db422bc4e9da96666d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276682"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Az Azure-ból Azure-ba irányuló virtuális gép replikációs hibáinak elhárítása

Ez a cikk ismerteti, hogyan hárítsa el az Azure Site Recovery gyakori hibáit az Azure virtuális gépek (VM-ek) egyik régióból a másikba történő replikációja és helyreállítása során. A támogatott konfigurációkról további információt az [Azure virtuális gépek replikálásának támogatási mátrixában talál.](site-recovery-support-matrix-azure-to-azure.md)

## <a name="azure-resource-quota-issues-error-code-150097"></a><a name="azure-resource-quota-issues-error-code-150097"></a>Az Azure-erőforrások kvótájával kapcsolatos problémák (hibakód: 150097)

Győződjön meg arról, hogy az előfizetés engedélyezve van az Azure-beli virtuális gépek létrehozásához a vész-helyreállítási régióként használni kívánt célrégióban. Győződjön meg arról is, hogy az előfizetés rendelkezik-e elegendő kvótával a szükséges méretű virtuális gépek létrehozásához. Alapértelmezés szerint a Site Recovery kiválasztja a cél virtuális gép méretét, amely megegyezik a forrás virtuális gép mérete. Ha a megfelelő méret nem érhető el, a Site Recovery automatikusan kiválasztja a legközelebbi elérhető méretet.

Ha nincs olyan méret, amely támogatja a forrás virtuálisgép-konfigurációt, a következő üzenet jelenik meg:

> "A replikáció nem engedélyezhető a Virtuális gép *VmName*számára."

### <a name="possible-causes"></a>Lehetséges okok

- Az előfizetés-azonosító nincs engedélyezve, hogy hozzon létre virtuális gépeket a célrégióban helyen.
- Az előfizetés-azonosító nincs engedélyezve, vagy nem rendelkezik elegendő kvótával, hogy hozzon létre adott virtuális gép méretek a célrégióban helyen.
- Nincs megfelelő cél virtuális gép mérete található, hogy megfeleljen a forrás virtuális gép hálózati adapter kártya (NIC) száma (2), az előfizetés-azonosító a célrégióban helyen.

### <a name="fix-the-problem"></a>A probléma javítása

Lépjen kapcsolatba az [Azure számlázási támogatásával,](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) hogy az előfizetés a szükséges méretű virtuális gépeket hozhassa létre a célhelyen. Ezután próbálkozzon újra a sikertelen művelettel.

Ha a célhely kapacitásmegkötéssel rendelkezik, tiltsa le a replikációt. Ezután engedélyezze a replikációt egy másik helyre, ahol az előfizetés elegendő kvótával rendelkezik a szükséges méretű virtuális gépek létrehozásához.

## <a name="trusted-root-certificates-error-code-151066"></a><a name="trusted-root-certificates-error-code-151066"></a>Megbízható főtanúsítványok (hibakód: 151066)

Ha nem a legújabb megbízható főtanúsítványok vannak jelen a virtuális gép, a "Replikáció engedélyezése" Site Recovery feladat sikertelen lehet. A hely-helyreállítási szolgáltatás virtuális gépről érkező hívásainak hitelesítése és engedélyezése nem sikerül ezek nélkül a tanúsítványok nélkül.

Ha a "Replikáció engedélyezése" feladat sikertelen, a következő üzenet jelenik meg:

> "A Site Recovery konfigurációja nem sikerült."

### <a name="possible-cause"></a>Lehetséges ok

Az engedélyezéshez és hitelesítéshez szükséges megbízható főtanúsítványok nincsenek jelen a virtuális gépen.

### <a name="fix-the-problem"></a>A probléma javítása

#### <a name="windows"></a>Windows

A Windows operációs rendszert futtató virtuális gép esetén telepítse a legújabb Windows-frissítéseket a virtuális gépre, hogy az összes megbízható főtanúsítvány jelen van a számítógépen. Kövesse a windows-frissítés-kezelési vagy tanúsítványfrissítés-kezelési folyamat tipikus folyamatát a szervezetben a legújabb főtanúsítványok és a frissített tanúsítvány-visszavonási lista lekéréséhez a virtuális gépeken.

Ha leválasztott környezetben van, kövesse a windowsos frissítési folyamatot a szervezetben a tanúsítványok beszerzéséhez. Ha a szükséges tanúsítványok nem szerepelnek a virtuális gépen, a Site Recovery szolgáltatás hívásai biztonsági okokból sikertelenek lesznek.

Annak ellenőrzéséhez, hogy a probléma megoldódott-e, nyissa meg a login.microsoftonline.com a virtuális gép böngészőjéből.

További információt a [Megbízható gyökerek és a nem engedélyezett tanúsítványok konfigurálása című témakörben talál.](https://technet.microsoft.com/library/dn265983.aspx)

#### <a name="linux"></a>Linux

Kövesse a Linux operációs rendszer verziójának forgalmazója által adott útmutatást a legújabb megbízható főtanúsítványok és a virtuális gép legújabb tanúsítvány-visszavonási listájának lekéréséhez.

Mivel a SUSE Linux szimbolikus hivatkozásokat (vagy *symlinkseket)* használ a tanúsítványok listájának fenntartásához, kövesse az alábbi lépéseket:

1. Jelentkezzen be gyökérfelhasználóként.

1. A címtár módosításához futtassa ezt a parancsot:

    **# cd /etc/ssl/certs**

1. Ellenőrizze, hogy a Symantec legfelső szintű hitelesítésszolgáltatótanúsítvány a következő:

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Ha a Symantec legfelső szintű hitelesítésszolgáltatói tanúsítványa nem található, futtassa a következő parancsot a fájl letöltéséhez. Ellenőrizze a hibákat, és kövesse a hálózati hibák ra vonatkozó ajánlott műveleteket.

    **# wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Ellenőrizze, hogy a baltimore-i legfelső szintű hitelesítésszolgáltató tanúsítvány a következő:

    **# Baltimore_CyberTrust_Root.pem**

1. Ha a baltimore-i legfelső szintű hitelesítésszolgáltató tanúsítványa nem található, futtassa ezt a parancsot a tanúsítvány letöltéséhez:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. Ellenőrizze, hogy a DigiCert_Global_Root_CA tanúsítvány a következő:

    **# DigiCert_Global_Root_CA.pem**

1. Ha a DigiCert_Global_Root_CA nem található, futtassa a következő parancsokat a tanúsítvány letöltéséhez:

    **# wgethttp://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem**

1. Futtassa a rehash parancsfájlt az újonnan letöltött tanúsítványok tanúsítványtulajdonosi kivonatainak frissítéséhez:

    **# c_rehash**

1. Futtassa ezeket a parancsokat annak ellenőrzéséhez, hogy a tulajdonos imázsként létrehozott-e kivonatokat a tanúsítványokhoz:

    - Parancs:

        **# ls -l | grep Baltimore között**

    - Kimenet:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Parancs:

        **# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - Kimenet:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Parancs:

        **# ls -l | grep DigiCert_Global_Root**

    - Kimenet:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. A VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem fájl másolatának létrehozása b204d74a.0 fájlnévvel:

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. A Baltimore_CyberTrust_Root.pem fájl másolatának létrehozása 653b494a.0 fájlnévvel:

    **# cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. A DigiCert_Global_Root_CA.pem fájl másolatának létrehozása a 3513523f.0 fájlnévvel:

    **# cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. Ellenőrizze, hogy a fájlok jelen vannak-e:

    - Parancs:

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - Kimenet

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő kapcsolat a hely-helyreállítási URL-címekhez vagy IP-tartományokhoz (hibakód: 151037 vagy 151072)

Ahhoz, hogy a Site Recovery replikációja működjön, a virtuális géptől adott URL-címekhez kimenő kapcsolat szükséges. Ha a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályokat használ a kimenő kapcsolatok szabályozására, előfordulhat, hogy az alábbi problémák egyikével szembesülhet. Kérjük, vegye figyelembe, hogy bár továbbra is támogatjuk az URL-eken keresztüli kimenő hozzáférést, az IP-tartományok engedélyezési listájának használata már nem támogatott.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>1. probléma: Nem sikerült regisztrálni az Azure virtuális gépet a Site Recovery szolgáltatással (151195) </br>
- **Lehetséges ok** </br>
  - Dns-feloldási hiba miatt nem lehet kapcsolatot létesíteni a hely-helyreállítási végpontokkal.
  - Ez gyakrabban fordul elő ismételt védelem esetén, amikor feladatátvételt végzett a virtuális gépen, de a DNS-kiszolgáló nem érhető el a vészhelyreállítási régióból.

- **Resolution** (Osztás)
   - Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a Vész-helyreállítási régióból. Annak ellenőrzéséhez, hogy van-e egyéni DNS-e, látogasson el a virtuális gép> vész-helyreállítási hálózat> DNS-kiszolgálók. Próbálja meg elérni a DNS-kiszolgálót a virtuális gépről. Ha nem érhető el, akkor tegye elérhetővé a DNS-kiszolgáló feletti hiba, vagy a DR-hálózat és a DNS közötti helyvonal létrehozásával.

    ![com-hiba](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>2. probléma: A hely-helyreállítási konfiguráció nem sikerült (151196)
- **Lehetséges ok** </br>
  - Nem lehet kapcsolatot létesíteni az Office 365 hitelesítési és IP4-identitás-végpontjaival.

- **Resolution** (Osztás)
  - Az Azure Site Recovery hozzáféréssel rendelkezik az Office 365 IP-kiszolgálóihoz a hitelesítéshez.
    Ha az Azure Network Security Group (NSG) szabályok/tűzfal proxy a virtuális gép kimenő hálózati kapcsolatának szabályozásához, győződjön meg arról, hogy [az Azure Active Directory (AAD) szolgáltatáscímke-alapú](../virtual-network/security-overview.md#service-tags) NSG-szabályt használja az AAD-hoz való hozzáférés engedélyezéséhez. Már nem támogatjuk az IP-címalapú NSG-szabályokat.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>3. probléma: A hely-helyreállítási konfiguráció nem sikerült (151197)
- **Lehetséges ok** </br>
  - Nem hozható létre kapcsolat az Azure Site Recovery szolgáltatás végpontjaihoz.

- **Resolution** (Osztás)
  - Ha az Azure Network Security Group (NSG) szabályok/tűzfal proxy a kimenő hálózati kapcsolat a virtuális gép, kérjük, győződjön meg róla, hogy a szolgáltatás címkék használata. Már nem támogatjuk az IP-címek engedélyezési listájának használatát az Azure Site Recovery (ASR) NSG-ken keresztül.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>4. probléma: Az A2A replikációja nem sikerült, amikor a hálózati forgalom a helyszíni proxykiszolgálón keresztül megy (151072)
 - **Lehetséges ok** </br>
   - Az egyéni proxybeállítások érvénytelenek, és az ASR Mobility Service ügynök nem észlelte automatikusan az IE proxybeállításait


 - **Resolution** (Osztás)
   1.    A Mobility Service agent észleli az IE proxybeállításait Windows rendszeren és /etc/environment Linuxon.
   2.  Ha csak az ASR Mobility Service proxyját szeretné beállítani, akkor a proxy részleteit a ProxyInfo.conf fájlban adja meg, amely a következő helyen található:</br>
       - ``/usr/local/InMage/config/``***Linuxon***
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``***Windows*** rendszeren
   3.    A ProxyInfo.conf a proxybeállításokat a következő INI-formátumban tárolja.</br>
                   *[proxy]*</br>
                   *Cím =http://1.2.3.4*</br>
                   *Port=567*</br>
   4. Az ASR Mobility Service ügynök csak ***a nem hitelesített proxykat***támogatja.


### <a name="fix-the-problem"></a>A probléma javítása

Ha hozzá szeretné adni [a szükséges URL-címeket](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) egy engedélyezési listához, kövesse a [hálózati útmutató dokumentum lépéseit.](site-recovery-azure-to-azure-networking-guidance.md)


### <a name="more-information"></a>További információ

A [szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy [a szükséges IP-tartományok](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)megadásához kövesse a [Hálózatkezelés az Azure-ban az Azure replikációja](site-recovery-azure-to-azure-networking-guidance.md)című útmutatót.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>A lemez nem található a készülékben (hibakód: 150039)

A virtuális géphez csatlakoztatott új lemezt inicializálni kell. Ha a lemez nem található, a következő üzenet jelenik meg:

> "A *DiskName* DiskURI logikai egységszámmal rendelkező *DiskNAME* *DiskURI* adatlemez nem lett leképezve az azonos logikai értékkel rendelkező virtuális gépről jelentett megfelelő lemezre. *LUNValue*

### <a name="possible-causes"></a>Lehetséges okok

- Egy új adatlemez lett csatolva a virtuális géphez, de nem inicializálva.
- A virtuális gépben lévő adatlemez nem megfelelően jelenti azt a logikai egységszámot (LUN) értéket, amelyen a lemez a virtuális géphez lett csatlakoztatva.

### <a name="fix-the-problem"></a>A probléma javítása

Győződjön meg arról, hogy az adatlemezek inicializálódnak, majd próbálkozzon újra a művelettel.

- **Windows**: [Új lemez csatolása és inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [Új adatlemez inicializálása Linux alatt](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Egy vagy több lemez áll rendelkezésre védelemre (hibakód: 153039)

### <a name="possible-causes"></a>Lehetséges okok

- Egy vagy több lemez a közelmúltban került a virtuális gépre a védelem után.
- Egy vagy több lemez inicializálása a virtuális gép védelme után történt.

### <a name="fix-the-problem"></a>A probléma javítása

Ha ismét kifogástalanállapotot szeretne a virtuális gép replikációs állapotának, választhata a lemezek védelmét, vagy elveti a figyelmeztetést.

#### <a name="to-protect-the-disks"></a>A lemezek védelme

1. Nyissa meg a **Replikált elemek** > *virtuálisgép-neve* > **Lemezek című**területet.
1. Jelölje ki a nem védett lemezt, majd válassza **a Replikáció engedélyezése**lehetőséget:

    ![Replikáció engedélyezése virtuálisgép-lemezeken](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>A figyelmeztetés elvetése

1. Nyissa meg a **Replikált elemek** > *virtuális gépének nevét.*
1. Jelölje ki a figyelmeztetést az **Áttekintés** szakaszban, majd kattintson az **OK gombra.**

    ![Új lemez figyelmeztetés elvetése](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>A virtuális gép eltávolítása a tárolóból, amely információkkal van kiegészítve (hibakód: 150225)

Amikor védi a virtuális gépet, a Site Recovery létrehoz néhány kapcsolatot a forrás virtuális gépen. A védelem eltávolításakor vagy a replikáció letiltásakor a Site Recovery a törlési feladat részeként eltávolítja ezeket a hivatkozásokat. Ha a virtuális gép rendelkezik erőforrás-zárolással, a törlési feladat befejeződik az adatokkal. Az információ azt mondja, hogy a virtuális gép el lett távolítva a Recovery Services tárolójából, de az elavult kapcsolatok egy része nem tisztítható meg a forrásgépen.

Figyelmen kívül hagyhatja ezt a figyelmeztetést, ha soha nem kívánja megvédeni ezt a virtuális gépet újra. De ha később meg kell védenie ezt a virtuális gépet, kövesse a "Probléma megoldása" című részt a hivatkozások megtisztításához.

> [!WARNING]
> Ha nem végez karbantartást:
>
> - Ha engedélyezi a replikációt a Recovery Services tárolón, a virtuális gép nem jelenik meg.
> - Ha a virtuális gépet a **virtuális gép** > beállításai**vész-helyreállítási****Settings** > használatával próbálja védeni, a művelet sikertelen lesz a "Replikáció nem engedélyezhető a virtuális gépen meglévő elavult erőforrás-kapcsolatok miatt" üzenettel.

### <a name="fix-the-problem"></a>A probléma javítása

> [!NOTE]
> A Site Recovery nem törli a forrás virtuális gépet, és semmilyen módon nem befolyásolja azt a lépések végrehajtása közben.

1. Távolítsa el a zárolást a virtuális gép vagy a virtuális gép erőforráscsoportból. A következő képen például törölni kell a "MoveDemo" nevű virtuális gép erőforrászárolását:

    ![Zárolás eltávolítása a virtuális gépről](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. A parancsfájl letöltésével [távolítsa el az elavult site recovery konfigurációt.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Futtassa a parancsfájlt, amely a cleanup-elavult-asr-config-Azure-VM.ps1. Adja meg az előfizetés-azonosítót, a virtuálisgép-erőforráscsoportot és a virtuális gép nevét paraméterekként.
1. Ha a rendszer Azure-hitelesítő adatokat kér, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hiba nélkül fut-e.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>A replikáció nem engedélyezhető a virtuális gép elavult erőforrás-kapcsolatai miatt (hibakód: 150226)

### <a name="possible-cause"></a>Lehetséges ok

A virtuális gép elavult konfigurációval rendelkezik a korábbi Site Recovery védelemből.

Egy elavult konfiguráció előfordulhat egy Azure-beli virtuális gép, ha engedélyezte a replikációt az Azure virtuális gép a Site Recovery használatával, majd:

- Letiltotta a replikációt, de a forrás virtuális gép erőforrás-zárolással rendelkezett.
- A site recovery tároló tana nélkül explicit módon letiltása replikáció a virtuális gép.
- A site recovery-tárolót tartalmazó erőforráscsoportot törölte anélkül, hogy kifejezetten letiltana a replikációt a virtuális gépen.

### <a name="fix-the-problem"></a>A probléma javítása

> [!NOTE]
> A Site Recovery nem törli a forrás virtuális gépet, és semmilyen módon nem befolyásolja azt a lépések végrehajtása közben.

1. Távolítsa el a zárolást a virtuális gép vagy a virtuális gép erőforráscsoportból. A következő képen például törölni kell a "MoveDemo" nevű virtuális gép erőforrászárolását:

    ![Zárolás eltávolítása a virtuális gépről](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. A parancsfájl letöltésével [távolítsa el az elavult site recovery konfigurációt.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Futtassa a parancsfájlt, amely a cleanup-elavult-asr-config-Azure-VM.ps1. Adja meg az előfizetés-azonosítót, a virtuálisgép-erőforráscsoportot és a virtuális gép nevét paraméterekként.
1. Ha a rendszer Azure-hitelesítő adatokat kér, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hiba nélkül fut-e.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Nem található az Azure virtuális gép vagy erőforráscsoport a "Replikáció engedélyezése" feladat kiválasztásához

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>1. ok: Az erőforráscsoport és a forrás virtuális gép különböző helyeken található

A Site Recovery jelenleg megköveteli, hogy a forrásrégió erőforráscsoportja és a virtuális gépek ugyanazon a helyen legyenek. Ha nem, akkor nem fogja tudni megtalálni a virtuális gépet vagy erőforráscsoportot, amikor védelmet próbál alkalmazni.

Kerülő megoldásként engedélyezheti a replikációt a virtuális gépről a Recovery Services-tároló helyett. Nyissa meg a **Forrás virtuálisgép** > **tulajdonságai** > **vész-helyreállítási** és a replikáció engedélyezése.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>2. ok: Az erőforráscsoport nem része a kijelölt előfizetésnek

Előfordulhat, hogy nem találja az erőforráscsoportot a védelem időpontjában, ha az erőforráscsoport nem része a kijelölt előfizetésnek. Győződjön meg arról, hogy az erőforráscsoport a használt előfizetéshez tartozik.

### <a name="cause-3-stale-configuration"></a>3. ok: Elavult konfiguráció

Előfordulhat, hogy nem jelenik meg a virtuális gép, amely engedélyezni kívánt replikációra, ha egy elavult hely-helyreállítási konfiguráció maradt az Azure virtuális gép. Ez a feltétel akkor fordulhat elő, ha engedélyezte a replikációt az Azure virtuális gép a Site Recovery használatával, majd:

- A site recovery tároló tana nélkül explicit módon letiltása replikáció a virtuális gép.
- A site recovery-tárolót tartalmazó erőforráscsoportot törölte anélkül, hogy kifejezetten letiltana a replikációt a virtuális gépen.
- Letiltotta a replikációt, de a forrás virtuális gép erőforrás-zárolással rendelkezett.

### <a name="fix-the-problem"></a>A probléma javítása

> [!NOTE]
> Győződjön meg arról, hogy frissítse az "AzureRM.Resources" modult, mielőtt a jelen szakaszban említett parancsfájlt használna.  A Site Recovery nem törli a forrás virtuális gépet, és semmilyen módon nem befolyásolja azt a lépések végrehajtása közben.

1. Távolítsa el a zárolást, ha van ilyen, a virtuális gép vagy a virtuális gép erőforráscsoportból. A következő képen például törölni kell a "MoveDemo" nevű virtuális gép erőforrászárolását:

    ![Zárolás eltávolítása a virtuális gépről](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. A parancsfájl letöltésével [távolítsa el az elavult site recovery konfigurációt.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Futtassa a parancsfájlt, amely a cleanup-elavult-asr-config-Azure-VM.ps1. Adja meg az előfizetés-azonosítót, a virtuálisgép-erőforráscsoportot és a virtuális gép nevét paraméterekként.
1. Ha a rendszer Azure-hitelesítő adatokat kér, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hiba nélkül fut-e.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Nem lehet kijelölni egy virtuális gépet a védelemhez

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>1. ok: A virtuális gép egy bővítmény telepítése sikertelen vagy nem válaszoló állapotban van

Nyissa meg a **Virtuálisgépek** > **beállítási** > **bővítményeit,** és ellenőrizze, hogy vannak-e sikertelen állapotú bővítmények. Távolítsa el a sikertelen bővítményeket, majd próbálkozzon újra a virtuális gép védelmével.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>2. ok: A virtuális gép létesítési állapota érvénytelen

Tekintse meg a hibaelhárítási lépéseket [a virtuális gép létesítési állapota nem érvényes,](#the-vms-provisioning-state-is-not-valid-error-code-150019)a cikk későbbi részében.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>A virtuális gép létesítési állapota érvénytelen (hibakód: 150019)

A virtuális gép replikációjának engedélyezéséhez a létesítési állapotnak **sikeresnek**kell lennie. A kiépítési állapot ellenőrzéséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az **Erőforrás-kezelőt** az **Összes szolgáltatásból.**
1. Bontsa ki az **Előfizetések** listát, és válassza ki az előfizetést.
1. Bontsa ki a **ResourceGroups listát,** és válassza ki a virtuális gép erőforráscsoportját.
1. Bontsa ki az **erőforrások listáját,** és válassza ki a virtuális gép.
1. Ellenőrizze a **kiépítésállapot** mezőjét a jobb oldali Példány nézetben.

### <a name="fix-the-problem"></a>A probléma javítása

- Ha **a provisioningState** **sikertelen,** lépjen kapcsolatba az ügyfélszolgálattal a hibaelhárításhoz.
- Ha **a provisioningState** **frissítése**van, egy másik bővítmény is üzembe helyezhető. Ellenőrizze, hogy vannak-e folyamatban lévő műveletek a virtuális gépen, várja meg, amíg befejeződnek, majd próbálkozzon újra a sikertelen site recovery "Replikáció engedélyezése" feladattal.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Nem lehet kiválasztani a cél virtuális gépét (a hálózat kijelölése lap nem érhető el)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>1. ok: A virtuális gép olyan hálózathoz van csatlakoztatva, amely már le van képezve egy célhálózathoz

Ha a forrás virtuális gép egy virtuális hálózat része, és egy másik virtuális gép ugyanabból a virtuális hálózatból már le van képezve a célerőforrás-csoportban lévő hálózattal, a hálózatkijelölés legördülő lista alapértelmezés szerint nem érhető el (halványan jelenik meg).

![A hálózatkijelölési lista nem érhető el](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>2. ok: Korábban a Site Recovery használatával védte a virtuális gép, majd letiltotta a replikációt

A virtuális gép replikációjának letiltása nem törli a hálózati hozzárendelést. A leképezést törölni kell a Recovery Services tárolóból, ahol a virtuális gép védett volt. Nyissa meg a *Helyreállítási szolgáltatások tárolója* > **helyhelyreállítási infrastruktúra** > **hálózati leképezése webhelyre való ugrását.**

![Hálózati hozzárendelés törlése](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

A vész-helyreállítási telepítés során konfigurált célhálózat a kezdeti telepítés után, a virtuális gép védelme után módosítható:

![Hálózati hozzárendelés módosítása](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Vegye figyelembe, hogy a hálózati leképezés módosítása hatással van az összes védett virtuális gépre, amely ugyanazt a hálózati hozzárendelést használja.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ vagy Kötet árnyékmásolata szolgáltatás hiba (hibakód: 151025)

A hiba bekövetkeztekor a következő üzenet jelenik meg:

> "A Site Recovery bővítmény telepítése nem sikerült"

### <a name="possible-causes"></a>Lehetséges okok

- A COM+ rendszeralkalmazás szolgáltatás le van tiltva.
- A Kötet árnyékmásolata szolgáltatás le van tiltva.

### <a name="fix-the-problem"></a>A probléma javítása

Állítsa a COM+ rendszeralkalmazás és a Kötet árnyékmásolat a szolgáltatást automatikus vagy manuális indítási módba.

1. Nyissa meg a Szolgáltatások konzolt a Windows rendszerben.
1. Győződjön meg arról, hogy a COM+ rendszeralkalmazás és a Kötet árnyékmásolata szolgáltatás **indítási típusaként**nincs **Letiltva** értékre állítva.

    ![A COM+ rendszeralkalmazás és a kötet árnyékmásolata szolgáltatások indítási típusának ellenőrzése](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nem támogatott felügyelt lemezméret (hibakód: 150172)

A hiba bekövetkeztekor a következő üzenet jelenik meg:

> "A védelem nem engedélyezhető a virtuális gépen, mivel *diskname* méretű *DiskSize*)* kisebb, mint a minimálisan támogatott méret 1024 MB."

### <a name="possible-cause"></a>Lehetséges ok

A lemez kisebb, mint a támogatott 1024 MB-os méret.

### <a name="fix-the-problem"></a>A probléma javítása

Győződjön meg arról, hogy a lemez mérete a támogatott mérettartományon belül van, majd próbálkozzon újra a művelettel.

## <a name="protection-was-not-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a><a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>A védelem nem volt engedélyezve, mert a GRUB-konfiguráció az UUID helyett az eszköz nevét tartalmazza (hibakód: 151126)

### <a name="possible-cause"></a>Lehetséges ok

A Linux GRUB konfigurációs fájlok (/boot/grub/menu.lst", /boot/grub/grub.cfg, /boot/grub2/grub.cfg vagy /etc/default/grub) a *gyökér* és a *folytatás* paramétereinek UUID értékei helyett a tényleges eszközneveket adhatják meg. A Site Recovery felhasználói felhasználói azonosítókat igényel, mert az eszköznevek változhatnak. Újraindításután előfordulhat, hogy a virtuális gép nem jön létre ugyanazt a nevet a feladatátvétel, ami problémákat eredményez.

A következő példák a GRUB-fájlok azon sorai, ahol az eszköznevek (félkövérrel felezve jelennek meg) a szükséges UUID-k helyett:

- Fájl /boot/grub2/grub.cfg

  > linux /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2** ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- Fájl: /boot/grub/menu.lst

  > kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314


### <a name="fix-the-problem"></a>A probléma javítása

Cserélje le az egyes eszközök nevét a megfelelő UUID azonosítóra:

1. Keresse meg az eszköz UUID azonosítóját a parancs **blkid** ***eszköznevének***végrehajtásával. Példa:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Cserélje le az eszköz nevét az UUID azonosítójára a **root=UUID**=*uuid* és **resume=UUID**=*UUID*formátumban. A csere után például a /boot/grub/menu.lst (korábban tárgyalt) sor a következőkre mutat:

    > kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID =6f614b444-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314

1. Próbálkozzon újra a védelemmel.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>A védelem engedélyezése nem sikerült, mert a GRUB-konfigurációban említett eszköz nem létezik (hibakód: 151124)

### <a name="possible-cause"></a>Lehetséges ok

A GRUB konfigurációs fájlok (/boot/grub/menu.lst, /boot/grub/grub.cfg, /boot/grub2/grub.cfg vagy /etc/default/grub) tartalmazhatják a *paramétereket rd.lvm.lv* vagy *rd_LVM_LV*. Ezek a paraméterek azonosítják a logikai kötetkezelő (LVM) eszközöket, amelyeket a rendszerindításkor fel kell deríteni. Ha ezek az LVM eszközök nem léteznek, maga a védett rendszer nem indul el, és beragad a rendszerindítási folyamatba. Ugyanez a probléma is látható a feladatátvevő virtuális gép. Íme néhány példa:

- Fájl: /boot/grub2/grub.cfg az RHEL7-en:

    > linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup-rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US. UTF-8

- Fájl: /etc/default/grub on RHEL7:

    > GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"

- Fájl: /boot/grub/menu.lst az RHEL6-on:

    > kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US. UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto **rd_LVM_LV=rootvg/lv_root** KEYBOARDTYPE=pc KEYTABLE=us **rd_LVM_LV=rootvg/lv_swap** rd_NO_DM rhgb quiet

Minden példában a félkövérrel szedett rész azt mutatja, hogy a GRUB-nak két LVM-eszközt kell észlelnie a "root" és a "swap" nevekkel a "rootvg" kötetcsoportból.

### <a name="fix-the-problem"></a>A probléma javítása

Ha az LVM-eszköz nem létezik, hozza létre, vagy távolítsa el a megfelelő paramétereket a GRUB konfigurációs fájlokból. Ezután próbálja meg újra engedélyezni a védelmet.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>A Site Recovery Mobility Service frissítése figyelmeztetésekkel fejeződött be (hibakód: 151083)

A Site Recovery Mobility Service számos összetevőből áll, amelyek közül az egyiket szűrő-illesztőprogramnak nevezik. A szűrőillesztő csak a rendszer újraindítása során töltődik be a rendszermemóriába. Amikor a Mobility Service frissítése szűrőillesztő-módosításokat tartalmaz, a számítógép frissül, de továbbra is megjelenik egy figyelmeztetés, hogy egyes javítások újraindítást igényelnek. A figyelmeztetés azért jelenik meg, mert a szűrőillesztő javításai csak az új szűrőillesztő betöltésekor lépnek érvénybe, ami csak újraindítás közben történik.

> [!NOTE]
> Ez csak egy figyelmeztetés. A meglévő replikáció az új ügynökfrissítés után is működik. Bármikor újraindíthatja az új szűrőillesztő előnyeit, de a régi szűrőillesztő továbbra is működik, ha nem indul újra.
>
> A szűrő-illesztőprogramon kívül a Mobilitási szolgáltatás frissítésében található egyéb fejlesztések és javítások előnyei újraindítás nélkül lépnek érvénybe.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>A védelem nem engedélyezhető, mert a replikánderes felügyelt lemez már létezik a várt címkék nélkül a célerőforráscsoportban (hibakód: 150161)

### <a name="possible-cause"></a>Lehetséges ok

Ez a probléma akkor fordulhat elő, ha a virtuális gép korábban védett volt, és a replikáció le van tiltva, a replikalemez nem lett megtisztítva.

### <a name="fix-the-problem"></a>A probléma javítása

Törölje a hibaüzenetben azonosított replikalemezt, és próbálkozzon újra a sikertelen védelmi feladatkal.

## <a name="next-steps"></a>További lépések

[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
