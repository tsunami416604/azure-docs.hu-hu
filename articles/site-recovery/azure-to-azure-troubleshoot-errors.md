---
title: Az Azure VM-replikáció – problémamegoldás az Azure Site Recovery szolgáltatásban
description: Hibák elhárítása, amikor az Azure virtuális gépek replikálása a vész-helyreállítási hibák.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 9f95677211a89c1bb2a18076741dec08e76c5d70
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390398"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Az Azure-ból Azure-ba irányuló virtuális gép replikációs hibáinak elhárítása

Ez a cikk ismerteti, hogyan hárítsa el az Azure Site Recovery gyakori hibáit az Azure virtuális gépek (VM) egyik régióból a másikba történő replikációja és helyreállítása során. A támogatott konfigurációkról további információt az [Azure virtuális gépek replikálásának támogatási mátrixában talál.](azure-to-azure-support-matrix.md)

## <a name="azure-resource-quota-issues-error-code-150097"></a>Az Azure-erőforrások kvótájával kapcsolatos problémák (hibakód: 150097)

Győződjön meg arról, hogy az előfizetés e-alapú azure-beli virtuális gépek et a vész-helyreállítási (DR) régióként használni kívánt célrégióban hozhat létre. Az előfizetésnek elegendő kvótára van szüksége a szükséges méretű virtuális gépek létrehozásához. Alapértelmezés szerint a Site Recovery kiválasztja a cél virtuális gép méretét, amely megegyezik a forrás virtuális gép mérete. Ha a megfelelő méret nem érhető el, a Site Recovery automatikusan kiválasztja a legközelebbi elérhető méretet.

Ha nincs olyan méret, amely támogatja a forrás virtuálisgép-konfigurációt, a következő üzenet jelenik meg:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Lehetséges okok

- Az előfizetés-azonosító nincs engedélyezve, hogy hozzon létre virtuális gépeket a célrégióban helyen.
- Az előfizetés-azonosító nincs engedélyezve, vagy nem rendelkezik elegendő kvótával, hogy hozzon létre adott virtuális gép méretek a célrégióban helyen.
- Nincs megfelelő cél virtuális gép mérete található, hogy megfeleljen a forrás virtuális gép hálózati adapter kártya (NIC) száma (2), az előfizetés-azonosító a célrégióban helyen.

### <a name="fix-the-problem"></a>A probléma javítása

Lépjen kapcsolatba az [Azure számlázási támogatásával,](/azure/azure-portal/supportability/resource-manager-core-quotas-request) hogy az előfizetés a szükséges méretű virtuális gépeket hozhassa létre a célhelyen. Ezután próbálkozzon újra a sikertelen művelettel.

Ha a célhely kapacitásmegkötéssel rendelkezik, tiltsa le az adott helyre történő replikációt. Ezután engedélyezze a replikációt egy másik helyre, ahol az előfizetés elegendő kvótával rendelkezik a szükséges méretű virtuális gépek létrehozásához.

## <a name="trusted-root-certificates-error-code-151066"></a>Megbízható főtanúsítványok (hibakód: 151066)

Ha nem a legújabb megbízható főtanúsítványok vannak jelen a virtuális gép, a feladat, hogy a replikáció a Site Recovery sikertelen lehet. A hely-helyreállítási szolgáltatás virtuális gépről érkező hívásainak hitelesítése és engedélyezése nem sikerül ezek nélkül a tanúsítványok nélkül.

Ha a replikációs feladat engedélyezése sikertelen, a következő üzenet jelenik meg:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Lehetséges ok

Az engedélyezéshez és hitelesítéshez szükséges megbízható főtanúsítványok nincsenek jelen a virtuális gépen.

### <a name="fix-the-problem"></a>A probléma javítása

#### <a name="windows"></a>Windows

