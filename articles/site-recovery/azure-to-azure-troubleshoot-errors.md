---
title: Azure-beli virtuális gépek replikálásának hibája Azure Site Recovery
description: Hibák elhárítása az Azure-beli virtuális gépek vész-helyreállításra való replikálásakor.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: 32d826f3c27cea3d0993c47e8562360315b7bd2e
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78256046"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Az Azure-ról az Azure-ba irányuló virtuális gépek replikációs hibáinak elhárítása

Ez a cikk az Azure Virtual Machines (VM) az egyik régióból a másikba történő replikációja és helyreállítása során Azure Site Recovery gyakori hibáinak elhárítását ismerteti. További információ a támogatott konfigurációkról: Azure-beli [virtuális gépek replikálásának támogatási mátrixa](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure-erőforrás-kvótával kapcsolatos problémák (hibakód: 150097)

Győződjön meg arról, hogy az előfizetése engedélyezve van olyan Azure-beli virtuális gépek létrehozásához, amelyeket vészhelyzeti helyreállítási régióként kíván használni. Győződjön meg arról is, hogy az előfizetése elegendő kvótával rendelkezik a szükséges méretű virtuális gépek létrehozásához. Alapértelmezés szerint a Site Recovery kiválasztja a virtuálisgép-mérethez hasonló cél virtuális gép méretét. Ha az egyező méret nem érhető el, Site Recovery automatikusan kiválasztja a legközelebbi elérhető méretet.

Ha nincs olyan méret, amely támogatja a forrás virtuális gép konfigurációját, a következő üzenet jelenik meg:

> "A virtuális gép *VmName*nem sikerült engedélyezni a replikációt."

### <a name="possible-causes"></a>Lehetséges okok

- Az előfizetés-azonosító nincs engedélyezve a célként megadott régióban található virtuális gépek létrehozásához.
- Az előfizetés-azonosító nincs engedélyezve, vagy nem rendelkezik elegendő kvótával a virtuálisgép-méretek létrehozásához a célként megadott régióban.
- Nem található megfelelő célként megadott virtuálisgép-méret a forrás virtuális gép hálózati adapterének (NIC) számával (2), az előfizetés-AZONOSÍTÓhoz a célként megadott régióban.

### <a name="fix-the-problem"></a>A probléma megoldása

Lépjen kapcsolatba az [Azure számlázási támogatási szolgálatával](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) , és engedélyezze, hogy előfizetése virtuális gépeket hozzon létre a célhelyen a szükséges méretekben. Ezután próbálja megismételni a sikertelen műveletet.

Ha a célhely kapacitása korlátozást tartalmaz, tiltsa le a replikációt. Ezután engedélyezze a replikálást egy másik helyre, ahol az előfizetése elegendő kvótával rendelkezik a szükséges méretű virtuális gépek létrehozásához.

## <a name="trusted-root-certificates-error-code-151066"></a>Megbízható legfelső szintű tanúsítványok (hibakód: 151066)

Ha nem minden legújabb megbízható főtanúsítvány van jelen a virtuális gépen, akkor a "replikáció engedélyezése" Site Recovery feladat sikertelen lehet. A virtuális gépről érkező Site Recovery szolgáltatás-hívások hitelesítése és engedélyezése a tanúsítványok nélkül meghiúsul. 

Ha a "replikáció engedélyezése" művelet meghiúsul, a következő üzenet jelenik meg:

> "Site Recovery konfiguráció nem sikerült."

### <a name="possible-cause"></a>Lehetséges ok

Az engedélyezéshez és a hitelesítéshez szükséges megbízható főtanúsítványok nem jelennek meg a virtuális gépen.

### <a name="fix-the-problem"></a>A probléma megoldása

#### <a name="windows"></a>Windows

A Windows operációs rendszert futtató virtuális gépek esetében telepítse a legújabb Windows-frissítéseket a virtuális gépre, hogy az összes megbízható főtanúsítvány megtalálható legyen a gépen. A szervezeten belüli, a Windows Update-Management vagy a Certificate Update-Management folyamatot követve szerezheti be a legfelső szintű tanúsítványokat és a frissített tanúsítvány-visszavonási listát a virtuális gépeken.

Ha Ön leválasztott környezetben, hajtsa végre a szabványos Windows frissítési folyamatot a szervezet tanúsítványok beolvasása. Ha a szükséges tanúsítványok nem megtalálható a virtuális Gépen, a Site Recovery szolgáltatás felé irányuló biztonsági okokból nem sikerült.

Győződjön meg arról, hogy a probléma megoldódott, lépjen a virtuális gépen egy böngészőből login.microsoftonline.com.

További információ: [megbízható gyökerek és nem engedélyezett tanúsítványok konfigurálása](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Kövesse a Linux operációs rendszer verziójának forgalmazója által biztosított útmutatást a legújabb megbízható főtanúsítványok és a tanúsítvány-visszavonási lista a virtuális gépen való lekéréséhez.

Mivel a SUSE Linux szimbolikus hivatkozásokat (vagy *symlinkeket*) használ a tanúsítványok listájának karbantartásához, kövesse az alábbi lépéseket:

1. Jelentkezzen be gyökérszintű felhasználóként.

1. A következő parancs futtatásával módosítsa a könyvtárat:

    **# CD/etc/SSL/certs**

1. Győződjön meg arról, hogy a Symantec legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa megtalálható-e:

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5. PEM**

1. Ha a Symantec legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa nem található, futtassa a következő parancsot a fájl letöltéséhez. Keressen hibákat, és kövesse az ajánlott műveleteket a hálózati hibákhoz.

    **# wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem-O VeriSign_Class_3_Public_Primary_Certification_Authority_G5. PEM**

1. Győződjön meg arról, hogy a Baltimore legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa megtalálható-e:

    **# ls Baltimore_CyberTrust_Root. PEM**

1. Ha a Baltimore legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa nem található, futtassa ezt a parancsot a tanúsítvány letöltéséhez:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem-O Baltimore_CyberTrust_Root. PEM**

1. Győződjön meg arról, hogy az DigiCert_Global_Root_CA-tanúsítvány megtalálható:

    **# ls DigiCert_Global_Root_CA. PEM**

1. Ha a DigiCert_Global_Root_CA nem található, futtassa a következő parancsokat a tanúsítvány letöltéséhez:

    **# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# OpenSSL x509 – a DigiCertGlobalRootCA. CRT-ben – tájékoztassa az t-t a PEM-ből DigiCert_Global_Root_CA. PEM**

1. Az újrakivonatoló parancsfájl futtatásával frissítse a tanúsítvány tulajdonosának kivonatait az újonnan letöltött tanúsítványokhoz:

    **# c_rehash**

1. Futtassa ezeket a parancsokat annak megállapításához, hogy a tulajdonos-kivonatok létre lettek-e hozva a tanúsítványokhoz:

    - Parancs

        **# ls-l | GREP Baltimore**

    - Kimenet:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Parancs

        **# ls-l | GREP VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - Kimenet:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Parancs

        **# ls-l | GREP DigiCert_Global_Root**

    - Kimenet:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Hozzon létre egy másolatot a fájlról VeriSign_Class_3_Public_Primary_Certification_Authority_G5. PEM fájlnév b204d74a. 0 fájllal:

    **# CP VeriSign_Class_3_Public_Primary_Certification_Authority_G5. PEM b204d74a. 0**

1. Hozzon létre egy másolatot a fájlról Baltimore_CyberTrust_Root. PEM fájlnév 653b494a. 0 fájllal:

    **# CP Baltimore_CyberTrust_Root. PEM 653b494a. 0**

1. Hozzon létre egy másolatot a fájlról DigiCert_Global_Root_CA. PEM fájlnév 3513523f. 0 fájllal:

    **# CP DigiCert_Global_Root_CA. PEM 3513523f. 0**

1. Győződjön meg arról, hogy a fájlok jelen vannak:

    - Parancs

        **# ls-l 653b494a. 0 b204d74a. 0 3513523f. 0**

    - Kimenet

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>A Site Recovery URL-címek vagy IP-címtartományokat (hibakód: 151037 vagy 151072) kimenő kapcsolatok

Site Recovery replikálás működéséhez a virtuális gépnek kimenő kapcsolatra van szüksége adott URL-címekre vagy IP-tartományokra. Ha a virtuális gép tűzfal mögött található, vagy használja a hálózati biztonsági csoport (NSG) szabályai kimenő kapcsolat szabályozásához, előfordulhat, hogy között ezek a problémák egyike.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>1. probléma: nem sikerült regisztrálni az Azure-beli virtuális gépet Site Recovery (hibakód: 151195)

#### <a name="possible-cause"></a>Lehetséges ok 

A DNS-feloldási hiba miatt nem lehet kapcsolódni Site Recovery végpontokhoz.

Ez a probléma leggyakrabban az ismételt védelem során fordul elő, ha a virtuális gép feladatátvétele megtörtént, de a DNS-kiszolgáló nem érhető el a vész-helyreállítási (DR) régióból.

#### <a name="fix-the-problem"></a>A probléma megoldása

Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a vész-helyreállítási régióból. Ha szeretné megtudni, hogy van-e egyéni DNS, a virtuális gépen lépjen a vész- *helyreállítási hálózat* > **DNS-kiszolgálók**elemre.

![Egyéni DNS-kiszolgálók listája](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

Próbálja meg elérni a DNS-kiszolgálót a virtuális gépről. Ha a kiszolgáló nem érhető el, a DNS-kiszolgáló meghibásodása vagy a DR hálózat és a DNS közötti hely létrehozásával teheti elérhetővé.

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>2\. probléma: Site Recovery konfiguráció nem sikerült (hibakód: 151196)

#### <a name="possible-cause"></a>Lehetséges ok

Nem lehet kapcsolódni az Office 365-hitelesítéshez és az Identity IP4-végpontokhoz.

#### <a name="fix-the-problem"></a>A probléma megoldása

A Site Recovery az Office 365 IP-tartományokhoz való hozzáférést igényel a hitelesítéshez.
Ha az Azure NSG-szabályokat vagy a tűzfal-proxyt használja a kimenő hálózati kapcsolat vezérlésére a virtuális gépen, ügyeljen arra, hogy engedélyezze a kommunikációt az Office 365 IP-tartományával. Hozzon létre egy NSG-szabályt egy [Azure Active Directory (Azure ad) szolgáltatás címkéje](../virtual-network/security-overview.md#service-tags)alapján, amely lehetővé teszi az Azure ad-hez tartozó összes IP-cím elérését. Ha a jövőben új címeket adnak hozzá az Azure AD-hoz, új NSG-szabályokat kell létrehoznia.

> [!NOTE]
> Ha a virtuális gépek *standard* belső terheléselosztó mögött vannak, a terheléselosztó alapértelmezés szerint nem fér hozzá az Office 365 IP-tartományokhoz (azaz login.microsoftonline.com). Módosítsa a belső terheléselosztó típusát *alapszintű* értékre, vagy hozzon létre kimenő hozzáférést a terheléselosztás [és a kimenő szabályok konfigurálása](https://aka.ms/lboutboundrulescli)című cikkben leírtak szerint.

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>3\. probléma: Site Recovery konfiguráció nem sikerült (hibakód: 151197)

#### <a name="possible-cause"></a>Lehetséges ok

Nem lehet létrehozni a kapcsolódást Site Recovery szolgáltatási végpontok számára.

#### <a name="fix-the-problem"></a>A probléma megoldása

A Site Recovery a régiótól függően [site Recovery IP-címtartományok](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)elérését igényli. Győződjön meg arról, hogy a szükséges IP-címtartományok elérhetők a virtuális gépről.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>4\. probléma: az Azure – Azure replikáció sikertelen volt, ha a hálózati forgalom egy helyszíni proxykiszolgálón halad át (151072-es hibakód)

#### <a name="possible-cause"></a>Lehetséges ok

Az egyéni proxybeállítások érvénytelenek, és a Site Recovery mobilitási szolgáltatás ügynöke nem tudta automatikusan felderíteni a proxybeállításokat az Internet Explorerben.

#### <a name="fix-the-problem"></a>A probléma megoldása

A mobilitási szolgáltatás ügynöke észleli az Internet Explorer proxybeállításait a Windowsban, illetve a Linux/etc/Environment.

Ha a proxyt csak a mobilitási szolgáltatáshoz szeretné beállítani, a ProxyInfo. conf fájlban adhatja meg a proxy részleteit a következő helyen:

- **Linux**:/usr/local/InMage/config/
- **Windows**: C:\ProgramData\Microsoft Azure site Recovery\Config

A ProxyInfo. conf fájlban adja meg a proxybeállításokat a következő inicializálási fájlformátumban:

> [*proxy*]

> Címe = *http://1.2.3.4*

> Port =*567*


> [!NOTE]
> A Site Recovery mobilitási szolgáltatás ügynöke csak a nem *hitelesített proxykat*támogatja.

### <a name="more-information"></a>További információ

A [szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) és a [szükséges IP-címtartományok](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)megadásához kövesse az [Azure-beli hálózatkezelés az Azure-ban való replikálásával foglalkozó témakör](site-recovery-azure-to-azure-networking-guidance.md)útmutatását.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>A lemez nem található a gépen (hibakód: 150039)

Egy új lemezt a virtuális Géphez csatolt inicializálni kell. Ha a lemez nem található, a következő üzenet jelenik meg:

> "Az Azure adatlemez- *DiskName* *DiskURI* és a logikai egységek száma *LUN* *LUNValue* nem volt leképezve egy, a virtuális gépen található, azonos LUN értékkel rendelkező lemezre.

### <a name="possible-causes"></a>Lehetséges okok

- Új adatlemez lett csatolva a virtuális géphez, de nem lett inicializálva.
- A virtuális gépen belüli adatlemez nem jelenti megfelelően a logikai egység számát (LUN), amelyen a lemez a virtuális géphez lett csatolva.

### <a name="fix-the-problem"></a>A probléma megoldása

Győződjön meg arról, hogy az adatlemezek inicializálva vannak, majd próbálja megismételni a műveletet.

- **Windows**: [új lemez csatolása és inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [új adatlemez inicializálása Linuxon](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Ha a probléma tartósan fennáll, forduljon az ügyfélszolgálathoz.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>Egy vagy több lemez védelem alatt áll (hibakód: 153039)

### <a name="possible-causes"></a>Lehetséges okok

- A védelem után egy vagy több lemezt nemrégiben adtak hozzá a virtuális géphez.
- Egy vagy több lemez inicializálása a virtuális gép védelme után történt.

### <a name="fix-the-problem"></a>A probléma megoldása

A virtuális gép replikálási állapotának újbóli megadásához választhatja a lemezek védelme vagy a figyelmeztetés mellőzése lehetőséget.

#### <a name="to-protect-the-disks"></a>A lemezek védelme

1. Nyissa meg a **replikált elemeket** > *virtuális gép nevét* > **lemezeket**.
1. Válassza ki a nem védett lemezt, majd válassza a **replikáció engedélyezése**lehetőséget:

    ![VIRTUÁLIS gépeken futó lemezek replikálásának engedélyezése](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>A figyelmeztetés mellőzése

1. Nyissa meg a **replikált elemeket** > *virtuális gép nevét*.
1. Válassza ki a figyelmeztetést az **Áttekintés** szakaszban, majd kattintson az **OK gombra**.

    ![Új lemezre vonatkozó figyelmeztetés mellőzése](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Távolítsa el a virtuális gépet a tárolóból, és információkkal fejeződött be (hibakód: 150225)

Ha megvédi a virtuális gépet, Site Recovery hoz létre néhány hivatkozást a forrás virtuális gépen. Ha eltávolítja a védelmet vagy letiltja a replikálást, Site Recovery eltávolítja ezeket a hivatkozásokat a karbantartási feladatok részeként. Ha a virtuális gép rendelkezik erőforrás-zárolással, a karbantartási feladatot a rendszer az adatokkal együtt végrehajtja. Az információ szerint a virtuális gép el lett távolítva a Recovery Services-tárolóból, de az elavult hivatkozások némelyikét nem lehetett törölni a forrásszámítógépen.

Figyelmen kívül hagyhatja ezt a figyelmeztetést, ha soha nem szeretné többé a virtuális gépet védelemmel ellátni. Ha azonban később el kell végeznie ezt a virtuális gépet, a hivatkozások törléséhez kövesse a "probléma elhárítása" című szakasz lépéseit.

> [!WARNING]
> Ha nem végzi el a karbantartást:
>
> - Ha a Recovery Services-tároló segítségével engedélyezi a replikálást, a virtuális gép nem jelenik meg.
> - Ha a virtuális gép > **beállítások** > vész- **helyreállítás**használatával próbálja meg védelemmel ellátni a **virtuális gépet** , a művelet sikertelen lesz, és a "replikáció nem engedélyezhető a virtuális gépen lévő elavult erőforrás-hivatkozások miatt" üzenettel.

### <a name="fix-the-problem"></a>A probléma megoldása

> [!NOTE]
> Site Recovery nem törli a forrásként szolgáló virtuális gépet, vagy semmilyen módon nem befolyásolja ezeket a lépéseket.

1. Szüntesse meg a virtuális gép vagy a virtuálisgép-erőforráscsoport zárolását. Az alábbi ábrán például törölni kell a "MoveDemo" nevű virtuális gép erőforrás-zárolását:

    ![A virtuális gép zárolásának eltávolítása](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Töltse le a szkriptet [egy elavult site Recovery konfiguráció eltávolításához](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Futtassa a szkriptet, amely neve Cleanup-stale-asr-config-Azure-VM. ps1. Adja meg az előfizetés AZONOSÍTÓját, a virtuálisgép-erőforráscsoportot és a virtuális gép nevét paraméterként.
1. Ha az Azure-beli hitelesítő adatokat kéri, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hibák nélkül fut-e.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Nem engedélyezhető a replikáció a virtuális gépen lévő elavult erőforrás-hivatkozások miatt (150226-es hibakód)

### <a name="possible-cause"></a>Lehetséges ok

A virtuális gépen elavult konfiguráció található a korábbi Site Recovery-védelemből.

Ha az Azure-beli virtuális gép replikálását a Site Recovery használatával engedélyezte, elavult konfigurációt lehet használni az Azure-beli virtuális gépen, majd:

- Letiltotta a replikálást, de a forrás virtuális gépnek erőforrás-zárolása volt.
- A Site Recovery-tárolót a virtuális gép replikációjának explicit letiltása nélkül törölte.
- Törölte a Site Recovery tárolót tartalmazó erőforráscsoportot anélkül, hogy explicit módon le kellene tiltani a replikálást a virtuális gépen.

### <a name="fix-the-problem"></a>A probléma megoldása

> [!NOTE]
> Site Recovery nem törli a forrásként szolgáló virtuális gépet, vagy semmilyen módon nem befolyásolja ezeket a lépéseket.

1. Szüntesse meg a virtuális gép vagy a virtuálisgép-erőforráscsoport zárolását. Az alábbi ábrán például törölni kell a "MoveDemo" nevű virtuális gép erőforrás-zárolását:

    ![A virtuális gép zárolásának eltávolítása](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Töltse le a szkriptet [egy elavult site Recovery konfiguráció eltávolításához](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Futtassa a szkriptet, amely neve Cleanup-stale-asr-config-Azure-VM. ps1. Adja meg az előfizetés AZONOSÍTÓját, a virtuálisgép-erőforráscsoportot és a virtuális gép nevét paraméterként.
1. Ha az Azure-beli hitelesítő adatokat kéri, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hibák nélkül fut-e.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Nem található az Azure-beli virtuális gép vagy erőforráscsoport a (z) "replikáció engedélyezése" feladatokban való kijelöléshez

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>1\. ok: az erőforráscsoport és a forrás virtuális gép eltérő helyen található

Site Recovery jelenleg a forrás-régió erőforráscsoport és a virtuális gépek azonos helyen kell lenniük. Ha nem, akkor nem fogja tudni megkeresni a virtuális gépet vagy az erőforráscsoportot a védelem alkalmazására irányuló kísérlet során.

Megkerülő megoldásként engedélyezheti a virtuális gép replikálását a Recovery Services-tároló helyett. Lépjen a **forrás virtuális gép** > **Tulajdonságok** > vész- **helyreállítás** lehetőséget, és engedélyezze a replikálást.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>2\. ok: az erőforráscsoport nem része a kijelölt előfizetésnek

Előfordulhat, hogy nem fogja tudni megkeresni az erőforráscsoportot a védelem idején, ha az erőforráscsoport nem része a kijelölt előfizetésnek. Győződjön meg arról, hogy az erőforráscsoport a használt előfizetéshez tartozik.

### <a name="cause-3-stale-configuration"></a>3\. ok: elavult konfiguráció

Előfordulhat, hogy nem látja azt a virtuális gépet, amelyet engedélyezni szeretne a replikáláshoz, ha az Azure-beli virtuális gépen elavult Site Recovery konfiguráció van hátra. Ez az állapot akkor fordulhat elő, ha a Site Recovery használatával engedélyezte az Azure-beli virtuális gép replikálását, majd:

- A Site Recovery-tárolót a virtuális gép replikációjának explicit letiltása nélkül törölte.
- Törölte a Site Recovery tárolót tartalmazó erőforráscsoportot anélkül, hogy explicit módon le kellene tiltani a replikálást a virtuális gépen.
- Letiltotta a replikálást, de a forrás virtuális gépnek erőforrás-zárolása volt.

### <a name="fix-the-problem"></a>A probléma megoldása

> [!NOTE]
> Az ebben a szakaszban említett szkript használata előtt győződjön meg arról, hogy a "AzureRM. Resources" modult frissíti.  Site Recovery nem törli a forrásként szolgáló virtuális gépet, vagy semmilyen módon nem befolyásolja ezeket a lépéseket.

1. Távolítsa el a virtuális gép vagy a virtuálisgép-erőforráscsoport zárolását, ha van ilyen. Az alábbi ábrán például törölni kell a "MoveDemo" nevű virtuális gép erőforrás-zárolását:

    ![A virtuális gép zárolásának eltávolítása](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Töltse le a szkriptet [egy elavult site Recovery konfiguráció eltávolításához](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Futtassa a szkriptet, amely neve Cleanup-stale-asr-config-Azure-VM. ps1. Adja meg az előfizetés AZONOSÍTÓját, a virtuálisgép-erőforráscsoportot és a virtuális gép nevét paraméterként.
1. Ha az Azure-beli hitelesítő adatokat kéri, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hibák nélkül fut-e.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Nem lehet kijelölni a virtuális gépet a védelemhez

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>1\. ok: a virtuális gépnek egy sikertelen vagy nem válaszoló állapotba telepített bővítménye van

Lépjen a **virtuális gépek** > **Beállítások** > **bővítmények** elemre, és keresse meg a hibás állapotú bővítményeket. Távolítsa el a sikertelen bővítményeket, majd próbálkozzon újra a virtuális gép védelemmel.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>2\. ok: a virtuális gép kiépítési állapota érvénytelen.

A [virtuális gép kiépítési állapotával](#the-vms-provisioning-state-is-not-valid-error-code-150019)kapcsolatos hibaelhárítási lépések nem érvényesek a cikk későbbi részében.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>A virtuális gép kiépítési állapota érvénytelen (hibakód: 150019)

A virtuális gép replikálásának engedélyezéséhez a létesítési állapotnak **sikeresnek**kell lennie. Az alábbi lépéseket követve ellenőrizheti a kiépítési állapotot:

1. A Azure Portal válassza ki a **erőforrás-kezelő** az **összes szolgáltatásból**.
1. Bontsa ki az **előfizetések** listát, és válassza ki az előfizetését.
1. Bontsa ki a **ResourceGroups** listát, és válassza ki a virtuális gép erőforrás-csoportját.
1. Bontsa ki az **erőforrások** listát, és válassza ki a virtuális gépet.
1. A jobb oldalon tekintse meg a **provisioningState** mezőt a példány nézetben.

### <a name="fix-the-problem"></a>A probléma megoldása

- Ha a provisioningState **sikertelen**, a hibakereséshez forduljon az ügyfélszolgálathoz.
- Ha a provisioningState **frissítése**folyamatban van, előfordulhat, hogy egy másik bővítményt is üzembe kell helyezni. Győződjön meg arról, hogy vannak-e folyamatban lévő műveletek a virtuális gépen, várjon, amíg befejeződik, majd próbálja megismételni a sikertelen Site Recovery "replikáció engedélyezése" feladatot.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>A cél virtuális gép nem választható ki (a hálózati kijelölés lap nem érhető el)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>1\. ok: a virtuális gép egy olyan hálózathoz van csatlakoztatva, amely már le van képezve egy célként megadott hálózatra.

Ha a forrás virtuális gép egy virtuális hálózat része, és egy ugyanazon virtuális hálózatból származó másik virtuális gép már le van képezve a célként megadott erőforráscsoport egyik hálózatára, akkor a hálózat – kijelölés legördülő lista nem érhető el (ez a beállítás szürkén jelenik meg).

![A hálózati kiválasztási lista nem érhető el](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>2\. ok: előzőleg a virtuális gépet a Site Recovery használatával védte, majd letiltotta a replikálást.

A virtuális gép replikálásának letiltása nem törli a hálózati leképezést. A leképezést törölni kell a Recovery Services tárolóból, ahol a virtuális gép védett volt. Nyissa meg *Recovery Services* -tároló > **site Recovery infrastruktúra** > **hálózati leképezést**.

![Hálózati leképezés törlése](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

A vész-helyreállítási telepítés során konfigurált célként megadott hálózat a kezdeti beállítás után módosítható a virtuális gép védelme után:

![Hálózati leképezés módosítása](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Vegye figyelembe, hogy a hálózati megfeleltetés módosítása hatással van minden olyan védett virtuális gépre, amely ugyanazt a hálózati leképezést használja.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ vagy Kötet árnyékmásolata szolgáltatás hibája (hibakód: 151025)

Ha ez a hiba történik, a következő üzenet jelenik meg:

> "Site Recovery nem sikerült telepíteni a bővítményt"

### <a name="possible-causes"></a>Lehetséges okok

- A COM+ rendszeralkalmazás-szolgáltatás le van tiltva.
- A Kötet árnyékmásolata szolgáltatás le van tiltva.

### <a name="fix-the-problem"></a>A probléma megoldása

Állítsa be a COM+ rendszeralkalmazást és a Kötet árnyékmásolata szolgáltatást automatikus vagy manuális indítási módra.

1. Nyissa meg a szolgáltatások konzolt a Windows rendszerben.
1. Győződjön meg arról, hogy a COM+ rendszeralkalmazás és a Kötet árnyékmásolata szolgáltatás nincs **Letiltva** az **indítási típusként**.

    ![A COM+ rendszeralkalmazás indítási típusának és a Kötet árnyékmásolata szolgáltatásnak a megkeresése](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>A felügyelt lemez mérete nem támogatott (hibakód: 150172)

Ha ez a hiba történik, a következő üzenet jelenik meg:

> "Nem sikerült engedélyezni a virtuális gép védelmét, mert a *DiskName* mérete *DiskSize*) *, amely kisebb, mint a minimálisan támogatott méret 1024 MB."

### <a name="possible-cause"></a>Lehetséges ok

A lemez kisebb, mint a támogatott 1024 MB-os méret.

### <a name="fix-the-problem"></a>A probléma megoldása

Győződjön meg arról, hogy a lemez mérete a támogatott tartományon belül van, majd próbálja megismételni a műveletet.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>A védelem nincs engedélyezve, mert a GRUB-konfiguráció tartalmazza az eszköz nevét az UUID helyett (hibakód: 151126)

### <a name="possible-cause"></a>Lehetséges ok

A Linux GRUB konfigurációs fájljai (/boot/grub/menu.lst ",/boot/grub/grub.cfg,/boot/GRUB2/grub.cfg vagy/etc/default/grub) megadhatják a tényleges eszköznév helyett a *gyökér* és a *Folytatás* paraméterek értékét. A Site Recovery UUID-ket igényel, mert az eszközök nevei módosíthatók. Újraindításkor előfordulhat, hogy a virtuális gép nem ugyanazzal a névvel van ellátva a feladatátvétel során, ami problémákat okozhat.

A következő példák olyan GRUB-fájlokból származó sorok, amelyekben a szükséges UUID azonosítók helyett az eszközök nevei (félkövér színnel láthatók):

- Fájl/boot/GRUB2/grub.cfg

  > Linux/boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **resume =/dev/sda1** Splash = csendes csendes showopts

- Fájl:/boot/grub/menu.lst

  > kernel/boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **resume =/dev/sda1** Splash = Silent crashkernel = 256M-: 128M showopts VGA = 0x314


### <a name="fix-the-problem"></a>A probléma megoldása

Cserélje le az egyes eszközök nevét a megfelelő UUID-ra:

1. Keresse meg az eszköz UUID-azonosítóját a blkid- ***eszköz nevének***végrehajtásával. Például:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Cserélje le az eszköz nevét az UUID-ra, a **root = uuid**=*UUID* és a **resume = UUID**=*UUID*formátumban. A csere után például a/boot/grub/menu.lst (korábban tárgyalt) sor a következőképpen fog kinézni:

    > kernel/boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** Splash = Silent crashkernel = 256M-: 128M showopts VGA = 0x314

1. Próbálja megismételni a védelmet.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>A védelem engedélyezése nem sikerült, mert a GRUB-konfigurációban említett eszköz nem létezik (hibakód: 151124)

### <a name="possible-cause"></a>Lehetséges ok

A GRUB konfigurációs fájljai (/boot/grub/menu.lst,/boot/grub/grub.cfg,/boot/GRUB2/grub.cfg vagy/etc/default/grub) tartalmazhatják a *Rd.LVM.lv* vagy a *rd_LVM_LV*paramétereket. Ezek a paraméterek azonosítják a rendszerindításkor felderített logikai kötet-kezelő (LVM) eszközöket. Ha ezek az LVM-eszközök nem léteznek, a védett rendszer nem fog elindulni, és a rendszerindítási folyamat elakad. Ugyanez a probléma a feladatátvételi virtuális géppel is látható lesz. Íme néhány példa:

- Fájl:/boot/GRUB2/grub.cfg a RHEL7-on:

    > linux16/vmlinuz-3.10.0-957.el7. x86_64 root =/dev/Mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** RHGB csendes LANG = en_US. UTF-8

- Fájl:/etc/default/grub a RHEL7-on:

    > GRUB_CMDLINE_LINUX = "crashkernel = Auto **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet"

- Fájl:/boot/grub/menu.lst a 64 bites RHEL6-on:

    > kernel/vmlinuz-2.6.32-754.el6. x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = Auto **rd_LVM_LV = rootvg/lv_root** KEYBOARDTYPE = PC-s tábla = US **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb csendes

Az egyes példákban a félkövér kifejezés azt mutatja, hogy a GRUB-nak két LVM-eszközt kell észlelni a "root" és a "swap" névvel a "rootvg" kötetből.

### <a name="fix-the-problem"></a>A probléma megoldása

Ha az LVM-eszköz nem létezik, hozza létre, vagy távolítsa el a megfelelő paramétereket a GRUB konfigurációs fájljaiból. Ezután próbálkozzon újra a védelem engedélyezéséhez.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>A Site Recovery mobilitási szolgáltatás frissítése figyelmeztetésekkel fejeződött be (hibakód: 151083)

A Site Recovery mobilitási szolgáltatásnak számos összetevője van, amelyek közül az egyik neve szűrő-illesztőprogram. A szűrő-illesztőprogram csak a rendszer újraindításakor töltődik be a rendszermemóriába. Ha egy mobilitási szolgáltatás frissítése magában foglalja a szűrő-illesztőprogram változásait, a rendszer frissíti a gépet, de még mindig megjelenik egy figyelmeztetés arról, hogy egyes javítások újraindítást igényelnek. A figyelmeztetés akkor jelenik meg, ha a szűrő-illesztőprogram javításai csak akkor lépnek életbe, ha az új szűrő illesztőprogramja be van töltve, ami csak újraindítás közben történik.

> [!NOTE]
> Ez csak egy figyelmeztetés. A meglévő replikáció az új ügynök frissítése után is továbbra is működni fog. Ha az új szűrő illesztőprogramjának előnyeit szeretné kipróbálni, a régi szűrő-illesztőprogram továbbra is működni fog, ha nem indítja újra.
>
> A szűrő illesztőprogramján kívül a mobilitási szolgáltatás frissítésének egyéb továbbfejlesztései és javításai is az újraindítás nélkül lépnek életbe.  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Nem sikerült engedélyezni a védelmet, mert a replika által felügyelt lemez már létezik, a várt címkék nélkül a cél erőforráscsoporthoz (hibakód: 150161)

### <a name="possible-cause"></a>Lehetséges ok

Ez a probléma akkor fordulhat elő, ha a virtuális gépet korábban védelemmel látta el, és a replikálás le lett tiltva. a replika lemeze nem lett megtisztítva.

### <a name="fix-the-problem"></a>A probléma megoldása

Törölje a hibaüzenetben azonosított replika lemezt, majd próbálja megismételni a sikertelen védelmi feladatot.

## <a name="next-steps"></a>Következő lépések

[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