A Windows operációs rendszert futtató virtuális gép esetén telepítse a legújabb Windows-frissítéseket, hogy az összes megbízható főtanúsítvány jelen van a virtuális gépen. Kövesse a windows update-kezelés vagy tanúsítványfrissítés-kezelési folyamat tipikus a szervezetben, hogy a legújabb főtanúsítványok és a frissített tanúsítvány-visszavonási lista a virtuális gépeken.

- Ha leválasztott környezetben van, kövesse a windowsos frissítési folyamatot a szervezetben a tanúsítványok beszerzéséhez.
- Ha a szükséges tanúsítványok nem szerepelnek a virtuális gépen, a Site Recovery szolgáltatás hívásai biztonsági okokból sikertelenek lesznek.

Annak ellenőrzéséhez, hogy a `login.microsoftonline.com` probléma megoldódott-e, nyissa meg a virtuális gép böngészőjéből.

További információt a [Megbízható gyökerek és a nem engedélyezett tanúsítványok konfigurálása című témakörben talál.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))

#### <a name="linux"></a>Linux

Kövesse a Linux operációs rendszer verziójának forgalmazója által adott útmutatást a legújabb megbízható főtanúsítványok és a virtuális gép legújabb tanúsítvány-visszavonási listájának lekéréséhez.

Mivel a SUSE Linux szimbolikus hivatkozásokat vagy symlinkseket használ a tanúsítványok listájának fenntartásához, kövesse az alábbi lépéseket:

1. Jelentkezzen be **gyökérfelhasználóként.** A kivonatszimbólum`#`( ) az alapértelmezett parancssor.

1. A könyvtár módosításához futtassa a következő parancsot:

   `cd /etc/ssl/certs`

1. Ellenőrizze, hogy a Symantec legfelső szintű hitelesítésszolgáltatótanúsítvány a következő:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Ha a Symantec legfelső szintű hitelesítésszolgáltatói tanúsítványa nem található, futtassa a következő parancsot a fájl letöltéséhez. Ellenőrizze a hibákat, és kövesse a hálózati hibák ra vonatkozó ajánlott műveleteket.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Ellenőrizze, hogy a baltimore-i legfelső szintű hitelesítésszolgáltató tanúsítvány a következő:

   `ls Baltimore_CyberTrust_Root.pem`

   - Ha a baltimore-i legfelső szintű hitelesítésszolgáltató tanúsítványa nem található, futtassa ezt a parancsot a tanúsítvány letöltéséhez:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Ellenőrizze, hogy a DigiCert_Global_Root_CA tanúsítvány a következő:

   `ls DigiCert_Global_Root_CA.pem`

    - Ha a DigiCert_Global_Root_CA nem található, futtassa a következő parancsokat a tanúsítvány letöltéséhez:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Az újonnan letöltött tanúsítványok tanúsítványtulajdonosi kivonatainak frissítéséhez futtassa a rehash parancsfájlt:

   `c_rehash`

1. Annak ellenőrzéséhez, hogy a tulajdonos szimlinkként készült-e létrea tanúsítványokkal, futtassa az alábbi parancsokat:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. A _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ fájl másolatának létrehozása _b204d74a.0_fájlnévvel:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. A _Baltimore_CyberTrust_Root.pem_ fájl másolatának létrehozása _653b494a.0_fájlnévvel:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. A fájl másolatának létrehozása _DigiCert_Global_Root_CA.pem_ fájlról _a 3513523f.0 fájlnévvel:_

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Ellenőrizze, hogy a fájlok jelen vannak-e:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő URL-címek vagy IP-tartományok (hibakód: 151037 vagy 151072)

Ahhoz, hogy a Site Recovery replikációja működjön, a virtuális géptől adott URL-címekhez kimenő kapcsolat szükséges. Ha a virtuális gép tűzfal mögött van, vagy hálózati biztonsági csoport (NSG) szabályokat használ a kimenő kapcsolatok szabályozására, előfordulhat, hogy az alábbi problémák egyikével szembesülhet. Bár továbbra is támogatjuk az URL-címeken keresztüli kimenő hozzáférést, az IP-tartományok engedélyezési listájának használata már nem támogatott.

### <a name="issue-1-failed-to-register-azure-vm-with-site-recovery-151195"></a>1. probléma: Nem sikerült regisztrálni az Azure virtuális gépet a site recovery szolgáltatással (151195)

#### <a name="possible-causes"></a>Lehetséges okok

- A dns-feloldási hiba miatt nem lehet kapcsolatot létesíteni a hely-helyreállítási végpontokkal.
- Ez a probléma gyakoribb az újravédelem során, ha a virtuális gépen nem sikerült, de a DNS-kiszolgáló nem érhető el a vész-helyreállítási (DR) régióból.

#### <a name="fix-the-problem"></a>A probléma javítása

Ha egyéni DNS-t használ, győződjön meg arról, hogy a DNS-kiszolgáló elérhető a vész-helyreállítási régióból.

Annak ellenőrzése, hogy a virtuális gép egyéni DNS-beállítást használ-e:

1. Nyissa **meg a virtuális gépeket,** és válassza ki a virtuális gépet.
1. Nyissa meg a virtuális gépek **beállításait,** és válassza **a Hálózat lehetőséget.**
1. A **Virtuális hálózat/alhálózat alkalmazásban**válassza ki a hivatkozást a virtuális hálózat erőforráslapjának megnyitásához.
1. Nyissa meg a **Beállítások lapot,** és válassza a **DNS-kiszolgálók lehetőséget.**

Próbálja meg elérni a DNS-kiszolgálót a virtuális gépről. Ha a DNS-kiszolgáló nem érhető el, tegye elérhetővé a DNS-kiszolgáló feletti hiba, vagy a DR-hálózat és a DNS közötti helyvonal létrehozásával.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-hiba.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>2. probléma: A hely-helyreállítási konfiguráció nem sikerült (151196)

#### <a name="possible-cause"></a>Lehetséges ok

Az Office 365 hitelesítési és IP4-identitás-végpontjaihoz nem lehet kapcsolatot létesíteni.

#### <a name="fix-the-problem"></a>A probléma javítása

Az Azure Site Recovery hitelesítéshez hozzáférést kért az Office 365 IP-tartományaihoz.
Ha az Azure Network Security Group (NSG) szabályok/tűzfal proxy a virtuális gép kimenő hálózati kapcsolatának szabályozásához, győződjön meg arról, hogy [az Azure Active Directory (AAD) szolgáltatáscímke-alapú](/azure/virtual-network/security-overview#service-tags) NSG-szabályt használja az AAD-hoz való hozzáférés engedélyezéséhez. Már nem támogatjuk az IP-címalapú NSG-szabályokat.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>3. probléma: A hely-helyreállítási konfiguráció nem sikerült (151197)

#### <a name="possible-cause"></a>Lehetséges ok

A kapcsolat nem lehet létrehozni az Azure Site Recovery szolgáltatás végpontjaihoz.

#### <a name="fix-the-problem"></a>A probléma javítása

Ha az Azure Network Security Group (NSG) szabályok/tűzfal proxy segítségével szabályozza a kimenő hálózati kapcsolatot a virtuális gépen, győződjön meg arról, hogy szolgáltatáscímkéket használ. Már nem támogatjuk az IP-címek engedélyezési listájának használatát az Azure Site Recovery NSG-ken keresztül.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>4. probléma: A replikáció sikertelen lesz, ha a hálózati forgalom helyszíni proxykiszolgálót használ (151072)

#### <a name="possible-cause"></a>Lehetséges ok

Az egyéni proxybeállítások érvénytelenek, és a Mobilszolgáltató ügynöke nem észlelte automatikusan az Internet Explorer (IE) proxybeállításait.

#### <a name="fix-the-problem"></a>A probléma javítása

1. A Mobility service agent észleli a proxy `/etc/environment` beállításokat ie Windows és Linux rendszeren.
1. Ha csak a Mobilitás szolgáltatáshoz szeretne proxyt beállítani, akkor a proxy részleteit a _ProxyInfo.conf_ fájlban adja meg, amely a következő helyen található:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. A _ProxyInfo.conf_ proxybeállításainak a következő INI formátumban kell _rendelkeznie._

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> A Mobilitási szolgáltatásügynök csak a **nem hitelesített proxykat**támogatja.

### <a name="more-information"></a>További információ

A [szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-címek](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)megadásához kövesse a [Hálózatról az Azure-ban az Azure replikációra](azure-to-azure-about-networking.md)című útmutatót.

## <a name="disk-not-found-in-vm-error-code-150039"></a>A virtuális gépben nem található lemez (hibakód: 150039)

A virtuális géphez csatlakoztatott új lemezt inicializálni kell. Ha a lemez nem található, a következő üzenet jelenik meg:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Lehetséges okok

- Egy új adatlemez lett csatolva a virtuális géphez, de nem inicializálva.
- A virtuális gépben lévő adatlemez nem megfelelően jelenti azt a logikai egységszámot (LUN) értéket, amelyen a lemez a virtuális géphez lett csatolva.

### <a name="fix-the-problem"></a>A probléma javítása

Győződjön meg arról, hogy az adatlemezek inicializálódnak, majd próbálkozzon újra a művelettel.

- **Windows**: [Új lemez csatolása és inicializálása](/azure/virtual-machines/windows/attach-managed-disk-portal).
- **Linux**: [Új adatlemez inicializálása Linux alatt](/azure/virtual-machines/linux/add-disk).

Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Több lemez áll rendelkezésre a védelem (hibakód: 153039)

### <a name="possible-causes"></a>Lehetséges okok

- Egy vagy több lemez a közelmúltban került a virtuális gépre a védelem után.
- Egy vagy több lemez inicializálása a virtuális gép védelme után történt.

### <a name="fix-the-problem"></a>A probléma javítása

Ha ismét kifogástalanállapotot szeretne a virtuális gép replikációs állapotának, választhata a lemezek védelmét, vagy elveti a figyelmeztetést.

#### <a name="to-protect-the-disks"></a>A lemezek védelme

1. Nyissa meg a **Replikált elemek** > _virtuálisgép-neve_ > **Lemezek című**területet.
1. Jelölje ki a nem védett lemezt, majd válassza **a Replikáció engedélyezése**lehetőséget:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Engedélyezze a replikációt virtuálisgép-lemezeken.":::

#### <a name="to-dismiss-the-warning"></a>A figyelmeztetés elvetése

1. Nyissa meg a **Replikált elemek** > _virtuális gépének nevét._
1. Jelölje ki a figyelmeztetést az **Áttekintés** szakaszban, majd kattintson az **OK gombra.**

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Az új lemezre vonatkozó figyelmeztetés elvetése.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>A tárolóból eltávolított virtuális gép információval kiegészítve (hibakód: 150225)

Amikor a Site Recovery védi a virtuális gépet, kapcsolatokat hoz létre a forrás virtuális gépen. A védelem eltávolításakor vagy a replikáció letiltásakor a Site Recovery a törlési feladat részeként eltávolítja ezeket a hivatkozásokat. Ha a virtuális gép rendelkezik erőforrás-zárolással, a törlési feladat befejeződik az adatokkal. Az információ azt mondja, hogy a virtuális gép el lett távolítva a Recovery Services tárolójából, de az elavult kapcsolatok egy része nem tisztítható meg a forrásgépen.

Figyelmen kívül hagyhatja ezt a figyelmeztetést, ha soha nem kívánja megvédeni ezt a virtuális gépet újra. De ha később meg kell védenie ezt a virtuális gépet, kövesse az ebben a szakaszban a hivatkozások karbantartásához.

> [!WARNING]
> Ha nem végez karbantartást:
>
> - Ha engedélyezi a replikációt a Recovery Services tárolón, a virtuális gép nem jelenik meg.
> - Ha a virtuális gépet a **virtuális gép** > **beállításaivész-helyreállítással****Settings** > próbálja védeni, a művelet sikertelen lesz azzal az üzenettel, hogy a **replikáció nem engedélyezhető a virtuális gépen meglévő elavult erőforrás-kapcsolatok miatt.**

### <a name="fix-the-problem"></a>A probléma javítása

> [!NOTE]
> A Site Recovery nem törli a forrás virtuális gépet, és semmilyen módon nem befolyásolja azt a lépések végrehajtása közben.

1. Távolítsa el a zárolást a virtuális gép vagy a virtuális gép erőforráscsoportból. A következő képen például törölni kell a megnevezett virtuális gép erőforrászárolását: `MoveDemo`

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Távolítsa el a zárolást a virtuális gépről.":::

1. A parancsfájl letöltésével [távolítsa el az elavult site recovery konfigurációt.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Futtassa a _Cleanup-stale-asr-config-Azure-VM.ps1 parancsfájlt._ Adja meg az **előfizetési azonosítót**, **a virtuálisgép-erőforráscsoportot**és a **virtuális gép nevét** paraméterekként.
1. Ha a rendszer kéri az Azure-hitelesítő adatok megadását, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hiba nélkül fut-e.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>A replikáció nincs engedélyezve az elavult erőforrásokkal rendelkező virtuális számítógépen (hibakód: 150226)

### <a name="possible-causes"></a>Lehetséges okok

A virtuális gép elavult konfigurációval rendelkezik a korábbi Site Recovery védelemből.

Egy elavult konfiguráció előfordulhat egy Azure-beli virtuális gép, ha engedélyezte a replikációt az Azure virtuális gép a Site Recovery használatával, majd:

- Letiltotta a replikációt, de a forrás virtuális gép erőforrás-zárolással rendelkezett.
- A site recovery tároló tana nélkül explicit módon letiltása replikáció a virtuális gép.
- A site recovery-tárolót tartalmazó erőforráscsoportot törölte anélkül, hogy kifejezetten letiltana a replikációt a virtuális gépen.

### <a name="fix-the-problem"></a>A probléma javítása

> [!NOTE]
> A Site Recovery nem törli a forrás virtuális gépet, és semmilyen módon nem befolyásolja azt a lépések végrehajtása közben.

1. Távolítsa el a zárolást a virtuális gép vagy a virtuális gép erőforráscsoportból. A következő képen például törölni kell a megnevezett virtuális gép erőforrászárolását: `MoveDemo`

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Távolítsa el a zárolást a virtuális gépről.":::

1. A parancsfájl letöltésével [távolítsa el az elavult site recovery konfigurációt.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Futtassa a _Cleanup-stale-asr-config-Azure-VM.ps1 parancsfájlt._ Adja meg az **előfizetési azonosítót**, **a virtuálisgép-erőforráscsoportot**és a **virtuális gép nevét** paraméterekként.
1. Ha a rendszer kéri az Azure-hitelesítő adatok megadását, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hiba nélkül fut-e.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>A virtuális gép vagy az erőforráscsoport nem jelölhető ki a replikációs feladat engedélyezéséhez

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>1. probléma: Az erőforráscsoport és a forrásvirtuális gép különböző helyeken található

A Site Recovery jelenleg megköveteli, hogy a forrásrégió erőforráscsoportja és a virtuális gépek ugyanazon a helyen legyenek. Ha nem, akkor nem fogja tudni megtalálni a virtuális gépet vagy erőforráscsoportot, amikor védelmet próbál alkalmazni.

Kerülő megoldásként engedélyezheti a replikációt a virtuális gépről a Recovery Services-tároló helyett. Nyissa meg a **Forrás virtuálisgép** > **tulajdonságai** > **vész-helyreállítási** és a replikáció engedélyezése.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>2. probléma: Az erőforráscsoport nem része a kijelölt előfizetésnek

Előfordulhat, hogy nem találja az erőforráscsoportot a védelem időpontjában, ha az erőforráscsoport nem része a kijelölt előfizetésnek. Győződjön meg arról, hogy az erőforráscsoport a használt előfizetéshez tartozik.

### <a name="issue-3-stale-configuration"></a>3. probléma: Elavult konfiguráció

Előfordulhat, hogy nem jelenik meg a virtuális gép, amely engedélyezni kívánt replikációs, ha egy elavult site recovery konfiguráció létezik az Azure virtuális gép. Ez a feltétel akkor fordulhat elő, ha engedélyezte a replikációt az Azure virtuális gép a Site Recovery használatával, majd:

- A site recovery tároló tana nélkül explicit módon letiltása replikáció a virtuális gép.
- A site recovery-tárolót tartalmazó erőforráscsoportot törölte anélkül, hogy kifejezetten letiltana a replikációt a virtuális gépen.
- Letiltotta a replikációt, de a forrás virtuális gép erőforrás-zárolással rendelkezett.

### <a name="fix-the-problem"></a>A probléma javítása

> [!NOTE]
> Győződjön meg `AzureRM.Resources` róla, hogy frissíti a modult, mielőtt a jelen szakaszban említett parancsfájlt. A Site Recovery nem törli a forrás virtuális gépet, és semmilyen módon nem befolyásolja azt a lépések végrehajtása közben.

1. Távolítsa el a zárolást, ha van ilyen, a virtuális gép vagy a virtuális gép erőforráscsoportból. A következő képen például törölni kell a megnevezett virtuális gép erőforrászárolását: `MoveDemo`

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Távolítsa el a zárolást a virtuális gépről.":::

1. A parancsfájl letöltésével [távolítsa el az elavult site recovery konfigurációt.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. Futtassa a _Cleanup-stale-asr-config-Azure-VM.ps1 parancsfájlt._ Adja meg az **előfizetési azonosítót**, **a virtuálisgép-erőforráscsoportot**és a **virtuális gép nevét** paraméterekként.
1. Ha a rendszer kéri az Azure-hitelesítő adatok megadását, adja meg őket. Ezután ellenőrizze, hogy a parancsfájl hiba nélkül fut-e.

## <a name="unable-to-select-a-vm-for-protection"></a>Nem lehet kiválasztani a virtuális gép védelmi

### <a name="possible-cause"></a>Lehetséges ok

A virtuális gép egy bővítményt telepített sikertelen vagy nem válaszoló állapotban

### <a name="fix-the-problem"></a>A probléma javítása

Nyissa meg a **Virtuálisgépek** > **beállítási** > **bővítményeit,** és ellenőrizze, hogy vannak-e sikertelen állapotú bővítmények. Távolítsa el a sikertelen bővítményeket, majd próbálkozzon újra a virtuális gép védelmével.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>A virtuális gép létesítési állapota érvénytelen (150019-es hibakód)

A virtuális gép replikációjának engedélyezéséhez a létesítési állapotnak **sikeresnek**kell lennie. A kiépítési állapot ellenőrzéséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki az **Erőforrás-kezelőt** az **Összes szolgáltatásból.**
1. Bontsa ki az **Előfizetések** listát, és válassza ki az előfizetést.
1. Bontsa ki a **ResourceGroups listát,** és válassza ki a virtuális gép erőforráscsoportját.
1. Bontsa ki az **erőforrások listáját,** és válassza ki a virtuális gép.
1. Ellenőrizze a **provisioningState** mezőt a jobb oldali példánynézetben.

### <a name="fix-the-problem"></a>A probléma javítása

- Ha a **provisioningState** **sikertelen,** forduljon az ügyfélszolgálathoz a hibaelhárításhoz szükséges részletekkel.
- Ha a **provisioningState** **frissítése,** egy másik bővítmény telepítése lehet. Ellenőrizze, hogy vannak-e folyamatban lévő műveletek a virtuális gépen, várja meg, amíg befejeződnek, majd próbálja meg újra a sikertelen Site Recovery feladatot a replikáció engedélyezéséhez.

## <a name="unable-to-select-target-vm"></a>Nem lehet kiválasztani a cél virtuális gépét

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>1. probléma: A virtuális gép olyan hálózathoz van csatlakoztatva, amely már le van képezve egy célhálózatra

A vész-helyreállítási konfiguráció során, ha a forrás virtuális gép egy virtuális hálózat része, és egy másik virtuális gép ugyanabból a virtuális hálózatból már le van képezve a célerőforrás-csoport ban lévő hálózattal, a hálózatkijelölés legördülő lista alapértelmezés szerint nem érhető el (halványan jelenik meg).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="A hálózati kijelölési lista nem érhető el.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>2. probléma: Korábban megvédte a virtuális gép, majd letiltotta a replikációt

A virtuális gép replikációjának letiltása nem törli a hálózati hozzárendelést. A leképezést törölni kell a Recovery Services tárolóból, ahol a virtuális gép védett volt. Válassza ki a **Helyreállítási szolgáltatások tárolóját,** és nyissa meg **a Site** > **Recovery Infrastructure** > **for Azure virtuális gépek** > **hálózati leképezése című**területet.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Hálózati hozzárendelés törlése.":::

A vész-helyreállítási telepítés során konfigurált célhálózat a kezdeti telepítés után és a virtuális gép védelme után módosítható. A **hálózati hozzárendelés módosítása esetén** válassza ki a hálózat nevét:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Hálózati hozzárendelés módosítása.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ vagy VSS (hibakód: 151025)

A COM+ vagy a Volume Shadow Copy Service (VSS) hiba esetén a következő üzenet jelenik meg:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Lehetséges okok

- A COM+ rendszeralkalmazás szolgáltatás le van tiltva.
- A Kötet árnyékmásolata szolgáltatás le van tiltva.

### <a name="fix-the-problem"></a>A probléma javítása

Állítsa a COM+ rendszeralkalmazás és a Kötet árnyékmásolata szolgáltatást automatikus vagy manuális indítási módba.

1. Nyissa meg a Szolgáltatások konzolt a Windows rendszerben.
1. Győződjön meg arról, hogy a COM+ rendszeralkalmazás és a Kötet árnyékmásolata szolgáltatás **indítási típusaként**nincs **letiltva** értékre állítva.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Ellenőrizze a COM, valamint a Rendszeralkalmazás és a Kötet árnyékmásolata szolgáltatás indítási típusát.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nem támogatott felügyelt lemezméret (hibakód: 150172)

A hiba bekövetkeztekor a következő üzenet jelenik meg:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Lehetséges ok

A lemez kisebb, mint a támogatott 1024 MB-os méret.

### <a name="fix-the-problem"></a>A probléma javítása

Győződjön meg arról, hogy a lemez mérete a támogatott mérettartományon belül van, majd próbálkozzon újra a művelettel.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>A védelem nem engedélyezett, ha a GRUB eszköznevet használ (hibakód: 151126)

### <a name="possible-causes"></a>Lehetséges okok

A Linux Grand Unified Bootloader (GRUB) konfigurációs fájlok ( /boot/grub/menu.lst , _/boot/grub/grub.cfg_, /boot/grub2/grub.cfg , vagy _/etc/default/grub)_ konfigurációs `root` fájlok `resume` (_/boot/grub/menu.lst_, /boot/grub.cfg , _/boot/grub2/grub.cfg_, vagy /etc/default/grub) adhatják meg a tényleges eszközneveket az univerzálisan egyedi azonosító (UUID) értékek helyett. A Site Recovery felhasználói felhasználói azonosítókat igényel, mert az eszköznevek változhatnak. Újraindításután előfordulhat, hogy a virtuális gép nem jön létre ugyanazt a nevet a feladatátvétel, ami problémákat eredményez.

A következő példák a GRUB-fájlok azon sorai, ahol az eszköznevek a szükséges UUIAzonosítók helyett jelennek meg:

- Fájl _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Fájl: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>A probléma javítása

Cserélje le az egyes eszközök nevét a megfelelő UUID azonosítóra:

1. Keresse meg az eszköz UUID azonosítóját a parancs `blkid <device name>`végrehajtásával. Például:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Cserélje le az eszköz nevét az UUID azonosítójára, a `root=UUID=<UUID>` formátumokban és `resume=UUID=<UUID>`a . A csere után például a _/boot/grub/menu.lst_ sor a következő sornak tűnik:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Próbálkozzon újra a védelemmel.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>A védelem nem sikerült, mert a GRUB eszköz nem létezik (hibakód: 151124)

### <a name="possible-cause"></a>Lehetséges ok

A GRUB konfigurációs fájlok (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_vagy _/etc/default/grub_) tartalmazhatják a paramétereket `rd.lvm.lv` vagy `rd_LVM_LV`. Ezek a paraméterek azonosítják a logikai kötetkezelő (LVM) eszközöket, amelyeket a rendszerindításkor fel kell deríteni. Ha ezek az LVM eszközök nem léteznek, maga a védett rendszer nem indul el, és beragad a rendszerindítási folyamatba. Ugyanez a probléma is látható a feladatátvevő virtuális gép. Íme néhány példa:

- Fájl: _/boot/grub2/grub.cfg_ az RHEL7-en:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Fájl: _/etc/default/grub_ on RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Fájl: _/boot/grub/menu.lst_ az RHEL6-on:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

A GRUB-nak minden példában két LVM `root` `swap` eszközt kell `rootvg`észlelnie a nevekkel és a kötetcsoportból.

### <a name="fix-the-problem"></a>A probléma javítása

Ha az LVM-eszköz nem létezik, hozza létre, vagy távolítsa el a megfelelő paramétereket a GRUB konfigurációs fájlokból. Ezután próbálja meg újra engedélyezni a védelmet.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>A mobilitási szolgáltatás frissítése figyelmeztetésekkel fejeződött be (hibakód: 151083)

A Site Recovery Mobility szolgáltatás számos összetevőből áll, amelyek közül az egyiket szűrő-illesztőprogramnak nevezik. A szűrőillesztő csak a rendszer újraindítása során töltődik be a rendszermemóriába. Amikor egy mobilitási szolgáltatás frissítése szűrőillesztő-módosításokat tartalmaz, a számítógép frissül, de továbbra is megjelenik egy figyelmeztetés, hogy egyes javítások újraindítást igényelnek. A figyelmeztetés azért jelenik meg, mert a szűrőillesztő javításai csak az új szűrőillesztő betöltésekor lépnek érvénybe, ami csak újraindítás közben történik.

> [!NOTE]
> Ez csak egy figyelmeztetés. A meglévő replikáció az új ügynökfrissítés után is működik. Bármikor újraindíthatja az új szűrőillesztő előnyeit, de a régi szűrőillesztő továbbra is működik, ha nem indul újra.
>
> A szűrő-illesztőprogramon kívül a Mobilitási szolgáltatás frissítésében található egyéb fejlesztések és javítások előnyei újraindítás nélkül lépnek érvénybe.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>A védelem nem engedélyezett, ha a replikán i menedzselt lemez létezik

Ez a hiba akkor fordul elő, ha a kópia felügyelt lemeze már létezik a várt címkék nélkül a célerőforrás-csoportban.

### <a name="possible-cause"></a>Lehetséges ok

Ez a probléma akkor fordulhat elő, ha a virtuális gép korábban védett volt, és amikor a replikáció le volt tiltva, a replikalemez nem lett eltávolítva.

### <a name="fix-the-problem"></a>A probléma javítása

Törölje a hibaüzenetben azonosított replikalemezt, és próbálkozzon újra a sikertelen védelmi feladatkal.

## <a name="next-steps"></a>További lépések

[Azure-beli virtuális gépek replikálása egy másik Azure-régióba](azure-to-azure-how-to-enable-replication.md)
